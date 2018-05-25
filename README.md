# Data Vizualisation with Kibana, ElasticSearch & Logstash

## Prerequisites
Install :
1. Java 8 (http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
2. Kibana (https://www.elastic.co/downloads/kibana)
or on MacOS run this command :
```
brew install kibana
```
3. ElasticSearch (https://www.elastic.co/downloads/elasticsearch)
or on MacOS run this command :
```
brew install elasticsearch && brew info elasticsearch
```
4. Logstash (https://www.elastic.co/downloads/logstash)
or on MacOS run this command :
```
brew install logstash
```

# Start Kibana
Open the Kibana configuration file:  
- kibana.yml

Uncomment the directives for defining the Kibana port and Elasticsearch instance:
```
server.port: 5601
elasticsearch.url: "http://localhost:9200”
```


```
$ brew services start kibana
```

Visit this url `http://localhost:5601` on your browser.

In Dashboard click in `Dev tools` view and add this code :
```
PUT items
{
  "settings": {
    "number_of_shards": 1
  }, 
  "mappings": {
    "doc": {
      "properties": {
        "geo_point_2d" : { "type": "geo_point" }
      }
    }
  }
}

GET items

DELETE items
```

# Start Logstash
Run this command :
```
$ brew services start logstash
```

Create file `logstash.conf` on your shell with this command :
```
sudo vim /etc/logstash/conf.d/logstash.conf
```

Add this code in your file config logstash (you can custom the filter object according to your needs):
```
input {
  stdin {
    type => "stdin-type"
  }
  file {
    path => [ "/${path}/data/bureaux-de-poste.csv"]
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  csv {
    separator => ";"
    columns => [
      "geometry",
      "commune",
      "libelle",
      "code-postal",
      "objectid",
      "geo_point_2d"
    ]
    # mutate {
    #   convert => {"geo_point_2d" => "float"}
    # }
  }
}

output {
  elasticsearch {
    hosts => ["127.0.0.1:9200"] 
    index => "dataviz-lille"
  }
  stdout { codec => rubydebug }
}
```

Run the file config logstash on your shell

```
$ logstash -f logstash/filename.conf
```

# Start Elasticsearch
Run this command :
```
$ brew services start elasticsearch
```
Visit this url `http://localhost:9200` on your browser.


# Check if the services are started
Check confirm services started with this command :
```
$ brew services list
```

# Authors

- Dany PHENGSIAROUN