---
title: ListView 組件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 3ab7a923dabd6b98c509870abaa51b12fb63c8d2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510128"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Xamarin. Android ListView 元件和功能

`ListView`包含下列元件:

- 資料**列**&ndash;清單中資料的可見標記法。

- **介面卡**&ndash;將資料來源系結至清單視圖的非視覺類別。

- **快速滾動**&ndash;可讓使用者滾動清單長度的控制碼。

- **區段索引**&ndash;使用者介面專案, 這個專案會浮動在滾動列上方, 以指出目前資料列在清單中的所在位置。

這些螢幕擷取畫面會使用`ListView`基本控制項來顯示如何呈現快速滾動和區段索引:

[![使用一般舊資料列、快速滾動和區段索引的應用程式螢幕擷取畫面](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

組成的元素`ListView`會在下面更詳細地說明:


## <a name="rows"></a>列

每個資料列都`View`有自己的。 此視圖可以是中`Android.Resources`所定義的其中一個內建視圖, 或是自訂視圖。 每個資料列都可以使用相同的 view 版面配置, 或兩者都可以不同。 本檔中的範例使用內建版面配置, 以及說明如何定義自訂版面配置的其他內容。


## <a name="adapter"></a>配接器

控制項需要提供每個資料列的`View`格式化。 `ListView` `Adapter` Android 具有可以使用的內建介面卡和視圖, 或可建立自訂類別。


## <a name="fast-scrolling"></a>快速滾動

`ListView`當包含許多資料列時, 可以啟用快速滾動功能, 以協助使用者流覽至清單的任何部分。 快速滾動的「捲軸」可以選擇性地啟用 (並在 API 層級11和更新版本中自訂)。


## <a name="section-index"></a>區段索引

當滾動長清單時, 選擇性的區段索引會提供使用者對目前正在觀看之清單中哪些部分的意見反應。 這只適用于長清單, 通常與快速滾動搭配使用。


## <a name="classes-overview"></a>類別總覽

用來顯示`ListViews`的主要類別如下所示:

[![說明 ListView 與相關類別之間關聯性的 UML 圖表](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

每個類別的用途如下所述:

- **ListView**&ndash;顯示可滾動資料列集合的使用者介面元素。 在手機上, 它通常會使用整個畫面 (在此情況下`ListActivity` , 可以使用類別), 也可能是手機或平板電腦裝置上較大版面配置的一部分。

- **View**Android 中的視圖可以是任何使用者介面專案, 但在的內容`ListView`中, 需要`View`為每個資料列提供。 &ndash;

- **BaseAdapter**&ndash; 用`ListView`來將系結至資料來源之介面卡執行的基類。

- **ArrayAdapter**內建介面卡類別, 會將字串陣列系結`ListView`至以供顯示。 &ndash; 一般`ArrayAdapter<T>`會針對其他類型執行相同的工作。

- **CursorAdapter**&ndash;使用或來`SimpleCursorAdapter`顯示以 SQLite 查詢為基礎的資料。 `CursorAdapter`

本檔包含的簡單範例, 會`ArrayAdapter`使用和需要自訂`BaseAdapter`或`CursorAdapter`的更複雜範例。

