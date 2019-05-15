# タッチアンドトライ ~ ReButton ~

---

目次

- [タッチアンドトライのゴール](#S-0)
- [1章 ボタンクリックをIoT Centralへ伝える](#1-0)
  - [IoTC :  アプリケーションを作成](#1-1)
  - [IoTC : デバイステンプレートを作成](#1-2)
  - [IoTC : 測定にメッセージを追加](#1-3)
  - [IoTC : デバイスを追加](#1-4)
  - [ReButton : 工場出荷時設定にリセット](#1-5)
  - [ReButton : 接続情報を設定](#1-6)
  - [ReButton+ : ボタンをクリックしてIoT Centralに通知](#1-7)
- [2章 測定項目を増やす](#2-0)
  - [IoTC : 測定項目にクリック種類を追加](#2-1)
  - [IoTC : 測定項目にバッテリー電圧を追加](#2-2)
  - [ReButton+ : ボタンをクリックしてIoT Centralに通知](#2-3)
- [3章 ボタンクリックをSMS通知する](#3-0)
  - [Azure : アクショングループを作成](#3-1)
  - [IoTC : ルールを追加](#3-2)
  - [ReButton+ : ボタンをクリックしてSMS通知](#3-3)
- [4章 ボタンクリックからコード呼び出し](#4-0)
  - [Azure : Function Appを作成](#4-1)
  - [VSCode : ローカルにコードを作成](#4-2)
  - [VSCode : ローカルのコードをFunction Appに配置](#4-3)
  - [IoTC : ルールを追加](#4-4)
  - [VSCode : コードのログをモニタリング](#4-5)
- [後始末](#F-0)
  - [ReButton : 工場出荷時設定にリセット](#F-1)
  - [IoTC : アプリケーションを削除](#F-2)
  - [Azure : アクショングループを削除](#F-3)
  - [Azure : リソースグループを削除](#F-4)

---

## <a name="S-0">タッチアンドトライのゴール</a>

本テキストは、Azureへ簡単にメッセージ送信できるデバイス**Seeed ReButton**と、デバイスを手軽に管理できるクラウドサービス**Azure IoT Central**を使って、IoTシステム構築するタッチアンドトライです。  
デバイス設定、クラウド設定、クラウドサービス連携の手軽さと、プログラミング可能な柔軟性を体感しましょう。

![61](img/61.png)

使用するデバイスとサービスは、次の通りです。

|品名|ドキュメント|
|:--|:--|
|[Seeed ReButton](https://www.seeedstudio.com/ReButton-p-2930.html)|[公式(英語)](https://aka.ms/rebutton), [非公式(日本語)](http://matsujirushi.hatenablog.jp/entry/2019/01/23/171257)|
|Azure IoT Central|[公式](https://azure.microsoft.com/ja-jp/services/iot-central/)|
|Azure Monitor|[公式](https://azure.microsoft.com/ja-jp/services/monitor/)|
|Azure Functions|[公式](https://azure.microsoft.com/ja-jp/services/functions/)|

**1章**は、ReButtonのボタンクリック操作をIoT Centralに通知して、Web画面でボタンクリックを知ることができるようにします。  
具体的には、IoT Centralにアプリケーションを作成してデバイステンプレートを作成、デバイスを追加した後、ReButtonにWi-Fi接続情報とIoT Centralへのデバイス接続情報を設定します。

**2章**は、IoT CentralのWeb画面で表示する項目を増やします。  
IoT Centralのデバイステンプレートに、測定項目を追加する簡単な操作だけで、Web画面の表示項目が増えるのを体験します。

**3章**は、ReButtonのボタンクリック操作をIoT Centralを経由してAzure Monitorに通知し、スマートフォンへSMS通知します。  
IoT Centralのルールという機能を使って、Azure Monitorに通知します。Azure Monitorにはさまざまなオペレーターへの通知方法がありますが、SMS通知を使います。

**4章**は、ReButtonのボタンクリック操作をIoT Centralを経由してAzure Functionsに通知します。  
Azure Functionsへ通知することで、その後の動作をプログラミングによって変更できることを学びます。

---

## <a name="1-0">1章 ボタンクリックをIoT Centralへ伝える</a>

### <a name="1-1">IoTC :  アプリケーションを作成</a>

> Azure IoT Centralのアプリケーションを作成します。

Webブラウザで`https://apps.azureiotcentral.com`を開いてください。URLを開く際に、Microsoftアカウントでのログイン認証が求められます。保有しているMicrosoftアカウントを使って、ログインしてください。  
開くと、Azure IoT Centralのアプリケーションが一覧表示されます。  
左上にある、①`新しいアプリケーション`をクリックして、アプリケーションを作成してください。

![1](img/1.png)

次に、アプリケーションの作成に必要な情報を設定します。  
下表のとおり設定して、最後に⑧`作成`をクリックしてください。

|項目名|値|
|:--|:--|
|①支払いプラン|従量課金制|
|②アプリケーションテンプレート|カスタムアプリケーション|
|③アプリケーション名|==任意==|
|④URL|==ユニークURL==（同じURLが既に存在するとエラーになります）|
|⑤ディレクトリ|==任意==|
|⑥Azureサブスクリプション|==任意==|
|⑦リージョン|West US|

![2](img/2.png)  
![3](img/3.png)

しばらくすると、アプリケーションが作成されて、ダッシュボードが表示されます。  
黄色いポップアップが表示されている場合は、①`了解しました`をクリックして、表示を消してください。

![4](img/4.png)

次の画像のとおり表示されていれば成功です。

![5](img/5.png)

---

### <a name="1-2">IoTC : デバイステンプレートを作成</a>

> 作成したアプリケーションに、ReButtonのデバイステンプレートを追加します。

左の①`デバイステンプレート`をクリックしてください。デバイステンプレートの一覧が表示されます。  
ここで、右上の②[`+`]マークをクリックして、デバイステンプレートを作成してください。

![6](img/6.png)

デバイステンプレートの種類は①`カスタム`を選択してください。

![7](img/7.png)

デバイステンプレートの名前に①`ReButton`と入力して、②`作成`をクリックしてください。

![8](img/8.png)

左の①`デバイスエクスプローラー`をクリックしてください。  
②`ReButton (1.0.0)`という名前のデバイステンプレートが表示されて、`ReButton-1(シミュレート済み)`という名前のデバイスが1つ含まれていれば成功です。

![10](img/10.png)

---

### <a name="1-3">IoTC : 測定にメッセージを追加</a>

> ReButtonのデバイステンプレートに、ReButtonから送られてくる情報を定義します。

左の①`デバイステンプレート`をクリックして、②`ReButton`デバイステンプレートをクリックしてください。

![11](img/11.png)

デバイステンプレートの①`測定`タブ画面が表示されます。  
②`新しい測定`をクリックしてください。測定の追加画面が表示されます。

![12](img/12.png)

測定の種類が3つ表示されているので、その中から①`イベント`をクリックしてください。

![13](img/13.png)

次に、測定の追加に必要な情報を設定します。  
下表のとおり設定して、最後に④`保存`をクリックしてください。

|項目名|値|備考|
|:--|:--|:--|
|①Display Name|メッセージ||
|②フィールド名|message|必ず、この値にしてください|
|③既定の重要度|情報||

![14](img/14.png)

次の画像のとおり、イベントに`メッセージ`が表示されていれば成功です。

![15](img/15.png)

---

### <a name="1-4">IoTC : デバイスを追加</a>

> ReButtonのデバイステンプレートに、ReButtonデバイスを追加します。

左の①`デバイスエクスプローラー`をクリックしてください。②`ReButton (1.0.0)`デバイステンプレートに含まれているデバイスが一覧表示されます。  
ここで、上にある③[`+`]マークをクリックして、④`実際`を選択してください。

![16](img/16.png)

①`デバイス名`に==任意==の名前を入力して、②`作成`をクリックしてください。

![17](img/17.png)

すると、作成したReButtonデバイスが表示されます。  
右上の①`接続`をクリックして、このデバイスの接続情報を表示してください。

![18](img/18.png)

ここで表示されている`スコープID`,`デバイスID`,`SAS主キー`は、後でReButtonに設定します。  
**メモ帳などにコピー**しておいてください。

![19](img/19.png)

---

### <a name="1-5">ReButton : 工場出荷時設定にリセット</a>

> ReButtonの設定を工場出荷時の設定にリセットします。

`ReButton`の裏ぶたを取り外してください。  
裏ぶたはシリコン製なのでグンニャリします。隙間にツメを突っ込んで引っ張り上げれば簡単に外れると思います。  
外れたら、電池の横にあるジャンパーピンを写真のように、両方のピンに刺さるようにはめて5秒程度待ちます。

![21](img/21.png)

その後、またジャンパーピンを写真のように、片方だけ刺さるようにはめ直します。  
これで、`ReButton`の工場出荷時設定へのリセットは完了です。

![20](img/20.png)

---

### <a name="1-6">ReButton : 接続情報を設定</a>

> ReButtonを`Access Pointモード`で起動して、パソコンからReButtonに接続、WebページでWi-FiアクセスポイントとAzure IoT Centralの接続情報を設定します。  
> **ここでのReButtonへの接続情報の設定中は、一旦パソコンのインターネット接続が途切れます。**

まず、ReButtonをAccess Pointモードで起動します。
このために、10秒以上ボタンを押し続けてください。  
ボタンを数秒間押し続けてでも、LED(青色)が光らない場合は、一度ボタンを押すのをやめて、ひと呼吸、間を開けてから、もう一度ボタンを押し続けてみてください。(ボタンを強く押す必要はありません。少しだけ押し方や押す場所を変えてみると、うまくいくかもしれません。)  

うまくいった場合は、ボタンを押している間、LEDが青色→黄色→水色→白色と変化します。白色になったら、ボタンを離してください。

すると、ボタンが`Access Pointモード`で起動して、LEDが白色の点滅になります。

![22](img/22.png)

ReButtonが`Access Pointモード`で起動したことが確認できたら、次にパソコンからReButtonにWi-Fi接続をします。  
パソコンのWi-Fi接続設定(SSID一覧表示)の中から、「AZB-xxxxxxxx」という名前のアクセスポイントを探してください。  
(「AZB-xxxxxxxx」は、自分が利用するReButtonの側面に貼ってあるSSIDを選択してください。)  
見つけたら、①`接続`をクリックして、Wi-Fi接続します。

![23](img/23.png)

接続操作すると、接続試行中のまま時間がかかりますが、接続試行中表示のまま、次の作業に進みます。  
ここでは、ReButtonにWi-FiとAzure IoT Centralの接続設定をするので、任意のブラウザで、①`http://192.168.0.1/` にアクセスしてください。  
ブラウザ画面上に、ReButtonの設定項目一覧`ReButton - Home`画面が表示されますので、②`Wi-Fi`のリンクをクリックしてください。

![24](img/24.png)

ReButtonのWi-Fi接続設定画面が表示されます。  
下表のとおり設定して、最後に③`Save`をクリックしてください。

|項目名|値|
|:--|:--|
|①Wi-Fi SSID|`XXXXXXX`をプルダウン候補から選択(当日別途展開します)|
|②Wi-Fi Passphrase|`zzzzzz`(当日別途展開します)|

![25](img/25.png)

「Wi-Fi saved.」の表示が出たら、①`Home`をクリックしてください。  

![26](img/26.png)

次に、Azure IoT Centralの接続設定を行っていきます。
ブラウザ画面上に、ReButtonの設定項目一覧`ReButton - Home`画面から、①`Azure IoT Central`のリンクをクリックしてください。

![27](img/27.png)

ReButtonのAzure IoT Central接続設定画面が表示されます。  
事前に**メモ帳などにコピー**しておいた各種情報を下表のとおり設定して、最後に④`Save`をクリックしてください。

|項目名|値|
|:--|:--|
|①Scope ID|== 事前にメモしておいた`スコープID` ==|
|②Devise ID|== 事前にメモしておいた`デバイスID` ==|
|③SAS Key|== 事前にメモしておいた`SAS主キー` ==|

![28](img/28.png)

最後に、①`Shutdown`をクリックしてください。

![29](img/29.png)

`Shutdown`の表示がされたら、設定完了です。確認ができたら、ブラウザを閉じてください。  
この後は、`Azure IoT Central`側での操作、動作確認になります。  
パソコンのWi-Fi設定をインターネット接続できるアクセスポイントに切り替えてください。

![30](img/30.png)

---

### <a name="1-7">ReButton+ : ボタンをクリックしてIoT Centralに通知</a>

> 設定済みのReButtonデバイステンプレート上で、ボタンのクリック通知(`メッセージ`)が届くことを確認します。

`Azure IoT Central`上で、①`デバイスエクスプローラー`をクリックします。  
その後、テンプレートの②`ReButton (1.0.0)`(自分がつけたデバイステンプレート名)と、続いて③`ReButton #1`(自分がつけたデバイス名)を順にクリックします。

![31](img/31.png)

自動的に`ReButton`の測定画面が表示されます。  
もし、異なる画面が表示された場合、①`測定`をクリックします。

![32](img/32.png)

**さあ、ボタンを押してみましょう！**

`ReButton`は、最初のクリックの反応が遅いです。  
これは、ボタンを押したときに回路の電源スイッチがONして動き出そうとしている時間です。  
そのため、最初のプッシュはちょっと長めに押し続ける必要があります。  
LEDが青色に点灯するまで押して、青色になったら離す。  
「強く押す」ではありません！ **「長く押す」** です。

ボタンの内部プログラムでは、

1. 押す→LEDが青色に  
1. 離す→LEDは青色のまま  
1. 1秒間放置→シングルクリックと判断  
1. クラウドへ通知しようとし、LEDが点滅します。  
(クラウドへの通知に失敗した場合は、最後に赤色に点滅します。**成功するまで、ボタンを押し直してください。**)

また、3.の1秒間放置のときに押すと、ダブルクリック、トリプルクリックと変化して、LED表示が変わります。

|クリック種類|LED色|
|:--|:--|
|シングルクリック|青|
|ダブルクリック|緑|
|トリプルクリック|紫|

もしくは、ボタンをずっと押し続けていると、下記のように変化します。

|クリック種類|LED色|備考|
|:--|:--|:--|
|ロングプレス|黄||
|スーパーロングプレス|水色||
|ウルトラロングプレス|白|`Access Pointモード`|

**【注意！】**  
LED(白)になるまで、ボタンを押し続けた場合は、最初の設定で行った`Access Pointモード`になってしまいます。  
この場合は、一度裏ぶたを開けて、電池を抜き差しして、`Access Pointモード`を解除し、元に戻してください。  
(設定済みのWi-Fi情報やAzure IoT Central接続情報も消えてしまいますので、再度設定をし直す必要があります。)

![22](img/22.png)

ブラウザでAzure IoT Central上のデバイス`ReButton #1`にイベント通知が届いたか、確認するために、ブラウザの表示を最新に更新します。  
クラウドへのメッセージが届いて、`デバイスエクスプローラー`の測定画面で確認できるまでには、少し時間がかかります。  
しばらく、根気よく表示の更新を続けてみてください。  
正常にメッセージが届いていると、`♦`マークの表示が出て、確認できます。  
さらに、この`♦`マークにマウスカーソルを合わせ、クリックするとメッセージの詳細が確認できます。

![33](img/33.png)

---

## <a name="2-0">2章 測定項目を増やす</a>

### <a name="2-1">IoTC : 測定項目にクリック種類を追加</a>

> 最後のクリック種類ごとの状態値を計測できるようにします。

①`デバイステンプレート`をクリックし、続いて②`ReButton`のデバイステンプレート名をクリックします。

![34](img/34.png)

最初に測定項目を作った時と同じように、デバイステンプレートの①`測定`タブ画面が表示されます。  
②`新しい測定`をクリックしてください。測定の追加画面が表示されます。

![35](img/35.png)

測定の種類が3つ表示されているので、その中から①`状態`をクリックしてください。

![36](img/36.png)

次に、測定の追加に必要な情報を設定します。  
まず、下表に示す①，②を設定します。

|項目名|値|備考|
|:--|:--|:--|
|①Display Name|クリック種類||
|②フィールド名|actionNum|必ず、この値にしてください|

続いて、③[`+`]をクリックして、状態(クリック種類)ごとの④`値`、⑤`表示名`、⑥`カラー`を下表のように設定しながら、繰り返し値を追加していきます。  
**④`値`は、必ず下表の値を設定してください。**

|状態(クリック種類)|④値|⑤表示名|⑥カラー|参考設定色|RGB|
|:--|:--|:--|:--|:--|:--|
|シングルクリック|**1**|Single Click|== 任意の色 ==|薄水色|rgb(138, 212, 235)|
|ダブルクリック|**2**|Double Click|== 任意の色 ==|薄緑|rgb(1, 184, 170)|
|トリプルクリック|**3**|Triple Click|== 任意の色 ==|マゼンタ|rgb(211, 19, 211)|
|ロングプレス|**10**|Long Press|== 任意の色 ==|ダークイエロー|rgb(242, 200, 15)|
|スーパーロングプレス|**11**|Super Long Press|== 任意の色 ==|水色|rgb(115, 248, 255)|

![37](img/37.png)

全ての状態(クリック種類)の設定が完了したら、①`保存`をクリックしてください。

![38](img/38.png)

---

### <a name="2-2">IoTC : 測定項目にバッテリー電圧を追加</a>

> ReButtonのバッテリー電圧を計測できるようにします。

前と同じように、①`新しい測定`をクリックしてください。測定の追加画面が表示されます。

![39](img/39.png)

今度は、①`テレメトリ`をクリックしてください。

![40](img/40.png)

`テレメトリ`の設定に必要な情報は、下表のようになります。

各項目を設定して、最後に⑦`保存`をクリックしてください。

|項目名|値|備考|
|:--|:--|:--|
|①Display Name|バッテリー電圧||
|②フィールド名|batteryVoltage|必ず、この値にしてください|
|③単位|V||
|④最小値|0||
|⑤最大値|4||
|⑥小数点以下桁数|1||
|カラー|== 任意の色 ==|初期値のままでOK|

![41](img/41.png)

デバイステンプレートの`測定`画面でテレメトリと状態のそれぞれに`バッテリー電圧`と`クリック種類`の測定項目が追加されていることを確認してください。

![42](img/42.png)

---

### <a name="2-3">ReButton+ : ボタンをクリックしてIoT Centralに通知</a>

> 設定済みのReButtonデバイステンプレート上で、ボタンのクリック通知(`テレメトリ`、`状態`、`メッセージ`)が届くことを確認します。

`Azure IoT Central`上で、①`デバイスエクスプローラー`をクリックします。  
その後、テンプレートの②`ReButton (1.0.0)`(自分がつけたデバイステンプレート名)と、続いて③`ReButton #1`(自分がつけたデバイス名)を順にクリックします。

![43](img/43.png)

自動的に`ReButton`の測定画面が表示されます。  
もし、異なる画面が表示された場合、①`測定`をクリックします。

![44](img/44.png)

**さあ、ボタンを押してみましょう！**

イベント通知の確認のときと同じように、ボタンを押します。

|クリック種類|LED色|
|:--|:--|
|シングルクリック|青|
|ダブルクリック|緑|
|トリプルクリック|紫|

もしくは、ボタンをずっと押し続けていると、下記のように変化します。

|クリック種類|LED色|備考|
|:--|:--|:--|
|ロングプレス|黄||
|スーパーロングプレス|水色||
|ウルトラロングプレス|白|`Access Pointモード`|

いろいろな押し方で何度か押してみましょう。

**【注意！】**  
LED(白)になるまで、ボタンを押し続けた場合は、最初の設定で行った`Access Pointモード`になってしまいます。  
この場合は、一度裏ぶたを開けて、電池を抜き差しして、`Access Pointモード`を解除し、元に戻してください。  
(設定済みのWi-Fi情報やAzure IoT Central接続情報も消えてしまいますので、再度設定をし直す必要があります。)

![45](img/45.png)

ブラウザでAzure IoT Central上のデバイス`ReButton #1`にイベント通知が届いたか、確認するために、ブラウザの表示を最新に更新します。  
クラウドへのメッセージが届いて、`デバイスエクスプローラー`の測定画面で確認できるまでには、少し時間がかかります。  
しばらく、根気よく表示の更新を続けてみてください。  
正常にメッセージが届いていると、`バッテリー電圧`のグラフ(ポイント)、`状態`の帯表示、`♦`マークの表示が出て、確認できます。  

![46](img/46.png)

---

## <a name="3-0">3章 ボタンクリックをSMS通知する</a>

### <a name="3-1">Azure : アクショングループを作成</a>

![47](img/47.png)

![48](img/48.png)

![49](img/49.png)

![50](img/50.png)

![51](img/51.png)

![52](img/52.png)

---

### <a name="3-2">IoTC : ルールを追加</a>

![53](img/53.png)

![54](img/54.png)

![55](img/55.png)

![56](img/56.png)

![57](img/57.png)

![58](img/58.png)

---

### <a name="3-3">ReButton+ : ボタンをクリックしてSMS通知</a>

![59](img/59.png)

![60](img/60.png)

---

## <a name="4-0">4章 ボタンクリックからコード呼び出し</a>

### <a name="4-1">Azure : Function Appを作成</a>

![62](img/62.png)

![63](img/63.png)

![64](img/64.png)

---

### <a name="4-2">VSCode : ローカルにコードを作成</a>

![65](img/65.png)

![66](img/66.png)

![67](img/67.png)

![68](img/68.png)

![69](img/69.png)

![70](img/70.png)

![71](img/71.png)

![72](img/72.png)

![73](img/73.png)

![74](img/74.png)

---

### <a name="4-3">VSCode : ローカルのコードをFunction Appに配置</a>

![75](img/75.png)

![76](img/76.png)

![77](img/77.png)

![78](img/78.png)

![79](img/79.png)

![80](img/80.png)

![81](img/81.png)

---

### <a name="4-4">IoTC : ルールを追加</a>

![82](img/82.png)

![83](img/83.png)

![84](img/84.png)

![85](img/85.png)

![86](img/86.png)

![87](img/87.png)

---

### <a name="4-5">VSCode : コードのログをモニタリング</a>

![88](img/88.png)

![89](img/89.png)

---

## <a name="F-0">後始末</a>

### <a name="F-1">ReButton : 工場出荷時設定にリセット</a>

![90](img/90.png)

![91](img/91.png)

---

### <a name="F-2">IoTC : アプリケーションを削除</a>

![92](img/92.png)

---

### <a name="F-3">Azure : アクショングループを削除</a>

![93](img/93.png)

![94](img/94.png)

---

### <a name="F-4">Azure : リソースグループを削除</a>

![95](img/95.png)

![96](img/96.png)

![97](img/97.png)

---
