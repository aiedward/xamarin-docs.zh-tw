---
title: 材質佈景主題
description: 如何使用材質主題來為您的 Xamarin Android 應用程式提供主題
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 027460196f97173fcf977971b79def722ab672d9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647871"
---
# <a name="material-theme"></a>材質佈景主題

*材質主題*是一種使用者介面樣式, 可決定從 Android 5.0 (棒糖) 開始的視圖和活動外觀與風格。 材質主題內建于 Android 5.0 中, 因此系統 UI 以及應用程式會使用它。 「材質主題」並不是使用者可以從 [設定] 功能表動態選擇之全系統外觀選項的「主題」。 相反地, 您可以將材質主題視為一組相關的內建基底樣式, 您可以用來自訂應用程式的外觀與風格。

Android 提供三種材質主題:

-  `Theme.Material`&ndash;材質主題的深色版本; 這是 Android 5.0 中的預設類別。

-  `Theme.Material.Light`&ndash;材質主題的光線版本。

-  `Theme.Material.Light.DarkActionBar`&ndash;材質主題的光線版本, 但具有深色動作列。

這些材質主題類別的範例如下所示:

[![深色主題、淺色主題和深色動作列主題的範例螢幕擷取畫面](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

您可以從材質主題衍生, 以建立您自己的主題, 並覆寫部分或全部的色彩屬性。 例如, 您可以建立衍生自`Theme.Material.Light`的主題, 但會覆寫應用程式行色彩以符合您品牌的色彩。 您也可以將個別的視圖樣式:例如, 您可以為具有更多圓角的[CardView](~/android/user-interface/controls/card-view.md)建立樣式, 並使用較暗的背景色彩。

您可以針對整個應用程式使用單一主題, 也可以在應用程式中針對不同的畫面 (活動) 使用不同的主題。 例如, 在上面的螢幕擷取畫面中, 單一應用程式會針對每個活動使用不同的主題, 以示範內建的色彩配置。 選項按鈕會將應用程式切換至不同的活動, 因此會顯示不同的主題。

因為只有 Android 5.0 和更新版本才支援材質主題, 所以您無法使用它 (或從材質主題衍生的自訂主題) 來建立應用程式的主題, 以便在舊版 Android 上執行。 不過, 您可以設定應用程式在 Android 5.0 裝置上使用材質主題, 並在舊版 Android 上執行時, 正常地切換回先前的主題 (如需詳細資訊, 請參閱本文的「[相容性](#compatibility)」一節)。


## <a name="requirements"></a>需求

若要在 Xamarin 應用程式中使用新的 Android 5.0 材質主題功能, 需要下列各項:

-  您必須安裝並設定 Visual Studio 或 Visual Studio for Mac 的**xamarin android** &ndash; xamarin. android 4.20 或更新版本。 

-  **Android SDK**&ndash; Android 5.0 (API 21) 或更新版本必須透過 Android SDK Manager 安裝。

-  **JAVA JDK 1.8**&ndash;如果您特別瞄準 API 層級23和更早版本, 則可以使用 JDK 1.7。 JDK 1.8 可從[Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)取得。

若要瞭解如何設定 Android 5.0 應用程式專案, 請參閱[設定 android 5.0 專案](~/android/platform/lollipop.md)。


## <a name="using-the-built-in-themes"></a>使用內建主題

使用材質主題的最簡單方式是將您的應用程式設定為使用內建主題, 而不需要自訂。 如果您不想要明確設定主題, 您的應用程式會預設`Theme.Material`為 (深色主題)。 如果您的應用程式只有一個活動, 您可以在活動層級設定主題。 如果您的應用程式有多個活動, 您可以在應用層級設定主題, 讓它在所有活動上使用相同的主題, 或者您也可以將不同的主題指派給不同的活動。 下列各節說明如何在應用層級和活動層級設定主題。


### <a name="theming-an-application"></a>主題應用程式

若要將整個應用程式設定為使用材質主題類別, 請`android:theme`將**androidmanifest.xml**中應用程式節點的屬性設為下列其中一項:

-  `@android:style/Theme.Material`&ndash;深色主題。

-  `@android:style/Theme.Material.Light`&ndash;淺色主題。

-  `@android:style/Theme.Material.Light.DarkActionBar`&ndash;具有深色動作列的淺色主題。

下列範例會將應用程式*MyApp*設定為使用淺色主題:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者, 您也可以在`Theme` **AssemblyInfo.cs** (或**Properties.cs**) 中設定應用程式屬性。 例如：

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

當應用程式主題設定為時`@android:style/Theme.Material.Light`, *MyApp*中的每個活動都會使用`Theme.Material.Light`來顯示。


### <a name="theming-an-activity"></a>為活動主題

若要`Theme`建立活動的主題, 請將設定新增`[Activity]`至活動宣告上方的屬性, `Theme`並將其指派給您要使用的材質主題類別。 下列範例會使用`Theme.Material.Light`來主題活動:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此應用程式中的其他活動會使用`Theme.Material`預設的深色色彩配置 (或者, 如果已設定, 則為應用程式主題設定)。

<a name="customtheme" />

## <a name="using-custom-themes"></a>使用自訂主題

您可以建立自訂主題, 讓您的應用程式以您的品牌&rsquo;色彩進行樣式, 以增強您的品牌。 若要建立自訂主題, 您可以定義衍生自內建材質主題類別的新樣式, 以覆寫您想要變更的色彩屬性。 例如, 您可以定義衍生`Theme.Material.Light.DarkActionBar`自的自訂主題, 並將螢幕背景色彩變更為米色, 而不是白色。

材質主題會公開下列用於自訂的版面配置屬性:

-  `colorPrimary`&ndash;應用程式行的色彩。

-  `colorPrimaryDark`狀態列的色彩和內容相關的應用程式列; 這通常是的深色`colorPrimary`版本。 &ndash;

-  `colorAccent`&ndash; UI 控制項的色彩, 例如核取方塊、選項按鈕和編輯文字方塊。

-  `windowBackground`&ndash;螢幕背景的色彩。

-  `textColorPrimary`&ndash;應用程式行中的 UI 文字色彩。

-  `statusBarColor`&ndash;狀態列的色彩。

-  `navigationBarColor`&ndash;巡覽列的色彩。

這些畫面區域會在下圖中標示:

[![屬性的圖表和其相關聯的螢幕區域](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

根據預設, `statusBarColor`會設定為的`colorPrimaryDark`值。 您可以將`statusBarColor`設定為純色, 或將`@android:color/transparent`其設定為, 讓狀態列變成透明。 將設定`navigationBarColor`為, 也可以將巡覽列設為`@android:color/transparent`透明。


### <a name="creating-a-custom-app-theme"></a>建立自訂應用程式主題

您可以建立和修改應用程式專案的 [**資源**] 資料夾中的檔案, 以建立自訂應用程式主題。 若要使用自訂主題來為您的應用程式建立樣式, 請使用下列步驟:

-   在 [**資源]/[值** &mdash; ] 中建立**彩色 .xml**檔案, 您可以使用這個檔案來定義自訂主題色彩。 例如, 您可以將下列程式碼貼入**彩色 .xml**中, 以協助您開始使用:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   修改此範例檔案, 以定義您將在自訂主題中使用之色彩資源的名稱和顏色代碼。

-   建立**資源/值-v21**資料夾。 在此資料夾中, 建立**樣式 .xml**檔案:

    [![Resources/values-21 資料夾中的樣式 .xml 位置](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    請注意,**資源/值-v21**專用於 android 5.0 &ndash;舊版的 android 將不會讀取此資料夾中的檔案。

-   將節點加入至**樣式 .xml** , 並使用自`style`定義主題的名稱定義節點。 `resources` 例如, 以下是定義*MyCustomTheme* (衍生自內`Theme.Material.Light`建主題樣式) 的**樣式 .xml**檔案:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   此時, 使用*MyCustomTheme*的應用程式將會顯示股票`Theme.Material.Light`主題, 而不需要自訂:

    [![自訂前的自訂主題外觀](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   藉由定義您想要變更的版面配置屬性色彩, 將色彩自訂加入至**xml 樣式。** 例如, 若要將應用程式行色彩變更`my_blue`為, 並將 UI 控制項的色彩`my_purple`變更為, 請將色彩覆寫新增至**樣式 .xml** , 以參考  以 color 設定的色彩資源。 xml:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

這些變更都備妥之後, 使用*MyCustomTheme*的應用程式將會在中顯示應用`my_blue`程式列色彩, `my_purple`並在中使用`Theme.Material.Light` UI 控制項, 但請在其他地方使用色彩配置:

[![自訂之後的自訂主題外觀](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

在此範例中, *MyCustomTheme*會從`Theme.Material.Light`背景色彩、狀態列和文字色彩來借用色彩, 但它會將應用程式行的色彩變更為`my_blue` , 並將此選項按鈕的色彩設定`my_purple`為。

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>建立自訂視圖樣式

Android 5.0 也可以讓您將個別視圖設為樣式。 建立**colors**和 style **.xml**之後 (如上一節所述), 您可以將視圖樣式加入至**config.xml**樣式。
若要為個別視圖的樣式, 請使用下列步驟:

-   編輯**Resources/values-v21/樣式 .xml** , 並加入`style`具有自訂視圖樣式名稱的節點。 在此`style`節點中設定您的視圖的自訂色彩屬性。 例如, 若要建立具有更多圓角的自訂[CardView](~/android/user-interface/controls/card-view.md)樣式, `my_blue`並使用做為卡片背景`style`色彩, 請將節點加入至 style **.xml** ( `resources`在節點內部), 並設定背景色彩和圓角半徑:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   在您的版面配置中`style` , 設定該視圖的屬性, 以符合您在上一個步驟中選擇的自訂樣式名稱。 例如：

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下列螢幕擷取畫面提供了預設值`CardView`的範例 (如左側所示), 相較`CardView`于已使用自訂`CardView.MyBlue`主題 (顯示于右邊) 的來設計的。

[![預設 CardView 和自訂 CardView 的範例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

在此範例中, 會`CardView`以背景色彩`my_blue`和18dp 圓角半徑來顯示自訂。


## <a name="compatibility"></a>相容性

若要將您的應用程式設為樣式, 使其使用 Android 5.0 上的材質主題, 但自動還原為較舊 Android 版本上的向下相容樣式, 請使用下列步驟:

-   定義**資源/values-v21/樣式 .xml**中的自訂主題, 其衍生自材質主題樣式。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   定義**資源/值/樣式 .xml**中的自訂主題, 其衍生自舊版主題, 但使用的主題名稱與上述相同。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   在**androidmanifest.xml**中, 使用自訂主題名稱來設定您的應用程式。 
    例如：

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   或者, 您可以使用自訂主題來建立特定活動的樣式:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果您的主題使用 color **.xml**檔案中所定義的色彩, 請務必將此檔案放在**資源/值**(而不是**資源/值-v21**) 中, 讓自訂主題的這兩個版本都可以存取您的色彩定義。

當您的應用程式在 Android 5.0 裝置上執行時, 它會使用**Resources/values-v21/樣式 .xml**中指定的主題定義。 當此應用程式在較舊的 Android 裝置上執行時, 它會自動切換回**Resources/values/styles .xml**中所指定的主題定義。

如需有關舊版 Android 版本之主題相容性的詳細資訊, 請參閱[替代資源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>總結

本文引進了包含在 Android 5.0 (棒糖) 中的新材質主題使用者介面樣式。 其中說明了三種內建材質主題類型, 可讓您用來建立應用程式的樣式, 並說明如何建立自訂主題來為您的應用程式加上商標, 並提供如何主題個別視圖的範例。 最後, 本文說明如何在您的應用程式中使用材質主題, 同時維持與舊版 Android 的相容性。



## <a name="related-links"></a>相關連結

- [ThemeSwitcher (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [棒糖的簡介](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [替代資源](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android 棒糖](https://developer.android.com/about/versions/lollipop)
- [Android 圓形圖開發人員](https://developer.android.com/about/versions/pie/)
- [材質設計](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [材質設計原則](https://material.io/design/)
- [維護相容性](https://developer.android.com/training/backward-compatible-ui/)
