## React with Cookies

In case you don't want to use passport but plain cookies, you've to take a couple of things into consideration when working with React. Setting and reading cookies accross origins (cors) works a tad different. You need to make 2 small adjustments. 1 when you **set** the cookie on the backend (Express) and 1 when you **send** the cookie from the client-side (React).

### Setting Cookies from Express
Do you remember the following code?
```
var cors = require('cors')
app.use(cors())
```
With this code we are telling the express app to accept all requests from **all** origins. If we want to deal with cookies as well, the security requirements are stricter. It's often in cookies that the most sensitive data is kept, like credentials (session id's). That's why the CORS policy demands us to be more specific about the origins we accept:
```
var cors = require('cors')
app.use(cors({
  origin: 'http://localhost:3000',
  credentials: true
}))
```
Now we are telling the app we only accept requests from **1** other origin, namely `http://localhost:3000`. Note that we've also set credentials to true. Another name could have been "cookies: true", but they decided to make this confusing instead. Now you can set cookies with `res.cookie(....)` like you're used to.

### Sending Cookies on the Client Side (React)
If a cookie is set, the browser normally sends the cookie on every request back to the server. If they have different origins, you've to explicitly tell the browser to still send the cookie. You do this by adding 1 extra configuration:
```
axios("http://localhost:3001/countries/a", {withCredentials: true})
 .then((result)=> {
     // do something with the result
 })
```
`{withCredentials: true}` is the extra configuration. You have to do this at least for every API request that requires authentication, like a profile page.

That's it!

### Note
The reason you've to set CORS is not really caused by React. It's caused by splitting up the application on a development server and a backend server. If you would host the React code and the API on the same origin (same domain and port), you wouldn't need to set CORS.