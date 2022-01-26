# ELK Stack

This is a simple implementation of ELK stack using docker-compose

## Run

```commandline
sudo docker-compose up --build
```

## Check elasticsearch
```commandline
curl localhost:9200
```

## Check kibana
```commandline
curl localhost:5601
```
## Run logstash for terminal
```
sudo docker run -d -h logstash --name logstash --link elasticsearch:elasticsearch --net elk_default -it --rm -v "$PWD"/logstash:/config-dir logstash:7.16.3 -f /config-dir/terminal-listener.conf
```

Then pass in some input at the same terminal
```commandline
a
test1
b
c
```

Then follow the steps below:
1. browse to `localhost:5601`
2. then go to `http://localhost:5601/app/management/kibana/indexPatterns`
3. create an index pattern for kibana
   1. Note: Your index should have common characters of the inputs unless kibana does not create an index without any inputs. 
4. Then browse to `http://localhost:5601/app/discover`
5. And you will see your first index by using the upper left dropdown you can change between your indices.

<img src="https://raw.githubusercontent.com/pooya-mohammadi/elk-projects/master/images/discover_01.png" alt="discover_01" >

# Docker Run for elastic and kibana:
```commandline
sudo docker run -d -p 9200:9200 -p 9300:9300 --rm -h elasticsearch --name elasticsearch -e xpack.security.enabled=false -e discovery.type=single-node  elasticsearch:7.16.3
sudo docker run -d -p 5601:5601  --rm -h kibana --name kibana --link elasticsearch:elasticsearch kibana:7.16.3
```

# logstash listening to a port:
```commandline
sudo docker-compose -f docker-compose.yml up --build
sudo docker run -d -h logstash --name logstash -p 9300:9300 --link elasticsearch:http://localhost:9200 -it --rm -v "$PWD"/logstash:/config-dir logstash:7.16.3 -f /config-dir/port-listener.conf
```

## Or packing all containers in a single docker-compose

```
sudo docker-compose -f docker-compose-port.yml up 
```

Send logs to defined port on logstash container using `telnet`:
```commandline
telnet localhost 9300
```
Append your messages and you will find your request appending on `http://localhost:5601/app/discover` as well

# How to log containers using fluentd
1. First off, create `fluentd/Dockerfile` & `fluentd/conf/fluent.conf`
2. Then, add the following section to all the container that needs to be logged.
```commandline
logging:
      driver: "fluentd"
      options:
        fluentd-address: localhost:24224
        tag: httpd.access
``` 
3. Finally run the following command

```
sudo docker-compose -f docker-compose-fluentd.yml up --build
```

# References:
1. https://www.youtube.com/watch?v=6bXSfjwQVIc
2. https://medium.com/analytics-vidhya/installing-elk-stack-in-docker-828df335e421
3. https://logz.io/blog/docker-logging/