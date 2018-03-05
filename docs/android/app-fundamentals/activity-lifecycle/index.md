---
title: "活動開發週期"
description: "活動是基本的建置組塊的 Android 應用程式，它們可以存在於不同的狀態數目。 活動的生命週期有具現化開始和結束解構而且之間包含許多狀態。 當活動變更狀態時，會呼叫適當的生命週期事件的方法，通知即將發生的狀態變更的活動，並讓它可以執行程式碼，該變更會隨著調整。 這篇文章會檢查活動的生命週期和說明責任活動有每個階段的行為良好、 可靠的應用程式一部分這些狀態變更。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ccd55d4d7f1aea55110e109bed1fbd4ebc90b93f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="activity-lifecycle"></a>活動開發週期

_活動是基本的建置組塊的 Android 應用程式，它們可以存在於不同的狀態數目。活動的生命週期有具現化開始和結束解構而且之間包含許多狀態。當活動變更狀態時，會呼叫適當的生命週期事件的方法，通知即將發生的狀態變更的活動，並讓它可以執行程式碼，該變更會隨著調整。這篇文章會檢查活動的生命週期和說明責任活動有每個階段的行為良好、 可靠的應用程式一部分這些狀態變更。_

## <a name="activity-lifecycle-overview"></a>活動生命週期概觀

活動是特定的 Android 不尋常程式設計概念。 在傳統的應用程式開發通常沒有靜態的 main 方法，這會執行，以啟動應用程式。 與 Android，不過，事情就會不同。Android 應用程式可以透過任何已註冊的活動，在應用程式內啟動。 實際上，大部分的應用程式只能指定為應用程式進入點的特定活動。 不過，如果應用程式當機，或已終止的作業系統，作業系統可以嘗試重新啟動應用程式在開啟的最後一個活動或先前的活動堆疊內的任何其他地方。
此外，OS 可能暫停的活動時不是作用中，而且如果記憶體不足時回收它們。 請仔細考慮必須對允許應用程式的活動重新啟動，尤其是，從上一個活動的資料取決於活動中正確地還原其狀態。

活動的生命週期會實作為作業系統的方法呼叫，整個活動的生命週期的集合。 這些方法可讓開發人員實作是為了滿足其應用程式的狀態和資源管理需求的功能。

它是極為重要的應用程式開發人員分析每個活動的需求，以判斷需要實作活動的生命週期所公開的方法。 若要這樣做可能導致應用程式不穩定、 損毀、 資源過度膨脹和甚至於基礎的作業系統不穩定。

本章會檢查活動的生命週期中的詳細資料，包括：

-  活動狀態
-  存留週期方法
-  保留應用程式的狀態


本節也包含[逐步解說](~/android/app-fundamentals/activity-lifecycle/saving-state.md)實用範例提供如何有效率地將狀態儲存在活動的生命週期期間。 您應該具有活動的生命週期，以及如何支援 Android 應用程式中了解此章節的結尾。

## <a name="activity-lifecycle"></a>活動開發週期

Android 的活動生命週期包含活動類別內所公開的方法，為開發人員提供的資源管理架構的集合。 此架構可讓開發人員符合應用程式中的每個活動的唯一的狀態管理需求，適當地處理資源管理。

### <a name="activity-states"></a>活動狀態

Android OS arbitrates 其狀態為基礎的活動。 這有助於 Android 識別出不再使用時，允許 OS 回收記憶體和資源的活動。 下圖說明活動可以瀏覽在其存留期間的狀態：

[ ![活動狀態圖](images/image1-sml.png)](images/image1.png)

這些狀態可以拆成 4 個主要群組，如下所示：

1.  *作用中或執行*&ndash;活動會視為作用中或執行如果它們是在前景，也稱為活動堆疊的頂端。 這會被視為最高優先權活動在 Android 中，而且在這種情況就只會作業系統在極端情況下終止，例如如同活動便會嘗試使用更多的記憶體比使用裝置上因為這可能會造成變得沒有回應 UI。

1.  *暫停*&ndash;時裝置會進入睡眠狀態，或活動仍看得到，但是部分隱藏新的、 非完整大小或透明的活動，活動會被視為已暫停。 已暫停的活動是仍在執行，也就是維護所有的狀態和成員資訊，並維持附加至視窗管理員。 這會被視為第二個最高優先權活動在 Android 中，因此就只會終止由 OS 如果刪除此活動會滿足保持穩定且回應迅速的作用中/執行的活動所需的資源需求。

1.  *停止/Backgrounded* &ndash;已停止或在背景中，會被視為完全遮住其他活動的活動。
    停止的活動仍會嘗試保留其狀態和成員資訊，只要可行的但已停止的活動數字會被視為最低優先順序的三種狀態，而且在這種情況，作業系統會終止活動處於此狀態，以滿足資源較高的優先順序活動的需求。

1.  *重新啟動*&ndash;便可從任何位置的活動暫停為停止從記憶體移除 android 開發週期中。 如果使用者導覽回到活動必須重新啟動它，還原至先前儲存的狀態，然後顯示給使用者。


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>活動重新建立，以便回應組態變更

若要讓事情更複雜，Android 會擲回一個詳細扳手混合稱為組態變更。 組態變更會快速活動解構/重新 creation 循環時活動的組態變更，例如裝置時所發生[旋轉](~/android/app-fundamentals/handling-rotation.md)（和活動需要重新取得內建的橫向或縱向模式），當顯示鍵盤 （以及活動會提供調整大小的機會），或是當裝置會變成 「 停駐、 和其他項目。

組態變更仍會導致相同的活動狀態變更，停止並重新啟動活動期間發生。 不過，為了確定應用程式都有回應，而且也可以在組態變更期間執行，請務必它們會盡快處理。 因為這個緣故，Android 提供可用來將狀態保存組態變更期間的特定 API。
我們會更新版本中[管理狀態整個生命週期](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle)> 一節。

### <a name="activity-lifecycle-methods"></a>活動存留週期方法

Android SDK，藉由擴充，Xamarin.Android 架構提供了功能強大的模型管理的應用程式內的活動狀態。 活動的狀態已變更時，活動會收到的作業系統，該活動呼叫特有的方法。 下圖將說明這些活動的生命週期的關聯性中的方法：

[ ![活動生命週期的流程圖](images/image2-sml.png)](images/image2.png)

身為開發人員，您可以藉由覆寫這些方法，活動中的處理狀態變更。 請務必注意，不過，所有的存留週期方法 UI 執行緒上呼叫，且將會封鎖從執行下一個片段的 UI 工作，例如隱藏目前的活動，顯示新的活動等作業系統。因此，這些方法中的程式碼應越簡短越好，以讓覺得也執行應用程式。 背景執行緒上應該執行任何長時間執行的工作。

讓我們檢查每個這些存留週期方法和用法：

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)是第一種方法來建立活動時呼叫。
`OnCreate` 一定會覆寫以執行所需活動例如任何啟動初始設定：

-  建立檢視
-  初始化變數
-  靜態資料繫結至清單


`OnCreate` 會採用[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)參數，這是用於儲存和傳遞如果組合不是 null 的活動之間的狀態資訊和物件的字典，這表示活動正在重新啟動，而且它應該還原其狀態從前一個執行個體。 下列程式碼說明如何從配套擷取的值：

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

一次`OnCreate`已完成之後，Android 將會呼叫`OnStart`。

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/)之後，系統一律會呼叫`OnCreate`已完成。 如果他們需要先執行特定工作的任何權限，才能活動隨即顯示，例如重新整理檢視，在活動的目前值，活動可能會覆寫這個方法。 Android 將會呼叫`OnResume`後面這個方法。

#### <a name="onresume"></a>OnResume

系統呼叫[OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/)當活動已準備好開始與使用者互動。
活動應該覆寫這個方法，以執行下列工作：

-  根本上畫面播放速率 （遊戲的建置中的一般工作）
-  啟動動畫
-  正在接聽的 GPS 更新
-  顯示任何相關的警示或對話方塊
-  網路外部的事件處理常式


例如，下列程式碼片段會示範如何初始化相機：

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

`OnResume` 很重要，因為這是任何作業在完成`OnPause`應該是在未完成`OnResume`，因為它一定會執行之後的唯一生命週期方法`OnPause`活動回到生命時。

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/)系統即將在背景或活動會變成暗放置活動時呼叫。 活動應該覆寫這個方法，如果它們需要：

-   未儲存的變更認可到持續性資料

-   損毀，或清除耗用資源的其他物件

-   向下畫面播放速率和暫停的動畫遞增

-   取消註冊通知的處理常式 （也就是那些繫結至服務） 或外部的事件處理常式。 這必須以避免活動記憶體流失。

-   同樣地，如果活動顯示任何對話方塊或警示，它們必須清除與`.Dismiss()`方法。

例如，下列程式碼片段會在釋放相機活動不能使用它暫停時：

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

有兩種可能的生命週期方法，將呼叫之後`OnPause`:

1.  `OnResume` 如果活動是要傳回至前景，會呼叫。
1.  `OnStop` 如果活動正被放在背景中將會呼叫。


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)不再顯示給使用者的活動時呼叫。 這會發生下列其中一項：

-  正在啟動新活動，並涵蓋設定此活動。
-  現有的活動會回到前景。
-  活動正在被終結。


`OnStop` 永遠不可呼叫在記憶體不足的情況下，例如當 Android 耗盡資源並無法正確背景活動。 基於這個理由，最好不依賴`OnStop`為解構準備活動時進行呼叫。 可以呼叫之後這一個將會是下一個存留週期方法`OnDestroy`如果活動即將消失，或`OnRestart`如果活動是傳回與使用者互動。

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/)最後再已經終結並從記憶體中完全移除，會將活動執行個體上呼叫的方法。 在極端情況下 Android 可能會刪除應用程式處理序裝載的活動，將會導致`OnDestroy`不叫用。 大部分的活動不會實作這個方法，因為大部分清除，而且已完成關機`OnPause`和`OnStop`方法。 `OnDestroy`通常會覆寫方法清除長時間在執行的資源可能遺漏資源。 此動作的範例可能已在中啟動的背景執行緒`OnCreate`。

會有任何活動已終結後呼叫的存留週期方法。

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/)之後您的活動已停止，重新啟動之前呼叫。 良好的範例狀況是當使用者按下應用程式中的活動上的 [首頁] 按鈕。 當發生這種情況`OnPause`然後`OnStop`方法呼叫，並移至背景活動，但並不會終結。 若使用者是使用工作管理員或類似的應用程式還原應用程式，將會呼叫 Android`OnRestart`活動的方法。

有沒有一般的指導方針進行何種邏輯應該在實作`OnRestart`。 這是因為`OnStart`永遠叫用不論是否已建立的活動或正在重新啟動，因此應該在初始化活動所需的任何資源`OnStart`，而非`OnRestart`。

下一個存留週期方法之後呼叫`OnRestart`將`OnStart`。

### <a name="back-vs-home"></a>備份與。首頁

許多的 Android 裝置有兩個不同的按鈕: [上一頁] 按鈕和 「 家用 」 按鈕。 Android 4.0.3 的下列螢幕擷取畫面中，可以看到此動作的範例：

[ ![上一步和首頁按鈕](images/image4-sml.png)](images/image4.png)

即使它們有相同的效果，在背景中放置應用程式便會顯示沒有兩個按鈕，細微的差異。 當使用者按一下 [上一頁] 按鈕時，它們會告知 Android 它們與活動完成。 Android 將會損毀的活動。 相反地，當使用者按一下 [首頁] 按鈕活動只是將放入背景&ndash;Android 不會終止活動。

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>整個生命週期管理狀態

當活動已停止或終結時系統會提供機會儲存稍後解除凍結的活動的狀態。
這個儲存的狀態被指執行個體狀態。 Android 提供活動生命週期當中儲存執行個體狀態的三個的選項：

1. 儲存中的基本值`Dictionary`稱為[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)Android 將會使用儲存狀態。

1. 建立自訂的類別，將會保留複雜的值，例如點陣圖。 Android 將會使用這個自訂類別來儲存狀態。

1. 規避組態變更生命週期，並假設完成負責維護活動的狀態。


本指南涵蓋前兩個選項。

 <a name="Bundle_State" />


### <a name="bundle-state"></a>組合狀態

儲存執行個體狀態的主要選項是使用索引鍵/值字典物件，稱為[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)。
活動在建立時，請記得，`OnCreate`方法組合做為參數傳遞時，此套件組合可以用來還原執行個體的狀態。 不建議使用組合更複雜或的資料將不會快速輕鬆地將序列化為索引鍵/值組 （例如點陣圖）;相反地，它應該用於如字串的簡單值。

活動會提供方法，以協助進行儲存和擷取執行個體中的狀態組合：

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash;這會叫用由 Android 活動正在被終結時。 活動可以實作這個方法，如果它們需要保存的任何索引鍵/值的狀態項目。

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash;之後呼叫`OnCreate`方法完成之後，並提供另一個用來初始化完成之後，還原其狀態的活動的機會。

下圖說明如何使用這些方法：

[ ![組合狀態的流程圖](images/image3-sml.png)](images/image3.png)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/)正在停止活動被呼叫。 它會接收活動可以儲存在其狀態的組合參數。 當裝置發生設定變更時，可以使用活動`Bundle`會傳遞至藉由覆寫會保留的活動狀態中的物件`OnSaveInstanceState`。 例如，請參考下列程式碼：

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

上述程式碼遞增整數名為`c`時按鈕名為`incrementCounter`按一下時，顯示在結果`TextView`名為`output`。 組態變更會-例如，當裝置旋轉-上述程式碼會失去值`c`因為`bundle`會`null`下圖, 所示：

[ ![顯示不會顯示先前的值](images/07-sml.png)](images/07.png)

若要保留的值`c`在此範例中，活動可以覆寫`OnSaveInstanceState`，配套中儲存值，如下所示：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

現在為新的方向旋轉裝置時，整數組合中儲存和擷取的一行：

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> **注意：**很重要一律呼叫的基底實作`OnSaveInstanceState`這樣也可以儲存的檢視階層架構的狀態。


<a name="View_State" />

##### <a name="view-state"></a>檢視狀態

覆寫`OnSaveInstanceState`是適當的機制來儲存暫時性資料在活動中，跨方向的變更，例如上述範例中的計數器。 不過的預設實作`OnSaveInstanceState`會負責將 UI 中的每個檢視，用於儲存暫時性資料，只要每個檢視都有指派的識別碼。 例如，假設應用程式有`EditText`，如下所示定義 XML 項目：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

因為`EditText`控制項有`id`指派，當使用者輸入一些資料，並旋轉裝置時，資料仍會顯示，如下所示：

[ ![資料會保留在橫向模式](images/08-sml.png)](images/08.png)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/)將呼叫之後`OnStart`。 提供活動還原先前儲存之組合至先前的任何狀態的機會`OnSaveInstanceState`。 這是提供給相同組合`OnCreate`，但是。

下列程式碼示範如何在還原狀態`OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

這個方法存在時還原狀態，提供一些彈性。 有時候很等候，直到還原執行個體狀態之前完成所有初始設定更適當。 此外，現有活動的子類別可能只想要從執行個體狀態還原的特定值。 在許多情況下，就不需要覆寫`OnRestoreInstanceState`，因為大部分的活動可以還原使用組合提供給狀態`OnCreate`。

如需的儲存狀態的使用範例`Bundle`，請參閱[逐步解說-將儲存的活動狀態](saving-state.md)。


<a name="Bundle_Limitations" />

#### <a name="bundle-limitations"></a>配套的限制

雖然`OnSaveInstanceState`使得簡單儲存暫時性資料，它會有一些限制：

-   它不會在所有情況下呼叫。 例如，按**首頁**或**回**結束活動不會導致`OnSaveInstanceState`所呼叫。

-   組合傳遞至`OnSaveInstanceState`不是為大型物件，例如影像。 在大型物件，儲存從物件的情況下[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/)都適合，如下所述。

-   使用組合來儲存的資料會序列化，這會導致延遲。

組合狀態是適用於簡單的資料不會使用太多記憶體，而*非設定執行個體資料*適用於更複雜的資料，或是會高度耗費資源擷取的資料，例如 web 服務呼叫或複雜資料庫查詢。 視需要取得會在物件中儲存非組態執行個體資料。 下一節將介紹`OnRetainNonConfigurationInstance`保留更複雜的資料類型，透過組態變更的方式。

<a name="Persisting_Complex_Data" />

### <a name="persisting-complex-data"></a>保存的複雜資料

組合中的保存資料，除了 Android 也支援將資料儲存方式是覆寫[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/)和傳回的執行個體`Java.Lang.Object`，其中包含要保存的資料。 有兩個主要的優點之一使用`OnRetainNonConfigurationInstance`儲存狀態：

-   從傳回的物件`OnRetainNonConfigurationInstance`效能與規模較大且更複雜的資料類型，因為記憶體會保留此物件。

-   `OnRetainNonConfigurationInstance`方法會視情況下，被呼叫，而且只在需要時。 這是更為經濟比使用手動快取。

使用`OnRetainNonConfigurationInstance`是適用於很昂貴多次擷取資料的情況，例如 web 服務呼叫。 例如，請考慮下列程式碼，以搜尋 Twitter:

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

這段程式碼擷取結果從格式化為 JSON web、 會剖析，並再呈現結果清單，如下列螢幕擷取畫面所示：

[ ![在螢幕上顯示的結果](images/06-sml.png)](images/06.png)

設定發生變更時-比方說，當裝置旋轉-程式碼重複此程序。 若要重複使用原始擷取的結果並不會造成不必要、 備援的網路呼叫，我們可以使用`OnRetainNonconfigurationInstance`來儲存結果，如下所示：

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

現在當旋轉裝置時，從擷取的原始結果`LastNonConfiguartionInstance`屬性。 在此範例中，結果包含`string[]`包含推文。 因為`OnRetainNonConfigurationInstance`要求`Java.Lang.Object`傳回，`string[]`包裝在類別中的子類別`Java.Lang.Object`，如下所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，嘗試使用`TextView`從傳回的物件為`OnRetainNonConfigurationInstance`流失活動，如下列程式碼所示：

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

在本節中，我們學到如何使用簡單的狀態資料保留`Bundle`，並將保存具有複雜資料型別`OnRetainNonConfigurationInstance`。

## <a name="summary"></a>總結

Android 活動的生命週期提供強大的架構的應用程式內的活動的狀態管理，但可能很難了解和實作。 本章介紹活動可能在其存留期，以及與這些狀態相關聯的存留週期方法期間會經歷的不同狀態。 接下來，而應執行何種邏輯，在每一種方法提供指引。


## <a name="related-links"></a>相關連結

- [處理旋轉](~/android/app-fundamentals/handling-rotation.md)
- [Android 的活動](https://developer.xamarin.com/api/type/Android.App.Activity/)
