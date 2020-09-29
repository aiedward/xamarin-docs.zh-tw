---
title: 處理旋轉
description: 本主題說明如何在 Xamarin. Android 中處理裝置方向變更。 它涵蓋如何使用 Android 資源系統來自動載入特定裝置方向的資源，以及如何以程式設計方式處理方向變更。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 944452160ed023f47fc2c648f092032c245350fb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455663"
---
# <a name="handling-rotation"></a>處理旋轉

_本主題說明如何在 Xamarin. Android 中處理裝置方向變更。它涵蓋如何使用 Android 資源系統來自動載入特定裝置方向的資源，以及如何以程式設計方式處理方向變更。_

## <a name="overview"></a>概觀

因為行動裝置可輕鬆旋轉，所以內建的旋轉是行動作業系統的標準功能。 Android 提供精密的架構來處理應用程式內的旋轉、是否以 XML 宣告方式建立使用者介面，或在程式碼中以程式設計方式建立使用者介面。 自動處理輪替裝置上的宣告式版面配置變更時，應用程式可以與 Android 資源系統緊密整合的優點。 針對程式設計的配置，必須手動處理變更。 這可讓您在執行時間進行更細微的控制，但代價是開發人員的工作更多。 應用程式也可以退出宣告活動重新開機，並手動控制方向變更。

本指南將探討下列方向主題：

- **宣告式版面配置旋轉** &ndash; 如何使用 Android 資源系統建立面向感知的應用程式，包括如何載入特定方向的版面配置和可繪製資源。

- 程式**設計的版面配置旋轉** &ndash;如何以程式設計方式新增控制項，以及如何手動處理方向變更。

## <a name="handling-rotation-declaratively-with-layouts"></a>使用版面配置以宣告方式處理旋轉

藉由在遵循命名慣例的資料夾中包含檔案，當方向變更時，Android 會自動載入適當的檔案。
這包括下列項目的支援：

- *版面配置資源* &ndash; 指定針對每個方向放大的版面配置檔案。

- *繪製資源* &ndash; 指定針對每個方向載入的可繪製資源。

### <a name="layout-resources"></a>版面配置資源

依預設，[ **資源/** 配置] 資料夾中包含的 Android XML (.axml) 檔案，用於轉譯活動的視圖。 如果未特別針對橫向提供任何額外的版面配置資源，則此資料夾的資源會用於直向和橫向方向。 請考慮預設專案範本所建立的專案結構：

[![預設專案範本結構](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

這個專案會在**Resources/layout**資料夾中建立單一的 **.axml**檔案。 當呼叫活動的 `OnCreate` 方法時，它會擴大在 **.axml** 中定義的視圖，這會宣告一個按鈕，如下列 XML 所示：

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

如果裝置旋轉為橫向，則 `OnCreate` 會再次呼叫活動的方法，並將相同的 **主要 .axml** 檔案放大，如下列螢幕擷取畫面所示：

[![相同畫面，但以橫向顯示](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)

#### <a name="orientation-specific-layouts"></a>方向特定版面配置

除了預設為直向的版面配置資料夾 (，也可以包含名為 [) ] 的資料夾，以明確命名的配置 *埠* `layout-land` ，讓應用程式可以在不需要變更任何程式碼的情況下，定義其所需的觀點。

假設 **.axml** 檔案包含下列 XML：

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

如果將包含額外 **.axml** 檔案的名稱為 layout 的資料夾加入專案中，則因而誇大版面配置時，現在會導致 Android 載入新加入的 **.axml。** 針對簡單起見，請考慮包含下列程式碼 (**.axml** 檔案的橫向版本，此 XML 類似于預設的直向版本的程式碼，但在) 中使用不同的字串 `TextView` ：

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

執行此程式碼，並將裝置從直向垂直旋轉為橫向，會示範新的 XML 載入，如下所示：

[![直向和橫向列印縱向模式的螢幕擷取畫面](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)

### <a name="drawable-resources"></a>繪製資源

在輪替期間，Android 會將可繪製的資源視為版面配置資源。 在此情況下，系統會分別從 **resources/繪製** 和 **資源/繪製-land** 資料夾取得可繪製資源。

例如，假設專案包含 [ **資源/可繪製** ] 資料夾中名為 Monkey.png 的影像，其中可繪製的可繪製 `ImageView` 方式如下所示：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

讓我們進一步假設**資源/可繪製-land**下包含不同版本的**Monkey.png** 。 就像設定檔案一樣，當裝置旋轉時，會對指定的方向進行可繪製的變更，如下所示：

[![以直向和橫向模式顯示的不同 Monkey.png 版本](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)

## <a name="handling-rotation-programmatically"></a>以程式設計方式處理旋轉

有時我們會在程式碼中定義版面配置。 這可能會因各種原因而發生，包括技術限制、開發人員喜好設定等。當我們以程式設計的方式新增控制項時，應用程式必須以手動方式來處理裝置方向，而當我們使用 XML 資源時，會自動處理該裝置

### <a name="adding-controls-in-code"></a>在程式碼中加入控制項

若要以程式設計方式新增控制項，應用程式必須執行下列步驟：

- 建立版面配置。
- 設定版面配置參數。
- 建立控制項。
- 設定控制項版面配置參數。
- 將控制項加入至版面配置。
- 將版面配置設定為內容視圖。

例如，假設有一個由 `TextView` 新增至的單一控制項所組成的使用者介面 `RelativeLayout` ，如下列程式碼所示。

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

此程式碼會建立類別的實例 `RelativeLayout` ，並設定其 `LayoutParameters` 屬性。 `LayoutParams`類別是以可重複使用的方式來封裝控制項定位方式的 Android 方法。 一旦建立版面配置的實例之後，就可以建立控制項並將其加入至其中。 控制項也有 `LayoutParameters` ，例如 `TextView` 此範例中的。 在 `TextView` 建立之後，將它加入至， `RelativeLayout` 並將設定 `RelativeLayout` 為內容視圖會導致應用程式顯示， `TextView` 如下所示：

[![直向和橫向模式顯示的遞增計數器按鈕](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)

### <a name="detecting-orientation-in-code"></a>在程式碼中偵測方向

如果在呼叫時，應用程式嘗試為每個方向載入不同的使用者介面 `OnCreate` (這會在每次裝置旋轉) 時發生，它必須偵測方向，然後載入所需的使用者介面程式碼。 Android 有一個稱為的類別 `WindowManager` ，可用來透過屬性來判斷目前的裝置旋轉 `WindowManager.DefaultDisplay.Rotation` ，如下所示：

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

這段程式碼會將設定 `TextView` 為從畫面左上方放置100圖元，並在旋轉為橫向時自動以動畫方式呈現新的版面配置，如下所示：

[![跨直向和橫向模式會保留檢視狀態](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)

### <a name="preventing-activity-restart"></a>防止活動重新開機

除了處理中的所有專案之外 `OnCreate` ，應用程式也可以在中設定的方向變更時，防止活動重新開機，如下所示 `ConfigurationChanges` `ActivityAttribute` ：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```

現在裝置旋轉時，不會重新開機活動。 若要在此情況下手動處理方向變更，活動可以覆寫方法， `OnConfigurationChanged` 並從傳入的物件判斷方向 `Configuration` ，如同下列活動的新執行：

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

在這裡， `TextView's` 版面配置參數會針對橫向和縱向進行初始化。 類別變數會保存參數及其 `TextView` 本身，因為在方向變更時，不會重新建立活動。 程式碼仍會使用 `surfaceOrientartion` 中的 `OnCreate` 來設定的初始配置 `TextView` 。 之後，會 `OnConfigurationChanged` 處理所有後續的版面配置變更。

當我們執行應用程式時，Android 會在裝置輪替時載入使用者介面變更，而不會重新開機活動。

## <a name="preventing-activity-restart-for-declarative-layouts"></a>防止活動重新開機宣告式版面配置

如果在 XML 中定義版面配置，也可以防止裝置輪替所造成的活動重新開機。 例如，如果我們想要防止活動重新開機 (基於效能考慮，我們可以使用這個方法（也許) ，而且我們不需要為不同的方向載入新的資源）。

若要這樣做，我們會遵循與程式設計配置搭配使用的相同程式。 只要 `ConfigurationChanges` 在中設定 `ActivityAttribute` ，就像我們在先前所做的一樣 `CodeLayoutActivity` 。 任何需要針對方向變更執行的程式碼，都可以再次在方法中執行 `OnConfigurationChanged` 。

## <a name="maintaining-state-during-orientation-changes"></a>在方向變更期間維護狀態

無論是以宣告方式或以程式設計方式處理輪替，所有 Android 應用程式都應該在裝置方向變更時，執行相同的技術來管理狀態。 管理狀態很重要，因為當 Android 裝置旋轉時，系統會重新開機正在執行的活動。 Android 可讓您輕鬆地載入替代資源，例如專為特定方向設計的版面配置和可繪製資源。 當它重新開機時，活動會失去任何可能儲存在區域類別變數中的暫時性狀態。 因此，如果活動是狀態相依的，則必須在應用層級保存其狀態。 應用程式需要處理所要保留的任何應用程式狀態，以進行跨方向變更。

如需在 Android 中保存狀態的詳細資訊，請參閱《 [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md) 指南》。

## <a name="summary"></a>摘要

本文涵蓋如何使用 Android 的內建功能來處理旋轉。 首先，它會說明如何使用 Android 資源系統建立面向感知的應用程式。 接著，它會介紹如何在程式碼中加入控制項，以及如何以手動方式處理方向變更。

## <a name="related-links"></a>相關連結

- [旋轉示範 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [處理執行時間變更](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速畫面方向變更](https://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)