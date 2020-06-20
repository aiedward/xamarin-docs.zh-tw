---
title: Xamarin.Forms資料格
description: Xamarin.Forms資料格可以加入至 Listview 和 TableViews。 本文列出包含在中的資料格 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fff62aea5a20a8a14271123c4664c2c0b4e26d1e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573322"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms資料格

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_可以將 [Xamarin] 資料格加入至 Listview 和 TableViews。_

「資料*格*」是用於資料表中專案的特殊元素，並描述如何呈現清單中的每個專案。 [`Cell`](xref:Xamarin.Forms.Cell)類別衍生自 [`Element`](xref:Xamarin.Forms.Element) ，其 [`VisualElement`](xref:Xamarin.Forms.Element) 也衍生自。 儲存格本身不是視覺元素;而是用來建立視覺元素的範本。

`Cell`專門用於 [`ListView`](views.md#listview) 和 [`TableView`](views.md#tableview) 控制項。 若要瞭解如何使用和自訂資料格，請參閱 [`ListView`](~/xamarin-forms/user-interface/listview/index.md) 和 [`TableView`](~/xamarin-forms/user-interface/tableview.md) 檔。

## <a name="cells"></a>資料格

Xamarin.Forms支援下列資料格類型：

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| 會 [`TextCell`](xref:Xamarin.Forms.TextCell) 顯示一或兩個文字字串。 將 [`Text`](xref:Xamarin.Forms.TextCell.Text) 屬性和屬性（選擇性）設定 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 為這些文字字串。<br /><br />[API 檔](xref:Xamarin.Forms.TextCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [`ImageCell`](xref:Xamarin.Forms.ImageCell)會顯示與相同的資訊， [`TextCell`](#textcell) 但包含以屬性設定的點陣圖 [`Source`](xref:Xamarin.Forms.Image.Source) 。<br /><br />[API 檔](xref:Xamarin.Forms.ImageCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)包含具有屬性的文字集 [`Text`](xref:Xamarin.Forms.SwitchCell.Text) ，以及一開始設定了布林值屬性的開啟/關閉參數 [`On`](xref:Xamarin.Forms.SwitchCell.On) 。 處理 [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) 屬性變更時要通知的事件 `On` 。<br /><br />[API 檔](xref:Xamarin.Forms.SwitchCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [`EntryCell`](xref:Xamarin.Forms.EntryCell) [`Label`](xref:Xamarin.Forms.EntryCell.Label) 會定義屬性，以識別儲存格以及屬性中的一行可編輯文字 [`Text`](xref:Xamarin.Forms.EntryCell.Text) 。 處理 [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) 使用者完成文字輸入時要通知的事件。<br /><br />[API 檔](xref:Xamarin.Forms.EntryCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.FormsFormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsAPI 檔](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
