
Node - Interview Questions

# Explain in brief what is node js?
=> Node. js (Node) is an open source development platform for executing JavaScript code server-side. Node is useful for developing applications that require a persistent connection from the browser to the server and is often used for real-time applications such as chat, news feeds and web push notifications.

**OR**

- Node.js is an open source server environment
- Node.js is free
- Node.js runs on various platforms (Windows, - - Linux, Unix, Mac OS X, etc.)
- Node.js uses JavaScript on the server

# How is node js non blocking?
=> **Non-Blocking:** It refers to the program that does not block the execution of further operations. Non-Blocking methods are executed asynchronously. Asynchronously means that the program may not necessarily execute line by line. The program calls the function and move to the next operation and does not wait for it to return.

Example: Following example uses the readFile() function to read files and demonstrate Non-Blocking in Node.js
```js
const fs = require('fs');

const filepath = 'text.txt';

// Reads a file in a asynchronous and non-blocking way
fs.readFile(filepath, {encoding: 'utf8'}, (err, data) => {
	// Prints the content of file
	console.log(data);
});


// This section calculates the sum of numbers from 1 to 10
let sum = 0;
for(let i=1; i<=10; i++){
	sum = sum + i;
}

// Prints the sum
console.log('Sum: ', sum);
```
Run the index.js file using the following command: 
```js
node index.js
```
Output:
```js
Sum:  55
This is from text file.
```
**Note:** In the non-blocking program the sum actually prints before the content of the file. This is because the program does not wait for the readFile() function to return and move to the next operation. And when the readFile() function returns it prints the content.


**Blocking:** It refers to the blocking of further operation until the current operation finishes. Blocking methods are executed synchronously. Synchronously means that the program is executed line by line. The program waits until the called function or the operation returns.

Example: Following example uses the readFileSync() function to read files and demonstrate Blocking in Node.js
```js
const fs = require('fs');

const filepath = 'text.txt';

// Reads a file in a synchronous and blocking way
const data = fs.readFileSync(filepath, {encoding: 'utf8'});

// Prints the content of file
console.log(data);

// This section calculates the sum of numbers from 1 to 10
let sum = 0;
for(let i=1; i<=10; i++){
	sum = sum + i;
}

// Prints the sum
console.log('Sum: ', sum);
```
Run the index.js file using the following command:
```js
node index.js
```
Output:
```js
This is from text file.
Sum:  55
```

# What is throughput?
=> Throughput is the number of units that can be produced by a production process within a certain period of time. For example, if 800 units can be produced during an eight-hour shift, then the production process generates throughput of 100 units per hour.

**OR**

The target is to achieve an average throughput of less than one second with no failed requests. On a 4 core machine, the app handles upto 4015 requests per second without any failures. However since the target is 10000 requests per second, we deployed the node app in a clustered environment.

# How is Node js having high IO throughput?
=> <a href="https://nodejs.org/api/cluster.html">Clustering</a> is a technique used to horizontally scale a Node.js server on a single machine by spawning child processes (workers) that run concurrently and share a single port. It is a common tactic to reduce downtime, slowdowns and outages by distributing the incoming connections across all the available worker processes so that available CPU cores are utilized to their full potential. Since a Node.js instance runs on a single thread, it cannot take advantage of multi-core systems properly - hence the need for clustering.

You can cluster your Node.js server through the cluster module in the standard library. Here's an example taken from the official documentation:
```js
const cluster = require("cluster");
const http = require("http");
const process = require("process");
const os = require("os");
 
const cpus = os.cpus;
 
const numCPUs = cpus().length;
 
if (cluster.isPrimary) {
  console.log(`Primary ${process.pid} is running`);
 
  // Fork workers.
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
 
  cluster.on("exit", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);
  });
} else {
  // Workers can share any TCP connection
  // In this case it is an HTTP server
  http
    .createServer((req, res) => {
      res.writeHead(200);
      res.end("hello world\n");
    })
    .listen(8000);
 
  console.log(`Worker ${process.pid} started`);
}
```
Once you run this program, connections sent to port 8000 will be shared between the worker processes. This will lead to more efficient requests management in the application:
```js
$ node server.js
Primary 15990 is running
Worker 15997 started
Worker 15998 started
Worker 16010 started
Worker 16004 started
```

# What are CPU intensive tasks?
=> It means that too many (or too long) CPU-intensive tasks could keep the main thread too busy to handle other requests, practically blocking it. The Node. js execution model was designed to cater to the needs of most web servers, which tend to be I/O-intensive.

# How can you end up blocking your main thread in node.js?
=> you can end up blocking your thread in node.js due to always choose asynchronous versions of the standard library APIs. Otherwise, after running your app, the client can experience several problems, starting with the degraded throughput and ending on complete withdrawal, which is fatal from the user’s perspective.

There are two types of threads in Node.js: Main Thread – which is handled by Event Loop, and Worker Pool (thread pool) – which is the pool of threads – thanks to libuv. Each of them has a different job. The goal of the first one is to handle non-blocking I/O operations, and the second is responsible for CPU intensive work and also blocking I/O.

Since Node.js is single-threaded (JS part), CPU-intensive tasks will block all requests in progress until the particular task is completed. So, it`s true that in Node.js you can block every request just because one of them had a blocking instruction inside. The blocking code means that it takes more that few milliseconds to finish. But do not confuse long response time with blocking. The response from database can take a very long time, but it does not block your process (application).

Blocking methods execute synchronously and non-blocking methods execute asynchronously.

# What is the event loop?
=> The event loop allows Node.js to perform non-blocking I/O operations despite the fact that JavaScript is single-threaded. It is done by assigning operations to the operating system whenever and wherever possible.

Docs: <a href="https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/">Link</a>

**Features of Event Loop:**

- Event loop is an endless loop, which waits for tasks, executes them and then sleeps until it receives more tasks.
- The event loop executes tasks from the event queue only when the call stack is empty i.e. there is no ongoing task.
- The event loop allows us to use callbacks and promises.
- The event loop executes the tasks starting from the oldest first.
Example:
```js
console.log("This is the first statement");
   
setTimeout(function(){
    console.log("This is the second statement");
}, 1000);
   
console.log("This is the third statement"); 
// *Output:
// This is the first statement
// This is the third statement
// This is the second statement
```

# What are different phases in event loop?
=> The Event Loop contains six main phases: timers, I/O callbacks, preparation / idle phase, I/O polling, setImmediate() callbacks execution, and close events callbacks.

- **timers:** this phase executes callbacks scheduled by setTimeout() and setInterval().
- **pending callbacks:** executes I/O callbacks deferred to the next loop iteration.
- **idle, prepare:** only used internally.
- **poll:** retrieve new I/O events; execute I/O related callbacks (almost all with the exception of close callbacks, the ones scheduled by timers, and setImmediate()); node will block here when appropriate.
- **check:** setImmediate() callbacks are invoked here.
- **close callbacks:** some close callbacks, e.g. socket.on('close', ...).
Between each run of the event loop, Node.js checks if it is waiting for any asynchronous I/O or timers and shuts down cleanly if there are not any.

# What is process.tick?
=> As you try to understand the Node.js event loop, one important part of it is process.nextTick().

Every time the event loop takes a full trip, we call it a tick.

When we pass a function to process.nextTick(), we instruct the engine to invoke this function at the end of the current operation, before the next event loop tick starts:
```js
process.nextTick(() => {
  //do something
})
```
The event loop is busy processing the current function code.

When this operation ends, the JS engine runs all the functions passed to nextTick calls during that operation.

It's the way we can tell the JS engine to process a function asynchronously (after the current function), but as soon as possible, not queue it.

Calling setTimeout(() => {}, 0) will execute the function at the end of next tick, much later than when using nextTick() which prioritizes the call and executes it just before the beginning of the next tick.

Use nextTick() when you want to make sure that in the next event loop iteration that code is already executed.

# When can process.tick starve your event loop?
=> Looking back at our diagram, any time you call process.nextTick() in a given phase, all callbacks passed to process.nextTick() will be resolved before the event loop continues. This can create some bad situations because it allows you to "starve" your I/O by making recursive process.nextTick() calls, which prevents the event loop from reaching the poll phase.

# What is the difference between setTimeout and setInterval?
=> setTimeout( function, duration) − This function calls function after duration milliseconds from now. This goes for one execution. Let’s see an example −

It waits for 2000 milliseconds, and then runs the callback function alert(‘Hello’) −
```js
setTimeout(function() { alert('Hello');}, 2000);
```
setInterval(function, duration) − This function calls function after every duration milliseconds. This goes for unlimited times. Let’s see an example −

It triggers the alert(‘Hello’) after every 2000 milliseconds, not only once.
```js
setInterval(function() { alert('Hello');}, 2000);
```

# How can you make a network request with http module from the backend?
=> A GET request is possible just using the Node.js standard modules, although it's more verbose than the option above:
```js
const https = require('https')
const options = {
  hostname: 'example.com',
  port: 443,
  path: '/todos',
  method: 'GET'
}

const req = https.request(options, res => {
  console.log(`statusCode: ${res.statusCode}`)

  res.on('data', d => {
    process.stdout.write(d)
  })
})

req.on('error', error => {
  console.error(error)
})

req.end()
```
Or alternatively, use Node.js standard modules:
```js
const https = require('https')

const data = JSON.stringify({
  todo: 'Buy the milk'
})

const options = {
  hostname: 'whatever.com',
  port: 443,
  path: '/todos',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': data.length
  }
}

const req = https.request(options, res => {
  console.log(`statusCode: ${res.statusCode}`)

  res.on('data', d => {
    process.stdout.write(d)
  })
})

req.on('error', error => {
  console.error(error)
})

req.write(data)
req.end()
```

# How can you create your own events?
=> Events Module
Node.js has a built-in module, called "Events", where you can create-, fire-, and listen for- your own events.

To include the built-in Events module use the require() method. In addition, all event properties and methods are an instance of an EventEmitter object. To be able to access these properties and methods, create an EventEmitter object:
```js
var events = require('events');
var eventEmitter = new events.EventEmitter();
```
The EventEmitter Object
You can assign event handlers to your own events with the EventEmitter object.

In the example below we have created a function that will be executed when a "scream" event is fired.

To fire an event, use the emit() method.

Example
```js
var events = require('events');
var eventEmitter = new events.EventEmitter();

//Create an event handler:
var myEventHandler = function () {
  console.log('I hear a scream!');
}

//Assign the event handler to an event:
eventEmitter.on('scream', myEventHandler);

//Fire the 'scream' event:
eventEmitter.emit('scream');
```

# What are clusters?
=> Node.js runs single threaded programming, which is very memory efficient, but to take advantage of computers multi-core systems, the Cluster module allows you to easily create child processes that each runs on their own single thread, to handle the load.

Syntax: 
The syntax for including the cluster module in your application:
```js
var cluster = require('cluster');

if (cluster.isWorker) {
  console.log('I am a worker');
} else {
  console.log('I am a master');
  cluster.fork();
  cluster.fork();
}
```

# How does your Node.js application handle scale? Elaborate
=> There are mainly three different things we can do to scale an application:

1 — Cloning
The easiest thing to do to scale a big application is to clone it multiple times and have each cloned instance handle part of the workload (with a load balancer, for example). This does not cost a lot in term of development time and it’s highly effective. This strategy is the minimum you should do and Node.js has the built-in module, cluster, to make it easier for you to implement the cloning strategy on a single server.

2 — Decomposing
We can also scale an application by decomposing it based on functionalities and services. This means having multiple, different applications with different code bases and sometimes with their own dedicated databases and User Interfaces.

This strategy is commonly associated with the term Microservice, where micro indicates that those services should be as small as possible, but in reality, the size of the service is not what’s important but rather the enforcement of loose coupling and high cohesion between services. The implementation of this strategy is often not easy and could result in long-term unexpected problems, but when done right the advantages are great.

3 — Splitting
We can also split the application into multiple instances where each instance is responsible for only a part of the application’s data. This strategy is often named horizontal partitioning, or sharding, in databases. Data partitioning requires a lookup step before each operation to determine which instance of the application to use. For example, maybe we want to partition our users based on their country or language. We need to do a lookup of that information first.

# What is the difference between readFile and readFileSync?
=> readFileSync() is synchronous and blocks execution until finished. These return their results as return values. readFile() are asynchronous and return immediately while they function in the background. You pass a callback function which gets called when they finish.
```js
var fs = require('fs');
fs.readFile(filename, "utf8", function(err, data) {
        if (err) throw err;
        console.log(data);
});
var fs = require('fs');
function readFileAsSync(){
    new Promise((resolve, reject)=>{
        fs.readFile(filename, "utf8", function(err, data) {
                if (err) throw err;
                resolve(data);
        });
    });
}

async function callRead(){
    let data = await readFileAsSync();
    console.log(data);
}

callRead();
```

# What are CORS? How do you configure them? Why do you need them?
=> Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources. CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request. In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request.

**For IIS6:** 
- Open Internet Information Service (IIS) Manager.
- Right click the site you want to enable CORS for and go to Properties.
- Change to the HTTP Headers tab.
- In the Custom HTTP headers section, click Add.
- Enter Access-Control-Allow-Origin as the header name.
- Enter * as the header value.
- Click Ok twice.

The CORS mechanism supports secure cross-origin requests and data transfers between browsers and servers. Modern browsers use CORS in APIs such as XMLHttpRequest or Fetch to mitigate the risks of cross-origin HTTP requests.

# What is rate limiting?
=> Rate limiting is a feature used to control outgoing and incoming requests in a server. We might limit the number of requests a user without a premium account makes to 100 to an hour.

Code in Backend:
```js
const express = require("express");
const indexRoute = require("./router");
const rateLimit = require("express-rate-limit");
const app = express();
const port = 3000;

app.use(
  rateLimit({
    windowMs: 12 * 60 * 60 * 1000, // 12 hour duration in milliseconds
    max: 5,
    message: "You exceeded 100 requests in 12 hour limit!",
    headers: true,
  })
);

app.use("/posts", indexRoute);

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});
```

# How does middlewares work in express?
=> Middleware functions are functions that have access to the request object ( req ), the response object ( res ), and the next function in the application's request-response cycle. The next function is a function in the Express router which, when invoked, executes the middleware succeeding the current middleware.

Examples:
```js
const express = require('express')
const app = express()

const myLogger = function (req, res, next) {
  console.log('LOGGED')
  next()
}

app.use(myLogger)

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(3000)
```
```js
const express = require('express')
const app = express()

const requestTime = function (req, res, next) {
  req.requestTime = Date.now()
  next()
}

app.use(requestTime)

app.get('/', (req, res) => {
  let responseText = 'Hello World!<br>'
  responseText += `<small>Requested at: ${req.requestTime}</small>`
  res.send(responseText)
})

app.listen(3000)
```
```js
const express = require('express')
const cookieParser = require('cookie-parser')
const cookieValidator = require('./cookieValidator')

const app = express()

async function validateCookies (req, res, next) {
  await cookieValidator(req.cookies)
  next()
}

app.use(cookieParser())

app.use(validateCookies)

// error handler
app.use((err, req, res, next) => {
  res.status(400).send(err.message)
})

app.listen(3000)
```

# What is the difference between Encryption and Hashing?
=> Encryption is the process of converting a normal readable message known as plaintext into a garbage message or not readable message known as Ciphertext. The ciphertext obtained from the encryption can easily be transformed into plaintext using the encryption key. Some of the examples of encryption algorithms are RSA, AES, and Blowfish.

Hashing is the process of converting the information into a key using a hash function. The original information cannot be retrieved from the hash key by any means. Generally, the hash keys are stored in the database and they are compared to check whether the original information matches or not. They are generally used to store the passwords for login. Some of the examples of a hashing algorithm are MD5, SHA256.

# What is the difference between https and http?
=> Differences between HTTP and HTTPS: 

- In HTTP, URL begins with “http://” whereas URL starts with “https://”
- HTTP uses port number 80 for communication and HTTPS uses 443
- HTTP is considered to be unsecure and HTTPS is secure
- HTTP Works at Application Layer and HTTPS works at Transport Layer
- In HTTP, Encryption is absent and Encryption is present in HTTPS as discussed above
- HTTP does not require any certificates and HTTPS needs SSL Certificates.

# What is TLS?
=> Transport Layer Securities (TLS) are designed to provide security at the transport layer. TLS was derived from a security protocol called Secure Socket Layer (SSL). TLS ensures that no third party may eavesdrop or tampers with any message. 

There are several benefits of TLS: 
 

- **Encryption:** 
TLS/SSL can help to secure transmitted data using encryption.
- **Interoperability:** 
TLS/SSL works with most web browsers, including Microsoft Internet Explorer and on most operating systems and web servers.
- **Algorithm flexibility:** 
TLS/SSL provides operations for authentication mechanism, encryption algorithms and hashing algorithm that are used during the secure session.
- **Ease of Deployment:** 
Many applications TLS/SSL temporarily on a windows server 2003 operating systems.
- **Ease of Use:** 
Because we implement TLS/SSL beneath the application layer, most of its operations are completely invisible to client. 
 
# What is AES?
=> Advanced Encryption Standard (AES) is a specification for the encryption of electronic data established by the U.S National Institute of Standards and Technology (NIST) in 2001. AES is widely used today as it is a much stronger than DES and triple DES despite being harder to implement.

Points to remember: 
- AES is a block cipher.
- The key size can be 128/192/256 bits.
- Encrypts data in blocks of 128 bits each.

That means it takes 128 bits as input and outputs 128 bits of encrypted cipher text as output. AES relies on substitution-permutation network principle which means it is performed using a series of linked operations which involves replacing and shuffling of the input data.

# What is JWT Token? Why do we need to use JWT? What are some pros and cons?
=> JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the HMAC algorithm) or a public/private key pair using RSA or ECDSA.

**Pros and Cons of JWTs:**
- JWTs expire at specific intervals. When a JWT is created it is given a specific expiration instant. ...
- JWTs are signed. Since JWTs are cryptographically signed, they require a cryptographic algorithm to verify. ...
- JWTs aren't easily revocable. ...
- JWTs have exploits. ...
- Sessions as an Alternative.

# What is salting? Where do we store salt?
=> Salting makes password hashing more secure. Salting is an extra action during hashing. If two clients have the same password, they will also have the same password hashes. A salt, which is a random series of characters, is an extra input to the password before hashing. This makes an alternate hash result for the two passwords. Salting makes it difficult to use lookup tables and rainbow tables to crack a hash. A lookup table is a data structure that processes several hash lookups for every second.

Implementation of Salting:
The following suggestions are used to implement salting:

- Size of the salt should match the size of the hash function’s output.
- Always hash on the server in a web application.
- The salt should be unique for every user’s password.

A Cryptographically Secure Pseudo-Random Number Generator (CSPRNG) is the best option to produce salt. It is completely unpredictable and produces a random number. So it is highly secure.

**To store a password:**
- Use CSPRNG (Cryptographically Secure Pseudo-Random Number Generator) to produce a salt.
- Add salt to the starting of the password.
- Hash it with SHA-256.
- Save the hash and the salt.

The salt is not an encryption key, so it can be stored in the password database along with the username – it serves merely to prevent two users with the same password getting the same hash.

# What is the difference between Authorisation and Authentication?
=> Authentication is the process of verifying who someone is, whereas authorization is the process of verifying what specific applications, files, and data a user has access to.

**OR**

In simple terms, authentication is the process of verifying who a user is, while authorization is the process of verifying what they have access to.

Comparing these processes to a real-world example, when you go through security in an airport, you show your ID to authenticate your identity. Then, when you arrive at the gate, you present your boarding pass to the flight attendant, so they can authorize you to board your flight and allow access to the plane.

# What is difference between JS on the browser and node?
=> Js uses Google's V8 engine to provide JavaScript runtime and employes its own event loop using the libuv library (written in c). Node follows the same callback approach like Web APIs and works in a similar fashion as the browser.

# What is V8?
=> V8 is the name of the JavaScript engine that powers Google Chrome. It's the thing that takes our JavaScript and executes it while browsing with Chrome.

V8 provides the runtime environment in which JavaScript executes. The DOM, and the other Web Platform APIs are provided by the browser.

The cool thing is that the JavaScript engine is independent of the browser in which it's hosted. This key feature enabled the rise of Node.js. V8 was chosen to be the engine that powered Node.js back in 2009, and as the popularity of Node.js exploded, V8 became the engine that now powers an incredible amount of server-side code written in JavaScript.

The Node.js ecosystem is huge and thanks to V8 which also powers desktop apps, with projects like Electron.