# Data Vizualisation with Kibana, ElasticSearch & Logstash

## Prerequisites
Install :
- Java 8 (http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- Kibana
(`brew install kibana`)

- ElasticSearch
(`brew install elasticsearch && brew info elasticsearch`)

- Logstash
(`brew install logstash`)

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

On `Dev tools` view add this code :
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
```
$ brew services start logstash
```

Create file `logstash.conf`
```
sudo vim /etc/logstash/conf.d/logstash.conf
```

File config logstash :
```
input {
  stdin {
    type => "stdin-type"
  }
  file {
    path => [ "/Users/Phengsiaroun/Desktop/dataviz/bureaux-de-poste.csv"]
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

Run file config logstash on your shell

```
$ logstash -f logstash.conf
```

# Start Elasticsearch
```
$ brew services start elasticsearch
```
Visit this url `http://localhost:9200` on your browser.


# Check Services started
Check confirm services started with this command :
```
$ brew services list
```

# Authors

- Dany PHENGSIAROUN