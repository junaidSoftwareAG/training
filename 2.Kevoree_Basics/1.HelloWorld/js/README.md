#### First step
Get `npm` (**npm** comes with **[node.js](http://nodejs.org/download/)**)

Install `Yeoman` ([Yeoman](http://yeoman.io/learning/index.html) is a project scaffolder)
```sh
npm i -g yo
```

Install the `Kevoree Yeoman` generator called `generator-kevoree`
```sh
npm i -g generator-kevoree
```
Scaffold your project where you want
```sh
# create a folder for your project
mkdir /tmp/HelloWorld
# cd into it
cd /tmp/HelloWorld
# ask Yeoman to generate the architecture of your Kevoree JS project
yo kevoree
```
> Follow the instructions:  
> [?] What kind of entity would you like to create? (comp|chan|group|node) **comp**  
> [?] Choose a name for this entity? (Java camel case naming convention) **HelloWorld**  
> [?] Choose a package name for your module? (i.e my.package.name) **my.package**  
> [?] Choose a module name: **kevoree-comp-helloworld**  
> [?] Do you plan on using this component with a browser runtime? **No**


#### Purpose of the HelloWorld component
The `HelloWorld` component has a basic behavior to help users understand the concepts and code/project structures of KevoreeJS.

**Component start:** supposed to say "Hello, world!"  
**Component stop:** supposed to say "Bye, world!"

This behavior is translated in a Kevoree component with this code:  
```js
var AbstractComponent = require('kevoree-entities').AbstractComponent;

/**
 * Kevoree component
 */
var HelloWorld = AbstractComponent.extend({
    toString: 'HelloWorld',

    start: function (done) {
        this._super(function () {
            this.log.info(this.toString(), 'Hello, world!');
            done();
        }.bind(this));
    },

    stop: function (done) {
        this._super(function () {
            this.log.info(this.toString(), 'Bye, world!');
            done();
        }.bind(this));
    }
});

module.exports = HelloWorld;
```

#### How to run the project
> - If you have not used `yeoman` to scaffold the project, then you **must** run `npm install` in order to install your project dependencies.  
>   In the HEADS IDA,  right-click on `package.json` and click `npm install`  
>   In a console, just `cd` to your project, and run `npm install`
> - You need [GruntJS](http://gruntjs.com/getting-started) to be installed:  
>   `npm i -g grunt-cli`

When you have `grunt` installed, do:
```sh
# from the command-line
grunt kevoree
```

**or in the HEADS IDE:**  
Right-click on `kevs/main.kevs` > Compile and Run as Kevoree Script

This command ask for `grunt` to run the `kevoree`  task.
The `kevoree` task is a way to automatically run a project using the `kevoree-nodejs-runtime` using a **KevScript** (and some tricks to allow you to test your code while developing it).  
By default, it will use the KevScript `kevs/main.kevs`.  
If you have a look at that KevScript file, you can see that it defines a Kevoree model using your own component "HelloWorld":
```txt
// the node that hosts your component
add node0 : JavascriptNode

// create and add your HelloWorld component (named "myComp") in "node0"
add node0.myComp : org.kevoree.example.HelloWorld

// set the node log level to 'info' so that we do not get the debug logs
set node0.logLevel = 'info'
```

Output of the `grunt kevoree` command:
```txt
╭─leiko@kevtop /tmp/HelloWorld
╰─➤ grunt kevoree
# ... many logs ...
14:08:41  INFO   HelloWorld       Hello, world!
>> Bootstrap model deployed successfully
```

If you can see **"Bootstrap model deployed successfully"** then you have successfully started a new KevoreeJS runtime using your `kevs/main.kevs` model.

#### Understand the HelloWorld component
KevoreeJS is all about **naming-convention**.  
Because Kevoree handles your components lifecycle, it has to know which methods to call.
So you have to follow the naming conventions in order for your component to be compliant with
the framework.  
When Kevoree will have to **start** your component, it will call
```js
start: function (done) {
  this._super(function () {
    // do what you want to do when your component starts
    // call done() when you are done starting
    done();
  });
}
```
When Kevoree will have to **stop** your component, it will call
```js
stop: function (done) {
  this._super(function () {
    // do what you want to do when your component stops
    // call done() when you are done stopping
    done();
  });
}
```

KevoreeJS knows several other `keywords` but they will be explained in a later tutorial.

Every KevoreeJS entity (components, channels, groups, nodes) share the same logger object.
You can access it by using `this.log` within your code:
```js
this.log.info(this.toString(), 'Hello, world!');
```

#### How to stop the runtime
In order to gracefully stop your KevoreeJS runtime you just have to do a `Ctrl-c`. This action will trigger a stopping adaptation in the KevoreeJS core which will ask for every components to stop, and then exit.

```txt
>> Bootstrap model deployed successfully
14:08:45  WARN   NodeJSRuntime    Got SIGINT.  Shutting down Kevoree gracefully... (^C again to force quit)
14:08:45  INFO   HelloWorld       Bye, world!
14:08:45  INFO   KevoreeCore      Platform stopped: node0

Done, without errors.
```
