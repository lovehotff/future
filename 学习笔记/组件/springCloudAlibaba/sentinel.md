# # **Sentinel详解**

## @SentinelResource

### 概念
	用于定义资源，并提供可选得异常处理和熔断配置

### 属性
* value：资源名称，必需项（不能为空）

* entryType：entry 类型，可选项（默认为 EntryType.OUT）

  ---

* blockHandler / blockHandlerClass
	blockHandler 对应处理 BlockException 的函数名称，可选项。blockHandler 函数访问范围需要是 public，返回类型需要与原方法相匹配，参数类型需要和原方法相匹配并且最后加一个额外的参数，类型为 BlockException。blockHandler 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 blockHandlerClass 为对应的类的 Class 对象，注意对应的函数必需为 static 函数，否则无法解析。
	
	#### 	**全局兜底方法(如下)**
	
	​		tip：**兜底的方法的参数，和原来方法的参数必须一致，并且兜底的方法里面需要增加一个【BlockException exception】参数**
```
@RestController
public class RegisterController {

    @GetMapping(value = "/register")
    @SentinelResource(value = "/SentinelOne",
             blockHandlerClass = SentinelException.class,blockHandler = "blockException")
    public String getDate() {
        return new Date().toString();
    }
    
}
```
```
public class SentinelException {

    public static String blockException(BlockException e){
        return "===该接口已被限流===";
    }

    public static String fallbackException(Exception e){
        return "===该接口已被熔断===";
    }
}
```
---
* fallback / fallbackClass
	fallback，可选项，用于在抛出异常的时候提供 fallback 处理逻辑。fallback 函数可以针对所有类型的异常（除了 exceptionsToIgnore 里面排除掉的异常类型）进行处理。
	fallback 函数签名和位置要求：返回值类型必须与原函数的入参出参一致；方法参数列表需要和原函数一致，或者可以额外多一个 Throwable 类型的参数用于接收对应的异常；fallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 fallbackClass 为对应的类的 Class 对象，注意对应的函数必需为 static 函数，否则无法解析。跟之前的 blockHandlerClass 用法一致。
* defaultFallback：与fallback用法一致，类似一个空构造器，入参为空，或者可以额外多一个 Throwable 类型的参数用于接收对应的异常
* exceptionsToIgnore：用于指定哪些异常被排除掉，不走熔断的方法