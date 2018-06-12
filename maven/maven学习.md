# 是什么？
Maven -- 用来构建和管理Java项目的工具

# 干什么？

# 怎么用？
Maven运行有一套生命周期， 顺序为：
clean - pre-clean, clean, post-clean

default - validate, initialize, generate-sources, process-sources, generate-resources, process-resources, compile, process-classes, generate-test-sources, process-test-sources, generate-test-resources, process-test-resources, test-compile, process-test-classes, test, prepare-package, package, pre-integration-test, integration-test, post-integration-test, verify, install, deploy

site - pre-site site post-site site-deploy

```sh
#一条完整的构建线路，clean -> deploy -> site-deploy.
mvn clean deploy site-deploy
```

`mvn verify`告诉Maven构建所有模块，运行所有测试，验证所有输出都是OK。检查target目录，你会找到所有构建输出。

所有插件如果需要执行任务都会进行优化（比如如果没有变化就会缓存等），某些情况下，输出可能会输出之前的构建的结果，然后得到的结果不是你所期望的。在这种情况下，你可以先调用clean，清除掉缓存下来的输出。当然你可以和后续的步骤一起执行，如`mvn clean verify`,这意味着：先执行清理，然后构建工程，验证输出。


# Maven plugins
在Maven中，有构建（Build）和报告（Reporting）插件：  
构建插件在<build/>元素中配置，用来执行所有构建。  
报告插件在<reporting/>元素中配置，在site生成时执行，（TODO：什么是site?)

所有插件至少需要的信息：groupId， artifactId和版本。

*注意*： 强烈建议定义每个插件的版本。好的实践方法就是在`<build><pluginManagement/></build>`来为每个插件指定版本。通常情况下，你会在一个在父Pom中定义`<pluginManagement/>`。 reporting插件也是一样，可以定义在`<build><pluginManagement/></build>`中。也可以对插件单独定义`<reporting><plugins/><reporting>`。

# Maven插件配置内容（如参数等）
Maven插件（构建和报告）通过指定<configuration>元素来指定配置，<configuration>里面的子元素来映射到插件类的字段，或者setters。

例子：自己实现的Mojo插件，用来执行指定URL的连接（带有超时时间和一系列选项）
```java
public class MyQueryMojo extends AbstractMojo {
    /**
     * @parameter expression="${query.url}"
     */
    private String url;

    /**
     *  @parameter expression="${query.url}"
     */
    private int timeout;

    private String[] options;

    public void execute() throws MojoExecutionException{
        ...
    }
}
```
配置我们写的Mojo插件：
```xml
<project>
    ...
    <build>
        <plugins>
            <artifactId>maven-myquery-plugin</artifactId>
            <version>1.0</version>
            <configuration>
                <url>http://www.foobar.com/query</url>
                <timeout>10</timeout>
                <options>
                    <option>one</option>
                    <option>two</option>
                    <option>three</option>
                </options>
        </plugins>
    </build>
</project>
```
不但如此，我们可以将内容`${参数}`来替代，如上面的url，我们可以写成`<url>${query.url}</url>`,然后在调用maven是指定`query.url`的内容：
```sh
#myquery指定的是可执行，后面是执行目标
mvn myquery:query -D query.url=http://maven.apache.org
```

# Help目标
最新的Maven插件都有help目标，用来描述我们的插件，例如，我们需要查看javadoc插件，javadoc目标的详细内容：
```sh
#-D goal=javadoc  目标为javadoc
#-D detail        详细
mvn javadoc:help -D detail -Dgoal=javadoc
```

