# React router (v6)

Si installa con:

```bash
> npm i -D react-router-dom
# oppure aggiornando da un progetto con V5
> npm i -D react-router-dom@latest
```

Si deve usare il tag padre `Routes` e i tag figli `Route` dove in questi si specifica il path ed il componente da usare.
E' possibile avere rotte innestate (come quella di Layout che contiene altre rotte).

Con `index` si specifica il componente di default tra n figli, mentre con `*` vale per tutte quelle rotte che non matchano le precedenti (no match rule).

```javascript
import ReactDOM from "react-dom/client";
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Layout from "./pages/Layout";
import Home from "./pages/Home";
import Blogs from "./pages/Blogs";
import NoPage from "./pages/NoPage";

export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="blogs" element={<Blogs />} />
          <Route path="users" element={<Users users={users} />}>
            <Route path=":userId" element={<User />} />
          </Route>
          <Route path="*" element={<NoPage />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);
```

IL componente Layout avrà dentro l' `Outlet` per renderizzare dentro i figli. Tramite il componente `Link` è possibile avere il redirect al path specificato nell'attributo `to`. E' possibile avere dei link \<Navlink /> con stili condizionali a seconda se attivi.

```javascript
import { Outlet, Link } from "react-router-dom";

const Layout = () => {
  return (
    <>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/blogs">Blogs</Link>
          </li>
        </ul>
      </nav>

      <Outlet />
    </>
  );
};

export default Layout;
```

Mentre per le rotte User si possono leggere i parametri tramite:

```javascript

// componente per path /users
const Users = ({ users }) => {
  return (
    <>
      <h2>Users</h2>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            <Link to={user.id}> // notare che è un link relativo per andare a /users/1234 !!!!
              {user.fullName}
            </Link>
          </li>
        ))}
      </ul>
    </>
  );
};

// componente mostrato per /users/1234
import {
  useNavigate
  useParams,
} from 'react-router-dom';

const User = () => {
  const { userId } = useParams();
  const navigate = useNavigate();

  const goBack = ()=>{
    navigate('/users'); // PROGRAMMATICAMENTE
  }

  return (
    <>
      <h2>User: {userId}</h2>
      <button onClick={goBack}>Back</button>
      <Link to="/users">Back to Users</Link>
    </>
  );
};
```

## Parametri di search

Per i query string (chiamati in React Router "search params") tipo /users?name=robin si usa l'hook:

```javascript
import {
  ...
  useSearchParams,
} from 'react-router-dom';

const Users = ({ users }) => {
  const [searchParams, setSearchParams] = useSearchParams();
  const searchTerm = searchParams.get('name') || '';

  const handleSearch = (event) => {
    const name = event.target.value;

    if (name) {
      setSearchParams({ name: event.target.value });
    } else {
      setSearchParams({});
    }
  };

  return (
    <>
      <h2>Users</h2>
      <input
        type="text"
        value={searchTerm}
        onChange={handleSearch}
      />

      <ul>
        {users
          .filter((user) =>
            user.fullName
              .toLowerCase()
              .includes(searchTerm.toLocaleLowerCase())
          )
          .map((user) => (
            <li key={user.id}>
              <Link to={user.id}>{user.fullName}</Link>
            </li>
          ))}
      </ul>

      <Outlet />
    </>
  );
};
```

# Nested Routes (e parametri)

Dato che a volte non possiamo mettere tutti gli input all'interno del file app è possibile specificare le rotte direttamente dentro il componente. Questo approccio è da utilizzare anche per le nested routes dove all'interno del componente contenitore si dovranno specificare le rotte:

```javascript
// nell' About Component
import { Route, useNavigate, Routes } from "react-router-dom";

// nested routes
import Offers from "./Offers";

export default function About() {
  const navigate = useNavigate();

  return (
    <div className="content">
      <h2>About Us</h2>
      <p>Lorem ipsum dolor, sit amet consectetur adipisicing elit. </p>

      <button onClick={() => navigate("/products")}>See our products</button>

      <Routes>
        <Route path="offers" element={<Offers />} />
      </Routes>
    </div>
  );
}
```

Nell'app component si deve specificare la rotta About con `*`. In tale modo Si potrà renderizzare solo l' About component quando si ha un path `/about` ma anche il componente About e altri componenti interni quando si ha un path ad esempio `/about/offers`.

```javascript
import { BrowserRouter, Link, Route, Routes, Navigate } from "react-router-dom";
import { useState } from "react";

// pages
import Home from "./pages/Home";
import About from "./pages/About";
import Products from "./pages/Products";
import ProductDetails from "./pages/ProductDetails";

function App() {
  const [cartIsEmpty] = useState(false);

  return (
    <div className="App">
      <BrowserRouter>
        {" "}
        I link stanno dentro
        <nav>
          <h1>The Ninja Clothing Company</h1>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
          <Link to="/products">Products</Link>
        </nav>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about/*" element={<About />} />
          <Route path="/products/:id/*" element={<ProductDetails />} />
          <Route path="/products" element={<Products />} />
          <Route
            path="/test"
            element={
              <div>
                <h2>Test page</h2>
              </div>
            }
          />
          <Route path="/redirect" element={<Navigate to="/about" />} />
          <Route
            path="/checkout"
            element={
              cartIsEmpty ? <Navigate to="/products" /> : <p>checkout</p>
            }
          />
        </Routes>
      </BrowserRouter>
    </div>
  );
}

export default App;
```

## Links

- [Official page](https://reactrouter.com/en/v6.3.0)
- [w3schools](https://www.w3schools.com/react/react_router.asp)
- [Sunto 1](https://www.robinwieruch.de/react-router/)
- [Net ninjia](https://github.com/iamshaunjp/React-Router-Version-6)
