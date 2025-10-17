

# Express

### Basic server

```js
const express = require("express");
const app = express();
const PORT = 3000;

app.get("/", (req, res) => {
  res.send("Hello, Express!");
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```


### Middleware

```js
// Built-in
app.use(express.json());            // parse JSON body
app.use(express.urlencoded({ extended: true })); // form data

// Custom
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});
```

### Routing

```js
// Basic routes
app.get("/users", (req, res) => res.send("GET users"));
app.post("/users", (req, res) => res.send("POST user"));

// URL Params
app.get("/users/:id", (req, res) => {
  res.send(`User ID: ${req.params.id}`);
});

// Query Params
app.get("/search", (req, res) => {
  res.send(`Search term: ${req.query.q}`);
});
```


### Router (Modular Routes)

```js
// routes/ folder
const express = require("express");
const router = express.Router();

router.get("/", (req, res) => res.send("All users"));
router.post("/", (req, res) => res.send("Create user"));

module.exports = router;

// In main app:
const userRoutes = require("./routes/users");
app.use("/users", userRoutes);
```

### Serving Static Files

```js
app.use(express.static("public"));
// Access: http://localhost:3000/style.css
```

### Request & Response Helpers

```js
res.send("Hello");                 // Send text/HTML
res.json({ success: true });       // Send JSON
res.status(201).send("Created");   // Status code + response
res.redirect("/login");            // Redirect
```

### Error Handling

```js
// 404 handler
app.use((req, res) => {
  res.status(404).send("Not Found");
});

// Error handler
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send("Something broke!");
});
```













