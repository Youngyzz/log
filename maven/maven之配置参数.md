# maven之插件参数配置
## 简单对象（int,String,double等内置类型)。
```xml
...
<configuration>
  <myString>a string</myString>
  <myBoolean>true</myBoolean>
  <myInteger>10</myInteger>
  <myDouble>1.0</myDouble>
  <myFile>c:\temp</myFile>
  <myURL>http://maven.apache.org</myURL>
</configuration>
...
```
## 复杂对象（自定义类）
```xml
...
<configuration>
  <person>
    <firstName>Jason</firstName>
    <lastName>van Zyl</lastName>
  </person>
</configuration>
...
```
映射复杂对象有一定规则：
* 必须存在对应元素名字的私有字段，如person，在插件类中必须有`private Person person`
* The object instantiated must be in the same package as the Mojo itself. So if your mojo is in com.mycompany.mojo.query then the mapping mechanism will look in that package for an object named Person. As you can see the mechanism will capitalize the first letter of the element name and use that to search for the object to instantiate.(不清楚什么意思)
*  If you wish to have the object to be instantiated live in a different package or have a more complicated name then you must specify this using an implementation attribute like the following:
```xml
...
<configuration>
  <person implementation="com.mycompany.mojo.query.SuperPerson">
    <firstName>Jason</firstName>
    <lastName>van Zyl</lastName>
  </person>
</configuration>
...
```
## 通用集合（list, map, property等）。