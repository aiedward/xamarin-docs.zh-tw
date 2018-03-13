---
title: CardView
description: "Cardview widget 會顯示文字和映像內容的檢視，類似於卡片的 UI 元件。 本指南說明如何使用和自訂 CardView Xamarin.Android 應用程式中，同時維持與舊版的 Android 回溯相容性。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 46eec10bbabec74719affabce1e8033a083680be
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="cardview"></a>CardView

_Cardview widget 會顯示文字和映像內容的檢視，類似於卡片的 UI 元件。本指南說明如何使用和自訂 CardView Xamarin.Android 應用程式中，同時維持與舊版的 Android 回溯相容性。_


## <a name="overview"></a>總覽

`Cardview`  Widget 中，Android 5.0 （棒棒糖符號） 中導入是 text 和 image 內容呈現的檢視，類似於卡 UI 元件。 `CardView` 會實作為`FrameLayout`widget 具有圓的角和陰影。 一般而言，`CardView`用來顯示中的單一資料列項目`ListView`或`GridView`檢視群組。 例如，下列螢幕擷取畫面是旅行保留應用程式實作的範例`CardView`-基礎旅行目的地卡的可捲動`ListView`:

![使用 CardView 每個移動目的地的範例應用程式](card-view-images/01-cardview-example.png)

本指南說明如何新增`CardView`封裝至您的 Xamarin.Android 專案、 如何新增`CardView`版面配置，以及如何自訂的外觀`CardView`應用程式中。 此外，本指南提供的詳細的清單`CardView`屬性，您可以變更，包括可協助您使用的屬性`CardView`Android 5.0 棒棒糖符號之前的 Android 版本。

<a name="requirements" />

## <a name="requirements"></a>需求

下列才能使用新的 Android 5.0 及更新版本的功能 (包括`CardView`) 中的 Xamarin 應用程式：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定 Visual Studio 或 Visual Studio for mac。

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須安裝 Android SDK Manager 透過。

-  **Java JDK 1.8** &ndash; JDK 1.7 可以使用，如果您特別以應用程式開發介面層級 23 及更早版本。 JDK 1.8 是可從[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

您的應用程式必須也包含`Xamarin.Android.Support.v7.CardView`封裝。 若要加入`Xamarin.Android.Support.v7.CardView`適用於 Mac 的 Visual Studio 中的封裝：

1. 開啟您的專案，以滑鼠右鍵按一下**封裝**選取**新增套件**。

2. 在**新增套件**對話方塊中，搜尋**CardView**。

3. 選取**Xamarin 支援程式庫 v7 CardView**，然後按一下 **加入封裝**。

若要加入`Xamarin.Android.Support.v7.CardView`Visual Studio 中的封裝：

1. 開啟您的專案，以滑鼠右鍵按一下**參考**節點 (在**方案總管 中**窗格) 選取**管理 NuGet 封裝...**.

2. 當**管理 NuGet 封裝**會顯示對話方塊中，輸入**CardView** [搜尋] 方塊中。

3. 當**Xamarin 支援程式庫 v7 CardView**出現，請按一下**安裝**。

若要了解如何設定 Android 5.0 應用程式專案，請參閱[設定 Android 5.0 專案](~/android/platform/lollipop.md)。
如需安裝 NuGet 封裝的詳細資訊，請參閱[逐步解說： 在您的專案包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


## <a name="introducing-cardview"></a>介紹 CardView

預設值`CardView`像白色卡片具有最低限度圓的角和陰影。 下列範例**Main.axml**配置顯示單一`CardView`widget，其中包含`TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

如果您要取代現有的內容使用這段 XML **Main.axml**，請務必在任何程式碼註解**Weatherapp**參考先前在 XML 中的資源。

這個版面配置範例會建立預設`CardView`與單行文字，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面的白色背景和文字行 CardView](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此範例中，應用程式樣式設定為淺材料佈景主題 (`Theme.Material.Light`) 以便`CardView`陰影和邊緣是看得更清楚。 如需主題 Android 5.0 應用程式的詳細資訊，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。 在下一步 區段中，我們將了解如何自訂`CardView`應用程式。


## <a name="customizing-cardview"></a>自訂 CardView

您可以修改基本`CardView`屬性以自訂的外觀`CardView`應用程式中。 例如，提高的權限`CardView`可以增加以較大的陰影 （這樣會似乎 float 背景上更高的卡片）。 此外，邊角的半徑可以增加設為圓角的智慧卡詳細捨入。

在下一步 的版面配置範例中，自訂`CardView`用來建立列印照片 （「 快照集 」） 的模擬。 `ImageView`加入至`CardView`顯示影像和`TextView`放置之下`ImageView`來顯示影像的標題。
在此範例配置中，`CardView`具有以下自訂內容：

-  `cardElevation`增加至 4dp 轉換較大的陰影。
-  `cardCornerRadius`增加至 5dp 設為圓角會出現一個圓角。

因為`CardView`係由 Android v7 支援程式庫，其屬性不是可從`android:`命名空間。 因此，您必須定義您自己的 XML 命名空間，而且使用該命名空間為`CardView`屬性前置詞。 在下列版面配置範例中，我們將使用這一行來定義命名空間稱為`cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以呼叫此命名空間`card_view`或甚至`myapp`如果您選擇 （它是只在這個檔案的範圍內存取的）。 無論您選擇呼叫此命名空間，您必須使用它的前置詞`CardView`您想要修改的屬性。 版面配置在本例中，`cardview`命名空間是前置詞`cardElevation`和`cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

這個版面配置範例使用在相片檢視應用程式中，顯示影像時`CardView`具有相片快照集的外觀，如下列螢幕擷取畫面所示：

[![使用影像和影像下方的標題 CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

這個螢幕擷取畫面取自[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)範例應用程式，會使用`RecyclerView`widget 來呈現捲動清單`CardView`映像，以檢視相片。 如需有關`RecyclerView`，請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)指南。

請注意，`CardView`可以在其內容區域中顯示一個以上的子檢視。 例如，在上述的相片，瀏覽應用程式範例，內容區域組成`ListView`包含`ImageView`和`TextView`。 雖然`CardView`執行個體通常會以垂直方式排列，您也可以排列它們水平 (請參閱[建立自訂檢視樣式](~/android/user-interface/material-theme.md#customview)的範例螢幕擷取畫面)。


### <a name="cardview-layout-options"></a>CardView 版面配置選項

`CardView` 您可以自訂版面配置設定的一或多個會影響其邊框距離、 提高權限、 圓角半徑和背景色彩的屬性：

[![CardView 屬性的圖表](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

每個屬性也可動態變更藉由呼叫對應的`CardView`方法 (如需有關`CardView`方法，請參閱[CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html))。
請注意，這些屬性 （除了背景色彩） 會接受維度的值，也就是十進位數字後面的單位。 例如，`11.5dp`指定 11.5 密度無關的像素。


#### <a name="padding"></a>與邊框距離
`
CardView` 提供五個填補屬性，將卡片中的內容。 您可以在您配置的 XML 中進行設定，或您可以在程式碼中呼叫的類似方法：

[![Padding 屬性 CardView 的圖表](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填補屬性說明，如下所示：

-  `contentPadding` &ndash; 內部的子檢視之間的填補`CardView`和智慧卡的所有邊緣。

-  `contentPaddingBottom` &ndash; 內部的子檢視之間的填補`CardView`和卡片的下邊緣。

-  `contentPaddingLeft` &ndash; 內部的子檢視之間的填補`CardView`和左邊的緣的卡片。

-  `contentPaddingRight` &ndash; 內部的子檢視之間的填補`CardView`和卡片的右邊緣。

-  `contentPaddingTop` &ndash; 內部的子檢視之間的填補`CardView`和卡片上的邊緣。

內容與邊框距離屬性會以相對於內容區域，而不是位於內容區域中任何給定的小工具的界限。
例如，如果`contentPadding`相片檢視應用程式中充分增加`CardView`會裁剪影像和卡片上顯示的文字。



#### <a name="elevation"></a>提高權限

`CardView` 提供兩個提高權限屬性來控制其權限提高，如此一來，其陰影的大小：

[![CardView 提高權限屬性的圖表](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

說明的提高權限屬性，如下所示：

-  `cardElevation` &ndash; 提高的權限`CardView`（表示其 Z 軸）。

-  `cardMaxElevation` &ndash; 最大值`CardView`的提高權限。

較大的值的`cardElevation`增加陰影的大小，以讓`CardView`似乎背景上更高的浮點數。 `cardElevation`屬性也會決定重疊檢視的繪製順序; 也就是說，`CardView`會繪製在另一個重疊檢視較高的權限提高設定和上述任何重疊的檢視設定較低權限提高。
`cardMaxElevation`設定適用於當您的應用程式提升權限會動態變更&ndash;會防止您定義這項設定的上限延伸陰影。


#### <a name="corner-radius-and-background-color"></a>圓角半徑和背景色彩

`CardView` 提供屬性，您可以用來控制其圓角半徑和它的背景色彩。 這兩個屬性可讓您變更整體樣式`CardView`:

[![CardView 角 radious 和背景色彩屬性的圖表](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

說明這些屬性，如下所示：

-  `cardCornerRadius` &ndash; 各個角落的圓角半徑`CardView`。

-  `cardBackgroundColor` &ndash; 背景色彩`CardView`。

在此圖中，`cardCornerRadius`設為更圓角 10dp 和`cardBackgroundColor`設`"#FFFFCC"`（淺黃色）。


## <a name="compatibility"></a>相容性

您可以使用`CardView`Android 5.0 棒棒糖符號之前的 Android 版本。 因為`CardView`是一部分的 Android v7 支援程式庫，您可以使用`CardView`與 Android 2.1 (API level 7) 及更新版本。
不過，您必須安裝`Xamarin.Android.Support.v7.CardView`封裝中所述[需求](#requirements)上述。

`CardView` 表現之前棒棒糖符號 (API level 21) 的裝置上稍微不同的行為：

-  `CardView` 使用新增其他填補的程式設計陰影實作。

-  `CardView` 不會裁剪子檢視與交集`CardView`的圓角。

若要可協助管理這些相容性的差異，`CardView`提供數個您可以設定您的版面配置中的其他屬性：

-   `cardPreventCornerOverlap` &ndash; 將此屬性設為`true`稍早 Android 版本 （API 層級 20 和更早版本） 上執行您的應用程式時，加入填補。 此設定可防止`CardView`內容從交集與`CardView`的圓角。

-   `cardUseCompatPadding` &ndash; 將此屬性設為`true`在 Android 或大於 API level 21 的版本中執行您的應用程式時，加入填補。 如果您想要使用`CardView`前棒棒糖符號裝置上，並讓它棒棒糖符號 （或更新版本） 相同的外觀，將這個屬性設定為`true`。 啟用此屬性時，`CardView`加入額外的邊框距離前棒棒糖符號裝置上執行時，繪製陰影。 這有助於克服填補引發的棒棒糖符號前程式設計陰影實作中都有效時的差異。

如需有關如何維護與舊版的 Android 相容性的詳細資訊，請參閱[維護相容性](https://developer.android.com/training/material/compatibility.html)。


## <a name="summary"></a>總結

本指南導入新`CardView`小工具隨附於 Android 5.0 （棒棒糖符號）。 它示範預設`CardView`外觀，說明如何自訂`CardView`藉由變更其權限提高、 圓角，內容與邊框距離和背景色彩。 它列出`CardView`配置屬性 （具有參考圖表），並說明如何使用`CardView`早於 Android 5.0 棒棒糖符號 Android 裝置上。 如需有關`CardView`，請參閱[CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。


## <a name="related-links"></a>相關連結

- [RecyclerView （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒棒糖符號簡介](~/android/platform/lollipop.md)
- [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
