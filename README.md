# UDPforATSEx
## 概要
AtsExプラグインからUDP通信をするための拡張機能プラグインです

尚一部未完成なコードも含まれております予めご了承ください
## 動作環境
同一マシン内での動作のみ試験しております

利用にはATSEXが必要です

ver.1.00RC-6での動作は確認済みです

本プラグインは同一バージョン同士の利用を想定し作成しております

バージョン間での互換性は一切保証しませんので予めご了承ください

PHONプラグインを利用して発生した一切の損害においては当方は責任を負いかねます
## 利用方法
ATSEx拡張プラグインフォルダ(C:\Users\Public\Documents\AtsEx\1.0\Extensions\等)に配置してください

Bve起動後最初のシナリオを開いた際に自動的に起動されます

ポート設定ウィンドウはBVEの右クリックメニューから開くことができます

送信ホストには送信相手のホスト名

送信ポートには送信相手が受け取るポート番号

受信ポートにはこちらが受信するポート番号を入力してください

開始ボタンで開始し終了ボタンで終了します
## 開発方法
尚最低限のC#の使い方とATSExプラグイン作成の方法を理解している前提とします
### 1. アセンブリを参照し名前空間の使用を宣言
通常のAtsExプラグイン開発環境にこのプラグインのアセンブリ参照を追加します
```
using AtsEx.Extensions.UDP;
```
このプラグインはAtsEx.Extensions.UDP内に定義してありますので名前空間の参照間違いにご注意ください
### 2. プラグインアクセス用のインタフェースを作成する
```
IUDP udpHandle;
public Sample(PluginBuilder builder) : base(builder)
{
  BveHacker.ScenarioCreated +=OnScenarioCreated;    
  udpHandle=Extensions.GetExtension<IUDP>();
}
```
```
AtsEx.PluginHost.Plugins.Extentions.GetExtention<IUDP>();
```
でプラグインのインタフェースを取得できます
### 3a. 送信する
```
List<byte> bytes = new List<byte>();
udpHandle.Send("Location", bytes);
```
```
IUDP.Send(string key,List<byte> data);
```
で送信バッファに追加できます
すでに登録されているkeyは上書きされます

### 3b. 受信する
```
List<byte> bytes;
bytes= udpHandle.Get("Location");
```
```
IUDP.Get(string key);
```
で受信バッファから取得します

送信時に設定されたkeyが必要です

受信バッファに指定されたkeyのデータが存在しないときnullを返します
### 送信サンプル
```
using AtsEx.PluginHost;
using AtsEx.PluginHost.Plugins;
using AtsEx.Extensions.UDP;
namespace Sample
{
  [PluginType(PluginType.VehiclePlugin)]
  class SendSample
  {
    IUDP udpHandle;
    public SendSample(PluginBuilder builder) : base(builder)
    {
    udpHandle=Extensions.GetExtension<IUDP>();
    }
    public override TickResult Tick(TimeSpan elapsed)
    {
      VehiclePluginTickResult tickResult =new VehiclePluginTickResult();
      List<byte> bytes1 =BitConverter.GetBytes(BveHacker.Scenario.LocationManager.Location);
      udpHandle.Send("Location", bytes1);
      return tickResult;   
    }
  }
}
```
### 受信サンプル
```
using AtsEx.PluginHost;
using AtsEx.PluginHost.Plugins;
using AtsEx.Extensions.UDP;
namespace Sample
{
  [PluginType(PluginType.VehiclePlugin)]
  class SendSample
  {
    IUDP udpHandle;
    public SendSample(PluginBuilder builder) : base(builder)
    {
    udpHandle=Extensions.GetExtension<IUDP>();
    }
    public override TickResult Tick(TimeSpan elapsed)
    {
      VehiclePluginTickResult tickResult =new VehiclePluginTickResult();
      List<byte> bytes1 = udpHandle.Get("Location");
      BveHacker.Scenario.LocationManager.Location=BitConverter.ToDouble(bytes1,0);
      return tickResult;   
    }
  }
}
```
## ライセンス
参照についてはどうぞご自由に

無許可での再配布は許可しません

連絡くれたら許可するかもなのでお気軽にご連絡ください
