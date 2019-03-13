---
title: ListView 組件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 248baa5daceff6db01098a155600ea204547e845
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116106"
---
# <a name="listview-parts-and-functionality"></a>ListView 組件和功能


## <a name="overview"></a>總覽

A`ListView`下列部分所組成：

- **資料列**&ndash;可見的表示法的清單中的資料。

- **配接器**&ndash;繫結至清單檢視的資料來源的隱藏式類別。

- **快速捲動**&ndash;的控制代碼，讓使用者可以捲動清單的長度。

- **區段索引**&ndash;漂浮捲動的使用者介面項目以指出清單中的目前資料列的所在位置的資料列。

這些螢幕擷取畫面使用基本`ListView`控制項顯示快速捲動和區段索引的呈現方式：

[![使用純舊的資料列，應用程式的螢幕擷取畫面快速捲動和區段索引](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

項目構成`ListView`詳細說明如下所述：


## <a name="rows"></a>列

每個資料列都有它自己`View`。 檢視可以是內建的檢視中定義的其中一個`Android.Resources`，或自訂的檢視。 每個資料列可以使用相同的檢視版面配置或它們都可以不同。 使用內建的版面配置和其他人說明如何定義自訂版面配置的這份文件中有範例。


## <a name="adapter"></a>配接器

`ListView`控制項需要`Adapter`提供的格式化`View`每個資料列。 Android 提供內建配接器和檢視，可以使用，或您可以建立自訂類別。


## <a name="fast-scrolling"></a>快速捲動

當`ListView`包含多個資料列的資料快速捲動可幫助使用者瀏覽至清單的任何部分。 快速捲動 '捲軸' 可以選擇性地啟用 （和自訂 API 層級 11 及更新版本）。


## <a name="section-index"></a>區段索引

捲動時透過長的清單，選擇性的區段索引提供意見反應的使用者清單的哪個部分目前檢視。 它只適合在較長的清單，通常是在搭配快速捲動。


## <a name="classes-overview"></a>類別概觀

用來顯示的主要類別`ListViews`如下所示：

[![說明 ListView 和相關的類別之間的關聯性的 UML 圖表](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

每個類別的用途如下所述。

- **ListView** &ndash;顯示可捲動的資料列集合的使用者介面項目。 在手機它通常會用完全螢幕 (在此情況下，`ListActivity`類別可用於) 或它可能屬於較大的配置，在手機或平板電腦裝置。

- **檢視**&ndash;在 Android 中的檢視可以是任何使用者介面項目，但在內容中`ListView`它需要`View`来提供給每個資料列。

- **BaseAdapter** &ndash;繫結的配接器實作的基底類別`ListView`到資料來源。

- **ArrayAdapter** &ndash;內建配接器類別，會將繫結至字串陣列`ListView`顯示。 泛型`ArrayAdapter<T>`會執行相同的其他類型。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`顯示 SQLite 查詢為基礎的資料。

本文件包含簡單的範例，使用`ArrayAdapter`以及更複雜的範例需要的自訂實作`BaseAdapter`或`CursorAdapter`。

