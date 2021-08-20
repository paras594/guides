# Deployment Guide



## Deploying Nodejs Mongodb on heroku 



**Setup Database on mongodb**

1. visit mongodb atlas, create account, and create cluster

2. create a database user (paras594, mongodbparas101)

3. get the connection URI and add it to project

   ```js
   `mongodb+srv://${MONGO_USERNAME}:${MONGO_PASSWORD}@cluster0.zaceq.mongodb.net/${dbName}?retryWrites=true&w=majority`
   ```

**Deploying the api on heroku**

1. create a start script in package.json -> `node server.js`

2. add the following info in package.json

   ```
   "engines": {
       "node": ">=10.0.0",
       "npm": ">=6.0.0"
   },
   ```

   

3. login heroku -> `heroku login`

4. inside the project, create heroku project -> `heroku create project-name`

5. create a Procfile

   ```
   web: node server.js
   ```

6. commit all changes and push code to github 

7. after pushing to github push changes to heroku -> `git push heroku master`

8. login to heroku dashboard in browser and go to your project. inside it add config vars and add all your environment variables

9. to test the app you can open app from dashboard or run `heroku open` inside project