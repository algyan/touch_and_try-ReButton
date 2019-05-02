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
  - Azure : アクショングループを作成
  - IoTC : ルールを追加
  - ReButton+ : ボタンをクリックしてSMS通知
- [後始末](#F-0)
  - ReButton : 工場出荷時設定にリセット
  - IoTC : アプリケーションを削除
  - Azure : アクショングループを削除
---

## <a name="S-0">タッチアンドトライのゴール

WIP

## <a name="1-0">1章 ボタンクリックをIoT Centralへ伝える

WIP

### <a name="1-1">IoTC :  アプリケーションを作成

> Azure IoT Centralのアプリケーションを作成します。

Webブラウザで`https://apps.azureiotcentral.com`を開いてください。Azure IoT Centralのアプリケーションが一覧表示されます。  
左上にある、`新しいアプリケーション`をクリックして、アプリケーションを作成してください。

![1](img/1.png)

次に、アプリケーションの作成に必要な情報を設定します。  
下表のとおり設定して、`作成`をクリックしてください。

|項目名|値|
|:--|:--|
|支払いプラン|従量課金制|
|アプリケーションテンプレート|カスタムアプリケーション|
|アプリケーション名|==任意==|
|URL|==任意==|
|ディレクトリ|==任意==|
|Azureサブスクリプション|==任意==|
|リージョン|West US|

![2](img/2.png)  
![3](img/3.png)

しばらくすると、アプリケーションが作成されて、ダッシュボードが表示されます。  
黄色いポップアップが表示されている場合は、`了解しました`をクリックして、表示を消してください。

![4](img/4.png)

次の画像のとおり表示されていれば成功です。

![5](img/5.png)

### <a name="1-2">IoTC : デバイステンプレートを作成

> 作成したアプリケーションに、ReButtonのデバイステンプレートを追加します。

左の`デバイステンプレート`をクリックしてください。デバイステンプレートの一覧が表示されます。  
ここで、右上の`+`マークをクリックして、デバイステンプレートを作成してください。

![6](img/6.png)

デバイステンプレートの種類は`カスタム`を選択してください。

![7](img/7.png)

デバイステンプレートの名前に`ReButton`と入力して、`作成`をクリックしてください。

![8](img/8.png)

左の`デバイスエクスプローラー`をクリックしてください。  
`ReButton`という名前のデバイステンプレートが表示されて、シミュレート済みデバイスが1つ含まれていれば成功です。

![10](img/10.png)

### <a name="1-3">IoTC : 測定にメッセージを追加

> ReButtonのデバイステンプレートに、ReButtonから送られてくる情報を定義します。

左の`デバイステンプレート`をクリックして、`ReButton`デバイステンプレートをクリックしてください。

![11](img/11.png)

`新しい測定`をクリックしてください。測定の追加画面が表示されます。

![12](img/12.png)

測定の種類が3つ表示されているので、その中から`イベント`をクリックしてください。

![13](img/13.png)

次に、測定の追加に必要な情報を設定します。  
下表のとおり設定して、`保存`をクリックしてください。

|項目名|値|
|:--|:--|
|Display Name|メッセージ|
|フィールド名|message|
|既定の重要度|情報|

![14](img/14.png)

次の画像のとおり、イベントに`メッセージ`が表示されていれば成功です。

![15](img/15.png)

### <a name="1-4">IoTC : デバイスを追加

> ReButtonのデバイステンプレートに、ReButtonデバイスを追加します。

左の`デバイスエクスプローラー`をクリックしてください。`ReButton`デバイステンプレートに含まれているデバイスが一覧表示されます。  
ここで、上にある`+`マークをクリックして、`実際`を選択してください。

![16](img/16.png)

`デバイス名'に==任意==の名前を入力して、`作成`をクリックしてください。

![17](img/17.png)

すると、作成したReButtonデバイスが表示されます。  
右上の`接続`をクリックして、このデバイスの接続情報を表示してください。

![18](img/18.png)

ここで表示されている`スコープID`,`デバイスID`,`SAS主キー`は、後でReButtonに設定します。  
メモ帳などにコピーしておいてください。

![19](img/19.png)

### <a name="1-5">ReButton : 工場出荷時設定にリセット

![21](img/21.png)

![20](img/20.png)

### <a name="1-6">ReButton : 接続情報を設定

![22](img/22.png)

![23](img/23.png)

![24](img/24.png)

![25](img/25.png)

![26](img/26.png)

![27](img/27.png)

![28](img/28.png)

![29](img/29.png)

![30](img/30.png)

### <a name="1-7">ReButton+ : ボタンをクリックしてIoT Centralに通知

![31](img/31.png)

![32](img/32.png)

![22](img/22.png)

![33](img/33.png)

## <a name="2-0">2章 測定項目を増やす

### <a name="2-1">IoTC : 測定項目にクリック種類を追加

![34](img/34.png)

![35](img/35.png)

![36](img/36.png)

![37](img/37.png)

![38](img/38.png)

### <a name="2-2">IoTC : 測定項目にバッテリー電圧を追加

![39](img/39.png)

![40](img/40.png)

![41](img/41.png)

![42](img/42.png)

### <a name="2-3">ReButton+ : ボタンをクリックしてIoT Centralに通知

![43](img/43.png)

![44](img/44.png)

![45](img/45.png)

![46](img/46.png)

## <a name="3-0">3章 ボタンクリックをSMS通知する

## <a name="F-0">後始末

