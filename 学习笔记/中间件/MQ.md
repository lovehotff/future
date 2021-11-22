>  为什么使用消息队列？

> > 解耦，异步，削峰

> 消息队列有什么优点和缺点？

> > * 系统可用性降低
> >
> >   * [如何保证消息队列的高可用](https://github.com/caokang1/advanced-java/blob/main/docs/high-concurrency/how-to-ensure-high-availability-of-message-queues.md)
> >
> >     单机模式，集群模式（横向扩容，无高可用），镜像集群模式（可实现高可用，代价高）
> >
> >   * 系统复杂度提高
> >
> >   * [消息丢失](https://github.com/caokang1/advanced-java/blob/main/docs/high-concurrency/how-to-ensure-the-reliable-transmission-of-messages.md)
> >
> >     1. 从producer到broker（消息队列服务器实体）会丢失，有事务或者confirm机制。
> >
> >        ```java
> >        // 开启事务
> >        channel.txSelect
> >        try {
> >            // 这里发送消息
> >        } catch (Exception e) {
> >            channel.txRollback
> >            // 这里再次重发这条消息
> >        }
> >        // 提交事务
> >        channel.txCommit
> >        ```
> >        
> >     2. 从broker到内存的时候，consumer还没来得及消费，broker就挂掉了，有持久化设置，如果producer没有接到回馈的ack报文，就会触发重发机制
> >3. consumer接到消息后，没有进行业务处理，broker以为已经处理完毕，发送后续消息时，这时需要关闭autoack，开启手动ack（调用channel中的basicAsk）
> >     
> >   * 消息的重复消费（消息的幂等性）
> >
> >     ​	其实还是得结合业务来思考，我这里给几个思路：
> >
> >     - 比如你拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update 一下好吧。
> >  - 比如你是写 Redis，那没问题了，反正每次都是 set，天然幂等性。
> >     - 比如你不是上面两个场景，那做的稍微复杂一点，你需要让生产者发送每条数据的时候，里面加一个全局唯一的 id，类似订单 id 之类的东西，然后你这里消费到了之后，先根据这个 id 去比如 Redis 里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个 id 写 Redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。
> >     - 比如基于数据库的唯一键来保证重复数据不会重复插入多条。因为有唯一键约束了，重复数据插入只会报错，不会导致数据库中出现脏数据。

​		

* 一致性问题（事务消息）

  ![image-20210128121232485](C:\Users\kgcaox\AppData\Roaming\Typora\typora-user-images\image-20210128121232485.png)
  
* 保证消息顺序执行

  ​	拆分多个quene，用一个quene对接一个consumer，会降低rabbitmq的吞吐量；优化后，可以使用java的内存队列，使用多线程去消费，然后分发给底层不同的worker来处理

  ```java
  public class Pusher implements Runnable {
  		private Queue<String> msgs = Queues.newConcurrentLinkedQueue();
  		// 怼事件到内存队列
  		void push(String event) {
  			msgs.add(event);
  		}
  
  		@Override
  		public void run() {
  			while (true) {
  			//消费事件
  			String data = msgs.poll();
  		}
          }
      
       public static void main(String[] args) {
          new Thread().start;
      }
  ```

- #### [如何解决消息队列的延时及过期失效问题？](https://github.com/caokang1/advanced-java/blob/main/docs/high-concurrency/mq-time-delay-and-expired-failure.md)

  ​		假设你用的是 RabbitMQ，RabbtiMQ 是可以设置过期时间的，也就是 TTL。如果消息在 queue 中积压超过一定的时间就会被 RabbitMQ 给清理掉，这个数据就没了。那这就是第二个坑了。这就不是说数据会大量积压在 mq 里，而是**大量的数据会直接搞丢**。

  ​		这个情况下，就不是说要增加 consumer 消费积压的消息，因为实际上没啥积压，而是丢了大量的消息。我们可以采取一个方案，就是**批量重导**，这个我们之前线上也有类似的场景干过。就是大量积压的时候，我们当时就直接丢弃数据了，然后等过了高峰期以后，比如大家一起喝咖啡熬夜到晚上 12 点以后，用户都睡觉了。这个时候我们就开始写程序，将丢失的那批数据，写个临时程序，一点一点的查出来，然后重新灌入 mq 里面去，把白天丢的数据给他补回来。也只能是这样了。

  ​		假设 1 万个订单积压在 mq 里面，没有处理，其中 1000 个订单都丢了，你只能手动写程序把那 1000 个订单给查出来，手动发到 mq 里去再补一次。

- 如何解决大量消息堆积在mq

    1. 先修复 consumer 的问题，确保其恢复消费速度，然后将现有 consumer 都停掉。
    2. 新建一个 topic，partition 是原来的 10 倍，临时建立好原先 10 倍的 queue 数量。
    3. 然后写一个临时的分发数据的 consumer 程序，这个程序部署上去消费积压的数据，**消费之后不做耗时的处理**，直接均匀轮询写入临时建立好的 10 倍数量的 queue。
    4. 接着临时征用 10 倍的机器来部署 consumer，每一批 consumer 消费一个临时 queue 的数据。这种做法相当于是临时将 queue 资源和 consumer 资源扩大 10 倍，以正常的 10 倍速度来消费数据。
    5. 等快速消费完积压数据之后，**得恢复原先部署的架构**，**重新**用原先的 consumer 机器来消费消息。

- mq写满了怎么办

  ​	如果消息积压在 mq 里，你很长时间都没有处理掉，此时导致 mq 都快写满了，咋办？这个还有别的办法吗？没有，谁让你第一个方案执行的太慢了，你临时写程序，接入数据来消费，**消费一个丢弃一个，都不要了**，快速消费掉所有的消息。然后走第二个方案，到了晚上再补数据吧

- Kafka、ActiveMQ、RabbitMQ、RocketMQ 都有什么区别，以及适合哪些场景？

