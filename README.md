# My Tutorials about devops

[My youtube channel](https://studio.youtube.com/channel/UCmJ3RnxnLnx-ZfnyE6A5jaA/videos)

## create docker network

```
docker network create somenetwork
```

## run elastic search

### create custom elasticsearch image

- change the current directory to the elasticsearch

```
docker build -t elastic .
```

### run docker container using customed elastic search

```
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elastic
```

## run fluentd

### build the custom fluentd image

- change the current directory to the fluentd folder

```
docker build -t fluentd .
```

### run the docker container on the customed image of fluentd

```
docker run --name fluentd -d --net somenetwork -p 8880:8880 fluentd
```

## run the kibana

### build the custom kibana image

- change the current diretctory to kibana folder

```
docker build -t kibana .
```

### run the docker container using the kibana customed image

```
docker run -d --name kibana --net somenetwork -p 5601:5601 kibana
```

## test the lab

```
curl -X POST -d 'json={"key1":"value1" , "key2":"value2"}' http://localhost:9880/access.log
```

# elasticsearch commands

- list all the indexes

```
curl -X GET 'http://localhost:9200/_cat/indices?v'
```

- list all the docs with the index

```

curl -X GET 'http://localhost:9200/fluentd.access.log/_search'

```

- put new doc

```
curl -XPUT --header 'Content-Type: application/json' http://localhost:9200/fluentd.access.log/_doc/200 -d '{
   "key1" : "value1",
   "key2" : "value2",
   "key3" : "value3"
}'
```
