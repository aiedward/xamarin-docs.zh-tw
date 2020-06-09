---
title: 活動開發週期
description: 活動是 Android 應用程式的基本組建區塊，而且可以存在於許多不同的狀態。 活動生命週期從具現化開始，並以銷毀結束，並在之間包含許多狀態。 當活動變更狀態時，會呼叫適當的生命週期事件方法，通知活動即將發生的狀態變更，並允許它執行程式碼以適應該變更。 本文將探討活動的生命週期，並說明活動在每個狀態變更期間的責任，使其成為運作正常且可靠的應用程式的一部分。
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: b9404e8a54899d3af6e6534fc9157e78139ca7d1
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568702"
---
# <a name="activity-lifecycle"></a>活動開發週期

_活動是 Android 應用程式的基本組建區塊，而且可以存在於許多不同的狀態。活動生命週期從具現化開始，並以銷毀結束，並在之間包含許多狀態。當活動變更狀態時，會呼叫適當的生命週期事件方法，通知活動即將發生的狀態變更，並允許它執行程式碼以適應該變更。本文將探討活動的生命週期，並說明活動在每個狀態變更期間的責任，使其成為運作正常且可靠的應用程式的一部分。_

## <a name="activity-lifecycle-overview"></a>活動生命週期總覽

活動是 Android 特有的異常程式設計概念。 在傳統應用程式開發中，通常會使用靜態 main 方法來啟動應用程式。 不過，使用 Android 時，會有不同的專案;Android 應用程式可以透過應用程式內任何已註冊的活動來啟動。 實際上，大部分的應用程式只會有指定為應用程式進入點的特定活動。 不過，如果應用程式當機，或作業系統終止，則 OS 可以嘗試在最後一個開啟的活動或上一個活動堆疊內的其他位置重新開機應用程式。
此外，作業系統可能會在未啟用時暫停活動，如果記憶體不足，則回收它們。 必須謹慎考慮，才能讓應用程式在活動重新開機時，正確還原其狀態，特別是當該活動相依于先前活動的資料時。

活動生命週期會實作為作業系統在整個活動生命週期中呼叫的方法集合。 這些方法可讓開發人員執行必要的功能，以滿足其應用程式的狀態和資源管理需求。

應用程式開發人員必須分析每個活動的需求，以判斷活動生命週期所公開的方法必須加以執行，這是非常重要的。 若未執行此動作，可能會導致應用程式不穩定、當機、資源膨脹，甚至可能的根本作業系統不穩定。

本章會詳細檢查活動生命週期，包括：

- 活動狀態
- 生命週期方法
- 保留應用程式的狀態

本節也包含[逐步](~/android/app-fundamentals/activity-lifecycle/saving-state.md)解說，提供如何在活動生命週期期間有效率地儲存狀態的實用範例。 在本章結尾，您應該瞭解活動生命週期，以及如何在 Android 應用程式中加以支援。

## <a name="activity-lifecycle"></a>活動開發週期

Android 活動生命週期包含在活動類別內公開的方法集合，可為開發人員提供資源管理架構。 此架構可讓開發人員符合應用程式內每個活動的獨特狀態管理需求，並適當地處理資源管理。

### <a name="activity-states"></a>活動狀態

Android OS 會根據其狀態仲裁活動。 這可協助 Android 識別不再使用的活動，讓 OS 能夠回收記憶體和資源。 下圖說明活動在其存留期內可以經歷的狀態：

[![活動狀態圖表](images/image1-sml.png)](images/image1.png#lightbox)

這些狀態可以分成4個主要群組，如下所示：

1. 使用中*或正在* &ndash; 執行如果活動在前景（也稱為活動堆疊的頂端），則會將它們視為作用中或正在執行。 這在 Android 中被視為最高優先順序的活動，因此只有在極端情況下，作業系統才會終止，例如活動嘗試使用超過裝置上可用的記憶體，因為這可能會導致 UI 變得沒有回應。

1. 已*暫停* &ndash;當裝置進入睡眠狀態，或某個活動仍然可見，但有一項新的非完整大小或透明活動部分隱藏時，活動會被視為已暫停。 已暫停的活動仍在運作中，亦即，它們會維護所有狀態和成員資訊，並繼續附加至視窗管理員。 這會被視為 Android 中第二個最高優先順序的活動，因此，只有在結束此活動時，作業系統才會終止這項活動，以滿足讓作用中/執行中的活動穩定且快速回應所需的資源需求。

1. *已停止/背景執行* &ndash;另一個活動所完全遮蔽的活動會被視為已停止或在背景中。
    已停止的活動仍會嘗試盡可能保留其狀態和成員資訊，但已停止的活動會被視為三種狀態的最低優先順序，因此，OS 會先終止此狀態中的活動，以滿足較高優先順序活動的資源需求。

1. *重新開機* &ndash;在生命週期中，從暫停到停止的活動可能會被 Android 從記憶體移除。 如果使用者流覽回到活動，它必須重新開機、還原到先前儲存的狀態，然後向使用者顯示。

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>重新建立活動以回應設定變更

為了讓事情更加複雜，Android 會在混合中擲回另一次的扳手，稱為設定變更。 設定變更是快速的活動損毀/重新建立週期，當活動的設定變更時，例如當裝置[旋轉](~/android/app-fundamentals/handling-rotation.md)（而活動需要以橫向或直向模式重新建立時）、顯示鍵盤（而且活動會有機會自行調整大小），或裝置放在停駐時，也會出現在其他情況下。

設定變更仍然會導致停止和重新開機活動時所發生的相同活動狀態變更。 不過，為了確保應用程式在進行設定變更時能夠順利進行回應，請務必儘快處理。 因此，Android 具有特定的 API，可在設定變更期間用來保存狀態。
我們稍後會在[整個生命週期的管理狀態](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle)一節中討論。

### <a name="activity-lifecycle-methods"></a>活動生命週期方法

Android SDK 和（依擴充功能），Xamarin. Android framework 提供強大的模型來管理應用程式內的活動狀態。 當活動的狀態為 [正在變更] 時，會由 OS 通知活動，以呼叫該活動的特定方法。 下圖說明與活動生命週期相關的這些方法：

[![活動生命週期流程圖](images/image2-sml.png)](images/image2.png#lightbox)

身為開發人員，您可以藉由覆寫活動中的這些方法來處理狀態變更。 不過，請務必注意，所有生命週期方法都是在 UI 執行緒上呼叫，並會封鎖 OS 執行下一段 UI 工作，例如隱藏目前的活動、顯示新的活動等等。因此，這些方法中的程式碼應該盡可能簡短，才能讓應用程式順利執行。 任何長時間執行的工作都應該在背景執行緒上執行。

讓我們來檢查每個生命週期方法及其使用方式：

#### <a name="oncreate"></a>OnCreate

[OnCreate](xref:Android.App.Activity.OnCreate*)是建立活動時要呼叫的第一個方法。
`OnCreate`一律會覆寫，以執行活動可能需要的任何啟動初始化，例如：

- 建立視圖
- 初始化變數
- 將靜態資料系結至清單

`OnCreate`採用配套[參數，](xref:Android.OS.Bundle)這是在組合不是 null 的情況下，用來儲存和傳遞狀態資訊和物件之間的物件的字典，這表示活動正在重新開機，而且應該從先前的實例還原其狀態。 下列程式碼說明如何從組合中取出值：

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

`OnCreate`完成後，Android 會呼叫 `OnStart` 。

#### <a name="onstart"></a>OnStart

在完成之後，系統一律會呼叫[OnStart](xref:Android.App.Activity.OnStart) `OnCreate` 。 活動可能會覆寫這個方法，如果它們需要在活動變成可見之前執行任何特定的工作，例如在活動內重新整理視圖的目前值。 Android 會 `OnResume` 在此方法之後立即呼叫。

#### <a name="onresume"></a>OnResume

當活動準備好開始與使用者互動時，系統就會呼叫[OnResume](xref:Android.App.Activity.OnResume) 。
活動應該覆寫此方法來執行下列工作：

- 向上加速畫面播放速率（遊戲開發的一般工作）
- 起始動畫
- 接聽 GPS 更新
- 顯示任何相關的警示或對話方塊
- 連接外部事件處理常式

例如，下列程式碼片段顯示如何初始化相機：

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume`很重要，因為在中完成的任何作業都 `OnPause` 應該在中取消執行 `OnResume` ，因為它是唯一的生命週期方法，保證會在 `OnPause` 將活動帶回生命後執行。

#### <a name="onpause"></a>OnPause

當系統即將將活動放入背景，或活動變成部分模糊時，會呼叫[OnPause](xref:Android.App.Activity.OnPause) 。 活動應該覆寫此方法，如果他們需要：

- 認可持續性資料的未儲存變更

- 損毀或清除其他耗用資源的物件

- 向下移動畫面播放速率和暫停動畫

- 取消註冊外部事件處理常式或通知處理常式（也就是系結至服務的相關聯）。 這必須完成，以避免活動記憶體流失。

- 同樣地，如果活動已顯示任何對話或警示，就必須使用方法來清除它們 `.Dismiss()` 。

例如，下列程式碼片段會放開相機，因為活動無法在暫停時使用它：

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

有兩種可能的生命週期方法會在之後呼叫 `OnPause` ：

1. `OnResume`如果活動要傳回前景，將會呼叫。
1. `OnStop`如果活動放在背景中，將會呼叫。

#### <a name="onstop"></a>OnStop

當使用者不再看到活動時，會呼叫[OnStop](xref:Android.App.Activity.OnStop) 。 當發生下列其中一種情況時，就會發生這種情況：

- 正在啟動新的活動，並已涵蓋此活動。
- 正在將現有的活動帶入前景。
- 活動已終結。

`OnStop`不一定會在記憶體不足的情況下呼叫，例如當 Android 耗盡資源時，無法正確背景活動。 基於這個理由，最好不要依賴在 `OnStop` 準備要終結的活動時呼叫。 下一次可能會呼叫的生命週期方法，會是 `OnDestroy` 活動是否已消失，或 `OnRestart` 活動是否回到與使用者互動。

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](xref:Android.App.Activity.OnDestroy)是在活動實例上呼叫的最後一個方法，它會在它被終結並完全從記憶體移除。 在極端情況下，Android 可能會終止裝載活動的應用程式進程，而不會叫用它 `OnDestroy` 。 大部分的活動都不會執行這個方法，因為大部分的清除和關閉都是在 `OnPause` 和 `OnStop` 方法中完成。 `OnDestroy`通常會覆寫方法，以清除可能會流失資源的長時間執行資源。 其中一個範例可能是在中啟動的背景執行緒 `OnCreate` 。

在終止活動之後，將不會呼叫任何生命週期方法。

#### <a name="onrestart"></a>OnRestart

[OnRestart](xref:Android.App.Activity.OnRestart)會在您的活動停止後，于其再次啟動之前呼叫。 當使用者在應用程式中的活動上按下 [首頁] 按鈕時，就是一個很好的例子。 發生這種情況時 `OnPause` `OnStop` ，會呼叫方法，並將活動移至背景，但不會終結。 如果使用者接著使用工作管理員或類似的應用程式來還原應用程式，則 Android 會呼叫活動的 `OnRestart` 方法。

在中，不應執行何種邏輯的一般指導方針 `OnRestart` 。 這是因為 `OnStart` 不論活動是建立或重新開機，一律會叫用，因此活動所需的任何資源都應該在中初始化 `OnStart` ，而不是 `OnRestart` 。

之後呼叫的下一個生命週期方法 `OnRestart` 會是 `OnStart` 。

### <a name="back-vs-home"></a>上一頁與 Home

許多 Android 裝置都有兩個不同的按鈕： [上一頁] 按鈕和 [Home （首頁）] 按鈕。 您可以在下列 Android 4.0.3 的螢幕擷取畫面中看到這種情況的範例：

[![[上一頁] 和 [首頁] 按鈕](images/image4-sml.png)](images/image4.png#lightbox)

這兩個按鈕之間有些許差異，即使它們的效果與將應用程式放在背景中一樣。 當使用者按一下 [上一步] 按鈕時，他們會告訴 Android 已完成活動。 Android 會摧毀活動。 相反地，當使用者按一下 [首頁] 按鈕時，只會將活動放入背景 Android 中， &ndash; 而不會終止活動。

<a name="Managing_State_Throughout_the_Lifecycle"></a>

## <a name="managing-state-throughout-the-lifecycle"></a>在整個生命週期中管理狀態

當活動停止或損毀時，系統會提供機會來儲存活動的狀態，以供日後解除凍結。
這個儲存的狀態稱為實例狀態。 Android 提供三個選項，可在活動生命週期期間儲存實例狀態：

1. 將基本型別儲存在稱為「配套」中 `Dictionary` ，以供 Android 用來儲存狀態。 [Bundle](xref:Android.OS.Bundle)

1. 建立會保存複雜值（例如點陣圖）的自訂類別。 Android 會使用此自訂類別來儲存狀態。

1. 規避設定變更生命週期，並假設完全負責維護活動中的狀態。

本指南涵蓋前兩個選項。

### <a name="bundle-state"></a>配套狀態

儲存實例狀態的主要選項是使用稱為「組合[」的索引](xref:Android.OS.Bundle)鍵/值字典物件。
回想一下，當建立了方法，並將組合當做 `OnCreate` 參數傳遞時，可以使用此配套來還原實例狀態。 不建議您針對較複雜的資料使用組合，而不會快速或輕鬆地序列化成索引鍵/值組（例如點陣圖）;而是應該用於簡單的值（例如字串）。

活動提供方法來協助儲存和抓取組合中的實例狀態：

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash;當活動被終結時，Android 會叫用此方法。 如果活動需要保存任何索引鍵/值的狀態專案，則可以執行此方法。

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) &ndash;這會在 `OnCreate` 方法完成後呼叫，並提供另一個機會讓活動在初始化完成之後還原其狀態。

下圖說明如何使用這些方法：

[![套件組合狀態流程圖](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

當活動停止時，將會呼叫[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) 。 它會收到一個配套參數，活動可以在其中儲存其狀態。 當裝置發生設定變更時，活動可以使用傳入的物件，藉 `Bundle` 由覆寫來保留活動狀態 `OnSaveInstanceState` 。 例如，請參考下列程式碼：

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

當按一下名為的按鈕時，上述程式碼會遞增名為的整數 `c` `incrementCounter` ，並將結果顯示在 `TextView` 名為的中 `output` 。 發生設定變更時-例如，當裝置旋轉時，上述程式碼將會遺失的值 `c` `bundle` ，因為會是 `null` ，如下圖所示：

[![顯示未顯示先前的值](images/07-sml.png)](images/07.png#lightbox)

為了保留 `c` 此範例中的值，活動可以覆寫 `OnSaveInstanceState` ，將值儲存在組合中，如下所示：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

現在當裝置旋轉為新的方向時，會將整數儲存在配套中，並以下列程式程式碼來取得：

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> 請務必一律呼叫的基底實作為， `OnSaveInstanceState` 讓視圖階層的狀態也可以儲存。

##### <a name="view-state"></a>檢視狀態

覆寫 `OnSaveInstanceState` 是一種適當的機制，可在不同方向變更（例如上述範例中的計數器）儲存活動中的暫時性資料。 不過，的預設執行 `OnSaveInstanceState` 會負責在每個視圖的 UI 中儲存暫時性資料，只要每個 view 都有指派的識別碼即可。 例如，假設應用程式的 `EditText` 元素定義在 XML 中，如下所示：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

由於 `EditText` 控制項具有指派的 `id` ，因此當使用者輸入某些資料並旋轉裝置時，仍會顯示資料，如下所示：

[![資料會以橫向模式保留](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*)將會在之後呼叫 `OnStart` 。 它提供了一個機會，讓您還原先前儲存到配套的任何狀態 `OnSaveInstanceState` 。 不過，這是提供給的相同組合 `OnCreate` 。

下列程式碼會示範如何在中還原狀態 `OnRestoreInstanceState` ：

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

此方法的存在是為了在應還原狀態時提供一些彈性。 有時候在還原實例狀態之前，最好先等候所有初始化完成。 此外，現有活動的子類別可能只會想要從實例狀態還原特定的值。 在許多情況下，不需要覆寫 `OnRestoreInstanceState` ，因為大部分的活動都可以使用提供給的組合來還原狀態 `OnCreate` 。

如需使用儲存狀態的範例 `Bundle` ，請參閱逐步解說[-儲存活動狀態](saving-state.md)。

#### <a name="bundle-limitations"></a>配套限制

雖然 `OnSaveInstanceState` 可讓您輕鬆地儲存暫時性資料，但還是有一些限制：

- 在所有情況下都不會呼叫它。 例如，按**Home**或**Back**鍵結束活動不會導致 `OnSaveInstanceState` 被呼叫。

- 傳入的組合 `OnSaveInstanceState` 不是針對大型物件（例如影像）所設計。 在大型物件的情況下，最好是從[OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance)儲存物件，如下所述。

- 使用配套儲存的資料會序列化，這可能會導致延遲。

套件組合狀態適用于不會使用太多記憶體的簡單資料，而*非設定實例資料*適用于較複雜的資料，或是抓取耗費資源的資料，例如來自 web 服務呼叫或複雜的資料庫查詢。 非設定實例資料會視需要儲存在物件中。 下一節將介紹如何 `OnRetainNonConfigurationInstance` 透過設定變更來保留更複雜的資料類型。

### <a name="persisting-complex-data"></a>保存複雜資料

除了保存配套中的資料之外，Android 也支援藉由覆寫[OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance)並傳回 `Java.Lang.Object` 包含要保存之資料的實例來儲存資料。 使用來儲存狀態有兩個主要優點 `OnRetainNonConfigurationInstance` ：

- 從傳回的物件會 `OnRetainNonConfigurationInstance` 對較大且更複雜的資料類型執行良好的處理，因為記憶體會保留此物件。

- `OnRetainNonConfigurationInstance`只有在需要時，才會呼叫方法。 這比使用手動快取更經濟實惠。

使用 `OnRetainNonConfigurationInstance` 適合用來多次抓取資料的情況，例如在 web 服務呼叫中。 例如，請考慮下列搜尋 Twitter 的程式碼：

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

此程式碼會從已格式化為 JSON 的 web 抓取結果、剖析它們，然後在清單中顯示結果，如下列螢幕擷取畫面所示：

[![畫面上顯示的結果](images/06-sml.png)](images/06.png#lightbox)

發生設定變更時（例如，當裝置旋轉時），程式碼會重複處理。 為了重複使用原先抓取的結果，而不會造成不必要的重複網路呼叫，我們可以使用 `OnRetainNonconfigurationInstance` 來儲存結果，如下所示：

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

現在當裝置旋轉時，原始的結果會從屬性中取出 `LastNonConfiguartionInstance` 。 在此範例中，結果是由包含推文的所組成 `string[]` 。 因為 `OnRetainNonConfigurationInstance` 需要 `Java.Lang.Object` 傳回，所以 `string[]` 會包裝在子類別的類別中，如下 `Java.Lang.Object` 所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，嘗試使用當做 `TextView` 從傳回的物件 `OnRetainNonConfigurationInstance` 將會洩漏活動，如下列程式碼所示：

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

在本節中，我們已瞭解如何使用來保留簡單的狀態資料 `Bundle` ，並使用保存較複雜的資料類型 `OnRetainNonConfigurationInstance` 。

## <a name="summary"></a>總結

Android 活動生命週期提供強大的架構來管理應用程式內的活動，但很難瞭解和執行。 這一章介紹了活動在其存留期內可能會經歷的不同狀態，以及與這些狀態相關聯的生命週期方法。 接下來，會提供指引，說明每個方法中應該執行哪種邏輯。

## <a name="related-links"></a>相關連結

- [處理旋轉](~/android/app-fundamentals/handling-rotation.md)
- [Android 活動](xref:Android.App.Activity)
