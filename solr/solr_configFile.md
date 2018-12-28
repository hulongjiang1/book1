# Solr配置文件

 由 Carrie 创建， 最后一次修改 2017-11-06     

在 Solr 中有几个配置文件，您将在执行过程中与之交互。     


这些文件中的很多都是 XML 格式的，尽管与配置设置交互的 API 在需要时往往接受 JSON 以进行编程访问。

## Solr Home



在运行 Solr 时，您需要访问主目录。

当您第一次安装 Solr 时，您的主目录是：server/solr。但是，一些示例可能会更改此位置（例如，如果您运行：bin/solr start -e cloud，您的主目录将会是：example/cloud）。

Solr 主目录包含重要的配置信息，并且是 Solr 将存储其索引的地方。当您在独立模式下与在 SolrCloud 模式下运行 Solr 时，主目录的布局看起来会有所不同。

以下示例显示了 Solr 主目录中的关键部分：

### 示例-独立模式

```
<solr-home-directory>/
   solr.xml
   core_name1/
      core.properties
      conf/
         solrconfig.xml
         managed-schema
      data/
   core_name2/
      core.properties
      conf/
         solrconfig.xml
         managed-schema
      data/
```

### 示例-SolrCloud 模式

```
<solr-home-directory>/
   solr.xml
   core_name1/
      core.properties
      data/
   core_name2/
      core.properties
      data/
```

您可能会看到其他文件，但您需要了解的主要部分将在下一节中讨论。

## Solr 配置文件

在 Solr 的主页中，你会发现这些文件：

- solr.xml：为您的 Solr 服务器实例指定配置选项。有关 solr.xml 的更多信息，请参阅：Solr Cores 和 solr.xml。
- 每个 Solr 核心：         
  - core.properties：为每个核心定义特定的属性，例如其名称、核心所属的集合、模式的位置以及其他参数。有关 core.properties 的更多详细信息，请参阅定义 core.properties 一节。
  - solrconfig.xml：控制高级行为。例如，您可以为数据目录指定一个备用位置。有关 solrconfig.xml 的更多信息，请参阅 配置 solrconfig.xml。
  - managed-schema（或用 schema.xml 替代）描述您将要求 Solr  索引的文档。模式将文档定义为字段集合。您可以同时定义字段类型和字段本身。字段类型定义功能强大，包含有关 Solr  如何处理传入字段值和查询值的信息。有关 Solr 架构的更多信息，请参阅文档、字段和模式设计以及模式 API。
  - data/：包含低级索引文件的目录。

请注意，SolrCloud 示例不包括每个 Solr Core 的 conf 目录（所以没有 solrconfig.xml 或 Schema 文件）。这是因为通常在 conf 目录中找到的配置文件存储在ZooKeeper 中，所以它们可以在群集中传播。

如果您正在使用 SolrCloud 与嵌入式 ZooKeeper 的情况下，您还可以看到 zoo.cfg 和 zoo.data，它们是  ZooKeeper 的配置和数据文件。但是，如果您正在运行自己的 ZooKeeper 集成，则您在启动 ZooKeeper  配置文件时，将会提供您自己的 ZooKeeper 配置文件，而 Solr 中的副本将不会被使用。有关 SolrCloud 的更多信息，请参阅  SolrCloud 部分。