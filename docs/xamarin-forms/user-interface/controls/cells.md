---
title: Xamarin.Forms 資料格
description: Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。 本文章列出包含在 Xamarin.Forms 中的資料格。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 34e28d8532c232549c6f45e549787885a9155684
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526854"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 資料格

_Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。_

A*儲存格*是特製化的項目，用於在資料表中的項目，並說明應該如何呈現在清單中的每個項目。 [ `Cell` ](xref:Xamarin.Forms.Cell)類別衍生自[ `Element` ](xref:Xamarin.Forms.Element)，從中[ `VisualElement` ](xref:Xamarin.Forms.Element)也是衍生。 資料格本身就是不可見的項目;它是改為建立 visual 項目範本。

`Cell` 使用專用[ `ListView` ](views.md#listView)並[ `TableView` ](views.md#tableView)控制項。 若要了解如何使用和自訂資料格，請參閱[ `ListView` ](~/xamarin-forms/user-interface/listview/index.md)並[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)文件。

## <a name="cells"></a>資料格

Xamarin.Forms 可支援下列資料格類型：

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| A [ `TextCell` ](xref:Xamarin.Forms.TextCell)會顯示一或兩個文字字串。 設定[ `Text` ](xref:Xamarin.Forms.TextCell.Text)屬性，並選擇性地[ `Detail` ](xref:Xamarin.Forms.TextCell.Detail)這些文字字串的屬性。<br /><br />[API 文件](xref:Xamarin.Forms.TextCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [ `ImageCell` ](xref:Xamarin.Forms.ImageCell)會顯示相同的資訊[ `TextCell` ](#textCell)但包含了您設有一個點陣圖[ `Source` ](xref:Xamarin.Forms.Image.Source)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.ImageCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell)包含文字設有[ `Text`'](xref:Xamarin.Forms.SwitchCell.Text)屬性和開啟/關閉參數，一開始設布林值，且[ `On` ](xref:Xamarin.Forms.SwitchCell.On)屬性。 處理[ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged)事件時收到通知`On`屬性變更。<br /><br />[API 文件](xref:Xamarin.Forms.SwitchCell) / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [ `EntryCell` ](xref:Xamarin.Forms.EntryCell)定義[ `Label` ](xref:Xamarin.Forms.EntryCell.Label)屬性，識別資料格，而單行中可編輯的文字[ `Text` ](xref:Xamarin.Forms.EntryCell.Text)屬性。 處理[`Completed`](xref:Xamarin.Forms.EntryCell.Completed)使用者完成文字 項目時收到通知的事件。<br /><br />[API 文件](xref:Xamarin.Forms.EntryCell) / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
