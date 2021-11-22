# Optional

## 引言

​	Optional 类主要解决的问题是臭名昭著的空指针异常（NullPointerException），简化代码，实现函数式编程。

---

## API介绍

1. ### [Optional(T value),empty(),of(T value),ofNullable(T value)](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)
 > **Optional(T value)** 
   >
   > > `Optional(T value)`，即构造函数，它是 private 权限的，不能由外部调用的。本质上就是内部存储了一个真实的值，在构造时直接判断该值是否为空
   > >
   > > 源码如下：
   > > ```
   > > private Optional(T value) {this.value = Objects.requireNonNull(value);}
   > > public static <T> T requireNonNull(T obj) {
   > > if (obj == null)
   > >   throw new NullPointerException();
   > >   return obj;
   > > }
   > > ```
---
   > **EMPTY**()
   >
   > > `empty（）`的作用就是new一个Optional对象，返回Null，用Optional泛型包裹
   > > 
---
> **Optional.of(T value)**
> 
>> of方法其实就是套用了一层Optional(T value)方法
>>
---
> **Optional.ofNullable**
> 
>> 其实也是套用了一层Optional.of和empty()方法，当 value 值为 null 时，of(T value) 会报 NullPointerException 异常；`ofNullable(T value)`不会 throw Exception，`ofNullable(T value)`直接返回一个`EMPTY`对象。
>> ```
>>    public static <T> Optional<T> ofNullable(T value) {
        	return value == null ? empty() : of(value);
    }
    ```
2. ### [orElse(T other)，orElseGet(Supplier other) 和 orElseThrow(Supplier exceptionSupplier)](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

   ```
   @Test
   public void test() {
       User user = null;
       user = Optional.ofNullable(user).orElse(createUser());
       user = Optional.ofNullable(user).orElseGet(() -> createUser());
       
   }
   public User createUser(){
       User user = new User();
       user.setName("zhangsan");
       return user;
   }
   ```

   > **orElse和orElseGet**
   >
   > > ​		`orElse`和`orElseGet`的用法如下所示，相当于 value 值为 null 时，给予一个默认值，上述方法，当 user 值不为 null 时，`orElse`函数依然会执行 createUser() 方法，而`orElseGet`函数并不会执行 createUser() 方法。
   > >
   > > **tip:orElseGet调用了supplier接口，每次调用get()方法时都会调用构造方法创建一个新对象。这种语法创建对象，并不会真正在内存中实例对象，而是调用get方法的时候才会实例对象，有点类似于spring的懒加载**
   >
   > **orElseThrow**
   >
   > > 就是 value 值为 null 时, 直接抛一个异常出去
   > >
   > > ```
   > > User user = null;
   > > Optional.ofNullable(user).orElseThrow(()->new Exception("用户不存在"));
   > > ```

3. ### [map(Function mapper) 和 flatMap(Function mapper)](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

   ​		这两个函数，在函数体上没什么区别。唯一区别的就是入参，map 函数所接受的入参类型为`Function<? super T, ? extends U>`，而 flapMap 的入参类型为`Function<? super T, Optional<U>>`。

   实例：

   ```
   public final class Optional<T> {
    //省略....
    public boolean isPresent() {
        return value != null;
    }
    //省略...
    public void ifPresent(Consumer<? super T> consumer) {
        if (value != null)
            consumer.accept(value);
    }
}
   ```

4. ### [isPresent() 和 ifPresent(Consumer consumer)](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

   这两个函数放在一起记忆，`isPresent`即判断 value 值是否为空，而`ifPresent`就是在 value 值不为空时，做一些操作。这两个函数的源码如下

   ```
   Optional.ofNullable(user).ifPresent(u->{
       // TODO: do something
   });
   ```

5. ### [filter(Predicate predicate)](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

   filter 方法接受一个 `Predicate` 来对 `Optional` 中包含的值进行过滤，如果包含的值满足条件，那么还是返回这个 Optional；否则返回 `Optional.empty`


## 实战

 以前写法

```
public String getCity(User user)  throws Exception{
        if(user!=null){
            if(user.getAddress()!=null){
                Address address = user.getAddress();
                if(address.getCity()!=null){
                    return address.getCity();
                }
            }
        }
        throw new Excpetion("取值错误"); 
    }
```

现在写法

```
public String getCity(User user)  throws Exception{
	Optional.ofNullable(user)
	 .map(u-> u.getAddress())
     .map(a->a.getCity())
	.orElseThrow(()->new Exception("取值错误"))
}
```

---

以前写法

```
if(user!=null){
    dosomething(user);
}
```

现在写法

```
Optional.ofNullable(user).ifPresent(u ->{
	 dosomething(u);
}
```

---

以前写法

```java
public User getUser(User user) throws Exception{
    if(user!=null){
        String name = user.getName();
        if("zhangsan".equals(name)){
            return user;
        }
    }else{
        user = new User();
        user.setName("zhangsan");
        return user;
    }
}
```

现在写法

```java
public User getUser(User user){
	Optional.ofNullable(user)
	.filter(user -> "zhangsan".equals(user.getName())
	.orElseGet(
		user.setName("zhangsan");
		return user;
	))
}
```

