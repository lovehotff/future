# stream的构造方式

>## 	stream内置的构造方法

>> ```java
>> public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)
>> public static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)
>> public static<T> Builder<T> builder()
>> public static<T> Stream<T> of(T t)
>> public static<T> Stream<T> empty()
>> public static<T> Stream<T> generate(Supplier<T> s)
>> ```


> ## 	接口stream对元素的操作方法定义

>> * 过滤 filter
>>
>>   ```java
>>     public void streamTestFilter() {
>>           List<String> demo = Arrays.asList("a", "b", "c");
>>           Predicate<String> f1 = item -> item.equals("a");
>>           Predicate<String> f2 = item -> item.equals("C");
>>           demo.stream().filter(f1.or(f2)).forEach(item -> log.info("===filter==={}" + item));
>>       }
>>   ```

>> * 映射转化 map
>
>>   ```java
>>   public class StreamTest {
>>            static class User{
>>            public User(Integer id){this.id = id; }
>>           Integer id; public Integer getId() {  return id; }
>>       }
>>       @Test
>>    public static void main(String[] args) {
>>        List<User> demo = Arrays.asList(new User(1), new User(2), new User(3));
>>        // User 转为 Integer(id)
>>        demo.stream().mapToInt(User::getId).forEach(System.out::println);
>>    }
>>    }
>>   ```
>


>> * 数据处理 peek
>
>>   ```java
>>   public class StreamTest {
>>              static class User{
>>              public User(Integer id){this.id = id; }
>>              Integer id; public Integer getId() {  return id; }
>>        }
>>       @Test
>>   public static void main(String[] args) {
>>       List<User> demo = Arrays.asList(new User(1), new User(2), new User(3));
>>       // 无返回值
>>       demo.stream().peek(User::getId).forEachOrdered(System.out::println);
>>   	}
>>   }
>>   ```
>
>>映射撵平 flatMap
>>
>>```
>>   public class StreamTest {
>>          static class User{
>>              public User(Integer id){this.id = id; }
>>              Integer id; public Integer getId() {  return id; }
>>         }
>>   
>>          @Test
>>          public static void main(String[] args) {
>>              List<Stream<Integer>> demo = Arrays.asList(Stream.of(5), Stream.of(2), Stream.of(1));
>>              // User 转为 Integer(id)
>>              demo.stream().flatMap(Function.identity()).forEachOrdered(System.out::println);
>>          }
>>    }
>>```

>> * 去重 distinct

>> * 排序 sorted
>>
>>   ```java
>>   //降序
>>    public static void main(String[] args) {
>>           List<Stream<Integer>> demo = Arrays.asList(Stream.of(1), Stream.of(2), Stream.of(1));
>>           // User 转为 Integer(id)
>>           Comparator<Integer> comparator = Comparator.<Integer, Integer>comparing(item -> item).reversed();
>>           demo.stream().flatMap(Function.identity()).sorted(comparator).forEachOrdered(System.out::println);
>>     }
>>   ```

>> * 个数限制limit和跳过skip

>> * JDK9提供的新操作
	和filter的区别，takeWhile是取满足条件的元素，直到不满足为止；dropWhile是丢弃满足条件的元素，直到不满足为止

>> * 遍历

>> * 获取数组结果
>>
>>   ```java
>>   //遍历消费
>>   void forEach(Consumer<? super T> action);
>>   //顺序遍历消费,和forEach的区别是forEachOrdered在多线程parallelStream执行，其顺序也不会乱
>>   void forEachOrdered(Consumer<? super T> action);
>>   ```

>> * 获取最大最小值
>>
>>   ```java
>>   Optional<Integer> min = demo.stream().min(Comparator.comparing(item->item));
>>   Optional<Integer> max = demo.stream().max(Comparator.comparing(item->item));
>>   ```

>> * 查找匹配
>>
>>   ```java
>>   //任意一个匹配
>>   boolean anyMatch(Predicate<? super T> predicate)
>>   //全部匹配
>>   boolean allMatch(Predicate<? super T> predicate)
>>   //不匹配 
>>   boolean noneMatch(Predicate<? super T> predicate)
>>   //查找第一个
>>   Optional<T> findFirst();
>>   //任意一个
>>   Optional<T> findAny();
>>   ---------------------------------------------------------------------------------------------------
>>   @Test
>>   public static void main(String[] args) {
>>           List<Stream<Integer>> demo = Arrays.asList(Stream.of(1), Stream.of(2), Stream.of(3));
>>           // User 转为 Integer(id)
>>           Comparator<Integer> comparator = Comparator.<Integer, Integer>comparing(item -> item).reversed();
>>           Optional<Integer> optional = demo.stream().flatMap(Function.identity()).findAny();
>>           System.out.println(optional);
>>       }
>>   ```

>> * 归约合并
>>
>>   ```java
>>   List<Integer> demo = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
>>   //数字转化为字符串，然后使用“-”拼接起来
>>   String data = demo.stream().reduce("0", (u, t) -> u + "-" + t, (s1, s2) -> s1 + "-" + s2);
>>   System.out.println(data);
>>   ```

>> * COUNT

>> * 聚合处理
>>
>>   ```java
>>   /**
>>    * supplier:返回结果类型的生产者
>>    * accumulator:元素消费者（处理并加入R）
>>    * combiner: 返回结果 R 怎么组合（多线程执行时，会产生多个返回值R，需要合并）
>>    */
>>   <R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner);
>>   /**
>>    * collector一般是由 supplier、accumulator、combiner、finisher、characteristics组合成的聚合类
>>    * Collectors 可提供一些内置的聚合类或者方法
>>    */
>>   <R, A> R collect(Collector<? super T, A, R> collector);
>>   ```
>>
>>   

> ## 	Collectors工具类集（Collector和CollectorImpl）
> > * 流聚合转换成List, Set
> >
> >   ```java
> >   @Test
> >       public static void main(String[] args) {
> >           List<Integer> demo = Arrays.asList(1, 2, 3);
> >           List<Integer> col = demo.stream().collect(Collectors.toList());
> >           Set<Integer> row = demo.stream().collect(Collectors.toSet());
> >           System.out.println(col);
> >           System.out.println(row);
> >       }
> >   ```
> >

> > * 字符串流聚合拼接  
> >   ```java
> > List<String> demo = Arrays.asList("c", "s", "c","w","潜行前行");
> > String name = demo.stream().collect(Collectors.joining("-"));
> > System.out.println(name);
> >   ```

> > * 流聚合转化成Map
> >   ```java
> > List<User> demo = Arrays.asList(new User(1), new User(2), new User(3));
> > Map<Integer,User> map = demo.stream().collect(Collectors.toMap(User::getId,item->item));
> > System.out.println(map);
> >   ```

> > * 映射处理再聚合流
> >
> >   ```java
> >   List<String> demo = Arrays.asList("1", "2", "3");
> >   List<Integer> data = demo.stream().collect(Collectors.mapping(Integer::valueOf, Collectors.toList()));
> >   System.out.println(data);
> >   ```

> > * 聚合后再转换结果
> >
> >   ```java
> >   List<Integer> demo = Arrays.asList(1, 2, 3, 4, 5, 6);
> >   //聚合成List,最后提取数组的size作为返回值
> >   Integer size = demo.stream().collect(Collectors.collectingAndThen(Collectors.toList(), List::size));
> >   System.out.println(size);
> >   ```

> > * 流分组（Map是HashMap）
> >
> >   ```java
> >   public static void main(String[] args) throws Exception {
> >       List<Integer> demo = Stream.iterate(0, item -> item + 1)
> >               .limit(15)
> >               .collect(Collectors.toList());
> >       // 分成三组，并且每组元素转化为String类型        
> >       Map<Integer, List<String>> map = demo.stream()
> >               .collect(Collectors.groupingBy(item -> item % 3,
> >                       HashMap::new,
> >                       Collectors.mapping(String::valueOf, Collectors.toList())));
> >       System.out.println(map);
> >   }
> >   ```
> >
> > * 流分组(分组使用的Map是ConcurrentHashMap)

> > * 拆分流，一变二（相当于特殊的groupingBy）
> >
> >   ```java
> >   List<Integer> demo = Arrays.asList(1, 2,3,4, 5,6);
> >   // 奇数偶数分组
> >   Map<Boolean, List<Integer>> map = demo.stream()
> >   	.collect(Collectors.partitioningBy(item -> item % 2 == 0));
> >   System.out.println(map);
> >   ```

> > * 聚合求平均值
> >
> >   ```java
> >   List<Integer> demo = Arrays.asList(1, 2, 5);
> >   Double data = demo.stream().collect(Collectors.averagingInt(Integer::intValue));
> >   System.out.println(data);
> >   ```
> >
> > * 流聚合查找最大最小值
> >
> >   ```java
> >   List<Integer> demo = Arrays.asList(1, 2, 5);
> >   Optional<Integer> min = demo.stream().collect(Collectors.minBy(Comparator.comparing(item -> item)));
> >   Optional<Integer> max = demo.stream().collect(Collectors.maxBy(Comparator.comparing(item -> item)));
> >   System.out.println(min.get()+"-"+max.get());
> >   ```


> > * 聚合计算统计结果
> >   ```java
> >   List<Integer> demo = Arrays.asList(1, 2, 5);
> >   IntSummaryStatistics data = demo.stream().collect(Collectors.summarizingInt(Integer::intValue));
> >   System.out.println(data);
> >   ```

# 并发paralleStream的使用

- parallelStream的方法确实会使用多线程去运行，并且可以指定线程池，不过自定义线程必须是ForkJoinPool类型，否则会默认使ForkJoinPool.commonPool()的线程

  ```java
  public static void main(String[] args)  throws Exception{
      List<Integer> demo = Stream.iterate(0, item -> item + 1)
              .limit(5)
              .collect(Collectors.toList());
      //示例1
      Stopwatch stopwatch = Stopwatch.createStarted(Ticker.systemTicker());
      demo.stream().forEach(item -> {
          try {
              Thread.sleep(500);
              System.out.println("示例1-"+Thread.currentThread().getName());
          } catch (Exception e) { }
      });
      System.out.println("示例1-"+stopwatch.stop().elapsed(TimeUnit.MILLISECONDS));
  
      //示例2, 注意需要ForkJoinPool，parallelStream才会使用executor指定的线程，否则还是用默认的 ForkJoinPool.commonPool()
      ExecutorService executor = new ForkJoinPool(10);
      stopwatch.reset(); stopwatch.start();
      CompletableFuture.runAsync(() -> demo.parallelStream().forEach(item -> {
          try {
              Thread.sleep(1000);
              System.out.println("示例2-" + Thread.currentThread().getName());
          } catch (Exception e) { }
      }), executor).join();
      System.out.println("示例2-"+stopwatch.stop().elapsed(TimeUnit.MILLISECONDS));
      //示例３
      stopwatch.reset(); stopwatch.start();
      demo.parallelStream().forEach(item -> {
          try {
              Thread.sleep(1000);
              System.out.println("示例3-"+Thread.currentThread().getName());
          } catch (Exception e) { }
      });
      System.out.println("示例3-"+stopwatch.stop().elapsed(TimeUnit.MILLISECONDS));
      executor.shutdown();
  
  }
  ```
```

  
```