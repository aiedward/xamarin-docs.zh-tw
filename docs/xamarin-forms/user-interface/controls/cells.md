---
title: Xamarin.Forms 資料格
description: Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。 本文章列出包含在 Xamarin.Forms 中的資料格。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 289d47a700762caa8e41baef23818be6c684c61a
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998057"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 資料格

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。_

A*儲存格*是特製化的項目，用於在資料表中的項目，並說明應該如何呈現在清單中的每個項目。 [ `Cell` ](xref:Xamarin.Forms.Cell)類別衍生自[ `Element` ](xref:Xamarin.Forms.Element)，從中[ `VisualElement` ](xref:Xamarin.Forms.Element)也是衍生。 資料格本身就是不可見的項目;它是改為建立 visual 項目範本。

`Cell` 使用專用[ `ListView` ](views.md#listView)並[ `TableView` ](views.md#tableView)控制項。 若要了解如何使用和自訂資料格，請參閱[ `ListView` ](~/xamarin-forms/user-interface/listview/index.md)並[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)文件。

## <a name="cells"></a>資料格

Xamarin.Forms 可支援下列資料格類型：

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| A [ `TextCell` ](xref:Xamarin.Forms.TextCell)會顯示一或兩個文字字串。 設定[ `Text` ](xref:Xamarin.Forms.TextCell.Text)屬性，並選擇性地[ `Detail` ](xref:Xamarin.Forms.TextCell.Detail)這些文字字串的屬性。<br /><br />[API 文件](xref:Xamarin.Forms.TextCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [ `ImageCell` ](xref:Xamarin.Forms.ImageCell)會顯示相同的資訊[ `TextCell` ](#textCell)但包含了您設有一個點陣圖[ `Source` ](xref:Xamarin.Forms.Image.Source)屬性。<br /><br />[API 文件](xref:Xamarin.Forms.ImageCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| 包含具有屬性的文字集，以及一開始設定了布林值[`On`](xref:Xamarin.Forms.SwitchCell.On)屬性的開啟/關閉參數。 [`Text`](xref:Xamarin.Forms.SwitchCell.Text) [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 處理[ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged)事件時收到通知`On`屬性變更。<br /><br />[API 文件](xref:Xamarin.Forms.SwitchCell) / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [ `EntryCell` ](xref:Xamarin.Forms.EntryCell)定義[ `Label` ](xref:Xamarin.Forms.EntryCell.Label)屬性，識別資料格，而單行中可編輯的文字[ `Text` ](xref:Xamarin.Forms.EntryCell.Text)屬性。 處理[ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed)使用者完成文字 項目時收到通知的事件。<br /><br />[API 文件](xref:Xamarin.Forms.EntryCell) / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
