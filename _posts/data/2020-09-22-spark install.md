---
layout: post
title: "[스파크] 설치"
categories: data
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

우분투 20.04 기준

## Java 8 설치

```
sudo apt install -y openjdk-8-jdk
```

zshrc 에서 JAVA_HOME 세팅

## Spark 설치

```
wget https://mirror.navercorp.com/apache/spark/spark-3.1.2/spark-3.1.2-bin-hadoop3.2.tgz
```

압축 해제

```
tar -zxvf spark-3.1.2-bin-hadoop3.2.tgz
sudo mv spark-3.2.1-bin-hadoop3.2 /opt/spark
```

## 환경 변수 설정

~~~bash
# file: ".zshrc"
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
export SPARK_HOME=/opt/spark
export PATH=${JAVA_HOME}/bin:${SPARK_HOME}/bin:$PATH
~~~


