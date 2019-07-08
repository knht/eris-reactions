# Eris Reactions
**Eris Reactions** is a simple to use, lightweight and blazing fast **Reaction Handler** for the Node.js Discord library [Eris](https://github.com/abalabahaha/eris).

It supports two ways of handling reactions:
- Bulk collecting a specified amount of reactions over a set period of time
- Continuous listening for new reactions being added, which can be highly useful for implementing Embed pagination

The API is pretty straight forward and only requires the native event emitter as a dependency for blazing fast event emitting.

# Getting Started
Simply install Eris Reactions via NPM `npm install eris-reactions` and require it wherever you need it!

# API 
#### Bulk collecting a specified amount of reactions and a set maximum time:
```js
ReactionHandler.collectReactions(message, filter, options);
```
- **Eris.Message** `message` - *An Eris message emitted from the `messageCreate` event.*
- **Function** `filter` - *A filter function which is passed a **userID** as a Discord Snowflake.*
- **Object** `options` - *An object containing following options:*
    - **Number** `options.maxMatches` - *The maximum amount of reactions to collect.*
    - **Number** `options.time` - *The maximum amount of time the collector should run for in milliseconds.*

The result is an **Array** of `resultObject` containing all collected reaction results within the specification.
- **Object** `resultObject` - *The result object*
    - **Eris.Message | Object** `resultObject.msg` - The message on which the user reacted.
    - **Object** `resultObject.emoji` - *An emoji object containing:*
        - **String** `resultObject.emoji.id` - *The emoji ID (Which will be `null` for non-custom emojis).*
        - **String** `resultObject.emoji.name` - *The emoji name.*
    - **String** `resultObject.userID` - *The user ID of the user who has reacted (as a Discord Snowflake).*

<hr>

#### Setting up a listener for continuous Reaction listening:
```js
new ReactionHandler.continuousReactionStream(message, filter, permanent, options);
```
- **Eris.Message** `message` - *An Eris message emitted from the `messageCreate` event.*
- **Function** `filter` - *A filter function which is passed a **userID** as a Discord Snowflake.*
- **Boolean** `permanent` - *Whether or not the listener should stay attached or be automatically removed after use.*
- **Object** `options` - *An object containing following options:*
    - **Number** `options.maxMatches` - *The maximum amount of reactions to collect.*
    - **Number** `options.time` - *The maximum amount of time the collector should run for in milliseconds.*

In addition to that a temporary listener needs to be attached to listen for the `reacted` event:
```js
.on('reacted', eventListener);
```
- **String** `'reacted'` - *The event name. This **MUST** stay the same!*
- **Function** `eventListener` - *The event listener which is passed an event object.*
    - **Object** `eventObject` - *The object emitted from the `reacted` event*.
        - **Eris.Message | Object** `eventObject.msg` - The message on which the user reacted.
        - **Object** `eventObject.emoji` - *An emoji object containing:*
            - **String** `eventObject.emoji.id` - *The emoji ID (Which will be `null` for non-custom emojis).*
            - **String** `eventObject.emoji.name` - *The emoji name.*
        - **String** `eventObject.userID` - *The user ID of the user who has reacted (as a Discord Snowflake).*

# Examples
#### Bulk collecting reactions:
```js
const Eris = require('eris');
const ReactionHandler = require('eris-reactions');
const bot = new Eris('BOT_TOKEN');

bot.on('ready', () => {
    console.log('Ready!');
});

bot.on('messageCreate', async (message) => {
    if (message.content === '!test') {

        // This will collect exactly 5 reactions over the course of 1 minute
        const reactions = await ReactionHandler.collectReactions(
            message, 
            (userID) => userID === message.author.id, 
            { maxMatches: 5, time: 60000 }
        );
        
        console.log(reactions);
    }
});

bot.connect();
```
#### Continuously listening for reactions:
```js
const Eris = require('eris');
const ReactionHandler = require('eris-reactions');
const bot = new Eris('BOT_TOKEN');

bot.on('ready', () => {
    console.log('Ready!');
});

bot.on('messageCreate', async (message) => {
    if (message.content === '!test') {

        // This will continuously listen for 100 incoming reactions over the course of 15 minutes
        const reactionListener = new ReactionHandler.continuousReactionStream(
            message, 
            (userID) => userID === message.author.id, 
            false, 
            { maxMatches: 100, time: 900000 }
        );

        reactionListener.on('reacted', (event) => {
            message.channel.createMessage('You reacted with: ' + event.emoji.name);
        });
    }
});

bot.connect();
```
# License
This repository makes use of the [MIT License](https://opensource.org/licenses/MIT) and all of its correlating traits.

While it isn't mandatory, a small credit if this repository was to be reused would be highly appreciated!