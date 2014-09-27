# chatty

A very (very) simple chat server with web client interface.



## Errata for OpenShift blog readers

Below are some things that are easier to keep up to date here as I can update these docs faster than others.

### When testing locally, please use `node server` not `npm start`

It appears that OpenShift is still running WebSocket connections through port 8000. The `npm start` command is made for the uber awesome OpenShift automation monkeys that autostart our app when we checkin.

If you are following along in the article, `node server` is what you want to use to test on your localhost, not `npm start` unless you are proxying your WebSocket connections through port 8000 (which you likely aren't).

### Use the tips of this code repo

If you are going through the article I recommend that your final stop be the tips of the master branch, not the tags referenced in the article. In other words, if you want just the tips and you want them immediately:

    rhc app create chatty nodejs-0.10
    cd chatty
    git remote add -f chatty-upstream git://github.com/jeremyosborne/chatty.git
    git branch chatty-upstream chatty-upstream/master
    git merge -Xtheirs chatty-upstream
    git push

### Consider using a newer version of Node on OpenShift

This looks cool: https://github.com/ramr/nodejs-custom-version-openshift

## Article local to repo

I've placed the article that spawned this code in the *openshift_nodejs_chatty.md* file. It contains walkthrough information, short and long form, and is likely why you are here.



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
