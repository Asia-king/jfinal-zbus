jfinal-zbus插件

项目依赖：jfina 2.0 ，zbus， fastjson ， reflections，详见pom.xml文件

更新说明：

1）发送接受全部泛型化，更容易写出类型安全，高质量的代码。

2）支持直接发送JFinal中的Model，Record对象。

3）简化设计，不支持异步消息，只支持同步消息。

4）建议仅发送简单对象，类似List<Map<String,Model>>的复杂对象暂不支持。

使用方法：
在JFinal的Config配置文件中配置
```
//初始化zbus插件
String brokerAddress = "127.0.0.1:155555";
String scanRootPackage = "com.wellbole";
ZbusPlugin zbusPlugin = new ZbusPlugin(brokerAddress,scanRootPackage);
me.add(zbusPlugin);
```

在其他地方，通过Zbus来发送消息。
```
//初始化一个MQ泛型（Dict类型，Dict继承子Model）发送器，构造函数参数为MQ名
Sender<Dict> mqSender = new MqSender<Dict>("MyMQ");
Dict dict = new Dict();
dict.setId(1L);
dict.set("key", "key"+1);
dict.set("value", "value"+1);
//同步发送对象到MQ
mqSender.sendSync(dict);
//sendAsync
//异步发送对象到MQ
//mqSender.sendAsync(dict);
//初始化一个Topic泛型（String类型）发送器，构造函数参数为，MQ名，Topic名
Sender<String> topicSender = new TopicSender<String>("Topic", "Check");
//同步发送对象到topic
topicSender.sendSync("这时一个订阅消息");
//异步发送对象到topic
//topicSender.sendAsync("这时一个订阅消息");
```

接收zbus传送的消息
```
//MQ消息处理器
@MqHandler("MyMQ")
//Topic（pubsub）消息处理器
//@TopicHandler(mq="Topic", topic="Check")
public class DictMsgHandler extends TMsgHandler<Dict> {
	private static final Logger LOG = Logger.getLogger("DictMsgHandler");

	@Override
	public void handle(Dict msg) {
		LOG.info(msg.toString());
	}
}
```