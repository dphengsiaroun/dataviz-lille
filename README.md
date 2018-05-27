# Data Vizualisation with Kibana, ElasticSearch & Logstash

This is a project to make a data vizualisation on Open data of Lille.
All the data were found here (https://opendata.lillemetropole.fr/) and export to csv.
You can find my export in data directory.

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

# Step by Step
1. Start services (Kibana, Logstash, Elasticsearch)
2. Create a file logstash_filename.conf and run this file in your shell
3. Go to Kibana (http://localhost:5601) on your browser.
4. On Kibana navigation, go to `Dev Tools` and to add the code of the part `Getting started with Kibana` 
5. Run the method 
```
GET items/_count
```
and check to result of count.

6. On Kibana navigation, go to `Management` and `Create Index pattern`. The index name is equal to that defined in the logstash_filename.conf. You can see your data on Kibana navigation in `Discover`.

7. On Kibana navigation, go to `Vizualize` and click on button `+` to create a new visualization. Choose the coordinate map and select your `index_pattern`, choose `Geo Hash` and Geo point columns then click on run.


# Getting started with Kibana
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

Go to (http://localhost:5601) on your browser.

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
  "blocks": {
    "read_only_allow_delete": "false"
  }
}

GET items/_search
{
  "query": {
    "match_all": {}
  }
}

GET items/_count

DELETE items
```

# Getting started with Logstash
Run this command :
```
$ brew services start logstash
```

Create file `logstash_filename.conf` on your shell with this command :
```
sudo vim logstash_filename.conf
```

Add this code in your file config logstash (you can custom the filter object according to your needs):
```
input {
  stdin {
    type => "stdin-type"
  }
  file {
    path => [ "/PATH_DIR/data/bureaux-de-poste.csv"]
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
    index => "NAME_OF_YOUR_INDEX_PATTERN"
  }
  stdout { codec => rubydebug }
}
```

Run the file config logstash on your shell

```
$ logstash -f logstash/filename.conf
```

# Getting started with Elasticsearch
Run this command :
```
$ brew services start elasticsearch
```
Visit this url `http://localhost:9200` on your browser.


# Check if the services are started
Verify if all your services are started with this command :
```
$ brew services list
```

# Authors

- Dany PHENGSIAROUN