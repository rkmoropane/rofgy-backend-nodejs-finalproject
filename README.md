# coding-project-template
# Final Project: Social Media Dashboard

![Cognitive Class AI Logo](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-CC0100EN-SkillsNetwork/images/IDSN-logo.png)

## Project Information

- **Estimated time:** 3 hours
- **Backend:** Node.js and Express.js
- **Database:** MongoDB
- **Frontend:** HTML, CSS, and JavaScript
- **Authentication:** JWT and Express sessions
- **Deployment:** Docker and Docker Compose

---

## Overview

In this final project, you will build and deploy a Social Media Dashboard using Node.js, Express.js, MongoDB, JWT authentication, sessions, and Docker.

The application allows users to:

- Register an account.
- Log in and log out.
- Create posts.
- View their posts.
- Edit their posts.
- Delete their posts.
- Navigate through posts using pagination.

---

## Learning Objectives

After completing this project, you will be able to:

1. Create Users and Posts collections in MongoDB.
2. Implement JWT-based authentication.
3. Create authentication middleware.
4. Build API endpoints for users and posts.
5. Develop a frontend social media dashboard.
6. Deploy the application using Docker.
7. Test the application with cURL.
8. Save the required files and command outputs for evaluation.

---

## Important Lab Notice

The Skills Network Cloud IDE environment is temporary. Files and directories may be deleted when you log out.

Before ending your session:

- Save all cURL output.
- Download the completed app.js file.
- Save important project files outside the temporary environment.
- Keep a copy of your MongoDB connection details.
- Record any changes made to the starter project.

---

# Exercise 1: Start the MongoDB Server

## Step 1: Open the Skills Network Toolbox

Open the Skills Network Toolbox from the Cloud IDE.

![Skills Network Toolbox](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-DB0151EN-edX/images/skills-network-toolbox.png)

---

## Step 2: Start MongoDB

1. Locate MongoDB under the Databases section.
2. Click MongoDB.
3. Click the Start button.
4. Wait for the MongoDB service to become active.

![Inactive MongoDB](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-DB0151EN-edX/images/inactive-mongodb.png)

![Start MongoDB](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/0H2R2Gu5FuNmdtFHkraT8Q/mongo-inactive-new.png)

![Starting MongoDB](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/pBxGMvTsMnWPFbK-RkDcFA/starting-mongodb-new.png)

---

## Step 3: Save the MongoDB Connection Details

After MongoDB starts, open the Connection Information section and save:

- MONGO_HOST
- MONGO_PORT
- MONGO_PASSWORD

MongoDB authentication is enabled in this exercise.

![MongoDB Connection Information](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/Rk87wS_X0v9Mqw3x2DFS0w/mongo-connection-info.png)

The host and password may change when a new lab session is started.

---

# Exercise 2: Clone and Configure the Starter Project

## Step 1: Clone the Repository

Clone the starter repository:

~~~bash
"git clone https://github.com/ibm-developer-skills-network/rofgy-backend-nodejs-finalproject.git"
~~~

---

## Step 2: Enter the Project Directory

~~~bash
"cd rofgy-backend-nodejs-finalproject"
~~~

---

## Step 3: Install Dependencies

~~~bash
"npm install"
~~~

This installs packages such as:

- Express.js.
- Mongoose.
- JSON Web Token.
- Express Session.
- Body Parser.
- dotenv.

---

## Step 4: Configure MongoDB

Open app.js and replace the MongoDB connection settings with the host, port, and password provided by the MongoDB service.

Use the authenticated MongoDB connection details from Exercise 1.

Do not commit passwords or credentials to Git.

Example environment variables:

~~~env
"MONGO_HOST=your-mongodb-host"

"MONGO_PORT=your-mongodb-port"

"MONGO_PASSWORD=your-mongodb-password"

"SECRET_KEY=your-secret-key"

"PORT=3000"
~~~

---

## Step 5: Configure the MongoDB URI

The MongoDB connection string must include the correct host, port, credentials, and authentication database.

A typical authenticated connection has the following structure:

~~~javascript
"const uri = `mongodb://admin:${MONGO_PASSWORD}@${MONGO_HOST}:${MONGO_PORT}/?authSource=admin`;"
~~~

Use the exact format required by the MongoDB connection information provided by the lab environment.

---

# Exercise 3: Implement Authentication and API Endpoints

## Authentication Overview

The application uses:

- JWT tokens for authentication.
- Express sessions for storing JWT tokens.
- Middleware to protect routes.
- MongoDB for storing users and posts.

---

## Step 1: Add the authenticateJWT Middleware

Add this function to app.js:

~~~javascript
"function authenticateJWT(req, res, next) {"

"  const token = req.session.token;"

""

"  if (!token) {"

"    return res.status(401).json({ message: 'Unauthorized' });"

"  }"

""

"  try {"

"    const decoded = jwt.verify(token, SECRET_KEY);"

"    req.user = decoded;"

"    next();"

"  } catch (error) {"

"    return res.status(401).json({ message: 'Invalid token' });"

"  }"

"}"
~~~

### Middleware Behavior

- Retrieves the JWT from the session.
- Returns a 401 response if no token exists.
- Verifies the token with SECRET_KEY.
- Attaches decoded user information to req.user.
- Passes the request to the next middleware if authentication succeeds.
- Returns a 401 response when the token is invalid.

---

## Step 2: Add the requireAuth Middleware

Add this function to protect browser pages:

~~~javascript
"function requireAuth(req, res, next) {"

"  const token = req.session.token;"

""

"  if (!token) {"

"    return res.redirect('/login');"

"  }"

""

"  try {"

"    const decoded = jwt.verify(token, SECRET_KEY);"

"    req.user = decoded;"

"    next();"

"  } catch (error) {"

"    return res.redirect('/login');"

"  }"

"}"
~~~

This middleware redirects unauthenticated users to the login page.

---

## Step 3: Add HTML Routes

Add routes for the frontend files:

~~~javascript
"app.get('/', (req, res) => {"

"  res.sendFile(path.join(__dirname, 'public', 'index.html'));"

"});"

""

"app.get('/register', (req, res) => {"

"  res.sendFile(path.join(__dirname, 'public', 'register.html'));"

"});"

""

"app.get('/login', (req, res) => {"

"  res.sendFile(path.join(__dirname, 'public', 'login.html'));"

"});"

""

"app.get('/post', requireAuth, (req, res) => {"

"  res.sendFile(path.join(__dirname, 'public', 'post.html'));"

"});"

""

"app.get('/index', requireAuth, (req, res) => {"

"  res.sendFile(path.join(__dirname, 'public', 'index.html'));"

"});"
~~~

### HTML Route Summary

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Displays index.html |
| GET | `/register` | Displays register.html |
| GET | `/login` | Displays login.html |
| GET | `/post` | Displays post.html for authenticated users |
| GET | `/index` | Displays the authenticated dashboard |

---

## Step 4: Implement User Registration

Add the registration endpoint:

~~~javascript
"app.post('/register', async (req, res) => {"

"  const { username, email, password } = req.body;"

""

"  try {"

"    const existingUser = await User.findOne({"

"      $or: [{ username }, { email }]"

"    });"

""

"    if (existingUser) {"

"      return res.status(400).json({"

"        message: 'User already exists'"

"      });"

"    }"

""

"    const newUser = new User({"

"      username,"

"      email,"

"      password"

"    });"

""

"    await newUser.save();"

""

"    const token = jwt.sign("

"      { userId: newUser._id, username: newUser.username },"

"      SECRET_KEY,"

"      { expiresIn: '1h' }"

"    );"

""

"    req.session.token = token;"

""

"    res.redirect(`/index?username=${newUser.username}`);"

"  } catch (error) {"

"    console.error(error);"

"    res.status(500).json({"

"      message: 'Internal Server Error'"

"    });"

"  }"

"});"
~~~

### Registration Behavior

The endpoint:

1. Receives username, email, and password.
2. Checks whether the username or email already exists.
3. Creates a new user.
4. Saves the user to MongoDB.
5. Generates a JWT token.
6. Stores the token in the session.
7. Redirects the user to the dashboard.

---

## Step 5: Implement User Login

Add the login endpoint:

~~~javascript
"app.post('/login', async (req, res) => {"

"  const { username, password } = req.body;"

""

"  try {"

"    const user = await User.findOne({ username, password });"

""

"    if (!user) {"

"      return res.status(401).json({"

"        message: 'Invalid credentials'"

"      });"

"    }"

""

"    const token = jwt.sign("

"      { userId: user._id, username: user.username },"

"      SECRET_KEY,"

"      { expiresIn: '1h' }"

"    );"

""

"    req.session.token = token;"

""

"    res.redirect(`/index?username=${user.username}`);"

"  } catch (error) {"

"    console.error(error);"

"    res.status(500).json({"

"      message: 'Internal Server Error'"

"    });"

"  }"

"});"
~~~

### Login Behavior

The endpoint:

1. Receives the username and password.
2. Searches for a matching user.
3. Returns an error if the credentials are invalid.
4. Generates a JWT token when login succeeds.
5. Stores the token in the session.
6. Redirects the user to the dashboard.

> **Security note:** For a production application, passwords should be hashed with a package such as bcrypt instead of being stored as plain text.

---

## Step 6: Create a Post

Add the post-creation endpoint:

~~~javascript
"app.post('/post', authenticateJWT, async (req, res) => {"

"  const { text } = req.body;"

""

"  if (!text || typeof text !== 'string') {"

"    return res.status(400).json({"

"      message: 'Please provide valid post content'"

"    });"

"  }"

""

"  try {"

"    const newPost = new Post({"

"      userId: req.user.userId,"

"      text"

"    });"

""

"    await newPost.save();"

""

"    res.status(201).json({"

"      message: 'Post created successfully',"

"      post: newPost"

"    });"

"  } catch (error) {"

"    console.error(error);"

"    res.status(500).json({"

"      message: 'Internal Server Error'"

"    });"

"  }"

"});"
~~~

The route requires authentication and associates the post with the authenticated user's ID.

---

## Step 7: Retrieve Posts

Add the endpoint for retrieving the logged-in user's posts:

~~~javascript
"app.get('/posts', authenticateJWT, async (req, res) => {"

"  try {"

"    const posts = await Post.find({"

"      userId: req.user.userId"

"    });"

""

"    res.json({ posts });"

"  } catch (error) {"

"    console.error(error);"

"    res.status(500).json({"

"      message: 'Internal Server Error'"

"    });"

"  }"

"});"
~~~

This endpoint only returns posts belonging to the authenticated user.

---

## Step 8: Update a Post

Add the update endpoint:

~~~javascript
"app.put('/posts/:postId', authenticateJWT, async (req, res) => {"

"  const { postId } = req.params;"

"  const { text } = req.body;"

""

"  try {"

"    const post = await Post.findOneAndUpdate("

"      { _id: postId, userId: req.user.userId },"

"      { text },"

"      { new: true }"

"    );"

""

"    if (!post) {"

"      return res.status(404).json({"

"        message: 'Post not found'"

"      });"

"    }"

""

"    res.json({"

"      message: 'Post updated successfully',"

"      updatedPost: post"

"    });"

"  } catch (error) {"

"    console.error(error);"

"    res.status(500).json({"

"      message: 'Internal Server Error'"

"    });"

"  }"

"});"
~~~

The query checks both the post ID and the authenticated user's ID, preventing users from editing other users' posts.

---

## Step 9: Delete a Post

Add the deletion endpoint:

~~~javascript
"app.delete('/posts/:postId', authenticateJWT, async (req, res) => {"

"  const { postId } = req.params;"

""

"  try {"

"    const post = await Post.findOneAndDelete({"

"      _id: postId,"

"      userId: req.user.userId"

"    });"

""

"    if (!post) {"

"      return res.status(404).json({"

"        message: 'Post not found'"

"      });"

"    }"

""

"    res.json({"

"      message: 'Post deleted successfully',"

"      deletedPost: post"

"    });"

"  } catch (error) {"

"    console.error(error);"

"    res.status(500).json({"

"      message: 'Internal Server Error'"

"    });"

"  }"

"});"
~~~

The route deletes only posts owned by the authenticated user.

---

## Step 10: Implement Logout

Add the logout endpoint:

~~~javascript
"app.get('/logout', (req, res) => {"

"  req.session.destroy(error => {"

"    if (error) {"

"      console.error(error);"

"    }"

""

"    res.redirect('/login');"

"  });"

"});"
~~~

Logout destroys the session and redirects the user to the login page.

---

# Exercise 4: Build the Frontend

The frontend files are stored in the public directory.

Expected files:

"public/"

"├── index.html"

"├── login.html"

"├── register.html"

"└── post.html"

---

## index.html

The dashboard page should include:

- Register button.
- Login button.
- Logout button.
- Welcome message.
- Social media application names.
- Create Post button.

The page uses JavaScript to:

- Read the username from the URL.
- Display the correct authentication buttons.
- Show the welcome message.
- Redirect authenticated users to `/post`.
- Redirect users to `/logout`.

---

## login.html

The login page should include:

- Username input.
- Password input.
- Login button.
- Link to the registration page.

The form submits a POST request to:

"POST /login"

---

## register.html

The registration page should include:

- Username input.
- Email input.
- Password input.
- Register button.
- Link to the login page.

The form submits a POST request to:

"POST /register"

---

## post.html

The post page should include:

- Text area for writing posts.
- Create Post button.
- List of posts.
- Edit buttons.
- Delete buttons.
- Pagination buttons.
- Logout button.

The frontend communicates with the backend using:

| Operation | Method | Endpoint |
|---|---|---|
| Create post | POST | `/post` |
| Retrieve posts | GET | `/posts` |
| Update post | PUT | `/posts/:postId` |
| Delete post | DELETE | `/posts/:postId` |
| Logout | GET | `/logout` |

---

## Frontend Pagination

The frontend displays three posts per page.

The pagination logic:

1. Stores the current page.
2. Calculates the starting index.
3. Displays three posts.
4. Creates page buttons.
5. Re-renders the posts when a page button is selected.
6. Displays the fourth post on the second page.

---

# Exercise 5: Test the API with cURL

Start the server before testing:

~~~bash
"node app.js"
~~~

Keep a copy of every cURL response for the final submission.

---

## Register a New User

~~~bash
"curl -X POST -H \"Content-Type: application/json\" -d '{"

"  \"username\": \"coopercoote\","

"  \"email\": \"ccoote@gmail.com\","

"  \"password\": \"cg123\""

"}' -k http://localhost:3000/register"
~~~

Save the terminal output.

---

## Register an Existing User

Run the registration command again with the same details.

Expected response:

~~~json
"{"

"  \"message\": \"User already exists\""

"}"
~~~

Save the terminal output.

---

## Login with Valid Credentials

~~~bash
"curl -X POST -H \"Content-Type: application/json\" -d '{"

"  \"username\": \"coopercoote\","

"  \"password\": \"cg123\""

"}' -k http://localhost:3000/login"
~~~

Save the terminal output.

---

## Login with an Incorrect Password

Run the login request again using an incorrect password.

Expected response:

~~~json
"{"

"  \"message\": \"Invalid credentials\""

"}"
~~~

Save the terminal output.

---

## Important cURL Note

The application stores JWT tokens inside an Express session. For testing protected endpoints with cURL, preserve cookies between requests.

Save login cookies:

~~~bash
"curl -c cookies.txt -X POST -H \"Content-Type: application/json\" -d '{"

"  \"username\": \"coopercoote\","

"  \"password\": \"cg123\""

"}' -k http://localhost:3000/login"
~~~

Use the saved session cookie:

~~~bash
"curl -b cookies.txt http://localhost:3000/posts"
~~~

Stop the server after testing:

~~~text
"Ctrl+C"
~~~

---

# Exercise 6: Deploy the Application with Docker

## Step 1: Update the MongoDB Connection

For the Docker Compose deployment, the MongoDB service is named `mongodb`.

Replace the authenticated lab connection with:

~~~javascript
"const uri = 'mongodb://mongodb:27017';"

"mongoose.connect(uri, { dbName: 'SocialDB' });"
~~~

Inside Docker, use:

- Host: `mongodb`
- Port: `27017`

Do not use `localhost` for the MongoDB host inside the Node.js container.

---

## Step 2: Build the Docker Image

Build the application image:

~~~bash
"docker build . -t socialapp"
~~~

---

## Step 3: Start the Docker Services

The Docker Compose file starts:

- MongoDB.
- The Node.js application.

Run:

~~~bash
"docker-compose up"
~~~

If using the newer Docker CLI, use:

~~~bash
"docker compose up"
~~~

To rebuild the image before starting:

~~~bash
"docker compose up --build"
~~~

---

## Step 4: Launch the Dashboard

Open the application using the Skills Network launch option.

Verify that:

- The dashboard loads.
- The registration page opens.
- The login page opens.
- The post page requires authentication.
- MongoDB is connected.
- The application is running on port 3000.

---

## Step 5: Save app.js

Download the completed app.js file.

This file is required for the final project submission and evaluation.

---

# Exercise 7: Test the Social Media Dashboard

## Step 1: Open the Dashboard

Launch the application and confirm that the main dashboard is displayed.

![Dashboard](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social4a.png)

---

## Step 2: Test Protected Post Access

Append `/post` to the application URL.

Example:

~~~text
"http://localhost:3000/post"
~~~

If the user is not authenticated, the application should redirect to `/login`.

![Login Redirect](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social5.png)

---

## Step 3: Register a User

Click Register and provide:

- Username.
- Email.
- Password.

![Registration](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social6.png)

---

## Step 4: Verify the Welcome Message

After registration, confirm that the dashboard displays the username.

![Welcome Message](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social7.png)

---

## Step 5: Verify the Create Post Button

Confirm that the Create Post button is visible after registration.

![Create Post Button](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social8.png)

---

## Step 6: Create a Post

Create a post and verify that the content appears in the post list.

![Create Post](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social13.png)

---

## Step 7: Edit a Post

Click Edit, change the post text, and confirm that the updated content is displayed.

![Edit Post](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social9.png)

---

## Step 8: Delete a Post

Click Delete and confirm that the post is removed from the list.

![Delete Post](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social10.png)

---

## Step 9: Test Pagination

Create at least four posts.

Confirm that:

- Three posts appear on the first page.
- The fourth post appears on a new page.
- Pagination buttons work correctly.

![Pagination](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social11.png)

---

## Step 10: Test Logout

Click Logout and confirm that:

- The session is destroyed.
- The user is redirected to `/login`.
- Protected pages are no longer accessible.

![Logout](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-JS0211EN-SkillsNetwork/labs/v1/m5/social12.png)

---

# API Endpoint Summary

| Method | Endpoint | Authentication | Description |
|---|---|---:|---|
| GET | `/` | No | Displays the dashboard |
| GET | `/register` | No | Displays registration page |
| POST | `/register` | No | Registers a new user |
| GET | `/login` | No | Displays login page |
| POST | `/login` | No | Authenticates a user |
| GET | `/post` | Yes | Displays the post page |
| POST | `/post` | Yes | Creates a post |
| GET | `/posts` | Yes | Retrieves the user's posts |
| PUT | `/posts/:postId` | Yes | Updates a post |
| DELETE | `/posts/:postId` | Yes | Deletes a post |
| GET | `/logout` | Yes | Destroys the session |

---

# Troubleshooting

## MongoDB Connection Fails

Check that:

- MongoDB is running.
- The host and port are correct.
- The MongoDB password is correct in Exercise 1.
- The Docker hostname is `mongodb` in Exercise 5.
- The database name is correct.
- The application is not using `localhost` inside Docker.

---

## Protected Routes Redirect to Login

Check that:

- The user has logged in.
- The session cookie is enabled.
- The JWT is stored in `req.session.token`.
- `SECRET_KEY` is defined.
- The request includes the session cookie.
- The token has not expired.

---

## Posts Are Not Displayed

Check that:

- The user is authenticated.
- The `/posts` request is successful.
- The post belongs to the logged-in user.
- MongoDB contains the Posts collection.
- The browser console has no JavaScript errors.

---

## Docker Application Cannot Connect to MongoDB

Inside Docker, use:

~~~javascript
"mongodb://mongodb:27017"
~~~

Do not use:

~~~javascript
"mongodb://localhost:27017"
~~~

The hostname `mongodb` comes from the Docker Compose service name.

---

# Final Submission Checklist

Before submitting the project, confirm that:

- [ ] MongoDB was started.
- [ ] MONGO_HOST was saved.
- [ ] MONGO_PORT was saved.
- [ ] MONGO_PASSWORD was saved.
- [ ] The repository was cloned.
- [ ] npm install was completed.
- [ ] JWT middleware was added.
- [ ] Authentication middleware was added.
- [ ] Registration works.
- [ ] Login works.
- [ ] Logout works.
- [ ] Users are saved in MongoDB.
- [ ] Posts are created.
- [ ] Posts are retrieved.
- [ ] Posts can be edited.
- [ ] Posts can be deleted.
- [ ] Pagination works.
- [ ] The frontend pages are complete.
- [ ] The Docker image was built.
- [ ] Docker Compose starts both services.
- [ ] The dashboard works in Docker.
- [ ] The completed app.js file was downloaded.
- [ ] The cURL output for new-user registration was saved.
- [ ] The cURL output for duplicate-user registration was saved.
- [ ] The cURL output for valid login was saved.
- [ ] The cURL output for invalid login was saved.

---

# Required Evaluation Answers

Use the completed app.js file to answer the following questions:

1. Copy the output from registering a new user.
2. Copy the output from registering an existing user.
3. Copy the output from logging in with valid credentials.
4. Copy the output from logging in with an incorrect password.
5. Copy the user registration code after replacing the JSON response with `res.redirect`.
6. Copy the user login code after replacing the JSON response with `res.redirect`.
7. Copy the line from `authenticateJWT` that validates the token and handles failure.
8. Copy the JWT expiration line from the registration endpoint.
9. Copy the line that validates post content as a non-empty string.
10. Copy the line that destroys the session during logout.

Important code lines include:

~~~javascript
"const decoded = jwt.verify(token, SECRET_KEY);"
~~~

~~~javascript
"{ expiresIn: '1h' }"
~~~

~~~javascript
"if (!text || typeof text !== 'string')"
~~~

~~~javascript
"req.session.destroy(error => {"
~~~

---

# Conclusion

This project demonstrates how to build a complete Social Media Dashboard using Node.js, Express.js, MongoDB, JWT authentication, sessions, and Docker.

The completed application supports:

- User registration.
- User login.
- JWT-based authentication.
- Protected routes.
- Session-based logout.
- Post creation.
- Post retrieval.
- Post editing.
- Post deletion.
- Pagination.
- Docker deployment.

---

## Authors

Pallavi Rai

## Other Contributors

[**Nikesh Kumar**](https://www.linkedin.com/in/raonikeshnk/)

Vandana Pandey

---

© IBM Corporation. All rights reserved.
