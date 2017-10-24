##Java9 相关笔记
### 1 编译及执行命令
#### 1.1 编译命令
$javac -d mods --module-source-path src $(find src -name "*.java")

#### 1.2 打包命令
$jar --create --file lib/org.eugene.simple-module-1.0.jar --main-class Welcome --module-version 1.0 -C mods/org.eugene.module.simple .

#### 1.3 运行命令
$java --module-path lib --module org.eugene.module.simple
或者
$java --module-path mods -m org.eugene.module.simple/org.eugene.module.simple.Welcome

### 2 模块module总结
如果模块需要使用另一个模块中包含的公共类型，则第二个模块需要导出包含类型的包，而第一个模块需要读取第二个模块。

一个模块使用exports语句导出其包。 模块可以将其软件包导出到特定模块。 导出包中的公共类型在编译时和运行时可用于其他模块。 导出的包不允许对公共类型的非公开成员深层反射。

¡如果一个模块允许其他模块访问所有类型的成员（公共和非公共）使用反射，则该模块必须被声明为开放模块，或者模块可以使用打开语句选择性地开放包。 从开放包中访问类型的模块不需要读取包含这些打开包的模块。

一个模块使用require语句来声明对另一个模块的依赖。 这种依赖可以使用transitive修饰符声明为传递依赖。 如果模块M声明对模块N的传递依赖性，则声明对模块M依赖的任何模块声明对模块N的隐含依赖。

依赖关系可以在编译时声明为必须的，但在运行时可以使用require语句中的static修饰符为可选依赖。 依赖关系在运行时可以同时是可选和传递的。

JDK 9中的模块系统已经改变了公共类型（public）的含义。 在模块中定义的公共类型可能属于三个类别之一：仅在定义模块中公开，仅限于特定模块，或向所有人公开。

基于一个模块的声明以及它是否有一个名称，有几种类型的模块。基于模块是否具有名称，模块可以是命名模块或未命名模块。当模块具有名称时，可以在模块声明中明确指定名称，或者可以自动（或隐式地）生成名称。如果名称在模块声明中明确指定，则称为显式模块。如果名称由模块系统通过读取模块路径上的JAR文件名生成，则称为自动模块。如果您在不使用open修饰符的情况下声明模块，则称为正常模块。如果使用打开的修饰符声明模块，则称为开放模块。基于这些定义，开放模块也是显式模块和命名模块。自动模块是一个命名模块，因为它具有自动生成的名称，但它不是显式模块，因为它在模块系统在编译时和运行时被隐式声明。

将JAR（而不是模块JAR）放在模块路径上时，JAR表示一个自动模块，其名称是从JAR文件名派生的。 自动模块读取所有其他模块，并将其所有软件包导出并打开。

在JDK 9中，类加载器可以从模块或类路径加载类。 每个类加载器都维护一个名为未命名模块的模块，该模块包含从类路径加载的所有类型的模块。 一个未命名的模块读取每个其他模块。 它导出并打开所有其他模块的所有包。 未命名的模块没有名称，因此显式模块无法声明对未命名模块的编译时依赖。 如果显式模块需要访问未命名模块中的类型，则前者可以使用自动模块作为桥梁或使用反射。

可以使用javap工具打印模块声明或属性。 使用工具的-verbose（或-v）选项打印模块描述符的类属性。 JDK 9以特殊格式存储运行时映像。 JDK 9引入了一个名为jrt的新文件方案，可以使用它来访问运行时映像的内容。 它的语法是jrt：/ <module> / <path-to-a-file>。
