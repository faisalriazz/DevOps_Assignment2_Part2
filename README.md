# 1. Part 2: Multi-Container Application Deployment using Docker Compose
## 1.1. Tasks

### 1.1.1. Create a Docker file for the web application container which should include the necessary dependencies to run the web application. 
sample node application docker file 
```docker
# node version 16.17.1
FROM node:16.17.1-alpine3.15 
# Create and set the working directory
WORKDIR /home/app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# install app dependecies
RUN npm install
# copy all contents from the app folder to working directory
COPY .  . 
# EXPOSE the port the app run
EXPOSE 3000
# run the node.js application
CMD ["node","server.js"]
```
building an image from docker file

**docker build . -t faisalriazz/my-app-node:1.0**
```
 
#0 building with "default" instance using docker driver

#1 [internal] load .dockerignore
#1 transferring context: 2B done
#1 DONE 0.0s

#2 [internal] load build definition from Dockerfile
#2 transferring dockerfile: 513B 0.0s done
#2 DONE 0.1s

#3 [internal] load metadata for docker.io/library/node:16.17.1-alpine3.15
#3 DONE 0.9s

#4 [1/5] FROM docker.io/library/node:16.17.1-alpine3.15@sha256:84c606bfbe4cf120c52b17c3b727e614d99e357a8e350f83a4e7b9ec0798e04a
#4 DONE 0.0s

#5 [2/5] WORKDIR /home/app
#5 CACHED

#6 [internal] load build context
#6 transferring context: 752B 0.0s done
#6 DONE 0.0s

#7 [3/5] COPY package*.json ./
#7 DONE 0.1s

#8 [4/5] RUN npm install
#8 4.024 npm WARN deprecated bson@1.1.1: Fixed a critical issue with BSON serialization documented in CVE-2019-2391, see https://bit.ly/2KcpXdo for more details
#8 5.156
#8 5.156 added 58 packages, and audited 59 packages in 4s
#8 5.202
#8 5.202 5 vulnerabilities (1 moderate, 3 high, 1 critical)
#8 5.202
#8 5.202 To address all issues, run:
#8 5.202   npm audit fix
#8 5.202
#8 5.202 Run `npm audit` for details.
#8 5.204 npm notice
#8 5.204 npm notice New major version of npm available! 8.15.0 -> 9.8.1
#8 5.205 npm notice Changelog: <https://github.com/npm/cli/releases/tag/v9.8.1>
#8 5.205 npm notice Run `npm install -g npm@9.8.1` to update!
#8 5.205 npm notice
#8 DONE 6.5s

#9 [5/5] COPY .  .
#9 DONE 0.1s

#10 exporting to image
#10 exporting layers
#10 exporting layers 0.3s done
#10 writing image sha256:0341b70662b1c9fcd895ef67b1c8213717f6973e550a1c855c5f57ef11a28244 done
#10 naming to docker.io/faisalriazz/my-app-node:1.0 0.0s done
#10 DONE 0.4s

What's Next?
  View summary of image vulnerabilities and recommendations → docker scout quickview

```
**$ docker images**
```
REPOSITORY                   TAG       IMAGE ID       CREATED          SIZE
faisalriazz/my-app-node      1.0       0341b70662b1   23 minutes ago   130MB
faisalriazz/sample_fastapi   v1        f025f201355b   8 days ago       217MB
faisalriazz/sample_fastapi   latest    95a5cd02d5cb   9 days ago       216MB
```
Running container to verify app

**docker run -d -p 3000:3000 --name myNodeApp faisalriazz/my-app-node:1.0**
```c9fe66484b0ea33dae5a7b26e80fb445698d0ebb0046c387f55624e93b4a43f3
```
**$ curl http://localhost:3000**

 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
<html lang="en">
<style>
    .container {
        margin: 40px auto;
        width: 80%;
    }
    .button {
        width: 160px;
        height: 45px;
        border-radius: 6px;
        font-size: 15px;
        margin-top: 20px;
    }
    img {
        width: 328px;
        height: 287px;
        display: block;
        margin-bottom: 20px;
    }
    hr {
        width: 400px;
        margin-left: 0;
    }
    h3 {
        display: inline-block;
    }
    #container {
        display: true;
    }
    #container-edit {
        display: true;
    }
    #container-edit input {
        height: 32px;
    }
    #container-edit hr {
        margin: 25px 0;
    }
    #container-edit input {
        width: 195px;
        font-size: 15px;
    }
</style>
<script>
    (async function init() {
        const response = await fetch('http://localhost:3000/get-profile');
        console.log("response", response);
        const user = await response.json();
        console.log(JSON.stringify(user));

        document.getElementById('name').textContent = user.name ? user.name : 'Anna Smith';
        document.getElementById('email').textContent = user.email ? user.email : 'anna.smith@example.com';
        document.getElementById('interests').textContent = user.interests ? user.interests : 'coding';

        const cont = document.getElementById('container');
        cont.style.display = 'block';
    })();

    async function handleUpdateProfileRequest() {
        const contEdit = document.getElementById('container-edit');
        const cont = document.getElementById('container');

        const payload = {
            name: document.getElementById('input-name').value,
            email: document.getElementById('input-email').value,
            interests: document.getElementById('input-interests').value
        };

        const response = await fetch('http://localhost:3000/update-profile', {
            method: "POST",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(payload)
        });
        const jsonResponse = await response.json();

        document.getElementById('name').textContent = jsonResponse.name;
        document.getElementById('email').textContent = jsonResponse.email;
        document.getElementById('interests').textContent = jsonResponse.interests;

        cont.style.display = 'block';
        contEdit.style.display = 'none';
    }

    function updateProfile() {
        const contEdit = document.getElementById('container-edit');
        const cont = document.getElementById('container');

        document.getElementById('input-name').value = document.getElementById('name').textContent;
        document.getElementById('input-email').value = document.getElementById('email').textContent;
        document.getElementById('input-interests').value = document.getElementById('interests').textContent;

        cont.style.display = 'none';
        contEdit.style.display = 'block';
    }
</script>
<body>
    <div class='container' id='container'>
        <h1>User profile</h1>
        <img src='profile-picture' alt="user-profile">
        <span>Name: </span><h3 id='name'>Anna Smith</h3>
        <hr />
        <span>Email: </span><h3 id='email'>anna.smith@example.com</h3>
        <hr />
        <span>Interests: </span><h3 id='interests'>coding</h3>
        <hr />
        <button class='button' onclick="updateProfile()">Edit Profile</button>
    </div>
    <div class='container' id='container-edit'>
        <h1>User profile</h1>
        <img src='profile-picture' alt="user-profile">
        <span>Name: </span><label for='input-name'></label><input type="text" id='input-name' value='Anna Smith' />
        <hr />
        <span>Email: </span><label for='input-email'></label><input type="email" id='input-email' value='anna.smith@example.com' />
        <hr />
        <span>Interests: </span><label for='input-interests'></label><input type="text" id='input-interests' value='coding' />
        <hr />
100  4199  100  4199    0     0   136k      0 --:--:-- --:--:-- --:--:--  136klass='button' onclick="handleUpdateProfileRequest()">Update Profile</button>
    </div>
</body>
</html>

### 1.1.2. Create a Docker file for the database container which should include the necessary dependencies to run the database. You can use any database of your choice (e.g., MySQL, PostgreSQL, MongoDB, etc.)

```docker
# Selecting the image
FROM mongo:latest 
# Environment Variable
ENV MONGO_DB_USERNAME=admin
ENV MONGO_DB_PWD=password
# EXPOSE the port for mongodb
EXPOSE 27017

```
creating image from docker file
**$ docker build . -t mongodb:0.1**

**$ docker images**
```
REPOSITORY                   TAG       IMAGE ID       CREATED          SIZE
faisalriazz/my-app-node      1.0       f14891505b5f   38 minutes ago   130MB
mongodb                      0.1       7775b3b8ceb2   10 hours ago     653MB
faisalriazz/sample_fastapi   v1        f025f201355b   9 days ago       217MB
faisalriazz/sample_fastapi   latest    95a5cd02d5cb   9 days ago       216MB
```
running and verifying container 

**$ docker run -d -p 27017:27017 --name mongodb mongodb:0.1**
```
6961b7bb1c283d5b9e37c8cc46f54b8e81625de30311fc088071528e6c24ffba
```
**$ docker ps**
```
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                      NAMES
6961b7bb1c28   mongodb:0.1                   "docker-entrypoint.s…"   11 minutes ago   Up 11 minutes   0.0.0.0:27017->27017/tcp   mongodb
c9fe66484b0e   faisalriazz/my-app-node:1.0   "docker-entrypoint.s…"   51 minutes ago   Up 51 minutes   0.0.0.0:3000->3000/tcp     myNodeApp
```
**curl http://localhost:27017**
```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    85  100    85    0     0  15562      0 --:--:-- --:--:-- --:--:-- 17000It looks like you are trying to access MongoDB over HTTP on the native driver port.

```
### 1.1.3. Create a docker-compose.yml file that defines the two services - web and database. The web service should be linked to the database service using Docker Compose networking.
docker-compose file
```yaml
version: "3"
services:
  my-app-node:
    image: faisalriazz/my-app-node:1.0
    ports:
      - 3000:3000
  mongodb:
    image: mongo
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password
  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8080:8081
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=password
      - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
      - mongodb
```
 ### 1.1.4. Use the docker-compose up command to build and run the application. Verify that the web application is accessible from a web browser and that the database is running.
 
 **docker compose -f docker-compose.yaml up -d**

Confirming all the containers are up 

**$ docker ps**
```
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                      NAMES
0872d89a38e5   mongo-express                 "tini -- /docker-ent…"   11 minutes ago   Up 11 minutes   0.0.0.0:8080->8081/tcp     app-mongo-express-1
655287e93360   mongo                         "docker-entrypoint.s…"   13 minutes ago   Up 11 minutes   0.0.0.0:27017->27017/tcp   app-mongodb-1
3eebdd77dbd8   faisalriazz/my-app-node:1.0   "docker-entrypoint.s…"   13 minutes ago   Up 2 minutes    0.0.0.0:3000->3000/tcp     app-my-app-node-1
```
verifying front end
**$ curl http://localhost:3000**

<html lang="en">
<style>
    .container {
        margin: 40px auto;
        width: 80%;
    }
    .button {
        width: 160px;
        height: 45px;
        border-radius: 6px;
        font-size: 15px;
        margin-top: 20px;
    }
    img {
        width: 328px;
        height: 287px;
        display: block;
        margin-bottom: 20px;
    }
    hr {
        width: 400px;
        margin-left: 0;
    }
    h3 {
        display: inline-block;
    }
    #container {
        display: true;
    }
    #container-edit {
        display: true;
    }
    #container-edit input {
        height: 32px;
    }
    #container-edit hr {
        margin: 25px 0;
    }
    #container-edit input {
        width: 195px;
        font-size: 15px;
    }
</style>
<script>
    (async function init() {
        const response = await fetch('http://localhost:3000/get-profile');
        console.log("response", response);
        const user = await response.json();
        console.log(JSON.stringify(user));

        document.getElementById('name').textContent = user.name ? user.name : 'Anna Smith';
        document.getElementById('email').textContent = user.email ? user.email : 'anna.smith@example.com';
        document.getElementById('interests').textContent = user.interests ? user.interests : 'coding';

        const cont = document.getElementById('container');
        cont.style.display = 'block';
    })();

    async function handleUpdateProfileRequest() {
        const contEdit = document.getElementById('container-edit');
        const cont = document.getElementById('container');

        const payload = {
            name: document.getElementById('input-name').value,
            email: document.getElementById('input-email').value,
            interests: document.getElementById('input-interests').value
        };

        const response = await fetch('http://localhost:3000/update-profile', {
            method: "POST",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(payload)
        });
        const jsonResponse = await response.json();

        document.getElementById('name').textContent = jsonResponse.name;
        document.getElementById('email').textContent = jsonResponse.email;
        document.getElementById('interests').textContent = jsonResponse.interests;

        cont.style.display = 'block';
        contEdit.style.display = 'none';
    }

    function updateProfile() {
        const contEdit = document.getElementById('container-edit');
        const cont = document.getElementById('container');

        document.getElementById('input-name').value = document.getElementById('name').textContent;
        document.getElementById('input-email').value = document.getElementById('email').textContent;
        document.getElementById('input-interests').value = document.getElementById('interests').textContent;

        cont.style.display = 'none';
        contEdit.style.display = 'block';
    }
</script>
<body>
    <div class='container' id='container'>
        <h1>User profile</h1>
        <img src='profile-picture' alt="user-profile">
        <span>Name: </span><h3 id='name'>Anna Smith</h3>
        <hr />
        <span>Email: </span><h3 id='email'>anna.smith@example.com</h3>
        <hr />
        <span>Interests: </span><h3 id='interests'>coding</h3>
        <hr />
        <button class='button' onclick="updateProfile()">Edit Profile</button>
    </div>
    <div class='container' id='container-edit'>
        <h1>User profile</h1>
        <img src='profile-picture' alt="user-profile">
        <span>Name: </span><label for='input-name'></label><input type="text" id='input-name' value='Anna Smith' />
        <hr />
        <span>Email: </span><label for='input-email'></label><input type="email" id='input-email' value='anna.smith@example.com' />
        <hr />
        <span>Interests: </span><label for='input-interests'></label><input type="text" id='input-interests' value='coding' />
        <hr />
100  4199  100  4199    0     0   353k      0 --:--:-- --:--:-- --:--:--  372klass='button' onclick="handleUpdateProfileRequest()">Update Profile</button>
    </div>
</body>
</html>

verifying connection to db via mongo-express
**$ curl http://localhost:8080**

<!DOCTYPE html>
<html lang="en">
<head>
  <base href="/">
  <meta charset="utf-8">
  <title>Home - Mongo Express</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <link href="/public/css/bootstrap.min.css" rel="stylesheet">
  <link href="/public/css/bootstrap-theme.min.css" rel="stylesheet">
  <link href="/public/css/style.css" rel="stylesheet" />

  <link rel="prefetch" href="/public/img/gears.gif">

  <style type="text/css">
    body {
      padding-top: 60px;
      padding-bottom: 40px;
    }
    .sidebar-nav {
      padding: 9px 0;
    }
    .sidebar-nav a {
      display: inline-block;
      text-overflow: ellipsis;
      white-space: nowrap;
      overflow: hidden;
    }
    .tooltip-inner {
      max-width: 500px;
    }
  </style>


</head>

<body>

<nav class="navbar navbar-default navbar-fixed-top">
  <div class="container">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <div class="logo-nav">
        <a href="/">
          <img src="/public/img/mongo-express-logo.png" />
        </a>
      </div>
      <a class="navbar-brand" href="">Mongo Express</a>
    </div>
    <div id="navbar" class="navbar-collapse collapse">
      <ul class="nav navbar-nav">

  <li class="dropdown">
    <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Database<span class="caret"></span></a>
    <ul class="dropdown-menu">

      <li><a href="/db/admin/">admin</a></li>

      <li><a href="/db/config/">config</a></li>

      <li><a href="/db/local/">local</a></li>

    </ul>
  </li>

      </ul>
    </div><!--/.nav-collapse -->
  </div>
</nav>


<div class="container">
  <div class="row">
    <div class="col-md-12">
      <div class="page-header main-title">
        <h1 id="pageTitle"></h1>
      </div>





    </div>
  </div>

  <div class="row">
    <div class="col-md-12">

<div class="panel panel-default">
  <div class="panel-heading">
    <div style="display: inline-block;">
      <h4 style="font-weight: bold;">Databases</h4>
    </div>

    <form class="form-inline" method="POST" style="float:right;">
      <div class="input-group">
        <input class="form-control" type="text" id="database" name="database" placeholder="Database Name" title="Database Name">
        <span class="input-group-btn">
          <button type="submit" class="btn btn-primary pull-right">
            <span class="glyphicon glyphicon-plus"></span>
            Create Database
          </button>
        </span>
      </div>
    </form>

    </div>
  <div class="panel-body no-padding">
<table class="table table-bordered table-striped table-condensed no-margin">

  <tr>
    <td class="col-md-2">
      <a href="/db/admin/" class="btn btn-success btn-block" >
        <span class="glyphicon glyphicon-eye-open"></span><br>View
      </a>
    </td>
    <td><h3><a href="/db/admin/">admin</a></h3></td>

    <td class="col-md-2">
      <form method="POST" action="/admin" style="margin: 0px;">
        <input type="hidden" name="_method" value="delete">
        <button type="submit" class="hidden"></button>
        <button type="button" class="btn btn-danger btn-block deleteButton" database-name="admin" >
          <span class="glyphicon glyphicon-trash"></span><br>Del
        </button>
      </form>
    </td>

  </tr>

  <tr>
    <td class="col-md-2">
      <a href="/db/config/" class="btn btn-success btn-block" >
        <span class="glyphicon glyphicon-eye-open"></span><br>View
      </a>
    </td>
    <td><h3><a href="/db/config/">config</a></h3></td>

    <td class="col-md-2">
      <form method="POST" action="/config" style="margin: 0px;">
100  7219  100  7219    0     0   229k      0 --:--:-- --:--:-- --:--:--  234kname="_method" value="delete">
        <button type="submit" class="hidden"></button>
        <button type="button" class="btn btn-danger btn-block deleteButton" database-name="config" >
          <span class="glyphicon glyphicon-trash"></span><br>Del
        </button>
      </form>
    </td>

  </tr>

  <tr>
    <td class="col-md-2">
      <a href="/db/local/" class="btn btn-success btn-block" >
        <span class="glyphicon glyphicon-eye-open"></span><br>View
      </a>
    </td>
    <td><h3><a href="/db/local/">local</a></h3></td>

    <td class="col-md-2">
      <form method="POST" action="/local" style="margin: 0px;">
        <input type="hidden" name="_method" value="delete">
        <button type="submit" class="hidden"></button>
        <button type="button" class="btn btn-danger btn-block deleteButton" database-name="local" >
          <span class="glyphicon glyphicon-trash"></span><br>Del
        </button>
      </form>
    </td>

  </tr>

</table>
</div>
  </div>

<div id="confirm-deletion" class="modal fade" role="dialog" aria-labelledby="confirmDeletionLabel">
  <div class="modal-dialog" role="document">
    <div class="modal-content">

      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="myModalLabel">Delete database</h4>
      </div>

      <div class="modal-body">
        <p>
          Be careful! You are about to delete whole <strong><span id="modal-database-name"></span></strong> database.
        </p>
        <p>
          <label for="confirmation-input">Type the database name to proceed.</label>
          <input type="text" id="confirmation-input" name="confirmation-input" shouldbe="" value="" />
        </p>
      </div>

      <div class="modal-footer">
        <button type="button" data-dismiss="modal" class="btn btn btn-danger" id="delete">Delete</button>
        <button type="button" data-dismiss="modal" class="btn">Cancel</button>
      </div>
    </div>
  </div>
</div>

<!--h2>Create Database</h2>
<form class="well form-inline" method="POST">
    <div class="form-group">
      <input class="input-medium" type="text" id="database" name="database" placeholder="Database Name" title="Database Name">
    </div>
    <button type="submit" class="btn btn-primary pull-right">
      <span class="glyphicon glyphicon-plus"></span>
      Create Database
    </button>
</form-->




<h2>Server Status</h2>
<p>
  Turn on admin in config.js to view server stats!
</p>






    </div>
  </div>
</div>

<script src="/public/vendor-d1b820f8a9cf3d5a8c6a.min.js"></script>
<script type="text/javascript">
'use strict';
window.ME_SETTINGS = {
  readOnly: 'false' === 'true',
  noDelete: 'false' === 'true',
  codeMirrorEditorTheme: '',
  baseHref: '/',
  collapsibleJSON: 'true' === 'true',
  collapsibleJSONDefaultUnfold: parseInt('1', 10),
  confirmDelete: 'false' === 'true',
  dbName: '',
  collectionName: '',
  bucketName: ''
};
</script>


<script src="/public/index-6145173d12f8f196322e.min.js"></script>


</body>
</html>

### 1.1.5. Implement a backup strategy for the database. This can be achieved using a Docker volume or by running a backup script periodically
 * Implementing backup using named docker volume.
 * modified docker-compose file is shown below
```yaml
version: '3'
services:
  my-app-node:
    image: faisalriazz/my-app-node:1.0
    ports:
      - 3000:3000
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=admin
     - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
     - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    restart: always
    ports:
     - 8080:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
     - ME_CONFIG_MONGODB_ADMINPASSWORD=password
     - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
     - "mongodb"
volumes:
  mongo-data:
    driver: local
```
* using modified docker compose file to run the containers.
**docker compose -f docker-compose.yaml up -d**
```
Network app_default  Creating
 Network app_default  Created
 Volume "app_mongo-data"  Creating
 Volume "app_mongo-data"  Created
 Container app-mongodb-1  Creating
 Container app-my-app-node-1  Creating
 Container app-my-app-node-1  Created
 Container app-mongodb-1  Created
 Container app-mongo-express-1  Creating
 Container app-mongo-express-1  Created
 Container app-my-app-node-1  Starting
 Container app-mongodb-1  Starting
 Container app-mongodb-1  Started
 Container app-mongo-express-1  Starting
 Container app-my-app-node-1  Started
 Container app-mongo-express-1  Started

``` 
* verifying named volumes with mongo-data is created.
**docker volume ls**
```
$ docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                      NAMES
835f17a0532e   mongo-express                 "tini -- /docker-ent…"   17 seconds ago   Up 3 seconds    0.0.0.0:8080->8081/tcp     app-mongo-express-1
2b9ea372a616   mongo                         "docker-entrypoint.s…"   17 seconds ago   Up 15 seconds   0.0.0.0:27017->27017/tcp   app-mongodb-1
70d368a129da   faisalriazz/my-app-node:1.0   "docker-entrypoint.s…"   17 seconds ago   Up 15 seconds   0.0.0.0:3000->3000/tcp     app-my-app-node-1
$ docker volume ls
DRIVER    VOLUME NAME
local     22e5d646e6aa4e43486b3cf3c58c41bc3fa97f13aca1ee2bc21b480c71080ae2
local     90f281f44fb6edc353eaa8398e2362614dd6797ffb06be6a51f0db8b4f0e1832
local     470397efe52b0d987aef696db26efb3fb95a29c104c32df2ad6d31810f9fbd7a
local     app_mongo-data
local     bbd6d537ad6ea534c0f410cf577c1e699fadfea29127ca98000bb4b1cb4afb5d
local     f0752e4b8d951426896a90a069490d5273f4c1a29bb6b370d7a45451cb886c0b
```
### 1.1.6. Implement a scaling strategy for the web application. This can be achieved by using the docker-compose scale command to create multiple instances of the web service. Verify that the web application is accessible and working correctly when scaled.

*  for docker compose scaling  need to change port binding in docker compose file. Modified file is shown below 
```yaml
version: '3'
services:
  my-app-node:
    image: faisalriazz/my-app-node:1.0
    ports:
      - 3000-3005:3000
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=admin
     - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
     - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    restart: always
    ports:
     - 8080:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
     - ME_CONFIG_MONGODB_ADMINPASSWORD=password
     - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
     - "mongodb"
volumes:
  mongo-data:
    driver: local
``` 
* Running two instance of sample node app.
One for port `3000` and other one for `3001`.

**docker compose -f docker-compose.yaml up -d --scale my-app-node=2**
```
Container app-mongodb-1  Running
 Container app-mongo-express-1  Running
 Container app-my-app-node-1  Recreate
 Container app-my-app-node-2  Recreate
 Container app-my-app-node-2  Recreated
 Container app-my-app-node-1  Recreated
 Container app-my-app-node-1  Starting
 Container app-my-app-node-1  Started
 Container app-my-app-node-2  Starting
 Container app-my-app-node-2  Started

```
* verfying both instances running using curl
```
 curl http://localhost:3000
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
```
```
curl http://localhost:3001
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
```
### 1.1.7. Implement a monitoring strategy for the application. This can be achieved by using a tool like Prometheus or Grafana to monitor the health and performance of the containers.

* modifying docker compose file to implement grafana
```yaml
version: '3'
services:
  my-app-node:
    image: faisalriazz/my-app-node:1.0
    ports:
      - 3001-3005:3000
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=admin
     - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
     - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    restart: always
    ports:
     - 8080:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
     - ME_CONFIG_MONGODB_ADMINPASSWORD=password
     - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
     - "mongodb"
  grafana:
    image: grafana/grafana:latest
    ports:
     - 5000:3000
    environment:
      GF_SECURITY_ADMIN_USER: admin
      GF_SECURITY_ADMIN_PASSWORD: admin
    volumes:
     - ./datasources:/etc/grafana/provisioning/datasources
     - grafana-data:/var/lib/grafana
volumes:
  mongo-data:
    driver: local
  grafana-data:
```
* executing docker compose to deploy the containers.
**$ docker compose -f docker-compose.yaml up -d**
```
 Network app_default  Creating
 Network app_default  Created
 Volume "app_grafana-data"  Creating
 Volume "app_grafana-data"  Created
 Container app-mongodb-1  Creating
 Container app-grafana-1  Creating
 Container app-my-app-node-1  Creating
 Container app-my-app-node-1  Created
 Container app-grafana-1  Created
 Container app-mongodb-1  Created
 Container app-mongo-express-1  Creating
 Container app-mongo-express-1  Created
 Container app-grafana-1  Starting
 Container app-my-app-node-1  Starting
 Container app-mongodb-1  Starting
 Container app-mongodb-1  Started
 Container app-mongo-express-1  Starting
 Container app-my-app-node-1  Started
 Container app-grafana-1  Started
 Container app-mongo-express-1  Started
```
```
$ docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS         PORTS                      NAMES
e172ba4033cd   mongo-express                 "tini -- /docker-ent…"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->8081/tcp     app-mongo-express-1
3a9f2762a8a4   grafana/grafana:latest        "/run.sh"                3 minutes ago   Up 3 minutes   0.0.0.0:5000->3000/tcp     app-grafana-1
b599b2e4f140   mongo                         "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   0.0.0.0:27017->27017/tcp   app-mongodb-1
770e1583316f   faisalriazz/my-app-node:1.0   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   0.0.0.0:3001->3000/tcp     app-my-app-node-1

```
* verifying grafana is running using curl. 
```
$ curl http://localhost:5000
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    29  100    29    0     0   4577      0 --:--:-- --:--:-- --:--:--  4833<a href="/login">Found</a>.

```
### 1.1.8. Docker hub repo
https://hub.docker.com/r/faisalriazz/my-app-node/tags
