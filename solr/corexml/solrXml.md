# solr.xml的格式

 由 Carrie 创建， 最后一次修改 2017-12-13     

solr.xml 文件定义了适用于全部或多个内核的全局配置选项。     
 

本节将介绍Solr附带的默认的solr.xml文件以及如何根据需要对其进行修改。有关如何配置core.properties的详细信息，请参阅“定义core.properties”一节。

## 定义solr.xml



您可以在您的$SOLR_HOME目录（通常是：server/solr）中以独立模式，或者在使用SolrCloud时在ZooKeeper中找到solr.xml。默认solr.xml文件如下所示：

```
<solr>

  <solrcloud>
    <str name="host">${host:}</str>
    <int name="hostPort">${jetty.port:8983}</int>
    <str name="hostContext">${hostContext:solr}</str>
    <int name="zkClientTimeout">${zkClientTimeout:15000}</int>
    <bool name="genericCoreNodeNames">${genericCoreNodeNames:true}</bool>
  </solrcloud>

  <shardHandlerFactory name="shardHandlerFactory"
    class="HttpShardHandlerFactory">
    <int name="socketTimeout">${socketTimeout:0}</int>
    <int name="connTimeout">${connTimeout:0}</int>
  </shardHandlerFactory>

</solr>
```

如您所见，发现 Solr 配置是“SolrCloud 友好的”。但是，<solrcloud>元素的存在并不意味着Solr实例在SolrCloud模式下运行。除非在启动时指定了-DzkHost或-DzkRun，本节被忽略。     
 

## Solr.xml参数



### <solr>元素

没有属性可以在<solr>标签中指定，这是solr.xml的根元素。下表列出了solr.xml中每个XML元素的子节点。     
 

- adminHandler

  这个属性不需要设置。

  如果使用，则应将此属性设置为继承自CoreAdminHandler的类的FQN（完全限定名称）。例如，`<str name="adminHandler">com.myorg.MyAdminHandler</str>`将配置自定义管理处理程序（MyAdminHandler）来处理管理请求。

  如果这个属性没有设置，Solr使用默认的管理处理程序，`org.apache.solr.handler.admin.CoreAdminHandler`。

- collectionsHandler

  如上所述，用于自定义CollectionsHandler实现。

- infoHandler

  如上所述，用于自定义InfoHandler实现。

- coreLoadThreads

  指定将分配给并行加载内核的线程数。

- coreRootDirectory

  核心发现树的根目录，默认为`$SOLR_HOME`（默认`server/solr`）。

- managementPath

  目前不可操作。

- sharedLib

  指定将在所有内核之间共享的公用库目录的路径。此目录中的任何JAR文件都将被添加到Solr插件的搜索路径中。这条路是相对`$SOLR_HOME`的。自定义处理程序可能被放置在这个目录中。

- shareSchema

  此属性设置为`true`，可确保指向同一个Schema资源文件的多个核心将引用同一个IndexSchema对象。共享IndexSchema对象使加载核心更快。如果您使用此功能，请确保您的架构文件中没有使用特定于核心的属性。

- transientCacheSize

  定义`transient=true`在最近使用最少的内核之前，可以加载多少个内核。

- configSetBaseDir

  可以找到用于Solr核心的configSets的目录。默认为`$SOLR_HOME/configsets`。

### <solrcloud>元素

这个元素定义了几个与SolrCloud相关的参数。除非Solr实例以-DzkRun或-DzkHost开始，否则这部分被忽略。

- distribUpdateConnTimeout

  用于设置集群内部更新的基础`connTimeout`。

- distribUpdateSoTimeout

  用于设置集群内部更新的基础`socketTimeout`。

- host

  Solr用于访问核心的主机名称。

- hostContext

  url上下文路径。

- hostPort

  Solr端口用于访问内核。

  在默认`solr.xml`文件中，设置为`${jetty.port:8983}`，将使用Jetty中定义的Solr端口，否则返回到8983。

- leaderVoteWait

  当SolrCloud启动时，每个Solr节点将等待该分片的所有已知副本多长时间，然后才能假定任何未报告的节点已关闭。

- leaderConflictResolveWait

  当试图为碎片选择一个引线时，该属性设置副本将等待查看冲突的状态信息以待解决的最大时间；在进行滚动重启时，特别是当托管监督员的节点重新启动时，可能会出现状态信息中的临时冲突。             
  ​         

  通常，`180000`（ms）的默认值足以解决冲突；如果您在SolrCloud中有数百或数千个小集合，则可能需要增加此值。

- zkClientTimeout

  连接到ZooKeeper服务器的超时。它与SolrCloud一起使用。

- zkHost

  在SolrCloud模式下，Solr应该用于集群状态信息的ZooKeeper主机的URL。

- genericCoreNodeNames

  如果为`TRUE`，则节点名称不是基于节点的地址，而是基于标识核心的通用名称。当一台不同的机器接管服务的时候，核心的东西就会更容易理解。

- zkCredentialsProvider ＆ zkACLProvider

  如果使用ZooKeeper访问控制，则这是可以指定的可选参数。

### <logging>元素

- class

  用于记录的类。相应的JAR文件必须可用于Solr，也许通过`solrconfig.xml`中的一个`<lib>`指令。

- enabled

  true / false - 是否启用日志记录。

#### <logging> <watcher>元素

- size               

  缓冲的日志事件的数量。

- threshold

  您的特定日志记录实现将记录到的日志记录级别。例如，当使用 log4j 时，可以指定DEBUG，WARN，INFO等。

### <shardHandlerFactory>元素

如果您希望创建自定义分片处理程序，则可以在solr.xml定义自定义分片处理程序：

```
<shardHandlerFactory name="ShardHandlerFactory" class="qualified.class.name">
```

由于这是自定义分片处理程序，因此子元素是特定于实现的。Solr提供的默认和唯一的分片处理程序是HttpShardHandlerFactory，在这种情况下，可以指定以下子元素：

- socketTimeout

  群集内查询和管理请求的读取超时。默认值与`<solrcloud>`节中指定的`distribUpdateSoTimeout`相同。

- connTimeout

  群集内查询和管理请求的连接超时。默认为`distribUpdateConnTimeout`在该`<solrcloud>`部分中指定的。

- urlScheme

  要在分布式搜索中使用的URL方案。

- maxConnectionsPerHost

  每个主机允许的最大连接数，默认为`20`。

- maxConnections

  允许的最大连接总数。默认为`10000`。

- corePoolSize

  线程池服务请求的初始核心大小。默认是`0`。

- maximumPoolSize

  线程池服务请求的最大大小。默认是无限的。

- maxThreadIdleTime

  空闲线程在被杀死之前在队列中持续的时间（以秒为单位）。默认是`5`秒。

- sizeOfQueue

  如果线程池使用后备队列，则使用直接切换的最大大小是多少？默认是使用SynchronousQueue。

- fairnessPolicy

  一个布尔值，用于配置线程池是否支持吞吐量的公平性。缺省值为false以支持吞吐量。

### <metrics>元素

solr.xml中的<metrics>元素允许您自定义Solr报告的度量标准。您可以定义不应该返回的系统属性，或定义自定义供应商和记者。     
 

在默认的 solr. xml 中，您将看不到任何<metrics>配置。如果您想为您的安装自定义度量标准，请参阅“指标配置”部分。

## 替换solr.xml中的JVM系统属性



Solr支持在solr.xml的JVM系统属性值的变量替换，它允许运行时指定各种配置选项。语法是：${propertyname[:option  default  value]}。这允许定义Solr启动时可以覆盖的默认值。如果未指定默认值，则必须在运行时指定该属性，否则该solr.xml文件将在分析时生成错误。     
 

在启动JVM时通常使用-D标志指定的任何JVM系统属性都可以用作solr.xml文件中的变量。

例如，在下面显示的solr.xml文件中，socketTimeout和connTimeout值都被设置为“0”。但是，如果您使用bin/solr   -DsocketTimeout=1000开启Solr，则HttpShardHandlerFactory的socketTimeout的选项要使用1000毫秒的值覆盖，而重写的connTimeout选项将继续使用默认属性值“0”。

```
<solr>
  <shardHandlerFactory name="shardHandlerFactory"
                       class="HttpShardHandlerFactory">
    <int name="socketTimeout">${socketTimeout:0}</int>
    <int name="connTimeout">${connTimeout:0}</int>
  </shardHandlerFactory>
</solr>
```