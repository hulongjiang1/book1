​             *摘要：*              Logstash的output模块,相比于input模块来说是一个输出模块,output模块集成了大量的输出插件,可以输出到指定文件,也可输出到指定的网络端口,当然也可以输出数据到ES.在这里我只介绍如何输出到ES,至于如何输出到端口和指定文件,有很多的文档资料可查找.         

Logstash的output模块,相比于input模块来说是一个输出模块,output模块集成了大量的输出插件,可以输出到指定文件,也可输出到指定的网络端口,当然也可以输出数据到ES.
在这里我只介绍如何输出到ES,至于如何输出到端口和指定文件,有很多的文档资料可查找.

```
  elasticsearch{  
    hosts=>["172.132.12.3:9200"]  
    action=>"index"  
    index=>"indextemplate-logstash"  
    #document_type=>"%{@type}"  
    document_id=>"ignore"  
      
    template=>"/opt/logstash-conf/es-template.json"  
    template_name=>"es-template.json"  
    template_overwrite=>true       
    }
```

action=>”index”  #es要执行的动作   index, delete, create, update
l index:将logstash.时间索引到一个文档
l delete:根据id删除一个document(这个动作需要一个id)
l create:建立一个索引document，如果id存在 动作失败.
l update:根据id更新一个document，有一种特殊情况可以upsert--如果document不是已经存在的情况更新document 。参见upsert选项。
l  A sprintf style string to change the action based on the content of the  event. The value %{[foo]} would use the foo field for the action
document_id=>” ”  为索引提供document id ，对重写elasticsearch中相同id词目很有用
document_type=>” ”事件要被写入的document type，一般要将相似事件写入同一type，可用%{}引用事件type，默认type=log
index=>”logstash-%{+YYYY,MM.dd}”  事件要被写进的索引，可是动态的用%{foo}语句
hosts=>[“127.0.0.0”]["127.0.0.1:9200","127.0.0.2:9200"] "https://127.0.0.1:9200" 
manage_template=>true  一个默认的es mapping 模板将启用（除非设置为false 用自己的template）
template=>””  有效的filepath   设置自己的template文件路径，不设置就用已有的
template_name=>”logstash” 在es内部模板的名字
这里需要十分注意的一个问题是,document_id尽量保证值得唯一,这样会解决你面即将面临的ES数据重复问题,切记切记!