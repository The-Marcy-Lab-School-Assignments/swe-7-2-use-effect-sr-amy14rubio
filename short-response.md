# Short Response: useEffect, Fetching, and the Vite Proxy

Answer each question below. Write in complete sentences (3–5 per answer).

---

## Question 1 — useEffect and the Dependency Array

Explain what `useEffect` does and the role of the dependency array. In your answer, describe the difference in behavior between these three calls:

```js
useEffect(sideEffect);
useEffect(sideEffect, []);
useEffect(sideEffect, [currentUser]);
```

**Your answer:**
`useEffect` is a **React hook** that handles side effects by taking in **a function and a dependency array**. The values in the dependency array are what `useEffect` will use to **watch for changes and run the effect again**. In the first call of the example above, `useEffect` constantly runs `sideEffect` whenever **a change is made to any state** in the app. In the second call, `useEffect` only runs **once on mount** or when the application first renders. In the third call, `useEffect` runs **every time `currentUser` changes**.

---

## Question 2 — Why Not `async` Directly?

The code below throws an error. Explain why and what the correct workaround is?

```jsx
// This throws an error — why?
const loadData = async () => {
  const { data } = await loadData();
  setData(data);
};

useEffect(loadData, []);
```

**Your answer:**
The first error in the code is that `useEffect` **takes in an async function**, `loadData`, which returns a promise. `useEffect` expects either `undefined` or a cleanup function to be returned, so an async function **cannot** be passed directly into it. The correct workaround is to **pass a callback function** to `useEffect` and invoke `loadData` there. The second error is that `loadData` **recursively calls itself** with `await loadData()`, creating an infinite loop of function calls.

---

## Question 3 — Refetch After Write

Describe the **refetch-after-write** pattern. Why do we re-fetch the full list from the server after a POST, PATCH, or DELETE, rather than just updating the state array directly in the frontend?

**Your answer:**
We have to **refetch-after-write** in order to **keep the frontend state synchronized** with the database. Whenever a change is made to the database, the frontend state doesn't **immediately** match the modified server data. Instead, the data needs to be **re-fetched from the server** so the frontend can reflect the appropriate changes made to the database.

---

## Question 4 — Same-Origin vs. Cross-Origin and the Vite Proxy

In development, the React app runs on `http://localhost:5173` and the Express server runs on `http://localhost:8080`.

(a) If you write `fetch('/api/entries')` in your React code, what origin does the request go to (without a proxy)?

(b) What does the Vite proxy do, and how does it solve that problem?

(c) Why don't we need the proxy in production?

**Your answer:**
(a) Without a proxy, the request would go to Vite's frontend origin at `http://localhost:5173/api/entries`. This would fail because the `/api/entries` endpoint **is not handled by the Vite development server** and instead belongs to the backend Express server running on a **different origin**.

(b) The Vite proxy redirects any `/api` endpoints to the app's **Express server origin**, where the endpoint can be appropriately handled. This allows the browser to make requests **without directly communicating with a different backend origin**.

(c) We don't need a proxy in production because **all endpoints in production are accessed through the backend Express server**. In production, the frontend is served through our backend referencing the `/dist` folder where all of our **frontend code is compiled to JavaScript**. This means that both the frontend and backend are **served from the same origin**.

---

## Question 5 — Bug Fix

The component below is supposed to fetch a list of posts when the user types a search query and hits Search. But it has a bug: every time any state in `App` updates (for any reason), the fetch fires again — even when the search query hasn't changed.

Identify the bug and explain why the original code causes this behavior. Then explain how you would fix it.

```jsx
const App = () => {
  const [query, setQuery] = useState('');
  const [posts, setPosts] = useState([]);
  const [count, setCount] = useState(0);

  // Bug is here
  useEffect(() => {
    const load = async () => {
      if (!query) return;
      const { data } = await searchPosts(query);
      setPosts(data);
    };
    load();
  });

  return (
    <>
      <button onClick={() => setCount((c) => c + 1)}>Clicked {count} times</button>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <button onClick={() => {}}>Search</button>
      <PostList posts={posts} />
    </>
  );
};
```

**Your answer:**
The bug in this code is that `useEffect` **doesn't have a dependency array**, meaning that the effect runs after every render, or whenever any state updates in the component. This causes **unnecessary fetch requests** and repeatedly updates the `posts` state even when the `query` value **has not changed**. I would fix this bug by **adding a dependency array** that watches over the `query` state so the **effect only runs whenever `query` changes**.

```jsx
useEffect(() => {
  const load = async () => {
    if (!query) return;
    const { data } = await searchPosts(query);
    setPosts(data);
  };
  load();
}, [query]);
```
