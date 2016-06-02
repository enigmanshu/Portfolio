---
layout: post
title: Chat Application with Meteor
description: "Concepts and lessons that I learned while building chat application on meteor."
modified: 2015-05-28
tags: [chat, application, meteor]
image:
  feature: python-love.png
  credit: nylas.com
  creditlink: https://nylas.com/blog/packaging-deploying-python/
---
I am building a slack like chat application on meteor. This application will have following features:
* Channels
* Direct messages
* Channel can be created only by admin

### Defining Collections
We need only two collections `channels` and `messages`. Let's look at `channels.js` first.

```
Channels = new Mongo.Collection( 'channels' );

Channels.allow({
	insert: () => false;
	update: () => false;
	remove: () => false;
});

Channels.deny({
	insert: () => true;
	update: () => true;
	remove: () => true;
});

let ChannelSchema = new SimpleSchema({
	'name': {
		type: String;
		label: 'The name of the Channel.'
	}
});

Channels.attachSchema(ChannelSchema);
```
Here we are defining our collections and then locking down our allow and deny rules for security sake and for controlled access. Hence we locked these down on client and force all the database operations to take place on server i.e. we'll be depending on [meteor methods](http://docs.meteor.com/api/methods.html#Meteor-methods) to handle database operations on the client.

Now we'll be setting up our schemas. Schemas are pretty simple. 
