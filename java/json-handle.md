# JSON 处理

- [JSON 处理](#json-处理)
  - [Java](#java)
    - [jackson](#jackson)


## Java

### jackson

1. 对象转json
> objectMapping.writeValueAsString(obj)
2. json转对象
> objectMapping.readValue(str, ojb.class)

注意事项：
1. json字符串中的key应该与java对象的属性名相同
   1. java对象名和json中名不一致时解决方法： 在目标对象的字段级别上添加注解：`@JsonProperty(value = "name")`
2. java对象中属性如果为private，则需要显示生成getter/setter方法；如果属性为public，则可以不必写getter/setter方法
3. java对象如果有自定义的构造方法，json字符串转换为java对象时会出错
4. 如果json字符串中的属性个数小于java对象中的属性个数，可以顺利转换，java中多的那个属性为null
5. 如果json字符串中出现java对象中没有的属性，则在将json转换为java对象时会报错：`Unrecognized field, not marked as ignorable`
   1. 在目标对象的类级别上添加注解：`@JsonIgnoreProperties(ignoreUnknown = true)` 

使用示例
```java
ObjectMapper mapper = new ObjectMapper(); 
Person person = new Person(); 
String jsonString = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(person); 
Person deserializedPerson = mapper.readValue(jsonString, Person.class);
```
