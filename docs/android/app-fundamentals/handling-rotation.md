---
title: 處理旋轉
description: 本主題說明如何處理 Xamarin. Android 中的裝置方向變更。 其中涵蓋如何使用 Android 資源系統，自動載入特定裝置方向的資源，以及如何以程式設計方式處理方向變更。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3277dd5eb7600500a5f60b2bbb13621aa237a235
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019275"
---
# <a name="handling-rotation"></a>處理旋轉

_本主題說明如何處理 Xamarin. Android 中的裝置方向變更。其中涵蓋如何使用 Android 資源系統，自動載入特定裝置方向的資源，以及如何以程式設計方式處理方向變更。_

## <a name="overview"></a>總覽

因為行動裝置很容易旋轉，所以內建旋轉是行動作業系統中的標準功能。 Android 提供精密的架構來處理應用程式內的旋轉，無論使用者介面是以 XML 宣告方式建立，或是以程式設計方式在程式碼中建立。 當自動處理旋轉裝置上的宣告式版面配置變更時，應用程式可以從與 Android 資源系統緊密整合的方式獲益。 針對程式設計版面配置，必須手動處理變更。 這可讓您在執行時間進行更精細的控制，但代價是開發人員的工作更多。 應用程式也可以退出宣告活動重新開機，並手動控制方向的變更。

本指南會檢查下列方向主題：

- **宣告式版面配置輪替**&ndash; 如何使用 Android 資源系統建立方向感知應用程式，包括如何載入特定方向的版面配置和可繪製資源。

- 程式**設計版面配置旋轉**&ndash; 如何以程式設計方式加入控制項，以及如何手動處理方向變更。

## <a name="handling-rotation-declaratively-with-layouts"></a>以宣告方式使用版面配置來處理旋轉

藉由將檔案包含在遵循命名慣例的資料夾中，Android 會在方向變更時自動載入適當的檔案。
這包括的支援：

- 配置*資源*&ndash; 指定要針對每個方向放大的版面配置檔案。

- 可*繪製資源*&ndash; 指定要針對每個方向載入的可繪製資源。

### <a name="layout-resources"></a>版面配置資源

根據預設，**資源/版面**配置資料夾中所包含的 Android XML （AXML）檔案會用於呈現活動的視圖。 如果沒有專為橫向提供的其他版面配置資源，則會使用此資料夾的資源來進行直向和橫向列印。 請考慮預設專案範本所建立的專案結構：

[![預設專案範本結構](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

此專案會在**Resources/layout**資料夾中建立單一**axml**檔案。 呼叫活動的 `OnCreate` 方法時，它會擴大 axml 中定義的視圖 **，** 這會宣告一個按鈕，如下列 XML 所示：

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

如果裝置已旋轉為橫向方向，則會再次呼叫活動的 `OnCreate` 方法，並擴大相同的**axml**檔案，如下列螢幕擷取畫面所示：

[![相同的畫面，但橫向的方向](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)

#### <a name="orientation-specific-layouts"></a>方向特定的版面配置

除了 [配置] 資料夾（預設為 [縱向]，也可以藉由包含名為 [`layout-land`] 的資料夾，明確命名為 [配置 *-埠*]），應用程式可以在不變更任何程式碼的情況下，定義所需的視圖。

假設**axml**檔案包含下列 XML：

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

如果將包含額外**axml**檔案的資料夾加入至專案，則在橫向時因而誇大配置，現在會導致 Android 載入新增的**Main. axml。** 請考慮包含下列程式碼的**axml**檔案的橫向版本（為了簡單起見，此 XML 類似于預設的程式碼垂直版本，但在 `TextView`中使用不同的字串）：

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

執行此程式碼並將裝置從直向直向橫向，會示範新的 XML 載入，如下所示：

[![直向和橫向螢幕擷取畫面列印直向模式](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)

### <a name="drawable-resources"></a>繪製資源

在旋轉期間，Android 會將可繪製的資源視為類似于配置資源。 在此情況下，系統會分別從**資源/「繪製**」和「**資源/能繪製**」的資料夾取得可繪製資源。

例如，假設專案包含**Resources/可繪製**資料夾中名為猴子的影像，其中可從 XML 中的 `ImageView` 參考繪製，如下所示：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

讓我們進一步假設不同版本的**猴子**都包含在**資源/可繪製-land**之下。 就像設定檔案一樣，當裝置旋轉時，會針對指定的方向進行可繪製的變更，如下所示：

[以直向和橫向模式顯示的![不同的猴子版本。](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)

## <a name="handling-rotation-programmatically"></a>以程式設計方式處理旋轉

有時候我們會在程式碼中定義版面配置。 發生這種情況的原因有很多種，包括技術限制、開發人員喜好設定等。當我們以程式設計方式新增控制項時，應用程式必須手動考慮裝置的方向，這會在我們使用 XML 資源時自動處理。

### <a name="adding-controls-in-code"></a>在程式碼中加入控制項

若要以程式設計方式加入控制項，應用程式需要執行下列步驟：

- 建立版面配置。
- 設定版面配置參數。
- 建立控制項。
- 設定控制項版面配置參數。
- 將控制項加入至版面配置。
- 將版面配置設定為內容視圖。

例如，假設有一個由加入至 `RelativeLayout`的單一 `TextView` 控制群組成的使用者介面，如下列程式碼所示。

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

此程式碼會建立 `RelativeLayout` 類別的實例，並設定它的 `LayoutParameters` 屬性。 `LayoutParams` 類別是以可重複使用的方式來封裝控制項位置的 Android 方式。 建立版面配置的實例之後，就可以建立控制項並將其加入其中。 控制項也有 `LayoutParameters`，例如此範例中的 `TextView`。 建立 `TextView` 之後，將其新增至 `RelativeLayout`，並將 `RelativeLayout` 設定為內容視圖，會導致應用程式顯示 `TextView`，如下所示：

[以直向和橫向模式顯示的![遞增計數器] 按鈕](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)

### <a name="detecting-orientation-in-code"></a>在程式碼中偵測方向

如果應用程式在呼叫 `OnCreate` 時，嘗試為每個方向載入不同的使用者介面（這會在每次裝置旋轉時發生），它必須偵測方向，然後載入所需的使用者介面程式碼。 Android 有一個稱為 `WindowManager`的類別，可用來透過 `WindowManager.DefaultDisplay.Rotation` 屬性來判斷目前的裝置旋轉，如下所示：

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

這段程式碼會將 `TextView` 設定為從畫面左上角到100圖元的位置，當旋轉為橫向時，會自動以動畫顯示新的版面配置，如下所示：

[跨直向和橫向模式會保留![檢視狀態](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)

### <a name="preventing-activity-restart"></a>防止活動重新開機

除了處理 `OnCreate`中的所有專案，應用程式也可以在方向設定 `ActivityAttribute` `ConfigurationChanges` 時，防止重新開機活動，如下所示：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```

現在當裝置旋轉時，不會重新開機活動。 為了在此情況下手動處理方向變更，活動可以覆寫 `OnConfigurationChanged` 方法，並從傳入的 `Configuration` 物件判斷方向，如同下列活動的新執行：

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

這裡的 `TextView's` 版面配置參數會針對橫向和直向進行初始化。 類別變數會連同 `TextView` 本身一樣保存參數，因為當方向變更時，不會重新建立活動。 程式碼仍會使用 `OnCreate` 中的 `surfaceOrientartion` 來設定 `TextView`的初始配置。 之後，`OnConfigurationChanged` 會處理所有後續的版面配置變更。

當我們執行應用程式時，Android 會在裝置輪替發生時載入使用者介面變更，而不會重新開機活動。

## <a name="preventing-activity-restart-for-declarative-layouts"></a>防止活動重新開機以進行宣告式版面配置

如果我們在 XML 中定義版面配置，也可以防止裝置輪替所造成的活動重新開機。 例如，如果我們想要防止活動重新開機（可能是基於效能的原因），我們可以使用這種方法，而我們不需要針對不同的方向載入新的資源。

若要這樣做，我們會遵循與程式設計配置搭配使用的相同程式。 只要在 `ActivityAttribute`中設定 `ConfigurationChanges`，就像我們稍早在 `CodeLayoutActivity` 中所做的一樣。 任何需要針對方向變更執行的程式碼，都可以再次在 `OnConfigurationChanged` 方法中執行。

## <a name="maintaining-state-during-orientation-changes"></a>在方向變更時維護狀態

無論是以宣告方式或程式設計方式處理旋轉，所有 Android 應用程式都應該在裝置方向變更時，執行用來管理狀態的相同技術。 管理狀態很重要，因為當 Android 裝置旋轉時，系統會重新開機正在執行的活動。 Android 會執行此工作，讓您輕鬆地載入替代的資源，例如專為特定方向設計的版面配置和可繪製資源。 當它重新開機時，活動會失去可能儲存在本機類別變數中的任何暫時性狀態。 因此，如果活動的狀態是「相依」，它就必須在應用層級保存其狀態。 應用程式必須處理儲存和還原任何想要保留的應用程式狀態，以跨越方向變更。

如需有關在 Android 中保存狀態的詳細資訊，請參閱[活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。

## <a name="summary"></a>總結

本文涵蓋如何使用 Android 的內建功能來處理旋轉。 首先，它會說明如何使用 Android 資源系統來建立面向感知應用程式。 接著，它會示範如何在程式碼中加入控制項，以及如何手動處理方向變更。

## <a name="related-links"></a>相關連結

- [旋轉示範（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [處理執行時間變更](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速畫面方向變更](https://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
