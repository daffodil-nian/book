# dubbo项目学习（顺带讲了怎么启动nacos）

## 快速入门

https://cn.dubbo.apache.org/zh-cn/overview/mannual/java-sdk/quick-start/starter/

这个链接里面有介绍dubbo，接下来就让我先简单带过一下

1. **引入spring boot starter**

   我们在GitHub上clone下来的dubbo-simple项目稍微有一点问题，需要我们稍作修改，这里我们需要给消费端和生产端的xml文件里面引入maven依赖，如果启动项目有问题**请看下面的补充点** ，列举了我出现的各种问题。

   ```xml
   <dependencyManagement>
           <dependencies>
               <dependency>
                   <groupId>org.apache.dubbo</groupId>
                   <artifactId>dubbo-bom</artifactId>
                   <version>3.3.0</version>
                   <type>pom</type>
                   <scope>import</scope>
               </dependency>
           </dependencies>
       </dependencyManagement>
   ```

2. **选定通信协议**

   创建好应用后我们要选择通信协议，这里我们可以查阅官方文档

   https://cn.dubbo.apache.org/zh-cn/overview/mannual/java-sdk/tasks/protocols/protocol/

   我们选择dubbo协议，因为它是**基于TCP**的 **高性能** **私有** 的通信协议，就是 **通用性差**

3. **定义服务**

   接下来就要定义服务，在service层可以随意补充接口，这里就不多说了。

4. **注册中心的配置**

   注册中心具有 **服务发现**的能力，也就是说 **dubbo把注册实例地址**给 注册中心，然后消费者通过订阅中心的变更获取新的实例的变化，从而把流量转发到正确的节点上。

还有其他要学的得拿到一个示例才能开始学QAQ



## 启动nacos

启动nacos，我们可以打开官方文档按照步骤来执行命令

1. 首先找到没有中文路径的文件夹，创建一个 **非中文命名的英文文件夹**

2. 然后在新建文件夹里面git clone nacos的源码下来

3. 接着打开终端，执行下面命令

   ```markdown
   nacos-setup
   ```

   

4. 接着找到终端对应写nacos的账号密码，把dubbo项目中的密码修改一下

5. 最后，我们能看到，项目运行起来，可以跑通了

![image-20260731224126802](https://github.com/daffodil-nian/book/blob/main/tech_notes/demo_project/images/nacos-username-pic.png)

![image-20260731224405414](https://github.com/daffodil-nian/book/blob/main/tech_notes/demo_project/images/dubbo-res-pic.png)

这是nacos的官方文档

https://nacos.io/docs/latest/quickstart/quick-start/?spm=5238cd80.2ef5001f.0.0.3f613b7cKgZ9b0

![image-20260731230408144](https://github.com/daffodil-nian/book/blob/main/tech_notes/demo_project/images/nacos-center.png)



## 补充点

### 设置JAVA_HOME

下面的命令可以指定当前页面的JAVA_HOME，能够避免**直接修改**环境变量中JAVA_HOME的值

```markdown
$env:JAVA_HOME

```

### 查看一个jar包是怎么被引入进来的

```markdown
[ERROR] No plugin found for prefix '.springframework' in the current project and in the plugin groups [org.apache.maven.plugins, org.codehaus.mojo] available from the repositories [local (C:\Users\lily\.m2\repository), apache.snapshots (https://repository.apache.org/snapshots), central (https://repo.maven.apache.org/maven2)] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/NoPluginFoundForPrefixException

```

可以根据这个命令查看

```bash
mvn dependency:tree "-Dincludes=org.springframework:spring-core"
```

结果如下：

```markdown
[INFO] --- dependency:3.6.1:tree (default-cli) @ dubbo-samples-spring-boot-consumer ---
[INFO] org.apache.dubbo:dubbo-samples-spring-boot-consumer:jar:1.0-SNAPSHOT
[INFO] \- org.springframework.boot:spring-boot-starter:jar:3.2.3:compile
[INFO]    \- org.springframework:spring-core:jar:5.3.39:compile

```

可以人为修改版本号，这样就不会引入 5.x.x 版本的spring-framework了

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>6.1.4</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

接下来运行dubbo代码，但是爆出这种错误

```bash
java.lang.ClassNotFoundException: org.slf4j.spi.LoggingEventBuilder
```

可以发现，**slf4j**这家伙又给我们“惹事”了，冲突slf4j版本，咱最好不要引入这个东西，我们改改版本号

```xml
<dependency>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-api</artifactId>
          <version>2.0.9</version>
        </dependency>
```

下面是我查阅的一些文章

https://blog.csdn.net/weixin_41276656/article/details/129972499?ops_request_misc=elastic_search_misc&request_id=5ba7d5e506ef6fdcdb01894594873766&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~ElasticSearch~search_v2-2-129972499-null-null.142^v102^pc_search_result_base9&utm_term=%E6%80%8E%E4%B9%88%E6%9F%A5%E7%9C%8Bjar%E5%8C%85%E6%98%AF%E6%80%8E%E4%B9%88%E8%A2%AB%E8%B0%81%E5%BC%95%E5%85%A5%E7%9A%84&spm=1018.2226.3001.4187