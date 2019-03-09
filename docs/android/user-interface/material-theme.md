---
title: 材質佈景主題
description: 佈景主題的如何 Xamarin.Android 應用程式與資料主題
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: ff94211086956e36da377445d90359789b62fc60
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668318"
---
# <a name="material-theme"></a>材質佈景主題

*材料佈景主題*是判斷檢視和開頭為 Android 5.0 (Lollipop) 的活動的外觀及操作的使用者介面樣式。 材料佈景主題是內建 Android 5.0 版，所以它會使用由系統 UI 以及應用程式。 材料佈景主題不是 「 主題 」，因為使用者可以動態地選擇從 [設定] 功能表的全系統外觀選項。 相反地，材料佈景主題可以視為一組的相關內建的基底樣式可供您自訂您的應用程式的外觀與風格。

Android 提供三種材料佈景主題類型：

-  `Theme.Material` &ndash; 深色的版本的材料佈景主題這是 Android 5.0 中的預設類別。

-  `Theme.Material.Light` &ndash; 材料佈景主題的輕量版。

-  `Theme.Material.Light.DarkActionBar` &ndash; 輕量版的材料佈景主題，但以深色的動作列。

這裡會顯示這些材料佈景主題類別的範例：

[![深色佈景主題、 淺色佈景主題，以及深色動作列佈景主題的範例螢幕擷取畫面](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

您可以衍生自材料佈景主題，以建立您自己的佈景主題覆寫部分或所有色彩屬性。 例如，您可以建立衍生自佈景主題`Theme.Material.Light`，但會覆寫應用程式列色彩以符合您的品牌色彩。 您也可以設定樣式的個別檢視;例如，您可以在其中建立的樣式[CardView](~/android/user-interface/controls/card-view.md) ，更具有圓角，並使用較深的背景色彩。

您可以使用單一的佈景主題為整個應用程式，或您可以針對不同的檢視畫面 （活動） 應用程式中使用不同的佈景主題。 在上述螢幕擷取畫面中，例如，單一應用程式使用不同的佈景主題，每個活動來示範內建的色彩配置。 選項按鈕切換至不同的活動的 應用程式，如此一來，顯示不同的佈景主題。

因為只有 Android 5.0 和更新版本支援材料佈景主題，則您無法使用 （或衍生自材料佈景主題自訂佈景主題） 的佈景主題您的應用程式在舊版的 Android 上執行。 不過，您可以設定應用程式在 Android 5.0 裝置上使用材料佈景主題，並依正常程序切換回先前的佈景主題在舊版 Android 上執行時 (請參閱[相容性](#compatibility)如需詳細資訊一節)。


## <a name="requirements"></a>需求

需要下列項目以 Xamarin 為基礎的應用程式中使用新的 Android 5.0 材料佈景主題功能：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定與 Visual Studio 或 Visual Studio for mac。 

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須安裝 Android SDK 管理員透過。

-  **Java JDK 1.8** &ndash;可以使用 JDK 1.7，如果您是特別瞄準的 API 層級 23 和更早版本。 JDK 1.8 是可從[Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

若要了解如何設定 Android 5.0 應用程式專案，請參閱[設定註冊的 Android 5.0 專案](~/android/platform/lollipop.md)。


## <a name="using-the-built-in-themes"></a>使用內建的佈景主題

使用材料佈景主題的最簡單方式是設定您要使用內建的佈景主題，而不需要自訂的應用程式。 如果您不要明確設定佈景主題，您的應用程式會預設為`Theme.Material`（暗色調佈景主題）。 如果您的應用程式有只有一個活動，您可以設定應用程式層級的佈景主題。 如果您的應用程式有多個活動，您可以設定佈景主題，應用程式層級，讓它使用相同的佈景主題之間所有的活動，或您可以將不同的佈景主題指派給不同的活動即可。 下列各節說明如何設定佈景主題，應用程式層級和活動層級。


### <a name="theming-an-application"></a>佈景主題的應用程式

若要設定整個應用程式使用的材料佈景主題類別，設定`android:theme`的應用程式節點的屬性**AndroidManifest.xml**至下列其中之一：

-  `@android:style/Theme.Material` &ndash; 深色佈景主題。

-  `@android:style/Theme.Material.Light` &ndash; 淺色佈景主題。

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; 淺色與深色的動作列的佈景主題。

下列範例會設定應用程式*MyApp*使用淺色佈景主題：

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者，您可以設定應用程式`Theme`屬性中**AssemblyInfo.cs** (或**Properties.cs**)。 例如: 

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

當應用程式佈景主題設定為`@android:style/Theme.Material.Light`，在每個活動*MyApp*就會使用顯示`Theme.Material.Light`。


### <a name="theming-an-activity"></a>佈景主題的活動

一個活動的佈景主題，您將新增`Theme`設為`[Activity]`活動宣告上方的屬性，並指派`Theme`至您想要使用的材料佈景主題類別。 下列範例主題的活動，具有`Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此應用程式中的其他活動會使用預設`Theme.Material`深色色彩配置 （或者，如果設定，應用程式佈景主題設定）。

<a name="customtheme" />

## <a name="using-custom-themes"></a>使用自訂的佈景主題

您可以建立自訂佈景主題，設定您的應用程式與您的品牌的樣式來強化您的品牌&rsquo;的色彩。 若要建立自訂佈景主題，您定義新的樣式，衍生自內建的材料佈景主題類別，覆寫您想要變更色彩屬性。 例如，您可以定義自訂的佈景主題，衍生自`Theme.Material.Light.DarkActionBar`和畫面背景色彩變更成米黃色，而不是白色。

材料佈景主題會公開下列版面配置屬性的自訂：

-  `colorPrimary` &ndash; 應用程式列的色彩。

-  `colorPrimaryDark` &ndash; 內容相關的應用程式列; 與狀態列色彩這通常是深版本`colorPrimary`。

-  `colorAccent` &ndash; UI 控制項，例如核取方塊、 選項按鈕和編輯文字方塊中的色彩。

-  `windowBackground` &ndash; 畫面背景色彩。

-  `textColorPrimary` &ndash; 在應用程式列中的 UI 文字的色彩。

-  `statusBarColor` &ndash; 狀態列的色彩。

-  `navigationBarColor` &ndash; 導覽列的色彩。

下圖中標示這些畫面區域：

[![屬性和其相關聯之的螢幕區域的圖表](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

根據預設，`statusBarColor`設定的值為`colorPrimaryDark`。 您可以設定`statusBarColor`為純色，或將它設定為`@android:color/transparent`讓狀態列變成透明。 瀏覽列也可以設定為透明 splittunneling`navigationBarColor`至`@android:color/transparent`。


### <a name="creating-a-custom-app-theme"></a>建立自訂應用程式佈景主題

您可以藉由建立及修改中的檔案建立自訂應用程式佈景主題**資源**應用程式專案的資料夾。 若要設定您的應用程式，以自訂主題的樣式，使用下列步驟：

-   建立**colors.xml**中的檔案**資源/值**&mdash;您使用此檔案來定義您的自訂佈景主題色彩。 例如，您可以貼上下列程式碼**colors.xml**來協助您開始使用：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   修改此範例檔案，以定義的名稱和色彩編碼，您將使用您自訂的佈景主題中的色彩資源。

-   建立**資源/值-v21**資料夾。 在此資料夾中，建立**styles.xml 組**檔案：

    [![資源/值-21.xml 資料夾中的 styles.xml 組的位置](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    請注意，**資源/值-v21**特有 Android 5.0&ndash;舊版 Android 將不會讀取此資料夾中的檔案。

-   新增`resources`節點，以**styles.xml** ，並定義`style`您自訂的佈景主題的名稱的節點。 例如，以下是**styles.xml**定義的檔案*MyCustomTheme* (衍生自內建`Theme.Material.Light`佈景主題樣式):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   到目前為止，使用的應用程式*MyCustomTheme*會顯示股票`Theme.Material.Light`而不需要自訂的佈景主題：

    [![自訂佈景主題自訂項目之前的外觀](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   新增色彩自訂**styles.xml 組**藉由定義您想要變更的版面配置屬性的色彩。 例如，若要變更的應用程式列色彩`my_blue`變更至 UI 控制項的色彩`my_purple`，新增色彩覆寫**styles.xml 組**那指的設定中的色彩資源**colors.xml**:

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

這些變更之後，應用程式，會使用*MyCustomTheme*會顯示在應用程式列色彩`my_blue`UI 中和控制項`my_purple`，但使用`Theme.Material.Light`其他位置的色彩配置：

[![自訂佈景主題自訂項目之後的外觀](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

在此範例中， *MyCustomTheme*借用色彩`Theme.Material.Light`背景色彩、 狀態列和文字色彩，但變更的應用程式列的色彩`my_blue`和設定的選項按鈕以色彩`my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>建立自訂檢視樣式

Android 5.0 也可讓您設定的個別檢視的樣式。 在建立後**colors.xml**並**styles.xml 組**（如所述在上一節中），您可以新增要檢視樣式**styles.xml 組**。
若要設定個別的檢視的樣式，使用下列步驟：

-   編輯**Resources/values-v21/styles.xml** ，並新增`style`的自訂檢視樣式名稱的節點。 設定自訂色彩屬性，在這個檢視`style`節點。 例如，若要建立自訂[CardView](~/android/user-interface/controls/card-view.md)更圓角和使用的樣式`my_blue`卡片的背景色彩，以新增`style`節點**styles.xml 組**(內`resources`節點)，並設定背景色彩與邊角半徑：

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   在配置中，設定`style`該檢視，以符合您在上一個步驟中選擇自訂樣式名稱的屬性。 例如: 

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下列螢幕擷取畫面會提供預設值的範例`CardView`（顯示在左邊），相較於`CardView`已經設定的樣式以自訂`CardView.MyBlue`佈景主題 （在右側顯示）：

[![預設 CardView 和自訂 CardView 的範例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

在此範例中，自訂`CardView`就會顯示背景色彩`my_blue`和 18dp 圓角半徑。


## <a name="compatibility"></a>相容性

若要設定您的應用程式的樣式，讓它在 Android 5.0 上使用材料佈景主題，但會自動回復為舊版 Android 上的向下相容樣式，使用下列步驟：

-   定義自訂的佈景主題中**Resources/values-v21/styles.xml**衍生自材料佈景主題樣式。 例如: 

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   定義自訂的佈景主題中**Resources/values/styles.xml** ，衍生自較舊的佈景主題，但使用與上述相同佈景主題名稱。 例如: 

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   在  **AndroidManifest.xml**，設定應用程式的自訂佈景主題名稱。 
    例如: 

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   或者，您可以設定特定的活動使用您自訂的佈景主題樣式：

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果您的佈景主題使用中定義的色彩**colors.xml**檔案，請務必將此檔案在放**資源/值**(而非**資源/值-v21**)，讓這兩個版本您自訂的佈景主題可以存取您的色彩定義。

當您的應用程式在 Android 5.0 裝置上執行時，它會使用佈景主題定義中指定**Resources/values-v21/styles.xml**。 當此應用程式在舊的 Android 裝置上執行時，它會自動切換回在指定的佈景主題定義**Resources/values/styles.xml**。

如需佈景主題與較舊的 Android 版本的相容性的詳細資訊，請參閱[替代資源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>總結

本文介紹 Android 5.0 (Lollipop) 中包含新材料佈景主題使用者介面樣式。 它所述來設定您的應用程式的樣式，您可以使用三種內建材料佈景主題類型、 它說明如何建立自訂佈景主題來設定您的應用程式的商標和它所提供的佈景主題的範例個別的檢視。 最後，本文會說明如何在您的應用程式中使用材料佈景主題，同時維持與舊版 Android 的向下相容性。



## <a name="related-links"></a>相關連結

- [ThemeSwitcher （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [棒棒糖符號的簡介](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [替代資源](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android Lollipop](https://developer.android.com/about/versions/lollipop)
- [Android 的圓形圖開發人員](https://developer.android.com/about/versions/pie/)
- [材料設計](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [材料設計原則](https://material.io/design/)
- [維護相容性](https://developer.android.com/training/backward-compatible-ui/)
