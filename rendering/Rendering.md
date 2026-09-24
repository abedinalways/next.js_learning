Rendering আসলে কী?

প্রথমে Next.js ভুলে যাও।

শুধু React চিন্তা করো।

তুমি লিখলে:

function Home() {
  return <h1>Hello Abedin</h1>
}

এখানে তুমি একটা component লিখেছো।

কিন্তু user তো এই code দেখবে না।

User দেখবে:

Hello Abedin

অর্থাৎ:

React Component
      ↓
   Rendering
      ↓
   UI তৈরি
      ↓
User দেখতে পায়
Rendering-এর সহজ সংজ্ঞা:

Component-এর code থেকে user যে UI দেখতে পায়, সেই UI তৈরি হওয়ার process-ই Rendering।

Part 2 — Browser আসলে কী বোঝে?

Browser মূলত বোঝে:

<h1>Hello Abedin</h1>

কিন্তু browser সরাসরি এইটা বুঝবে না:

function Home() {
  return <h1>Hello Abedin</h1>
}

কারণ এটা React/JavaScript code।

তাই মাঝখানে একটা process লাগে:

React Code
    ↓
React
    ↓
HTML/UI representation
    ↓
Browser
    ↓
Screen

এই process-এর নাম Rendering।

Part 3 — সবচেয়ে গুরুত্বপূর্ণ প্রশ্ন

Rendering নিয়ে যখনই কোনো প্রশ্ন আসবে, নিজেকে ২টা প্রশ্ন করবে:

Question 1:

কোথায় render হচ্ছে?

Server?
নাকি
Browser?
Question 2:

কখন render হচ্ছে?

Build time?
Request time?
Browser runtime?

এই দুইটা বুঝে ফেললে Next.js Rendering-এর 70% বুঝে ফেলবে।

Part 4 — Server আর Browser আলাদা

এটা খুব ভালোভাবে বুঝতে হবে।

ধরো তোমার Next.js application:

my-app

এবং একজন user Chrome দিয়ে website খুললো।

তখন দুইটা environment আছে:

            Next.js App
                 │
        ┌────────┴────────┐
        ↓                 ↓
      Server            Browser
Server

এখানে থাকতে পারে:

Node.js
Next.js
Database
Prisma
Secret API keys
Backend logic
Server Components
Browser

এখানে:

Chrome
JavaScript
React client runtime
DOM
User interaction
Click
Input
Mouse
Keyboard
Part 5 — Server Rendering কী?

ধরো:

// app/page.tsx

export default function Home() {
  return (
    <h1>Hello Abedin</h1>
  )
}

এটা App Router-এর default Server Component।

Conceptually:

Browser
   │
   │ Request
   ↓
Next.js Server
   │
   ↓
Home()
   │
   ↓
<h1>Hello Abedin</h1>
   │
   ↓
Browser
   │
   ↓
User sees:
Hello Abedin

অর্থাৎ component server-side environment-এ execute/render হচ্ছে।

Part 6 — তাহলে Browser কী পেল?

Browser-এ সবসময় এমন না যে এই পুরো code চলে গেল:

export default function Home() {
  return <h1>Hello Abedin</h1>
}

Server Component-এর ক্ষেত্রে server-side rendering/data work-এর বড় অংশ server-এ হয়।

Browser initial UI পেতে পারে এবং Client Components থাকলে তাদের প্রয়োজনীয় JavaScript-ও পায়।

তাই Server Component-এর উদ্দেশ্য হলো:

যে কাজ browser-এ করার প্রয়োজন নেই, সেটা server-এ করা।

Part 7 — কেন Server-এ render করব?

একটা real example দেখি।

ধরো database:

PostgreSQL
    │
    ↓
Users

তুমি page-এ user দেখাতে চাও।

Server Component:

import { prisma } from '@/lib/prisma'

export default async function UsersPage() {
  const users = await prisma.user.findMany()

  return (
    <div>
      {users.map(user => (
        <p key={user.id}>
          {user.name}
        </p>
      ))}
    </div>
  )
}

এখানে flow:

Browser
   │
   │ Request /users
   ↓
Next.js Server
   │
   ↓
UsersPage()
   │
   ↓
Prisma
   │
   ↓
PostgreSQL
   │
   ↓
Users data
   │
   ↓
React rendering
   │
   ↓
Response
   │
   ↓
Browser

এখানে browser সরাসরি database-এ যাচ্ছে না।

এটা খুব important।

Part 8 — Client Rendering কী?

এবার উল্টোটা দেখি।

'use client'

import { useState } from 'react'

export default function Counter() {
  const [count, setCount] = useState(0)

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  )
}

এখানে:

useState()

এবং:

onClick

দুটোই browser interaction-এর সাথে সম্পর্কিত।

তাই এটা Client Component।

Part 9 — use client আসলে কী?

এই line:

'use client'

মানে:

এই component-কে client-side React functionality-এর জন্য ব্যবহার করতে হবে।

এটা এমন না যে:

"use client দিলে component শুধু browser-এ render হবে।"

এই distinction খুব important।

Client Component initial rendering server-এর সাথে involved হতে পারে, কিন্তু তার interactive JavaScript browser-এ প্রয়োজন হয়।

তাই:

'use client'

কে শুধু:

"Browser rendering only"

হিসেবে ভাবা ভুল।

বরং ভাবো:

"এই component-এর জন্য client-side JavaScript/React interaction দরকার।"

Part 10 — কেন useState Server Component-এ চলে না?

Server Component:

export default function Counter() {
  const [count, setCount] = useState(0)
}

এটা allowed না।

কেন?

কারণ server component-এর কাজ হলো request-এর জন্য UI/data তৈরি করা।

কিন্তু:

useState()

এর state browser interaction-এর সাথে connected।

User click করলে:

Click
 ↓
State change
 ↓
React re-render
 ↓
UI update

এই lifecycle browser-side interactive environment-এর প্রয়োজন।

Part 11 — এবার খুব সহজ একটা analogy

ধরো তুমি একটা restaurant চালাও।

Server = Kitchen

Kitchen-এ:

Food prepare
Ingredients
Cooking
Data processing
Browser = Customer table

Customer:

Food receives
Eat
Order more
Click menu
Interact

Server Component:

Kitchen-এ খাবার তৈরি।

Client Component:

Table-এ customer interaction।

Part 12 — Static Rendering

এখন আসি rendering-এর দ্বিতীয় dimension-এ:

কখন render হচ্ছে?

ধরো:

export default function About() {
  return (
    <h1>
      About Our Company
    </h1>
  )
}

এই content প্রতিদিন পরিবর্তন হয় না।

Next.js build-এর সময় এটা pre-render করতে পারে।

npm run build
      ↓
Next.js
      ↓
About page render
      ↓
Ready output
      ↓
Deploy

তারপর user request করলে:

User
 ↓
Server/CDN
 ↓
Already prepared output
 ↓
Browser

এটাকে সহজভাবে Static Rendering হিসেবে ভাবো।

Part 13 — Static কেন fast?

কারণ user আসার সময় সব কাজ নতুন করে করতে হচ্ছে না।

ধরো:

1000 users

Static page হলে একই prepared result অনেক user-এর কাছে serve করা যায়।

Prepared page
     │
 ┌───┼────┬────┐
 ↓   ↓    ↓    ↓
 U1  U2   U3   U4
Part 14 — Dynamic Rendering

এখন ধরো:

/dashboard

User A:

Hello Abedin
Balance: $500

User B:

Hello Rahim
Balance: $100

একই page কিন্তু data আলাদা।

তখন request-এর সময় user-specific data বের করতে হয়।

User request
     ↓
Next.js Server
     ↓
Authenticate user
     ↓
Database
     ↓
User data
     ↓
Render
     ↓
Browser

এটাই dynamic rendering-এর basic idea।

Part 15 — Static vs Dynamic একদম সহজভাবে

ধরো restaurant-এর menu।

Static

আজকের fixed menu:

Burger
Pizza
Pasta

সবাই একই জিনিস দেখবে।

Dynamic

Customer-specific order:

Abedin → 2 Burgers
Rahim → 1 Pizza
Karim → 3 Pastas

এটা user অনুযায়ী পরিবর্তিত।

Part 16 — Static Rendering কখন?

উদাহরণ:

/about
/contact
/pricing
/terms
/privacy

এগুলোর content সাধারণত user অনুযায়ী বদলায় না।

Part 17 — Dynamic Rendering কখন?

উদাহরণ:

/dashboard
/profile
/orders
/account

এখানে user-specific information থাকতে পারে।

Part 18 — এখন CSR বুঝি

CSR = Client-Side Rendering।

ধরো:

'use client'

import { useEffect, useState } from 'react'

export default function Products() {
  const [products, setProducts] = useState([])

  useEffect(() => {
    fetch('/api/products')
      .then(res => res.json())
      .then(data => setProducts(data))
  }, [])

  return (
    <div>
      {products.map(product => (
        <p key={product.id}>
          {product.name}
        </p>
      ))}
    </div>
  )
}

এখানে flow:

Browser
 ↓
Load page
 ↓
Download JS
 ↓
React starts
 ↓
useEffect()
 ↓
API request
 ↓
Data আসে
 ↓
setProducts()
 ↓
React re-render
 ↓
Products দেখা যায়

এটাই CSR-এর classic flow।

Part 19 — Server Rendering আর CSR-এর পার্থক্য
Server rendering
Request
 ↓
Server
 ↓
Data
 ↓
Render
 ↓
Browser
CSR
Request
 ↓
Browser
 ↓
JS
 ↓
API
 ↓
Data
 ↓
Render
Part 20 — কোনটা better?

এখানে "একটা সবসময় better" বলা ঠিক না।

Use case অনুযায়ী।

Server rendering useful যখন:
SEO
Initial content
Database access
Sensitive data
Fast initial UI
Client rendering useful যখন:
Highly interactive UI
Browser APIs
Live interaction
Client state
Drag/drop
Complex editors
WebSocket UI
Part 21 — এখন Hydration

এটা অনেক beginner-এর কাছে confusing।

ধরো server একটা button পাঠালো:

<button>
  Like
</button>

Browser button দেখতে পাচ্ছে।

কিন্তু server HTML নিজে থেকে এই JavaScript behavior বহন করছে না:

onClick={() => likePost()}

Client-side React JavaScript load হওয়ার পর React এই UI-এর সাথে interactive behavior connect করে।

এই process:

Hydration

সহজভাবে:

Server থেকে পাওয়া UI-কে browser-side React-এর মাধ্যমে interactive করে তোলাই hydration।

Part 22 — Hydration flow
Server
 ↓
HTML/UI
 ↓
Browser
 ↓
User দেখতে পায়
 ↓
JavaScript download
 ↓
React hydrate
 ↓
Events connect
 ↓
Interactive

তাই initial UI দেখা এবং fully interactive হওয়া একই মুহূর্তে নাও হতে পারে।

Part 23 — Example
'use client'

export default function LikeButton() {
  return (
    <button onClick={() => alert('Liked')}>
      Like
    </button>
  )
}

Initial UI:

[ Like ]

Hydration-এর পরে:

[ Like ] ← click works
Part 24 — এখন Re-rendering

ধরো:

'use client'

function Counter() {
  const [count, setCount] = useState(0)

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  )
}

Initial:

0

Click:

setCount(1)

React component আবার render করে:

1

এটাকে re-render বলে।

খেয়াল করো:

Re-rendering আর Server Rendering একই জিনিস না।

Part 25 — একটা ভুল ধারণা দূর করি

অনেকে ভাবে:

Server Component
=
Page শুধু একবার render হবে

না।

Server Component request/navigation/data flow অনুযায়ী আবার execute/render হতে পারে।

আবার Client Component:

state change

হলে browser-এ re-render করতে পারে।

Part 26 — Data Fetching + Rendering

এখন real example:

export default async function ProductsPage() {
  const res = await fetch(
    'https://api.example.com/products'
  )

  const products = await res.json()

  return (
    <div>
      {products.map(product => (
        <p key={product.id}>
          {product.name}
        </p>
      ))}
    </div>
  )
}

এখানে তোমার প্রশ্ন হওয়া উচিত:

fetch() কোথায় চলছে?

যদি এটা Server Component হয়:

Server
 ↓
fetch()
 ↓
API
 ↓
Data
 ↓
React rendering
 ↓
Browser
Part 27 — তাহলে Client Component-এ fetch করলে?
'use client'

useEffect(() => {
  fetch('/api/products')
}, [])

Flow:

Browser
 ↓
React
 ↓
useEffect
 ↓
fetch
 ↓
API
 ↓
Data
 ↓
setState
 ↓
Re-render
Part 28 — এখান থেকেই Cache

এখন ধরো API call হলো:

GET /products

প্রথমবার:

Server
 ↓
API
 ↓
Products

যদি result cache করা যায়:

API
 ↓
Products
 ↓
CACHE

পরের request:

Server
 ↓
CACHE
 ↓
Products

API call আবার না-ও লাগতে পারে।

Part 29 — Cache ≠ Rendering

এটা bold করে মনে রাখো:

Rendering বলে UI কোথায়/কখন তৈরি হবে।

Caching বলে আগের data/result reuse করা যাবে কি না।

দুটো related, কিন্তু এক জিনিস না।

Part 30 — Revalidation

Cache forever রাখতে চাই না।

ধরো:

revalidate: 60

মানে conceptually:

0 sec
 ↓
Data cache

1 sec
 ↓
Cache

30 sec
 ↓
Cache

59 sec
 ↓
Cache

60+ sec
 ↓
Revalidation
 ↓
Fresh data

এটা useful:

Blogs
News
Products
Public content

যেখানে data update হয়, কিন্তু প্রতি request-এ fresh data দরকার নেই।

Part 31 — Streaming

এবার ধরো একটা page:

Dashboard
 ├── Header
 ├── Profile
 ├── Orders
 ├── Analytics
 └── Notifications

Analytics API অনেক slow।

যদি পুরো page-এর জন্য অপেক্ষা করো:

Header
Profile
Orders
Analytics
Notifications
     ↓
সব ready
     ↓
Browser

User অপেক্ষা করবে।

Streaming-এ:

Header       → Browser
Profile      → Browser
Orders       → Browser

Analytics
   ↓
still loading

Analytics ready
   ↓
Browser

এটাই streaming।

Part 32 — Suspense

এটা streaming-এর সাথে খুব useful।

<Suspense fallback={<Loading />}>
  <Analytics />
</Suspense>

Flow:

Page
 │
 ├── Header → ready
 │
 ├── Profile → ready
 │
 └── Analytics
        ↓
     Loading...
        ↓
     Data ready
        ↓
     Analytics UI
Part 33 — কেন loading.tsx দরকার?

ধরো:

app/
└── dashboard/
    ├── page.tsx
    └── loading.tsx

page.tsx slow হলে Next.js loading UI দেখাতে পারে।

export default function Loading() {
  return <DashboardSkeleton />
}

User blank screen না দেখে:

Dashboard skeleton

দেখতে পারে।

Part 34 — এখন পুরো Rendering Flow

এখন একটা request-এর পুরো story দেখি।

User লিখলো:

https://example.com/products

তারপর:

1. Browser request পাঠায়
          ↓
2. Next.js request receive করে
          ↓
3. Route determine করে
          ↓
4. Server Components execute হতে পারে
          ↓
5. Data fetch হয়
          ↓
6. Cache/revalidation rules apply হতে পারে
          ↓
7. React server-side output তৈরি করে
          ↓
8. Initial HTML/RSC data browser-এর দিকে যায়
          ↓
9. Browser UI display করে
          ↓
10. Client Component JS load হয়
          ↓
11. Hydration হয়
          ↓
12. UI interactive হয়

এই flow-টা ভালোভাবে বুঝে ফেললে Next.js rendering অনেক সহজ হয়ে যাবে।

Part 35 — Server Component + Client Component একসাথে

Production-এ সাধারণত এমন architecture দেখবে:

export default async function ProductPage() {
  const product = await getProduct()

  return (
    <main>
      <ProductInfo product={product} />

      <AddToCartButton
        productId={product.id}
      />
    </main>
  )
}

এখানে:

ProductPage
    │
    ├── ProductInfo
    │       ↓
    │    Server
    │
    └── AddToCartButton
            ↓
         Client

এটাই খুব common pattern।

Part 36 — কেন পুরো page-এ use client দেব না?

ধরো:

'use client'

export default function ProductPage() {
  ...
}

যদি পুরো page-এ interaction না লাগে, তাহলে unnecessary client boundary তৈরি হতে পারে।

বরং:

export default function ProductPage() {
  return (
    <>
      <ProductDetails />
      <AddToCartButton />
    </>
  )
}

শুধু:

'use client'

export function AddToCartButton() {
  ...
}

এভাবে রাখলে server/client separation পরিষ্কার থাকে।

Part 37 — Rendering শেখার সময় একটা খুব important mistake

এই প্রশ্নটা:

“এই page SSR নাকি CSR?”

এটা এখন আর যথেষ্ট না।

Modern Next.js-এ better questions:

1.

এই component Server নাকি Client?

2.

Data কোথা থেকে আসছে?

3.

Data কখন fetch হচ্ছে?

4.

Data cache হচ্ছে?

5.

Page static নাকি dynamic behavior নিচ্ছে?

6.

কোন অংশ streaming হচ্ছে?

7.

কোন অংশ hydrate হচ্ছে?

এই ৭টা প্রশ্ন করলে architecture বুঝতে পারবে।

Part 38 — একটি complete example

ধরো তোমার app:

app/
└── products/
    └── page.tsx
export default async function ProductsPage() {
  const products = await getProducts()

  return (
    <main>
      <h1>Products</h1>

      {products.map(product => (
        <ProductCard
          key={product.id}
          product={product}
        />
      ))}
    </main>
  )
}

এখানে ProductsPage Server Component।

তারপর:

'use client'

export default function ProductCard({
  product,
}: Props) {
  const [liked, setLiked] = useState(false)

  return (
    <article>
      <h2>{product.name}</h2>

      <button onClick={() => setLiked(!liked)}>
        {liked ? 'Liked' : 'Like'}
      </button>
    </article>
  )
}

এখন architecture:

                 ProductsPage
                      │
                 Server Component
                      │
                fetch/get data
                      │
                      ↓
                  ProductCard
                      │
                Client Component
                      │
                 useState
                      │
                   onClick

এখানে server-এর কাজ:

Data fetching
Content rendering

Client-এর কাজ:

Interaction
State
Events
Part 39 — এটা মনে রাখার shortcut

আমি তোমাকে একটা shortcut দিচ্ছি।

Server = Data + Content

Server Component সাধারণত:

Database
API
Authentication
Secrets
Data fetching
SEO content
Client = Interaction

Client Component সাধারণত:

useState
useEffect
onClick
Forms
Browser API
Animation
WebSocket UI
Drag/drop
Interactive widgets

এটা absolute rule না, কিন্তু beginner হিসেবে mental model হিসেবে খুব useful।

Part 40 — তোমার GSAP-এর ক্ষেত্রেও এটা গুরুত্বপূর্ণ

তুমি যেহেতু GSAP শিখছো, এটা তোমার জন্য খুব important।

GSAP সাধারণত browser DOM-এর সাথে কাজ করে।

যেমন:

'use client'

import gsap from 'gsap'
import { useEffect, useRef } from 'react'

export default function Hero() {
  const titleRef = useRef<HTMLHeadingElement>(null)

  useEffect(() => {
    gsap.from(titleRef.current, {
      y: 50,
      opacity: 0,
    })
  }, [])

  return (
    <h1 ref={titleRef}>
      Hello
    </h1>
  )
}

এখানে:

GSAP
 ↓
DOM
 ↓
Browser

তাই এই interaction অংশ Client Component হওয়া স্বাভাবিক।

কিন্তু পুরো page-কে Client Component বানানো দরকার নেই।

Part 41 — Socket.io-এর ক্ষেত্রেও

ধরো:

/chat

তোমার architecture হতে পারে:

Server Component
      ↓
Initial chat data
      ↓
Client Component
      ↓
Socket.io
      ↓
Real-time messages

অর্থাৎ:

Server
 ↓
Initial state

Client
 ↓
Real-time interaction

এটা খুব powerful pattern।

Part 42 — RTK Query-এর ক্ষেত্রেও

তোমার project-এ যদি RTK Query থাকে:

Server Component
       ↓
Initial server data

Client Component
       ↓
RTK Query
       ↓
Client cache
       ↓
Mutations / refetch

এভাবে server rendering এবং client data management combine করা যায়।

Part 43 — Rendering শেখার পর তোমার next target কী হওয়া উচিত?

আমি তোমার learning sequence এভাবে রাখতাম:

STEP 01
Rendering basic
      ↓
STEP 02
Server vs Client Component
      ↓
STEP 03
Static vs Dynamic Rendering
      ↓
STEP 04
Hydration
      ↓
STEP 05
Caching
      ↓
STEP 06
Revalidation
      ↓
STEP 07
Streaming
      ↓
STEP 08
Suspense
      ↓
STEP 09
RSC
      ↓
STEP 10
Cache Components
      ↓
STEP 11
Production architecture

এর মধ্যে Server vs Client + Hydration + Cache সবচেয়ে ভালোভাবে বোঝা দরকার।

সবচেয়ে সহজভাবে পুরো বিষয়টা মনে রাখো
                    NEXT.JS
                       │
                       ↓
                  RENDERING
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
       SERVER                    CLIENT
          │                         │
          ↓                         ↓
 Server Component            Client Component
          │                         │
          ↓                         ↓
 Data / Content               Interaction
          │                         │
          ↓                         ↓
 Static / Dynamic             Hydration
          │                         │
          └────────────┬────────────┘
                       ↓
                    Browser
                       ↓
                       UI

আর এর চারপাশে:

Cache
Revalidation
Streaming
Suspense