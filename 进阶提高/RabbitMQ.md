```bash
service rabbitmq-server start    # 启动
service rabbitmq-server stop     # 停止
service rabbitmq-server restart  # 重启 
systemctl status rabbitmq-server #Active: active (running) 说明处于运行状态
rabbitmqctl help # 查看 rabbitmq 命令
```

```bash
rabbitmq-plugins enable|list|disable [rabbitmq_management]   # 启用|查看|停止插件
service rabbitmq-server restart    # 重启
#  http://localhost:15672 默认账户guest/guest
rabbitmqctl add_user admin yourpassword   # 增加普通用户
rabbitmqctl set_user_tags admin administrator    # 给普通用户分配管理员角色 
# 赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源
sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```

```yml
spring:
  rabbitmq:
  # 用户密码使用新增的
    username: guest
    password: guest
    host: 192.168.64.137
    port: 5672
```

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class MailApplication {
    public static void main(String[] args) {
        SpringApplication.run(MailApplication.class,args);
    }

    @Bean
    public Queue queue(){
        return new Queue("welcome");
    }
}
```

```java
// RabbitMQ配置类
@Configuration
public class RabbitMQConfig {

    // 连接工厂
    @Resource
    private CachingConnectionFactory cachingConnectionFactory;

    @Resource
    private IMailLogService mailLogService;

    // 配置日志
    private static final Logger LOGGER= LoggerFactory.getLogger(RabbitMQConfig.class);

    // rabbitMQ 消息回调配置
    @Bean
    public RabbitTemplate rabbitTemplate(){
        RabbitTemplate rabbitTemplate = new RabbitTemplate(cachingConnectionFactory);

        /**
         * 消息确认回调,确认消息是否到达broker
         * data:消息唯一标识
         * ack:确认结果
         * cause:失败原因
         */
        rabbitTemplate.setConfirmCallback((data,ack,cause)->{
            String msgId = data.getId();
            // 发送成功
            if(ack){
                LOGGER.info("{}====>消息发送成功",msgId);
                // 更新数据库状态为1
                mailLogService.update(new UpdateWrapper<MailLog>().set("status",1).eq("msgId",msgId));
            }else {
                LOGGER.error("{}====>消息发送失败",msgId);
            }
        });

        /**
         * 消息失败回调
         * msg:消息的主题
         * repCode:响应码
         * repText:响应描述
         * exchange:交换机
         * routingKey:路由键
         */
        rabbitTemplate.setReturnCallback((msg,repCode,repText,exchange,routingKey)->{
            LOGGER.error("{}====>消息发送queue时失败",msg.getBody());
        });

        return rabbitTemplate;
    }

    // 队列名配置
    @Bean
    public Queue queue(){
        return new Queue(MailConstants.MAIL_QUEUE_NAME);
    }

    // 交换机配置
    @Bean
    public DirectExchange directExchange(){
        return new DirectExchange(MailConstants.MAIL_EXCHANGE_NAME);
    }

    // 绑定,队列和交换机用路由键绑定
    @Bean
    public Binding binding(){
        return BindingBuilder.bind(queue()).to(directExchange()).with(MailConstants.MAIL_ROUTING_KEY_NAME);
    }
}
```

