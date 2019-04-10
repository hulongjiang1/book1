
https://github.com/openzipkin/zipkin/tree/master/zipkin-server#configuration-for-the-ui

* `ES_HOSTS`: A comma separated list of elasticsearch base urls to connect to ex. http://host:9200.
              Defaults to "http://localhost:9200".
* `ES_PIPELINE`: Only valid when the destination is Elasticsearch 5+. Indicates the ingest
                 pipeline used before spans are indexed. No default.
* `ES_TIMEOUT`: Controls the connect, read and write socket timeouts (in milliseconds) for
                Elasticsearch Api. Defaults to 10000 (10 seconds)
* `ES_MAX_REQUESTS`: Only valid when the transport is http. Sets maximum in-flight requests from
                     this process to any Elasticsearch host. Defaults to 64.
* `ES_INDEX`: The index prefix to use when generating daily index names. Defaults to zipkin.
* `ES_DATE_SEPARATOR`: The date separator to use when generating daily index names. Defaults to '-'.
* `ES_INDEX_SHARDS`: The number of shards to split the index into. Each shard and its replicas
                     are assigned to a machine in the cluster. Increasing the number of shards
                     and machines in the cluster will improve read and write performance. Number
                     of shards cannot be changed for existing indices, but new daily indices
                     will pick up changes to the setting. Defaults to 5.
* `ES_INDEX_REPLICAS`: The number of replica copies of each shard in the index. Each shard and
                       its replicas are assigned to a machine in the cluster. Increasing the
                       number of replicas and machines in the cluster will improve read
                       performance, but not write performance. Number of replicas can be changed
                       for existing indices. Defaults to 1. It is highly discouraged to set this
                       to 0 as it would mean a machine failure results in data loss.
* `ES_USERNAME` and `ES_PASSWORD`: Elasticsearch basic authentication, which defaults to empty string.
                                   Use when X-Pack security (formerly Shield) is in place.
* `ES_HTTP_LOGGING`: When set, controls the volume of HTTP logging of the Elasticsearch Api.
                     Options are BASIC, HEADERS, BODY

$ STORAGE_TYPE=elasticsearch ES_HOSTS=http://10.22.29.175:9200 ES_INDEX=zipkin_market ES_USERNAME=elastic ES_PASSWORD=1qazXSW@   java -jar zipkin.jar
