# Next.js Guide v2



### Major Imports

```js
import Link from "next/link";
import {useRouter} from "next/router";
import Head from "next/head";
```





### Create next app

```bash
npx create-next-app
```



### Start the app

```
npm run dev
```



### Navigation

```jsx
// using link 
import Link from "next/link";

<Link href="/news"><a>News</a></Link>

// programatic redirect/navigation
import {useRouter} from "next/router";

const router = useRouter();
router.push("/news");
```



### Dynamic page

- name the file/folder with square brackets and add an identified in between them
- access the identified like below

```jsx
// filename: [newsId].js
import {useRouter} from "next/router";

function DetailsPage() {
    const router = useRouter();
    const {newsId} = router.query;
    
    return <h1>Details page</h1>
}
```



### _app.js file

- kind of root component
- you can add your layout around the component it renders



### css modules

- creating a file with module in its name make it scoped to that component

```jsx
// filename: mystyle.module.css
.detail {
   text-align: center;
}

.detail img {
   width: 100%;
}

// using the css 
import classes from "./mystyles.module.css";
<section className={classes.detail}>...</section>
```



### Page pre rendering

Next offers two ways of pre rendering:

- static generation (shoudl be used)
- server side rendering



**Static Generation**

if you need to wait for data use the following in page components only

you also need to export getStaticPaths function (if dynamic page and using staticProps)

```js
function page(props){ console.log(props.items)}

export async function getStaticPaths(context) {
    return {
        fallback: false, // false means pages are defined
        				// true means fetch other pages
        paths: [
            {
                params: {
                	meetupId: "m1"
            	}
            },
            {
                params: {
                    meetupId: "m2"
                }
            }
        ]
    }
}

export async function getStaticProps(context) {
    const { meetupId } = context.params;
    
    // fetch data
    return {
        props: {
            items: data
        }, 
        revalidate: 10 // 10s
    }
}
```

- it runs before our component runs. It can be async
- it runs during build process so data is never fetched after that during in live site
- revalidate generates pages even after build and generates new pages at a specified interval of time



**Server side rendering**

```js
export function getServerSideProps(context) {
    const {req, res} = context;
   
    // fetch data from api
    
    return {
        props: {
            items: data
        }
    }
}
```

- if data changes very frequently
- use when you need to access req, res object like auth cookie session



### API routes

create `api` folder in pages folder

```js
// /api/new-meetup
import { MongoClient } from "mongodb";

async function handler(req, res) {
	if (req.method === "POST") {
		const data = req.body;

		const client = await MongoClient.connect(
			"mongodb+srv://paras594:mongodbparas101@cluster0.zaceq.mongodb.net/meetups?retryWrites=true&w=majority"
		);
		const db = client.db();

		const meetupsCollection = db.collection("meetups");

		const result = await meetupsCollection.insertOne(data);
		console.log(result);

		client.close();

		res.status(201).json({
			message: "Meetup Inserted",
		});
	}
}

export default handler;
```



### meta data

```jsx
import Head from "next/head"

<Head>
    <title>my title</title>
	<meta
        name="description"
        content="Browse a huge list of highly active meetups"
    />
</Head>
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



### points

- no need to import react. handled by next behind the scenes
- hydrate means react will take over and turn this into a single page app
- you can directly fetch data from database in getStaticProps instead of querying api  