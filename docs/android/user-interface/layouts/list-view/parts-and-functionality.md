---
title: ListView 組件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: ec9b6583cac9478957e14f709c7c7ee8eb273cbc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="listview-parts-and-functionality"></a>ListView 組件和功能


## <a name="overview"></a>總覽

A`ListView`包含下列部分：

- **資料列**&ndash;可見的表示法的清單中的資料。

- **配接器**&ndash;將資料來源繫結至 [清單] 檢視的非視覺化類別。

- **快速捲動**&ndash;的控制代碼，可讓使用者捲動清單的長度。

- **主題索引**&ndash;漂浮在捲動的使用者介面項目以指出清單中目前資料列的所在位置的資料列。

這些螢幕擷取畫面使用基本`ListView`控制項以顯示快速捲動和區段索引的呈現方式：

[![使用一般舊資料列，應用程式的螢幕擷取畫面快速捲動和區段索引](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

構成項目`ListView`以下更詳細說明：


## <a name="rows"></a>列

每個資料列都有它自己`View`。 檢視可以是內建的檢視中定義的其中一個`Android.Resources`，或自訂的檢視。 每個資料列可以使用相同的檢視配置或它們都可以不同。 使用內建的配置和其他項目以說明如何定義自訂版面配置的這份文件中有範例。


## <a name="adapter"></a>配接器

`ListView`控制項需要`Adapter`提供格式化`View`每個資料列。 Android 具有內建配接器以及檢視，可以使用，或您可以建立自訂類別。


## <a name="fast-scrolling"></a>快速捲動

當`ListView`包含多個資料列資料的快速捲動也能夠幫助使用者瀏覽至清單的任何部分。 快速捲動捲' 可以選擇性地啟用 （和自訂應用程式開發介面層級 11 和更高版本）。


## <a name="section-index"></a>區段索引

捲動詳細清單，同時選擇性區段索引提供意見反應與使用者清單的哪個部分它們目前正在檢視。 只適合在較長的清單，通常是在配合快速捲動。


## <a name="classes-overview"></a>類別概觀

主要的類別，用來顯示`ListViews`如下所示：

[![UML 圖表說明 ListView 和相關的類別之間的關聯性](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

下面會描述每個類別的用途：

- **ListView** &ndash;顯示可捲動的資料列集合的使用者介面項目。 在手機它通常會用完全螢幕 (在此情況下，`ListActivity`類別可以用) 或可能是較大的版面配置，在手機或平板電腦裝置上的一部分。

- **檢視** &ndash; Android 中的檢視，可以是任何使用者介面項目，但在內容中`ListView`它需要`View`来提供給每個資料列。

- **BaseAdapter** &ndash;基底類別繫結的配接器實作`ListView`到資料來源。

- **ArrayAdapter** &ndash;繫結到字串陣列的內建配接器類別`ListView`供顯示。 泛型`ArrayAdapter<T>`會執行其他類型相同。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`顯示 SQLite 查詢為基礎的資料。

本文件包含簡單的範例，使用`ArrayAdapter`更複雜的範例需要的自訂實作以及`BaseAdapter`或`CursorAdapter`。

