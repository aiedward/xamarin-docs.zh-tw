---
title: 處理旋轉
description: 本主題描述如何處理在 Xamarin.Android 裝置方向變更。 其中涵蓋如何搭配使用 Android 資源系統如何以程式設計方式處理方向變更自動載入資源的特定裝置方向。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1cdc7928c45b99cdd8c8149b3ae9b06e790deeca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="handling-rotation"></a>處理旋轉

_本主題描述如何處理在 Xamarin.Android 裝置方向變更。其中涵蓋如何搭配使用 Android 資源系統如何以程式設計方式處理方向變更自動載入資源的特定裝置方向。_


## <a name="overview"></a>總覽

因為行動裝置會輕鬆地循環，內建旋轉是行動作業系統的標準功能。 Android 會提供複雜的架構來處理循環中的應用程式，是否在 XML 中，或以程式設計方式在程式碼中以宣告方式建立的使用者介面。 自動處理時旋轉的裝置上的宣告式的版面配置變更，應用程式可以發揮與 Android 資源系統緊密整合。 以程式設計方式配置，必須以手動方式處理變更。 這可讓較細微的控制在執行階段，但代價更多工作，開發人員。 應用程式也可以選擇退出活動重新啟動，並採取手動控制的方向變更。

本指南會檢查下列方向主題：

-   **宣告式配置旋轉**&ndash;如何建置方向感知應用程式，包括如何載入配置以及特定方向 drawables 使用 Android 資源系統。

-   **以程式設計方式配置旋轉**&ndash;如何以程式設計方式加入控制項，以及如何手動處理方向的變更。


## <a name="handling-rotation-declaratively-with-layouts"></a>以宣告方式使用的版面配置處理旋轉

只要遵循命名慣例的資料夾中包含的檔案，Android 自動載入適當的檔案變更方向時。
這包括支援：

-   *配置資源*&ndash;指定哪些配置檔案會擴大為每一個方向。

-   *Drawable 資源*&ndash;指定哪些 drawables 已載入每個方向。


### <a name="layout-resources"></a>配置資源

根據預設，Android XML (AXML) 檔案包含在**資源/配置**資料夾活動時，可用來呈現檢視。 這個資料夾的資源用於直向或橫向如果沒有其他的版面配置資源會提供專為橫向。 預設專案範本所建立的專案結構，請考慮：

[![預設專案範本的結構](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

此專案會建立單一**Main.axml**檔案**資源/配置**資料夾。 當活動的`OnCreate`方法呼叫，它會擴大中定義的檢視**Main.axml，**其中宣告按鈕，如下列 XML 所示：

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

如果裝置旋轉為 橫向，活動的`OnCreate`再次呼叫方法和相同**Main.axml**膨脹檔案，如以下螢幕擷取畫面所示：

[![相同但在螢幕橫向](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>特定方向配置

除了配置資料夾 (預設值為 [縱向] 以及也可以明確地命名*配置連接埠*包含名為的資料夾`layout-land`)，應用程式可以定義在沒有任何程式碼橫印時需要的檢視變更。

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

如果資料夾的名稱包含額外的版面配置土地**Main.axml**檔案會加入至專案，因而誇大在橫向中的配置現在會在載入新加入的 Android **Main.axml。** 請考慮橫向版本**Main.axml**檔案，其中包含下列程式碼 (為了簡單起見，此 XML 類似於預設縱向版本的程式碼，但使用不同的字串中`TextView`):

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

執行這個程式碼和旋轉直向變成橫向從裝置示範新的 XML 載入，如下所示：

[![縱向或橫向螢幕擷取畫面列印直向模式](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Drawable 資源

在旋轉期間 Android 會將 drawable 資源同樣地配置資源。 在此情況下，系統會取得從 drawables**資源/drawable**和**資源/drawable 土地**資料夾，分別。

例如，專案包含名為 Monkey.png 中的映像**drawable 資源/**資料夾，drawable 參考是從`ImageView`xml 如下：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

讓我們進一步假設不同版本的**Monkey.png**位於**資源/drawable 土地**。 就像配置檔案時，裝置時旋轉 drawable 變更為指定的方向，如下所示：

[![不同版本的 Monkey.png 縱向或橫向模式中顯示](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>以程式設計方式處理旋轉

有時候我們會在程式碼中定義配置。 這可能會發生各種原因造成，包括技術限制、 開發人員偏好設定等。當我們以程式設計方式加入控制項時，應用程式必須以手動方式負責裝置方向，我們會使用 XML 資源時，會自動處理。


### <a name="adding-controls-in-code"></a>在程式碼中加入控制項

若要以程式設計方式加入控制項，應用程式需要執行下列步驟：

-  建立配置。
-  設定版面配置參數。
-  建立控制項。
-  設定控制項的版面配置參數。
-  將控制項加入版面配置。
-  將版面配置設定為內容的檢視。

例如，請考慮使用者介面組成的單一`TextView`控制項加入至`RelativeLayout`，如下列程式碼所示。

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

此程式碼建立的執行個體`RelativeLayout`類別並設定其`LayoutParameters`屬性。 `LayoutParams`類別是 Android 的方式，將封裝如何將控制項放置在可重複使用的方式。 一旦建立版面配置的執行個體時，可以建立並新增到該控制項。 控制項也有`LayoutParameters`，例如`TextView`在此範例中。 之後`TextView`建立時，將它加入至`RelativeLayout`和設定`RelativeLayout`當做內容檢視結果，在應用程式顯示`TextView`所示：

[![遞增計數器按鈕顯示在縱向或橫向模式](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>在程式碼中偵測到方向

如果應用程式嘗試載入不同的使用者介面的每個方向時`OnCreate`稱為 （會發生這種每次裝置旋轉），它必須偵測的方向，並再載入所需的使用者介面程式碼。 Android 提供一種類別稱為`WindowManager`，可用來判斷目前的裝置旋轉，透過`WindowManager.DefaultDisplay.Rotation`屬性，如下所示：

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

這個程式碼設定`TextView`要定位的 100 像素螢幕左下角，會自動設為新的版面配置，動畫時旋轉橫向，如下所示：

[![保留跨縱向或橫向模式檢視狀態](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>防止活動重新啟動

除了處理中的所有`OnCreate`，應用程式也可以防止活動正在重新啟動時變更設定的方向`ConfigurationChanges`中`ActivityAttribute`，如下所示：

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

這裡`TextView's`初始化橫向和縱向的版面配置參數。 類別變數會保留參數，連同`TextView`本身，因為該活動不會重新建立方向變更時。 程式碼仍會使用`surfaceOrientartion`中`OnCreate`設定的初始配置`TextView`。 在這之後，`OnConfigurationChanged`會處理所有後續的版面配置的變更。

當我們執行應用程式時，Android 裝置旋轉，就會發生，並不會重新啟動活動，就會載入使用者介面的變更。


## <a name="preventing-activity-restart-for-declarative-layouts"></a>防止宣告式配置的活動重新啟動

如果我們在 XML 中定義的配置，也會防止活動造成裝置旋轉的重新啟動。 比方說，我們可以使用這個方法如果我們想要防止活動重新啟動 (基於效能考量，可能是) 而我們不需要載入新的資源，針對不同的方向。

若要這樣做，我們會遵循相同的程序，我們使用程式設計的版面配置。 只要設定`ConfigurationChanges`中`ActivityAttribute`，如同`CodeLayoutActivity`稍早。 需要執行一次可以在實作的方向變更任何程式碼`OnConfigurationChanged`方法。


## <a name="maintaining-state-during-orientation-changes"></a>維護期間方向變更的狀態

以宣告方式或以程式設計方式處理的旋轉方式、 是否所有 Android 應用程式應該實作相同的技術來管理裝置方向變更時的狀態。 管理狀態很重要，因為 Android 裝置旋轉時，在系統重新啟動正在執行的活動。 Android 做可以讓您輕鬆載入替代資源，例如版面配置和 drawables，專為特定的方向。 重新啟動時，活動就會失去任何暫時性狀態，它可能會儲存在本機的類別變數。 因此，如果活動是相依的狀態，它必須保存其狀態在應用程式層級。 應用程式必須處理儲存和還原它想要保留跨方向變更任何應用程式狀態。

如需有關在 Android 保存狀態的詳細資訊，請參閱[活動的生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。


## <a name="summary"></a>總結

這篇文章討論了如何使用 Android 的內建功能來處理旋轉。 首先，它會說明如何使用 Android 資源系統建立方向感知的應用程式。 然後它會顯示如何在程式碼中加入控制項，以及如何手動處理方向的變更。



## <a name="related-links"></a>相關連結

- [旋轉示範 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [處理執行階段變更](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速螢幕方向變更](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
