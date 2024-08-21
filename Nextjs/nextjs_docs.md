# Introduction

Welcome to the Next.js documentation!

## What is Next.js?

Next.js is a React framework for building full-stack web applications. You use React Components to build user interfaces, and Next.js for additional features and optimizations.

Under the hood, Next.js also abstracts and automatically configures tooling needed for React, like bundling, compiling, and more. This allows you to focus on building your application instead of spending time with configuration.

Whether you're an individual developer or part of a larger team, Next.js can help you build interactive, dynamic, and fast React applications.

## Main Features

Some of the main Next.js features include:

| Feature | Description |
|---------|-------------|
| Routing | A file-system based router built on top of Server Components that supports layouts, nested routing, loading states, error handling, and more. |
| Rendering | Client-side and Server-side Rendering with Client and Server Components. Further optimized with Static and Dynamic Rendering on the server with Next.js. Streaming on Edge and Node.js runtimes. |
| Data Fetching | Simplified data fetching with async/await in Server Components, and an extended fetch API for request memoization, data caching and revalidation. |
| Styling | Support for your preferred styling methods, including CSS Modules, Tailwind CSS, and CSS-in-JS |
| Optimizations | Image, Fonts, and Script Optimizations to improve your application's Core Web Vitals and User Experience. |
| TypeScript | Improved support for TypeScript, with better type checking and more efficient compilation, as well as custom TypeScript Plugin and type checker. |

## Installation

### System Requirements:

- Node.js 18.18 or later.
- macOS, Windows (including WSL), and Linux are supported.

### Automatic Installation

We recommend starting a new Next.js app using create-next-app, which sets up everything automatically for you. To create a project, run:

```bash
npx create-next-app@latest
```

On installation, you'll see the following prompts:

```
What is your project named? my-app
Would you like to use TypeScript? No / Yes
Would you like to use ESLint? No / Yes
Would you like to use Tailwind CSS? No / Yes
Would you like your code inside a `src/` directory? No / Yes
Would you like to use App Router? (recommended) No / Yes
Would you like to use Turbopack for `next dev`?  No / Yes
Would you like to customize the import alias (`@/*` by default)? No / Yes
What import alias would you like configured? @/*
```

After the prompts, create-next-app will create a folder with your project name and install the required dependencies.

> **Good to know:**
> - Next.js now ships with TypeScript, ESLint, and Tailwind CSS configuration by default.
> - You can optionally use a src directory in the root of your project to separate your application's code from configuration files.

### Manual Installation

To manually create a new Next.js app, install the required packages:

```bash
npm install next@latest react@latest react-dom@latest
```

Open your package.json file and add the following scripts:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

These scripts refer to the different stages of developing an application:

- `dev`: runs `next dev` to start Next.js in development mode.
- `build`: runs `next build` to build the application for production usage.
- `start`: runs `next start` to start a Next.js production server.
- `lint`: runs `next lint` to set up Next.js' built-in ESLint configuration.

### Creating directories

Next.js uses file-system routing, which means the routes in your application are determined by how you structure your files.

### The app directory

For new applications, we recommend using the App Router. This router allows you to use React's latest features and is an evolution of the Pages Router based on community feedback.

Create an `app/` folder, then add a `layout.tsx` and `page.tsx` file. These will be rendered when the user visits the root of your application (`/`).

App Folder Structure

Create a root layout inside `app/layout.tsx` with the required `<html>` and `<body>` tags:

```typescript
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

Finally, create a home page `app/page.tsx` with some initial content:

```typescript
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

> **Good to know:** If you forget to create `layout.tsx`, Next.js will automatically create this file when running the development server with `next dev`.

#### The public folder (optional)

Create a `public` folder to store static assets such as images, fonts, etc. Files inside public directory can then be referenced by your code starting from the base URL (`/`).

### Run the Development Server

1. Run `npm run dev` to start the development server.
2. Visit `http://localhost:3000` to view your application.
3. Edit `app/page.tsx` (or `pages/index.tsx`) file and save it to see the updated result in your browser.

# Next.js Project Structure

## Directory Structure

- `app`: App Router
- `public`: Static assets to be served
- `src`: Optional application source folder

## Top-level Files

Top-level files are used to configure your application, manage dependencies, run middleware, integrate monitoring tools, and define environment variables.

### Next.js

| File | Description |
|------|-------------|
| `next.config.js` | Configuration file for Next.js |
| `package.json` | Project dependencies and scripts |
| `instrumentation.ts` | OpenTelemetry and Instrumentation file |
| `middleware.ts` | Next.js request middleware |
| `.env` | Environment variables |
| `.env.local` | Local environment variables |
| `.env.production` | Production environment variables |
| `.env.development` | Development environment variables |
| `.eslintrc.json` | Configuration file for ESLint |
| `.gitignore` | Git files and folders to ignore |
| `next-env.d.ts` | TypeScript declaration file for Next.js |
| `tsconfig.json` | Configuration file for TypeScript |
| `jsconfig.json` | Configuration file for JavaScript |

## app Routing Conventions

The following file conventions are used to define routes and handle metadata in the app router.

### Routing Files

| File | Extensions | Description |
|------|------------|-------------|
| `layout` | `.js` `.jsx` `.tsx` | Layout |
| `page` | `.js` `.jsx` `.tsx` | Page |
| `loading` | `.js` `.jsx` `.tsx` | Loading UI |
| `not-found` | `.js` `.jsx` `.tsx` | Not found UI |
| `error` | `.js` `.jsx` `.tsx` | Error UI |
| `global-error` | `.js` `.jsx` `.tsx` | Global error UI |
| `route` | `.js` `.ts` | API endpoint |
| `template` | `.js` `.jsx` `.tsx` | Re-rendered layout |
| `default` | `.js` `.jsx` `.tsx` | Parallel route fallback page |

### Nested Routes

| Folder Structure | Description |
|-------------------|-------------|
| `folder` | Route segment |
| `folder/folder` | Nested route segment |

### Dynamic Routes

| Folder Structure | Description |
|-------------------|-------------|
| `[folder]` | Dynamic route segment |
| `[...folder]` | Catch-all route segment |
| `[[...folder]]` | Optional catch-all route segment |

### Route Groups and Private Folders

| Folder Structure | Description |
|-------------------|-------------|
| `(folder)` | Group routes without affecting routing |
| `_folder` | Opt folder and all child segments out of routing |

### Parallel and Intercepted Routes

| Folder Structure | Description |
|-------------------|-------------|
| `@folder` | Named slot |
| `(.)folder` | Intercept same level |
| `(..)folder` | Intercept one level above |
| `(..)(..)folder` | Intercept two levels above |
| `(...)folder` | Intercept from root |

### Metadata File Conventions

#### App Icons

| File | Extensions | Description |
|------|------------|-------------|
| `favicon` | `.ico` | Favicon file |
| `icon` | `.ico` `.jpg` `.jpeg` `.png` `.svg` | App Icon file |
| `icon` | `.js` `.ts` `.tsx` | Generated App Icon |
| `apple-icon` | `.jpg` `.jpeg` `.png` | Apple App Icon file |
| `apple-icon` | `.js` `.ts` `.tsx` | Generated Apple App Icon |

#### Open Graph and Twitter Images

| File | Extensions | Description |
|------|------------|-------------|
| `opengraph-image` | `.jpg` `.jpeg` `.png` `.gif` | Open Graph image file |
| `opengraph-image` | `.js` `.ts` `.tsx` | Generated Open Graph image |
| `twitter-image` | `.jpg` `.jpeg` `.png` `.gif` | Twitter image file |
| `twitter-image` | `.js` `.ts` `.tsx` | Generated Twitter image |

#### SEO

| File | Extensions | Description |
|------|------------|-------------|
| `sitemap` | `.xml` | Sitemap file |
| `sitemap` | `.js` `.ts` | Generated Sitemap |
| `robots` | `.txt` | Robots file |
| `robots` | `.js` `.ts` | Generated Robots file |

# App Router

The Next.js App Router introduces a new model for building applications using React's latest features such as Server Components, Streaming with Suspense, and Server Actions.

## Frequently Asked Questions

### How can I access the request object in a layout?

You intentionally cannot access the raw request object. However, you can access headers and cookies through server-only functions. You can also set cookies.

Layouts do not rerender. They can be cached and reused to avoid unnecessary computation when navigating between pages. By restricting layouts from accessing the raw request, Next.js can prevent the execution of potentially slow or expensive user code within the layout, which could negatively impact performance.

This design also enforces consistent and predictable behavior for layouts across different pages, which simplifies development and debugging.

Depending on the UI pattern you're building, Parallel Routes allow you to render multiple pages in the same layout, and pages have access to the route segments as well as the URL search params.

### How can I access the URL on a page?

By default, pages are Server Components. You can access the route segments through the `params` prop and the URL search params through the `searchParams` prop for a given page.

If you are using Client Components, you can use `usePathname`, `useSelectedLayoutSegment`, and `useSelectedLayoutSegments` for more complex routes.

Further, depending on the UI pattern you're building, Parallel Routes allow you to render multiple pages in the same layout, and pages have access to the route segments as well as the URL search params.

### How can I redirect from a Server Component?

You can use `redirect` to redirect from a page to a relative or absolute URL. `redirect` is a temporary (307) redirect, while `permanentRedirect` is a permanent (308) redirect. When these functions are used while streaming UI, they will insert a meta tag to emit the redirect on the client side.

### How can I set cookies?

You can set cookies in Server Actions or Route Handlers using the `cookies` function.

Since HTTP does not allow setting cookies after streaming starts, you cannot set cookies from a page or layout directly. You can also set cookies from Middle

# Routing 

The skeleton of every application is routing. This page will introduce you to the fundamental concepts of routing for the web and how to handle routing in Next.js.

## Terminology

First, you will see these terms being used throughout the documentation. Here's a quick reference:

## Terminology for Component Tree

- **Tree**: A convention for visualizing a hierarchical structure. For example, a component tree with parent and children components, a folder structure, etc.
- **Subtree**: Part of a tree, starting at a new root (first) and ending at the leaves (last).
- **Root**: The first node in a tree or subtree, such as a root layout.
- **Leaf**: Nodes in a subtree that have no children, such as the last segment in a URL path.

## Terminology for URL Anatomy

- **URL Segment**: Part of the URL path delimited by slashes.
- **URL Path**: Part of the URL that comes after the domain (composed of segments).

## The app Router

In version 13, Next.js introduced a new App Router built on React Server Components, which supports shared layouts, nested routing, loading states, error handling, and more.

The App Router works in a new directory named `app`. The `app` directory works alongside the `pages` directory to allow for incremental adoption. This allows you to opt some routes of your application into the new behavior while keeping other routes in the `pages` directory for previous behavior. If your application uses the `pages` directory, please also see the Pages Router documentation.

> **Good to know**: The App Router takes priority over the Pages Router. Routes across directories should not resolve to the same URL path and will cause a build-time error to prevent a conflict.

```
Next.js App Directory
```

By default, components inside `app` are React Server Components. This is a performance optimization and allows you to easily adopt them, and you can also use Client Components.

> **Recommendation**: Check out the Server page if you're new to Server Components.

## Roles of Folders and Files

Next.js uses a file-system based router where:

- Folders are used to define routes. A route is a single path of nested folders, following the file-system hierarchy from the root folder down to a final leaf folder that includes a `page.js` file. See Defining Routes.
- Files are used to create UI that is shown for a route segment. See special files.

## Route Segments

Each folder in a route represents a route segment. Each route segment is mapped to a corresponding segment in a URL path.

```
How Route Segments Map to URL Segments
```

## Nested Routes

To create a nested route, you can nest folders inside each other. For example, you can add a new `/dashboard/settings` route by nesting two new folders in the `app` directory.

The `/dashboard/settings` route is composed of three segments:

1. `/` (Root segment)
2. `dashboard` (Segment)
3. `settings` (Leaf segment)

## File Conventions

Next.js provides a set of special files to create UI with specific behavior in nested routes:

| File           | Purpose                                                   |
|----------------|-----------------------------------------------------------|
| `layout`       | Shared UI for a segment and its children                  |
| `page`         | Unique UI of a route and make routes publicly accessible  |
| `loading`      | Loading UI for a segment and its children                 |
| `not-found`    | Not found UI for a segment and its children               |
| `error`        | Error UI for a segment and its children                   |
| `global-error` | Global Error UI                                           |
| `route`        | Server-side API endpoint                                  |
| `template`     | Specialized re-rendered Layout UI                         |
| `default`      | Fallback UI for Parallel Routes                           |

> **Good to know**: `.js`, `.jsx`, or `.tsx` file extensions can be used for special files.

## Component Hierarchy

The React components defined in special files of a route segment are rendered in a specific hierarchy:

1. `layout.js`
2. `template.js`
3. `error.js` (React error boundary)
4. `loading.js` (React suspense boundary)
5. `not-found.js` (React error boundary)
6. `page.js` or nested `layout.js`

```
Component Hierarchy for File Conventions
```

In a nested route, the components of a segment will be nested inside the components of its parent segment.

```
Nested File Conventions Component Hierarchy
```

## Colocation

In addition to special files, you have the option to colocate your own files (e.g. components, styles, tests, etc) inside folders in the `app` directory.

This is because while folders define routes, only the contents returned by `page.js` or `route.js` are publicly addressable.

```
An example folder structure with colocated files
```

Learn more about [Project Organization and Colocation](https://nextjs.org/docs/app/building-your-application/routing/colocation).

## Advanced Routing Patterns

The App Router also provides a set of conventions to help you implement more advanced routing patterns. These include:

- **Parallel Routes**: Allow you to simultaneously show two or more pages in the same view that can be navigated independently. You can use them for split views that have their own sub-navigation. E.g. Dashboards.
- **Intercepting Routes**: Allow you to intercept a route and show it in the context of another route. You can use these when keeping the context for the current page is important. E.g. Seeing all tasks while editing one task or expanding a photo in a feed.

These patterns allow you to build richer and more complex UIs, democratizing features that were historically complex for small teams and individual developers to implement.

## Defining Routes

This page will guide you through how to define and organize routes in your Next.js application.

### Creating Routes

Next.js uses a file-system based router where folders are used to define routes.

Each folder represents a route segment that maps to a URL segment. To create a nested route, you can nest folders inside each other.

**Route segments to path segments**

A special `page.js` file is used to make route segments publicly accessible.

### Defining Routes

In this example, the `/dashboard/analytics` URL path is not publicly accessible because it does not have a corresponding `page.js` file. This folder could be used to store components, stylesheets, images, or other colocated files.

> **Good to know:** `.js`, `.jsx`, or `.tsx` file extensions can be used for special files.

### Creating UI

Special file conventions are used to create UI for each route segment. The most common are pages to show UI unique to a route, and layouts to show UI that is shared across multiple routes.

For example, to create your first page, add a `page.js` file inside the app directory and export a React component:

```typescript
// app/page.tsx
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

## Pages

A page is UI that is unique to a route. You can define a page by default exporting a component from a `page.js` file.

For example, to create your index page, add the `page.js` file inside the app directory:

```typescript
// app/page.tsx
// `app/page.tsx` is the UI for the `/` URL
export default function Page() {
  return <h1>Hello, Home page!</h1>
}
```

Then, to create further pages, create a new folder and add the `page.js` file inside it. For example, to create a page for the `/dashboard` route, create a new folder called dashboard, and add the `page.js` file inside it:

```typescript
// app/dashboard/page.tsx
// `app/dashboard/page.tsx` is the UI for the `/dashboard` URL
export default function Page() {
  return <h1>Hello, Dashboard Page!</h1>
}
```

> **Good to know:**
> 
> - The `.js`, `.jsx`, or `.tsx` file extensions can be used for Pages.
> - A page is always the leaf of the route subtree.
> - A `page.js` file is required to make a route segment publicly accessible.
> - Pages are Server Components by default, but can be set to a Client Component.
> - Pages can fetch data. View the Data Fetching section for more information.

## Linking and Navigating

There are four ways to navigate between routes in Next.js:

1. Using the `<Link>` Component
2. Using the `useRouter` hook (Client Components)
3. Using the `redirect` function (Server Components)
4. Using the native History API

This page will go through how to use each of these options, and dive deeper into how navigation works.

### `<Link>` Component

`<Link>` is a built-in component that extends the HTML `<a>` tag to provide prefetching and client-side navigation between routes. It is the primary and recommended way to navigate between routes in Next.js.

You can use it by importing it from `next/link`, and passing a `href` prop to the component:

```typescript
// app/page.tsx
import Link from 'next/link'
 
export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>
}
```

There are other optional props you can pass to `<Link>`. See the API reference for more.

### Examples

**Linking to Dynamic Segments**

When linking to dynamic segments, you can use template literals and interpolation to generate a list of links. For example, to generate a list of blog posts:

```javascript
// app/blog/PostList.js
import Link from 'next/link'
 
export default function PostList({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

**Checking Active Links**

You can use `usePathname()` to determine if a link is active. For example, to add a class to the active link, you can check if the current pathname matches the href of the link:

```typescript
// @/app/ui/nav-links.tsx
'use client'
 
import { usePathname } from 'next/navigation'
import Link from 'next/link'
 
export function Links() {
  const pathname = usePathname()
 
  return (
    <nav>
      <Link className={`link ${pathname === '/' ? 'active' : ''}`} href="/">
        Home
      </Link>
 
      <Link
        className={`link ${pathname === '/about' ? 'active' : ''}`}
        href="/about"
      >
        About
      </Link>
    </nav>
  )
}
```

**Scrolling to an id**

The default behavior of the Next.js App Router is to scroll to the top of a new route or to maintain the scroll position for backwards and forwards navigation.

If you'd like to scroll to a specific id on navigation, you can append your URL with a `#` hash link or just pass a hash link to the `href` prop. This is possible since `<Link>` renders to an `<a>` element.

```jsx
<Link href="/dashboard#settings">Settings</Link>
 
// Output
<a href="/dashboard#settings">Settings</a>
```

> **Good to know:** Next.js will scroll to the Page if it is not visible in the viewport upon navigation.

**Disabling scroll restoration**

The default behavior of the Next.js App Router is to scroll to the top of a new route or to maintain the scroll position for backwards and forwards navigation. If you'd like to disable this behavior, you can pass `scroll={false}` to the `<Link>` component, or `scroll: false` to `router.push()` or `router.replace()`.

```jsx
// next/link
<Link href="/dashboard" scroll={false}>
  Dashboard
</Link>

// useRouter
import { useRouter } from 'next/navigation'
 
const router = useRouter()
 
router.push('/dashboard', { scroll: false })
```

### `useRouter()` hook

The `useRouter` hook allows you to programmatically change routes from Client Components.

```jsx
// app/page.js
'use client'
 
import { useRouter } from 'next/navigation'
 
export default function Page() {
  const router = useRouter()
 
  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  )
}
```

For a full list of `useRouter` methods, see the API reference.

> **Recommendation:** Use the `<Link>` component to navigate between routes unless you have a specific requirement for using `useRouter`.

### `redirect` function

For Server Components, use the `redirect` function instead.

```typescript
// app/team/[id]/page.tsx
import { redirect } from 'next/navigation'
 
async function fetchTeam(id: string) {
  const res = await fetch('https://...')
  if (!res.ok) return undefined
  return res.json()
}
 
export default async function Profile({ params }: { params: { id: string } }) {
  const team = await fetchTeam(params.id)
  if (!team) {
    redirect('/login')
  }
 
  // ...
}
```

> **Good to know:**
> 
> - `redirect` returns a 307 (Temporary Redirect) status code by default. When used in a Server Action, it returns a 303 (See Other), which is commonly used for redirecting to a success page as a result of a POST request.
> - `redirect` internally throws an error so it should be called outside of try/catch blocks.
> - `redirect` can be called in Client Components during the rendering process but not in event handlers. You can use the `useRouter` hook instead.
> - `redirect` also accepts absolute URLs and can be used to redirect to external links.
> - If you'd like to redirect before the render process, use `next.config.js` or Middleware.
> 
> See the `redirect` API reference for more information.

### Using the native History API

Next.js allows you to use the native `window.history.pushState` and `window.history.replaceState` methods to update the browser's history stack without reloading the page.

`pushState` and `replaceState` calls integrate into the Next.js Router, allowing you to sync with `usePathname` and `useSearchParams`.

**window.history.pushState**

Use it to add a new entry to the browser's history stack. The user can navigate back to the previous state. For example, to sort a list of products:

```jsx
'use client'
 
import { useSearchParams } from 'next/navigation'
 
export default function SortProducts() {
  const searchParams = useSearchParams()
 
  function updateSorting(sortOrder: string) {
    const params = new URLSearchParams(searchParams.toString())
    params.set('sort', sortOrder)
    window.history.pushState(null, '', `?${params.toString()}`)
  }
 
  return (
    <>
      <button onClick={() => updateSorting('asc')}>Sort Ascending</button>
      <button onClick={() => updateSorting('desc')}>Sort Descending</button>
    </>
  )
}
```

**window.history.replaceState**

Use it to replace the current entry on the browser's history stack. The user is not able to navigate back to the previous state. For example, to switch the application's locale:

```jsx
'use client'
 
import { usePathname } from 'next/navigation'
 
export function LocaleSwitcher() {
  const pathname = usePathname()
 
  function switchLocale(locale: string) {
    // e.g. '/en/about' or '/fr/contact'
    const newPath = `/${locale}${pathname}`
    window.history.replaceState(null, '', newPath)
  }
 
  return (
    <>
      <button onClick={() => switchLocale('en')}>English</button>
      <button onClick={() => switchLocale('fr')}>French</button>
    </>
  )
}
```

## How Routing and Navigation Works

The App Router uses a hybrid approach for routing and navigation. On the server, your application code is automatically code-split by route segments. And on the client, Next.js prefetches and caches the route segments. This means, when a user navigates to a new route, the browser doesn't reload the page, and only the route segments that change re-render - improving the navigation experience and performance.

1. **Code Splitting**

   Code splitting allows you to split your application code into smaller bundles to be downloaded and executed by the browser. This reduces the amount of data transferred and execution time for each request, leading to improved performance.

   Server Components allow your application code to be automatically code-split by route segments. This means only the code needed for the current route is loaded on navigation.

2. **Prefetching**

   Prefetching is a way to preload a route in the background before the user visits it.

   There are two ways routes are prefetched in Next.js:

   - `<Link>` component: Routes are automatically prefetched as they become visible in the user's viewport. Prefetching happens when the page first loads or when it comes into view through scrolling.
   - `router.prefetch()`: The `useRouter` hook can be used to prefetch routes programmatically.

   The `<Link>`'s default prefetching behavior (i.e. when the `prefetch` prop is left unspecified or set to `null`) is different depending on your usage of `loading.js`. Only the shared layout, down the rendered "tree" of components until the first `loading.js` file, is prefetched and cached for 30s. This reduces the cost of fetching an entire dynamic route, and it means you can show an instant loading state for better visual feedback to users.

   You can disable prefetching by setting the `prefetch` prop to `false`. Alternatively, you can prefetch the full page data beyond the loading boundaries by setting the `prefetch` prop to `true`.

   See the `<Link>` API reference for more information.

   > **Good to know:** Prefetching is not enabled in development, only in production.

3. **Caching**

   Next.js has an in-memory client-side cache called the Router Cache. As users navigate around the app, the React Server Component Payload of prefetched route segments and visited routes are stored in the cache.

   This means on navigation, the cache is reused as much as possible, instead of making a new request to the server - improving performance by reducing the number of requests and data transferred.

   Learn more about how the Router Cache works and how to configure it.

4. **Partial Rendering**

   Partial rendering means only the route segments that change on navigation re-render on the client, and any shared segments are preserved.

   For example, when navigating between two sibling routes, `/dashboard/settings` and `/dashboard/analytics`, the `settings` and `analytics` pages will be rendered, and the shared `dashboard` layout will be preserved.

   **How partial rendering works**

   Without partial rendering, each navigation would cause the full page to re-render on the client. Rendering only the segment that changes reduces the amount of data transferred and execution time, leading to improved performance.

5. **Soft Navigation**

   Browsers perform a "hard navigation" when navigating between pages. The Next.js App Router enables "soft navigation" between pages, ensuring only the route segments that have changed are re-rendered (partial rendering). This enables client React state to be preserved during navigation.

6. **Back and Forward Navigation**

   By default, Next.js will maintain the scroll position for backwards and forwards navigation, and re-use route segments in the Router Cache.

7. **Routing between pages/ and app/**

   When incrementally migrating from `pages/` to `app/`, the Next.js router will automatically handle hard navigation between the two. To detect transitions from `pages/` to `app/`, there is a client router filter that leverages probabilistic checking of app routes, which can occasionally result in false positives. By default, such occurrences should be very rare, as we configure the false positive likelihood to be 0.01%. This likelihood can be customized via the `experimental.clientRouterFilterAllowedRate` option in `next.config.js`. It's important to note that lowering the false positive rate will increase the size of the generated filter in the client bundle.

   Alternatively, if you prefer to disable this handling completely and manage the routing between `pages/` and `app/` manually, you can set `experimental.clientRouterFilter` to `false` in `next.config.js`. When this feature is disabled, any dynamic routes in pages that overlap with app routes won't be navigated to properly by default.

## Error Handling

Errors can be divided into two categories: expected errors and uncaught exceptions:

- Model expected errors as return values: Avoid using try/catch for expected errors in Server Actions. Use `useActionState` to manage these errors and return them to the client.
- Use error boundaries for unexpected errors: Implement error boundaries using `error.tsx` and `global-error.tsx` files to handle unexpected errors and provide a fallback UI.

> Good to know: These examples use React's `useActionState` hook, which is available in React 19 RC. If you are using an earlier version of React, use `useFormState` instead. See the [React docs](https://react.dev) for more information.

### Handling Expected Errors

Expected errors are those that can occur during the normal operation of the application, such as those from server-side form validation or failed requests. These errors should be handled explicitly and returned to the client.

#### Handling Expected Errors from Server Actions

Use the `useActionState` hook to manage the state of Server Actions, including handling errors. This approach avoids try/catch blocks for expected errors, which should be modeled as return values rather than thrown exceptions.

app/actions.ts:

```typescript
'use server'

import { redirect } from 'next/navigation'

export async function createUser(prevState: any, formData: FormData) {
  const res = await fetch('https://...')
  const json = await res.json()

  if (!res.ok) {
    return { message: 'Please enter a valid email' }
  }

  redirect('/dashboard')
}
```

Then, you can pass your action to the `useActionState` hook and use the returned state to display an error message.

app/ui/signup.tsx:

```typescript
'use client'

import { useActionState } from 'react'
import { createUser } from '@/app/actions'

const initialState = {
  message: '',
}

export function Signup() {
  const [state, formAction] = useActionState(createUser, initialState)

  return (
    <form action={formAction}>
      <label htmlFor="email">Email</label>
      <input type="text" id="email" name="email" required />
      {/* ... */}
      <p aria-live="polite">{state?.message}</p>
      <button>Sign up</button>
    </form>
  )
}
```

You could also use the returned state to display a toast message from the client component.

#### Handling Expected Errors from Server Components

When fetching data inside of a Server Component, you can use the response to conditionally render an error message or redirect.

app/page.tsx:

```typescript
export default async function Page() {
  const res = await fetch(`https://...`)
  const data = await res.json()

  if (!res.ok) {
    return 'There was an error.'
  }

  return '...'
}
```

### Uncaught Exceptions

Uncaught exceptions are unexpected errors that indicate bugs or issues that should not occur during the normal flow of your application. These should be handled by throwing errors, which will then be caught by error boundaries.

- Common: Handle uncaught errors below the root layout with `error.js`.
- Optional: Handle granular uncaught errors with nested `error.js` files (e.g. `app/dashboard/error.js`)
- Uncommon: Handle uncaught errors in the root layout with `global-error.js`.

#### Using Error Boundaries

Next.js uses error boundaries to handle uncaught exceptions. Error boundaries catch errors in their child components and display a fallback UI instead of the component tree that crashed.

Create an error boundary by adding an `error.tsx` file inside a route segment and exporting a React component:

app/dashboard/error.tsx:

```typescript
'use client' // Error boundaries must be Client Components

import { useEffect } from 'react'

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error)
  }, [error])

  return (
    <div>
      <h2>Something went wrong!</h2>
      <button
        onClick={
          // Attempt to recover by trying to re-render the segment
          () => reset()
        }
      >
        Try again
      </button>
    </div>
  )
}
```

If you want errors to bubble up to the parent error boundary, you can throw when rendering the error component.

#### Handling Errors in Nested Routes

Errors will bubble up to the nearest parent error boundary. This allows for granular error handling by placing `error.tsx` files at different levels in the route hierarchy.

Nested Error Component Hierarchy

#### Handling Global Errors

While less common, you can handle errors in the root layout using `app/global-error.js`, located in the root app directory, even when leveraging internationalization. Global error UI must define its own `<html>` and `<body>` tags, since it is replacing the root layout or template when active.

app/global-error.tsx:

```typescript
'use client' // Error boundaries must be Client Components

export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    // global-error must include html and body tags
    <html>
      <body>
        <h2>Something went wrong!</h2>
        <button onClick={() => reset()}>Try again</button>
      </body>
    </html>
  )
}
```

## Loading UI and Streaming

The special file `loading.js` helps you create meaningful Loading UI with React Suspense. With this convention, you can show an instant loading state from the server while the content of a route segment loads. The new content is automatically swapped in once rendering is complete.

### Loading UI

#### Instant Loading States

An instant loading state is fallback UI that is shown immediately upon navigation. You can pre-render loading indicators such as skeletons and spinners, or a small but meaningful part of future screens such as a cover photo, title, etc. This helps users understand the app is responding and provides a better user experience.

Create a loading state by adding a `loading.js` file inside a folder.

##### loading.js special file

```typescript
// app/dashboard/loading.tsx
export default function Loading() {
  // You can add any UI inside Loading, including a Skeleton.
  return <LoadingSkeleton />
}
```

In the same folder, `loading.js` will be nested inside `layout.js`. It will automatically wrap the `page.js` file and any children below in a `<Suspense>` boundary.

**Good to know:**

- Navigation is immediate, even with server-centric routing.
- Navigation is interruptible, meaning changing routes does not need to wait for the content of the route to fully load before navigating to another route.
- Shared layouts remain interactive while new route segments load.

**Recommendation:** Use the `loading.js` convention for route segments (layouts and pages) as Next.js optimizes this functionality.

### Streaming with Suspense

In addition to `loading.js`, you can also manually create Suspense Boundaries for your own UI components. The App Router supports streaming with Suspense for both Node.js and Edge runtimes.

**Good to know:**

Some browsers buffer a streaming response. You may not see the streamed response until the response exceeds 1024 bytes. This typically only affects "hello world" applications, but not real applications.

#### What is Streaming?

To learn how Streaming works in React and Next.js, it's helpful to understand Server-Side Rendering (SSR) and its limitations.

With SSR, there's a series of steps that need to be completed before a user can see and interact with a page:

1. First, all data for a given page is fetched on the server.
2. The server then renders the HTML for the page.
3. The HTML, CSS, and JavaScript for the page are sent to the client.
4. A non-interactive user interface is shown using the generated HTML, and CSS.
5. Finally, React hydrates the user interface to make it interactive.

> Chart showing Server Rendering without Streaming

These steps are sequential and blocking, meaning the server can only render the HTML for a page once all the data has been fetched. And, on the client, React can only hydrate the UI once the code for all components in the page has been downloaded.

SSR with React and Next.js helps improve the perceived loading performance by showing a non-interactive page to the user as soon as possible.

> Server Rendering without Streaming

However, it can still be slow as all data fetching on server needs to be completed before the page can be shown to the user.

Streaming allows you to break down the page's HTML into smaller chunks and progressively send those chunks from the server to the client.

> How Server Rendering with Streaming Works

This enables parts of the page to be displayed sooner, without waiting for all the data to load before any UI can be rendered.

Streaming works well with React's component model because each component can be considered a chunk. Components that have higher priority (e.g. product information) or that don't rely on data can be sent first (e.g. layout), and React can start hydration earlier. Components that have lower priority (e.g. reviews, related products) can be sent in the same server request after their data has been fetched.

> Chart showing Server Rendering with Streaming

Streaming is particularly beneficial when you want to prevent long data requests from blocking the page from rendering as it can reduce the Time To First Byte (TTFB) and First Contentful Paint (FCP). It also helps improve Time to Interactive (TTI), especially on slower devices.

#### Example

`<Suspense>` works by wrapping a component that performs an asynchronous action (e.g. fetch data), showing fallback UI (e.g. skeleton, spinner) while it's happening, and then swapping in your component once the action completes.

```typescript
// app/dashboard/page.tsx
import { Suspense } from 'react'
import { PostFeed, Weather } from './Components'
 
export default function Posts() {
  return (
    <section>
      <Suspense fallback={<p>Loading feed...</p>}>
        <PostFeed />
      </Suspense>
      <Suspense fallback={<p>Loading weather...</p>}>
        <Weather />
      </Suspense>
    </section>
  )
}
```

By using Suspense, you get the benefits of:

- Streaming Server Rendering - Progressively rendering HTML from the server to the client.
- Selective Hydration - React prioritizes what components to make interactive first based on user interaction.

For more Suspense examples and use cases, please see the [React Documentation](https://react.dev/reference/react/Suspense).

### SEO

- Next.js will wait for data fetching inside `generateMetadata` to complete before streaming UI to the client. This guarantees the first part of a streamed response includes `<head>` tags.
- Since streaming is server-rendered, it does not impact SEO. You can use the Rich Results Test tool from Google to see how your page appears to Google's web crawlers and view the serialized HTML (source).

### Status Codes

When streaming, a 200 status code will be returned to signal that the request was successful.

The server can still communicate errors or issues to the client within the streamed content itself, for example, when using `redirect` or `notFound`. Since the response headers have already been sent to the client, the status code of the response cannot be updated. This does not affect SEO.

## Redirecting

There are a few ways you can handle redirects in Next.js. This page will go through each available option, use cases, and how to manage large numbers of redirects.

| API | Purpose | Where | Status Code |
|-----|---------|-------|-------------|
| `redirect` | Redirect user after a mutation or event | Server Components, Server Actions, Route Handlers | 307 (Temporary) or 303 (Server Action) |
| `permanentRedirect` | Redirect user after a mutation or event | Server Components, Server Actions, Route Handlers | 308 (Permanent) |
| `useRouter` | Perform a client-side navigation | Event Handlers in Client Components | N/A |
| `redirects` in next.config.js | Redirect an incoming request based on a path | next.config.js file | 307 (Temporary) or 308 (Permanent) |
| `NextResponse.redirect` | Redirect an incoming request based on a condition | Middleware | Any |

### redirect function

The `redirect` function allows you to redirect the user to another URL. You can call `redirect` in Server Components, Route Handlers, and Server Actions.

`redirect` is often used after a mutation or event. For example, creating a post:

```typescript
// app/actions.tsx
'use server'

import { redirect } from 'next/navigation'
import { revalidatePath } from 'next/cache'

export async function createPost(id: string) {
  try {
    // Call database
  } catch (error) {
    // Handle errors
  }

  revalidatePath('/posts') // Update cached posts
  redirect(`/post/${id}`) // Navigate to the new post page
}
```

Good to know:

- `redirect` returns a 307 (Temporary Redirect) status code by default. When used in a Server Action, it returns a 303 (See Other), which is commonly used for redirecting to a success page as a result of a POST request.
- `redirect` internally throws an error so it should be called outside of try/catch blocks.
- `redirect` can be called in Client Components during the rendering process but not in event handlers. You can use the `useRouter` hook instead.
- `redirect` also accepts absolute URLs and can be used to redirect to external links.
- If you'd like to redirect before the render process, use `next.config.js` or Middleware.

See the [redirect API reference](https://nextjs.org/docs/app/api-reference/functions/redirect) for more information.

### permanentRedirect function

The `permanentRedirect` function allows you to permanently redirect the user to another URL. You can call `permanentRedirect` in Server Components, Route Handlers, and Server Actions.

`permanentRedirect` is often used after a mutation or event that changes an entity's canonical URL, such as updating a user's profile URL after they change their username:

```typescript
// app/actions.ts
'use server'

import { permanentRedirect } from 'next/navigation'
import { revalidateTag } from 'next/cache'

export async function updateUsername(username: string, formData: FormData) {
  try {
    // Call database
  } catch (error) {
    // Handle errors
  }

  revalidateTag('username') // Update all references to the username
  permanentRedirect(`/profile/${username}`) // Navigate to the new user profile
}
```

Good to know:

- `permanentRedirect` returns a 308 (permanent redirect) status code by default.
- `permanentRedirect` also accepts absolute URLs and can be used to redirect to external links.
- If you'd like to redirect before the render process, use `next.config.js` or Middleware.

See the [permanentRedirect API reference](https://nextjs.org/docs/app/api-reference/functions/permanentRedirect) for more information.

### useRouter() hook

If you need to redirect inside an event handler in a Client Component, you can use the `push` method from the `useRouter` hook. For example:

```typescript
// app/page.tsx
'use client'

import { useRouter } from 'next/navigation'

export default function Page() {
  const router = useRouter()

  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  )
}
```

Good to know:

- If you don't need to programmatically navigate a user, you should use a `<Link>` component.

See the [useRouter API reference](https://nextjs.org/docs/app/api-reference/functions/use-router) for more information.

### redirects in next.config.js

The `redirects` option in the `next.config.js` file allows you to redirect an incoming request path to a different destination path. This is useful when you change the URL structure of pages or have a list of redirects that are known ahead of time.

`redirects` supports path, header, cookie, and query matching, giving you the flexibility to redirect users based on an incoming request.

To use redirects, add the option to your `next.config.js` file:

```javascript
// next.config.js
module.exports = {
  async redirects() {
    return [
      // Basic redirect
      {
        source: '/about',
        destination: '/',
        permanent: true,
      },
      // Wildcard path matching
      {
        source: '/blog/:slug',
        destination: '/news/:slug',
        permanent: true,
      },
    ]
  },
}
```

See the [redirects API reference](https://nextjs.org/docs/app/api-reference/next-config-js/redirects) for more information.

Good to know:

- `redirects` can return a 307 (Temporary Redirect) or 308 (Permanent Redirect) status code with the `permanent` option.
- `redirects` may have a limit on platforms. For example, on Vercel, there's a limit of 1,024 redirects. To manage a large number of redirects (1000+), consider creating a custom solution using Middleware. See managing redirects at scale for more.
- `redirects` runs before Middleware.

### NextResponse.redirect in Middleware

Middleware allows you to run code before a request is completed. Then, based on the incoming request, redirect to a different URL using `NextResponse.redirect`. This is useful if you want to redirect users based on a condition (e.g. authentication, session management, etc) or have a large number of redirects.

For example, to redirect the user to a `/login` page if they are not authenticated:

```typescript
// middleware.ts
import { NextResponse, NextRequest } from 'next/server'
import { authenticate } from 'auth-provider'

export function middleware(request: NextRequest) {
  const isAuthenticated = authenticate(request)

  // If the user is authenticated, continue as normal
  if (isAuthenticated) {
    return NextResponse.next()
  }

  // Redirect to login page if not authenticated
  return NextResponse.redirect(new URL('/login', request.url))
}

export const config = {
  matcher: '/dashboard/:path*',
}
```

Good to know:

- Middleware runs after redirects in `next.config.js` and before rendering.

See the [Middleware documentation](https://nextjs.org/docs/app/building-your-application/routing/middleware) for more information.

### Managing redirects at scale (advanced)

To manage a large number of redirects (1000+), you may consider creating a custom solution using Middleware. This allows you to handle redirects programmatically without having to redeploy your application.

To do this, you'll need to consider:

1. Creating and storing a redirect map.
2. Optimizing data lookup performance.

Next.js Example: See our [Middleware with Bloom filter example](https://github.com/vercel/examples/tree/main/edge-middleware/bloom-filter-redirection) for an implementation of the recommendations below.

#### 1. Creating and storing a redirect map

A redirect map is a list of redirects that you can store in a database (usually a key-value store) or JSON file.

Consider the following data structure:

```json
{
  "/old": {
    "destination": "/new",
    "permanent": true
  },
  "/blog/post-old": {
    "destination": "/blog/post-new",
    "permanent": true
  }
}
```

In Middleware, you can read from a database such as Vercel's Edge Config or Redis, and redirect the user based on the incoming request:

```typescript
// middleware.ts
import { NextResponse, NextRequest } from 'next/server'
import { get } from '@vercel/edge-config'

type RedirectEntry = {
  destination: string
  permanent: boolean
}

export async function middleware(request: NextRequest) {
  const pathname = request.nextUrl.pathname
  const redirectData = await get(pathname)

  if (redirectData && typeof redirectData === 'string') {
    const redirectEntry: RedirectEntry = JSON.parse(redirectData)
    const statusCode = redirectEntry.permanent ? 308 : 307
    return NextResponse.redirect(redirectEntry.destination, statusCode)
  }

  // No redirect found, continue without redirecting
  return NextResponse.next()
}
```

#### 2. Optimizing data lookup performance

Reading a large dataset for every incoming request can be slow and expensive. There are two ways you can optimize data lookup performance:

- Use a database that is optimized for fast reads, such as Vercel Edge Config or Redis.
- Use a data lookup strategy such as a Bloom filter to efficiently check if a redirect exists before reading the larger redirects file or database.

Considering the previous example, you can import a generated bloom filter file into Middleware, then, check if the incoming request pathname exists in the bloom filter.

If it does, forward the request to a Route Handler which will check the actual file and redirect the user to the appropriate URL. This avoids importing a large redirects file into Middleware, which can slow down every incoming request.

```typescript
// middleware.ts
import { NextResponse, NextRequest } from 'next/server'
import { ScalableBloomFilter } from 'bloom-filters'
import GeneratedBloomFilter from './redirects/bloom-filter.json'

type RedirectEntry = {
  destination: string
  permanent: boolean
}

// Initialize bloom filter from a generated JSON file
const bloomFilter = ScalableBloomFilter.fromJSON(GeneratedBloomFilter as any)

export async function middleware(request: NextRequest) {
  // Get the path for the incoming request
  const pathname = request.nextUrl.pathname

  // Check if the path is in the bloom filter
  if (bloomFilter.has(pathname)) {
    // Forward the pathname to the Route Handler
    const api = new URL(
      `/api/redirects?pathname=${encodeURIComponent(request.nextUrl.pathname)}`,
      request.nextUrl.origin
    )

    try {
      // Fetch redirect data from the Route Handler
      const redirectData = await fetch(api)

      if (redirectData.ok) {
        const redirectEntry: RedirectEntry | undefined =
          await redirectData.json()

        if (redirectEntry) {
          // Determine the status code
          const statusCode = redirectEntry.permanent ? 308 : 307

          // Redirect to the destination
          return NextResponse.redirect(redirectEntry.destination, statusCode)
        }
      }
    } catch (error) {
      console.error(error)
    }
  }

  // No redirect found, continue the request without redirecting
  return NextResponse.next()
}
```

Then, in the Route Handler:

```typescript
// app/redirects/route.ts
import { NextRequest, NextResponse } from 'next/server'
import redirects from '@/app/redirects/redirects.json'

type RedirectEntry = {
  destination: string
  permanent: boolean
}

export function GET(request: NextRequest) {
  const pathname = request.nextUrl.searchParams.get('pathname')
  if (!pathname) {
    return new Response('Bad Request', { status: 400 })
  }

  // Get the redirect entry from the redirects.json file
  const redirect = (redirects as Record<string, RedirectEntry>)[pathname]

  // Account for bloom filter false positives
  if (!redirect) {
    return new Response('No redirect', { status: 400 })
  }

  // Return the redirect entry
  return NextResponse.json(redirect)
}
```

Good to know:

- To generate a bloom filter, you can use a library like [bloom-filters](https://www.npmjs.com/package/bloom-filters).
- You should validate requests made to your Route Handler to prevent malicious requests.


## Route Groups

In the `app` directory, nested folders are normally mapped to URL paths. However, you can mark a folder as a Route Group to prevent the folder from being included in the route's URL path.

This allows you to organize your route segments and project files into logical groups without affecting the URL path structure.

Route groups are useful for:

- Organizing routes into groups e.g. by site section, intent, or team.
- Enabling nested layouts in the same route segment level:
  - Creating multiple nested layouts in the same segment, including multiple root layouts
  - Adding a layout to a subset of routes in a common segment

### Convention

A route group can be created by wrapping a folder's name in parenthesis: `(folderName)`

### Examples

#### Organize routes without affecting the URL path

To organize routes without affecting the URL, create a group to keep related routes together. The folders in parenthesis will be omitted from the URL (e.g. `(marketing)` or `(shop)`).

![Organizing Routes with Route Groups](image-url-here)

Even though routes inside `(marketing)` and `(shop)` share the same URL hierarchy, you can create a different layout for each group by adding a `layout.js` file inside their folders.

![Route Groups with Multiple Layouts](image-url-here)

#### Opting specific segments into a layout

To opt specific routes into a layout, create a new route group (e.g. `(shop)`) and move the routes that share the same layout into the group (e.g. `account` and `cart`). The routes outside of the group will not share the layout (e.g. `checkout`).

![Route Groups with Opt-in Layouts](image-url-here)

#### Creating multiple root layouts

To create multiple root layouts, remove the top-level `layout.js` file, and add a `layout.js` file inside each route group. This is useful for partitioning an application into sections that have a completely different UI or experience. The `<html>` and `<body>` tags need to be added to each root layout.

![Route Groups with Multiple Root Layouts](image-url-here)

In the example above, both `(marketing)` and `(shop)` have their own root layout.

> Good to know:
> 
> - The naming of route groups has no special significance other than for organization. They do not affect the URL path.
> - Routes that include a route group should not resolve to the same URL path as other routes. For example, since route groups don't affect URL structure, `(marketing)/about/page.js` and `(shop)/about/page.js` would both resolve to `/about` and cause an error.
> - If you use multiple root layouts without a top-level `layout.js` file, your home `page.js` file should be defined in one of the route groups, For example: `app/(marketing)/page.js`.
> - Navigating across multiple root layouts will cause a full page load (as opposed to a client-side navigation). For example, navigating from `/cart` that uses `app/(shop)/layout.js` to `/blog` that uses `app/(marketing)/layout.js` will cause a full page load. This only applies to multiple root layouts.

## Project Organization and File Colocation

Apart from routing folder and file conventions, Next.js is unopinionated about how you organize and colocate your project files.

This page shares default behavior and features you can use to organize your project.

### Safe colocation by default

In the `app` directory, nested folder hierarchy defines route structure.

Each folder represents a route segment that is mapped to a corresponding segment in a URL path.

However, even though route structure is defined through folders, a route is not publicly accessible until a `page.js` or `route.js` file is added to a route segment.

*A diagram showing how a route is not publicly accessible until a page.js or route.js file is added to a route segment.*

And, even when a route is made publicly accessible, only the content returned by `page.js` or `route.js` is sent to the client.

*A diagram showing how page.js and route.js files make routes publicly accessible.*

This means that project files can be safely colocated inside route segments in the `app` directory without accidentally being routable.

*A diagram showing colocated project files are not routable even when a segment contains a page.js or route.js file.*

> Good to know:
> - This is different from the `pages` directory, where any file in pages is considered a route.
> - While you can colocate your project files in `app` you don't have to. If you prefer, you can keep them outside the `app` directory.

### Project organization features

Next.js provides several features to help you organize your project.

#### Private Folders

Private folders can be created by prefixing a folder with an underscore: `_folderName`

This indicates the folder is a private implementation detail and should not be considered by the routing system, thereby opting the folder and all its subfolders out of routing.

*An example folder structure using private folders*

Since files in the `app` directory can be safely colocated by default, private folders are not required for colocation. However, they can be useful for:

- Separating UI logic from routing logic.
- Consistently organizing internal files across a project and the Next.js ecosystem.
- Sorting and grouping files in code editors.
- Avoiding potential naming conflicts with future Next.js file conventions.

> Good to know:
> - While not a framework convention, you might also consider marking files outside private folders as "private" using the same underscore pattern.
> - You can create URL segments that start with an underscore by prefixing the folder name with `%5F` (the URL-encoded form of an underscore): `%5FfolderName`.
> - If you don't use private folders, it would be helpful to know Next.js special file conventions to prevent unexpected naming conflicts.

#### Route Groups

Route groups can be created by wrapping a folder in parenthesis: `(folderName)`

This indicates the folder is for organizational purposes and should not be included in the route's URL path.

*An example folder structure using route groups*

Route groups are useful for:

- Organizing routes into groups e.g. by site section, intent, or team.
- Enabling nested layouts in the same route segment level:
  - Creating multiple nested layouts in the same segment, including multiple root layouts
  - Adding a layout to a subset of routes in a common segment

#### src Directory

Next.js supports storing application code (including `app`) inside an optional `src` directory. This separates application code from project configuration files which mostly live in the root of a project.

*An example folder structure with the `src` directory*

#### Module Path Aliases

Next.js supports Module Path Aliases which make it easier to read and maintain imports across deeply nested project files.

```javascript
// app/dashboard/settings/analytics/page.js

// before
import { Button } from '../../../components/button'
 
// after
import { Button } from '@/components/button'
```

### Project organization strategies

There is no "right" or "wrong" way when it comes to organizing your own files and folders in a Next.js project.

The following section lists a very high-level overview of common strategies. The simplest takeaway is to choose a strategy that works for you and your team and be consistent across the project.

> Good to know: In our examples below, we're using `components` and `lib` folders as generalized placeholders, their naming has no special framework significance and your projects might use other folders like `ui`, `utils`, `hooks`, `styles`, etc.

#### Store project files outside of app

This strategy stores all application code in shared folders in the root of your project and keeps the `app` directory purely for routing purposes.

*An example folder structure with project files outside of app*

#### Store project files in top-level folders inside of app

This strategy stores all application code in shared folders in the root of the `app` directory.

*An example folder structure with project files inside app*

#### Split project files by feature or route

This strategy stores globally shared application code in the root `app` directory and splits more specific application code into the route segments that use them.

## Dynamic Routes

When you don't know the exact segment names ahead of time and want to create routes from dynamic data, you can use Dynamic Segments that are filled in at request time or prerendered at build time.

### Convention

A Dynamic Segment can be created by wrapping a folder's name in square brackets: `[folderName]`. For example, `[id]` or `[slug]`.

Dynamic Segments are passed as the `params` prop to `layout`, `page`, `route`, and `generateMetadata` functions.

### Example

For example, a blog could include the following route `app/blog/[slug]/page.js` where `[slug]` is the Dynamic Segment for blog posts.

```typescript
// app/blog/[slug]/page.tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <div>My Post: {params.slug}</div>
}
```

| Route | Example URL | params |
|-------|-------------|--------|
| app/blog/[slug]/page.js | /blog/a | `{ slug: 'a' }` |
| app/blog/[slug]/page.js | /blog/b | `{ slug: 'b' }` |
| app/blog/[slug]/page.js | /blog/c | `{ slug: 'c' }` |

See the `generateStaticParams()` page to learn how to generate the params for the segment.

> **Good to know**: Dynamic Segments are equivalent to Dynamic Routes in the pages directory.

### Generating Static Params

The `generateStaticParams` function can be used in combination with dynamic route segments to statically generate routes at build time instead of on-demand at request time.

```typescript
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetch('https://.../posts').then((res) => res.json())
 
  return posts.map((post) => ({
    slug: post.slug,
  }))
}
```

The primary benefit of the `generateStaticParams` function is its smart retrieval of data. If content is fetched within the `generateStaticParams` function using a fetch request, the requests are automatically memoized. This means a fetch request with the same arguments across multiple `generateStaticParams`, Layouts, and Pages will only be made once, which decreases build times.

Use the migration guide if you are migrating from the pages directory.

See `generateStaticParams` server function documentation for more information and advanced use cases.

### Catch-all Segments

Dynamic Segments can be extended to catch-all subsequent segments by adding an ellipsis inside the brackets `[...folderName]`.

For example, `app/shop/[...slug]/page.js` will match `/shop/clothes`, but also `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`, and so on.

| Route | Example URL | params |
|-------|-------------|--------|
| app/shop/[...slug]/page.js | /shop/a | `{ slug: ['a'] }` |
| app/shop/[...slug]/page.js | /shop/a/b | `{ slug: ['a', 'b'] }` |
| app/shop/[...slug]/page.js | /shop/a/b/c | `{ slug: ['a', 'b', 'c'] }` |

### Optional Catch-all Segments

Catch-all Segments can be made optional by including the parameter in double square brackets: `[[...folderName]]`.

For example, `app/shop/[[...slug]]/page.js` will also match `/shop`, in addition to `/shop/clothes`, `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`.

The difference between catch-all and optional catch-all segments is that with optional, the route without the parameter is also matched (`/shop` in the example above).

| Route | Example URL | params |
|-------|-------------|--------|
| app/shop/[[...slug]]/page.js | /shop | `{}` |
| app/shop/[[...slug]]/page.js | /shop/a | `{ slug: ['a'] }` |
| app/shop/[[...slug]]/page.js | /shop/a/b | `{ slug: ['a', 'b'] }` |
| app/shop/[[...slug]]/page.js | /shop/a/b/c | `{ slug: ['a', 'b', 'c'] }` |

### TypeScript

When using TypeScript, you can add types for params depending on your configured route segment.

```typescript
// app/blog/[slug]/page.tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <h1>My Page</h1>
}
```

| Route | params Type Definition |
|-------|------------------------|
| app/blog/[slug]/page.js | `{ slug: string }` |
| app/shop/[...slug]/page.js | `{ slug: string[] }` |
| app/shop/[[...slug]]/page.js | `{ slug?: string[] }` |
| app/[categoryId]/[itemId]/page.js | `{ categoryId: string, itemId: string }` |

> **Good to know**: This may be done automatically by the TypeScript plugin in the future.

## Parallel Routes

Parallel Routes allows you to simultaneously or conditionally render one or more pages within the same layout. They are useful for highly dynamic sections of an app, such as dashboards and feeds on social sites.

For example, considering a dashboard, you can use parallel routes to simultaneously render the team and analytics pages:

[Parallel Routes Diagram]

### Slots

Parallel routes are created using named slots. Slots are defined with the `@folder` convention. For example, the following file structure defines two slots: `@analytics` and `@team`:

[Parallel Routes File-system Structure]

Slots are passed as props to the shared parent layout. For the example above, the component in `app/layout.js` now accepts the `@analytics` and `@team` slots props, and can render them in parallel alongside the `children` prop:

```typescript
// app/layout.tsx
export default function Layout({
  children,
  team,
  analytics,
}: {
  children: React.ReactNode
  analytics: React.ReactNode
  team: React.ReactNode
}) {
  return (
    <>
      {children}
      {team}
      {analytics}
    </>
  )
}
```

However, slots are not route segments and do not affect the URL structure. For example, for `/@analytics/views`, the URL will be `/views` since `@analytics` is a slot.

**Good to know:**

- The `children` prop is an implicit slot that does not need to be mapped to a folder. This means `app/page.js` is equivalent to `app/@children/page.js`.

### Active state and navigation

By default, Next.js keeps track of the active state (or subpage) for each slot. However, the content rendered within a slot will depend on the type of navigation:

- **Soft Navigation**: During client-side navigation, Next.js will perform a partial render, changing the subpage within the slot, while maintaining the other slot's active subpages, even if they don't match the current URL.
- **Hard Navigation**: After a full-page load (browser refresh), Next.js cannot determine the active state for the slots that don't match the current URL. Instead, it will render a `default.js` file for the unmatched slots, or 404 if `default.js` doesn't exist.

**Good to know:**

- The 404 for unmatched routes helps ensure that you don't accidentally render a parallel route on a page that it was not intended for.

### default.js

You can define a `default.js` file to render as a fallback for unmatched slots during the initial load or full-page reload.

Consider the following folder structure. The `@team` slot has a `/settings` page, but `@analytics` does not.

[Parallel Routes unmatched routes]

When navigating to `/settings`, the `@team` slot will render the `/settings` page while maintaining the currently active page for the `@analytics` slot.

On refresh, Next.js will render a `default.js` for `@analytics`. If `default.js` doesn't exist, a 404 is rendered instead.

Additionally, since `children` is an implicit slot, you also need to create a `default.js` file to render a fallback for `children` when Next.js cannot recover the active state of the parent page.

### useSelectedLayoutSegment(s)

Both `useSelectedLayoutSegment` and `useSelectedLayoutSegments` accept a `parallelRoutesKey` parameter, which allows you to read the active route segment within a slot.

```typescript
// app/layout.tsx
'use client'

import { useSelectedLayoutSegment } from 'next/navigation'

export default function Layout({ auth }: { auth: React.ReactNode }) {
  const loginSegment = useSelectedLayoutSegment('auth')
  // ...
}
```

When a user navigates to `app/@auth/login` (or `/login` in the URL bar), `loginSegment` will be equal to the string "login".

### Examples

#### Conditional Routes

You can use Parallel Routes to conditionally render routes based on certain conditions, such as user role. For example, to render a different dashboard page for the `/admin` or `/user` roles:

[Conditional routes diagram]

```typescript
// app/dashboard/layout.tsx
import { checkUserRole } from '@/lib/auth'

export default function Layout({
  user,
  admin,
}: {
  user: React.ReactNode
  admin: React.ReactNode
}) {
  const role = checkUserRole()
  return <>{role === 'admin' ? admin : user}</>
}
```

#### Tab Groups

You can add a layout inside a slot to allow users to navigate the slot independently. This is useful for creating tabs.

For example, the `@analytics` slot has two subpages: `/page-views` and `/visitors`.

[Analytics slot with two subpages and a layout]

Within `@analytics`, create a layout file to share the tabs between the two pages:

```typescript
// app/@analytics/layout.tsx
import Link from 'next/link'

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <nav>
        <Link href="/page-views">Page Views</Link>
        <Link href="/visitors">Visitors</Link>
      </nav>
      <div>{children}</div>
    </>
  )
}
```

#### Modals

Parallel Routes can be used together with Intercepting Routes to create modals that support deep linking. This allows you to solve common challenges when building modals, such as:

- Making the modal content shareable through a URL.
- Preserving context when the page is refreshed, instead of closing the modal.
- Closing the modal on backwards navigation rather than going to the previous route.
- Reopening the modal on forwards navigation.

Consider the following UI pattern, where a user can open a login modal from a layout using client-side navigation, or access a separate `/login` page:

[Parallel Routes Diagram]

To implement this pattern, start by creating a `/login` route that renders your main login page.

[Parallel Routes Diagram]

```typescript
// app/login/page.tsx
import { Login } from '@/app/ui/login'

export default function Page() {
  return <Login />
}
```

Then, inside the `@auth` slot, add `default.js` file that returns null. This ensures that the modal is not rendered when it's not active.

```typescript
// app/@auth/default.tsx
export default function Default() {
  return '...'
}
```

Inside your `@auth` slot, intercept the `/login` route by updating the `/(.)login` folder. Import the `<Modal>` component and its children into the `/(.)login/page.tsx` file:

```typescript
// app/@auth/(.)login/page.tsx
import { Modal } from '@/app/ui/modal'
import { Login } from '@/app/ui/login'

export default function Page() {
  return (
    <Modal>
      <Login />
    </Modal>
  )
}
```

**Good to know:**

- The convention used to intercept the route, e.g. `(.)`, depends on your file-system structure. See Intercepting Routes convention.
- By separating the `<Modal>` functionality from the modal content (`<Login>`), you can ensure any content inside the modal, e.g. forms, are Server Components. See Interleaving Client and Server Components for more information.

##### Opening the modal

Now, you can leverage the Next.js router to open and close the modal. This ensures the URL is correctly updated when the modal is open, and when navigating backwards and forwards.

To open the modal, pass the `@auth` slot as a prop to the parent layout and render it alongside the `children` prop.

```typescript
// app/layout.tsx
import Link from 'next/link'

export default function Layout({
  auth,
  children,
}: {
  auth: React.ReactNode
  children: React.ReactNode
}) {
  return (
    <>
      <nav>
        <Link href="/login">Open modal</Link>
      </nav>
      <div>{auth}</div>
      <div>{children}</div>
    </>
  )
}
```

When the user clicks the `<Link>`, the modal will open instead of navigating to the `/login` page. However, on refresh or initial load, navigating to `/login` will take the user to the main login page.

##### Closing the modal

You can close the modal by calling `router.back()` or by using the `Link` component.

```typescript
// app/ui/modal.tsx
'use client'

import { useRouter } from 'next/navigation'

export function Modal({ children }: { children: React.ReactNode }) {
  const router = useRouter()

  return (
    <>
      <button
        onClick={() => {
          router.back()
        }}
      >
        Close modal
      </button>
      <div>{children}</div>
    </>
  )
}
```

When using the `Link` component to navigate away from a page that shouldn't render the `@auth` slot anymore, we need to make sure the parallel route matches to a component that returns null. For example, when navigating back to the root page, we create a `@auth/page.tsx` component:

```typescript
// app/ui/modal.tsx
import Link from 'next/link'

export function Modal({ children }: { children: React.ReactNode }) {
  return (
    <>
      <Link href="/">Close modal</Link>
      <div>{children}</div>
    </>
  )
}
```

```typescript
// app/@auth/page.tsx
export default function Page() {
  return '...'
}
```

Or if navigating to any other page (such as `/foo`, `/foo/bar`, etc), you can use a catch-all slot:

```typescript
// app/@auth/[...catchAll]/page.tsx
export default function CatchAll() {
  return '...'
}
```

**Good to know:**

- We use a catch-all route in our `@auth` slot to close the modal because of the behavior described in Active state and navigation. Since client-side navigations to a route that no longer match the slot will remain visible, we need to match the slot to a route that returns null to close the modal.
- Other examples could include opening a photo modal in a gallery while also having a dedicated `/photo/[id]` page, or opening a shopping cart in a side modal.

View an example of modals with Intercepted and Parallel Routes.

### Loading and Error UI

Parallel Routes can be streamed independently, allowing you to define independent error and loading states for each route:

[Parallel routes enable custom error and loading states]

See the Loading UI and Error Handling documentation for more information.

## Intercepting Routes

Intercepting routes allows you to load a route from another part of your application within the current layout. This routing paradigm can be useful when you want to display the content of a route without the user switching to a different context.

For example, when clicking on a photo in a feed, you can display the photo in a modal, overlaying the feed. In this case, Next.js intercepts the `/photo/123` route, masks the URL, and overlays it over `/feed`.

### Intercepting routes soft navigation

However, when navigating to the photo by clicking a shareable URL or by refreshing the page, the entire photo page should render instead of the modal. No route interception should occur.

### Intercepting routes hard navigation

### Convention

Intercepting routes can be defined with the `(..)` convention, which is similar to relative path convention `../` but for segments.

You can use:

- `(.)` to match segments on the same level
- `(..)` to match segments one level above
- `(..)(..)` to match segments two levels above
- `(...)` to match segments from the root app directory

For example, you can intercept the photo segment from within the feed segment by creating a `(..)photo` directory.

```
Intercepting routes folder structure
```

Note that the `(..)` convention is based on route segments, not the file-system.

### Examples

#### Modals

Intercepting Routes can be used together with Parallel Routes to create modals. This allows you to solve common challenges when building modals, such as:

- Making the modal content shareable through a URL.
- Preserving context when the page is refreshed, instead of closing the modal.
- Closing the modal on backwards navigation rather than going to the previous route.
- Reopening the modal on forwards navigation.

Consider the following UI pattern, where a user can open a photo modal from a gallery using client-side navigation, or navigate to the photo page directly from a shareable URL:

```
Intercepting routes modal example
```

In the above example, the path to the photo segment can use the `(..)` matcher since `@modal` is a slot and not a segment. This means that the photo route is only one segment level higher, despite being two file-system levels higher.

See the Parallel Routes documentation for a step-by-step example, or see our [image gallery example](https://github.com/vercel/next.js/tree/canary/examples/with-parallel-routes-and-intercepting-routes).

**Good to know:**

- Other examples could include opening a login modal in a top navbar while also having a dedicated `/login` page, or opening a shopping cart in a side modal.

## Route Handlers

Route Handlers allow you to create custom request handlers for a given route using the Web Request and Response APIs.

### Route.js Special File

**Good to know**: Route Handlers are only available inside the `app` directory. They are the equivalent of API Routes inside the `pages` directory meaning you do not need to use API Routes and Route Handlers together.

### Convention

Route Handlers are defined in a `route.js|ts` file inside the `app` directory:

```typescript
// app/api/route.ts
export async function GET(request: Request) {}
```

Route Handlers can be nested anywhere inside the `app` directory, similar to `page.js` and `layout.js`. But there cannot be a `route.js` file at the same route segment level as `page.js`.

### Supported HTTP Methods

The following HTTP methods are supported: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, and `OPTIONS`. If an unsupported method is called, Next.js will return a 405 Method Not Allowed response.

### Extended NextRequest and NextResponse APIs

In addition to supporting the native Request and Response APIs, Next.js extends them with `NextRequest` and `NextResponse` to provide convenient helpers for advanced use cases.

### Behavior

#### Caching

Route Handlers are not cached by default. You can, however, opt into caching for GET methods. To do so, use a route config option such as `export const dynamic = 'force-static'` in your Route Handler file.

```typescript
// app/items/route.ts
export const dynamic = 'force-static'

export async function GET() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  })
  const data = await res.json()

  return Response.json({ data })
}
```

#### Special Route Handlers

Special Route Handlers like `sitemap.ts`, `opengraph-image.tsx`, and `icon.tsx`, and other metadata files remain static by default unless they use dynamic functions or dynamic config options.

### Route Resolution

You can consider a route the lowest level routing primitive.

- They do not participate in layouts or client-side navigations like `page`.
- There cannot be a `route.js` file at the same route as `page.js`.

| Page | Route | Result |
|------|-------|--------|
| app/page.js | app/route.js | ❌ Conflict |
| app/page.js | app/api/route.js | ✅ Valid |
| app/[user]/page.js | app/api/route.js | ✅ Valid |

Each `route.js` or `page.js` file takes over all HTTP verbs for that route.

```javascript
// app/page.js
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}

// ❌ Conflict
// `app/route.js`
export async function POST(request) {}
```

### Examples

The following examples show how to combine Route Handlers with other Next.js APIs and features.

#### Revalidating Cached Data

You can revalidate cached data using the `next.revalidate` option:

```typescript
// app/items/route.ts
export async function GET() {
  const res = await fetch('https://data.mongodb-api.com/...', {
    next: { revalidate: 60 }, // Revalidate every 60 seconds
  })
  const data = await res.json()

  return Response.json(data)
}
```

Alternatively, you can use the `revalidate` segment config option:

```typescript
export const revalidate = 60
```

#### Dynamic Functions

Route Handlers can be used with dynamic functions from Next.js, like `cookies` and `headers`.

##### Cookies

You can read or set cookies with `cookies` from `next/headers`. This server function can be called directly in a Route Handler, or nested inside of another function.

Alternatively, you can return a new `Response` using the `Set-Cookie` header.

```typescript
// app/api/route.ts
import { cookies } from 'next/headers'

export async function GET(request: Request) {
  const cookieStore = cookies()
  const token = cookieStore.get('token')

  return new Response('Hello, Next.js!', {
    status: 200,
    headers: { 'Set-Cookie': `token=${token.value}` },
  })
}
```

You can also use the underlying Web APIs to read cookies from the request (`NextRequest`):

```typescript
// app/api/route.ts
import { type NextRequest } from 'next/server'

export async function GET(request: NextRequest) {
  const token = request.cookies.get('token')
}
```

##### Headers

You can read headers with `headers` from `next/headers`. This server function can be called directly in a Route Handler, or nested inside of another function.

This `headers` instance is read-only. To set headers, you need to return a new `Response` with new headers.

```typescript
// app/api/route.ts
import { headers } from 'next/headers'

export async function GET(request: Request) {
  const headersList = headers()
  const referer = headersList.get('referer')

  return new Response('Hello, Next.js!', {
    status: 200,
    headers: { referer: referer },
  })
}
```

You can also use the underlying Web APIs to read headers from the request (`NextRequest`):

```typescript
// app/api/route.ts
import { type NextRequest } from 'next/server'

export async function GET(request: NextRequest) {
  const requestHeaders = new Headers(request.headers)
}
```

##### Redirects

```typescript
// app/api/route.ts
import { redirect } from 'next/navigation'

export async function GET(request: Request) {
  redirect('https://nextjs.org/')
}
```

#### Dynamic Route Segments

We recommend reading the [Defining Routes](https://nextjs.org/docs/app/building-your-application/routing/defining-routes) page before continuing.

Route Handlers can use Dynamic Segments to create request handlers from dynamic data.

```typescript
// app/items/[slug]/route.ts
export async function GET(
  request: Request,
  { params }: { params: { slug: string } }
) {
  const slug = params.slug // 'a', 'b', or 'c'
}
```

| Route | Example URL | params |
|-------|-------------|--------|
| app/items/[slug]/route.js | /items/a | { slug: 'a' } |
| app/items/[slug]/route.js | /items/b | { slug: 'b' } |
| app/items/[slug]/route.js | /items/c | { slug: 'c' } |

#### URL Query Parameters

The request object passed to the Route Handler is a `NextRequest` instance, which has some additional convenience methods, including for more easily handling query parameters.

```typescript
// app/api/search/route.ts
import { type NextRequest } from 'next/server'

export function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams
  const query = searchParams.get('query')
  // query is "hello" for /api/search?query=hello
}
```

#### Streaming

Streaming is commonly used in combination with Large Language Models (LLMs), such as OpenAI, for AI-generated content. Learn more about the [AI SDK](https://sdk.vercel.ai/docs).

```typescript
// app/api/chat/route.ts
import { openai } from '@ai-sdk/openai'
import { StreamingTextResponse, streamText } from 'ai'

export async function POST(req) {
  const { messages } = await req.json()
  const result = await streamText({
    model: openai('gpt-4-turbo'),
    messages,
  })

  return new StreamingTextResponse(result.toAIStream())
}
```

These abstractions use the Web APIs to create a stream. You can also use the underlying Web APIs directly.

```typescript
// app/api/route.ts
// https://developer.mozilla.org/docs/Web/API/ReadableStream#convert_async_iterator_to_stream
function iteratorToStream(iterator: any) {
  return new ReadableStream({
    async pull(controller) {
      const { value, done } = await iterator.next()

      if (done) {
        controller.close()
      } else {
        controller.enqueue(value)
      }
    },
  })
}

function sleep(time: number) {
  return new Promise((resolve) => {
    setTimeout(resolve, time)
  })
}

const encoder = new TextEncoder()

async function* makeIterator() {
  yield encoder.encode('<p>One</p>')
  await sleep(200)
  yield encoder.encode('<p>Two</p>')
  await sleep(200)
  yield encoder.encode('<p>Three</p>')
}

export async function GET() {
  const iterator = makeIterator()
  const stream = iteratorToStream(iterator)

  return new Response(stream)
}
```

#### Request Body

You can read the Request body using the standard Web API methods:

```typescript
// app/items/route.ts
export async function POST(request: Request) {
  const res = await request.json()
  return Response.json({ res })
}
```

#### Request Body FormData

You can read the FormData using the `request.formData()` function:

```typescript
// app/items/route.ts
export async function POST(request: Request) {
  const formData = await request.formData()
  const name = formData.get('name')
  const email = formData.get('email')
  return Response.json({ name, email })
}
```

Since formData data are all strings, you may want to use [zod-form-data](https://github.com/airjp73/remix-validated-form/tree/main/packages/zod-form-data) to validate the request and retrieve data in the format you prefer (e.g. number).

#### CORS

You can set CORS headers for a specific Route Handler using the standard Web API methods:

```typescript
// app/api/route.ts
export async function GET(request: Request) {
  return new Response('Hello, Next.js!', {
    status: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization',
    },
  })
}
```

**Good to know**:

- To add CORS headers to multiple Route Handlers, you can use [Middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware) or the `next.config.js` file.
- Alternatively, see our [CORS example package](https://github.com/vercel/examples/tree/main/packages/cors).

#### Webhooks

You can use a Route Handler to receive webhooks from third-party services:

```typescript
// app/api/route.ts
export async function POST(request: Request) {
  try {
    const text = await request.text()
    // Process the webhook payload
  } catch (error) {
    return new Response(`Webhook error: ${error.message}`, {
      status: 400,
    })
  }

  return new Response('Success!', {
    status: 200,
  })
}
```

Notably, unlike API Routes with the Pages Router, you do not need to use `bodyParser` to use any additional configuration.

#### Non-UI Responses

You can use Route Handlers to return non-UI content. Note that `sitemap.xml`, `robots.txt`, app icons, and open graph images all have built-in support.

```typescript
// app/rss.xml/route.ts
export async function GET() {
  return new Response(
    `<?xml version="1.0" encoding="UTF-8" ?>
<rss version="2.0">

<channel>
  <title>Next.js Documentation</title>
  <link>https://nextjs.org/docs</link>
  <description>The React Framework for the Web</description>
</channel>

</rss>`,
    {
      headers: {
        'Content-Type': 'text/xml',
      },
    }
  )
}
```

#### Segment Config Options

Route Handlers use the same [route segment configuration](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config) as pages and layouts.

```typescript
// app/items/route.ts
export const dynamic = 'auto'
export const dynamicParams = true
export const revalidate = false
export const fetchCache = 'auto'
export const runtime = 'nodejs'
export const preferredRegion = 'auto'
```

## Middleware

Middleware allows you to run code before a request is completed. Then, based on the incoming request, you can modify the response by rewriting, redirecting, modifying the request or response headers, or responding directly.

Middleware runs before cached content and routes are matched. See [Matching Paths](#matching-paths) for more details.

### Use Cases

Integrating Middleware into your application can lead to significant improvements in performance, security, and user experience. Some common scenarios where Middleware is particularly effective include:

- **Authentication and Authorization**: Ensure user identity and check session cookies before granting access to specific pages or API routes.
- **Server-Side Redirects**: Redirect users at the server level based on certain conditions (e.g., locale, user role).
- **Path Rewriting**: Support A/B testing, feature rollouts, or legacy paths by dynamically rewriting paths to API routes or pages based on request properties.
- **Bot Detection**: Protect your resources by detecting and blocking bot traffic.
- **Logging and Analytics**: Capture and analyze request data for insights before processing by the page or API.
- **Feature Flagging**: Enable or disable features dynamically for seamless feature rollouts or testing.

Recognizing situations where middleware may not be the optimal approach is just as crucial. Here are some scenarios to be mindful of:

- **Complex Data Fetching and Manipulation**: Middleware is not designed for direct data fetching or manipulation, this should be done within Route Handlers or server-side utilities instead.
- **Heavy Computational Tasks**: Middleware should be lightweight and respond quickly or it can cause delays in page load. Heavy computational tasks or long-running processes should be done within dedicated Route Handlers.
- **Extensive Session Management**: While Middleware can manage basic session tasks, extensive session management should be managed by dedicated authentication services or within Route Handlers.
- **Direct Database Operations**: Performing direct database operations within Middleware is not recommended. Database interactions should be done within Route Handlers or server-side utilities.

### Convention

Use the file `middleware.ts` (or `.js`) in the root of your project to define Middleware. For example, at the same level as `pages` or `app`, or inside `src` if applicable.

**Note**: While only one `middleware.ts` file is supported per project, you can still organize your middleware logic modularly. Break out middleware functionalities into separate `.ts` or `.js` files and import them into your main `middleware.ts` file. This allows for cleaner management of route-specific middleware, aggregated in the `middleware.ts` for centralized control. By enforcing a single middleware file, it simplifies configuration, prevents potential conflicts, and optimizes performance by avoiding multiple middleware layers.

### Example

`middleware.ts`

```typescript
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
 
// This function can be marked `async` if using `await` inside
export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL('/home', request.url))
}
 
// See "Matching Paths" below to learn more
export const config = {
  matcher: '/about/:path*',
}
```

### Matching Paths

Middleware will be invoked for every route in your project. Given this, it's crucial to use matchers to precisely target or exclude specific routes. The following is the execution order:

1. `headers` from `next.config.js`
2. `redirects` from `next.config.js`
3. Middleware (rewrites, redirects, etc.)
4. `beforeFiles` (rewrites) from `next.config.js`
5. Filesystem routes (`public/`, `_next/static/`, `pages/`, `app/`, etc.)
6. `afterFiles` (rewrites) from `next.config.js`
7. Dynamic Routes (`/blog/[slug]`)
8. `fallback` (rewrites) from `next.config.js`

There are two ways to define which paths Middleware will run on:

1. Custom matcher config
2. Conditional statements

#### Matcher

`matcher` allows you to filter Middleware to run on specific paths.

```javascript
export const config = {
  matcher: '/about/:path*',
}
```

You can match a single path or multiple paths with an array syntax:

```javascript
export const config = {
  matcher: ['/about/:path*', '/dashboard/:path*'],
}
```

The matcher config allows full regex so matching like negative lookaheads or character matching is supported. An example of a negative lookahead to match all except specific paths can be seen here:

```javascript
export const config = {
  matcher: [
    /*
     * Match all request paths except for the ones starting with:
     * - api (API routes)
     * - _next/static (static files)
     * - _next/image (image optimization files)
     * - favicon.ico, sitemap.xml, robots.txt (metadata files)
     */
    '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
  ],
}
```

You can also bypass Middleware for certain requests by using the `missing` or `has` arrays, or a combination of both:

```javascript
export const config = {
  matcher: [
    /*
     * Match all request paths except for the ones starting with:
     * - api (API routes)
     * - _next/static (static files)
     * - _next/image (image optimization files)
     * - favicon.ico, sitemap.xml, robots.txt (metadata files)
     */
    {
      source:
        '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
      missing: [
        { type: 'header', key: 'next-router-prefetch' },
        { type: 'header', key: 'purpose', value: 'prefetch' },
      ],
    },
 
    {
      source:
        '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
      has: [
        { type: 'header', key: 'next-router-prefetch' },
        { type: 'header', key: 'purpose', value: 'prefetch' },
      ],
    },
 
    {
      source:
        '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
      has: [{ type: 'header', key: 'x-present' }],
      missing: [{ type: 'header', key: 'x-missing', value: 'prefetch' }],
    },
  ],
}
```

**Good to know**: The matcher values need to be constants so they can be statically analyzed at build-time. Dynamic values such as variables will be ignored.

Configured matchers:

- MUST start with `/`
- Can include named parameters: `/about/:path` matches `/about/a` and `/about/b` but not `/about/a/c`
- Can have modifiers on named parameters (starting with `:`): `/about/:path*` matches `/about/a/b/c` because `*` is zero or more. `?` is zero or one and `+` one or more
- Can use regular expression enclosed in parenthesis: `/about/(.*)` is the same as `/about/:path*`

**Good to know**: For backward compatibility, Next.js always considers `/public` as `/public/index`. Therefore, a matcher of `/public/:path` will match.

#### Conditional Statements

```typescript
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
 
export function middleware(request: NextRequest) {
  if (request.nextUrl.pathname.startsWith('/about')) {
    return NextResponse.rewrite(new URL('/about-2', request.url))
  }
 
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.rewrite(new URL('/dashboard/user', request.url))
  }
}
```

### NextResponse

The NextResponse API allows you to:

- redirect the incoming request to a different URL
- rewrite the response by displaying a given URL
- Set request headers for API Routes, `getServerSideProps`, and rewrite destinations
- Set response cookies
- Set response headers

To produce a response from Middleware, you can:

- rewrite to a route (Page or Route Handler) that produces a response
- return a NextResponse directly. See [Producing a Response](#producing-a-response)

### Using Cookies

Cookies are regular headers. On a Request, they are stored in the `Cookie` header. On a Response they are in the `Set-Cookie` header. Next.js provides a convenient way to access and manipulate these cookies through the `cookies` extension on `NextRequest` and `NextResponse`.

- For incoming requests, `cookies` comes with the following methods: `get`, `getAll`, `set`, and `delete` cookies. You can check for the existence of a cookie with `has` or remove all cookies with `clear`.
- For outgoing responses, `cookies` have the following methods `get`, `getAll`, `set`, and `delete`.

```typescript
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
 
export function middleware(request: NextRequest) {
  // Assume a "Cookie:nextjs=fast" header to be present on the incoming request
  // Getting cookies from the request using the `RequestCookies` API
  let cookie = request.cookies.get('nextjs')
  console.log(cookie) // => { name: 'nextjs', value: 'fast', Path: '/' }
  const allCookies = request.cookies.getAll()
  console.log(allCookies) // => [{ name: 'nextjs', value: 'fast' }]
 
  request.cookies.has('nextjs') // => true
  request.cookies.delete('nextjs')
  request.cookies.has('nextjs') // => false
 
  // Setting cookies on the response using the `ResponseCookies` API
  const response = NextResponse.next()
  response.cookies.set('vercel', 'fast')
  response.cookies.set({
    name: 'vercel',
    value: 'fast',
    path: '/',
  })
  cookie = response.cookies.get('vercel')
  console.log(cookie) // => { name: 'vercel', value: 'fast', Path: '/' }
  // The outgoing response will have a `Set-Cookie:vercel=fast;path=/` header.
 
  return response
}
```

### Setting Headers

You can set request and response headers using the NextResponse API (setting request headers is available since Next.js v13.0.0).

```typescript
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
 
export function middleware(request: NextRequest) {
  // Clone the request headers and set a new header `x-hello-from-middleware1`
  const requestHeaders = new Headers(request.headers)
  requestHeaders.set('x-hello-from-middleware1', 'hello')
 
  // You can also set request headers in NextResponse.next
  const response = NextResponse.next({
    request: {
      // New request headers
      headers: requestHeaders,
    },
  })
 
  // Set a new response header `x-hello-from-middleware2`
  response.headers.set('x-hello-from-middleware2', 'hello')
  return response
}
```

**Good to know**: Avoid setting large headers as it might cause 431 Request Header Fields Too Large error depending on your backend web server configuration.

### CORS

You can set CORS headers in Middleware to allow cross-origin requests, including simple and preflighted requests.

```typescript
import { NextRequest, NextResponse } from 'next/server'
 
const allowedOrigins = ['https://acme.com', 'https://my-app.org']
 
const corsOptions = {
  'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type, Authorization',
}
 
export function middleware(request: NextRequest) {
  // Check the origin from the request
  const origin = request.headers.get('origin') ?? ''
  const isAllowedOrigin = allowedOrigins.includes(origin)
 
  // Handle preflighted requests
  const isPreflight = request.method === 'OPTIONS'
 
  if (isPreflight) {
    const preflightHeaders = {
      ...(isAllowedOrigin && { 'Access-Control-Allow-Origin': origin }),
      ...corsOptions,
    }
    return NextResponse.json({}, { headers: preflightHeaders })
  }
 
  // Handle simple requests
  const response = NextResponse.next()
 
  if (isAllowedOrigin) {
    response.headers.set('Access-Control-Allow-Origin', origin)
  }
 
  Object.entries(corsOptions).forEach(([key, value]) => {
    response.headers.set(key, value)
  })
 
  return response
}
 
export const config = {
  matcher: '/api/:path*',
}
```

**Good to know**: You can configure CORS headers for individual routes in Route Handlers.

### Producing a Response

You can respond from Middleware directly by returning a `Response` or `NextResponse` instance. (This is available since Next.js v13.1.0)

```typescript
import type { NextRequest } from 'next/server'
import { isAuthenticated } from '@lib/auth'
 
// Limit the middleware to paths starting with `/api/`
export const config = {
  matcher: '/api/:function*',
}
 
export function middleware(request: NextRequest) {
  // Call our authentication function to check the request
  if (!isAuthenticated(request)) {
    // Respond with JSON indicating an error message
    return Response.json(
      { success: false, message: 'authentication failed' },
      { status: 401 }
    )
  }
}
```

### waitUntil and NextFetchEvent

The `NextFetchEvent` object extends the native `FetchEvent` object, and includes the `waitUntil()` method.

The `waitUntil()` method takes a promise as an argument, and extends the lifetime of the Middleware until the promise settles. This is useful for performing work in the background.

```javascript
import { NextResponse } from 'next/server'
import type { NextFetchEvent, NextRequest } from 'next/server'
 
export function middleware(req: NextRequest, event: NextFetchEvent) {
  event.waitUntil(
    fetch('https://my-analytics-platform.com', {
      method: 'POST',
      body: JSON.stringify({ pathname: req.nextUrl.pathname }),
    })
  )
 
  return NextResponse.next()
}
```

### Advanced Middleware Flags

In v13.1 of Next.js two additional flags were introduced for middleware, `skipMiddlewareUrlNormalize` and `skipTrailingSlashRedirect` to handle advanced use cases.

`skipTrailingSlashRedirect` disables Next.js redirects for adding or removing trailing slashes. This allows custom handling inside middleware to maintain the trailing slash for some paths but not others, which can make incremental migrations easier.

```javascript
// next.config.js
module.exports = {
  skipTrailingSlashRedirect: true,
}

// middleware.js
const legacyPrefixes = ['/docs', '/blog']
 
export default async function middleware(req) {
  const { pathname } = req.nextUrl
 
  if (legacyPrefixes.some((prefix) => pathname.startsWith(prefix))) {
    return NextResponse.next()
  }
 
  // apply trailing slash handling
  if (
    !pathname.endsWith('/') &&
    !pathname.match(/((?!\.well-known(?:\/.*)?)(?:[^/]+\/)*[^/]+\.\w+)/)
  ) {
    return NextResponse.redirect(
      new URL(`${req.nextUrl.pathname}/`, req.nextUrl)
    )
  }
}
```

`skipMiddlewareUrlNormalize` allows for disabling the URL normalization in Next.js to make handling direct visits and client-transitions the same. In some advanced cases, this option provides full control by using the original URL.

```javascript
// next.config.js
module.exports = {
  skipMiddlewareUrlNormalize: true,
}

// middleware.js
export default async function middleware(req) {
  const { pathname } = req.nextUrl
 
  // GET /_next/data/build-id/hello.json
 
  console.log(pathname)
  // with the flag this now /_next/data/build-id/hello.json
  // without the flag this would be normalized to /hello
}
```

### Runtime

Middleware currently only supports the Edge runtime. The Node.js runtime can not be used.

### Version History

| Version | Changes |
|---------|---------|
| v13.1.0 | Advanced Middleware flags added |
| v13.0.0 | Middleware can modify request headers, response headers, and send responses |
| v12.2.0 | Middleware is stable, please see the upgrade guide |
| v12.0.9 | Enforce absolute URLs in Edge Runtime ([PR](https://github.com/vercel/next.js/pull/36259)) |
| v12.0.0 | Middleware (Beta) added |


## Internationalization

Next.js enables you to configure the routing and rendering of content to support multiple languages. Making your site adaptive to different locales includes translated content (localization) and internationalized routes.

### Terminology

- **Locale**: An identifier for a set of language and formatting preferences. This usually includes the preferred language of the user and possibly their geographic region.
  - `en-US`: English as spoken in the United States
  - `nl-NL`: Dutch as spoken in the Netherlands
  - `nl`: Dutch, no specific region

### Routing Overview

It's recommended to use the user's language preferences in the browser to select which locale to use. Changing your preferred language will modify the incoming `Accept-Language` header to your application.

For example, using the following libraries, you can look at an incoming `Request` to determine which locale to select, based on the `Headers`, locales you plan to support, and the default locale.

```javascript
// middleware.js
import { match } from '@formatjs/intl-localematcher'
import Negotiator from 'negotiator'
 
let headers = { 'accept-language': 'en-US,en;q=0.5' }
let languages = new Negotiator({ headers }).languages()
let locales = ['en-US', 'nl-NL', 'nl']
let defaultLocale = 'en-US'
 
match(languages, locales, defaultLocale) // -> 'en-US'
```

Routing can be internationalized by either the sub-path (`/fr/products`) or domain (`my-site.fr/products`). With this information, you can now redirect the user based on the locale inside Middleware.

```javascript
// middleware.js
import { NextResponse } from "next/server";
 
let locales = ['en-US', 'nl-NL', 'nl']
 
// Get the preferred locale, similar to the above or using a library
function getLocale(request) { ... }
 
export function middleware(request) {
  // Check if there is any supported locale in the pathname
  const { pathname } = request.nextUrl
  const pathnameHasLocale = locales.some(
    (locale) => pathname.startsWith(`/${locale}/`) || pathname === `/${locale}`
  )
 
  if (pathnameHasLocale) return
 
  // Redirect if there is no locale
  const locale = getLocale(request)
  request.nextUrl.pathname = `/${locale}${pathname}`
  // e.g. incoming request is /products
  // The new URL is now /en-US/products
  return NextResponse.redirect(request.nextUrl)
}
 
export const config = {
  matcher: [
    // Skip all internal paths (_next)
    '/((?!_next).*)',
    // Optional: only run on root (/) URL
    // '/'
  ],
}
```

Finally, ensure all special files inside `app/` are nested under `app/[lang]`. This enables the Next.js router to dynamically handle different locales in the route, and forward the `lang` parameter to every layout and page. For example:

```javascript
// app/[lang]/page.js
// You now have access to the current locale
// e.g. /en-US/products -> `lang` is "en-US"
export default async function Page({ params: { lang } }) {
  return ...
}
```

The root layout can also be nested in the new folder (e.g. `app/[lang]/layout.js`).

### Localization

Changing displayed content based on the user's preferred locale, or localization, is not something specific to Next.js. The patterns described below would work the same with any web application.

Let's assume we want to support both English and Dutch content inside our application. We might maintain two different "dictionaries", which are objects that give us a mapping from some key to a localized string. For example:

```json
// dictionaries/en.json
{
  "products": {
    "cart": "Add to Cart"
  }
}

// dictionaries/nl.json
{
  "products": {
    "cart": "Toevoegen aan Winkelwagen"
  }
}
```

We can then create a `getDictionary` function to load the translations for the requested locale:

```javascript
// app/[lang]/dictionaries.js
import 'server-only'
 
const dictionaries = {
  en: () => import('./dictionaries/en.json').then((module) => module.default),
  nl: () => import('./dictionaries/nl.json').then((module) => module.default),
}
 
export const getDictionary = async (locale) => dictionaries[locale]()
```

Given the currently selected language, we can fetch the dictionary inside of a layout or page.

```javascript
// app/[lang]/page.js
import { getDictionary } from './dictionaries'
 
export default async function Page({ params: { lang } }) {
  const dict = await getDictionary(lang) // en
  return <button>{dict.products.cart}</button> // Add to Cart
}
```

Because all layouts and pages in the `app/` directory default to Server Components, we do not need to worry about the size of the translation files affecting our client-side JavaScript bundle size. This code will only run on the server, and only the resulting HTML will be sent to the browser.

### Static Generation

To generate static routes for a given set of locales, we can use `generateStaticParams` with any page or layout. This can be global, for example, in the root layout:

```javascript
// app/[lang]/layout.js
export async function generateStaticParams() {
  return [{ lang: 'en-US' }, { lang: 'de' }]
}
 
export default function Root({ children, params }) {
  return (
    <html lang={params.lang}>
      <body>{children}</body>
    </html>
  )
}
```

# Data Fetching

Data fetching is a core part of any application. This page walks through best practices for fetching data using your preferred method.

## Should I fetch data on the server or the client?

Deciding whether to fetch data on the server or the client depends on the type of UI you're building.

For most cases, where you don't need real-time data (e.g. polling), you can fetch data on the server with Server Components. There are a few benefits to doing this:

- You can fetch data in a single server-round trip, reducing the number of network requests and client-server waterfalls.
- Prevent sensitive information, such as access tokens and API keys, from being exposed to the client (which would require an intermediate API route).
- Reduce latency by fetching data close to the source (if your application code and database are in the same region).
- Data requests can be cached and revalidated.

However, server-side data fetching will cause the whole page to be re-rendered on the server. In cases where you need to mutate/revalidate smaller pieces of UI or continually fetch real-time data (e.g. live views), client-side data fetching might be better suited, as it'll allow you to re-render the specific piece of UI on the client.

## 4 ways to fetch data in Next.js

1. `fetch` API on the server.
2. ORMs or Database Clients on the server.
3. Route Handlers on the server, via the client.
4. Data Fetching Libraries on the client.

### fetch API

Next.js extends the native fetch Web API to allow you to configure the caching and revalidating behavior for each fetch request on the server. You can use `fetch` in Server Components, Route Handlers, and Server Actions. For example:

```typescript
// app/page.tsx
export default async function Page() {
  const data = await fetch('https://api.example.com/...').then((res) =>
    res.json()
  )
 
  return '...'
}
```

By default, fetch requests retrieve fresh data. Using it will cause a whole route to be dynamically rendered and data will not be cached.

You can cache fetch requests by setting the `cache` option to `force-cache`. This means data will be cached, and the component will be statically rendered:

```javascript
fetch('https://...', { cache: 'force-cache' })
```

Alternatively, if using PPR, we recommend wrapping components that use fetch requests in Suspense boundaries. This will ensure only the component that uses fetch is dynamically rendered and streamed in, rather than the entire page:

```jsx
import { Suspense } from 'react'

export default async function Cart() {
  const res = await fetch('https://api.example.com/...')
 
  return '...'
}
 
export default function Navigation() {
  return (
    <>
      <Suspense fallback={<LoadingIcon />}>
        <Cart />
      </Suspense>
    </>
  )
}
```

See the [caching and revalidating docs](https://nextjs.org/docs/app/building-your-application/data-fetching/caching) for more information.

> **Good to know**: In Next.js 14 and earlier, fetch requests were cached by default. See the upgrade guide for more information.

#### Request Memoization

If you need to fetch the same data in multiple components in a tree, you do not have to fetch data globally and pass props down. Instead, you can fetch data in the components that need the data without worrying about the performance implications of making multiple requests for the same data.

This is possible because fetch requests with the same URL and options are automatically memoized during a React render pass.

Learn more about [request memoization](https://nextjs.org/docs/app/building-your-application/data-fetching/caching#request-memoization).

### ORMs and Database Clients

You can call your ORM or database client in Server Components, Route Handlers, and Server Actions.

You can use React `cache` to memoize data requests during a React render pass. For example, although the `getItem` function is called in both layout and page, only one query will be made to the database:

```typescript
// app/utils.ts
import { cache } from 'react'
 
export const getItem = cache(async (id: string) => {
  const item = await db.item.findUnique({ id })
  return item
})

// app/item/[id]/layout.tsx
import { getItem } from '@/utils/get-item'
 
export default async function Layout({
  params: { id },
}: {
  params: { id: string }
}) {
  const item = await getItem(id)
  // ...
}

// app/item/[id]/page.tsx
import { getItem } from '@/utils/get-item'
 
export default async function Page({
  params: { id },
}: {
  params: { id: string }
}) {
  const item = await getItem(id)
  // ...
}
```

You can also configure the caching and revalidating behavior of these requests using the experimental `unstable_cache` and `unstable_noStore` APIs.

### Data Fetching Libraries

You can fetch data in Client Components using data fetching libraries such as SWR or React Query. These libraries provide their own APIs for caching, revalidating, and mutating data.

For example, using SWR to fetch data periodically on the client:

```jsx
"use client"
 
import useSWR from 'swr'
import fetcher from '@/utils/fetcher'
 
export default function PollingComponent() {
  // Polling interval set to 2000 milliseconds
  const { data } = useSWR('/api/data', fetcher, { refreshInterval: 2000 });
 
  return '...'
}
```

### Route Handlers

If you need to create API endpoints, Next.js supports Route Handlers. Route Handlers execute on the server and prevent sensitive information from being exposed to the client (e.g. API credentials).

For example, using SWR to call a Route Handler:

```typescript
// app/ui/message.tsx
'use client'
 
import useSWR from 'swr'
import fetcher from '@/utils/fetcher'
 
export default function Message() {
  const { data } = useSWR('/api/messages', fetcher)
 
  return '...'
}

// app/api/messages/route.ts
export async function GET() {
  const data = await fetch('https://...', {
    headers: {
      'Content-Type': 'application/json',
      'API-Key': process.env.DATA_API_KEY,
    },
  }).then((res) => res.json())
 
  return Response.json({ data })
}
```

See the [Route Handler docs](https://nextjs.org/docs/app/building-your-application/routing/route-handlers) for more examples.

> **Good to know**: Since Server Components render on the server, you don't need to call a Route Handler from a Server Component. You can access your data directly.

## Patterns

### Parallel and sequential data fetching

When fetching data inside components, you need to be aware of two data fetching patterns: Parallel and Sequential.

- **Sequential**: requests in a component tree are dependent on each other. This can lead to longer loading times.
- **Parallel**: requests in a route are eagerly initiated and will load data at the same time. This reduces the total time it takes to load data.

#### Sequential data fetching

If you have nested components, and each component fetches its own data, then data fetching will happen sequentially if those data requests are not memoized.

There may be cases where you want this pattern because one fetch depends on the result of the other. For example, the Playlists component will only start fetching data once the Artist component has finished fetching data because Playlists depends on the `artistID` prop:

```typescript
// app/artist/[username]/page.tsx
export default async function Page({
  params: { username },
}: {
  params: { username: string }
}) {
  // Get artist information
  const artist = await getArtist(username)
 
  return (
    <>
      <h1>{artist.name}</h1>
      {/* Show fallback UI while the Playlists component is loading */}
      <Suspense fallback={<div>Loading...</div>}>
        {/* Pass the artist ID to the Playlists component */}
        <Playlists artistID={artist.id} />
      </Suspense>
    </>
  )
}
 
async function Playlists({ artistID }: { artistID: string }) {
  // Use the artist ID to fetch playlists
  const playlists = await getArtistPlaylists(artistID)
 
  return (
    <ul>
      {playlists.map((playlist) => (
        <li key={playlist.id}>{playlist.name}</li>
      ))}
    </ul>
  )
}
```

You can use `loading.js` (for route segments) or React `<Suspense>` (for nested components) to show an instant loading state while React streams in the result.

This will prevent the whole route from being blocked by data requests, and the user will be able to interact with the parts of the page that are ready.

#### Parallel Data Fetching

By default, layout and page segments are rendered in parallel. This means requests will be initiated in parallel.

However, due to the nature of async/await, an awaited request inside the same segment or component will block any requests below it.

To fetch data in parallel, you can eagerly initiate requests by defining them outside the components that use the data. This saves time by initiating both requests in parallel, however, the user won't see the rendered result until both promises are resolved.

In the example below, the `getArtist` and `getAlbums` functions are defined outside the Page component and initiated inside the component using `Promise.all`:

```typescript
// app/artist/[username]/page.tsx
import Albums from './albums'
 
async function getArtist(username: string) {
  const res = await fetch(`https://api.example.com/artist/${username}`)
  return res.json()
}
 
async function getAlbums(username: string) {
  const res = await fetch(`https://api.example.com/artist/${username}/albums`)
  return res.json()
}
 
export default async function Page({
  params: { username },
}: {
  params: { username: string }
}) {
  const artistData = getArtist(username)
  const albumsData = getAlbums(username)
 
  // Initiate both requests in parallel
  const [artist, albums] = await Promise.all([artistData, albumsData])
 
  return (
    <>
      <h1>{artist.name}</h1>
      <Albums list={albums} />
    </>
  )
}
```

In addition, you can add a Suspense Boundary to break up the rendering work and show part of the result as soon as possible.

### Preloading Data

Another way to prevent waterfalls is to use the preload pattern by creating an utility function that you eagerly call above blocking requests. For example, `checkIsAvailable()` blocks `<Item/>` from rendering, so you can call `preload()` before it to eagerly initiate `<Item/>` data dependencies. By the time `<Item/>` is rendered, its data has already been fetched.

Note that preload function doesn't block `checkIsAvailable()` from running.

```typescript
import { getItem } from '@/utils/get-item'
 
export const preload = (id: string) => {
  // void evaluates the given expression and returns undefined
  // https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void
  void getItem(id)
}
export default async function Item({ id }: { id: string }) {
  const result = await getItem(id)
  // ...
}

import Item, { preload, checkIsAvailable } from '@/components/Item'
 
export default async function Page({
  params: { id },
}: {
  params: { id: string }
}) {
  // starting loading item data
  preload(id)
  // perform another asynchronous task
  const isAvailable = await checkIsAvailable()
 
  return isAvailable ? <Item id={id} /> : null
}
```

> **Good to know**: The "preload" function can also have any name as it's a pattern, not an API.

#### Using React cache and server-only with the Preload Pattern

You can combine the `cache` function, the preload pattern, and the `server-only` package to create a data fetching utility that can be used throughout your app.

```typescript
// utils/get-item.ts
import { cache } from 'react'
import 'server-only'
 
export const preload = (id: string) => {
  void getItem(id)
}
 
export const getItem = cache(async (id: string) => {
  // ...
})
```

With this approach, you can eagerly fetch data, cache responses, and guarantee that this data fetching only happens on the server.

The `utils/get-item` exports can be used by Layouts, Pages, or other components to give them control over when an item's data is fetched.

> **Good to know**: We recommend using the `server-only` package to make sure server data fetching functions are never used on the client.

## Preventing sensitive data from being exposed to the client

We recommend using React's taint APIs, `taintObjectReference` and `taintUniqueValue`, to prevent whole object instances or sensitive values from being passed to the client.

To enable tainting in your application, set the Next.js Config experimental.taint option to true:

```javascript
// next.config.js
module.exports = {
  experimental: {
    taint: true,
  },
}
```

Then pass the object or value you want to taint to the `experimental_taintObjectReference` or `experimental_taintUniqueValue` functions:

```typescript
// app/utils.ts
import { queryDataFromDB } from './api'
import {
  experimental_taintObjectReference,
  experimental_taintUniqueValue,
} from 'react'
 
export async function getUserData() {
  const data = await queryDataFromDB()
  experimental_taintObjectReference(
    'Do not pass the whole user object to the client',
    data
  )
  experimental_taintUniqueValue(
    "Do not pass the user's address to the client",
    data,
    data.address
  )
  return data
}

// app/page.tsx
import { getUserData } from './data'
 
export async function Page() {
  const userData = getUserData()
  return (
    <ClientComponent
      user={userData} // this will cause an error because of taintObjectReference
      address={userData.address} // this will cause an error because of taintUniqueValue
    />
  )
}
```

## Caching and Revalidating

### Caching

Caching is the process of storing data to reduce the number of requests made to the server. Next.js provides a built-in Data Cache for individual data requests, giving you granular control of caching behavior.

#### fetch requests

`fetch` requests are not cached by default in Next.js 15.

To cache an individual fetch request, you can use the `cache: 'force-cache'` option:

```javascript
fetch('https://...', { cache: 'force-cache' })
```

#### Data fetching libraries and ORMs

To cache specific requests to your database or ORM, you can use the `unstable_cache` API:

```javascript
import { getUser } from './data'
import { unstable_cache } from 'next/cache'

const getCachedUser = unstable_cache(async (id) => getUser(id), ['my-app-user'])

export default async function Component({ userID }) {
  const user = await getCachedUser(userID)
  return user
}
```

### Revalidating data

Revalidation is the process of purging the Data Cache and re-fetching the latest data. This is useful when your data changes and you want to ensure you show the latest information while still benefiting from the speed of static rendering.

Cached data can be revalidated in two ways:

1. **Time-based revalidation**: Automatically revalidate data after a certain amount of time has passed. This is useful for data that changes infrequently and freshness is not as critical.
2. **On-demand revalidation**: Manually revalidate data based on an event (e.g. form submission). On-demand revalidation can use a tag-based or path-based approach to revalidate groups of data at once. This is useful when you want to ensure the latest data is shown as soon as possible (e.g. when content from your headless CMS is updated).

#### Time-based revalidation

To revalidate data at a timed interval, you can use the `next.revalidate` option of `fetch` to set the cache lifetime of a resource (in seconds).

```javascript
fetch('https://...', { next: { revalidate: 3600 } }) // revalidate at most every hour
```

Alternatively, to revalidate all requests in a route segment, you can use the Segment Config Options.

```javascript
// layout.js | page.js
export const revalidate = 3600 // revalidate at most every hour
```

Learn how time-based revalidation works

> **Good to know:**
> 
> - If you have multiple fetch requests in a statically rendered route, and each has a different revalidation frequency. The lowest time will be used for all requests.
> - For dynamically rendered routes, each fetch request will be revalidated independently.
> - To save server resources, we recommend setting a high revalidation time whenever possible. For instance, 1 hour instead of 1 second. If you need real-time data, consider switching to dynamic rendering or client-side data fetching.

#### On-demand revalidation

Data can be revalidated on-demand with the `revalidatePath` and `revalidateTag` APIs.

Use `revalidatePath` in Server Actions or Route Handler to revalidate data for specific routes:

```javascript
import { revalidatePath } from 'next/cache'

export async function createPost() {
  // Mutate data
  revalidatePath('/posts')
}
```

Use `revalidateTag` to revalidate fetch requests across routes.

- When using `fetch`, you have the option to tag cache entries with one or more tags.
- Then, you can call `revalidateTag` to revalidate all entries associated with that tag.

For example, the following fetch request adds the cache tag `collection`:

```typescript
// app/page.tsx
export default async function Page() {
  const res = await fetch('https://...', { next: { tags: ['collection'] } })
  const data = await res.json()
  // ...
}
```

You can then revalidate this fetch call tagged with `collection` by calling `revalidateTag`:

```typescript
// @/app/actions.ts
'use server'

import { revalidateTag } from 'next/cache'

export async function action() {
  revalidateTag('collection')
}
```

Learn how on-demand revalidation works.

### Error handling and revalidation

If an error is thrown while attempting to revalidate data, the last successfully generated data will continue to be served from the cache. On the next subsequent request, Next.js will retry revalidating the data.

## Server Actions and Mutations

Server Actions are asynchronous functions that are executed on the server. They can be called in Server and Client Components to handle form submissions and data mutations in Next.js applications.

### Convention

A Server Action can be defined with the React `"use server"` directive. You can place the directive at the top of an async function to mark the function as a Server Action, or at the top of a separate file to mark all exports of that file as Server Actions.

### Server Components

Server Components can use the inline function level or module level `"use server"` directive. To inline a Server Action, add `"use server"` to the top of the function body:

```typescript
// app/page.tsx
export default function Page() {
  // Server Action
  async function create() {
    'use server'
    // Mutate data
  }
 
  return '...'
}
```

### Client Components

To call a Server Action in a Client Component, create a new file and add the `"use server"` directive at the top of it. All functions within the file will be marked as Server Actions that can be reused in both Client and Server Components:

```typescript
// app/actions.ts
'use server'
 
export async function create() {}
```

```typescript
// app/ui/button.tsx
'use client'
 
import { create } from '@/app/actions'
 
export function Button() {
  return <Button onClick={create} />
}
```

### Passing actions as props

You can also pass a Server Action to a Client Component as a prop:

```typescript
<ClientComponent updateItemAction={updateItem} />
```

```typescript
// app/client-component.tsx
'use client'
 
export default function ClientComponent({
  updateItemAction,
}: {
  updateItemAction: (formData: FormData) => void
}) {
  return <form action={updateItemAction}>{/* ... */}</form>
}
```

Usually, the Next.js TypeScript plugin would flag `updateItemAction` in client-component.tsx since it is a function which generally can't be serialized across client-server boundaries. However, props named `action` or ending with `Action` are assumed to receive Server Actions. This is only a heuristic since the TypeScript plugin doesn't actually know if it receives a Server Action or an ordinary function. Runtime type-checking will still ensure you don't accidentally pass a function to a Client Component.

### Behavior

Server actions can be invoked using the `action` attribute in a `<form>` element:

- Server Components support progressive enhancement by default, meaning the form will be submitted even if JavaScript hasn't loaded yet or is disabled.
- In Client Components, forms invoking Server Actions will queue submissions if JavaScript isn't loaded yet, prioritizing client hydration.
- After hydration, the browser does not refresh on form submission.
- Server Actions are not limited to `<form>` and can be invoked from event handlers, `useEffect`, third-party libraries, and other form elements like `<button>`.
- Server Actions integrate with the Next.js caching and revalidation architecture. When an action is invoked, Next.js can return both the updated UI and new data in a single server roundtrip.
- Behind the scenes, actions use the POST method, and only this HTTP method can invoke them.
- The arguments and return value of Server Actions must be serializable by React. See the [React docs](https://react.dev/reference/react/use-server#serializable-parameters-and-return-values) for a list of serializable arguments and values.
- Server Actions are functions. This means they can be reused anywhere in your application.
- Server Actions inherit the runtime from the page or layout they are used on.
- Server Actions inherit the Route Segment Config from the page or layout they are used on, including fields like `maxDuration`.

### Examples

#### Forms

React extends the HTML `<form>` element to allow Server Actions to be invoked with the `action` prop.

When invoked in a form, the action automatically receives the `FormData` object. You don't need to use React `useState` to manage fields, instead, you can extract the data using the native `FormData` methods:

```typescript
// app/invoices/page.tsx
export default function Page() {
  async function createInvoice(formData: FormData) {
    'use server'
 
    const rawFormData = {
      customerId: formData.get('customerId'),
      amount: formData.get('amount'),
      status: formData.get('status'),
    }
 
    // mutate data
    // revalidate cache
  }
 
  return <form action={createInvoice}>...</form>
}
```

**Good to know:**

- [Example: Form with Loading & Error States](https://github.com/vercel/next.js/tree/canary/examples/next-forms)
- When working with forms that have many fields, you may want to consider using the `entries()` method with JavaScript's `Object.fromEntries()`. For example: `const rawFormData = Object.fromEntries(formData)`. One thing to note is that the formData will include additional `$ACTION_` properties.
- See [React `<form>` documentation](https://react.dev/reference/react-dom/components/form) to learn more.

#### Passing additional arguments

You can pass additional arguments to a Server Action using the JavaScript `bind` method.

```typescript
// app/client-component.tsx
'use client'
 
import { updateUser } from './actions'
 
export function UserProfile({ userId }: { userId: string }) {
  const updateUserWithId = updateUser.bind(null, userId)
 
  return (
    <form action={updateUserWithId}>
      <input type="text" name="name" />
      <button type="submit">Update User Name</button>
    </form>
  )
}
```

The Server Action will receive the `userId` argument, in addition to the form data:

```typescript
// app/actions.js
'use server'
 
export async function updateUser(userId, formData) {}
```

**Good to know:**

- An alternative is to pass arguments as hidden input fields in the form (e.g. `<input type="hidden" name="userId" value={userId} />`). However, the value will be part of the rendered HTML and will not be encoded.
- `.bind` works in both Server and Client Components. It also supports progressive enhancement.

#### Nested form elements

You can also invoke a Server Action in elements nested inside `<form>` such as `<button>`, `<input type="submit">`, and `<input type="image">`. These elements accept the `formAction` prop or event handlers.

This is useful in cases where you want to call multiple server actions within a form. For example, you can create a specific `<button>` element for saving a post draft in addition to publishing it. See the [React `<form>` docs](https://react.dev/reference/react-dom/components/form) for more information.

#### Programmatic form submission

You can trigger a form submission programmatically using the `requestSubmit()` method. For example, when the user submits a form using the ⌘ + Enter keyboard shortcut, you can listen for the `onKeyDown` event:

```typescript
// app/entry.tsx
'use client'
 
export function Entry() {
  const handleKeyDown = (e: React.KeyboardEvent<HTMLTextAreaElement>) => {
    if (
      (e.ctrlKey || e.metaKey) &&
      (e.key === 'Enter' || e.key === 'NumpadEnter')
    ) {
      e.preventDefault()
      e.currentTarget.form?.requestSubmit()
    }
  }
 
  return (
    <div>
      <textarea name="entry" rows={20} required onKeyDown={handleKeyDown} />
    </div>
  )
}
```

This will trigger the submission of the nearest `<form>` ancestor, which will invoke the Server Action.

#### Server-side form validation

You can use the HTML attributes like `required` and `type="email"` for basic client-side form validation.

For more advanced server-side validation, you can use a library like `zod` to validate the form fields before mutating the data:

```typescript
// app/actions.ts
'use server'
 
import { z } from 'zod'
 
const schema = z.object({
  email: z.string({
    invalid_type_error: 'Invalid Email',
  }),
})
 
export default async function createUser(formData: FormData) {
  const validatedFields = schema.safeParse({
    email: formData.get('email'),
  })
 
  // Return early if the form data is invalid
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
    }
  }
 
  // Mutate data
}
```

Once the fields have been validated on the server, you can return a serializable object in your action and use the React `useActionState` hook to show a message to the user.

By passing the action to `useActionState`, the action's function signature changes to receive a new `prevState` or `initialState` parameter as its first argument.

`useActionState` is a React hook and therefore must be used in a Client Component.

**Good to know:** These examples use React's `useActionState` hook, which is available in React 19 RC. If you are using an earlier version of React, use `useFormState` instead. See the [React docs](https://react.dev/reference/react-dom/hooks/useFormState) for more information.

```typescript
// app/actions.ts
'use server'
 
import { redirect } from 'next/navigation'
 
export async function createUser(prevState: any, formData: FormData) {
  const res = await fetch('https://...')
  const json = await res.json()
 
  if (!res.ok) {
    return { message: 'Please enter a valid email' }
  }
 
  redirect('/dashboard')
}
```

Then, you can pass your action to the `useActionState` hook and use the returned state to display an error message.

```typescript
// app/ui/signup.tsx
'use client'
 
import { useActionState } from 'react'
import { createUser } from '@/app/actions'
 
const initialState = {
  message: '',
}
 
export function Signup() {
  const [state, formAction] = useActionState(createUser, initialState)
 
  return (
    <form action={formAction}>
      <label htmlFor="email">Email</label>
      <input type="text" id="email" name="email" required />
      {/* ... */}
      <p aria-live="polite">{state?.message}</p>
      <button>Sign up</button>
    </form>
  )
}
```

**Good to know:**

- Before mutating data, you should always ensure a user is also authorized to perform the action. See [Authentication and Authorization](https://nextjs.org/docs/app/building-your-application/authentication).

#### Pending states

The `useActionState` hook exposes a pending state that can be used to show a loading indicator while the action is being executed.

```typescript
// app/submit-button.tsx
'use client'
 
import { useActionState } from 'react'
import { createUser } from '@/app/actions'
 
const initialState = {
  message: '',
}
 
export function Signup() {
  const [state, formAction, pending] = useActionState(createUser, initialState)
 
  return (
    <form action={formAction}>
      <label htmlFor="email">Email</label>
      <input type="text" id="email" name="email" required />
      {/* ... */}
      <p aria-live="polite" className="sr-only">
        {state?.message}
      </p>
      <button aria-disabled={pending} type="submit">
        {pending ? 'Submitting...' : 'Sign up'}
      </button>
    </form>
  )
}
```

**Good to know:** Alternatively, you can also use the `useFormStatus` hook to show a pending state for a specific form.

#### Optimistic updates

You can use the React `useOptimistic` hook to optimistically update the UI before the Server Action finishes executing, rather than waiting for the response:

```typescript
// app/page.tsx
'use client'
 
import { useOptimistic } from 'react'
import { send } from './actions'
 
type Message = {
  message: string
}
 
export function Thread({ messages }: { messages: Message[] }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic<
    Message[],
    string
  >(messages, (state, newMessage) => [...state, { message: newMessage }])
 
  const formAction = async (formData) => {
    const message = formData.get('message') as string
    addOptimisticMessage(message)
    await send(message)
  }
 
  return (
    <div>
      {optimisticMessages.map((m, i) => (
        <div key={i}>{m.message}</div>
      ))}
      <form action={formAction}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </div>
  )
}
```

#### Event handlers

While it's common to use Server Actions within `<form>` elements, they can also be invoked with event handlers such as `onClick`. For example, to increment a like count:

```typescript
// app/like-button.tsx
'use client'
 
import { incrementLike } from './actions'
import { useState } from 'react'
 
export default function LikeButton({ initialLikes }: { initialLikes: number }) {
  const [likes, setLikes] = useState(initialLikes)
 
  return (
    <>
      <p>Total Likes: {likes}</p>
      <button
        onClick={async () => {
          const updatedLikes = await incrementLike()
          setLikes(updatedLikes)
        }}
      >
        Like
      </button>
    </>
  )
}
```

You can also add event handlers to form elements, for example, to save a form field `onChange`:

```typescript
// app/ui/edit-post.tsx
'use client'
 
import { publishPost, saveDraft } from './actions'
 
export default function EditPost() {
  return (
    <form action={publishPost}>
      <textarea
        name="content"
        onChange={async (e) => {
          await saveDraft(e.target.value)
        }}
      />
      <button type="submit">Publish</button>
    </form>
  )
}
```

For cases like this, where multiple events might be fired in quick succession, we recommend debouncing to prevent unnecessary Server Action invocations.

#### useEffect

You can use the React `useEffect` hook to invoke a Server Action when the component mounts or a dependency changes. This is useful for mutations that depend on global events or need to be triggered automatically. For example, `onKeyDown` for app shortcuts, an intersection observer hook for infinite scrolling, or when the component mounts to update a view count:

```typescript
// app/view-count.tsx
'use client'
 
import { incrementViews } from './actions'
import { useState, useEffect } from 'react'
 
export default function ViewCount({ initialViews }: { initialViews: number }) {
  const [views, setViews] = useState(initialViews)
 
  useEffect(() => {
    const updateViews = async () => {
      const updatedViews = await incrementViews()
      setViews(updatedViews)
    }
 
    updateViews()
  }, [])
 
  return <p>Total Views: {views}</p>
}
```

Remember to consider the behavior and caveats of `useEffect`.

### Error Handling

When an error is thrown, it'll be caught by the nearest `error.js` or `<Suspense>` boundary on the client. We recommend using `try/catch` to return errors to be handled by your UI.

For example, your Server Action might handle errors from creating a new item by returning a message:

```typescript
// app/actions.ts
'use server'
 
export async function createTodo(prevState: any, formData: FormData) {
  try {
    // Mutate data
  } catch (e) {
    throw new Error('Failed to create task')
  }
}
```

**Good to know:**

- Aside from throwing the error, you can also return an object to be handled by `useActionState`. See [Server-side validation and error handling](#server-side-form-validation).

### Revalidating data

You can revalidate the Next.js Cache inside your Server Actions with the `revalidatePath` API:

```typescript
// app/actions.ts
'use server'
 
import { revalidatePath } from 'next/cache'
 
export async function createPost() {
  try {
    // ...
  } catch (error) {
    // ...
  }
 
  revalidatePath('/posts')
}
```

Or invalidate a specific data fetch with a cache tag using `revalidateTag`:

```typescript
// app/actions.ts
'use server'
 
import { revalidateTag } from 'next/cache'
 
export async function createPost() {
  try {
    // ...
  } catch (error) {
    // ...
  }
 
  revalidateTag('posts')
}
```

### Redirecting

If you would like to redirect the user to a different route after the completion of a Server Action, you can use `redirect` API. `redirect` needs to be called outside of the `try/catch` block:

```typescript
// app/actions.ts
'use server'
 
import { redirect } from 'next/navigation'
import { revalidateTag } from 'next/cache'
 
export async function createPost(id: string) {
  try {
    // ...
  } catch (error) {
    // ...
  }
 
  revalidateTag('posts') // Update cached posts
  redirect(`/post/${id}`) // Navigate to the new post page
}
```

### Cookies

You can get, set, and delete cookies inside a Server Action using the `cookies` API:

```typescript
// app/actions.ts
'use server'
 
import { cookies } from 'next/headers'
 
export async function exampleAction() {
  // Get cookie
  const value = cookies().get('name')?.value
 
  // Set cookie
  cookies().set('name', 'Delba')
 
  // Delete cookie
  cookies().delete('name')
}
```

See additional examples for [deleting cookies from Server Actions](https://nextjs.org/docs/app/api-reference/functions/cookies#deleting-cookies).

### Security

#### Authentication and authorization

You should treat Server Actions as you would public-facing API endpoints, and ensure that the user is authorized to perform the action. For example:

```typescript
// app/actions.ts
'use server'
 
import { auth } from './lib'
 
export function addItem() {
  const { user } = auth()
  if (!user) {
    throw new Error('You must be signed in to perform this action')
  }
 
  // ...
}
```

#### Closures and encryption

Defining a Server Action inside a component creates a closure where the action has access to the outer function's scope. For example, the `publish` action has access to the `publishVersion` variable:

```typescript
// app/page.tsx
export default async function Page() {
  const publishVersion = await getLatestVersion();
 
  async function publish() {
    "use server";
    if (publishVersion !== await getLatestVersion()) {
      throw new Error('The version has changed since pressing publish');
    }
    ...
  }
 
  return (
    <form>
      <button formAction={publish}>Publish</button>
    </form>
  );
}
```

Closures are useful when you need to capture a snapshot of data (e.g. `publishVersion`) at the time of rendering so that it can be used later when the action is invoked.

However, for this to happen, the captured variables are sent to the client and back to the server when the action is invoked. To prevent sensitive data from being exposed to the client, Next.js automatically encrypts the closed-over variables. A new private key is generated for each action every time a Next.js application is built. This means actions can only be invoked for a specific build.

**Good to know:** We don't recommend relying on encryption alone to prevent sensitive values from being exposed on the client. Instead, you should use the React taint APIs to proactively prevent specific data from being sent to the client.

#### Overwriting encryption keys (advanced)

When self-hosting your Next.js application across multiple servers, each server instance may end up with a different encryption key, leading to potential inconsistencies.

To mitigate this, you can overwrite the encryption key using the `process.env.NEXT_SERVER_ACTIONS_ENCRYPTION_KEY` environment variable. Specifying this variable ensures that your encryption keys are persistent across builds, and all server instances use the same key.

This is an advanced use case where consistent encryption behavior across multiple deployments is critical for your application. You should consider standard security practices such key rotation and signing.

**Good to know:** Next.js applications deployed to Vercel automatically handle this.

#### Allowed origins (advanced)

Since Server Actions can be invoked in a `<form>` element, this opens them up to CSRF attacks.

Behind the scenes, Server Actions use the POST method, and only this HTTP method is allowed to invoke them. This prevents most CSRF vulnerabilities in modern browsers, particularly with SameSite cookies being the default.

As an additional protection, Server Actions in Next.js also compare the `Origin` header to the `Host` header (or `X-Forwarded-Host`). If these don't match, the request will be aborted. In other words, Server Actions can only be invoked on the same host as the page that hosts it.

For large applications that use reverse proxies or multi-layered backend architectures (where the server API differs from the production domain), it's recommended to use the configuration option `serverActions.allowedOrigins` option to specify a list of safe origins. The option accepts an array of strings.

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
module.exports = {
  experimental: {
    serverActions: {
      allowedOrigins: ['my-proxy.com', '*.my-proxy.com'],
    },
  },
}
```

# Rendering

Rendering converts the code you write into user interfaces. React and Next.js allow you to create hybrid web applications where parts of your code can be rendered on the server or the client. This section will help you understand the differences between these rendering environments, strategies, and runtimes.

## Fundamentals

To start, it's helpful to be familiar with three foundational web concepts:

1. The **Environments** your application code can be executed in: the server and the client.
2. The **Request-Response Lifecycle** that's initiated when a user visits or interacts with your application.
3. The **Network Boundary** that separates server and client code.

## Rendering Environments

There are two environments where web applications can be rendered: the client and the server.

### Client and Server Environments

- The **client** refers to the browser on a user's device that sends a request to a server for your application code. It then turns the response from the server into a user interface.
- The **server** refers to the computer in a data center that stores your application code, receives requests from a client, and sends back an appropriate response.

Historically, developers had to use different languages (e.g. JavaScript, PHP) and frameworks when writing code for the server and the client. With React, developers can use the same language (JavaScript), and the same framework (e.g. Next.js or your framework of choice). This flexibility allows you to seamlessly write code for both environments without context switching.

However, each environment has its own set of capabilities and constraints. Therefore, the code you write for the server and the client is not always the same. There are certain operations (e.g. data fetching or managing user state) that are better suited for one environment over the other.

Understanding these differences is key to effectively using React and Next.js. We'll cover the differences and use cases in more detail on the Server and Client Components pages, for now, let's continue building on our foundation.

## Request-Response Lifecycle

Broadly speaking, all websites follow the same Request-Response Lifecycle:

1. **User Action**: The user interacts with a web application. This could be clicking a link, submitting a form, or typing a URL directly into the browser's address bar.
2. **HTTP Request**: The client sends an HTTP request to the server that contains necessary information about what resources are being requested, what method is being used (e.g. GET, POST), and additional data if necessary.
3. **Server**: The server processes the request and responds with the appropriate resources. This process may take a couple of steps like routing, fetching data, etc.
4. **HTTP Response**: After processing the request, the server sends an HTTP response back to the client. This response contains a status code (which tells the client whether the request was successful or not) and requested resources (e.g. HTML, CSS, JavaScript, static assets, etc).
5. **Client**: The client parses the resources to render the user interface.
6. **User Action**: Once the user interface is rendered, the user can interact with it, and the whole process starts again.

A major part of building a hybrid web application is deciding how to split the work in the lifecycle, and where to place the Network Boundary.

## Network Boundary

In web development, the Network Boundary is a conceptual line that separates the different environments. For example, the client and the server, or the server and the data store.

In React, you choose where to place the client-server network boundary wherever it makes the most sense.

Behind the scenes, the work is split into two parts: the client module graph and the server module graph. The server module graph contains all the components that are rendered on the server, and the client module graph contains all components that are rendered on the client.

It may be helpful to think about module graphs as a visual representation of how files in your application depend on each other.

You can use the React `"use client"` convention to define the boundary. There's also a `"use server"` convention, which tells React to do some computational work on the server.

## Building Hybrid Applications

When working in these environments, it's helpful to think of the flow of the code in your application as unidirectional. In other words, during a response, your application code flows in one direction: from the server to the client.

If you need to access the server from the client, you send a new request to the server rather than re-use the same request. This makes it easier to understand where to render your components and where to place the Network Boundary.

In practice, this model encourages developers to think about what they want to execute on the server first, before sending the result to the client and making the application interactive.

## Server Components

React Server Components allow you to write UI that can be rendered and optionally cached on the server. In Next.js, the rendering work is further split by route segments to enable streaming and partial rendering, and there are three different server rendering strategies:

- Static Rendering
- Dynamic Rendering
- Streaming

This page will go through how Server Components work, when you might use them, and the different server rendering strategies.

### Benefits of Server Rendering

There are a couple of benefits to doing the rendering work on the server, including:

1. **Data Fetching**: Server Components allow you to move data fetching to the server, closer to your data source. This can improve performance by reducing time it takes to fetch data needed for rendering, and the number of requests the client needs to make.

2. **Security**: Server Components allow you to keep sensitive data and logic on the server, such as tokens and API keys, without the risk of exposing them to the client.

3. **Caching**: By rendering on the server, the result can be cached and reused on subsequent requests and across users. This can improve performance and reduce cost by reducing the amount of rendering and data fetching done on each request.

4. **Performance**: Server Components give you additional tools to optimize performance from the baseline. For example, if you start with an app composed of entirely Client Components, moving non-interactive pieces of your UI to Server Components can reduce the amount of client-side JavaScript needed. This is beneficial for users with slower internet or less powerful devices, as the browser has less client-side JavaScript to download, parse, and execute.

5. **Initial Page Load and First Contentful Paint (FCP)**: On the server, we can generate HTML to allow users to view the page immediately, without waiting for the client to download, parse and execute the JavaScript needed to render the page.

6. **Search Engine Optimization and Social Network Shareability**: The rendered HTML can be used by search engine bots to index your pages and social network bots to generate social card previews for your pages.

7. **Streaming**: Server Components allow you to split the rendering work into chunks and stream them to the client as they become ready. This allows the user to see parts of the page earlier without having to wait for the entire page to be rendered on the server.

### Using Server Components in Next.js

By default, Next.js uses Server Components. This allows you to automatically implement server rendering with no additional configuration, and you can opt into using Client Components when needed, see Client Components.

### How are Server Components rendered?

On the server, Next.js uses React's APIs to orchestrate rendering. The rendering work is split into chunks: by individual route segments and Suspense Boundaries.

Each chunk is rendered in two steps:

1. React renders Server Components into a special data format called the React Server Component Payload (RSC Payload).
2. Next.js uses the RSC Payload and Client Component JavaScript instructions to render HTML on the server.

Then, on the client:

1. The HTML is used to immediately show a fast non-interactive preview of the route - this is for the initial page load only.
2. The React Server Components Payload is used to reconcile the Client and Server Component trees, and update the DOM.
3. The JavaScript instructions are used to hydrate Client Components and make the application interactive.

### What is the React Server Component Payload (RSC)?

The RSC Payload is a compact binary representation of the rendered React Server Components tree. It's used by React on the client to update the browser's DOM. The RSC Payload contains:

- The rendered result of Server Components
- Placeholders for where Client Components should be rendered and references to their JavaScript files
- Any props passed from a Server Component to a Client Component

### Server Rendering Strategies

There are three subsets of server rendering: Static, Dynamic, and Streaming.

#### Static Rendering (Default)

With Static Rendering, routes are rendered at build time, or in the background after data revalidation. The result is cached and can be pushed to a Content Delivery Network (CDN). This optimization allows you to share the result of the rendering work between users and server requests.

Static rendering is useful when a route has data that is not personalized to the user and can be known at build time, such as a static blog post or a product page.

#### Dynamic Rendering

With Dynamic Rendering, routes are rendered for each user at request time.

Dynamic rendering is useful when a route has data that is personalized to the user or has information that can only be known at request time, such as cookies or the URL's search params.

##### Dynamic Routes with Cached Data

In most websites, routes are not fully static or fully dynamic - it's a spectrum. For example, you can have an e-commerce page that uses cached product data that's revalidated at an interval, but also has uncached, personalized customer data.

In Next.js, you can have dynamically rendered routes that have both cached and uncached data. This is because the RSC Payload and data are cached separately. This allows you to opt into dynamic rendering without worrying about the performance impact of fetching all the data at request time.

Learn more about the full-route cache and Data Cache.

##### Switching to Dynamic Rendering

During rendering, if a dynamic function or uncached data request is discovered, Next.js will switch to dynamically rendering the whole route. This table summarizes how dynamic functions and data caching affect whether a route is statically or dynamically rendered:

| Dynamic Functions | Data       | Route                 |
|-------------------|------------|----------------------|
| No                | Cached     | Statically Rendered  |
| Yes               | Cached     | Dynamically Rendered |
| No                | Not Cached | Dynamically Rendered |
| Yes               | Not Cached | Dynamically Rendered |

In the table above, for a route to be fully static, all data must be cached. However, you can have a dynamically rendered route that uses both cached and uncached data fetches.

As a developer, you do not need to choose between static and dynamic rendering as Next.js will automatically choose the best rendering strategy for each route based on the features and APIs used. Instead, you choose when to cache or revalidate specific data, and you may choose to stream parts of your UI.

##### Dynamic Functions

Dynamic functions rely on information that can only be known at request time such as a user's cookies, current requests headers, or the URL's search params. In Next.js, these dynamic APIs are:

- `cookies()`
- `headers()`
- `unstable_noStore()`
- `unstable_after()`
- `searchParams` prop

Using any of these functions will opt the whole route into dynamic rendering at request time.

#### Streaming

Streaming enables you to progressively render UI from the server. Work is split into chunks and streamed to the client as it becomes ready. This allows the user to see parts of the page immediately, before the entire content has finished rendering.

Streaming is built into the Next.js App Router by default. This helps improve both the initial page loading performance, as well as UI that depends on slower data fetches that would block rendering the whole route. For example, reviews on a product page.

You can start streaming route segments using `loading.js` and UI components with React Suspense. See the Loading UI and Streaming section for more information.

## Client Components

Client Components allow you to write interactive UI that is prerendered on the server and can use client JavaScript to run in the browser.

This page will go through how Client Components work, how they're rendered, and when you might use them.

### Benefits of Client Rendering

There are a couple of benefits to doing the rendering work on the client, including:

- **Interactivity**: Client Components can use state, effects, and event listeners, meaning they can provide immediate feedback to the user and update the UI.
- **Browser APIs**: Client Components have access to browser APIs, like geolocation or localStorage.

### Using Client Components in Next.js

To use Client Components, you can add the React `"use client"` directive at the top of a file, above your imports.

`"use client"` is used to declare a boundary between a Server and Client Component modules. This means that by defining a `"use client"` in a file, all other modules imported into it, including child components, are considered part of the client bundle.

```typescript
// app/counter.tsx
'use client'
 
import { useState } from 'react'
 
export default function Counter() {
  const [count, setCount] = useState(0)
 
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  )
}
```

> Note: The diagram showing the use of `onClick` and `useState` in a nested component (toggle.js) causing an error without the "use client" directive is not included here, but the concept is explained in the text.

### Defining multiple use client entry points:

You can define multiple `"use client"` entry points in your React Component tree. This allows you to split your application into multiple client bundles.

However, `"use client"` doesn't need to be defined in every component that needs to be rendered on the client. Once you define the boundary, all child components and modules imported into it are considered part of the client bundle.

### How are Client Components Rendered?

In Next.js, Client Components are rendered differently depending on whether the request is part of a full page load (an initial visit to your application or a page reload triggered by a browser refresh) or a subsequent navigation.

#### Full page load

To optimize the initial page load, Next.js will use React's APIs to render a static HTML preview on the server for both Client and Server Components. This means, when the user first visits your application, they will see the content of the page immediately, without having to wait for the client to download, parse, and execute the Client Component JavaScript bundle.

On the server:

1. React renders Server Components into a special data format called the React Server Component Payload (RSC Payload), which includes references to Client Components.
2. Next.js uses the RSC Payload and Client Component JavaScript instructions to render HTML for the route on the server.

Then, on the client:

1. The HTML is used to immediately show a fast non-interactive initial preview of the route.
2. The React Server Components Payload is used to reconcile the Client and Server Component trees, and update the DOM.
3. The JavaScript instructions are used to hydrate Client Components and make their UI interactive.

> **What is hydration?**
> 
> Hydration is the process of attaching event listeners to the DOM, to make the static HTML interactive. Behind the scenes, hydration is done with the `hydrateRoot` React API.

#### Subsequent Navigations

On subsequent navigations, Client Components are rendered entirely on the client, without the server-rendered HTML.

This means the Client Component JavaScript bundle is downloaded and parsed. Once the bundle is ready, React will use the RSC Payload to reconcile the Client and Server Component trees, and update the DOM.

### Going back to the Server Environment

Sometimes, after you've declared the `"use client"` boundary, you may want to go back to the server environment. For example, you may want to reduce the client bundle size, fetch data on the server, or use an API that is only available on the server.

You can keep code on the server even though it's theoretically nested inside Client Components by interleaving Client and Server Components and Server Actions. See the Composition Patterns page for more information.

## Server and Client Composition Patterns

When building React applications, you will need to consider what parts of your application should be rendered on the server or the client. This page covers some recommended composition patterns when using Server and Client Components.

### When to use Server and Client Components?

Here's a quick summary of the different use cases for Server and Client Components:

| What do you need to do? | Server Component | Client Component |
|-------------------------|------------------|-------------------|
| Fetch data | ✅ | ❌ |
| Access backend resources (directly) | ✅ | ❌ |
| Keep sensitive information on the server (access tokens, API keys, etc) | ✅ | ❌ |
| Keep large dependencies on the server / Reduce client-side JavaScript | ✅ | ❌ |
| Add interactivity and event listeners (onClick(), onChange(), etc) | ❌ | ✅ |
| Use State and Lifecycle Effects (useState(), useReducer(), useEffect(), etc) | ❌ | ✅ |
| Use browser-only APIs | ❌ | ✅ |
| Use custom hooks that depend on state, effects, or browser-only APIs | ❌ | ✅ |
| Use React Class components | ❌ | ✅ |

### Server Component Patterns

Before opting into client-side rendering, you may wish to do some work on the server like fetching data, or accessing your database or backend services.

Here are some common patterns when working with Server Components:

#### Sharing data between components

When fetching data on the server, there may be cases where you need to share data across different components. For example, you may have a layout and a page that depend on the same data.

Instead of using React Context (which is not available on the server) or passing data as props, you can use `fetch` or React's `cache` function to fetch the same data in the components that need it, without worrying about making duplicate requests for the same data. This is because React extends `fetch` to automatically memoize data requests, and the `cache` function can be used when `fetch` is not available.

Learn more about memoization in React.

#### Keeping Server-only Code out of the Client Environment

Since JavaScript modules can be shared between both Server and Client Components modules, it's possible for code that was only ever intended to be run on the server to sneak its way into the client.

For example, take the following data-fetching function:

```typescript
// lib/data.ts
export async function getData() {
  const res = await fetch('https://external-service.com/data', {
    headers: {
      authorization: process.env.API_KEY,
    },
  })
 
  return res.json()
}
```

At first glance, it appears that `getData` works on both the server and the client. However, this function contains an `API_KEY`, written with the intention that it would only ever be executed on the server.

Since the environment variable `API_KEY` is not prefixed with `NEXT_PUBLIC`, it's a private variable that can only be accessed on the server. To prevent your environment variables from being leaked to the client, Next.js replaces private environment variables with an empty string.

As a result, even though `getData()` can be imported and executed on the client, it won't work as expected. And while making the variable public would make the function work on the client, you may not want to expose sensitive information to the client.

To prevent this sort of unintended client usage of server code, we can use the `server-only` package to give other developers a build-time error if they ever accidentally import one of these modules into a Client Component.

To use `server-only`, first install the package:

```bash
npm install server-only
```

Then import the package into any module that contains server-only code:

```javascript
// lib/data.js
import 'server-only'
 
export async function getData() {
  const res = await fetch('https://external-service.com/data', {
    headers: {
      authorization: process.env.API_KEY,
    },
  })
 
  return res.json()
}
```

Now, any Client Component that imports `getData()` will receive a build-time error explaining that this module can only be used on the server.

The corresponding package `client-only` can be used to mark modules that contain client-only code – for example, code that accesses the `window` object.

#### Using Third-party Packages and Providers

Since Server Components are a new React feature, third-party packages and providers in the ecosystem are just beginning to add the `"use client"` directive to components that use client-only features like `useState`, `useEffect`, and `createContext`.

Today, many components from npm packages that use client-only features do not yet have the directive. These third-party components will work as expected within Client Components since they have the `"use client"` directive, but they won't work within Server Components.

For example, let's say you've installed the hypothetical `acme-carousel` package which has a `<Carousel />` component. This component uses `useState`, but it doesn't yet have the `"use client"` directive.

If you use `<Carousel />` within a Client Component, it will work as expected:

```typescript
// app/gallery.tsx
'use client'
 
import { useState } from 'react'
import { Carousel } from 'acme-carousel'
 
export default function Gallery() {
  const [isOpen, setIsOpen] = useState(false)
 
  return (
    <div>
      <button onClick={() => setIsOpen(true)}>View pictures</button>
 
      {/* Works, since Carousel is used within a Client Component */}
      {isOpen && <Carousel />}
    </div>
  )
}
```

However, if you try to use it directly within a Server Component, you'll see an error:

```typescript
// app/page.tsx
import { Carousel } from 'acme-carousel'
 
export default function Page() {
  return (
    <div>
      <p>View pictures</p>
 
      {/* Error: `useState` can not be used within Server Components */}
      <Carousel />
    </div>
  )
}
```

This is because Next.js doesn't know `<Carousel />` is using client-only features.

To fix this, you can wrap third-party components that rely on client-only features in your own Client Components:

```typescript
// app/carousel.tsx
'use client'
 
import { Carousel } from 'acme-carousel'
 
export default Carousel
```

Now, you can use `<Carousel />` directly within a Server Component:

```typescript
// app/page.tsx
import Carousel from './carousel'
 
export default function Page() {
  return (
    <div>
      <p>View pictures</p>
 
      {/*  Works, since Carousel is a Client Component */}
      <Carousel />
    </div>
  )
}
```

We don't expect you to need to wrap most third-party components since it's likely you'll be using them within Client Components. However, one exception is providers, since they rely on React state and context, and are typically needed at the root of an application. Learn more about third-party context providers below.

#### Using Context Providers

Context providers are typically rendered near the root of an application to share global concerns, like the current theme. Since React context is not supported in Server Components, trying to create a context at the root of your application will cause an error:

```typescript
// app/layout.tsx
import { createContext } from 'react'
 
//  createContext is not supported in Server Components
export const ThemeContext = createContext({})
 
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>
      </body>
    </html>
  )
}
```

To fix this, create your context and render its provider inside of a Client Component:

```typescript
// app/theme-provider.tsx
'use client'
 
import { createContext } from 'react'
 
export const ThemeContext = createContext({})
 
export default function ThemeProvider({
  children,
}: {
  children: React.ReactNode
}) {
  return <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>
}
```

Your Server Component will now be able to directly render your provider since it's been marked as a Client Component:

```typescript
// app/layout.tsx
import ThemeProvider from './theme-provider'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <ThemeProvider>{children}</ThemeProvider>
      </body>
    </html>
  )
}
```

With the provider rendered at the root, all other Client Components throughout your app will be able to consume this context.

> **Good to know**: You should render providers as deep as possible in the tree – notice how `ThemeProvider` only wraps `{children}` instead of the entire `<html>` document. This makes it easier for Next.js to optimize the static parts of your Server Components.

#### Advice for Library Authors

In a similar fashion, library authors creating packages to be consumed by other developers can use the `"use client"` directive to mark client entry points of their package. This allows users of the package to import package components directly into their Server Components without having to create a wrapping boundary.

You can optimize your package by using `'use client'` deeper in the tree, allowing the imported modules to be part of the Server Component module graph.

It's worth noting some bundlers might strip out `"use client"` directives. You can find an example of how to configure esbuild to include the `"use client"` directive in the React Wrap Balancer and Vercel Analytics repositories.

### Client Components

#### Moving Client Components Down the Tree

To reduce the Client JavaScript bundle size, we recommend moving Client Components down your component tree.

For example, you may have a Layout that has static elements (e.g. logo, links, etc) and an interactive search bar that uses state.

Instead of making the whole layout a Client Component, move the interactive logic to a Client Component (e.g. `<SearchBar />`) and keep your layout as a Server Component. This means you don't have to send all the component Javascript of the layout to the client.

```typescript
// app/layout.tsx
// SearchBar is a Client Component
import SearchBar from './searchbar'
// Logo is a Server Component
import Logo from './logo'
 
// Layout is a Server Component by default
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <nav>
        <Logo />
        <SearchBar />
      </nav>
      <main>{children}</main>
    </>
  )
}
```

#### Passing props from Server to Client Components (Serialization)

If you fetch data in a Server Component, you may want to pass data down as props to Client Components. Props passed from the Server to Client Components need to be serializable by React.

If your Client Components depend on data that is not serializable, you can fetch data on the client with a third party library or on the server via a Route Handler.

#### Interleaving Server and Client Components

When interleaving Client and Server Components, it may be helpful to visualize your UI as a tree of components. Starting with the root layout, which is a Server Component, you can then render certain subtrees of components on the client by adding the `"use client"` directive.

Within those client subtrees, you can still nest Server Components or call Server Actions, however there are some things to keep in mind:

- During a request-response lifecycle, your code moves from the server to the client. If you need to access data or resources on the server while on the client, you'll be making a new request to the server - not switching back and forth.
- When a new request is made to the server, all Server Components are rendered first, including those nested inside Client Components. The rendered result (RSC Payload) will contain references to the locations of Client Components. Then, on the client, React uses the RSC Payload to reconcile Server and Client Components into a single tree.
- Since Client Components are rendered after Server Components, you cannot import a Server Component into a Client Component module (since it would require a new request back to the server). Instead, you can pass a Server Component as props to a Client Component. See the unsupported pattern and supported pattern sections below.

##### Unsupported Pattern: Importing Server Components into Client Components

The following pattern is not supported. You cannot import a Server Component into a Client Component:

```typescript
// app/client-component.tsx
'use client'
 
// You cannot import a Server Component into a Client Component.
import ServerComponent from './Server-Component'
 
export default function ClientComponent({
  children,
}: {
  children: React.ReactNode
}) {
  const [count, setCount] = useState(0)
 
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
 
      <ServerComponent />
    </>
  )
}
```

##### Supported Pattern: Passing Server Components to Client Components as Props

The following pattern is supported. You can pass Server Components as a prop to a Client Component.

A common pattern is to use the React `children` prop to create a "slot" in your Client Component.

In the example below, `<ClientComponent>` accepts a `children` prop:

```typescript
// app/client-component.tsx
'use client'
 
import { useState } from 'react'
 
export default function ClientComponent({
  children,
}: {
  children: React.ReactNode
}) {
  const [count, setCount] = useState(0)
 
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      {children}
    </>
  )
}
```

`<ClientComponent>` doesn't know that `children` will eventually be filled in by the result of a Server Component. The only responsibility `<ClientComponent>` has is to decide where `children` will eventually be placed.

In a parent Server Component, you can import both the `<ClientComponent>` and `<ServerComponent>` and pass `<ServerComponent>` as a child of `<ClientComponent>`:

```typescript
// app/page.tsx
// This pattern works:
// You can pass a Server Component as a child or prop of a
// Client Component.
import ClientComponent from './client-component'
import ServerComponent from './server-component'
 
// Pages in Next.js are Server Components by default
export default function Page() {
  return (
    <ClientComponent>
      <ServerComponent />
    </ClientComponent>
  )
}
```

With this approach, `<ClientComponent>` and `<ServerComponent>` are decoupled and can be rendered independently. In this case, the child `<ServerComponent>` can be rendered on the server, well before `<ClientComponent>` is rendered on the client.

> **Good to know**:
> - The pattern of "lifting content up" has been used to avoid re-rendering a nested child component when a parent component re-renders.
> - You're not limited to the `children` prop. You can use any prop to pass JSX.

## Partial Prerendering

Partial Prerendering (PPR) enables you to combine static and dynamic components together in the same route.

During the build, Next.js prerenders as much of the route as possible. If dynamic code is detected, like reading from the incoming request, you can wrap the relevant component with a React Suspense boundary. The Suspense boundary fallback will then be included in the prerendered HTML.

> **Note:** Partial Prerendering is an experimental feature and subject to change. It is not ready for production use.

> Partially Prerendered Product Page showing static nav and product information, and dynamic cart and recommended products

🎥 Watch: [Why PPR and how it works → YouTube (10 minutes)](https://www.youtube.com/watch?v=your-video-id-here).

### Background

PPR enables your Next.js server to immediately send prerendered content.

To prevent client to server waterfalls, dynamic components begin streaming from the server in parallel while serving the initial prerender. This ensures dynamic components can begin rendering before client JavaScript has been loaded in the browser.

To prevent creating many HTTP requests for each dynamic component, PPR is able to combine the static prerender and dynamic components together into a single HTTP request. This ensures there are not multiple network roundtrips needed for each dynamic component.

### Using Partial Prerendering

#### Incremental Adoption (Version 15)

In Next.js 15, you can incrementally adopt Partial Prerendering in layouts and pages by setting the `ppr` option in `next.config.js` to `incremental`, and exporting the `experimental_ppr` route config option at the top of the file:

```typescript
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  experimental: {
    ppr: 'incremental',
  },
}

export default nextConfig
```

```typescript
// app/page.tsx
import { Suspense } from "react"
import { StaticComponent, DynamicComponent, Fallback } from "@/app/ui"

export const experimental_ppr = true

export default function Page() {
  return (
    <>
      <StaticComponent />
      <Suspense fallback={<Fallback />}>
        <DynamicComponent />
      </Suspense>
    </>
  );
}
```

Good to know:

- Routes that don't have `experimental_ppr` will default to `false` and will not be prerendered using PPR. You need to explicitly opt-in to PPR for each route.
- `experimental_ppr` will apply to all children of the route segment, including nested layouts and pages. You don't have to add it to every file, only the top segment of a route.
- To disable PPR for children segments, you can set `experimental_ppr` to `false` in the child segment.

#### Enabling PPR (Version 14)

For version 14, you can enable it by adding the `ppr` option to your `next.config.js` file. This will apply to all routes in your application:

```typescript
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  experimental: {
    ppr: true,
  },
}

export default nextConfig
```

### Dynamic Components

When creating the prender for your route during `next build`, Next.js requires that dynamic functions are wrapped with React Suspense. The fallback is then included in the prerender.

For example, using functions like `cookies()` or `headers()`:

```typescript
// app/user.tsx
import { cookies } from 'next/headers'

export function User() {
  const session = cookies().get('session')?.value
  return '...'
}
```

This component requires looking at the incoming request to read cookies. To use this with PPR, you should wrap the component with Suspense:

```typescript
// app/page.tsx
import { Suspense } from 'react'
import { User, AvatarSkeleton } from './user'

export const experimental_ppr = true

export default function Page() {
  return (
    <section>
      <h1>This will be prerendered</h1>
      <Suspense fallback={<AvatarSkeleton />}>
        <User />
      </Suspense>
    </section>
  )
}
```

Components only opt into dynamic rendering when the value is accessed.

For example, if you are reading `searchParams` from a page, you can forward this value to another component as a prop:

```typescript
// app/page.tsx
import { Table } from './table'

export default function Page({
  searchParams,
}: {
  searchParams: { sort: string }
}) {
  return (
    <section>
      <h1>This will be prerendered</h1>
      <Table searchParams={searchParams} />
    </section>
  )
}
```

Inside of the table component, accessing the value from `searchParams` will make the component run dynamically:

```typescript
// app/table.tsx
export function Table({ searchParams }: { searchParams: { sort: string } }) {
  const sort = searchParams.sort === 'true'
  return '...'
}
```

## Runtimes

Next.js has two server runtimes you can use in your application:

1. The **Node.js Runtime** (default) which has access to all Node.js APIs and compatible packages from the ecosystem.
2. The **Edge Runtime** which contains a more limited set of APIs.

### Use Cases

- The Node.js runtime is used for rendering your application.
- The Edge runtime is used for Middleware (routing rules like redirects, rewrites, and setting headers).

### Caveats

- The Edge runtime does not support all Node.js APIs. Some packages will not work. Learn more about the unsupported APIs in the [Edge Runtime](https://nextjs.org/docs/api-reference/edge-runtime).
- The Edge runtime does not support Incremental Static Regeneration (ISR).
- Both runtimes can support streaming depending on your deployment infrastructure.
