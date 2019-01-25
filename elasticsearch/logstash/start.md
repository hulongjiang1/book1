When you’re done, the contents of first-pipeline.conf should look like this:

```json
input {
    beats {
        port => "5044"
    }
}
# The filter part of this file is commented out to indicate that it is
# optional.
# filter {
#
# }
output {
    stdout { codec => rubydebug }
}
```

```shell
bin/logstash -f first-pipeline.conf --config.test_and_exit
```

The `--config.test_and_exit` 这个选择项可以解析配置文件的错误并产出报告和错误。

使用配置文件启动logstash，可使用如下命令:

```shell
bin/logstash -f first-pipeline.conf --config.reload.automatic
```

The `--config.reload.automatic` 这个配置可以不需要停止或重启logstash的情况下，当配置文件修改时，自动装载配置文件。



```json
input {
    beats {
        port => "5044"
    }
}
 filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG}"}
    }
    geoip {
        source => "clientip"
    }
}
output {
    elasticsearch {
        hosts => [ "localhost:9200" ]
    }
}
```

 However, you do need to force Filebeat to read the log file from scratch. To do this, go to the terminal window where Filebeat is running and press Ctrl+C to shut down Filebeat. Then delete the Filebeat registry file. For example, run:

```shell
sudo rm data/registry
```

Since Filebeat stores the state of each file it harvests in the registry, deleting the registry file forces Filebeat to read all the files it’s harvesting from scratch.

Next, restart Filebeat with the following command:

```shell
sudo ./filebeat -e -c filebeat.yml -d "publish"
```

There might be a slight delay before Filebeat begins processing events if it needs to wait for Logstash to reload the config file.