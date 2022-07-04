## What the heck is Node.js?

www.nodejs.org

There was a time when the only place we could imagine javascript code could execute was in the browser. We could not leverage any capabilities it had anywhere else. Mostly Javascript was used to deal with frontend manipulation.

Then there came a time when [Dahl](https://en.wikipedia.org/wiki/Ryan_Dahl) created a software environment that allowed Javascript code to run on any machine as a standalone process.

This software environment is known as [**Node.js**](https://nodejs.org/en/).

Before Node.js we could only utilize JS to do what was allowed by the browser to be done. It could only handle stuff like click events, animations, upload files, etc.

But there was no way to use it like other general-purpose languages such as Java, C++, etc. It could not be used to build web servers or connect to databases.

With Node.js, it changes. Node enables Javascript code execution on machines, the same JS code that was used to run in browsers now can be used to build web servers, create command-line interfaces or manage database queries.

> I hope it clears what Node does which is:

> “It is an environment which allows Javascript code to run on servers as opposed to run it only on browsers”

### HOW?

We have seen what Node.js is and what it helps with. Now let’s have a look at HOW does it work to achieve the above-mentioned functionality.

First, let’s figure out how JS even works in web browsers?

Different browsers use different types of engines to execute Javascript e.g [SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey) used by Mozilla, [Chakra](https://github.com/microsoft/ChakraCore) used by Microsoft Edge or [V8 Engine](https://github.com/microsoft/ChakraCore) used by Google Chrome.

Node.js is a runtime environment built on V8 Engine which is the same engine used by Chrome to run Javascript. This means that it uses the same engine to execute Javascript code as used by Chrome. V8 is a Google open-source project.

**What does Engine Do?  
**JavaScript engine is responsible to take in JavaScript code and compile it down to machine code that your machine can actually execute.

As the V8 engine is written in c++ means any c++ application can incorporate and extend its functionality. That is how chrome and node both use it.

> One thing that is clear now is that :

> “Node is not a programming language but a runtime environment where JS runs. It provides add-ons and tools to a language to extend its functionality.”

Chrome v8 empowers JS developers to use different functions to handle events or manipulate DOM. These DOM-related functionalities are not required in Node as it does not run in a browser

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1655590100322/LHhQWpNfZ.gif)

Javascript running in the browser

Instead, it provides other features like access to the file system, creating servers to handle requests, etc.

In the end, all we use is simple javascript with added functionality.

**Behind the scene, its C++** Although Node developer is not required to have an understanding of C++, V8 Engine is mostly created in C++ so Node and Chrome are too.   
C++ bindings are the main source that allows Chrome and Node to interact with the DOM or to interact with your file system when neither is a part of Javascript.

When chrome runs a Javascript file, some C++ code gets executed behind the scenes which are responsible for taking care of the functionality.

Chrome is not just passing Javascript code to V8 it is also passing down these C++ bindings creating the context for which the Javascript code should be executed.

Javascript doesn’t know how to read a file from disk but C++ does.  
So when someone uses Javascript code and Node.js to read a file from disk it just defers all of that to the C++ function that can actually read the file from disk and get the results back to where they need to be.

Once again we have a series of methods that can be used in our Javascript code which are in reality just running C++ code behind the scenes.

That is the reason why the window object is accessible in the browser but not in Node because it is only implemented by the browser (As shown in the earlier gif). Same with process object that only exists in Node but not in the browser.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1655590103007/pV8dB1WOJ.gif)

Javascript running in Node

> “Node is able to teach Javascript new things by providing C++ bindings to the V8 Engine.”

This allows JavaScript in essence to do anything that C++ can do and C++ can indeed access the file system.

I hope it explains the fundamental concept of Node.js.

If you have found anything incorrect or you have any feedback regarding how may I improve this article let me know.