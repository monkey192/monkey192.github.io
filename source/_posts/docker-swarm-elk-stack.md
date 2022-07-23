---
title: Using elk stack for monitoring docker swarm service
date: 2021-12-28 16:12:09
tags:
    - docker
    - elkstack
---
Containerで動くのシステムを構築する案件に参加しまして、
経験がなかったので、調査しながら開発を進めていきました。
色々新技術を勉強できまして楽しかった！！！

担当したモジュールは　コンテナ・システム全体を`監視 / Monitoring`機能を展開するのです。
忘れないうちに、自分用のメモを残したいと思います。

## ELK Stackってなんのものだ？
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

##　システム構成
On-premisesにDocker swarm の利用でシステムを構築する
Masterノード: 1台
Workノード: 3〜4台
コンテナ合計: 6(ELK除く)

#### あったIssueと解決
開始する時に以下のような要件が出ました
- コンテナとして動いてるため、コンテナ停止したらログが残さない
- アプリケーションログを永久化にしたい
- 特殊エラーなどの時に警告メールで通知
- アクセス頻度、バッチ進捗などはvisualizeしたい
- ...

#### ソリューション：
調べながら開発進めました。
その時は ELK Stackがよく出てたので採用になります。
- Container log: [Filebeat's Container Input](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-container.html)

#### 注意：(TBD)
あった問題：
- Logstash file output: https://www.elastic.co/guide/en/logstash/current/tuning-logstash.html
- ...

おさらい
。。。（時間があるとき、もっと書きますように）