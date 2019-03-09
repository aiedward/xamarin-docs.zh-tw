---
title: 處理旋轉
description: 本主題描述如何處理在 Xamarin.Android 中的裝置方向變更。 它涵蓋了解如何使用 Android 資源系統會自動載入資源的特定裝置方向，以及如何以程式設計方式處理方向變更。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 62e64be89e26e5a8412cd34221da581e99fc5e6a
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668215"
---
# <a name="handling-rotation"></a>處理旋轉

_本主題描述如何處理在 Xamarin.Android 中的裝置方向變更。它涵蓋了解如何使用 Android 資源系統會自動載入資源的特定裝置方向，以及如何以程式設計方式處理方向變更。_


## <a name="overview"></a>總覽

因為行動裝置會輕易地旋轉，內建的輪替會是行動作業系統中的標準功能。 Android 會提供複雜的架構來處理循環內的應用程式，是否在 XML 中，或以程式設計方式在程式碼中以宣告方式建立的使用者介面。 自動處理時旋轉的裝置上的宣告式版面配置變更，應用程式可以受益於與 Android 資源系統的緊密整合。 以程式設計方式配置，必須以手動方式處理變更。 這可讓細微的控制在執行階段，但代價是更多工作的開發人員。 應用程式也可以選擇退出活動重新啟動，並手動掌控方向的變更。

本指南會檢查下列方向主題：

-   **宣告式版面配置旋轉**&ndash;如何使用 Android 資源系統建置方向感知應用程式，包括如何載入版面配置和可繪製資源來針對特定的方向。

-   **以程式設計方式配置旋轉**&ndash;如何以程式設計方式加入控制項，以及如何以手動方式處理方向的變更。


## <a name="handling-rotation-declaratively-with-layouts"></a>使用版面配置以宣告方式處理旋轉

藉由遵循命名慣例的資料夾中包含的檔案，Android 會自動載入適當的檔案方向變更時。
這包括的支援：

-   *配置資源*&ndash;指定哪一個版面配置檔會擴大為每一個方向。

-   *可繪製資源*&ndash;指定哪些可繪製資源來載入的每個方向。


### <a name="layout-resources"></a>版面配置的資源

根據預設，Android XML (AXML) 檔案包含在**資源/配置**資料夾用於呈現檢視的活動。 此資料夾的資源用於直向或橫向如果沒有額外的版面配置的資源提供專為橫向。 預設專案範本所建立的專案結構，請考慮：

[![預設專案範本的結構](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

此專案會建立單一**Main.axml**中的檔案**資源/配置**資料夾。 當活動的`OnCreate`呼叫方法，它擴大中定義的檢視**Main.axml，** 其中宣告按鈕，如下列 XML 所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

如果裝置旋轉為橫向，活動的`OnCreate`再次呼叫方法和相同**Main.axml**擴大檔案，如以下螢幕擷取畫面所示：

[![相同但在畫面橫式方向](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>方向特定版面配置

除了配置資料夾 (其中預設為直向，而且也可以明確地命名*版面配置連接埠*包含名為的資料夾`layout-land`)，應用程式可以定義它所需時不需要任何程式碼的橫向檢視變更。

假設**Main.axml**檔案包含下列 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

如果資料夾的名稱包含額外的版面配置 land **Main.axml**檔案新增至專案，並以 橫向配置現在會載入新加入的 Android **Main.axml。** 請考慮橫向版本**Main.axml**檔案，其中包含下列程式碼 (為了簡單起見，此 XML 類似於直向的預設版本的程式碼，但使用不同的字串中`TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

執行此程式碼和旋轉的裝置，從直向變成橫向示範新的 XML 載入，如下所示：

[![直向和橫向螢幕擷取畫面列印直向模式](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>可繪製資源

在旋轉，Android 會將可繪製資源同樣地配置資源。 在此情況下，系統會取得從可繪製資源來**資源/drawable**並**資源/drawable land**資料夾分別。

比方說，假設專案會包含名為 Monkey.png 中的映像**資源/drawable**資料夾中，drawable 參考是從`ImageView`xml 如下：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

讓我們進一步假設，不同版本的**Monkey.png**包含之下**資源/drawable land**。 就像版面配置檔中，裝置時的旋轉，可繪製變更為指定的方向，如下所示：

[![不同版本的 Monkey.png 直向和橫向模式中顯示](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>以程式設計方式處理旋轉

有時我們會在程式碼中定義版面配置。 這種情形的各種不同的原因，包括技術上的限制、 開發人員喜好設定等。當我們以程式設計方式新增控制項時，應用程式必須以手動方式負責裝置方向，將會自動處理當我們使用的 XML 資源。


### <a name="adding-controls-in-code"></a>在程式碼中加入控制項

若要以程式設計方式加入控制項，應用程式需要執行下列步驟：

-  建立版面配置。
-  設定版面配置參數。
-  建立控制項。
-  設定控制項的版面配置參數。
-  將控制項加入版面配置。
-  將版面配置設定為 內容檢視。

例如，假設使用者介面，其中包含單一`TextView`加入至控制項`RelativeLayout`，如下列程式碼所示。

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此程式碼建立的執行個體`RelativeLayout`類別，並設定其`LayoutParameters`屬性。 `LayoutParams`類別是 Android 的方式封裝如何將控制項放置在可重複使用的方式。 版面配置的執行個體建立之後，就可以建立並加入控制項。 控制項也都有`LayoutParameters`，例如`TextView`在此範例中。 之後`TextView`建立時，將它加入至`RelativeLayout`並設定`RelativeLayout`做為應用程式顯示內容檢視結果`TextView`所示：

[![直向和橫向模式中顯示的遞增計數器按鈕](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>在程式碼偵測方向

如果應用程式會嘗試載入不同的使用者介面，讓每個方向時`OnCreate`呼叫 （這會發生在旋轉裝置每次），它必須偵測方向，，，然後載入所需的使用者介面程式碼。 Android 提供類別，稱為`WindowManager`，這可以用來判斷目前的裝置旋轉，透過`WindowManager.DefaultDisplay.Rotation`屬性，如下所示：

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此程式碼會設定`TextView`是定位的 100 像素為單位，從頂端螢幕左下角，會自動以動畫顯示新的版面配置，當旋轉為橫向，如下所示：

[![檢視狀態會保留在直向和橫向模式](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>防止活動重新啟動

除了處理中的所有項目`OnCreate`，應用程式也可能導致活動無法在重新啟動時藉由設定變更方向`ConfigurationChanges`在`ActivityAttribute`，如下所示：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
現在當旋轉裝置時，該活動不會重新啟動。 若要手動處理方向變更在此情況下，活動可以覆寫`OnConfigurationChanged`方法，並判斷方向，從`Configuration`傳入，如同下列活動的新實作的物件：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

這裡`TextView's`針對橫向和直向的初始化配置參數。 類別變數會保留參數，連同`TextView`本身，因為活動將不會重新建立方向變更時。 程式碼仍會使用`surfaceOrientartion`中`OnCreate`若要設定的初始配置`TextView`。 在那之後，`OnConfigurationChanged`會處理所有後續的版面配置的變更。

當我們執行應用程式時，Android 裝置旋轉，就會發生，並不會重新啟動的活動，就會載入使用者介面的變更。


## <a name="preventing-activity-restart-for-declarative-layouts"></a>防止活動為宣告式配置的重新啟動

如果我們在 XML 中定義的版面配置，也可防止因裝置旋轉的活動重新啟動。 比方說，我們可以使用這個方法如果我們想要防止活動重新啟動 (基於效能考量，可能是) 和我們不必載入新的資源，針對不同的方向。

若要這樣做，我們會遵循相同的程序，我們會使用程式設計的版面配置。 只要設定`ConfigurationChanges`中`ActivityAttribute`，如同我們在`CodeLayoutActivity`稍早。 需要執行一次可以在實作方向變更任何程式碼`OnConfigurationChanged`方法。


## <a name="maintaining-state-during-orientation-changes"></a>在方向變更時維護狀態

處理旋轉，以宣告方式或以程式設計方式、 是否所有的 Android 應用程式應該實作相同的技術來管理裝置方向變更時的狀態。 管理狀態很重要，因為系統在 Android 裝置旋轉時，會重新啟動正在執行的活動。 Android 這麼做是以輕鬆地將替代資源，例如版面配置和專為特定的方向的可繪製資源來載入。 當重新啟動時，活動就會失去它可能會有儲存在本機的類別變數中的任何暫時性狀態。 因此，如果活動是相依的狀態，它必須保存其狀態，應用程式層級。 應用程式需要處理儲存和還原它想要保留在方向變更任何應用程式狀態。

如需有關在 Android 中的保存狀態的詳細資訊，請參閱[活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。


## <a name="summary"></a>總結

本文涵蓋如何使用 Android 的內建功能，來處理循環。 首先，它會說明如何使用 Android 資源系統建立方向感知應用程式。 然後它會顯示如何在程式碼中加入控制項，以及如何以手動方式處理方向的變更。



## <a name="related-links"></a>相關連結

- [旋轉示範 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [處理執行階段變更](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速的螢幕方向變更](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
