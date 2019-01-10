# [使用Logstash来实时同步MySQL数据到ES]
新增mysqltoes.conf文件，配置Input和output参数如下，连接jdbc按照规则同步指定的数据到es
```
input {
 stdin { }
    jdbc {
        jdbc_connection_string => "jdbc:mysql://10.39.48.80:3306/cloud_marketplace"
        jdbc_user => "root"
        jdbc_password => "1QAZxsw@"
        jdbc_driver_library => "/etc/logstash/mysql-connector-java-5.1.45.jar"
        jdbc_driver_class => "com.mysql.jdbc.Driver" 
        jdbc_paging_enabled => "true"
        jdbc_page_size => "50000"
        statement => "SELECT COMMODITY_ID ID,RESOURCE_NAME,COMMODITY_NAME,COMMODITY_DESCRIPTION,COMMODITY_FUNCTION,COMMODITY_ADVANTAGE FROM STORE_COMMODITY_T market"
        schedule => "* * * * *"
    }
}
  
output {
     stdout {
        codec => json_lines
    }
    elasticsearch {
        hosts => "10.39.48.80:9200"
        index => "commodityidx"
        document_type => "commodity"
        document_id => "%{id}"
    }
}
```
使用logstash按照conf文件执行 bin\logstash.bat -f mysqltoes.conf