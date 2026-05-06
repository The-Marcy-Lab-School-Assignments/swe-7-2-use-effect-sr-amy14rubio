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

---

## Question 3 — Refetch After Write

Describe the **refetch-after-write** pattern. Why do we re-fetch the full list from the server after a POST, PATCH, or DELETE, rather than just updating the state array directly in the frontend?

**Your answer:**

---

## Question 4 — Same-Origin vs. Cross-Origin and the Vite Proxy

In development, the React app runs on `http://localhost:5173` and the Express server runs on `http://localhost:8080`.

(a) If you write `fetch('/api/entries')` in your React code, what origin does the request go to (without a proxy)?

(b) What does the Vite proxy do, and how does it solve that problem?

(c) Why don't we need the proxy in production?

**Your answer:**

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
      <button onClick={() => setCount(c => c + 1)}>Clicked {count} times</button>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <button onClick={() => {}}>Search</button>
      <PostList posts={posts} />
    </>
  );
};
```

**Your answer:**

---
