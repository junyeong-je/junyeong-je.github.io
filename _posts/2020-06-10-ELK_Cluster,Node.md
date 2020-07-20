---
title: "Elasticsearch Cluster,Node 정의 및 Replica 시스템 설정 "
layout: post
lastmod : 2020-06-10 12:45:00 +0900
category: note
tags: [Elasticsearch]
excerpt: "Cluster와 Node 개념에 대한 정의와 2개의 노드를 구성해 데이터 리플리카를 확인합니다."
---

## 개요

* Elasticsearch Cluster와 Node 정의
* Elassticsearch master,data 2개의 노드에 구성 후 데이터 리플리카 확인
* 동일한 7.5.0버전의 Elasticsearch 설치.(RPM설치)

참조

* [https://lhcsoft.blogspot.com/2019/03/elasticsearch-cluster.html](https://lhcsoft.blogspot.com/2019/03/elasticsearch-cluster.html)

<br>
## Cluster, Node란

Elasticsearch를 사용 전 Cluster와 Node에 개념에 대해 이해가 필요합니다.
![](https://mblogthumb-phinf.pstatic.net/MjAxNzAyMjJfMjM5/MDAxNDg3NzUxODcxNjQ3.k2ClCsMZP8YamvxDKzJHJazYaqBQxf5WJ-WSZngzZDQg._LVBOtJKtc63AXXB2w7-1XEdZeo_ue5d-tpMPIsJ5pAg.PNG.indy9052/image.png?type=w800)<br>
위의 이미지를 살펴보시면 <span>**클러스터**는 여러 노드를 포함하고 있는 Elasticsearch system에 가장 큰 단위 입니다.</span><br>
<span>하나의 클러스터는 다수의 노드로 구성되어 있습니다.</span><br>
<span>하나의 클러스터는 다수의 서버로 바인딩하여 운영하거나 하나의 서버에서 다수의 클러스터를 운영 할 수 있습니다.</span><br>

하나의 클러스터와 두개의 데이터 노드를 구성해 데이터 shard를 확인해 보겠습니다.<br>
노드와 샤드에 대해 아래 블로그를 참조하시면 좋습니다.<br>
[https://brownbears.tistory.com/4](https://brownbears.tistory.com/4)

## Elasticsearch 설정

### 마스터 겸 node-1 설정

* /etc/elasticsearch/elasticsearch.yml 설정을 아래와 같이 변경합니다.

```
cluster.name: elastic-jun0
node.name: es-node-1
node.master: true
node.data: true
network.host: 10.0.0.73
http.host: 0.0.0.0
cluster.initial_master_nodes: ["10.0.0.73"]
```

- cluster.name: node-2와 동일한 클러스터 명을 사용해야합니다.<br>
- node.name: 사용할 노드명을 입력합니다.<br>
\- node\.master : 마스터노드로 사용하기 위해 true로 설정합니다\.<br>
\- node\.data : 마스터노드 겸 데이터노드로 사용하기 위해 true로 설정합니다\.<br>
\- network\.host : 서버의 사설 IP로 등록합니다\.<br>
\- http\.host : 웹에서 elasticsearch를 사용할 수 있게 접근을 허용합니다\.<br>
\(반드시필요한 옵션은 아닙니다\)<br>
\- cluster\.initial\_master\_nodes : 마스터 옵션으로 반드시 입력해야합니다\.

### node-2 설정

* 동일하게 노드 설정을 위해 elasticsearch.yml 을 수정합니다.

```
cluster.name: elastic-jun0
node.name: es-node-2
node.master: false
node.data: true
network.host: 10.0.1.237
http.host: 0.0.0.0
discovery.seed_hosts: ["10.0.0.73:9300"]
```

\- 마스터노드 설정과 크게 다르지 않으며\, master노드로 사용하지 않기때문에 false로 설정합니다\.
<br>
### index status 확인

\- elasticsearch의 index에는 3가지의 상태가 있습니다\.
![](Img/2020-06-10-ELK_Cluster,Node/1.png)<br>
Yellow 상태는 Primary shard는 준비가 되었으나, Replica shard가 준비되어 있지 않다! 라는 뜻 입니다.<br>
Replica shard가 없으면 Yellow라는 것은, 필요한 이유가 있기 때문인데 그 것이 무엇일까?<br>

1\. 장애 복구 \(fail\-over\) : 동일한 데이터를 나눠서 가지고 있기 때문에 하나의 노드에 문제가 생겨도\, replica shard가 primary shard로 교체가 됩니다\.<br>
2\. 검색 성능 : replica shard도 읽기 요청을 처리할 수 있기 때문에 replica가 많아질 경우 검색 성능이 증가하지만\, 하드웨어를 더 추가해야만 증가한다고 합니다\.<br>

<span style="color:#555555">근데 node가 하나라면 복제를 할 수 없다는 점입니다. 기본적으로 elasticsearch는 같은 노드에 동일한 데이터를 가지는 shard를 저장할 수 없습니다.</span><br>
![](Img/2020-06-10-ELK_Cluster,Node/2.png)

* replica 서버가 off 되었을 때.

```
[root@ip-10-0-0-73 system]# curl -XGET 'localhost:9200/_cat/indices?v'
health status index                              uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   .kibana_task_manager_1             nbB6TrJeRZGI9mQ_zjNQfA   1   1          2            1     37.9kb         37.9kb
green  open   .apm-agent-configuration           GW4jgi71RHWaqzXdvpJ4Xw   1   1          0            0       566b           283b
yellow open   apm-7.5.0-metric-000001            X5IP6E_VT-atjVi7lwp2Iw   1   1        816            0    389.1kb        194.5kb
green  open   apm-7.5.0-transaction-000001       WSDY2YYqTCmhQ1c26vEgWQ   1   1         11            0    361.5kb        180.7kb
green  open   apm-7.5.0-span-000001              tKBmS5P0S1-X9EWTFIndLA   1   1          0            0       566b           283b
green  open   apm-7.5.0-error-000001             CHRZoZa5RZqxO7CcCl8ItQ   1   1          0            0       566b           283b
yellow open   .kibana_1                          c-snxIxlTV-5TLLTK2Lr0g   1   1        947           36      969kb          969kb
green  open   apm-7.5.0-onboarding-2020.01.02    VcWeYnn7RvmVunpoY9a0Ag   1   1          2            0     25.1kb         12.5kb
green  open   metricbeat-7.5.0-2019.12.17-000001 gYXMipKcSFiNwRCDUp-27A   1   1     194030            0     83.3mb         41.6mb
```

* replica 서버가 on 되었을 때.

```
[root@ip-10-0-0-73 system]# curl -XGET 'localhost:9200/_cat/indices?v'
health status index                              uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .kibana_task_manager_1             nbB6TrJeRZGI9mQ_zjNQfA   1   1          2            1     75.8kb         37.9kb
green  open   .apm-agent-configuration           GW4jgi71RHWaqzXdvpJ4Xw   1   1          0            0       566b           283b
green  open   apm-7.5.0-metric-000001            X5IP6E_VT-atjVi7lwp2Iw   1   1        816            0    389.1kb        194.5kb
green  open   apm-7.5.0-span-000001              tKBmS5P0S1-X9EWTFIndLA   1   1          0            0       566b           283b
green  open   apm-7.5.0-transaction-000001       WSDY2YYqTCmhQ1c26vEgWQ   1   1         11            0    361.5kb        180.7kb
```

\- 여러 노드로 replica되면 index health상태가 green으로 변경되는 것을 확인할 수 있습니다\.

- - -

## 트러블슈팅

replication error 에러

```
[1]: the default discovery settings are unsuitable for production use;
 at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
```
- master node에서 cluster.initial_master_nodes: ["10.0.0.73”] 와 같은 형태로 옵션을 넣어야만 아래 에러가 발생하지 않는다.