## Next.js App Router Course - Starter

### clsx

- library that lets you toolge class names easily

### fonts

- Next.js automatically optimizes fonts in the application when you use `next/font`  module
- It downloads font files at **build time and hosts them with other statis assets**
    - = this means that there are no additional network request for fonts when a user vists your application
- how to add a primary font
    - Import a font you want from anywhere
        - ex.) `next/font/google` and load what you like to load
            - ex.) if you want to load `Inter`
                - then, it will be like this at `/app/ui/fonts.ts`
                    
                    ```jsx
                    import { Inter } from 'next/font/google';
                     
                    export const inter = Inter({ subsets: ['latin'] });
                    ```
                    
    - After importing the font to `fonts.ts` , you need to add the imported font to your body
        - ex.) `/app/layout.tsx`
            
            ```jsx
            import '@/app/ui/global.css';
            import { inter } from '@/app/ui/fonts';
             
            export default function RootLayout({
              children,
            }: {
              children: React.ReactNode;
            }) {
              return (
                <html lang="en">
                  <body className={`${inter.className} antialiased`}>{children}</body>
                </html>
              );
            }
            ```
            
            - `antialiased` is a Tailwind class which smooths out the font
                - not really neccessary to use this class, but it adss a nice touch!

### Images

- Next.js can serve static assets (ex. images) under the top-level `/public`  folder
- `<Image>` Component is extension of the HTML `<img>`  tag
    - provides
        - Preventing layout shift auotmatically when images are loading
        - Resizing image to avoid shipping large images to devices with smaller viewport
        - Lazy loading images by default
            - = images load as they enter the viewport
        - Serving images in modern formats like WebP and AVIF when the browser supports them
    - ex.)
        
        ```jsx
         <Image
                src="/hero-desktop.png"
                width={1000}
                height={760}
                className="hidden md:block"
                alt="Screenshots of the dashboard project showing desktop version"
              />
        ```
        
- it is a good pratice to set the `width`  and `height`  of your images to avoid layour shift
- example for being responsive for both desktop and mobile screen
    
    ```jsx
        <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
          {/* Add Hero Images Here */}
          <Image
            src="/hero-desktop.png"
            width={1000}
            height={760}
            className="hidden md:block"
            alt="Screenshots of the dashboard project showing desktop version"
          />
          <Image
            src="/hero-mobile.png"
            width={560}
            height={620}
            className="block md:hidden"
            alt="Screenshot of the dashboard project showing mobile version"
          />
        </div>
    ```
    
- Images without dimensions and web fonts are common causes of **layout shift** due to the browser having to download additional resources
    - What is layout shift?
        - If layout shift occurs → after components are loaded, page components are getting unexpectedly shifted → may decrease User Experience

### Nested Routing

- To create a nested route, you can nest folders inside each other and add `page.tsx`  files inside them, just like this
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e530245f-dd58-49bd-9e89-e741af7d4bef/619d25d7-07bc-4e97-976d-7b83a585ee98/image.png)
    
    - just by creating the folder, you can now access the /dashboard/page.tsx by simply routing to `localhost:3000/dashboard`

### Layout

- `layout.tsx` : special file to create UI that is shared between multiple pages.
    - Any component you import into this file will be part of the layout
    
    ```jsx
    import SideNav from '@/app/ui/dashboard/sidenav';
     
    export default function Layout({ children }: { children: React.ReactNode }) {
      return (
        <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
          <div className="w-full flex-none md:w-64">
            <SideNav />
          </div>
          <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
        </div>
      );
    ```
    
    - `Layout`  component receives `children`  props
        - this child can either be **a page or another layout**
- One benefit of using layouts in Next.js is  that on navigation, **only the page components update while the layout would not re-render!**
    - this is called **Partial Rendering**
- Having a “root layout” is required
    - root layout: having a layout.tsx at the root direcotry
    - Any UI you add to the root layout will be shared across all pages in your application

### Link

- `<Link />`  Component is used to link between pages in your application
    - allows you to do client-side navigation with JS
    - you can replace `<a>` tag with `<Link>`
- Why use `<Link>` instead of `<a>` ?
    - By using Link, you will be able to navigate between the pages without seeing a **full refresh, making it feel like a web app!**
        - How is it possible?
            - Next.js automatically code splits your application by route segments
                - This is different from a traditional React SPA (Single-page Application) ← where the browser loads all your application code on initial load
            - “splitting code by routes” = pages become isolated
                - If a certain page throws an error, the rest of the application will still work
    - When `<Link>` component appears in the borwser’s viewport, Next.js automatically prefetches the code for the linked route in the background
        - By the time the user clicks the link, the code for the destination page will already be loaded in the background ← making page transition near-instant!

- Showing Active Links
    - Next.js provides a hook called `usePathname()`
        - you can use to check the path and get the user’s current path from the URL
        - since this is a hook, you need to add `'use client'`
    - How can you use it?
        - by using `clsx`  library, you can conditionally apply classnames when the link is active → `link.href`  matches the `pathname`

### Fetching Data

- two options: API Layer & Database queries
    - When to use API Layer
        - if you are using 3rd party services that provides an API
        - if you are fetching data from the client, you want to have an API layer that runs on the server to avoid exposing your database secrets to the client
    - When yo use Database queries
        - When creating your API endpoints, you need to write logic to interact with your database
        - if you are using React Server Components ( = fetching data on the server), you can skip the API Layer, and query your database directly without risking to expose your dtabase secrets to the clients
- By defauly, Next.js uses React Server Components.
    - Benefits
        - Server Components support promises, providing a simpler solution for asynchronous tasks like data fetching
            - you can use `async/await`  without reaching out for `useEffect`  or `useState`
        - Server Components execute on the server = you can keep expensive data fetches and logic on the server and only sned the result to the client
        - you can query the DB directly without an additional API layer

### Using SQL in Vercel

- Why use SQL?
    - SQL is the industry standard for querying relational DB
    - Basic Understanding of SQL can help you to underestand the fundamentals of relational DB
    - SQL is versatile ← allowing you to fetch and manipulate specific data
    - Vercel Postgres SDK provides protection against SQL injections
- you can call `sql` inside any Server Component
- Things to be aware of
    1. The data requests are unintentionally blocking each other, creating a **request waterfall**
    2. By default, Next.js prerenders routes to improve performance ( = **Static Rendering**). So if the data changes, it would not reflect in the dashboard

### Request Waterfall

- “Waterfall” refers to a sequence of network requests that depend on the completion of previous requests
    - In terms of data fetching, **each request can only begin once the previous request has returned data**
- There may be cases where you want waterfalls, because you want a condition to be satisfied before making the next request
    - ex.) you want to fetch user ID and profile information first, and then proceed to fetch their lists of friends
    - This behavior can be unintentionally and impact performance
- Common way to avoid waterfall is to initiate all data requests at the same time = **Parallel data Fetching**
    - we can -use `Promise.all()`  or `Promise.allSettled()` to initiate all promises at the same time
    - ex.)
        
        ```jsx
        export async function fetchCardData() {
          try {
            const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
            const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
            const invoiceStatusPromise = sql`SELECT
                 SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
                 SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
                 FROM invoices`;
         
            const data = await Promise.all([
              invoiceCountPromise,
              customerCountPromise,
              invoiceStatusPromise,
            ]);
            // ...
          }
        }
        ```
        
    - By using this pattern, you can
        - Start executing all data fetches at the same time ← may lead to performance gains
        - Use a native JS pattern that can be applied to any Library or framework
    - But what if one data request is slower than all others?
        - the whole page is blocked from showing UI to the users while the data is being fetched
        - with Dynamic Rendering, your **application is only as fast as your slowest data fetch**
            - **Streaming** is the solution in this case

### Static Rendering

- with Static Rendering, data fetching and rendering happens on the **server at build time ( = when you deploy) or when revalidating data**
- Whenever a user visits your application, the cached result is served
    - benefits of static rendering:
        - Faster Website ← Prerendered content can be cached and globally distributed
            - This ensures that users can access your website’s content quickly and reliably
        - Reduced Server Load ← Since the content is cached, your server do not have to dynamnically generate content for each user request
        - SEO ← Prerendered content is eaiser for SEO crawlers to index ← leading to imporved search engine rankings
- it is useful for UI with **no data or data that is shared across users** (ex. static blog posts or a product page), but might not be a good fit for a **dashboard that has personalized data which is regularly updated**
    - **= Application will not reflect the latest data changes**

### Dynamic Rendering

- With Dynamic Rendering, content is rendred on the server for each user at reques time ( = when a user visits the page)
- Benefits
    - Real-Time Data ← it allows your application to display real-time or frequently updated data
    - User-Specific Content ← it is easier to serve personalized content, and update the data based on user interaction
    - Request Time information ← allows you to access information that can only be knwon at request time (ex. cookies or URL search parameters)

### Streaming

- Streaming is a data transfer techinque
    - allows you to break down a route into smaller chunks
        - progressively stream them from the server to the client as they become ready

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e530245f-dd58-49bd-9e89-e741af7d4bef/d730e1af-5790-404d-9e78-6df2f8f31305/image.png)

- By streaming, you can prevent slow data requests from blocking the whole page
    - Allows users to see and interact with parts of the page without waiting for all the data to load before any UI can be shown to the user
- Each React components can be considered a **chunk**
    - Chunks are rendered in parallel, reducing the overall load time
- How to implement it in Next.js?
    1. At the page level, withe the `loading.tsx`  file
    2. For specific components, with `<Suspense>` 
- `loading.tsx`  is a special Next.js file built on top of Suspense
    - allows you to create fallback UI to show as a replacement while page content loads
    - Static contents are loaded first, while the dynamic contnet is loading
    - users do not have to wait for the page to finish loading before navigating away
    - when you create a new folder with `( )` , the name would not be included in the URL path
- Suspense
    - allows you to derfer rendering parts of your application until some condition is met
    - You can wrap your dynamic components in Suspense
        - then pass it a fallback component to show hile the dynamic component loads
    - You can use Suspense to stream only the components you want and immediately show the rest of the page’s UI
    - Deciding where to place Suspense boundaries
        - Will depend on a few things
            1. How you want the user to experience the page as it streams
            2. What content you want to prioritize
            3. If the components rely on data fetching
        - there is no right answer
            - you can stream the whole page with `loading.tsx`
                - but may lead to a longer loading time if one of the component has a slow data fetch
            - You can stream every components
                - but may lead to UI ppping into the screen as it becomes ready
            - you can create a staggered effect by streaming page sections
                - but need to create wrapper components
        - **it is a good practice to move your data fetches down to the components that need it, and the wrap those components in Suspense**

### Partial Prerendering (PPR)

- a new rendering model that allows you to combine the benefits of static and dynamic rendering in the same route

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e530245f-dd58-49bd-9e89-e741af7d4bef/d9d369c4-6ef9-46c7-b41d-3bc732848c10/image.png)

- What are the holes in the context?
    - Locations where dynamic content will load asynchronously at reques time
- How does it work?
    - Uses React Suspense to defer rendering parts of your application until some condition is met (ex. data is loaded)
    - At build time
        - the static content is **prerendered** to create a static shell
        - the rendering of dynamic content is **postponed** until the user requests the route
- Wrapping a component in Suspense does not make the component itself dynamic ← rather used as a boundary between static and dynamic code

### URL Search Params

- Benefits
    - Bookmarkable and Shareable URLs
        - since search parameters are in the URL, users can bookmark the current state of the application
            - this includes search queries and filters for future reference or sharing
    - Server-Side Rendering and Initial Load
        - URL parameters can be directly consumed on the server to render the initial state, making it ieasier to hanlde server rendering
    - Analytics and Tracking
        - Having search queries and filters directly in the URL makes it easier to track user behavior without requiring additional client-side logic
- Hooks the will be used for search functionality
    - `useSearchParams`
        - Allows  you to access the parameters of the current URL
            - ex.) if URL = `/dashboard/invoices?page=1&query=pending` , the result would look like this: `{page: '1', query: 'pending'}`.
        - `URLSearchParams` is a Web API that provides utility methods for manipulating the URL query parameters
            - Instead of creating a complex string literal, you can use it to get the params strings like `?page=1&query=a`
    - `usePathname`
        - Let’s you read the current URL’s pathname
            - ex.) for the route `/dashboard/invoices`, `usePathname`
            would return `'/dashboard/invoices'`
    - `useRouter`
        - Enables navigation between routes within client components programmatically
- Overview of the implementation steps
    1. Capture the user’s input
        1. but the URL is updated on every keystroke
            1. implement **Debouncing, t**hat limits the rate at which a function can fire
                1. ex.)  you only want to query the database when the user has stopped typing 
    2. Update the URL with the search params
        - `replace(${pathname}?${params.toString()})` updates the URL with the user's search data. For example, `/dashboard/invoices?query=lee` if the user searches for "Lee".
            - ex.) `replace(${pathname}?${params.toString()})`
    3. Keep the URL in sync with the input file
        1. you can pass `defaultValue` to input by reading from searchParams
            1. ex.) `defaultValue={searchParams.get('query')?.toString()}`
    4. Update the table to reflect the search query
        1. now you have to modify the result page to accept a prop called `searchParams` so that you can pass the current URL params to the Table components
    - By doing this,
        - if you search for a term → updates URL → send a new request to the server → data will be fetched on the server → only the invoices that matchs the term will be returned

### Debouncing

- How it works
    1. Trigger Event
        1. When an event that should be debounced (ex. keystroke in the search box) occurs, a **timer starts**
    2. Wait
        1. If a new event occurs before the timer expires, the timer is reset
    3. Execution
        1. If the tiemer reaches the end of its countdown, the debounce function is executed
- First, let’s install `use-debounce`
- Example of using it
    
    ```jsx
      // Previous code before useDebouncedCallback
      function handleSearch(term: string){
        console.log('Searching ... ', term);
        const params = new URLSearchParams(searchParams);
        if (term){
          params.set('query', term)
        } else{
          params.delete('query')
        }
        replace(`${pathname}?${params.toString()}`)
      }
    
      
      
      // Using useDebouncedCallback with 300ms limit
      const handleSearch = useDebouncedCallback((term) => {
        console.log('Searching ... ', term);
        const params = new URLSearchParams(searchParams);
        if (term){
          params.set('query', term)
        } else{
          params.delete('query')
        }
        replace(`${pathname}?${params.toString()}`)
      }, 300); 
    ```
    
- This prevents a new database query on every keystroke ⇒ saves resources

### Pagination

- Adding pagination allows users to navigate through the different pages to view all the invoices
- https://nextjs.org/learn/dashboard-app/adding-search-and-pagination

### Server Action

- Allows you to run asynchronous code directly on the server
- eliminate the need to create API endpoints to mutate your data
    - instead, you can write asynchronous functions that exeucte on the server and can be invoked from your Client or Server Components
- Server Action offers **effective** **security solution**
    - protecting against different types of attacks
    - securing data
    - ensuring authorized access
- Server Action offers them through techniques like
    - POST requests
    - Encrypted Closures
    - Strict Input Checks
    - Error Messaging Hashing
    - Host restrictions
- Significantly enhances your app’s safety
- you can use `action`  attribute in the `<form>` element to invoke actions
    - actions will automatically receive the **native FormData object**, containing the captured data
    - why invoke Server Action within a Server Component? = Progressive Enhancement
        - **Forms will work even if JS is disabled on the client**
- Server Actions are deeply integrated with Next.js Caching
    - when a form is submitted through a Server Action, not only can you use the actions to **mutate data** but you can also **revalidate the associated cache** using APIs like `revalidatePath`  and `revalidateTag`
- `'use server'`
    - by adding this, you mark all the exported functionms within the file as Server Action
    - These server functions can then be imported and used in both Client and Server components
- In HTML, if you pass a URL to the `action` attribute,
    - URL would be the destination where your form data should be submitted ( = API endpoint)
- In React, `action` attribute is considered a special prop
    - React builds on top of it to allow actions to be invoked
    - Server Actions create a POST API endpoint
        - This is why you do not have to create API endpoints manually when using Server Actions

### Type Validation and Coercion

- It is important to validate that the data from your form aligns with the epxected types in your database
- To handle type validation, there are few options
    - manually validate types
    - using a type validation library
        - ex.) Zod: Typescript validation library that can simplify tasks for validating
- It i s a good practice to store monetary (화폐의) values in cents in your database to eliminate JS floating-point errors and ensure greater accuracy
    - ex.) `const amountInCents = amount * 100;`
- Next.js has a Client-side Router Cache that stores the route segments in the user’s browser for a time
    - this cache ensures that users can quickly navigate between routes while reducing the number of requests made to the server
    - `revalidatePath`  - you can use this function to clear the cache and trigger a new request to the server
        - after revalidating path, you would want to redirect the user back to the page using `redirect`

### Dynamic Route Segment

- Next.js allows you to create Dynamic Route Segment when you do not know the exact segment name and want to create routes based on data
    - ex.) Blog post title, product pages, …
    - You can create it by wrapping a folder’s name in `[ ]`
        - ex.) `[id]` , `[post]` , …
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e530245f-dd58-49bd-9e89-e741af7d4bef/7fb83d2a-9692-411a-9d08-ac0808946e58/image.png)
    
- should `id` be UUID or Auto-incrementing Keys?
    - UUID makes the URL longer, but eliminates the risk of ID collision ← reducing the risk of enumeration attacks ← **ideal for large databases**
    - Auto-incrementing keys would proivde cleaner URLs
- it is not possible to pass the `id` to the Server Action by simply passing it as an argument like this
    
    ```jsx
    // Passing an id as argument won't work
    <form action={updateInvoice(id)}>
    ```
    
    - Instead, you can pass `id` to the Server Action using `JS bind`
        - this ensures that any values passed to the Server Action are encoded
            - ex.)
            
            ```jsx
            import { updateInvoice } from '@/app/lib/actions';
             
            export default function EditInvoiceForm({
              invoice,
              customers,
            }: {
              invoice: InvoiceForm;
              customers: CustomerField[];
            }) {
              const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);
             
              return (
                <form action={updateInvoiceWithId}>
                  <input type="hidden" name="id" value={invoice.id} />
                </form>
              );
            }
            ```
            
        - steps to create an item
            1. Extract the data you want to create from `formData` 
            2. Validate the types with Zod
            3. Convert the things needed
            4. Pass the variables to SQL query
            5. Call `revalidatePath`  to clear the client cache and make a new server request
            6. Call `redirect`  to redirect the user to the page

### Handling Error with `error.tsx`

- `error.tsx` can be used to define UI boundaries for a route segment.
    - Serves as a “catch-all” for all unexpected errors and allows you to display a fallback UI to users
    - `error.tsx`  needs to be a Client Component ← `'use client'`  should be included
    - Accepts two props
        - `error` : This object is an instance of JS native `Error`  object
        - `reset` : This is a function to reset the error boundary
            - when executed, the function will try to re-render the route segment
- `error.tsx`  can be useful for catching all errors, `notFound`  can be used when you try to fetch a resource that does not exist
    - you have to import `{ notFound }`  from `next/navigation` , and then create `not-found.tsx` to gracefully handle not found error.
    - `notFound` takes priority over `error.tsx` ←  so you can reach out for it when you want to handle more specific errors

### Accessibility

- Improving accessibility in forms
    1. Semantic HTML
        - Using semantic elements (ex. `<input>` , `<option>` , …) instead of spamming `<div>`
        - This allows Assistive Technologies (AT) to focus on the input elements and provide contextual information to the user
    2. Labelling
        - Including `<label>`  and `htmlFor`  attribute enusres that each form filed has a descriptive text label
        - improves AT support by providing context and enhances usability
    3. Focus Outline
        - Fields are properly styled to show an outline when they are in focus
        - This is critical for accessibility as it visaully indicates the active element on the page

### Client-Side Validation

- there are a couple ways to validate forms on the client
- Simplest one would be to rely on the form validation provided by the browser
    - adding `required`  attribute to the `<input>`  and `<select>` elements in the forms
        - ex.)
            
            ```jsx
            <input
              id="amount"
              name="amount"
              type="number"
              placeholder="Enter USD amount"
              className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
              required
            />
            ```
            
- This approach is generally okay ← some ATs suppport browser validation

### Server-Side Validation

- By validating forms on the server, you can
    - Ensure the data is in the expected format before sending it to your DB
    - Reduce the risk of malicious users bypassing client-side validation
    - Have one source of truth for what is considered valid data
- import `useActionState`  hook from `react`
    - need to use `use client` directive
    - This takes two arguments: `(action, initialState)`
        - `initialState`  can be anything you define
    - This returns two values: `[state, formAction]`
        - the form state, and a function to be called when the form is submitted
    - ex.)
        
        ```jsx
        export default function Form({ customers }: { customers: CustomerField[] }) {
          const [state, formAction] = useActionState(createInvoice, initialState);
         
          return <form action={formAction}>...</form>;
        }
        ```
        
- example of full code
    
    ```jsx
    import { updateInvoice, State } from '@/app/lib/actions';
    import { useActionState } from 'react';
     
    export default function EditInvoiceForm({
      invoice,
      customers,
    }: {
      invoice: InvoiceForm;
      customers: CustomerField[];
    }) {
      const initialState: State = { message: null, errors: {} };
      const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);
      const [state, formAction] = useActionState(updateInvoiceWithId, initialState);
     
      return <form action={formAction}></form>;
    }
    ```
    
    ```jsx
    export async function updateInvoice(
      id: string,
      prevState: State,
      formData: FormData,
    ) {
      const validatedFields = UpdateInvoice.safeParse({
        customerId: formData.get('customerId'),
        amount: formData.get('amount'),
        status: formData.get('status'),
      });
     
      if (!validatedFields.success) {
        return {
          errors: validatedFields.error.flatten().fieldErrors,
          message: 'Missing Fields. Failed to Update Invoice.',
        };
      }
     
      const { customerId, amount, status } = validatedFields.data;
      const amountInCents = amount * 100;
     
      try {
        await sql`
          UPDATE invoices
          SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
          WHERE id = ${id}
        `;
      } catch (error) {
        return { message: 'Database Error: Failed to Update Invoice.' };
      }
     
      revalidatePath('/dashboard/invoices');
      redirect('/dashboard/invoices');
    }
    ```
    

### Authentication

- key part of many web applications today
    - how a system checks if the user is who they say they are
- Authentication vs Authorization
    - Authentication: making sure the user is who they say they are
        - you are providing your identity with something you have (ex. username, password…)
        - “checking who you are”
    - Authorization: next step of authentication
        - Once a user’s identity is confrimed, authorization decides what **parts of the application they are allowed to use**
        - “what you can do or access in the application”
- NextAuth.js adds authentication to your application
    - abstracts away much of the complexity involved in
        - managing sessions
        - sign-in , sign-out
        - other aspects of authentication
    - simplifies the process, providing a unified solution or auth in Next.js Applications

### NextAuth.js

- you first need to install NextAuth.js
    
    ```jsx
    pnpm i next-auth@beta
    ```
    
- Next, generate a secret key for your application
    - this key wil lbe used to encrypt cookies, ensuring the security of user sessions
    
    ```jsx
    openssl rand -base64 32
    ```
    
- Then, add your generated key to the `AUTH_SECRET`  variable
    
    ```jsx
    AUTH_SECRET=your_secret_key
    ```
    
- You need to add the pages option
    - create an `auth.config.ts` file at the root
        - you can use `pages` option to specify the routes for custom sign-in, sign-out, and error pages.
            
            ```jsx
            import type { NextAuthConfig } from 'next-auth';
             
            export const authConfig = {
              pages: {
                signIn: '/login',
              },
            } satisfies NextAuthConfig;
            ```
            
            - By adding this, the user will be redirected to our custom login page, rather than the NextAuth.js default page
- Next, add the logic to protect your routes
    - This will prevent users from accessing the dashoard pages unless they are logged in
    - here is an example code
        
        ```jsx
        import type { NextAuthConfig } from 'next-auth';
         
        export const authConfig = {
          pages: {
            signIn: '/login',
          },
          callbacks: {
            authorized({ auth, request: { nextUrl } }) {
              const isLoggedIn = !!auth?.user;
              const isOnDashboard = nextUrl.pathname.startsWith('/dashboard');
              if (isOnDashboard) {
                if (isLoggedIn) return true;
                return false; // Redirect unauthenticated users to login page
              } else if (isLoggedIn) {
                return Response.redirect(new URL('/dashboard', nextUrl));
              }
              return true;
            },
          },
          providers: [], // Add providers with an empty array for now
        } satisfies NextAuthConfig;
        ```
        
        - `authorized` callback is used to verify if the request is authorized to access a page via Next.js Middleware
            - It should be called before a request is completed
            - It receives an object with the `auth`  and `request` properties
                - `auth`  contains the user’s session
                - `request` contains the incoming request
        - `providers`  option is an array where you list different login options
            - empty array = NextAuth config
        - you need to import the `authConfig` object into a Middleware file
            - create a file `middleware.ts`
            - `matcher`  : specifies that it should run on specific paths
            - advantage of employing Middleware is that the protected routes will not even start rendering until the Middleware verifies the authentication
                - enhances security & performance of your application

### Password Hashing

- it is good practice to hash password before storing them in a database
    - Hashing converts a password into a fixed-legnth string of characters
        - which appears random, providing a layer of secuirty
- `bcrypt`  package is used to hash the user’s password before storing
    - this relies on Node.js APIs ← not available in Next.js Middleware
        - hence, create a file `auth.ts` at the root
        
        ```jsx
        import NextAuth from 'next-auth';
        import { authConfig } from './auth.config';
        import Credentials from 'next-auth/providers/credentials';
         
        export const { auth, signIn, signOut } = NextAuth({
          ...authConfig,
          providers: [Credentials({})],
        });
        ```
        
        - `providers` is an array where you list different login options
            - ex.) GitHub, Google, …
            - `[Credentials({})]`  provider allows users to log in with a username and a password
                - it is generally recommended to use alternative providers such as `OAuth`  or `email` providers
                
                ```jsx
                import NextAuth from 'next-auth';
                import Credentials from 'next-auth/providers/credentials';
                import { authConfig } from './auth.config';
                import { sql } from '@vercel/postgres';
                import { z } from 'zod';
                import type { User } from '@/app/lib/definitions';
                import bcrypt from 'bcrypt';
                 
                // ...
                 
                export const { auth, signIn, signOut } = NextAuth({
                  ...authConfig,
                  providers: [
                    Credentials({
                      async authorize(credentials) {
                        // ...
                 
                        if (parsedCredentials.success) {
                          const { email, password } = parsedCredentials.data;
                          const user = await getUser(email);
                          if (!user) return null;
                          const passwordsMatch = await bcrypt.compare(password, user.password);
                 
                          if (passwordsMatch) return user;
                        }
                 
                        console.log('Invalid credentials');
                        return null;
                      },
                    }),
                  ],
                });
                ```
                
                - `authorize`  function can handle the authentication logic
                    - you can use `zod` to validate the email and password before checking if the user exists in the database
                - `getUser` function would query the user from the database
                    
                    ```jsx
                    async function getUser(email: string): Promise<User | undefined> {
                      try {
                        const user = await sql<User>`SELECT * FROM users WHERE email=${email}`;
                        return user.rows[0];
                      } catch (error) {
                        console.error('Failed to fetch user:', error);
                        throw new Error('Failed to fetch user.');
                      }
                    }
                    ```
                    

### Metadata

- Metadata provides additional details about a webpage
- It is not visible to the users visiting the page, but it works behind the scenes
    - it is embedded within the page’s HTML ← usually within `<head>`
- This information is crucial for search engines and other systems that need to understand the webpage’s contents better
- Why is it important?
    - enhances a webpage’s SEO
        - making it more accessible and understandable for search engines and social media platforms
    - Proper metadata helps search engines effectivley index webpages → improves their ranking in search results
    - metadata like Open Graph improves the appearance of shared links on social media → making the content more appealing and informative for users
- types of metadata
    - there are various types, each serving a unique purpose
        - **Title Metadata**
            - Responsible for the title of a webpage that is displayed on the browser tab
            - crucial for SEO as it helps search engines understand what the pages is about
            
            ```jsx
            <title>Page Title</title>
            ```
            
        - **Description Metadata**
            - Provides a brief overview of the webpage content and is often displayed in search engine results
            
            ```jsx
            <meta name="description" content="A brief description of the page content." />
            ```
            
        - **Keyword Metadata**
            - includes the keywords related to the webpage content
            - helps search engines to index the page
            
            ```jsx
            <meta name="keywords" content="keyword1, keyword2, keyword3" />
            ```
            
        - Open Graph Metadata
            - enhances the way a webpage is represented when shared on social media platforms
            - provides ifnromation such as title, description, and preview image
            
            ```jsx
            <meta property="og:title" content="Title Here" />
            <meta property="og:description" content="Description Here" />
            <meta property="og:image" content="image_url_here" />
            ```
            
        - Favicon Metadata
            - links the favicon to the webpage, displayed in the browser’s address bar or tab
            
            ```jsx
            <link rel="icon" href="path/to/favicon.ico" />
            ```
            
- Two ways to add metadata to your application
    1. Config-based
        - Export a static `metadata` object or a dynamic `generateMetadata` function in a `layout.js`  or `page.js` file
    2. File-based
        - there are special files that can be used for metadata purposes
            - `favicon.ico`, `apple-icon.jpg`, and `icon.jpg`: Utilized for favicons and icons
            - `opengraph-image.jpg` and `twitter-image.jpg`: Employed for social media images
            - `robots.txt`: Provides instructions for search engine crawling
            - `sitemap.xml`: Offers information about the website's structure
    - With both these options, Next.js will automatically generate the relevant `<head>`  elements for your pages
- Favicon and Open Graph Image
    - if you move `favicon.ico`  and `opengraph-image.jpg` to `/app`  folder, Next.js will automatically identify and use these files as your favicon and OG image
        - You can also create dynamic OG images uisng `ImageResponse` constructor
- Page title and descriptions
    - in any `layout.js` or `page.js` , add additional page information like title and description. Any metadat in `layout.js` will be inherited by all pages that use it
    
    ```jsx
    import { Metadata } from 'next';
     
    export const metadata: Metadata = {
      title: 'Acme Dashboard',
      description: 'The official Next.js Course Dashboard, built with App Router.',
      metadataBase: new URL('https://next-learn-dashboard.vercel.sh'),
    };
     
    export default function RootLayout() {
      // ...
    }
    ```
    
    - Next.js will automatically add the title and metadata to your application
    - What if you want to add a custom title for a specific page?
        
        ```jsx
        import { Metadata } from 'next';
         
        export const metadata: Metadata = {
          title: {
            template: '%s | Acme Dashboard',
            default: 'Acme Dashboard',
          },
          description: 'The official Next.js Learn Dashboard built with App Router.',
          metadataBase: new URL('https://next-learn-dashboard.vercel.sh'),
        };
        ```
        
        - `%s`  in the template will be replaced with the specific page title
            - go to the specific page you want and add this!
                
                ```jsx
                export const metadata: Metadata = {
                  title: 'Invoices',
                };
                ```
