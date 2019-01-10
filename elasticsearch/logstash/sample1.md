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

The `--config.test_and_exit` option parses your configuration file and reports any errors.

If the configuration file passes the configuration test, start Logstash with the following command:

```shell
bin/logstash -f first-pipeline.conf --config.reload.automatic
```

The `--config.reload.automatic` option enables automatic config reloading so that you don’t have to stop and restart Logstash every time you modify the configuration file.



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