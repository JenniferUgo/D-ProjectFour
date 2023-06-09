# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

## MEAN Stack is a combination of following components:
1. **MongoDB** (Document database) – Stores and allows to retrieve data.
2. **Express** (Back-end application framework) – Makes requests to Database for Reads and Writes.
3. **Angular** (Front-end application framework) – Handles Client and Server Requests
4. **Node.js** (JavaScript runtime environment) – Accepts requests and displays results to end user.

### First, we need to do these steps:
1. Create an account on [AWS](https://aws.amazon.com/)
2. Create an instance (virtual machine) by selecting “ubuntu server 20.04 LTS” from Amazon Machine Image(AMI)(free tier).
3. Select “t2.micro(free tier eligible)” and select you Key Pair
4. On the security group and select “existing security group” review and click Launch Instance.

#### This launches the instance and takes you to the Instances dashboard
![launch instance](./images/launch-instance.png)

5. Then open a terminal on your system and enter the folder where your previously downloaded PEM file is located.

#### ***In this case we use the Git Bash Terminal***

![open terminal](./images/gitbash.png)

6. Connect to the instance from ubuntu terminal using this command:

>`ssh -i "Jennee-EC2.pem" ubuntu@ec2-35-176-8-236.eu-west-2.compute.amazonaws.com`
###### ***Use your own***

![connect to EC2 instance](./images/connect-to-instance.png)

#### *This automatically connects to the instance when you click Enter*
<br>

###  INSTALLING NODEJS

#### *Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.*

<br>

### First, we Update ubuntu using this command:
>`sudo apt update`

![update ubuntu](./images/update-ubuntu.png)

### Then Upgrade ubuntu using this command:
>`sudo apt upgrade`

![upgrade ubuntu](./images/upgrade-ubuntu.png)

### Then we Add certificates, using this command:

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

![add certificates](./images/add-certificates.png)

### Next, Install NodeJS, using this command:
>`sudo apt install -y nodejs`

![Install NodeJS](./images/install-nodejs.png)

<br>

### INSTALLING MONGODB

### *MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time.*

#### Here, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages. With these commands:
<br>

```
 sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
 ```
 ```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

![install mongodb](./images/install-mongodb.png)

### Install MongoDB using this command:
>`sudo apt install -y mongodb

![installing mongodb](./images/installing-mongodb.png)

### Then, we Start the server with this command:
>`sudo service mongodb start`

#### To verify that the server is up and running, we run the command:
>`
![start and verify the server](./images/start-and-verify-server.png)

### Next, we Install **npm** – Node package manager, using this command:
>`sudo apt install -y npm`

![install npm](./images/install-npm.png)

### After that, we Install **body-parser** package, using this command:
>`sudo npm install body-parser`

##### *The **‘body-parser’** package is used to process JSON files passed in requests to the server.*

![install body-parser](./images/install-body-parser.png)

### Next, we create a folder named **Books**, using this command:
>` mkdir Books && cd Books`

#### In the Books directory, Initialize npm project, using this command:
>`npm init`

###### *Hit the **Enter** button on your keyboard until you get the prompt "Is this OK? (yes), type 'yes' and hit **Enter***

![like so](./images/npm-init.png)

### Next, Add a file to the **Books** named ***server.js***, using this command:
>` touch server.js`

#### Then open the file using this command:
>`vi server.js`

#### And paste the following web server code into the ***server.js*** file:

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

![like so](./images/paste-in%3Dserverjs.png)

##### Click esc, then,
##### Save with:
>`:w`

##### Exit with:
>`:qa`

<br>

---

### INSTALL EXPRESS AND SET UP ROUTES TO THE SERVER

#### *Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express here to pass book information to and from our MongoDB database.*
<br>

#### *We will also use Mongoose package, which provides a straight-forward, schema-based solution to model our application data. We will use Mongoose to establish a schema for the database to store data of our book register.*

### We do that using this command:
>`sudo npm install express mongoose`

![like so](./images/install-express-mongoose.png)

### In the **Books** folder, create a folder named ***apps***, using this command:

>`mkdir apps && cd apps`

#### Then create a file named **routes.js**, using this command:
>`touch routes.js`

#### Next, open the file with this command@
>`vi routes.js`

### Paste the following code into **routes.js** file:
```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

![like so](./images/paste-in-routesjs.png)

##### Click esc, then,
##### Save with:
>`:w`

##### Exit with:
>`:qa`

### Now, In the **apps** folder, create a folder named ***models***, using this command:
>`mkdir models && cd models`

#### Then create a file in it named **book**, using this command:
>`touch book.js`
#### Next, open the file with this command@
>`vi book.js`

### Paste the following code into **book.js** file:
```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```
![like so](./images/paste-in-bookjs.png)

##### Click esc, then,
##### Save with:
>`:w`

##### Exit with:
>`:qa`

<br>

### ACCESS THE ROUTES WITH ANGULARJS
#### *AngularJS provides a web framework for creating dynamic views in your web applications. Here, we'll use AngularJS to connect our web page with Express and perform actions on our book register.*
<br>

### Change the directory back to **Books**
<br>

### Then create a folder named **public**, using this command:
>`mkdir public && cd public`

#### Next, create a file in it, named **script.js**, using this command:
>`touch script.js`

#### Open the file with this command:
>`vi script.js`

### Paste the following code into **script.js** file:
```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
![like so](./images/paste-in-scriptjs.png)

##### Click esc, then,
##### Save with:
>`:w`

##### Exit with:
>`:qa`

<br>

### Next, create a file named ***index.html*** in **public** folder, using this command:
>`touch index.html`
#### Open the file with this command:
>`vi index.html`

### Paste the following code into **index.html** file:
```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

![like so](./images/paste-in-index.html.png)

### Change the directory back up to **Books**

#### Start the server by running this command:
>`node server.js`

![start the server](./images/start-server.png)

#### *The server is now up and running, we can connect it via port 3300.* 
#### To access it on the internet, we edit inbound rules on our instance set up TCP port 3300 by clicking on Add Rule in our EC2 Instance

![like so](./images/edit-inbound-rules-port3300.png)


#### Access the page with:
>`http://13.40.104.201:3300`

![like so](./images/access-webpage-angularjs.png)