如果您需要在同一进程中运行多个管道，Logstash提供了一种通过名为pipelines.yml的配置文件来完成此操作的方法。 该文件必须放置在path.settings文件夹中，并遵循以下结构：

Bash

```bash
- pipeline.id: my-pipeline_1
  path.config: "/etc/path/to/p1.config"
  pipeline.workers: 3
- pipeline.id: my-other-pipeline
  path.config: "/etc/different/path/p2.cfg"
  queue.type: persisted
```

​       该文件在YAML中格式化，并包含一个字典列表，其中每个字典描述一个管道，每个键/值对指定该管道的设置。  该示例显示了由它们的ID和配置路径描述的两个不同的管道。  对于第一个管道，pipeline.workers的值设置为3，而另一个则启用持续队列功能。  未在pipelines.yml文件中明确设置的设置的值将回退到logstash.yml设置文件中指定的默认值。

​     <font color="red"> 在不带参数的情况下启动Logstash时，它将读取pipelines.yml文件并实例化文件中指定的所有管道。 另一方面，当使用-e或-f时，Logstash会忽略pipelines.yml文件并记录警告。</font>

#### 使用注意事项

​       如果当前的配置具有不共享相同inputs/filters and  outputs并且使用标记和条件相互分离的事件流，则使用多个管道尤其有用。  在单个实例中具有多个管道还允许这些事件流具有不同的性能和持久性参数（例如，different settings for pipeline  workers and persistent queues）。这种分离意味着一个管道中的阻塞输出不会对另一个管道产生反压力。

​     也就是说，考虑到管道之间的资源竞争是重要的，因为默认值是针对单一管道进行调整的。 因此，例如，考虑减少每个管道使用的管道工的数量，因为每个管道默认使用每个CPU核心1个worker。

​     持久队列和死信队列在每个流水线上是隔离的，它们的位置由pipeline.id值隔开。