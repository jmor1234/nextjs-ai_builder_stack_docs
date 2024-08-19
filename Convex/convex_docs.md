# Convex Docs

Convex is an all-in-one backend platform with thoughtful, product-centric APIs.

Use *TypeScript* to write queries as code that are automatically cached and realtime, with an acid compliant relational database.

# Frequently Asked Questions

## Is Convex a database?
Yes, but it is a lot more than this. Convex is a full cloud backend designed to replace your database, server functions, backend functionality, and the interface all the way out to your application. See the [docs](https://docs.convex.dev/) for a full list of features.

## What makes Convex realtime?
Convex's realtime database tracks all dependencies for every query function. Whenever any dependency changes, including any database rows, Convex reruns the query function and triggers an update to any active subscription on the client. Read more on [how Convex works](https://docs.convex.dev/overview).

## Where do Convex functions run?
Convex functions run server-side in an isolated execution environment within the Convex database itself. These provide direct efficient access to the underlying data but also to scheduling, storage, general purpose actions, etc. You can think of them like super-charged SQL queries.

## What database does Convex use?
Convex has a custom database engine designed to support live reactivity, incremental schema, automatic scaling, etc. Durability is provided by a write-ahead log that is stored persistently on AWS RDS.

## How is Convex different than Supabase?
Supabase is a collection of existing technologies packaged together. Convex is a rethink of how different parts of your application should stitch together. Convex query and mutation functions are designed to be database transactions in their entirety, and automatically update your web or native app.

## Can Convex talk to external services like OpenAI or Stripe?
Yes! Convex provides a function type we call *actions* that are designed to talk to the outside world. Actions do not have the same realtime and transactional guarantees as query and mutation functions, but are designed to work with them seamlessly within your Convex backend. See the [actions documentation](https://docs.convex.dev/actions) for more details.

## Is Convex open source?
Yes! You can clone, build, and run the open-source Convex backend on your own hardware. In addition, our client libraries, utility libraries, demo projects, and more can be found on our [GitHub account](https://github.com/convex-dev).

## Do I have to use TypeScript to use Convex?
Convex is a great fit for web applications built in TypeScript or JavaScript and has additional client support for Python and Rust, along with an HTTP client for interfacing with other languages. Convex server functions run natively in TypeScript or JavaScript but stay tuned for more general-purpose language support.

## Can I try Convex for free?
Yes! We have paid plans for larger applications and endeavor to make Convex cheaper than running infrastructure yourself.

## Can I build mobile apps using Convex?
We have many satisfied developers building mobile applications using React Native. Additional mobile support is on the todo list.

# How Convex Works

### Introduction

Over the past years, Convex has grown into a flourishing backend platform. We designed Convex to let builders just build and not have to worry about irrelevant details about administering backend infrastructure. Yet, curious developers have been asking us: How does Convex actually work? With our recent open source release, now is a perfect time to answer this question. Let's jump in.

In this article, we'll go on a tour of Convex, starting with an overview of the system. Then, we'll focus on the system's core state "at rest," exploring what a Convex deployment looks like when it's idle. We'll then gradually introduce motion, seeing how live requests flow through the system. By the time we're done, we'll know the major pieces of Convex's infrastructure, how they fit together, and the design principles underlying its construction.

### Overview

Let's get started with Convex by deploying James's swaghaus app from his talk "The future of databases is not just a database." This small demo app has a store where users can add items to their shopping cart, and items have a limited inventory. Users can't add out-of-stock items to their shopping cart.

#### Deploying

Let's start by `git clone`'ing the repository and deploying to Convex and our hosting platform.

Our codebase has two halves: the Web app starting from `index.html` that we build with `vite build` and deploy to our hosting provider and the backend endpoints within `convex/` that we push with `convex deploy` to the Convex cloud.

At its heart, a Convex deployment is a database that runs in the Convex cloud. But, it's a new type of database that directly runs your application code in the `convex/` folder as transactions, coupled with an end-to-end type system and consistency guarantees via its sync protocol.

Put another way, the most important thing to understand about Convex is that it's a database running in the cloud that runs client-defined API functions as transactions directly within the database.

#### Serving

Now that we've deployed our app, let's serve some traffic! Here's the high-level architecture diagram.

Visiting our app at `https://swaghaus.netlify.app` creates a WebSocket connection to our Convex deployment for executing functions on the server and receiving their responses. Let's open up that opaque Convex deployment box and see what's inside.

There are three main pieces of a Convex deployment: the sync worker, which manages WebSocket sessions from clients, the function runner, which runs the functions in our `convex/` folder, and the database, which stores our app's state.

### Convex at rest

We'll start with the Convex deployment quietly at rest, right after deploying our app but before we've served any traffic. Let's take a closer look at our `convex/` folder. There are two primary pieces: functions and schema.

#### Functions

All public traffic to a Convex app's backend must flow through public functions registered with `query`, `mutation`, and `action` from `convex/server`. In Swaghaus, we have a `query` function `getItems` that lists all items in the store that still have some stock remaining:

```typescript
// convex/getItems.ts
import { query } from "./_generated/server";

export default query({
  args: {},
  handler: async ({ db }) => {
    const items = await db
      .query("items")
      .withIndex("remaining", (q) => q.gt("remaining", 0))
      .collect();
    return items;
  },
});
```

Then, whenever the user adds an item to their cart, they call the `addCart` mutation. We've left out a few parts of this function for brevity, but you can always see the full source on GitHub.

```typescript
// convex/addCart.ts
import { v } from "convex/values";
import { mutation } from "./_generated/server";

// Moves item to the given shopping cart and decrements quantity 
// in stock.
export default mutation({
  args: { itemId: v.id("items") },
  handler: async ({ db }, args) => {
    // Check the item exists and has sufficient stock.
    const item = await db.get(args.itemId);
    if (item.remaining <= 0) {
      throw new Error(`Insufficient stock of ${item.name}`);
    }

    // Increment the item's count in cart.
    const cartItem = await db
      .query("carts")
      .withIndex("user_item", (q) =>
        q.eq("userToken", userToken).eq("itemId", args.itemId)
      )
      .first();  
      
    // Note: We're leaving out the code to insert the document
    // if it isn't there already.
    await db.patch(cartItem._id, { count: cartItem.count + 1 });  

    // Deduct stock for item.
    await db.patch(args.itemId, { remaining: item.remaining - 1 });
  },
});
```

Our function runner uses V8 for executing JavaScript, and since V8 can't run TypeScript directly, we bundle (or compile) the code in your `convex/` directory before sending it to the server for execution. This process also creates smaller code units that execute faster as well as source maps that help us provide high quality error backtraces.

#### Tables and schema

Apps can optionally specify their tables and validators for the data within them within a `schema.ts` file. Here's the one for Swaghaus:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  items: defineTable({
    name: v.string(),
    description: v.string(),
    price: v.float64(),
    remaining: v.float64(),
    image: v.string(),
  }).index("remaining", ["remaining"]),
  carts: defineTable({
    userToken: v.string(),
    itemId: v.id("items"),
    count: v.float64(),
  }).index("user_item", ["userToken", "itemId"]),
});
```

This schema defines two tables, `items` and `carts`, along with expected fields and field types for each one. This schema also defines indexes on `items` and `carts`: More on indexes later.

Tables contain documents, which can be arbitrary Convex objects. Convex supports a slight extension of JSON that adds 64-bit signed integers and binary data. All documents have a unique document ID that's generated by the system and stored on the `_id` field. So, our `items` table might contain a document that looks like:

```json
{
  "_id": "j970pq0asyav77fekdj08grwan6npmh1",
  "description": "Keeps you shady",
  "image": "hat.png",
  "name": "Convex Hat",
  "price": 19.5,
  "remaining": 11
}
```

#### The transaction log

So far we've just discussed user facing parts of our system. Let's dive into our first implementation detail to see how Convex actually stores data internally.

The Convex database stores its tables in the transaction log, an append-only data structure that stores all versions of documents within the database. Every version of a document contains a monotonically increasing timestamp within the log that's like a version number. The timestamp is purely an internal detail of the log, and it isn't included in the document's object.

So, our database state in Swaghaus might have a transaction log that looks like:

```
Timestamp | Operation | Document
---------+------------+----------
       14 | Insert     | { "_id": "j970pq0asyav77fekdj08grwan6npmh1", "description": "Keeps you shady", "image": "hat.png", "name": "Convex Hat", "price": 19.5, "remaining": 12 }
       15 | Update     | { "_id": "j970pq0asyav77fekdj08grwan6npmh1", "description": "Keeps you shady", "image": "hat.png", "name": "Convex Hat", "price": 19.5, "remaining": 11 }
```

The transaction log contains all tables' documents mixed together in timestamp order, where all tables share the same sequence of timestamps. Each timestamp `t` defines a snapshot of the database that includes all revisions up to `t`. In our example above, we have two snapshots of the database, each corresponding to a timestamp in the log.

#### Indexes

The transaction log is a minimalist data structure: It only supports appending some entries at the end and querying the entries at a given timestamp. However, this isn't powerful enough to access our data efficiently from queries. To make querying a snapshot efficient, we build indexes on top of the log.

So, to look up documents by their `_id` field, we can maintain an index that maps each `_id` to its latest value.

We also support queries at multiple versions for timestamps in the recent past, making our index multiversioned.

### Starting it up: The reactor

We now have all the pieces to start letting some requests through our system! Let's focus on the `getItems` query and `addCart` mutations we looked at earlier.

#### Concurrency and race conditions

For our Convex deployment to be Web Scale™, the backend needs to execute many `getItems` and `addCart` requests at the same time. Simply executing one function at a time is too slow for any real application. Introducing concurrency, however, comes with its own problems. In many systems, the possibility of concurrent requests forces developers to handle race conditions where requests interact with each other in unexpected ways.

#### Transactions

Transactions are the missing piece that let us have our cake and eat it too. A transaction is an atomic group of reads and writes to the database that encapsulates some application logic. Convex ensures that all transactions in the system are serializable, which means that their behavior is exactly the same as if they executed one at a time. Therefore, developers don't have to worry about race conditions when writing apps on Convex.

#### Read and write sets

Convex implements serializability using optimistic concurrency control. Optimistic concurrency control algorithms don't grab locks on rows in the database. Instead, they assume that conflicts between transactions are rare, record what each transaction reads and writes, and check for conflicts at the end of a transaction's execution.

Transactions have three main ingredients: a begin timestamp, their read set, and their write set. The committer in our system is the sole writer to the transaction log, and it receives finalized transactions, decides if they're safe to commit, and then appends their write sets to the transaction log.

#### Subscriptions

We can also use read sets for implementing realtime updates for queries, where a user can subscribe to the result of a query changing. The sync worker maintains all read sets for all active subscriptions and efficiently determines whether a transaction overlaps with any active subscription's read set.

#### Functions: Sandboxing and determinism

We implement both sandboxing and determinism by directly using V8's runtime and carefully controlling the environment we expose to executing JavaScript and scheduling IO operations.

### Putting it all together: Walking through a request

Let's walk through a few requests from the client all the way to the Convex backend and back.

#### Executing a query

The web server serves our app's JS, which creates the Convex client, executes our React components, and renders to the DOM. Mounting our `Items` component registers the `getItems` query with the Convex client, which sends a message on the WebSocket to tell the sync worker to execute this query. The sync worker passes this request along to the function runner, which executes the function's JavaScript and sends the result back to the client.

#### Executing a mutation

Clicking the "Add to Cart" button in Swaghaus triggers a mutation call to the server. The sync worker passes this message over to the function runner, which chooses a begin timestamp and executes the function, querying database indexes as needed. The function runner sends the mutation transaction's read set, write set, and begin timestamp to the sync worker, which forwards the transaction to the committer. If the serializability check passes, the committer appends the writes to the transaction log and returns to the sync worker, which then passes the mutation's return value to the client.

#### Updating a subscription

Since we added a new item to our cart with `addCart`, our view of `getItems` is no longer up-to-date. The subscription worker reads our new entry in the transaction log and determines that it overlaps with the read set of our previous `getItems` query. The sync worker then tells the function runner to rerun `getItems`, which proceeds as before. The sync worker pushes the updated result to the client over the WebSocket and updates its subscription with the new read set.

## The "Full-Stack Framework" Fallacy

I'd like to get into the nuance of Frontend vs. Backend vs. Full-stack, given all the buzz around Laravel and the JS ecosystem.

My take is that when people want the JS ecosystem to have a Ruby on Rails type of full-stack offering, what they really want is an opinionated backend ecosystem that interoperates seamlessly with their frontends in a way that empowers full-stack developers to easily build apps that can scale.

### Opinionated Backend

Having a backend platform that has opinionated answers for these is awesome:

1. Schema definition, especially if it guarantees that the schema in code matches the live DB schema, and enables managing the evolution of your schema alongside code versioning.
2. Database access: querying with indexes and pagination, writes with strong transactional guarantees, schema validation.
3. Argument validation so your code doesn't have to opt-in to safety.
4. Authentication baked in.
5. Scheduled jobs, or whatever your preferred term is for functions that run in the background to support async workflows, crons, retries, etc.
6. Realtime subscriptions / streaming (e.g. using a Socket, WebSocket, or Server-Sent Events)
7. Caching with automatic invalidation and consistency guarantees.
8. Security: hiding data from frontends, authorizing access and execution, not just exposing your database to a client directly.
9. Text search that is consistent with the searched data.
10. Vector search for your hot new AI app idea.
11. File storage with strong durability guarantees.
12. Automatic scaling so you don't get bogged down in DevOps.

Not having these in a consistent package is a huge pain point surfaced by this whole discussion, and highlights the fragmentation of the JS ecosystem. Beyond decision fatigue, or wiring together a constellation of frameworks, libraries and services, it's cumbersome today for an ecosystem to develop. It's not easy to:

- Publish an npm package with a frontend component that also needs to talk to a server function, which needs a consistent interface for accessing persisted data.
- Drop in a library that talks to your own DB with transactional guarantees that also calls 3rd party APIs, handles webhooks, and schedules future follow-up tasks. For instance, implementing a drip email campaign that has rules around sending emails to users, based on some state in the DB.

However, I'll posit that this backend doesn't need to tightly couple with HTML rendering or your client-specific logic.

### Frontend Inter-op

Having a frontend framework that seamlessly works with your backend framework is amazing:

1. End-to-end type safety so you can author frontend and backend changes simultaneously.
2. Reactive / realtime updates so your UI automatically updates when data changes.
3. Optimistic updates for local changes that automatically resolve when the server data has updated and is reflected locally.
4. Authentication flows that enable automatically prompting the user to log in and securely hiding UI.
5. Consistent data views: all of the data on a page coming from the same logical snapshot, so you don't have two parts of your UI that disagree.

When you don't have these, you end up with workarounds:

- You separately declare the types that you expect from HTTP endpoints, or rely on codegen which can get out of date.
- You poll for new data or expect users to "just refresh the tab" to get updated data.
- You hand-craft logic to re-fetch the right data whenever a user does something meaningful.

However, this doesn't need to be the same framework as your backend (and probably shouldn't if you care about multi-platform support).

### The Full-Stack Fallacy

Full-stack is exciting because the same developer can work on the frontend and backend at the same time, ideally in the same language so the same types flow through. Full-stack development is about enabling full-stack workflows.

It doesn't require that the same company makes your backend and frontend abstractions, only that there are strong abstractions that enable working across them transparently.

It's nice when you can have multiple frontends for your product co-exist, even if they use different frameworks or languages (ReactNative, React, Swift, Kotlin, etc.). It's a benefit to be able to change frontends over time without rewriting all of your business logic, while still being owned by a single full-stack dev. Once you deeply couple your business logic with an HTML rendering strategy, what are you going to do when you decide you need a native mobile app?

### The Convex Take

With Convex as your backend you can write your backend in TypeScript and your frontend with Next.js, Vite (Remix), Vue, Svelte, or whatever comes next, and they can all talk to the same backend API with end-to-end types and real-time reactivity. This is possible because Convex has clients that provide types, manage the WebSocket connection, ensure consistent data views, refetch auth tokens, expose optimistic update APIs, and more. And if you're not using TypeScript, you can still hit the same API endpoints from any language and get the same guarantees, whether you're using our Rust client library (which powers our Python client) or talking HTTP directly. You can still share this logic with your server-side React calls, but there is a clear distinction between your frontend logic and backend API.

Convex has built-in:

1. Schema validation with atomic code/schema versioning.
2. Reactive database with the strongest transaction guarantees (ACID with serializable isolation): no more read-modify-write races.
3. Realtime subscriptions over WebSocket with zero-config consistent caching with automatic cache invalidation.
4. Argument validation so your code doesn't have to opt-in to safety or cast types.
5. Authentication to know which user is making a request.
6. Scheduled jobs that can be transactionally scheduled and canceled (separate from cron jobs, which it also has).
7. Security: hiding data from frontends using server-side functions, authorizing access and execution, not just exposing your database to a client directly with an over-wrought RLS DSL.
8. Text search that is immediately consistent with the searched data.
9. Vector search for your hot new AI app idea.
10. File storage with strong durability guarantees.
11. Automatic scaling by the team that built and scaled Dropbox.

This enables you to ship frontend components that have associated backend logic, and makes it easy for people to drop that into their own projects, since the database models and logic is all speaking Convex, rather than supporting any number of database connectors with varying degrees of transaction guarantees (Convex has serializable isolation - the highest level).

It's the opinionated backend for TypeScript developers AND you can hit the same API from GoLang / Swift / etc. without duplicating all your business logic from your `/app` to `/api`.

I made a stateful migration management tool that is built entirely on top of Convex (anyone could have written it) and published it along with many other tools to the `convex-helpers` npm package. It calls user-defined functions, saves migrations state, recursively schedules batches of data to migrate, transactionally reads and writes to your database, and is trivial to drop into any Convex project. I also just published a rate limiting library that provides transactional application-layer rate limiting. It adds a single table added to the user's Convex project, and all the operations happen within the same transaction window as the code using it.

We are also in the midst of designing and building a "components" ecosystem for backend logic - so you can easily extend your own app with opinionated, stateful, powerful backend features like email sending or Stripe payment handling. It's like spinning up a constellation of services in Kubernetes, except you get atomic deploys, cross-component transactions, type safe APIs, and strongly isolated function calls instead of network RPCs and service-oriented version skew hell. And you don't have to use Kubernetes (which I personally loved nerding out on, but gets in the way of actually building a product people care about).

## Summary

Full-stack is great and here to stay. Writing your frontend and backend in the same language is really useful. Opinionated backends should make your life easier, and not box you into coupling your data and business logic with your web app UI. Full-stack workflows should enable you to seamlessly co-develop your frontend and backend with end-to-end typing, strong data correctness guarantees, caching, and all of the tools you need to build a modern responsive app.

# The Zen of Convex

Convex is an opinionated framework, with every element designed to pull developers into the pit of success.

The Zen of Convex is a set of guidelines & best practices developers have discovered that keep their projects falling into this wonderful pit.

## Performance

### Double down on the reactor
There's a reason why a deterministic, reactive database is the beating heart of Convex: the more you center your apps around its properties, the better your projects will fare over time. Your projects will be easier to understand and refactor. Your app's performance will stay *screaming fast*. You won't have any consistency or state management problems.

### Use a query for nearly every app read
Queries are the reactive, automatically cacheable, consistent and resilient way to propagate data to your application and its jobs. With very few exceptions, every read operation in your app should happen via a query function.

### Keep reactor functions light & fast
In general, your mutations and queries should be working with less than a few hundred records and should aim to finish in less than 100ms. It's nearly impossible to maintain a *snappy, responsive app* if your synchronous transactions involve a lot more work than this.

### Use actions sparingly and incrementally
Actions are wonderful for batch jobs and/or integrating with outside services. They're very powerful, but they're slower, more expensive, and Convex provides a lot fewer guarantees about their behavior. So never use an action if a query or mutation will get the job done.

### Don't over-complicate client-side state management
Convex builds in a ton of its own caching and consistency controls into the app's client library. Rather than reinvent the wheel, let your client-side code take advantage of these built-in performance boosts.

### Let Convex handle caching & consistency
You might be tempted to quickly build your own local cache or state aggregation layer in Convex to sit between your components and your Convex functions. With Convex, most of the time, you won't end up needing this. More often than not, you can bind your components to Convex functions in pretty simple ways and things will *Just Work* and be plenty fast.

### Be thoughtful about the return values of mutations
Mutation return values can be useful to trigger state changes in your app, but it's rarely a good idea to use them to set in-app state to update the UI. Let queries and the reactor do that.

## Architecture

### Create server-side frameworks using "just code"
Convex's built-in primitives are pretty low level! They're just functions. What about authentication frameworks? What about object-relational mappings? Do you need to wait until Convex ships some in-built feature to get those? *Nope*. In general, you should solve composition and encapsulation problems in your server-side Convex code using the same methods you use for the rest of your TypeScript code bases. After all, this is why Convex is "just code!" [Stack](https://stackoverflow.com) always has great examples of ways to tackle these needs.

### Don't misuse actions
Actions are powerful, but it's important to be intentional in how they fit into your app's data flow.

### Don't invoke actions directly from your app
In general, it's an anti-pattern to call actions from the browser. Usually, actions are running on some dependent record that should be living in a Convex table. So it's best trigger actions by invoking a mutation that both writes that dependent record and schedules the subsequent action to run in the background.

### Don't think 'background jobs', think 'workflow'
When actions are involved, it's useful to write chains of effects and mutations, such as:

```
action code → mutation → more action code → mutation
```

Then apps or other jobs can follow along with queries.

### Record progress one step at a time
While actions could work with thousands of records and call dozens of APIs, it's normally best to do smaller batches of work and/or to perform individual transformations with outside services. Then record your progress with a mutation, of course. Using this pattern makes it easy to debug issues, resume partial jobs, and report incremental progress in your app's UI.

# Next.js Quickstart

## Convex + Next.js

Convex is an all-in-one backend and database that integrates quickly and easily with Next.js.

Once you've started below, see how to set up Server Rendering and Hosting.

To get set up quickly with Convex and Next.js, run:

```
npm create convex@latest
```

or follow the guide below.

Learn how to query data from Convex in a Next.js app using the App Router and TypeScript. Alternatively, see the Pages Router version of this quickstart.

## Create a React app

Create a Next.js app using the `npx create-next-app` command. Choose the default option for every prompt (hit Enter).

```
npx create-next-app@latest my-app
```

## Install the Convex client and server library

To get started, install the `convex` package, which provides a convenient interface for working with Convex from a React app.

Navigate to your app and install `convex`:

```
cd my-app && npm install convex
```

## Set up a Convex dev deployment

Next, run `npx convex dev`. This will prompt you to log in with GitHub, create a project, and save your production and deployment URLs. It will also create a `convex/` folder for you to write your backend API functions in. The `dev` command will then continue running to sync your functions with your dev deployment in the cloud.

```
npx convex dev
```

## Create sample data for your database

In a new terminal window, create a `sampleData.jsonl` file with some sample data:

```
{"text": "Buy groceries", "isCompleted": true}
{"text": "Go for a swim", "isCompleted": true}
{"text": "Integrate Convex", "isCompleted": false}
```

## Add the sample data to your database

Now that your project is ready, add a `tasks` table with the sample data into your Convex database with the `import` command:

```
npx convex import --table tasks sampleData.jsonl
```

## Expose a database query

Add a new file `tasks.ts` in the `convex/` folder with a query function that loads the data.

Exporting a query function from this file declares an API function named after the file and the export name, `api.tasks.get`.

```typescript
import { query } from "./_generated/server";

export const get = query({
  args: {},
  handler: async (ctx) => {
    return await ctx.db.query("tasks").collect();
  },
});
```

## Create a client component for the Convex provider

Add a new file `ConvexClientProvider.tsx` in the `app/` folder. Include the `"use client";` directive, create a `ConvexReactClient`, and a component that wraps its children in a `ConvexProvider`.

```typescript
"use client";

import { ConvexProvider, ConvexReactClient } from "convex/react";
import { ReactNode } from "react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return <ConvexProvider client={convex}>{children}</ConvexProvider>;
}
```

## Wire up the ConvexClientProvider

In `app/layout.tsx`, wrap the children of the body element with the `ConvexClientProvider`.

```typescript
import "./globals.css";
import type { Metadata } from "next";
import { Inter } from "next/font/google";
import { ConvexClientProvider } from "./ConvexClientProvider";

const inter = Inter({ subsets: ["latin"] });

export const metadata: Metadata = {
  title: "Create Next App",
  description: "Generated by create next app",
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <ConvexClientProvider>{children}</ConvexClientProvider>
      </body>
    </html>
  );
}
```

## Display the data in your app

In `app/page.tsx`, use the `useQuery` hook to fetch from your `api.tasks.get` API function.

```typescript
"use client";

import { useQuery } from "convex/react";
import { api } from "../convex/_generated/api";

export default function Home() {
  const tasks = useQuery(api.tasks.get);
  return (
    <main className="flex min-h-screen flex-col items-center justify-between p-24">
      {tasks?.map(({ _id, text }) => <div key={_id}>{text}</div>)}
    </main>
  );
}
```

## Start the app

Start the app, open `http://localhost:3000` in a browser, and see the list of tasks.

```
npm run dev
```

## Convex Next.js API Reference

### Module: nextjs
Helpers for integrating Convex into Next.js applications using server rendering.

This module contains:

- `preloadQuery`, for preloading data for reactive client components.
- `fetchQuery`, `fetchMutation`, and `fetchAction` for loading and mutating Convex data from Next.js Server Components, Server Actions, and Route Handlers.

### Usage
All exported functions assume that a Convex deployment URL is set in the `NEXT_PUBLIC_CONVEX_URL` environment variable. `npx convex dev` will automatically set it during local development.

### Preloading data
Preload data inside a Server Component:

```javascript
import { preloadQuery } from "convex/nextjs";
import { api } from "@/convex/_generated/api";
import ClientComponent from "./ClientComponent";

export async function ServerComponent() {
  const preloaded = await preloadQuery(api.foo.baz);
  return <ClientComponent preloaded={preloaded} />;
}
```

And pass it to a Client Component:

```javascript
import { Preloaded, usePreloadedQuery } from "convex/nextjs";
import { api } from "@/convex/_generated/api";

export function ClientComponent(props: {
  preloaded: Preloaded<typeof api.foo.baz>;
}) {
  const data = await usePreloadedQuery(props.preloaded);
  // render `data`...
}
```

### Type Aliases

### `NextjsOptions`
Options to `preloadQuery`, `fetchQuery`, `fetchMutation`, and `fetchAction`.

| Name | Type | Description |
| --- | --- | --- |
| `token?` | `string` | The JWT-encoded OpenID Connect authentication token to use for the function call. |
| `url?` | `string` | The URL of the Convex deployment to use for the function call. Defaults to `process.env.NEXT_PUBLIC_CONVEX_URL`. |
| `skipConvexDeploymentUrlCheck?` | `boolean` | Skip validating that the Convex deployment URL looks like `https://happy-animal-123.convex.cloud` or `localhost`. This can be useful if running a self-hosted Convex backend that uses a different URL. The default value is `false`. |

### Functions

### `preloadQuery`
`▸ preloadQuery<Query>(query, ...args): Promise<Preloaded<Query>>`

Execute a Convex query function and return a `Preloaded` payload which can be passed to `usePreloadedQuery` in a Client Component.

**Type Parameters:**
- `Query`: extends `FunctionReference<"query">`

**Parameters:**
- `query`: `Query` - a `FunctionReference` for the public query to run like `api.dir1.dir2.filename.func`.
- `...args`: `ArgsAndOptions<Query, NextjsOptions>` - The arguments object for the query. If this is omitted, the arguments will be `{}`.

**Returns:**
`Promise<Preloaded<Query>>` - A promise of the `Preloaded` payload.

### `preloadedQueryResult`
`▸ preloadedQueryResult<Query>(preloaded): FunctionReturnType<Query>`

Returns the result of executing a query via `preloadQuery`.

**Type Parameters:**
- `Query`: extends `FunctionReference<"query">`

**Parameters:**
- `preloaded`: `Preloaded<Query>` - The `Preloaded` payload returned by `preloadQuery`.

**Returns:**
`FunctionReturnType<Query>` - The query result.

### `fetchQuery`
`▸ fetchQuery<Query>(query, ...args): Promise<FunctionReturnType<Query>>`

Execute a Convex query function.

**Type Parameters:**
- `Query`: extends `FunctionReference<"query">`

**Parameters:**
- `query`: `Query` - a `FunctionReference` for the public query to run like `api.dir1.dir2.filename.func`.
- `...args`: `ArgsAndOptions<Query, NextjsOptions>` - The arguments object for the query. If this is omitted, the arguments will be `{}`.

**Returns:**
`Promise<FunctionReturnType<Query>>` - A promise of the query's result.

### `fetchMutation`
`▸ fetchMutation<Mutation>(mutation, ...args): Promise<FunctionReturnType<Mutation>>`

Execute a Convex mutation function.

**Type Parameters:**
- `Mutation`: extends `FunctionReference<"mutation">`

**Parameters:**
- `mutation`: `Mutation` - A `FunctionReference` for the public mutation to run like `api.dir1.dir2.filename.func`.
- `...args`: `ArgsAndOptions<Mutation, NextjsOptions>` - The arguments object for the mutation. If this is omitted, the arguments will be `{}`.

**Returns:**
`Promise<FunctionReturnType<Mutation>>` - A promise of the mutation's result.

### `fetchAction`
`▸ fetchAction<Action>(action, ...args): Promise<FunctionReturnType<Action>>`

Execute a Convex action function.

**Type Parameters:**
- `Action`: extends `FunctionReference<"action">`

**Parameters:**
- `action`: `Action` - A `FunctionReference` for the public action to run like `api.dir1.dir2.filename.func`.
- `...args`: `ArgsAndOptions<Action, NextjsOptions>` - The arguments object for the action. If this is omitted, the arguments will be `{}`.

**Returns:**
`Promise<FunctionReturnType<Action>>` - A promise of the action's result.

## Next.js Server Rendering

Next.js automatically renders both Client and Server Components on the server during the initial page load.

By default, Client Components will not wait for Convex data to be loaded, and your UI will render in a "loading" state. Read on to learn how to preload data during server rendering and how to interact with the Convex deployment from Next.js server-side.

## Example: Next.js App Router

This page covers the App Router variant of Next.js.

> **Note:** Next.js Server Rendering support is currently a beta feature. If you have feedback or feature requests, let us know on Discord!

### Preloading data for Client Components

If you want to preload data from Convex and leverage Next.js server rendering, but still retain reactivity after the initial page load, use `preloadQuery` from `convex/nextjs`.

In a Server Component, call `preloadQuery`:

```typescript
// app/TasksWrapper.tsx
import { preloadQuery } from "convex/nextjs";
import { api } from "@/convex/_generated/api";
import { Tasks } from "./Tasks";

export async function TasksWrapper() {
  const preloadedTasks = await preloadQuery(api.tasks.list, {
    list: "default",
  });
  return <Tasks preloadedTasks={preloadedTasks} />;
}
```

In a Client Component, call `usePreloadedQuery`:

```typescript
// app/TasksWrapper.tsx
"use client";

import { Preloaded, usePreloadedQuery } from "convex/react";
import { api } from "@/convex/_generated/api";

export function Tasks(props: {
  preloadedTasks: Preloaded<typeof api.tasks.list>;
}) {
  const tasks = usePreloadedQuery(props.preloadedTasks);
  // render `tasks`...
  return <div>...</div>;
}
```

`preloadQuery` takes three arguments:

1. The query reference
2. Optionally, the arguments object passed to the query
3. Optionally, a `NextjsOptions` object

`preloadQuery` uses the `cache: 'no-store'` policy, so any Server Components using it will not be eligible for static rendering.

### Using the query result

`preloadQuery` returns an opaque `Preloaded` payload that should be passed through to `usePreloadedQuery`. If you want to use the return value of the query, perhaps to decide whether to even render the Client Component, you can pass the `Preloaded` payload to the `preloadedQueryResult` function.

### Using Convex to render Server Components

If you need Convex data on the server, you can load data from Convex in your Server Components, but it will be non-reactive. To do this, use the `fetchQuery` function from `convex/nextjs`:

```typescript
// app/StaticTasks.tsx
import { fetchQuery } from "convex/nextjs";
import { api } from "@/convex/_generated/api";

export async function StaticTasks() {
  const tasks = await fetchQuery(api.tasks.list, { list: "default" });
  // render `tasks`...
  return <div>...</div>;
}
```

### Server Actions and Route Handlers

Next.js supports building HTTP request handling routes, similar to Convex HTTP Actions. You can use Convex from a Server Action or a Route Handler as you would any other database service.

To load and edit Convex data in your Server Action or Route Handler, you can use the `fetchQuery`, `fetchMutation`, and `fetchAction` functions.

Here's an example inline Server Action calling a Convex mutation:

```typescript
// app/example/page.tsx
import { api } from "@/convex/_generated/api";
import { fetchMutation, fetchQuery } from "convex/nextjs";
import { revalidatePath } from "next/cache";

export default async function PureServerPage() {
  const tasks = await fetchQuery(api.tasks.list, { list: "default" });
  async function createTask(formData: FormData) {
    "use server";

    await fetchMutation(api.tasks.create, {
      text: formData.get("text") as string,
    });
    revalidatePath("/example");
  }
  // render tasks and task creation form
  return <form action={createTask}>...</form>;
}
```

Here's an example Route Handler calling a Convex mutation:

```typescript
// app/api/route.ts
import { NextResponse } from "next/server";
// Hack for TypeScript before 5.2
const Response = NextResponse;

import { api } from "@/convex/_generated/api";
import { fetchMutation } from "convex/nextjs";

export async function POST(request: Request) {
  const args = await request.json();
  await fetchMutation(api.tasks.create, { text: args.text });
  return Response.json({ success: true });
}
```

### Server-side authentication

To make authenticated requests to Convex during server rendering, pass a JWT token to `preloadQuery` or `fetchQuery` in the third options argument:

```typescript
// app/TasksWrapper.tsx
import { preloadQuery } from "convex/nextjs";
import { api } from "@/convex/_generated/api";
import { Tasks } from "./Tasks";

export async function TasksWrapper() {
  const token = await getAuthToken();
  const preloadedTasks = await preloadQuery(
    api.tasks.list,
    { list: "default" },
    { token },
  );
  return <Tasks preloadedTasks={preloadedTasks} />;
}
```

The implementation of `getAuthToken` depends on your authentication provider, such as Clerk or Auth0:

```typescript
// app/auth.ts
import { auth } from "@clerk/nextjs";

export async function getAuthToken() {
  return (await auth().getToken({ template: "convex" })) ?? undefined;
}
```

### Configuring Convex deployment URL

Convex hooks used by Client Components are configured via the `ConvexReactClient` constructor, as shown in the Next.js Quickstart.

To use `preloadQuery`, `fetchQuery`, `fetchMutation`, and `fetchAction` in Server Components, Server Actions, and Route Handlers, you must either:

- Have the `NEXT_PUBLIC_CONVEX_URL` environment variable set to the Convex deployment URL
- Or pass the `url` option in the third argument to `preloadQuery`, `fetchQuery`, `fetchMutation`, or `fetchAction`

### Consistency

`preloadQuery` and `fetchQuery` use the `ConvexHTTPClient` under the hood. This client is stateless, which means that two calls to `preloadQuery` are not guaranteed to return consistent data based on the same database state. This is similar to more traditional databases, but is different from the guaranteed consistency provided by the `ConvexReactClient`.

To prevent rendering an inconsistent UI, avoid using multiple `preloadQuery` calls on the same page.

# Functions

Functions run on the backend and are written in JavaScript (or TypeScript). They are automatically available as APIs accessed through client libraries. Everything you do in the Convex backend starts from functions.

There are three types of functions:

1. **Queries** read data from your Convex database and are automatically cached and subscribable (realtime, reactive).
2. **Mutations** write data to the database and run as a transaction.
3. **Actions** can call Open AI, Stripe, Twilio, or any other service or API you need to make your app work.

You can also build HTTP actions when you want to call your functions from a webhook or a custom client.

## Queries

Queries are the bread and butter of your backend API. They fetch data from the database, check authentication or perform other business logic, and return data back to the client application.

This is an example query, taking in named arguments, reading data from the database and returning a result:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

// Return the last 100 tasks in a given task list.
export const getTaskList = query({
  args: { taskListId: v.id("taskLists") },
  handler: async (ctx, args) => {
    const tasks = await ctx.db
      .query("tasks")
      .filter((q) => q.eq(q.field("taskListId"), args.taskListId))
      .order("desc")
      .take(100);
    return tasks;
  },
});
```

Read on to understand how to build queries yourself.

### Query names

Queries are defined in TypeScript files inside your `convex/` directory.

The path and name of the file, as well as the way the function is exported from the file, determine the name the client will use to call it:

```typescript
// This function will be referred to as `api.myFunctions.myQuery`.
export const myQuery = …;

// This function will be referred to as `api.myFunctions.sum`.
export const sum = …;
```

To structure your API you can nest directories inside the `convex/` directory:

```typescript
// This function will be referred to as `api.foo.myQueries.listMessages`.
export const listMessages = …;
```

Default exports receive the name `default`.

```typescript
// This function will be referred to as `api.myFunctions.default`.
export default …;
```

The same rules apply to mutations and actions, while HTTP actions use a different routing approach.

Client libraries in languages other than JavaScript and TypeScript use strings instead of API objects:

- `api.myFunctions.myQuery` is `"myFunctions:myQuery"`
- `api.foo.myQueries.myQuery` is `"foo/myQueries:myQuery"`
- `api.myFunction.default` is `"myFunction:default"` or `"myFunction"`

### The query constructor

To actually declare a query in Convex you use the `query` constructor function. Pass it an object with a `handler` function, which returns the query result:

```typescript
import { query } from "./_generated/server";

export const myConstantString = query({
  handler: () => {
    return "My never changing string";
  },
});
```

### Query arguments

Queries accept named arguments. The argument values are accessible as fields of the second parameter of the `handler` function:

```typescript
import { query } from "./_generated/server";

export const sum = query({
  handler: (_, args: { a: number; b: number }) => {
    return args.a + args.b;
  },
});
```

Arguments and responses are automatically serialized and deserialized, and you can pass and return most value-like JavaScript data to and from your query.

To both declare the types of arguments and to validate them, add an `args` object using `v` validators:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const sum = query({
  args: { a: v.number(), b: v.number() },
  handler: (_, args) => {
    return args.a + args.b;
  },
});
```

See [argument validation](https://docs.convex.dev/docs/reference/values#argument-validation) for the full list of supported types and validators.

The first parameter of the `handler` function contains the query context.

### Query responses

Queries can return values of any supported Convex type which will be automatically serialized and deserialized.

Queries can also return `undefined`, which is not a valid Convex value. When a query returns `undefined` it is translated to `null` on the client.

### Query context

The `query` constructor enables fetching data, and other Convex features by passing a `QueryCtx` object to the `handler` function as the first parameter:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const myQuery = query({
  args: { a: v.number(), b: v.number() },
  handler: (ctx, args) => {
    // Do something with `ctx`
  },
});
```

Which part of the query context is used depends on what your query needs to do:

To fetch from the database use the `db` field. Note that we make the `handler` function an `async` function so we can `await` the promise returned by `db.get()`:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const getTask = query({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    return await ctx.db.get(args.id);
  },
});
```

Read more about [Reading Data](https://docs.convex.dev/docs/reference/data#reading-data).

To return URLs to stored files use the `storage` field. Read more about [File Storage](https://docs.convex.dev/docs/reference/storage).

To check user authentication use the `auth` field. Read more about [Authentication](https://docs.convex.dev/docs/reference/auth).

### Splitting up query code via helpers

When you want to split up the code in your query or reuse logic across multiple Convex functions you can define and call helper TypeScript functions:

```typescript
import { Id } from "./_generated/dataModel";
import { query, QueryCtx } from "./_generated/server";
import { v } from "convex/values";

export const getTaskAndAuthor = query({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    const task = await ctx.db.get(args.id);
    if (task === null) {
      return null;
    }
    return { task, author: await getUserName(ctx, task.authorId ?? null) };
  },
});

async function getUserName(ctx: QueryCtx, userId: Id<"users"> | null) {
  if (userId === null) {
    return null;
  }
  return (await ctx.db.get(userId))?.name;
}
```

You can export helpers to use them across multiple files. They will not be callable from outside of your Convex functions.

See [Type annotating server side helpers](https://docs.convex.dev/docs/reference/typescript#type-annotating-server-side-helpers) for more guidance on TypeScript types.

### Using NPM packages

Queries can import NPM packages installed in `node_modules`. Not all NPM packages are supported, see [Runtimes](https://docs.convex.dev/docs/reference/runtimes) for more details.

```typescript
import { query } from "./_generated/server";
import { faker } from "@faker-js/faker";

export const randomName = query({
  args: {},
  handler: () => {
    faker.seed();
    return faker.person.fullName();
  },
});
```

### Calling queries from clients

To call a query from React use the `useQuery` hook along with the generated `api` object.

```typescript
import { useQuery } from "convex/react";
import { api } from "../convex/_generated/api";

export function MyApp() {
  const data = useQuery(api.myFunctions.sum, { a: 1, b: 2 });
  // do something with `data`
}
```

See the [React client documentation](https://docs.convex.dev/docs/reference/react) for all the ways queries can be called.

### Caching & reactivity

Queries have two awesome attributes:

1. **Caching**: Convex caches query results automatically. If many clients request the same query, with the same arguments, they will receive a cached response.
2. **Reactivity**: clients can subscribe to queries to receive new results when the underlying data changes.

To have these attributes the `handler` function must be deterministic, which means that given the same arguments (including the query context) it will return the same response.

For this reason queries cannot fetch from third party APIs. To call third party APIs, use actions.

You might wonder whether you can use non-deterministic language functionality like `Math.random()` or `Date.now()`. The short answer is that Convex takes care of implementing these in a way that you don't have to think about the deterministic constraint.

## Mutations

Mutations insert, update and remove data from the database, check authentication or perform other business logic, and optionally return a response to the client application.

This is an example mutation, taking in named arguments, writing data to the database and returning a result:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

// Create a new task with the given text
export const createTask = mutation({
  args: { text: v.string() },
  handler: async (ctx, args) => {
    const newTaskId = await ctx.db.insert("tasks", { text: args.text });
    return newTaskId;
  },
});
```

### Mutation names

Mutations follow the same naming rules as queries, see [Query names](https://docs.convex.dev/docs/queries#query-names).

Queries and mutations can be defined in the same file when using named exports.

### The mutation constructor

To declare a mutation in Convex use the `mutation` constructor function. Pass it an object with a `handler` function, which performs the mutation:

```typescript
import { mutation } from "./_generated/server";

export const mutateSomething = mutation({
  handler: () => {
    // implementation will be here
  },
});
```

Unlike a query, a mutation can but does not have to return a value.

### Mutation arguments

Just like queries, mutations accept named arguments, and the argument values are accessible as fields of the second parameter of the `handler` function:

```typescript
import { mutation } from "./_generated/server";

export const mutateSomething = mutation({
  handler: (_, args: { a: number; b: number }) => {
    // do something with `args.a` and `args.b`

    // optionally return a value
    return "success";
  },
});
```

Arguments and responses are automatically serialized and deserialized, and you can pass and return most value-like JavaScript data to and from your mutation.

To both declare the types of arguments and to validate them, add an `args` object using `v` validators:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const mutateSomething = mutation({
  args: { a: v.number(), b: v.number() },
  handler: (_, args) => {
    // do something with `args.a` and `args.b`
  },
});
```

See [argument validation](https://docs.convex.dev/docs/queries#argument-validation) for the full list of supported types and validators.

The first parameter to the `handler` function is reserved for the mutation context.

### Mutation responses

Queries can return values of any supported Convex type which will be automatically serialized and deserialized.

Mutations can also return `undefined`, which is not a valid Convex value. When a mutation returns `undefined` it is translated to `null` on the client.

### Mutation context

The mutation constructor enables writing data to the database, and other Convex features by passing a `MutationCtx` object to the `handler` function as the first parameter:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const mutateSomething = mutation({
  args: { a: v.number(), b: v.number() },
  handler: (ctx, args) => {
    // Do something with `ctx`
  },
});
```

Which part of the mutation context is used depends on what your mutation needs to do:

- To read from and write to the database use the `db` field. Note that we make the `handler` function an `async` function so we can `await` the promise returned by `db.insert()`:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const addItem = mutation({
  args: { text: v.string() },
  handler: async (ctx, args) => {
    await ctx.db.insert("tasks", { text: args.text });
  },
});
```

Read on about [Writing Data](https://docs.convex.dev/docs/database#writing-data).

- To generate upload URLs for storing files use the `storage` field. Read on about [File Storage](https://docs.convex.dev/docs/file-storage).
- To check user authentication use the `auth` field. Read on about [Authentication](https://docs.convex.dev/docs/authentication).
- To schedule functions to run in the future, use the `scheduler` field. Read on about [Scheduled Functions](https://docs.convex.dev/docs/scheduled-functions).

### Splitting up mutation code via helpers

When you want to split up the code in your mutation or reuse logic across multiple Convex functions you can define and call helper TypeScript functions:

```typescript
import { v } from "convex/values";
import { mutation, MutationCtx } from "./_generated/server";

export const addItem = mutation({
  args: { text: v.string() },
  handler: async (ctx, args) => {
    await ctx.db.insert("tasks", { text: args.text });
    await trackChange(ctx, "addItem");
  },
});

async function trackChange(ctx: MutationCtx, type: "addItem" | "removeItem") {
  await ctx.db.insert("changes", { type });
}
```

Mutations can call helpers that take a `QueryCtx` as argument, since the mutation context can do everything query context can.

You can export helpers to use them across multiple files. They will not be callable from outside of your Convex functions.

See [Type annotating server side helpers](https://docs.convex.dev/docs/queries#type-annotating-server-side-helpers) for more guidance on TypeScript types.

### Using NPM packages

Mutations can import NPM packages installed in `node_modules`. Not all NPM packages are supported, see [Runtimes](https://docs.convex.dev/docs/runtimes) for more details.

```typescript
import { faker } from "@faker-js/faker";
import { mutation } from "./_generated/server";

export const randomName = mutation({
  args: {},
  handler: async (ctx) => {
    faker.seed();
    await ctx.db.insert("tasks", { text: "Greet " + faker.person.fullName() });
  },
});
```

### Calling mutations from clients

To call a mutation from React use the `useMutation` hook along with the generated `api` object.

```typescript
import { useMutation } from "convex/react";
import { api } from "../convex/_generated/api";

export function MyApp() {
  const mutateSomething = useMutation(api.myFunctions.mutateSomething);
  const handleClick = () => {
    mutateSomething({ a: 1, b: 2 });
  };
  // pass `handleClick` to a button
  // ...
}
```

See the [React client documentation](https://docs.convex.dev/docs/react-client) for all the ways queries can be called.

When mutations are called from the React or Rust clients, they are executed one at a time in a single, ordered queue. You don't have to worry about mutations editing the database in a different order than they were triggered.

### Transactions

Mutations run transactionally. This means that:

- All database reads inside the transaction get a consistent view of the data in the database. You don't have to worry about a concurrent update changing the data in the middle of the execution.
- All database writes get committed together. If the mutation writes some data to the database, but later throws an error, no data is actually written to the database.

For this to work, similarly to queries, mutations must be deterministic, and cannot call third party APIs. To call third party APIs, use actions.

## Actions

Actions can call third-party services to do things such as processing a payment with Stripe. They can be run in Convex's JavaScript environment or in Node.js. They can interact with the database indirectly by calling queries and mutations.

### Action names

Actions follow the same naming rules as queries, see [Query names](https://docs.convex.dev/docs/reference/query-names).

### The action constructor

To declare an action in Convex you use the `action` constructor function. Pass it an object with a `handler` function, which performs the action:

```typescript
// convex/myFunctions.ts
import { action } from "./_generated/server";

export const doSomething = action({
  handler: () => {
    // implementation goes here

    // optionally return a value
    return "success";
  },
});
```

Unlike a query, an action can but does not have to return a value.

### Action arguments and responses

Action arguments and responses follow the same rules as mutations:

```typescript
// convex/myFunctions.ts
import { action } from "./_generated/server";
import { v } from "convex/values";

export const doSomething = action({
  args: { a: v.number(), b: v.number() },
  handler: (_, args) => {
    // do something with `args.a` and `args.b`

    // optionally return a value
    return "success";
  },
});
```

The first argument to the `handler` function is reserved for the action context.

### Action context

The action constructor enables interacting with the database, and other Convex features by passing an `ActionCtx` object to the `handler` function as the first argument:

```typescript
// convex/myFunctions.ts
import { action } from "./_generated/server";
import { v } from "convex/values";

export const doSomething = action({
  args: { a: v.number(), b: v.number() },
  handler: (ctx, args) => {
    // do something with `ctx`
  },
});
```

Which part of that action context is used depends on what your action needs to do:

To read data from the database use the `runQuery` field, and call a query that performs the read:

```typescript
// convex/myFunctions.ts
import { action, internalQuery } from "./_generated/server";
import { internal } from "./_generated/api";
import { v } from "convex/values";

export const doSomething = action({
  args: { a: v.number() },
  handler: async (ctx, args) => {
    const data = await ctx.runQuery(internal.myFunctions.readData, {
      a: args.a,
    });
    // do something with `data`
  },
});

export const readData = internalQuery({
  args: { a: v.number() },
  handler: async (ctx, args) => {
    // read from `ctx.db` here
  },
});
```

Here `readData` is an internal query because we don't want to expose it to the client directly. Actions, mutations and queries can be defined in the same file.

To write data to the database use the `runMutation` field, and call a mutation that performs the write:

```typescript
// convex/myFunctions.ts
import { v } from "convex/values";
import { action } from "./_generated/server";
import { internal } from "./_generated/api";

export const doSomething = action({
  args: { a: v.number() },
  handler: async (ctx, args) => {
    const data = await ctx.runMutation(internal.myMutations.writeData, {
      a: args.a,
    });
    // do something else, optionally use `data`
  },
});
```

Use an internal mutation when you want to prevent users from calling the mutation directly.

As with queries, it's often convenient to define actions and mutations in the same file.

To generate upload URLs for storing files use the `storage` field. Read on about [File Storage](https://docs.convex.dev/docs/reference/file-storage).

To check user authentication use the `auth` field. Auth is propagated automatically when calling queries and mutations from the action. Read on about [Authentication](https://docs.convex.dev/docs/reference/authentication).

To schedule functions to run in the future, use the `scheduler` field. Read on about [Scheduled Functions](https://docs.convex.dev/docs/reference/scheduled-functions).

To search a vector index, use the `vectorSearch` field. Read on about [Vector Search](https://docs.convex.dev/docs/reference/vector-search).

### Dealing with circular type inference

Working around the TypeScript error: some action implicitly has type 'any' because it does not have a type annotation and is referenced directly or indirectly in its own initializer.

### Calling third-party APIs and using NPM packages

Actions can run in Convex's custom JavaScript environment or in Node.js.

By default, actions run in Convex's environment. This environment supports `fetch`, so actions that simply want to call a third-party API using `fetch` can be run in this environment:

```typescript
// convex/myFunctions.ts
import { action } from "./_generated/server";

export const doSomething = action({
  args: {},
  handler: async () => {
    const data = await fetch("https://api.thirdpartyservice.com");
    // do something with data
  },
});
```

Actions running in Convex's environment are faster compared to Node.js, since they don't require extra time to start up before running your action (cold starts). They can also be defined in the same file as other Convex functions. Like queries and mutations they can import NPM packages, but not all are supported.

Actions needing unsupported NPM packages or Node.js APIs can be configured to run in Node.js by adding the `"use node"` directive at the top of the file. Note that other Convex functions cannot be defined in files with the `"use node"` directive.

```typescript
// convex/myAction.ts
"use node";

import { action } from "./_generated/server";
import SomeNpmPackage from "some-npm-package";

export const doSomething = action({
  args: {},
  handler: () => {
    // do something with SomeNpmPackage
  },
});
```

Learn more about the [two Convex Runtimes](https://docs.convex.dev/docs/reference/runtimes).

### Splitting up action code via helpers

Just like with queries and mutations you can define and call helper functions to split up the code in your actions or reuse logic across multiple Convex functions.

But note that the `ActionCtx` only has the `auth` field in common with `QueryCtx` and `MutationCtx`.

### Calling actions from clients

To call an action from React use the `useAction` hook along with the generated `api` object.

```typescript
// src/myApp.tsx
import { useAction } from "convex/react";
import { api } from "../convex/_generated/api";

export function MyApp() {
  const performMyAction = useAction(api.myFunctions.doSomething);
  const handleClick = () => {
    performMyAction({ a: 1 });
  };
  // pass `handleClick` to a button
  // ...
}
```

Unlike mutations, actions from a single client are parallelized. Each action will be executed as soon as it reaches the server (even if other actions and mutations from the same client are running). If your app relies on actions running after other actions or mutations, make sure to only trigger the action after the relevant previous function completes.

Note: In most cases calling an action directly from a client is an anti-pattern. Instead, have the client call a mutation which captures the user intent by writing into the database and then schedules an action:

```typescript
// convex/myFunctions.ts
import { v } from "convex/values";
import { internal } from "./_generated/api";
import { internalAction, mutation } from "./_generated/server";

export const mutationThatSchedulesAction = mutation({
  args: { text: v.string() },
  handler: async (ctx, { text }) => {
    const taskId = await ctx.db.insert("tasks", { text });
    await ctx.scheduler.runAfter(0, internal.myFunctions.actionThatCallsAPI, {
      taskId,
      text,
    });
  },
});

export const actionThatCallsAPI = internalAction({
  args: { taskId: v.id("tasks"), text: v.string() },
  handler: (_, args): void => {
    // do something with `taskId` and `text`, like call an API
    // then run another mutation to store the result
  },
});
```

This way the mutation can enforce invariants, such as preventing the user from executing the same action twice.

### Limits

- Actions time out after 10 minutes.
- Node.js and Convex runtime have 512MB and 64MB memory limit respectively. Please contact us if you have a use case that requires configuring higher limits.
- Actions can do up to 1000 concurrent operations, such as executing queries, mutations or performing fetch requests.

For information on other limits, see [here](https://docs.convex.dev/docs/reference/limits).

### Error handling

Unlike queries and mutations, actions may have side-effects and therefore can't be automatically retried by Convex when errors occur. For example, say your action calls Stripe to send a customer invoice. If the HTTP request fails, Convex has no way of knowing if the invoice was already sent. Like in normal backend code, it is the responsibility of the caller to handle errors raised by actions and retry the action call if appropriate.

### Dangling promises

Make sure to `await` all promises created within an action. Async tasks still running when the function returns might or might not complete. In addition, since the Node.js execution environment might be reused between action calls, dangling promises might result in errors in subsequent action invocations.

## HTTP Actions

HTTP actions allow you to build an HTTP API right in Convex!

HTTP actions take in a `Request` and return a `Response` following the Fetch API. HTTP actions can manipulate the request and response directly, and interact with data in Convex indirectly by running queries, mutations, and actions. HTTP actions might be used for receiving webhooks from external applications or defining a public HTTP API.

HTTP actions are exposed at `https://<your deployment name>.convex.site` (e.g. `https://happy-animal-123.convex.site`).

### Example: HTTP Actions

#### Defining HTTP Actions

HTTP action handlers are defined using the `httpAction` constructor, similar to the `action` constructor for normal actions:

```typescript
// convex/myHttpActions.ts
import { httpAction } from "./_generated/server";

export const doSomething = httpAction(async () => {
  // implementation will be here
  return new Response();
});
```

The first argument to the handler is an `ActionCtx` object, which provides `auth`, `storage`, and `scheduler`, as well as `runQuery`, `runMutation`, `runAction`.

The second argument contains the `Request` data. HTTP actions do not support argument validation, as the parsing of arguments from the incoming `Request` is left entirely to you.

Here's an example:

```typescript
// convex/messages.ts
import { httpAction } from "./_generated/server";
import { internal } from "./_generated/api";

export const postMessage = httpAction(async (ctx, request) => {
  const { author, body } = await request.json();

  await ctx.runMutation(internal.messages.sendOne, {
    body: `Sent via HTTP action: ${body}`,
    author,
  });

  return new Response(null, {
    status: 200,
  });
});
```

To expose the HTTP Action, export an instance of `HttpRouter` from the `convex/http.ts` file. To create the instance, call the `httpRouter` function. On the `HttpRouter`, you can expose routes using the `route` method:

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { postMessage, getByAuthor, getByAuthorPathSuffix } from "./messages";

const http = httpRouter();

http.route({
  path: "/postMessage",
  method: "POST",
  handler: postMessage,
});

// Define additional routes
http.route({
  path: "/getMessagesByAuthor",
  method: "GET",
  handler: getByAuthor,
});

// Define a route using a path prefix
http.route({
  // Will match /getAuthorMessages/User+123 and /getAuthorMessages/User+234 etc.
  pathPrefix: "/getAuthorMessages/",
  method: "GET",
  handler: getByAuthorPathSuffix,
});

// Convex expects the router to be the default export of `convex/http.js`.
export default http;
```

You can now call this action via HTTP and interact with data stored in the Convex Database. HTTP actions are exposed on `https://<your deployment name>.convex.site`.

```bash
export DEPLOYMENT_NAME=... # example: "happy-animal-123"
curl -d '{ "author": "User 123", "body": "Hello world" }' \
    -H 'content-type: application/json' "https://$DEPLOYMENT_NAME.convex.site/postMessage"
```

Like other Convex functions, you can view your HTTP actions in the Functions view of your dashboard and view logs produced by them in the Logs view.

### Limits

HTTP actions run in the same environment as queries and mutations, so also do not have access to Node.js-specific JavaScript APIs. HTTP actions can call actions, which can run in Node.js.

Like actions, HTTP actions may have side-effects and will not be automatically retried by Convex when errors occur. It is a responsibility of the caller to handle errors and retry the request if appropriate.

Request and response size is limited to 20MB.

HTTP actions support request and response body types of `.text()`, `.json()`, `.blob()`, and `.arrayBuffer()`.

Note that you don't need to define an HTTP action to call your queries, mutations, and actions over HTTP if you control the caller, since you can use the JavaScript `ConvexHttpClient` or the Python client to call these functions directly.

### Debugging

#### Step 1: Check that your HTTP actions were deployed.

Check the functions page in the dashboard and make sure there's an entry called `http`.

If not, double-check that you've defined your HTTP actions with the `httpRouter` in a file called `http.js` or `http.ts` (the name of the file must match exactly), and that `npx convex dev` has no errors.

#### Step 2: Check that you can access your endpoint using `curl`

Get your URL from the dashboard under Settings > URL and Deploy Key.

Make sure this is the URL that ends in `.convex.site`, and not `.convex.cloud`. E.g. `https://happy-animal-123.convex.site`

Run a `curl` command to hit one of your defined endpoints, potentially defining a new endpoint specifically for testing:

```bash
curl -X GET https://<deployment name>.convex.site/myEndpoint
```

Check the logs page in the dashboard to confirm that there's an entry for your HTTP action.

#### Step 3: Check the request being made by your browser

If you've determined that your HTTP actions have been deployed and are accessible via `curl`, but there are still issues requesting them from your app, check the exact requests being made by your browser.

Open the Network tab in your browser's developer tools, and trigger your HTTP requests.

Check that this URL matches what you tested earlier with `curl` -- it ends in `.convex.site` and has the right deployment name.

You should be able to see these requests in the dashboard logs page.

If you see "CORS error" or messages in the browser console like `Access to fetch at '...' from origin '...' has been blocked by CORS policy`, you likely need to configure CORS headers and potentially add a handler for the pre-flight `OPTIONS` request. See the "CORS" section below.

### Common Patterns

#### File Storage

HTTP actions can be used to handle uploading and fetching stored files, see [File Storage with HTTP actions](https://docs.convex.dev/file-storage).

#### CORS

To make requests to HTTP actions from a website, you need to add Cross-Origin Resource Sharing (CORS) headers to your HTTP actions.

There are existing resources for exactly which CORS headers are required based on the use case. This [site](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) provides an interactive walkthrough for what CORS headers to add. Here's an example of adding CORS headers to a Convex HTTP action:

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { api } from "./_generated/api";
import { Id } from "./_generated/dataModel";

const http = httpRouter();

http.route({
  path: "/sendImage",
  method: "POST",
  handler: httpAction(async (ctx, request) => {
    // Step 1: Store the file
    const blob = await request.blob();
    const storageId = await ctx.storage.store(blob);

    // Step 2: Save the storage ID to the database via a mutation
    const author = new URL(request.url).searchParams.get("author");
    await ctx.runMutation(api.messages.sendImage, { storageId, author });

    // Step 3: Return a response with the correct CORS headers
    return new Response(null, {
      status: 200,
      // CORS headers
      headers: new Headers({
        // e.g. https://mywebsite.com, configured on your Convex dashboard
        "Access-Control-Allow-Origin": process.env.CLIENT_ORIGIN!,
        Vary: "origin",
      }),
    });
  }),
});

// Pre-flight request for /sendImage
http.route({
  path: "/sendImage",
  method: "OPTIONS",
  handler: httpAction(async (_, request) => {
    // Make sure the necessary headers are present
    // for this to be a valid pre-flight request
    const headers = request.headers;
    if (
      headers.get("Origin") !== null &&
      headers.get("Access-Control-Request-Method") !== null &&
      headers.get("Access-Control-Request-Headers") !== null
    ) {
      return new Response(null, {
        headers: new Headers({
          // e.g. https://mywebsite.com, configured on your Convex dashboard
          "Access-Control-Allow-Origin": process.env.CLIENT_ORIGIN!,
          "Access-Control-Allow-Methods": "POST",
          "Access-Control-Allow-Headers": "Content-Type, Digest",
          "Access-Control-Max-Age": "86400",
        }),
      });
    } else {
      return new Response();
    }
  }),
});
```

#### Authentication

You can leverage Convex's built-in authentication integration and access a user identity from `ctx.auth.getUserIdentity()`. To do this, call your endpoint with an `Authorization` header including a JWT token:

```typescript
// myPage.ts
const jwtToken = "...";

fetch("https://<deployment name>.convex.site/myAction", {
  headers: {
    Authorization: `Bearer ${jwtToken}`,
  },
});
```

## Internal Functions

Internal functions can only be called by other functions and cannot be called directly from a Convex client.

By default, your Convex functions are public and accessible to clients. Public functions may be called by malicious users in ways that cause surprising results. *Internal functions help you mitigate this risk*. We recommend using internal functions any time you're writing logic that should not be called from a client.

While internal functions help mitigate risk by reducing the public surface area of your application, you can still validate internal invariants using argument validation and/or authentication.

### Use Cases for Internal Functions

Leverage internal functions by:

1. Calling them from actions via `runQuery` and `runMutation`
2. Calling them from HTTP actions via `runQuery`, `runMutation`, and `runAction`
3. Scheduling them from other functions to run in the future
4. Scheduling them to run periodically from cron jobs
5. Running them using the Dashboard
6. Running them from the CLI

### Defining Internal Functions

An internal function is defined using `internalQuery`, `internalMutation`, or `internalAction`. For example:

```typescript
// convex/plans.ts
import { internalMutation } from "./_generated/server";
import { v } from "convex/values";

export const markPlanAsProfessional = internalMutation({
  args: { planId: v.id("plans") },
  handler: async (ctx, args) => {
    await ctx.db.patch(args.planId, { planType: "professional" });
  },
});
```

If you need to pass complicated objects to internal functions, you might prefer to not use argument validation. Note though that if you're using `internalQuery` or `internalMutation`, it's a better idea to pass around document IDs instead of documents, to ensure the query or mutation is working with the up-to-date state of the database.

### Internal Function without Argument Validation

```typescript
// convex/plans.ts
import { internalAction } from "./_generated/server";
import { Doc } from "./_generated/dataModel";

export const markPlanAsProfessional = internalAction({
  handler: async (actionCtx, args) => {
    // perform an action, perhaps calling a third-party API
  },
});
```

### Calling Internal Functions

Internal functions can be called from actions and scheduled from actions and mutation using the `internal` object.

For example, consider this public `upgrade` action that calls the internal `plans.markPlanAsProfessional` mutation we defined above:

```typescript
// convex/changes.ts
import { action } from "./_generated/server";
import { internal } from "./_generated/api";
import { v } from "convex/values";

export const upgrade = action({
  args: {
    planId: v.id("plans"),
  },
  handler: async (ctx, args) => {
    // Call out to payment provider (e.g. Stripe) to charge customer
    const response = await fetch("https://...");
    if (response.ok) {
      // Mark the plan as "professional" in the Convex DB
      await ctx.runMutation(internal.plans.markPlanAsProfessional, {
        planId: args.planId,
      });
    }
  },
});
```

In this example, a user should not be able to directly call `internal.plans.markPlanAsProfessional` without going through the `upgrade` action — if they did, then they would get a free upgrade.

You can define public and internal functions in the same file.

## Argument and Return Value Validation

Argument and return value validators ensure that queries, mutations, and actions are called with the correct types of arguments and return the expected types of return values.

This is important for security! Without argument validation, a malicious user can call your public functions with unexpected arguments and cause surprising results. TypeScript alone won't help because TypeScript types aren't present at runtime. We recommend adding argument validation for all public functions in production apps. For non-public functions that are not called by clients, we recommend internal functions and optionally validation.

### Example: Argument Validation

#### Adding validators

To add argument validation to your functions, pass an object with `args` and `handler` properties to the `query`, `mutation` or `action` constructor. To add return value validation, use the `returns` property in this object:

```typescript
convex/message.ts
import { mutation, query } from "./_generated/server";
import { v } from "convex/values";

export const send = mutation({
  args: {
    body: v.string(),
    author: v.string(),
  },
  returns: v.null(),
  handler: async (ctx, args) => {
    const { body, author } = args;
    await ctx.db.insert("messages", { body, author });
  },
});
```

If you define your function with an argument validator, there is no need to include TypeScript type annotations! The type of your function will be inferred automatically. Similarly, if you define a return value validator, the return type of your function will be inferred from the validator, and TypeScript will check that it matches the inferred return type of the handler function.

Unlike TypeScript, validation for an object will throw if the object contains properties that are not declared in the validator.

If the client supplies arguments not declared in `args`, or if the function returns a value that does not match the validator declared in `returns`, this is helpful to prevent bugs caused by mistyped names of arguments or returning more data than intended to a client.

Even `args: {}` is a helpful use of validators because TypeScript will show an error on the client if you try to pass any arguments to the function which doesn't expect them.

### Supported types

All functions, both public and internal, can accept and return the following data types. Each type has a corresponding validator that can be accessed on the `v` object imported from `"convex/values"`.

The database can store the exact same set of data types.

Additionally, you can also express type unions, literals, any types, and optional fields.

#### Convex values

Convex supports the following types of values:

| Convex Type | TS/JS Type | Example Usage | Validator for Argument Validation and Schemas | JSON Format for Export | Notes |
| --- | --- | --- | --- | --- | --- |
| Id | `string` | `doc._id` | `v.id(tableName)` | `string` | See Document IDs. |
| Null | `null` | `null` | `v.null()` | `null` | JavaScript's `undefined` is not a valid Convex value. Functions the return `undefined` or do not return will return `null` when called from a client. Use `null` instead. |
| Int64 | `bigint` | `3n` | `v.int64()` | `string (base10)` | Int64s only support BigInts between -2^63 and 2^63-1. Convex supports bigints in most modern browsers. |
| Float64 | `number` | `3.1` | `v.number()` | `number / string` | Convex supports all IEEE-754 double-precision floating point numbers (such as NaNs). `Inf` and `NaN` are JSON serialized as strings. |
| Boolean | `boolean` | `true` | `v.boolean()` | `bool` |  |
| String | `string` | `"abc"` | `v.string()` | `string` | Strings are stored as UTF-8 and must be valid Unicode sequences. Strings must be smaller than the 1MB total size limit when encoded as UTF-8. |
| Bytes | `ArrayBuffer` | `new ArrayBuffer(8)` | `v.bytes()` | `string (base64)` | Convex supports first class bytestrings, passed in as ArrayBuffers. Bytestrings must be smaller than the 1MB total size limit for Convex types. |
| Array | `Array` | `[1, 3.2, "abc"]` | `v.array(values)` | `array` | Arrays can have at most 8192 values. |
| Object | `Object` | `{a: "abc"}` | `v.object({property: value})` | `object` | Convex only supports "plain old JavaScript objects" (objects that do not have a custom prototype). Convex includes all enumerable properties. Objects can have at most 1024 entries. Field names must be nonempty and not start with "$" or "_". |

### Unions

You can describe fields that could be one of multiple types using `v.union`:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export default mutation({
  args: {
    stringOrNumber: v.union(v.string(), v.number()),
  },
  handler: async ({ db }, { stringOrNumber }) => {
    //...
  },
});
```

### Literals

Fields that are a constant can be expressed with `v.literal`. This is especially useful when combined with unions:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export default mutation({
  args: {
    oneTwoOrThree: v.union(
      v.literal("one"),
      v.literal("two"),
      v.literal("three"),
    ),
  },
  handler: async ({ db }, { oneTwoOrThree }) => {
    //...
  },
});
```

### Any

Fields that could take on any value can be represented with `v.any()`:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export default mutation({
  args: {
    anyValue: v.any(),
  },
  handler: async ({ db }, { anyValue }) => {
    //...
  },
});
```

This corresponds to the `any` type in TypeScript.

### Optional fields

You can describe optional fields by wrapping their type with `v.optional(...)`:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export default mutation({
  args: {
    optionalString: v.optional(v.string()),
    optionalNumber: v.optional(v.number()),
  },
  handler: async ({ db }, { optionalString, optionalNumber }) => {
    //...
  },
});
```

This corresponds to marking fields as optional with `?` in TypeScript.

### Extracting TypeScript types

The `Infer` type allows you to turn validator calls into TypeScript types. This can be useful to remove duplication between your validators and TypeScript types:

```typescript
import { mutation } from "./_generated/server";
import { Infer, v } from "convex/values";

const nestedObject = v.object({
  property: v.string(),
});

// Resolves to `{property: string}`.
export type NestedObject = Infer<typeof nestedObject>;

export default mutation({
  args: {
    nested: nestedObject,
  },
  handler: async ({ db }, { nested }) => {
    //...
  },
});
```

## Error Handling

There are four reasons why your Convex queries and mutations may hit errors:

1. **Application Errors**: The function code hits a logical condition that should stop further processing, and your code throws a `ConvexError`.
2. **Developer Errors**: There is a bug in the function (like calling `db.get(null)` instead of `db.get(id)`).
3. **Read/Write Limit Errors**: The function is retrieving or writing too much data.
4. **Internal Convex Errors**: There is a problem within Convex (like a network blip).

Convex will automatically handle internal Convex errors. If there are problems on our end, we'll automatically retry your queries and mutations until the problem is resolved and your queries and mutations succeed.

On the other hand, you must decide how to handle application, developer and read/write limit errors. When one of these errors happens, the best practices are to:

1. Show the user some appropriate UI.
2. Send the error to an exception reporting service using the Exception Reporting Integration.
3. Log the incident using `console.*` and set up reporting with Log Streaming. This can be done in addition to the above options, and doesn't require an exception to be thrown.

Additionally, you might also want to send client-side errors to a service like Sentry to capture additional information for debugging and observability.

### Errors in queries

If your query function hits an error, the error will be sent to the client and thrown from your `useQuery` call site. The best way to handle these errors is with a React error boundary component.

Error boundaries allow you to catch errors thrown in their child component tree, render fallback UI, and send information about the error to your exception handling service. Adding error boundaries to your app is a great way to handle errors in Convex query functions as well as other errors in your React components. If you are using Sentry, you can use their `Sentry.ErrorBoundary` component.

With error boundaries, you can decide how granular you'd like your fallback UI to be. One simple option is to wrap your entire application in a single error boundary like:

```jsx
<StrictMode>
  <ErrorBoundary>
    <ConvexProvider client={convex}>
      <App />
    </ConvexProvider>
  </ErrorBoundary>
</StrictMode>,
```

Then any error in any of your components will be caught by the boundary and render the same fallback UI.

On the other hand, if you'd like to enable some portions of your app to continue functioning even if other parts hit errors, you can instead wrap different parts of your app in separate error boundaries.

### Retrying

Unlike other frameworks, there is no concept of "retrying" if your query function hits an error. Because Convex functions are deterministic, if the query function hits an error, retrying will always produce the same error. There is no point in running the query function with the same arguments again.

### Errors in mutations

If a mutation hits an error, this will:

1. Cause the promise returned from your mutation call to be rejected.
2. Cause your optimistic update to be rolled back.

If you have an exception service like Sentry configured, it should report "unhandled promise rejections" like this automatically. That means that with no additional work your mutation errors should be reported.

Note that errors in mutations won't be caught by your error boundaries because the error doesn't happen as part of rendering your components.

If you would like to render UI specifically in response to a mutation failure, you can use `.catch` on your mutation call. For example:

```javascript
sendMessage(newMessageText).catch((error) => {
  // Do something with `error` here
});
```

If you're using an async handled function you can also use `try...catch`:

```javascript
try {
  await sendMessage(newMessageText);
} catch (error) {
  // Do something with `error` here
}
```

### Reporting caught errors

If you handle your mutation error, it will no longer become an unhandled promise rejection. You may need to report this error to your exception handling service manually.

### Errors in action functions

Unlike queries and mutations, actions may have side-effects and therefore can't be automatically retried by Convex when errors occur. For example, say your action sends a email. If it fails part-way through, Convex has no way of knowing if the email was already sent and can't safely retry the action. It is responsibility of the caller to handle errors raised by actions and retry if appropriate.

### Differences in error reporting between dev and prod

Using a dev deployment any server error thrown on the client will include the original error message and a server-side stack trace to ease debugging.

Using a production deployment any server error will be redacted to only include the name of the function and a generic "Server Error" message, with no stack trace. Server application errors will still include their custom data.

Both development and production deployments log full errors with stack traces which can be found on the Logs page of a given deployment.

### Application errors, expected failures

If you have expected ways your functions might fail, you can either return different values or throw `ConvexErrors`.

See [Application Errors](https://docs.convex.dev/reference/errors#application-errors).

### Read/write limit errors

To ensure uptime and guarantee performance, Convex will catch queries and mutations that try to read or write too much data. These limits are enforced at the level of a single query or mutation function execution. The limits are:

Queries and mutations error out when:

- More than 16384 documents are scanned
- More than 8MiB worth of data is scanned
- More than 4096 queries calls to `db.get` or `db.query` are made
- The function spends more than 1 second executing JavaScript

In addition, mutations error out when:

- More than 8192 documents are written
- More than 8MiB worth of data is written

Documents are "scanned" by the database to figure out which documents should be returned from `db.query`. So for example `db.query("table").take(5).collect()` will only need to scan 5 documents, but `db.query("table").filter(...).first()` might scan up to as many documents as there are in "table", to find the first one that matches the given filter.

Number of calls to `db.get` and `db.query` has a limit to prevent a single query from subscribing to too many index ranges.

In general, if you're running into these limits frequently, we recommend indexing your queries to reduce the number of documents scanned, allowing you to avoid unnecessary reads. Queries that scan large swaths of your data may look innocent at first, but can easily blow up at any production scale. If your functions are close to hitting these limits they will log a warning.

### Application Errors

If you have expected ways your functions might fail, you can either return different values or throw `ConvexErrors`.

#### Returning different values

If you're using TypeScript, different return types can enforce that you're handling error scenarios.

For example, a `createUser` mutation could return:

```typescript
Id<"users"> | { error: "EMAIL_ADDRESS_IN_USE" };
```

to express that either the mutation succeeded or the email address was already taken. This ensures that you remember to handle these cases in your UI.

#### Throwing application errors

You might prefer to throw errors for the following reasons:

1. You can use the exception bubbling mechanism to throw from a deeply nested function call, instead of manually propagating error results up the call stack. This will work for `runQuery`, `runMutation`, and `runAction` calls in actions too.
2. In mutations, throwing an error will prevent the mutation transaction from committing.
3. On the client, it might be simpler to handle all kinds of errors, both expected and unexpected, uniformly.

Convex provides an error subclass, `ConvexError`, which can be used to carry information from the backend to the client:

```typescript
// convex/myFunctions.ts
import { ConvexError } from "convex/values";
import { mutation } from "./_generated/server";

export const assignRole = mutation({
  args: {
    // ...
  },
  handler: (ctx, args) => {
    const isTaken = isRoleTaken(/* ... */);
    if (isTaken) {
      throw new ConvexError("Role is already taken");
    }
    // ...
  },
});
```

#### Application error data payload

You can pass the same data types supported by function arguments, return types, and the database, to the `ConvexError` constructor. This data will be stored on the `data` property of the error:

```typescript
// error.data === "My fancy error message"
throw new ConvexError("My fancy error message");

// error.data === {message: "My fancy error message", code: 123, severity: "high"}
throw new ConvexError({
  message: "My fancy error message",
  code: 123,
  severity: "high",
});

// error.data === {code: 123, severity: "high"}
throw new ConvexError({
  code: 123,
  severity: "high",
});
```

Error payloads more complicated than a simple string are helpful for more structured error logging, or for handling sets of errors differently on the client.

#### Handling application errors on the client

On the client, application errors also use the `ConvexError` class, and the data they carry can be accessed via the `data` property:

```typescript
// src/App.tsx
import { ConvexError } from "convex/values";
import { useMutation } from "convex/react";
import { api } from "../convex/_generated/api";

export function MyApp() {
  const doSomething = useMutation(api.myFunctions.mutateSomething);
  const handleSomething = async () => {
    try {
      await doSomething({ a: 1, b: 2 });
    } catch (error) {
      const errorMessage =
        // Check whether the error is an application error
        error instanceof ConvexError
          ? // Access data and cast it to the type we expect
            (error.data as { message: string }).message
          : // Must be some developer error,
            // and prod deployments will not
            // reveal any more information about it
            // to the client
            "Unexpected error occurred";
      // do something with `errorMessage`
    }
  };
  // ...
}
```

## Runtimes

Convex functions can run in two runtimes:

1. Default Convex runtime
2. Opt-in Node.js runtime

### Default Convex runtime

All Convex backend functions are written in JavaScript or TypeScript. By default, all functions run in a custom JavaScript runtime very similar to the Cloudflare Workers runtime with access to most web standard globals.

The default runtime has many advantages including:

- **No cold starts**: The runtime is always up and ready to handle any function at a moment's notice.
- **Latest web JavaScript standards**: The runtime is based on V8 that also powers Google Chrome. This ensures it provides an interface very similar to your frontend code, allowing further simplification to your code.
- **Low overhead access to your data**: The runtime is designed to have low overhead access to your data via query & mutation functions, allowing you to access your database via a simple JavaScript interface.

### Supported APIs

The default runtime supports most npm libraries that work in the browser, Deno, and Cloudflare workers. If your library isn't supported, you can use an action with the Node.js runtime, or reach out in Discord. We are improving support all the time.

The runtime supports the following APIs:

- **Network APIs**: `Blob`, `Event`, `EventTarget`, `fetch` (in Actions only), `File`, `FormData`, `Headers`, `Request`, `Response`
- **Encoding APIs**: `TextDecoder`, `TextEncoder`, `atob`, `btoa`
- **Web Stream APIs**: `ReadableStream`, `ReadableStreamBYOBReader`, `ReadableStreamDefaultReader`, `TransformStream`, `WritableStream`, `WritableStreamDefaultWriter`
- **Web Crypto APIs**: `crypto`, `CryptoKey`, `SubtleCrypto`

### Restrictions on queries and mutations

Query and mutation functions are further restricted by the runtime to be deterministic. This allows Convex to automatically retry them by the system as necessary.

Determinism means that no matter how many times your function is run, as long as it is given the same arguments, it will have identical side effects and return the same value.

You don't have to think all that much about maintaining these properties of determinism when you write your Convex functions. Convex will provide helpful error messages as you go, so you can't accidentally do something forbidden.

#### Using randomness and time in queries and mutations

Convex provides a "seeded" strong pseudo-random number generator at `Math.random()` so that it can guarantee the determinism of your function. The random number generator's seed is an implicit parameter to your function. Multiple calls to `Math.random()` in one function call will return different random values. Note that Convex does not reevaluate the JavaScript modules on every function run, so a call to `Math.random()` stored in a global variable will not change between function runs.

To ensure the logic within your function is reproducible, the system time used globally (outside of any function) is "frozen" at deploy time, while the system time during Convex function execution is "frozen" when the function begins. `Date.now()` will return the same result for the entirety of your function's execution. For example:

```javascript
const globalRand = Math.random(); // `globalRand` does not change between runs.
const globalNow = Date.now(); // `globalNow` is the time when Convex functions were deployed.

export const updateSomething = mutation({
  handler: () => {
    const now1 = Date.now(); // `now1` is the time when the function execution started.
    const rand1 = Math.random(); // `rand1` has a new value for each function run.
    // implementation
    const now2 = Date.now(); // `now2` === `now1`
    const rand2 = Math.random(); // `rand1` !== `rand2`
  },
});
```

### Actions

Actions are unrestricted by the same rules of determinism as query and mutation functions. Notably, actions are allowed to call third-party HTTP endpoints via the browser-standard `fetch` function.

By default, actions also run in Convex's custom JavaScript runtime with all of its advantages, including no cold starts and a browser-like API environment. They can also live in the same file as your query and mutation functions.

### Node.js runtime

Some JavaScript and TypeScript libraries use features that are not included in the default Convex runtime. This is why Convex actions provide an escape hatch to Node.js 18 via the "use node" directive at the top of a file that contains your action. [Learn more](link-to-learn-more).

Use of the Node.js environment is restricted to action functions only. If you want to use a library designed for Node.js and interact with the Convex database, you need to call the Node.js library from an action, and use `runQuery` or `runMutation` helper to call a query or mutation.

Every `.ts` and `.js` file in the `convex` directory is bundled either for the default Convex JavaScript runtime or Node.js, even if it contains no Convex functions. So a `convex/utils.ts` file that used Node.js-specific libraries would also need to have "use node" at the top to avoid warnings at bundle time.

## Bundling

Bundling is the process of gathering, optimizing, and transpiling the JS/TS source code of functions and their dependencies. During development and when deploying, the code is transformed to a format that Convex runtimes can directly and efficiently execute.

Convex currently bundles all dependencies automatically, but for the Node.js runtime you can disable bundling certain packages via the *external packages* config.

### Bundling for Convex

When you push code either via `npx convex dev` or `npx convex deploy`, the Convex CLI uses esbuild to traverse your `convex/` folder and bundle your functions and all of their used dependencies into a source code bundle. This bundle is then sent to the server.

Thanks to bundling you can write your code using both modern ECMAScript Modules (ESM) or the older CommonJS (CJS) syntax.

#### ESM vs. CJS

**ESM**:
- Is the standard for browser JavaScript
- Uses static imports via the `import` and `export` keywords (not functions) at the global scope
- Also supports dynamic imports via the asynchronous `import` function

**CJS**:
- Was previously the standard module system for Node.js
- Relies on dynamic imports via the `require` and asynchronous `import` functions for fetching external modules
- Uses the `module.exports` object for exports

### Bundling Limitations

The nature of bundling comes with a few limitations.

#### Code Size Limits

The total size of your bundled function code in your `convex/` folder is limited to 32MiB (~33.55MB). Other platform limits can be found [here](https://docs.convex.dev/limits).

While this limit in itself is quite high for just source code, certain dependencies can quickly make your bundle size cross over this limit, particularly if they are not effectively tree-shakeable (such as `aws-sdk` or `snowflake-sdk`).

You can follow these steps to debug bundle size:

1. Make sure you're using the most recent version of Convex:
   ```
   npm install convex@latest
   ```
2. Generate the bundle (this will not push code, and just generates a bundle for debugging purposes):
   ```
   npx convex dev --once --debug-bundle-path /tmp/myBundle
   ```
3. Visualize the bundle using `source-map-explorer`:
   ```
   npx source-map-explorer /tmp/myBundle/**/*.js
   ```
   Code bundled for the Convex runtime will be in the `isolate` directory, while code bundled for Node actions will be in the `node` directory.
4. Large Node dependencies can be eliminated from the bundle by marking them as *external packages*.

### Dynamic Dependencies

Some libraries rely on dynamic imports (via `import`/`require` calls) to avoid always including their dependencies. These imports are not supported by the default Convex runtime and will throw an error at runtime.

Additionally, some libraries rely on local files, which cannot be bundled by esbuild. If bundling is used, irrespective of the choice of runtime, these imports will always fail in Convex.

**Examples of Libraries with Dynamic Dependencies**:
- `langchain` relying on the presence of peer dependencies that it can dynamically import
- `sharp` relying on the presence of `libvips` binaries for image-processing operations
- `pdf-parse` relying on being dynamically imported with `require()` in order to detect if it is being run in test mode
- `tiktoken` relying on local WASM files

### External Packages

As a workaround for the bundling limitations above, Convex provides an escape hatch: *external packages*. This feature is currently exclusive to Convex's Node.js runtime.

External packages use esbuild's facility for marking a dependency as external. This tells esbuild to not bundle the external dependency at all and to leave the import as a dynamic runtime import using `require()` or `import()`. Thus, your Convex modules will rely on the underlying system having that dependency made available at execution-time.

#### External Packages are in Beta

External Packages are currently a beta feature. If you have feedback or feature requests, let us know on [Discord](https://discord.gg/convex)!

### Package Installation on the Server

Packages marked as external are installed from npm the first time you push code that uses them. The version installed matches the version installed in the `node_modules` folder on your local machine.

While this comes with a latency penalty the first time you push external packages, your packages are cached and this install step only ever needs to rerun if your external packages change. Once cached, pushes can actually be faster due to smaller source code bundles being sent to the server during pushes!

### Specifying External Packages

Create a `convex.json` file in the same directory as your `package.json` if it does not exist already. Set the `node.externalPackages` field to `["*"]` to mark all dependencies used within your Node actions as external:

```json
{
  "node": {
    "externalPackages": ["*"]
  }
}
```

Alternatively, you can explicitly specify which packages to mark as external:

```json
{
  "node": {
    "externalPackages": ["aws-sdk", "sharp"]
  }
}
```

The package identifiers should match the string used in `import`/`require` in your Node.js action.

### Troubleshooting External Packages

**Incorrect Package Versions**:
The Convex CLI searches for external packages within your local `node_modules` directory. Thus, changing the version of a package in the `package.json` will not affect the version used on the server until you've updated the package version installed in your local `node_modules` folder (e.g., running `npm install`).

**Import Errors**:
Marking a dependency as external may result in errors like this:

```
The requested module "some-module" is a CommonJs module, which may not support all module.exports as named exports. CommonJs modules can always be imported via the default export
```

This requires rewriting any imports for this module as follows:

```javascript
// ❌ old
import { Foo } from "some-module";

// ✅ new
import SomeModule from "some-module";
const { Foo } = SomeModule;
```

#### Limitations

The total size of your source code bundle and external packages cannot exceed the following:

- 45MB zipped
- 240MB unzipped

Packages that are known not to work at this time:

- Puppeteer - browser binary installation exceeds the size limit
- `@ffmpeg.wasm` - since 0.12.0, no longer supports Node environments

If there is a package that you would like working in your Convex functions, let us know.

## Debugging

Debugging is the process of figuring out why your code isn't behaving as you expect.

### Debugging during development

During development the built-in console API allows you to understand what's going on inside your functions:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const mutateSomething = mutation({
  args: { a: v.number(), b: v.number() },
  handler: (_, args) => {
    console.log("Received args", args);
    // ...
  },
});
```

The following methods are available in the default Convex runtime:

- Logging values, with a specified severity level:
  - `console.log`
  - `console.info`
  - `console.warn`
  - `console.error`
  - `console.debug`
- Logging with a stack trace:
  - `console.trace`
- Measuring execution time:
  - `console.time`
  - `console.timeLog`
  - `console.timeEnd`

The Convex backend also automatically logs all successful function executions and all errors thrown by your functions.

You can view these logs:

1. When using the ConvexReactClient, in your browser developer tools console pane. The logs are sent from your dev deployment to your client, and the client logs them to the browser. Production deployments do not send logs to the client.
2. In your Convex dashboard on the Logs page.
3. In your terminal by running `npx convex logs` or `npx convex dev --tail-logs`.

### Using a debugger

You can exercise your functions from tests, in which case you can add `debugger;` statements and step through your code. See [Testing](https://docs.convex.dev/testing).

### Debugging in production

When debugging an issue in production your options are:

1. Leverage existing logging
2. Add more logging and deploy a new version of your backend to production

Convex backend currently only preserves a limited number of logs, and logs can be erased at any time when the Convex team performs internal maintenance and upgrades. You should therefore set up log streaming and error reporting integrations to enable your team easy access to historical logs and additional information logged by your client.

### Finding relevant logs by Request ID

To find the appropriate logs for an error you or your users experience, Convex includes a Request ID in all exception messages in both dev and prod in this format: `[Request ID: <request_id>]`.

You can copy and paste a Request ID into your Convex dashboard to view the logs for functions started by that request. See the Dashboard logs page for details.

# Database

The Convex database provides a relational data model, stores JSON-like documents, and can be used with or without a schema. It "just works," giving you predictable query performance in an easy-to-use interface.

Query and mutation functions read and write data through a lightweight JavaScript API. There is nothing to set up, and no need to write any SQL. Just use JavaScript to express your app's needs.

## Getting Started

Start by learning about the basics of:

1. Tables & Documents
2. Reading Data
3. Writing Data

## Advanced Features

As your app grows more complex, you'll need more from your database:

- Relational data modeling with Document IDs
- Fast querying with Indexes
- Exposing large datasets with Paginated Queries
- Type safety by Defining a Schema
- Interoperability with data Import & Export

## Tables

Your Convex deployment contains tables that hold your app's data. Initially, your deployment contains no tables or documents.

Each table springs into existence as soon as you add the first document to it.

```javascript
// `friends` table doesn't exist.
await ctx.db.insert("friends", { name: "Jamie" });
// Now it does, and it has one document.
```

You do not have to specify a schema up front or create tables explicitly.

## Documents

Tables contain documents. Documents are very similar to JavaScript objects. They have fields and values, and you can nest arrays or objects within them.

These are all valid Convex documents:

- `{}`
- `{"name": "Jamie"}`
- `{"name": {"first": "Ari", "second": "Cole"}, "age": 60}`

They can also contain references to other documents in other tables. See [Data Types](link_to_data_types) to learn more about the types supported in Convex and [Document IDs](link_to_document_ids) to learn about how to use those types to model your data.

## Reading Data

Query and mutation functions can read data from database tables using document ids and document queries.

### Reading a Single Document

Given a single document's id you can read its data with the `db.get` method:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const getTask = query({
  args: { taskId: v.id("tasks") },
  handler: async (ctx, args) => {
    const task = await ctx.db.get(args.taskId);
    // do something with `task`
  },
});
```

*Note: You should use the `v.id` validator like in the example above to make sure you are not exposing data from tables other than the ones you intended.*

### Querying Documents

Document queries always begin by choosing the table to query with the `db.query` method:

```typescript
import { query } from "./_generated/server";

export const listTasks = query({
  handler: async (ctx) => {
    const tasks = await ctx.db.query("tasks").collect();
    // do something with `tasks`
  },
});
```

Then you can:

- filter
- order
- and await the results

### Filtering

The `filter` method allows you to restrict the documents that your document query returns. This method takes a filter constructed by `FilterBuilder` and will only select documents that match.

The examples below demonstrate some of the common uses of `filter`. You can see the full list of available filtering methods in the reference docs.

If you need to filter to documents containing some keywords, use a search query.

#### Equality Conditions

This document query finds documents in the `users` table where `doc.name === "Alex"`:

```typescript
// Get all users named "Alex".
const usersNamedAlex = await ctx.db
  .query("users")
  .filter((q) => q.eq(q.field("name"), "Alex"))
  .collect();
```

Here `q` is the `FilterBuilder` utility object. It contains methods for all of our supported filter operators.

This filter will run on all documents in the table. For each document, `q.field("name")` evaluates to the `name` property. Then `q.eq` checks if this property is equal to `"Alex"`.

If your query references a field that is missing from a given document then that field will be considered to have the value `undefined`.

#### Comparisons

Filters can also be used to compare fields against values. This document query finds documents where `doc.age >= 18`:

```typescript
// Get all users with an age of 18 or higher.
const adults = await ctx.db
  .query("users")
  .filter((q) => q.gte(q.field("age"), 18))
  .collect();
```

Here the `q.gte` operator checks if the first argument (`doc.age`) is greater than or equal to the second (`18`).

Here's the full list of comparisons:

| Operator | Equivalent TypeScript |
| --- | --- |
| `q.eq(l, r)` | `l === r` |
| `q.neq(l, r)` | `l !== r` |
| `q.lt(l, r)` | `l < r` |
| `q.lte(l, r)` | `l <= r` |
| `q.gt(l, r)` | `l > r` |
| `q.gte(l, r)` | `l >= r` |

#### Arithmetic

You can also include basic arithmetic in your queries. This document query finds documents in the `carpets` table where `doc.height * doc.width > 100`:

```typescript
// Get all carpets that have an area of over 100.
const largeCarpets = await ctx.db
  .query("carpets")
  .filter((q) => q.gt(q.mul(q.field("height"), q.field("width")), 100))
  .collect();
```

Here's the full list of arithmetic operators:

| Operator | Equivalent TypeScript |
| --- | --- |
| `q.add(l, r)` | `l + r` |
| `q.sub(l, r)` | `l - r` |
| `q.mul(l, r)` | `l * r` |
| `q.div(l, r)` | `l / r` |
| `q.mod(l, r)` | `l % r` |
| `q.neg(x)` | `-x` |

#### Combining Operators

You can construct more complex filters using methods like `q.and`, `q.or`, and `q.not`. This document query finds documents where `doc.name === "Alex" && doc.age >= 18`:

```typescript
// Get all users named "Alex" whose age is at least 18.
const adultAlexes = await ctx.db
  .query("users")
  .filter((q) =>
    q.and(q.eq(q.field("name"), "Alex"), q.gte(q.field("age"), 18)),
  )
  .collect();
```

Here is a query that finds all users where `doc.name === "Alex" || doc.name === "Emma"`:

```typescript
// Get all users named "Alex" or "Emma".
const usersNamedAlexOrEmma = await ctx.db
  .query("users")
  .filter((q) =>
    q.or(q.eq(q.field("name"), "Alex"), q.eq(q.field("name"), "Emma")),
  )
  .collect();
```

### Ordering

By default Convex always returns documents ordered by `_creationTime`.

You can use `.order("asc" | "desc")` to pick whether the order is ascending or descending. If the order isn't specified, it defaults to ascending.

```typescript
// Get all messages, oldest to newest.
const messages = await ctx.db.query("messages").order("asc").collect();

// Get all messages, newest to oldest.
const messages = await ctx.db.query("messages").order("desc").collect();
```

If you need to sort on a field other than `_creationTime` and your document query returns a small number of documents (on the order of hundreds rather than thousands of documents), consider sorting in JavaScript:

```typescript
// Get top 10 most liked messages, assuming messages is a fairly small table:
const messages = await ctx.db.query("messages").collect();
const topTenMostLikedMessages = recentMessages
  .sort((a, b) => b.likes - a.likes)
  .slice(0, 10);
```

For document queries that return larger numbers of documents, you'll want to use an index to improve the performance. Document queries that use indexes will be ordered based on the columns in the index and can avoid slow table scans.

```typescript
// Get the top 20 most liked messages of all time, using the "by_likes" index.
const messages = await ctx.db
  .query("messages")
  .withIndex("by_likes")
  .order("desc")
  .take(20);
```

See [Limit expressions with indexes](https://docs.convex.dev/docs/reference/query-api#limit-expressions-with-indexes) for details.

#### Ordering of Different Types of Values

A single field can have values of any Convex type. When there are values of different types in an indexed field, their ascending order is as follows:

`No value set (undefined) < Null (null) < Int64 (bigint) < Float64 (number) < Boolean (boolean) < String (string) < Bytes (ArrayBuffer) < Array (Array) < Object (Object)`

The same ordering is used by the filtering comparison operators `q.lt()`, `q.lte()`, `q.gt()` and `q.gte()`.

### Retrieving Results

Most of our previous examples have ended the document query with the `.collect()` method, which returns all the documents that match your filters. Here are the other options for retrieving results.

#### Taking n Results

`.take(n)` selects only the first `n` results that match your query.

```typescript
const users = await ctx.db.query("users").take(5);
```

#### Finding the First Result

`.first()` selects the first document that matches your query and returns `null` if no documents were found.

```typescript
// We expect only one user with that email address.
const userOrNull = await ctx.db
  .query("users")
  .filter((q) => q.eq(q.field("email"), "test@example.com"))
  .first();
```

#### Using a Unique Result

`.unique()` selects the single document from your query or returns `null` if no documents were found. If there are multiple results it will throw an exception.

```typescript
// Our counter table only has one document.
const counterOrNull = await ctx.db.query("counter").unique();
```

#### Loading a Page of Results

`.paginate(opts)` loads a page of results and returns a `Cursor` for loading additional results.

See [Paginated Queries](https://docs.convex.dev/docs/reference/query-api#paginated-queries) to learn more.

### More Complex Queries

Convex prefers to have a few, simple ways to walk through and select documents from tables. In Convex, there is no specific query language for complex logic like a join, an aggregation, or a group by.

Instead, you can write the complex logic in JavaScript! Convex guarantees that the results will be consistent.

### Join

Table join might look like:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const eventAttendees = query({
  args: { eventId: v.id("events") },
  handler: async (ctx, args) => {
    const event = await ctx.db.get(args.eventId);
    return Promise.all(
      (event?.attendeeIds ?? []).map((userId) => ctx.db.get(userId)),
    );
  },
});
```

### Aggregation

Here's an example of computing an average:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const averagePurchasePrice = query({
  args: { email: v.string() },
  handler: async (ctx, args) => {
    const userPurchases = await ctx.db
      .query("purchases")
      .filter((q) => q.eq(q.field("buyer"), args.email))
      .collect();
    const sum = userPurchases.reduce((a, { value: b }) => a + b, 0);
    return sum / userPurchases.length;
  },
});
```

### Group By

Here's an example of grouping and counting:

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const numPurchasesPerBuyer = query({
  args: { email: v.string() },
  handler: async (ctx, args) => {
    const userPurchases = await ctx.db.query("purchases").collect();

    return userPurchases.reduce(
      (counts, { buyer }) => ({
        ...counts,
        [buyer]: counts[buyer] ?? 0 + 1,
      }),
      {} as Record<string, number>,
    );
  },
});
```

### Querying Performance and Limits

Most of the example document queries above can lead to a full table scan. That is, for the document query to return the requested results, it might need to walk over every single document in the table.

Take this simple example:

```typescript
const tasks = await ctx.db.query("tasks").take(5);
```

This document query will not scan more than 5 documents.

On the other hand, this document query:

```typescript
const tasks = await ctx.db
  .query("tasks")
  .filter((q) => q.eq(q.field("isCompleted"), true))
  .first();
```

might need to walk over every single document in the "tasks" table just to find the first one with `isCompleted: true`.

If a table has more than a few thousand documents, you should use indexes to improve your document query performance. Otherwise, you may run into our enforced limits, detailed [here](https://docs.convex.dev/docs/reference/query-api#query-performance-and-limits).

## Writing Data

Mutations can insert, update, and remove data from database tables.

### Inserting new documents

You can create new documents in the database with the `db.insert` method:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const createTask = mutation({
  args: { text: v.string() },
  handler: async (ctx, args) => {
    const taskId = await ctx.db.insert("tasks", { text: args.text });
    // do something with `taskId`
  },
});
```

The second argument to `db.insert` is a JavaScript object with data for the new document.

The same types of values that can be passed into and returned from queries and mutations can be written into the database. See [Data Types](https://docs.convex.dev/data-types) for the full list of supported types.

The `insert` method returns a globally unique ID for the newly inserted document.

### Updating existing documents

Given an existing document ID, the document can be updated using the following methods:

1. The `db.patch` method will patch an existing document, shallow merging it with the given partial document. New fields are added. Existing fields are overwritten. Fields set to `undefined` are removed.

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const updateTask = mutation({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    const { id } = args;
    console.log(await ctx.db.get(id));
    // { text: "foo", status: { done: true }, _id: ... }

    // Add `tag` and overwrite `status`:
    await ctx.db.patch(id, { tag: "bar", status: { archived: true } });
    console.log(await ctx.db.get(id));
    // { text: "foo", tag: "bar", status: { archived: true }, _id: ... }

    // Unset `tag` by setting it to `undefined`
    await ctx.db.patch(id, { tag: undefined });
    console.log(await ctx.db.get(id));
    // { text: "foo", status: { archived: true }, _id: ... }
  },
});
```

2. The `db.replace` method will replace the existing document entirely, potentially removing existing fields:

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const replaceTask = mutation({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    const { id } = args;
    console.log(await ctx.db.get(id));
    // { text: "foo", _id: ... }

    // Replace the whole document
    await ctx.db.replace(id, { invalid: true });
    console.log(await ctx.db.get(id));
    // { invalid: true, _id: ... }
  },
});
```

### Deleting documents

Given an existing document ID, the document can be removed from the table with the `db.delete` method.

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const deleteTask = mutation({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.id);
  },
});
```

## Document IDs

Every document in Convex has a globally unique string document ID that is automatically generated by the system.

```javascript
const userId = await ctx.db.insert("users", { name: "Michael Jordan" });
```

You can use this ID to efficiently read a single document using the `get` method:

```javascript
const retrievedUser = await ctx.db.get(userId);
```

You can access the ID of a document in the `_id` field:

```javascript
const userId = retrievedUser._id;
```

Also, this same ID can be used to update that document in place:

```javascript
await ctx.db.patch(userId, { name: "Steph Curry" });
```

Convex generates an `Id` TypeScript type based on your schema that is parameterized over your table names:

```typescript
import { Id } from "./_generated/dataModel";

const userId: Id<"users"> = user._id;
```

IDs are strings at runtime, but the `Id` type can be used to distinguish IDs from other strings at compile time.

### References and relationships

In Convex, you can reference a document simply by embedding its `Id` in another document:

```javascript
await ctx.db.insert("books", {
  title,
  ownerId: user._id,
});
```

You can follow references with `ctx.db.get`:

```javascript
const user = await ctx.db.get(book.ownerId);
```

And query for documents with a reference:

```javascript
const myBooks = await ctx.db
  .query("books")
  .filter((q) => q.eq(q.field("ownerId"), user._id))
  .collect();
```

Using IDs as references can allow you to build a complex data model.

### Trading off deeply nested documents vs. relationships

While it's useful that Convex supports nested objects and arrays, you should keep documents relatively small in size. In practice, we recommend limiting Arrays to no more than 5-10 elements and avoiding deeply nested Objects.

Instead, leverage separate tables, documents, and references to structure your data. This will lead to better maintainability and performance as your project grows.

### Serializing IDs

IDs are strings, which can be easily inserted into URLs or stored outside of Convex.

You can pass an ID string from an external source (like a URL) into a Convex function and get the corresponding object. If you're using TypeScript on the client, you can cast a string to the `Id` type:

```typescript
// src/App.tsx
import { useQuery } from "convex/react";
import { Id } from "../convex/_generated/dataModel";
import { api } from "../convex/_generated/api";

export function App() {
  const id = localStorage.getItem("myIDStorage");
  const task = useQuery(api.tasks.getTask, { taskId: id as Id<"tasks"> });
  // ...
}
```

Since this ID is coming from an external source, use an argument validator or `ctx.db.normalizeId` to confirm that the ID belongs to the expected table before returning the object.

```typescript
// convex/tasks.ts
import { query } from "./_generated/server";
import { v } from "convex/values";

export const getTask = query({
  args: {
    taskId: v.id("tasks"),
  },
  handler: async (ctx, args) => {
    const task = await ctx.db.get(args.taskId);
    // ...
  },
});
```

## Data Types

All Convex documents are defined as JavaScript objects. These objects can have field values of any of the types below.

You can codify the shape of documents within your tables by defining a schema.

### Convex values

Convex supports the following types of values:

| Convex Type | TS/JS Type | Example Usage | Validator for Argument Validation and Schemas | JSON Format for Export | Notes |
| --- | --- | --- | --- | --- | --- |
| Id | `string` | `doc._id` | `v.id(tableName)` | `string` | See Document IDs. |
| Null | `null` | `null` | `v.null()` | `null` | JavaScript's `undefined` is not a valid Convex value. Functions the return `undefined` or do not return will return `null` when called from a client. Use `null` instead. |
| Int64 | `bigint` | `3n` | `v.int64()` | `string (base10)` | Int64s only support BigInts between -2^63 and 2^63-1. Convex supports bigints in most modern browsers. |
| Float64 | `number` | `3.1` | `v.number()` | `number / string` | Convex supports all IEEE-754 double-precision floating point numbers (such as NaNs). `Inf` and `NaN` are JSON serialized as strings. |
| Boolean | `boolean` | `true` | `v.boolean()` | `bool` |  |
| String | `string` | `"abc"` | `v.string()` | `string` | Strings are stored as UTF-8 and must be valid Unicode sequences. Strings must be smaller than the 1MB total size limit when encoded as UTF-8. |
| Bytes | `ArrayBuffer` | `new ArrayBuffer(8)` | `v.bytes()` | `string (base64)` | Convex supports first class bytestrings, passed in as ArrayBuffers. Bytestrings must be smaller than the 1MB total size limit for Convex types. |
| Array | `Array` | `[1, 3.2, "abc"]` | `v.array(values)` | `array` | Arrays can have at most 8192 values. |
| Object | `Object` | `{a: "abc"}` | `v.object({property: value})` | `object` | Convex only supports "plain old JavaScript objects" (objects that do not have a custom prototype). Convex includes all enumerable properties. Objects can have at most 1024 entries. Field names must be nonempty and not start with "$" or "_". |

### System fields

Every document in Convex has two automatically-generated system fields:

- `_id`: The document ID of the document.
- `_creationTime`: The time this document was created, in milliseconds since the Unix epoch.

### Limits

Convex values must be less than 1MB in total size. This is an approximate limit for now, but if you're running into these limits and would like a more precise method to calculate a document's size, reach out to us. Documents can have nested values, either objects or arrays that contain other Convex types. Convex types can have at most 16 levels of nesting, and the cumulative size of a nested tree of values must be under the 1MB limit.

Table names may contain alphanumeric characters ("a" to "z", "A" to "Z", and "0" to "9") and underscores ("_"), and they cannot start with an underscore.

For information on other limits, see [here](https://docs.convex.dev/limits).

If any of these limits don't work for you, let us know!

### Working with dates and times

Convex does not have a special data type for working with dates and times. How you store dates depends on the needs of your application:

- If you only care about a point in time, you can store a UTC timestamp. We recommend following the `_creationTime` field example, which stores the timestamp as a number in milliseconds. In your functions and on the client you can create a JavaScript `Date` by passing the timestamp to its constructor: `new Date(timeInMsSinceEpoch)`. You can then print the date and time in the desired time zone (such as your user's machine's configured time zone).
- To get the current UTC timestamp in your function and store it in the database, use `Date.now()`.
- If you care about a calendar date or a specific clock time, such as when implementing a booking app, you should store the actual date and/or time as a string. If your app supports multiple timezones you should store the timezone as well. ISO8601 is a common format for storing dates and times together in a single string like "2024-03-21T14:37:15Z". If your users can choose a specific time zone you should probably store it in a separate string field, usually using the IANA time zone name (although you could concatenate the two fields with unique character like "|").

## Schemas

A schema is a description of:

- The tables in your Convex project
- The type of documents within your tables

While it is possible to use Convex without defining a schema, adding a `schema.ts` file will ensure that the documents in your tables are the correct type. If you're using TypeScript, adding a schema will also give you end-to-end type safety throughout your app.

We recommend beginning your project without a schema for rapid prototyping and then adding a schema once you've solidified your plan. To learn more, see our [Schema Philosophy](link_to_schema_philosophy).

### Writing Schemas

Schemas are defined in a `schema.ts` file in your `convex/` directory and look like:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  messages: defineTable({
    body: v.string(),
    user: v.id("users"),
  }),
  users: defineTable({
    name: v.string(),
    tokenIdentifier: v.string(),
  }).index("by_token", ["tokenIdentifier"]),
});
```

This schema (which is based on our users and auth example), has 3 tables: `channels`, `messages`, and `users`. Each table is defined using the `defineTable` function. Within each table, the document type is defined using the validator builder, `v`. In addition to the fields listed, Convex will also automatically add `_id` and `_creationTime` fields. To learn more, see [System Fields](link_to_system_fields).

### Generating a Schema

While writing your schema, it can be helpful to consult the Convex Dashboard. The "Generate Schema" button in the "Data" view suggests a schema declaration based on the data in your tables.

### Validators

The validator builder, `v` is used to define the type of documents in each table. It has methods for each of Convex's types:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  documents: defineTable({
    id: v.id("documents"),
    string: v.string(),
    number: v.number(),
    boolean: v.boolean(),
    nestedObject: v.object({
      property: v.string(),
    }),
  }),
});
```

It additionally allows you to define unions, optional properties, string literals, and more. Argument validation and schemas both use the same validator builder, `v`.

### Optional Fields

You can describe optional fields by wrapping their type with `v.optional(...)`:

```typescript
defineTable({
  optionalString: v.optional(v.string()),
  optionalNumber: v.optional(v.number()),
});
```

This corresponds to marking fields as optional with `?` in TypeScript.

### Unions

You can describe fields that could be one of multiple types using `v.union`:

```typescript
defineTable({
  stringOrNumber: v.union(v.string(), v.number()),
});
```

If your table stores multiple different types of documents, you can use `v.union` at the top level:

```typescript
defineTable(
  v.union(
    v.object({
      kind: v.literal("StringDocument"),
      value: v.string(),
    }),
    v.object({
      kind: v.literal("NumberDocument"),
      value: v.number(),
    }),
  ),
);
```

In this schema, documents either have a `kind` of `"StringDocument"` and a `string` for their `value`, or they have a `kind` of `"NumberDocument"` and a `number` for their `value`.

### Literals

Fields that are a constant can be expressed with `v.literal`:

```typescript
defineTable({
  oneTwoOrThree: v.union(
    v.literal("one"),
    v.literal("two"),
    v.literal("three"),
  ),
});
```

### Any

Fields or documents that could take on any value can be represented with `v.any()`:

```typescript
defineTable({
  anyValue: v.any(),
});
```

This corresponds to the `any` type in TypeScript.

### Options

These options are passed as part of the `options` argument to `defineSchema`.

#### `schemaValidation: boolean`

Whether Convex should validate at runtime that your documents match your schema.

By default, Convex will enforce that all new and existing documents match your schema. You can disable `schemaValidation` by passing in `schemaValidation: false`:

```typescript
defineSchema(
  {
    // Define tables here.
  },
  {
    schemaValidation: false,
  },
);
```

When `schemaValidation` is disabled, Convex will not validate that new or existing documents match your schema. You'll still get schema-specific TypeScript types, but there will be no validation at runtime that your documents match those types.

##### `strictTableNameTypes: boolean`

Whether the TypeScript types should allow accessing tables not in the schema.

By default, the TypeScript table name types produced by your schema are strict. That means that they will be a union of strings (e.g., `"messages" | "channels"`) and only support accessing tables explicitly listed in your schema.

You can disable `strictTableNameTypes` by passing in `strictTableNameTypes: false`:

```typescript
defineSchema(
  {
    // Define tables here.
  },
  {
    strictTableNameTypes: false,
  },
);
```

When `strictTableNameTypes` is disabled, the TypeScript types will allow access to tables not listed in the schema, and their document type will be `any`.

Regardless of the value of `strictTableNameTypes`, your schema will only validate documents in the tables listed in the schema. You can still create and modify documents in other tables in JavaScript or on the dashboard (they just won't be validated).

### Schema Validation

Schemas are pushed automatically in `npx convex dev` and `npx convex deploy`.

The first push after a schema is added or modified will validate that all existing documents match the schema. If there are documents that fail validation, the push will fail.

After the schema is pushed, Convex will validate that all future document inserts and updates match the schema.

Schema validation is skipped if `schemaValidation` is set to `false`.

Note that schemas only validate documents in the tables listed in the schema. You can still create and modify documents in other tables (they just won't be validated).

#### Circular References

You might want to define a schema with circular ID references like:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    preferencesId: v.id("preferences"),
  }),
  preferences: defineTable({
    userId: v.id("users"),
  }),
});
```

In this schema, documents in the `users` table contain a reference to documents in `preferences`, and vice versa.

Because schema validation enforces your schema on every `db.insert`, `db.replace`, and `db.patch` call, creating circular references like this is not possible.

The easiest way around this is to make one of the references nullable:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    preferencesId: v.id("preferences"),
  }),
  preferences: defineTable({
    userId: v.union(v.id("users"), v.null()),
  }),
});
```

This way, you can create a `preferences` document first, then create a `user` document, and then set the reference on the `preferences` document:

```typescript
// convex/users.ts
import { mutation } from "./_generated/server";

export default mutation(async (ctx) => {
  const preferencesId = await ctx.db.insert("preferences", {});
  const userId = await ctx.db.insert("users", { preferencesId });
  await ctx.db.patch(preferencesId, { userId });
});
```

Let us know if you need better support for circular references.

#### TypeScript Types

Once you've defined a schema, `npx convex dev` will produce new versions of `dataModel.d.ts` and `server.d.ts` with types based on your schema.

##### `Doc<TableName>`

The `Doc` TypeScript type from `dataModel.d.ts` provides document types for all of your tables. You can use these both when writing Convex functions and in your React components:

```typescript
// MessageView.tsx
import { Doc } from "../convex/_generated/dataModel";

function MessageView(props: { message: Doc<"messages"> }) {
  // ...
}
```

If you need the type for a portion of a document, use the `Infer` type helper.

##### `query` and `mutation`

The `query` and `mutation` functions in `server.js` have the same API as before, but now provide a `db` with more precise types. Functions like `db.insert(table, document)` now understand your schema. Additionally, database queries will now return the correct document type (not `any`).

## Paginated Queries

Paginated queries are queries that return a list of results in incremental pages.

This can be used to build components with "Load More" buttons or "infinite scroll" UIs where more results are loaded as the user scrolls.

### Example: Paginated Messaging App

Using pagination in Convex is as simple as:

1. Writing a paginated query function that calls `.paginate(paginationOpts)`.
2. Using the `usePaginatedQuery` React hook.

Like other Convex queries, paginated queries are completely reactive.

> **Paginated queries are in beta**
>
> Paginated queries are currently a beta feature. If you have feedback or feature requests, let us know on Discord!

### Writing paginated query functions

Convex uses cursor-based pagination. This means that paginated queries return a string called a Cursor that represents the point in the results that the current page ended. To load more results, you simply call the query function again, passing in the cursor.

To build this in Convex, define a query function that:

1. Takes in a single arguments object with a `paginationOpts` property of type `PaginationOptions`.
2. `PaginationOptions` is an object with `numItems` and `cursor` fields.
3. Use `paginationOptsValidator` exported from `"convex/server"` to validate this argument.
4. The arguments object may include properties as well.
5. Calls `.paginate(paginationOpts)` on a database query, passing in the `PaginationOptions` and returning its result.
6. The returned `page` in the `PaginationResult` is an array of documents. You may map or filter it before returning it.

```typescript
// convex/messages.ts
import { v } from "convex/values";
import { query, mutation } from "./_generated/server";
import { paginationOptsValidator } from "convex/server";

export const list = query({
  args: { paginationOpts: paginationOptsValidator },
  handler: async (ctx, args) => {
    const foo = await ctx.db
      .query("messages")
      .order("desc")
      .paginate(args.paginationOpts);
    return foo;
  },
});
```

### Additional arguments

You can define paginated query functions that take arguments in addition to `paginationOpts`:

```typescript
// convex/messages.ts
export const listWithExtraArg = query({
  args: { paginationOpts: paginationOptsValidator, author: v.string() },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .filter((q) => q.eq(q.field("author"), args.author))
      .order("desc")
      .paginate(args.paginationOpts);
  },
});
```

### Transforming results

You can apply arbitrary transformations to the `page` property of the object returned by `paginate`, which contains the array of documents:

```typescript
// convex/messages.ts
export const listWithTransformation = query({
  args: { paginationOpts: paginationOptsValidator },
  handler: async (ctx, args) => {
    const results = await ctx.db
      .query("messages")
      .order("desc")
      .paginate(args.paginationOpts);
    return {
      ...results,
      page: results.page.map((message) => ({
        author: message.author.slice(0, 1),
        body: message.body.toUpperCase(),
      })),
    };
  },
});
```

### Paginating within React Components

To paginate within a React component, use the `usePaginatedQuery` hook. This hook gives you a simple interface for rendering the current items and requesting more. Internally, this hook manages the continuation cursors.

The arguments to this hook are:

1. The name of the paginated query function.
2. The arguments object to pass to the query function, excluding the `paginationOpts` (that's injected by the hook).
3. An options object with the `initialNumItems` to load on the first page.

The hook returns an object with:

- `results`: An array of the currently loaded results.
- `isLoading`: Whether the hook is currently loading results.
- `status`: The status of the pagination. The possible statuses are:
  - `"LoadingFirstPage"`: The hook is loading the first page of results.
  - `"CanLoadMore"`: This query may have more items to fetch. Call `loadMore` to fetch another page.
  - `"LoadingMore"`: We're currently loading another page of results.
  - `"Exhausted"`: We've paginated to the end of the list.
- `loadMore(n)`: A callback to fetch more results. This will only fetch more results if the status is `"CanLoadMore"`.

```typescript
// src/App.tsx
import { usePaginatedQuery } from "convex/react";
import { api } from "../convex/_generated/api";

export function App() {
  const { results, status, loadMore } = usePaginatedQuery(
    api.messages.list,
    {},
    { initialNumItems: 5 },
  );
  return (
    <div>
      {results?.map(({ _id, text }) => <div key={_id}>{text}</div>)}
      <button onClick={() => loadMore(5)} disabled={status !== "CanLoadMore"}>
        Load More
      </button>
    </div>
  );
}
```

You can also pass additional arguments in the arguments object if your function expects them:

```typescript
// src/App.tsx
import { usePaginatedQuery } from "convex/react";
import { api } from "../convex/_generated/api";

export function App() {
  const { results, status, loadMore } = usePaginatedQuery(
    api.messages.listWithExtraArg,
    { author: "Alex" },
    { initialNumItems: 5 },
  );
  return (
    <div>
      {results?.map(({ _id, text }) => <div key={_id}>{text}</div>)}
      <button onClick={() => loadMore(5)} disabled={status !== "CanLoadMore"}>
        Load More
      </button>
    </div>
  );
}
```

### Reactivity

Like any other Convex query functions, paginated queries are completely reactive. Your React components will automatically rerender if items in your paginated list are added, removed or changed.

One consequence of this is that page sizes in Convex may change! If you request a page of 10 items and then one item is removed, this page may "shrink" to only have 9 items. Similarly if new items are added, a page may "grow" beyond its initial size.

### Paginating manually

If you're paginating outside of React, you can manually call your paginated function multiple times to collect the items:

```typescript
// download.ts
import { ConvexHttpClient } from "convex/browser";
import { api } from "../convex/_generated/api";

require("dotenv").config();

const client = new ConvexHttpClient(process.env.VITE_CONVEX_URL!);

/**
 * Logs an array containing all messages from the paginated query "listMessages"
 * by combining pages of results into a single array.
 */
async function getAllMessages() {
  let continueCursor = null;
  let isDone = false;
  let page;

  const results = [];

  while (!isDone) {
    ({ continueCursor, isDone, page } = await client.query(api.messages.list, {
      paginationOpts: { numItems: 5, cursor: continueCursor },
    }));
    console.log("got", page.length);
    results.push(...page);
  }

  console.log(results);
}

getAllMessages();
```

## Indexes

Indexes are a data structure that allow you to speed up your document queries by telling Convex how to organize your documents. Indexes also allow you to change the order of documents in query results.

For a more in-depth introduction to indexing, see [Indexes and Query Performance](link_to_indexes_and_query_performance).

### Defining Indexes

Indexes are defined as part of your Convex schema. Each index consists of:

- A name
  - Must be unique per table
- An ordered list of fields to index
  - To specify a field on a nested document, use a dot-separated path like `properties.name`.

To add an index onto a table, use the `index` method on your table's schema:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

// Define a messages table with two indexes.
export default defineSchema({
  messages: defineTable({
    channel: v.id("channels"),
    body: v.string(),
    user: v.id("users"),
  })
    .index("by_channel", ["channel"])
    .index("by_channel_user", ["channel", "user"]),
});
```

The `by_channel` index is ordered by the `channel` field defined in the schema. For messages in the same channel, they are ordered by the system-generated `_creationTime` field which is added to all indexes automatically.

By contrast, the `by_channel_user` index orders messages in the same channel by the `user` who sent them, and only then by `_creationTime`.

Indexes are created in `npx convex dev` and `npx convex deploy`.

You may notice that the first deploy that defines an index is a bit slower than normal. This is because Convex needs to backfill your index. The more data in your table, the longer it will take Convex to organize it in index order. If this is problematic for your workflow, contact us.

You can feel free to query an index in the same deploy that defines it. Convex will ensure that the index is backfilled before the new query and mutation functions are registered.

### Be Careful When Removing Indexes

In addition to adding new indexes, `npx convex deploy` will delete indexes that are no longer present in your schema. Make sure that your indexes are completely unused before removing them from your schema!

### Querying Documents Using Indexes

A query for "messages in channel created 1-2 minutes ago" over the `by_channel` index would look like:

```typescript
const messages = await ctx.db
  .query("messages")
  .withIndex("by_channel", (q) =>
    q
      .eq("channel", channel)
      .gt("_creationTime", Date.now() - 2 * 60000)
      .lt("_creationTime", Date.now() - 60000),
  )
  .collect();
```

The `.withIndex` method defines which index to query and how Convex will use that index to select documents. The first argument is the name of the index and the second is an index range expression. An index range expression is a description of which documents Convex should consider when running the query.

The choice of index both affects how you write the index range expression and what order the results are returned in. For instance, by making both a `by_channel` and `by_channel_user` index, we can get results within a channel ordered by `_creationTime` or by `user`, respectively.

An index range expression is always a chained list of:

1. 0 or more equality expressions defined with `.eq`.
2. [Optionally] A lower bound expression defined with `.gt` or `.gte`.
3. [Optionally] An upper bound expression defined with `.lt` or `.lte`.

You must step through fields in index order.

The performance of your query is based on the specificity of the range. For example, if the query is:

```typescript
const messages = await ctx.db
  .query("messages")
  .withIndex("by_channel", (q) =>
    q
      .eq("channel", channel)
      .gt("_creationTime", Date.now() - 2 * 60000)
      .lt("_creationTime", Date.now() - 60000),
  )
  .collect();
```

then the query's performance would be based on the number of messages in the `channel` created 1-2 minutes ago.

If the index range is not specified, all documents in the index will be considered in the query.

### Picking a Good Index Range

For performance, define index ranges that are as specific as possible! If you are querying a large table and you're unable to add any equality conditions with `.eq`, you should consider defining a new index.

`.withIndex` is designed to only allow you to specify ranges that Convex can efficiently use your index to find. For all other filtering, you can use the `.filter` method.

For example, to query for "messages in channel not created by me" you could do:

```typescript
const messages = await ctx.db
  .query("messages")
  .withIndex("by_channel", q => q.eq("channel", channel))
  .filter(q => q.neq(q.field("user"), myUserId)
  .collect();
```

In this case, the performance of this query will be based on how many messages are in the channel. Convex will consider each message in the channel and only return the messages where the `user` field matches `myUserId`.

### Sorting with Indexes

Queries that use `withIndex` are ordered by the columns specified in the index. The order of the columns in the index dictates the priority for sorting. The values of the columns listed first in the index are compared first. Subsequent columns are only compared as tie breakers only if all earlier columns match.

Since Convex automatically includes `_creationTime` as the last column in all indexes, `_creationTime` will always be the final tie breaker if all other columns in the index are equal.

For example, `by_channel_user` includes `channel`, `user`, and `_creationTime`. So queries on messages that use `.withIndex("by_channel_user")` will be sorted first by `channel`, then by `user` within each `channel`, and finally by the creation time.

Sorting with indexes allows you to satisfy use cases like displaying the top N scoring users, the most recent N transactions, or the most N liked messages.

For example, to get the top 10 highest scoring players in your game, you might define an index on the player's highest score:

```typescript
export default defineSchema({
  players: defineTable({
    username: v.string(),
    highestScore: v.number(),
  }).index("by_highest_score", ["highestScore"]),
});

const topScoringPlayers = await ctx.db
  .query("users")
  .withIndex("by_highest_score")
  .order("desc")
  .take(10);
```

In this example, the range expression is omitted because we're looking for the highest scoring players of all time. This particular query is reasonably efficient for large data sets only because we're using `take()`.

If you use an index without a range expression, you should always use one of the following in conjunction with `withIndex`:

- `.first()`
- `.unique()`
- `.take(n)`
- `.paginate(ops)`

These APIs allow you to efficiently limit your query to a reasonable size without performing a full table scan.

### Full Table Scans

When your query fetches documents from the database, it will scan the rows in the range you specify. If you are using `.collect()`, for instance, it will scan all of the rows in the range. So if you use `withIndex` without a range expression, you will be scanning the whole table, which can be slow when your table has thousands of rows. `.filter()` doesn't affect which documents are scanned. Using `.first()` or `.unique()` or `.take(n)` will only scan rows until it has enough documents.

You can include a range expression to satisfy more targeted queries. For example, to get the top scoring players in Canada, you might use both `take()` and a range expression:

```typescript
// query the top 10 highest scoring players in Canada.
const topScoringPlayers = await ctx.db
  .query("users")
  .withIndex("by_country_highest_score", (q) => q.eq("country", "CA"))
  .order("desc")
  .take(10);
```

### Limits

Convex supports indexes containing up to 16 fields. You can define 32 indexes on each table. Indexes can't contain duplicate fields.

No reserved fields (starting with `_`) are allowed in indexes. The `_creationTime` field is automatically added to the end of every index to ensure a stable ordering. It should not be added explicitly in the index definition, and it's counted towards the index fields limit.

The `by_creation_time` index is created automatically (and is what is used in database queries that don't specify an index). The `by_id` index is reserved.

### Introduction to Indexes and Query Performance

How do I ensure my Convex database queries are fast and efficient? When should I define an index? What is an index?

This document explains how you should think about query performance in Convex by describing a simplified model of how queries and indexes function.

If you already have a strong understanding of database queries and indexes you can jump straight to the reference documentation instead:

- [Reading Data](https://docs.convex.dev/api/reading-data)
- [Indexes](https://docs.convex.dev/api/indexes)

### A Library of Documents

You can imagine that Convex is a physical library storing documents as physical books. In this world, every time you add a document to Convex with `db.insert("books", {...})` a librarian places the book on a shelf.

By default, Convex organizes your documents in the order they were inserted. You can imagine the librarian inserting documents left to right on a shelf.

If you run a query to find the first book like:

```javascript
const firstBook = await ctx.db.query("books").first();
```

then the librarian could start at the left edge of the shelf and find the first book. This is an extremely fast query because the librarian only has to look at a single book to get the result.

Similarly, if we want to retrieve the last book that was inserted we could instead do:

```javascript
const lastBook = await ctx.db.query("books").order("desc").first();
```

This is the same query but we've swapped the order to descending. In the library, this means that the librarian will start on the right edge of the shelf and scan right-to-left. The librarian still only needs to look at a single book to determine the result so this query is also extremely fast.

### Full Table Scans

Now imagine that someone shows up at the library and asks "what books do you have by Jane Austen?"

This could be expressed as:

```javascript
const books = await ctx.db
  .query("books")
  .filter((q) => q.eq(q.field("author"), "Jane Austen"))
  .collect();
```

This query is saying "look through all of the books, left-to-right, and collect the ones where the author field is Jane Austen." To do this the librarian will need to look through the entire shelf and check the author of every book.

This query is a full table scan because it requires Convex to look at every document in the table. The performance of this query is based on the number of books in the library.

If your Convex table has a small number of documents, this is fine! Full table scans should still be fast if there are a few hundred documents, but if the table has many thousands of documents these queries will become slow.

In the library analogy, this kind of query is fine if the library has a single shelf. As the library expands into a bookcase with many shelves or many bookcases, this approach becomes infeasible.

### Card Catalogs

How can we more efficiently find books given an author?

One option is to re-sort the entire library by author. This will solve our immediate problem but now our original queries for `firstBook` and `lastBook` would become full table scans because we'd need to examine every book to see which was inserted first/last.

Another option is to duplicate the entire library. We could purchase 2 copies of every book and put them on 2 separate shelves: one shelf sorted by insertion time and another sorted by author. This would work, but it's expensive. We now need twice as much space for our library.

A better option is to build an index on author. In the library, we could use an old-school card catalog to organize the books by author. The idea here is that the librarian will write an index card for each book that contains:

- The book's author
- The location of the book on the shelves

These index cards will be sorted by author and live in a separate organizer from the shelves that hold the books. The card catalog should stay small because it only has an index card per book (not the entire text of the book).

![Card Catalog](https://docs.convex.dev/images/card-catalog.png)

When a patron asks for "books by Jane Austen", the librarian can now:

1. Go to the card catalog and quickly find all of the cards for "Jane Austen".
2. For each card, go and find the book on the shelf.

This is quite fast because the librarian can quickly find the index cards for Jane Austen. It's still a little bit of work to find the book for each card but the number of index cards is small so this is quite fast.

### Indexes

Database indexes work based on the same concept! With Convex you can define an index with:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  books: defineTable({
    author: v.string(),
    title: v.string(),
    text: v.string(),
  }).index("by_author", ["author"]),
});
```

then Convex will create a new index called `by_author` on `author`. This means that your `books` table will now have an additional data structure that is sorted by the `author` field.

You can query this index with:

```javascript
const austenBooks = await ctx.db
  .query("books")
  .withIndex("by_author", (q) => q.eq("author", "Jane Austen"))
  .collect();
```

This query instructs Convex to go to the `by_author` index and find all the entries where `doc.author === "Jane Austen"`. Because the index is sorted by author, this is a very efficient operation. This means that Convex can execute this query in the same manner that the librarian can:

1. Find the range of the index with entries for Jane Austen.
2. For each entry in that range, get the corresponding document.

The performance of this query is based on the number of documents where `doc.author === "Jane Austen"` which should be quite small. We've dramatically sped up the query!

### Backfilling and Maintaining Indexes

One interesting detail to think about is the work needed to create this new structure. In the library, the librarian must go through every book on the shelf and put a new index card for each one in the card catalog sorted by author. Only after that can the librarian trust that the card catalog will give it correct results.

The same is true for Convex indexes! When you define a new index, the first time you run `npx convex deploy` Convex will need to loop through all of your documents and index each one. This is why the first deploy after the creation of a new index will be slightly slower than normal; Convex has to do a bit of work for each document in your table.

Similarly, even after an index is defined, Convex will have to do a bit of extra work to keep this index up to date as the data changes. Every time a document is inserted, updated, or deleted in an indexed table, Convex will also update its index entry. This is analogous to a librarian creating new index cards for new books as they add them to the library.

If you are defining a few indexes there is no need to worry about the maintenance cost. As you define more indexes, the cost to maintain them grows because every insert needs to update every index. This is why Convex has a limit of 32 indexes per table. In practice most applications define a handful of indexes per table to make their important queries efficient.

### Indexing Multiple Fields

Now imagine that a patron shows up at the library and would like to check out *Foundation* by Isaac Asimov. Given our index on author, we can write a query that uses the index to find all the books by Isaac Asimov and then examines the title of each book to see if it's *Foundation*.

```javascript
const foundation = await ctx.db
  .query("books")
  .withIndex("by_author", (q) => q.eq("author", "Isaac Asimov"))
  .filter((q) => q.eq(q.field("title"), "Foundation"))
  .unique();
```

This query describes how a librarian might execute the query. The librarian will use the card catalog to find all of the index cards for Isaac Asimov's books. The cards themselves don't have the title of the book so the librarian will need to find every Asimov book on the shelves and look at its title to find the one named *Foundation*. Lastly, this query ends with `.unique()` because we expect there to be at most one result.

This query demonstrates the difference between filtering using `withIndex` and `filter`. `withIndex` only allows you to restrict your query based on the index. You can only do operations that the index can do efficiently like finding all documents with a given author.

`filter` on the other hand allows you to write arbitrary, complex expressions but it won't be run using the index. Instead, `filter` expressions will be evaluated on every document in the range.

Given all of this, we can conclude that the performance of indexed queries is based on how many documents are in the index range. In this case, the performance is based on the number of Isaac Asimov books because the librarian will need to look at each one to examine its title.

Unfortunately, Isaac Asimov wrote a lot of books. Realistically even with 500+ books, this will be fast enough on Convex with the existing index, but let's consider how we could improve it anyway.

One approach is to build a separate `by_title` index on `title`. This could let us swap the work we do in `.filter` and `.withIndex` to instead be:

```javascript
const foundation = await ctx.db
  .query("books")
  .withIndex("by_title", (q) => q.eq("title", "Foundation"))
  .filter((q) => q.eq(q.field("author"), "Isaac Asimov"))
  .unique();
```

In this query, we're efficiently using the index to find all the books called *Foundation* and then filtering through to find the one by Isaac Asimov.

This is okay, but we're still at risk of having a slow query because too many books have a title of *Foundation*. An even better approach could be to build a compound index that indexes both author and title. Compound indexes are indexes on an ordered list of fields.

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  books: defineTable({
    author: v.string(),
    title: v.string(),
    text: v.string(),
  }).index("by_author_title", ["author", "title"]),
});
```

In this index, books are sorted first by the author and then within each author by title. This means that a librarian can use the index to jump to the Isaac Asimov section and quickly find *Foundation* within it.

Expressing this as a Convex query this looks like:

```javascript
const foundation = await ctx.db
  .query("books")
  .withIndex("by_author_title", (q) =>
    q.eq("author", "Isaac Asimov").eq("title", "Foundation"),
  )
  .unique();
```

Here the index range expression tells Convex to only consider documents where the author is Isaac Asimov and the title is *Foundation*. This is only a single document so this query will be quite fast!

Because this index sorts by author and then by title, it also efficiently supports queries like "All books by Isaac Asimov that start with F." We could express this as:

```javascript
const asimovBooksStartingWithF = await ctx.db
  .query("books")
  .withIndex("by_author_title", (q) =>
    q.eq("author", "Isaac Asimov").gte("title", "F").lt("title", "G"),
  )
  .collect();
```

This query uses the index to find books where `author === "Isaac Asimov" && "F" <= title < "G"`. Once again, the performance of this query is based on how many documents are in the index range. In this case, that's just the Asimov books that begin with "F" which is quite small.

Also note that this index also supports our original query for "books by Jane Austen." It's okay to only use the author field in an index range expression and not restrict by title at all.

Lastly, imagine that a library patron asks for the book *The Three-Body Problem* but they don't know the author's name. Our `by_author_title` index won't help us here because it's sorted first by author, and then by title. The title, *The Three-Body Problem*, could appear anywhere in the index!

The Convex TypeScript types in the `withIndex` make this clear because they require that you compare index fields in order. Because the index is defined on `["author", "title"]`, you must first compare the author with `.eq` before the title.

In this case, the best option is probably to create the separate `by_title` index to facilitate this query.

### Conclusions

Congrats! You now understand how queries and indexes work within Convex!

Here are the main points we've covered:

- By default Convex queries are full table scans. This is appropriate for prototyping and querying small tables.
- As your tables grow larger, you can improve your query performance by adding indexes. Indexes are separate data structures that order your documents for fast querying.
- In Convex, queries use the `withIndex` method to express the portion of the query that uses the index. The performance of a query is based on how many documents are in the index range expression.
- Convex also supports compound indexes that index multiple fields.

## Import & Export

If you're bootstrapping your app from existing data, Convex provides three ways to get the data in:

1. One-shot import into a single table via the CLI.
2. One-shot import of all tables via the CLI.
3. Streaming import from any existing database via Fivetran or Airbyte destination connectors.

You can export data from Convex in two ways:

1. Snapshot export via the dashboard. Great for running a quick analysis.
2. Streaming export to any external database via Fivetran or Airbyte. Great for connecting to a custom BI setup (e.g., Snowflake, Databricks, or BigQuery).

## Exporting Data Snapshots

You can export your data from Convex using Snapshot export, which you can find in the settings page of your project in the dashboard.

Alternatively, you can export the same data with the command line:

```
npx convex export --path ~/Downloads
```

### Snapshot export is in beta

Snapshot export is currently a beta feature. If you have feedback or feature requests, let us know on [Discord](https://discord.com)!

When you request an export, Convex will generate a ZIP file with all documents in all Convex tables in your deployment. This may take a few seconds or a few minutes, depending on how much data is in your deployment. You can download the ZIP file by clicking on the link in the Latest Snapshot table.

Each export is a consistent snapshot of your data at the time of your request. The ZIP file's name has the format `snapshot_{ts}.zip` where `ts` is a UNIX timestamp of the snapshot in nanoseconds. The export ZIP file contains documents for each table at `<table_name>/documents.jsonl`, with one document per line.

Exported ZIP files also optionally contain data from file storage in a `_storage` folder, with metadata like IDs and checksums in `_storage/documents.jsonl` and each file as `_storage/<id>`.

### Latest Export View

### Using the snapshot

Exported ZIP files can be imported into the same deployment or a different deployment with the CLI:

```
npx convex import path/to/snapshot.zip
```

### What the snapshot doesn't contain

The snapshot ZIP file only contains the documents for your tables. In particular it lacks:

- Your deployment's code and configuration. Convex functions, `crons.ts`, `auth.config.js`, `schema.ts`, etc. are configured in your source code.
- Pending scheduled functions. You can access pending scheduled functions in the `_scheduled_functions` system table.
- Environment variables. Environment variables can be copied from Settings in the Convex dashboard.

### FAQ

**Can I use snapshot export as an automated backup?**

Right now, you can only access one export at a time, so this is not an automated backup system. You are welcome to download your tables routinely, and feel free to chime in on [Discord](https://discord.com) if you would like to see better support for backups.

**Are there any limitations?**

Each export is accessible for up to 14 days (if no more exports are requested). There is no limit to how many exports you can request.

Snapshot export uses database bandwidth to read all documents, and file bandwidth if the export includes file storage. You can observe this bandwidth in the usage dashboard as function name `_cli/export`. Check the limits docs for pricing details.

## Importing Data Snapshots

You can import data into Convex from a local file using the command line.

```
npx convex import
```

### Single table import

```
npx convex import --table <tableName> <path>
```

Import a CSV, JSON, or JSONLines file into a Convex table.

- `.csv` files must have a header, and each row's entries are interpreted either as a (floating point) number or a string.
- `.jsonl` files must have a JSON object per line.
- `.json` files must be an array of JSON objects.

JSON arrays have a size limit of 8MiB. To import more data, use CSV or JSONLines. You can convert json to jsonl with a command like `jq -c '.[]' data.json > data.jsonl`.

Imports into a table with existing data will fail by default, but you can specify `--append` to append the imported rows to the table or `--replace` to replace existing data in the table with your import.

The default is to import into your dev deployment. Use `--prod` to import to your production deployment or `--preview-name` to import into a preview deployment.

### Import data from a snapshot ZIP file

```
npx convex import <path>.zip
```

Import a ZIP file into a Convex deployment, where the ZIP file has been downloaded from Snapshot Export in the dashboard. Documents will retain their `_id` and `_creationTime` fields so references between tables are maintained.

Imports where tables have existing data will fail by default, but you can specify `--replace` to replace existing data in tables mentioned in the ZIP file.

### Use cases

1. Restore a deployment from backup. Download a snapshot export when tables are in a good state, and restore from this backup if needed.
   ```
   npx convex import --prod --replace backup_snapshot.zip
   ```

2. Seed dev deployments with sample data, exported from prod or another dev deployment.
   ```
   npx convex import seed_snapshot.zip
   ```

3. Seed preview deployments with sample data, exported from prod, dev, or another preview deployment. Example for Vercel, seeding data from `seed_snapshot.zip` committed in the root of the repo.
   ```
   npx convex deploy --cmd 'npm run build' &&
   if [ "$VERCEL_ENV" == "preview" ]; then
   npx convex import --preview-name "$VERCEL_GIT_COMMIT_REF" seed_snapshot.zip;
   fi
   ```

4. Clear a table efficiently with an empty snapshot import.
   ```
   touch empty_file.jsonl
   npx convex import --replace --table <tableNameToClear> empty_file.jsonl
   ```

### Features

- Snapshot import is the only way to create documents with pre-existing `_id` and `_creationTime` fields.
- The `_id` field must match Convex's ID format.
- If `_id` or `_creationTime` are not provided, new values are chosen during import.
- Snapshot import creates and replaces tables atomically (except when using `--append`).
- Queries and mutations will not view intermediate states where partial data is imported.
- Indexes and schemas will work on the new data without needing time for re-backfilling or re-validating.
- Snapshot import only affects tables that are mentioned in the import, either by `--table` or as entries in the ZIP file.
- While JSON and JSONLines can import arbitrary JSON values, ZIP imports can additionally import other Convex values: `Int64`, `Bytes`, etc. Types are preserved in the ZIP file through the `generated_schema.jsonl` file.
- Snapshot import of ZIP files that include file storage import the files and preserve `_storage` documents, including their `_id`, `_creationTime`, and `contentType` fields.

### Warnings

- Streaming Export (Fivetran or Airbyte) does not understand snapshot imports, similar to table deletion and creation and some schema changes. We recommend resetting streaming export sync after a snapshot import.
- Avoid changing the ZIP file between downloading it from Snapshot Export and importing it with `npx convex import`. Some manual changes of the ZIP file may be possible, but remain undocumented. Please share your use case and check with the Convex team in Discord.
- Snapshot import is not always supported when importing into a deployment that was created before Convex version 1.7. The import may work, especially when importing a ZIP snapshot from a deployment created around the same time as the target deployment. As a special case, snapshot import can always restore a deployment from its own backup. Reach out in Discord if you encounter issues, as there may be a workaround.
- Snapshot import uses database bandwidth to write all documents, and file bandwidth if the export includes file storage. You can observe this bandwidth in the usage dashboard as function name `_cli/import` and associated cost in the limits docs.

## Advanced

### System Tables

System tables enable read-only access to metadata for built-in Convex features. Currently there are two system tables exposed:

1. The "_scheduled_functions" table contains metadata for scheduled functions.
2. The "_storage" table contains metadata for stored files.

You can read data from system tables using the `db.system.get` and `db.system.query` methods, which work the same as the standard `db.get` and `db.query` methods. Queries reading from system tables are reactive and realtime just like queries reading from all other tables, and pagination can be used to enumerate all documents even when there are too many to read in a single query.

### Schema Philosophy

With Convex there is no need to write any `CREATE TABLE` statements, or think through your stored table structure ahead of time so you can name your fields and types. You simply put your objects into Convex and keep building your app!

However, moving fast early can be problematic later. "*Was that field a number or a string? I think I changed it when I fixed that one bug?*"

Storage systems which are too permissive can sometimes become liabilities as your system matures and you want to be able to reason assuredly about exactly what data is in your system.

The good news is Convex is always typed. It's just *implicitly* typed! When you submit a document to Convex, it tracks all the types of all the fields in your document. You can go to your dashboard and view the inferred schema of any table to understand what you've ended up with.

"*What about that field I changed from a string to a number?*" Convex can handle this too. Convex will track those changes, in this case the field is a union like `v.union(v.number(), v.string())`. That way even when you change your mind about your documents' fields and types, Convex has your back.

Once you are ready to formalize your schema, you can define it using our schema builder to enable schema validation and generate types based on it.

### OCC and Atomicity

In Queries, we mentioned that determinism as important in the way optimistic concurrency control (OCC) was used within Convex. In this section, we'll dive much deeper into why.

### Convex Financial, Inc.

Imagine that you're building a banking app, and therefore your databases stores accounts with balances. You want your users to be able to give each other money, so you write a mutation function that transfers funds from one user's account to another.

One run of that transaction might read Alice's account balance, and then Bob's. You then propose to deduct $5 from Alice's account and increase Bob's balance by the same $5.

Here's our pseudocode:

```
$14 <- READ Alice
$11 <- READ Bob
WRITE Alice $9
WRITE Bob $16
```

This ledger balance transfer is a classic database scenario that requires a guarantee that these write operations will only apply together. It is a really bad thing if only one operation succeeds!

```
$14 <- READ Alice
$11 <- READ Bob
WRITE Alice $9
*crash* // $5 lost from your bank
```

You need a guarantee that this can never happen. You require transaction atomicity, and Convex provides it.

The problem of data correctness is much deeper. Concurrent transactions that read and edit the same records can create data races.

In the case of our app it's entirely possible that someone deducts Alice's balance right after we read it. Maybe she bought a Coke Zero at the airport with her debit card for $3.

```
$5 Transfer                           $3 Debit Card Charge
----------------------------------------------------------
$14 <- READ Alice
$11 <- READ Bob
                                        $14 <- READ Alice
                                        WRITE Alice $11
WRITE Alice $9 // Free coke!
WRITE Bob $16
```

Clearly, we need to prevent these types of data races from happening. We need a way to handle these concurrent conflicts. Generally, there are two common approaches.

Most traditional databases choose a *pessimistic locking strategy*. (Pessimism in this case means the strategy assumes conflict will happen ahead of time so seeks to prevent it.) With pessimistic locking, you first need to acquire a lock on Alice's record, and then acquire a lock on Bob's record. Then you can proceed to conduct your transaction, knowing that any other transaction that needed to touch those records will wait until you are done and all your writes are committed.

After decades of experience, the drawbacks of pessimistic locking are well understood and undeniable. The biggest limitation arises from real-life networks and computers being inherently unreliable. If the lock holder goes missing for whatever reason half way through its transaction, everyone else that wants to modify any of those records is waiting indefinitely. *Not good!*

*Optimistic concurrency control* is, as the name states, optimistic. It assumes the transaction will succeed and doesn't worry about locking anything ahead of time. Very brash! How can it be so sure?

It does this by treating the transaction as a declarative proposal to write records on the basis of any read record versions (the "read set"). At the end of the transaction, the writes all commit if every version in the read set is still the latest version of that record. This means no concurrent conflict occurred.

Now using our version read set, let's see how OCC would have prevented the soda-catastrophe above:

```
$5 Transfer                           $3 Debit Card Charge
----------------------------------------------------------
(v1, $14) <- READ Alice
(v7, $11) <- READ Bob
                                        (v1, $14) <- READ Alice
                                        WRITE Alice $11
                                        IF Alice.v = v1

WRITE Alice = $9, Bob = $16
    IF Alice.v = v1, Bob.v = v7 // Fails! Alice is = v2
```

This is akin to being unable to push your Git repository because you're not at HEAD. We all know in that circumstance, we need to pull, and rebase or merge, etc.

### When OCC Loses, Determinism Wins

A naive optimistic concurrency control solution would be to solve this the same way that Git does: require the user/application to resolve the conflict and determine if it is safe to retry.

In Convex, however, we don't need to do that. We know the transaction is deterministic. It didn't charge money to Stripe, it didn't write a permanent value out to the filesystem. It had no effect at all other than proposing some atomic changes to Convex tables that were not applied.

The determinism means that we can simply re-run the transaction; you never need to worry about temporary data races. We can run several retries if necessary until we succeed to execute the transaction without any conflicts.

> In fact, the Git analogy stays very apt. An OCC conflict means we cannot push because our HEAD is out of date, so we need to rebase our changes and try again. And determinism is what guarantees there is never a "merge conflict", so (unlike with Git) this rebase operation will always eventually succeed without developer intervention.

### Snapshot Isolation vs Serializability

It is common for optimistic multi-version concurrency control databases to provide a guarantee of *snapshot isolation*. This isolation level provides the illusion that all transactions execute on an atomic snapshot of the data but it is vulnerable to anomalies where certain combinations of concurrent transactions can yield incorrect results. The implementation of optimistic concurrency control in Convex instead provides *true serializability* and will yield correct results regardless of what transactions are issued concurrently.

### No Need to Think About This

The beauty of this approach is that you can simply write your mutation functions as if they will always succeed, and always be guaranteed to be atomic.

Aside from sheer curiosity about how Convex works, day to day there's no need to worry about conflicts, locking, or atomicity when you make changes to your tables and documents. The "obvious way" to write your mutation functions will just work.

# Realtime

Turns out Convex is automatically realtime! You don't have to do anything special if you are already using query functions, database, and client libraries in your app. Convex tracks the dependencies to your query functions, including database changes, and triggers the subscription in the client libraries.

# Authentication

## Convex & Clerk

Clerk is an authentication platform providing login via passwords, social identity providers, one-time email or SMS access codes, and multi-factor authentication and basic user management.

If you're using Next.js, see the [Next.js setup guide](https://docs.clerk.com/docs/nextjs-setup).

### Get started

This guide assumes you already have a working React app with Convex. If not, follow the [Convex React Quickstart](https://docs.convex.dev/quickstart/react) first. Then:

1. **Sign up for Clerk**
   Sign up for a free Clerk account at [clerk.com/sign-up](https://clerk.com/sign-up).

2. **Create an application in Clerk**
   Choose how you want your users to sign in.

3. **Create a JWT Template**
   In the JWT Templates section of the Clerk dashboard, tap on + New template and choose Convex.
   Copy the Issuer URL from the Issuer input field.
   Hit Apply Changes.

   *Note: Do NOT rename the JWT token, it must be called `convex`.*

4. **Create the auth config**
   In the `convex` folder, create a new file `auth.config.ts` with the server-side configuration for validating access tokens.

   ```typescript
   export default {
     providers: [
       {
         domain: "https://your-issuer-url.clerk.accounts.dev/",
         applicationID: "convex",
       },
     ]
   };
   ```

5. **Deploy your changes**
   Run `npx convex dev` to automatically sync your configuration to your backend.

6. **Install Clerk**
   In a new terminal window, install the Clerk React library:
   ```
   npm install @clerk/clerk-react
   ```

7. **Get your Clerk Publishable key**
   On the Clerk dashboard, in the API Keys section, copy the Publishable key.

8. **Configure `ConvexProviderWithClerk`**
   Now replace your `ConvexProvider` with `ClerkProvider` wrapping `ConvexProviderWithClerk`.

   Pass the Clerk `useAuth` hook to the `ConvexProviderWithClerk`.

   Paste the Publishable key as a prop to `ClerkProvider`.

   ```typescript
   import React from "react";
   import ReactDOM from "react-dom/client";
   import App from "./App";
   import "./index.css";
   import { ClerkProvider, useAuth } from "@clerk/clerk-react";
   import { ConvexProviderWithClerk } from "convex/react-clerk";
   import { ConvexReactClient } from "convex/react";

   const convex = new ConvexReactClient(import.meta.env.VITE_CONVEX_URL as string);

   ReactDOM.createRoot(document.getElementById("root")!).render(
     <React.StrictMode>
       <ClerkProvider publishableKey="pk_test_...">
         <ConvexProviderWithClerk client={convex} useAuth={useAuth}>
           <App />
         </ConvexProviderWithClerk>
       </ClerkProvider>
     </React.StrictMode>,
   );
   ```

### Show UI based on authentication state

You can control which UI is shown when the user is signed in or signed out with the provided components from `"convex/react"` and `"@clerk/clerk-react"`.

To get started, create a shell that will let the user sign in and sign out.

Because the `Content` component is a child of `Authenticated`, within it and any of its children, authentication is guaranteed, and Convex queries can require it.

```typescript
import { SignInButton, UserButton } from "@clerk/clerk-react";
import { Authenticated, Unauthenticated, useQuery } from "convex/react";
import { api } from "../convex/_generated/api";

function App() {
  return (
    <main>
      <Unauthenticated>
        <SignInButton />
      </Unauthenticated>
      <Authenticated>
        <UserButton />
        <Content />
      </Authenticated>
    </main>
  );
}

function Content() {
  const messages = useQuery(api.messages.getForCurrentUser);
  return <div>Authenticated content: {messages?.length}</div>;
}

export default App;
```

### Use authentication state in your Convex functions

If the client is authenticated, you can access the information stored in the JWT via `ctx.auth.getUserIdentity`.

If the client isn't authenticated, `ctx.auth.getUserIdentity` will return `null`.

Make sure that the component calling this query is a child of `Authenticated` from `"convex/react"`, otherwise it will throw on page load.

```typescript
import { query } from "./_generated/server";

export const getForCurrentUser = query({
  args: {},
  handler: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    if (identity === null) {
      throw new Error("Not authenticated");
    }
    return await ctx.db
      .query("messages")
      .filter((q) => q.eq(q.field("author"), identity.email))
      .collect();
  },
});
```

### Login and logout Flows

Now that you have everything set up, you can use the `SignInButton` component to create a login flow for your app.

The login button will open the Clerk-configured login dialog:

```typescript
import { SignInButton } from "@clerk/clerk-react";

function App() {
  return (
    <div className="App">
      <SignInButton mode="modal" />
    </div>
  );
}
```

To enable a logout flow, you can use the `SignOutButton` or the `UserButton`, which opens a dialog that includes a sign-out button.

### Logged-in and logged-out views

Use the `useConvexAuth()` hook instead of Clerk's `useAuth` hook when you need to check whether the user is logged in or not. The `useConvexAuth` hook makes sure that the browser has fetched the auth token needed to make authenticated requests to your Convex backend, and that the Convex backend has validated it:

```typescript
import { useConvexAuth } from "convex/react";

function App() {
  const { isLoading, isAuthenticated } = useConvexAuth();

  return (
    <div className="App">
      {isAuthenticated ? "Logged in" : "Logged out or still loading"}
    </div>
  );
}
```

You can also use the `Authenticated`, `Unauthenticated`, and `AuthLoading` helper components instead of the similarly named Clerk components. These components use the `useConvex` hook under the hood:

```typescript
import { Authenticated, Unauthenticated, AuthLoading } from "convex/react";

function App() {
  return (
    <div className="App">
      <Authenticated>Logged in</Authenticated>
      <Unauthenticated>Logged out</Unauthenticated>
      <AuthLoading>Still loading</AuthLoading>
    </div>
  );
}
```

### User information in React

You can access information about the authenticated user, like their name, from Clerk's `useUser` hook. See the [User doc](https://docs.clerk.com/docs/user) for the list of available fields:

```typescript
import { useUser } from "@clerk/clerk-react";

export default function Badge() {
  const { user } = useUser();
  return <span>Logged in as {user.fullName}</span>;
}
```

### Configuring dev and prod instances

To configure a different Clerk instance between your Convex development and production deployments, you can use environment variables configured on the Convex dashboard.

#### Configuring the backend

First, change your `auth.config.ts` file to use an environment variable:

```typescript
export default {
  providers: [
    {
      domain: process.env.CLERK_JWT_ISSUER_DOMAIN,
      applicationID: "convex",
    },
  ],
};
```

**Development configuration**

Open the Settings for your dev deployment on the Convex dashboard and add the variables there:

![Convex dashboard dev deployment settings](https://docs.convex.dev/images/clerk-dev-config.png)

Now switch to the new configuration by running `npx convex dev`.

**Production configuration**

Similarly, on the Convex dashboard, switch to your production deployment in the left-side menu and set the values for your production Clerk instance there.

Now switch to the new configuration by running `npx convex deploy`.

#### Configuring a React client

To configure your client, you can use environment variables as well. The exact name of the environment variables and the way to refer to them depend on each client platform (Vite vs. Next.js, etc.). Refer to our corresponding Quickstart or the relevant documentation for the platform you're using.

Change the props to `ClerkProvider` to take in an environment variable:

```typescript
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import "./index.css";
import { ClerkProvider, useAuth } from "@clerk/clerk-react";
import { ConvexProviderWithClerk } from "convex/react-clerk";
import { ConvexReactClient } from "convex/react";

const convex = new ConvexReactClient(import.meta.env.VITE_CONVEX_URL as string);

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <ClerkProvider publishableKey={import.meta.env.VITE_CLERK_PUBLISHABLE_KEY}>
      <ConvexProviderWithClerk client={convex} useAuth={useAuth}>
        <App />
      </ConvexProviderWithClerk>
    </ClerkProvider>
  </React.StrictMode>,
);
```

**Development configuration**

Use the `.env.local` or `.env` file to configure your client when running locally. The name of the environment variables file depends on each client platform (Vite vs. Next.js, etc.). Refer to our corresponding Quickstart or the relevant documentation for the platform you're using:

`.env.local`
```
VITE_CLERK_PUBLISHABLE_KEY="pk_test_..."
```

**Production configuration**

Set the environment variable in your production environment depending on your hosting platform. See [Hosting](https://docs.convex.dev/hosting).

### Debugging authentication

If a user goes through the Clerk login flow successfully, and after being redirected back to your page, `useConvexAuth` gives `isAuthenticated: false`, it's possible that your backend isn't correctly configured.

The `auth.config.ts` file in your `convex/` directory contains a list of configured authentication providers. You must run `npx convex dev` or `npx convex deploy` after adding a new provider to sync the configuration to your backend.

For more thorough debugging steps, see [Debugging Authentication](https://docs.convex.dev/authentication/debugging).

### Under the hood

The authentication flow looks like this under the hood:

1. The user clicks a login button.
2. The user is redirected to a page where they log in via whatever method you configure in Clerk.
3. After a successful login, Clerk redirects back to your page, or a different page which you configure via the `afterSignIn` prop.
4. The `ClerkProvider` now knows that the user is authenticated.
5. The `ConvexProviderWithClerk` fetches an auth token from Clerk.
6. The `ConvexReactClient` passes this token down to your Convex backend to validate.
7. Your Convex backend retrieves the public key from Clerk to check that the token's signature is valid.
8. The `ConvexReactClient` is notified of successful authentication, and `ConvexProviderWithClerk` now knows that the user is authenticated with Convex. `useConvexAuth` returns `isAuthenticated: true`, and the `Authenticated` component renders its children.
9. `ConvexProviderWithClerk` takes care of refetching the token when needed to make sure the user stays authenticated with your backend.


## Auth in Functions

Within a Convex function, you can access information about the currently logged-in user by using the `auth` property of the `QueryCtx`, `MutationCtx`, or `ActionCtx` object:

```typescript
// convex/myFunctions.ts
import { mutation } from "./_generated/server";

export const myMutation = mutation({
  args: {
    // ...
  },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (identity === null) {
      throw new Error("Unauthenticated call to mutation");
    }
    //...
  },
});
```

### User identity fields

The `UserIdentity` object returned by `getUserIdentity` is guaranteed to have `tokenIdentifier`, `subject`, and `issuer` fields. Which other fields it will include depends on the identity provider used and the configuration of JWT tokens and OpenID scopes.

`tokenIdentifier` is a combination of `subject` and `issuer` to ensure uniqueness even when multiple providers are used.

If you followed one of our integrations with Clerk or Auth0, at least the following fields will be present: `familyName`, `givenName`, `nickname`, `pictureUrl`, `updatedAt`, `email`, `emailVerified`. See their corresponding standard definition in the OpenID docs.

```typescript
// convex/myFunctions.ts
import { mutation } from "./_generated/server";

export const myMutation = mutation({
  args: {
    // ...
  },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    const { tokenIdentifier, name, email } = identity!;
    //...
  },
});
```

### Clerk claims configuration

If you're using Clerk, the fields returned by `getUserIdentity` are determined by your JWT template's Claims config. If you've set custom claims, they will be returned by `getUserIdentity` as well.

### HTTP Actions

You can also access the user identity from an HTTP action `ctx.auth.getUserIdentity()`, by calling your endpoint with an `Authorization` header including a JWT token:

```typescript
// myPage.ts
const jwtToken = "...";

fetch("https://<deployment name>.convex.site/myAction", {
  headers: {
    Authorization: `Bearer ${jwtToken}`,
  },
});
```

## Database

### Storing Users in the Convex Database

If you're using Convex Auth, the user information is already stored in your database. There's nothing else you need to implement.

You might want to store user information directly in your Convex database for the following reasons:

- Your functions need information about other users, not just about the currently logged-in user.
- Your functions need access to information other than the fields available in the Open ID Connect JWT.

There are two ways you can choose from for storing user information in your database (but only the second one allows storing information not contained in the JWT):

1. Have your app's client call a mutation that stores the information from the JWT available on `ctx.auth`.
2. Implement a webhook and have your identity provider call it whenever user information changes.

### Call a Mutation from the Client


### (Optional) Users Table Schema

You can define a "users" table, optionally with an index for efficient looking up the users in the database.

In the examples below, we will use the `tokenIdentifier` from the `ctx.auth.getUserIdentity()` to identify the user, but you could use the `subject` field (which is usually set to the unique user ID from your auth provider) or even email, if your authentication provider provides email verification and you have it enabled.

Which field you use will determine how multiple providers interact, and how hard it will be to migrate to a different provider.

```typescript
// convex/schema.ts
users: defineTable({
  name: v.string(),
  tokenIdentifier: v.string(),
}).index("by_token", ["tokenIdentifier"]),
```

### Mutation for Storing Current User

This is an example of a mutation that stores the user's name and `tokenIdentifier`:

```typescript
// convex/users.ts
import { mutation } from "./_generated/server";

export const store = mutation({
  args: {},
  handler: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) {
      throw new Error("Called storeUser without authentication present");
    }

    // Check if we've already stored this identity before.
    // Note: If you don't want to define an index right away, you can use
    // ctx.db.query("users")
    //  .filter(q => q.eq(q.field("tokenIdentifier"), identity.tokenIdentifier))
    //  .unique();
    const user = await ctx.db
      .query("users")
      .withIndex("by_token", (q) =>
        q.eq("tokenIdentifier", identity.tokenIdentifier),
      )
      .unique();
    if (user !== null) {
      // If we've seen this identity before but the name has changed, patch the value.
      if (user.name !== identity.name) {
        await ctx.db.patch(user._id, { name: identity.name });
      }
      return user._id;
    }
    // If it's a new identity, create a new `User`.
    return await ctx.db.insert("users", {
      name: identity.name ?? "Anonymous",
      tokenIdentifier: identity.tokenIdentifier,
    });
  },
});
```

### Calling the Store User Mutation from React

You can call this mutation when the user logs in from a `useEffect` hook. After the mutation succeeds, you can update local state to reflect that the user has been stored.

This helper hook that does the job:

```typescript
// src/useStoreUserEffect.ts
import { useUser } from "@clerk/clerk-react";
import { useConvexAuth } from "convex/react";
import { useEffect, useState } from "react";
import { useMutation } from "convex/react";
import { api } from "../convex/_generated/api";
import { Id } from "../convex/_generated/dataModel";

export function useStoreUserEffect() {
  const { isLoading, isAuthenticated } = useConvexAuth();
  const { user } = useUser();
  // When this state is set we know the server
  // has stored the user.
  const [userId, setUserId] = useState<Id<"users"> | null>(null);
  const storeUser = useMutation(api.users.store);
  // Call the `storeUser` mutation function to store
  // the current user in the `users` table and return the `Id` value.
  useEffect(() => {
    // If the user is not logged in don't do anything
    if (!isAuthenticated) {
      return;
    }
    // Store the user in the database.
    // Recall that `storeUser` gets the user information via the `auth`
    // object on the server. You don't need to pass anything manually here.
    async function createUser() {
      const id = await storeUser();
      setUserId(id);
    }
    createUser();
    return () => setUserId(null);
    // Make sure the effect reruns if the user logs in with
    // a different identity
  }, [isAuthenticated, storeUser, user?.id]);
  // Combine the local state with the state from context
  return {
    isLoading: isLoading || (isAuthenticated && userId === null),
    isAuthenticated: isAuthenticated && userId !== null,
  };
}
```

You can use this hook in your top-level component. If your queries need the user document to be present, make sure that you only render the components that call them after the user has been stored:

```typescript
// src/App.tsx
import { SignInButton, UserButton } from "@clerk/clerk-react";
import { useQuery } from "convex/react";
import { api } from "../convex/_generated/api";
import { useStoreUserEffect } from "./useStoreUserEffect.js";

function App() {
  const { isLoading, isAuthenticated } = useStoreUserEffect();
  return (
    <main>
      {isLoading ? (
        <>Loading...</>
      ) : !isAuthenticated ? (
        <SignInButton />
      ) : (
        <>
          <UserButton />
          <Content />
        </>
      )}
    </main>
  );
}

function Content() {
  const messages = useQuery(api.messages.getForCurrentUser);
  return <div>Authenticated content: {messages?.length}</div>;
}

export default App;

## Using the Current User's Document ID

Similarly to the store user mutation, you can retrieve the current user's ID, or throw an error if the user hasn't been stored.

Now that you have users stored as documents in your Convex database, you can use their IDs as foreign keys in other documents:

### `convex/messages.ts`

```typescript
import { v } from "convex/values";
import { mutation } from "./_generated/server";

export const send = mutation({
  args: { body: v.string() },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) {
      throw new Error("Unauthenticated call to mutation");
    }
    const user = await ctx.db
      .query("users")
      .withIndex("by_token", (q) =>
        q.eq("tokenIdentifier", identity.tokenIdentifier),
      )
      .unique();
    if (!user) {
      throw new Error("Unauthenticated call to mutation");
    }
    await ctx.db.insert("messages", { body: args.body, user: user._id });
  },
});
```

### Loading Users by Their ID

The information about other users can be retrieved via their IDs:

`convex/messages.ts`

```typescript
import { query } from "./_generated/server";

export const list = query({
  args: {},
  handler: async (ctx) => {
    const messages = await ctx.db.query("messages").collect();
    return Promise.all(
      messages.map(async (message) => {
        // For each message in this channel, fetch the `User` who wrote it and
        // insert their name into the `author` field.
        const user = await ctx.db.get(message.user);
        return {
          author: user?.name ?? "Anonymous",
          ...message,
        };
      }),
    );
  },
});
```

### Set Up Webhooks

This guide will use Clerk, but Auth0 can be set up similarly via Auth0 Actions.

With this implementation Clerk will call your Convex backend via an HTTP endpoint any time a user signs up, updates or deletes their account.


#### Configure the Webhook Endpoint in Clerk

On your Clerk dashboard, go to Webhooks, click on + Add Endpoint.

Set Endpoint URL to `https://<your deployment name>.convex.site/clerk-users-webhook` (note the domain ends in `.site`, not `.cloud`). You can see your deployment name in the `.env.local` file in your project directory, or on your Convex dashboard as part of the Deployment URL. For example, the endpoint URL could be: `https://happy-horse-123.convex.site/clerk-users-webhook`.

In Message Filtering, select `user` for all user events (scroll down or use the search input).

Click on Create.

After the endpoint is saved, copy the Signing Secret (on the right side of the UI), it should start with `whsec_`. Set it as the value of the `CLERK_WEBHOOK_SECRET` environment variable in your Convex dashboard.

#### (Optional) Users Table Schema

You can define a "users" table, optionally with an index for efficient looking up the users in the database.

In the examples below we will use the subject from the `ctx.auth.getUserIdentity()` to identify the user, which should be set to the Clerk user ID.

```typescript
// convex/schema.ts
users: defineTable({
  name: v.string(),
  // this the Clerk ID, stored in the subject JWT field
  externalId: v.string(),
}).index("byExternalId", ["externalId"]),
```

### Mutations for Upserting and Deleting Users

This is an example of mutations that handle the updates received via the webhook:

```typescript
// convex/users.ts
import { internalMutation, query, QueryCtx } from "./_generated/server";
import { UserJSON } from "@clerk/backend";
import { v, Validator } from "convex/values";

export const current = query({
  args: {},
  handler: async (ctx) => {
    return await getCurrentUser(ctx);
  },
});

export const upsertFromClerk = internalMutation({
  args: { data: v.any() as Validator<UserJSON> }, // no runtime validation, trust Clerk
  async handler(ctx, { data }) {
    const userAttributes = {
      name: `${data.first_name} ${data.last_name}`,
      externalId: data.id,
    };

    const user = await userByExternalId(ctx, data.id);
    if (user === null) {
      await ctx.db.insert("users", userAttributes);
    } else {
      await ctx.db.patch(user._id, userAttributes);
    }
  },
});

export const deleteFromClerk = internalMutation({
  args: { clerkUserId: v.string() },
  async handler(ctx, { clerkUserId }) {
    const user = await userByExternalId(ctx, clerkUserId);

    if (user !== null) {
      await ctx.db.delete(user._id);
    } else {
      console.warn(
        `Can't delete user, there is none for Clerk user ID: ${clerkUserId}`,
      );
    }
  },
});

export async function getCurrentUserOrThrow(ctx: QueryCtx) {
  const userRecord = await getCurrentUser(ctx);
  if (!userRecord) throw new Error("Can't get current user");
  return userRecord;
}

export async function getCurrentUser(ctx: QueryCtx) {
  const identity = await ctx.auth.getUserIdentity();
  if (identity === null) {
    return null;
  }
  return await userByExternalId(ctx, identity.subject);
}

async function userByExternalId(ctx: QueryCtx, externalId: string) {
  return await ctx.db
    .query("users")
    .withIndex("byExternalId", (q) => q.eq("externalId", externalId))
    .unique();
}
```

There are also a few helpers in this file:

- `current` exposes the user information to the client, which will helps the client determine whether the webhook already succeeded
- `upsertFromClerk` will be called when a user signs up or when they update their account
- `deleteFromClerk` will be called when a user deletes their account via Clerk UI from your app
- `getCurrentUserOrThrow` retrieves the currently logged-in user or throws an error
- `getCurrentUser` retrieves the currently logged-in user or returns `null`
- `userByExternalId` retrieves a user given the Clerk ID, and is used only for retrieving the current user or when updating an existing user via the webhook

### Webhook Endpoint Implementation

This how the actual HTTP endpoint can be implemented:

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { internal } from "./_generated/api";
import type { WebhookEvent } from "@clerk/backend";
import { Webhook } from "svix";

const http = httpRouter();

http.route({
  path: "/clerk-users-webhook",
  method: "POST",
  handler: httpAction(async (ctx, request) => {
    const event = await validateRequest(request);
    if (!event) {
      return new Response("Error occured", { status: 400 });
    }
    switch (event.type) {
      case "user.created": // intentional fallthrough
      case "user.updated":
        await ctx.runMutation(internal.users.upsertFromClerk, {
          data: event.data,
        });
        break;

      case "user.deleted": {
        const clerkUserId = event.data.id!;
        await ctx.runMutation(internal.users.deleteFromClerk, { clerkUserId });
        break;
      }
      default:
        console.log("Ignored Clerk webhook event", event.type);
    }

    return new Response(null, { status: 200 });
  }),
});

async function validateRequest(req: Request): Promise<WebhookEvent | null> {
  const payloadString = await req.text();
  const svixHeaders = {
    "svix-id": req.headers.get("svix-id")!,
    "svix-timestamp": req.headers.get("svix-timestamp")!,
    "svix-signature": req.headers.get("svix-signature")!,
  };
  const wh = new Webhook(process.env.CLERK_WEBHOOK_SECRET!);
  try {
    return wh.verify(payloadString, svixHeaders) as unknown as WebhookEvent;
  } catch (error) {
    console.error("Error verifying webhook event", error);
    return null;
  }
}

export default http;
```

If you deploy your code now and sign in, you should see the user being created in your Convex database.

### Using the Current User's Document

You can use the helpers defined before to retrieve the current user's document.

Now that you have users stored as documents in your Convex database, you can use their IDs as foreign keys in other documents:

`convex/messages.ts`

```typescript
import { v } from "convex/values";
import { mutation } from "./_generated/server";
import { getCurrentUserOrThrow } from "./users";

export const send = mutation({
  args: { body: v.string() },
  handler: async (ctx, args) => {
    const user = await getCurrentUserOrThrow(ctx);
    await ctx.db.insert("messages", { body: args.body, userId: user._id });
  },
});
```

### Loading Users by Their ID

The information about other users can be retrieved via their IDs:

`convex/messages.ts`

```typescript
export const list = query({
  args: {},
  handler: async (ctx) => {
    const messages = await ctx.db.query("messages").collect();
    return Promise.all(
      messages.map(async (message) => {
        // For each message in this channel, fetch the `User` who wrote it and
        // insert their name into the `author` field.
        const user = await ctx.db.get(message.user);
        return {
          author: user?.name ?? "Anonymous",
          ...message,
        };
      }),
    );
  },
});
```

### Waiting for Current User to Be Stored

If you want to use the current user's document in a query, make sure that the user has already been stored. You can do this by explicitly checking for this condition before rendering the components that call the query, or before redirecting to the authenticated portion of your app.

For example you can define a hook that determines the current authentication state of the client, taking into account whether the current user has been stored:

`src/useCurrentUser.ts`

```typescript
import { useConvexAuth, useQuery } from "convex/react";
import { api } from "../convex/_generated/api";

export function useCurrentUser() {
  const { isLoading, isAuthenticated } = useConvexAuth();
  const user = useQuery(api.users.current);
  // Combine the authentication state with the user existence check
  return {
    isLoading: isLoading || (isAuthenticated && user === null),
    isAuthenticated: isAuthenticated && user !== null,
  };
}
```

And then you can use it to render the appropriate components:

`src/App.tsx`

```typescript
import { useCurrentUser } from "./useCurrentUser";

export default function App() {
  const { isLoading, isAuthenticated } = useCurrentUser();
  return (
    <main>
      {isLoading ? (
        <>Loading...</>
      ) : isAuthenticated ? (
        <Content />
      ) : (
        <LoginPage />
      )}
    </main>
  );
}
```

## Debugging Authentication

You have followed one of our authentication guides but something is not working. You have double checked that you followed all the steps, and that you used the correct secrets, but you are still stuck.

### Frequently Encountered Issues

`ctx.auth.getUserIdentity()` returns `null` in a query

This often happens when subscribing to queries via `useQuery` in React, without waiting for the client to be authenticated. Even if the user has been logged-in previously, it takes some time for the client to authenticate with the Convex backend. Therefore on page load, `ctx.auth.getUserIdentity()` called within a query returns `null`.

To handle this, you can either:

1. Use the `Authenticated` component from `convex/react` to wrap the component that includes the `useQuery` call (see the last two steps in the Clerk guide)
2. Or return `null` or some other "sentinel" value from the query and handle it on the client

If you are using `fetchQuery` for Next.js Server Rendering, make sure you are explicitly passing in a JWT token as documented [here](https://docs.convex.dev/api/fetchQuery).

If this hasn't helped, follow the steps below to resolve your issue.

Step 1: Check whether authentication works on the backend

Add the following code to the beginning of your function (query, mutation, action or http action):

```javascript
console.log("server identity", await ctx.auth.getUserIdentity());
```

Then call this function from whichever client you're using to talk to Convex.

Open the logs page on your dashboard.

**What do you see on the logs page?**

**Answer: I don't see anything:**

- Potential cause: You don't have the right dashboard open. Confirm that the Deployment URL on Settings > URL and Deploy Key page matches how your client is configured.
- Potential cause: Your client is not connected to Convex. Check your client logs (browser logs) for errors. Reload the page / restart the client.
- Potential cause: The code has not been pushed. For dev deployments make sure you have `npx convex dev` running. For prod deployments make sure you successfully pushed via `npx convex deploy`. Go to the Functions page on the dashboard and check that the code shown there includes the `console.log` line you added.

When you resolved the cause you should see the log appear.

**Answer: I see a log with 'server identity' `null`:**

- Potential cause: The client is not supplying an auth token.
- Potential cause: Your deployment is misconfigured.
- Potential cause: Your client is misconfigured.

Proceed to step 2.

**Answer: I see a log with 'server identity' `{ tokenIdentifier: '... }`**

Great, you are all set!

Step 2: Check whether authentication works on the frontend

No matter which client you use, it must pass a JWT token to your backend for authentication to work.

The most bullet-proof way of ensuring your client is passing the token to the backend, is to inspect the traffic between them.

If you're using a client from the web browser, open the Network tab in your browser's developer tools.

**Check the token**

For Websocket-based clients (`ConvexReactClient` and `ConvexClient`), filter for the sync name and select WS as the type of traffic. Check the sync items. After the client is initialized (commonly after loading the page), it will send a message (check the Messages tab) with `type: "Authenticate"`, and `value` will be the authentication token.

![Network tab inspecting Websocket messages](https://docs.convex.dev/images/auth-debug-websocket.png)

For HTTP based clients (`ConvexHTTPClient` and the HTTP API), select Fetch/XHR as the type of traffic. You should see an individual network request for each function call, with an `Authorization` header with value `Bearer` followed by the authentication token.

![Network tab inspecting HTTP headers](https://docs.convex.dev/images/auth-debug-http.png)

**Do you see the authentication token in the traffic?**

**Answer: No:**

- Potential cause: The Convex client is not configured to get/fetch a JWT token. You're not using `ConvexProviderWithClerk`/`ConvexProviderWithAuth0`/`ConvexProviderWithAuth` with the `ConvexReactClient` or you forgot to call `setAuth` on `ConvexHTTPClient` or `ConvexClient`.
- Potential cause: You are not signed in, so the token is `null` or `undefined` and the `ConvexReactClient` skipped authentication altogether. Verify that you are signed in via `console.log`ing the token from whichever auth provider you are using:

  Clerk:
  ```javascript
  // import { useAuth } from "@clerk/nextjs"; // for Next.js
  import { useAuth } from "@clerk/clerk-react";

  const { getToken } = useAuth();
  console.log(getToken({ template: "convex" }));
  ```

  Auth0:
  ```javascript
  import { useAuth0 } from "@auth0/auth0-react";

  const { getAccessTokenSilently } = useAuth0();
  const response = await getAccessTokenSilently({
    detailedResponse: true,
  });
  const token = response.id_token;
  console.log(token);
  ```

  Custom: However you implemented `useAuthFromProviderX`

If you don't see a long string that looks like a token, check the browser logs for errors from your auth provider. If there are none, check the Network tab to see whether requests to your provider are failing. Perhaps the auth provider is misconfigured. Double check the auth provider configuration (in the corresponding React provider or however your auth provider is configured for the client). Try clearing your cookies in the browser (in dev tools Application > Cookies > Clear all cookies button).

**Answer: Yes, I see a long string that looks like a JWT:**

Great, copy the whole token (there can be `.`s in it, so make sure you're not copying just a portion of it).

Open [https://jwt.io/](https://jwt.io/), scroll down and paste the token in the Encoded textarea on the left of the page. On the right you should see:

- In HEADER, `"typ": "JWT"`
- in PAYLOAD, a valid JSON with at least `"aud"`, `"iss"` and `"sub"` fields. If you see gibberish in the payload you probably didn't copy the token correctly or it's not a valid JWT token.

If you see a valid JWT token, repeat step 1. If you still don't see correct identity, proceed to step 3.

Step 3: Check that backend configuration matches frontend configuration

You have a valid JWT token on the frontend, and you know that it is being passed to the backend, but the backend is not validating it.

Open the Settings > Authentication on your dashboard. What do you see?

**Answer: I see "This deployment has no configured authentication providers":**

Cause: You do not have an `auth.config.ts` (or `auth.config.js`) file in your convex directory, or you haven't pushed your code. Follow the authentication guide to create a valid auth config file. For dev deployments make sure you have `npx convex dev` running. For prod deployments make sure you successfully pushed via `npx convex deploy`.

**Answer: I see one or more Domain and Application ID pairs.**

Great, let's check they match the JWT token.

Look at the `iss` field in the JWT token payload at [https://jwt.io/](https://jwt.io/). Does it match a Domain on the Authentication page?

**Answer: No, I don't see the `iss` URL on the Convex dashboard:**

- Potential cause: You copied the wrong value into your `auth.config.ts`'s `domain`, or into the environment variable that is used there. Go back to the authentication guide and make sure you have the right URL from your auth provider.
- Potential cause: Your client is misconfigured:
  - Clerk: You have the wrong `publishableKey` configured. The key must belong to the Clerk instance that you used to configure your `auth.config.ts`.
    Also make sure that the JWT token in Clerk is called `convex`, as that's the name `ConvexProviderWithClerk` uses to fetch the token!
  - Auth0: You have the wrong `domain` configured (on the client!). The domain must belong to the Auth0 instance that you used to configure your `auth.config.ts`.
  - Custom: Make sure that your client is correctly configured to match your `auth.config.ts`.

**Answer: Yes, I do see the `iss` URL:**

Great, let's move one.

Look at the `aud` field in the JWT token payload at [https://jwt.io/](https://jwt.io/). Does it match the Application ID under the correct Domain on the Authentication page?

**Answer: No, I don't see the `aud` value in the Application ID field:**

- Potential cause: You copied the wrong value into your `auth.config.ts`'s `applicationID`, or into the environment variable that is used there. Go back to the authentication guide and make sure you have the right value from your auth provider.
- Potential cause: Your client is misconfigured:
  - Clerk: You have the wrong `publishableKey` configured. The key must belong to the Clerk instance that you used to configure your `auth.config.ts`.
  - Auth0: You have the wrong `clientId` configured. Make sure you're using the right `clientId` for the Auth0 instance that you used to configure your `auth.config.ts`.
  - Custom: Make sure that your client is correctly configured to match your `auth.config.ts`.

**Answer: Yes, I do see the `aud` value in the Application ID field:**

Great, repeat step 1 and you should be all set!

# Scheduling

Convex lets you easily schedule a function to run once or repeatedly in the future. This allows you to build durable workflows like sending a welcome email a day after someone joins or regularly reconciling your accounts with Stripe. Convex provides two different features for scheduling:

1. **Scheduled Functions** can be scheduled durably by any other function to run at a later point in time. You can schedule functions minutes, days, and even months in the future.
2. **Cron Jobs** schedule functions to run on a recurring basis, such as daily.

## Scheduled Functions

Convex allows you to schedule functions to run in the future. This allows you to build powerful durable workflows without the need to set up and maintain queues or other infrastructure.

Scheduled functions are stored in the database. This means you can schedule functions minutes, days, and even months in the future. Scheduling is resilient against unexpected downtime or system restarts.


### Scheduling functions

You can schedule public functions and internal functions from mutations and actions via the scheduler provided in the respective function context.

- `runAfter` schedules a function to run after a delay (measured in milliseconds).
- `runAt` schedules a function run at a date or timestamp (measured in milliseconds elapsed since the epoch).

The rest of the arguments are the path to the function and its arguments, similar to invoking a function from the client. For example, here is how to send a message that self-destructs in five seconds.

```typescript
// convex/messages.ts
import { mutation, internalMutation } from "./_generated/server";
import { internal } from "./_generated/api";
import { v } from "convex/values";

export const sendExpiringMessage = mutation({
  args: { body: v.string(), author: v.string() },
  handler: async (ctx, args) => {
    const { body, author } = args;
    const id = await ctx.db.insert("messages", { body, author });
    await ctx.scheduler.runAfter(5000, internal.messages.destruct, {
      messageId: id,
    });
  },
});

export const destruct = internalMutation({
  args: {
    messageId: v.id("messages"),
  },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.messageId);
  },
});
```

A single function can schedule up to 1000 functions with total argument size of 8MB.

#### Scheduling from mutations

Scheduling functions from mutations is atomic with the rest of the mutation. This means that if the mutation succeeds, the scheduled function is guaranteed to be scheduled. On the other hand, if the mutations fails, no function will be scheduled, even if the function fails after the scheduling call.

#### Scheduling from actions

Unlike mutations, actions don't execute as a single database transaction and can have side effects. Thus, scheduling from actions does not depend on the outcome of the function. This means that an action might succeed to schedule some functions and later fail due to transient error or a timeout. The scheduled functions will still be executed.

#### Scheduling immediately

Using `runAfter()` with delay set to 0 is used to immediately add a function to the event queue. This usage may be familiar to you if you're used to calling `setTimeout(fn, 0)`.

As noted above, actions are not atomic and are meant to cause side effects. Scheduling immediately becomes useful when you specifically want to trigger an action from a mutation that is conditional on the mutation succeeding. This post goes over a direct example of this in action, where the application depends on an external service to fill in information to the database.

#### Retrieving scheduled function status

Every scheduled function is reflected as a document in the `"_scheduled_functions"` system table. `runAfter()` and `runAt()` return the id of scheduled function. You can read data from system tables using the `db.system.get` and `db.system.query` methods, which work the same as the standard `db.get` and `db.query` methods.

```typescript
// convex/messages.ts
export const listScheduledMessages = query({
  args: {},
  handler: async (ctx, args) => {
    return await ctx.db.system.query("_scheduled_functions").collect();
  },
});

export const getScheduledMessage = query({
  args: {
    id: v.id("_scheduled_functions"),
  },
  handler: async (ctx, args) => {
    return await ctx.db.system.get(args.id);
  },
});
```

This is an example of the returned document:

```json
{
  "_creationTime": 1699931054642.111,
  "_id": "3ep33196167235462543626ss0scq09aj4gqn9kdxrdr",
  "args": [{}],
  "completedTime": 1699931054690.366,
  "name": "messages.js:destruct",
  "scheduledTime": 1699931054657,
  "state": { "kind": "success" }
}
```

The returned document has the following fields:

- `name`: the path of the scheduled function
- `args`: the arguments passed to the scheduled function
- `scheduledTime`: the timestamp of when the function is scheduled to run (measured in milliseconds elapsed since the epoch)
- `completedTime`: the timestamp of when the function finished running, if it has completed (measured in milliseconds elapsed since the epoch)
- `state`: the status of the scheduled function. Here are the possible states a scheduled function can be in:
  - `Pending`: the function has not been started yet
  - `InProgress`: the function has started running is not completed yet (only applies to actions)
  - `Success`: the function finished running successfully with no errors
  - `Failed`: the function hit an error while running, which can either be a user error or an internal server error
  - `Canceled`: the function was canceled via the dashboard, `ctx.scheduler.cancel`, or recursively by a parent scheduled function that was canceled while in progress

Scheduled function results are available for 7 days after they have completed.

#### Canceling scheduled functions

You can cancel a previously scheduled function with `cancel` via the scheduler provided in the respective function context.

```typescript
// convex/messages.ts
export const cancelMessage = mutation({
  args: {
    id: v.id("_scheduled_functions"),
  },
  handler: async (ctx, args) => {
    await ctx.scheduler.cancel(args.id);
  },
});
```

What `cancel` does depends on the state of the scheduled function:

- If it hasn't started running, it won't run.
- If it already started, it will continue to run, but any functions it schedules will not run.

### Debugging

You can view logs from previously executed scheduled functions in the Convex dashboard Logs view. You can view and cancel yet to be executed functions in the Functions view.

### Error handling

Once scheduled, mutations are guaranteed to be executed exactly once. Convex will automatically retry any internal Convex errors, and only fail on developer errors. See Error Handling for more details on different error types.

Since actions may have side effects, they are not automatically retried by Convex. Thus, actions will be executed at most once, and permanently fail if there are transient errors while executing them. Developers can retry those manually by scheduling a mutation that checks if the desired outcome has been achieved and if not schedule the action again.

### Auth

The auth is not propagated from the scheduling to the scheduled function. If you want to authenticate or check authorization, you'll have to pass the requisite user information in as a parameter.

## Cron Jobs

Convex allows you to schedule functions to run on a recurring basis. For example, cron jobs can be used to clean up data at a regular interval, send a reminder email at the same time every month, or schedule a backup every Saturday.


### Defining your cron jobs

Cron jobs are defined in a `crons.ts` file in your `convex/` directory and look like:

```typescript
import { cronJobs } from "convex/server";
import { internal } from "./_generated/api";

const crons = cronJobs();

crons.interval(
  "clear messages table",
  { minutes: 1 }, // every minute
  internal.messages.clearAll,
);

crons.monthly(
  "payment reminder",
  { day: 1, hourUTC: 16, minuteUTC: 0 }, // Every month on the first day at 8:00am PST
  internal.payments.sendPaymentEmail,
  { email: "my_email@gmail.com" }, // argument to sendPaymentEmail
);

// An alternative way to create the same schedule as above with cron syntax
crons.cron(
  "payment reminder duplicate",
  "0 16 1 * *",
  internal.payments.sendPaymentEmail,
  { email: "my_email@gmail.com" }, // argument to sendPaymentEmail
);

export default crons;
```

The first argument is a unique identifier for the cron job.

The second argument is the schedule at which the function should run, see *Supported schedules* below.

The third argument is the name of the public function or internal function, either a mutation or an action.

### Supported schedules

1. `crons.interval()` runs a function every specified number of seconds, minutes, or hours. The first run occurs when the cron job is first deployed to Convex. Unlike traditional crons, this option allows you to have seconds-level granularity.
2. `crons.cron()` the traditional way of specifying cron jobs by a string with five fields separated by spaces (e.g. "* * * * *"). Times in cron syntax are in the UTC timezone. [Crontab Guru](https://crontab.guru/) is a helpful resource for understanding and creating schedules in this format.
3. `crons.hourly()`, `crons.daily()`, `crons.weekly()`, `crons.monthly()` provide an alternative syntax for common cron schedules with explicitly named arguments.

### Viewing your cron jobs

You can view all your cron jobs in the Convex dashboard cron jobs view. You can view added, updated, and deleted cron jobs in the logs and history view. Results of previously executed runs of the cron jobs are also available in the logs view.

### Error handling

Mutations and actions have the same guarantees that are described in *Error handling for scheduled functions*.

At most one run of each cron job can be executing at any moment. If the function scheduled by the cron job takes too long to run, following runs of the cron job may be skipped to avoid execution from falling behind. Skipping a scheduled run of a cron job due to the previous run still executing logs a message visible in the logs view of the dashboard.

# File Storage

File Storage makes it easy to implement file upload in your app, store files from and send files to third-party APIs, and to serve dynamic files to your users. *All file types are supported.*

## Key Features

1. Upload files to store them in Convex and reference them in your database documents
2. Store files generated or fetched from third-party APIs
3. Serve files via URL
4. Delete files stored in Convex
5. Access file metadata

You can manage your stored files on the dashboard.

## Uploading and Storing Files
Files can be uploaded by your users and stored in Convex.

### File Upload in React
To make implementing file upload even easier, we made a standalone React library, check out [uploadstuff.dev](https://uploadstuff.dev/). You can follow the library docs on implementing both your file upload frontend and backend.

If you're not using React or have a more custom use case, follow the docs below.

### Uploading Files via Upload URLs
Arbitrarily large files can be uploaded directly to your backend using a generated upload URL. This requires the client to make 3 requests:

1. Generate an upload URL using a mutation that calls `storage.generateUploadUrl()`.
2. Send a `POST` request with the file contents to the upload URL and receive a storage ID.
3. Save the storage ID into your data model via another mutation.

In the first mutation that generates the upload URL, you can control who can upload files to your Convex storage.

#### Example: File Storage with Queries and Mutations

```typescript
// Calling the upload APIs from a web page
import { FormEvent, useRef, useState } from "react";
import { useMutation } from "convex/react";
import { api } from "../convex/_generated/api";

export default function App() {
  const generateUploadUrl = useMutation(api.messages.generateUploadUrl);
  const sendImage = useMutation(api.messages.sendImage);

  const imageInput = useRef<HTMLInputElement>(null);
  const [selectedImage, setSelectedImage] = useState<File | null>(null);

  const [name] = useState(() => "User " + Math.floor(Math.random() * 10000));
  async function handleSendImage(event: FormEvent) {
    event.preventDefault();

    // Step 1: Get a short-lived upload URL
    const postUrl = await generateUploadUrl();
    // Step 2: POST the file to the URL
    const result = await fetch(postUrl, {
      method: "POST",
      headers: { "Content-Type": selectedImage!.type },
      body: selectedImage,
    });
    const { storageId } = await result.json();
    // Step 3: Save the newly allocated storage id to the database
    await sendImage({ storageId, author: name });

    setSelectedImage(null);
    imageInput.current!.value = "";
  }
  // ...
}
```

#### Generating the Upload URL
An upload URL can be generated by the `storage.generateUploadUrl` function of the `MutationCtx` object:

```typescript
// convex/messages.ts
import { mutation } from "./_generated/server";

export const generateUploadUrl = mutation(async (ctx) => {
  return await ctx.storage.generateUploadUrl();
});
```

This mutation can control who is allowed to upload files.

The upload URL expires in 1 hour and so should be fetched shortly before the upload is made.

#### Writing the New Storage ID to the Database
Since the storage ID is returned to the client, it is likely you will want to persist it in the database via another mutation:

```typescript
// convex/messages.ts
import { mutation } from "./_generated/server";

export const sendImage = mutation({
  args: { storageId: v.id("_storage"), author: v.string() },
  handler: async (ctx, args) => {
    await ctx.db.insert("messages", {
      body: args.storageId,
      author: args.author,
      format: "image",
    });
  },
});
```

### Limits
The file size is not limited, but the upload `POST` request has a 2-minute timeout.

### Uploading Files via an HTTP Action
The file upload process can be more tightly controlled by leveraging HTTP actions, performing the whole upload flow using a single request, but requiring correct CORS headers configuration.

The custom upload HTTP action can control who can upload files to your Convex storage. But note that the HTTP action request size is currently limited to 20MB. For larger files, you need to use upload URLs as described above.

#### Example: File Storage with HTTP Actions

```typescript
// Calling the upload HTTP action from a web page
import { FormEvent, useRef, useState } from "react";

const convexSiteUrl = import.meta.env.VITE_CONVEX_SITE_URL;

export default function App() {
  const imageInput = useRef<HTMLInputElement>(null);
  const [selectedImage, setSelectedImage] = useState<File | null>(null);

  async function handleSendImage(event: FormEvent) {
    event.preventDefault();

    // e.g. https://happy-animal-123.convex.site/sendImage?author=User+123
    const sendImageUrl = new URL(`${convexSiteUrl}/sendImage`);
    sendImageUrl.searchParams.set("author", "Jack Smith");

    await fetch(sendImageUrl, {
      method: "POST",
      headers: { "Content-Type": selectedImage!.type },
      body: selectedImage,
    });

    setSelectedImage(null);
    imageInput.current!.value = "";
  }
  // ...
}
```

#### Defining the Upload HTTP Action
A file sent in the HTTP request body can be stored using the `storage.store` function of the `ActionCtx` object. This function returns an `Id<"_storage">` of the stored file.

From the HTTP action, you can call a mutation to write the storage ID to a document in your database.

To confirm success back to your hosted website, you will need to set the right CORS headers:

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { api } from "./_generated/api";
import { Id } from "./_generated/dataModel";

const http = httpRouter();

http.route({
  path: "/sendImage",
  method: "POST",
  handler: httpAction(async (ctx, request) => {
    // Step 1: Store the file
    const blob = await request.blob();
    const storageId = await ctx.storage.store(blob);

    // Step 2: Save the storage ID to the database via a mutation
    const author = new URL(request.url).searchParams.get("author");
    await ctx.runMutation(api.messages.sendImage, { storageId, author });

    // Step 3: Return a response with the correct CORS headers
    return new Response(null, {
      status: 200,
      // CORS headers
      headers: new Headers({
        // e.g. https://mywebsite.com, configured on your Convex dashboard
        "Access-Control-Allow-Origin": process.env.CLIENT_ORIGIN!,
        Vary: "origin",
      }),
    });
  }),
});

// Pre-flight request for /sendImage
http.route({
  path: "/sendImage",
  method: "OPTIONS",
  handler: httpAction(async (_, request) => {
    // Make sure the necessary headers are present
    // for this to be a valid pre-flight request
    const headers = request.headers;
    if (
      headers.get("Origin") !== null &&
      headers.get("Access-Control-Request-Method") !== null &&
      headers.get("Access-Control-Request-Headers") !== null
    ) {
      return new Response(null, {
        headers: new Headers({
          // e.g. https://mywebsite.com, configured on your Convex dashboard
          "Access-Control-Allow-Origin": process.env.CLIENT_ORIGIN!,
          "Access-Control-Allow-Methods": "POST",
          "Access-Control-Allow-Headers": "Content-Type, Digest",
          "Access-Control-Max-Age": "86400",
        }),
      });
    } else {
      return new Response();
    }
  }),
});
```

## Storing Generated Files

Files can be uploaded to Convex from a client and stored directly, see *Upload*.

Alternatively, files can also be stored after they've been fetched or generated in actions and HTTP actions. For example, you might call a third-party API to generate an image based on a user prompt and then store that image in Convex.

### Storing files in actions

Storing files in actions is similar to uploading a file via an HTTP action. The action takes these steps:

1. Fetch or generate an image.
2. Store the image using `storage.store()` and receive a storage ID.
3. Save the storage ID into your data model via a mutation.

Storage IDs correspond to documents in the `"_storage"` system table (see *Metadata*), so they can be validated using the `v.id("_storage")` validator and typed as `Id<"_storage">` in TypeScript.

```typescript
// convex/images.ts
import { action, internalMutation, query } from "./_generated/server";
import { internal } from "./_generated/api";
import { v } from "convex/values";
import { Id } from "./_generated/dataModel";

export const generateAndStore = action({
  args: { prompt: v.string() },
  handler: async (ctx, args) => {
    // Not shown: generate imageUrl from `prompt`
    const imageUrl = "https://....";

    // Download the image
    const response = await fetch(imageUrl);
    const image = await response.blob();

    // Store the image in Convex
    const storageId: Id<"_storage"> = await ctx.storage.store(image);

    // Write `storageId` to a document
    await ctx.runMutation(internal.images.storeResult, {
      storageId,
      prompt: args.prompt,
    });
  },
});

export const storeResult = internalMutation({
  args: {
    storageId: v.id("_storage"),
    prompt: v.string(),
  },
  handler: async (ctx, args) => {
    const { storageId, prompt } = args;
    await ctx.db.insert("images", { storageId, prompt });
  },
});
```

## Serving Files
Files stored in Convex can be served to your users by generating a URL pointing to a given file.

### Generating File URLs in Queries
The simplest way to serve files is to return URLs along with other data required by your app from queries and mutations.

A file URL can be generated from a storage ID by the `storage.getUrl` function of the `QueryCtx`, `MutationCtx`, or `ActionCtx` object:

```typescript
// convex/listMessages.ts
import { query } from "./_generated/server";

export const list = query({
  args: {},
  handler: async (ctx) => {
    const messages = await ctx.db.query("messages").collect();
    return Promise.all(
      messages.map(async (message) => ({
        ...message,
        // If the message is an "image" its `body` is an `Id<"_storage">`
        ...(message.format === "image"
          ? { url: await ctx.storage.getUrl(message.body) }
          : {}),
      })),
    );
  },
});
```

File URLs can be used in `img` elements to render images:

```typescript
// src/App.tsx
function Image({ message }: { message: { url: string } }) {
  return <img src={message.url} height="300px" width="auto" />;
}
```

In your query, you can control who gets access to a file when the URL is generated. If you need to control access when the file is served, you can define your own file serving HTTP actions instead.

### Serving Files from HTTP Actions
You can serve files directly from HTTP actions. An HTTP action will need to take some parameter(s) that can be mapped to a storage ID, or a storage ID itself.

This enables access control at the time the file is served, such as when an image is displayed on a website. But note that the HTTP actions response size is currently limited to 20MB. For larger files, you need to use file URLs as described above.

A file `Blob` object can be generated from a storage ID by the `storage.get` function of the `ActionCtx` object, which can be returned in a `Response`:

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { Id } from "./_generated/dataModel";

const http = httpRouter();

http.route({
  path: "/getImage",
  method: "GET",
  handler: httpAction(async (ctx, request) => {
    const { searchParams } = new URL(request.url);
    const storageId = searchParams.get("storageId")! as Id<"_storage">;
    const blob = await ctx.storage.get(storageId);
    if (blob === null) {
      return new Response("Image not found", {
        status: 404,
      });
    }
    return new Response(blob);
  }),
});

export default http;
```

The URL of such an action can be used directly in `img` elements to render images:

```typescript
// src/App.tsx
const convexSiteUrl = import.meta.env.VITE_CONVEX_SITE_URL;

function Image({ storageId }: { storageId: string }) {
  // e.g. https://happy-animal-123.convex.site/getImage?storageId=456
  const getImageUrl = new URL(`${convexSiteUrl}/getImage`);
  getImageUrl.searchParams.set("storageId", storageId);

  return <img src={getImageUrl.href} height="300px" width="auto" />;
}
```

## Deleting Files

Files stored in Convex can be deleted from mutations, actions, and HTTP actions via the `storage.delete()` function, which accepts a storage ID.

Storage IDs correspond to documents in the `"_storage"` system table (see Metadata), so they can be validated using the `v.id("_storage")`.

```typescript
// convex/images.ts
import { v } from "convex/values";
import { Id } from "./_generated/dataModel";
import { mutation } from "./_generated/server";

export const deleteById = mutation({
  args: {
    storageId: v.id("_storage"),
  },
  handler: async (ctx, args) => {
    return await ctx.storage.delete(args.storageId);
  },
});
```

## Accessing File Metadata
Every stored file is reflected as a document in the `"_storage"` system table. File metadata of a file can be accessed from queries and mutations via `db.system.get` and `db.system.query`:

```typescript
// convex/images.ts
import { v } from "convex/values";
import { query } from "./_generated/server";

export const getMetadata = query({
  args: {
    storageId: v.id("_storage"),
  },
  handler: async (ctx, args) => {
    return await ctx.db.system.get(args.storageId);
  },
});

export const listAllFiles = query({
  handler: async (ctx) => {
    // You can use .paginate() as well
    return await ctx.db.system.query("_storage").collect();
  },
});
```

This is an example of the returned document:

```json
{
  "_creationTime": 1700697415295.742,
  "_id": "3k7ty84apk2zy00ay4st1n5p9kh7tf8",
  "contentType": "image/jpeg",
  "sha256": "cb58f529b2ed5a1b8b6681d91126265e919ac61fff6a367b8341c0f46b06a5bd",
  "size": 125338
}
```

The returned document has the following fields:

- `sha256`: a base16 encoded sha256 checksum of the file contents
- `size`: the size of the file in bytes
- `contentType`: the ContentType of the file if it was provided on upload

You can check the metadata manually on your dashboard.

### Accessing metadata from actions (deprecated)
Alternatively, a `storage.getMetadata()` function is available to access individual file metadata from actions and HTTP actions:

```typescript
// convex/images.ts
import { v } from "convex/values";
import { action } from "./_generated/server";

export const getMetadata = action({
  args: { storageId: v.id("_storage") },
  handler: async (ctx, args) => {
    return await ctx.storage.getMetadata(args.storageId);
  },
});
```

Note that `storage.getMetadata()` returns a `FileMetadata`, which has a slightly different shape than the result from `db.system.get`.

# AI & Search

Whether building RAG-enabled chatbots or quick search in your applications, Convex provides easy APIs to create powerful AI and search-enabled products.

## Vector Search

Vector Search enables searching for documents based on their semantic meaning. It uses vector embeddings to calculate similarity and retrieve documents that are similar to a given query. Vector search is a key part of common AI techniques like RAG.

## Full Text Search

Full Text Search enables keyword and phrase search within your documents. It supports both prefix and fuzzy matching. Convex full text search is also reactive and always up to date like all Convex queries, making it easy to build reliable quick search boxes.

## Convex Actions

Convex Actions easily enable you to call AI APIs, save data to your database, and drive your user interface. See examples of how you can use this to build sophisticated AI applications.

## Vector Search

Vector search allows you to find Convex documents similar to a provided vector. Typically, vectors will be *embeddings* which are numerical representations of text, images, or audio.

Embeddings and vector search enable you to provide useful context to LLMs for AI powered applications, recommendations for similar content and more.

Vector search is consistent and fully up-to-date. You can write a vector and immediately read it from a vector search. Unlike full text search, however, vector search is only available in Convex actions.


To use vector search you need to:

1. Define a vector index.
2. Run a vector search from within an action.

### Defining Vector Indexes

Like database indexes, vector indexes are a data structure that is built in advance to enable efficient querying. Vector indexes are defined as part of your Convex schema.

To add a vector index onto a table, use the `vectorIndex` method on your table's schema. Every vector index has a unique name and a definition with:

- `vectorField string`: The name of the field indexed for vector search.
- `dimensions number`: The fixed size of the vectors index. If you're using embeddings, this dimension should match the size of your embeddings (e.g. 1536 for OpenAI).
- `[Optional] filterFields array`: The names of additional fields that are indexed for fast filtering within your vector index.

For example, if you want an index that can search for similar foods within a given cuisine, your table definition could look like:

```typescript
convex/schema.ts
foods: defineTable({
  description: v.string(),
  cuisine: v.string(),
  embedding: v.array(v.float64()),
}).vectorIndex("by_embedding", {
  vectorField: "embedding",
  dimensions: 1536,
  filterFields: ["cuisine"],
}),
```

You can specify vector and filter fields on nested documents by using a dot-separated path like `properties.name`.

### Running Vector Searches

Unlike database queries or full text search, vector searches can only be performed in a Convex action.

They generally involve three steps:

1. Generate a vector from provided input (e.g. using OpenAI)
2. Use `ctx.vectorSearch` to fetch the IDs of similar documents
3. Load the desired information for the documents

Here's an example of the first two steps for searching for similar French foods based on a description:

```typescript
convex/foods.ts
import { v } from "convex/values";
import { action } from "./_generated/server";

export const similarFoods = action({
  args: {
    descriptionQuery: v.string(),
  },
  handler: async (ctx, args) => {
    // 1. Generate an embedding from you favorite third party API:
    const embedding = await embed(args.descriptionQuery);
    // 2. Then search for similar foods!
    const results = await ctx.vectorSearch("foods", "by_embedding", {
      vector: embedding,
      limit: 16,
      filter: (q) => q.eq("cuisine", "French"),
    });
    // ...
  },
});
```

The `vectorSearch` API takes in the table name, the index name, and finally a `VectorSearchQuery` object describing the search. This object has the following fields:

- `vector array`: An array of numbers (e.g. embedding) to use in the search. The search will return the document IDs of the documents with the most similar stored vectors. It must have the same length as the dimensions of the index.
- `[Optional] limit number`: The number of results to get back. If specified, this value must be between 1 and 256.
- `[Optional] filter`: An expression that restricts the set of results based on the `filterFields` in the `vectorIndex` in your schema. See "Filter Expressions" for details.

It returns an Array of objects containing exactly two fields:

- `_id`: The Document ID for the matching document in the table
- `_score`: An indicator of how similar the result is to the vector you were searching for, ranging from -1 (least similar) to 1 (most similar)

Neither the underlying document nor the vector are included in results, so once you have the list of results, you will want to load the desired information about the results.

There are a few strategies for loading this information documented in the "Advanced Patterns" section.

For now, let's load the documents and return them from the action. To do so, we'll pass the list of results to a Convex query and run it inside of our action, returning the result:

```typescript
convex/foods.ts
export const fetchResults = internalQuery({
  args: { ids: v.array(v.id("foods")) },
  handler: async (ctx, args) => {
    const results = [];
    for (const id of args.ids) {
      const doc = await ctx.db.get(id);
      if (doc === null) {
        continue;
      }
      results.push(doc);
    }
    return results;
  },
});

convex/foods.ts
export const similarFoods = action({
  args: {
    descriptionQuery: v.string(),
  },
  handler: async (ctx, args) => {
    // 1. Generate an embedding from you favorite third party API:
    const embedding = await embed(args.descriptionQuery);
    // 2. Then search for similar foods!
    const results = await ctx.vectorSearch("foods", "by_embedding", {
      vector: embedding,
      limit: 16,
      filter: (q) => q.eq("cuisine", "French"),
    });
    // 3. Fetch the results
    const foods: Array<Doc<"foods">> = await ctx.runQuery(
      internal.foods.fetchResults,
      { ids: results.map((result) => result._id) },
    );
    return foods;
  },
});
```

### Filter Expressions

As mentioned above, vector searches support efficiently filtering results by additional fields on your document using either exact equality on a single field, or an OR of expressions.

For example, here's a filter for foods with cuisine exactly equal to "French":

```typescript
filter: (q) => q.eq("cuisine", "French"),
```

You can also filter documents by a single field that contains several different values using an `or` expression. Here's a filter for French or Indonesian dishes:

```typescript
filter: (q) =>
  q.or(q.eq("cuisine", "French"), q.eq("cuisine", "Indonesian")),
```

For indexes with multiple filter fields, you can also use `.or()` filters on different fields. Here's a filter for dishes whose cuisine is French or whose main ingredient is butter:

```typescript
filter: (q) =>
  q.or(q.eq("cuisine", "French"), q.eq("mainIngredient", "butter")),
```

Both `cuisine` and `mainIngredient` would need to be included in the `filterFields` in the `.vectorIndex` definition.

### Other Filtering

Results can be filtered based on how similar they are to the provided vector using the `_score` field in your action:

```typescript
const results = await ctx.vectorSearch("foods", "by_embedding", {
  vector: embedding,
});
const filteredResults = results.filter((result) => result._score >= 0.9);
```

Additional filtering can always be done by passing the vector search results to a query or mutation function that loads the documents and performs filtering using any of the fields on the document.

For performance, always put as many of your filters as possible into `.vectorSearch`.

#### Ordering

Vector queries always return results in relevance order.

Currently Convex searches vectors using an approximate nearest neighbor search based on cosine similarity. Support for more similarity metrics will come in the future.

If multiple documents have the same score, ties are broken by the document ID.

### Advanced Patterns

#### Using a Separate Table to Store Vectors

There are two main options for setting up a vector index:

1. Storing vectors in the same table as other metadata
2. Storing vectors in a separate table, with a reference

The examples above show the first option, which is simpler and works well for reading small amounts of documents. The second option is more complex, but better supports reading or returning large amounts of documents.

Since vectors are typically large and not useful beyond performing vector searches, it's nice to avoid loading them from the database when reading other data (e.g. `db.get()`) or returning them from functions by storing them in a separate table.

A table definition for movies, and a vector index supporting search for similar movies filtering by genre would look like this:

```typescript
convex/schema.ts
movieEmbeddings: defineTable({
  embedding: v.array(v.float64()),
  genre: v.string(),
}).vectorIndex("by_embedding", {
  vectorField: "embedding",
  dimensions: 1536,
  filterFields: ["genre"],
}),
movies: defineTable({
  title: v.string(),
  genre: v.string(),
  description: v.string(),
  votes: v.number(),
  embeddingId: v.optional(v.id("movieEmbeddings")),
}).index("by_embedding", ["embeddingId"]),
```

Generating an embedding and running a vector search are the same as using a single table. Loading the relevant documents given the vector search result is different since we have an ID for `movieEmbeddings` but want to load a `movies` document. We can do this using the `by_embedding` database index on the `movies` table:

```typescript
convex/movies.ts
export const fetchMovies = query({
  args: {
    ids: v.array(v.id("movieEmbeddings")),
  },
  handler: async (ctx, args) => {
    const results = [];
    for (const id of args.ids) {
      const doc = await ctx.db
        .query("movies")
        .withIndex("by_embedding", (q) => q.eq("embeddingId", id))
        .unique();
      if (doc === null) {
        continue;
      }
      results.push(doc);
    }
    return results;
  },
});
```

#### Fetching Results and Adding New Documents

Returning information from a vector search involves an action (since vector search is only available in actions) and a query or mutation to load the data.

The example above used a query to load data and return it from an action. Since this is an action, the data returned is not reactive. An alternative would be to return the results of the vector search in the action, and have a separate query that reactively loads the data. The search results will not update reactively, but the data about each result would be reactive.

The Vector Search Demo App uses this strategy to show similar movies with a reactive "Votes" count.

### Limits

Convex supports millions of vectors today. This is an ongoing project and we will continue to scale this offering out with the rest of Convex.

Vector indexes must have:

- Exactly 1 vector index field.
- The field must be of type `v.array(v.float64())` (or a union in which one of the possible types is `v.array(v.float64())`)
- Exactly 1 dimension field with a value between 2 and 4096.
- Up to 16 filter fields.

Vector indexes count towards the limit of 32 indexes per table. In addition you can have up to 4 vector indexes per table.

Vector searches can have:

- Exactly 1 vector to search by in the vector field
- Up to 64 filter expressions
- Up to 256 requested results (defaulting to 10).

If your action performs a vector search then passes the results to a query or mutation function, you may find that one or more results from the vector search have been deleted or mutated. Because vector search is only available in actions, you cannot perform additional transactional queries or mutations based on the results. If this is important for your use case, please let us know on Discord!

Only documents that contain a vector of the size and in the field specified by a vector index will be included in the index and returned by the vector search.

## Full Text Search
Full text search allows you to find Convex documents that approximately match a search query.

Unlike normal document queries, search queries look within a string field to find the keywords. This can be used to build search features within your app like searching for messages that contain certain words.

Search queries are automatically reactive, consistent, transactional, and work seamlessly with pagination. They even include new documents created with a mutation!

To use full text search you need to:

1. Define a search index.
2. Run a search query.

**Search is in beta**
Search is currently a beta feature. If you have feedback or feature requests, let us know on Discord!

### Defining search indexes
Like database indexes, search indexes are a data structure that is built in advance to enable efficient querying. Search indexes are defined as part of your Convex schema.

Every search index definition consists of:

- A name. Must be unique per table.
- A `searchField`. This is the field which will be indexed for full text search. It must be of type string.
- [Optional] A list of `filterFields`. These are additional fields that are indexed for fast equality filtering within your search index.

To add a search index onto a table, use the `searchIndex` method on your table's schema. For example, if you want an index which can search for messages matching a keyword in a channel, your schema could look like:

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  messages: defineTable({
    body: v.string(),
    channel: v.string(),
  }).searchIndex("search_body", {
    searchField: "body",
    filterFields: ["channel"],
  }),
});
```

You can specify search and filter fields on nested documents by using a dot-separated path like `properties.name`.

### Running search queries
A query for "10 messages in channel '#general' that best match the query 'hello hi' in their body" would look like:

```typescript
const messages = await ctx.db
  .query("messages")
  .withSearchIndex("search_body", (q) =>
    q.search("body", "hello hi").eq("channel", "#general"),
  )
  .take(10);
```

This is just a normal database read that begins by querying the search index!

The `.withSearchIndex` method defines which search index to query and how Convex will use that search index to select documents. The first argument is the name of the index and the second is a search filter expression. A search filter expression is a description of which documents Convex should consider when running the query.

A search filter expression is always a chained list of:

1. 1 search expression against the index's search field defined with `.search`.
2. 0 or more equality expressions against the index's filter fields defined with `.eq`.

**Search expressions**
Search expressions are issued against a search index, filtering and ranking documents by their relevance to the search expression's query. Internally, Convex will break up the query into separate words (called terms) and approximately rank documents matching these terms.

In the example above, the expression `search("body", "hello hi")` would internally be split into "hi" and "hello" and matched against words in your document (ignoring case and punctuation).

The behavior of `search` incorporates fuzzy and prefix matching rules.

**Equality expressions**
Unlike search expressions, equality expressions will filter to only documents that have an exact match in the given field. In the example above, `eq("channel", "#general")` will only match documents that have exactly "#general" in their `channel` field.

Equality expressions support fields of any type (not just text). To filter to documents that are missing a field, use `q.eq("fieldName", undefined)`.

**Other filtering**
Because search queries are normal database queries, you can also filter results using the `.filter` method!

Here's a query for "messages containing 'hi' sent in the last 10 minutes":

```typescript
const messages = await ctx.db
  .query("messages")
  .withSearchIndex("search_body", (q) => q.search("body", "hi"))
  .filter((q) => q.gt(q.field("_creationTime", Date.now() - 10 * 60000)))
  .take(10);
```

For performance, always put as many of your filters as possible into `.withSearchIndex`.

Every search query is executed by:

1. First, querying the search index using the search filter expression in `withSearchIndex`.
2. Then, filtering the results one-by-one using any additional filter expressions.

Having a very specific search filter expression will make your query faster and less likely to hit Convex's limits because Convex will use the search index to efficiently cut down on the number of results to consider.

**Retrieving results and paginating**
Just like ordinary database queries, you can retrieve the results using `.collect()`, `.take(n)`, `.first()`, and `.unique()`.

Additionally, search results can be paginated using `.paginate(paginationOpts)`.

Note that `collect()` will throw an exception if it attempts to collect more than the limit of 1024 documents. It is often better to pick a smaller limit and use `take(n)` or paginate the results.

**Ordering**
Search queries always return results in relevance order based on how well the document matches the search query. Different ordering of results are not supported.

### Search Behavior
**Fuzzy and Prefix Search**
Convex full-text search is designed to power as-you-type search experiences. To achieve this, full-text search automatically applies fuzzy and prefix matching rules to search terms! This means that documents matched by a search query do not necessarily need to exactly match any of the query's terms.

Depending on the length of search terms, a fixed number of typos is permitted between matches. Typos are defined in terms of Levenshtein distance. The specific typo-tolerance rules are:

- Terms with length <= 4 allow no typos
- Terms with 5 < length <= 8 allow 1 typo
- Terms with length > 8 allow 2 typos

For example, the expression `search("body", "hello something")` would match the following documents:

- "hillo"
- "somethan"
- "hallo somethan"
- "I left something in my car"

In addition to typo-tolerance, the final search term has prefix search enabled, matching any term that is a prefix of the original term. For example, the expression `search("body", "r")` would match the documents:

- "rabbit"
- "Rakeeb searches"
- "send request"

**Relevance order**
Relevance order is subject to change. The relevance of search results and the exact typo-tolerance rules Convex applies is subject to change to improve the quality of search results.

Search queries return results in relevance order. Internally, Convex ranks the relevance of a document based on a combination of its BM25 score and several other criteria such as the number of typos of matched terms in the document, the proximity of matches, the number of exact matches, and more. The BM25 score takes into account:

- How many words in the search query appear in the field?
- How many times do they appear?
- How long is the text field?

If multiple documents have the same score, the newest documents are returned first.

### Limits
Search indexes must have:

- Exactly 1 search field.
- Up to 16 filter fields.
- Search indexes count against the limit of 32 indexes per table.

Search queries can have:

- Up to 16 terms (words) in the search expression.
- Up to 8 filter expressions.
- Additionally, search queries can scan up to 1024 results from the search index.

# Testing

Convex makes it easy to test your app via automated tests running in JS or against a real backend, and manually in *dev*, *preview*, and *staging* environments.

## Automated tests

### convex-test library

Use the `convex-test` library to test your functions in JS via the excellent Vitest testing framework.

### Testing against a real backend

Convex open source builds allow you to test all of your backend logic running on a real local Convex backend.

### Set up testing in CI

It's a good idea to test your app continuously in a controlled environment. No matter which way automated method you use, it's easy to run them with Github Actions.

## Manual tests

### Running a function in dev

Manually run a function in *dev* to quickly see if things are working:

1. Run functions from the command line
2. Run functions from the dashboard

### Preview deployments

Use *preview deployments* to get early feedback from your team for your in-progress features.

### Staging environment

You can set up a separate project as a *staging environment* to test against. See [Deploying Your App to Production](https://example.com).

## convex-test

The `convex-test` library provides a mock implementation of the Convex backend in JavaScript. It enables fast automated testing of the logic in your functions.

### Install test dependencies

Install Vitest and the `convex-test` library.

```
npm install --save-dev convex-test vitest @edge-runtime/vm
```

### Setup NPM scripts

Add these scripts to your `package.json`:

```json
"scripts": {
  "test": "vitest",
  "test:once": "vitest run",
  "test:debug": "vitest --inspect-brk --no-file-parallelism",
  "test:coverage": "vitest run --coverage --coverage.reporter=text"
}
```

### Configure Vitest

Add a `vitest.config.mts` file to configure the test environment to better match the Convex runtime, and to inline the test library for better dependency tracking.

```typescript
import { defineConfig } from "vitest/config";

export default defineConfig({
  test: {
    environment: "edge-runtime",
    server: { deps: { inline: ["convex-test"] } },
  },
});
```

### Add a test file

In your `convex` folder, add a file ending in `.test.ts`. The example test calls the `api.messages.send` mutation twice and then asserts that the `api.messages.list` query returns the expected results.

```typescript
import { convexTest } from "convex-test";
import { expect, test } from "vitest";
import { api } from "./_generated/api";
import schema from "./schema";

test("sending messages", async () => {
  const t = convexTest(schema);
  await t.mutation(api.messages.send, { body: "Hi!", author: "Sarah" });
  await t.mutation(api.messages.send, { body: "Hey!", author: "Tom" });
  const messages = await t.query(api.messages.list);
  expect(messages).toMatchObject([
    { body: "Hi!", author: "Sarah" },
    { body: "Hey!", author: "Tom" }
  ]);
});
```

### Run tests

Start the tests with `npm run test`. When you change the test file or your functions, the tests will rerun automatically.

```
npm run test
```

If you're not familiar with Vitest or Jest, read the [Vitest Getting Started docs](https://vitest.dev/guide/) first.

`convexTest`

The library exports a `convexTest` function which should be called at the start of each of your tests. The function returns an object which is by convention stored in the `t` variable and which provides methods for exercising your Convex functions.

If your project uses a schema, you should pass it to the `convexTest` function:

```typescript
import { convexTest } from "convex-test";
import { test } from "vitest";
import schema from "./schema";

test("some behavior", async () => {
  const t = convexTest(schema);
  // use `t`...
});
```

Passing in the schema is required for the tests to correctly implement schema validation and for correct typing of `t.run`.

If you don't have a schema, call `convexTest()` with no argument.

Calling functions with `t.query`, `t.mutation` and `t.action`

Your test can call public and internal Convex functions in your project:

```typescript
import { convexTest } from "convex-test";
import { test } from "vitest";
import { api, internal } from "./_generated/api";

test("functions", async () => {
  const t = convexTest();
  const x = await t.query(api.myFunctions.myQuery, { a: 1, b: 2 });
  const y = await t.query(internal.myFunctions.internalQuery, { a: 1, b: 2 });
  const z = await t.mutation(api.myFunctions.mutateSomething, { a: 1, b: 2 });
  const w = await t.mutation(internal.myFunctions.mutateSomething, { a: 1 });
  const u = await t.action(api.myFunctions.doSomething, { a: 1, b: 2 });
  const v = await t.action(internal.myFunctions.internalAction, { a: 1, b: 2 });
});
```

### Setting up and inspecting data and storage with `t.run`

Sometimes you might want to directly write to the mock database or file storage from your test, without needing a declared function in your project. You can use the `t.run` method which takes a handler that is given a `ctx` that allows reading from and writing to the mock backend:

```typescript
import { convexTest } from "convex-test";
import { expect, test } from "vitest";

test("functions", async () => {
  const t = convexTest();
  const firstTask = await t.run(async (ctx) => {
    await ctx.db.insert("tasks", { text: "Eat breakfast" });
    return await ctx.db.query("tasks").first();
  });
  expect(firstTask).toMatchObject({ text: "Eat breakfast" });
});
```

### Testing HTTP actions with `t.fetch`

Your test can call HTTP actions registered by your router:

```typescript
import { convexTest } from "convex-test";
import { test } from "vitest";

test("functions", async () => {
  const t = convexTest();
  const response = await t.fetch("/some/path", { method: "POST" });
});
```

Mocking the global `fetch` function doesn't affect `t.fetch`, but you can use `t.fetch` in a `fetch` mock to route to your HTTP actions.

### Testing scheduled functions

One advantage of using a mock implementation running purely in JavaScript is that you can control time in the Vitest test environment. To test implementations relying on scheduled functions, use Vitest's fake timers in combination with `t.finishInProgressScheduledFunctions`:

```typescript
import { convexTest } from "convex-test";
import { expect, test, vi } from "vitest";
import { api } from "./_generated/api";
import schema from "./schema";

test("mutation scheduling action", async () => {
  // Enable fake timers
  vi.useFakeTimers();

  const t = convexTest(schema);

  // Call a function that schedules a mutation or action
  const scheduledFunctionId = await t.mutation(
    api.scheduler.mutationSchedulingAction,
    { delayMs: 10000 },
  );

  // Advance the mocked time
  vi.advanceTimersByTime(5000);

  // Advance the mocked time past the scheduled time of the function
  vi.advanceTimersByTime(6000);

  // Or run all currently pending timers
  vi.runAllTimers();

  // At this point the scheduled function will be `inProgress`,
  // now wait for it to finish
  await t.finishInProgressScheduledFunctions();

  // Assert that the scheduled function succeeded or failed
  const scheduledFunctionStatus = t.run(async (ctx) => {
    return await ctx.db.get(scheduledFunctionId);
  });
  expect(scheduledFunctionStatus).toMatchObject({ state: { kind: "success" } });

  // Reset to normal `setTimeout` etc. implementation
  vi.useRealTimers();
});
```

If you have a chain of several scheduled functions, for example a mutation that schedules an action that schedules another action, you can use `t.finishAllScheduledFunctions` to wait for all scheduled functions, including recursively scheduled functions, to finish:

```typescript
import { convexTest } from "convex-test";
import { expect, test, vi } from "vitest";
import { api } from "./_generated/api";
import schema from "./schema";

test("mutation scheduling action scheduling action", async () => {
  // Enable fake timers
  vi.useFakeTimers();

  const t = convexTest(schema);

  // Call a function that schedules a mutation or action
  await t.mutation(api.scheduler.mutationSchedulingActionSchedulingAction);

  // Wait for all scheduled functions, repeatedly
  // advancing time and waiting for currently in-progress
  // functions to finish
  await t.finishAllScheduledFunctions(vi.runAllTimers);

  // Assert the resulting state after all scheduled functions finished
  const createdTask = t.run(async (ctx) => {
    return await ctx.db.query("tasks").first();
  });
  expect(createdTask).toMatchObject({ author: "AI" });

  // Reset to normal `setTimeout` etc. implementation
  vi.useRealTimers();
});
```

### Testing authentication with `t.withIdentity`

To test functions which depend on the current authenticated user identity, you can create a version of the `t` accessor with given user identity attributes. If you don't provide them, `issuer`, `subject` and `tokenIdentifier` will be generated automatically:

```typescript
import { convexTest } from "convex-test";
import { expect, test } from "vitest";
import { api } from "./_generated/api";
import schema from "./schema";

test("authenticated functions", async () => {
  const t = convexTest(schema);

  const asSarah = t.withIdentity({ name: "Sarah" });
  await asSarah.mutation(api.tasks.create, { text: "Add tests" });

  const sarahsTasks = await asSarah.query(api.tasks.list);
  expect(sarahsTasks).toMatchObject([{ text: "Add tests" }]);

  const asLee = t.withIdentity({ name: "Lee" });
  const leesTasks = await asLee.query(api.tasks.list);
  expect(leesTasks).toEqual([]);
});
```

### Mocking `fetch` calls

You can use Vitest's `vi.stubGlobal` method:

```typescript
import { expect, test, vi } from "vitest";
import { convexTest } from "../index";
import { api } from "./_generated/api";
import schema from "./schema";

test("ai", async () => {
  const t = convexTest(schema);

  vi.stubGlobal(
    "fetch",
    vi.fn(async () => ({ text: async () => "I am the overlord" }) as Response),
  );

  const reply = await t.action(api.messages.sendAIMessage, { prompt: "hello" });
  expect(reply).toEqual("I am the overlord");

  vi.unstubAllGlobals();
});
```

### Asserting results

See Vitest's [Expect reference](https://vitest.dev/api/#expect). `toMatchObject()` is particularly helpful when asserting the shape of results without needing to list every object field.

### Asserting errors

To assert that a function throws, use `.rejects.toThrowError()`:

```typescript
import { convexTest } from "convex-test";
import { expect, test } from "vitest";
import { api } from "./_generated/api";
import schema from "./schema";

test("messages validation", async () => {
  const t = convexTest(schema);
  expect(async () => {
    await t.mutation(api.messages.send, { body: "", author: "James" });
  }).rejects.toThrowError("Empty message body is not allowed");
});
```

### Measuring test coverage

You can get a printout of the code coverage provided by your tests. Besides answering the question "how much of my code is covered by tests" it is also helpful to check that your test is actually exercising the code that you want it to exercise.

Run `npm run test:coverage`. It will ask you to install a required dependency the first time you run it.

### Debugging tests

You can attach a debugger to the running tests. Read the [Vitest Debugging docs](https://vitest.dev/guide/debugging.html) and then use `npm run test:debug`.

### Multiple environments

If you want to use Vitest to test both your Convex functions and your React frontend, you might want to use multiple Vitest environments depending on the test file location via `environmentMatchGlobs`:

```typescript
import { defineConfig } from "vitest/config";

export default defineConfig({
  test: {
    environmentMatchGlobs: [
      // all tests in convex/ will run in edge-runtime
      ["convex/**", "edge-runtime"],
      // all other tests use jsdom
      ["**", "jsdom"],
    ],
    server: { deps: { inline: ["convex-test"] } },
  },
});
```

### Custom `convex/` folder name or location

If your project has a different name or location configured for the `convex/` folder in `convex.json`, you need to call `import.meta.glob` and pass the result as the second argument to `convexTest`.

The argument to `import.meta.glob` must be a glob pattern matching all the files containing your Convex functions. The paths are relative to the test file in which `import.meta.glob` is called. It's best to do this in one place in your custom functions folder:

```typescript
/// <reference types="vite/client" />
export const modules = import.meta.glob("./**/!(*.*.*)*.*s");
```

Use the result in your tests:

```typescript
import { convexTest } from "convex-test";
import { test } from "vitest";
import schema from "./schema";
import { modules } from "./test.setup";

test("some behavior", async () => {
  const t = convexTest(schema, modules);
  // use `t`...
});
```

### Limitations

Since `convex-test` is only a mock implementation, it doesn't have many of the behaviors of the real Convex backend. Still, it should be helpful for testing the logic in your functions, and catching regressions caused by changes to your code.

Some of the ways the mock differs:

- Error messages content. You should not write product logic that relies on the content of error messages thrown by the real backend, as they are always subject to change.
- Limits. The mock doesn't enforce size and time limits.
- ID format. Your code should not depend on the document or storage ID format.
- Runtime built-ins. Most of your functions are written for the Convex default runtime, while Vitest uses a mock of Vercel's Edge Runtime, which is similar but might differ from the Convex runtime. You should always test new code manually to make sure it doesn't use built-ins not available in the Convex runtime.

Some features have only simplified semantics, namely:

- Text search returns all documents that include a word for which at least one word in the searched string is a prefix. It does not implement fuzzy searching and doesn't sort the results by relevance.
- Vector search returns results sorted by cosine similarity, but doesn't use an efficient vector index in its implementation.

There is no support for cron jobs, you should trigger your functions manually from the test.

To test your functions running on a real Convex backend, check out [Testing Local Backend](https://docs.convex.dev/testing/local-backend).

# Deploying Your App to Production

Convex is built to serve live, production app traffic. Here we cover how to deploy and maintain a production version of your app.

## Project Management

When you sign up for Convex, a Convex team is created for you. You can create more teams from the dashboard and add other people to them as members. You can upgrade your team to the Pro plan for additional features, higher limits and usage-based pricing.

Each team can have multiple projects. When you run `npx convex dev` for the first time, a project is created for you automatically. You can also create a project from the dashboard.

Every project has one shared production deployment and one development deployment per team member. This allows each team member to make and test changes independently before they are deployed to the production deployment.

Usually all deployments belonging to a single project run the same code base (or a version of it), but Convex doesn't enforce this. You can also run the same code base on multiple different prod deployments belonging to different projects, see *staging* below.

## Deploying to Production

Your Convex deployments run your backend logic and in most cases you will also develop a client that uses the backend. If your client is a web app, follow the [Hosting and Deployment guide](https://docs.convex.dev/hosting-and-deployment), to learn how to deploy your client and your Convex backend together.

You can also deploy your backend on its own. Check out the [Project Configuration page](https://docs.convex.dev/project-configuration) to learn more.

## Staging Environment

With Convex preview deployments your team can test out changes before deploying them to production. If you need a more permanent staging environment, you can use a separate Convex project, and deploy to it by setting the `CONVEX_DEPLOY_KEY` environment variable when running `npx convex deploy`.

## Typical Team Development Workflow

Teams developing on Convex usually follow this workflow:

1. If this is the team's first project, one team member creates a team on the dashboard.
2. One team member creates a project by running `npx convex dev`, perhaps starting with a quickstart or a template.
3. The team member creates a Git repository from the initial code and shares it with their team (via GitHub, GitLab etc.).
4. Other team members pull the codebase, and get their own dev deployments by running `npx convex dev`.
5. All team members can make backend changes and test them out with their individual dev deployments. When a change is ready the team member opens a pull-request (or commits to a shared branch).
6. Snapshot import can be used to populate a dev deployment with a data snapshot from prod deployment.
7. Members of a team with the Pro plan can get separate preview deployments to test each other's pull-requests.
8. Deployment to production can happen automatically when changes get merged to the designated branch (say `main`).
9. Alternatively one of the team members can deploy to production manually by running `npx convex deploy`.

## Making Safe Changes

Especially if your app is live you want to make sure that changes you make to your Convex codebase do not break it.

Some unsafe changes are handled and caught by Convex, but others you need handle yourself.

- **Schema must always match existing data.** Convex enforces this constraint. You cannot push a schema to a deployment with existing data that doesn't match it, unless you turn off schema enforcement. In general it safe to:
  - Add new tables to the schema.
  - Add an optional field to an existing table's schema, set the field on all documents in the table, and then make the field required.
  - Mark an existing field as optional, remove the field from all documents, and then remove the field.
  - Mark an existing field as a union of the existing type and a new type, modify the field on all documents to match the new type, and then change the type to the new type.
- **Functions should be backwards compatible.** Even if your only client is a website, and you deploy it together with your backend, your users might still be running the old version of your website when your backend changes. Therefore you should make your functions backwards compatible until you are OK to break old clients. In general it is safe to:
  - Add new functions.
  - Add an optional named argument to an existing function.
  - Mark an existing named argument as optional.
  - Mark an existing named argument as a union of the existing type and a new type.
  - Change the behavior of the function in such a way that given the arguments from an old client its behavior will still be acceptable to the old client.
- **Scheduled functions should be backwards compatible.** When you schedule a function to run in the future, you provide the argument values it will receive. Whenever a function runs, it always runs its currently deployed version. If you change the function between the time it was scheduled and the time it runs, you must ensure the new version will behave acceptably given the old arguments.

## Using Convex with Vercel
Hosting your Convex app on Vercel allows you to automatically re-deploy both your backend and your frontend whenever you push your code.

### Deploying to Vercel
This guide assumes you already have a working React app with Convex. If not, follow the Convex React Quickstart first. Then:

1. Create a Vercel account
   If you haven't done so, create a Vercel account. This is free for small projects and should take less than a minute to set up.

2. Link your project on Vercel
   Create a Vercel project at https://vercel.com/new and link it to the source code repository for your project on GitHub or other Git platform.

   ![Vercel import project](https://user-images.githubusercontent.com/123456/123456789-abcdef0.png)

3. Override the Build command
   Override the "Build command" to be `npx convex deploy --cmd 'npm run build'`.

   If your project lives in a subdirectory of your repository, you'll also need to change "Root Directory" accordingly.

   ![Vercel build settings](https://user-images.githubusercontent.com/123456/123456789-abcdef0.png)

4. Set up the `CONVEX_DEPLOY_KEY` environment variable
   On your Convex Dashboard, go to your project's Settings page. Click the "Generate Production Deploy Key" button to generate a Production deploy key. Then click the copy button to copy the key.

   In Vercel, click "Environment Variables". Create an environment variable named `CONVEX_DEPLOY_KEY` and paste in your deploy key. Under "Environment", uncheck all except "Production" and click "Save".

   ![Vercel environment variable CONVEX_DEPLOY_KEY](https://user-images.githubusercontent.com/123456/123456789-abcdef0.png)

5. Deploy your site
   Now click the "Deploy" button and your work here is done!

   Vercel will automatically publish your site to a URL like `https://<site-name>.vercel.app`, shown on the page after deploying. Every time you push to your Git repository, Vercel will automatically deploy your Convex functions and publish your site changes.

### How it works
In Vercel, we overrode the Build Command to be `npx convex deploy --cmd 'npm run build'`.

`npx convex deploy` will read `CONVEX_DEPLOY_KEY` from the environment and use it to set the `CONVEX_URL` (or similarly named) environment variable to point to your production deployment.

Your frontend framework of choice invoked by `npm run build` will read the `CONVEX_URL` (or similarly named) environment variable to point your deployed site (via `ConvexReactClient`) at your production deployment.

Finally, `npx convex deploy` will push your Convex functions to your production deployment.

Now, your production deployment has your newest functions, and your app is configured to connect to it.

You can use `--cmd-url-env-var-name` to customize the variable name used by your frontend code if the deploy command cannot infer it, like:

```
npx convex deploy --cmd-url-env-var-name CUSTOM_CONVEX_URL --cmd 'npm run build'
```

### Authentication
You will want to configure your authentication provider (Clerk, Auth0 or other) to accept your production URL. Note that Clerk does not support `https://<site-name>.vercel.app`, so you'll have to configure a custom domain.

### Preview Deployments
Vercel Preview Deployments allow you to preview changes to your app before they're merged in. In order to preview both changes to frontend code and Convex functions, you can set up Convex preview deployments.

This will create a fresh Convex backend for each preview and leave your production and development deployments unaffected.

This assumes you have already followed the steps in "Deploying to Vercel" above.

1. Set up the `CONVEX_DEPLOY_KEY` environment variable
   On your Convex Dashboard, go to your project's Settings page. Click the "Generate Preview Deploy Key" button to generate a Preview deploy key. Then click the copy button to copy the key.

   In Vercel, click "Environment Variables". Create an environment variable named `CONVEX_DEPLOY_KEY` and paste in your deploy key. Under "Environment", uncheck all except "Preview" and click "Save".

   ![Vercel environment variable CONVEX_DEPLOY_KEY](https://user-images.githubusercontent.com/123456/123456789-abcdef0.png)

2. (optional) Set up default environment variables
   If your app depends on certain Convex environment variables, you can set up default environment variables for preview and development deployments in your project.

   ![Project Default Environment Variables](https://user-images.githubusercontent.com/123456/123456789-abcdef0.png)

3. (optional) Run a function to set up initial data
   Vercel Preview Deployments run against fresh Convex backends, which do not share data with development or production Convex deployments. You can call a Convex function to set up data by adding `--preview-run 'functionName'` to the `npx convex deploy` command. This function will only be run for preview deployments and will be ignored when deploying to production.

   ```
   Vercel > Settings > Build & Development settings > Build Command
   npx convex deploy --cmd 'npm run build' --preview-run 'functionName'
   ```

Now test out creating a PR and generating a Preview Deployment! You can find the Convex deployment for your branch in the Convex dashboard.

![Preview Deployment in Deployment Picker](https://user-images.githubusercontent.com/123456/123456789-abcdef0.png)

### How it works
For Preview Deployments, `npx convex deploy` will read `CONVEX_DEPLOY_KEY` from the environment and use it to create a Convex deployment associated with the Git branch name for the Vercel Preview Deployment. It will set the `CONVEX_URL` (or similarly named) environment variable to point to the new Convex deployment.

Your frontend framework of choice invoked by `npm run build` will read the `CONVEX_URL` environment variable and point your deployed site (via `ConvexReactClient`) at the Convex preview deployment.

Finally, `npx convex deploy` will push your Convex functions to the preview deployment and run the `--preview-run` function (if provided). This deployment has separate functions, data, crons, and all other configuration from any other deployments.

`npx convex deploy` will infer the Git branch name for Vercel, Netlify, GitHub, and GitLab environments, but the `--preview-create` option can be used to customize the name associated with the newly created deployment.

Production deployments will work exactly the same as before.

## Custom Domains & Hosting

### Domains

You can configure a custom domain, like `api.example.com`, to serve HTTP actions or Convex functions from your production Convex deployments. The settings for this feature are accessed through the Project Settings page on any of your projects.

#### Add Custom Domain

After you enter a domain, you will be shown which records to set on your DNS provider. Some popular DNS providers that you can use to buy a domain are Cloudflare and GoDaddy. We will verify your domain in the background, and once these records are set, you will see a green checkmark.

When you see that checkmark, your backend will now serve traffic from that domain. The first request may take up to a minute because Convex will have to mint a new SSL certificate.

Reach out to [support@convex.dev](mailto:support@convex.dev) if you have any questions about getting set up!

*Custom domains require a Convex Pro plan.*

### Hosting

If you're using only Convex for backend functionality, you can host your web app on any static hosting provider. This guide will use GitHub Pages as an example.

If you're using Next.js or other framework with server functionality, you'll need to use a provider that supports it, such as Netlify or Vercel. You can still host Next.js statically via a static export.

#### Configure your build

First, make sure that you have a working build process.

In this guide, we'll set up a local build, but your hosting provider might support a remote build. For example, see Vite's [Deploying to GitHub Pages guide](https://vitejs.dev/guide/static-deploy.html#github-pages) which uses GitHub actions.

We'll use Vite and GitHub Pages as an example.

```typescript
// vite.config.mts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  build: {
    outDir: "docs",
  },
  base: "/some-repo-name/",
});
```

The `build.outDir` field specifies where Vite will place the production build, and we use `docs` because that's the directory GitHub Pages allow hosting from.

The `base` field specifies the URL path under which you'll serve your app, in this case, we will serve on `https://<some username>.github.io/<some repo name>`.

#### Configure your hosting provider

With GitHub Pages, you can choose whether you want to include your build output in your main working branch or publish from a separate branch.

Open your repository's GitHub page > Settings > Pages. Under "Build and deployment" > "Source" choose "Deploy from a branch".

Under "branch" choose a branch (if you want to use a separate branch, push at least one commit to it first), and the `/docs` folder name. Hit "Save".

#### Build and deploy to Convex and GitHub Pages

To manually deploy to GitHub pages, follow these steps:

1. Checkout the branch you chose to publish from
2. Run `npx convex deploy --cmd 'npm run build'` and confirm that you want to push your current backend code to your production deployment
3. Commit the build output changes and push to GitHub.

#### How it works

First, `npx convex deploy` runs through these steps:

1. It sets the `VITE_CONVEX_URL` (or similarly named) environment variable to your production Convex deployment.
2. It invokes the frontend framework build process, via `npm run build`. The build process reads the environment variable and uses it to point the built site at your production deployment.
3. It deploys your backend code, from the `convex` directory, to your production deployment.

Afterwards, you deploy the built frontend code to your hosting provider. In this case, you used Git, but for other providers, you might use a different method, such as an old-school FTP request.

You can use `--cmd-url-env-var-name` to customize the variable name used by your frontend code if the deploy command cannot infer it, like:

```
npx convex deploy --cmd-url-env-var-name CUSTOM_CONVEX_URL --cmd 'npm run build'
```

#### Authentication

You will want to configure your authentication provider (Clerk, Auth0 or other) to accept your production URL, where your frontend is served.

## Preview Deployments

Convex preview deployments allow your team to test out backend changes before pushing them to production.

In combination with Vercel Preview Deployments or Netlify Deploy Previews, you can preview both *frontend* and *backend* changes together.

Convex preview deployments require a Convex Pro plan. [Learn more about our plans or upgrade](https://www.convex.com/pricing).

Convex preview deployments are currently a *beta* feature. If you have feedback or feature requests, let us know on [Discord](https://discord.gg/convex)!

### Setup

Follow the Vercel or Netlify hosting guide for setting up frontend and backend previews together, as well as details on how Convex preview deployments work.

See `npx convex deploy --help` for all available options for `npx convex deploy`.

### Limits

Convex preview deployments are automatically cleaned up 14 days after creation, or when a new preview deployment with the same name is created. They can also be manually deleted from the Convex dashboard.

When a Convex preview deployment is deleted, the Vercel/Netlify preview link will open and show UI, but will be unable to run any Convex functions since it is pointing at a Convex deployment that no longer exists. In these cases, re-deploying in Vercel/Netlify should produce a link pointing at a new Convex deployment.

Convex allows a single deployment at a time for a given name (Git branch). When pushing updates to an existing branch, the deployment will be deleted, resulting in a preview link unable to run Convex functions, before it is replaced by a new Convex deployment.

Initial data can be set up on a Convex preview deployment by running a function. There are currently no other ways to set up data on a Convex preview deployment -- viewing changes against a copy of production data or importing data from a different Convex deployment is not supported.

Note that if the function call fails, the deploy command will fail, but the new preview deployment will have already been provisioned. Best course of action is to fix the issue in the function and redeploy.

## Integrations

Convex integrates with a variety of supported third-party tools for log streaming and exception reporting.

### Log Streams

Log Streams enable streaming of log events from your Convex deployment to supported destinations, such as Axiom, Datadog, or a custom webhook.

### Exception Reporting

Exception Reporting gives visibility into errors in your Convex function executions.

### Configuring an Integration

To configure an integration, navigate to the Deployment Settings in the Dashboard, and the "Integrations" tab in the sidebar. This page provides a list of your configured integrations, their current health status, and other integrations available to be configured. To configure an integration, click on the card and follow the setup directions.

![Integrations Page](https://example.com/integrations-page.png)

### Deleting an Integration

To remove an integration and stop further events from being piped out to the configured destination, select the menu icon in the upper-right corner of a configured panel and select "Delete integration". After confirming, the integration will stop running within a few seconds.

### Log Streams

Log streams enable streaming of events such as function executions and `console.log`s from your Convex deployment to supported destinations, such as Axiom, Datadog, or a custom webhook.

The most recent logs produced by your Convex deployment can be viewed in the Dashboard Logs page, the Convex CLI, or in the browser console, providing a quick and easy way to view recent logs.

Log streaming to a third-party destination like Axiom or Datadog enables storing historical logs, more powerful querying and data visualization, and integrations with other tools (e.g., PagerDuty, Slack).

Log streams require a Convex Pro plan. [Learn more about our plans or upgrade](https://www.convex.com/pricing).

*Log streams are currently a beta feature.* If you have feedback or feature requests, let us know on [Discord](https://discord.gg/convex)!

#### Configuring log streams

We currently support the following log streams, with plans to support many more:

- Axiom
- Datadog
- Webhook to a custom URL

See the instructions for [configuring an integration](https://www.convex.com/docs/integrations/log-streams). The specific information needed for each log stream is covered below.

##### Axiom

Configuring an Axiom log stream requires specifying:

- The name of your Axiom dataset
- An Axiom API key
- An optional list of attributes and their values to be included in all log events sent to Axiom. These will be sent via the `attributes` field in the Ingest API.

##### Datadog

Configuring a Datadog log stream requires specifying:

- The site location of your Datadog deployment
- A Datadog API key
- A comma-separated list of tags that will be passed using the `ddtags` field in all payloads sent to Datadog. This can be used to include any other metadata that can be useful for querying or categorizing your Convex logs ingested by your Datadog deployment.

##### Webhook

A webhook log stream is the simplest and most generic stream, allowing piping logs via POST requests to any URL you configure. The only parameter required to set up this stream is the desired webhook URL.

A request to this webhook contains as its body a JSON array of events in the schema defined below.

*Info: Log streams configured before May 23, 2024 will use the legacy format documented on this page. We recommend updating your log stream to use the new format.*

Log events have a well-defined JSON schema that allow building complex, type-safe pipelines ingesting log events.

This data model is currently in beta, so is subject to change.

All events will have the following two fields:

- `topic`: `string`, categorizes a log event, one of `["verification", "console", "function_execution", "audit_log"]`
- `timestamp`: `number`, Unix epoch timestamp in milliseconds as an integer

`verification` events

This is an event sent to confirm the log stream is working. Schema:

- `topic`: `"verification"`
- `timestamp`: Unix epoch timestamp in milliseconds
- `message`: `string`

`console` events

Convex function logs via the `console` API.

Schema:

- `topic`: `"console"`
- `timestamp`: Unix epoch timestamp in milliseconds
- `function`: `object`, see `function` fields
- `log_level`: `string`, one of `["DEBUG", "INFO", "LOG", "WARN", "ERROR"]`
- `message`: `string`, the object-inspect representation of the `console.log` payload
- `is_truncated`: `boolean`, whether this message was truncated to fit within our logging limits
- `system_code`: `optional string`, present for automatically added warnings when functions are approaching limits

Example event for `console.log("Sent message!")` from a mutation:

```json
{
    "topic": "console",
    "timestamp": 1715879172882,
    "function": {
      "path": "messages:send",
      "request_id": "d064ef901f7ec0b7",
      "type": "mutation"
    },
    "log_level": "LOG",
    "message": "'Sent message!'"
}
```

`function_execution` events

These events occur whenever a function is run.

Schema:

- `topic`: `"console"`
- `timestamp`: Unix epoch timestamp in milliseconds
- `function`: `object`, see `function` fields
- `execution_time_ms`: `number`, the time in milliseconds this function took to run
- `status`: `string`, one of `["success", "failure"]`
- `error_message`: `string`, present for functions with status `failure`, containing the error and any stack trace
- `usage`:
  - `database_read_bytes`: `number`
  - `database_write_bytes`: `number`, this and `database_read_bytes` make up the database bandwidth used by the function
  - `file_storage_read_bytes`: `number`
  - `file_storage_write_bytes`: `number`, this and `file_storage_read_bytes` make up the file bandwidth used by the function
  - `vector_storage_read_bytes`: `number`
  - `vector_storage_write_bytes`: `number`, this and `vector_storage_read_bytes` make up the vector bandwidth used by the function
  - `action_memory_used_mb`: `number`, for actions, the memory used in MiB. This combined with `execution_time_ms` makes up the action compute

Example event for a query:

```json
{
  "data": {
    "execution_time_ms": 294,
    "function": {
      "cached": false,
      "path": "message:list",
      "request_id": "892104e63bd39d9a",
      "type": "query"
    },
    "status": "success",
    "timestamp": 1715973841548,
    "topic": "function_execution",
    "usage": {
      "database_read_bytes": 1077,
      "database_write_bytes": 0,
      "file_storage_read_bytes": 0,
      "file_storage_write_bytes": 0,
      "vector_storage_read_bytes": 0,
      "vector_storage_write_bytes": 0
    }
  }
}
```

Function fields

The following fields are added under `function` for all `console` and `function_execution` events:

- `type`: `string`, one of `["query", "mutation", "action", "http_action"]`
- `path`: `string`, e.g., `"myDir/myFile:myFunction"`, or `"POST /my_endpoint"`
- `cached`: `optional boolean`, for queries this denotes whether this event came from a cached function execution
- `request_id`: `string`, the request ID of the function

`audit_log` events

These events represent changes to your deployment, which also show up in the History tab in the dashboard.

Schema:

- `topic`: `"audit_log"`
- `timestamp`: Unix epoch timestamp in milliseconds
- `audit_log_action`: `string`, e.g., `"create_environment_variable"`, `"push_config"`, `"change_deployment_state"`
- `audit_log_metadata`: `string`, stringified JSON holding metadata about the event. The exact format of this event may change.

Example `push_config` audit log:

```json
{
  "topic": "audit_log",
  "timestamp": 1714421999886,
  "audit_log_action": "push_config",
  "audit_log_metadata": "{\"auth\":{\"added\":[],\"removed\":[]},\"crons\":{\"added\":[],\"deleted\":[],\"updated\":[]},..."
}
```

#### Guarantees

Log events provide a best-effort delivery guarantee. Log streams are buffered in-memory and sent out in batches to your deployment's configured streams. This means that logs can be dropped if ingestion throughput is too high. Similarly, due to network retries, it is possible for a log event to be duplicated in a log stream.

That's it! Your logs are now configured to stream out. If there is a log streaming destination that you would like to see supported, please [let us know](https://discord.gg/convex)!

### Exception Reporting

Configure exception reporting to gain visibility into errors from your Convex function executions. Convex supports integration with Sentry.

Currently, exception reporting is only available to Pro users.

### Exception Reporting Integrations are in beta

Exception Reporting Integrations are currently a beta feature. If you have feedback or feature requests, let us know on Discord!

### Configuring Sentry

To configure Sentry, navigate to the Deployment Settings in the Dashboard, and the "Integrations" tab in the sidebar.

![Integrations Page](https://via.placeholder.com/150)

Click on the Sentry card and follow the setup directions. You will need your Sentry DSN.

![Configure sentry](https://via.placeholder.com/150)

### Supported Tags

Convex automatically tags exception events on their way to Sentry with the following tags:

- `func`: The name of the running function in string format
- `func_type`: One of `["query", "mutation", "action", "http_action"]`
- `func_runtime`: One of the function runtimes - `["default", "node"]`
- `request_id`: The request ID of the function that errored
- `server_name`: The name of the deployment, e.g., `happy-animal-123`
- `environment`: One of `["prod", "dev", "preview"]`
- `user`: If the function is authenticated, then the `tokenIdentifier` is used as the user ID on Sentry. The `tokenIdentifier` is a stable and globally unique string representing the authenticated user.

### Sentry Notes

1. Sentry Exceptions may take a minute or two to propagate to Sentry.
2. Convex's built-in Sentry support does not yet support the advanced customization provided by the Sentry SDK.

### Streaming Data in and out of Convex

Fivetran and Airbyte are data integration platforms that allow you to sync your Convex data with other databases.

Fivetran enables streaming export from Convex to any of their supported destinations. The Convex team maintains a Convex source connector, for streaming export. Streaming import into Convex via Fivetran is not supported at the moment.

Using Airbyte enables streaming import from any of their supported sources into Convex and streaming export from Convex into any of their supported destinations. The Convex team maintains a Convex source connector for streaming export and a Convex destination connector for streaming import.

#### Fivetran & Airbyte integrations are in beta

Fivetran & Airbyte integrations are currently a beta feature. If you have feedback or feature requests, let us know on Discord!

#### Streaming Export

Exporting data can be useful for handling workloads that aren't supported by Convex directly. Some use cases include:

1. **Analytics**: Convex isn't optimized for queries that load huge amounts of data. A data platform like Databricks or Snowflake is more appropriate.
2. **Flexible querying**: While Convex has powerful database queries and built-in full text search support, there are still some queries that are difficult to write within Convex. If you need very dynamic sorting and filtering for something like an "advanced search" view, databases like ElasticSearch can be helpful.
3. **Machine learning training**: Convex isn't optimized for queries running computationally intensive machine learning algorithms.

Streaming export requires a Convex Pro plan. Learn more about our plans or upgrade.

See the [Fivetran](https://fivetran.com/) or [Airbyte](https://airbyte.com/) docs to learn how to set up a streaming export. Contact us if you need help or have questions.

#### Streaming Import

Adopting new technologies can be a slow, daunting process, especially when the technologies involve databases. Streaming import enables adopting Convex alongside your existing stack without having to write your own migration or data sync tooling. Some use cases include:

- Prototyping how Convex could replace your project's existing backend using its own data.
- Building new products faster by using Convex alongside existing databases.
- Developing a reactive UI-layer on top of an existing dataset.
- Migrating your data to Convex (if the CLI tool doesn't meet your needs).

##### Make imported tables read-only

A common use case is to "mirror" a table in the source database to Convex to build something new using Convex. We recommend leaving imported tables as read-only in Convex because syncing the results back to the source database could result in dangerous write conflicts. While Convex doesn't yet have access controls that would ensure a table is read-only, you can make sure that there are no mutations or actions writing to imported tables in your code and avoid editing documents in imported tables in the dashboard.

## Status and Guarantees

Please contact us with any specific requirements or if you want to build a project on Convex that is not yet satisfied by our guarantees.

### Guarantees

The official Convex Terms of Service, Privacy Policy and Customer Agreements are outlined in our official terms. We do not yet have contractual agreements beyond what is listed in our official terms and the discussions within this document don't constitute an amendment to these terms.

Convex is always under continual development and future releases may require code changes in order to upgrade to a new version. *Code developed on Convex 1.0 or later will continue to operate as-is.* If we are required to make a breaking change in future we will contact teams directly to provide substantial advance notice.

All user data in Convex is encrypted at rest. Database state is replicated durably across multiple physical availability zones. Regular periodic and incremental database backups are performed and stored with 99.999999999% (11 9's) durability.

We target an availability of 99.99% (4 9's) for Convex deployments although these may experience downtime for maintenance without notice. A physical outage may affect availability of a deployment but will not affect durability of the data stored in Convex.

### Limits

For information on limits, see [here](https://example.com).

### Beta Features

Features tagged with beta in these docs are still in development. They can be used in production but their APIs might change in the future, requiring additional effort when upgrading to a new version of the Convex NPM package and other Convex client libraries.

### Future Features

Convex is still under very active development and here we list some of the missing functionality on our radar. We'd love to hear more about your requirements in the [Convex Discord Community](https://example.com).

#### Authorization

Convex currently has an authentication framework which verifies user identities. In the future we plan to add an authorization framework which will allow developers to define what data a user can access.

For now, you can implement manual authorization checks within your queries and mutations, but stay tuned for a more comprehensive, fool-proof solution in the future.

#### Telemetry

Currently, the dashboard provides only basic metrics. Serious sites at scale are going to need to integrate our logs and metrics into more fully fledged observability systems that categorize them and empower things like alerting.

Convex will eventually have methods to publish deployment data in formats that can be ingested by third parties.

#### Analytics / OLAP

Convex is designed to primarily service all your app's realtime implementation (OLTP) needs. It is less suited to be a good solution for the kinds of complex queries and huge table scans that are necessary to address the requirements of analytics (OLAP) use cases.

Convex exposes Fivetran and Airbyte connectors to export Convex data to external analytics systems.

## Limits

We'd love for you to have unlimited joy building on Convex but engineering practicalities dictate a few limits. This page outlines current limits in the Convex ecosystem.

Many of these limits will become more permissive over time. Please get in touch if any are prohibitive for your application.

Limits are applied per team unless stated otherwise.

### Team

| Plan | Starter | Professional |
| --- | --- | --- |
| Developers | 1-2 | 1-20 |
| Price | $25/member per month | - |

### Projects

| Plan | Starter | Professional |
| --- | --- | --- |
| Projects | 20 | 100 |

### Database

| Plan | Starter | Professional | Notes |
| --- | --- | --- | --- |
| Storage | 0.5 GiB | 50 GiB included | Includes database rows and indexes but not files or snapshots. $0.20/month per additional GiB |
| Bandwidth | 1 GiB/month | 50 GiB/month included | Document and index data transferred between Convex functions and the underlying database. $0.20 per additional GiB |
| Tables | 10,000 | 10,000 | Per deployment. |
| Indexes per table | 32 | 32 | - |
| Fields per index | 16 | 16 | - |
| Index name length | 64 characters | 64 characters | - |

**Restrictions:**
- Table and index names must be valid identifiers and cannot start with an underscore.

### Documents

Applied per document and to any nested Object unless stated otherwise.

| Notes | Value |
| --- | --- |
| Size | 1 MiB |
| Fields | 1024 | The number of fields/keys |
| Field name length | 64 characters | Nested Object keys can have length up to 1024 characters. |
| Field nesting depth | 16 | How many times objects and arrays can be nested, e.g. [[[[]]]] |
| Array elements | 8192 | - |

**Restrictions:**
- Field names must only contain non-control alphanumeric ASCII characters and underscores and must start with an alphabetic character or underscore.
- Strings must be valid Unicode sequences with no unpaired surrogates.

### Functions

| Plan | Starter | Professional | Notes |
| --- | --- | --- | --- |
| Function calls | 1,000,000/month | 25,000,000/month included | Explicit client calls, scheduled executions, subscription updates, and file accesses count as function calls. $2 per additional 1,000,000 |
| Action execution | 20 GiB-hours | 250 GiB-hours included | Convex runtime: 64 MiB RAM. Node.js runtime: 512 MiB RAM. $0.30/GiB-hour additional |
| Code size | 32 MiB | 32 MiB | Per deployment. |
| Function argument size | 8 MiB | 8 MiB | - |
| Function return value size | 8 MiB | 8 MiB | - |
| HTTP action response size | 20 MiB | 20 MiB | There is no specific limit on request size. |
| Number of documents scanned in a query | 16384 | 16384 | Documents not returned due to a filter count as scanned. |
| Number of documents written in a mutation | 8192 | 8192 | - |
| Length of a console.log line | 4 KiB | 4 KiB | - |
| Log streaming limits | 4096 logs, flushed every 10 seconds | 4096 logs, flushed every 10 seconds | How many logs can be buffered when streaming. |

### Execution Time and Scheduling

| Notes | Value |
| --- | --- |
| Query/mutation execution time | 1 second | Limit applies only to user code and doesn't include database operations. |
| Action execution time | 10 minutes | - |
| Scheduled functions | 1000 | The number of other functions a single mutation can schedule. |
| Total size of scheduled functions' arguments | 8 MiB | Applies only to mutations. |
| Concurrent IO operations per function | 1000 | The number of IO operations a single function can perform, e.g., a database operation, or a fetch request in an action. |
| Outstanding scheduled functions | 1,000,000 | - |

### Transactions

These limits apply to each query or mutation function.

| Notes | Value |
| --- | --- |
| Data read | 8 MiB |
| Data written | 8 MiB |

### Environment Variables

Applied per-deployment.

| Notes | Value |
| --- | --- |
| Number of variables | 100 |
| Maximum name length | 40 characters |
| Maximum value size | 8 KiB |

### File Storage

| Plan | Starter | Professional |
| --- | --- | --- |
| Storage | 1 GiB | 100 GiB included |
| Bandwidth | 1 GiB/month | 50 GiB/month included |
| Price | - | $0.03/month per additional GiB |
| Price | - | $0.30 per additional GiB |

### Full Text Search

*Full text search is currently a beta feature. If you have feedback or feature requests, let us know on Discord!*

| Value |
| --- |
| Search indexes per table | 4 |
| Filters per search index | 16 |
| Terms per search query | 16 |
| Filters per search query | 8 |
| Maximum term length | 32 B |
| Maximum result set | 1024 |

### Vector Search

*Vector search is currently a beta feature. If you have feedback or feature requests, let us know on Discord!*

| Value |
| --- |
| Vector indexes per table | 4 |
| Filters per vector index | 16 |
| Terms per search query | 16 |
| Vectors to search by | 1 |
| Dimension fields | 1 (value between 2-4096) |
| Filters per search query | 64 |
| Maximum term length | 32 B |
| Maximum result set | 256 (defaults to 10) |

If any of these limits don't work for you, let us know!

Please see our [plans and pricing page](https://www.convex.com/pricing) for resource limits. After these limits are hit on a free plan, new mutations that attempt to commit more insertions or updates may fail. Paid plans have no hard resource limits - they can scale to billions of documents and TBs of storage.

## Environment Variables

Environment variables are key-value pairs that are useful for storing values you wouldn't want to put in code or in a table, such as an API key. You can set environment variables in Convex through the dashboard, and you can access them in functions using `process.env`.

### Setting Environment Variables

Under *Deployment Settings* in the Dashboard, you can see a list of environment variables in the current deployment.

You can add up to 100 environment variables. Environment variable names cannot be more than 40 characters long, and they must start with a letter and only contain letters, numbers, and underscores. Environment variable values cannot be larger than 8KB.

You can modify environment variables using the pencil icon button:

![Edit Environment Variable](edit-environment-variable.png)

Environment variables can also be viewed and modified with the command line:

```
npx convex env list
npx convex env set API_KEY secret-api-key
```

### Using Environment Variables in Dev and Prod Deployments

Since environment variables are set per-deployment, you can use different values for the same key in dev and prod deployments. This can be useful for when you have different external accounts you'd like to use depending on the environment. For example, you might have a dev and prod SendGrid account for sending emails, and your function expects an environment variable called `SENDGRID_API_KEY` that should work in both environments.

If you expect an environment variable to be always present in a function, you must add it to all your deployments. In this example, you would add an environment variable with the name `SENDGRID_API_KEY` to your dev and prod deployments, with a different value for dev and prod.

### Accessing Environment Variables

You can access environment variables in Convex functions using `process.env.KEY`. If the variable is set, it is a string; otherwise, it is `undefined`. Here is an example of accessing an environment variable with the key `GIPHY_KEY`:

```javascript
function giphyUrl(query) {
  return (
    "https://api.giphy.com/v1/gifs/translate?api_key=" +
    process.env.GIPHY_KEY +
    "&s=" +
    encodeURIComponent(query)
  );
}
```

Note that you should not condition your Convex function exports on environment variables. The set of Convex functions that can be called is determined during deployment and is not reevaluated when you change an environment variable. The following code will throw an error at runtime if the `DEBUG` environment variable changes between deployment and calling the function:

```javascript
// THIS WILL NOT WORK!
export const myFunc = process.env.DEBUG ? mutation(...) : internalMutation(...);
```

Similarly, environment variables used in cron definitions will only be reevaluated on deployment.

### System Environment Variables

The following environment variables are always available in Convex functions:

- `CONVEX_CLOUD_URL` - Your deployment URL (e.g., `https://dusty-nightingale-847.convex.cloud`) for use with Convex clients.
- `CONVEX_SITE_URL` - Your deployment site URL (e.g., `https://dusty-nightingale-847.convex.site`) for use with HTTP Actions.

### Project Environment Variable Defaults

You can set up default environment variable values for a project for development and preview deployments in *Project Settings*.

![Project Default Environment Variables](project-default-environment-variables.png)

These default values will be used when creating a new development or preview deployment, and will have no effect on existing deployments (they are not kept in sync).

The *Deployment Settings* will indicate when a deployment has environment variables that do not match the project defaults.

## Project Configuration

### Local development

When you're developing locally you need two pieces of information:

1. The name of your dev deployment. This is where your functions are pushed to and served from. It is stored in the `CONVEX_DEPLOYMENT` environment variable. `npx convex dev` writes it to the `.env.local` file.
2. The URL of your dev deployment, for your client to connect to. The name of the variable and which file it can be read from varies between client frameworks. `npx convex dev` writes the URL to the `.env.local` or `.env` file.

### Production deployment

You should only be deploying to your production deployment once you have tested your changes on your local deployment. When you're ready, you can deploy either via a hosting/CI provider or from your local machine.

For a CI environment you can follow the hosting docs. `npx convex deploy` run by the CI pipeline will use the `CONVEX_DEPLOY_KEY`, and the frontend build command will use the deployment URL variable, both configured in your CI environment.

You can also deploy your backend from your local machine. `npx convex deploy` will ask for a confirmation and then deploy to the production deployment in the same project as your configured development `CONVEX_DEPLOYMENT`.

`convex.json`

Additional project configuration can be specified in the `convex.json` file in the root of your project (in the same directory as your `package.json`).

The file supports the following configuration options:

Changing the `convex/` folder name or location

You can choose a different name or location for the `convex/` folder via the `functions` field. For example, Create React App doesn't allow importing from outside the `src/` directory, so if you're using Create React App you should have the following config:

```json
{
  "functions": "src/convex/"
}
```

### Installing packages on the server

You can specify which packages used by Node actions should be installed on the server, instead of being bundled, via the `node.externalPackages` field. Read more.

### Importing the generated functions API via `require()` syntax

The Convex code generation can be configured to generate a CommonJS-version of the `_generated/api.js` file via the `generateCommonJSApi` field. Read more.

## Pausing a Deployment

Pausing a deployment is a way to "turn off" a deployment without deleting any data. This can be useful if you have an action that is blowing through a third-party API quota and you just need a big red stop button.

When a deployment is paused:

1. New function calls will return an error.
2. Scheduled jobs will queue and run when the deployment is resumed.
3. Cron jobs will be skipped.
4. Everything else (e.g. code push, dashboard edits) should work as usual.

*This is important!* All new function calls will return an error when a deployment is paused, so if you are running an app in production you may want to consider alternatives like pushing code that disables a feature you are trying to "turn off". We recommend testing this feature in a dev deployment first before pausing a production deployment.

### Pause Deployment Button

A deployment can be resumed with this button on the same page:

## Best Practices

Here's a collection of our recommendations on how best to use Convex to build your application. If you want guidance specific to your app's needs or have discovered other ways of using Convex, message us on Discord!

### Use TypeScript

All Convex libraries have complete type annotations and using these types is a great way to learn the framework.

Even better, Convex supports code generation to create types that are specific to your app's schema and Convex functions.

Code generation is run automatically by `npx convex dev`.

### Functions

1. Use argument validation in all public functions.
   Argument validation prevents malicious users from calling your functions with the wrong types of arguments. It's okay to skip argument validation for internal functions because they are not publicly accessible.

2. Use `console.log` to debug your Convex functions.
   All server-side logs from Convex functions are shown on the dashboard Logs page. If a server-side exception occurs, it will also be logged as an error event.

   On a dev deployment, the logs will also be forwarded to the client and will show up in the browser developer tools Console for the user who invoked the function call, including full server error messages and server-side stack traces.

3. Use helper functions to write shared code.
   Write helper functions in your `convex/` directory and use them within your Convex functions. Helpers can be a powerful way to share business logic, authorization code, and more.

   Helper functions allow sharing code while still executing the entire query or mutation in a single transaction. For actions, sharing code via helper functions instead of using `ctx.runAction` reduces function calls and resource usage.

See the [TypeScript page](https://docs.convex.dev/typescript) for useful types.

```typescript
// convex/teams.ts
import { QueryCtx, mutation } from "./_generated/server";
import { v } from "convex/values";
import { getCurrentUser } from "./userHelpers";
import { Doc, Id } from "./_generated/dataModel";

export const remove = mutation({
  args: { teamId: v.id("teams") },
  handler: async (ctx, { teamId }) => {
    const currentUser = await getCurrentUser(ctx);
    await ensureTeamAdmin(ctx, currentUser, teamId);
    await ctx.db.delete(teamId);
  },
});

async function ensureTeamAdmin(
  ctx: QueryCtx,
  user: Doc<"users">,
  teamId: Id<"teams">,
) {
  // use `ctx.db` to check that `user` is a team admin and throw an error otherwise
}
```

```typescript
// convex/userHelpers.ts
import { Doc } from "./_generated/dataModel";
import { QueryCtx } from "./_generated/server";

export async function getCurrentUser(ctx: QueryCtx): Promise<Doc<"users">> {
  // load user details using `ctx.auth` and `ctx.db`
}
```

### Prefer queries and mutations over actions

You should generally avoid using actions when the same goal can be achieved using queries or mutations. Since actions can have side effects, they can't be automatically retried nor their results cached. Actions should be used in more limited scenarios, such as calling third-party services.

### Database

1. Use indexes or paginate all large database queries.
   Database indexes with range expressions allow you to write efficient database queries that only scan a small number of documents in the table. Pagination allows you to quickly display incremental lists of results. If your table could contain more than a few thousand documents, you should consider pagination or an index with a range expression to ensure that your queries stay fast.

   For more details, check out our [Introduction to Indexes and Query Performance](https://docs.convex.dev/indexes-and-query-performance) article.

2. Use tables to separate logical object types.
   Even though Convex does support nested documents, it is often better to put separate objects into separate tables and use IDs to create references between them. This will give you more flexibility when loading and querying documents.

   You can read more about this at [Document IDs](https://docs.convex.dev/document-ids).

### UI patterns

1. Check for `undefined` to determine if a query is loading.
   The `useQuery` React hook will return `undefined` when it is first mounted, before the query has been loaded from Convex. Once a query is loaded, it will never be `undefined` again (even as the data reactively updates). `undefined` is not a valid return type for queries (you can see the types that Convex supports at [Data Types](https://docs.convex.dev/data-types)).

   You can use this as a signal for when to render loading indicators and placeholder UI.

2. Add optimistic updates for the interactions you want to feel snappy.
   By default, all relevant `useQuery` hooks will update automatically after a mutation is synced from Convex. If you would like some interactions to happen even faster, you can add optimistic updates to your `useMutation` calls so that the UI updates instantaneously.

3. Use an exception handling service and error boundaries to manage errors.
   Inevitably, your Convex functions will have bugs and hit exceptions. If you have an exception handling service and error boundaries configured, you can ensure that you hear about these errors and your users see appropriate UI.

   See [Error Handling](https://docs.convex.dev/error-handling) for more information.

### TypeScript

Convex provides end-to-end type support when Convex functions are written in TypeScript.

You can gradually add TypeScript to a Convex project: the following steps provide progressively better type support. For the best support you'll want to complete them all.

#### Writing Convex Functions in TypeScript

The first step to improving type support in a Convex project is to writing your Convex functions in TypeScript by using the `.ts` extension.

If you are using argument validation, Convex will infer the types of your functions arguments automatically:

```typescript
// convex/sendMessage.ts
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export default mutation({
  args: {
    body: v.string(),
    author: v.string(),
  },
  // Convex knows that the argument type is `{body: string, author: string}`.
  handler: async (ctx, args) => {
    const { body, author } = args;
    await ctx.db.insert("messages", { body, author });
  },
});
```

Otherwise you can annotate the arguments type manually:

```typescript
// convex/sendMessage.ts
import { internalMutation } from "./_generated/server";

export default internalMutation({
  // To convert this function from JavaScript to
  // TypeScript you annotate the type of the arguments object.
  handler: async (ctx, args: { body: string; author: string }) => {
    const { body, author } = args;
    await ctx.db.insert("messages", { body, author });
  },
});
```

This can be useful for internal functions accepting complicated types.

If TypeScript is installed in your project `npx convex dev` and `npx convex deploy` will typecheck Convex functions before sending code to the Convex backend.

Convex functions are typechecked with the `tsconfig.json` in the Convex folder: you can modify some parts of this file to change typechecking settings, or delete this file to disable this typecheck.

You'll find most database methods have a return type of `Promise<any>` until you add a schema.

#### Adding a Schema

Once you define a schema the type signature of database methods will be known. You'll also be able to use types imported from `convex/_generated/dataModel` in both Convex functions and clients written in TypeScript (React, React Native, Node.js etc.).

The types of documents in tables can be described using the `Doc` type from the generated data model and references to documents can be described with parametrized `Document IDs`.

```typescript
// convex/messages.ts
import { query } from "./_generated/server";

export const list = query({
  args: {},
  // The inferred return type of `handler` is now `Promise<Doc<"messages">[]>`
  handler: (ctx) => {
    return ctx.db.query("messages").collect();
  },
});
```

#### Type Annotating Server-Side Helpers

When you want to reuse logic across Convex functions you'll want to define helper TypeScript functions, and these might need some of the provided context, to access the database, authentication and any other Convex feature.

Convex generates types corresponding to documents and IDs in your database, `Doc` and `Id`, as well as `QueryCtx`, `MutationCtx` and `ActionCtx` types based on your schema and declared Convex functions:

```typescript
// convex/helpers.ts
// Types based on your schema
import { Doc, Id } from "./_generated/dataModel";
// Types based on your schema and declared functions
import {
  QueryCtx,
  MutationCtx,
  ActionCtx,
  DatabaseReader,
  DatabaseWriter,
} from "./_generated/server";
// Types that don't depend on schema or function
import {
  Auth,
  StorageReader,
  StorageWriter,
  StorageActionWriter,
} from "convex/server";

// Note that a `MutationCtx` also satisfies the `QueryCtx` interface
export function myReadHelper(ctx: QueryCtx, id: Id<"channels">) {
  /* ... */
}

export function myActionHelper(ctx: ActionCtx, doc: Doc<"messages">) {
  /* ... */
}
```

#### Inferring Types from Validators

Validators can be reused between argument validation and schema validation. You can use the provided `Infer` type to get a TypeScript type corresponding to a validator:

```typescript
// convex/helpers.ts
import { Infer, v } from "convex/values";

export const courseValidator = v.union(
  v.literal("appetizer"),
  v.literal("main"),
  v.literal("dessert"),
);

// The corresponding type can be used in server or client-side helpers:
export type Course = Infer<typeof courseValidator>;
// is inferred as `'appetizer' | 'main' | 'dessert'`
```

#### Document Types Without System Fields

All documents in Convex include the built-in `_id` and `_creationTime` fields, and so does the generated `Doc` type. When creating or updating a document you might want use the type without the system fields. Convex provides `WithoutSystemFields` for this purpose:

```typescript
// convex/helpers.ts
import { MutationCtx } from "./_generated/server";
import { WithoutSystemFields } from "convex/server";
import { Doc } from "./_generated/dataModel";

export async function insertMessageHelper(
  ctx: MutationCtx,
  values: WithoutSystemFields<Doc<"messages">>,
) {
  // ...
  await ctx.db.insert("messages", values);
  // ...
}
```

#### Writing Frontend Code in TypeScript

All Convex JavaScript clients, including React hooks like `useQuery` and `useMutation` provide end to end type safety by ensuring that arguments and return values match the corresponding Convex functions declarations. For React, install and configure TypeScript so you can write your React components in `.tsx` files instead of `.jsx` files.

Follow our React or Next.js quickstart to get started with Convex and TypeScript.

#### Type Annotating Client-Side Code

When you want to pass the result of calling a function around your client codebase, you can use the generated types `Doc` and `Id`, just like on the backend:

```typescript
// src/App.tsx
import { Doc, Id } from "../convex/_generated/dataModel";

function Channel(props: { channelId: Id<"channels"> }) {
  // ...
}

function MessagesView(props: { message: Doc<"messages"> }) {
  // ...
}
```

You can also declare custom types inside your backend codebase which include `Docs` and `Ids`, and import them in your client-side code.

You can also use `WithoutSystemFields` and any types inferred from validators via `Infer`.

#### Using Inferred Function Return Types

Sometimes you might want to annotate a type on the client based on whatever your backend function returns. Beside manually declaring the type (on the backend or on the frontend), you can use the generic `FunctionReturnType` and `UsePaginatedQueryReturnType` types with a function reference:

```typescript
// src/Components.tsx
import { FunctionReturnType } from "convex/server";
import { UsePaginatedQueryReturnType } from "convex/react";
import { api } from "../convex/_generated/api";

export function MyHelperComponent(props: {
  data: FunctionReturnType<typeof api.myFunctions.getSomething>;
}) {
  // ...
}

export function MyPaginationHelperComponent(props: {
  paginatedData: UsePaginatedQueryReturnType<
    typeof api.myFunctions.getSomethingPaginated
  >;
}) {
  // ...
}
```

#### Turning Strings into Valid Document IDs

See [Serializing IDs](https://docs.convex.dev/docs/guides/serializing-ids).

#### Required TypeScript Version

Convex requires TypeScript version 5.0.3 or newer.

# CLI
The Convex command-line interface (CLI) is your interface for managing Convex projects and Convex functions.

To install the CLI, run:

```
npm install convex
```

You can view the full list of commands with:

```
npx convex
```

## Configure
### Create a new project
The first time you run

```
npx convex dev
```

it will ask you to log in your device and create a new Convex project. It will then create:

- The `convex/` directory: This is the home for your query and mutation functions.
- `.env.local` with `CONVEX_DEPLOYMENT` variable: This is the main configuration for your Convex project. It is the name of your development deployment.

### Recreate project configuration
Run

```
npx convex dev
```

in a project directory without a set `CONVEX_DEPLOYMENT` to configure a new or existing project.

### Log out
```
npx convex logout
```

Remove the existing Convex credentials from your device, so subsequent commands like `npx convex dev` can use a different Convex account.

## Develop
### Run the Convex dev server
```
npx convex dev
```

Watches the local filesystem. When you change a function or the schema, the new versions are pushed to your dev deployment and the generated types in `convex/_generated` are updated.

### Open the dashboard
```
npx convex dashboard
```

Open the Convex dashboard.

### Open the docs
```
npx convex docs
```

Get back to these docs!

### Run Convex functions
```
npx convex run <functionName> [args]
```

Run a public or internal Convex query, mutation, or action on your development deployment.

Arguments are specified as a JSON object.

```
npx convex run messages:send '{"body": "hello", "author": "me"}'
```

Add `--watch` to live update the results of a query. Add `--push` to push local code to the deployment before running the function.

Use `--prod` to run functions in the production deployment for a project.

### Tail deployment logs
```
npx convex logs
```

This pipes logs from your dev deployment to your console. This can be followed with `--prod` to tail the prod deployment logs instead.

You can also simultaneously deploy code to the Convex dev deployment, watching for filesystem changes, and pipe logs generated on your dev deployment to your console:

```
npx convex dev --tail-logs
```

### Import data from a file
```
npx convex import --table <tableName> <path>
npx convex import <path>.zip
```

See description and use-cases: snapshot import.

### Export data to a file
```
npx convex export --path <directoryPath>
npx convex export --path <filePath>.zip
npx convex export --include-file-storage --path <path>
```

See description and use-cases: snapshot export.

### Display data from tables
```
npx convex data  # lists tables
npx convex data <table>
```

Display a simple view of the dashboard data page in the command line.

The command supports `--limit` and `--order` flags to change data displayed. For more complex filters, use the dashboard data page or write a query.

The `npx convex data <table>` command works with system tables, such as `_storage`, in addition to your own tables.

### Read and write environment variables
```
npx convex env list
npx convex env get <name>
npx convex env set <name> <value>
npx convex env remove <name>
```

See and update the deployment environment variables which you can otherwise manage on the dashboard environment variables settings page.

## Deploy
### Deploy Convex functions to production
```
npx convex deploy
```

The target deployment to push to is determined like this:

1. If the `CONVEX_DEPLOY_KEY` environment variable is set (typical in CI), then it is the deployment associated with that key.
2. If the `CONVEX_DEPLOYMENT` environment variable is set (typical during local development), then the target deployment is the production deployment of the project that the deployment specified by `CONVEX_DEPLOYMENT` belongs to. This allows you to deploy to your prod deployment while developing against your dev deployment.

This command will:

- Run a command if specified with `--cmd`. The command will have `CONVEX_URL` (or similar) environment variable available:
  ```
  npx convex deploy --cmd "npm run build"
  ```
  You can customize the URL environment variable name with `--cmd-url-env-var-name`:
  ```
  npx convex deploy --cmd 'npm run build' --cmd-url-env-var-name CUSTOM_CONVEX_URL
  ```
- Typecheck your Convex functions.
- Regenerate the generated code in the `convex/_generated` directory.
- Bundle your Convex functions and their dependencies.
- Push your functions, indexes, and schema to production.

Once this command succeeds, the new functions will be available immediately.

### Deploy Convex functions to a preview deployment
```
npx convex deploy
```

When run with the `CONVEX_DEPLOY_KEY` environment variable containing a Preview Deploy Key, this command will:

- Create a deployment with the specified name. `npx convex deploy` will infer the Git branch name for Vercel, Netlify, GitHub, and GitLab environments, but the `--preview-create` option can be used to customize the name associated with the newly created deployment.
  ```
  npx convex deploy --preview-create my-branch-name
  ```
- Run a command if specified with `--cmd`. The command will have `CONVEX_URL` (or similar) environment variable available:
  ```
  npx convex deploy --cmd "npm run build"
  ```
  You can customize the URL environment variable name with `--cmd-url-env-var-name`:
  ```
  npx convex deploy --cmd 'npm run build' --cmd-url-env-var-name CUSTOM_CONVEX_URL
  ```
- Typecheck your Convex functions.
- Regenerate the generated code in the `convex/_generated` directory.
- Bundle your Convex functions and their dependencies.
- Push your functions, indexes, and schema to the deployment.
- Run a function specified by `--preview-run` (similar to the `--run` option for `npx convex dev`).
  ```
  npx convex deploy --preview-run myFunction
  ```

See the Vercel or Netlify hosting guide for setting up frontend and backend previews together.

### Update generated code
```
npx convex codegen
```

Update the generated code in `convex/_generated` without pushing. This can be useful for orchestrating build steps in CI.

# Generated Code

Convex uses code generation to create code that is specific to your app's data model and API. Convex generates JavaScript files (`.js`) with TypeScript type definitions (`.d.ts`).

Code generation isn't required to use Convex, but using the generated code will give you *more better* autocompletion in your editor and *more type safety* if you're using TypeScript.

To generate the code, run:

```
npx convex dev
```

This creates a `convex/_generated` directory that contains:

- `api.js` and `api.d.ts`
- `dataModel.d.ts`
- `server.js` and `server.d.ts`

## `dataModel.d.ts`

This code is generated. These exports are not directly available in the Convex package!

Instead, you must run `npx convex dev` to create `convex/_generated/dataModel.d.ts`.

### Generated Data Model Types

#### `TableNames`
**Type**: `string`

The names of all of your Convex tables.

#### `Doc<TableName>`
**Type**: `Object`

The type of a document stored in Convex.

**Type Parameters**:
- `TableName`: `extends TableNames` - A string literal type of the table name (like "users").

#### `Id`
An identifier for a document in Convex.

Convex documents are uniquely identified by their `Id`, which is accessible on the `_id` field. To learn more, see [Document IDs](https://docs.convex.dev/docs/data-model#document-ids).

Documents can be loaded using `db.get(id)` in query and mutation functions.

IDs are just strings at runtime, but this type can be used to distinguish them from other strings when type checking.

This is an alias of `GenericId` that is typed for your data model.

**Type Parameters**:
- `TableName`: `extends TableNames` - A string literal type of the table name (like "users").

#### `DataModel`
**Type**: `Object`

A type describing your Convex data model.

This type includes information about what tables you have, the type of documents stored in those tables, and the indexes defined on them.

This type is used to parameterize methods like `queryGeneric` and `mutationGeneric` to make them type-safe.

## `api.js`

This code is generated. These exports are not directly available in the Convex package!

Instead, you need to run `npx convex dev` to create `convex/_generated/api.js` and `convex/_generated/api.d.ts`.

These types require running code generation because they are specific to the Convex functions you define for your app.

If you aren't using code generation, you can use `makeFunctionReference` instead.

### `api`

An object of type `API` describing your app's public Convex API.

Its `API` type includes information about the arguments and return types of your app's Convex functions.

The `api` object is used by client-side React hooks and Convex functions that run or schedule other functions.

```javascript
import { api } from "../convex/_generated/api";
import { useQuery } from "convex/react";

const data = useQuery(api.messages.list);
```

### `internal`

Another object of type `API` describing your app's internal Convex API.

## `convex/upgrade.js`

```javascript
import { action } from "../_generated/server";
import { internal } from "../_generated/api";

export default action(async ({ runMutation }, { planId, ... }) => {
  // Call out to payment provider (e.g. Stripe) to charge customer
  const response = await fetch(...);
  if (response.ok) {
    // Mark the plan as "professional" in the Convex DB
    await runMutation(internal.plans.markPlanAsProfessional, { planId });
  }
});
```

## server.js

This code is generated. These exports are not directly available in the Convex package!

Instead, you must run `npx convex dev` to create `convex/_generated/server.js` and `convex/_generated/server.d.ts`.

Generated utilities for implementing server-side Convex query and mutation functions.

### Functions

#### `query`
```typescript
▸ query(func): RegisteredQuery
```

Define a query in this Convex app's public API.

This function will be allowed to read your Convex database and will be accessible from the client.

This is an alias of `queryGeneric` that is typed for your app's data model.

**Parameters**
- `func`: The query function. It receives a `QueryCtx` as its first argument.

**Returns**
- `RegisteredQuery`: The wrapped query. Include this as an export to name it and make it accessible.

#### `internalQuery`
```typescript
▸ internalQuery(func): RegisteredQuery
```

Define a query that is only accessible from other Convex functions (but not from the client).

This function will be allowed to read from your Convex database. It will not be accessible from the client.

This is an alias of `internalQueryGeneric` that is typed for your app's data model.

**Parameters**
- `func`: The query function. It receives a `QueryCtx` as its first argument.

**Returns**
- `RegisteredQuery`: The wrapped query. Include this as an export to name it and make it accessible.

#### `mutation`
```typescript
▸ mutation(func): RegisteredMutation
```

Define a mutation in this Convex app's public API.

This function will be allowed to modify your Convex database and will be accessible from the client.

This is an alias of `mutationGeneric` that is typed for your app's data model.

**Parameters**
- `func`: The mutation function. It receives a `MutationCtx` as its first argument.

**Returns**
- `RegisteredMutation`: The wrapped mutation. Include this as an export to name it and make it accessible.

#### `internalMutation`
```typescript
▸ internalMutation(func): RegisteredMutation
```

Define a mutation that is only accessible from other Convex functions (but not from the client).

This function will be allowed to read and write from your Convex database. It will not be accessible from the client.

This is an alias of `internalMutationGeneric` that is typed for your app's data model.

**Parameters**
- `func`: The mutation function. It receives a `MutationCtx` as its first argument.

**Returns**
- `RegisteredMutation`: The wrapped mutation. Include this as an export to name it and make it accessible.

#### `action`
```typescript
▸ action(func): RegisteredAction
```

Define an action in this Convex app's public API.

An action is a function which can execute any JavaScript code, including non-deterministic code and code with side-effects, like calling third-party services. They can be run in Convex's JavaScript environment or in Node.js using the "use node" directive. They can interact with the database indirectly by calling queries and mutations using the `ActionCtx`.

This is an alias of `actionGeneric` that is typed for your app's data model.

**Parameters**
- `func`: The action function. It receives an `ActionCtx` as its first argument.

**Returns**
- `RegisteredAction`: The wrapped function. Include this as an export to name it and make it accessible.

#### `internalAction`
```typescript
▸ internalAction(func): RegisteredAction
```

Define an action that is only accessible from other Convex functions (but not from the client).

This is an alias of `internalActionGeneric` that is typed for your app's data model.

**Parameters**
- `func`: The action function. It receives an `ActionCtx` as its first argument.

**Returns**
- `RegisteredAction`: The wrapped action. Include this as an export to name it and make it accessible.

#### `httpAction`
```typescript
▸ httpAction(func: (ctx: ActionCtx, request: Request) => Promise<Response>): PublicHttpAction
```

**Parameters**
- `func`: The function. It receives an `ActionCtx` as its first argument and a `Request` as its second argument.

**Returns**
- `PublicHttpAction`: The wrapped function. Import this function from `convex/http.js` and route it to hook it up.

### Types

#### `QueryCtx`
```typescript
Ƭ QueryCtx: Object
```

A set of services for use within Convex query functions.

The query context is passed as the first argument to any Convex query function run on the server.

This differs from the `MutationCtx` because all of the services are read-only.

This is an alias of `GenericQueryCtx` that is typed for your app's data model.

**Type declaration**
- `db`: `DatabaseReader`
- `auth`: `Auth`
- `storage`: `StorageReader`

#### `MutationCtx`
```typescript
Ƭ MutationCtx: Object
```

A set of services for use within Convex mutation functions.

The mutation context is passed as the first argument to any Convex mutation function run on the server.

This is an alias of `GenericMutationCtx` that is typed for your app's data model.

**Type declaration**
- `db`: `DatabaseWriter`
- `auth`: `Auth`
- `storage`: `StorageWriter`
- `scheduler`: `Scheduler`

#### `ActionCtx`
```typescript
Ƭ ActionCtx: Object
```

A set of services for use within Convex action functions.

The action context is passed as the first argument to any Convex action function run on the server.

This is an alias of `ActionCtx` that is typed for your app's data model.

**Type declaration**
- `runQuery`: `(name: string, args?: Record<string, Value>) => Promise<Value>`
- `runMutation`: `(name: string, args?: Record<string, Value>) => Promise<Value>`
- `runAction`: `(name: string, args?: Record<string, Value>) => Promise<Value>`
- `auth`: `Auth`
- `scheduler`: `Scheduler`
- `storage`: `StorageActionWriter`
- `vectorSearch`: `(tableName: string, indexName: string, query: VectorSearchQuery) => Promise<Array<{ _id: Id, _score: number }>>`

#### `DatabaseReader`
An interface to read from the database within Convex query functions.

This is an alias of `GenericDatabaseReader` that is typed for your app's data model.

#### `DatabaseWriter`
An interface to read from and write to the database within Convex mutation functions.

This is an alias of `GenericDatabaseWriter` that is typed for your app's data model.

# HTTP APIs

HTTP APIs include:

- Functions API
- Streaming export API
- Streaming import API
- Convex value format

Each of the HTTP APIs take a `format` query param that describes how documents are formatted. Currently the only supported value is `json`. See our [types page](https://docs.convex.dev/types) for details. Note that for simplicity, the `json` format does not support all Convex data types as input, and uses overlapping representation for several data types in output. We plan to add a new format with support for all Convex data types in the future.

## API authentication

The Functions API can be optionally authenticated as a user via a bearer token in a `Authorization` header. The value is `Bearer <access_key>` where the key is a token from your auth provider. See the [under the hood portion of the Clerk docs](https://docs.clerk.dev/under-the-hood/authentication) for details on how this works with Clerk.

Streaming export and streaming import requests require deployment admin authorization via the HTTP header `Authorization`. The value is `Convex <access_key>` where the access key comes from "Deploy key" on the Convex dashboard and gives full read and write access to your Convex data.

## Functions API

### `POST /api/query`, `/api/mutation`, `/api/action`

These HTTP endpoints allow you to call Convex functions and get the result as a value.

You can find your backend deployment URL on the dashboard Settings page, then the API URL will be `<CONVEX_URL>/api/query` etc., for example:

```shell
const url = "https://acoustic-panther-728.convex.cloud/api/query";
const request = { path: "messages:list", args: {}, format: "json" };

const response = fetch(url, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify(request),
});
```

**JSON Body parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `path` | `string` | `y` | Path to the Convex function formatted as a string as defined [here](https://docs.convex.dev/functions/overview#function-paths). |
| `args` | `object` | `y` | Named argument object to pass to the Convex function. |
| `format` | `string` | `y` | Output format for values. Valid values: `[json]` |

**Result JSON on success**

| Field Name | Type | Description |
| --- | --- | --- |
| `status` | `string` | `"success"` |
| `value` | `object` | Result of the Convex function in the requested format. |
| `logLines` | `list[string]` | Log lines printed out during the function execution. |

**Result JSON on error**

| Field Name | Type | Description |
| --- | --- | --- |
| `status` | `string` | `"error"` |
| `errorMessage` | `string` | The error message. |
| `errorData` | `object` | Error data within an application error if it was thrown. |
| `logLines` | `list[string]` | Log lines printed out during the function execution. |

### `POST /api/run/{functionIdentifier}`

This HTTP endpoint allows you to call arbitrary Convex function types with the path in the request URL and get the result as a value. The function identifier is formatted as a string as defined [here](https://docs.convex.dev/functions/overview#function-paths).

You can find your backend deployment URL on the dashboard Settings page, then the API URL will be `<CONVEX_URL>/api/run/{functionIdentifier}` etc., for example:

```shell
const url = "https://acoustic-panther-728.convex.cloud/api/run/messages:list";
const request = { args: {}, format: "json" };

const response = fetch(url, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify(request),
});
```

**JSON Body parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `args` | `object` | `y` | Named argument object to pass to the Convex function. |
| `format` | `string` | `n` | Output format for values. Defaults to `json`. Valid values: `[json]` |

**Result JSON on success**

| Field Name | Type | Description |
| --- | --- | --- |
| `status` | `string` | `"success"` |
| `value` | `object` | Result of the Convex function in the requested format. |
| `logLines` | `list[string]` | Log lines printed out during the function execution. |

**Result JSON on error**

| Field Name | Type | Description |
| --- | --- | --- |
| `status` | `string` | `"error"` |
| `errorMessage` | `string` | The error message. |
| `errorData` | `object` | Error data within an application error if it was thrown. |
| `logLines` | `list[string]` | Log lines printed out during the function execution. |

## Streaming export API

Convex supports streaming export. Convex provides connector implementations for Fivetran and Airbyte. Those connectors use the following APIs.

> **Note:** Sign up for a Professional plan for streaming export support. You can also read the [documentation on streaming export](https://docs.convex.dev/streaming-export).

> **Note:** Streaming Export HTTP APIs are currently a beta feature. If you have feedback or feature requests, let us know on [Discord](https://discord.gg/convex)!

### `GET /api/json_schemas`

The JSON Schemas endpoint lists tables, and for each table describes how documents will be encoded, given as JSON Schema. This endpoint returns `$description` tags throughout the schema to describe unintuitive encodings and give extra information like the table referenced by Id fields.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `deltaSchema` | `boolean` | `n` | If set, include metadata fields returned by `document_deltas` and `list_snapshot` (`_ts`, `_deleted`, and `_table`) |
| `format` | `string` | `n` | Output format for values. Valid values: `[json]` |

### `GET /api/list_snapshot`

The `list_snapshot` endpoint walks a consistent snapshot of documents. It may take one or more calls to `list_snapshot` to walk a full snapshot.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `snapshot` | `int` | `n` | Database timestamp at which to continue the snapshot. If omitted, select the latest timestamp. |
| `cursor` | `string` | `n` | An opaque cursor representing the progress in paginating through the snapshot. If omitted, start from the first page of the snapshot. |
| `tableName` | `string` | `n` | If provided, filters the snapshot to a table. If omitted, provide snapshot across all tables. |
| `format` | `string` | `n` | Output format for values. Valid values: `[json]` |

**Result JSON**

| Field Name | Type | Description |
| --- | --- | --- |
| `values` | `List[ConvexValue]` | List of convex values in the requested format. Each value includes extra fields `_ts` and `_table`. |
| `hasMore` | `boolean` | `True` if there are more pages to the snapshot. |
| `snapshot` | `int` | A value that represents the database timestamp at which the snapshot was taken. |
| `cursor` | `string` | An opaque cursor representing the end of the progress on the given page. Pass this to subsequent calls. |

**Expected API usage (pseudocode)**:

```python
def list_full_snapshot():
    snapshot_values = []
    snapshot = None
    cursor = None
    while True:
        result = api.list_snapshot(cursor, snapshot)
        snapshot_values.extend(result.values)
        (cursor, snapshot) = (result.cursor, result.snapshot)
        if not result.hasMore:
            break
    return (snapshot_values, result.snapshot)
```

### `GET /api/document_deltas`

The `document_deltas` endpoint walks the change log of documents to find new, updated, and deleted documents in the order of their mutations. This order is given by a `_ts` field on the returned documents. Deletions are represented as JSON objects with fields `_id`, `_ts`, and `_deleted: true`.

**Query parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `cursor` | `int` | `y` | Database timestamp after which to continue streaming document deltas. Initial value is the `snapshot` field returned from `list_snapshot`. |
| `tableName` | `string` | `n` | If provided, filters the document deltas to a table. If omitted, provide deltas across all tables. |
| `format` | `string` | `n` | Output format for values. Valid values: `[json]` |

**Result JSON**

| Field Name | Type | Description |
| --- | --- | --- |
| `values` | `List[ConvexValue]` | List of convex values in the requested format. Each value includes extra fields for `_ts`, and `_table`. Deletions include a field `_deleted`. |
| `hasMore` | `boolean` | `True` if there are more pages to the snapshot. |
| `cursor` | `int` | A value that represents the database timestamp at the end of the page. Pass to subsequent calls to `document_deltas`. |

**Expected API usage (pseudocode)**:

```python
def delta_sync(delta_cursor):
    delta_values = []
    while True:
        result = api.document_deltas(cursor)
        delta_values.extend(result.values)
        cursor = result.cursor
        if not result.hasMore:
            break
    return (delta_values, delta_cursor)

(snapshot_values, delta_cursor) = list_full_snapshot()
(delta_values, delta_cursor) = delta_sync(delta_cursor)
# Save delta_cursor for the next sync
```

## Streaming import API

Convex supports streaming import. Convex provides a connector implementation for Airbyte. Those connectors use the following APIs.

> **Note:** Streaming import support is automatically enabled for all Convex projects.

### Headers

Streaming import endpoints accept a `Convex-Client: streaming-import-<version>` header, where the version follows Semver guidelines. If this header is not specified, Convex will default to the latest version. We recommend using the header to ensure the consumer of this API does not break as the API changes.

### `GET /api/streaming_import/primary_key_indexes_ready`

The `primary_key_indexes_ready` endpoint takes a list of table names and returns `true` if the primary key indexes (created by `add_primary_key_indexes`) on all those tables are ready. If the tables are newly created, the indexes should be ready immediately; however if there are existing documents in the tables, it may take some time to backfill the primary key indexes. The response looks like:

```json
{
  "indexesReady": true
}
```

### `PUT /api/streaming_import/add_primary_key_indexes`

The `add_primary_key_indexes` endpoint takes a JSON body containing the primary keys for tables and creates indexes on the primary keys to be backfilled. Note that they are not immediately ready to query - the `primary_key_indexes_ready` endpoint needs to be polled until it returns `True` before calling `import_airbyte_records` with records that require primary key indexes. Also note that Convex queries will not have access to these added indexes. These are solely for use in `import_airbyte_records`. The body takes the form of a map of index names to list of field paths to index. Each field path is represented by a list of fields that can represent nested field paths.

```json
{
  "indexes": {
    "<table_name>": [["<field1>"], ["<field2>", "<nested_field>"]]
  }
}
```

### `PUT /api/streaming_import/clear_tables`

The `clear_tables` endpoint deletes all documents from the specified tables. Note that this may require multiple transactions. If there is an intermediate error only some documents may be deleted. The JSON body to use this API request contains a list of table names:

```json
{
  "tableNames": ["<table_1>", "<table_2>"]
}
```

### `POST /api/streaming_import/import_airbyte_records`

The `import_airbyte_records` endpoint enables streaming ingress into a Convex deployment and is designed to be called from an Airbyte destination connector.

It takes a map of streams and a list of messages in the JSON body. Each stream has a name and JSON schema that will correspond to a Convex table. Streams where records should be deduplicated include a primary key as well, which is represented as a list of lists of strings that are field paths. Records for streams without a primary key are appended to tables; records for streams with a primary key replace an existing record where the primary key value matches or are appended if there is no match. If you are using primary keys, you must call the `add_primary_key_indexes` endpoint first and wait for them to backfill by polling `primary_key_indexes_ready`.

Each message contains a stream name and a JSON document that will be inserted (or replaced, in the case of deduplicated sync) into the table with the corresponding stream name. Table names are same as the stream names. Airbyte records become Convex documents.

```json
{
   "tables": {
      "<stream_name>": {
         "primaryKey": [["<field1>"], ["<field2>", "<nested_field>"]],
         "jsonSchema": // see https://json-schema.org/ for examples
      }
   },
   "messages": [{
      "tableName": "<table_name>",
      "data": {} // JSON object conforming to the `json_schema` for that stream
   }]
}
```

Similar to `clear_tables`, it is possible to execute a partial import using `import_airbyte_records` if there is a failure after a transaction has committed.

**Expected API Usage**:

1. [Optional] Add any indexes if using primary keys and deduplicated sync (see `add_primary_key_indexes` above).
2. [Optional] Delete all documents in specified tables using `clear_tables` if using overwrite sync.
3. Make a request to `import_airbyte_records` with new records to sync and stream information.

# Errors and Warnings

This page explains specific errors thrown by Convex.

See [Error Handling](link_to_error_handling) to learn about handling errors in general.

## Optimistic Concurrency Control (OCC) Conflict

This system error is thrown when a mutation repeatedly fails due to conflicting changes from parallel mutation executions.

### Example A

A mutation `updateCounter` always updates the same document:

```javascript
export const updateCounter = mutation({
  args: {},
  handler: async (ctx) => {
    const doc = await ctx.db.get(process.env.COUNTER_ID);
    await ctx.db.patch(doc._id, { value: doc.value + 1 });
  },
});
```

If this mutation is called many times per second, many of its executions will conflict with each other. Convex internally does several retries to mitigate this concern, but if the mutation is called more rapidly than Convex can execute it, some of the invocations will eventually throw this error:

> failure updateCounter
> Documents read from or written to the table "counters" changed while this mutation was being run and on every subsequent retry. Another call to this mutation changed the document with ID "123456789101112".

The error message will note the table name, which mutation caused the conflict (in this example, it's another call to the same mutation), and one document ID which was part of the conflicting change.

### Example B

Mutation `writeCount` depends on the entire `tasks` table:

```javascript
export const writeCount = mutation({
  args: {
    target: v.id("counts"),
  },
  handler: async (ctx, args) => {
    const tasks = await ctx.db.query("tasks").collect();
    await ctx.db.patch(args.target, { value: tasks });
  },
});

export const addTask = mutation({
  args: {
    text: v.string(),
  },
  handler: async (ctx, args) => {
    await ctx.db.insert("tasks", { text: args.text });
  },
});
```

If the mutation `writeCount` is called at the same time as many calls to `addTask` are made, either of the mutations can fail with this error. This is because any change to the "tasks" table will conflict with the `writeCount` mutation:

> failure writeCount
> Documents read from or written to the table "tasks" changed while this mutation was being run and on every subsequent retry. A call to "addTask" changed the document with ID "123456789101112".

## Remediation

To fix this issue:

1. Make sure that your mutations only read the data they need. Consider reducing the amount of data read by using indexed queries with selective index range expressions.
2. Make sure you are not calling a mutation an unexpected number of times, perhaps from an action inside a loop.
3. Design your data model such that it doesn't require making many writes to the same document.




