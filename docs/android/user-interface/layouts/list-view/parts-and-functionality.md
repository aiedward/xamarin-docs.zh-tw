---
title: ListView 組件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: b8fd44a70f4c7ecdcf7919dec1c81461200b35bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028858"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Xamarin. Android ListView 元件和功能

`ListView` 由下列部分組成：

- [資料**列**] &ndash; 清單中可見的資料標記法。

- **介面卡**&ndash; 將資料來源系結至清單視圖的非視覺類別。

- **快速滾動**&ndash; 一個可讓使用者滾動清單長度的控制碼。

- **區段索引**&ndash; 使用者介面專案，這個專案會浮動在捲軸資料列上方，以指出目前資料列在清單中的所在位置。

這些螢幕擷取畫面會使用基本的 `ListView` 控制項，以顯示如何呈現快速滾動和區段索引：

[使用簡單的舊資料列、快速滾動和區段索引，![應用程式的螢幕擷取畫面](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

下圖詳細說明組成 `ListView` 的元素：

## <a name="rows"></a>列

每個資料列都有自己的 `View`。 此視圖可以是 `Android.Resources`中所定義的其中一個內建視圖，或是自訂視圖。 每個資料列都可以使用相同的 view 版面配置，或兩者都可以不同。 本檔中的範例使用內建版面配置，以及說明如何定義自訂版面配置的其他內容。

## <a name="adapter"></a>配接器

`ListView` 控制項需要 `Adapter` 提供每個資料列的格式化 `View`。 Android 具有可以使用的內建介面卡和視圖，或可建立自訂類別。

## <a name="fast-scrolling"></a>快速滾動

當 `ListView` 包含許多資料列時，可以啟用快速滾動，以協助使用者流覽至清單的任何部分。 快速滾動的「捲軸」可以選擇性地啟用（並在 API 層級11和更新版本中自訂）。

## <a name="section-index"></a>區段索引

當滾動長清單時，選擇性的區段索引會提供使用者對目前正在觀看之清單中哪些部分的意見反應。 這只適用于長清單，通常與快速滾動搭配使用。

## <a name="classes-overview"></a>類別總覽

用來顯示 `ListViews` 的主要類別如下所示：

[![UML 圖表說明 ListView 與相關類別之間的關聯性](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

每個類別的用途如下所述：

- **ListView** &ndash; 使用者介面專案，其會顯示可滾動的資料列集合。 在手機上，它通常會使用整個畫面（在此情況下，可以使用 `ListActivity` 類別），也可能是手機或平板電腦裝置上較大版面配置的一部分。

- **View** &ndash; 在 Android 中的視圖可以是任何使用者介面專案，但在 `ListView` 的內容中，需要為每個資料列提供 `View`。

- **BaseAdapter**介面卡執行 &ndash; 基類，以將 `ListView` 系結至資料來源。

- **ArrayAdapter** &ndash; 內建介面卡類別，將字串陣列系結至要顯示的 `ListView`。 泛型 `ArrayAdapter<T>` 對其他類型執行相同的工作。

- **CursorAdapter** &ndash; 使用 `CursorAdapter` 或 `SimpleCursorAdapter` 來顯示以 SQLite 查詢為基礎的資料。

本檔包含使用 `ArrayAdapter` 的簡單範例，以及需要 `BaseAdapter` 或 `CursorAdapter`自訂的更複雜範例。
