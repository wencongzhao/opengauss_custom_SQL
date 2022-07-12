# 15 基于openGauss自定义SQL命令

Q: 选择了opengauss自定义sql命令，请问需要看哪些资料和任务的具体要求是什么？

A: 任务要求的话，就是理解opengauss的sql引擎，看一下它从接收到一条sql语句之后是怎么解析，然后形成执行计划，获取数据返回。在解析过程中，就有自定义SQL命令的思路，可以借鉴一下思路，看看能不能实现自定义的增删查改命令，比如自定义的watch tableName命令可以达到select * from tableName的效果。

然后不完全借鉴它源码的思路，看看能不能找到其他的实现方法。

资料可以看看这个，作为入门了解https://blog.csdn.net/gaussdb/category_11159303.html

opengauss本质是对postgresql套了层皮，如果用opengauss关键字找不到资料可以换成postgre找一下。

------

安装openGauss参考资料：[centos7.6编译安装openGauss2.1.0.pdf](https://github.com/wencongzhao/opengauss_custom_SQL/blob/main/reference/centos7.6编译安装openGauss2.1.0.pdf)

自定义SQL需要词法解析和语法解析前置知识，可学习flex&bison；编译的时候使用make -j4可以加快速度。

有问题继续QQ交流讨论

------

20220712更新

1.重点关注`src\include\parser\kwlist.h`文件和`src\common\backend\parser\gram.y`文件。如果编译报相关错误，再关注一下`src\common\interfaces\libpq\frontend_parser\gram.y`文件。

2.可参考的资料

①.[openGauss数据库源码解析系列文章--openGauss简介（一）_Gauss松鼠会-CSDN博客_opengauss](https://blog.csdn.net/GaussDB/article/details/116132257)

②.[openGauss数据库源码解析系列文章—— SQL引擎源解析（一）_Gauss松鼠会-CSDN博客](https://blog.csdn.net/GaussDB/article/details/119594313)

③.[详解PostgreSQL语法中关键字的添加_""-其它代码类资源-CSDN文库](https://download.csdn.net/download/weixin_38601446/12826711?utm_source=iteye_new)

④[支持的sql语法_添加一个新的SQL语句_weixin_39940755的博客-CSDN博客](https://blog.csdn.net/weixin_39940755/article/details/112680979)

⑤.https://www.cnblogs.com/gaojian/archive/2012/09/10/2678227.html

第五个可以点进博主主页多看几篇相关的。

3.openGauss下一种实现自定义SQL语句的思路

简而言之：**替换**（或者叫**映射**）

所谓自定义SQL语句，并不是真正意义上的从设计词法分析，语法分析到语义检查以及如何生成相应执行计划这一整套实现流程的完全自定义。而是在保留相同的访问结果的基础上，将原本按照SQL规范写出来的比较长的SQL语句**映射**到可能不是那么规范的比较简短的SQL语句上，比如将获取整张表内容的`select * from table`语句映射到自定义的`shows table`语句上。

在`src\common\backend\parser\gram.y`文件里面，官方实现了一个`table tableName`的SQL语句。这条语句达到的效果跟`select * from tableName`是一样的。

![image-20211205233139668](C:\Users\WCZ\AppData\Roaming\Typora\typora-user-images\image-20211205233139668.png)

这就是所谓的**替换**（或者叫**映射**）的思路。实现自定义的增删查改的SQL语句，不过就是把原来符合标准的SQL语句的语法树，写到自定义SQL语句下的语法分支里。
