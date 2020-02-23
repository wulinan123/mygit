# ***MAVEN笔记***

* ## Maven就是是专门为Java项目打造的管理和构建工具

## 主要功能：

	*  提供了一套标准化的项目结构
	*  提供编译、测试、打包、发布等步骤的标准化构建流程
	*  提供一套依赖管理机制

##  项目结构：

```ascii
a-maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```

项目的根目录`a-maven-project`是项目名，它有一个项目描述文件`pom.xml`，存放Java源码的目录是`src/main/java`，存放资源文件的目录是`src/main/resources`，存放测试源码的目录是`src/test/java`，存放测试资源的目录是`src/test/resources`，最后，所有编译、打包生成的文件都放在`target`目录里。这些就是一个Maven项目的标准目录结构。

*不要随意修改目录结构*

##  pom.xml

	* 最关键的一个项目描述文件

* 样式：

```xml
<project ...>
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.itranswarp.learnjava</groupId>
	<artifactId>hello</artifactId>
	<version>1.0</version>
	<packaging>jar</packaging>
	<properties>
        ...
	</properties>
	<dependencies>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
	</dependencies>
</project>
```

`groupID`:类似java包名，通常为公司名

`artifactID`:类似java类名，通常为项目名

`version`: 工程版本号，用于区分不同版本

`dependency`:使用`dependency`声明一个依赖后，Maven就会自动下载这个依赖包并把它放到classpath中

* 全部标签：https://www.runoob.com/maven/maven-pom.html

## 依赖管理：

Maven解决了依赖管理问题，无需我们去手动添加需要的依赖（jar包），只要我们声明所需的依赖，它会自动解析并判断所需的依赖及其他的必需依赖包；

## 依赖关系：

`compile`:编译时需要该jar包（默认选项）

`test`: 编译Test时需要该jar包

`runtime`: 编译时不需要但运行时需要

`provided`:编译时需要，但运行时由JDK或某个服务器提供

* 在pow.xml文件中使用`<scope>`标签定义依赖关系；

* 实例：

  ```
  <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.3.2</version>
      <scope>test</scope>
  </dependency>
  ```

* Maven维护了一个中央仓库（[repo1.maven.org](https://repo1.maven.org/)），所有第三方库将自身的jar以及相关信息上传至中央仓库，所以Maven可以从中央仓库把所需依赖下载到本地。一个jar包一旦被下载过，就会被Maven自动缓存在本地目录（用户主目录的`.m2`目录）。

* 搜索第三方组件：通过[search.maven.org](https://search.maven.org/)搜索关键字，找到对应的组件后，直接复制

## 生命周期：

- `lifecycle`相当于Java的package，它包含一个或多个phase；
- `phase`相当于Java的class，它包含一个或多个goal；
- `goal`相当于class的method，是最小的任务单元，它其实才是真正干活的。

Maven的生命周期由一系列阶段（phase）构成，默认执行内置周期`default`<br>我们使用`mvn`命令时，后面的参数是要运行到的阶段<br>如果我们运行`mvn package`，就会执行default生命周期，从开始一直运行到`package`这个阶段为止；而执行一个阶段又会触发多个goal。

## Maven插件：

每个生命周期中都包含着一系列的阶段(phase)。这些 phase 就相当于 Maven 提供的统一的接口，然后这些 phase 的实现由 Maven 的插件来完成。

输入 `mvn clean`，clean 对应的就是 Clean 生命周期中的 clean 阶段,但是 clean 的具体操作是由插件**maven-clean-plugin** 来实现的。<br>所以说 Maven 生命周期的每一个阶段的具体实现都是由 Maven 插件实现的。

Maven 实际上是一个依赖插件执行的框架，每个任务实际上是由插件完成。

##  模块管理：

在软件开发中，把一个大项目分拆为多个模块是降低软件复杂度的有效方法，Maven可以将一个项目拆分成多个模块，每个都可以当作独立的Maven项目，含有独立的pow.xml。

```ascii
mutiple-project
├── module-a
│   ├── pom.xml
│   └── src
├── module-b
│   ├── pom.xml
│   └── src
└── module-c
    ├── pom.xml
    └── src
```

如果存在一些模块的pow.xml有很多重复，可以提取共同部分作为`parent`:

```ascii
multiple-project
├── pom.xml
├── parent
│   		└── pom.xml
├── module-a
│   		├── pom.xml
│   		└── src
├── module-b
│ 		  	├── pom.xml
│  			└── src
└── module-c
  				├── pom.xml
  				└── src
```



## 仓库分类：

### 中央仓库

其实我们使用的大多数第三方模块都是这个用法，例如，我们使用commons logging、log4j这些第三方模块， 就是第三方模块的开发者自己把编译好的jar包发布到Maven的中央仓库中。

### 私有仓库

私有仓库是指公司内部如果不希望把源码和jar包放到公网上，那么可以搭建私有仓库。私有仓库总是在公司内部使用，它只需要在本地的`~/.m2/settings.xml`中配置好，使用方式和中央仓位没有任何区别。

### 本地仓库

本地仓库是指把本地开发的项目“发布”在本地，这样其他项目可以通过本地仓库引用它。但是我们不推荐把自己的模块安装到Maven的本地仓库，因为每次修改某个模块的源码，都需要重新安装，非常容易出现版本不一致的情况。更好的方法是使用模块化编译，在编译的时候，告诉Maven几个模块之间存在依赖关系，需要一块编译，Maven就会自动按依赖顺序编译这些模块。

## Maven Wrapper

### （暂未学习）

默认情况下，系统所有项目都会使用全局安装的Maven版本。如果对于某些项目必须使用某个特定的Maven版本就需要使用Maven Wrapper，它可以负责给这个特定的项目安装指定版本的Maven，而其他项目不受影响。

