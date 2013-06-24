# Node.js + OpenShift = chatty, a simple "HTML5" chat server

by Jeremy Osborne


## Wherefore art thou JavaScript?

If every decade was associated with a programming language or languages that one needed to 
know, I see this decade set for those who embrace Python, Ruby, or JavaScript. As one of these de facto languages, learning JavaScript will only be a benefit, and with the advent of all of the new "HTML5" APIs along with Node.js, your plate will likely be full of work should you want it.
  
JavaScript is a language that, due to the dynamic nature of the web, regularly provides new avenues for education. So much so that it is easy to take the same idea for some example and rewrite it every few years using new APIs, like a web based chat server. 

A few years ago, a chat client/server would probably have been branded "Ajax" and would have used a framework like [Twisted](http://twistedmatrix.com/trac/) on the backend.

Today, we can take that same chat client/server, apply the newer, cooler buzzwords like "HTML5" and "JavaScript on the Server" and have fun messing with:

* [WebSockets](http://en.wikipedia.org/wiki/WebSocket): The new, cross-origin friendly, two-way communication interface.
* [Node.js](http://nodejs.org/): JavaScript isn't just for the web browser anymore. Node.js is definitely a great implementation of a JavaScript runtime for a server.
* [npm](http://npmjs.org): Node.js is as cool as it is mainly thanks to the Node Package Manager, and all of the great third party tools and projects that have sprung up. But beyond being a great package repository, we'll see it is also a fabulous development tool.
* [OpenShift](https://openshift.redhat.com/app/): We wouldn't be playing with the cool kids if we didn't throw "cloud" into our mix of buzzwords. I'm relatively new to OpenShift, but when it comes to deploying a Node.js app, I've been pleasantly surprised at how easy it is to do.

If you follow this article, you will get:

* A real, running, mother-effing CHAT SERVER+CLIENT BABY! You can invite your dog, your significant other, or anyone to your URL and ask them meaningful questions like, "A/S/L?" Get ready to add HTML5, JavaScript, WebSockets, Node.js, Cloud, and other cool words like that to your CV.
* A Node.js rough setup that should give you the experience, if you are new to Node.js, to jump right in later for bigger and badder projects.
* A contrived, yet fun example of what it's like to organize and run some chunks of code on both the client and the server without (much) modification.
* All of the scaffolding and Kung Fu practice necessary to almost-one-button our code up to the OpenShift platform. Even better, as you'll see, the code here is not locked in, in anyway, to OpenShift, and you're free to use it on your own server or elsewhere.

So there we have it. You (likely) won't win any awards for building or publishing a chat system. You (likely) won't get rich, either. But if you're new to these technologies, than this article is a gentle walkthrough into all of these systems. Plus, you don't have to write a byte of code, unless you want to. How much easier can it get than that?



## Prerequisite Steps
Install [git](http://git-scm.com/downloads). Check to see if it is installed by typing the command:

    git --version

Install [Node.js](http://nodejs.org) and [npm](https://npmjs.org/). You can check to see if either is installed with:

    node --version
    npm --version

Get an OpenShift account if you do not have one by going to [https://openshift.redhat.com/app/account/new](https://openshift.redhat.com/app/account/new).

This guide assumes you are using the [OpenShift commandline tools](https://openshift.redhat.com/community/get-started). If you do not have the commandline tools, and you are on a Mac OS X system or a UNIX-like system, and you want to attempt to get them right now, from your commandline run: 

    sudo gem install rhc
    rhc setup



## Please, for the love of all that is holy, I'm smart and can figure the code out myself, just give it to meeee NAAAOOOOOOOOOO!!!!

I love autodidacts. Here you go:

    rhc app create chatty nodejs-0.6
    cd chatty
    git remote add -f chatty-upstream git://github.com/jeremyosborne/chatty.git
    git branch chatty-upstream chatty-upstream/master
    git merge -Xtheirs v0.1.2
    git push
    # go check out your app hosted on your OpenShift site.

If you just want a copy of the code, which will work just fine outside of OpenShift:

    git clone git://github.com/jeremyosborne/chatty.git
    # To run the code locally.
    cd chatty
    npm install
    npm start
    
    # open a web browser up and point it to:
    #     http://localhost:8080/
    #
    # for barrels of fun, open more than one tab to the
    # link and talk to yourself :)



## Begin tl;dr



### Preface: The soul of a front-end engineer (or the attitude to foster, even if you aren't one)

My guess, is that if you interview what people today are calling front-end engineers, which is usually a euphamism for JavaScript progammers, most of us became this way almost by accident. Some of us, like me, who were on the bottom of the engineering chain, and wanted to move up, found ourselves in the following situations:

    [manager type person] "Hey, we need some work done on this demo."
    
    [me, overly-enthusiastic] "Okay, I can do it!"
    
    [manager type person] "We need to show how we can do this awesome foozle functionality all on the web, and it needs to work in IE6."
    
    [me, still overly-enthusiastic] "Duh, of course I can do it. I'll have it to you by tomorrow."

Of course I didn't have it done by tomorrow, but no one else would take the project, and this ensured my education on the job. At some point over the last few years, those of us willing to self-educate in the world of the Web have found ourselves in the post-time of a bizarre singularity: there is really too much work out there and not enough front-ender engineers to take the jobs. We likely have the iPhone and the iPad to thank for that, even if we're not coding Objective-C.



### Round 1: The Developer Experiment: making sure chatty works

The web is paved with good intentions, and these good intentions need sanity checking. So let's make sure we're sane and make sure our chat code works:

    # Since we're going to push our app to OpenShift
    # and because OpenShift most awesomely uses git
    # for code management and deployment, we will
    # use the base application repo as our testing
    # ground.
    rhc app create chatty nodejs-0.6
    cd chatty
    
    # write the client code on top of our current code
    # at the relevant place in our code. We'll have a
    # few file artifacts from the default OpenShift
    # app skeleton, but they won't hurt anything.
    git remote add -f chatty-upstream git://github.com/jeremyosborne/chatty.git
    git branch chatty-upstream chatty-upstream/master
    git merge -Xtheirs v0.1.0
    
    # For local testing, let's pull down the npm modules
    # we need.
    npm install
    
    # Startup the server.
    node server
    
    # Open a browser or two to the following URL:
    #     http://localhost:8080/
    # It's more fun if you open a couple of tabs and
    # have a conversation with yourself.

It's a wonderful moment when things work. An overview of the pieces we have playing together:

* **Server code**
    * Node.js is our server platform, hence all code is in JavaScript.
    * npm, while not directly running, downloads and installs our server side dependencies. We manage our dependencies via the `package.json` file.
    * [express](http://expressjs.com/): A light, web application framework, looking to be the Django or Rails of Node.js.
* **Client code**
    * Good old (and new) JavaScript, CSS, and HTML.
    * [jQuery](http://jquery.com/): Sort of de facto for simple DOM manipulation.    
    * NOTE ABOUT OLDER BROWSERS: This is sample code. I have not tested a plethora of browsers, and have not tested IE6-8. I don't really care about those browsers, so requests for code fixes there will likely be ignored.
* **Client+Server code (or code shared by both parts of the application)**
    * [socket.io](http://socket.io): An implementation of the idea of a socket for the web, not necessarily just for WebSockets. There [are browsers](http://caniuse.com/#feat=websockets) that can't use WebSockets as is, so it's good to have a fallback if necessary. Additionally, given WebSockets are a different protocol from HTTP, it saves me having to implement the new protocol by hand (as it is not supported by default in Node.js).
    * [Handlebars](http://handlebarsjs.com/): String templating is all the rage. [There](http://twitter.github.com/hogan.js/) [are](https://github.com/flatiron/plates) [many](https://github.com/janl/mustache.js) [options](http://akdubya.github.com/dustjs/) [out](http://underscorejs.org/#template) [there](http://embeddedjs.com/), but I prefer Handlebars.
    * Some simple JavaScript objects to wrap our User and Message data.



#### npm and Package.json

I've known about Node.js for most of its life, and I remember using a really, really early build. It was impressive even then, except for one things: I didn't want to write everything from scratch everytime I wanted to develop a web application. Along comes npm, and not only does it make it easy to install third-party packages we need, it really does make our project easier to manage. A quote from the article [Introduction to npm](http://howtonode.org/introduction-to-npm) sums up the real way to use npm:

> npm is a development tool, first and foremost. 
<br>
<br>
> People sometimes say "Yeah, I haven't gotten time to check out that package manager stuff yet. Maybe I will when my code is more stable."
<br>
<br>
> That's like saying that you're going to start using source control when your code is done. It's just silly. Source control should make your process easier, and if it doesn't, then you're using a broken SCM. Same for package management. It should make it easier, and if it doesn't, then something is wrong.
<br>
<br>
> npm isn't "for" publishing. That's just something it can do. It's "for" playing. That's why I wrote it: to play with your code, without having to remember a dozen different ways to install your stuff, or having to get you all to structure your code the same way.
<br>
<br>
> It's supposed to make the process funner.

Taking a look at the `package.json` file, there's a few things to note:

    {
        "name": "Chatty",
        "version": "0.1.0",
        "description": "Chatty, a simple web chat room",
        "keywords": ["OpenShift", "Node.js", "application", "socket.io", "MongoDB"],
        "homepage": "https://github.com/jeremyosborne/chatty",
        "author": {
            "name": "Jeremy Osborne",
            "email": "jeremywosborne@gmail.com",
            "url": "http://jeremyosborne.com/"
        },
        "repository": {
            "type": "git",
            "url": "https://github.com/jeremyosborne/chatty.git"
        },
        "engines": {
            "node": ">= 0.6.0",
            "npm": ">= 1.0.0"
        },
    
        "dependencies": {
            "socket.io": "0.9.x",
            "express": "3.x"
        },
        "devDependencies": {},
        "bundleDependencies": [],
    
        "private": true,
        "main": "server.js"
    }

1. You might want to mark the code as yours. Feel free to change the `author` block around and take all the credit. You have my permission.
2. The `engines` block declares that this code will only run on a Node.js version greater than 0.6 and when using npm greater than version 1. The `package.json` file can help us protect our code from being deployed in the wrong environments.
3. The `dependencies` block is perhaps the most important and useful block. This contains the names of the npm packages that we wish to include in our project, and the version, or relative version, that we'll accept. When we earlier ran `npm install`, npm actually looks at our `package.json` file and reads out the dependencies block. It then finds the packages that conform to our version requirements, as well as installing all of the dependencies that those packages also depend on. If you expand this code, the first thing you'll probably want to do is extend the dependencies block and when done, do another `npm install`.



#### The .gitignore file

If you're on a Unix-like system, and you're not familiar with git, you might have missed the `.gitignore` file that got included in the code merge. There's very little in the file, except one thing of note:

    .DS_Store
    .project
    .settings
    node_modules

The `node_modules` directory is where npm installs our modules locally during an `npm install`. Modules installed in this directory are painlessly made available to the `require("library")` statement. However, I don't view them as necessary for checkin, and thus the directory is ignored. As we'll see later, thanks to the deploy process of OpenShift, there's no need to checkin our 3rd party code into our own private code branch if we don't want to.



#### Running server-dot-js: Handling HTTP requests with express

The heart, and sometimes heartbreak, of web applications lies in the processing of HTTP requests in some web service. Node.js out of the box gives us the basics to do every bit of web-schtuff that we might need. But writing, and rewriting, low level code whenever we want to create something slightly different can get tiresome. Node.js without npm is like the Python/C API without the Python. For our example, even though it's a bit overkill, we delegate the handling of basic HTTP requests to the express framework and mainly forget about the low level request/response objects, asynchronously reading files off the disk, and all the other stuff. 


On our tour through the code, lets take a look at the current `server.js` file, lines 16-32:

    // Setup a very simple express application.
    app = express();
    // The client path is for client specific code.
    app.use('/client', express.static(__dirname + '/client'));
    // The common path is for shared code: used by both client and server.
    app.use('/common', express.static(__dirname + '/common'));
    // The root path should serve the client HTML.
    app.get('/', function (req, res) {
        res.sendfile(__dirname + '/client/index.html');
    });
    
    // Our express application functions as our main listener for HTTP requests
    // in this example which is why we don't just invoke listen on the app object.
    server = require('http').createServer(app);
    server.listen(PORT, IPADDRESS);

It's not what we did write, it's what we didn't have to write.

I often like to play the game with the classes I teach called, "Can you explain this code/technology to my mother?" My mom is really smart, you can use big words, but she's not a programmer. If I were going to explain this to my mom, I'd say that the above:

* Takes any request whose URL path begins with `/client`, like `http://localhost:8080/client/code.js`, maps the request into the local `client/` folder on the file system, and attempts to serve any static files that match the remainder of the path, like here `code.js`. Useful for serving the couple of pieces of client code we need that don't live in the index.html file.
* Takes any request whose URL path begins with `/common`, like `http://localhost:8080/common/handlebars.js`, maps the request into the local `common/` folder on the file system, and attempts to serve any static files that match the remainder of the path, like here `handlebars.js`. Useful for giving the web browser access to the JavaScript files that are also used by our server code (aka. the common code).
* If the request is only for the root path, like `http://localhost:8080/`, serve up the `client/index.html` file. If we want people to join our chat, we need to give them the root URL to our web host for now, unless of course you change it.

The express `app` that we created above is passed off as the handler to a Node.js HTTP Server instance. Why we build the server separately versus just calling `app.listen()` is because we need to augment the server to handle our WebSockets.



#### Handling the socket connections with socket.io

While express is responsible for catching the standard HTTP requests, we wrap our server with the socket.io framework. This framework goes above and beyond the basic [WebSocket API](http://dev.w3.org/html5/websockets/) for a couple of reasons:

* There are a good number of older browsers that don't support WebSockets, or don't support them correctly.
* Implementing the WebSocket protocol on the server is not usually an out of the box solution, because WebSockets !== HTTP. So we need logic that can handle both HTTP requests and WebSockets.

What's nice with socket.io is that we can code our app and (sort of) forget the transport protocol being used. In general things just work.

The majority of the server-side work and code happens inside of lines 84-156 of the `server.js` file, which I will include, but trim up a bit for this discussion:

    // socket.io augments our existing HTTP server instance.
    io = require('socket.io').listen(server);
    // Called on a new connection from the client. The socket object should be
    // referenced for future communication with an explicit client.
    io.sockets.on('connection', function (socket) {
        
        // The username for this socket.
        var user = User();
        
        // ___snip some stuff that appears in the code and is not copied here___
        
        socket.on('chat', function (data) {
            // Message passed by a client to the server with the intent of
            // broadcasting to the chatroom.
            if (data.user && data.user.name == user.name) {
                io.sockets.emit('chat', Message(data.message, user, "chat"));
            }
            else if (!user.name) {
                // Do not allow people to communicate without having a saved user
                // name.
                socket.emit('chat', Message(messages.invalidRequireName(), User('server'), 'error'));
                disconnectSocket();
            }
            else {
                // Do not allow people to change their name in the request alone
                // on this socket. (Example of a very pathetic amount of security).
                socket.emit('chat', Message(messages.invalidNameChange(), User('server'), 'error'));
                disconnectSocket();
            }
        });
    });

A chatroom is a great counter example to the default stateless behavior of the Web and HTTP requests. The server does actually need to save a reference to a user connection when they log in to the chatroom, because when another user logs in, and sends a message, we want to relay that message in as-real-time-as-possible to each of the other logged in users.

The two most important identifers above are `io` and `socket`. 

The `io.sockets` identifier points to what is essentially a pool of the current connections being serviced by this server. There is a lot of logic built in to help keep state on the server side. For example, one browser support WebSockets, but another browser need to connect via long-hanging XMLHttpRequests, we as socket.io developers don't really need to think about it. Since Node.js doesn't bleed global variables like JavaScript in the web browser, we make the `io` a module scoped variable, and thus `io.sockets` is available module wide.

Like most everything in the JavaScript world, the collection of `io.sockets`, as well as each individual `socket` representing a single connection to a specific user, emit events. In the code snippet above, there are two events being subscribed to:

* An `io.sockets` connection event: Emmitted when a new TCP/IP connection is made, and each time the callback is called it signals, "Another use has joined our chatroom."
* A `socket` chat event: This is called when a logged in user sends an event. We have a very minor amount of code to keep things from exploding, but in general, things will be running smoothly. If everything is kosher, we take the chat message from a particular user and broadcast it out back to everyone who is connected.

While this code, and probably all sample code on the planet, could always use some refactoring, we take advantage of JavaScript's expression of [closure](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work) to reference enclosed variables in the the server.js file. It's why JavaScript can be so hard sometimes, and at the same time so concise and easy to write.



#### String templating, it's what the cool kids are doing

I could also have said, "[MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller), it's what the cool kids are doing," but this code is a poor demonstration of any form of well adhered to MVC approach that is all the rage today. While I don't always MVC my code, I always like to separate concerns as much as is necessary to keep me a happy programmer.

One thing that crops up again, and again, and again in web prorgams is templating of output information. In general, the templates represent HTML to be rendered by a web browser, but sometimes even general messages are templated out.

Since this is a totally fun and contrived example, I took a tenet of what people love to say about Node.js, that it's just JavaScript, and took a look at how could I reuse the same code on both the client and the server. One way I could do that would be to use Handlebars and template out my server and HTML templates using the same bit of code.

Handlebars templates use the popular double-curly brace syntax to mark off search and replace markers in the string. For example, on our server we have the `leftChatRoom` message which is defined in the `server.js` file as:

    {{name}} has left the chatroom.

And on the client we have the generic message HTML output, that is defined in the `client/index.html` file as:

    <li class='message {{type}}'>
        ({{user.name}}): {{message}}
    </li>

The idea of these templates is to build JavaScript objects that will have property names that get mapped into the template.

But at the end of the day, what templates allow us to do is something that we've been doing for years anyway: get various formatting strings and layout strings out of our code. And what's also cool is that our server uses the exact same template engine code (found in `common/handlebars.js`) as our client, no separate code bases needed.

An aside before we move on: I know npm offers a [named handlebars package](https://npmjs.org/package/handlebars), but I wanted to see if standard Handlebars code ran without modifications. It didn't, which is not surprising (it was a scoping issue), so I changed one line of code at the top of the file. It's a very minor change and I commnented it at the top of the file.



#### The common code

There is nothing special about the naming convention for my `common/` folder. I just named the folder this way to remind myself of code that will end up in a `require()` statment on the server-side and can also end up in a `script` tag on the client side. Code in here should, and is, designed to deal with the differences in variable scoping found in Node.js vs. the web browser.

A general, simple way to organize your code is to know the following:

    // The following is true in the global scope on a web browser
    // that is not in global strict mode.
    if (typeof window != "undefined") {
        console.log(this === window); // true
    }
    // The following is true in the module scope found on Node.js…
    if (typeof exports != "undefined") {
        console.log(this === exports); // true
        // …however, in a file, remember this is not true
        console.log(this === global);  // false
    }
    


#### The client code

Finally there is our client code. While there is a bit of [HTML DOM](http://en.wikipedia.org/wiki/Document_Object_Model) stuff facilitated by jQuery in the code, the majority of the work is servicing the socket.io connection.

IMO, what's nice about socket.io is how the client and the server API is expressed in very much the same way. Since the client deals with only one connection and not a pool of them, the API is a sebset.

For example, in line 4-12 of `client/code.js`, we can see how the client opens a connection to the server and sets up one of many listeners to server emmitted events:

    // What is the base URI for the socket?
    var socketURI = '/';
    // We use socket.io as our bridge. It will sort out what sort of
    // connection we're going to use.
    var socket = io.connect(socketURI);
    // These are events reserved by socket.io, and we listen in.
    socket.on('connect', function() {
        messages.info('connection to server established.');
    });

When we want to send a message to the server, we `socket.emit` (lines 128-148):

    $('#status-update-form input[type="text"]').on('keydown', function(e) {
        // The context of the message is determined by our state.
        var message = $(this).val();
        
        // Send a message to everyone on the return key.
        if (e.which == 13 && message && socket.socket.connected) {
            if (!user.name) {
                // Attempt to name ourselves first.
                user.setName(message);
            }
            else {
                // Normal message broadcast.
                socket.emit('chat', {
                    'user': user,
                    'message': message
                });
            }
            // Clean out the value for the next input.
            $(this).val("");
        }
    });

When emitting an event, the name of the event is always the first argument, and the data to be passed is the second argument. While all messages passed back and forth get transmitted as text, socket.io will unserialize the objects for us and make them available in their native JavaScript form. Use [JSON](http://json.org) friendly objects to make sure things work smoothly.



### Round 2: Host this baby in the cloud!

If the app works, [time to test the code in production](http://www.troll.me/2011/05/15/the-most-interesting-man-in-the-world/i-dont-always-test-my-code-but-when-i-do-i-do-it-in-production/)! To do that, let's update our code a tiny, tiny bit to work with OpenShift:

    # Merge the next chunk of code.
    git merge -Xtheirs v0.1.1

We changed a whopping two lines of code at the top of `server.js` for deployment of our application to OpenShift: the port we listen on and the ip we are bound to. That, to me, is darn impressive, and one of the reasons I've so far enjoyed my experience with OpenShift.

    var PORT = process.env.OPENSHIFT_INTERNAL_PORT || 8080;
    var IPADDRESS = process.env.OPENSHIFT_INTERNAL_IP || '127.0.0.1';

The `process` object is a global object in Node.js that, among other things, makes an associative array of environment variables available to use via the `process.env` object. The nice thing about JavaScript is that accessing properties on objects that don't exist just return `undefined`, they don't throw, so the code will work just fine whether we test locally or we are deploying our code to OpenShift. 

And talk about deploying, it's easy. We use git to push our code up the repo. Even better, there's a lot of procedure wrapped up in the deploy process. If you haven't yet, you really should read the `README` file that is part of the skeleton repo, and poke around inside of the `.openshift` directory. 

But, for the most part, we don't have to think about any of that. All we have to do at this point is:
    
    # From our chatty directory, push to production!
    git push
    
If you like watching the logs of a process like I do (at least once so I get familiar with things), you'll see we're not pushing anything that was `npm install`ed with our code, and that we don't need to becaus the server will `npm install` everything we need based on what is found inside of `package.json`. I won't belabor this particular topic here, but I did want to call it out as one of those, "That was easy," moments.

If all went well, we should see some final set of messages like:

    remote: hot_deploy_added=false
    remote: Done
    remote: Running .openshift/action_hooks/post_deploy
    To ssh://d8174fe9e92140399f7ebcfca723e48b@chatty-jeremyosborne.rhcloud.com/~/git/chatty.git/
       eb36895..b9cf0a5  master -> master

Let's check out our application in all of its glory:

    # Get the URL of our application from the commandline,
    # in case we forgot it:
    rhc domain

You should get output that looks something like:

    Applications in jeremyosborne
    =============================

      chatty @ http://chatty-jeremyosborne.rhcloud.com/
      =================================================
        Application Info
        ================
          Created   = 9:15 PM
          UUID      = d8174fe9e92140399f7ebcfca723e48b
          Gear Size = small
          Git URL   = ssh://d8174fe9e92140399f7ebcfca723e48b@chatty-jeremyosborne.rhcloud.com/~/git/chatty.git/
          SSH URL   = ssh://d8174fe9e92140399f7ebcfca723e48b@chatty-jeremyosborne.rhcloud.com
        Cartridges
        ==========
          nodejs-0.6

Take the URL that follows the `chatty @` header and plug it into your browser. (NOTE: Please don't go to my URL because there is a real chance the app is not up and running, and you'll miss the next *fun* part of the example).



#### sorry, we are experiencing technical difficulties

If you are like me, the first time you checked in the code, you tested things in Chrome. And you got the message that heads up this section. And you thought, "What gives?"

Life on the web has many moments like this. Things work on your development box. They work on your favorite web browser. You *really did* test the code. You might have even written unit tests. Push to production and BAM! Something goes wrong. Let's see if we can figure out what's happening:

    # If you forgot the SSH URL to your OpenShift instance, run:
    rhc domain
    # SSH in to your box (please don't cut and paste the following
    # it is only a guide)
    ssh d8174fe9e92140399f7ebcfca723e48b@chatty-yourusername.rhcloud.com 
    
    # Let's poke around on the box…
    ls
    ls nodejs-0.6/logs
    tail -f nodejs-0.6/logs/node.log
    
    # Refresh the web browser and watch.
    # You should see some error in the log that looks something like
    #
    # debug: setting request GET /socket.io/1/websocket/zeo3oelooJnu73JECdEl
    # debug: set heartbeat interval for client zeo3oelooJnu73JECdEl
    # warn: websocket connection invalid
    # info: transport end (undefined)
    
But why? Well, the answer is pretty easy: at the time of this writing, [WebSocket support is still new on OpenShift](https://openshift.redhat.com/community/blogs/paas-websockets). But this is not a problem. In fact, life on the web has so many ups and downs, we even have terminology for supporting new technology and boot-strapping technology to older browsers.



### Round 3: Progressive enhancements and graceful degredations

If you have followed all the steps up until now, our code works fine when hosting locally, and pushing-to-production pointed out a problem that makes us step back and tweak a few things. The following is my preferred solution right now, and one that makes me happy:

    # Merge the next chunk of code to our code.
    git merge -Xtheirs v0.1.2

    # We have a setup change, please also run:
    npm install
    
    # And now to test locally, please run command with options:
    node server.js --websocket-port=8080 --log-level=1

How we know stuff works: the client code should operate just like it did before. But now we get to pass in commandline options as a bonus.



#### Accepting commandline parameters

If you open up the `package.json` file, you will see one of the changes is the addition of a dependency named [optimist](https://github.com/substack/node-optimist). There are some classic mistakes that all of us engineers make from time to time, and one of the most classic ones I know is building our own commandline parsers. Don't do it. Go find one that is pre-built, and that's what we're doing here.

We've added to possible parameters to our application:

* `--websocket-port=8000` which we can pass in to allow us to redirect incoming websocket traffic to a particular port.
* `log-level=1` or 0, or 2, or 3. 0 is the lowest level, which will only show errors, 1 will show warnings, 2 will show info, and 3 (the default) will show debugging information. If this parameter is not passed in, it still defaults to 3.

This will make our deployment logs more manageable, and will allow us an easy way keep the log level high when running locally.

The actual changes to our code in `server.js` are very minor.

We get the commandline parameters, parsed by optimist into an associative array, on line 13:

    var argv = require('optimist').argv;

We rig up a separate endpoint that is used to configure our incoming port on line 18-25, making use of express again:

    // How we pass our websocket URL to the client.
    app.use('/varSocketURI.js', function(req, res) {
        var port = argv['websocket-port'];
        // Modify the URI only if we pass an optional connection port in.
        var socketURI = port ? ':'+port+'/' : '/';
        res.set('Content-Type', 'text/javascript');
        res.send('var socketURI="'+socketURI+'";');
    });


and then configure the `io` object in code we inserted at lines 94-98:

    io.configure(function() {
        // Logging: 3 = debug (default), 1 = warn
        var logLevel = (argv["log-level"] === undefined) ? 3 : argv["log-level"];
        io.set("log level", logLevel);
    });

Among other things, optimist correctly parses our log-level into a number so we don't need to worry about parseInt-ing anything.

Our client code received a few changes. One is the addition of an extra script tag in the `index.html` file that is used to retrieve the `var socketURI` from the `/varSocketURI.js` endpoint. Our script tags are highly unoptimized, so I didn't feel adding one additional one in our example code was cause for any alarm.

Finally, we also removed the older, embedded `var socketURI` from the `client/code.js` file. With those changes to our code, we're very close to being done.



#### Addition of a startup command

One additional change in our `package.json` is the addition of a startup script:

    "scripts": { 
        "start": "node server.js --websocket-port=8000 --log-level=1" 
    },

OpenShift respects the start command and will run what is in our `package.json` file (via `npm start`) instead of some other hard coded script. That is fab, I love when things are configurable.



#### Push here to test in production

When you're ready, do a `git push` and then check out your application on OpenShift. The initial error should be gone, and things should be working as usual. Nice work.



### Conclusion of the tl;dr

Life on the web is a lot of fun and buzzwords like Canvas, WebSockets, and WebGL. It's also filled with other sayings like progressive enhancment, graceful degredation, and "I'm only going to support IE6 if you double my salary."

If you're new to Node.js or OpenShift or WebSockets, we covered a lot of ground in all of these areas. I'd like to take a little bit and point out some areas that, if you are interested in this demo code, you could improve (aka. good educational opportunities for those new to JavaScript or Node.js):

* The JavaScript files are not packaged (not concatenated, minified, or obfuscated). Check out [uglify.js](https://github.com/mishoo/UglifyJS).
* The code could use a port to some MV* framework, and I would definitely do that before expanding the code base anymore. Should happen on both the client and the server code, and with a bit of care you could use the same MV* framework in both places.
* I use Handlebars for templating because I like it, but at the current code level it is a bit overkill, especially for the server side messages. It does provide some escaping of HTML on the client which provides a low level of protection against people putting HTML/script in the messages, but other than that, overkill. Maybe micro-templating?
* There is very little protection against any sort of XSS or CSRF, aka. this code is not very secure.
* It would have probably been better to filter the server messages into different channels from the usual chat channel. This would make it easier to control the message flow in the code directly vs. process the message objects. For this simple example I chose the less convoluted (to me) approach by keeping the code light and having all Message objects describe what type of message they were especially since this matched with how persistent data was stored and referenced.

As with anything, good luck with your own coding. It's what you do with your own code that makes the difference. Take care, and have fun.



## Appendix: Anticipated Questions

* Oops I forgot the git repo to my chatty app and I'm on another computer?
              
        rhc app show chatty
        # Look for the line in the output that shows the git URI for chatty.
        git clone ssh://really.long.URI.to.chatty.app

* I've made a couple of apps, and I want to see a list of them all:
           
        rhc domain show
           
* I want to delete my chatty app because (insert reason here):
   
        rhc app delete chatty



## Appendix: Further Reading
* [Try Out Low-Latency Connections with WebSockets](https://openshift.redhat.com/community/blogs/paas-websockets)
* [NPM, a developer tool](http://foohack.com/2010/08/intro-to-npm/)
* [Setting up Node.js on OpenShift](https://openshift.redhat.com/community/blogs/nodejs-on-openshift-you-bet-your-javascript)
* [Using a non-default Node.js on OpenShift](https://openshift.redhat.com/community/blogs/any-version-of-nodejs-you-want-in-the-cloud-openshift-does-it-paas-style)
* [Access the OpenShift shell via ssh](https://openshift.redhat.com/community/blogs/dive-into-openshift-with-ssh)
