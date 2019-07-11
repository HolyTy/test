CLUSTERPRO のインストールと設定
---
1. ミラーディスク用のパーティションを作成
 - /dev/oracleoci/oraclevdb1：RAW
 - /dev/oracleoci/oraclevdb2：ext4
 
1. CLUSTERPRO をインストールし、ライセンスを登録する

1. Cluster WebUI を起動する

1. サーバを追加し、インタコネクトを設定する
 - Node1(10.0.0.8), Node2(10.0.0.9), MDC：mdc1
 
1. NP解決(10.0.0.1)を設定する

1. フェイルオーバグループ(failover1)の追加
 - ミラーディスクリソースを作成する
  - ミラーパティションデバイス名：/dev/NMP1
  - マウントポイント：/mnt/md1
  - データパーティションデバイス名：/dev/oracleoci/oraclevdb2
  - クラスタパーティションデバイス名：/dev/oracleoci/oraclevdb1
  - ファイルシステム：ext4
 - Azure プローブポートリソースを作成する
  - プローブポート：26001
  
1. モニタリソースの追加
 - Azure プローブポートモニタリソース
  - Azure プローブポートリソース追加時に自動作成される
 - Azure ロードバランスモニタリソース
  - Azure プローブポートリソース追加時に自動作成される
  
1. Cluster WebUI から設定の反映 を行う
