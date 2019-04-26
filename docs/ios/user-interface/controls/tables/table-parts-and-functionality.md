---
title: 資料表組件和在 Xamarin.iOS 中的功能
description: 本文件說明 UITableView 在 iOS 中的各個層面。 它討論區段標頭、 資料格、 區段頁尾、 索引和編輯模式。
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c4d788cce12a9aabdd1170cd1a52915f3b30285f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200498"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>資料表組件和在 Xamarin.iOS 中的功能

UITableView 可以 '分組' 或 'plain' 的樣式，而且包含下列部分：

-  [區段標頭](#Section_Header)
-  [資料格](#Cells)（或資料列，如果您偏好）
-  [頁尾區段](#Section_Footer)
-  [Tuple](#Index)
-  [編輯模式](#Edit_Features)(包含 '滑到刪除' 並拖曳控點來變更資料列順序) 

這些螢幕擷取畫面顯示區段的資料列、 頁首、 頁尾、 編輯控制項和索引的顯示方式。

 [![](table-parts-and-functionality-images/image1a.png "這些螢幕擷取畫面顯示區段的資料列、 頁首、 頁尾、 編輯控制項和索引的顯示方式")](table-parts-and-functionality-images/image1a.png#lightbox)

下面詳細說明這些組件：

<a name="Section_Header" />

## <a name="section-header"></a>區段標頭

資料格可以選擇性地會分成多個章節，加上自訂標頭，及/或加上頁尾。 標頭可以設定具有字串值，或可以提供自訂檢視，來允許不同的版面配置或樣式。

<a name="Cells" />

## <a name="cells"></a>資料格

資料格都是資料表的主要使用者介面項目。 正確實作，當儲存格是重複使用的記憶體效率。 有四個內建的儲存格樣式，而且您可以建立您自己自訂的資料格 – 程式碼或設計工具時使用分鏡腳本。

<a name="Section_Footer"/>

## <a name="section-footer"></a>頁尾區段

選讀小節頁尾可以設定具有字串值，或可以提供自訂檢視，來允許不同的版面配置或樣式。 區段頁首和頁尾可以分別設定。

<a name="Index" />

## <a name="index"></a>索引

索引會顯示為 右邊緣資料表的下個字元的寬帶。
觸碰，或拖曳索引可加速向下捲動到該部分的資料表。 索引是選擇性，但建議使用，可協助您瀏覽完整的清單。 未分組樣式通常使用索引。

<a name="Edit_Features" />

## <a name="editing-mode"></a>編輯模式

有幾個不同的編輯功能：

- 若要刪除個別的儲存格的撥動。
- 進入編輯模式，以顯示每個資料列上的 [刪除] 按鈕 
- 正在進入編輯模式，以顯示 重新排序的控制代碼。 
- 插入新的儲存格 （含動畫）。

這份文件的其餘部分會示範如何實作使用 Xamarin.iOS 的所有這些 UITableView 功能。


## <a name="classes-overview"></a>類別概觀

用來顯示資料表檢視的主要類別如下所示：

[![](table-parts-and-functionality-images/classdiagram.png "用來顯示資料表檢視的主要類別如下所示")](table-parts-and-functionality-images/classdiagram.png#lightbox)

每個類別的用途如下所述。

- **UITableView** – 包含捲動的容器內的儲存格集合的檢視。 資料表檢視通常會在整個螢幕使用 iPhone 應用程式中，但可能較大的檢視，在 iPad 上的一部分 （或出現在 popover）。 
- **UITableViewCell** – 代表單一資料格 （或資料列），在資料表檢視中的檢視。 有四個內建的儲存格型別，而且可以建立自訂中的資料格這兩個C#或使用 iOS 設計工具。 
- **UITableViewSource** – Xamarin.iOS 專屬抽象類別，提供要顯示的資料表，其中包含資料列計數、 傳回每個資料列的資料格檢視、 處理資料列選取範圍和許多其他選擇性功能所需的所有方法。 您*必須*子類別將取得 UITableView 工作。 
- **NSIndexPath** – 包含資料列和區段的屬性可唯一識別資料表中的儲存格位置。 
- **UITableViewController** – 為其檢視並可透過 TableView 屬性已硬式編碼的 UITableView 已準備好使用 UIViewController。 
- **UIViewController** -如果資料表不會佔用整個螢幕，您可以新增至任何使用其框架的 UIViewController UITableView 適當地設定。 

UITableViewSource 會取代下列 Xamarin.iOS 中仍然可用，但不是通常需要兩個類別：

- **UITableViewDataSource** – 為抽象類別的 Xamarin.iOS 中模型化的 OBJECTIVE-C 通訊協定。 必須是子類別，以提供的檢視中的資料表，每個資料格，以及標頭、 頁尾和資料列和資料表中的區段數目相關資訊。 
- **UITableViewDelegate** – 做為類別的 Xamarin.iOS 中模型化的 OBJECTIVE-C 通訊協定。 處理選項，編輯功能和其他選用的資料表功能。 

本文件中的範例會使用 UITableViewSource，並略過這兩個類別。 由於 Apple 的文件中找到任何 Objective C 範例會參考它們，就很有用了解他們執行的動作 （以及您可以改為使用 Xamarin.iOS 的 UITableViewSource），它們是此處提及。

## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
