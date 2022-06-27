# 15 基于openGauss自定义SQL命令

Q: 选择了opengauss自定义sql命令，请问需要看哪些资料和任务的具体要求是什么？

A: 任务要求的话，就是理解opengauss的sql引擎，看一下它从接收到一条sql语句之后是怎么解析，然后形成执行计划，获取数据返回。在解析过程中，就有自定义SQL命令的思路，可以借鉴一下思路，看看能不能实现自定义的增删查改命令，比如自定义的watch tableName命令可以达到select * from tableName的效果。

然后不完全借鉴它源码的思路，看看能不能找到其他的实现方法。

资料可以看看这个，作为入门了解https://blog.csdn.net/gaussdb/category_11159303.html。

opengauss本质是对postgresql套了层皮，如果用opengauss关键字找不到资料可以换成postgre找一下。

------

安装openGauss参考资料：[centos7.6编译安装openGauss2.1.0.pdf](https://github.com/wencongzhao/opengauss_custom_SQL/blob/main/reference/centos7.6编译安装openGauss2.1.0.pdf)

自定义SQL需要词法解析和语法解析前置知识，可学习flex&bison；编译的时候使用make -j4可以加快速度。

有问题继续QQ交流讨论

