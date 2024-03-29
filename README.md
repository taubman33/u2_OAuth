# 
<img src="https://i.imgur.com/y42RtQC.jpg" width="600">

# OAuth Authentication<br>with<br>Express & Passport

## Learning Objectives

| Students will be able to: |
|---|
| Explain the difference between Authentication & Authorization |
| Identify the advantages OAuth provides for users and businesses |
| Explain what happens when a user clicks "Login with [OAuth Provider]" |
| Add OAuth authentication to an Express app using PassportJS |
| Use Middleware & PassportJS to implement authorization |

## Road Map

1. Setup
2. Intro to Authentication
3. Why OAuth?
4. What is OAuth?
5. Today's OAuth Game Plan
    - **Step 1:** Register our App with Google's OAuth Server
    - **Step 2:** Define the `User` model
    - **Step 3:** Discuss PassportJS
    - **Step 4:** Install & Configure Session middleware
    - **Step 5:** Install PassportJS
    - **Step 6:** Create a Passport config module
    - **Step 7:** Install a Passport Strategy for OAuth
    - **Step 8:** Configure Passport
    - **Step 9:** Define routes for authentication
    - **Step 10:** Add Login/Logout UI
6. Implement Authorization




### Why We Need Authentication

An application's functionality typically revolves around a particular user.

For example, when we use online banking, or more importantly, save songs to our Spotify playlists, the application has to know who we are - and this is where **authentication** comes in.

### What is Authentication?

Authentication is what enables an application to know the **identity** of the person using it.

In SEBR, we're going to learn 3 types of **authentication**:

- **Unit 2**: Logging in via a third-party provider (OAuth)
- **Unit 3**: Session-based username/password login
- **Unit 4**: Token-based username/password login

### Authentication vs. Authorization

_Authentication_ and _authorization_ are not the same thing...

**Authentication** verifies a user's identity.

**Authorization** determines what functionality a given user can access. For example:

- Features a logged in (authenticated) user has vs. an anonymous visitor
- Features an _admin_ user has vs. some other user _role_
- Only the user that added a given comment can delete that comment

## 3. Why OAuth?

Consider applications where we have to sign up and log in using a username and a password...

<details>
<summary>
❓ What pitfalls of username/password authentication can you think of from a user's perspective?
</summary>
<hr>

- Creating multiple logins requires you to remember and manage all of those login credentials.

- You will often use the same credentials across multiple sites, so if there's a security breach at one of the sites where you are a member, the hackers know that users often use the same credentials across all of their sites - oh snap!

- You are tempted to use simple/weak passwords so that you can remember all of them.

<hr>
</details>

<details>
<summary>
❓ What might be the pitfalls of username/password authentication from a business that owns the web app point of view?
</summary>
<hr>

- Managing users' credentials requires carefully crafted security code written by highly-paid developers.

- Users (customers) are annoyed by having to create dedicated accounts, especially for entertainment or personal interest type websites.

- Managing credentials makes your business a target for hackers (internal and external) and that brings with it liability.

<hr>
</details>

The bottom-line is that users prefer to use OAuth instead of creating another set of credentials to use your site.

When users are your customers, you want to make them as happy as possible!

OAuth is hot, so let's use it!

## 4. What is OAuth?

### A Bit of OAuth Vocabulary

- **[OAuth 2.0](https://oauth.net/2/)**: The current OAuth standard.  Version 1.0 is obsolete and should not be used.

- **OAuth provider**: A service company such as _Google_ that makes its OAuth authentication service available to third-party applications.

- **client application**: Our web application!  Remember, this is from an _OAuth provider's_ perspective.

- **owner**: A user of a service such as _Facebook_, _Google_, _Dropbox_, etc.

- **resources**: An _owner's_ information on a service that **may** be exposed to _client applications_. For example, a user of Dropbox may allow access to their files.

- **access token**: An temporary key that provides access to an _owner's_ _resources_.

- **scope**: Determines what _resources_ and rights (read-only, update, etc) a particular _token_ has.

### What is it?

OAuth is an open standard that provides **client applications** access to **resources** of a service such as Google with the permission of the resources' **owner**.

There are numerous OAuth Providers including:

- Facebook
- Google
- GitHub
- Twitter
- [Many more...](https://en.wikipedia.org/wiki/List_of_OAuth_providers)

### OAuth 2's Flow & Scope

<img src="https://i.imgur.com/pgb5FN9.png">

The above image, taken from [this excellent article](https://darutk.medium.com/diagrams-and-movies-of-all-the-oauth-2-0-flows-194f3c3ade85), diagrams the flow of logging in using OAuth 2.0.

The ultimate goal is for the _client application_ (our web app) to obtain an **access token** from an OAuth provider that allows the app to access the user's resources from that provider's API's.

OAuth is **token** based.  A token is a generated string of characters. 

Each token has a **scope** that determines what resources an app can access for that user.

If an application needs to access more than a user's basic profile, the **scope** would need to be expanded as dictated by the specific provider's documentation on how to access additional resources.

Yes, OAuth is complex. But not to worry, we don't have to know all of the nitty gritty details in order to take advantage of it because we will be using PassportJS middleware that will handle most of the "OAuth dance" for us.

### ❓ OAuth Review Questions

<details>
<summary>
(1) True or False: If your site allows users to authenticate via OAuth, you should ensure they create a "strong" password.
</summary>
<hr>

**False** - Trick question because users won't be providing a password to our site if we use OAuth 😀

<hr>
</details>

<details>
<summary>
(2) To an OAuth provider, such as Google, what is the <em>client application</em>?
</summary>
<hr>

**Our web application/server**

<hr>
</details>


## Today's OAuth Game Plan

Ready for some OAuth?  Here's today's game plan:

- **Step 1:** Register our App with Google's OAuth Server
- **Step 2:** Define the `User` model
- **Step 3:** Discuss PassportJS
- **Step 4:** Install & Configure Session middleware
- **Step 5:** Install PassportJS
- **Step 6:** Create a Passport config module
- **Step 7:** Install a Passport Strategy for OAuth
- **Step 8:** Configure Passport
- **Step 9:** Define routes for authentication
- **Step 10:** Add Login/Logout UI

Yep, lots of fun ahead...

### Step 1 - Register our App

Every OAuth provider requires that our web app be registered with it.

When we do so, we obtain a **Client ID** and a **Client Secret** that identifies **our application** to the OAuth provider.

For this lesson, we are going to use [Google's OAuth provider](https://developers.google.com/identity/protocols/OAuth2).

Time to register our app...

#### Step 1.1 - Google Developers Console

- You must be logged into the [console for Google's Cloud Platform Console](https://console.developers.google.com).  Here's what you'll see once logged in to the console for the first time and consent to the Terms of Service:

<img src="https://i.imgur.com/OjjoaRx.png">

#### Step 1.2 - Create a Project

- Click **Select a project**:

<img src="https://i.imgur.com/XuqQCLu.png">

- Click **NEW PROJECT**:

<img src="https://i.imgur.com/ylSC8mW.png">

- Update the **Project name** to something like `mongoose-movies`, then click **CREATE**:

<img src="https://i.imgur.com/dgKSFOG.png">

> Note: The project name must be globally unique, so Google will append and additional id to the name you provide.

- It might take a bit to create the project.  When done, click SELECT PROJECT:

<img src="https://i.imgur.com/tPviNAe.png">

#### Step 1.3 - Enable the People API

- So that we can access the user's basic profile, we'll need to enable the People API.

- Click **Go to APIs overview**: 

<img src="https://i.imgur.com/dqFFhh3.png">

- Click **+ ENABLE APIS AND SERVICES**:

<img src="https://i.imgur.com/HO1KJjY.png">

- Search for **people** and click on **Google People API** when it is visible:

<img src="https://i.imgur.com/pUEqE4y.png">

- Click **ENABLE**:

<img src="https://i.imgur.com/ylHcvQK.png">

#### Step 1.4 - Obtain Credentials for App

- Now we need to create credentials for the app. Click **CREATE CREDENTIALS**:

<img src="https://i.imgur.com/nayg9Ve.png">

- Select **User data** then click **NEXT**:

<img src="https://i.imgur.com/FvPfXrY.png">

- Under **OAuth Consent Screen** provide a friendly **App name**, select the **User support email**, **leave App Logo blank**, type in a developer **Email address** then click SAVE AND CONTINUE:

<img src="https://i.imgur.com/pSuDMeV.png">

- Ignore the **Scopes** section by clicking **SAVE AND CONTINUE** (you may have to scroll down a bit):

<img src="https://i.imgur.com/KrZDT91.png">

- In the dropdown, select **Web application**, then click the **+ ADD URI** in the **Authorized redirect URIs**:

<img src="https://i.imgur.com/Va5Ef86.png">

- Copy/paste `http://localhost:3000/oauth2callback` into the **URIs 1** input then click **CREATE**:

<img src="https://i.imgur.com/UfwhOaA.png">


- We now need to change the publishing status so that anyone can log in.  Click the **OAuth consent screen page** link:

<img src="https://i.imgur.com/h9MbE5z.png">

- Click the **PUBLISH APP** button:

<img src="https://i.imgur.com/uI9QDhX.png">

- Click the **CONFIRM**:

<img src="https://i.imgur.com/ztX7wvL.png">

- Awesome. Now let's go view our credentials by clicking **Credentials** in the side menu:

<img src="https://i.imgur.com/Wbu1uKR.png">

- Click the **Web client 1** link:

<img src="https://i.imgur.com/gjK4cEk.png">

#### Step 1.5 - Add the Client ID and Client Secret to `.env`

Strangely, we either have to make our browser window very narrow or very large in order to see the **Client ID** and the **Client secret**:

<img src="https://i.imgur.com/S1RcEu4.png">

Now we can add the credentials, along with that callback URI we provided, to the `.env` file so that it looks something like this:

```
DATABASE_URL=mongodb+srv://<username>:<password>@cluster0-oxnsb.azure.mongodb.net/mongoose-movies?retryWrites=true&w=majority
GOOGLE_CLIENT_ID=245025414218-2r7f4bvh3t88s3shh6hhagrki0f6op8t.apps.googleusercontent.com
GOOGLE_SECRET=Yn9T_2BKzxr5zgprzKDGI5j3
GOOGLE_CALLBACK=http://localhost:3000/oauth2callback
```

> 👀 Registering a project with other OAuth providers will be similar, but unique to that provider.

**Congrats on Registering your App with Google's OAuth Service!**

### Step 2 - Define the `User` Model

Let's start by defining a minimal `User` model:

1. Create the Node module:

    ```
    touch models/user.js
    ```

2. Define the minimal schema, compile the schema into a `User` model and export it:

    ```js
    const mongoose = require('mongoose');
    const Schema = mongoose.Schema;

    const userSchema = new Schema({
      name: String
    }, {
      timestamps: true
    });

    module.exports = mongoose.model('User', userSchema);
    ```

We will add additional properties once we "discover" what user info Google provides us with.

### Step 3 - Passport Discussion

Implementing OAuth is complex. There are redirects going on everywhere, access tokens that only last for a short time, refresh tokens used to obtain a fresh access token, etc.

As usual, we will stand on the shoulders of giants that have done much of the heavy lifting for us - enter [PassportJS](http://www.passportjs.org/).

Passport is by far the most popular authentication framework out there for Express apps.

[Passport's website](http://passportjs.org/) states that it provides _Simple, unobtrusive authentication for Node.js_.

Basically this means that it handles much of the mundane tasks related to authentication for us, but leaves the details up to us, for example, not forcing us to configure our user model a certain way.

There are numerous types of authentication, if Passport itself was designed to do them all, it would be ginormous!

Instead, Passport uses **Strategies** designed to handle a given type of authentication. Think of them as plug-ins for Passport.

Each Express app with Passport can use one or more of these strategies.

[Passport's site](http://passportjs.org/) currently shows over 500 strategies available.

OAuth2, although a standard, can be implemented slightly differently by OAuth providers such as Facebook and Google.

As such, there are strategies available for each flavor of OAuth provider.

For this lesson, we will be using the [passport-google-oauth](https://github.com/jaredhanson/passport-google-oauth) strategy.

Passport is just middleware designed to authenticate requests.

**IMPORTANT INFO BELOW**

When a request is sent from an authenticated user, Passport's middleware will automatically add a `user` property to the `req` object.

**👀 `req.user` will be the logged in user's Mongoose document❗️**

If a user is not logged in, `req.user` will be `undefined`.

You will then be able to access the `req.user` document in all of the controller actions - so, DO NOT write code to retrieve the user document from the DB because `req.user` is already the document!

### Step 4 - Session Middleware

Before we install Passport and a strategy, we need to install the [`express-session`](https://github.com/expressjs/session?_ga=1.40272994.1784656250.1446759094) middleware.

Sessions, are a server-side way of remembering a user's browser session.

Sessions remembers the browser session by setting a cookie that contains a _session id_. No other data is stored in the cookie, just the _id_ of the session.

On the server-side, your application can optionally store data pertaining to the user's session (`req.session`).

Passport will use the session, which is an in-memory data-store by default, to store a nugget of information that will allow us to lookup the user in the database.

FYI, since sessions are maintained in memory by default, if the server restarts, session data will be lost. You will see this happen when nodemon restarts the server and you are no longer logged in.

#### Step 4.1 - Installing Session Middleware

Let's install the module:

```
npm install express-session
```

Next, require it below `methodOverride`:

```js
const methodOverride = require('method-override');
// new code below
const session = require('express-session');
```

#### Step 4.2 - Add a SECRET to `.env`

When session middleware is configured, it will require a "secret" string that it uses to digitally sign the session cookie.  Let's add the secret to the `.env`:

```
...
GOOGLE_CALLBACK=http://localhost:3000/oauth2callback
SECRET=SEIRocks
```

#### Step 4.3 - Configure and Mount Session Middleware

Now, we can configure and mount the session middleware below the `methodOverride` middleware:

```js
app.use(express.static(path.join(__dirname, 'public')));
app.use(methodOverride('_method'));


app.use(session({
  secret: process.env.SECRET,
  resave: false,
  saveUninitialized: true
}));
```

The `resave` and `saveUninitialized` settings, they are being set to suppress deprecation warnings.

#### Step 4.4 - Verifying Session Middleware

Make sure your server is running with nodemon.

Browse to the app at `localhost:3000`.

Open the _Application_ tab in _DevTools_, then expand _Cookies_ in the menu on the left.

A cookie named `connect.sid` confirms that the session middleware is doing its job.

**Congrats, the session middleware is now in place!**

Time for a few questions...

#### ❓ Review Questions (1 min)

<details>
<summary>
(1) Before a web app can use an OAuth provider, it must first r__________ with it to obtain a client ____ and a client secret.

</summary>
<hr>

...it must first **register** with it to obtain a client **ID** and a client secret.

<hr>
</details>

<details>
<summary>
(2) Passport uses s__________ designed to handle specific types of authentication.
</summary>
<hr>

**strategies**

<hr>
</details>

<details>
<summary>
(3) If there is an authenticated user, the request (<code>req</code>) object will have what property attached to it by Passport?
</summary>
<hr>

**`req.user`**

<hr>
</details>

<details>
<summary>
👀 Do you need to sync your code?
</summary>
<hr>

**`git reset --hard origin/sync-16-sessions`**

<hr>
</details>

### Install Passport

The Passport middleware is easy to install, but challenging to configure correctly.

First the easy part:

```
npm i passport
```

Require it as usual below `express-session`:

```js
const session = require('express-session');
// new code below
const passport = require('passport');
```

### Mounting Passport

With Passport required, we need to mount it. Be sure to mount it **after** the session middleware and always **before** any of the routes that would need access to the current user:

```js
// server.js

// app.use(session({... code above

app.use(passport.initialize());
app.use(passport.session());
```
	
The way `passport` middleware is being mounted is straight from the docs.

### Step 6 - Create a Passport Config Module

Because it takes a significant amount of code to configure Passport, we will create a separate module so that we don't pollute **server.js**.

Let's create the file:

```
touch config/passport.js
```

In case you're wondering, although the module is named the same as the `passport` module we've already required, it won't cause a problem because a module's full path uniquely identifies it to Node.

#### Step 6.1 - Passport Module's Exports Code 

Our `config/passport` module is not middleware.

Its code will basically configure Passport and be done with it. We're not going to export anything either.

Requiring below our database is as good of a place as any in **server.js**:

```js
// server.js

require('./config/database');
// new code below
require('./config/passport');
```

#### Step 6.2 - Require Passport 

In the **config/passport.js** module we will certainly need access to the `passport` module:

```js
// config/passport.js

const passport = require('passport');
```

Now on to the _strategy_...

### Step 7 - Install the OAuth Strategy

Time to install the strategy that will implement Google's flavor of OAuth:

```
npm i passport-google-oauth
```

This module implements Google's OAuth 2.0 and 1.0 API. 

Note that _OAuth 1.0_ does still exist here and there, but it's obsolete.

#### Step 7.1 - Require the OAuth Strategy

Now let's require the `passport-google-oauth` module below that of `passport` in **config/passport.js**:

```js
const passport = require('passport');
// new code below
const GoogleStrategy = require('passport-google-oauth').OAuth2Strategy;
```

Note that the variable is named using upper-camel-case.

<details>
<summary>
❓ What does this naming convention typically hint at?
</summary>
<hr>

**The it's a `class`**

<hr>
</details>

Let's make sure there's no errors before moving on to the fun stuff!

### Step 8 - Configuring Passport

To configure Passport we will:

1. Call the `passport.use()` method to plug-in an instance of the OAuth strategy and provide a _verify_ callback function that will be called whenever a user has logged in using OAuth.

2. Call the `passport.serializeUser()` method to provide a callback that Passport will call after the _verify_ callback.

3. Call the `passport.deserializeUser()` method to provide a callback that Passport will call for every request when a user is logged in.

#### Step 8.1 `passport.use()`

Time to call the `passport.use()` method to plug-in an instance of the OAuth strategy and provide a _verify_ callback function that will be called whenever a user logs in with OAuth.

In **config/passport.js**:

```js
const GoogleStrategy = require('passport-google-oauth').OAuth2Strategy;
// new code below
passport.use(new GoogleStrategy(
  // Configuration object
  {
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_SECRET,
    callbackURL: process.env.GOOGLE_CALLBACK
  },
  // The verify callback function
  // Let's use async/await!
  async function(accessToken, refreshToken, profile, cb) {
    // A user has logged in with OAuth...
  }
));
```

Note the settings from the `.env` file being passed to the `GoogleStrategy` constructor function.

Next we have to code the _verify_ callback function...

#### Step 8.2 - The Verify Callback

The _verify_ callback will be called by Passport when a user has logged in with OAuth.

It's called a _verify_ callback because with most other strategies we would have to verify the credentials, but with OAuth, well, there are no credentials!

In this callback we must:

- Fetch the user from the database and provide them back to Passport by calling the `cb()` callback function, or...

- If the user does not exist, we have a new user! We will add them to the database and pass along this new user in the `cb()` callback function.

But wait, how can we tell what user to lookup?

Looking at the callback's signature:

```js
function(accessToken, refreshToken, profile, cb) {
```
	
We can see that we are being provided the user's `profile` by Google.

If we were to inspect this `profile` object, we'd find the following useful properties:

- **`id`**: The user's _Google Id_ that uniquely identifies each Google account.
- **`displayName`**: The user's full name.
- **`emails`**: An array of email objects associated with the account.
- **`photos`**: An array of avatar image objects associated with the account.

Let's add these field to our `User` model's schema to hold it...

#### Step 8.3 - Modify the `User` Model

Let's add a property for `googleId` to our `userSchema` inside `models/user.js` file:

```js
const userSchema = new Schema({
  name: String,
  googleId: {
    type: String,
    required: true
  },
  email: String,
  avatar: String
}, {
  timestamps: true
});
```

Cool, now when we get a new user via OAuth, we can use the Google `profile` object's info to create our new user!

#### Step 8.4 - Verify Callback Code

Now we need to code the callback!

We're going to need access to our `User` model:

```js
const GoogleStrategy = require('passport-google-oauth').OAuth2Strategy;
// new code below
const User = require('../models/user');
```

Cool, here comes the code for the entire `passport.use` method. We'll review it as we type it in...

```js
passport.use(new GoogleStrategy(
  // Configuration object
  {
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_SECRET,
    callbackURL: process.env.GOOGLE_CALLBACK
  },
  // The verify callback function...
  // Marking a function as an async function allows us
  // to consume promises using the await keyword
  async function(accessToken, refreshToken, profile, cb) {
    // When using async/await  we use a
    // try/catch block to handle an error
    try {
      // A user has logged in with OAuth...
      let user = await User.findOne({ googleId: profile.id });
      // Existing user found, so provide it to passport
      if (user) return cb(null, user);
      // We have a new user via OAuth!
      user = await User.create({
        name: profile.displayName,
        googleId: profile.id,
        email: profile.emails[0].value,
        avatar: profile.photos[0].value
      });
      return cb(null, user);
    } catch (err) {
      return cb(err);
    }
  }
));
```

#### Step 8.5 `serializeUser()` & `deserializeUser()` Methods

With the `passport.use()` method done, we now need to code two more methods inside of `config/passport` module.

#### Step 8.6 `serializeUser()` Method

After the verify callback function returns the user document, passport calls the `passport.serializeUser()` method's callback passing that same user document as an argument.

It is the job of that callback function to return the nugget of data that passport is going to add to the **session** used to track the user:

```js
// Add to bottom of config/passport.js
passport.serializeUser(function(user, cb) {
  cb(null, user._id);
});
```

By using the MongoDB `_id` of the user document we can easily retrieve the user doc within the `deserializeUser()` method's callback...

#### Step 8.7 `deserializeUser()` Method

The `passport.deserializeUser()` method's callback function is called every time a request comes in from an existing logged in user.

The callback needs to return what we want passport to assign to the `req.user` object.

Code it below the `passport.serializeUser()` method:

```js
// Add to bottom of config/passport.js
passport.deserializeUser(async function(userId, cb) {
  // It's nice to be able to use await in-line!
  cb(null, await User.findById(userId));
});
```

Let's do another error check.

<details>
<summary>
👀 Do you need to sync your code?
</summary>
<hr>

**`git reset --hard origin/sync-17-passport`**

<hr>
</details>

### Step 9 - Define Routes for Authentication

We're going to need three auth related routes:

1. A route to handle the request sent when the user clicks Login with Google

2. The `/oauth2callback` route that we told Google to call after the user confirms or denies the OAuth login.

3. Lastly, we will need a route for the user to logout.

#### Coding the Routes...

We're going to code these three new auth related routes in our server.js file

These new routes will need to access the `passport` module, so let's require it in **server.js**:

```js
// new code below
const passport = require('passport');
```

#### Step 9.2 Login Route

In **server.js**, let's add the login route below the root route:

```js
// Google OAuth login route
app.get('/auth/google', passport.authenticate(
  // Which passport strategy is being used?
  'google',
  {
    // Requesting the user's profile and email
    scope: ['profile', 'email'],
    // Optionally force pick account every time
    // prompt: "select_account"
  }
));
``` 

The `passport.authenticate()` method will return a middleware function that does the coordinating with Google's OAuth server.

The user will be presented the consent screen if they haven't previously consented.

#### Step 9.3 Google Callback Route

Below the login route we just added, let's add the "callback" route that Google will call after the user confirms:

```js
// Google OAuth callback route
app.get('/oauth2callback', passport.authenticate(
  'google',
  {
    successRedirect: '/movies',
    failureRedirect: '/movies'
  }
));
```

Note that we can specify the redirects for a successful and unsuccessful login. For this app, we will redirect to our main `/movies` route in both cases.

#### Step 9.4 Logout Route

The last route to add is the route that will logout the user:

```js
// OAuth logout route
app.get('/logout', function(req, res){
  req.logout(function() {
    res.redirect('/movies');
  });
});
```
	
The `logout()` method was automatically added to the `req` object by Passport!

Good time to do another error check.


```js
app.use(passport.session());

// Add this middleware BELOW passport middleware
app.use(function (req, res, next) {
  res.locals.user = req.user;
  next();
});
```

Now the logged in user is in a `user` variable that's available inside all EJS templates!

If nobody is logged in, `user` will be `undefined`.


#### Try Logging In/Out!

We've finally got to the point where you can test out our app's authentication!

May the force be with us!


## Attaching to existing code

Lets say we want to add a User as a Parent to any Child Model. We do so by embedding the User in like this:


```js
const reviewSchema = new Schema({
  content: {
    type: String,
    required: true
  },
  rating: {
    type: Number,
    min: 1,
    max: 5,
    default: 5
  },
  // Don't forget to add the comma above then
  // add the 3 new properties below
  user: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  userName: String,
  userAvatar: String
}, {
  timestamps: true
});
```

> Note that `required: true` has been added to prevent "orphan" reviews. You'll want to do the same on other user-centric schemas as well.

### Creating the `create` action in a Reviews controller

Much of an application's CRUD data operations pertain to the logged in user.

The [Guide to User-Centric CRUD using Express & Mongoose](https://gist.github.com/jim-clark/a714016bab26fad52106f6b2490e3eb7) will prove very helpful when coding your projects.


## Implement Authorization

<details>
<summary>
❓ What is <em>authorization</em>?
</summary>
<hr>

**Authorization** determines what functionality a given user can access.  For example, we don't want unauthenticated users to be able to delete reviews. 

<hr>
</details>

We've already coded some client-side authorization by:

- Conditionally displaying the `<form>` used to add reviews.
- Showing the delete button for only the reviews created by the logged in user.

We also performed a bit of server-side authorization by ensuring that the logged in user was the owner of the review being deleted.

In this step, we will see how we can easily protect the routes that require a user to be logged in, e.g., adding a movie or review.

Passport adds a nice method to the `req` object, `req.isAuthenticated()` that returns `true` or `false` depending upon whether there's a logged in user or not.

We're going to write our own little middleware function to take advantage of `req.isAuthenticated()` to perform some authorization.

### Code the Authorization Middleware

Since we want to protect routes defined in multiple modules, we'll want to stay DRY and code the middleware function in its own module.

```
touch config/ensureLoggedIn.js
```

We only want to export a single thing, a middleware function.  Thus, the best approach is to **assign** the function to `module.exports` as follows:

```js
// ensureLoggedIn.js

// Middleware for routes that require a logged in user
module.exports = function(req, res, next) {
  // Pass the req/res to the next middleware/route handler
  if ( req.isAuthenticated() ) return next();
  // Redirect to login if the user is not already logged in
  res.redirect('/auth/google');
}
```

Our custom `ensureLoggedIn` middleware function, like all middleware, will either call `next()`, or respond to the request.

### Using Authorization Middleware

Express's middleware and routing is extremely flexible and powerful.

We can add multiple middleware functions before a route's final middleware function!

Let's modify **server.js** to protect routes that require a logged in user using our new ensureLoggedIn middleware:

```js
// This works with a MoviesDB, with an attached controller you can create

const moviesController = require('../controllers/movies');
// Require the auth middleware
const ensureLoggedIn = require('../config/ensureLoggedIn');

app.get('/', moviesController.getMovies);
// Use ensureLoggedIn middleware to protect routes
app.get('/movieId', moviesController.getMovieById);
app.put('/:movieId', ensureLoggedIn, moviesController.updateMovie);
app.post('/', ensureLoggedIn, moviesController.createMovie);
app.delete('/:movieId', ensureLoggedIn, moviesController.deleteMovie);
```

Note the inserted `ensureLoggedIn` middleware function!

If the `ensureLoggedIn` middleware calls `next()`, then the next middleware, e.g., `moviesCtrl.new` will be called.

#### 👍 Congrats on implementing OAuth authentication and authorization!

Now you're ready to start your project and implement OAuth authentication before any other CRUD functionality.


## References

- [Google OAuth2](https://developers.google.com/identity/protocols/OAuth2)

- [Guide to User-Centric CRUD using Express & Mongoose](https://gist.github.com/jim-clark/a714016bab26fad52106f6b2490e3eb7)

- [Mongoose](http://mongoosejs.com/)
