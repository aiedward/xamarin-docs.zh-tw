---
title: 材料佈景主題
description: 如何主題 Xamarin.Android 應用程式與資料的佈景主題
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a3b5f908330833a38aad9e329835a4a437fc29f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="material-theme"></a>材料佈景主題

*材料佈景主題*是決定外觀與風格的檢視和活動的開頭為 Android 5.0 （棒棒糖符號） 的使用者介面樣式。 材料佈景主題是內建 Android 5.0 版，因此它會使用由系統 UI 以及由應用程式。 材料佈景主題不是 「 主題 」 中的使用者可以動態地選擇設定 功能表中的全系統外觀選項的意義。 相反地，資料的佈景主題可以視為一組相關內建的基底樣式可讓您自訂您的應用程式的外觀與風格。

Android 提供三個材料佈景主題類別：

-  `Theme.Material` &ndash; 深色材料佈景主題; 的版本這是 Android 5.0 中的預設類別。

-  `Theme.Material.Light` &ndash; 材料佈景主題的輕量版。

-  `Theme.Material.Light.DarkActionBar` &ndash; 輕量版的材料佈景主題，但以深動作列。

這些資料的佈景主題類別的範例會顯示在這裡：

[![深色佈景主題淺色佈景主題及深色動作列佈景主題的範例螢幕擷取畫面](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

您可以從其中材料佈景主題，以建立您自己的佈景主題，覆寫部分或所有色彩屬性。 例如，您可以建立衍生自的佈景主題`Theme.Material.Light`，但是會覆寫應用程式狀態列色彩，以符合您的品牌的色彩。 您也可以設定樣式的個別檢視;例如，您可以建立的樣式[CardView](~/android/user-interface/controls/card-view.md) ，更有圓角，並使用較深的背景色彩。

您可以在整個應用程式中，使用單一的佈景主題，或您可以針對不同螢幕 （活動） 的應用程式中使用不同佈景主題。 在上面的螢幕擷取畫面，例如，單一應用程式會使用不同的佈景主題的每個活動示範內建的色彩配置。 選項按鈕切換至不同的活動，應用程式，如此一來，顯示不同的佈景主題。

只有在 Android 5.0 及更新版本支援材料佈景主題，因為您無法使用 （或衍生自材料佈景主題的自訂佈景主題） 佈景主題您的應用程式在舊版的 Android 上執行。 不過，您可以設定您的應用程式在 Android 5.0 裝置上使用資料的佈景主題和依正常程序切換回先前的佈景主題在舊版的 Android 上執行時 (請參閱[相容性](#compatibility)本文，如需詳細資訊一節)。


## <a name="requirements"></a>需求

需要下列項目在 Xamarin 應用程式中使用 Android 5.0 材料佈景主題的新功能：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定 Visual Studio 或 Visual Studio for mac。 

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須安裝 Android SDK Manager 透過。

-  **Java JDK 1.8** &ndash; JDK 1.7 可以使用，如果您特別以應用程式開發介面層級 23 及更早版本。 JDK 1.8 是可從[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

若要了解如何設定 Android 5.0 應用程式專案，請參閱[設定 Android 5.0 專案](~/android/platform/lollipop.md)。


## <a name="using-the-built-in-themes"></a>使用內建的佈景主題

使用材料佈景主題的最簡單方式是設定您的應用程式使用沒有自訂內建的佈景主題。 如果您不要明確設定佈景主題，您的應用程式將會預設為`Theme.Material`（深色的佈景主題）。 如果您的應用程式有一個活動，您可以在應用程式層級設定佈景主題。 如果您的應用程式有多個活動，您可以設定佈景主題，應用程式層級，讓它會針對所有活動，使用相同的佈景主題，或者您可以指派不同的佈景主題不同活動即可。 下列各節說明如何在應用程式層級，與在活動層級中設定佈景主題。


### <a name="theming-an-application"></a>主題設定應用程式

若要設定整個應用程式使用資料的佈景主題類別，設定`android:theme`屬性中的應用程式節點**AndroidManifest.xml**下列其中之一：

-  `@android:style/Theme.Material` &ndash; 深色佈景主題。

-  `@android:style/Theme.Material.Light` &ndash; 淺色佈景主題。

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; 淺色佈景主題，以深色動作列。

下列範例會設定應用程式*MyApp*使用淺色佈景主題：

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者，您可以設定應用程式`Theme`屬性**AssemblyInfo.cs** (或**Properties.cs**)。 例如: 

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

當應用程式佈景主題設為`@android:style/Theme.Material.Light`，在每個活動*MyApp*就會使用顯示`Theme.Material.Light`。


### <a name="theming-an-activity"></a>設定活動主題

在您新增至一個活動的佈景主題，`Theme`設為`[Activity]`活動宣告上方的屬性，並指派`Theme`至您想要使用的材料佈景主題類別。 下列範例主題的活動，具有`Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此應用程式中的其他活動會使用預設`Theme.Material`深色色彩配置 （或者，如果設定，應用程式佈景主題設定）。

<a name="customtheme" />

## <a name="using-custom-themes"></a>使用自訂佈景主題

您可以藉由建立自訂的佈景主題樣式應用程式與您的品牌增強您的品牌&rsquo;的色彩。 若要建立自訂佈景主題，您定義新的樣式衍生自內建的材料佈景主題類別，覆寫您想要變更的色彩屬性。 例如，您可以定義自訂的佈景主題衍生自`Theme.Material.Light.DarkActionBar`和變更而不是空白的米黃色畫面背景色彩。

材料佈景主題會公開下列版面配置屬性的自訂：

-  `colorPrimary` &ndash; 應用程式列色彩。

-  `colorPrimaryDark` &ndash; 色彩的狀態列和內容應用程式列，這通常是深色版本`colorPrimary`。

-  `colorAccent` &ndash; UI 控制項，例如核取方塊、 選項按鈕和編輯文字方塊中的色彩。

-  `windowBackground` &ndash; 螢幕背景色彩。

-  `textColorPrimary` &ndash; 在應用程式列中的 UI 文字的色彩。

-  `statusBarColor` &ndash; 狀態列的色彩。

-  `navigationBarColor` &ndash; 導覽列的色彩。

下圖中標示為這些螢幕區域：

[![屬性和其相關聯之的螢幕區域的圖表](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

根據預設，`statusBarColor`設定的值為`colorPrimaryDark`。 您可以設定`statusBarColor`為純色，或將它設定為`@android:color/transparent`讓狀態列變成透明。 導覽列也可以設定為透明藉由設定`navigationBarColor`至`@android:color/transparent`。


### <a name="creating-a-custom-app-theme"></a>建立自訂應用程式佈景主題

您可以建立自訂應用程式佈景主題建立和修改中的檔案**資源**應用程式專案的資料夾。 若要設定樣式應用程式與自訂佈景主題，使用下列步驟：

-   建立**colors.xml**檔案**資源/值**&mdash;您使用這個檔案來定義自訂的佈景主題色彩。 例如，您可以貼上下列程式碼**colors.xml**來協助您快速入門：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   修改此範例檔案，以定義的名稱和色彩編碼，讓您自訂的佈景主題色彩資源。

-   建立**資源/值-v21**資料夾。 在這個資料夾中建立**styles.xml**檔案：

    [![Styles.xml 資源/值-21.xml 資料夾中的位置](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    請注意，**資源/值-v21**特有 Android 5.0 &ndash; Android 的舊版本將不會讀取此資料夾中的檔案。

-   新增`resources`節點**styles.xml**並定義`style`節點與您的自訂佈景主題的名稱。 例如，以下是**styles.xml**檔案，可定義*MyCustomTheme* (衍生自內建`Theme.Material.Light`佈景主題樣式):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   此時，應用程式使用*MyCustomTheme*會顯示股票`Theme.Material.Light`沒有自訂的佈景主題：

    [![自訂的佈景主題之前自訂的外觀](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   新增自訂色彩**styles.xml**藉由定義您想要變更的版面配置屬性的色彩。 例如，若要變更的應用程式列色彩`my_blue`和變更的 UI 控制項加入至色彩`my_purple`，加入色彩覆寫**styles.xml** ，請參閱中設定色彩資源**colors.xml**:

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

這些變更之後，應用程式，會使用*MyCustomTheme*會顯示在應用程式狀態列色彩`my_blue`和 UI 控制項`my_purple`，但使用`Theme.Material.Light`其他位置的色彩配置：

[![自訂佈景主題的自訂之後的外觀](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

在此範例中， *MyCustomTheme*借用的色彩`Theme.Material.Light`背景色彩、 狀態列和文字色彩，但變更的應用程式列的色彩`my_blue`和設定的選項按鈕以色彩`my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>建立自訂檢視樣式

Android 5.0 也可讓您設定個別的檢視的樣式。 在建立之後**colors.xml**和**styles.xml** （如中所述上一節），您可以加入至檢視樣式**styles.xml**。
若要設定個別的檢視的樣式，使用下列步驟：

-   編輯**Resources/values-v21/styles.xml**並加入`style`節點的自訂檢視樣式名稱。 設定在這個檢視的自訂色彩屬性`style`節點。 例如，若要建立自訂[CardView](~/android/user-interface/controls/card-view.md)更具有圓角，並使用的樣式`my_blue`卡片的背景色彩，加入`style`節點**styles.xml** (內`resources`節點)，並設定背景色彩和角半徑：

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   在您的版面配置設定`style`適用於該檢視，以符合您在上一個步驟中選擇自訂樣式名稱的屬性。 例如: 

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下列螢幕擷取畫面會提供預設值的範例`CardView`（顯示於左邊） 相較於為`CardView`，已使用的自訂樣式`CardView.MyBlue`佈景主題 （在右側顯示）：

[![預設 CardView 和自訂 CardView 的範例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

在此範例中，自訂`CardView`以背景色彩顯示`my_blue`和 18dp 圓角半徑。


## <a name="compatibility"></a>相容性

若要設定您的應用程式的樣式，讓它在 Android 5.0 上使用材料佈景主題，但會自動還原為舊版 Android 上的向下相容樣式，使用下列步驟：

-   定義自訂的佈景主題中**Resources/values-v21/styles.xml**衍生自材料佈景主題樣式。 例如: 

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   定義自訂的佈景主題中**Resources/values/styles.xml**會衍生自較舊的佈景主題，但使用與上述相同的佈景主題名稱。 例如: 

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   在**AndroidManifest.xml**，設定您的應用程式具有自訂佈景主題名稱。 
    例如: 

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   或者，您可以使用自訂的佈景主題的特定活動設定樣式：

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果您的佈景主題使用色彩中定義**colors.xml**檔案，請務必將此檔案中的**資源/值**(而非**資源/值-v21**)，讓這兩個版本自訂的佈景主題可以存取您色彩的定義。

當您的應用程式在 Android 5.0 裝置上執行時，它會使用在指定的佈景主題定義**Resources/values-v21/styles.xml**。 當此應用程式在較舊的 Android 裝置上執行時，它會自動切換回中指定的佈景主題定義**Resources/values/styles.xml**。

如需佈景主題與較舊的 Android 版本的相容性的詳細資訊，請參閱[替代資源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>總結

這篇文章導入新資料的佈景主題使用者介面樣式隨附於 Android 5.0 （棒棒糖符號）。 它描述樣式應用程式時，您可以使用三個內建材料佈景主題類別，它說明如何建立自訂佈景主題為您的應用程式的商標，它提供個別的檢視主題的範例。 最後，本文將說明如何在您的應用程式中使用材料佈景主題，同時維持與舊版的 Android 的向下相容性。



## <a name="related-links"></a>相關連結

- [ThemeSwitcher （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [棒棒糖符號簡介](~/android/platform/lollipop.md)
- [CardView](~/android/user-interface/controls/card-view.md)
- [替代資源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)
- [L android 開發人員預覽](http://developer.android.com/preview/index.html)
- [材料設計](http://developer.android.com/preview/material/index.html)
- [材料設計原則](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
- [維護相容性](http://developer.android.com/preview/material/compatibility.html)
