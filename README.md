# egg-amqplib-tf

This module adds AMQP connection manager on the basis of egg-amqplib
Amqp plugin for egg with amqplib, Use connection manager to maintain MQ connection and support automatic reconnection！

## Chinese
[中文说明](README.zh_CN.md)

## Install
```
$ npm i egg-amqplib-tf --save
```

## Usage
```js
// config/plugin.js
exports.amqplib = {
  enable: true,
  package: 'egg-amqplib-tf',
};
```

## Configuration
```js
// {app_root}/config/config.default.js
exports.amqplib = {
  client: {
    connectOptions: {
      protocol: 'amqp',
      hostname: 'localhost',
      port: 5672,
      username: 'guest',
      password: 'guest',
      locale: 'en_US',
      frameMax: 0,
      heartbeat: 0,
      vhost: '/',
    },
    // socketOptions: {
    //   cert: certificateAsBuffer, // client cert
    //   key: privateKeyAsBuffer, // client key
    //   passphrase: 'MySecretPassword', // passphrase for key
    //   ca: [caCertAsBuffer], // array of trusted CA certs
    // },
  },
};
```

## Example

```js
  // Ask the connection manager for a ChannelWrapper.  Specify a setup function to
  // run every time we reconnect to the broker.
  const channelWrapper = await connection.createChannel({
    json: true,
    setup: (channel) => {
      // `channel` here is a regular amqplib `ConfirmChannel`.
      // Note that `this` here is the channelWrapper instance.
      return channel.assertQueue('rxQueueName', {durable: true});
    }
  });

  // Send some messages to the queue.  If we're not currently connected, these will be queued up in memory
  // until we connect.  Note that `sendToQueue()` and `publish()` return a Promise which is fulfilled or rejected
  // when the message is actually sent (or not sent.)
  channelWrapper.sendToQueue('rxQueueName', {hello: 'world'})
  .then(function() {
      return console.log("Message was sent!  Hooray!");
  }).catch(function(err) {
      return console.log("Message was rejected...  Boo!");
  });

  // Sometimes it's handy to modify a channel at run time. For example, suppose you have a channel that's listening to one kind of message, and you decide you now also want to listen to some other kind of message. This can be done by adding a new setup function to an existing ChannelWrapper:
  channelWrapper.addSetup(function(channel) {
      return Promise.all([
          channel.assertQueue("my-queue", { exclusive: true, autoDelete: true }),
          channel.bindQueue("my-queue", "my-exchange", "create"),
          channel.consume("my-queue", handleMessage)
      ])
  });
```

## API Document
More instructions [here](https://www.npmjs.com/package/amqp-connection-manager).

## Questions & Suggestions

Please open an issue [here](https://github.com/meguoe/egg-amqplib-tf/issues).

## License
[MIT](LICENSE)