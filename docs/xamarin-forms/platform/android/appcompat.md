---
title: "加入 AppCompat 和材料設計"
description: "請遵循下列步驟，將現有的 Xamarin.Forms Android 應用程式以使用 AppCompat 和材料設計"
ms.topic: article
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: 3014db91ff87f0e73291595a17dd780b5e3cd3c2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="adding-appcompat-and-material-design"></a>加入 AppCompat 和材料設計

_請遵循下列步驟，將現有的 Xamarin.Forms Android 應用程式以使用 AppCompat 和材料設計_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>總覽

下列指示說明如何更新現有的 Xamarin.Forms Android 應用程式使用 AppCompat 程式庫和 Android Xamarin.Forms 應用程式的版本中啟用材料設計。

### <a name="1-update-xamarinforms"></a>1.Update Xamarin.Forms

確保解決方案使用 Xamarin.Forms 2.0 或更新版本。 視需要更新的 Xamarin.Forms Nuget 套件 2.0。

### <a name="2-check-android-version"></a>2.檢查 Android 版本

請確定 Android 專案的目標 framework 是 Android 6.0 (Marshmallow)。 請檢查**Android 專案 > 選項 > 建置 > 一般**選取設定，以確保修改架構：

 ![](appcompat-images/target-android-6-sml.png "建置 android 一般組態")

### <a name="3-add-new-themes-to-support-material-design"></a>3.加入新的佈景主題支援材料設計

在您的 Android 專案中建立下列三個檔案，並貼上下列內容。 Google 提供[樣式指南](http://www.google.com/design/spec/style/color.html#color-color-palette)和[色彩調色盤產生器](http://www.materialpalette.com/)可協助您選擇要指定替代的色彩配置。

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

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

其他樣式必須包含在**值 v21**套用特定的屬性，當執行 Android 的棒棒糖符號和較新的資料夾。

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4.Update AndroidManifest.xml

若要確保這個新佈景主題的資訊是使用的設定中的主題**AndroidManifest**檔案加`android:theme="@style/MyTheme"`（因為它已保留其餘的 XML）。

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5.提供工具列和索引標籤的版面配置

建立**Tabbar.axml**和**Toolbar.axml**檔案**資源/配置**目錄和其內容從底下中的貼上：

**Resources/layout/Tabbar.axml**

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

已設定一些屬性索引標籤包括以索引標籤的重力`fill`和模式來`fixed`。
如果您有許多索引標籤可能會想要切換這以可捲動的閱讀 Android [TabLayout 文件](http://developer.android.com/reference/android/support/design/widget/TabLayout.html)如需詳細資訊。

**Resources/layout/Toolbar.axml**

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

我們在這些檔案中建立特定的佈景主題為您的應用程式而異的工具列。
請參閱[Hello 工具列](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/)部落格文章，如需詳細資訊。


### <a name="6-update-the-mainactivity"></a>6.更新 `MainActivity`

在現有的 Xamarin.Forms 應用程式中**Weatherapp**類別將繼承自`FormsApplicationActivity`。 這必須取代`FormsAppCompatActivity`啟用新功能。

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

最後，「 連接 」 中的步驟 5 中的新配置`OnCreate`方法，如下所示：

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
