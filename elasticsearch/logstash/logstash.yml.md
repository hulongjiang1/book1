## logstash.yml

You can set options in the Logstash settings file, `logstash.yml`, to control Logstash execution. For example, you can specify pipeline settings, the location of configuration files, logging options, and other settings. Most of the settings in the `logstash.yml` file are also available as [command-line flags](https://www.elastic.co/guide/en/logstash/current/running-logstash-command-line.html#command-line-flags) when you run Logstash. Any flags that you set at the command line override the corresponding settings in the `logstash.yml` file.

logstash.yml文件使用YAML格式. 文件所在路径依据不同的平台而不同，可以参考 (see [Logstash Directory Layout](https://www.elastic.co/guide/en/logstash/current/dir-layout.html)). 可以按层次结构形式指定设置，也可以在一行中设置。例如，使用分层形式设置管道批大小和批延迟, 如下:

```yaml
pipeline:
  batch:
    size: 125
    delay: 50
```

在一行中表达，下面是相同的意思：

```yaml
pipeline.batch.size: 125
pipeline.batch.delay: 50
```

logstash.yml 文件还支持使用环境变量的bash样式插值

```yaml
pipeline:
  batch:
    size: ${BATCH_SIZE}
    delay: ${BATCH_DELAY:50}
node:
  name: "node_${LS_NODE_NAME}"
path:
   queue: "/tmp/${QUEUE_DIR:queue}"
```

支持这样的格式：`${VAR_NAME:default_value}`。在上面的例子中，设置了batch delay延时默认是50，path.queue的默认值是/temp/queue。

Modules may also be specified in the `logstash.yml` file. The modules definition will have this format:

```yaml
modules:
  - name: MODULE_NAME1
    var.PLUGIN_TYPE1.PLUGIN_NAME1.KEY1: VALUE
    var.PLUGIN_TYPE1.PLUGIN_NAME1.KEY2: VALUE
    var.PLUGIN_TYPE2.PLUGIN_NAME2.KEY1: VALUE
    var.PLUGIN_TYPE3.PLUGIN_NAME3.KEY1: VALUE
  - name: MODULE_NAME2
    var.PLUGIN_TYPE1.PLUGIN_NAME1.KEY1: VALUE
    var.PLUGIN_TYPE1.PLUGIN_NAME1.KEY2: VALUE
```

![Important](https://www.elastic.co/guide/en/logstash/current/images/icons/important.png)

If the [command-line flag](https://www.elastic.co/guide/en/logstash/current/running-logstash-command-line.html#command-line-flags) `--modules` is used, any modules defined in the `logstash.yml` file will be ignored.

The `logstash.yml` file includes the following settings. If you are using X-Pack, also see [X-Pack Settings in Logstash](http://www.elastic.co/guide/en/logstash/6.5/settings-xpack.html).

| Setting                       | Description                                                  | Default value                                                |
| ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `node.name`                   | 节点名称                                                     | 机器的hostname                                               |
| `path.data`                   | The directory that Logstash and its plugins use for any persistent needs. | `LOGSTASH_HOME/data`                                         |
| `pipeline.id`                 | The ID of the pipeline.                                      | `main`                                                       |
| `pipeline.workers`            | pipeline线程数，即filter_output的处理线程数，默认是cpu核数.  假如你发现事件在备份，或者CPU不饱和，可以考虑增加这个数量，用以更好的利用机器的多线程能力。 | Number of the host’s CPU cores                               |
| `pipeline.batch.size`         | 在尝试执行其筛选器和输出之前，单个工作线程将从输入收集的最大事件数。较大的批处理大小通常效率更高，但会增加内存开销。您可能需要增加'jvm.options'配置文件中的jvm堆空间。有关详细信息.   请参阅 [Logstash Configuration Files](https://www.elastic.co/guide/en/logstash/current/config-setting-files.html) | `125`                                                        |
| `pipeline.batch.delay`        | When creating pipeline event batches, how long in milliseconds to wait for   each event before dispatching an undersized batch to pipeline workers. | `50`                                                         |
| `pipeline.unsafe_shutdown`    | When set to `true`, forces Logstash to exit during shutdown even if there are still inflight events   in memory. By default, Logstash will refuse to quit until all received events   have been pushed to the outputs. Enabling this option can lead to data loss during shutdown. | `false`                                                      |
| `path.config`                 | The path to the Logstash config for the main pipeline. If you specify a directory or wildcard,   config files are read from the directory in alphabetical order. | Platform-specific. See [Logstash Directory Layout](https://www.elastic.co/guide/en/logstash/current/dir-layout.html). |
| `config.string`               | A string that contains the pipeline configuration to use for the main pipeline. Use the same syntax as   the config file. | None                                                         |
| `config.test_and_exit`        | When set to `true`, checks that the configuration is valid and then exits. Note that grok patterns are not checked for   correctness with this setting. Logstash can read multiple config files from a directory. If you combine this   setting with `log.level: debug`, Logstash will log the combined config file, annotating   each config block with the source file it came from. | `false`                                                      |
| `config.reload.automatic`     | When set to `true`, periodically checks if the configuration has changed and reloads the configuration whenever it is changed.   This can also be triggered manually through the SIGHUP signal. | `false`                                                      |
| `config.reload.interval`      | How often in seconds Logstash checks the config files for changes. | `3s`                                                         |
| `config.debug`                | When set to `true`, shows the fully compiled configuration as a debug log message. You must also set `log.level: debug`.   WARNING: The log message will include any *password* options passed to plugin configs as plaintext, and may result   in plaintext passwords appearing in your logs! | `false`                                                      |
| `config.support_escapes`      | When set to `true`, quoted strings will process the following escape sequences: `\n` becomes a literal newline (ASCII 10). `\r` becomes a literal carriage return (ASCII 13). `\t` becomes a literal tab (ASCII 9). `\\` becomes a literal backslash `\`. `\"` becomes a literal double quotation mark. `\'` becomes a literal quotation mark. | `false`                                                      |
| `modules`                     | When configured, `modules` must be in the nested YAML structure described above this table. | None                                                         |
| `queue.type`                  | The internal queuing model to use for event buffering. Specify `memory` for legacy in-memory based queuing, or `persisted` for disk-based ACKed queueing ([persistent queues](https://www.elastic.co/guide/en/logstash/current/persistent-queues.html)). | `memory`                                                     |
| `path.queue`                  | The directory path where the data files will be stored when persistent queues are enabled (`queue.type: persisted`). | `path.data/queue`                                            |
| `queue.page_capacity`         | The size of the page data files used when persistent queues are enabled (`queue.type: persisted`). The queue data consists of append-only data files separated into pages. | 64mb                                                         |
| `queue.max_events`            | The maximum number of unread events in the queue when persistent queues are enabled (`queue.type: persisted`). | 0 (unlimited)                                                |
| `queue.max_bytes`             | The  total capacity of the queue in number of bytes. Make sure the capacity  of your disk drive is greater than the value you specify here. If both `queue.max_events` and `queue.max_bytes` are specified, Logstash uses whichever criteria is reached first. | 1024mb (1g)                                                  |
| `queue.checkpoint.acks`       | The maximum number of ACKed events before forcing a checkpoint when persistent queues are enabled (`queue.type: persisted`). Specify `queue.checkpoint.acks: 0` to set this value to unlimited. | 1024                                                         |
| `queue.checkpoint.writes`     | The maximum number of written events before forcing a checkpoint when persistent queues are enabled (`queue.type: persisted`). Specify `queue.checkpoint.writes: 0` to set this value to unlimited. | 1024                                                         |
| `queue.checkpoint.retry`      | When  enabled, Logstash will retry once per attempted checkpoint write for  any checkpoint writes that fail. Any subsequent errors are not retried.  This is a workaround for failed checkpoint writes that have been seen  only on filesystems with non-standard behavior such as SANs and is not  recommended except in those specific circumstances. | `false`                                                      |
| `queue.drain`                 | When enabled, Logstash waits until the persistent queue is drained before shutting down. | `false`                                                      |
| `dead_letter_queue.enable`    | Flag to instruct Logstash to enable the DLQ feature supported by plugins. | `false`                                                      |
| `dead_letter_queue.max_bytes` | The maximum size of each dead letter queue. Entries will be dropped if they   would increase the size of the dead letter queue beyond this setting. | `1024mb`                                                     |
| `path.dead_letter_queue`      | The directory path where the data files will be stored for the dead-letter queue. | `path.data/dead_letter_queue`                                |
| `http.host`                   | The bind address for the metrics REST endpoint.              | `"127.0.0.1"`                                                |
| `http.port`                   | The bind port for the metrics REST endpoint.                 | `9600`                                                       |
| `log.level`                   | The log level. Valid options are:  `fatal`  `error`  `warn`  `info`  `debug`  `trace` | `info`                                                       |
| `log.format`                  | The log format. Set to `json` to log in JSON format, or `plain` to use `Object#.inspect`. | `plain`                                                      |
| `path.logs`                   | The directory where Logstash will write its log to.          | `LOGSTASH_HOME/logs`                                         |
| `path.plugins`                | Where to find custom plugins. You can specify this setting multiple times to include   multiple paths. Plugins are expected to be in a specific directory hierarchy:   `PATH/logstash/TYPE/NAME.rb` where `TYPE` is `inputs`, `filters`, `outputs`, or `codecs`,   and `NAME` is the name of the plugin. | Platform-specific. See [L](https://www.elastic.co/guide/en/logstash/current/dir-layout.html) |