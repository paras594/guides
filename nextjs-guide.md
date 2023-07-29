
### Start Project

```bash
npx create-next-app project-name
```



### Start dev server

```bash
npm run dev
```



### Creating Pages

Next has file based routing. Path to file becomes the url path.

- pages/index.js is associated with the route "/"
- pages/posts/first-post.js is associated with route "/posts/first-post"

```jsx
export default function FirstPost(){
   return <h1>First Post</h1>;
}
```



### Navigation

For client side navigation nextjs offers <Link> React component. Page transition happens using javascript. 

```jsx
import Link from "next/link"

// usage
<Link href="/posts/first-post">
   <a>First Post</a> // add classes to <a> instead of Link
</Link>
```



> To Link an external page outside next app, use <a> tag without <Link>



### Assets

Static assets such as images can be placed in public folder and can be accessed from the root of application similar to pages

```html
<!-- using image in public/myimage.svg -->
<img src="/myimage.svg" alt="my image" />
```



### Metadata

Metadata includes data that goes inside head tags of html such as title, favicon link etc.

Reference: [Link](https://nextjs.org/docs/api-reference/next/head)

```jsx
import Head from 'next/head';

export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
        <meta
          name="description"
          content="Learn how to build a personal website using Next.js"
        />
      </Head>
      // ... other data
    </>
  )
}
```



### Layout Component

```jsx
// components/layout.js
import Head from "next/head";
import NavBar from "./Navbar";

const Layout = ({ children }) => (
	<>
		<NavBar />
		{children}
	</>
);

export default Layout;
```



#### Using Layout Component

```jsx
import Head from 'next/head'
import Link from 'next/link'
import Layout from '../../components/layout'

export default function FirstPost() {
  return (
    <Layout>
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </Layout>
  )
}
```



### Global App Component

If you want something to be available for every page in app e.g. global css, state etc. next js has support for that

NOTE: Restart development server when you add '_app.js'

```jsx
// pages/_app.js
// import App from 'next/app'
import "../css/style.css";
import "semantic-ui-css/semantic.min.css";
import Layout from "../components/Layout";

// this is a top level component which is common across all pages
function MyApp({ Component, pageProps }) {
	return (
		<Layout>
			<Component {...pageProps} />
		</Layout>
	);
}
```



### Pre-rendering

By default, Next.js pre-renders every page. This means that Next.js *generates HTML for each page in advance*, instead of having it all done by client-side JavaScript. Pre-rendering can result in better performance and SEO.

Each generated HTML is associated with minimal JavaScript code necessary for that page. When a page is loaded by the browser, its JavaScript code runs and makes the page fully interactive. (This process is called **hydration**.)



#### Two Forms of pre-rendering

Next.js has two forms of pre-rendering: **Static Generation** and **Server-side Rendering**. The difference is in **when** it generates the HTML for a page.

- **Static Generation** is the pre-rendering method that generates the HTML at **build time**. The pre-rendered HTML is then *reused* on each request.
- **Server-side Rendering** is the pre-rendering method that generates the HTML on **each request**.



##### Static Generation

For some pages, you might not be able to render the HTML without first fetching some external data. Maybe you need to access the file system, fetch external API, or query your database at build time.

Reference: [Link](https://nextjs.org/learn/basics/data-fetching/with-data)

```jsx
// static props are passed to function
export default function Home({ data }){
   return (
   	<div>
      	{ /* render data here */}
      </div>
   )
}

// called at build time on server side
export async function getStaticProps(context) {
  // context = { params, preview, previewData }
  // we can also run database queries in here
  const allPostsData = fetchData()
  return {
    props: {
      data
    }
  }
}
```



> it’s meant to be run at build time, you won’t be able to use data that’s only available during request time, such as query parameters or HTTP headers.



**When to use getStaticProps: **

- The data required to render the page is available at build time ahead of a user’s request.
- The data comes from headless CMS.
- The data can be publicly cached (not user-specific).
- The page must be pre-rendered (for SEO) and be very fast — `getStaticProps` generates HTML and JSON files, both of which can be cached by a CDN for performance.



##### Server-side rendering

Fetching data at request time. Method used `getServerSideProps`

```jsx
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps(context) {
  // context: { params, req, res, query, preview, previewData }
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}

export default Page
```



### Dynamic Routes

```jsx
// pages/posts/[id].js
export default function Post({ postData }) {
  return (
    <Layout>
      {postData.title}
      <br />
      {postData.id}
      <br />
      {postData.date}
    </Layout>
  )
}

// get data for the dynamic route
export async function getStaticProps({ params }) {
  const postData = getPostData(params.id)
  return {
    props: {
      postData
    }
  }
}

// create static paths 
export async function getStaticPaths() {
	const res = await fetch(`https://jsonplaceholder.typicode.com/todos/`);
	const posts = await res.json();

   // create static urls
   // /posts/1
   // /posts/2 ...
	const paths = todos.map((todo) => `/posts/${todo.id}`); 

   return {
      paths,
      fallback: false
   }
}
```



### Next Router 

```jsx
import Router, { useRouter } from 'next/router';

// for demo path: pages/post/[pid].js
const Post = () => {
   const router = useRouter();
   const { pid } = router.query;

   // imperitive routing
   Router.push('/about');

   return <p>Post: {pid}</p>
}

export default Post;
```



