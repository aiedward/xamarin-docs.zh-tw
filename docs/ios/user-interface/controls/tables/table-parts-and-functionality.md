---
title: 資料表組件和 Xamarin.iOS 中的功能
description: 本文件說明的各種 UITableView 在 iOS 中。 它討論區段標頭、 資料格、 區段頁尾、 索引和編輯模式。
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: c7c9f810798c3d02078b48e17a2ab951cb36aa12
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789872"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>資料表組件和 Xamarin.iOS 中的功能

UITableView 可以分組' 或 '一般' 的樣式，而且包含下列部分：

-  [區段標頭](#Section_Header)
-  [資料格](#Cells)（或資料列，如果您想要的話）
-  [區段頁尾](#Section_Footer)
-  [索引](#Index)
-  [編輯模式](#Edit_Features)(包含 '滑到刪除' 並拖曳控點來變更資料列順序) 

這些螢幕擷取畫面顯示區段的資料列、 頁首、 頁尾、 編輯控制項和索引的顯示方式。

 [![](table-parts-and-functionality-images/image1a.png "這些螢幕擷取畫面顯示區段的資料列、 頁首、 頁尾、 編輯控制項和索引的顯示方式")](table-parts-and-functionality-images/image1a.png#lightbox)

以下更詳細描述這些組件：

<a name="Section_Header" />

## <a name="section-header"></a>區段標頭

資料格可以選擇性地進行分成區段，加上自訂標頭，及/或加上頁尾。 標頭可使用的字串值來設定或自訂檢視可以提供給供不同的版面配置和樣式。

<a name="Cells" />

## <a name="cells"></a>資料格

資料格都是資料表的主要使用者介面項目。 正確實作，當儲存格是重複使用的記憶體效率。 有四個內建的儲存格樣式，而且您可以建立您自己自訂的儲存格-程式碼或設計工具中使用分鏡腳本時。

<a name="Section_Footer"/>

## <a name="section-footer"></a>區段頁尾

選擇性區段頁尾可以設定具有字串值，或可以提供自訂檢視，即可供不同的版面配置和樣式。 區段頁首和頁尾可以分別設定。

<a name="Index" />

## <a name="index"></a>索引

索引會顯示為資料表的右邊緣向字元的寬帶。
觸及或索引上的拖曳加速向下捲動到該資料表的一部分。 索引是選擇性但建議使用，可協助您瀏覽詳細清單。 不為群組樣式通常使用索引。

<a name="Edit_Features" />

## <a name="editing-mode"></a>編輯模式

有幾個不同的編輯功能：

- 若要刪除的個別資料格撥動。
- 進入編輯模式，以顯示每個資料列的刪除按鈕 
- 正在進入編輯模式，以顯示 重新排序的控制代碼。 
- 插入新的儲存格 （與動畫）。

這份文件的其餘部分會示範如何實作與 Xamarin.iOS 這些 UITableView 功能。


## <a name="classes-overview"></a>類別概觀

用來顯示資料表檢視的主要類別如下所示：

[![](table-parts-and-functionality-images/classdiagram.png "用來顯示資料表檢視的主要類別如下所示")](table-parts-and-functionality-images/classdiagram.png#lightbox)

下面會描述每個類別的用途：

- **UITableView** – 包含捲動的容器內的儲存格的集合的檢視。 資料表檢視通常會在整個螢幕使用 iPhone 應用程式中，但可能會做為較大的 iPad 上檢視的一部分 （或出現在 popover）。 
- **UITableViewCell** – 代表單一資料格 （或資料列），在資料表檢視中的檢視。 有四種內建的儲存格類型，而且很可能同時在 C# 或 iOS 設計工具建立自訂儲存格。 
- **UITableViewSource** – Xamarin.iOS 獨佔抽象類別，提供顯示資料表，包括資料列計數，傳回每個資料列的資料格檢視，處理資料列選取範圍和許多其他選擇性功能所需的所有方法。 您*必須*子類別化這 UITableView 運作。 
- **NSIndexPath** – 包含資料列和區段的屬性可唯一識別資料表中儲存格的位置。 
- **UITableViewController** – 已備妥要使用 UIViewController 具有 UITableView 硬式編碼成其檢視和可透過 TableView 屬性存取。 
- **UIViewController** – 如果資料表不會佔用整個螢幕，您可以加入至其框架任何 UIViewController UITableView 適當地設定。 

UITableViewSource 會取代下列 Xamarin.iOS 中仍然可用但不是通常需要兩個類別：

- **UITableViewDataSource** – Objective C 中 Xamarin.iOS 為抽象類別模型化的通訊協定。 必須是子類別，來提供資料表的檢視，針對每個資料格，以及頁首、 頁尾和資料列和資料表中的區段數目的相關資訊。 
- **UITableViewDelegate** – Xamarin.iOS 做為類別中模型化的 OBJECTIVE-C 通訊協定。 會處理選取項目，編輯功能和其他選用的資料表功能。 

本文件中的範例使用 UITableViewSource，並略過這兩個類別。 因為 Apple 文件中找到任何 Objective C 範例會參考它們，就很有用了解它們的功用 （及您可以改為使用 Xamarin.iOS 的 UITableViewSource），也會提及這裡。

## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
