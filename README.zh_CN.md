# egg-amqplib-tf

基于 amqplib 的 egg amqp 插件。
本模块是在[egg-amqplib] (https://github.com/zubinzhang/egg-amqplib).基础上增加了 [amqp-connection-manager](https://www.npmjs.com/package/amqp-connection-manager)amqp连接管理器, 能够实现amqp断线重连等功能！

## 开启插件

```js
// config/plugin.js
exports.amqplib = {
  enable: true,
  package: 'egg-amqplib-tf',
};
```

## 示例

```js
  // 建立Channel时通过设置setup以便每次重新连接时自动运行。
  const channelWrapper = await connection.createChannel({
    json: true,
    setup: (channel) => {
      // 这里是channelWrapper的实例（channel是常规的amqplib ConfirmChannel）
      return channel.assertQueue('rxQueueName', {durable: true});
    }
  });

  // 向队列发送消息，如果当前没有连接，则将消息缓存至内存中，只到连接建立成功后发送。
  channelWrapper.sendToQueue('rxQueueName', {hello: 'world'})
  .then(function() {
      return console.log("Message was sent!  Hooray!");
  }).catch(function(err) {
      return console.log("Message was rejected...  Boo!");
  });

  // 假设您有一个正在收听一种消息的频道，并且您决定现在还想收听其他类型的消息。这可以通过向现有的ChannelWrapper添加新的设置函数来完成：
  channelWrapper.addSetup(function(channel) {
      return Promise.all([
          channel.assertQueue("my-queue", { exclusive: true, autoDelete: true }),
          channel.bindQueue("my-queue", "my-exchange", "create"),
          channel.consume("my-queue", handleMessage)
      ])
  });
```



## API文档

请到 [amqp-connection-manager](https://www.npmjs.com/package/amqp-connection-manager).

## License

[MIT](LICENSE)
