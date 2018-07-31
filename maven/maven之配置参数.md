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
## 通用集合（list, map, property等）
### list
```java
public class MyAnimalMojo extends AbstractMojo{
    /*
     * @parameter
     */
    private List animals;
    public void execute() throws MojoExecuteException {
        ...
    }
}
```
```xml
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-myanimal-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <animals>
            <animal>cat</animal>
            <animal>dog</animal>
            <animal>aardvark</animal>
          </animals>
        </configuration>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```   
如果继承一个list类型：
* If the XML element contains an implementation hint attribute, that is used。
* If the XML tag contains a ., try that as a fully qualified class name。
* Try the XML tag (with capitalized first letter) as a class in the same package as the mojo/object being configured。
* If the element has no children, assume its type is String. Otherwise, the configuration will fail。
### map
按照上面方式在插件类里面定义`private Map myMap`，
然后`pom.xml`使用：
```xml
...
  <configuration>
    <myMap>
      <key1>value1</key1>
      <key2>value2</key2>
    </myMap>
  </configuration>
...
```

### property
类里面定义`private Properties myProperties;`,pom.xml定义：
```xml
...
  <configuration>
    <myProperties>
      <property>
        <name>propertyName1</name>
        <value>propertyValue1</value>
      <property>
      <property>
        <name>propertyName2</name>
        <value>propertyValue2</value>
      <property>
    </myProperties>
  </configuration>
...
```