---
title: maven-dependence
date: 2019-05-30 
desc:
keywords: maven 
categories: [tool]
---

# scope参数在dependence中表示依赖的范围。

首先需要知道，maven在编译项目主代码的时候需要使用一套classpath。例如在编译项目主代码的时候需要用到spring-core，该文件以依赖的方式被引入到classpath中。
其次，maven在编译和执行测试的时候会使用另一套classpath，Junit就是一个很好的例子，该文件也以依赖的方式引入到测试使用的classpath中，不同的是这里的依赖
范围是test。最后，实际运行maven项目的时候，又会使用一套classpath。

依赖范围就是用来控制依赖与这三种classpath（编辑classpath、测试classpath、运行classpath）的关系，maven有以下几种依赖范围：

**Compile**：编译依赖范围。如果没有指定，就会默认使用该依赖范围。使用此依赖范围的maven依赖，对于**编译、测试、运行**三种classpath都有效。
典型的例子是spring-core，在编译、测试和运行的时候都需要使用该依赖。

**Test**：测试依赖范围。使用此依赖范围的maven依赖，只对于**测试**的classpath有效，在编译主代码或者运行项目的使用时将无法使用此类依赖。典型的例子是Junit，
它只有在编辑测试代码及运行测试的时候才需要。

**Provided**：已提供依赖范围。使用此依赖范围的maven依赖，对于**编译**和**测试**classpath有效，但在运行时无效。典型的例子是servlet-api，
编译和测试项目的时候需要该依赖，但在运行项目的时候，由于容器已经提供，就不需要maven重复的引入一遍。

**Runtime**：运行时依赖范围。使用该依赖范围的maven依赖，对于测试和运行classpath有效，但在编译主代码时无效。典型的例子是JDBC驱动实现，项目主代码的编译
只需要JDK提供的JDBC接口，只有在执行测试或者运行项目的时候才需要实现上述的具体JDBC驱动。

**System**：系统依赖范围。该依赖与三种classpath的关系，**和Provided依赖范围完全一致**。但是，使用System范围的依赖是必须通过systemPath元素显示地指定
依赖文件的路径。由于此类依赖不是通过maven仓库解析的，而且往往与本机的系统绑定，可能造成构建的不可移植，因此应该谨慎使用。systemPath元素可以引用环境
变量，如：

    <dependency>
        <groupId>java.sql</groupId>
        <artifactId>jdbc-stdext</artifactId>
        <version>2.0</version>
        <scope>system</scope>
        <systemPath>${java.home}/lib/rt.jar</systemPath>
    </dependency>
    
**Import(maven2.0.9及以上)**：导入依赖范围。该依赖范围不会对3中classpath产生实际影响。    

上述出import依赖的各种依赖范围3中classpath的关系如下表：

|    依赖范围(scope)    |      编译classpath有效       |      测试classpath有效     |      运行classpath有效     |      例子     |
|:-------:|:------- | :--------  | :--------| :---------|
|   compile  |     Y    |     Y    |     Y      |  spring-core  |
|   test     |     -    |     Y    |     -      |     junit     |
|   provided |     Y    |     Y    |     -      |  servlet-api  |
|   runtime  |     -    |     Y    |     Y      |    JDBC驱动   |
|   system   |     Y    |     Y    |     -      |  本地的，maven仓库之外的类库文件  |

# maven传递性依赖

传递性依赖和依赖范围：

依赖范围不仅可以控制依赖与三种classpath的关系，还对传递性依赖产生影响。例，account-email对于spring-core的依赖范围是compile，spring-core对于commons-logging
的依赖范围的是compile，那么account-email对于commons-logging这一传递依赖的范围也及时compile。假设A依赖与B，B依赖与C，我们说A对于B的依赖是第一直接依赖，
B对于C的依赖为第二直接依赖，A对于C是传递性依赖。第一直接依赖的范围是和第二直接依赖的范围决定了传递性依赖的范围，如下表所示，最左边一行表示第一直接依赖范围，
最上面一行便是第二直接依赖范围，中间的交叉单元格则便是传递性依赖范围:

|        |    compile  |   test   |    provided   |   runtime   |
|:-------:|:------- | :--------  | :--------| :---------|
|   compile  | compile |     -    |     -      |  runtime  |
|   test     |  test   |     -    |     -      |  test     |
|  provided  | provided |    -    |  provided  |  provided |
|   runtime  | runtime |     -    |     -      |  runtime  |

仔细观察表格，可以发现规律：当第二直接依赖为compile的时候，传递性依赖的范围与第一直接依赖范围一致；当第二直接依赖范围是test的时候，依赖不会得以传递，
当第二直接依赖的范围是provided的时候，只传递第一直接依赖范围也为provided的依赖，且传递性依赖的范围同样为provided；当第二传递依赖是runtime的时候，
传递性依赖的范围与第一直接依赖的范围一致，丹compile例外，此时传递性依赖的范围为runtime。

## maven可选依赖

使用<optional>元素表示依赖为可选依赖，只会对当前项目产生影响，当其他项目依赖当前项目时，该依赖不会被传递
