---
title: ''
description: 本文說明如何轉換現有的 Xamarin.Forms Android 應用程式，以使用 AppCompat 和材質設計。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24206f6d6764c73f13a4b06fb44fa746f9d353af
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135703"
---
# <a name="adding-appcompat-and-material-design"></a>新增 AppCompat 和材質設計

_請遵循下列步驟來轉換現有的 Xamarin.Forms Android 應用程式，以使用 AppCompat 和材質設計_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>概觀

這些指示說明如何更新您現有 Xamarin.Forms 的 android 應用程式，以使用 AppCompat 程式庫，並在 Android 版本的應用程式中啟用材質設計 Xamarin.Forms 。

### <a name="1-update-xamarinforms"></a>1. 更新Xamarin.Forms

請確定解決方案使用 Xamarin.Forms 2.0 或更新版本。 更新Xamarin.Forms
  NuGet 封裝至2.0 （如有需要）。

### <a name="2-check-android-version"></a>2. 檢查 Android 版本

確定 Android 專案的目標架構是 Android 6.0 （Marshmallow）。 檢查**Android 專案 > 選項 > 組建 > 一般**設定]，以確保已選取 corrent 架構：

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. 新增主題以支援材質設計

在您的 Android 專案中建立下列三個檔案，並貼上下列內容。 Google 提供一個[樣式指南](https://www.google.com/design/spec/style/color.html#color-color-palette)和一個[色調色板](https://www.materialpalette.com/)產生器，可協助您選擇所指定的替代色彩配置。

**資源/值/色彩 .xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style .xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

**V21**資料夾中必須包含額外的樣式，才能在 Android 棒和更新版本上執行時套用特定屬性。

**Resources/values-v21/style .xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. 更新 Androidmanifest.xml .xml

若要確保使用這個新的主題資訊，請在**androidmanifest.xml**檔案中設定主題，方法是新增 `android:theme="@style/MyTheme"` （保留其餘的 XML）。

**Properties/Androidmanifest.xml .xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. 提供工具列和索引標籤版面配置

在**Resources/layout**目錄中建立**Tabbar axml**和**axml**檔案，並貼上其內容，如下所示：

**Resources/layout/Tabbar. axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

已設定索引標籤的幾個屬性，包括索引標籤對 `fill` 和模式的重心 `fixed` 。
如果您有很多索引標籤，您可能會想要將其切換為可滾動-閱讀透過 Android [TabLayout 檔](https://developer.android.com/reference/android/support/design/widget/TabLayout.html)深入瞭解。

**資源/版面配置/工具列。 axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

在這些檔案中，我們會針對您的應用程式建立不同的工具列主題。
若要深入瞭解，請參閱[Hello 工具列](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/)的 blog 文章。

### <a name="6-update-the-mainactivity"></a>6. 更新`MainActivity`

在現有 Xamarin.Forms 的應用程式中， **MainActivity.cs**類別會繼承自 `FormsApplicationActivity` 。 這必須取代成 `FormsAppCompatActivity` 以啟用新功能。

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

最後，從方法中的步驟5「連線到」新的版面配置 `OnCreate` ，如下所示：

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
