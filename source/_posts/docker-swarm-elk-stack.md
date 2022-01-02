---
title: Using elk stack for monitoring docker swarm service
date: 2021-12-28 16:12:09
tags:
    - docker
    - elkstack
---
初めてContainerで動くのシステムを構築しましたので、
経験がなかったので、調べながら開発進めました。
色々新技術を勉強できました。楽しかった！！！
担当したモジュールの`監視系`機能をメモに残したいと思います

### ELK Stackってなんのものだ？
[ELK Stack](https://www.elastic.co/what-is/elk-stack)はみつのOpenSourceを組み合わせて
ログ収集（アプリケーションログ, システムログなど）・分析・可視化のを提供されます。
- `E`lasticsearch
- `L`ogstash
- `K`ibana

![ELK](elk-stack-elkb-diagram.svg)

### 構成
ELKの構成は以下のように構成されています。
1. ログ集めてlogstashに転送する. ELK Stackなら Beatと呼ばれる
2. logstashで待ち受けて、データ加工・解析
3. elasticsearchデータベースに書き出す
4. Kibanaでelasticsearchのデータをベースに可視化できる

![ELK](elk-stack-elkb-architech.png)

## 事例
On-premisesにDocker swarm serviceで構築した案件を参加させいただきました。
Masterノード: 1台
Workノード: 3、4台
コンテナ数: 6(ELK除く)

#### 問題：
開始する時に以下のような要件があります。
- コンテナとして動いてるため、落ちるとログが残さない
- アプリケーションログを永久化にしたい
- 特殊エラーなどの時Alertを送りたい
- アクセス頻度、バッチ進捗などはvisualizeしたい
- ...

#### ソリューション：
調べながら開発進めました。
その時は ELK Stackがよく出てたので利用になります。
- Container log: [Filebeat's Container Input](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-container.html)

#### 注意：(TBD)
あった問題：
- Logstash file output: https://www.elastic.co/guide/en/logstash/current/tuning-logstash.html
- ...
