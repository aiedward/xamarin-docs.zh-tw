---
title: CardView
description: Cardview 小工具會提供類似於卡片的檢視中的文字和影像內容的 UI 元件。 本指南說明如何使用和自訂 CardView 在 Xamarin.Android 應用程式，同時維持與舊版 Android 的回溯相容性。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 019d89261687d1139ebced9400afbdf5eaf7a128
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109194"
---
# <a name="cardview"></a>CardView

_Cardview 小工具會提供類似於卡片的檢視中的文字和影像內容的 UI 元件。本指南說明如何使用和自訂 CardView 在 Xamarin.Android 應用程式，同時維持與舊版 Android 的回溯相容性。_


## <a name="overview"></a>總覽

`Cardview`小工具之後，Android 5.0 (Lollipop) 中導入是一種 UI 元件，類似於卡片的檢視中顯示的文字和影像的內容。 `CardView` 會實作為`FrameLayout`小工具具有圓的角和陰影。 通常`CardView`用來顯示中的單一資料列項目`ListView`或`GridView`檢視群組。 例如，下列螢幕擷取畫面是旅遊預約應用程式實作的範例`CardView`為基礎的移動目的地卡在可捲動`ListView`:

![CardView 用於每個移動目的地的範例應用程式](card-view-images/01-cardview-example.png)

本指南說明如何新增`CardView`封裝，您的 xamarin.android 專案時，如何新增`CardView`至您的配置，以及如何自訂外觀`CardView`應用程式中。 此外，本指南提供的詳細的清單`CardView`您可以變更，包括可協助您使用的屬性的屬性`CardView`Android 5.0 Lollipop 之前的 Android 版本上。

<a name="requirements" />

## <a name="requirements"></a>需求

下列，才能使用新的 Android 5.0 和更新版本的功能 (包括`CardView`) 在以 Xamarin 為基礎的應用程式中：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定與 Visual Studio 或 Visual Studio for mac。

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須安裝 Android SDK 管理員透過。

-  **Java JDK 1.8** &ndash;可以使用 JDK 1.7，如果您是特別瞄準的 API 層級 23 和更早版本。 JDK 1.8 是可從[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

您的應用程式也必須包含`Xamarin.Android.Support.v7.CardView`封裝。 若要新增`Xamarin.Android.Support.v7.CardView`Visual Studio for Mac 中的封裝：

1. 開啟您的專案，以滑鼠右鍵按一下**封裝**，然後選取**新增套件**。

2. 在 **新增套件**對話方塊中，搜尋**CardView**。

3. 選取  **Xamarin 支援程式庫 v7 CardView**，然後按一下**加入封裝**。

若要新增`Xamarin.Android.Support.v7.CardView`Visual Studio 中的封裝：

1. 開啟您的專案，以滑鼠右鍵按一下**參考**節點 (在**方案總管**窗格)，然後選取**管理 NuGet 套件...**.

2. 當**管理 NuGet 套件**就會顯示對話方塊中，輸入**CardView**在搜尋方塊中。

3. 當**Xamarin 支援程式庫 v7 CardView**出現時，請按一下 **安裝**。

若要了解如何設定 Android 5.0 應用程式專案，請參閱[設定註冊的 Android 5.0 專案](~/android/platform/lollipop.md)。
如需有關如何安裝 NuGet 套件的詳細資訊，請參閱 <<c0> [ 逐步解說： 在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


## <a name="introducing-cardview"></a>簡介 CardView

預設值`CardView`就像具有最少圓的角和陰影白色卡片。 下例**Main.axml**版面配置會顯示單一`CardView`包含的小工具`TextView`:

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

如果您使用這個 XML 來取代現有的內容**Main.axml**，請務必在任何程式碼註解**MainActivity.cs**參考先前在 XML 中的資源。

此配置範例會建立預設`CardView`使用單行文字，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面的白色背景和文字行 CardView](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此範例中，應用程式樣式設定為淺材料佈景主題 (`Theme.Material.Light`)，讓`CardView`陰影和邊緣都看得更清楚。 如需佈景主題 Android 5.0 應用程式的詳細資訊，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。 在下一步 區段中，我們將了解如何自訂`CardView`應用程式。


## <a name="customizing-cardview"></a>自訂 CardView

您可以修改基本`CardView`屬性以自訂的外觀`CardView`應用程式中。 比方說，提高權限的`CardView`可增加到較大的陰影 （可讓卡片似乎背景上較高的浮點數）。 此外，您可以增加圓角半徑設為圓角的更多的卡片捨入。

在下一步 配置範例中，自訂`CardView`用來建立列印照片 （「 快照集 」） 的模擬。 `ImageView`新增至`CardView`顯示映像，以及`TextView`位於以下`ImageView`顯示的影像標題。
在此範例版面配置，`CardView`具有下列自訂：

-  `cardElevation`會增加至 4dp 轉換較大的陰影。
-  `cardCornerRadius`會增加至 5dp 設為圓角會出現一個圓角。

因為`CardView`係由 Android v7 支援程式庫，其屬性所沒有的`android:`命名空間。 因此，您必須定義您自己的 XML 命名空間，並使用該命名空間，做為`CardView`屬性前置詞。 在版面配置下列範例中，我們將使用這一行，來定義命名空間稱為`cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以呼叫此命名空間`card_view`甚至`myapp`如果您選擇 （它是只在這個檔案的範圍內存取的）。 所選擇的內容來呼叫此命名空間，您必須使用它的前置詞`CardView`您想要修改的屬性。 在此配置範例中，`cardview`命名空間是前置詞`cardElevation`和`cardCornerRadius`:

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

當此版面配置範例用來在相片檢視應用程式中，顯示映像`CardView`具有相片的快照集，外觀，如下列螢幕擷取畫面所示：

[![使用影像和標題影像下方的 CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

此螢幕擷取畫面取自[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)範例應用程式，它會使用`RecyclerView`小工具來展示捲動清單`CardView`檢視相片的映像。 如需詳細資訊`RecyclerView`，請參閱 < [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)指南。

請注意，`CardView`可以在其內容的區域中顯示一個以上的子檢視。 例如，在上述的相片，瀏覽應用程式範例，內容區域組成`ListView`，其中包含`ImageView`和`TextView`。 雖然`CardView`執行個體通常會以垂直方式排列，您也可以排列它們以水平方式 (請參閱 <<c2> [ 建立自訂檢視樣式](~/android/user-interface/material-theme.md#customview)的範例螢幕擷取畫面)。


### <a name="cardview-layout-options"></a>CardView 版面配置選項

`CardView` 您可以自訂版面配置，設定會影響其邊框距離、 提高權限、 圓角半徑和背景色彩的一或多個屬性：

[![CardView 屬性的圖表](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

每個屬性也可以變更動態藉由呼叫對應`CardView`方法 (如需詳細資訊`CardView`方法，請參閱[CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html))。
請注意這些屬性 （除了背景色彩） 接受維度值，也就是十進位數字，後面接著的單位。 比方說，`11.5dp`指定 11.5 密度獨立像素為單位。


#### <a name="padding"></a>與邊框距離
`
CardView` 提供五個填補屬性，將卡片內的內容。 您可以在您的版面配置 XML 中設定它們，或者您可以在程式碼中呼叫的類似方法：

[![CardView 填補屬性的圖表](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

邊框間距屬性說明，如下所示：

-  `contentPadding` &ndash; 內部的子檢視之間的填補`CardView`和智慧卡的所有邊緣。

-  `contentPaddingBottom` &ndash; 內部的子檢視之間的填補`CardView`與卡片的下邊緣。

-  `contentPaddingLeft` &ndash; 內部的子檢視之間的填補`CardView`和左邊的緣的卡片。

-  `contentPaddingRight` &ndash; 內部的子檢視之間的填補`CardView`與右邊緣的卡片。

-  `contentPaddingTop` &ndash; 內部的子檢視之間的填補`CardView`和卡片上的邊緣。

內容的填補屬性都是相對於內容區域，而不是位於 [內容] 區域內的任何給定的小工具的界限。
例如，如果`contentPadding`夠相片檢視應用程式中，已增加`CardView`會裁剪影像和卡片上所顯示的文字。



#### <a name="elevation"></a>提高權限

`CardView` 提供兩個的提高權限屬性，來控制它的提高權限，如此一來，及其陰影的大小：

[![CardView 提高權限屬性的圖表](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

說明的提高權限屬性，如下所示：

-  `cardElevation` &ndash; 權限提高`CardView`（代表其 Z 軸）。

-  `cardMaxElevation` &ndash; 最大值`CardView`的提高權限。

較大的值`cardElevation`增加陰影大小`CardView`似乎背景上較高的浮點數。 `cardElevation`屬性也會決定重疊檢視的繪製順序，亦即，`CardView`將另一個重疊檢視較高的提高權限設定或更新版本使用較低的提高權限設定的任何重疊檢視底下繪製。
`cardMaxElevation`設定可用於當您的應用程式變更提高權限以動態方式&ndash;它就會防止陰影擴充超過您定義這項設定的限制。


#### <a name="corner-radius-and-background-color"></a>圓角半徑和背景色彩

`CardView` 提供屬性，您可以用來控制其邊角半徑和它的背景色彩。 這兩個屬性可讓您變更整體樣式`CardView`:

[![CardView 角 radious 和背景色彩屬性的圖表](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

這些屬性會說明，如下所示：

-  `cardCornerRadius` &ndash; 各個角落的圓角半徑`CardView`。

-  `cardBackgroundColor` &ndash; 背景色彩`CardView`。

在此圖中，`cardCornerRadius`設為圓角更 10dp 並`cardBackgroundColor`設定為`"#FFFFCC"`（淺黃色）。


## <a name="compatibility"></a>相容性

您可以使用`CardView`Android 5.0 Lollipop 之前的 Android 版本上。 因為`CardView`屬於 Android v7 支援程式庫中，您可以使用`CardView`包含 Android 2.1 （API 層級 7） 和更新版本。
不過，您必須安裝`Xamarin.Android.Support.v7.CardView`封裝中所述[需求](#requirements)上面。

`CardView` 表現出之前 Lollipop (API level 21) 的裝置上有稍微不同的行為：

-  `CardView` 使用程式設計的陰影實作，會新增其他填補。

-  `CardView` 不會裁剪與交集的子檢視`CardView`的圓角。

若要可協助管理這些相容性的差異，`CardView`提供數個其他的屬性，您可以在您的版面配置設定：

-   `cardPreventCornerOverlap` &ndash; 將此屬性設定為`true`稍早的 Android 版本 （API 層級 20 和更早版本） 上執行您的應用程式時，加入填補。 此設定可防止`CardView`內容從使用交集`CardView`的圓角。

-   `cardUseCompatPadding` &ndash; 將此屬性設定為`true`到您的應用程式在 Android 或大於 API level 21 的版本在執行時，加上邊框間距。 如果您想要使用`CardView`前的棒棒糖符號裝置上，並讓它看起來相同棒棒糖符號 （或更新版本上），將此屬性設定為`true`。 啟用此屬性時，`CardView`加入額外的邊框距離在前的棒棒糖符號的裝置上執行時，繪製陰影。 這有助於克服前棒棒糖符號以程式設計方式陰影實作會有作用時導入的填補的差異。

如需有關如何維護與舊版的 Android 的相容性的詳細資訊，請參閱[維護相容性](https://developer.android.com/training/material/compatibility.html)。


## <a name="summary"></a>總結

本指南引進了新`CardView`小工具隨附於 Android 5.0 (Lollipop)。 課程中示範了預設值`CardView`外觀，說明如何自訂`CardView`藉由變更其權限提高、 圓角，內容與邊框距離，，和背景色彩。 其列出`CardView`版面配置屬性 （具有參考圖表），並說明如何使用`CardView`早於 Android 5.0 Lollipop Android 裝置上。 如需詳細資訊`CardView`，請參閱 < [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。


## <a name="related-links"></a>相關連結

- [RecyclerView （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒棒糖符號的簡介](~/android/platform/lollipop.md)
- [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
