---
title: Xamarin 中的資料表元件和功能
description: 本檔說明 iOS 中 Ios uitableview 範例的各種不同部分。 它會討論區段標題、資料格、頁尾、索引和編輯模式。
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 55f471fe02618becbfc4a7ab8571d9690fe44843
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430587"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Xamarin 中的資料表元件和功能

Ios uitableview 範例可以有「分組」或「純文字」樣式，並包含下列部分：

- [區段標題](#Section_Header)
- 如果您想要) ，資料[格](#Cells) (或資料列
- [頁尾](#Section_Footer)
- [Index](#Index)
- [編輯模式](#Edit_Features) (包括「滑動至刪除」以及將控點拖曳至變更資料列順序)  

這些螢幕擷取畫面顯示如何顯示區段列、頁首、頁尾、編輯控制項和索引。

 [![這些螢幕擷取畫面顯示如何顯示區段列、頁首、頁尾、編輯控制項和索引](table-parts-and-functionality-images/image1a.png)](table-parts-and-functionality-images/image1a.png#lightbox)

以下將更詳細地說明這些部分：

<a name="Section_Header"></a>

## <a name="section-header"></a>區段標題

您可以選擇性地將資料格分組為區段，並以自訂標頭標示，並（或）以頁尾標記。 您可以使用字串值設定標頭，也可以提供自訂視圖來允許不同的配置或樣式。

<a name="Cells"></a>

## <a name="cells"></a>資料格

資料格是資料表的主要使用者介面元素。 當正確執行時，會將資料格重複用於記憶體效率。 內建的儲存格樣式有四種，您可以在程式碼中建立自己的自訂資料格，或在使用分鏡腳本時，在設計工具中建立自己的資料格。

<a name="Section_Footer"></a>

## <a name="section-footer"></a>頁尾

您可以使用字串值來設定選擇性頁尾頁尾，也可以提供自訂視圖來允許不同的版面配置或樣式。 區段標頭和頁尾可以個別設定。

<a name="Index"></a>

## <a name="index"></a>索引

索引會顯示為數據表右邊緣下的字元帶。
在索引上觸及或拖曳可加速滾動至資料表的那個部分。 索引是選擇性的，但建議用來協助流覽長清單。 索引通常不能與群組的樣式一起使用。

<a name="Edit_Features"></a>

## <a name="editing-mode"></a>編輯模式

有幾個不同的編輯功能可供使用：

- 滑動以刪除個別儲存格。
- 進入編輯模式以顯示每個資料列上的刪除按鈕 
- 進入編輯模式以顯示重新排序控制碼。 
- 使用動畫) 插入新的資料格 (。

本檔的其餘部分將說明如何使用 Xamarin 來執行所有這些 Ios uitableview 範例功能。

## <a name="classes-overview"></a>類別總覽

用來顯示資料表視圖的主要類別如下所示：

[![用來顯示資料表視圖的主要類別如下所示](table-parts-and-functionality-images/classdiagram.png)](table-parts-and-functionality-images/classdiagram.png#lightbox)

每個類別的目的如下所述：

- **Ios uitableview 範例** –包含滾動容器內儲存格集合的視圖。 表格視圖通常會使用 iPhone 應用程式中的整個畫面，但可能會在 iPad (的較大觀點中存在，或出現在 popover) 中。 
- **UITableViewCell** –代表資料表視圖中單一資料格 (或資料列) 的視圖。 有四個內建資料格類型，而且可以使用 c # 或 iOS 設計工具來建立自訂儲存格。 
- **UITableViewSource** – Xamarin-專屬抽象類別，提供顯示資料表所需的所有方法，包括資料列計數、傳回每個資料列的資料格視圖、處理資料列選取，以及許多其他選擇性功能。 您  *必須* 將此類別設為子類別，才能讓 ios uitableview 範例運作。 
- **NSIndexPath** –包含資料列和區段屬性，可唯一識別資料表中資料格的位置。 
- **UITableViewController** –一種立即可用的 UIViewController，它會將 ios uitableview 範例編碼為其視圖，並可透過 TableView 屬性存取。 
- **UIViewController** –如果資料表不佔用整個畫面，您可以將 ios uitableview 範例新增至任何 UIViewController，並適當地設定其框架組。 

UITableViewSource 會取代下列兩個類別，這些類別在 Xamarin 中仍可使用，但通常不是必要的：

- **UITableViewDataSource** –在 Xamarin 中模型化為抽象類別的目標 C 通訊協定。 必須具子類別化，才能為資料表提供每個資料格的視圖，以及標頭、頁尾以及資料表中資料列和區段數目的相關資訊。 
- **UITableViewDelegate** –在 Xamarin 中以類別模型化的目標 C 通訊協定。 處理選取專案、編輯功能和其他選擇性資料表功能。 

在本檔中，範例全都使用 UITableViewSource 並忽略這兩個類別。 這裡提及的是，因為在 Apple 的檔中找到的任何目標 C 範例都會參考這些範例，因此瞭解它們 (，以及您可以) 改為使用 Xamarin 的 UITableViewSource 時，是很有用的。

## <a name="related-links"></a>相關連結

- [WorkingWithTables (範例) ](/samples/xamarin/ios-samples/workingwithtables)