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

# Caching

Caching in Next.js

Next.js improves your application's performance and reduces costs by caching rendering work and data requests. This page provides an in-depth look at Next.js caching mechanisms, the APIs you can use to configure them, and how they interact with each other.

> Good to know: This page helps you understand how Next.js works under the hood but is not essential knowledge to be productive with Next.js. Most of Next.js' caching heuristics are determined by your API usage and have defaults for the best performance with zero or minimal configuration.

## Overview

Here's a high-level overview of the different caching mechanisms and their purpose:

| Mechanism | What | Where | Purpose | Duration |
|-----------|------|-------|---------|----------|
| Request Memoization | Return values of functions | Server | Re-use data in a React Component tree | Per-request lifecycle |
| Data Cache | Data | Server | Store data across user requests and deployments | Persistent (can be revalidated) |
| Full Route Cache | HTML and RSC payload | Server | Reduce rendering cost and improve performance | Persistent (can be revalidated) |
| Router Cache | RSC Payload | Client | Reduce server requests on navigation | User session or time-based |

By default, Next.js will cache as much as possible to improve performance and reduce cost. This means routes are statically rendered and data requests are cached unless you opt out. The diagram below shows the default caching behavior: when a route is statically rendered at build time and when a static route is first visited.

[Diagram showing the default caching behavior in Next.js for the four mechanisms, with HIT, MISS and SET at build time and when a route is first visited.]

Caching behavior changes depending on whether the route is statically or dynamically rendered, data is cached or uncached, and whether a request is part of an initial visit or a subsequent navigation. Depending on your use case, you can configure the caching behavior for individual routes and data requests.

## Request Memoization

Next.js extends the fetch API to automatically memoize requests that have the same URL and options. This means you can call a fetch function for the same data in multiple places in a React component tree while only executing it once.

### Deduplicated Fetch Requests

For example, if you need to use the same data across a route (e.g. in a Layout, Page, and multiple components), you do not have to fetch data at the top of the tree, and forward props between components. Instead, you can fetch data in the components that need it without worrying about the performance implications of making multiple requests across the network for the same data.

```typescript
async function getItem() {
  // The `fetch` function is automatically memoized and the result
  // is cached
  const res = await fetch('https://.../item/1')
  return res.json()
}
 
// This function is called twice, but only executed the first time
const item = await getItem() // cache MISS
 
// The second call could be anywhere in your route
const item = await getItem() // cache HIT
```

### How Request Memoization Works

[Diagram showing how fetch memoization works during React rendering.]

1. While rendering a route, the first time a particular request is called, its result will not be in memory and it'll be a cache MISS.
2. Therefore, the function will be executed, and the data will be fetched from the external source, and the result will be stored in memory.
3. Subsequent function calls of the request in the same render pass will be a cache HIT, and the data will be returned from memory without executing the function.
4. Once the route has been rendered and the rendering pass is complete, memory is "reset" and all request memoization entries are cleared.

> Good to know:
> - Request memoization is a React feature, not a Next.js feature. It's included here to show how it interacts with the other caching mechanisms.
> - Memoization only applies to the GET method in fetch requests.
> - Memoization only applies to the React Component tree, this means:
>   - It applies to fetch requests in `generateMetadata`, `generateStaticParams`, Layouts, Pages, and other Server Components.
>   - It doesn't apply to fetch requests in Route Handlers as they are not a part of the React component tree.
> - For cases where fetch is not suitable (e.g. some database clients, CMS clients, or GraphQL clients), you can use the React cache function to memoize functions.

### Duration

The cache lasts the lifetime of a server request until the React component tree has finished rendering.

### Revalidating

Since the memoization is not shared across server requests and only applies during rendering, there is no need to revalidate it.

### Opting out

Memoization only applies to the GET method in fetch requests, other methods, such as POST and DELETE, are not memoized. This default behavior is a React optimization and we do not recommend opting out of it.

To manage individual requests, you can use the `signal` property from `AbortController`. However, this will not opt requests out of memoization, rather, abort in-flight requests.

```javascript
const { signal } = new AbortController()
fetch(url, { signal })
```

## Data Cache

Next.js has a built-in Data Cache that persists the result of data fetches across incoming server requests and deployments. This is possible because Next.js extends the native fetch API to allow each request on the server to set its own persistent caching semantics.

> Good to know: In the browser, the `cache` option of fetch indicates how a request will interact with the browser's HTTP cache, in Next.js, the `cache` option indicates how a server-side request will interact with the server's Data Cache.

By default, data requests that use fetch are not cached. You can use the `cache` and `next.revalidate` options of fetch to configure the caching behavior.

### How the Data Cache Works

[Diagram showing how cached and uncached fetch requests interact with the Data Cache. Cached requests are stored in the Data Cache, and memoized, uncached requests are fetched from the data source, not stored in the Data Cache, and memoized.]

1. The first time a fetch request with the 'force-cache' option is called during rendering, Next.js checks the Data Cache for a cached response.
2. If a cached response is found, it's returned immediately and memoized.
3. If a cached response is not found, the request is made to the data source, the result is stored in the Data Cache, and memoized.
4. For uncached data (e.g. no cache option defined or using `{ cache: 'no-store' }`), the result is always fetched from the data source, and memoized.
5. Whether the data is cached or uncached, the requests are always memoized to avoid making duplicate requests for the same data during a React render pass.

### Differences between the Data Cache and Request Memoization

While both caching mechanisms help improve performance by re-using cached data, the Data Cache is persistent across incoming requests and deployments, whereas memoization only lasts the lifetime of a request.

With memoization, we reduce the number of duplicate requests in the same render pass that have to cross the network boundary from the rendering server to the Data Cache server (e.g. a CDN or Edge Network) or data source (e.g. a database or CMS). With the Data Cache, we reduce the number of requests made to our origin data source.

### Duration

The Data Cache is persistent across incoming requests and deployments unless you revalidate or opt-out.

### Revalidating

Cached data can be revalidated in two ways, with:

1. **Time-based Revalidation**: Revalidate data after a certain amount of time has passed and a new request is made. This is useful for data that changes infrequently and freshness is not as critical.
2. **On-demand Revalidation**: Revalidate data based on an event (e.g. form submission). On-demand revalidation can use a tag-based or path-based approach to revalidate groups of data at once. This is useful when you want to ensure the latest data is shown as soon as possible (e.g. when content from your headless CMS is updated).

#### Time-based Revalidation

To revalidate data at a timed interval, you can use the `next.revalidate` option of fetch to set the cache lifetime of a resource (in seconds).

```javascript
// Revalidate at most every hour
fetch('https://...', { next: { revalidate: 3600 } })
```

Alternatively, you can use Route Segment Config options to configure all fetch requests in a segment or for cases where you're not able to use fetch.

##### How Time-based Revalidation Works

[Diagram showing how time-based revalidation works, after the revalidation period, stale data is returned for the first request, then data is revalidated.]

1. The first time a fetch request with revalidate is called, the data will be fetched from the external data source and stored in the Data Cache.
2. Any requests that are called within the specified timeframe (e.g. 60-seconds) will return the cached data.
3. After the timeframe, the next request will still return the cached (now stale) data.
4. Next.js will trigger a revalidation of the data in the background.
5. Once the data is fetched successfully, Next.js will update the Data Cache with the fresh data.
6. If the background revalidation fails, the previous data will be kept unaltered.

This is similar to stale-while-revalidate behavior.

#### On-demand Revalidation

Data can be revalidated on-demand by path (`revalidatePath`) or by cache tag (`revalidateTag`).

##### How On-Demand Revalidation Works

[Diagram showing how on-demand revalidation works, the Data Cache is updated with fresh data after a revalidation request.]

1. The first time a fetch request is called, the data will be fetched from the external data source and stored in the Data Cache.
2. When an on-demand revalidation is triggered, the appropriate cache entries will be purged from the cache.
3. This is different from time-based revalidation, which keeps the stale data in the cache until the fresh data is fetched.
4. The next time a request is made, it will be a cache MISS again, and the data will be fetched from the external data source and stored in the Data Cache.

### Opting out

Since fetch requests are not cached by default, you don't need to opt out of caching. This means data will be fetched from your data source whenever fetch is called.

> Note: Data Cache is currently only available in Layout, Pages, and Route Handlers, not Middleware. Any fetches done inside of your middleware will not be cached by default.

### Vercel Data Cache

If your Next.js application is deployed to Vercel, we recommend reading the [Vercel Data Cache documentation](https://vercel.com/docs/concepts/next.js/data-cache) for a better understanding of Vercel specific features.

## Full Route Cache

Related terms:

You may see the terms Automatic Static Optimization, Static Site Generation, or Static Rendering being used interchangeably to refer to the process of rendering and caching routes of your application at build time.

Next.js automatically renders and caches routes at build time. This is an optimization that allows you to serve the cached route instead of rendering on the server for every request, resulting in faster page loads.

To understand how the Full Route Cache works, it's helpful to look at how React handles rendering, and how Next.js caches the result:

### 1. React Rendering on the Server

On the server, Next.js uses React's APIs to orchestrate rendering. The rendering work is split into chunks: by individual routes segments and Suspense boundaries.

Each chunk is rendered in two steps:

1. React renders Server Components into a special data format, optimized for streaming, called the React Server Component Payload.
2. Next.js uses the React Server Component Payload and Client Component JavaScript instructions to render HTML on the server.

This means we don't have to wait for everything to render before caching the work or sending a response. Instead, we can stream a response as work is completed.

#### What is the React Server Component Payload?

The React Server Component Payload is a compact binary representation of the rendered React Server Components tree. It's used by React on the client to update the browser's DOM. The React Server Component Payload contains:

- The rendered result of Server Components
- Placeholders for where Client Components should be rendered and references to their JavaScript files
- Any props passed from a Server Component to a Client Component

To learn more, see the [Server Components documentation](https://nextjs.org/docs/getting-started/react-essentials).

### 2. Next.js Caching on the Server (Full Route Cache)

[Default behavior of the Full Route Cache, showing how the React Server Component Payload and HTML are cached on the server for statically rendered routes.]

The default behavior of Next.js is to cache the rendered result (React Server Component Payload and HTML) of a route on the server. This applies to statically rendered routes at build time, or during revalidation.

### 3. React Hydration and Reconciliation on the Client

At request time, on the client:

1. The HTML is used to immediately show a fast non-interactive initial preview of the Client and Server Components.
2. The React Server Components Payload is used to reconcile the Client and rendered Server Component trees, and update the DOM.
3. The JavaScript instructions are used to hydrate Client Components and make the application interactive.

### 4. Next.js Caching on the Client (Router Cache)

The React Server Component Payload is stored in the client-side Router Cache - a separate in-memory cache, split by individual route segment. This Router Cache is used to improve the navigation experience by storing previously visited routes and prefetching future routes.

### 5. Subsequent Navigations

On subsequent navigations or during prefetching, Next.js will check if the React Server Component Payload is stored in the Router Cache. If so, it will skip sending a new request to the server.

If the route segments are not in the cache, Next.js will fetch the React Server Component Payload from the server, and populate the Router Cache on the client.

### Static and Dynamic Rendering

Whether a route is cached or not at build time depends on whether it's statically or dynamically rendered. Static routes are cached by default, whereas dynamic routes are rendered at request time, and not cached.

This diagram shows the difference between statically and dynamically rendered routes, with cached and uncached data:

[How static and dynamic rendering affects the Full Route Cache. Static routes are cached at build time or after data revalidation, whereas dynamic routes are never cached]

Learn more about [static and dynamic rendering](https://nextjs.org/docs/app/building-your-application/rendering/server-components#static-rendering-default).

### Duration

By default, the Full Route Cache is persistent. This means that the render output is cached across user requests.

### Invalidation

There are two ways you can invalidate the Full Route Cache:

1. Revalidating Data: Revalidating the Data Cache, will in turn invalidate the Router Cache by re-rendering components on the server and caching the new render output.
2. Redeploying: Unlike the Data Cache, which persists across deployments, the Full Route Cache is cleared on new deployments.

### Opting out

You can opt out of the Full Route Cache, or in other words, dynamically render components for every incoming request, by:

1. Using a Dynamic Function: This will opt the route out from the Full Route Cache and dynamically render it at request time. The Data Cache can still be used.
2. Using the `dynamic = 'force-dynamic'` or `revalidate = 0` route segment config options: This will skip the Full Route Cache and the Data Cache. Meaning components will be rendered and data fetched on every incoming request to the server. The Router Cache will still apply as it's a client-side cache.
3. Opting out of the Data Cache: If a route has a fetch request that is not cached, this will opt the route out of the Full Route Cache. The data for the specific fetch request will be fetched for every incoming request. Other fetch requests that do not opt out of caching will still be cached in the Data Cache. This allows for a hybrid of cached and uncached data.

## Client-side Router Cache

Next.js has an in-memory client-side router cache that stores the RSC payload of route segments, split by layouts, loading states, and pages.

When a user navigates between routes, Next.js caches the visited route segments and prefetches the routes the user is likely to navigate to. This results in instant back/forward navigation, no full-page reload between navigations, and preservation of React state and browser state.

With the Router Cache:

- Layouts are cached and reused on navigation (partial rendering).
- Loading states are cached and reused on navigation for instant navigation.
- Pages are not cached by default, but are reused during browser backward and forward navigation. You can enable caching for page segments by using the experimental `staleTimes` config option.

> Good to know: This cache specifically applies to Next.js and Server Components, and is different to the browser's bfcache, though it has a similar result.

### Duration

The cache is stored in the browser's temporary memory. Two factors determine how long the router cache lasts:

1. **Session**: The cache persists across navigation. However, it's cleared on page refresh.
2. **Automatic Invalidation Period**: The cache of layouts and loading states is automatically invalidated after a specific time. The duration depends on how the resource was prefetched, and if the resource was statically generated:
   - Default Prefetching (`prefetch={null}` or unspecified): not cached for dynamic pages, 5 minutes for static pages.
   - Full Prefetching (`prefetch={true}` or `router.prefetch`): 5 minutes for both static & dynamic pages.

While a page refresh will clear all cached segments, the automatic invalidation period only affects the individual segment from the time it was prefetched.

> Good to know: The experimental `staleTimes` config option can be used to adjust the automatic invalidation times mentioned above.

### Invalidation

There are two ways you can invalidate the Router Cache:

1. In a Server Action:
   - Revalidating data on-demand by path with (`revalidatePath`) or by cache tag with (`revalidateTag`)
   - Using `cookies.set` or `cookies.delete` invalidates the Router Cache to prevent routes that use cookies from becoming stale (e.g. authentication).
2. Calling `router.refresh` will invalidate the Router Cache and make a new request to the server for the current route.

### Opting out

As of Next.js 15, page segments are opted out by default.

> Good to know: You can also opt out of prefetching by setting the `prefetch` prop of the `<Link>` component to `false`.

## Cache Interactions

When configuring the different caching mechanisms, it's important to understand how they interact with each other:

### Data Cache and Full Route Cache

- Revalidating or opting out of the Data Cache will invalidate the Full Route Cache, as the render output depends on data.
- Invalidating or opting out of the Full Route Cache does not affect the Data Cache. You can dynamically render a route that has both cached and uncached data. This is useful when most of your page uses cached data, but you have a few components that rely on data that needs to be fetched at request time. You can dynamically render without worrying about the performance impact of re-fetching all the data.

### Data Cache and Client-side Router cache

- To immediately invalidate the Data Cache and Router cache, you can use `revalidatePath` or `revalidateTag` in a Server Action.
- Revalidating the Data Cache in a Route Handler will not immediately invalidate the Router Cache as the Route Handler isn't tied to a specific route. This means Router Cache will continue to serve the previous payload until a hard refresh, or the automatic invalidation period has elapsed.

## APIs

The following table provides an overview of how different Next.js APIs affect caching:

| API | Router Cache | Full Route Cache | Data Cache | React Cache |
|-----|--------------|-------------------|------------|-------------|
| `<Link prefetch>` | Cache |  |  |  |
| `router.prefetch` | Cache |  |  |  |
| `router.refresh` | Revalidate |  |  |  |
| `fetch` |  |  | Cache | Cache |
| `fetch options.cache` |  |  | Cache or Opt out |  |
| `fetch options.next.revalidate` |  | Revalidate | Revalidate |  |
| `fetch options.next.tags` |  | Cache | Cache |  |
| `revalidateTag` | Revalidate (Server Action) | Revalidate | Revalidate |  |
| `revalidatePath` | Revalidate (Server Action) | Revalidate | Revalidate |  |
| `const revalidate` |  | Revalidate or Opt out | Revalidate or Opt out |  |
| `const dynamic` |  | Cache or Opt out | Cache or Opt out |  |
| `cookies` | Revalidate (Server Action) | Opt out |  |  |
| `headers`, `searchParams` |  | Opt out |  |  |
| `generateStaticParams` |  | Cache |  |  |
| `React.cache` |  |  |  | Cache |
| `unstable_cache` |  |  | Cache |  |

### `<Link>`

By default, the `<Link>` component automatically prefetches routes from the Full Route Cache and adds the React Server Component Payload to the Router Cache.

To disable prefetching, you can set the `prefetch` prop to `false`. But this will not skip the cache permanently, the route segment will still be cached client-side when the user visits the route.

Learn more about the [`<Link>` component](https://nextjs.org/docs/app/api-reference/components/link).

### `router.prefetch`

The `prefetch` option of the `useRouter` hook can be used to manually prefetch a route. This adds the React Server Component Payload to the Router Cache.

See the [useRouter hook API reference](https://nextjs.org/docs/app/api-reference/functions/use-router).

### `router.refresh`

The `refresh` option of the `useRouter` hook can be used to manually refresh a route. This completely clears the Router Cache, and makes a new request to the server for the current route. `refresh` does not affect the Data or Full Route Cache.

The rendered result will be reconciled on the client while preserving React state and browser state.

See the [useRouter hook API reference](https://nextjs.org/docs/app/api-reference/functions/use-router).

### `fetch`

Data returned from `fetch` is not automatically cached in the Data Cache.

```javascript
// Cached by default. `no-store` is the default option and can be omitted.
fetch(`https://...`, { cache: 'no-store' })
```

Since the render output depends on data, using `cache: 'no-store'` will also skip the Full Route Cache for the route where the fetch request is used. That is, the route will be dynamically rendered on every request, but you can still have other cached data requests in the same route.

See the [fetch API Reference](https://nextjs.org/docs/app/api-reference/functions/fetch) for more options.

### `fetch options.cache`

You can opt individual fetch into caching by setting the `cache` option to `force-cache`:

```javascript
// Opt out of caching
fetch(`https://...`, { cache: 'force-cache' })
```

See the [fetch API Reference](https://nextjs.org/docs/app/api-reference/functions/fetch) for more options.

### `fetch options.next.revalidate`

You can use the `next.revalidate` option of fetch to set the revalidation period (in seconds) of an individual fetch request. This will revalidate the Data Cache, which in turn will revalidate the Full Route Cache. Fresh data will be fetched, and components will be re-rendered on the server.

```javascript
// Revalidate at most after 1 hour
fetch(`https://...`, { next: { revalidate: 3600 } })
```

See the [fetch API reference](https://nextjs.org/docs/app/api-reference/functions/fetch) for more options.

### `fetch options.next.tags` and `revalidateTag`

Next.js has a cache tagging system for fine-grained data caching and revalidation.

- When using `fetch` or `unstable_cache`, you have the option to tag cache entries with one or more tags.
- Then, you can call `revalidateTag` to purge the cache entries associated with that tag.

For example, you can set a tag when fetching data:

```javascript
// Cache data with a tag
fetch(`https://...`, { next: { tags: ['a', 'b', 'c'] } })
```

Then, call `revalidateTag` with a tag to purge the cache entry:

```javascript
// Revalidate entries with a specific tag
revalidateTag('a')
```

There are two places you can use `revalidateTag`, depending on what you're trying to achieve:

1. Route Handlers - to revalidate data in response of a third party event (e.g. webhook). This will not invalidate the Router Cache immediately as the Router Handler isn't tied to a specific route.
2. Server Actions - to revalidate data after a user action (e.g. form submission). This will invalidate the Router Cache for the associated route.

### `revalidatePath`

`revalidatePath` allows you manually revalidate data and re-render the route segments below a specific path in a single operation. Calling the `revalidatePath` method revalidates the Data Cache, which in turn invalidates the Full Route Cache.

```javascript
revalidatePath('/')
```

There are two places you can use `revalidatePath`, depending on what you're trying to achieve:

1. Route Handlers - to revalidate data in response to a third party event (e.g. webhook).
2. Server Actions - to revalidate data after a user interaction (e.g. form submission, clicking a button).

See the [revalidatePath API reference](https://nextjs.org/docs/app/api-reference/functions/revalidatePath) for more information.

#### `revalidatePath` vs. `router.refresh`:

Calling `router.refresh` will clear the Router cache, and re-render route segments on the server without invalidating the Data Cache or the Full Route Cache.

The difference is that `revalidatePath` purges the Data Cache and Full Route Cache, whereas `router.refresh()` does not change the Data Cache and Full Route Cache, as it is a client-side API.

### Dynamic Functions

Dynamic functions like `cookies` and `headers`, and the `searchParams` prop in Pages depend on runtime incoming request information. Using them will opt a route out of the Full Route Cache, in other words, the route will be dynamically rendered.

### `cookies`

Using `cookies.set` or `cookies.delete` in a Server Action invalidates the Router Cache to prevent routes that use cookies from becoming stale (e.g. to reflect authentication changes).

See the [cookies API reference](https://nextjs.org/docs/app/api-reference/functions/cookies).

### Segment Config Options

The Route Segment Config options can be used to override the route segment defaults or when you're not able to use the fetch API (e.g. database client or 3rd party libraries).

The following Route Segment Config options will opt out of the Data Cache and Full Route Cache:

```javascript
const dynamic = 'force-dynamic'
const revalidate = 0
```

See the [Route Segment Config documentation](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config) for more options.

### `generateStaticParams`

For dynamic segments (e.g. `app/blog/[slug]/page.js`), paths provided by `generateStaticParams` are cached in the Full Route Cache at build time. At request time, Next.js will also cache paths that weren't known at build time the first time they're visited.

To statically render all paths at build time, supply the full list of paths to `generateStaticParams`:

```javascript
export async function generateStaticParams() {
  const posts = await fetch('https://.../posts').then((res) => res.json())
 
  return posts.map((post) => ({
    slug: post.slug,
  }))
}
```

To statically render a subset of paths at build time, and the rest the first time they're visited at runtime, return a partial list of paths:

```javascript
export async function generateStaticParams() {
  const posts = await fetch('https://.../posts').then((res) => res.json())
 
  // Render the first 10 posts at build time
  return posts.slice(0, 10).map((post) => ({
    slug: post.slug,
  }))
}
```

To statically render all paths the first time they're visited, return an empty array (no paths will be rendered at build time) or utilize `export const dynamic = 'force-static'`:

```javascript
export async function generateStaticParams() {
  return []
}
```

```javascript
export const dynamic = 'force-static'
```

> Good to know: You must return an array from `generateStaticParams`, even if it's empty. Otherwise, the route will be dynamically rendered.

To disable caching at request time, add the `export const dynamicParams = false` option in a route segment. When this config option is used, only paths provided by `generateStaticParams` will be served, and other routes will 404 or match (in the case of catch-all routes).

### React `cache` function

The React `cache` function allows you to memoize the return value of a function, allowing you to call the same function multiple times while only executing it once.

Since fetch requests are automatically memoized, you do not need to wrap it in React `cache`. However, you can use `cache` to manually memoize data requests for use cases when the fetch API is not suitable. For example, some database clients, CMS clients, or GraphQL clients.

```typescript
import { cache } from 'react'
import db from '@/lib/db'
 
export const getItem = cache(async (id: string) => {
  const item = await db.item.findUnique({ id })
  return item
})
```

# Styling

Next.js supports different ways of styling your application, including:

- **CSS Modules**: Create locally scoped CSS classes to avoid naming conflicts and improve maintainability.
- **Global CSS**: Simple to use and familiar for those experienced with traditional CSS, but can lead to larger CSS bundles and difficulty managing styles as the application grows.
- **Tailwind CSS**: A utility-first CSS framework that allows for rapid custom designs by composing utility classes.
- **Sass**: A popular CSS preprocessor that extends CSS with features like variables, nested rules, and mixins.
- **CSS-in-JS**: Embed CSS directly in your JavaScript components, enabling dynamic and scoped styling.

## CSS

Next.js supports multiple ways of handling CSS, including:

- CSS Modules
- Global Styles
- External Stylesheets

### CSS Modules

Next.js has built-in support for CSS Modules using the `.module.css` extension.

CSS Modules locally scope CSS by automatically creating a unique class name. This allows you to use the same class name in different files without worrying about collisions. This behavior makes CSS Modules the ideal way to include component-level CSS.

#### Example

CSS Modules can be imported into any file inside the app directory:

```typescript
// app/dashboard/layout.tsx
import styles from './styles.module.css'
 
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section className={styles.dashboard}>{children}</section>
}
```

```css
/* app/dashboard/styles.module.css */
.dashboard {
  padding: 24px;
}
```

CSS Modules are only enabled for files with the `.module.css` and `.module.sass` extensions.

In production, all CSS Module files will be automatically concatenated into many minified and code-split `.css` files. These `.css` files represent hot execution paths in your application, ensuring the minimal amount of CSS is loaded for your application to paint.

### Global Styles

Global styles can be imported into any layout, page, or component inside the app directory.

> Good to know:
> - This is different from the pages directory, where you can only import global styles inside the `_app.js` file.
> - Next.js does not support usage of global styles unless they are actually global, meaning they can apply to all pages and can live for the lifetime of the application. This is because Next.js uses React's built-in support for stylesheets to integrate with Suspense. This built-in support currently does not remove stylesheets as you navigate between routes. Because of this, we recommend using CSS Modules over global styles.

For example, consider a stylesheet named `app/global.css`:

```css
/* app/global.css */
body {
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

Inside the root layout (`app/layout.js`), import the `global.css` stylesheet to apply the styles to every route in your application:

```typescript
// app/layout.tsx
// These styles apply to every route in the application
import './global.css'
 
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

### External Stylesheets

Stylesheets published by external packages can be imported anywhere in the app directory, including colocated components:

```typescript
// app/layout.tsx
import 'bootstrap/dist/css/bootstrap.css'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className="container">{children}</body>
    </html>
  )
}
```

> Good to know: External stylesheets must be directly imported from an npm package or downloaded and colocated with your codebase. You cannot use `<link rel="stylesheet" />`.

### Ordering and Merging

Next.js optimizes CSS during production builds by automatically chunking (merging) stylesheets. The CSS order is determined by the order in which you import the stylesheets into your application code.

For example, `base-button.module.css` will be ordered before `page.module.css` since `<BaseButton>` is imported first in `<Page>`:

```typescript
// base-button.tsx
import styles from './base-button.module.css'
 
export function BaseButton() {
  return <button className={styles.primary} />
}
```

```typescript
// page.ts
import { BaseButton } from './base-button'
import styles from './page.module.css'
 
export function Page() {
  return <BaseButton className={styles.primary} />
}
```

To maintain a predictable order, we recommend the following:

1. Only import a CSS file in a single JS/TS file.
2. If using global class names, import the global styles in the same file in the order you want them to be applied.
3. Prefer CSS Modules over global styles.
4. Use a consistent naming convention for your CSS modules. For example, using `<name>.module.css` over `<name>.tsx`.
5. Extract shared styles into a separate shared component.
6. If using Tailwind, import the stylesheet at the top of the file, preferably in the Root Layout.
7. Turn off any linters/formatters (e.g., ESLint's sort-import) that automatically sort your imports. This can inadvertently affect your CSS since CSS import order matters.

> Good to know:
> - CSS ordering can behave differently in development mode, always ensure to check the build (`next build`) to verify the final CSS order.
> - You can use the `cssChunking` option in `next.config.js` to control how CSS is chunked.

### Additional Features

Next.js includes additional features to improve the authoring experience of adding styles:

- When running locally with `next dev`, local stylesheets (either global or CSS modules) will take advantage of Fast Refresh to instantly reflect changes as edits are saved.
- When building for production with `next build`, CSS files will be bundled into fewer minified `.css` files to reduce the number of network requests needed to retrieve styles.
- If you disable JavaScript, styles will still be loaded in the production build (`next start`). However, JavaScript is still required for `next dev` to enable Fast Refresh.

## Tailwind CSS

Tailwind CSS is a utility-first CSS framework that works exceptionally well with Next.js.

### Installing Tailwind

Install the Tailwind CSS packages and run the init command to generate both the `tailwind.config.js` and `postcss.config.js` files:

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Configuring Tailwind

Inside your Tailwind configuration file, add paths to the files that will use Tailwind class names:

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'
 
const config: Config = {
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}', // Note the addition of the `app` directory.
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
 
    // Or if using `src` directory:
    './src/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
export default config
```

You do not need to modify `postcss.config.js`.

### Importing Styles

Add the Tailwind CSS directives that Tailwind will use to inject its generated styles to a Global Stylesheet in your application, for example:

```css
/* app/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Inside the root layout (`app/layout.tsx`), import the `globals.css` stylesheet to apply the styles to every route in your application.

```typescript
// app/layout.tsx
import type { Metadata } from 'next'
 
// These styles apply to every route in the application
import './globals.css'
 
export const metadata: Metadata = {
  title: 'Create Next App',
  description: 'Generated by create next app',
}
 
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

### Using Classes

After installing Tailwind CSS and adding the global styles, you can use Tailwind's utility classes in your application.

```typescript
// app/page.tsx
export default function Page() {
  return <h1 className="text-3xl font-bold underline">Hello, Next.js!</h1>
}
```

### Usage with Turbopack

As of Next.js 13.1, Tailwind CSS and PostCSS are supported with Turbopack.

## Sass

Next.js has built-in support for integrating with Sass after the package is installed using both the `.scss` and `.sass` extensions. You can use component-level Sass via CSS Modules and the `.module.scss` or `.module.sass` extension.

First, install sass:

```bash
npm install --save-dev sass
```

> Good to know:
> - Sass supports two different syntaxes, each with their own extension. The `.scss` extension requires you use the SCSS syntax, while the `.sass` extension requires you use the Indented Syntax ("Sass").
> - If you're not sure which to choose, start with the `.scss` extension which is a superset of CSS, and doesn't require you learn the Indented Syntax ("Sass").

### Customizing Sass Options

If you want to configure the Sass compiler, use `sassOptions` in `next.config.js`.

```javascript
// next.config.js
const path = require('path')
 
module.exports = {
  sassOptions: {
    includePaths: [path.join(__dirname, 'styles')],
  },
}
```

### Sass Variables

Next.js supports Sass variables exported from CSS Module files.

For example, using the exported `primaryColor` Sass variable:

```scss
// app/variables.module.scss
$primary-color: #64ff00;
 
:export {
  primaryColor: $primary-color;
}
```

```javascript
// app/page.js
// maps to root `/` URL
 
import variables from './variables.module.scss'
 
export default function Page() {
  return <h1 style={{ color: variables.primaryColor }}>Hello, Next.js!</h1>
}
```

## CSS-in-JS

> Warning: CSS-in-JS libraries which require runtime JavaScript are not currently supported in Server Components. Using CSS-in-JS with newer React features like Server Components and Streaming requires library authors to support the latest version of React, including concurrent rendering.

We're working with the React team on upstream APIs to handle CSS and JavaScript assets with support for React Server Components and streaming architecture.

The following libraries are supported in Client Components in the app directory (alphabetical):

- ant-design
- chakra-ui
- @fluentui/react-components
- kuma-ui
- @mui/material
- @mui/joy
- pandacss
- styled-jsx
- styled-components
- stylex
- tamagui
- tss-react
- vanilla-extract

The following are currently working on support:

- emotion

> Good to know: We're testing out different CSS-in-JS libraries and we'll be adding more examples for libraries that support React 18 features and/or the app directory.

If you want to style Server Components, we recommend using CSS Modules or other solutions that output CSS files, like PostCSS or Tailwind CSS.

### Configuring CSS-in-JS in app

Configuring CSS-in-JS is a three-step opt-in process that involves:

1. A style registry to collect all CSS rules in a render.
2. The new `useServerInsertedHTML` hook to inject rules before any content that might use them.
3. A Client Component that wraps your app with the style registry during initial server-side rendering.

#### styled-jsx

Using styled-jsx in Client Components requires using v5.1.0. First, create a new registry:

```typescript
// app/registry.tsx
'use client'
 
import React, { useState } from 'react'
import { useServerInsertedHTML } from 'next/navigation'
import { StyleRegistry, createStyleRegistry } from 'styled-jsx'
 
export default function StyledJsxRegistry({
  children,
}: {
  children: React.ReactNode
}) {
  // Only create stylesheet once with lazy initial state
  // x-ref: https://reactjs.org/docs/hooks-reference.html#lazy-initial-state
  const [jsxStyleRegistry] = useState(() => createStyleRegistry())
 
  useServerInsertedHTML(() => {
    const styles = jsxStyleRegistry.styles()
    jsxStyleRegistry.flush()
    return <>{styles}</>
  })
 
  return <StyleRegistry registry={jsxStyleRegistry}>{children}</StyleRegistry>
}
```

Then, wrap your root layout with the registry:

```typescript
// app/layout.tsx
import StyledJsxRegistry from './registry'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <StyledJsxRegistry>{children}</StyledJsxRegistry>
      </body>
    </html>
  )
}
```

View an example [here](https://github.com/vercel/app-playground/tree/main/app/styling/styled-jsx).

#### Styled Components

Below is an example of how to configure styled-components@6 or newer:

First, enable styled-components in `next.config.js`.

```javascript
// next.config.js
module.exports = {
  compiler: {
    styledComponents: true,
  },
}
```

Then, use the styled-components API to create a global registry component to collect all CSS style rules generated during a render, and a function to return those rules. Then use the `useServerInsertedHTML` hook to inject the styles collected in the registry into the `<head>` HTML tag in the root layout.

```typescript
// lib/registry.tsx
'use client'
 
import React, { useState } from 'react'
import { useServerInsertedHTML } from 'next/navigation'
import { ServerStyleSheet, StyleSheetManager } from 'styled-components'
 
export default function StyledComponentsRegistry({
  children,
}: {
  children: React.ReactNode
}) {
  // Only create stylesheet once with lazy initial state
  // x-ref: https://reactjs.org/docs/hooks-reference.html#lazy-initial-state
  const [styledComponentsStyleSheet] = useState(() => new ServerStyleSheet())
 
  useServerInsertedHTML(() => {
    const styles = styledComponentsStyleSheet.getStyleElement()
    styledComponentsStyleSheet.instance.clearTag()
    return <>{styles}</>
  })
 
  if (typeof window !== 'undefined') return <>{children}</>
 
  return (
    <StyleSheetManager sheet={styledComponentsStyleSheet.instance}>
      {children}
    </StyleSheetManager>
  )
}
```

Wrap the children of the root layout with the style registry component:

```typescript
// app/layout.tsx
import StyledComponentsRegistry from './lib/registry'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <StyledComponentsRegistry>{children}</StyledComponentsRegistry>
      </body>
    </html>
  )
}
```

View an example [here](https://github.com/vercel/app-playground/tree/main/app/styling/styled-components).

> Good to know:
> - During server rendering, styles will be extracted to a global registry and flushed to the `<head>` of your HTML. This ensures the style rules are placed before any content that might use them. In the future, we may use an upcoming React feature to determine where to inject the styles.
> - During streaming, styles from each chunk will be collected and appended to existing styles. After client-side hydration is complete, styled-components will take over as usual and inject any further dynamic styles.
> - We specifically use a Client Component at the top level of the tree for the style registry because it's more efficient to extract CSS rules this way. It avoids re-generating styles on subsequent server renders, and prevents them from being sent in the Server Component payload.
> - For advanced use cases where you need to configure individual properties of styled-components compilation, you can read our [Next.js styled-components API reference](https://nextjs.org/docs/app/api-reference/next-config-js/compiler#styled-components) to learn more.


# Optimizations

Next.js comes with a variety of built-in optimizations designed to improve your application's speed and Core Web Vitals. This guide will cover the optimizations you can leverage to enhance your user experience.

## Built-in Components

Built-in components abstract away the complexity of implementing common UI optimizations. These components are:

- **Images**: Built on the native `<img>` element. The Image Component optimizes images for performance by lazy loading and automatically resizing images based on device size.
- **Link**: Built on the native `<a>` tags. The Link Component prefetches pages in the background, for faster and smoother page transitions.
- **Scripts**: Built on the native `<script>` tags. The Script Component gives you control over loading and execution of third-party scripts.

## Metadata

Metadata helps search engines understand your content better (which can result in better SEO), and allows you to customize how your content is presented on social media, helping you create a more engaging and consistent user experience across various platforms.

The Metadata API in Next.js allows you to modify the `<head>` element of a page. You can configure metadata in two ways:

1. **Config-based Metadata**: Export a static metadata object or a dynamic generateMetadata function in a layout.js or page.js file.
2. **File-based Metadata**: Add static or dynamically generated special files to route segments.

Additionally, you can create dynamic Open Graph Images using JSX and CSS with imageResponse constructor.

## Static Assets

Next.js `/public` folder can be used to serve static assets like images, fonts, and other files. Files inside `/public` can also be cached by CDN providers so that they are delivered efficiently.

## Analytics and Monitoring

For large applications, Next.js integrates with popular analytics and monitoring tools to help you understand how your application is performing. Learn more in the [OpenTelemetry and Instrumentation guides](https://nextjs.org/docs/app/building-your-application/optimizing/analytics).

## Image Optimization

According to Web Almanac, images account for a huge portion of the typical website's page weight and can have a sizable impact on your website's LCP performance.

The Next.js Image component extends the HTML `<img>` element with features for automatic image optimization:

- **Size Optimization**: Automatically serve correctly sized images for each device, using modern image formats like WebP and AVIF.
- **Visual Stability**: Prevent layout shift automatically when images are loading.
- **Faster Page Loads**: Images are only loaded when they enter the viewport using native browser lazy loading, with optional blur-up placeholders.
- **Asset Flexibility**: On-demand image resizing, even for images stored on remote servers

### Usage

```javascript
import Image from 'next/image'
```

You can then define the `src` for your image (either local or remote).

#### Local Images

To use a local image, import your `.jpg`, `.png`, or `.webp` image files.

Next.js will automatically determine the intrinsic width and height of your image based on the imported file. These values are used to determine the image ratio and prevent Cumulative Layout Shift while your image is loading.

```javascript
// app/page.js

import Image from 'next/image'
import profilePic from './me.png'
 
export default function Page() {
  return (
    <Image
      src={profilePic}
      alt="Picture of the author"
      // width={500} automatically provided
      // height={500} automatically provided
      // blurDataURL="data:..." automatically provided
      // placeholder="blur" // Optional blur-up while loading
    />
  )
}
```

> **Warning**: Dynamic `await import()` or `require()` are not supported. The import must be static so it can be analyzed at build time.

#### Remote Images

To use a remote image, the `src` property should be a URL string.

Since Next.js does not have access to remote files during the build process, you'll need to provide the `width`, `height` and optional `blurDataURL` props manually.

The `width` and `height` attributes are used to infer the correct aspect ratio of image and avoid layout shift from the image loading in. The `width` and `height` do not determine the rendered size of the image file. Learn more about [Image Sizing](#image-sizing).

```javascript
// app/page.js

import Image from 'next/image'
 
export default function Page() {
  return (
    <Image
      src="https://s3.amazonaws.com/my-bucket/profile.png"
      alt="Picture of the author"
      width={500}
      height={500}
    />
  )
}
```

To safely allow optimizing images, define a list of supported URL patterns in `next.config.js`. Be as specific as possible to prevent malicious usage. For example, the following configuration will only allow images from a specific AWS S3 bucket:

```javascript
// next.config.js

module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 's3.amazonaws.com',
        port: '',
        pathname: '/my-bucket/**',
      },
    ],
  },
}
```

Learn more about [remotePatterns configuration](https://nextjs.org/docs/api-reference/next/image#remote-patterns). If you want to use relative URLs for the image `src`, use a loader.

### Domains

Sometimes you may want to optimize a remote image, but still use the built-in Next.js Image Optimization API. To do this, leave the loader at its default setting and enter an absolute URL for the Image `src` prop.

To protect your application from malicious users, you must define a list of remote hostnames you intend to use with the `next/image` component.

Learn more about [remotePatterns configuration](https://nextjs.org/docs/api-reference/next/image#remote-patterns).

### Loaders

Note that in the example earlier, a partial URL (`"/me.png"`) is provided for a local image. This is possible because of the loader architecture.

A loader is a function that generates the URLs for your image. It modifies the provided `src`, and generates multiple URLs to request the image at different sizes. These multiple URLs are used in the automatic `srcset` generation, so that visitors to your site will be served an image that is the right size for their viewport.

The default loader for Next.js applications uses the built-in Image Optimization API, which optimizes images from anywhere on the web, and then serves them directly from the Next.js web server. If you would like to serve your images directly from a CDN or image server, you can write your own loader function with a few lines of JavaScript.

You can define a loader per-image with the `loader` prop, or at the application level with the `loaderFile` configuration.

### Priority

You should add the `priority` property to the image that will be the Largest Contentful Paint (LCP) element for each page. Doing so allows Next.js to specially prioritize the image for loading (e.g. through preload tags or priority hints), leading to a meaningful boost in LCP.

The LCP element is typically the largest image or text block visible within the viewport of the page. When you run `next dev`, you'll see a console warning if the LCP element is an `<Image>` without the `priority` property.

Once you've identified the LCP image, you can add the property like this:

```javascript
// app/page.js

import Image from 'next/image'
import profilePic from '../public/me.png'
 
export default function Page() {
  return <Image src={profilePic} alt="Picture of the author" priority />
}
```

See more about priority in the [next/image component documentation](https://nextjs.org/docs/api-reference/next/image#priority).

### Image Sizing

One of the ways that images most commonly hurt performance is through layout shift, where the image pushes other elements around on the page as it loads in. This performance problem is so annoying to users that it has its own Core Web Vital, called Cumulative Layout Shift. The way to avoid image-based layout shifts is to always size your images. This allows the browser to reserve precisely enough space for the image before it loads.

Because `next/image` is designed to guarantee good performance results, it cannot be used in a way that will contribute to layout shift, and must be sized in one of three ways:

1. Automatically, using a static import
2. Manually, by including a `width` and `height` property used to determine the image's aspect ratio.
3. Implicitly, by using `fill` which causes the image to expand to fill its parent element.

#### What if I don't know the size of my images?

If you are accessing images from a source without knowledge of the images' sizes, there are several things you can do:

1. **Use `fill`**

   The `fill` prop allows your image to be sized by its parent element. Consider using CSS to give the image's parent element space on the page along `sizes` prop to match any media query break points. You can also use `object-fit` with `fill`, `contain`, or `cover`, and `object-position` to define how the image should occupy that space.

2. **Normalize your images**

   If you're serving images from a source that you control, consider modifying your image pipeline to normalize the images to a specific size.

3. **Modify your API calls**

   If your application is retrieving image URLs using an API call (such as to a CMS), you may be able to modify the API call to return the image dimensions along with the URL.

If none of the suggested methods works for sizing your images, the `next/image` component is designed to work well on a page alongside standard `<img>` elements.

### Styling

Styling the Image component is similar to styling a normal `<img>` element, but there are a few guidelines to keep in mind:

- Use `className` or `style`, not `styled-jsx`.
  - In most cases, we recommend using the `className` prop. This can be an imported CSS Module, a global stylesheet, etc.
  - You can also use the `style` prop to assign inline styles.
  - You cannot use `styled-jsx` because it's scoped to the current component (unless you mark the style as global).
- When using `fill`, the parent element must have `position: relative`
  - This is necessary for the proper rendering of the image element in that layout mode.
- When using `fill`, the parent element must have `display: block`
  - This is the default for `<div>` elements but should be specified otherwise.

### Examples

#### Responsive

Responsive image filling the width and height of its parent container:

```javascript
import Image from 'next/image'
import mountains from '../public/mountains.jpg'
 
export default function Responsive() {
  return (
    <div style={{ display: 'flex', flexDirection: 'column' }}>
      <Image
        alt="Mountains"
        // Importing an image will
        // automatically set the width and height
        src={mountains}
        sizes="100vw"
        // Make the image display full width
        style={{
          width: '100%',
          height: 'auto',
        }}
      />
    </div>
  )
}
```

#### Fill Container

Grid of images filling parent container width:

```javascript
import Image from 'next/image'
import mountains from '../public/mountains.jpg'
 
export default function Fill() {
  return (
    <div
      style={{
        display: 'grid',
        gridGap: '8px',
        gridTemplateColumns: 'repeat(auto-fit, minmax(400px, auto))',
      }}
    >
      <div style={{ position: 'relative', height: '400px' }}>
        <Image
          alt="Mountains"
          src={mountains}
          fill
          sizes="(min-width: 808px) 50vw, 100vw"
          style={{
            objectFit: 'cover', // cover, contain, none
          }}
        />
      </div>
      {/* And more images in the grid... */}
    </div>
  )
}
```

#### Background Image

Background image taking full width and height of page:

```javascript
import Image from 'next/image'
import mountains from '../public/mountains.jpg'
 
export default function Background() {
  return (
    <Image
      alt="Mountains"
      src={mountains}
      placeholder="blur"
      quality={100}
      fill
      sizes="100vw"
      style={{
        objectFit: 'cover',
      }}
    />
  )
}
```

For examples of the Image component used with the various styles, see the [Image Component Demo](https://image-component.nextjs.gallery/).

### Other Properties

View all properties available to the `next/image` component in the [documentation](https://nextjs.org/docs/api-reference/next/image).

### Configuration

The `next/image` component and Next.js Image Optimization API can be configured in the `next.config.js` file. These configurations allow you to enable remote images, define custom image breakpoints, change caching behavior and more.

## Video Optimization

This page outlines how to use videos with Next.js applications, showing how to store and display video files without affecting performance.

### Using `<video>` and `<iframe>`

Videos can be embedded on the page using the HTML `<video>` tag for direct video files and `<iframe>` for external platform-hosted videos.

#### `<video>`

The HTML `<video>` tag can embed self-hosted or directly served video content, allowing full control over the playback and appearance.

```jsx
// app/ui/video.jsx

export function Video() {
  return (
    <video width="320" height="240" controls preload="none">
      <source src="/path/to/video.mp4" type="video/mp4" />
      <track
        src="/path/to/captions.vtt"
        kind="subtitles"
        srcLang="en"
        label="English"
      />
      Your browser does not support the video tag.
    </video>
  )
}
```

##### Common `<video>` tag attributes

| Attribute | Description | Example Value |
|-----------|-------------|---------------|
| src | Specifies the source of the video file. | `<video src="/path/to/video.mp4" />` |
| width | Sets the width of the video player. | `<video width="320" />` |
| height | Sets the height of the video player. | `<video height="240" />` |
| controls | If present, it displays the default set of playback controls. | `<video controls />` |
| autoPlay | Automatically starts playing the video when the page loads. Note: Autoplay policies vary across browsers. | `<video autoPlay />` |
| loop | Loops the video playback. | `<video loop />` |
| muted | Mutes the audio by default. Often used with autoPlay. | `<video muted />` |
| preload | Specifies how the video is preloaded. Values: none, metadata, auto. | `<video preload="none" />` |
| playsInline | Enables inline playback on iOS devices, often necessary for autoplay to work on iOS Safari. | `<video playsInline />` |

**Good to know:** When using the `autoPlay` attribute, it is important to also include the `muted` attribute to ensure the video plays automatically in most browsers and the `playsInline` attribute for compatibility with iOS devices.

For a comprehensive list of video attributes, refer to the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video#attributes).

##### Video best practices

- **Fallback Content:** When using the `<video>` tag, include fallback content inside the tag for browsers that do not support video playback.
- **Subtitles or Captions:** Include subtitles or captions for users who are deaf or hard of hearing. Utilize the `<track>` tag with your `<video>` elements to specify caption file sources.
- **Accessible Controls:** Standard HTML5 video controls are recommended for keyboard navigation and screen reader compatibility. For advanced needs, consider third-party players like react-player or video.js, which offer accessible controls and consistent browser experience.

#### `<iframe>`

The HTML `<iframe>` tag allows you to embed videos from external platforms like YouTube or Vimeo.

```jsx
// app/page.jsx

export default function Page() {
  return (
    <iframe
      src="https://www.youtube.com/watch?v=gfU1iZnjRZM"
      frameborder="0"
      allowfullscreen
    />
  )
}
```

##### Common `<iframe>` tag attributes

| Attribute | Description | Example Value |
|-----------|-------------|---------------|
| src | The URL of the page to embed. | `<iframe src="https://example.com" />` |
| width | Sets the width of the iframe. | `<iframe width="500" />` |
| height | Sets the height of the iframe. | `<iframe height="300" />` |
| frameborder | Specifies whether or not to display a border around the iframe. | `<iframe frameborder="0" />` |
| allowfullscreen | Allows the iframe content to be displayed in full-screen mode. | `<iframe allowfullscreen />` |
| sandbox | Enables an extra set of restrictions on the content within the iframe. | `<iframe sandbox />` |
| loading | Optimize loading behavior (e.g., lazy loading). | `<iframe loading="lazy" />` |
| title | Provides a title for the iframe to support accessibility. | `<iframe title="Description" />` |

For a comprehensive list of iframe attributes, refer to the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#attributes).

### Choosing a video embedding method

There are two ways you can embed videos in your Next.js application:

1. **Self-hosted or direct video files:** Embed self-hosted videos using the `<video>` tag for scenarios requiring detailed control over the player's functionality and appearance. This integration method within Next.js allows for customization and control of your video content.

2. **Using video hosting services (YouTube, Vimeo, etc.):** For video hosting services like YouTube or Vimeo, you'll embed their iframe-based players using the `<iframe>` tag. While this method limits some control over the player, it offers ease of use and features provided by these platforms.

Choose the embedding method that aligns with your application's requirements and the user experience you aim to deliver.

### Embedding externally hosted videos

To embed videos from external platforms, you can use Next.js to fetch the video information and React Suspense to handle the fallback state while loading.

1. Create a Server Component for video embedding

The first step is to create a Server Component that generates the appropriate iframe for embedding the video. This component will fetch the source URL for the video and render the iframe.

```jsx
// app/ui/video-component.jsx

export default async function VideoComponent() {
  const src = await getVideoSrc()
 
  return <iframe src={src} frameborder="0" allowfullscreen />
}
```

2. Stream the video component using React Suspense

After creating the Server Component to embed the video, the next step is to stream the component using React Suspense.

```jsx
// app/page.jsx

import { Suspense } from 'react'
import VideoComponent from '../ui/VideoComponent.jsx'
 
export default function Page() {
  return (
    <section>
      <Suspense fallback={<p>Loading video...</p>}>
        <VideoComponent />
      </Suspense>
      {/* Other content of the page */}
    </section>
  )
}
```

**Good to know:** When embedding videos from external platforms, consider the following best practices:

- Ensure the video embeds are responsive. Use CSS to make the iframe or video player adapt to different screen sizes.
- Implement strategies for loading videos based on network conditions, especially for users with limited data plans.

This approach results in a better user experience as it prevents the page from blocking, meaning the user can interact with the page while the video component streams in.

For a more engaging and informative loading experience, consider using a loading skeleton as the fallback UI. So instead of showing a simple loading message, you can show a skeleton that resembles the video player like this:

```jsx
// app/page.jsx

import { Suspense } from 'react'
import VideoComponent from '../ui/VideoComponent.jsx'
import VideoSkeleton from '../ui/VideoSkeleton.jsx'
 
export default function Page() {
  return (
    <section>
      <Suspense fallback={<VideoSkeleton />}>
        <VideoComponent />
      </Suspense>
      {/* Other content of the page */}
    </section>
  )
}
```

### Self-hosted videos

Self-hosting videos may be preferable for several reasons:

- **Complete control and independence:** Self-hosting gives you direct management over your video content, from playback to appearance, ensuring full ownership and control, free from external platform constraints.
- **Customization for specific needs:** Ideal for unique requirements, like dynamic background videos, it allows for tailored customization to align with design and functional needs.
- **Performance and scalability considerations:** Choose storage solutions that are both high-performing and scalable, to support increasing traffic and content size effectively.
- **Cost and integration:** Balance the costs of storage and bandwidth with the need for easy integration into your Next.js framework and broader tech ecosystem.

#### Using Vercel Blob for video hosting

Vercel Blob offers an efficient way to host videos, providing a scalable cloud storage solution that works well with Next.js. Here's how you can host a video using Vercel Blob:

1. Uploading a video to Vercel Blob

In your Vercel dashboard, navigate to the "Storage" tab and select your Vercel Blob store. In the Blob table's upper-right corner, find and click the "Upload" button. Then, choose the video file you wish to upload. After the upload completes, the video file will appear in the Blob table.

Alternatively, you can upload your video using a server action. For detailed instructions, refer to the Vercel documentation on server-side uploads. Vercel also supports client-side uploads. This method may be preferable for certain use cases.

2. Displaying the video in Next.js

Once the video is uploaded and stored, you can display it in your Next.js application. Here's an example of how to do this using the `<video>` tag and React Suspense:

```jsx
// app/page.jsx

import { Suspense } from 'react'
import { list } from '@vercel/blob'
 
export default function Page() {
  return (
    <Suspense fallback={<p>Loading video...</p>}>
      <VideoComponent fileName="my-video.mp4" />
    </Suspense>
  )
}
 
async function VideoComponent({ fileName }) {
  const { blobs } = await list({
    prefix: fileName,
    limit: 1,
  })
  const { url } = blobs[0]
 
  return (
    <video controls preload="none" aria-label="Video player">
      <source src={url} type="video/mp4" />
      Your browser does not support the video tag.
    </video>
  )
}
```

In this approach, the page uses the video's @vercel/blob URL to display the video using the VideoComponent. React Suspense is used to show a fallback until the video URL is fetched and the video is ready to be displayed.

#### Adding subtitles to your video

If you have subtitles for your video, you can easily add them using the `<track>` element inside your `<video>` tag. You can fetch the subtitle file from Vercel Blob in a similar way as the video file. Here's how you can update the `<VideoComponent>` to include subtitles.

```jsx
// app/page.jsx

async function VideoComponent({ fileName }) {
  const { blobs } = await list({
    prefix: fileName,
    limit: 2,
  })
  const { url } = blobs[0]
  const { url: captionsUrl } = blobs[1]
 
  return (
    <video controls preload="none" aria-label="Video player">
      <source src={url} type="video/mp4" />
      <track src={captionsUrl} kind="subtitles" srcLang="en" label="English" />
      Your browser does not support the video tag.
    </video>
  )
}
```

By following this approach, you can effectively self-host and integrate videos into your Next.js applications.

### Resources

To continue learning more about video optimization and best practices, please refer to the following resources:

- **Understanding video formats and codecs:** Choose the right format and codec, like MP4 for compatibility or WebM for web optimization, for your video needs. For more details, see [Mozilla's guide on video codecs](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Video_codecs).
- **Video compression:** Use tools like FFmpeg to effectively compress videos, balancing quality with file size. Learn about compression techniques at [FFmpeg's official website](https://ffmpeg.org/).
- **Resolution and bitrate adjustment:** Adjust resolution and bitrate based on the viewing platform, with lower settings for mobile devices.
- **Content Delivery Networks (CDNs):** Utilize a CDN to enhance video delivery speed and manage high traffic. When using some storage solutions, such as Vercel Blob, CDN functionality is automatically handled for you. Learn more about [CDNs and their benefits](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/).

Explore these video streaming platforms for integrating video into your Next.js projects:

1. **Open source next-video component**
   - Provides a `<Video>` component for Next.js, compatible with various hosting services including Vercel Blob, S3, Backblaze, and Mux.
   - [Detailed documentation](https://next-video.dev/) for using next-video.dev with different hosting services.

2. **Cloudinary Integration**
   - [Official documentation and integration guide](https://cloudinary.com/documentation/next_js_integration) for using Cloudinary with Next.js.
   - Includes a `<CldVideoPlayer>` component for drop-in video support.
   - Find [examples of integrating Cloudinary with Next.js](https://github.com/cloudinary-community/cloudinary-examples/tree/main/examples/nextjs-pages-router) including Adaptive Bitrate Streaming.
   - Other Cloudinary libraries including a [Node.js SDK](https://cloudinary.com/documentation/node_integration) are also available.

3. **Mux Video API**
   - Mux provides a [starter template](https://github.com/muxinc/next-video-site-starter) for creating a video course with Mux and Next.js.
   - Learn about [Mux's recommendations](https://www.mux.com/blog/the-best-way-to-embed-video-in-next-js) for embedding high-performance video for your Next.js application.
   - Explore an [example project](https://github.com/muxinc/examples/tree/main/next-js) demonstrating Mux with Next.js.

4. **Fastly**
   - Learn more about integrating [Fastly's solutions](https://www.fastly.com/products/media-and-streaming) for video on demand and streaming media into Next.js.

## Font Optimization

`next/font` will automatically optimize your fonts (including custom fonts) and remove external network requests for improved privacy and performance.

`next/font` includes built-in automatic self-hosting for any font file. This means you can optimally load web fonts with zero layout shift, thanks to the underlying CSS `size-adjust` property used.

This new font system also allows you to conveniently use all Google Fonts with performance and privacy in mind. CSS and font files are downloaded at build time and self-hosted with the rest of your static assets. No requests are sent to Google by the browser.

### Google Fonts

Automatically self-host any Google Font. Fonts are included in the deployment and served from the same domain as your deployment. No requests are sent to Google by the browser.

Get started by importing the font you would like to use from `next/font/google` as a function. We recommend using variable fonts for the best performance and flexibility.

```typescript
// app/layout.tsx
import { Inter } from 'next/font/google'

// If loading a variable font, you don't need to specify the font weight
const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  )
}
```

If you can't use a variable font, you will need to specify a weight:

```typescript
// app/layout.tsx
import { Roboto } from 'next/font/google'

const roboto = Roboto({
  weight: '400',
  subsets: ['latin'],
  display: 'swap',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={roboto.className}>
      <body>{children}</body>
    </html>
  )
}
```

You can specify multiple weights and/or styles by using an array:

```typescript
// app/layout.js
const roboto = Roboto({
  weight: ['400', '700'],
  style: ['normal', 'italic'],
  subsets: ['latin'],
  display: 'swap',
})
```

**Good to know:** Use an underscore (_) for font names with multiple words. E.g. Roboto Mono should be imported as `Roboto_Mono`.

### Specifying a subset

Google Fonts are automatically subset. This reduces the size of the font file and improves performance. You'll need to define which of these subsets you want to preload. Failing to specify any subsets while `preload` is `true` will result in a warning.

This can be done by adding it to the function call:

```typescript
// app/layout.tsx
const inter = Inter({ subsets: ['latin'] })
```

View the [Font API Reference](https://nextjs.org/docs/app/api-reference/components/font) for more information.

### Using Multiple Fonts

You can import and use multiple fonts in your application. There are two approaches you can take.

The first approach is to create a utility function that exports a font, imports it, and applies its `className` where needed. This ensures the font is preloaded only when it's rendered:

```typescript
// app/fonts.ts
import { Inter, Roboto_Mono } from 'next/font/google'

export const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
})

export const roboto_mono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
})
```

```typescript
// app/layout.tsx
import { inter } from './fonts'

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={inter.className}>
      <body>
        <div>{children}</div>
      </body>
    </html>
  )
}
```

```typescript
// app/page.tsx
import { roboto_mono } from './fonts'

export default function Page() {
  return (
    <>
      <h1 className={roboto_mono.className}>My page</h1>
    </>
  )
}
```

In the example above, Inter will be applied globally, and Roboto Mono can be imported and applied as needed.

Alternatively, you can create a CSS variable and use it with your preferred CSS solution:

```typescript
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google'
import styles from './global.css'

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',
  display: 'swap',
})

const roboto_mono = Roboto_Mono({
  subsets: ['latin'],
  variable: '--font-roboto-mono',
  display: 'swap',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={`${inter.variable} ${roboto_mono.variable}`}>
      <body>
        <h1>My App</h1>
        <div>{children}</div>
      </body>
    </html>
  )
}
```

```css
/* app/global.css */
html {
  font-family: var(--font-inter);
}

h1 {
  font-family: var(--font-roboto-mono);
}
```

In the example above, Inter will be applied globally, and any `<h1>` tags will be styled with Roboto Mono.

**Recommendation:** Use multiple fonts conservatively since each new font is an additional resource the client has to download.

### Local Fonts

Import `next/font/local` and specify the `src` of your local font file. We recommend using variable fonts for the best performance and flexibility.

```typescript
// app/layout.tsx
import localFont from 'next/font/local'

// Font files can be colocated inside of `app`
const myFont = localFont({
  src: './my-font.woff2',
  display: 'swap',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={myFont.className}>
      <body>{children}</body>
    </html>
  )
}
```

If you want to use multiple files for a single font family, `src` can be an array:

```typescript
const roboto = localFont({
  src: [
    {
      path: './Roboto-Regular.woff2',
      weight: '400',
      style: 'normal',
    },
    {
      path: './Roboto-Italic.woff2',
      weight: '400',
      style: 'italic',
    },
    {
      path: './Roboto-Bold.woff2',
      weight: '700',
      style: 'normal',
    },
    {
      path: './Roboto-BoldItalic.woff2',
      weight: '700',
      style: 'italic',
    },
  ],
})
```

View the [Font API Reference](https://nextjs.org/docs/app/api-reference/components/font) for more information.

### With Tailwind CSS

`next/font` can be used with Tailwind CSS through a CSS variable.

In the example below, we use the font Inter from `next/font/google` (you can use any font from Google or Local Fonts). Load your font with the `variable` option to define your CSS variable name and assign it to `inter`. Then, use `inter.variable` to add the CSS variable to your HTML document.

```typescript
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
})

const roboto_mono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-roboto-mono',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={`${inter.variable} ${roboto_mono.variable}`}>
      <body>{children}</body>
    </html>
  )
}
```

Finally, add the CSS variable to your Tailwind CSS config:

```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
    './app/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      fontFamily: {
        sans: ['var(--font-inter)'],
        mono: ['var(--font-roboto-mono)'],
      },
    },
  },
  plugins: [],
}
```

You can now use the `font-sans` and `font-mono` utility classes to apply the font to your elements.

### Preloading

When a font function is called on a page of your site, it is not globally available and preloaded on all routes. Rather, the font is only preloaded on the related routes based on the type of file where it is used:

- If it's a unique page, it is preloaded on the unique route for that page.
- If it's a layout, it is preloaded on all the routes wrapped by the layout.
- If it's the root layout, it is preloaded on all routes.

### Reusing fonts

Every time you call the `localFont` or Google font function, that font is hosted as one instance in your application. Therefore, if you load the same font function in multiple files, multiple instances of the same font are hosted. In this situation, it is recommended to do the following:

1. Call the font loader function in one shared file
2. Export it as a constant
3. Import the constant in each file where you would like to use this font

## Metadata

Next.js has a Metadata API that can be used to define your application metadata (e.g. `meta` and `link` tags inside your HTML `head` element) for improved SEO and web shareability.

There are two ways you can add metadata to your application:

1. **Config-based Metadata**: Export a static metadata object or a dynamic `generateMetadata` function in a `layout.js` or `page.js` file.
2. **File-based Metadata**: Add static or dynamically generated special files to route segments.

With both these options, Next.js will automatically generate the relevant `<head>` elements for your pages. You can also create dynamic OG images using the `ImageResponse` constructor.

### Static Metadata

To define static metadata, export a `Metadata` object from a `layout.js` or static `page.js` file.

```typescript
// layout.tsx | page.tsx
import type { Metadata } from 'next'

export const metadata: Metadata = {
  title: '...',
  description: '...',
}

export default function Page() {}
```

For all the available options, see the API Reference.

### Dynamic Metadata

You can use `generateMetadata` function to fetch metadata that requires dynamic values.

```typescript
// app/products/[id]/page.tsx
import type { Metadata, ResolvingMetadata } from 'next'

type Props = {
  params: { id: string }
  searchParams: { [key: string]: string | string[] | undefined }
}

export async function generateMetadata(
  { params, searchParams }: Props,
  parent: ResolvingMetadata
): Promise<Metadata> {
  // read route params
  const id = params.id

  // fetch data
  const product = await fetch(`https://.../${id}`).then((res) => res.json())

  // optionally access and extend (rather than replace) parent metadata
  const previousImages = (await parent).openGraph?.images || []

  return {
    title: product.title,
    openGraph: {
      images: ['/some-specific-page-image.jpg', ...previousImages],
    },
  }
}

export default function Page({ params, searchParams }: Props) {}
```

For all the available params, see the API Reference.

**Good to know:**

- Both static and dynamic metadata through `generateMetadata` are only supported in Server Components.
- `fetch` requests are automatically memoized for the same data across `generateMetadata`, `generateStaticParams`, Layouts, Pages, and Server Components. React `cache` can be used if `fetch` is unavailable.
- Next.js will wait for data fetching inside `generateMetadata` to complete before streaming UI to the client. This guarantees the first part of a streamed response includes `<head>` tags.

### File-based metadata

These special files are available for metadata:

- `favicon.ico`, `apple-icon.jpg`, and `icon.jpg`
- `opengraph-image.jpg` and `twitter-image.jpg`
- `robots.txt`
- `sitemap.xml`

You can use these for static metadata, or you can programmatically generate these files with code.

For implementation and examples, see the Metadata Files API Reference and Dynamic Image Generation.

### Behavior

File-based metadata has the higher priority and will override any config-based metadata.

### Default Fields

There are two default meta tags that are always added even if a route doesn't define metadata:

1. The `meta charset` tag sets the character encoding for the website.
2. The `meta viewport` tag sets the viewport width and scale for the website to adjust for different devices.

```html
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

**Good to know:** You can overwrite the default viewport meta tag.

### Ordering

Metadata is evaluated in order, starting from the root segment down to the segment closest to the final `page.js` segment. For example:

1. `app/layout.tsx` (Root Layout)
2. `app/blog/layout.tsx` (Nested Blog Layout)
3. `app/blog/[slug]/page.tsx` (Blog Page)

### Merging

Following the evaluation order, Metadata objects exported from multiple segments in the same route are shallowly merged together to form the final metadata output of a route. Duplicate keys are replaced based on their ordering.

This means metadata with nested fields such as `openGraph` and `robots` that are defined in an earlier segment are overwritten by the last segment to define them.

#### Overwriting fields

```javascript
// app/layout.js
export const metadata = {
  title: 'Acme',
  openGraph: {
    title: 'Acme',
    description: 'Acme is a...',
  },
}

// app/blog/page.js
export const metadata = {
  title: 'Blog',
  openGraph: {
    title: 'Blog',
  },
}

// Output:
// <title>Blog</title>
// <meta property="og:title" content="Blog" />
```

In the example above:

- `title` from `app/layout.js` is replaced by `title` in `app/blog/page.js`.
- All `openGraph` fields from `app/layout.js` are replaced in `app/blog/page.js` because `app/blog/page.js` sets `openGraph` metadata. Note the absence of `openGraph.description`.

If you'd like to share some nested fields between segments while overwriting others, you can pull them out into a separate variable:

```javascript
// app/shared-metadata.js
export const openGraphImage = { images: ['http://...'] }

// app/page.js
import { openGraphImage } from './shared-metadata'

export const metadata = {
  openGraph: {
    ...openGraphImage,
    title: 'Home',
  },
}

// app/about/page.js
import { openGraphImage } from '../shared-metadata'

export const metadata = {
  openGraph: {
    ...openGraphImage,
    title: 'About',
  },
}
```

In the example above, the OG image is shared between `app/layout.js` and `app/about/page.js` while the titles are different.

#### Inheriting fields

```javascript
// app/layout.js
export const metadata = {
  title: 'Acme',
  openGraph: {
    title: 'Acme',
    description: 'Acme is a...',
  },
}

// app/about/page.js
export const metadata = {
  title: 'About',
}

// Output:
// <title>About</title>
// <meta property="og:title" content="Acme" />
// <meta property="og:description" content="Acme is a..." />
```

Notes:

- `title` from `app/layout.js` is replaced by `title` in `app/about/page.js`.
- All `openGraph` fields from `app/layout.js` are inherited in `app/about/page.js` because `app/about/page.js` doesn't set `openGraph` metadata.

### Dynamic Image Generation

The `ImageResponse` constructor allows you to generate dynamic images using JSX and CSS. This is useful for creating social media images such as Open Graph images, Twitter cards, and more.

To use it, you can import `ImageResponse` from `next/og`:

```javascript
// app/about/route.js
import { ImageResponse } from 'next/og'

export async function GET() {
  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 128,
          background: 'white',
          width: '100%',
          height: '100%',
          display: 'flex',
          textAlign: 'center',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        Hello world!
      </div>
    ),
    {
      width: 1200,
      height: 600,
    }
  )
}
```

`ImageResponse` integrates well with other Next.js APIs, including Route Handlers and file-based Metadata. For example, you can use `ImageResponse` in a `opengraph-image.tsx` file to generate Open Graph images at build time or dynamically at request time.

`ImageResponse` supports common CSS properties including flexbox and absolute positioning, custom fonts, text wrapping, centering, and nested images. See the full list of supported CSS properties.

**Good to know:**

- Examples are available in the [Vercel OG Playground](https://og-playground.vercel.app/).
- `ImageResponse` uses `@vercel/og`, Satori, and Resvg to convert HTML and CSS into PNG.
- Only the Edge Runtime is supported. The default Node.js runtime will not work.
- Only flexbox and a subset of CSS properties are supported. Advanced layouts (e.g. `display: grid`) will not work.
- Maximum bundle size of 500KB. The bundle size includes your JSX, CSS, fonts, images, and any other assets. If you exceed the limit, consider reducing the size of any assets or fetching at runtime.
- Only `ttf`, `otf`, and `woff` font formats are supported. To maximize the font parsing speed, `ttf` or `otf` are preferred over `woff`.

### JSON-LD

JSON-LD is a format for structured data that can be used by search engines to understand your content. For example, you can use it to describe a person, an event, an organization, a movie, a book, a recipe, and many other types of entities.

Our current recommendation for JSON-LD is to render structured data as a `<script>` tag in your `layout.js` or `page.js` components. For example:

```typescript
// app/products/[id]/page.tsx
export default async function Page({ params }) {
  const product = await getProduct(params.id)

  const jsonLd = {
    '@context': 'https://schema.org',
    '@type': 'Product',
    name: product.name,
    image: product.image,
    description: product.description,
  }

  return (
    <section>
      {/* Add JSON-LD to your page */}
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      {/* ... */}
    </section>
  )
}
```

You can validate and test your structured data with the [Rich Results Test](https://search.google.com/test/rich-results) for Google or the generic [Schema Markup Validator](https://validator.schema.org/).

You can type your JSON-LD with TypeScript using community packages like `schema-dts`:

```typescript
import { Product, WithContext } from 'schema-dts'

const jsonLd: WithContext<Product> = {
  '@context': 'https://schema.org',
  '@type': 'Product',
  name: 'Next.js Sticker',
  image: 'https://nextjs.org/imgs/sticker.png',
  description: 'Dynamic at the speed of static.',
}
```

## Script Optimization

### Layout Scripts

To load a third-party script for multiple routes, import `next/script` and include the script directly in your layout component:

`app/dashboard/layout.tsx`:

```typescript
import Script from 'next/script'
 
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <>
      <section>{children}</section>
      <Script src="https://example.com/script.js" />
    </>
  )
}
```

The third-party script is fetched when the folder route (e.g. `dashboard/page.js`) or any nested route (e.g. `dashboard/settings/page.js`) is accessed by the user. Next.js will ensure the script will only load once, even if a user navigates between multiple routes in the same layout.

### Application Scripts

To load a third-party script for all routes, import `next/script` and include the script directly in your root layout:

`app/layout.tsx`:

```typescript
import Script from 'next/script'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
      <Script src="https://example.com/script.js" />
    </html>
  )
}
```

This script will load and execute when any route in your application is accessed. Next.js will ensure the script will only load once, even if a user navigates between multiple pages.

**Recommendation:** We recommend only including third-party scripts in specific pages or layouts in order to minimize any unnecessary impact to performance.

### Strategy

Although the default behavior of `next/script` allows you to load third-party scripts in any page or layout, you can fine-tune its loading behavior by using the `strategy` property:

- `beforeInteractive`: Load the script before any Next.js code and before any page hydration occurs.
- `afterInteractive`: (default) Load the script early but after some hydration on the page occurs.
- `lazyOnload`: Load the script later during browser idle time.
- `worker`: (experimental) Load the script in a web worker.

Refer to the `next/script` API reference documentation to learn more about each strategy and their use cases.

### Offloading Scripts To A Web Worker (experimental)

> Warning: The worker strategy is not yet stable and does not yet work with the app directory. Use with caution.

Scripts that use the `worker` strategy are offloaded and executed in a web worker with Partytown. This can improve the performance of your site by dedicating the main thread to the rest of your application code.

This strategy is still experimental and can only be used if the `nextScriptWorkers` flag is enabled in `next.config.js`:

```javascript
module.exports = {
  experimental: {
    nextScriptWorkers: true,
  },
}
```

Then, run `next` (normally `npm run dev` or `yarn dev`) and Next.js will guide you through the installation of the required packages to finish the setup:

```
npm run dev
```

You'll see instructions like these: `Please install Partytown by running npm install @builder.io/partytown`

Once setup is complete, defining `strategy="worker"` will automatically instantiate Partytown in your application and offload the script to a web worker.

`pages/home.tsx`:

```typescript
import Script from 'next/script'
 
export default function Home() {
  return (
    <>
      <Script src="https://example.com/script.js" strategy="worker" />
    </>
  )
}
```

There are a number of trade-offs that need to be considered when loading a third-party script in a web worker. Please see Partytown's tradeoffs documentation for more information.

### Inline Scripts

Inline scripts, or scripts not loaded from an external file, are also supported by the Script component. They can be written by placing the JavaScript within curly braces:

```jsx
<Script id="show-banner">
  {`document.getElementById('banner').classList.remove('hidden')`}
</Script>
```

Or by using the `dangerouslySetInnerHTML` property:

```jsx
<Script
  id="show-banner"
  dangerouslySetInnerHTML={{
    __html: `document.getElementById('banner').classList.remove('hidden')`,
  }}
/>
```

> Warning: An `id` property must be assigned for inline scripts in order for Next.js to track and optimize the script.

### Executing Additional Code

Event handlers can be used with the Script component to execute additional code after a certain event occurs:

- `onLoad`: Execute code after the script has finished loading.
- `onReady`: Execute code after the script has finished loading and every time the component is mounted.
- `onError`: Execute code if the script fails to load.

These handlers will only work when `next/script` is imported and used inside of a Client Component where `"use client"` is defined as the first line of code:

`app/page.tsx`:

```typescript
'use client'
 
import Script from 'next/script'
 
export default function Page() {
  return (
    <>
      <Script
        src="https://example.com/script.js"
        onLoad={() => {
          console.log('Script has loaded')
        }}
      />
    </>
  )
}
```

Refer to the `next/script` API reference to learn more about each event handler and view examples.

### Additional Attributes

There are many DOM attributes that can be assigned to a `<script>` element that are not used by the Script component, like `nonce` or custom data attributes. Including any additional attributes will automatically forward it to the final, optimized `<script>` element that is included in the HTML.

`app/page.tsx`:

```typescript
import Script from 'next/script'
 
export default function Page() {
  return (
    <>
      <Script
        src="https://example.com/script.js"
        id="example-script"
        nonce="XUENAJFW"
        data-test="script"
      />
    </>
  )
}
```

## Package Bundling

Bundling external packages can significantly improve the performance of your application. By default, packages imported inside Server Components and Route Handlers are automatically bundled by Next.js. This page will guide you through how to analyze and further optimize package bundling.

### Analyzing JavaScript bundles

`@next/bundle-analyzer` is a plugin for Next.js that helps you manage the size of your application bundles. It generates a visual report of the size of each package and their dependencies. You can use the information to remove large dependencies, split, or lazy-load your code.

#### Installation

Install the plugin by running the following command:

```bash
npm i @next/bundle-analyzer
# or
yarn add @next/bundle-analyzer
# or
pnpm add @next/bundle-analyzer
```

Then, add the bundle analyzer's settings to your `next.config.js`.

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {}
 
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})
 
module.exports = withBundleAnalyzer(nextConfig)
```

#### Generating a report

Run the following command to analyze your bundles:

```bash
ANALYZE=true npm run build
# or
ANALYZE=true yarn build
# or
ANALYZE=true pnpm build
```

The report will open three new tabs in your browser, which you can inspect. Periodically evaluating your application's bundles can help you maintain application performance over time.

### Optimizing package imports

Some packages, such as icon libraries, can export hundreds of modules, which can cause performance issues in development and production.

You can optimize how these packages are imported by adding the `optimizePackageImports` option to your `next.config.js`. This option will only load the modules you actually use, while still giving you the convenience of writing import statements with many named exports.

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    optimizePackageImports: ['icon-library'],
  },
}
 
module.exports = nextConfig
```

Next.js also optimizes some libraries automatically, thus they do not need to be included in the `optimizePackageImports` list. See the full list.

### Opting specific packages out of bundling

Since packages imported inside Server Components and Route Handlers are automatically bundled by Next.js, you can opt specific packages out of bundling using the `serverExternalPackages` option in your `next.config.js`.

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  serverExternalPackages: ['package-name'],
}
 
module.exports = nextConfig
```

## Lazy Loading

Lazy loading in Next.js helps improve the initial loading performance of an application by decreasing the amount of JavaScript needed to render a route.

It allows you to defer loading of Client Components and imported libraries, and only include them in the client bundle when they're needed. For example, you might want to defer loading a modal until a user clicks to open it.

There are two ways you can implement lazy loading in Next.js:

1. Using Dynamic Imports with `next/dynamic`
2. Using `React.lazy()` with Suspense

By default, Server Components are automatically code split, and you can use streaming to progressively send pieces of UI from the server to the client. Lazy loading applies to Client Components.

### next/dynamic

`next/dynamic` is a composite of `React.lazy()` and Suspense. It behaves the same way in the app and pages directories to allow for incremental migration.

#### Examples

##### Importing Client Components

```javascript
// app/page.js

'use client'
 
import { useState } from 'react'
import dynamic from 'next/dynamic'
 
// Client Components:
const ComponentA = dynamic(() => import('../components/A'))
const ComponentB = dynamic(() => import('../components/B'))
const ComponentC = dynamic(() => import('../components/C'), { ssr: false })
 
export default function ClientComponentExample() {
  const [showMore, setShowMore] = useState(false)
 
  return (
    <div>
      {/* Load immediately, but in a separate client bundle */}
      <ComponentA />
 
      {/* Load on demand, only when/if the condition is met */}
      {showMore && <ComponentB />}
      <button onClick={() => setShowMore(!showMore)}>Toggle</button>
 
      {/* Load only on the client side */}
      <ComponentC />
    </div>
  )
}
```

##### Skipping SSR

When using `React.lazy()` and Suspense, Client Components will be pre-rendered (SSR) by default.

If you want to disable pre-rendering for a Client Component, you can use the `ssr` option set to `false`:

```javascript
const ComponentC = dynamic(() => import('../components/C'), { ssr: false })
```

##### Importing Server Components

```javascript
// app/page.js

import dynamic from 'next/dynamic'
 
// Server Component:
const ServerComponent = dynamic(() => import('../components/ServerComponent'))
 
export default function ServerComponentExample() {
  return (
    <div>
      <ServerComponent />
    </div>
  )
}
```

##### Loading External Libraries

```javascript
// app/page.js

'use client'
 
import { useState } from 'react'
 
const names = ['Tim', 'Joe', 'Bel', 'Lee']
 
export default function Page() {
  const [results, setResults] = useState()
 
  return (
    <div>
      <input
        type="text"
        placeholder="Search"
        onChange={async (e) => {
          const { value } = e.currentTarget
          // Dynamically load fuse.js
          const Fuse = (await import('fuse.js')).default
          const fuse = new Fuse(names)
 
          setResults(fuse.search(value))
        }}
      />
      <pre>Results: {JSON.stringify(results, null, 2)}</pre>
    </div>
  )
}
```

##### Adding a custom loading component

```javascript
// app/page.js

import dynamic from 'next/dynamic'
 
const WithCustomLoading = dynamic(
  () => import('../components/WithCustomLoading'),
  {
    loading: () => <p>Loading...</p>,
  }
)
 
export default function Page() {
  return (
    <div>
      {/* The loading component will be rendered while  <WithCustomLoading/> is loading */}
      <WithCustomLoading />
    </div>
  )
}
```

##### Importing Named Exports

```javascript
// components/hello.js

'use client'
 
export function Hello() {
  return <p>Hello!</p>
}

// app/page.js

import dynamic from 'next/dynamic'
 
const ClientComponent = dynamic(() =>
  import('../components/hello').then((mod) => mod.Hello)
)
```

## Analytics

Next.js has built-in support for measuring and reporting performance metrics. You can either use the `useReportWebVitals` hook to manage reporting yourself, or alternatively, Vercel provides a managed service to automatically collect and visualize metrics for you.

### Build Your Own

```javascript
// app/_components/web-vitals.js

'use client'
 
import { useReportWebVitals } from 'next/web-vitals'
 
export function WebVitals() {
  useReportWebVitals((metric) => {
    console.log(metric)
  })
}

// app/layout.js

import { WebVitals } from './_components/web-vitals'
 
export default function Layout({ children }) {
  return (
    <html>
      <body>
        <WebVitals />
        {children}
      </body>
    </html>
  )
}
```

Since the `useReportWebVitals` hook requires the "use client" directive, the most performant approach is to create a separate component that the root layout imports. This confines the client boundary exclusively to the WebVitals component.

View the API Reference for more information.

### Web Vitals

Web Vitals are a set of useful metrics that aim to capture the user experience of a web page. The following web vitals are all included:

- Time to First Byte (TTFB)
- First Contentful Paint (FCP)
- Largest Contentful Paint (LCP)
- First Input Delay (FID)
- Cumulative Layout Shift (CLS)
- Interaction to Next Paint (INP)

You can handle all the results of these metrics using the `name` property.

```typescript
// app/_components/web-vitals.tsx
// TypeScript

'use client'
 
import { useReportWebVitals } from 'next/web-vitals'
 
export function WebVitals() {
  useReportWebVitals((metric) => {
    switch (metric.name) {
      case 'FCP': {
        // handle FCP results
      }
      case 'LCP': {
        // handle LCP results
      }
      // ...
    }
  })
}
```

### Sending results to external systems

You can send results to any endpoint to measure and track real user performance on your site. For example:

```javascript
useReportWebVitals((metric) => {
  const body = JSON.stringify(metric)
  const url = 'https://example.com/analytics'
 
  // Use `navigator.sendBeacon()` if available, falling back to `fetch()`.
  if (navigator.sendBeacon) {
    navigator.sendBeacon(url, body)
  } else {
    fetch(url, { body, method: 'POST', keepalive: true })
  }
})
```

Good to know: If you use Google Analytics, using the `id` value can allow you to construct metric distributions manually (to calculate percentiles, etc.)

```javascript
useReportWebVitals((metric) => {
  // Use `window.gtag` if you initialized Google Analytics as this example:
  // https://github.com/vercel/next.js/blob/canary/examples/with-google-analytics/pages/_app.js
  window.gtag('event', metric.name, {
    value: Math.round(
      metric.name === 'CLS' ? metric.value * 1000 : metric.value
    ), // values must be integers
    event_label: metric.id, // id unique to current page load
    non_interaction: true, // avoids affecting bounce rate.
  })
})
```

## Instrumentation

Instrumentation is the process of using code to integrate monitoring and logging tools into your application. This allows you to track the performance and behavior of your application, and to debug issues in production.

### Convention

To set up instrumentation, create `instrumentation.ts|js` file in the root directory of your project (or inside the `src` folder if using one).

Then, export a `register` function in the file. This function will be called once when a new Next.js server instance is initiated.

For example, to use Next.js with OpenTelemetry and @vercel/otel:

```typescript
// instrumentation.ts
import { registerOTel } from '@vercel/otel'

export function register() {
  registerOTel('next-app')
}
```

See the [Next.js with OpenTelemetry example](https://github.com/vercel/next.js/tree/canary/examples/with-opentelemetry) for a complete implementation.

> **Good to know:**
> - This feature is experimental. To use it, you must explicitly opt in by defining `experimental.instrumentationHook = true;` in your `next.config.js`.
> - The instrumentation file should be in the root of your project and not inside the `app` or `pages` directory. If you're using the `src` folder, then place the file inside `src` alongside `pages` and `app`.
> - If you use the `pageExtensions` config option to add a suffix, you will also need to update the instrumentation filename to match.

### Examples

#### Importing files with side effects

Sometimes, it may be useful to import a file in your code because of the side effects it will cause. For example, you might import a file that defines a set of global variables, but never explicitly use the imported file in your code. You would still have access to the global variables the package has declared.

We recommend importing files using JavaScript import syntax within your `register` function. The following example demonstrates a basic usage of import in a `register` function:

```typescript
// instrumentation.ts
export async function register() {
  await import('package-with-side-effect')
}
```

> **Good to know:**
> We recommend importing the file from within the `register` function, rather than at the top of the file. By doing this, you can colocate all of your side effects in one place in your code, and avoid any unintended consequences from importing globally at the top of the file.

#### Importing runtime-specific code

Next.js calls `register` in all environments, so it's important to conditionally import any code that doesn't support specific runtimes (e.g. Edge or Node.js). You can use the `NEXT_RUNTIME` environment variable to get the current environment:

```typescript
// instrumentation.ts
export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    await import('./instrumentation-node')
  }

  if (process.env.NEXT_RUNTIME === 'edge') {
    await import('./instrumentation-edge')
  }
}
```

## OpenTelemetry

Good to know: This feature is experimental, you need to explicitly opt-in by providing `experimental.instrumentationHook = true;` in your `next.config.js`.

Observability is crucial for understanding and optimizing the behavior and performance of your Next.js app.

As applications become more complex, it becomes increasingly difficult to identify and diagnose issues that may arise. By leveraging observability tools, such as logging and metrics, developers can gain insights into their application's behavior and identify areas for optimization. With observability, developers can proactively address issues before they become major problems and provide a better user experience. Therefore, it is highly recommended to use observability in your Next.js applications to improve performance, optimize resources, and enhance user experience.

We recommend using OpenTelemetry for instrumenting your apps. It's a platform-agnostic way to instrument apps that allows you to change your observability provider without changing your code. Read [Official OpenTelemetry docs](https://opentelemetry.io/docs/) for more information about OpenTelemetry and how it works.

This documentation uses terms like Span, Trace or Exporter throughout this doc, all of which can be found in the [OpenTelemetry Observability Primer](https://opentelemetry.io/docs/concepts/observability-primer/).

Next.js supports OpenTelemetry instrumentation out of the box, which means that we already instrumented Next.js itself. When you enable OpenTelemetry we will automatically wrap all your code like `getStaticProps` in spans with helpful attributes.

### Getting Started

OpenTelemetry is extensible but setting it up properly can be quite verbose. That's why we prepared a package `@vercel/otel` that helps you get started quickly.

#### Using @vercel/otel

To get started, install the following packages:

```bash
npm install @vercel/otel @opentelemetry/sdk-logs @opentelemetry/api-logs @opentelemetry/instrumentation
```

Next, create a custom `instrumentation.ts` (or `.js`) file in the root directory of the project (or inside `src` folder if using one):

```typescript
// your-project/instrumentation.ts
import { registerOTel } from '@vercel/otel'

export function register() {
  registerOTel({ serviceName: 'next-app' })
}
```

See the [@vercel/otel documentation](https://github.com/vercel/otel) for additional configuration options.

Good to know:

- The instrumentation file should be in the root of your project and not inside the `app` or `pages` directory. If you're using the `src` folder, then place the file inside `src` alongside `pages` and `app`.
- If you use the `pageExtensions` config option to add a suffix, you will also need to update the instrumentation filename to match.
- We have created a basic [with-opentelemetry example](https://github.com/vercel/next.js/tree/canary/examples/with-opentelemetry) that you can use.

### Manual OpenTelemetry configuration

The `@vercel/otel` package provides many configuration options and should serve most of common use cases. But if it doesn't suit your needs, you can configure OpenTelemetry manually.

Firstly you need to install OpenTelemetry packages:

```bash
npm install @opentelemetry/sdk-node @opentelemetry/resources @opentelemetry/semantic-conventions @opentelemetry/sdk-trace-node @opentelemetry/exporter-trace-otlp-http
```

Now you can initialize NodeSDK in your `instrumentation.ts`. Unlike `@vercel/otel`, NodeSDK is not compatible with edge runtime, so you need to make sure that you are importing them only when `process.env.NEXT_RUNTIME === 'nodejs'`. We recommend creating a new file `instrumentation.node.ts` which you conditionally import only when using node:

```typescript
// instrumentation.ts
export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    await import('./instrumentation.node.ts')
  }
}
```

```typescript
// instrumentation.node.ts
import { NodeSDK } from '@opentelemetry/sdk-node'
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-http'
import { Resource } from '@opentelemetry/resources'
import { SEMRESATTRS_SERVICE_NAME } from '@opentelemetry/semantic-conventions'
import { SimpleSpanProcessor } from '@opentelemetry/sdk-trace-node'

const sdk = new NodeSDK({
  resource: new Resource({
    [SEMRESATTRS_SERVICE_NAME]: 'next-app',
  }),
  spanProcessor: new SimpleSpanProcessor(new OTLPTraceExporter()),
})
sdk.start()
```

Doing this is equivalent to using `@vercel/otel`, but it's possible to modify and extend some features that are not exposed by the `@vercel/otel`. If edge runtime support is necessary, you will have to use `@vercel/otel`.

### Testing your instrumentation

You need an OpenTelemetry collector with a compatible backend to test OpenTelemetry traces locally. We recommend using our [OpenTelemetry dev environment](https://github.com/vercel/opentelemetry-collector-dev-setup).

If everything works well you should be able to see the root server span labeled as `GET /requested/pathname`. All other spans from that particular trace will be nested under it.

Next.js traces more spans than are emitted by default. To see more spans, you must set `NEXT_OTEL_VERBOSE=1`.

### Deployment

#### Using OpenTelemetry Collector

When you are deploying with OpenTelemetry Collector, you can use `@vercel/otel`. It will work both on Vercel and when self-hosted.

##### Deploying on Vercel

We made sure that OpenTelemetry works out of the box on Vercel.

Follow [Vercel documentation](https://vercel.com/docs/concepts/observability/otel-overview/quickstart) to connect your project to an observability provider.

##### Self-hosting

Deploying to other platforms is also straightforward. You will need to spin up your own OpenTelemetry Collector to receive and process the telemetry data from your Next.js app.

To do this, follow the [OpenTelemetry Collector Getting Started](https://opentelemetry.io/docs/collector/getting-started/) guide, which will walk you through setting up the collector and configuring it to receive data from your Next.js app.

Once you have your collector up and running, you can deploy your Next.js app to your chosen platform following their respective deployment guides.

#### Custom Exporters

OpenTelemetry Collector is not necessary. You can use a custom OpenTelemetry exporter with `@vercel/otel` or manual OpenTelemetry configuration.

### Custom Spans

You can add a custom span with OpenTelemetry APIs.

```bash
npm install @opentelemetry/api
```

The following example demonstrates a function that fetches GitHub stars and adds a custom `fetchGithubStars` span to track the fetch request's result:

```typescript
import { trace } from '@opentelemetry/api'

export async function fetchGithubStars() {
  return await trace
    .getTracer('nextjs-example')
    .startActiveSpan('fetchGithubStars', async (span) => {
      try {
        return await getValue()
      } finally {
        span.end()
      }
    })
}
```

The `register` function will execute before your code runs in a new environment. You can start creating new spans, and they should be correctly added to the exported trace.

### Default Spans in Next.js

Next.js automatically instruments several spans for you to provide useful insights into your application's performance.

Attributes on spans follow OpenTelemetry semantic conventions. We also add some custom attributes under the `next` namespace:

- `next.span_name` - duplicates span name
- `next.span_type` - each span type has a unique identifier
- `next.route` - The route pattern of the request (e.g., `/[param]/user`).
- `next.rsc` (true/false) - Whether the request is an RSC request, such as prefetch.
- `next.page`
  - This is an internal value used by an app router.
  - You can think about it as a route to a special file (like `page.ts`, `layout.ts`, `loading.ts` and others)
  - It can be used as a unique identifier only when paired with `next.route` because `/layout` can be used to identify both `/(groupA)/layout.ts` and `/(groupB)/layout.ts`

#### [http.method] [next.route]

`next.span_type: BaseServer.handleRequest`

This span represents the root span for each incoming request to your Next.js application. It tracks the HTTP method, route, target, and status code of the request.

Attributes:
- Common HTTP attributes
  - `http.method`
  - `http.status_code`
- Server HTTP attributes
  - `http.route`
  - `http.target`
- `next.span_name`
- `next.span_type`
- `next.route`

#### render route (app) [next.route]

`next.span_type: AppRender.getBodyResult`

This span represents the process of rendering a route in the app router.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.route`

#### fetch [http.method] [http.url]

`next.span_type: AppRender.fetch`

This span represents the fetch request executed in your code.

Attributes:
- Common HTTP attributes
  - `http.method`
- Client HTTP attributes
  - `http.url`
  - `net.peer.name`
  - `net.peer.port` (only if specified)
- `next.span_name`
- `next.span_type`

This span can be turned off by setting `NEXT_OTEL_FETCH_DISABLED=1` in your environment. This is useful when you want to use a custom fetch instrumentation library.

#### executing api route (app) [next.route]

`next.span_type: AppRouteRouteHandlers.runHandler`

This span represents the execution of an API route handler in the app router.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.route`

#### getServerSideProps [next.route]

`next.span_type: Render.getServerSideProps`

This span represents the execution of `getServerSideProps` for a specific route.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.route`

#### getStaticProps [next.route]

`next.span_type: Render.getStaticProps`

This span represents the execution of `getStaticProps` for a specific route.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.route`

#### render route (pages) [next.route]

`next.span_type: Render.renderDocument`

This span represents the process of rendering the document for a specific route.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.route`

#### generateMetadata [next.page]

`next.span_type: ResolveMetadata.generateMetadata`

This span represents the process of generating metadata for a specific page (a single route can have multiple of these spans).

Attributes:
- `next.span_name`
- `next.span_type`
- `next.page`

#### resolve page components

`next.span_type: NextNodeServer.findPageComponents`

This span represents the process of resolving page components for a specific page.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.route`

#### resolve segment modules

`next.span_type: NextNodeServer.getLayoutOrPageModule`

This span represents loading of code modules for a layout or a page.

Attributes:
- `next.span_name`
- `next.span_type`
- `next.segment`

#### start response

`next.span_type: NextNodeServer.startResponse`

This zero-length span represents the time when the first byte has been sent in the response.

## Static Assets in `public`

Next.js can serve static files, like images, under a folder called `public` in the root directory. Files inside `public` can then be referenced by your code starting from the base URL (`/`).

For example, the file `public/avatars/me.png` can be viewed by visiting the `/avatars/me.png` path. The code to display that image might look like:

```javascript
// avatar.js
import Image from 'next/image'

export function Avatar({ id, alt }) {
  return <Image src={`/avatars/${id}.png`} alt={alt} width="64" height="64" />
}

export function AvatarOfMe() {
  return <Avatar id="me" alt="A portrait of me" />
}
```

### Caching

Next.js cannot safely cache assets in the `public` folder because they may change. The default caching headers applied are:

> Cache-Control: public, max-age=0

### Robots, Favicons, and others

For static metadata files, such as `robots.txt`, `favicon.ico`, etc., you should use special metadata files inside the `app` folder.

### Good to know:

- The directory must be named `public`. The name cannot be changed and it's the only directory used to serve static assets.
- Only assets that are in the `public` directory at build time will be served by Next.js. Files added at request time won't be available. We recommend using a third-party service like Vercel Blob for persistent file storage.

## Third Party Libraries

`@next/third-parties` is a library that provides a collection of components and utilities that improve the performance and developer experience of loading popular third-party libraries in your Next.js application.

All third-party integrations provided by `@next/third-parties` have been optimized for performance and ease of use.

### Getting Started

To get started, install the `@next/third-parties` library:

```bash
npm install @next/third-parties@latest next@latest
```

`@next/third-parties` is currently an experimental library under active development. We recommend installing it with the latest or canary flags while we work on adding more third-party integrations.

### Google Third-Parties

All supported third-party libraries from Google can be imported from `@next/third-parties/google`.

#### Google Tag Manager

The `GoogleTagManager` component can be used to instantiate a Google Tag Manager container to your page. By default, it fetches the original inline script after hydration occurs on the page.

To load Google Tag Manager for all routes, include the component directly in your root layout and pass in your GTM container ID:

```typescript
// app/layout.tsx
import { GoogleTagManager } from '@next/third-parties/google'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <GoogleTagManager gtmId="GTM-XYZ" />
      <body>{children}</body>
    </html>
  )
}
```

To load Google Tag Manager for a single route, include the component in your page file:

```javascript
// app/page.js
import { GoogleTagManager } from '@next/third-parties/google'
 
export default function Page() {
  return <GoogleTagManager gtmId="GTM-XYZ" />
}
```

##### Sending Events

The `sendGTMEvent` function can be used to track user interactions on your page by sending events using the `dataLayer` object. For this function to work, the `<GoogleTagManager />` component must be included in either a parent layout, page, or component, or directly in the same file.

```javascript
// app/page.js
'use client'
 
import { sendGTMEvent } from '@next/third-parties/google'
 
export function EventButton() {
  return (
    <div>
      <button
        onClick={() => sendGTMEvent('event', 'buttonClicked', { value: 'xyz' })}
      >
        Send Event
      </button>
    </div>
  )
}
```

Refer to the Tag Manager developer documentation to learn about the different variables and events that can be passed into the function.

##### Options

Options to pass to the Google Tag Manager. For a full list of options, read the Google Tag Manager docs.

| Name | Type | Description |
|------|------|-------------|
| gtmId | Required | Your GTM container ID. Usually starts with GTM-. |
| dataLayer | Optional | Data layer object to instantiate the container with. |
| dataLayerName | Optional | Name of the data layer. Defaults to dataLayer. |
| auth | Optional | Value of authentication parameter (gtm_auth) for environment snippets. |
| preview | Optional | Value of preview parameter (gtm_preview) for environment snippets. |

#### Google Analytics

The `GoogleAnalytics` component can be used to include Google Analytics 4 to your page via the Google tag (gtag.js). By default, it fetches the original scripts after hydration occurs on the page.

**Recommendation**: If Google Tag Manager is already included in your application, you can configure Google Analytics directly using it, rather than including Google Analytics as a separate component. Refer to the documentation to learn more about the differences between Tag Manager and gtag.js.

To load Google Analytics for all routes, include the component directly in your root layout and pass in your measurement ID:

```typescript
// app/layout.tsx
import { GoogleAnalytics } from '@next/third-parties/google'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
      <GoogleAnalytics gaId="G-XYZ" />
    </html>
  )
}
```

To load Google Analytics for a single route, include the component in your page file:

```javascript
// app/page.js
import { GoogleAnalytics } from '@next/third-parties/google'
 
export default function Page() {
  return <GoogleAnalytics gaId="G-XYZ" />
}
```

##### Sending Events

The `sendGAEvent` function can be used to measure user interactions on your page by sending events using the `dataLayer` object. For this function to work, the `<GoogleAnalytics />` component must be included in either a parent layout, page, or component, or directly in the same file.

```javascript
// app/page.js
'use client'
 
import { sendGAEvent } from '@next/third-parties/google'
 
export function EventButton() {
  return (
    <div>
      <button
        onClick={() => sendGAEvent('event', 'buttonClicked', { value: 'xyz' })}
      >
        Send Event
      </button>
    </div>
  )
}
```

Refer to the Google Analytics developer documentation to learn more about event parameters.

##### Tracking Pageviews

Google Analytics automatically tracks pageviews when the browser history state changes. This means that client-side navigations between Next.js routes will send pageview data without any configuration.

To ensure that client-side navigations are being measured correctly, verify that the "Enhanced Measurement" property is enabled in your Admin panel and the "Page changes based on browser history events" checkbox is selected.

**Note**: If you decide to manually send pageview events, make sure to disable the default pageview measurement to avoid having duplicate data. Refer to the Google Analytics developer documentation to learn more.

##### Options

Options to pass to the `<GoogleAnalytics>` component.

| Name | Type | Description |
|------|------|-------------|
| gaId | Required | Your measurement ID. Usually starts with G-. |
| dataLayerName | Optional | Name of the data layer. Defaults to dataLayer. |

#### Google Maps Embed

The `GoogleMapsEmbed` component can be used to add a Google Maps Embed to your page. By default, it uses the loading attribute to lazy-load the embed below the fold.

```javascript
// app/page.js
import { GoogleMapsEmbed } from '@next/third-parties/google'
 
export default function Page() {
  return (
    <GoogleMapsEmbed
      apiKey="XYZ"
      height={200}
      width="100%"
      mode="place"
      q="Brooklyn+Bridge,New+York,NY"
    />
  )
}
```

##### Options

Options to pass to the Google Maps Embed. For a full list of options, read the Google Map Embed docs.

| Name | Type | Description |
|------|------|-------------|
| apiKey | Required | Your api key. |
| mode | Required | Map mode |
| height | Optional | Height of the embed. Defaults to auto. |
| width | Optional | Width of the embed. Defaults to auto. |
| style | Optional | Pass styles to the iframe. |
| allowfullscreen | Optional | Property to allow certain map parts to go full screen. |
| loading | Optional | Defaults to lazy. Consider changing if you know your embed will be above the fold. |
| q | Optional | Defines map marker location. This may be required depending on the map mode. |
| center | Optional | Defines the center of the map view. |
| zoom | Optional | Sets initial zoom level of the map. |
| maptype | Optional | Defines type of map tiles to load. |
| language | Optional | Defines the language to use for UI elements and for the display of labels on map tiles. |
| region | Optional | Defines the appropriate borders and labels to display, based on geo-political sensitivities. |

#### YouTube Embed

The `YouTubeEmbed` component can be used to load and display a YouTube embed. This component loads faster by using lite-youtube-embed under the hood.

```javascript
// app/page.js
import { YouTubeEmbed } from '@next/third-parties/google'
 
export default function Page() {
  return <YouTubeEmbed videoid="ogfYd705cRs" height={400} params="controls=0" />
}
```

##### Options

| Name | Type | Description |
|------|------|-------------|
| videoid | Required | YouTube video id. |
| width | Optional | Width of the video container. Defaults to auto |
| height | Optional | Height of the video container. Defaults to auto |
| playlabel | Optional | A visually hidden label for the play button for accessibility. |
| params | Optional | The video player params defined [here](https://developers.google.com/youtube/player_parameters#Parameters). Params are passed as a query param string. Eg: `params="controls=0&start=10&end=30"` |
| style | Optional | Used to apply styles to the video container. |

## Memory Usage

As applications grow and become more feature rich, they can demand more resources when developing locally or creating production builds.

Let's explore some strategies and techniques to optimize memory and address common memory issues in Next.js.

### Reduce number of dependencies

Applications with a large amount of dependencies will use more memory.

The Bundle Analyzer can help you investigate large dependencies in your application that may be able to be removed to improve performance and memory usage.

### Try experimental.webpackMemoryOptimizations

Starting in v15.0.0, you can add `experimental.webpackMemoryOptimizations: true` to your `next.config.js` file to change behavior in Webpack that reduces max memory usage but may increase compilation times by a slight amount.

> Good to know: This feature is currently experimental to test on more projects first, but it is considered to be low-risk.

### Run next build with --experimental-debug-memory-usage

Starting in 14.2.0, you can run `next build --experimental-debug-memory-usage` to run the build in a mode where Next.js will print out information about memory usage continuously throughout the build, such as heap usage and garbage collection statistics. Heap snapshots will also be taken automatically when memory usage gets close to the configured limit.

> Good to know: This feature is not compatible with the Webpack build worker option which is auto-enabled unless you have custom webpack config.

### Record a heap profile

To look for memory issues, you can record a heap profile from Node.js and load it in Chrome DevTools to identify potential sources of memory leaks.

In your terminal, pass the `--heap-prof` flag to Node.js when starting your Next.js build:

```bash
node --heap-prof node_modules/next/dist/bin/next build
```

At the end of the build, a `.heapprofile` file will be created by Node.js.

In Chrome DevTools, you can open the Memory tab and click on the "Load Profile" button to visualize the file.

### Analyze a snapshot of the heap

You can use an inspector tool to analyze the memory usage of the application.

When running the `next build` or `next dev` command, add `NODE_OPTIONS=--inspect` to the beginning of the command. This will expose the inspector agent on the default port. If you wish to break before any user code starts, you can pass `--inspect-brk` instead. While the process is running, you can use a tool such as Chrome DevTools to connect to the debugging port to record and analyze a snapshot of the heap to see what memory is being retained.

Starting in 14.2.0, you can also run `next build` with the `--experimental-debug-memory-usage` flag to make it easier to take heap snapshots.

While running in this mode, you can send a SIGUSR2 signal to the process at any point, and the process will take a heap snapshot.

The heap snapshot will be saved to the project root of the Next.js application and can be loaded in any heap analyzer, such as Chrome DevTools, to see what memory is retained. This mode is not yet compatible with Webpack build workers.

See [how to record and analyze heap snapshots](https://nodejs.org/en/docs/guides/diagnostics-flamegraph#heap-memory) for more information.

### Webpack build worker

The Webpack build worker allows you to run Webpack compilations inside a separate Node.js worker which will decrease memory usage of your application during builds.

This option is enabled by default if your application does not have a custom Webpack configuration starting in v14.1.0.

If you are using an older version of Next.js or you have a custom Webpack configuration, you can enable this option by setting `experimental.webpackBuildWorker: true` inside your `next.config.js`.

> Good to know: This feature may not be compatible with all custom Webpack plugins.

### Disable Webpack cache

The Webpack cache saves generated Webpack modules in memory and/or to disk to improve the speed of builds. This can help with performance, but it will also increase the memory usage of your application to store the cached data.

You can disable this behavior by adding a custom Webpack configuration to your application:

```javascript
// next.config.mjs

/** @type {import('next').NextConfig} */
const nextConfig = {
  webpack: (
    config,
    { buildId, dev, isServer, defaultLoaders, nextRuntime, webpack }
  ) => {
    if (config.cache && !dev) {
      config.cache = Object.freeze({
        type: 'memory',
      })
    }
    // Important: return the modified config
    return config
  },
}
 
export default nextConfig
```

### Disable source maps

Generating source maps consumes extra memory during the build process.

You can disable source map generation by adding `productionBrowserSourceMaps: false` and `experimental.serverSourceMaps: false` to your Next.js configuration.

> Good to know: Some plugins may turn on source maps and may require custom configuration to disable.

### Edge memory issues

Next.js v14.1.3 fixed a memory issue when using the Edge runtime. Please update to this version (or later) to see if it addresses your issue.

# Configuring

Next.js allows you to customize your project to meet specific requirements. This includes integrations with TypeScript, ESlint, and more, as well as internal configuration options such as Absolute Imports and Environment Variables.

## TypeScript

Next.js provides a TypeScript-first development experience for building your React application.

It comes with built-in TypeScript support for automatically installing the necessary packages and configuring the proper settings.

As well as a TypeScript Plugin for your editor.

### New Projects

`create-next-app` now ships with TypeScript by default.

```bash
npx create-next-app@latest
```

### Existing Projects

Add TypeScript to your project by renaming a file to `.ts` / `.tsx`. Run `next dev` and `next build` to automatically install the necessary dependencies and add a `tsconfig.json` file with the recommended config options.

If you already had a `jsconfig.json` file, copy the `paths` compiler option from the old `jsconfig.json` into the new `tsconfig.json` file, and delete the old `jsconfig.json` file.

We also recommend you to use `next.config.ts` over `next.config.js` for better type inference.

### TypeScript Plugin

Next.js includes a custom TypeScript plugin and type checker, which VSCode and other code editors can use for advanced type-checking and auto-completion.

You can enable the plugin in VS Code by:

1. Opening the command palette (Ctrl/⌘ + Shift + P)
2. Searching for "TypeScript: Select TypeScript Version"
3. Selecting "Use Workspace Version"

Now, when editing files, the custom plugin will be enabled. When running `next build`, the custom type checker will be used.

#### Plugin Features

The TypeScript plugin can help with:

- Warning if the invalid values for segment config options are passed.
- Showing available options and in-context documentation.
- Ensuring the `use client` directive is used correctly.
- Ensuring client hooks (like `useState`) are only used in Client Components.

**Good to know**: More features will be added in the future.

### Minimum TypeScript Version

It is highly recommended to be on at least v4.5.2 of TypeScript to get syntax features such as type modifiers on import names and performance improvements.

### Type checking in Next.js Configuration

#### Type checking next.config.js

When using the `next.config.js` file, you can add some type checking in your IDE using JSDoc as below:

```javascript
// @ts-check

/** @type {import('next').NextConfig} */
const nextConfig = {
  /* config options here */
}

module.exports = nextConfig
```

#### Type checking next.config.ts

You can use TypeScript and import types in your Next.js configuration by using `next.config.ts`.

```typescript
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  /* config options here */
}

export default nextConfig
```

**Good to know**: You can import Native ESM modules in `next.config.ts` without any additional configuration. Supports importing extensions like `.cjs`, `.cts`, `.mjs`, and `.mts`.

### Statically Typed Links

Next.js can statically type links to prevent typos and other errors when using `next/link`, improving type safety when navigating between pages.

To opt-into this feature, `experimental.typedRoutes` need to be enabled and the project needs to be using TypeScript.

```typescript
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  experimental: {
    typedRoutes: true,
  },
}

export default nextConfig
```

Next.js will generate a link definition in `.next/types` that contains information about all existing routes in your application, which TypeScript can then use to provide feedback in your editor about invalid links.

Currently, experimental support includes any string literal, including dynamic segments. For non-literal strings, you currently need to manually cast the `href` with `as Route`:

```typescript
import type { Route } from 'next';
import Link from 'next/link'

// No TypeScript errors if href is a valid route
<Link href="/about" />
<Link href="/blog/nextjs" />
<Link href={`/blog/${slug}`} />
<Link href={('/blog' + slug) as Route} />

// TypeScript errors if href is not a valid route
<Link href="/aboot" />
```

To accept `href` in a custom component wrapping `next/link`, use a generic:

```typescript
import type { Route } from 'next'
import Link from 'next/link'

function Card<T extends string>({ href }: { href: Route<T> | URL }) {
  return (
    <Link href={href}>
      <div>My Card</div>
    </Link>
  )
}
```

#### How does it work?

When running `next dev` or `next build`, Next.js generates a hidden `.d.ts` file inside `.next` that contains information about all existing routes in your application (all valid routes as the `href` type of `Link`). This `.d.ts` file is included in `tsconfig.json` and the TypeScript compiler will check that `.d.ts` and provide feedback in your editor about invalid links.

### End-to-End Type Safety

The Next.js App Router has enhanced type safety. This includes:

- No serialization of data between fetching function and page: You can fetch directly in components, layouts, and pages on the server. This data does not need to be serialized (converted to a string) to be passed to the client side for consumption in React. Instead, since app uses Server Components by default, we can use values like `Date`, `Map`, `Set`, and more without any extra steps. Previously, you needed to manually type the boundary between server and client with Next.js-specific types.
- Streamlined data flow between components: With the removal of `_app` in favor of root layouts, it is now easier to visualize the data flow between components and pages. Previously, data flowing between individual pages and `_app` were difficult to type and could introduce confusing bugs. With colocated data fetching in the App Router, this is no longer an issue.

Data Fetching in Next.js now provides as close to end-to-end type safety as possible without being prescriptive about your database or content provider selection.

We're able to type the response data as you would expect with normal TypeScript. For example:

```typescript
async function getData() {
  const res = await fetch('https://api.example.com/...')
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.
  return res.json()
}

export default async function Page() {
  const name = await getData()

  return '...'
}
```

For complete end-to-end type safety, this also requires your database or content provider to support TypeScript. This could be through using an ORM or type-safe query builder.

### Async Server Component TypeScript Error

To use an async Server Component with TypeScript, ensure you are using TypeScript 5.1.3 or higher and `@types/react` 18.2.8 or higher.

If you are using an older version of TypeScript, you may see a `'Promise<Element>' is not a valid JSX element` type error. Updating to the latest version of TypeScript and `@types/react` should resolve this issue.

### Passing Data Between Server & Client Components

When passing data between a Server and Client Component through props, the data is still serialized (converted to a string) for use in the browser. However, it does not need a special type. It's typed the same as passing any other props between components.

Further, there is less code to be serialized, as un-rendered data does not cross between the server and client (it remains on the server). This is only now possible through support for Server Components.

### Path aliases and baseUrl

Next.js automatically supports the `tsconfig.json` `"paths"` and `"baseUrl"` options.

You can learn more about this feature on the [Module Path aliases documentation](https://nextjs.org/docs/advanced-features/module-path-aliases).

### Incremental type checking

Since v10.2.1 Next.js supports incremental type checking when enabled in your `tsconfig.json`, this can help speed up type checking in larger applications.

### Ignoring TypeScript Errors

Next.js fails your production build (`next build`) when TypeScript errors are present in your project.

If you'd like Next.js to dangerously produce production code even when your application has errors, you can disable the built-in type checking step.

If disabled, be sure you are running type checks as part of your build or deploy process, otherwise this can be very dangerous.

Open `next.config.ts` and enable the `ignoreBuildErrors` option in the `typescript` config:

```typescript
export default {
  typescript: {
    // !! WARN !!
    // Dangerously allow production builds to successfully complete even if
    // your project has type errors.
    // !! WARN !!
    ignoreBuildErrors: true,
  },
}
```

### Custom Type Declarations

When you need to declare custom types, you might be tempted to modify `next-env.d.ts`. However, this file is automatically generated, so any changes you make will be overwritten. Instead, you should create a new file, let's call it `new-types.d.ts`, and reference it in your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "skipLibCheck": true
    //...truncated...
  },
  "include": [
    "new-types.d.ts",
    "next-env.d.ts",
    ".next/types/**/*.ts",
    "**/*.ts",
    "**/*.tsx"
  ],
  "exclude": ["node_modules"]
}
```

### Version Changes

| Version | Changes |
|---------|---------|
| v15.0.0 | `next.config.ts` support added for TypeScript projects. |
| v13.2.0 | Statically typed links are available in beta. |
| v12.0.0 | SWC is now used by default to compile TypeScript and TSX for faster builds. |
| v10.2.1 | Incremental type checking support added when enabled in your `tsconfig.json`. |

## ESLint

Next.js provides an integrated ESLint experience out of the box. Add `next lint` as a script to `package.json`:

```json
{
  "scripts": {
    "lint": "next lint"
  }
}
```

Then run `npm run lint` or `yarn lint`:

```bash
yarn lint
```

If you don't already have ESLint configured in your application, you will be guided through the installation and configuration process.

You'll see a prompt like this:

```
? How would you like to configure ESLint?

❯ Strict (recommended)
  Base
  Cancel
```

One of the following three options can be selected:

**Strict**: Includes Next.js' base ESLint configuration along with a stricter Core Web Vitals rule-set. This is the recommended configuration for developers setting up ESLint for the first time.

```json
{
  "extends": "next/core-web-vitals"
}
```

**Base**: Includes Next.js' base ESLint configuration.

```json
{
  "extends": "next"
}
```

**Cancel**: Does not include any ESLint configuration. Only select this option if you plan on setting up your own custom ESLint configuration.

If either of the two configuration options are selected, Next.js will automatically install `eslint` and `eslint-config-next` as dependencies in your application and create an `.eslintrc.json` file in the root of your project that includes your selected configuration.

You can now run `next lint` every time you want to run ESLint to catch errors. Once ESLint has been set up, it will also automatically run during every build (`next build`). Errors will fail the build, while warnings will not.

If you do not want ESLint to run during `next build`, refer to the documentation for [Ignoring ESLint](https://nextjs.org/docs/basic-features/eslint#ignoring-eslint).

We recommend using an appropriate integration to view warnings and errors directly in your code editor during development.

### ESLint Config

The default configuration (`eslint-config-next`) includes everything you need to have an optimal out-of-the-box linting experience in Next.js. If you do not have ESLint already configured in your application, we recommend using `next lint` to set up ESLint along with this configuration.

If you would like to use `eslint-config-next` along with other ESLint configurations, refer to the [Additional Configurations](#additional-configurations) section to learn how to do so without causing any conflicts.

Recommended rule-sets from the following ESLint plugins are all used within `eslint-config-next`:

- `eslint-plugin-react`
- `eslint-plugin-react-hooks`
- `eslint-plugin-next`

This will take precedence over the configuration from `next.config.js`.

### ESLint Plugin

Next.js provides an ESLint plugin, `eslint-plugin-next`, already bundled within the base configuration that makes it possible to catch common issues and problems in a Next.js application. The full set of rules is as follows:

✅ Enabled in the recommended configuration

| Rule | Description |
|------|-------------|
| `@next/next/google-font-display` | Enforce font-display behavior with Google Fonts. |
| `@next/next/google-font-preconnect` | Ensure `preconnect` is used with Google Fonts. |
| `@next/next/inline-script-id` | Enforce `id` attribute on `next/script` components with inline content. |
| `@next/next/next-script-for-ga` | Prefer `next/script` component when using the inline script for Google Analytics. |
| `@next/next/no-assign-module-variable` | Prevent assignment to the `module` variable. |
| `@next/next/no-async-client-component` | Prevent client components from being async functions. |
| `@next/next/no-before-interactive-script-outside-document` | Prevent usage of `next/script`'s `beforeInteractive` strategy outside of `pages/_document.js`. |
| `@next/next/no-css-tags` | Prevent manual stylesheet tags. |
| `@next/next/no-document-import-in-page` | Prevent importing `next/document` outside of `pages/_document.js`. |
| `@next/next/no-duplicate-head` | Prevent duplicate usage of `<Head>` in `pages/_document.js`. |
| `@next/next/no-head-element` | Prevent usage of `<head>` element. |
| `@next/next/no-head-import-in-document` | Prevent usage of `next/head` in `pages/_document.js`. |
| `@next/next/no-html-link-for-pages` | Prevent usage of `<a>` elements to navigate to internal Next.js pages. |
| `@next/next/no-img-element` | Prevent usage of `<img>` element due to slower LCP and higher bandwidth. |
| `@next/next/no-page-custom-font` | Prevent page-only custom fonts. |
| `@next/next/no-script-component-in-head` | Prevent usage of `next/script` in `next/head` component. |
| `@next/next/no-styled-jsx-in-document` | Prevent usage of `styled-jsx` in `pages/_document.js`. |
| `@next/next/no-sync-scripts` | Prevent synchronous scripts. |
| `@next/next/no-title-in-document-head` | Prevent usage of `<title>` with Head component from `next/document`. |
| `@next/next/no-typos` | Prevent common typos in Next.js's data fetching functions |
| `@next/next/no-unwanted-polyfillio` | Prevent duplicate polyfills from Polyfill.io. |

If you already have ESLint configured in your application, we recommend extending from this plugin directly instead of including `eslint-config-next` unless a few conditions are met. Refer to the [Recommended Plugin Ruleset](#recommended-plugin-ruleset) to learn more.

### Custom Settings

#### rootDir

If you're using `eslint-plugin-next` in a project where Next.js isn't installed in your root directory (such as a monorepo), you can tell `eslint-plugin-next` where to find your Next.js application using the `settings` property in your `.eslintrc`:

```json
{
  "extends": "next",
  "settings": {
    "next": {
      "rootDir": "packages/my-app/"
    }
  }
}
```

`rootDir` can be a path (relative or absolute), a glob (i.e. `"packages/*/""), or an array of paths and/or globs.

### Linting Custom Directories and Files

By default, Next.js will run ESLint for all files in the `pages/`, `app/`, `components/`, `lib/`, and `src/` directories. However, you can specify which directories using the `dirs` option in the `eslint` config in `next.config.js` for production builds:

```javascript
module.exports = {
  eslint: {
    dirs: ['pages', 'utils'], // Only run ESLint on the 'pages' and 'utils' directories during production builds (next build)
  },
}
```

Similarly, the `--dir` and `--file` flags can be used for `next lint` to lint specific directories and files:

```bash
next lint --dir pages --dir utils --file bar.js
```

### Caching

To improve performance, information of files processed by ESLint are cached by default. This is stored in `.next/cache` or in your defined build directory. If you include any ESLint rules that depend on more than the contents of a single source file and need to disable the cache, use the `--no-cache` flag with `next lint`.

```bash
next lint --no-cache
```

### Disabling Rules

If you would like to modify or disable any rules provided by the supported plugins (`react`, `react-hooks`, `next`), you can directly change them using the `rules` property in your `.eslintrc`:

```json
{
  "extends": "next",
  "rules": {
    "react/no-unescaped-entities": "off",
    "@next/next/no-page-custom-font": "off"
  }
}
```

### Core Web Vitals

The `next/core-web-vitals` rule set is enabled when `next lint` is run for the first time and the strict option is selected.

```json
{
  "extends": "next/core-web-vitals"
}
```

`next/core-web-vitals` updates `eslint-plugin-next` to error on a number of rules that are warnings by default if they affect Core Web Vitals.

The `next/core-web-vitals` entry point is automatically included for new applications built with [Create Next App](https://nextjs.org/docs/api-reference/create-next-app).

### TypeScript

In addition to the Next.js ESLint rules, create-next-app --typescript will also add TypeScript-specific lint rules with `next/typescript` to your config:

```json
{
  "extends": ["next/core-web-vitals", "next/typescript"]
}
```

Those rules are based on `plugin:@typescript-eslint/recommended`. See [typescript-eslint > Configs](https://typescript-eslint.io/docs/linting/configs/) for more details.

### Usage With Other Tools

#### Prettier

ESLint also contains code formatting rules, which can conflict with your existing Prettier setup. We recommend including `eslint-config-prettier` in your ESLint config to make ESLint and Prettier work together.

First, install the dependency:

```bash
npm install --save-dev eslint-config-prettier
 
yarn add --dev eslint-config-prettier
 
pnpm add --save-dev eslint-config-prettier
 
bun add --dev eslint-config-prettier
```

Then, add `prettier` to your existing ESLint config:

```json
{
  "extends": ["next", "prettier"]
}
```

#### lint-staged

If you would like to use `next lint` with lint-staged to run the linter on staged git files, you'll have to add the following to the `.lintstagedrc.js` file in the root of your project in order to specify usage of the `--file` flag.

```javascript
const path = require('path')

const buildEslintCommand = (filenames) =>
  `next lint --fix --file ${filenames
    .map((f) => path.relative(process.cwd(), f))
    .join(' --file ')}`

module.exports = {
  '*.{js,jsx,ts,tsx}': [buildEslintCommand],
}
```

### Migrating Existing Config

#### Recommended Plugin Ruleset

If you already have ESLint configured in your application and any of the following conditions are true:

- You have one or more of the following plugins already installed (either separately or through a different config such as `airbnb` or `react-app`):
  - `react`
  - `react-hooks`
  - `jsx-a11y`
  - `import`
- You've defined specific `parserOptions` that are different from how Babel is configured within Next.js (this is not recommended unless you have customized your Babel configuration)
- You have `eslint-plugin-import` installed with Node.js and/or TypeScript resolvers defined to handle imports

Then we recommend either removing these settings if you prefer how these properties have been configured within `eslint-config-next` or extending directly from the Next.js ESLint plugin instead:

```javascript
module.exports = {
  extends: [
    //...
    'plugin:@next/next/recommended',
  ],
}
```

The plugin can be installed normally in your project without needing to run `next lint`:

```bash
npm install --save-dev @next/eslint-plugin-next
 
yarn add --dev @next/eslint-plugin-next
 
pnpm add --save-dev @next/eslint-plugin-next
 
bun add --dev @next/eslint-plugin-next
```

This eliminates the risk of collisions or errors that can occur due to importing the same plugin or parser across multiple configurations.

#### Additional Configurations

If you already use a separate ESLint configuration and want to include `eslint-config-next`, ensure that it is extended last after other configurations. For example:

```json
{
  "extends": ["eslint:recommended", "next"]
}
```

The `next` configuration already handles setting default values for the `parser`, `plugins` and `settings` properties. There is no need to manually re-declare any of these properties unless you need a different configuration for your use case.

If you include any other shareable configurations, you will need to make sure that these properties are not overwritten or modified. Otherwise, we recommend removing any configurations that share behavior with the `next` configuration or extending directly from the Next.js ESLint plugin as mentioned above.

## Environment Variables

Next.js comes with built-in support for environment variables, which allows you to do the following:

- Use `.env` to load environment variables
- Bundle environment variables for the browser by prefixing with `NEXT_PUBLIC_`

### Loading Environment Variables

Next.js has built-in support for loading environment variables from `.env*` files into `process.env`.

```
# .env
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

Note: Next.js also supports multiline variables inside of your `.env*` files:

```
# .env

# you can write with line breaks
PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
...
Kh9NV...
...
-----END DSA PRIVATE KEY-----"

# or with `\n` inside double quotes
PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\nKh9NV...\n-----END DSA PRIVATE KEY-----\n"
```

Note: If you are using a `/src` folder, please note that Next.js will load the `.env` files only from the parent folder and not from the `/src` folder. This loads `process.env.DB_HOST`, `process.env.DB_USER`, and `process.env.DB_PASS` into the Node.js environment automatically allowing you to use them in Route Handlers.

For example:

```javascript
// app/api/route.js
export async function GET() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  })
  // ...
}
```

### Loading Environment Variables with @next/env

If you need to load environment variables outside of the Next.js runtime, such as in a root config file for an ORM or test runner, you can use the `@next/env` package.

This package is used internally by Next.js to load environment variables from `.env*` files.

To use it, install the package and use the `loadEnvConfig` function to load the environment variables:

```bash
npm install @next/env
```

```typescript
// envConfig.ts
import { loadEnvConfig } from '@next/env'

const projectDir = process.cwd()
loadEnvConfig(projectDir)
```

Then, you can import the configuration where needed. For example:

```typescript
// orm.config.ts
import './envConfig.ts'

export default defineConfig({
  dbCredentials: {
    connectionString: process.env.DATABASE_URL!,
  },
})
```

### Referencing Other Variables

Next.js will automatically expand variables that use `$` to reference other variables e.g. `$VARIABLE` inside of your `.env*` files. This allows you to reference other secrets. For example:

```
# .env
TWITTER_USER=nextjs
TWITTER_URL=https://x.com/$TWITTER_USER
```

In the above example, `process.env.TWITTER_URL` would be set to `https://x.com/nextjs`.

Good to know: If you need to use variable with a `$` in the actual value, it needs to be escaped e.g. `\$`.

### Bundling Environment Variables for the Browser

Non-`NEXT_PUBLIC_` environment variables are only available in the Node.js environment, meaning they aren't accessible to the browser (the client runs in a different environment).

In order to make the value of an environment variable accessible in the browser, Next.js can "inline" a value, at build time, into the js bundle that is delivered to the client, replacing all references to `process.env.[variable]` with a hard-coded value. To tell it to do this, you just have to prefix the variable with `NEXT_PUBLIC_`. For example:

```bash
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

This will tell Next.js to replace all references to `process.env.NEXT_PUBLIC_ANALYTICS_ID` in the Node.js environment with the value from the environment in which you run `next build`, allowing you to use it anywhere in your code. It will be inlined into any JavaScript sent to the browser.

Note: After being built, your app will no longer respond to changes to these environment variables. For instance, if you use a Heroku pipeline to promote slugs built in one environment to another environment, or if you build and deploy a single Docker image to multiple environments, all `NEXT_PUBLIC_` variables will be frozen with the value evaluated at build time, so these values need to be set appropriately when the project is built. If you need access to runtime environment values, you'll have to setup your own API to provide them to the client (either on demand or during initialization).

```javascript
// pages/index.js
import setupAnalyticsService from '../lib/my-analytics-service'

// 'NEXT_PUBLIC_ANALYTICS_ID' can be used here as it's prefixed by 'NEXT_PUBLIC_'.
// It will be transformed at build time to `setupAnalyticsService('abcdefghijk')`.
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

function HomePage() {
  return <h1>Hello World</h1>
}

export default HomePage
```

Note that dynamic lookups will not be inlined, such as:

```javascript
// This will NOT be inlined, because it uses a variable
const varName = 'NEXT_PUBLIC_ANALYTICS_ID'
setupAnalyticsService(process.env[varName])

// This will NOT be inlined, because it uses a variable
const env = process.env
setupAnalyticsService(env.NEXT_PUBLIC_ANALYTICS_ID)
```

### Runtime Environment Variables

Next.js can support both build time and runtime environment variables.

By default, environment variables are only available on the server. To expose an environment variable to the browser, it must be prefixed with `NEXT_PUBLIC_`. However, these public environment variables will be inlined into the JavaScript bundle during `next build`.

To read runtime environment variables, we recommend using `getServerSideProps` or incrementally adopting the App Router. With the App Router, we can safely read environment variables on the server during dynamic rendering. This allows you to use a singular Docker image that can be promoted through multiple environments with different values.

```javascript
import { unstable_noStore as noStore } from 'next/cache'

export default function Component() {
  noStore()
  // cookies(), headers(), and other dynamic functions
  // will also opt into dynamic rendering, meaning
  // this env variable is evaluated at runtime
  const value = process.env.MY_VALUE
  // ...
}
```

Good to know:

- You can run code on server startup using the `register` function.
- We do not recommend using the `runtimeConfig` option, as this does not work with the standalone output mode. Instead, we recommend incrementally adopting the App Router.

### Default Environment Variables

Typically, only `.env*` file is needed. However, sometimes you might want to add some defaults for the development (`next dev`) or production (`next start`) environment.

Next.js allows you to set defaults in `.env` (all environments), `.env.development` (development environment), and `.env.production` (production environment).

Good to know: `.env`, `.env.development`, and `.env.production` files should be included in your repository as they define defaults. All `.env` files are excluded in `.gitignore` by default, allowing you to opt-into committing these values to your repository.

### Environment Variables on Vercel

When deploying your Next.js application to Vercel, Environment Variables can be configured in the Project Settings.

All types of Environment Variables should be configured there. Even Environment Variables used in Development – which can be downloaded onto your local device afterwards.

If you've configured Development Environment Variables you can pull them into a `.env.local` for usage on your local machine using the following command:

```bash
vercel env pull
```

Good to know: When deploying your Next.js application to Vercel, your environment variables in `.env*` files will not be made available to Edge Runtime, unless their name are prefixed with `NEXT_PUBLIC_`. We strongly recommend managing your environment variables in Project Settings instead, from where all environment variables are available.

### Test Environment Variables

Apart from development and production environments, there is a 3rd option available: test. In the same way you can set defaults for development or production environments, you can do the same with a `.env.test` file for the testing environment (though this one is not as common as the previous two). Next.js will not load environment variables from `.env.development` or `.env.production` in the testing environment.

This one is useful when running tests with tools like jest or cypress where you need to set specific environment vars only for testing purposes. Test default values will be loaded if `NODE_ENV` is set to `test`, though you usually don't need to do this manually as testing tools will address it for you.

There is a small difference between test environment, and both development and production that you need to bear in mind: `.env.local` won't be loaded, as you expect tests to produce the same results for everyone. This way every test execution will use the same env defaults across different executions by ignoring your `.env.local` (which is intended to override the default set).

Good to know: similar to Default Environment Variables, `.env.test` file should be included in your repository, but `.env.test.local` shouldn't, as `.env*.local` are intended to be ignored through `.gitignore`.

While running unit tests you can make sure to load your environment variables the same way Next.js does by leveraging the `loadEnvConfig` function from the `@next/env` package.

```javascript
// The below can be used in a Jest global setup file or similar for your testing set-up
import { loadEnvConfig } from '@next/env'

export default async () => {
  const projectDir = process.cwd()
  loadEnvConfig(projectDir)
}
```

### Environment Variable Load Order

Environment variables are looked up in the following places, in order, stopping once the variable is found.

1. `process.env`
2. `.env.$(NODE_ENV).local`
3. `.env.local` (Not checked when `NODE_ENV` is `test`.)
4. `.env.$(NODE_ENV)`
5. `.env`

For example, if `NODE_ENV` is `development` and you define a variable in both `.env.development.local` and `.env`, the value in `.env.development.local` will be used.

Good to know: The allowed values for `NODE_ENV` are `production`, `development` and `test`.

Good to know:
- If you are using a `/src` directory, `.env.*` files should remain in the root of your project.
- If the environment variable `NODE_ENV` is unassigned, Next.js automatically assigns `development` when running the `next dev` command, or `production` for all other commands.

## Absolute Imports and Module Path Aliases

Next.js has in-built support for the "paths" and "baseUrl" options of tsconfig.json and jsconfig.json files.

These options allow you to alias project directories to absolute paths, making it easier to import modules. For example:

```javascript
// before
import { Button } from '../../../components/button'

// after
import { Button } from '@/components/button'
```

> Good to know: create-next-app will prompt to configure these options for you.

### Absolute Imports

The `baseUrl` configuration option allows you to import directly from the root of the project.

An example of this configuration:

tsconfig.json or jsconfig.json:

```json
{
  "compilerOptions": {
    "baseUrl": "."
  }
}
```

components/button.tsx:

```typescript
export default function Button() {
  return <button>Click me</button>
}
```

app/page.tsx:

```typescript
import Button from 'components/button'

export default function HomePage() {
  return (
    <>
      <h1>Hello World</h1>
      <Button />
    </>
  )
}
```

### Module Aliases

In addition to configuring the `baseUrl` path, you can use the "paths" option to "alias" module paths.

For example, the following configuration maps `@/components/*` to `components/*`:

tsconfig.json or jsconfig.json:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"]
    }
  }
}
```

components/button.tsx:

```typescript
export default function Button() {
  return <button>Click me</button>
}
```

app/page.tsx:

```typescript
import Button from '@/components/button'

export default function HomePage() {
  return (
    <>
      <h1>Hello World</h1>
      <Button />
    </>
  )
}
```

Each of the "paths" are relative to the `baseUrl` location. For example:

tsconfig.json or jsconfig.json:

```json
{
  "compilerOptions": {
    "baseUrl": "src/",
    "paths": {
      "@/styles/*": ["styles/*"],
      "@/components/*": ["components/*"]
    }
  }
}
```

app/page.tsx:

```typescript
import Button from '@/components/button'
import '@/styles/styles.css'
import Helper from 'utils/helper'

export default function HomePage() {
  return (
    <Helper>
      <h1>Hello World</h1>
      <Button />
    </Helper>
  )
}
```

## Markdown and MDX

Markdown is a lightweight markup language used to format text. It allows you to write using plain text syntax and convert it to structurally valid HTML. It's commonly used for writing content on websites and blogs.

You write...

```markdown
I **love** using [Next.js](https://nextjs.org/)
```

Output:

```html
<p>I <strong>love</strong> using <a href="https://nextjs.org/">Next.js</a></p>
```

MDX is a superset of markdown that lets you write JSX directly in your markdown files. It is a powerful way to add dynamic interactivity and embed React components within your content.

Next.js can support both local MDX content inside your application, as well as remote MDX files fetched dynamically on the server. The Next.js plugin handles transforming markdown and React components into HTML, including support for usage in Server Components (the default in App Router).

Good to know: View the Portfolio Starter Kit template for a complete working example.

### Install dependencies

The `@next/mdx` package, and related packages, are used to configure Next.js so it can process markdown and MDX. It sources data from local files, allowing you to create pages with a `.md` or `.mdx` extension, directly in your `/pages` or `/app` directory.

Install these packages to render MDX with Next.js:

```bash
npm install @next/mdx @mdx-js/loader @mdx-js/react @types/mdx
```

### Configure next.config.mjs

Update the `next.config.mjs` file at your project's root to configure it to use MDX:

```javascript
import createMDX from '@next/mdx'

/** @type {import('next').NextConfig} */
const nextConfig = {
  // Configure `pageExtensions` to include markdown and MDX files
  pageExtensions: ['js', 'jsx', 'md', 'mdx', 'ts', 'tsx'],
  // Optionally, add any other Next.js config below
}

const withMDX = createMDX({
  // Add markdown plugins here, as desired
})

// Merge MDX config with Next.js config
export default withMDX(nextConfig)
```

This allows `.md` and `.mdx` files to act as pages, routes, or imports in your application.

### Add an mdx-components.tsx file

Create an `mdx-components.tsx` (or `.js`) file in the root of your project to define global MDX Components. For example, at the same level as `pages` or `app`, or inside `src` if applicable.

```typescript
import type { MDXComponents } from 'mdx/types'

export function useMDXComponents(components: MDXComponents): MDXComponents {
  return {
    ...components,
  }
}
```

Good to know:

- `mdx-components.tsx` is required to use `@next/mdx` with App Router and will not work without it.
- Learn more about the `mdx-components.tsx` file convention.
- Learn how to use custom styles and components.

### Rendering MDX

You can render MDX using Next.js's file based routing or by importing MDX files into other pages.

#### Using file based routing

When using file based routing, you can use MDX pages like any other page.

In App Router apps, that includes being able to use metadata.

Create a new MDX page within the `/app` directory:

```
my-project
├── app
│   └── mdx-page
│       └── page.(mdx/md)
|── mdx-components.(tsx/js)
└── package.json
```

You can use MDX in these files, and even import React components, directly inside your MDX page:

```mdx
import { MyComponent } from 'my-component'

# Welcome to my MDX page!

This is some **bold** and _italics_ text.

This is a list in markdown:

- One
- Two
- Three

Checkout my React component:

<MyComponent />
```

Navigating to the `/mdx-page` route should display your rendered MDX page.

#### Using imports

Create a new page within the `/app` directory and an MDX file wherever you'd like:

```
my-project
├── app
│   └── mdx-page
│       └── page.(tsx/js)
├── markdown
│   └── welcome.(mdx/md)
|── mdx-components.(tsx/js)
└── package.json
```

You can use MDX in these files, and even import React components, directly inside your MDX page:

Import the MDX file inside the page to display the content:

```typescript
import Welcome from '@/markdown/welcome.mdx'

export default function Page() {
  return <Welcome />
}
```

Navigating to the `/mdx-page` route should display your rendered MDX page.

### Using custom styles and components

Markdown, when rendered, maps to native HTML elements. For example, writing the following markdown:

```markdown
## This is a heading

This is a list in markdown:

- One
- Two
- Three
```

Generates the following HTML:

```html
<h2>This is a heading</h2>

<p>This is a list in markdown:</p>

<ul>
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

To style your markdown, you can provide custom components that map to the generated HTML elements. Styles and components can be implemented globally, locally, and with shared layouts.

#### Global styles and components

Adding styles and components in `mdx-components.tsx` will affect all MDX files in your application.

```typescript
import type { MDXComponents } from 'mdx/types'
import Image, { ImageProps } from 'next/image'

// This file allows you to provide custom React components
// to be used in MDX files. You can import and use any
// React component you want, including inline styles,
// components from other libraries, and more.

export function useMDXComponents(components: MDXComponents): MDXComponents {
  return {
    // Allows customizing built-in components, e.g. to add styling.
    h1: ({ children }) => (
      <h1 style={{ color: 'red', fontSize: '48px' }}>{children}</h1>
    ),
    img: (props) => (
      <Image
        sizes="100vw"
        style={{ width: '100%', height: 'auto' }}
        {...(props as ImageProps)}
      />
    ),
    ...components,
  }
}
```

#### Local styles and components

You can apply local styles and components to specific pages by passing them into imported MDX components. These will merge with and override global styles and components.

```typescript
import Welcome from '@/markdown/welcome.mdx'

function CustomH1({ children }) {
  return <h1 style={{ color: 'blue', fontSize: '100px' }}>{children}</h1>
}

const overrideComponents = {
  h1: CustomH1,
}

export default function Page() {
  return <Welcome components={overrideComponents} />
}
```

#### Shared layouts

To share a layout across MDX pages, you can use the built-in layouts support with the App Router.

```typescript
export default function MdxLayout({ children }: { children: React.ReactNode }) {
  // Create any shared layout or styles here
  return <div style={{ color: 'blue' }}>{children}</div>
}
```

#### Using Tailwind typography plugin

If you are using Tailwind to style your application, using the `@tailwindcss/typography` plugin will allow you to reuse your Tailwind configuration and styles in your markdown files.

The plugin adds a set of prose classes that can be used to add typographic styles to content blocks that come from sources, like markdown.

Install Tailwind typography and use with shared layouts to add the prose you want.

```typescript
export default function MdxLayout({ children }: { children: React.ReactNode }) {
  // Create any shared layout or styles here
  return (
    <div className="prose prose-headings:mt-8 prose-headings:font-semibold prose-headings:text-black prose-h1:text-5xl prose-h2:text-4xl prose-h3:text-3xl prose-h4:text-2xl prose-h5:text-xl prose-h6:text-lg dark:prose-headings:text-white">
      {children}
    </div>
  )
}
```

### Frontmatter

Frontmatter is a YAML like key/value pairing that can be used to store data about a page. `@next/mdx` does not support frontmatter by default, though there are many solutions for adding frontmatter to your MDX content, such as:

- remark-frontmatter
- remark-mdx-frontmatter
- gray-matter

`@next/mdx` does allow you to use exports like any other JavaScript component:

Metadata can now be referenced outside of the MDX file:

```typescript
import BlogPost, { metadata } from '@/content/blog-post.mdx'

export default function Page() {
  console.log('metadata': metadata)
  //=> { author: 'John Doe' }
  return <BlogPost />
}
```

A common use case for this is when you want to iterate over a collection of MDX and extract data. For example, creating a blog index page from all blog posts. You can use packages like Node's `fs` module or `globby` to read a directory of posts and extract the metadata.

Good to know:

- Using `fs`, `globby`, etc. can only be used server-side.
- View the Portfolio Starter Kit template for a complete working example.

### Remark and Rehype Plugins

You can optionally provide remark and rehype plugins to transform the MDX content.

For example, you can use `remark-gfm` to support GitHub Flavored Markdown.

Since the remark and rehype ecosystem is ESM only, you'll need to use `next.config.mjs` as the configuration file.

```javascript
import remarkGfm from 'remark-gfm'
import createMDX from '@next/mdx'

/** @type {import('next').NextConfig} */
const nextConfig = {
  // Configure `pageExtensions`` to include MDX files
  pageExtensions: ['js', 'jsx', 'md', 'mdx', 'ts', 'tsx'],
  // Optionally, add any other Next.js config below
}

const withMDX = createMDX({
  // Add markdown plugins here, as desired
  options: {
    remarkPlugins: [remarkGfm],
    rehypePlugins: [],
  },
})

// Wrap MDX and Next.js config with each other
export default withMDX(nextConfig)
```

### Remote MDX

If your MDX files or content lives somewhere else, you can fetch it dynamically on the server. This is useful for content stored in a separate local folder, CMS, database, or anywhere else. A popular community package for this use is `next-mdx-remote`.

Good to know: Please proceed with caution. MDX compiles to JavaScript and is executed on the server. You should only fetch MDX content from a trusted source, otherwise this can lead to remote code execution (RCE).

The following example uses `next-mdx-remote`:

```typescript
import { MDXRemote } from 'next-mdx-remote/rsc'

export default async function RemoteMdxPage() {
  // MDX text - can be from a local file, database, CMS, fetch, anywhere...
  const res = await fetch('https://...')
  const markdown = await res.text()
  return <MDXRemote source={markdown} />
}
```

Navigating to the `/mdx-page-remote` route should display your rendered MDX.

### Deep Dive: How do you transform markdown into HTML?

React does not natively understand markdown. The markdown plaintext needs to first be transformed into HTML. This can be accomplished with remark and rehype.

remark is an ecosystem of tools around markdown. rehype is the same, but for HTML. For example, the following code snippet transforms markdown into HTML:

```javascript
import { unified } from 'unified'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import rehypeSanitize from 'rehype-sanitize'
import rehypeStringify from 'rehype-stringify'

main()

async function main() {
  const file = await unified()
    .use(remarkParse) // Convert into markdown AST
    .use(remarkRehype) // Transform to HTML AST
    .use(rehypeSanitize) // Sanitize HTML input
    .use(rehypeStringify) // Convert AST into serialized HTML
    .process('Hello, Next.js!')

  console.log(String(file)) // <p>Hello, Next.js!</p>
}
```

The remark and rehype ecosystem contains plugins for syntax highlighting, linking headings, generating a table of contents, and more.

When using `@next/mdx` as shown above, you do not need to use remark or rehype directly, as it is handled for you. We're describing it here for a deeper understanding of what the `@next/mdx` package is doing underneath.

### Using the Rust-based MDX compiler (experimental)

Next.js supports a new MDX compiler written in Rust. This compiler is still experimental and is not recommended for production use. To use the new compiler, you need to configure `next.config.js` when you pass it to `withMDX`:

```javascript
module.exports = withMDX({
  experimental: {
    mdxRs: true,
  },
})
```

`mdxRs` also accepts an object to configure how to transform mdx files.

```javascript
module.exports = withMDX({
  experimental: {
    mdxRs: {
      jsxRuntime?: string            // Custom jsx runtime
      jsxImportSource?: string       // Custom jsx import source,
      mdxType?: 'gfm' | 'commonmark' // Configure what kind of mdx syntax will be used to parse & transform
    },
  },
})
```

Good to know:

This option is required when processing markdown and MDX while using Turbopack (`next dev --turbo`).

## src Directory

As an alternative to having the special Next.js `app` or `pages` directories in the root of your project, Next.js also supports the common pattern of placing application code under the `src` directory.

This separates application code from project configuration files which mostly live in the root of a project, which is preferred by some individuals and teams.

To use the src directory, move the app Router folder or pages Router folder to `src/app` or `src/pages` respectively.

An example folder structure with the `src` directory:

Good to know:

- The `/public` directory should remain in the root of your project.
- Config files like `package.json`, `next.config.js` and `tsconfig.json` should remain in the root of your project.
- `.env.*` files should remain in the root of your project.
- `src/app` or `src/pages` will be ignored if `app` or `pages` are present in the root directory.
- If you're using `src`, you'll probably also move other application folders such as `/components` or `/lib`.
- If you're using Middleware, ensure it is placed inside the `src` directory.
- If you're using Tailwind CSS, you'll need to add the `/src` prefix to the `tailwind.config.js` file in the content section.
- If you are using TypeScript paths for imports such as `@/*`, you should update the `paths` object in `tsconfig.json` to include `src/`.

## Custom Server

Next.js includes its own server with `next start` by default. If you have an existing backend, you can still use it with Next.js (this is not a custom server). A custom Next.js server allows you to programmatically start a server for custom patterns. The majority of the time, you will not need this approach. However, it's available if you need to eject.

Good to know:

- Before deciding to use a custom server, keep in mind that it should only be used when the integrated router of Next.js can't meet your app requirements. A custom server will remove important performance optimizations, like Automatic Static Optimization.
- A custom server cannot be deployed on Vercel.
- When using standalone output mode, it does not trace custom server files. This mode outputs a separate minimal `server.js` file, instead. These cannot be used together.

Take a look at the following example of a custom server:

```typescript
// server.ts
import { createServer } from 'http'
import { parse } from 'url'
import next from 'next'
 
const port = parseInt(process.env.PORT || '3000', 10)
const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()
 
app.prepare().then(() => {
  createServer((req, res) => {
    const parsedUrl = parse(req.url!, true)
    handle(req, res, parsedUrl)
  }).listen(port)
 
  console.log(
    `> Server listening at http://localhost:${port} as ${
      dev ? 'development' : process.env.NODE_ENV
    }`
  )
})
```

`server.js` does not run through the Next.js Compiler or bundling process. Make sure the syntax and source code this file requires are compatible with the current Node.js version you are using. View an example.

To run the custom server, you'll need to update the scripts in `package.json` like so:

```json
{
  "scripts": {
    "dev": "node server.js",
    "build": "next build",
    "start": "NODE_ENV=production node server.js"
  }
}
```

Alternatively, you can set up nodemon (example). The custom server uses the following import to connect the server with the Next.js application:

```javascript
import next from 'next'
 
const app = next({})
```

The above `next` import is a function that receives an object with the following options:

| Option | Type | Description |
|--------|------|-------------|
| `conf` | Object | The same object you would use in `next.config.js`. Defaults to `{}` |
| `customServer` | Boolean | (Optional) Set to false when the server was created by Next.js |
| `dev` | Boolean | (Optional) Whether or not to launch Next.js in dev mode. Defaults to `false` |
| `dir` | String | (Optional) Location of the Next.js project. Defaults to `'.'` |
| `quiet` | Boolean | (Optional) Hide error messages containing server information. Defaults to `false` |
| `hostname` | String | (Optional) The hostname the server is running behind |
| `port` | Number | (Optional) The port the server is running behind |
| `httpServer` | node:http#Server | (Optional) The HTTP Server that Next.js is running behind |

The returned `app` can then be used to let Next.js handle requests as required.

## Draft Mode

Static rendering is useful when your pages fetch data from a headless CMS. However, it's not ideal when you're writing a draft on your headless CMS and want to view the draft immediately on your page. You'd want Next.js to render these pages at request time instead of build time and fetch the draft content instead of the published content. You'd want Next.js to switch to dynamic rendering only for this specific case.

Next.js has a feature called Draft Mode which solves this problem. Here are instructions on how to use it.

### Step 1: Create and access the Route Handler

First, create a Route Handler. It can have any name - e.g. `app/api/draft/route.ts`

Then, import `draftMode` from `next/headers` and call the `enable()` method.

```typescript
// app/api/draft/route.ts
// route handler enabling draft mode
import { draftMode } from 'next/headers'
 
export async function GET(request: Request) {
  draftMode().enable()
  return new Response('Draft mode is enabled')
}
```

This will set a cookie to enable draft mode. Subsequent requests containing this cookie will trigger Draft Mode changing the behavior for statically generated pages (more on this later).

You can test this manually by visiting `/api/draft` and looking at your browser's developer tools. Notice the `Set-Cookie` response header with a cookie named `__prerender_bypass`.

#### Securely accessing it from your Headless CMS

In practice, you'd want to call this Route Handler securely from your headless CMS. The specific steps will vary depending on which headless CMS you're using, but here are some common steps you could take.

These steps assume that the headless CMS you're using supports setting custom draft URLs. If it doesn't, you can still use this method to secure your draft URLs, but you'll need to construct and access the draft URL manually.

First, you should create a secret token string using a token generator of your choice. This secret will only be known by your Next.js app and your headless CMS. This secret prevents people who don't have access to your CMS from accessing draft URLs.

Second, if your headless CMS supports setting custom draft URLs, specify the following as the draft URL. This assumes that your Route Handler is located at `app/api/draft/route.ts`

```
https://<your-site>/api/draft?secret=<token>&slug=<path>
```

- `<your-site>` should be your deployment domain.
- `<token>` should be replaced with the secret token you generated.
- `<path>` should be the path for the page that you want to view. If you want to view `/posts/foo`, then you should use `&slug=/posts/foo`.

Your headless CMS might allow you to include a variable in the draft URL so that `<path>` can be set dynamically based on the CMS's data like so: `&slug=/posts/{entry.fields.slug}`

Finally, in the Route Handler:

- Check that the secret matches and that the slug parameter exists (if not, the request should fail).
- Call `draftMode.enable()` to set the cookie.
- Then redirect the browser to the path specified by slug.

```typescript
// app/api/draft/route.ts
// route handler with secret and slug
import { draftMode } from 'next/headers'
import { redirect } from 'next/navigation'
 
export async function GET(request: Request) {
  // Parse query string parameters
  const { searchParams } = new URL(request.url)
  const secret = searchParams.get('secret')
  const slug = searchParams.get('slug')
 
  // Check the secret and next parameters
  // This secret should only be known to this route handler and the CMS
  if (secret !== 'MY_SECRET_TOKEN' || !slug) {
    return new Response('Invalid token', { status: 401 })
  }
 
  // Fetch the headless CMS to check if the provided `slug` exists
  // getPostBySlug would implement the required fetching logic to the headless CMS
  const post = await getPostBySlug(slug)
 
  // If the slug doesn't exist prevent draft mode from being enabled
  if (!post) {
    return new Response('Invalid slug', { status: 401 })
  }
 
  // Enable Draft Mode by setting the cookie
  draftMode().enable()
 
  // Redirect to the path from the fetched post
  // We don't redirect to searchParams.slug as that might lead to open redirect vulnerabilities
  redirect(post.slug)
}
```

If it succeeds, then the browser will be redirected to the path you want to view with the draft mode cookie.

### Step 2: Update page

The next step is to update your page to check the value of `draftMode().isEnabled`.

If you request a page which has the cookie set, then data will be fetched at request time (instead of at build time).

Furthermore, the value of `isEnabled` will be true.

```typescript
// app/page.tsx
// page that fetches data
import { draftMode } from 'next/headers'
 
async function getData() {
  const { isEnabled } = draftMode()
 
  const url = isEnabled
    ? 'https://draft.example.com'
    : 'https://production.example.com'
 
  const res = await fetch(url)
 
  return res.json()
}
 
export default async function Page() {
  const { title, desc } = await getData()
 
  return (
    <main>
      <h1>{title}</h1>
      <p>{desc}</p>
    </main>
  )
}
```

That's it! If you access the draft Route Handler (with secret and slug) from your headless CMS or manually, you should now be able to see the draft content. And if you update your draft without publishing, you should be able to view the draft.

Set this as the draft URL on your headless CMS or access manually, and you should be able to see the draft.

```
https://<your-site>/api/draft?secret=<token>&slug=<path>
```

### More Details

#### Clear the Draft Mode cookie

By default, the Draft Mode session ends when the browser is closed.

To clear the Draft Mode cookie manually, create a Route Handler that calls `draftMode().disable()`:

```typescript
// app/api/disable-draft/route.ts
import { draftMode } from 'next/headers'
 
export async function GET(request: Request) {
  draftMode().disable()
  return new Response('Draft mode is disabled')
}
```

Then, send a request to `/api/disable-draft` to invoke the Route Handler. If calling this route using `next/link`, you must pass `prefetch={false}` to prevent accidentally deleting the cookie on prefetch.

#### Unique per next build

A new bypass cookie value will be generated each time you run `next build`.

This ensures that the bypass cookie can't be guessed.

Good to know: To test Draft Mode locally over HTTP, your browser will need to allow third-party cookies and local storage access.

## Content Security Policy

Content Security Policy (CSP) is important to guard your Next.js application against various security threats such as cross-site scripting (XSS), clickjacking, and other code injection attacks.

By using CSP, developers can specify which origins are permissible for content sources, scripts, stylesheets, images, fonts, objects, media (audio, video), iframes, and more.

### Nonces

A nonce is a unique, random string of characters created for a one-time use. It is used in conjunction with CSP to selectively allow certain inline scripts or styles to execute, bypassing strict CSP directives.

#### Why use a nonce?

Even though CSPs are designed to block malicious scripts, there are legitimate scenarios where inline scripts are necessary. In such cases, nonces offer a way to allow these scripts to execute if they have the correct nonce.

### Adding a nonce with Middleware

Middleware enables you to add headers and generate nonces before the page renders.

Every time a page is viewed, a fresh nonce should be generated. This means that you must use dynamic rendering to add nonces.

For example:

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server'
 
export function middleware(request: NextRequest) {
  const nonce = Buffer.from(crypto.randomUUID()).toString('base64')
  const cspHeader = `
    default-src 'self';
    script-src 'self' 'nonce-${nonce}' 'strict-dynamic';
    style-src 'self' 'nonce-${nonce}';
    img-src 'self' blob: data:;
    font-src 'self';
    object-src 'none';
    base-uri 'self';
    form-action 'self';
    frame-ancestors 'none';
    upgrade-insecure-requests;
`
  // Replace newline characters and spaces
  const contentSecurityPolicyHeaderValue = cspHeader
    .replace(/\s{2,}/g, ' ')
    .trim()
 
  const requestHeaders = new Headers(request.headers)
  requestHeaders.set('x-nonce', nonce)
 
  requestHeaders.set(
    'Content-Security-Policy',
    contentSecurityPolicyHeaderValue
  )
 
  const response = NextResponse.next({
    request: {
      headers: requestHeaders,
    },
  })
  response.headers.set(
    'Content-Security-Policy',
    contentSecurityPolicyHeaderValue
  )
 
  return response
}
```

By default, Middleware runs on all requests. You can filter Middleware to run on specific paths using a matcher.

We recommend ignoring matching prefetches (from `next/link`) and static assets that don't need the CSP header.

```typescript
// middleware.ts
export const config = {
  matcher: [
    /*
     * Match all request paths except for the ones starting with:
     * - api (API routes)
     * - _next/static (static files)
     * - _next/image (image optimization files)
     * - favicon.ico (favicon file)
     */
    {
      source: '/((?!api|_next/static|_next/image|favicon.ico).*)',
      missing: [
        { type: 'header', key: 'next-router-prefetch' },
        { type: 'header', key: 'purpose', value: 'prefetch' },
      ],
    },
  ],
}
```

### Reading the nonce

You can now read the nonce from a Server Component using headers:

```typescript
// app/page.tsx
import { headers } from 'next/headers'
import Script from 'next/script'
 
export default function Page() {
  const nonce = headers().get('x-nonce')
 
  return (
    <Script
      src="https://www.googletagmanager.com/gtag/js"
      strategy="afterInteractive"
      nonce={nonce}
    />
  )
}
```

### Without Nonces

For applications that do not require nonces, you can set the CSP header directly in your `next.config.js` file:

```javascript
// next.config.js
const cspHeader = `
    default-src 'self';
    script-src 'self' 'unsafe-eval' 'unsafe-inline';
    style-src 'self' 'unsafe-inline';
    img-src 'self' blob: data:;
    font-src 'self';
    object-src 'none';
    base-uri 'self';
    form-action 'self';
    frame-ancestors 'none';
    upgrade-insecure-requests;
`
 
module.exports = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: cspHeader.replace(/\n/g, ''),
          },
        ],
      },
    ]
  },
}
```

### Version History

We recommend using v13.4.20+ of Next.js to properly handle and apply nonces.

# Authentication

Understanding authentication is crucial for protecting your application's data. This page will guide you through what React and Next.js features to use to implement auth.

Before starting, it helps to break down the process into three concepts:

- **Authentication**: Verifies if the user is who they say they are. It requires the user to prove their identity with something they have, such as a username and password.
- **Session Management**: Tracks the user's auth state across requests.
- **Authorization**: Decides what routes and data the user can access.

This diagram shows the authentication flow using React and Next.js features:

[Diagram showing the authentication flow with React and Next.js features]

The examples on this page walk through basic username and password auth for educational purposes. While you can implement a custom auth solution, for increased security and simplicity, we recommend using an authentication library. These offer built-in solutions for authentication, session management, and authorization, as well as additional features such as social logins, multi-factor authentication, and role-based access control. You can find a list in the Auth Libraries section.

## Authentication

### Sign-up and login functionality

> Good to know: These examples use React's `useActionState` hook, which is available in React 19 RC. If you are using an earlier version of React, use `useFormState` instead. See the React docs for more information.

You can use the `<form>` element with React's Server Actions and `useActionState` to capture user credentials, validate form fields, and call your Authentication Provider's API or database.

Since Server Actions always execute on the server, they provide a secure environment for handling authentication logic.

Here are the steps to implement signup/login functionality:

1. Capture user credentials
To capture user credentials, create a form that invokes a Server Action on submission. For example, a signup form that accepts the user's name, email, and password:

```typescript
// app/ui/signup-form.tsx
import { signup } from '@/app/actions/auth'
 
export function SignupForm() {
  return (
    <form action={signup}>
      <div>
        <label htmlFor="name">Name</label>
        <input id="name" name="name" placeholder="Name" />
      </div>
      <div>
        <label htmlFor="email">Email</label>
        <input id="email" name="email" type="email" placeholder="Email" />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input id="password" name="password" type="password" />
      </div>
      <button type="submit">Sign Up</button>
    </form>
  )
}
```

```typescript
// app/actions/auth.tsx
export async function signup(formData: FormData) {}
```

2. Validate form fields on the server
Use the Server Action to validate the form fields on the server. If your authentication provider doesn't provide form validation, you can use a schema validation library like Zod or Yup.

Using Zod as an example, you can define a form schema with appropriate error messages:

```typescript
// app/lib/definitions.ts
import { z } from 'zod'
 
export const SignupFormSchema = z.object({
  name: z
    .string()
    .min(2, { message: 'Name must be at least 2 characters long.' })
    .trim(),
  email: z.string().email({ message: 'Please enter a valid email.' }).trim(),
  password: z
    .string()
    .min(8, { message: 'Be at least 8 characters long' })
    .regex(/[a-zA-Z]/, { message: 'Contain at least one letter.' })
    .regex(/[0-9]/, { message: 'Contain at least one number.' })
    .regex(/[^a-zA-Z0-9]/, {
      message: 'Contain at least one special character.',
    })
    .trim(),
})
 
export type FormState =
  | {
      errors?: {
        name?: string[]
        email?: string[]
        password?: string[]
      }
      message?: string
    }
  | undefined
```

To prevent unnecessary calls to your authentication provider's API or database, you can return early in the Server Action if any form fields do not match the defined schema.

```typescript
// app/actions/auth.ts
import { SignupFormSchema, FormState } from '@/app/lib/definitions'
 
export async function signup(state: FormState, formData: FormData) {
  // Validate form fields
  const validatedFields = SignupFormSchema.safeParse({
    name: formData.get('name'),
    email: formData.get('email'),
    password: formData.get('password'),
  })
 
  // If any form fields are invalid, return early
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
    }
  }
 
  // Call the provider or db to create a user...
}
```

Back in your `<SignupForm />`, you can use React's `useActionState()` hook to display validation errors and a pending state while the form is submitting:

```typescript
// app/ui/signup-form.tsx
'use client'
 
import { useActionState } from 'react'
import { signup } from '@/app/actions/auth'
 
export function SignupForm() {
  const [state, action, pending] = useActionState(signup, undefined)
 
  return (
    <form action={action}>
      <div>
        <label htmlFor="name">Name</label>
        <input id="name" name="name" placeholder="Name" />
      </div>
      {state?.errors?.name && <p>{state.errors.name}</p>}
 
      <div>
        <label htmlFor="email">Email</label>
        <input id="email" name="email" placeholder="Email" />
      </div>
      {state?.errors?.email && <p>{state.errors.email}</p>}
 
      <div>
        <label htmlFor="password">Password</label>
        <input id="password" name="password" type="password" />
      </div>
      {state?.errors?.password && (
        <div>
          <p>Password must:</p>
          <ul>
            {state.errors.password.map((error) => (
              <li key={error}>- {error}</li>
            ))}
          </ul>
        </div>
      )}
      <button aria-disabled={pending} type="submit">
        {pending ? 'Submitting...' : 'Sign up'}
      </button>
    </form>
  )
}
```

> Good to know: Alternatively, you can use the `useFormStatus` hook to display the pending state.

3. Create a user or check user credentials
After validating form fields, you can create a new user account or check if the user exists by calling your authentication provider's API or database.

Continuing from the previous example:

```typescript
// app/actions/auth.tsx
export async function signup(state: FormState, formData: FormData) {
  // 1. Validate form fields
  // ...
 
  // 2. Prepare data for insertion into database
  const { name, email, password } = validatedFields.data
  // e.g. Hash the user's password before storing it
  const hashedPassword = await bcrypt.hash(password, 10)
 
  // 3. Insert the user into the database or call an Auth Library's API
  const data = await db
    .insert(users)
    .values({
      name,
      email,
      password: hashedPassword,
    })
    .returning({ id: users.id })
 
  const user = data[0]
 
  if (!user) {
    return {
      message: 'An error occurred while creating your account.',
    }
  }
 
  // TODO:
  // 4. Create user session
  // 5. Redirect user
}
```

After successfully creating the user account or verifying the user credentials, you can create a session to manage the user's auth state. Depending on your session management strategy, the session can be stored in a cookie or database, or both. Continue to the Session Management section to learn more.

> Tips:
> 
> - The example above is verbose since it breaks down the authentication steps for the purpose of education. This highlights that implementing your own secure solution can quickly become complex. Consider using an Auth Library to simplify the process.
> - To improve the user experience, you may want to check for duplicate emails or usernames earlier in the registration flow. For example, as the user types in a username or the input field loses focus. This can help prevent unnecessary form submissions and provide immediate feedback to the user. You can debounce requests with libraries such as use-debounce to manage the frequency of these checks.

## Session Management

Session management ensures that the user's authenticated state is preserved across requests. It involves creating, storing, refreshing, and deleting sessions or tokens.

There are two types of sessions:

- **Stateless**: Session data (or a token) is stored in the browser's cookies. The cookie is sent with each request, allowing the session to be verified on the server. This method is simpler, but can be less secure if not implemented correctly.
- **Database**: Session data is stored in a database, with the user's browser only receiving the encrypted session ID. This method is more secure, but can be complex and use more server resources.

> Good to know: While you can use either method, or both, we recommend using session management library such as iron-session or Jose.

### Stateless Sessions

To create and manage stateless sessions, there are a few steps you need to follow:

1. Generate a secret key, which will be used to sign your session, and store it as an environment variable.
2. Write logic to encrypt/decrypt session data using a session management library.
3. Manage cookies using the Next.js `cookies()` API.

In addition to the above, consider adding functionality to update (or refresh) the session when the user returns to the application, and delete the session when the user logs out.

> Good to know: Check if your auth library includes session management.

1. Generating a secret key
There are a few ways you can generate secret key to sign your session. For example, you may choose to use the `openssl` command in your terminal:

```
openssl rand -base64 32
```

This command generates a 32-character random string that you can use as your secret key and store in your environment variables file:

```
# .env
SESSION_SECRET=your_secret_key
```

You can then reference this key in your session management logic:

```javascript
// app/lib/session.js
const secretKey = process.env.SESSION_SECRET
```

2. Encrypting and decrypting sessions
Next, you can use your preferred session management library to encrypt and decrypt sessions. Continuing from the previous example, we'll use Jose (compatible with the Edge Runtime) and React's server-only package to ensure that your session management logic is only executed on the server.

```typescript
// app/lib/session.ts
import 'server-only'
import { SignJWT, jwtVerify } from 'jose'
import { SessionPayload } from '@/app/lib/definitions'
 
const secretKey = process.env.SESSION_SECRET
const encodedKey = new TextEncoder().encode(secretKey)
 
export async function encrypt(payload: SessionPayload) {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: 'HS256' })
    .setIssuedAt()
    .setExpirationTime('7d')
    .sign(encodedKey)
}
 
export async function decrypt(session: string | undefined = '') {
  try {
    const { payload } = await jwtVerify(session, encodedKey, {
      algorithms: ['HS256'],
    })
    return payload
  } catch (error) {
    console.log('Failed to verify session')
  }
}
```

> Tips:
> 
> - The payload should contain the minimum, unique user data that'll be used in subsequent requests, such as the user's ID, role, etc. It should not contain personally identifiable information like phone number, email address, credit card information, etc, or sensitive data like passwords.

3. Setting cookies (recommended options)
To store the session in a cookie, use the Next.js `cookies()` API. The cookie should be set on the server, and include the recommended options:

- `HttpOnly`: Prevents client-side JavaScript from accessing the cookie.
- `Secure`: Use https to send the cookie.
- `SameSite`: Specify whether the cookie can be sent with cross-site requests.
- `Max-Age` or `Expires`: Delete the cookie after a certain period.
- `Path`: Define the URL path for the cookie.

Please refer to MDN for more information on each of these options.

```typescript
// app/lib/session.ts
import 'server-only'
import { cookies } from 'next/headers'
 
export async function createSession(userId: string) {
  const expiresAt = new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
  const session = await encrypt({ userId, expiresAt })
 
  cookies().set('session', session, {
    httpOnly: true,
    secure: true,
    expires: expiresAt,
    sameSite: 'lax',
    path: '/',
  })
}
```

Back in your Server Action, you can invoke the `createSession()` function, and use the `redirect()` API to redirect the user to the appropriate page:

```typescript
// app/actions/auth.ts
import { createSession } from '@/app/lib/session'
 
export async function signup(state: FormState, formData: FormData) {
  // Previous steps:
  // 1. Validate form fields
  // 2. Prepare data for insertion into database
  // 3. Insert the user into the database or call an Library API
 
  // Current steps:
  // 4. Create user session
  await createSession(user.id)
  // 5. Redirect user
  redirect('/profile')
}
```

> Tips:
> 
> - Cookies should be set on the server to prevent client-side tampering.
> - 🎥 Watch: Learn more about stateless sessions and authentication with Next.js → [YouTube (11 minutes)](https://www.youtube.com/watch?v=dQw4w9WgXcQ).

### Updating (or refreshing) sessions

You can also extend the session's expiration time. This is useful for keeping the user logged in after they access the application again. For example:

```typescript
// app/lib/session.ts
import 'server-only'
import { cookies } from 'next/headers'
import { decrypt } from '@/app/lib/session'
 
export async function updateSession() {
  const session = cookies().get('session')?.value
  const payload = await decrypt(session)
 
  if (!session || !payload) {
    return null
  }
 
  const expires = new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
  cookies().set('session', session, {
    httpOnly: true,
    secure: true,
    expires: expires,
    sameSite: 'lax',
    path: '/',
  })
}
```

> Tip: Check if your auth library supports refresh tokens, which can be used to extend the user's session.

### Deleting the session

To delete the session, you can delete the cookie:

```typescript
// app/lib/session.ts
import 'server-only'
import { cookies } from 'next/headers'
 
export function deleteSession() {
  cookies().delete('session')
}
```

Then you can reuse the `deleteSession()` function in your application, for example, on logout:

```typescript
// app/actions/auth.ts
import { cookies } from 'next/headers'
import { deleteSession } from '@/app/lib/session'
 
export async function logout() {
  deleteSession()
  redirect('/login')
}
```

### Database Sessions

To create and manage database sessions, you'll need to follow these steps:

1. Create a table in your database to store session and data (or check if your Auth Library handles this).
2. Implement functionality to insert, update, and delete sessions
3. Encrypt the session ID before storing it in the user's browser, and ensure the database and cookie stay in sync (this is optional, but recommended for optimistic auth checks in Middleware).

For example:

```typescript
// app/lib/session.ts
import cookies from 'next/headers'
import { db } from '@/app/lib/db'
import { encrypt } from '@/app/lib/session'
 
export async function createSession(id: number) {
  const expiresAt = new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
 
  // 1. Create a session in the database
  const data = await db
    .insert(sessions)
    .values({
      userId: id,
      expiresAt,
    })
    // Return the session ID
    .returning({ id: sessions.id })
 
  const sessionId = data[0].id
 
  // 2. Encrypt the session ID
  const session = await encrypt({ sessionId, expiresAt })
 
  // 3. Store the session in cookies for optimistic auth checks
  cookies().set('session', session, {
    httpOnly: true,
    secure: true,
    expires: expiresAt,
    sameSite: 'lax',
    path: '/',
  })
}
```

> Tips:
> 
> - For faster data retrieval, consider using a database like Vercel Redis. However, you can also keep the session data in your primary database, and combine data requests to reduce the number of queries.
> - You may opt to use database sessions for more advanced use cases, such as keeping track of the last time a user logged in, or number of active devices, or give users the ability to log out of all devices.

After implementing session management, you'll need to add authorization logic to control what users can access and do within your application. Continue to the Authorization section to learn more.

## Authorization

Once a user is authenticated and a session is created, you can implement authorization to control what the user can access and do within your application.

There are two main types of authorization checks:

- **Optimistic**: Checks if the user is authorized to access a route or perform an action using the session data stored in the cookie. These checks are useful for quick operations, such as showing/hiding UI elements or redirecting users based on permissions or roles.
- **Secure**: Checks if the user is authorized to access a route or perform an action using the session data stored in the database. These checks are more secure and are used for operations that require access to sensitive data or actions.

For both cases, we recommend:

- Creating a Data Access Layer to centralize your authorization logic
- Using Data Transfer Objects (DTO) to only return the necessary data
- Optionally use Middleware to perform optimistic checks.

### Optimistic checks with Middleware (Optional)

There are some cases where you may want to use Middleware and redirect users based on permissions:

- To perform optimistic checks. Since Middleware runs on every route, it's a good way to centralize redirect logic and pre-filter unauthorized users.
- To protect static routes that share data between users (e.g. content behind a paywall).

However, since Middleware runs on every route, including prefetched routes, it's important to only read the session from the cookie (optimistic checks), and avoid database checks to prevent performance issues.

For example:

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server'
import { decrypt } from '@/app/lib/session'
import { cookies } from 'next/headers'
 
// 1. Specify protected and public routes
const protectedRoutes = ['/dashboard']
const publicRoutes = ['/login', '/signup', '/']
 
export default async function middleware(req: NextRequest) {
  // 2. Check if the current route is protected or public
  const path = req.nextUrl.pathname
  const isProtectedRoute = protectedRoutes.includes(path)
  const isPublicRoute = publicRoutes.includes(path)
 
  // 3. Decrypt the session from the cookie
  const cookie = cookies().get('session')?.value
  const session = await decrypt(cookie)
 
  // 5. Redirect to /login if the user is not authenticated
  if (isProtectedRoute && !session?.userId) {
    return NextResponse.redirect(new URL('/login', req.nextUrl))
  }
 
  // 6. Redirect to /dashboard if the user is authenticated
  if (
    isPublicRoute &&
    session?.userId &&
    !req.nextUrl.pathname.startsWith('/dashboard')
  ) {
    return NextResponse.redirect(new URL('/dashboard', req.nextUrl))
  }
 
  return NextResponse.next()
}
 
// Routes Middleware should not run on
export const config = {
  matcher: ['/((?!api|_next/static|_next/image|.*\\.png$).*)'],
}
```

While Middleware can be useful for initial checks, it should not be your only line of defense in protecting your data. The majority of security checks should be performed as close as possible to your data source, see Data Access Layer for more information.

> Tips:
> 
> - In Middleware, you can also read cookies using `req.cookies.get('session).value`.
> - Middleware uses the Edge Runtime, check if your Auth library and session management library are compatible.
> - You can use the `matcher` property in the Middleware to specify which routes Middleware should run on. Although, for auth, it's recommended Middleware runs on all routes.

### Creating a Data Access Layer (DAL)

We recommend creating a DAL to centralize your data requests and authorization logic.

The DAL should include a function that verifies the user's session as they interact with your application. At the very least, the function should check if the session is valid, then redirect or return the user information needed to make further requests.

For example, create a separate file for your DAL that includes a `verifySession()` function. Then use React's `cache` API to memoize the return value of the function during a React render pass:

```typescript
// app/lib/dal.ts
import 'server-only'
 
import { cookies } from 'next/headers'
import { decrypt } from '@/app/lib/session'
 
export const verifySession = cache(async () => {
  const cookie = cookies().get('session')?.value
  const session = await decrypt(cookie)
 
  if (!session?.userId) {
    redirect('/login')
  }
 
  return { isAuth: true, userId: session.userId }
})
```

You can then invoke the `verifySession()` function in your data requests, Server Actions, Route Handlers:

```typescript
// app/lib/dal.ts
export const getUser = cache(async () => {
  const session = await verifySession()
  if (!session) return null
 
  try {
    const data = await db.query.users.findMany({
      where: eq(users.id, session.userId),
      // Explicitly return the columns you need rather than the whole user object
      columns: {
        id: true,
        name: true,
        email: true,
      },
    })
 
    const user = data[0]
 
    return user
  } catch (error) {
    console.log('Failed to fetch user')
    return null
  }
})
```

> Tip:
> 
> - A DAL can be used to protect data fetched at request time. However, for static routes that share data between users, data will be fetched at build time and not at request time. Use Middleware to protect static routes.
> - For secure checks, you can check if the session is valid by comparing the session ID with your database. Use React's `cache` function to avoid unnecessary duplicate requests to the database during a render pass.
> - You may wish to consolidate related data requests in a JavaScript class that runs `verifySession()` before any methods.

### Using Data Transfer Objects (DTO)

When retrieving data, it's recommended you return only the necessary data that will be used in your application, and not entire objects. For example, if you're fetching user data, you might only return the user's ID and name, rather than the entire user object which could contain passwords, phone numbers, etc.

However, if you have no control over the returned data structure, or are working in a team where you want to avoid whole objects being passed to the client, you can use strategies such as specifying what fields are safe to be exposed to the client.

```typescript
// app/lib/dto.ts
import 'server-only'
import { getUser } from '@/app/lib/dal'
 
function canSeeUsername(viewer: User) {
  return true
}
 
function canSeePhoneNumber(viewer: User, team: string) {
  return viewer.isAdmin || team === viewer.team
}
 
export async function getProfileDTO(slug: string) {
  const data = await db.query.users.findMany({
    where: eq(users.slug, slug),
    // Return specific columns here
  })
  const user = data[0]
 
  const currentUser = await getUser(user.id)
 
  // Or return only what's specific to the query here
  return {
    username: canSeeUsername(currentUser) ? user.username : null,
    phonenumber: canSeePhoneNumber(currentUser, user.team)
      ? user.phonenumber
      : null,
  }
}
```

By centralizing your data requests and authorization logic in a DAL and using DTOs, you can ensure that all data requests are secure and consistent, making it easier to maintain, audit, and debug as your application scales.

> Good to know:
> 
> - There are a couple of different ways you can define a DTO, from using `toJSON()`, to individual functions like the example above, or JS classes. Since these are JavaScript patterns and not a React or Next.js feature, we recommend doing some research to find the best pattern for your application.
> - Learn more about security best practices in our [Security in Next.js](https://nextjs.org/docs/pages/building-your-application/configuring/authentication#security-in-nextjs) article.

### Server Components

Auth check in Server Components are useful for role-based access. For example, to conditionally render components based on the user's role:

```typescript
// app/dashboard/page.tsx
import { verifySession } from '@/app/lib/dal'
 
export default function Dashboard() {
  const session = await verifySession()
  const userRole = session?.user?.role // Assuming 'role' is part of the session object
 
  if (userRole === 'admin') {
    return <AdminDashboard />
  } else if (userRole === 'user') {
    return <UserDashboard />
  } else {
    redirect('/login')
  }
}
```

In the example, we use the `verifySession()` function from our DAL to check for 'admin', 'user', and unauthorized roles. This pattern ensures that each user interacts only with components appropriate to their role.

### Layouts and auth checks

Due to Partial Rendering, be cautious when doing checks in Layouts as these don't re-render on navigation, meaning the user session won't be checked on every route change.

Instead, you should do the checks close to your data source or the component that'll be conditionally rendered.

For example, consider a shared layout that fetches the user data and displays the user image in a nav. Instead of doing the auth check in the layout, you should fetch the user data (`getUser()`) in the layout and do the auth check in your DAL.

This guarantees that wherever `getUser()` is called within your application, the auth check is performed, and prevents developers forgetting to check the user is authorized to access the data.

```typescript
// app/layout.tsx
export default async function Layout({
  children,
}: {
  children: React.ReactNode;
}) {
  const user = await getUser();
 
  return (
    // ...
  )
}
```

```typescript
// app/lib/dal.ts
export const getUser = cache(async () => {
  const session = await verifySession()
  if (!session) return null
 
  // Get user ID from session and fetch data
})
```

> Good to know: A common pattern in SPAs is to return null in a layout or a top-level component if a user is not authorized. This pattern is not not recommended since Next.js applications have multiple entry points, which will not prevent nested route segments and Server Actions from being accessed.

### Server Actions

Treat Server Actions with the same security considerations as public-facing API endpoints, and verify if the user is allowed to perform a mutation.

In the example below, we check the user's role before allowing the action to proceed:

```typescript
// app/lib/actions.ts
'use server'
import { verifySession } from '@/app/lib/dal'
 
export async function serverAction(formData: FormData) {
  const session = await verifySession()
  const userRole = session?.user?.role
 
  // Return early if user is not authorized to perform the action
  if (userRole !== 'admin') {
    return null
  }
 
  // Proceed with the action for authorized users
}
```

### Route Handlers

Treat Route Handlers with the same security considerations as public-facing API endpoints, and verify if the user is allowed to access the Route Handler.

For example:

```typescript
// app/api/route.ts
import { verifySession } from '@/app/lib/dal'
 
export async function GET() {
  // User authentication and role verification
  const session = await verifySession()
 
  // Check if the user is authenticated
  if (!session) {
    // User is not authenticated
    return new Response(null, { status: 401 })
  }
 
  // Check if the user has the 'admin' role
  if (session.user.role !== 'admin') {
    // User is authenticated but does not have the right permissions
    return new Response(null, { status: 403 })
  }
 
  // Continue for authorized users
}
```

The example above demonstrates a Route Handler with a two-tier security check. It first checks for an active session, and then verifies if the logged-in user is an 'admin'.

### Context Providers

Using context providers for auth work due to interleaving. However, React context is not supported in Server Components, making them only applicable to Client Components.

This works, but any child Server Components will be rendered on the server first, and will not have access to the context provider's session data:

```typescript
// app/layout.ts
import { ContextProvider } from 'auth-lib'
 
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <ContextProvider>{children}</ContextProvider>
      </body>
    </html>
  )
}
```

```typescript
"use client";
 
import { useSession } from "auth-lib";
 
export default function Profile() {
  const { userId } = useSession();
  const { data } = useSWR(`/api/user/${userId}`, fetcher)
 
  return (
    // ...
  );
}
```

If session data is needed in Client Components (e.g. for client-side data fetching), use React's `taintUniqueValue` API to prevent sensitive session data from being exposed to the client.

# Deploying

Congratulations, it's time to ship to production.

You can deploy managed Next.js with Vercel, or self-host on a Node.js server, Docker image, or even static HTML files. When deploying using `next start`, all Next.js features are supported.

## Production Builds

Running `next build` generates an optimized version of your application for production. HTML, CSS, and JavaScript files are created based on your pages. JavaScript is compiled and browser bundles are minified using the Next.js Compiler to help achieve the best performance and support all modern browsers.

Next.js produces a standard deployment output used by managed and self-hosted Next.js. This ensures all features are supported across both methods of deployment. In the next major version, we will be transforming this output into our Build Output API specification.

## Managed Next.js with Vercel

Vercel, the creators and maintainers of Next.js, provide managed infrastructure and a developer experience platform for your Next.js applications.

Deploying to Vercel is zero-configuration and provides additional enhancements for scalability, availability, and performance globally. However, all Next.js features are still supported when self-hosted.

Learn more about [Next.js on Vercel](https://nextjs.org/docs/app/building-your-application/deploying#managed-nextjs-with-vercel) or [deploy a template for free](https://vercel.com/templates/next.js) to try it out.

## Self-Hosting

You can self-host Next.js in three different ways:

1. A Node.js server
2. A Docker container
3. A static export

### Node.js Server

Next.js can be deployed to any hosting provider that supports Node.js. Ensure your `package.json` has the "build" and "start" scripts:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

Then, run `npm run build` to build your application. Finally, run `npm run start` to start the Node.js server. This server supports all Next.js features.

### Docker Image

Next.js can be deployed to any hosting provider that supports Docker containers. You can use this approach when deploying to container orchestrators such as Kubernetes or when running inside a container in any cloud provider.

1. Install Docker on your machine
2. Clone our example (or the multi-environment example)
3. Build your container: `docker build -t nextjs-docker .`
4. Run your container: `docker run -p 3000:3000 nextjs-docker`

Next.js through Docker supports all Next.js features.

### Static HTML Export

Next.js enables starting as a static site or Single-Page Application (SPA), then later optionally upgrading to use features that require a server.

Since Next.js supports this static export, it can be deployed and hosted on any web server that can serve HTML/CSS/JS static assets. This includes tools like AWS S3, Nginx, or Apache.

Running as a static export does not support Next.js features that require a server. [Learn more](https://nextjs.org/docs/app/building-your-application/deploying/static-exports).

**Good to know:**

- Server Components are supported with static exports.

## Features

### Image Optimization

Image Optimization through `next/image` works self-hosted with zero configuration when deploying using `next start`. If you would prefer to have a separate service to optimize images, you can configure an image loader.

Image Optimization can be used with a static export by defining a custom image loader in `next.config.js`. Note that images are optimized at runtime, not during the build.

**Good to know:**

- On glibc-based Linux systems, Image Optimization may require additional configuration to prevent excessive memory usage.
- Learn more about the [caching behavior of optimized images](https://nextjs.org/docs/app/building-your-application/deploying#caching-and-isr) and how to configure the TTL.
- You can also disable Image Optimization and still retain other benefits of using `next/image` if you prefer. For example, if you are optimizing images yourself separately.

### Middleware

Middleware works self-hosted with zero configuration when deploying using `next start`. Since it requires access to the incoming request, it is not supported when using a static export.

Middleware uses a runtime that is a subset of all available Node.js APIs to help ensure low latency, since it may run in front of every route or asset in your application. This runtime does not require running "at the edge" and works in a single-region server. Additional configuration and infrastructure are required to run Middleware in multiple regions.

If you are looking to add logic (or use an external package) that requires all Node.js APIs, you might be able to move this logic to a layout as a Server Component. For example, checking headers and redirecting. You can also use headers, cookies, or query parameters to redirect or rewrite through `next.config.js`. If that does not work, you can also use a custom server.

### Environment Variables

Next.js can support both build time and runtime environment variables.

By default, environment variables are only available on the server. To expose an environment variable to the browser, it must be prefixed with `NEXT_PUBLIC_`. However, these public environment variables will be inlined into the JavaScript bundle during `next build`.

To read runtime environment variables, we recommend using `getServerSideProps` or incrementally adopting the App Router. With the App Router, we can safely read environment variables on the server during dynamic rendering. This allows you to use a singular Docker image that can be promoted through multiple environments with different values.

```javascript
import { unstable_noStore as noStore } from 'next/cache';

export default function Component() {
  noStore();
  // cookies(), headers(), and other dynamic functions
  // will also opt into dynamic rendering, making
  // this env variable is evaluated at runtime
  const value = process.env.MY_VALUE
  ...
}
```

**Good to know:**

- You can run code on server startup using the `register` function.
- We do not recommend using the `runtimeConfig` option, as this does not work with the standalone output mode. Instead, we recommend incrementally adopting the App Router.

### Caching and ISR

Next.js can cache responses, generated static pages, build outputs, and other static assets like images, fonts, and scripts.

Caching and revalidating pages (using Incremental Static Regeneration (ISR) or newer functions in the App Router) use the same shared cache. By default, this cache is stored to the filesystem (on disk) on your Next.js server. This works automatically when self-hosting using both the Pages and App Router.

You can configure the Next.js cache location if you want to persist cached pages and data to durable storage, or share the cache across multiple containers or instances of your Next.js application.

#### Automatic Caching

- Next.js sets the `Cache-Control` header of `public, max-age=31536000, immutable` to truly immutable assets. It cannot be overridden. These immutable files contain a SHA-hash in the file name, so they can be safely cached indefinitely. For example, Static Image Imports. You can configure the TTL for images.
- Incremental Static Regeneration (ISR) sets the `Cache-Control` header of `s-maxage: <revalidate in getStaticProps>, stale-while-revalidate`. This revalidation time is defined in your `getStaticProps` function in seconds. If you set `revalidate: false`, it will default to a one-year cache duration.
- Dynamically rendered pages set a `Cache-Control` header of `private, no-cache, no-store, max-age=0, must-revalidate` to prevent user-specific data from being cached. This applies to both the App Router and Pages Router. This also includes Draft Mode.

#### Static Assets

If you want to host static assets on a different domain or CDN, you can use the `assetPrefix` configuration in `next.config.js`. Next.js will use this asset prefix when retrieving JavaScript or CSS files. Separating your assets to a different domain does come with the downside of extra time spent on DNS and TLS resolution.

Learn more about [assetPrefix](https://nextjs.org/docs/app/api-reference/next-config-js/assetPrefix).

#### Configuring Caching

By default, generated cache assets will be stored in memory (defaults to 50mb) and on disk. If you are hosting Next.js using a container orchestration platform like Kubernetes, each pod will have a copy of the cache. To prevent stale data from being shown since the cache is not shared between pods by default, you can configure the Next.js cache to provide a cache handler and disable in-memory caching.

To configure the ISR/Data Cache location when self-hosting, you can configure a custom handler in your `next.config.js` file:

```javascript
// next.config.js
module.exports = {
  cacheHandler: require.resolve('./cache-handler.js'),
  cacheMaxMemorySize: 0, // disable default in-memory caching
}
```

Then, create `cache-handler.js` in the root of your project, for example:

```javascript
// cache-handler.js
const cache = new Map()

module.exports = class CacheHandler {
  constructor(options) {
    this.options = options
  }

  async get(key) {
    // This could be stored anywhere, like durable storage
    return cache.get(key)
  }

  async set(key, data, ctx) {
    // This could be stored anywhere, like durable storage
    cache.set(key, {
      value: data,
      lastModified: Date.now(),
      tags: ctx.tags,
    })
  }

  async revalidateTag(tag) {
    // Iterate over all entries in the cache
    for (let [key, value] of cache) {
      // If the value's tags include the specified tag, delete this entry
      if (value.tags.includes(tag)) {
        cache.delete(key)
      }
    }
  }
}
```

Using a custom cache handler will allow you to ensure consistency across all pods hosting your Next.js application. For instance, you can save the cached values anywhere, like Redis or AWS S3.

**Good to know:**

- `revalidatePath` is a convenience layer on top of cache tags. Calling `revalidatePath` will call the `revalidateTag` function with a special default tag for the provided page.

### Build Cache

Next.js generates an ID during `next build` to identify which version of your application is being served. The same build should be used and boot up multiple containers.

If you are rebuilding for each stage of your environment, you will need to generate a consistent build ID to use between containers. Use the `generateBuildId` command in `next.config.js`:

```javascript
// next.config.js
module.exports = {
  generateBuildId: async () => {
    // This could be anything, using the latest git hash
    return process.env.GIT_HASH
  },
}
```

### Version Skew

Next.js will automatically mitigate most instances of version skew and automatically reload the application to retrieve new assets when detected. For example, if there is a mismatch in the `deploymentId`, transitions between pages will perform a hard navigation versus using a prefetched value.

When the application is reloaded, there may be a loss of application state if it's not designed to persist between page navigations. For example, using URL state or local storage would persist state after a page refresh. However, component state like `useState` would be lost in such navigations.

Vercel provides additional skew protection for Next.js applications to ensure assets and functions from the previous version are still available to older clients, even after the new version is deployed.

You can manually configure the `deploymentId` property in your `next.config.js` file to ensure each request uses either `?dpl` query string or `x-deployment-id` header.

### Streaming and Suspense

The Next.js App Router supports streaming responses when self-hosting. If you are using Nginx or a similar proxy, you will need to configure it to disable buffering to enable streaming.

For example, you can disable buffering in Nginx by setting `X-Accel-Buffering` to `no`:

```javascript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/:path*{/}?',
        headers: [
          {
            key: 'X-Accel-Buffering',
            value: 'no',
          },
        ],
      },
    ]
  },
}
```

### Partial Prerendering

Partial Prendering (experimental) works by default with Next.js and is not a CDN feature. This includes deployment as a Node.js server (through `next start`) and when used with a Docker container.

### Usage with CDNs

When using a CDN in front on your Next.js application, the page will include `Cache-Control: private` response header when dynamic APIs are accessed. This ensures that the resulting HTML page is marked as non-cachable. If the page is fully prerendered to static, it will include `Cache-Control: public` to allow the page to be cached on the CDN.

If you don't need a mix of both static and dynamic components, you can make your entire route static and cache the output HTML on a CDN. This Automatic Static Optimization is the default behavior when running `next build` if dynamic APIs are not used.

## Production Checklist

Before taking your Next.js application to production, there are some optimizations and patterns you should consider implementing for the best user experience, performance, and security.

This page provides best practices that you can use as a reference when building your application, before going to production, and after deployment - as well as the automatic Next.js optimizations you should be aware of.

### Automatic optimizations

These Next.js optimizations are enabled by default and require no configuration:

- **Server Components**: Next.js uses Server Components by default. Server Components run on the server, and don't require JavaScript to render on the client. As such, they have no impact on the size of your client-side JavaScript bundles. You can then use Client Components as needed for interactivity.
- **Code-splitting**: Server Components enable automatic code-splitting by route segments. You may also consider lazy loading Client Components and third-party libraries, where appropriate.
- **Prefetching**: When a link to a new route enters the user's viewport, Next.js prefetches the route in background. This makes navigation to new routes almost instant. You can opt out of prefetching, where appropriate.
- **Static Rendering**: Next.js statically renders Server and Client Components on the server at build time and caches the rendered result to improve your application's performance. You can opt into Dynamic Rendering for specific routes, where appropriate.
- **Caching**: Next.js caches data requests, the rendered result of Server and Client Components, static assets, and more, to reduce the number of network requests to your server, database, and backend services. You may opt out of caching, where appropriate.

These defaults aim to improve your application's performance, and reduce the cost and amount of data transferred on each network request.

### During development

While building your application, we recommend using the following features to ensure the best performance and user experience:

#### Routing and rendering

- **Layouts**: Use layouts to share UI across pages and enable partial rendering on navigation.
- **`<Link>` component**: Use the `<Link>` component for client-side navigation and prefetching.
- **Error Handling**: Gracefully handle catch-all errors and 404 errors in production by creating custom error pages.
- **Composition Patterns**: Follow the recommended composition patterns for Server and Client Components, and check the placement of your "use client" boundaries to avoid unnecessarily increasing your client-side JavaScript bundle.
- **Dynamic Functions**: Be aware that dynamic functions like `cookies()` and the `searchParams` prop will opt the entire route into Dynamic Rendering (or your whole application if used in the Root Layout). Ensure dynamic function usage is intentional and wrap them in `<Suspense>` boundaries where appropriate.

> **Good to know**: Partial Prerendering (experimental) will allow parts of a route to be dynamic without opting the whole route into dynamic rendering.

#### Data fetching and caching

- **Server Components**: Leverage the benefits of fetching data on the server using Server Components.
- **Route Handlers**: Use Route Handlers to access your backend resources from Client Components. But do not call Route Handlers from Server Components to avoid an additional server request.
- **Streaming**: Use Loading UI and React Suspense to progressively send UI from the server to the client, and prevent the whole route from blocking while data is being fetched.
- **Parallel Data Fetching**: Reduce network waterfalls by fetching data in parallel, where appropriate. Also, consider preloading data where appropriate.
- **Data Caching**: Verify whether your data requests are being cached or not, and opt into caching, where appropriate. Ensure requests that don't use `fetch` are cached.
- **Static Images**: Use the `public` directory to automatically cache your application's static assets, e.g. images.

#### UI and accessibility

- **Forms and Validation**: Use Server Actions to handle form submissions, server-side validation, and handle errors.
- **Font Module**: Optimize fonts by using the Font Module, which automatically hosts your font files with other static assets, removes external network requests, and reduces layout shift.
- **`<Image>` Component**: Optimize images by using the Image Component, which automatically optimizes images, prevents layout shift, and serves them in modern formats like WebP or AVIF.
- **`<Script>` Component**: Optimize third-party scripts by using the Script Component, which automatically defers scripts and prevents them from blocking the main thread.
- **ESLint**: Use the built-in `eslint-plugin-jsx-a11y` plugin to catch accessibility issues early.

#### Security

- **Tainting**: Prevent sensitive data from being exposed to the client by tainting data objects and/or specific values.
- **Server Actions**: Ensure users are authorized to call Server Actions. Review the recommended security practices.
- **Environment Variables**: Ensure your `.env.*` files are added to `.gitignore` and only public variables are prefixed with `NEXT_PUBLIC_`.
- **Content Security Policy**: Consider adding a Content Security Policy to protect your application against various security threats such as cross-site scripting, clickjacking, and other code injection attacks.

#### Metadata and SEO

- **Metadata API**: Use the Metadata API to improve your application's Search Engine Optimization (SEO) by adding page titles, descriptions, and more.
- **Open Graph (OG) images**: Create OG images to prepare your application for social sharing.
- **Sitemaps and Robots**: Help Search Engines crawl and index your pages by generating sitemaps and robots files.

#### Type safety

- **TypeScript and TS Plugin**: Use TypeScript and the TypeScript plugin for better type-safety, and to help you catch errors early.

### Before going to production

Before going to production, you can run `next build` to build your application locally and catch any build errors, then run `next start` to measure the performance of your application in a production-like environment.

#### Core Web Vitals

- **Lighthouse**: Run lighthouse in incognito to gain a better understanding of how your users will experience your site, and to identify areas for improvement. This is a simulated test and should be paired with looking at field data (such as Core Web Vitals).
- **`useReportWebVitals` hook**: Use this hook to send Core Web Vitals data to analytics tools.

#### Analyzing bundles

Use the `@next/bundle-analyzer` plugin to analyze the size of your JavaScript bundles and identify large modules and dependencies that might be impacting your application's performance.

Additionally, the following tools can help you understand the impact of adding new dependencies to your application:

- [Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost)
- [Package Phobia](https://packagephobia.com/)
- [Bundle Phobia](https://bundlephobia.com/)
- [bundlejs](https://bundlejs.com/)

### After deployment

Depending on where you deploy your application, you might have access to additional tools and integrations to help you monitor and improve your application's performance.

For Vercel deployments, we recommend the following:

- **Analytics**: A built-in analytics dashboard to help you understand your application's traffic, including the number of unique visitors, page views, and more.
- **Speed Insights**: Real-world performance insights based on visitor data, offering a practical view of how your website is performing in the field.
- **Logging**: Runtime and Activity logs to help you debug issues and monitor your application in production. Alternatively, see the integrations page for a list of third-party tools and services.

> **Good to know**: To get a comprehensive understanding of the best practices for production deployments on Vercel, including detailed strategies for improving website performance, refer to the [Vercel Production Checklist](https://vercel.com/docs/production-checklist).

## Static Exports

Next.js enables starting as a static site or Single-Page Application (SPA), then later optionally upgrading to use features that require a server.

When running `next build`, Next.js generates an HTML file per route. By breaking a strict SPA into individual HTML files, Next.js can avoid loading unnecessary JavaScript code on the client-side, reducing the bundle size and enabling faster page loads.

Since Next.js supports this static export, it can be deployed and hosted on any web server that can serve HTML/CSS/JS static assets.

### Configuration

To enable a static export, change the output mode inside `next.config.js`:

```javascript
// next.config.js

/**
 * @type {import('next').NextConfig}
 */
const nextConfig = {
  output: 'export',
 
  // Optional: Change links `/me` -> `/me/` and emit `/me.html` -> `/me/index.html`
  // trailingSlash: true,
 
  // Optional: Prevent automatic `/me` -> `/me/`, instead preserve `href`
  // skipTrailingSlashRedirect: true,
 
  // Optional: Change the output directory `out` -> `dist`
  // distDir: 'dist',
}
 
module.exports = nextConfig
```

After running `next build`, Next.js will produce an `out` folder which contains the HTML/CSS/JS assets for your application.

### Supported Features

The core of Next.js has been designed to support static exports.

#### Server Components

When you run `next build` to generate a static export, Server Components consumed inside the app directory will run during the build, similar to traditional static-site generation.

The resulting component will be rendered into static HTML for the initial page load and a static payload for client navigation between routes. No changes are required for your Server Components when using the static export, unless they consume dynamic server functions.

```typescript
// app/page.tsx

export default async function Page() {
  // This fetch will run on the server during `next build`
  const res = await fetch('https://api.example.com/...')
  const data = await res.json()
 
  return <main>...</main>
}
```

#### Client Components

If you want to perform data fetching on the client, you can use a Client Component with SWR to memoize requests.

```typescript
// app/other/page.tsx

'use client'
 
import useSWR from 'swr'
 
const fetcher = (url: string) => fetch(url).then((r) => r.json())
 
export default function Page() {
  const { data, error } = useSWR(
    `https://jsonplaceholder.typicode.com/posts/1`,
    fetcher
  )
  if (error) return 'Failed to load'
  if (!data) return 'Loading...'
 
  return data.title
}
```

Since route transitions happen client-side, this behaves like a traditional SPA. For example, the following index route allows you to navigate to different posts on the client:

```typescript
// app/page.tsx

import Link from 'next/link'
 
export default function Page() {
  return (
    <>
      <h1>Index Page</h1>
      <hr />
      <ul>
        <li>
          <Link href="/post/1">Post 1</Link>
        </li>
        <li>
          <Link href="/post/2">Post 2</Link>
        </li>
      </ul>
    </>
  )
}
```

#### Image Optimization

Image Optimization through `next/image` can be used with a static export by defining a custom image loader in `next.config.js`. For example, you can optimize images with a service like Cloudinary:

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
  images: {
    loader: 'custom',
    loaderFile: './my-loader.ts',
  },
}
 
module.exports = nextConfig
```

This custom loader will define how to fetch images from a remote source. For example, the following loader will construct the URL for Cloudinary:

```typescript
// my-loader.ts

export default function cloudinaryLoader({
  src,
  width,
  quality,
}: {
  src: string
  width: number
  quality?: number
}) {
  const params = ['f_auto', 'c_limit', `w_${width}`, `q_${quality || 'auto'}`]
  return `https://res.cloudinary.com/demo/image/upload/${params.join(
    ','
  )}${src}`
}
```

You can then use `next/image` in your application, defining relative paths to the image in Cloudinary:

```typescript
// app/page.tsx

import Image from 'next/image'
 
export default function Page() {
  return <Image alt="turtles" src="/turtles.jpg" width={300} height={300} />
}
```

#### Route Handlers

Route Handlers will render a static response when running `next build`. Only the GET HTTP verb is supported. This can be used to generate static HTML, JSON, TXT, or other files from cached or uncached data. For example:

```typescript
// app/data.json/route.ts

export async function GET() {
  return Response.json({ name: 'Lee' })
}
```

The above file `app/data.json/route.ts` will render to a static file during `next build`, producing `data.json` containing `{ name: 'Lee' }`.

If you need to read dynamic values from the incoming request, you cannot use a static export.

#### Browser APIs

Client Components are pre-rendered to HTML during `next build`. Because Web APIs like `window`, `localStorage`, and `navigator` are not available on the server, you need to safely access these APIs only when running in the browser. For example:

```typescript
'use client';
 
import { useEffect } from 'react';
 
export default function ClientComponent() {
  useEffect(() => {
    // You now have access to `window`
    console.log(window.innerHeight);
  }, [])
 
  return ...;
}
```

### Unsupported Features

Features that require a Node.js server, or dynamic logic that cannot be computed during the build process, are not supported:

- Dynamic Routes with `dynamicParams: true`
- Dynamic Routes without `generateStaticParams()`
- Route Handlers that rely on Request
- Cookies
- Rewrites
- Redirects
- Headers
- Middleware
- Incremental Static Regeneration
- Image Optimization with the default loader
- Draft Mode
- Server Actions

Attempting to use any of these features with `next dev` will result in an error, similar to setting the `dynamic` option to `error` in the root layout.

```typescript
export const dynamic = 'error'
```

### Deploying

With a static export, Next.js can be deployed and hosted on any web server that can serve HTML/CSS/JS static assets.

When running `next build`, Next.js generates the static export into the `out` folder. For example, let's say you have the following routes:

- `/`
- `/blog/[id]`

After running `next build`, Next.js will generate the following files:

- `/out/index.html`
- `/out/404.html`
- `/out/blog/post-1.html`
- `/out/blog/post-2.html`

If you are using a static host like Nginx, you can configure rewrites from incoming requests to the correct files:

```nginx
server {
  listen 80;
  server_name acme.com;
 
  root /var/www/out;
 
  location / {
      try_files $uri $uri.html $uri/ =404;
  }
 
  # This is necessary when `trailingSlash: false`.
  # You can omit this when `trailingSlash: true`.
  location /blog/ {
      rewrite ^/blog/(.*)$ /blog/$1.html break;
  }
 
  error_page 404 /404.html;
  location = /404.html {
      internal;
  }
}
```

### Version History

| Version | Changes |
|---------|---------|
| v14.0.0 | `next export` has been removed in favor of `"output": "export"` |
| v13.4.0 | App Router (Stable) adds enhanced static export support, including using React Server Components and Route Handlers. |
| v13.3.0 | `next export` is deprecated and replaced with `"output": "export"` |

## Multi-Zones

With Zones

Multi-Zones are an approach to micro-frontends that separate a large application on a domain into smaller Next.js applications that each serve a set of paths. This is useful when there are collections of pages unrelated to the other pages in the application. By moving those pages to a separate zone (i.e., a separate application), you can reduce the size of each application which improves build times and removes code that is only necessary for one of the zones.

For example, let's say you have the following set of pages that you would like to split up:

- `/blog/*` for all blog posts
- `/dashboard/*` for all pages when the user is logged-in to the dashboard
- `/*` for the rest of your website not covered by other zones

With Multi-Zones support, you can create three applications that all are served on the same domain and look the same to the user, but you can develop and deploy each of the applications independently.

![Three zones: A, B, C. Showing a hard navigation between routes from different zones, and soft navigations between routes within the same zone.](image_url_here)

Navigating between pages in the same zone will perform soft navigations, a navigation that does not require reloading the page. For example, in this diagram, navigating from `/` to `/products` will be a soft navigation.

Navigating from a page in one zone to a page in another zone, such as from `/` to `/dashboard`, will perform a hard navigation, unloading the resources of the current page and loading the resources of the new page. Pages that are frequently visited together should live in the same zone to avoid hard navigations.

### How to define a zone

There are no special APIs to define a new zone. A zone is a normal Next.js application where you also configure a `basePath` to avoid conflicts with pages and static files in other zones.

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  basePath: '/blog',
}
```

The default application that will handle all paths not sent to a more specific zone does not need a `basePath`.

Next.js assets, such as JavaScript and CSS, will also be prefixed with `basePath` to make sure that they don't conflict with assets from other zones. These assets will be served under `/basePath/_next/...` for each of the zones.

If the zone serves pages that don't share a common path prefix, such as `/home` and `/blog`, then you can also set `assetPrefix` to ensure that all Next.js assets are served under a unique path prefix for the zone without adding a path prefix to the rest of the routes in your application.

### How to route requests to the right zone

With the Multi Zones set-up, you need to route the paths to the correct zone since they are served by different applications. You can use any HTTP proxy to do this, but one of the Next.js applications can also be used to route requests for the entire domain.

To route to the correct zone using a Next.js application, you can use rewrites. For each path served by a different zone, you would add a rewrite rule to send that path to the domain of the other zone. For example:

```javascript
// next.config.js

async rewrites() {
    return [
        {
            source: '/blog',
            destination: `${process.env.BLOG_DOMAIN}/blog`,
        },
        {
            source: '/blog/:path+',
            destination: `${process.env.BLOG_DOMAIN}/blog/:path+`,
        }
    ];
}
```

`destination` should be a URL that is served by the zone, including scheme and domain. This should point to the zone's production domain, but it can also be used to route requests to localhost in local development.

**Good to know:** URL paths should be unique to a zone. For example, two zones trying to serve `/blog` would create a routing conflict.

### Linking between zones

Links to paths in a different zone should use an `a` tag instead of the Next.js `<Link>` component. This is because Next.js will try to prefetch and soft navigate to any relative path in `<Link>` component, which will not work across zones.

### Sharing code

The Next.js applications that make up the different zones can live in any repository. However, it is often convenient to put these zones in a monorepo to more easily share code. For zones that live in different repositories, code can also be shared using public or private NPM packages.

Since the pages in different zones may be released at different times, feature flags can be useful for enabling or disabling features in unison across the different zones.

For Next.js on Vercel applications, you can use a monorepo to deploy all affected zones with a single git push.

# Upgrade Guide

Upgrade your application to newer versions of Next.js or migrate from the Pages Router to the App Router.

## Version 15

### Upgrading from 14 to 15

To update to Next.js version 15, run the following command using your preferred package manager:

```bash
npm i next@rc react@rc react-dom@rc eslint-config-next@rc
```

```bash
yarn add next@rc react@rc react-dom@rc eslint-config-next@rc
```

```bash
pnpm up next@rc react@rc react-dom@rc eslint-config-next@rc
```

```bash
bun add next@rc react@rc react-dom@rc eslint-config-next@rc
```

Good to know:

- If you see a peer dependencies warning, you may need to update `react` and `react-dom` to the suggested versions, or you use the `--force` or `--legacy-peer-deps` flag to ignore the warning. This won't be necessary once both Next.js 15 and React 19 are stable.
- If you are using TypeScript, you'll need to temporarily override the React types. See the React 19 RC upgrade guide for more information.

### Minimum React version

The minimum `react` and `react-dom` is now 19.

### fetch requests

`fetch` requests are no longer cached by default.

To opt specific fetch requests into caching, you can pass the `cache: 'force-cache'` option.

```javascript
// app/layout.js
export default async function RootLayout() {
  const a = await fetch('https://...') // Not Cached
  const b = await fetch('https://...', { cache: 'force-cache' }) // Cached
 
  // ...
}
```

To opt all fetch requests in a layout or page into caching, you can use the `export const fetchCache = 'default-cache'` segment config option. If individual fetch requests specify a cache option, that will be used instead.

```javascript
// app/layout.js
// Since this is the root layout, all fetch requests in the app
// that don't set their own cache option will be cached.
export const fetchCache = 'default-cache'
 
export default async function RootLayout() {
  const a = await fetch('https://...') // Cached
  const b = await fetch('https://...', { cache: 'no-store' }) // Not cached
 
  // ...
}
```

### Route Handlers

GET functions in Route Handlers are no longer cached by default. To opt GET methods into caching, you can use a route config option such as `export const dynamic = 'force-static'` in your Route Handler file.

```javascript
// app/api/route.js
export const dynamic = 'force-static'
 
export async function GET() {}
```

### Client-side Router Cache

When navigating between pages via `<Link>` or `useRouter`, page segments are no longer reused from the client-side router cache. However, they are still reused during browser backward and forward navigation and for shared layouts.

To opt page segments into caching, you can use the `staleTimes` config option:

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    staleTimes: {
      dynamic: 30,
      static: 180,
    },
  },
}
 
module.exports = nextConfig
```

Layouts and loading states are still cached and reused on navigation.

### next/font

The `@next/font` package has been removed in favor of the built-in `next/font`. A codemod is available to safely and automatically rename your imports.

```javascript
// app/layout.js
// Before
import { Inter } from '@next/font/google'
 
// After
import { Inter } from 'next/font/google'
```

### bundlePagesRouterDependencies

`experimental.bundlePagesExternals` is now stable and renamed to `bundlePagesRouterDependencies`.

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Before
  experimental: {
    bundlePagesExternals: true,
  },
 
  // After
  bundlePagesRouterDependencies: true,
}
 
module.exports = nextConfig
```

### serverExternalPackages

`experimental.serverComponentsExternalPackages` is now stable and renamed to `serverExternalPackages`.

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Before
  experimental: {
    serverComponentsExternalPackages: ['package-name'],
  },
 
  // After
  serverExternalPackages: ['package-name'],
}
 
module.exports = nextConfig
```

# Architecture

Learn about the Next.js architecture and how it works under the hood.

## Accessibility

The Next.js team is committed to making Next.js accessible to all developers (and their end-users). By adding accessibility features to Next.js by default, we aim to make the Web more inclusive for everyone.

### Route Announcements

When transitioning between pages rendered on the server (e.g. using the `<a href>` tag) screen readers and other assistive technology announce the page title when the page loads so that users understand that the page has changed.

In addition to traditional page navigations, Next.js also supports client-side transitions for improved performance (using `next/link`). To ensure that client-side transitions are also announced to assistive technology, Next.js includes a route announcer by default.

The Next.js route announcer looks for the page name to announce by first inspecting `document.title`, then the `<h1>` element, and finally the URL pathname. For the most accessible user experience, ensure that each page in your application has a unique and descriptive title.

### Linting

Next.js provides an integrated ESLint experience out of the box, including custom rules for Next.js. By default, Next.js includes `eslint-plugin-jsx-a11y` to help catch accessibility issues early, including warning on:

- aria-props
- aria-proptypes
- aria-unsupported-elements
- role-has-required-aria-props
- role-supports-aria-props

For example, this plugin helps ensure you add alt text to `img` tags, use correct `aria-*` attributes, use correct `role` attributes, and more.

### Accessibility Resources

- [WebAIM WCAG checklist](https://webaim.org/standards/wcag/checklist)
- [WCAG 2.2 Guidelines](https://www.w3.org/TR/WCAG22/)
- [The A11y Project](https://www.a11yproject.com/)
- Check color contrast ratios between foreground and background elements
- Use `prefers-reduced-motion` when working with animations

## Fast Refresh

Fast refresh is a React feature integrated into Next.js that allows you live reload the browser page while maintaining temporary client-side state when you save changes to a file. It's enabled by default in all Next.js applications on 9.4 or newer. With Fast Refresh enabled, most edits should be visible within a second.

### How It Works

1. If you edit a file that only exports React component(s), Fast Refresh will update the code only for that file, and re-render your component. You can edit anything in that file, including styles, rendering logic, event handlers, or effects.
2. If you edit a file with exports that aren't React components, Fast Refresh will re-run both that file, and the other files importing it. So if both `Button.js` and `Modal.js` import `theme.js`, editing `theme.js` will update both components.
3. Finally, if you edit a file that's imported by files outside of the React tree, Fast Refresh will fall back to doing a full reload. You might have a file which renders a React component but also exports a value that is imported by a non-React component. For example, maybe your component also exports a constant, and a non-React utility file imports it. In that case, consider migrating the constant to a separate file and importing it into both files. This will re-enable Fast Refresh to work. Other cases can usually be solved in a similar way.

### Error Resilience

#### Syntax Errors

If you make a syntax error during development, you can fix it and save the file again. The error will disappear automatically, so you won't need to reload the app. You will not lose component state.

#### Runtime Errors

If you make a mistake that leads to a runtime error inside your component, you'll be greeted with a contextual overlay. Fixing the error will automatically dismiss the overlay, without reloading the app.

Component state will be retained if the error did not occur during rendering. If the error did occur during rendering, React will remount your application using the updated code.

If you have error boundaries in your app (which is a good idea for graceful failures in production), they will retry rendering on the next edit after a rendering error. This means having an error boundary can prevent you from always getting reset to the root app state. However, keep in mind that error boundaries shouldn't be too granular. They are used by React in production, and should always be designed intentionally.

### Limitations

Fast Refresh tries to preserve local React state in the component you're editing, but only if it's safe to do so. Here's a few reasons why you might see local state being reset on every edit to a file:

- Local state is not preserved for class components (only function components and Hooks preserve state).
- The file you're editing might have other exports in addition to a React component.
- Sometimes, a file would export the result of calling a higher-order component like `HOC(WrappedComponent)`. If the returned component is a class, its state will be reset.
- Anonymous arrow functions like `export default () => <div />;` cause Fast Refresh to not preserve local component state. For large codebases you can use our name-default-component codemod.

As more of your codebase moves to function components and Hooks, you can expect state to be preserved in more cases.

### Tips

- Fast Refresh preserves React local state in function components (and Hooks) by default.
- Sometimes you might want to force the state to be reset, and a component to be remounted. For example, this can be handy if you're tweaking an animation that only happens on mount. To do this, you can add `// @refresh reset` anywhere in the file you're editing. This directive is local to the file, and instructs Fast Refresh to remount components defined in that file on every edit.
- You can put `console.log` or `debugger;` into the components you edit during development.
- Remember that imports are case sensitive. Both fast and full refresh can fail, when your import doesn't match the actual filename. For example, `'./header'` vs `'./Header'`.

### Fast Refresh and Hooks

When possible, Fast Refresh attempts to preserve the state of your component between edits. In particular, `useState` and `useRef` preserve their previous values as long as you don't change their arguments or the order of the Hook calls.

Hooks with dependencies—such as `useEffect`, `useMemo`, and `useCallback`—will always update during Fast Refresh. Their list of dependencies will be ignored while Fast Refresh is happening.

For example, when you edit `useMemo(() => x * 2, [x])` to `useMemo(() => x * 10, [x])`, it will re-run even though `x` (the dependency) has not changed. If React didn't do that, your edit wouldn't reflect on the screen!

Sometimes, this can lead to unexpected results. For example, even a `useEffect` with an empty array of dependencies would still re-run once during Fast Refresh.

> However, writing code resilient to occasional re-running of `useEffect` is a good practice even without Fast Refresh. It will make it easier for you to introduce new dependencies to it later on and it's enforced by React Strict Mode, which we highly recommend enabling.

## Next.js Compiler

The Next.js Compiler, written in Rust using SWC, allows Next.js to transform and minify your JavaScript code for production. This replaces Babel for individual files and Terser for minifying output bundles.

Compilation using the Next.js Compiler is 17x faster than Babel and enabled by default since Next.js version 12. If you have an existing Babel configuration or are using unsupported features, your application will opt-out of the Next.js Compiler and continue using Babel.

### Why SWC?

SWC is an extensible Rust-based platform for the next generation of fast developer tools.

SWC can be used for compilation, minification, bundling, and more – and is designed to be extended. It's something you can call to perform code transformations (either built-in or custom). Running those transformations happens through higher-level tools like Next.js.

We chose to build on SWC for a few reasons:

- **Extensibility**: SWC can be used as a Crate inside Next.js, without having to fork the library or workaround design constraints.
- **Performance**: We were able to achieve ~3x faster Fast Refresh and ~5x faster builds in Next.js by switching to SWC, with more room for optimization still in progress.
- **WebAssembly**: Rust's support for WASM is essential for supporting all possible platforms and taking Next.js development everywhere.
- **Community**: The Rust community and ecosystem are amazing and still growing.

### Supported Features

#### Styled Components

We're working to port `babel-plugin-styled-components` to the Next.js Compiler.

First, update to the latest version of Next.js: `npm install next@latest`. Then, update your `next.config.js` file:

```javascript
// next.config.js
module.exports = {
  compiler: {
    styledComponents: true,
  },
}
```

For advanced use cases, you can configure individual properties for styled-components compilation.

> Note: `ssr` and `displayName` transforms are the main requirement for using styled-components in Next.js.

```javascript
// next.config.js
module.exports = {
  compiler: {
    // see https://styled-components.com/docs/tooling#babel-plugin for more info on the options.
    styledComponents: {
      // Enabled by default in development, disabled in production to reduce file size,
      // setting this will override the default for all environments.
      displayName?: boolean,
      // Enabled by default.
      ssr?: boolean,
      // Enabled by default.
      fileName?: boolean,
      // Empty by default.
      topLevelImportPaths?: string[],
      // Defaults to ["index"].
      meaninglessFileNames?: string[],
      // Enabled by default.
      minify?: boolean,
      // Enabled by default.
      transpileTemplateLiterals?: boolean,
      // Empty by default.
      namespace?: string,
      // Disabled by default.
      pure?: boolean,
      // Enabled by default.
      cssProp?: boolean,
    },
  },
}
```

#### Jest

The Next.js Compiler transpiles your tests and simplifies configuring Jest together with Next.js including:

- Auto mocking of `.css`, `.module.css` (and their `.scss` variants), and image imports
- Automatically sets up transform using SWC
- Loading `.env` (and all variants) into `process.env`
- Ignores `node_modules` from test resolving and transforms
- Ignoring `.next` from test resolving
- Loads `next.config.js` for flags that enable experimental SWC transforms

First, update to the latest version of Next.js: `npm install next@latest`. Then, update your `jest.config.js` file:

```javascript
// jest.config.js
const nextJest = require('next/jest')
 
// Providing the path to your Next.js app which will enable loading next.config.js and .env files
const createJestConfig = nextJest({ dir: './' })
 
// Any custom config you want to pass to Jest
const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
}
 
// createJestConfig is exported in this way to ensure that next/jest can load the Next.js configuration, which is async
module.exports = createJestConfig(customJestConfig)
```

#### Relay

To enable Relay support:

```javascript
// next.config.js
module.exports = {
  compiler: {
    relay: {
      // This should match relay.config.js
      src: './',
      artifactDirectory: './__generated__',
      language: 'typescript',
      eagerEsModules: false,
    },
  },
}
```

> Good to know: In Next.js, all JavaScript files in `pages` directory are considered routes. So, for `relay-compiler` you'll need to specify `artifactDirectory` configuration settings outside of the `pages`, otherwise `relay-compiler` will generate files next to the source file in the `__generated__` directory, and this file will be considered a route, which will break production builds.

#### Remove React Properties

Allows to remove JSX properties. This is often used for testing. Similar to `babel-plugin-react-remove-properties`.

To remove properties matching the default regex `^data-test`:

```javascript
// next.config.js
module.exports = {
  compiler: {
    reactRemoveProperties: true,
  },
}
```

To remove custom properties:

```javascript
// next.config.js
module.exports = {
  compiler: {
    // The regexes defined here are processed in Rust so the syntax is different from
    // JavaScript `RegExp`s. See https://docs.rs/regex.
    reactRemoveProperties: { properties: ['^data-custom$'] },
  },
}
```

#### Remove Console

This transform allows for removing all `console.*` calls in application code (not `node_modules`). Similar to `babel-plugin-transform-remove-console`.

Remove all `console.*` calls:

```javascript
// next.config.js
module.exports = {
  compiler: {
    removeConsole: true,
  },
}
```

Remove `console.*` output except `console.error`:

```javascript
// next.config.js
module.exports = {
  compiler: {
    removeConsole: {
      exclude: ['error'],
    },
  },
}
```

#### Legacy Decorators

Next.js will automatically detect `experimentalDecorators` in `jsconfig.json` or `tsconfig.json`. Legacy decorators are commonly used with older versions of libraries like mobx.

This flag is only supported for compatibility with existing applications. We do not recommend using legacy decorators in new applications.

First, update to the latest version of Next.js: `npm install next@latest`. Then, update your `jsconfig.json` or `tsconfig.json` file:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

#### importSource

Next.js will automatically detect `jsxImportSource` in `jsconfig.json` or `tsconfig.json` and apply that. This is commonly used with libraries like Theme UI.

First, update to the latest version of Next.js: `npm install next@latest`. Then, update your `jsconfig.json` or `tsconfig.json` file:

```json
{
  "compilerOptions": {
    "jsxImportSource": "theme-ui"
  }
}
```

#### Emotion

We're working to port `@emotion/babel-plugin` to the Next.js Compiler.

First, update to the latest version of Next.js: `npm install next@latest`. Then, update your `next.config.js` file:

```javascript
// next.config.js
module.exports = {
  compiler: {
    emotion: boolean | {
      // default is true. It will be disabled when build type is production.
      sourceMap?: boolean,
      // default is 'dev-only'.
      autoLabel?: 'never' | 'dev-only' | 'always',
      // default is '[local]'.
      // Allowed values: `[local]` `[filename]` and `[dirname]`
      // This option only works when autoLabel is set to 'dev-only' or 'always'.
      // It allows you to define the format of the resulting label.
      // The format is defined via string where variable parts are enclosed in square brackets [].
      // For example labelFormat: "my-classname--[local]", where [local] will be replaced with the name of the variable the result is assigned to.
      labelFormat?: string,
      // default is undefined.
      // This option allows you to tell the compiler what imports it should
      // look at to determine what it should transform so if you re-export
      // Emotion's exports, you can still use transforms.
      importMap?: {
        [packageName: string]: {
          [exportName: string]: {
            canonicalImport?: [string, string],
            styledBaseImport?: [string, string],
          }
        }
      },
    },
  },
}
```

#### Minification

Next.js' swc compiler is used for minification by default since v13. This is 7x faster than Terser.

If Terser is still needed for any reason this can be configured.

```javascript
// next.config.js
module.exports = {
  swcMinify: false,
}
```

#### Module Transpilation

Next.js can automatically transpile and bundle dependencies from local packages (like monorepos) or from external dependencies (`node_modules`). This replaces the `next-transpile-modules` package.

```javascript
// next.config.js
module.exports = {
  transpilePackages: ['@acme/ui', 'lodash-es'],
}
```

#### Modularize Imports

This option has been superseded by `optimizePackageImports` in Next.js 13.5. We recommend upgrading to use the new option that does not require manual configuration of import paths.

### Experimental Features

#### SWC Trace profiling

You can generate SWC's internal transform traces as chromium's trace event format.

```javascript
// next.config.js
module.exports = {
  experimental: {
    swcTraceProfiling: true,
  },
}
```

Once enabled, swc will generate trace named as `swc-trace-profile-${timestamp}.json` under `.next/`. Chromium's trace viewer (chrome://tracing/, https://ui.perfetto.dev/), or compatible flamegraph viewer (https://www.speedscope.app/) can load & visualize generated traces.

#### SWC Plugins (experimental)

You can configure swc's transform to use SWC's experimental plugin support written in wasm to customize transformation behavior.

```javascript
// next.config.js
module.exports = {
  experimental: {
    swcPlugins: [
      [
        'plugin',
        {
          ...pluginOptions,
        },
      ],
    ],
  },
}
```

`swcPlugins` accepts an array of tuples for configuring plugins. A tuple for the plugin contains the path to the plugin and an object for plugin configuration. The path to the plugin can be an npm module package name or an absolute path to the `.wasm` binary itself.

### Unsupported Features

When your application has a `.babelrc` file, Next.js will automatically fall back to using Babel for transforming individual files. This ensures backwards compatibility with existing applications that leverage custom Babel plugins.

If you're using a custom Babel setup, please share your configuration. We're working to port as many commonly used Babel transformations as possible, as well as supporting plugins in the future.

## Supported Browsers

Next.js supports modern browsers with zero configuration.

- Chrome 64+
- Edge 79+
- Firefox 67+
- Opera 51+
- Safari 12+

### Browserslist

If you would like to target specific browsers or features, Next.js supports Browserslist configuration in your `package.json` file. Next.js uses the following Browserslist configuration by default:

```json
{
  "browserslist": [
    "chrome 64",
    "edge 79",
    "firefox 67",
    "opera 51",
    "safari 12"
  ]
}
```

### Polyfills

We inject widely used polyfills, including:

- `fetch()` — Replacing: `whatwg-fetch` and `unfetch`.
- `URL` — Replacing: the `url` package (Node.js API).
- `Object.assign()` — Replacing: `object-assign`, `object.assign`, and `core-js/object/assign`.

If any of your dependencies include these polyfills, they'll be eliminated automatically from the production build to avoid duplication.

In addition, to reduce bundle size, Next.js will only load these polyfills for browsers that require them. The majority of the web traffic globally will not download these polyfills.

### Custom Polyfills

If your own code or any external npm dependencies require features not supported by your target browsers (such as IE 11), you need to add polyfills yourself.

In this case, you should add a top-level import for the specific polyfill you need in your Custom `<App>` or the individual component.

### JavaScript Language Features

Next.js allows you to use the latest JavaScript features out of the box. In addition to ES6 features, Next.js also supports:

- Async/await (ES2017)
- Object Rest/Spread Properties (ES2018)
- Dynamic `import()` (ES2020)
- Optional Chaining (ES2020)
- Nullish Coalescing (ES2020)
- Class Fields and Static Properties (ES2022)
- and more!

## Turbopack

Turbopack (beta) is an incremental bundler optimized for JavaScript and TypeScript, written in Rust, and built into Next.js.

### Usage

Turbopack can be used in Next.js in both the pages and app directories for faster local development. To enable Turbopack, use the `--turbo` flag when running the Next.js development server.

```json
{
  "scripts": {
    "dev": "next dev --turbo",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

### Supported features

Turbopack in Next.js requires zero-configuration for most users and can be extended for more advanced use cases. To learn more about the currently supported features for Turbopack, view the API Reference.

### Unsupported features

Turbopack currently only supports `next dev` and does not support `next build`. We are currently working on support for builds as we move closer towards stability.

These features are currently not supported:

1. Turbopack leverages Lightning CSS which doesn't support some low usage CSS Modules features
   - `:local` and `:global` as standalone pseudo classes. Only the function variant is supported, for example: `:global(a)`.
   - The `@value` rule which has been superseded by CSS variables.
   - `:import` and `:export` ICSS rules.
   - Invalid CSS comment syntax such as `//`
     - CSS comments should be written as `/* comment */` per the specification.
     - Preprocessors such as Sass do support this alternative syntax for comments.
2. `webpack()` configuration in `next.config.js`
   - Turbopack replaces Webpack, this means that webpack configuration is not supported.
   - To configure Turbopack, see the documentation.
   - A subset of Webpack loaders are supported in Turbopack.
3. Babel (`.babelrc`)
   - Turbopack leverages the SWC compiler for all transpilation and optimizations. This means that Babel is not included by default.
   - If you have a `.babelrc` file, you might no longer need it because Next.js includes common Babel plugins as SWC transforms that can be enabled. You can read more about this in the compiler documentation.
   - If you still need to use Babel after verifying your particular use case is not covered, you can leverage Turbopack's support for custom webpack loaders to include babel-loader.
4. Creating a root layout automatically in App Router.
   - This behavior is currently not supported since it changes input files, instead, an error will be shown for you manually add a root layout in the desired location.
5. `@next/font` (legacy font support).
   - `@next/font` is deprecated in favor of `next/font`. `next/font` is fully supported with Turbopack.
6. `new Worker('file', import.meta.url)`.
   - We are planning to implement this in the future.
7. Relay transforms
   - We are planning to implement this in the future.
8. Blocking `.css` imports in `pages/_document.tsx`
   - Currently with webpack Next.js blocks importing `.css` files in `pages/_document.tsx`
   - We are planning to implement this warning in the future.
9. `experimental.typedRoutes`
   - We are planning to implement this in the future.
10. `experimental.nextScriptWorkers`
    - We are planning to implement this in the future.
11. `experimental.sri.algorithm`
    - We are planning to implement this in the future.
12. `experimental.fallbackNodePolyfills`
    - We are planning to implement this in the future.
13. `experimental.esmExternals`
    - We are currently not planning to support the legacy esmExternals configuration in Next.js with Turbopack.
14. AMP.
    - We are currently not planning to support AMP in Next.js with Turbopack.
15. Yarn PnP
    - We are currently not planning to support Yarn PnP in Next.js with Turbopack.
16. `experimental.urlImports`
    - We are currently not planning to support `experimental.urlImports` in Next.js with Turbopack.
17. `:import` and `:export` ICSS rules
    - We are currently not planning to support `:import` and `:export` ICSS rules in Next.js with Turbopack as Lightning CSS the CSS parser Turbopack uses does not support these rules.

### Generating Trace Files

Trace files allow the Next.js team to investigate and improve performance metrics and memory usage. To generate a trace file, append `NEXT_TURBOPACK_TRACING=1` to the `next dev --turbo` command, this will generate a `.next/trace.log` file.

When reporting issues related to Turbopack performance and memory usage, please include the trace file in your GitHub issue.