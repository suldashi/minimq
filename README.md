# MiniMQ

## A tiny message queue module for JS async operations.

### Installation

MiniMQ is usable both in the browser and Node.js, and can be installed in npm by using the following command:
`npm install minimq`

### Usage
Put the require statement somewhere in your code. `const MiniMQ = require("minimq");`  
An instance of MiniMQ is created by calling MiniMQ with the new keyword. `let queue = new MiniMQ();`

In a MiniMQ instance, you can push elements at any time, however, the queue at any time may be closed or open. If the queue instance is open, then the element will be passed to the handler function, and if the queue is closed, it will be backed up internally until the queue is opened again. When the queue is opened, the elements will be passed to the handler in the order in which they entered the queue.

The handler function should be set when an instance of MiniMQ is created. This function will handle all the elements that pass through the queue.

### Methods

**addElement(el)**

Adds an element _el_ to the queue. If the queue is open, the element will be immediately handled by the handler function. If not, it will be stored internally until the queue is open again. Returns a Promise that can e resolved or rejected by the handler function when the element is processed.

**openQueue()**

Opens the queue, allowing all subsequently inserted elements to be processed immediately by the handler function. If there are any elements stored internally in the queue, they will be passed to the handler function in the order in which they were inserted.

**closeQueue()**

Closes the queue, stopping all subsequent elements from being processed by the handler function. Instead, they will be stored internally in the queue until a later call of _openQueue()_. Note that all instances of MiniMQ start as closed by default, and must be opened explicitly before any processing can occur.

**handlerFunction(el,prm,resolve,reject)**

The handler function can be assigned to a user-defined function that handles the elements that come through the queue. 

Params:

* _el_ - the element that passes through the queue
* _prm_ - the Promise that was created by the addElement function. It is included here if you want to chain it once it resolves or rejects.
* _resolve_ - a function that when called, resolves the above Promise. It can also take a value parameter that will be passed to whoever called addElement.
* _reject_ - a function that when called, rejects the above Promise. It can also take a value parameter that will be passed to whoever called addElement.

### Example

```
let queue = new MiniMQ();	//by default the MiniMQ instance starts closed
queue.handlerFunction = (el,prm,resolve,reject) => {
	console.log(el);
	resolve();	
};
queue.addElement("foo");	//nothing happens at this point
queue.addElement("bar");	//nothing here either
queue.open()				//at this point, "foo" and "bar" are printed in the console, and the Promises that were returned by addElement are resolved
queue.addElement("baz")		//"baz" is immediately printed to the console, and the Promise immediately resolved
```

### Why I made this

I needed to send messages through a long-lived WebSocket connection, however, the connection would occasionally go down for a few seconds until it reconnected again. In this case, it was better that the client of the WebSocket object did not handle the logic to check if the connection was down or not. The client simply pushed the messages though WebSocket, and if the connection was down, the WebSocket library would hold on to the messages until the connection would be restored.

### Final words

Made by [Ermir Suldashi](https://suldashi.com)  
License: MIT