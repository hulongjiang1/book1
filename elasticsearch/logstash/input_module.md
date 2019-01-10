Logstash由三个组件构造成，分别是input、filter以及output。我们可以吧Logstash三个组件的工作流理解为：input收集数据，filter处理数据，output输出数据。至于怎么收集、去哪收集、怎么处理、处理什么、怎么发生以及发送到哪等等一些列的问题就是我们接下啦要讨论的一个重点。

```
我们今天先讨论input组件的功能和基本插件。前面我们意见介绍过了，input组件是Logstash的眼睛和鼻子，负责收集数据的，那么们就不得不思考两个问题，第一个问题要清楚的就是，元数据在哪，当然，这就包含了元数据是什么类型，属于什么业务；第二个问题要清楚怎么去拿到元数据。只要搞明白了这两个问题，那么Logstash的input组件就算是弄明白了。
对于第一个问题，元数据的类型有很多，比如说你的元数据可以是日志、报表、可以是数据库的内容等等。元数据是什么样子的我们不需要关心，我们要关系的是元数据是什么类型的，只要你知道元数据是什么类型的，你才能给他分类，或者说给他一个type，这很重要，type对于你后面的工作处理是非常有帮助的。所以第一个问题的重心元数据在吗，是什么，现在已经是清楚了。那么进行第二个问题。
第二个问题的核心是怎么拿到这些不同类型的原数据？这是一个真个input组件的核心内容了，我们分门别类的来看待这和解决个问题。
首先，我们肯定需要认同的，什么样的数据源，就需要使用什么样的方式去获取数据。
```

我们列举几种：
1、文件类型：文件类型，顾名思义，文件数据源，我们可以使用input组件的file插件来获取数据。file{}插件有很多的属性参数，我们可以张开讲解一下。具体内容在下面的代码中展示：

```
input{
    file{
        #path属性接受的参数是一个数组，其含义是标明需要读取的文件位置
        path => [‘pathA’，‘pathB’]
        #表示多就去path路径下查看是够有新的文件产生。默认是15秒检查一次。
        discover_interval => 15
        #排除那些文件，也就是不去读取那些文件
        exclude => [‘fileName1’,‘fileNmae2’]
        #被监听的文件多久没更新后断开连接不在监听，默认是一个小时。
        close_older => 3600
        #在每次检查文件列 表的时候， 如果一个文件的最后 修改时间 超过这个值， 就忽略这个文件。 默认一天。
        ignore_older => 86400
        #logstash 每隔多 久检查一次被监听文件状态（ 是否有更新） ， 默认是 1 秒。
        stat_interval => 1
        #sincedb记录数据上一次的读取位置的一个index
        sincedb_path => ’$HOME/. sincedb‘
        #logstash 从什么 位置开始读取文件数据， 默认是结束位置 也可以设置为：beginning 从头开始
        start_position => ‘beginning’
        #注意：这里需要提醒大家的是，如果你需要每次都从同开始读取文件的话，关设置start_position => beginning是没有用的，你可以选择sincedb_path 定义为 /dev/null
    }            

}
```

2、数据库类型：数据库类型的数据源，就意味着我们需要去和数据库打交道了是吗？是的！那是必须的啊，不然怎么获取数据呢。input组件如何获取数据库类的数据呢？没错，下面即将隆重登场的是input组件的JDBC插件jdbc{}。同样的，jdbc{}有很多的属性，我们在下面的代码中作出说明；

```
input{
    jdbc{
    #jdbc sql server 驱动,各个数据库都有对应的驱动，需自己下载
    jdbc_driver_library => "/etc/logstash/driver.d/sqljdbc_2.0/enu/sqljdbc4.jar"
    #jdbc class 不同数据库有不同的 class 配置
    jdbc_driver_class => "com.microsoft.sqlserver.jdbc.SQLServerDriver"
    #配置数据库连接 ip 和端口，以及数据库    
    jdbc_connection_string => "jdbc:sqlserver://200.200.0.18:1433;databaseName=test_db"
    #配置数据库用户名
    jdbc_user =>   
    #配置数据库密码
    jdbc_password =>
    #上面这些都不重要，要是这些都看不懂的话，你的老板估计要考虑换人了。重要的是接下来的内容。
    # 定时器 多久执行一次SQL，默认是一分钟
    # schedule => 分 时 天 月 年  
    # schedule => * 22  *  *  * 表示每天22点执行一次
    schedule => "* * * * *"
    #是否清除 last_run_metadata_path 的记录,如果为真那么每次都相当于从头开始查询所有的数据库记录
    clean_run => false
    #是否需要记录某个column 的值,如果 record_last_run 为真,可以自定义我们需要表的字段名称，
    #此时该参数就要为 true. 否则默认 track 的是 timestamp 的值.
    use_column_value => true
    #如果 use_column_value 为真,需配置此参数. 这个参数就是数据库给出的一个字段名称。当然该字段必须是递增的，可以是 数据库的数据时间这类的
    tracking_column => create_time
    #是否记录上次执行结果, 如果为真,将会把上次执行到的 tracking_column 字段的值记录下来,保存到 last_run_metadata_path 指定的文件中
    record_last_run => true
    #们只需要在 SQL 语句中 WHERE MY_ID > :last_sql_value 即可. 其中 :last_sql_value 取得就是该文件中的值
    last_run_metadata_path => "/etc/logstash/run_metadata.d/my_info"
    #是否将字段名称转小写。
    #这里有个小的提示，如果你这前就处理过一次数据，并且在Kibana中有对应的搜索需求的话，还是改为true，
    #因为默认是true，并且Kibana是大小写区分的。准确的说应该是ES大小写区分
    lowercase_column_names => false
    #你的SQL的位置，当然，你的SQL也可以直接写在这里。
    #statement => SELECT * FROM tabeName t WHERE  t.creat_time > :last_sql_value
    statement_filepath => "/etc/logstash/statement_file.d/my_info.sql"
    #数据类型，标明你属于那一方势力。单了ES哪里好给你安排不同的山头。
    type => "my_info"
    }
    #注意：外载的SQL文件就是一个文本文件就可以了，还有需要注意的是，一个jdbc{}插件就只能处理一个SQL语句，
    #如果你有多个SQL需要处理的话，只能在重新建立一个jdbc{}插件。
}
```

好了，废话不多说了，接着第三种情况：

```
input {
  beats {
    #接受数据端口
    port => 5044
    #数据类型
    type => "logs"
  }
  #这个插件需要和filebeat进行配很这里不做多讲，到时候结合起来一起介绍。
}
```

现在我们基本清楚的知道了input组件需要做的事情和如何去做，当然他还有很多的插件可以进行数据的收集，比如说TCP这类的，还有可以对数据进行encode，这些感兴趣的朋友可以自己去查看，我说的只是我自己使用的。一般情况下我说的三种插件已经足够了。
今天的ELK种的Logstash的input组件就到这。后面还会讲述Logstash的另外另个组件filter和output。