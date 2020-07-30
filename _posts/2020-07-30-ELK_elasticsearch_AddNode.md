---
title: "ELK Elasticsearch Add Node"
layout: post
lastmod : 2020-07-30 13:53:00 +0900 UTC
category: note
tags: [Elasticsearch]
excerpt: "Elasticsearch의 Node 추가에 대해 다룹니다."
---

## 개요
* 지난 ELK_install 포스트에 이어집니다.
* Elassticsearch master,data 2개의 노드에 구성 후 데이터 리플리카 확인.
* 동일한 7.5.0버전의 Elasticsearch 설치.(RPM설치)
* 참고 사이트
  * https://lhcsoft.blogspot.com/2019/03/elasticsearch-cluster.html
<br>
## 본문
1. /etc/elasticsearch/elasticsearch.yml 설정<br>
* 마스터 설정<br>
```
cluster.name: elastic-jun0 
node.name: es-node-1
node.master: true
node.data: true
network.host: 10.0.0.73
http.host: 0.0.0.0
cluster.initial_master_nodes: ["10.0.0.73"]
```

* 리플리카 설정<br>
```
cluster.name: elastic-jun0
node.name: es-node-2
node.master: false
node.data: true
network.host: 10.0.1.237
http.host: 0.0.0.0
discovery.seed_hosts: ["10.0.0.73:9300"]
```

* replica 서버가 off 되었을 때<br>
```
[root@ip-10-0-0-73 system]# curl -XGET 'localhost:9200/_cat/indices?v'
health status index                              uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   .kibana_task_manager_1             nbB6TrJeRZGI9mQ_zjNQfA   1   1          2            1     37.9kb         37.9kb
green  open   .apm-agent-configuration           GW4jgi71RHWaqzXdvpJ4Xw   1   1          0            0       566b           283b
yellow open   apm-7.5.0-metric-000001            X5IP6E_VT-atjVi7lwp2Iw   1   1        816            0    389.1kb        194.5kb
green  open   apm-7.5.0-transaction-000001       WSDY2YYqTCmhQ1c26vEgWQ   1   1         11            0    361.5kb        180.7kb
green  open   apm-7.5.0-span-000001              tKBmS5P0S1-X9EWTFIndLA   1   1          0            0       566b           283b
green  open   apm-7.5.0-error-000001             CHRZoZa5RZqxO7CcCl8ItQ   1   1          0            0       566b           283b
yellow open   .kibana_1                          c-snxIxlTV-5TLLTK2Lr0g   1   1        947           36      969kb          969kb
green  open   apm-7.5.0-onboarding-2020.01.02    VcWeYnn7RvmVunpoY9a0Ag   1   1          2            0     25.1kb         12.5kb
green  open   metricbeat-7.5.0-2019.12.17-000001 gYXMipKcSFiNwRCDUp-27A   1   1     194030            0     83.3mb         41.6mb
```

* replica 서버가 on 되었을 때<br>
```
[root@ip-10-0-0-73 system]# curl -XGET 'localhost:9200/_cat/indices?v'
health status index                              uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .kibana_task_manager_1             nbB6TrJeRZGI9mQ_zjNQfA   1   1          2            1     75.8kb         37.9kb
green  open   .apm-agent-configuration           GW4jgi71RHWaqzXdvpJ4Xw   1   1          0            0       566b           283b
green  open   apm-7.5.0-metric-000001            X5IP6E_VT-atjVi7lwp2Iw   1   1        816            0    389.1kb        194.5kb
green  open   apm-7.5.0-span-000001              tKBmS5P0S1-X9EWTFIndLA   1   1          0            0       566b           283b
green  open   apm-7.5.0-transaction-000001       WSDY2YYqTCmhQ1c26vEgWQ   1   1         11            0    361.5kb        180.7kb
```

## 트러블 슈팅
replication error
: master node에서 cluster.initial_master_nodes: ["10.0.0.73”] 와 같은 형태로 옵션을 넣어야만 아래 에러가 발생하지 않는다.<br>
```
[1]: the default discovery settings are unsuitable for production use;
 at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
```