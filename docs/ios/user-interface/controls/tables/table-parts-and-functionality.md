---
title: Xamarin 中的資料表元件和功能
description: 本檔說明 iOS 中 UITableView 的各個部分。 它討論區段標頭、資料格、區段頁尾、索引和編輯模式。
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 6002e5cc62dfe30c14f21943c690a5a1b15a0c12
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936587"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Xamarin 中的資料表元件和功能

UITableView 可以有「群組」或「純文字」樣式，並由下列部分組成：

- [區段標頭](#Section_Header)
- [儲存格](#Cells)（或資料列，如果您想要的話）
- [區段頁尾](#Section_Footer)
- [Index](#Index)
- [編輯模式](#Edit_Features)（包括 [滑動至刪除] 和 [拖曳控點] 以變更資料列順序） 

這些螢幕擷取畫面顯示區段列、頁首、頁尾、編輯控制項和索引的顯示方式。

 [![這些螢幕擷取畫面顯示區段列、頁首、頁尾、編輯控制項和索引的顯示方式](table-parts-and-functionality-images/image1a.png)](table-parts-and-functionality-images/image1a.png#lightbox)

以下將更詳細地說明這些部分：

<a name="Section_Header"></a>

## <a name="section-header"></a>區段標頭

資料格可以選擇性地分組為區段，並以自訂標頭標示，並（或）以頁尾標示。 您可以使用字串值來設定標頭，或提供自訂視圖來允許不同的版面配置或樣式。

<a name="Cells"></a>

## <a name="cells"></a>資料格

資料格是資料表的主要使用者介面元素。 正確執行時，會重複使用資料格來提升記憶體效率。 有四種內建的儲存格樣式，您可以在程式碼中建立自己的自訂資料格，或是在使用分鏡腳本時在設計工具中建立。

<a name="Section_Footer"></a>

## <a name="section-footer"></a>區段頁尾

選擇性區段頁尾可以使用字串值來設定，或者可以提供自訂視圖來允許不同的版面配置或樣式。 區段標頭和頁尾可以獨立設定。

<a name="Index"></a>

## <a name="index"></a>索引

索引會顯示為下表右邊緣的字元帶狀。
在索引上觸及或拖曳可加速滾動到資料表的該部分。 索引是選擇性的，但建議用來協助導覽長清單。 索引通常不會與群組樣式一起使用。

<a name="Edit_Features"></a>

## <a name="editing-mode"></a>編輯模式

有幾種不同的編輯功能可供使用：

- 滑動以刪除個別資料格。
- 進入編輯模式以顯示每個資料列的刪除按鈕 
- 進入編輯模式以顯示重新排序控制碼。 
- 插入新的儲存格（含動畫）。

本檔的其餘部分將說明如何使用 Xamarin 來執行所有這些 UITableView 功能。

## <a name="classes-overview"></a>類別總覽

用來顯示資料表視圖的主要類別如下所示：

[![用來顯示資料表視圖的主要類別如下所示](table-parts-and-functionality-images/classdiagram.png)](table-parts-and-functionality-images/classdiagram.png#lightbox)

每個類別的用途如下所述：

- **UITableView** –包含滾動容器內儲存格集合的視圖。 [資料表] 視圖通常會在 iPhone 應用程式中使用整個螢幕，但可能做為 iPad 上較大視圖的一部分（或出現在 popover 中）。 
- **UITableViewCell** –代表資料表視圖中單一資料格（或資料列）的視圖。 有四個內建的儲存格類型，而且可以使用 c # 或 iOS 設計工具來建立自訂資料格。 
- **UITableViewSource** – Xamarin. iOS-專有抽象類別，提供顯示資料表所需的所有方法，包括資料列計數、傳回每個資料列的資料格視圖、處理資料列選取，以及許多其他選擇性功能。 您*必須*將此設為子類別，才能讓 UITableView 運作。 
- **NSIndexPath** –包含資料列和區段屬性，可唯一識別資料表中資料格的位置。 
- **UITableViewController** –一種立即可用的 UIViewController，其中的 UITableView 會硬式編碼為其觀點，並可透過 TableView 屬性加以存取。 
- **UIViewController** –如果資料表不佔用整個螢幕，您可以將 UITableView 新增至任何 UIViewController，並適當地設定其框架。 

UITableViewSource 會取代下列兩個類別，但它們仍可在 Xamarin 中使用，但通常不是必要的：

- **UITableViewDataSource** –以抽象類別形式在 Xamarin 中建立模型的目標 C 通訊協定。 必須進行子類別化，以提供資料表與每個資料格的 view，以及標頭、頁尾和資料表中的資料列和區段數目的相關資訊。 
- **UITableViewDelegate** –以類別形式在 Xamarin 中建立模型的目標 C 通訊協定。 處理選取、編輯功能和其他選擇性資料表功能。 

在本檔中，所有範例都使用 UITableViewSource，並忽略這兩個類別。 這裡有提到，因為在 Apple 檔中找到的任何目標-C 範例都會參考它們，因此瞭解其用途（而且您可以改用 Xamarin 的 UITableViewSource）會很有説明。

## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
