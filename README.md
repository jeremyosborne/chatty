# chatty

A very (very) simple chat server with web client interface.



## Errata for OpenShift blog readers

Below are some things that are easier to keep up to date here as I anticipate things changing ever more rapidly as Node moves ever forward and OpenShift looks to remain a solid platform (aka. older versions of node).

### When testing locally, please use `node server` not `npm start`

It appears that OpenShift is still running WebSocket connections through port 8000. The `npm start` command is made for the uber awesome OpenShift automation monkeys that autostart our app when we checkin.

If you are following along in the article, `node server` is what you want to use to test. The commandline args are different on purpose.

And, of course, if your kung fu is awesome, you'll notice the difference and can configure things to your hearts content.

### Use the tips of this code repo

If you are going through the article I recommend that your final stop be the tips of the master branch, not the tags referenced in the article, due to changes in OpenShift, Node.js, and Socket.io since the time of my writing the aritcle. In other words:

    rhc app create chatty nodejs-0.6
    cd chatty
    git remote add -f chatty-upstream git://github.com/jeremyosborne/chatty.git
    git branch chatty-upstream chatty-upstream/master
    git merge -Xtheirs chatty-upstream
    git push

### Consider using a newer version of Node on OpenShift

This looks really nice and painless: https://github.com/ramr/nodejs-custom-version-openshift



## Requirements

* node.js (assumes 0.6.0 or greater)
* npm (assumes 1.0.0 or greater)  



## Basic usage

    # all from the command prompt within this directory
    npm install
    # start the server piece
    node server.js
    # from here, load up a web browser and point it to
    #     http://localhost:8080/
    # and if you are secure in your sanity, open more than one
    # web browser and chat with yourself.



## Notes and Walkthrough

I've placed the article that spawned this code in the *openshift_nodejs_chatty.md* file. It contains walkthrough information, short and long form.
