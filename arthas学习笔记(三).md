#### 使用案例

排查spring boot应用`java.lang.NoSuchMethodError`

**编写测试demo**

```java
@SpringBootApplication
public class DemoNoSuchMethodErrorApp {

    public static void main(String[] args) throws IOException {
        try {
            SpringApplication.run(DemoNoSuchMethodErrorApp.class, args);
        } catch (Throwable e) {
            e.printStackTrace();
        }
        System.in.read();
    }
}
```

启动demo抛如下异常：

```java
java.lang.NoSuchMethodError: org.springframework.core.annotation.AnnotationAwareOrderComparator.sort(Ljava/util/List;)V
	at org.springframework.boot.SpringApplication.getSpringFactoriesInstances(SpringApplication.java:394)
	at org.springframework.boot.SpringApplication.getSpringFactoriesInstances(SpringApplication.java:383)
	at org.springframework.boot.SpringApplication.initialize(SpringApplication.java:249)
	at org.springframework.boot.SpringApplication.<init>(SpringApplication.java:225)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1118)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1107)
	at com.whuaz.arthas.demo.DemoNoSuchMethodErrorApp.main(DemoNoSuchMethodErrorApp.java:17)
```

根据异常提示，`AnnotationAwareOrderComparator`缺少sort(Ljava/util/List;)V方法

**启动arthas**

```shell
./as.sh
Arthas script version: 3.0.5
[INFO] JAVA_HOME: /home/grez/jdk/jdk1.8.0_181
Found existing java process, please choose one and hit RETURN.
* [1]: 24995 com.intellij.idea.Main
  [2]: 7413 /home/grez/dbeaver//plugins/org.eclipse.equinox.launcher_1.4.0.v20161219-1356.jar
  [3]: 13510 com.whuaz.arthas.demo.DemoNoSuchMethodErrorApp
  [4]: 12921 org.jetbrains.idea.maven.server.RemoteMavenServer
3
```

attatch到demo进程后

**使用sc命令查找该类所在的包**

```shell
$ sc -d org.springframework.core.annotation.AnnotationAwareOrderComparator
class-info        org.springframework.core.annotation.AnnotationAwareOrderComparator
code-source       /home/grez/apache-maven-repository/org/springframework/spring/2.5.6.SEC03/spring-2.5.6.SEC03.jar
name              org.springframework.core.annotation.AnnotationAwareOrderComparator
isInterface       false
isAnnotation      false
isEnum            false           
isAnonymousClass  false             
isArray           false              
isLocalClass      false             
isMemberClass     false            
isPrimitive       false                      
isSynthetic       false
simple-name       AnnotationAwareOrderComparator        
modifier          public                        
annotation                          
interfaces                     
super-class       +-org.springframework.core.OrderComparator 
+-java.lang.Object                              
class-loader      +-sun.misc.Launcher$AppClassLoader@18b4aac2           
+-sun.misc.Launcher$ExtClassLoader@4e515669                                    
Affect(row-cnt:1) cost in 13 ms.
```

发现`AnnotationAwareOrderComparator`从spring-2.5.6.SEC03.jar里面加载

**使用jad反编译代码**

```shell
$ jad org.springframework.core.annotation.AnnotationAwareOrderComparator

ClassLoader:                               
+-sun.misc.Launcher$AppClassLoader@18b4aac2 
  +-sun.misc.Launcher$ExtClassLoader@4e515669
         
Location:                                                                                                                                                                                                   
/home/grez/apache-maven-repository/org/springframework/spring/2.5.6.SEC03/spring-2.5.6.SEC03.jar                                                                                                            

/*
 * Decompiled with CFR 0_132.
 */
package org.springframework.core.annotation;

import java.lang.annotation.Annotation;
import org.springframework.core.OrderComparator;
import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;

public class AnnotationAwareOrderComparator
extends OrderComparator {
    protected int getOrder(Object obj) {
        Order order;
        if (obj instanceof Ordered) {
            return ((Ordered)obj).getOrder();
        }
        if (obj != null && (order = obj.getClass().getAnnotation(Order.class)) != null) {
            return order.value();
        }
        return Integer.MAX_VALUE;
    }
}
Affect(row-cnt:1) cost in 658 ms.
```

发现该类里并没有`sort(Ljava/util/List;)V`方法。

因此检查依赖，排除该`spring-2.5.6.SEC03.jar`依赖

重新启动项目后异常解决。