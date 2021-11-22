**Maven引入**：

```
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-databind</artifactId>
</dependency>
```
---

## Jackson 注解

### 属性命名

```
@JsonProperty注解指定一个属性用于 JSON 映射，默认情况下映射的JSON属性与注解的属性名称相同，不过可以使用该注解的value值修改 JSON 属性名，该注解还有一个index属性指定生成 JSON 属性的顺序
```

### 属性包含

```
@JsonIgnore注解用于排除某个属性，这样该属性就不会被 Jackson 序列化和反序列化
```

```
@JsonIgnoreType也是类注解，会排除所有指定类型的属性
```

### 序列化相关

```
@JsonPropertyOrder和@JsonProperty的index属性类似，指定属性序列化时的顺序
```

```
@JsonRootName注解用于指定 JSON 根属性的名称
```

## 处理 JSON

### 简单映射

如：

```
		ObjectMapper mapper = new ObjectMapper();
        Friend friend = new Friend("yitian", 25);

        // 写为字符串
        String text = mapper.writeValueAsString(friend);
        // 写为文件
        mapper.writeValue(new File("friend.json"), friend);
        // 写为字节流
        byte[] bytes = mapper.writeValueAsBytes(friend);
        System.out.println(text);
        // 从字符串中读取
        Friend newFriend = mapper.readValue(text, Friend.class);
        // 从字节流中读取
        newFriend = mapper.readValue(bytes, Friend.class);
        // 从文件中读取
        newFriend = mapper.readValue(new File("friend.json"), Friend.class);
        System.out.println(newFriend);
```

### 集合的映射

```
		ObjectMapper mapper = new ObjectMapper();

        Map<String, Object> map = new HashMap<>();
        map.put("age", 25);
        map.put("name", "yitian");
        map.put("interests", new String[]{"pc games", "music"});

        String text = mapper.writeValueAsString(map);
        System.out.println(text);

        Map<String, Object> map2 = mapper.readValue(text, new 					TypeReference<Map<String, Object>>() {
        });
        System.out.println(map2);

        JsonNode root = mapper.readTree(text);
        String name = root.get("name").asText();
        int age = root.get("age").asInt();

        System.out.println("name:" + name + " age:" + age);
        
        程序结果如下。

		{"name":"yitian","interests":["pc games","music"],"age":25}
		{name=yitian, interests=[pc games, music], age=25}
		name:yitian age:25
```

### Jackson 配置

```
// 美化输出
mapper.enable(SerializationFeature.INDENT_OUTPUT);
// 允许序列化空的POJO类
// （否则会抛出异常）
mapper.disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);
// 把java.util.Date, Calendar输出为数字（时间戳）
mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);

// 在遇到未知属性的时候不抛出异常
mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
// 强制JSON 空字符串("")转换为null对象值:
mapper.enable(DeserializationFeature.ACCEPT_EMPTY_STRING_AS_NULL_OBJECT);

// 在JSON中允许C/C++ 样式的注释(非标准，默认禁用)
mapper.configure(JsonParser.Feature.ALLOW_COMMENTS, true);
// 允许没有引号的字段名（非标准）
mapper.configure(JsonParser.Feature.ALLOW_UNQUOTED_FIELD_NAMES, true);
// 允许单引号（非标准）
mapper.configure(JsonParser.Feature.ALLOW_SINGLE_QUOTES, true);
// 强制转义非ASCII字符
mapper.configure(JsonGenerator.Feature.ESCAPE_NON_ASCII, true);
// 将内容包裹为一个JSON属性，属性名由@JsonRootName注解指定
mapper.configure(SerializationFeature.WRAP_ROOT_VALUE, true);
```

### 用注解管理映射

```
@JsonRootName("FriendDetail")
@JsonIgnoreProperties({"uselessProp1", "uselessProp3"})
public class FriendDetail {
    @JsonProperty("NickName")
    private String name;
    @JsonProperty("Age")
    private int age;
    private String uselessProp1;
    @JsonIgnore
    private int uselessProp2;
    private String uselessProp3;
}
```

```
ObjectMapper mapper = new ObjectMapper();
//mapper.enable(SerializationFeature.WRAP_ROOT_VALUE);
mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
FriendDetail fd = new FriendDetail("yitian", 25, "", 0, "");
String text = mapper.writeValueAsString(fd);
System.out.println(text);

FriendDetail fd2 = mapper.readValue(text, FriendDetail.class);
System.out.println(fd2);
```

### Java8 日期时间类支持

```
ObjectMapper mapper = new ObjectMapper()
                .registerModule(new JavaTimeModule())
                .registerModule(new ParameterNamesModule())
                .registerModule(new Jdk8Module());
```

## 处理 XML

Jackson 是一个处理 JSON 的类库，不过它也通过`jackson-dataformat-xml`包提供了处理 XML 的功能。Jackson 建议我们在处理 XML 的时候使用`woodstox-core`包，它是一个 XML 的实现，比 JDK 自带 XML 实现更加高效，也更加安全。



这里有个注意事项，如果你正在使用 Java 9 以上的 JDK，可能会出现`java.lang.NoClassDefFoundError: javax/xml/bind/JAXBException`异常，这是因为 Java 9 实现了 JDK 的模块化，将原本和 JDK 打包在一起的 JAXB 实现分隔出来。所以这时候需要我们手动添加 JAXB 的实现。在 Gradle 中添加下面的代码即可。



```
compile group: 'javax.xml.bind', name: 'jaxb-api', version: '2.3.0'
```



### 注解



Jackson XML 除了使用 Jackson JSON 和 JDK JAXB 的一些注解之外，自己也定义了一些注解。下面简单介绍一下几个常用注解。



`@JacksonXmlProperty`注解有三个属性，namespace 和 localname 属性用于指定 XML 命名空间的名称，isAttribute 指定该属性作为 XML 的属性（）还是作为子标签（）.



`@JacksonXmlRootElement`注解有两个属性，namespace 和 localname 属性用于指定 XML 根元素命名空间的名称。



`@JacksonXmlText`注解将属性直接作为未被标签包裹的普通文本表现。



`@JacksonXmlCData`将属性包裹在 CDATA 标签中。



### XML 映射



新建如下一个 Java 类。



```
@Data
@NoArgsConstructor
@AllArgsConstructor
@JsonRootName("Person")
public class Person {
    @JsonProperty("Name")
    private String name;
    @JsonProperty("NickName")
    //@JacksonXmlText
    private String nickname;
    @JsonProperty("Age")
    private int age;
    @JsonProperty("IdentityCode")
    @JacksonXmlCData
    private String identityCode;
    @JsonProperty("Birthday")
    //@JacksonXmlProperty(isAttribute = true)
    @JsonFormat(pattern = "yyyy/MM/DD")
    private LocalDate birthday;

}
```



下面是代码示例，基本上和 JSON 的 API 非常相似，`XmlMapper`实际上就是`ObjectMapper`的子类。



```
Person p1 = new Person("yitian", "易天", 25, "10000", LocalDate.of(1994, 1, 1));
        XmlMapper mapper = new XmlMapper();
        mapper.findAndRegisterModules();
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        mapper.enable(SerializationFeature.INDENT_OUTPUT);
        String text = mapper.writeValueAsString(p1);
        System.out.println(text);

        Person p2 = mapper.readValue(text, Person.class);
        System.out.println(p2);d
```



运行结果如下。



```
<Person>
  <Name>yitian</Name>
  <NickName>易天</NickName>
  <Age>25</Age>
  <IdentityCode><![CDATA[10000]]></IdentityCode>
  <Birthday>1994/01/01</Birthday>
</Person>

Person(name=yitian, nickname=易天, age=25, identityCode=10000, birthday=1994-01-01)
```



如果取消那两行注释，那么运行结果如下。可以看到 Jackson XML 注解对生成的 XML 的控制效果。



```
<Person birthday="1994/01/01">
  <Name>yitian</Name>易天
  <Age>25</Age>
  <IdentityCode><![CDATA[10000]]></IdentityCode>
</Person>

Person(name=yitian, nickname=null, age=25, identityCode=10000, birthday=1994-01-01)
```