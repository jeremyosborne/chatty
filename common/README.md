# Common code.

It's fun to be able to share code between client and server. The code in this 
directory does just that.

We take advantage of the fact that the following is true:

    // On a web browser, the following will be true in a global context:
    console.log(this === window);
    
    // In Node.js, the following will be true in a module context:
    console.log(this === exports);
    // While this is not equal to the global object in Node.js 
    // (in other words, the following is true):
    console.log(this !== global);
    
We use this trick to help us manage exporting in both environments without
the need for if or switch blocks, and in Node.js this also helps us not bleed
out of our modular scope.
