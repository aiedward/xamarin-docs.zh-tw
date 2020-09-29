---
title: CardView
description: Cardview 小工具是 UI 元件，可在類似卡片的視圖中提供文字和影像內容。 本指南說明如何在 Xamarin. Android 應用程式中使用和自訂 CardView，同時維持與舊版 Android 的回溯相容性。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d378b48741b6640f342d51e6eaa16aaf1a2f74a5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457454"
---
# <a name="xamarinandroid-cardview"></a>Xamarin. Android CardView

_Cardview 小工具是 UI 元件，可在類似卡片的視圖中提供文字和影像內容。本指南說明如何在 Xamarin. Android 應用程式中使用和自訂 CardView，同時維持與舊版 Android 的回溯相容性。_

## <a name="overview"></a>概觀

`Cardview`在 Android 5.0 中引進的 widget (棒卡) ）是一個 UI 元件，可在類似卡片的視圖中提供文字和影像內容。 `CardView` 會實作為 `FrameLayout` 具有圓角和陰影的小工具。 一般而言， `CardView` 會用來在 `ListView` 或 view 群組中顯示單一資料列專案 `GridView` 。 例如，下列螢幕擷取畫面是在可滾動的情況下，實作為旅遊目的地卡片的旅遊預約應用程式範例 `CardView` `ListView` ：

![針對每個行進目的地使用 CardView 的範例應用程式](card-view-images/01-cardview-example.png)

本指南說明如何將套件新增 `CardView` 至您的 Xamarin. Android 專案、如何新增 `CardView` 至您的版面配置，以及如何 `CardView` 在應用程式中自訂的外觀。 此外，本指南 `CardView` 也提供您可以變更之屬性的詳細清單，包括可協助您 `CardView` 在 Android 5.0 棒棒的 android 版本上使用的屬性。

<a name="requirements"></a>

## <a name="requirements"></a>需求

若要使用新的 Android 5.0 和更新版本功能， (包括 `CardView` 以 Xamarin 為基礎的應用程式中) ，需要下列各項：

- **Xamarin.Android**您 &ndash; 必須安裝 Visual Studio 或 Visual Studio for Mac，才能安裝並設定 Xamarin. Android xamarin. android 4.20 或更新版本。

- **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須透過 Android SDK 管理員安裝。

- **JAVA JDK 1.8** &ndash; 如果您特別以 API 層級23及更早的版本為目標，則可以使用 JDK 1.7。 JDK 1.8 可從 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)取得。

您的應用程式也必須包含 `Xamarin.Android.Support.v7.CardView` 套件。 若要 `Xamarin.Android.Support.v7.CardView` 在 Visual Studio for Mac 中新增封裝：

1. 開啟您的專案，以滑鼠右鍵按一下 [ **封裝** ]，然後選取 [ **新增套件**]。

2. 在 [ **新增封裝** ] 對話方塊中，搜尋 **CardView**。

3. 選取 [ **Xamarin 支援程式庫 V7 CardView**]，然後按一下 [ **新增套件**]。

若要 `Xamarin.Android.Support.v7.CardView` 在 Visual Studio 中新增封裝：

1. 開啟您的專案，以滑鼠右鍵按一下 [**方案總管**] 窗格中 (的 [**參考**) ] 節點，然後選取 [**管理 NuGet 套件 ...**]。

2. 顯示 [ **管理 NuGet 封裝** ] 對話方塊時，請在 [搜尋] 方塊中輸入 **CardView** 。

3. 當 [ **Xamarin 支援程式庫 V7 CardView** ] 出現時，按一下 [ **安裝**]。

若要瞭解如何設定 Android 5.0 應用程式專案，請參閱 [設定 android 5.0 專案](~/android/platform/lollipop.md)。
如需安裝 NuGet 套件的詳細資訊，請參閱 [逐步解說：在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)。

## <a name="introducing-cardview"></a>CardView 簡介

預設值 `CardView` 類似于具有最小圓角和輕微陰影的白色卡。 下列範例 **.axml** 版面配置會顯示一個 `CardView` 小工具，其中包含 `TextView` ：

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

如果您使用這個 XML 來取代 **.axml**的現有內容，請務必將 **MainActivity.cs** 中任何參考先前 XML 資源的程式碼標記為批註。

這個版面配置範例會 `CardView` 使用單行文字來建立預設值，如下列螢幕擷取畫面所示：

[![具有白色背景和文字行之 CardView 的螢幕擷取畫面](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此範例中，應用程式樣式設定為 [淺色材質] 主題 (`Theme.Material.Light`) ，讓 `CardView` 陰影和邊緣更容易查看。 如需有關 Android 5.0 應用程式主題的詳細資訊，請參閱 [材質主題](~/android/user-interface/material-theme.md)。 在下一節中，我們將瞭解如何自訂 `CardView` 應用程式。

## <a name="customizing-cardview"></a>自訂 CardView

您可以修改基本 `CardView` 屬性，以自訂 `CardView` 應用程式中的外觀。 例如，的 `CardView` 提高許可權可以增加以轉型較大的陰影 (這會使卡片看起來像背景) 更高的位置。 此外，也可以增加圓角半徑，讓卡片的角落更四捨五入。

在下一個版面配置範例中， `CardView` 會使用自訂的來建立 (「快照」 ) 的列印相片模擬。 會在中加入，以 `ImageView` `CardView` 顯示影像，而將放置在下方，以 `TextView` `ImageView` 顯示影像的標題。
在此範例版面配置中， `CardView` 有下列自訂專案：

- `cardElevation`會增加至4dp，以轉換較大的陰影。
- `cardCornerRadius`會增加至5dp，使角落顯示更圓角。

因為 `CardView` 是由 Android v7 支援程式庫所提供，所以無法從命名空間使用其屬性 `android:` 。 因此，您必須定義自己的 XML 命名空間，並使用該命名空間作為 `CardView` 屬性前置詞。 在以下的版面配置範例中，我們將使用這一行來定義命名空間，稱為 `cardview` ：

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以呼叫此命名空間， `card_view` 或即使 `myapp` 您選擇 (只能在這個檔案的範圍記憶體取它) 。 無論您選擇呼叫此命名空間的方式為何，您都必須使用它來為您要修改的屬性加上前置詞 `CardView` 。 在此版面配置範例中， `cardview` 命名空間是和的前置詞 `cardElevation` `cardCornerRadius` ：

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

當這個版面配置範例用來在相片觀看應用程式中顯示影像時，會 `CardView` 出現相片快照集的外觀，如下列螢幕擷取畫面所示：

[![影像下方的影像和標題 CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

此螢幕擷取畫面取自 [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer) 範例應用程式，此應用程式會使用 `RecyclerView` widget 來呈現影像的滾動清單，以供您用來 `CardView` 查看相片。 如需的詳細資訊 `RecyclerView` ，請參閱 [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) 指南。

請注意， `CardView` 可以在其內容區域中顯示一個以上的子視圖。 例如，在上述的相片觀看應用程式範例中，內容區域是由 `ListView` 包含 `ImageView` 與的 `TextView` 。 雖然 `CardView` 實例通常會垂直排列，但您也可以水準排列它們 (請參閱為範例螢幕擷取畫面) [建立自訂視圖樣式](~/android/user-interface/material-theme.md#customview) 。

### <a name="cardview-layout-options"></a>CardView 版面配置選項

`CardView` 您可以藉由設定一或多個影響其填補、提高許可權、圓角半徑和背景色彩的屬性來自訂版面配置：

[![CardView 屬性的圖表](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

您也可以呼叫互相對應的方法，以動態方式變更每個屬性 `CardView` (如需方法的詳細資訊 `CardView` ，請參閱 [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)) 。
請注意，這些屬性 (背景色彩) 接受維度值，也就是十進位數，後面接著單位。 例如， `11.5dp` 指定11.5 密度獨立圖元。

#### <a name="padding"></a>填補

`CardView` 提供五個填補屬性來放置卡片內的內容。 您可以在版面配置 XML 中設定它們，也可以在程式碼中呼叫類似的方法：

[![CardView 填補屬性的圖表](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填補屬性的說明如下所示：

- `contentPadding`&ndash;和卡片所有邊緣的子視圖之間的內部填補 `CardView` 。

- `contentPaddingBottom`的 &ndash; 子視圖與 `CardView` 卡片下邊緣之間的內部填補。

- `contentPaddingLeft`的 &ndash; 子視圖與 `CardView` 卡片左邊緣之間的內部填補。

- `contentPaddingRight`的 &ndash; 子視圖與 `CardView` 卡片右邊緣之間的內部填補。

- `contentPaddingTop`的 &ndash; 子視圖與 `CardView` 卡片上邊緣之間的內部填補。

內容填補屬性是相對於內容區域的界限，而不是位於內容區域內的任何指定 widget。
例如，如果 `contentPadding` 相片觀賞應用程式中已足夠增加，則 `CardView` 會裁剪影像和卡片上顯示的文字。

#### <a name="elevation"></a>Elevation

`CardView` 提供兩個提高許可權的屬性來控制其提高許可權，因此其陰影的大小如下：

[![CardView 提高許可權屬性的圖表](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

提高許可權屬性的說明如下：

- `cardElevation`&ndash; (的提高許可權 `CardView` 代表其 Z 軸) 。

- `cardMaxElevation`提高 &ndash; 許可權的最大值 `CardView` 。

較大的值會 `cardElevation` 增加陰影大小，使其 `CardView` 看似在背景上方更高的浮點數。 `cardElevation`屬性也會決定重迭視圖的繪製順序; 也就是說，將會 `CardView` 在另一個具有較高許可權設定的重迭視圖下，以及在具有較低許可權設定的重迭視圖上方繪製。
`cardMaxElevation`當您的應用程式以動態方式變更提高許可權時，此設定會很有用， &ndash; 因為它可防止陰影延伸超過您使用此設定所定義的限制。

#### <a name="corner-radius-and-background-color"></a>圓角半徑和背景色彩

`CardView` 提供屬性，可讓您用來控制其邊角半徑和其背景色彩。 這兩個屬性可讓您變更的整體樣式 `CardView` ：

[![CardView 邊角 radious 和背景色彩屬性的圖表](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

這些屬性的說明如下所示：

- `cardCornerRadius`&ndash;所有角落的圓角半徑 `CardView` 。

- `cardBackgroundColor`的 &ndash; 背景色彩 `CardView` 。

在此圖中， `cardCornerRadius` 會設定為較大的10dp，並 `cardBackgroundColor` 設定為 `"#FFFFCC"` (亮黃色) 。

## <a name="compatibility"></a>相容性

您可以使用 `CardView` android 5.0 或更早版本的 android 版本。 因為 `CardView` 是 android v7 支援程式庫的一部分，所以您可以搭配 `CardView` android 2.1 (API 層級 7) 和更高版本使用。
不過，您必須 `Xamarin.Android.Support.v7.CardView` 如上面的 [需求](#requirements)所述安裝套件。

`CardView` 在棒 (API 層級 21) 之前，裝置上會出現稍微不同的行為：

- `CardView` 使用可新增額外填補的程式設計陰影執行。

- `CardView` 不會裁剪與圓角相交的子視圖  `CardView` 。

為了協助管理這些相容性差異， `CardView` 提供數個可在版面配置中設定的其他屬性：

- `cardPreventCornerOverlap`&ndash; `true` 當您的應用程式在舊版 Android 版本上執行時，將此屬性設定為以新增填補 (API 層級20及更早的) 。 這種設定可防止 `CardView` 內容與 `CardView` 圓角的交集。

- `cardUseCompatPadding`&ndash;將此屬性設定為， `true` 以在您的應用程式在 Android 版本或大於 API 層級21執行時新增填補。 如果您想要 `CardView` 在棒棒的裝置上使用，並讓它在棒 (或更新版本) 上看起來相同，請將此屬性設定為 `true` 。 當啟用這個屬性時，會在 `CardView` 測試裝置上執行時，新增額外的填補來繪製陰影。 這有助於克服當棒型程式設計陰影實現生效時所引進的填補差異。

如需維護與舊版 Android 相容性的詳細資訊，請參閱 [維持相容性](https://developer.android.com/training/material/compatibility.html)。

## <a name="summary"></a>摘要

本指南引進了 `CardView` Android 5.0 (棒) 的新 widget。 它示範了預設 `CardView` 外觀，並說明如何藉 `CardView` 由變更其提高許可權、邊角圓度、內容填補和背景色彩來進行自訂。 它列出了 `CardView` 參考圖表 (的版面配置屬性) ，並說明如何 `CardView` 在 Android 5.0 棒棒的 android 裝置上使用。 如需的詳細資訊 `CardView` ，請參閱 [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。

## <a name="related-links"></a>相關連結

- [RecyclerView (範例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒棒的簡介](~/android/platform/lollipop.md)
- [CardView 類別參考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)