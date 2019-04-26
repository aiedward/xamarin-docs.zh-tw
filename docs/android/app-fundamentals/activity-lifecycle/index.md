---
title: 活動開發週期
description: 活動的 Android 應用程式的基本建置組塊，而它們可以存在於不同的狀態數目。 活動開發週期具現化的開頭和結尾解構，因此之間包含許多狀態。 當活動變更狀態時，會呼叫適當的生命週期事件的方法，通知即將發生的狀態變更的活動，並讓它執行程式碼來調整到該變更。 本文會檢查活動的生命週期，並說明負責該活動就會具有每個階段的行為良好、 可靠的應用程式一部分的這些狀態變更。
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 3592a3027469cb9997d973db53d636ddea9e679d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024239"
---
# <a name="activity-lifecycle"></a>活動開發週期

_活動的 Android 應用程式的基本建置組塊，而它們可以存在於不同的狀態數目。活動開發週期具現化的開頭和結尾解構，因此之間包含許多狀態。當活動變更狀態時，會呼叫適當的生命週期事件的方法，通知即將發生的狀態變更的活動，並讓它執行程式碼來調整到該變更。本文會檢查活動的生命週期，並說明負責該活動就會具有每個階段的行為良好、 可靠的應用程式一部分的這些狀態變更。_

## <a name="activity-lifecycle-overview"></a>活動生命週期概觀

活動是 Android 專用的不尋常程式設計概念。 在傳統的應用程式開發通常有靜態的 main 方法，這會執行，以啟動應用程式。 與 Android，不過，項目是不同的。Android 應用程式可以透過任何已註冊的活動，在應用程式內啟動。 實際上，大部分的應用程式只能指定為應用程式進入點的特定活動。 不過，如果應用程式損毀，或已終止由 OS，OS 可以嘗試重新啟動應用程式在最後一個開啟的活動或先前的活動堆疊內的任何其他地方。
此外，作業系統可能不在作用中時暫停活動和回收它們時記憶體不足。 請仔細考慮您必須對允許應用程式，活動重新啟動時，特別是當相依的資料是來自上一個活動的活動，正確還原其狀態。

活動開發週期會實作為活動生命週期期間的作業系統的方法呼叫的集合。 這些方法可讓開發人員實作，才能滿足他們的應用程式的狀態和資源管理需求的功能。

它是非常重要的應用程式開發人員分析的每個活動的需求，決定哪些活動開發週期所公開的方法實作。 若要這樣做可能導致應用程式不穩定、 當機、 資源過度膨脹和甚至可能還基礎作業系統不穩定。

本章檢查活動開發週期，在 詳細資料，包括：

-  活動狀態
-  生命週期方法
-  保留應用程式的狀態


本節也包含[逐步解說](~/android/app-fundamentals/activity-lifecycle/saving-state.md)，提供有關如何有效率地將狀態儲存在活動生命週期的實用範例。 結束時的這一章中，您應該了解活動開發週期，以及如何支援 Android 應用程式中。

## <a name="activity-lifecycle"></a>活動開發週期

Android 活動開發週期包含活動類別中公開的方法，為開發人員提供的資源管理架構的集合。 此架構可讓開發人員為了滿足應用程式內的每個活動的唯一狀態管理需求並適當地處理資源管理。

### <a name="activity-states"></a>活動狀態

Android OS 將會進行仲裁活動，根據其狀態。 這有助於識別已不再使用，允許以回收記憶體和資源的 OS 中的活動的 Android。 下圖說明活動在其存留期間，可以瀏覽狀態：

[![活動狀態圖表](images/image1-sml.png)](images/image1.png#lightbox)

這些狀態可以分成 4 個主要群組，如下所示：

1.  *作用中或執行*&ndash;活動會被視為作用中或執行它們是否在前景，也就是活動堆疊的頂端。 這會被視為在 Android 中，最高的優先順序活動，因此只即將刪除由 OS 在極端情況下，例如如同活動便會嘗試使用更多的記憶體比裝置上的可用，這可能會造成 UI 沒有回應。

1.  *暫停*&ndash;當裝置進入睡眠狀態，或活動是仍然可見，但部分隱藏新的、 非完整大小或透明的活動時，活動會被視為已暫停。 已暫停的活動是仍在執行，也就是維護所有的狀態和成員資訊，並會附加到視窗管理員。 這會被視為第二個最高的優先順序活動在 Android 中，因此將只由 OS 終止，如果刪除此活動會滿足維持穩定且回應迅速的作用中/執行的活動所需的資源需求。

1.  *停止/Backgrounded* &ndash;停止，或在背景中，會被視為完全遮住的另一個活動的活動。
    停止的活動仍會嘗試保留其狀態和成員資訊，只要可能，但已停止活動數字會被視為最低優先順序的三種狀態，且 OS 會在此情況下，終止活動，在此狀態下第一次，以滿足資源較高的優先順序活動的需求。

1.  *重新啟動*&ndash;便可從任何位置的活動暫停為停止從記憶體移除 android 生命週期中。 如果使用者巡覽回到必須重新啟動它，活動就會還原為其先前儲存的狀態，，，然後顯示給使用者。


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>活動以回應組態變更的重新建立

若要更多複雜，Android 會擲回一個詳細的扳手中的組合，稱為組態變更。 組態變更會快速活動解構/重新 creation 循環時活動的組態變更，例如裝置時，會發生[旋轉](~/android/app-fundamentals/handling-rotation.md)（和活動必須重新取得內建的橫向或縱向模式），會顯示鍵盤 （與活動會提供功能的機會來自行調整大小），或當裝置處於停駐，其他項目。

組態變更仍會導致相同的活動狀態變更期間停止並重新啟動的活動會發生。 不過，為了確保應用程式有回應，並執行也進行組態變更時，務必它們會盡快處理。 因為這個緣故，Android 會提供可用來將狀態保存在組態變更期間的特定 API。
我們將討論這稍後[管理狀態在整個生命週期](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle)一節。

### <a name="activity-lifecycle-methods"></a>活動開發週期方法

Android SDK 和延伸模組，Xamarin.Android framework 會提供功能強大的模型來管理應用程式內的活動狀態。 活動的狀態會變更，活動會由 OS，它會在該活動上呼叫特定方法收到通知。 下圖說明活動開發週期的關聯性中的這些方法：

[![活動生命週期的流程圖](images/image2-sml.png)](images/image2.png#lightbox)

身為開發人員，您可以藉由覆寫這些方法，活動中的處理狀態變更。 請務必注意，不過，所有的生命週期方法 UI 執行緒上呼叫，且將會封鎖作業系統執行下一個片段的 UI 工作，例如隱藏目前的活動，顯示新的活動等等。因此，這些方法中的程式碼應該越簡短越好，讓應用程式可以順利執行。 任何長時間執行的工作應該在背景執行緒上執行。

讓我們檢查每個這些生命週期方法和其用法：

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)是第一種方法來建立活動時呼叫。
`OnCreate` 一定會覆寫執行可能需要活動這類任何啟動初始設定：

-  建立檢視
-  初始化變數
-  靜態資料繫結至清單


`OnCreate` 會採用[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)參數，也就是用於儲存及組合不是 null 的活動之間傳遞狀態資訊和物件的字典，這表示活動正在重新啟動，而且它應該還原其狀態從先前的執行個體。 下列程式碼說明如何從配套擷取值：

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

一次`OnCreate`已完成之後，Android 會呼叫`OnStart`。

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/)之後，系統一定會呼叫`OnCreate`完成。 如果他們需要執行特定工作的權利，活動才會顯示諸如重新整理目前活動內的檢視表的值，活動可能會覆寫這個方法。 Android 會呼叫`OnResume`後面這個方法。

#### <a name="onresume"></a>OnResume

系統呼叫[OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/)活動時準備好開始與使用者互動。
活動應該覆寫這個方法，以執行下列工作：

-  緩慢增加的畫面播放速率 （遊戲的建置中常見的工作）
-  啟動動畫
-  接聽 GPS 更新
-  顯示相關警示或對話方塊
-  連接外部事件處理常式


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

`OnResume` 很重要，因為這是任何作業中完成`OnPause`應該是在未完成`OnResume`，因為它是唯一的生命週期方法，之後會執行`OnPause`時將活動帶回生命。

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/)系統要在背景或部分而變成模糊的活動，放置活動時呼叫。 活動應該覆寫這個方法，如果它們需要：

-   未儲存的變更認可到永續性資料

-   損毀，或清除耗用資源的其他物件

-   Ramp 下畫面播放速率和暫停的動畫

-   取消註冊外部事件處理常式或通知處理常式 （也就是其繫結至一個服務）。 這必須為了防止活動記憶體流失。

-   同樣地，如果活動顯示任何對話方塊或警示，他們必須先清除上與`.Dismiss()`方法。

例如，下列程式碼片段會釋放相機，活動不能使用它而暫停：

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

有兩種可能的生命週期方法會在之後呼叫`OnPause`:

1.  `OnResume` 如果活動是要傳回至前景，會呼叫。
1.  `OnStop` 如果活動放在背景中將會呼叫。


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)不再對使用者顯示的活動時呼叫。 會發生這種情況是當發生下列其中一項：

-  正在啟動新的活動，並涵蓋此活動。
-  現有的活動會回到前景。
-  活動正在被終結。


`OnStop` 可能不一定會呼叫在記憶體不足的情況下，當 Android 會耗盡資源及無法正確背景活動等。 基於這個理由，最好是不依賴`OnStop`取得解構準備活動時呼叫。 下一步 的生命週期方法可能被呼叫之後這`OnDestroy`如果活動即將消失，或`OnRestart`如果活動會回到與使用者互動。

#### <a name="ondestroy"></a>OnDestroy

[Editorwindow](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/)是它已損毀，並從記憶體中完全移除之前，會將活動執行個體呼叫的最後一個方法。 在極端情況下，Android 可能會終止裝載活動，這會導致應用程式處理序`OnDestroy`不叫用。 大部分的活動不會實作這個方法，因為大部分清除，而且已完成關機`OnPause`和`OnStop`方法。 `OnDestroy`通常會覆寫方法以清除 長時間在執行資源可能會流失資源。 這個範例可能是已在中啟動的背景執行緒`OnCreate`。

將會有已終結活動之後呼叫任何存留週期方法。

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/)之後您的活動已停止，在重新啟動它之前呼叫。 當使用者按下應用程式中的活動上的 [首頁] 按鈕時，會是一個很好的範例。 當發生這種情況`OnPause`，然後`OnStop`呼叫方法時，並移至 背景活動，但不是會損毀。 若使用者是使用工作管理員 或類似的應用程式中還原應用程式，會呼叫 Android`OnRestart`活動方法。

有沒有一般的指導方針進行何種邏輯應該在實作`OnRestart`。 這是因為`OnStart`永遠會叫用不論是否已建立活動或正在重新啟動，因此應該在初始化活動所需的任何資源`OnStart`，而非`OnRestart`。

下一步 的生命週期方法之後呼叫`OnRestart`會`OnStart`。

### <a name="back-vs-home"></a>將 vs。首頁

許多 Android 裝置有兩個不同的按鈕: [上一頁] 按鈕和 [首頁] 按鈕。 Android 4.0.3 的下列螢幕擷取畫面中，就可以看到這類範例：

[![上一步 和首頁按鈕](images/image4-sml.png)](images/image4.png#lightbox)

即使它們似乎具有相同的效果，將應用程式放在背景中，沒有兩個按鈕時，細微的差異。 當使用者按一下 [上一頁] 按鈕時，它們會告知 Android，它們會完成活動。 Android 將會損毀的活動。 相反地，當使用者按一下 [首頁] 按鈕的活動只放入背景&ndash;Android 不會終止活動。

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>在整個生命週期的管理狀態

當活動已停止，或被終結時系統會提供使用者將可以儲存稍後解除凍結的活動的狀態。
這已儲存狀態稱為執行個體的狀態。 Android 提供執行個體狀態儲存在活動開發週期期間的三個的選項：

1. 儲存中的基本值`Dictionary`稱為[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)Android 會使用儲存狀態。

1. 建立自訂的類別，將會保留複雜的值，例如點陣圖。 Android 會使用此自訂類別來儲存狀態。

1. 規避設定變更生命週期中，並假設完成負責維護活動中的狀態。


本指南涵蓋前兩個選項。



### <a name="bundle-state"></a>套件組合狀態

儲存執行個體狀態的主要選項是使用索引鍵/值字典物件，稱為[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)。
當活動建立時，請記得，`OnCreate`方法會傳遞做為參數的組合，此套件組合可以用來還原執行個體的狀態。 不建議用於更複雜的資料不會快速或輕鬆地序列化為索引鍵/值組 （例如點陣圖）; 中的套件組合相反地，它應該用於類似字串的簡單值。

活動會提供方法，以協助儲存和擷取組合中的執行個體狀態：

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash;這時所叫用 android 活動正在被終結。 活動可以實作這個方法，如果它們需要保存任何索引鍵/值的狀態項目。

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash;之後，這會呼叫`OnCreate`方法完成時，並提供另一個機會來初始化完成之後，還原其狀態的活動。

下圖說明如何使用這些方法：

[![套件組合狀態流程圖](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/)正在停止活動時所呼叫。 它會接收活動可以儲存在其狀態的套件組合參數。 當裝置發生設定變更時，可以使用活動`Bundle`物件會傳遞至活動的狀態，來覆寫保留， `OnSaveInstanceState`。 例如，請參考下列程式碼：

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

上述程式碼會遞增整數，名為`c`按鈕，名為時`incrementCounter`按一下時，顯示在結果`TextView`名為`output`。 當組態變更會-例如，當裝置旋轉-上述的程式碼會失去的值`c`因為`bundle`會是`null`，如下圖所示：

[![顯示不會顯示先前的值](images/07-sml.png)](images/07.png#lightbox)

若要保留的值`c`在此範例中，活動可以覆寫`OnSaveInstanceState`，將值儲存在組合中，如下所示：

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
> 它是務必一律呼叫的基底實作`OnSaveInstanceState`，因此也可以儲存的檢視階層的狀態。



##### <a name="view-state"></a>檢視狀態

覆寫`OnSaveInstanceState`是適當的機制，在活動中的暫時性的資料儲存在方向變更，例如上述範例中的計數器。 不過的預設實作`OnSaveInstanceState`會負責將暫時性的資料儲存在 UI 中的每個檢視，只要每個檢視都有一個指派的識別碼。 舉例來說，假設應用程式有`EditText`，如下所示，在 XML 中定義的項目：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

由於`EditText`控制項有`id`指派，當使用者輸入一些資料並旋轉裝置時，資料仍會顯示，如下所示：

[![資料會保留在橫向模式](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/)之後，會呼叫`OnStart`。 它提供機會來還原先前儲存的套件組合至先前的任何狀態活動`OnSaveInstanceState`。 這是相同的套件組合提供給`OnCreate`，不過。

下列程式碼示範如何在還原狀態`OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

這個方法存在時應該還原狀態，提供一些彈性。 有時候會更適當的等候，直到還原執行個體狀態之前完成所有初始設定。 此外，若要從執行個體的狀態還原某些值可能只想現有活動的子類別。 在許多情況下，它不需要覆寫`OnRestoreInstanceState`，因為大部分的活動可以還原使用可組合的狀態`OnCreate`。

如需的儲存狀態的使用範例`Bundle`，請參閱[逐步解說-儲存活動狀態](saving-state.md)。


#### <a name="bundle-limitations"></a>套件組合的限制

雖然`OnSaveInstanceState`可讓您輕鬆儲存暫時性資料，它會有一些限制：

-   它不會在所有情況下呼叫。 例如，按下**首頁**或**回**結束活動不會導致`OnSaveInstanceState`所呼叫。

-   套件組合傳遞至`OnSaveInstanceState`不是針對大型物件，例如影像。 在大型物件，儲存從物件的情況下[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/)都適合，如下所述。

-   透過使用組合來儲存的資料會序列化，這會導致延遲。

套件組合狀態是適用於簡單的資料，不會使用太多記憶體，而*非組態執行個體資料*是適用於更複雜的資料或會耗費大量資源擷取資料，例如從 web 服務呼叫也可能很複雜資料庫查詢。 視需要取得會在物件中儲存非組態執行個體資料。 下一節介紹`OnRetainNonConfigurationInstance`這種保留更複雜的資料類型，透過組態變更。


### <a name="persisting-complex-data"></a>保存的複雜資料

組合中的保存資料，除了 Android 也支援將資料儲存藉由覆寫[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) ，並將傳回的執行個體`Java.Lang.Object`其中包含要保存資料。 有兩個主要的優點之一使用`OnRetainNonConfigurationInstance`儲存狀態：

-   從傳回的物件`OnRetainNonConfigurationInstance`效能與更大型且更複雜的資料類型，因為記憶體會保留此物件。

-   `OnRetainNonConfigurationInstance`方法是視情況下，被呼叫，而且只需要時。 這是更具經濟效益，比使用手動快取。

使用`OnRetainNonConfigurationInstance`適用於很難擷取的資料多次的案例，例如 web 服務呼叫。 例如，請考慮下列程式碼，以搜尋 Twitter:

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

此程式碼擷取結果從 web 格式化為 JSON、 剖析，並顯示結果，然後在清單中，如下列螢幕擷取畫面所示：

[![在螢幕上顯示的結果](images/06-sml.png)](images/06.png#lightbox)

組態發生變更時-例如，當裝置旋轉-程式碼會重複此程序。 若要重複使用原始擷取的結果並不會造成不必要、 備援性高的網路呼叫，我們可以使用`OnRetainNonconfigurationInstance`來儲存結果，如下所示：

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

現在當旋轉裝置時，會從擷取的原始結果`LastNonConfiguartionInstance`屬性。 在此範例中，結果包括`string[]`包含推文。 由於`OnRetainNonConfigurationInstance`要求`Java.Lang.Object`會傳回`string[]`會包裝在類別子類別化`Java.Lang.Object`，如下所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，嘗試使用`TextView`傳回的物件為`OnRetainNonConfigurationInstance`會流失的活動，如下列程式碼所示：

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

在本節中，我們已了解如何使用簡單的狀態資料保留`Bundle`，並將保存更複雜的資料類型與`OnRetainNonConfigurationInstance`。

## <a name="summary"></a>總結

Android 活動開發週期提供強大的架構來進行狀態管理的應用程式內的活動，但可能很難了解和實作。 這一章導入了不同的活動可能會經歷其存留期，以及與這些狀態相關聯的生命週期方法的狀態。 接下來，對於應執行何種邏輯，在每一種方法提供指引。


## <a name="related-links"></a>相關連結

- [處理旋轉](~/android/app-fundamentals/handling-rotation.md)
- [Android 活動](https://developer.xamarin.com/api/type/Android.App.Activity/)
