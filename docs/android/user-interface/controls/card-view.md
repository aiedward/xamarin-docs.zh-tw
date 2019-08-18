---
title: CardView
description: Cardview widget 是一個 UI 元件, 它會在類似卡片的視圖中顯示文字和影像內容。 本指南說明如何在 Xamarin Android 應用程式中使用和自訂 CardView, 同時維持與舊版 Android 的回溯相容性。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 6daead7962e472c3c7d0b117d545efdbd3c65886
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523100"
---
# <a name="xamarinandroid-cardview"></a>Xamarin. Android CardView

_Cardview widget 是一個 UI 元件, 它會在類似卡片的視圖中顯示文字和影像內容。本指南說明如何在 Xamarin Android 應用程式中使用和自訂 CardView, 同時維持與舊版 Android 的回溯相容性。_

## <a name="overview"></a>總覽

Android `Cardview` 5.0 (棒糖) 中引進的 widget 是一個 UI 元件, 它會在類似卡片的視圖中顯示文字和影像內容。 `CardView`會實作為`FrameLayout`具有圓角和陰影的 widget。 通常會使用來呈現`ListView`或`GridView` view 群組中的單一資料列專案。 `CardView` 例如, 下列螢幕擷取畫面是一個行進保留應用程式的範例, 它會在`CardView`可滾動`ListView`的中執行以為基礎的旅遊目的地卡片:

![針對每個旅遊目的地使用 CardView 的範例應用程式](card-view-images/01-cardview-example.png)

本指南說明如何將套件新增`CardView`至您的 Xamarin Android 專案、如何新增`CardView`至您的配置, 以及如何`CardView`在應用程式中自訂的外觀。 此外, 本指南提供您可以變更之`CardView`屬性的詳細清單, 包括可協助您在 android 5.0 棒的 android 版本之前使用`CardView`的屬性。

<a name="requirements" />

## <a name="requirements"></a>需求

在 Xamarin 應用程式中使用新的 Android 5.0 和更新版本的`CardView`功能 (包括) 時, 需要下列各項:

- 您必須安裝並設定 Visual Studio 或 Visual Studio for Mac 的**xamarin android** &ndash; xamarin. android 4.20 或更新版本。

- **Android SDK**&ndash; Android 5.0 (API 21) 或更新版本必須透過 Android SDK Manager 安裝。

- **JAVA JDK 1.8**&ndash;如果您特別瞄準 API 層級23和更早版本, 則可以使用 JDK 1.7。 JDK 1.8 可從[Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)取得。

您的`Xamarin.Android.Support.v7.CardView`應用程式也必須包含套件。 若要在`Xamarin.Android.Support.v7.CardView` Visual Studio for Mac 中新增封裝:

1. 開啟專案, 以滑鼠右鍵按一下 [**套件**], 然後選取 [**新增套件**]。

2. 在 [**新增套件**] 對話方塊中, 搜尋**CardView**。

3. 選取 [ **Xamarin 支援程式庫 V7 CardView**], 然後按一下 [**新增套件**]。

若要在`Xamarin.Android.Support.v7.CardView` Visual Studio 中新增封裝:

1. 開啟您的專案, 以滑鼠右鍵按一下 [**參考**] 節點 (在 [**方案總管**] 窗格中), 然後選取 [**管理 NuGet 套件 ...** ]。

2. 顯示 [**管理 NuGet 封裝**] 對話方塊時, 請在搜尋方塊中輸入**CardView** 。

3. 出現 [ **Xamarin 支援程式庫 V7 CardView** ] 時, 按一下 [**安裝**]。

若要瞭解如何設定 Android 5.0 應用程式專案, 請參閱[設定 android 5.0 專案](~/android/platform/lollipop.md)。
如需安裝 NuGet 套件的詳細資訊, [請參閱逐步解說:在您的專案](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)中包含 NuGet。


## <a name="introducing-cardview"></a>CardView 簡介

預設`CardView`類似于具有最小圓角和輕微陰影的白色卡片。 下列範例**axml**版面配置會顯示單一`CardView` `TextView`widget, 其中包含:

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

如果您使用此 XML 來取代**axml**的現有內容, 請務必將**MainActivity.cs**中參考先前 XML 資源的任何程式碼標記為批註。

此配置範例會建立具有`CardView`一行文字的預設值, 如下列螢幕擷取畫面所示:

[![具有白色背景和行文字的 CardView 螢幕擷取畫面](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此範例中, 應用程式樣式設定為 [淺色材質] 主題`Theme.Material.Light`(), 讓`CardView`陰影和邊緣更容易看到。 如需有關 Android 5.0 應用程式主題的詳細資訊, 請參閱[材質主題](~/android/user-interface/material-theme.md)。 在下一節中, 我們將瞭解如何自`CardView`定義應用程式。


## <a name="customizing-cardview"></a>自訂 CardView

您可以修改基本`CardView`屬性, `CardView`在您的應用程式中自訂的外觀。 例如, 的提高許可權`CardView`可以增加以轉換較大的陰影 (這會使卡片看起來像是在背景上方的浮動位置)。 此外, 圓角半徑也可以增加, 讓卡片的角落更圓化。

在下一個版面配置範例中, `CardView`會使用自訂的來建立列印相片的模擬 (「快照集」)。 會將新增`CardView`至, 以顯示影像, 而`TextView`位於下方`ImageView`的會顯示影像的標題。 `ImageView`
在此範例版面配置中`CardView` , 具有下列自訂:

- `cardElevation`會增加至4dp 以轉換較大的陰影。
- `cardCornerRadius`會增加至 5dp, 讓角落顯得更圓化。

因為`CardView`是由 Android v7 支援程式庫提供, 所以其屬性無法`android:`從命名空間使用。 因此, 您必須定義自己的 XML 命名空間, 並使用該命名`CardView`空間做為屬性前置詞。 在下面的版面配置範例中, 我們將使用這一行來定義名`cardview`為的命名空間:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以呼叫這個命名`card_view`空間, `myapp`或即使您選擇 (只能在此檔案的範圍記憶體取)。 無論您選擇呼叫這個命名空間的方式為何, 都必須使用它`CardView`來為您要修改的屬性加上前置詞。 在此版面配置範例中`cardview` , 命名空間是`cardElevation`和`cardCornerRadius`的前置詞:

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

當此配置範例用來在相片觀賞應用程式中顯示影像時, `CardView`會具有相片快照的外觀, 如下列螢幕擷取畫面所示:

[![影像下方有影像和標題的 CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

這個螢幕擷取畫面是取自[RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)範例應用程式, 它會使用`RecyclerView` widget 來呈現影像的滾動清單`CardView`來觀看相片。 如需的詳細`RecyclerView`資訊, 請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)指南。

請注意, `CardView`可以在其內容區域中顯示一個以上的子視圖。 例如, 在上述相片觀看應用程式範例中, 內容區域是由`ListView` `ImageView`包含和`TextView`的組成。 雖然`CardView`實例通常會垂直排列, 但您也可以水準排列它們 (如需範例螢幕擷取畫面, 請參閱[建立自訂視圖樣式](~/android/user-interface/material-theme.md#customview))。


### <a name="cardview-layout-options"></a>CardView 版面配置選項

`CardView`您可以藉由設定一或多個會影響其填補、提高許可權、圓角半徑和背景色彩的屬性來自訂版面配置:

[![CardView 屬性的圖表](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

您也可以藉由呼叫互相對應`CardView`的方法, 以動態方式變更每個屬性 (如需`CardView`方法的詳細資訊, 請參閱[CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html))。
請注意, 這些屬性 (背景色彩除外) 接受維度值, 也就是十進位數, 後面接著單位。 例如, `11.5dp`指定11.5 與密度無關的圖元。


#### <a name="padding"></a>與邊框距離

`CardView`提供五個填補屬性來定位卡片內的內容。 您可以在版面配置 XML 中設定它們, 也可以在程式碼中呼叫類似的方法:

[![CardView 填補屬性的圖表](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填補屬性的說明如下:

- `contentPadding`&ndash;子視圖`CardView`與卡片所有邊緣之間的內部填補。

- `contentPaddingBottom`&ndash;子視圖`CardView`與卡片下邊緣之間的內部填補。

- `contentPaddingLeft`&ndash;子視圖`CardView`與卡片左邊緣之間的內部填補。

- `contentPaddingRight`&ndash;子視圖`CardView`與卡片右邊緣之間的內部填補。

- `contentPaddingTop`&ndash;子視圖`CardView`與卡片上邊緣之間的內部填補。

內容填補屬性是相對於內容區域的界限, 而不是位於內容區域內的任何指定 widget。
例如, 如果`contentPadding`在相片觀賞應用程式中已足夠增加, 則`CardView`會裁剪影像和卡片上顯示的文字。



#### <a name="elevation"></a>高度

`CardView`提供兩個提高許可權的屬性來控制其提升許可權, 並以其為其陰影的大小而產生:

[![CardView 提升許可權屬性的圖表](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

提高許可權屬性的說明如下:

- `cardElevation`&ndash;的高度`CardView` (代表其 Z 軸)。

- `cardMaxElevation`提高許可權的最大`CardView`值。 &ndash;

較大的`cardElevation`值會增加陰影大小, `CardView`使其看起來會高於背景上方的浮動。 屬性也會決定重迭視圖的繪製順序; 也就是說`CardView` , 會在另一個具有較高提高許可權設定的重迭視圖下繪製, 並在具有較低許可權設定的任何重迭視圖上方。 `cardElevation`
此設定適用于當您的應用程式以動態&ndash;方式變更提高許可權時, 它會防止陰影延伸超過您使用此設定所定義的限制。 `cardMaxElevation`


#### <a name="corner-radius-and-background-color"></a>圓角半徑和背景色彩

`CardView`提供可用來控制其圓角半徑和背景色彩的屬性。 這兩個屬性可讓您變更的整體樣式`CardView`:

[![CardView 角落 radious 和背景色彩屬性的圖表](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

這些屬性的說明如下:

- `cardCornerRadius`所有角落的圓角半徑。 `CardView` &ndash;

- `cardBackgroundColor`的背景色彩`CardView`。 &ndash;

在此圖表中`cardCornerRadius` , 會設定為更圓角的`cardBackgroundColor` 10dp, 並`"#FFFFCC"`設定為 (淺黃色)。


## <a name="compatibility"></a>相容性

您可以使用`CardView` android 5.0 棒的 android 以前版本。 因為`CardView`是 android v7 支援程式庫的一部分, 所以您可以`CardView`搭配 android 2.1 (API 層級 7) 和更高版本使用。
不過, 您必須依照上述`Xamarin.Android.Support.v7.CardView` [需求](#requirements)中所述的方式安裝套件。

`CardView`在趨勢棒 (API 層級 21) 之前, 裝置上的行為稍有不同:

- `CardView`使用以程式設計的陰影執行來增加額外的填補。

- `CardView`不會裁剪與`CardView`的圓角交集的子視圖。

為了協助管理這些相容性差異, `CardView`提供數個您可以在配置中設定的其他屬性:

- `cardPreventCornerOverlap`當您的應用`true`程式在較舊的 Android 版本上執行時, 將此屬性設定為以新增填補 (API 層級20和更早版本)。 &ndash; 此設定可`CardView`防止內容`CardView`與的圓角相交。

- `cardUseCompatPadding`當您的應用`true`程式在 Android 版本或高於 API 層級21執行時, 請將此屬性設定為以加入填補。 &ndash; 如果您想要在`CardView`棒型裝置上使用, 並讓它在棒糖 (或更新版本) 上看起來相同, `true`請將此屬性設定為。 當此屬性啟用時, `CardView`會加入額外的填補, 以在在棒式裝置上執行時繪製陰影。 這有助於克服當棒式程式設計的陰影執行效果生效時, 所引進的填補差異。

如需維護與舊版 Android 相容性的詳細資訊, 請參閱[維護相容性](https://developer.android.com/training/material/compatibility.html)。


## <a name="summary"></a>總結

本指南引進了 Android `CardView` 5.0 (棒棒) 中包含的新 widget。 它示範了預設`CardView`外觀, 並說明如何藉`CardView`由變更其提高許可權、角落圓度、內容填補和背景色彩來自訂。 它列出了`CardView`版面配置屬性 (使用參考圖表), 並說明如何在`CardView` android 5.0 棒的 android 裝置上使用。 如需的詳細`CardView`資訊, 請參閱[CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。


## <a name="related-links"></a>相關連結

- [RecyclerView (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒糖的簡介](~/android/platform/lollipop.md)
- [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
