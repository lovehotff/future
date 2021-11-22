### 常见Bean拷贝框架使用姿势及性能对比

bean属性拷贝常用jar包：

- cglib (直接使用 Spring 封装的 BeanCopier)
- apache
- MapStruct
- Spring（Spring5+做了优化）
- HuTool

使用效率：

apache ->  hutool -> spring ->  cglib -> mapstruct

### 1. apache BeanUtils

​		不要使用这个

---

###  2. cglib BeanCopier

​		cglib 是通过动态代理的方式来实现属性拷贝的，与上面基于反射实现方式存在本质上的区别，这也是它性能更优秀的主因

在 Spring 环境下，一般不需要额外的引入依赖；或者直接引入`spring-core`，或者直接引入cglib包

```
        BeanCopier copier = BeanCopier.create(source.getClass(), target, false);
        T res = target.newInstance();
        copier.copy(source, res, null);
```

---

### 3.spring BeanUtils

​	基于内省+反射，借助 getter/setter 方法实现属性拷贝，性能比 apache 高

```
        BeanUtils.copyProperties(source, res);
```

### 4.hutool BeanUtil

```
BeanUtil.toBean(source, target);
```

### 5.MapStruct

MapStruct 性能更强悍了，缺点也比较明显，需要声明 bean 的转换接口，自动代码生成的方式来实现拷贝，性能媲美直接的 get/set

```
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.4.2.Final</version>
</dependency>
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct-processor</artifactId>
    <version>1.4.2.Final</version>
</dependency>
```

```
@Mapper
public interface MapStructCopier {
    Target copy(Source source);
}

@Component
public class MapsCopier {
    private MapStructCopier mapStructCopier = Mappers.getMapper(MapStructCopier.class);

    public Target copy(Source source, Class<Target> target) {
        return mapStructCopier.copy(source);
    }
}
```

### 总结

以后使用spring的beanUtils或者cglib的方式