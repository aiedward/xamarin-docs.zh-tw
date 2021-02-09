---
title: 活動開發週期
description: 活動是 Android 應用程式的基本組建區塊，可以存在於許多不同的狀態。 活動生命週期會以具現化的開頭，並以終結的結尾，並在其間包含許多狀態。 當活動變更狀態時，會呼叫適當的生命週期事件方法，通知活動即將發生狀態變更，並允許它執行程式碼以調整該變更。 本文將探討活動的生命週期，並說明活動在這些狀態變更期間的責任，使其成為運作正常、可靠應用程式的一部分。
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: d01e32fcd710174aa458a749a9ede67e00301087
ms.sourcegitcommit: 877965466ae58a6821d9d16871de1013895fd083
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987527"
---
# <a name="activity-lifecycle"></a>活動開發週期

_活動是 Android 應用程式的基本組建區塊，可以存在於許多不同的狀態。活動生命週期會以具現化的開頭，並以終結的結尾，並在其間包含許多狀態。當活動變更狀態時，會呼叫適當的生命週期事件方法，通知活動即將發生狀態變更，並允許它執行程式碼以調整該變更。本文將探討活動的生命週期，並說明活動在這些狀態變更期間的責任，使其成為運作正常、可靠應用程式的一部分。_

## <a name="activity-lifecycle-overview"></a>活動生命週期總覽

活動是 Android 專用的異常程式設計概念。 在傳統的應用程式開發中，通常會有一個靜態的 main 方法，可執行此方法來啟動應用程式。 不過，在 Android 中，專案會不同;Android 應用程式可以透過應用程式內任何已註冊的活動啟動。 在實務上，大部分的應用程式只會有指定為應用程式進入點的特定活動。 但是，如果應用程式損毀，或由作業系統終止，則 OS 可以嘗試在最後一個開啟的活動或上一個活動堆疊內的其他位置重新開機應用程式。
此外，OS 可能會在活動未使用時暫停活動，如果記憶體不足，則回收它們。 必須謹慎考慮，才能讓應用程式在活動重新開機時正確地還原其狀態，特別是當該活動相依于先前活動的資料時。

活動生命週期會實作為作業系統在活動生命週期中所呼叫的方法集合。 這些方法可讓開發人員執行必要的功能，以滿足其應用程式的狀態和資源管理需求。

應用程式開發人員必須分析每個活動的需求，以判斷由活動生命週期所公開的方法需要執行，是非常重要的。 若未這麼做，可能會導致應用程式不穩定、損毀、資源膨脹，甚至根本不穩定的作業系統。

本章會詳細探討活動生命週期，包括：

- 活動狀態
- 生命週期方法
- 保留應用程式的狀態

本節也包含 [逐步](~/android/app-fundamentals/activity-lifecycle/saving-state.md) 解說，提供如何在活動生命週期中有效率地儲存狀態的實用範例。 在本章結束時，您應該瞭解活動生命週期，以及如何在 Android 應用程式中支援它。

## <a name="activity-lifecycle"></a>活動開發週期

Android 活動生命週期包含在活動類別中公開的方法集合，可為開發人員提供資源管理架構。 此架構可讓開發人員滿足應用程式內每個活動的獨特狀態管理需求，並適當地處理資源管理。

### <a name="activity-states"></a>活動狀態

Android OS 會根據其狀態仲裁活動。 這可協助 Android 找出不再使用的活動，讓作業系統回收記憶體和資源。 下圖說明活動在其存留期內可以經歷的狀態：

[![活動狀態圖表](images/image1-sml.png)](images/image1.png#lightbox)

這些狀態可以分成4個主要群組，如下所示：

1. 作用中 *或正在* &ndash; 執行活動會被視為作用中或正在執行（如果活動在前景中，也稱為活動堆疊的頂端）。 這會被視為 Android 中最高優先順序的活動，因此只會在極端情況下由作業系統終止，例如，如果活動嘗試使用的記憶體超過裝置上可用的記憶體，這可能會導致 UI 變成沒有回應。

1. 已 *暫停* &ndash;當裝置進入睡眠狀態，或活動仍可見但部分隱藏了新的非完整大小或透明的活動時，就會將活動視為已暫停。 已暫停的活動仍在運作中，也就是它們會維護所有狀態和成員資訊，並且仍會附加至視窗管理員。 這會被視為 Android 中第二個最高優先順序的活動，因此，只有在結束此活動時，才會終止此活動，以滿足保持作用中/執行中的活動穩定和回應所需的資源需求。

1. *已停止/背景執行* &ndash; 由其他活動完全遮蔽的活動會視為已停止或在背景中。
    已停止的活動仍會盡可能地保留其狀態和成員資訊，但已停止的活動會被視為三個狀態的最低優先權，因此，作業系統會先終止處於此狀態的活動，以滿足更高優先順序活動的資源需求。

1. *重新開機* &ndash; 在生命週期中，從暫停到停止的活動可能會被 Android 從記憶體中移除。 如果使用者流覽回到活動，則必須重新開機、還原至先前儲存的狀態，然後向使用者顯示。

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>用於回應設定變更的活動 Re-Creation

為了使其變得更複雜，Android 在混合中會擲回另一個扳手，稱為設定變更。 設定變更是快速的活動銷毀/重新建立週期，會在活動的設定變更時發生，例如，當裝置 [旋轉](~/android/app-fundamentals/handling-rotation.md) (，且活動需要在橫向或直向模式中重新建立時) 、顯示鍵盤時 (，以及讓活動有機會自行調整大小) ，或是將裝置放在 dock 中。

設定變更仍會導致在停止和重新開機活動期間發生的相同活動狀態變更。 不過，為了確保應用程式在進行設定變更時能夠迅速回應並順利執行，請務必儘快處理。 因此，Android 具有特定的 API，可在設定變更期間用來保存狀態。
我們稍後會在 [整個生命週期區段的管理狀態](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) 中討論此內容。

### <a name="activity-lifecycle-methods"></a>活動生命週期方法

Xamarin. Android 架構的 Android SDK 和擴充功能，提供了功能強大的模型來管理應用程式內的活動狀態。 當活動的狀態變更時，系統會通知活動，此活動會呼叫該活動上的特定方法。 下圖說明這些與活動生命週期相關的方法：

[![活動生命週期流程圖](images/image2-sml.png)](images/image2.png#lightbox)

如果您是開發人員，可以在活動中覆寫這些方法來處理狀態變更。 不過要注意的是，所有生命週期方法都是在 UI 執行緒上呼叫的，而且會封鎖 OS 使其無法執行下一段 UI 工作，例如隱藏目前的活動、顯示新的活動等等。因此，這些方法中的程式碼應該盡可能簡短，讓應用程式覺得正常執行。 任何長時間執行的工作都應該在背景執行緒上執行。

讓我們來檢查這些生命週期方法及其用途：

#### <a name="oncreate"></a>OnCreate

[>oncreate](xref:Android.App.Activity.OnCreate*) 是建立活動時所要呼叫的第一個方法。
`OnCreate` 一律會覆寫以執行活動可能需要的任何啟動初始化，例如：

- 建立視圖
- 初始化變數
- 將靜態資料系結至清單

`OnCreate` 採用組合 [參數，](xref:Android.OS.Bundle) 這是在組合不是 null 的情況下，用來儲存和傳遞狀態資訊和物件的字典。這表示活動正在重新開機，而且應該從上一個實例還原其狀態。 下列程式碼說明如何從組合中取出值：

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

`OnCreate`完成後，Android 將會呼叫 `OnStart` 。

#### <a name="onstart"></a>OnStart

在完成之後，系統一律會呼叫[OnStart](xref:Android.App.Activity.OnStart) `OnCreate` 。 如果活動必須在活動變成可見之前執行任何特定工作（例如重新整理活動內的目前視圖值），則活動可能會覆寫這個方法。 Android 會 `OnResume` 在此方法之後立即呼叫。

#### <a name="onresume"></a>OnResume

當活動準備好開始與使用者互動時，系統就會呼叫 [OnResume](xref:Android.App.Activity.OnResume) 。
活動應該覆寫這個方法以執行下列工作：

-  (遊戲開發中的常見工作，擴大畫面播放速率) 
- 開始動畫
- 接聽 GPS 更新
- 顯示任何相關的警示或對話
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

`OnResume` 很重要，因為在中完成的任何作業都 `OnPause` 應該在中取消完成 `OnResume` ，因為這是唯一可保證在活動恢復存留期之後執行的生命週期方法 `OnPause` 。

#### <a name="onpause"></a>OnPause

當系統即將讓活動進入背景或活動變成部分遮蔽時，會呼叫[OnPause](xref:Android.App.Activity.OnPause) 。 活動應該覆寫此方法（如果需要的話）：

- 認可持續性資料的未儲存變更

- 終結或清除其他耗用資源的物件

- 遞增畫面播放速率和暫停動畫

- 取消註冊外部事件處理常式或通知處理常式 (亦即系結至服務) 的處理常式。 必須這麼做才能防止活動記憶體流失。

- 同樣地，如果活動已顯示任何對話方塊或警示，則必須使用方法來清除它們 `.Dismiss()` 。

例如，下列程式碼片段會釋出相機，因為活動在暫停時無法使用它：

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

有兩個可能的生命週期方法會在下列情況下呼叫 `OnPause` ：

1. `OnResume` 如果活動要傳回前景，將會呼叫。
1. `OnStop` 如果活動是放置在背景中，則會呼叫。

#### <a name="onstop"></a>OnStop

當使用者不再看見活動時，就會呼叫[OnStop](xref:Android.App.Activity.OnStop) 。 發生下列其中一種情況時，就會發生這種情況：

- 正在啟動新的活動，並涵蓋此活動。
- 現有的活動即將進入前景。
- 活動已被終結。

`OnStop` 不一定會在低記憶體的情況下呼叫，例如當 Android 耗盡資源時，無法正確地背景活動。 基於這個理由，最好不要依賴在 `OnStop` 準備要進行終結的活動時呼叫。 在此情況下，可能會呼叫的下一個生命週期方法是 `OnDestroy` 活動是否即將消失，或 `OnRestart` 活動是否正回到與使用者互動。

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](xref:Android.App.Activity.OnDestroy) 是在活動實例上呼叫的最後一個方法，會在其終結並從記憶體中完全移除時呼叫。 在極端情況下，Android 可能會終止裝載活動的應用程式進程，而不會叫用該活動 `OnDestroy` 。 大部分的活動都不會實作為這個方法，因為在和方法中已完成大部分的清除和關閉 `OnPause` `OnStop` 。 通常會覆 `OnDestroy` 寫方法，以清除可能流失資源的長時間執行工作。 其中一個範例可能是在中啟動的背景執行緒 `OnCreate` 。

在活動終結之後，將不會呼叫任何生命週期方法。

#### <a name="onrestart"></a>OnRestart

[OnRestart](xref:Android.App.Activity.OnRestart) 會在您的活動停止之後呼叫，然後再重新開機。 其中一個很好的例子，就是當使用者在應用程式中的活動上按下 [home （首頁）] 按鈕時。 發生這種情況時 `OnPause` `OnStop` ，會呼叫方法，並將活動移至背景，但不會終結。 如果使用者接著使用工作管理員或類似的應用程式來還原應用程式，則 Android 會呼叫活動的 `OnRestart` 方法。

沒有應該在哪種邏輯上實行的一般方針 `OnRestart` 。 這是因為 `OnStart` 無論活動是建立或重新開機，一律都會叫用，因此活動所需的任何資源都應該在中初始化，而不是在中初始化 `OnStart` `OnRestart` 。

之後呼叫的下一個生命週期方法 `OnRestart` 將會是 `OnStart` 。

### <a name="back-vs-home"></a>返回與首頁

許多 Android 裝置都有兩個不同的按鈕：「上一頁」按鈕和「首頁」按鈕。 您可以在下列 Android 4.0.3 的螢幕擷取畫面中看到這種情況的範例：

[![返回和首頁按鈕](images/image4-sml.png)](images/image4.png#lightbox)

這兩個按鈕之間有細微的差異，即使它們看起來與將應用程式放在背景中的效果一樣。 當使用者按一下 [上一頁] 按鈕時，他們會告知 Android 它們是使用活動完成的。 Android 會摧毀活動。 相反地，當使用者按一下 [首頁] 按鈕時，活動只會放置在背景 &ndash; Android 中，而不會終止活動。

<a name="Managing_State_Throughout_the_Lifecycle"></a>

## <a name="managing-state-throughout-the-lifecycle"></a>在整個生命週期中管理狀態

當活動停止或損毀時，系統會提供機會來儲存活動的狀態，以供稍後解除凍結。
這個儲存的狀態稱為實例狀態。 Android 提供三個選項，可讓您在活動生命週期期間儲存實例狀態：

1. 將基本值儲存在稱為「套件組合」中 `Dictionary` ，以供 Android 用來儲存狀態。 [](xref:Android.OS.Bundle)

1. 建立會保存點陣圖等複雜值的自訂類別。 Android 會使用這個自訂類別來儲存狀態。

1. 規避設定變更生命週期，並假設要在活動中維持狀態的完整責任。

本指南涵蓋前兩個選項。

### <a name="bundle-state"></a>套件組合狀態

儲存實例狀態的主要選項是使用稱為 [組合的索引](xref:Android.OS.Bundle)鍵/值字典物件。
回想一下，當建立的活動指出 `OnCreate` 方法是以參數形式傳遞組合時，就可以使用此組合來還原實例狀態。 不建議將組合用於更複雜的資料，這些資料不會快速或輕鬆地序列化為索引鍵/值組 (例如點陣圖) ;相反地，它應該用於簡單的值，例如字串。

活動會提供方法來協助儲存和抓取套件組合中的實例狀態：

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash; 當活動被終結時，Android 會叫用此功能。 如果活動需要保存任何索引鍵/值狀態專案，則可以執行此方法。

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) &ndash; 這個方法會在 `OnCreate` 方法完成之後呼叫，並提供另一個機會讓活動在初始化完成之後還原其狀態。

下圖說明如何使用這些方法：

[![套件組合狀態流程圖](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

將在活動停止時呼叫[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) 。 它會收到一個套件組合參數，活動可以在其中儲存其狀態。 當裝置發生設定變更時，活動可以使用傳入的物件，藉 `Bundle` 由覆寫來保留活動狀態 `OnSaveInstanceState` 。 例如，請參考下列程式碼：

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

當按一下名為的按鈕時，上述程式碼會遞增名為的整數 `c` `incrementCounter` ，並將結果顯示在名為的中 `TextView` `output` 。 發生設定變更時（例如，當裝置旋轉時），上述程式碼會遺失的值 `c` `bundle` ，因為會是 `null` ，如下圖所示：

[![顯示未顯示先前的值](images/07-sml.png)](images/07.png#lightbox)

為了保留 `c` 此範例中的值，活動可以覆寫 `OnSaveInstanceState` ，將值儲存在組合中，如下所示：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

現在當裝置旋轉至新的方向時，該整數會儲存在組合中，並以下列程式程式碼取出：

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> 一定要呼叫的基底實作為， `OnSaveInstanceState` 如此一來，也可以儲存視圖階層的狀態。

##### <a name="view-state"></a>檢視狀態

覆寫 `OnSaveInstanceState` 是一種適當的機制，可將暫時性資料儲存在跨方向變更的活動中，例如上述範例中的計數器。 但是，的預設執行 `OnSaveInstanceState` 會負責將暫時性資料儲存在每個視圖的 UI 中，只要每個視圖都有指派的識別碼即可。 例如，假設應用程式具有 `EditText` 以 XML 定義的元素，如下所示：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

由於 `EditText` 控制項有 `id` 指派，當使用者輸入一些資料並旋轉裝置時，仍會顯示資料，如下所示：

[![以橫向模式保留資料](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[](xref:Android.App.Activity.OnRestoreInstanceState*)將在之後呼叫 OnRestoreInstanceState `OnStart` 。 它讓活動有機會還原先前儲存到套件組合的任何狀態 `OnSaveInstanceState` 。 不過，這是提供給的相同組合 `OnCreate` 。

下列程式碼會示範如何在中還原狀態 `OnRestoreInstanceState` ：

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

這種方法的存在，是為了在應還原狀態時提供一些彈性。 有時候，在還原實例狀態之前，先完成所有的初始化就更適合。 此外，現有活動的子類別可能只會想要還原實例狀態的特定值。 在許多情況下，不需要覆寫 `OnRestoreInstanceState` ，因為大部分的活動都可以使用提供的組合來還原狀態 `OnCreate` 。

如需使用儲存狀態的範例 `Bundle` ，請參閱逐步解說 [-儲存活動狀態](saving-state.md)。

#### <a name="bundle-limitations"></a>套件組合限制

雖然 `OnSaveInstanceState` 可讓您輕鬆地儲存暫時性資料，但有一些限制：

- 在所有情況下都不會呼叫。 例如，按 **Home** 或 **Back** 結束活動不會導致 `OnSaveInstanceState` 呼叫。

- 傳入的組合 `OnSaveInstanceState` 不是針對大型物件（例如影像）所設計。 如果是大型物件，最好是從 [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) 儲存物件，如下所述。

- 使用套件組合儲存的資料會序列化，這可能會導致延遲。

套件組合狀態適用于不使用海量儲存體的簡單資料，而 *非設定實例資料* 則適用于更複雜的資料，或需要耗用大量資源的資料，例如從 web 服務呼叫或複雜的資料庫查詢。 非設定實例資料會視需要儲存在物件中。 下一節將介紹如何 `OnRetainNonConfigurationInstance` 透過設定變更來保留更複雜的資料類型。

### <a name="persisting-complex-data"></a>保存複雜資料

除了保存組合中的資料之外，Android 也支援藉由覆寫 [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) 並傳回 `Java.Lang.Object` 包含要保存之資料的實例來儲存資料。 使用儲存狀態有兩個主要優點 `OnRetainNonConfigurationInstance` ：

- 從傳回的物件會 `OnRetainNonConfigurationInstance` 因為較大、更複雜的資料類型而執行得很好，因為記憶體會保留此物件。

- `OnRetainNonConfigurationInstance`只有在需要時，才會視需要呼叫方法。 這比使用手動快取更經濟實惠。

使用 `OnRetainNonConfigurationInstance` 很適合用來多次取出資料的情況，例如在 web 服務呼叫中。 例如，請考慮下列搜尋 Twitter 的程式碼：

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

此程式碼會從已格式化為 JSON 的 web 抓取結果、加以剖析，然後將結果顯示在清單中，如下列螢幕擷取畫面所示：

[![畫面上顯示的結果](images/06-sml.png)](images/06.png#lightbox)

發生設定變更時（例如，當裝置旋轉時），程式碼會重複此程式。 為了重複使用原始抓取的結果，而不會造成不必要的重複網路呼叫，我們可以使用 `OnRetainNonconfigurationInstance` 來儲存結果，如下所示：

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

現在，當裝置旋轉時，原始的結果會從屬性中取出 `LastNonConfiguartionInstance` 。 在此範例中，結果會包含 `string[]` 包含推文的。 由於 `OnRetainNonConfigurationInstance` 需要 `Java.Lang.Object` 傳回，因此 `string[]` 會包裝在子類別的類別中 `Java.Lang.Object` ，如下所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，嘗試使用 `TextView` 做為所傳回的物件 `OnRetainNonConfigurationInstance` 將會流失活動，如下列程式碼所示：

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

在本節中，我們已瞭解如何使用保留簡單的狀態資料 `Bundle` ，並使用來保存更複雜的資料類型 `OnRetainNonConfigurationInstance` 。

## <a name="summary"></a>摘要

Android 活動生命週期提供一個功能強大的架構，可讓您在應用程式內進行活動的狀態管理，但很難瞭解和實行。 本章介紹活動可能會在其存留期內經歷的不同狀態，以及與這些狀態相關聯的生命週期方法。 接下來，您可以在其中每個方法中，針對應執行的邏輯種類提供指引。

## <a name="related-links"></a>相關連結

- [處理旋轉](~/android/app-fundamentals/handling-rotation.md)
- [Android 活動](xref:Android.App.Activity)
