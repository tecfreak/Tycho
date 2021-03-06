Tycho
==========

**NOTE: As of (12/3/2012, v0.0.2), this README contains information that is out-of-date.  New documentation is forthcoming.**

A simple real-time multiplayer game framework for node.js.

```js
var tycho = require('tycho'),
	server = tycho.createServer();

server.listen(3000);
```

Tycho handles all of the real-time communication and architecture that is common across the majority of multiplayer games.  The purpose is freeing developers to focus
on writing their game, rather than worrying about managing all client-server interactions.

## Background

At its core, Tycho maintains three levels of granularity - server, instance, and connection.  A connection encapsulates an individual client or user, and handles communication between
server and client.  Depending on the type of game, an instance would likely represent either an instance of the game world (where the world is persistent, e.g. MMO) or a collection
of users (e.g. board game, lobby-based game).  Tycho is written is such a way as to provide as much flexibility as possible when structuring your game code.

## How to use

```tycho.createServer``` is the main entry point for the Tycho framework, and is likely one of only a handful of places where you would need to reference tycho directly.
Hooking your game logic into tycho is as simple as passing a few configuration parameters:

```js
tycho.createServer({
	maxConnections: 100,
	binding: {
		connection: User,
		instance: Game
	}
});
```

In this example, ```Game``` and ```User``` would be constructors of the user-defined classes that wrap the two tycho constructs.  Whenever tycho creates a new connection
(i.e. a new user connects), for example, an instance of the User class would also be created and automatically hooked into the tycho connection.  Any message or data from the client
is automatically routed to the User instance by tycho.

## Messaging

Tycho comes bundled with a default Messages class, but users are free to use any that they prefer.  Refer to the documentation for more details on specifying a custom messaging
interface.

Tycho's messaging interface looks like the following:

```js
// Server
tycho.messages.define({
	move: {
		init: function () { },
		validate: function () { },
		serialize: function () { },
		callback: function () { }
	},
	...
});

// Client
tycho.messages.accept('move', function (data, callback) {
	// Do stuff with the data
	// ...
	// Alert the server that you are done handling the message
	callback();
});
```

Of these four parameters, only serialize is required.  init is called each time a new message of the defined type is created, and establishes some default state for the other methods.
The validate method returns a boolean indicating whether the message should be sent, and serialize determines the actual data to send.  A callback can be provided and would be executed
when the message is finished being received.

## Client

Tycho provides a simple client-side library for interfacing with a tycho server.  In addition, the server-side messaging interface used by tycho is also available on the client
to provide consistency in message definitions.  All client-side code is purely optional and is provided to simplify getting started with tycho.

## Documentation

Full documentation can be found at: http://pbeardshear.github.com/Tycho

## Tycho?

Back in 1994, a small Macintosh developer named Bungie released Marathon, the first in a trilogy of first-person shooter games.  The story of Marathon centers around
a security officer (you), and a rampant Artificial Intelligence named Durandal bent on escaping the eventual end of the universe.  In doing so, he leads an enslaved alien
race in a rebellion against their captors, who originally destroyed the ship you and Durandal were aboard and enslaved one
of the other AIs that was aboard the ship.  The enslaved AI's name was Tycho.  Tycho doesn't figure into the story as prominently as Durandal, but whenever he does you know you
are in for a treat.  His sarcastic and witty remarks are a fine counterpoint to Durandal's more straightforward approach, a testament to the excellent writing done
throughout the games.  If you've never played a Marathon game before, you owe it to yourself to visit Aleph One (http://source.bungie.org/), a community continuation of
the Marathon game engine following its release to the public in 1999.
