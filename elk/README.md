# ELK config

![](https://cdn-images-1.medium.com/max/1600/1*xpO072p2xxwL9H7LCadj_w.jpeg)

## Components
1. Elasticsearch - It is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time.

Reference: https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html

2. Logstash - Is the central dataflow engine in the Elastic Stack for gathering, enriching, and unifying all of your data regardless of format or schema.

Reference: https://www.elastic.co/guide/en/logstash/current/introduction.html

3. Kibana - Is an open source analytics and visualization platform designed to work with Elasticsearch.
Reference: https://www.elastic.co/guide/en/kibana/master/introduction.html

4. Filebeat - Is a lightweight shipper for forwarding and centralizing log data. Installed as an agent on your servers, Filebeat monitors the log files or locations that you specify, collects log events, and forwards them to either to Elasticsearch or Logstash for indexing.
Reference: https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-getting-started.html


## Configuration
1. For nginx logs, filebeat has been configured to send the logs to the logstash and later the logstash sends the data to elasticsearh for indexing, which in turn is sent to kibana for displaying the data visually

* Filebeat YAML(/etc/filebeat/filebeat.yml) settings:

 Logstash listens on port 5044 for receiving the inputs from the filebeats and can be configured as below

```
#----------------------------- Logstash output --------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["localhost:5044"]
```
 Output can also be directly sent to the Elasticsearch if no processing is required on the received logs and below is how we can configure it. Elasticsearch listens on port 9200:

 ```
#-------------------------- Elasticsearch output ------------------------------
#output.elasticsearch:
  # Array of hosts to connect to.
  # hosts: ["[hosts]"]
 ```

Kibana listens on port 5601 and it can be configured via filebeat as below:

```
#============================== Kibana =====================================

# Starting with Beats version 6.0.0, the dashboards are loaded via the Kibana API.
# This requires a Kibana endpoint configuration.
setup.kibana:

  # Kibana Host
  # Scheme and port can be left out and will be set to the default (http and 5601)
  # In case you specify and additional path, the scheme is required: http://localhost:5601/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  host: "54.210.1.68:5601"
```

* nginx YAML(/etc/filebeat/modules.d/nginx.yml) settings:
Filebeats by default can identify many log formats. Below is how file beat is configured for nginx logs.

```
- module: nginx
  access:
    enabled: true
    var.paths: ["/home/ubuntu/logs/nginx-access.log"]
  error:
    enabled: true
    var.paths: ["/home/ubuntu/logs/nginx-error.log"]
```

* Logstash input configuration from filebeat:
This setting can be found in the `/etc/logstash/conf.d/dev-nginx.conf` file
```
input {
  beats {
    port => 5044
    host => "localhost"
  }
}
```

2. For webserver and worker logs we are pointing the input file directly to the logstash as below:
```
input{
 file {
        type => "inmail-web-logs"
        path => "/home/ubuntu/logs/web.log"
        start_position => "beginning"
   }
}
```
