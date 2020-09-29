---
title: 材質佈景主題
description: 如何使用材質主題來為您的 Xamarin Android 應用程式提供主題
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 22549bc7410b62e04cdb34c5c102a2fed18b0cf9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457090"
---
# <a name="material-theme"></a>材質佈景主題

*材質主題* 是一種使用者介面樣式，可決定從 Android 5.0 (棒棒) 開始的視圖和活動外觀和風格。 材質主題內建于 Android 5.0 中，因此系統 UI 和應用程式會使用它。 材質主題並不是「主題」，而是指使用者可以從 [設定] 功能表動態選擇的全系統外觀選項。 相反地，您可以將材質主題視為一組相關的內建基底樣式，讓您自訂應用程式的外觀與風格。

Android 提供三種材質主題的特色：

- `Theme.Material`&ndash;材質主題的深色版本; 這是 Android 5.0 中的預設類別。

- `Theme.Material.Light`&ndash;光線版本的材質主題。

- `Theme.Material.Light.DarkActionBar`&ndash;輕量版本的材質主題，但是具有深色的動作列。

這些材質主題類別的範例如下所示：

[![深色主題、淺色主題和深色動作列主題的範例螢幕擷取畫面](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

您可以從材質主題衍生，以建立您自己的主題，並覆寫部分或全部的色彩屬性。 例如，您可以建立衍生自的主題 `Theme.Material.Light` ，但會覆寫應用程式行色彩以符合您品牌的色彩。 您也可以針對個別的視圖進行樣式。例如，您可以建立具有更多圓角的 [CardView](~/android/user-interface/controls/card-view.md) 樣式，並使用較深色的背景色彩。

您可以針對整個應用程式使用單一主題，也可以針對應用程式中) 的不同畫面使用不同的主題 (活動。 例如，在上述螢幕擷取畫面中，單一應用程式會針對每個活動使用不同的主題，以示範內建的色彩配置。 選項按鈕會將應用程式切換到不同的活動，因此會顯示不同的主題。

因為只有 Android 5.0 和更新版本才支援材質主題，所以您無法使用它 (或衍生自材質主題的自訂主題) 來主題應用程式以在舊版 Android 上執行。 不過，您可以將應用程式設定為使用 Android 5.0 裝置上的材質主題，並在舊版 Android 上執行時，正常切換回先前的主題 (如需) 詳細資料，請參閱本文的「 [相容性](#compatibility) 」一節。

## <a name="requirements"></a>需求

在 Xamarin 應用程式中使用新的 Android 5.0 材質主題功能時，需要下列各項：

- **Xamarin.Android**您 &ndash; 必須安裝 Visual Studio 或 Visual Studio for Mac，才能安裝並設定 Xamarin. Android xamarin. android 4.20 或更新版本。 

- **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須透過 Android SDK 管理員安裝。

- **JAVA JDK 1.8** &ndash; 如果您特別以 API 層級23及更早的版本為目標，則可以使用 JDK 1.7。 JDK 1.8 可從 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)取得。

若要瞭解如何設定 Android 5.0 應用程式專案，請參閱 [設定 android 5.0 專案](~/android/platform/lollipop.md)。

## <a name="using-the-built-in-themes"></a>使用內建主題

使用材質主題最簡單的方式，就是將您的應用程式設定為使用內建的主題，而不需要自訂。 如果您不想要明確設定主題，您的應用程式會預設為 `Theme.Material` (深色主題) 。 如果您的應用程式只有一個活動，您可以在活動層級設定主題。 如果您的應用程式有多個活動，您可以在應用層級設定主題，使其在所有活動中都使用相同的主題，或者您可以將不同的主題指派給不同的活動。 下列各節說明如何在應用層級和活動層級設定主題。

### <a name="theming-an-application"></a>主題應用程式

若要設定整個應用程式使用材質主題類別，請將 `android:theme` **AndroidManifest.xml** 中的應用程式節點屬性設定為下列其中一項：

- `@android:style/Theme.Material`&ndash;深色主題。

- `@android:style/Theme.Material.Light`&ndash;淺色主題。

- `@android:style/Theme.Material.Light.DarkActionBar`&ndash;深色動作列的淺色主題。

下列範例會將應用程式 *MyApp* 設定為使用淺色主題：

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者，您可以 `Theme` 在 **AssemblyInfo.cs** (或 **Properties.cs**) 中設定應用程式屬性。 例如：

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

當應用程式主題設定為時 `@android:style/Theme.Material.Light` ， *MyApp* 中的每個活動都會使用來顯示 `Theme.Material.Light` 。

### <a name="theming-an-activity"></a>主題活動

若要為活動建立主題，請將 `Theme` 設定新增至 `[Activity]` 活動宣告上方的屬性，並將其指派給 `Theme` 您想要使用的材質主題類別。 下列範例會使用下列主題來提供活動 `Theme.Material.Light` ：

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此應用程式中的其他活動將會使用預設的 `Theme.Material` 深色色彩配置 (或者，如果已設定，則應用程式主題設定) 。

<a name="customtheme"></a>

## <a name="using-custom-themes"></a>使用自訂主題

您可以建立自訂主題，以使用您的品牌色彩來為應用程式建立樣式，藉以增強您的品牌 &rsquo; 。 若要建立自訂主題，您可以定義衍生自內建材質主題類別的新樣式，以覆寫您想要變更的色彩屬性。 例如，您可以定義衍生自的自訂主題， `Theme.Material.Light.DarkActionBar` 並將螢幕背景色彩變更為米色而非白色。

材質主題會公開下列可供自訂的版面配置屬性：

- `colorPrimary`&ndash;應用程式行的色彩。

- `colorPrimaryDark`&ndash;狀態列和內容應用程式橫條的色彩; 這通常是深色版 `colorPrimary` 。

- `colorAccent`&ndash;UI 控制項的色彩，例如核取方塊、選項按鈕和編輯文字方塊。

- `windowBackground`&ndash;螢幕背景的色彩。

- `textColorPrimary`&ndash;應用程式行中的 UI 文字色彩。

- `statusBarColor`&ndash;狀態列的色彩。

- `navigationBarColor`&ndash;巡覽列的色彩。

這些畫面區域會在下圖中標示：

[![屬性和其相關聯之螢幕區域的圖表](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

依預設， `statusBarColor` 會設定為的值 `colorPrimaryDark` 。 您可以設定 `statusBarColor` 為純色，也可以將它設定為 `@android:color/transparent` 使狀態列變成透明。 您也可以設定為，讓巡覽列變成 `navigationBarColor` 透明 `@android:color/transparent` 。

### <a name="creating-a-custom-app-theme"></a>建立自訂應用程式主題

您可以建立和修改應用程式專案的 [ **資源** ] 資料夾中的檔案，以建立自訂應用程式主題。 若要使用自訂主題來為您的應用程式建立樣式，請使用下列步驟：

- 在**資源/值**中建立**colors.xml**檔案， &mdash; 您可以使用這個檔案來定義您的自訂主題色彩。 例如，您可以將下列程式碼貼入 **colors.xml** ，以協助您開始使用：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

- 修改此範例檔案，以定義您將在自訂主題中使用之色彩資源的名稱和色彩代碼。

- 建立 **資源/值-v21** 資料夾。 在此資料夾中，建立 **styles.xml** 檔案：

    [![styles.xml 在 Resources/values-21.xml 資料夾中的位置](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    請注意， **資源/值-v21** 是 android 5.0 &ndash; 舊版 android 專用的，將不會讀取此資料夾中的檔案。

- 將 `resources` 節點新增至 **styles.xml** ，並 `style` 使用您的自訂主題名稱定義節點。 例如，以下是一個 **styles.xml** 檔案，它會定義衍生自內建主題樣式) 的 *MyCustomTheme* (`Theme.Material.Light` ：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

- 此時，使用 *MyCustomTheme* 的應用程式將會顯示股票主題， `Theme.Material.Light` 而不需要自訂：

    [![自訂之前的自訂主題外觀](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

- 藉由定義要變更的版面配置屬性色彩，將色彩自訂新增至 **styles.xml** 。 例如，若要將應用程式行色彩變更為 `my_blue` ，並將 UI 控制項的色彩變更為 `my_purple` ，請將色彩覆寫新增至參考**colors.xml**中所設定之色彩資源的**styles.xml** ：

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

進行這些變更之後，使用 *MyCustomTheme* 的應用程式將會在中顯示應用程式行色彩，而在中則會顯示 `my_blue` UI 控制項 `my_purple` ，但在 `Theme.Material.Light` 其他地方使用色彩配置：

[![自訂後的自訂主題外觀](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

在此範例中， *MyCustomTheme* `Theme.Material.Light` 會針對背景色彩、狀態列和文字色彩來借用色彩，但它會將應用程式列的色彩變更為 `my_blue` ，並將選項按鈕的色彩設定為 `my_purple` 。

<a name="customview"></a>

### <a name="creating-a-custom-view-style"></a>建立自訂視圖樣式

Android 5.0 也可讓您針對個別的視圖進行樣式。 依照上一節) 所述建立 **colors.xml** 和 **styles.xml** (之後，您就可以將視圖樣式加入至 **styles.xml**。
若要為個別的視圖進行樣式，請使用下列步驟：

- 編輯 **資源/值-v21/styles.xml** ，並 `style` 使用您的自訂視圖樣式名稱來新增節點。 為您在此節點內的視圖設定自訂色彩屬性 `style` 。 例如，若要建立具有更多圓角的自訂 [CardView](~/android/user-interface/controls/card-view.md) 樣式，並使用 `my_blue` 做為卡片背景色彩，請將 `style` 節點新增至節點內 **styles.xml** (`resources`) 並設定背景色彩和圓角半徑：

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

- 在您的版面配置中，設定 `style` 該視圖的屬性，使其符合您在上一個步驟中選擇的自訂樣式名稱。 例如：

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下列螢幕擷取畫面提供 `CardView` 左邊) 所顯示的預設 (範例，相較于已 `CardView` 使用自訂主題樣式的， `CardView.MyBlue` (顯示在右) ：

[![預設 CardView 和自訂 CardView 的範例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

在此範例中， `CardView` 會以背景色彩 `my_blue` 和18dp 邊角半徑來顯示自訂。

## <a name="compatibility"></a>相容性

若要為您的應用程式建立樣式，使其使用 Android 5.0 上的材質主題，但是在較舊的 Android 版本上自動還原為向下相容的樣式，請使用下列步驟：

- 在資源/值中定義自訂主題：衍生自材質主題樣式的 **v21/styles.xml** 。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- 在 **資源/值/styles.xml** 中定義衍生自舊版主題的自訂主題，但使用與上述相同的主題名稱。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- 在 **AndroidManifest.xml**中，使用自訂主題名稱設定您的應用程式。 
    例如：

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

- 或者，您可以使用自訂主題來建立特定活動的樣式：

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果您的主題使用 **colors.xml** 檔中定義的色彩，請務必將這個檔案放在 **資源/值** (而不是 **資源/值-v21**) 讓自訂主題的這兩個版本都能存取您的色彩定義。

當您的應用程式在 Android 5.0 裝置上執行時，它會使用 **Resources/values-v21/styles.xml**中指定的主題定義。 當此應用程式在較舊的 Android 裝置上執行時，它會自動切換回 **Resources/values/styles.xml**中指定的主題定義。

如需有關與舊版 Android 版本的主題相容性的詳細資訊，請參閱 [替代資源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>摘要

本文引進了 Android 5.0 (的新材質主題使用者介面樣式) 。 它說明了三個內建的材質主題，可讓您用來為應用程式建立樣式，並說明如何建立自訂主題來為您的應用程式建立商標，並提供如何主題個別視圖的範例。 最後，本文說明如何在您的應用程式中使用材質主題，同時維持與舊版 Android 的向下相容性。

## <a name="related-links"></a>相關連結

- [ThemeSwitcher (範例) ](/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [棒棒的簡介](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [替代資源](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android 棒棒](https://developer.android.com/about/versions/lollipop)
- [Android 圓形圖開發人員](https://developer.android.com/about/versions/pie/)
- [材質設計](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [材質設計原則](https://material.io/design/)
- [維持相容性](https://developer.android.com/training/backward-compatible-ui/)