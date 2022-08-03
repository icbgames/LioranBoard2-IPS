# IPS - ICB Point System for LioranBoard2
## What is this?
LB2でTwitchの視聴者に対してポイントを付与するための仕組みです  
Mikuiaのようなものと思ってください

## Version
| branch | version | link | remark |
|--------|---------|------|--------|
| stable | 1.0.0 | https://github.com/icbgames/LioranBoard2-IPS/tree/v1.0.0 |    |
| test   | 1.0.3 | https://github.com/icbgames/LioranBoard2-IPS/tree/v1.0.3 |    |

## 機能紹介
以下のようなことができます
* チャット参加者に対してポイントを付与
  * 一定間隔ごとにポイントを付与(デフォ1分間隔、トリガー設定にて変更可)
  * 最終発言から一定期間ポイント付与(有効時間、付与ポイントともに設定ファイルにて変更可)
  * サブスクユーザーに対して追加のポイント付与(Tierごとに設定可)
* レイドをくれたユーザーに対してポイント付与
  * レイドの人数によって追加のポイント付与
* bitsを投げてくれたユーザーに対してポイント付与
* サブスクしてくれたユーザーに対してポイント付与
  * Tierごとに付与ポイントを設定可
* サブスクギフトを投げてくれたユーザーに対してポイント付与
  * Tierごとに付与ポイントを設定可
* チャット欄にコマンドを打つことで自分の保有ポイントを確認
  * デフォで `!ips` (トリガー設定にて変更可)
  * 順位、1つ上の順位のユーザーとのポイント差を表示可(設定にてON/OFF選択)
* 自分のチャンネルの視聴者のポイントランキングを生成
* ポイントの名称、および単位を任意に設定可

## 導入手順
### LB2の導入
詳細は割愛しますので、ポイントだけ記しておきます。
LB2をインストールしたうえで、Twitchとの連携、OBSとの連携を実施してください

#### LB2のインストール
インストール先は任意の場所で大丈夫ですが、管理者権限がないとファイルを書き込むことのできない場所には置かない方が良いと思います。

#### Twitchアカウントとの連携
配信で使うメインのアカウントだけでなく、チャット投稿用のサブアカウントも登録しておくことを推奨します。

そうしておかないと、自動投稿された発言を削除することができなくなります。(botへの対策)

#### OBSとの連携
まずOBSのwebsocketプラグインを導入する必要があります。現時点では下記プラグインのver 4.9.1が適切なのでこちらをご利用ください。
[obs-websocket 4.9.1](https://github.com/obsproject/obs-websocket/releases/tag/4.9.1)

OBS側の設定ポートとLB2側の設定ポートが揃っていればOKです


#### Transmitterの設定
LB2側の `Settings` で `Allow Stream Deck and Transmitter` にチェックを入れるのを忘れないように。

OBS側で `transmitter.html` をブラウザリソースでローカルファイルとして取り込んでください。


### 設定ファイルの配置
LB2のインストール先に `IPS` という名前のディレクトリを作成し、本レポジトリの `ips.ini` と `bots.csv` を配置してください。

LB2を `D:\LioranBoard\` にインストールしているとした場合、 `D:\LioranBoard\IPS\ips.ini` というパスになっていればOKです。

### Deckごと一括でインポートする場合
`deck/IPS_decks.json` の中身をクリップボードにコピーし、LB2のDeck一覧画面から `Paste Deck` を行ってください。

### ボタンを個別でインポートする場合
#### Deckの作成
別途Deckを作成する必要があるので、任意の名称でDeckを作成してください。

#### 各種ボタンのインポート
本レポジトリの `deck/button` ディレクトリ配下の各種jsonファイルの中身をクリップボードにコピーし、LB2にてインポートしてボタンを作成します

## 設定ファイルの設定値の解説
### basic
本システムの基本的な項目を設定します

| key | type | value |
|----|----|----|
| channel | string | 本システムを導入する先、つまりあなたのチャンネル名を指定します<br>私の場合は `icb_games` となります |
| point_name | string | 本システムを利用して付与するポイントの名称を指定することができます |
| point_unit | string | 本システムを利用して付与するポイントの単位を指定することができます |
| check_mode | int | 1: チェックコマンド実行時に順位を表示する<br>2: 順位だけでなく上位との点差も表示する<br>その他: ポイントのみ表示 |
| debug | int | 1: デバッグモード有効<br>その他: 無効 |

--------------------

### chat
チャットでコメントしてくれた人に対して付与するポイントに関する設定です。

| key | type | value |
|----|----|----|
| expire_min | int | 最終発言から何分間ポイントを付与し続けるかを設定します |
| points_per_min | int | 1分間に付与するポイントを設定します |
| subscriber_additional_tier1 | int | Tier1サブスクしてくれている視聴者に対して、先述の `points_per_min` に加えて追加で付与するポイントを設定します |
| subscriber_additional_tier2 | int | Tier2サブスクしてくれている視聴者に対して、先述の `points_per_min` に加えて追加で付与するポイントを設定します |
| subscriber_additional_tier3 | int | Tier3サブスクしてくれている視聴者に対して、先述の `points_per_min` に加えて追加で付与するポイントを設定します |

--------------------

### bits
ビッツを投げてくれた視聴者に対して付与するポイントに関する設定です。

| key | type | value |
|----|----|----|
| points_per_100bits | int | 投げてくれたビッツ100bitsあたりに付与するポイントを設定します<br>小数以下は切り捨てのため、例えば設定値として `100` を設定した場合に250ビッツを投げてくれた人に対しては、200ポイントが付与されます |

--------------------

### raid
レイドしてくれた視聴者に対して付与するポイントに関する設定です。

| key | type | value |
|----|----|----|
| points_for_raid | int | レイドしてくれたユーザーに付与するポイントを設定します |
| points_per_raiders | int | レイドの人数1人につき追加で付与するポイントを設定します |

--------------------

### subscribe
サブスクしてくれた視聴者に対して付与するポイントに関する設定です

| key | type | value |
|----|----|----|
| points_for_subscribe_tier1 | int | Tier1でサブスクしてくれたユーザーに付与するポイントを設定します |
| points_for_subscribe_tier2 | int | Tier2でサブスクしてくれたユーザーに付与するポイントを設定します |
| points_for_subscribe_tier3 | int | Tier3でサブスクしてくれたユーザーに付与するポイントを設定します |
| points_for_subscribe_prime | int | Primeでサブスクしてくれたユーザーに付与するポイントを設定します |

--------------------

### gift
サブスクギフトを贈ってくれた視聴者に対して付与するポイントに関する設定です

| key | type | value |
|----|----|----|
| points_for_subgift_tier1 | int | Tier1のサブスクギフトを贈ってくれたユーザーに付与するポイントを設定します |
| points_for_subgift_tier2 | int | Tier2のサブスクギフトを贈ってくれたユーザーに付与するポイントを設定します |
| points_for_subgift_tier3 | int | Tier3のサブスクギフトを贈ってくれたユーザーに付与するポイントを設定します |

## ポイントランキング生成
LB2を起動した状態で `Ctrl` + `F11` キーを押すことでランキング生成処理が開始します  
1ユーザーにつき2秒のウェイトを挟んでいるため、時間を要します

## お問い合わせ
[Discord](https://discord.gg/Y4XfnGpk)にてご相談やご意見等々受け付けております
