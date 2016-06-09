---
layout: draft
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
We need only two collections `channels` and `messages`. Let's look at `collections/channels.js` first.

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

Now we'll be setting up our schemas. Schemas are pretty simple. We just need a name for our channel. The `_id` part is automatically taken care of by Meteor. Once this is setup we are all set after attaching our schema. Define the schema at `collections/messages.js`.

```
// Defining collection
Messages = new Mongo.Collection('messages');

// setting up allow and deny rules
Messages.allow({
   insert: () => false,
   update: () => false,
   remove: () => false
});

Messages.deny({
   insert: () => true,
   update: () => true,
   remove: () => true
});

// defining schema
let MessagesSchema = new SimpleSchema({
   // to avoid the need of extra schemas wr create two fields 'channel' and 'to'
   'channel': {
      type: String,
      label: 'The ID of the channel this message belongs to.',
      optional: true
   },
   'to': {
      type: String,
      label: 'The ID of the user this message was sent directly to.',
      optional: true
   },
   'owner': {
      type: String,
      label: 'The ID of the user that created this message.'
   },
   'timestamp': {
      type: Date,
      label: 'The date and time this message was created.'
   },
   'message': {
      type: String,
      label: 'The content of this message.'
   }
});

Messages.attachSchema(MessagesSchema);
```
As you can see in the comment to avoid the need of extra schemas we have defined `channel` and `to` fields. What we're doing here is where the message belongs based on whether the message is intended for the public or it is a direct message to another user. If the message is meant for the public channel then we are going to store its `_id` in `channel`. If instead the message is going to another user, we're going to store it in the `to`  field. The difference is subtle here but will shape up our database queries later on.

Now the schemas are defined, we need to work on signing up users. Our goal here is make sure our user has everything they need to map to our process for wiring up direct messages later.

Now we'll be creating a signup form where we'll be asking users email address, password, first name, last name and username. Let's take a look at HTML now and later on we'll be working on JavaScript. Insert this at client/templates/public/SignUp.html

```
<template name="signup">
   <div class="row">
      <div class="col-xs-12 col-sm-6 col-md-4">
         <h4 class="page-header">Sign Up</h4>
         <form id="signup" class="signup">
            <div class="row">
               <div class="col-xs-6">
                  <div class="form-group">
                     <label for="firstName"> First Name</label>
                     <input type="text" name="firstName" class="form-control" placeholder="First Name">
                  </div>
               </div>
               <div class="col-xs-6">
                  <div class="form-group">
                     <label for="lastName"> Last Name</label>
                     <input type="text" name="lastName" class="form-control" placeholder="Last Name">
                  </div>
               </div>
               <div class="form-group">
                  <label for="username"> Username</label>
                  <div class="input-group username">
                     <div class="input-group-addon">@</div>
                     <input type="text" class="form-control" name="username" placeholder="username">
                  </div>
               </div>
               <div class="form-group">
                  <label for="emailAddress">Email Address</label>
                  <input type="email" name="emailAddress" class="form-control" placeholder="Email Address">
               </div>
               <div class="form-group">
                  <label for="password">Password</label>
                  <input type="password" name="password" class="form-control" placeholder="Password">
               </div>
               <div class="form-group">
                  <input type="submit" class="btn btn-success" value="Sign Up">
               </div>
            </div>    
         </form>
         <p>Already have an account ? <a href="{{pathFor 'login'}}">Log In</a></p>
      </div>
   </div>
</template>
```
Restricting the entered username to only wanted literals. So what are wanted literals and how specifically do we want. So we want our code to do the followings:
* remove any punctuation like `#$@%`
* make sure all are lowecased
* remove all white spaces.
Writing the santiize username code at /client/modules/sanitize-username.js.

```
export default function(value) {
   return value.replace(/[^A-Za-z0-9\s]/g,'']).toLowerCase().trim();
}
```
This is single line function why make it a separate module. Well to maintain the reusability of the code.

Now let's get back to the templates folder and use this module. We head over to SignUp.js and let's put it to use.

```
import signup from '../../modules/signup';
import sanitizeUsername from '../../modules/sanitize-username';

Template.signup.onRendered( () => {
  signup({ form: '#signup', template: Template.instance() });
});

Template.signup.events({
  'submit form': ( event ) => event.preventDefault(),
  'keyup [name="username"]' ( event ) {
    let value     = event.target.value,
        formatted = sanitizeUsername( value );
    event.target.value = formatted;
  }
});
```
Notice the keyup event on our username field, it'll update the value of as the user types with the sanitized version of username.
Notice the first line, where we are importing the signup module, but we didn't write it. Actually
