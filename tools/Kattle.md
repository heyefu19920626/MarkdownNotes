# Kattle

Kettle这个ETL(Extract-Transform-Load的缩写,即数据抽取、转换、装载的过程)工具集,它允许你管理来自不同数据库的数据,通过提供一个图形化的用户环境来描述你想做什么,而不是你想怎么做。  
Kettle中有两种脚本文件,transformation(.ktr)和job(.kjb),transformation完成针对数据的基础转换,job则完成整个工作流的控制.

1. Transformation: 定义对数据操作的容器,数据操作就是数据从输入到输出的一个过程,可以理解为比Job粒度更小一级的容器,我们将任务分解成Job,然后需要将Job分解成一个或多个Transformation,每个Transformation只完成一部分工作.(定义对数据操作的容器,数据操作就是数据从输入到输出的一个过程,可以理解为比作业粒度更小一级的容器,我们将任务分解成作业,然后需要将作业分解成一个或多个转换,每个转换只完成一部分工作.)
2. Step:是Transformation内部的最小单元，每一个Step完成一个特定的功能
3. Job：负责将Transformation组织在一起进而完成某一工作，通常我们需要把一个大的任务分解成几个逻辑上隔离的Job，当这几个Job都完成了，也就说明这项任务完成了。
（负责将[转换]组织在一起进而完成某一块工作，通常我们需要把一个大的任务分解成几个逻辑上隔离的作业，当这几个作业都完成了，也就说明这项任务完成了.)
4. Job Entry：Job Entry是Job内部的执行单元，每一个Job Entry用于实现特定的功能，如：验证表是否存在，发送邮件等。可以通过Job来执行另一个Job或者Transformation，也就是说Transformation和Job都可以作为Job Entry
5. Hop：用于在Transformation中连接Step，或者在Job中连接Job Entry，是一个数据流的图形化表示  

在Kettle中Job中的JobEntry是串行执行的，故Job中必须有一个Start的JobEntry；Transformation中的Step是并行执行的