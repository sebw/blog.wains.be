---
date: 2020-03-20
title: "A Telegram Chatbot for Kanboard with NodeRED"
---

### Context

I've used a number of todo list applications over the years.

I have recently decided to use a simple kanban with three columns (todo/wip/done) as my todo list, and it works wonders.

I self-host a Kanboard instance, so I have full control over my lists.

In my article about my desktop environment, I cover how I even [integrated Kanboard in my i3 bar](https://blog.wains.be/2019/2019-12-11-my-linux-desktop-environment/#todo-list).

I no longer depend on some vendor that could shut down the service as they please.

Obviously, mobile apps are the strong point of proprietary solutions, while the [Android app for Kanboard](https://f-droid.org/en/packages/nl.patrickkostjens.kandroid/) is not as advanced.

I wanted to implement an easy way to add tasks to my Kanboard, so how about using a Telegram bot?

Here we go!

### Requirements

- a Kanboard instance, obviously
- an API token for Kanboard (Settings > API)
- a NodeRED instance
- `node-red-contrib-telegrambot` version 7.1.1+
- a Telegram account

### The workflow

![](https://blog.wains.be/images/nodered/workflow.png)

### Create the bot

Use [BotFather](https://core.telegram.org/bots#6-botfather) to create your new bot.

Configure the bot:

![](https://blog.wains.be/images/nodered/bot.png)

### Nodes

The query to Kanboard API should be done with the http request node:

![](https://blog.wains.be/images/nodered/query.png)

`getAllTasks` node:

```
msg.chatId = msg.payload.chatId;
var item = msg.payload.content.replace(/^\s+/g, '');
msg.payload = "{\"jsonrpc\": \"2.0\", \"method\": \"getAllTasks\", \"id\": 1, \"params\": {\"project_id\": 6}}";
return msg;
```

`createTask` node:

Change the project ID number.

```
msg.chatId = msg.payload.chatId;
msg.item = msg.payload.content.replace(/^\s+/g, '');
msg.payload = "{\"jsonrpc\": \"2.0\", \"method\": \"createTask\", \"id\": 1, \"params\": {\"title\": \"" + msg.item + "\", \"project_id\": 6}}";
return msg;
```

`Generate the reply list`:

Change the column ID.

```
var items = msg.payload.result;

msg.payload.chatId = msg.chatId;
msg.payload.type = 'message';
msg.task = [];

// this will generate an array with "null" for tasks not in column 20
for(var i = 0; i < items.length; i++) {
   if(items[i].column_id === '20') {
     msg.task[i] = "- " + items[i].title + "\n";
   }
}

// remove null entries
msg.cleaned = msg.task.filter(n => n)
// turn array into string
var list = msg.cleaned.join("");
// message
if(list === "") {
    msg.payload.content = "Nothing in the list."
} else {
msg.payload.content = list;
}

return [ msg ];
```

`Generate the reply todo`:

If you make your board public, you can share in the reply.

```
msg.payload.chatId = msg.chatId;
msg.payload.type = 'message';
msg.payload.content = 'Added "'+ msg.item + '" to your todo list.\n\nFull list at https://kanban.example.org/public/board/XXX';
return [ msg ];
```

### Result

![](https://blog.wains.be/images/nodered/telegram.png)