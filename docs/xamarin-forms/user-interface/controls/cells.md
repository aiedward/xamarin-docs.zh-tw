---
title: Xamarin.Forms 資料格
description: Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。 本文列出包含在 Xamarin.Forms 中的資料格。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 0947027b43eacd0bac269ebf7a779746e0d22866
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243352"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 資料格

_Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。_

A*儲存格*資料表中的項目所使用的特定項目，並描述清單中的每個項目轉譯的方式。 [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)類別衍生自[ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)，從中[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)也衍生。 資料格本身並不可見的項目。相反地，它是用來建立視覺項目範本。

`Cell` 用於以獨佔方式[ `ListView` ](views.md#listView)和[ `TableView` ](views.md#tableView)控制項。 若要了解如何使用和自訂資料格，請參閱[ `ListView` ](~/xamarin-forms/user-interface/listview/index.md)和[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)文件。

## <a name="cells"></a>資料格

Xamarin.Forms 可支援下列資料格類型：

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| A [ `TextCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell)顯示一個或兩個文字字串。 設定[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/)屬性，並選擇性地[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/)這些文字字串的屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell)顯示相同的資訊[ `TextCell` ](#textCell)但包含您使用設定點陣圖[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/)屬性。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [ `SwitchCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell)包含文字與設定[ `Text`'](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCellText/)屬性，並開啟/關閉參數，一開始設定使用布林值[ `On` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCell.On/)屬性。 處理[ `OnChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SwitchCell.OnChanged/)事件時通知`On`屬性變更。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [ `EntryCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell)定義[ `Label` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Label/)屬性會識別資料格，而單一行中可編輯的文字[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Text/)屬性。 處理[ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.EntryCell.Completed/)使用者已完成的文字項目時收到通知的事件。<br /><br />[應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/root/Xamarin.Forms/)
