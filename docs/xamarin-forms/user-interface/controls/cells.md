---
title: Xamarin.Forms 細胞
description: Xamarin.Forms 您可以將資料格加入至 Listview 和 TableViews。 本文列出內含的儲存格 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be5b2854c2f546e2194bd4dc95db77c9dcaf0760
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562804"
---
# <a name="no-locxamarinforms-cells"></a>Xamarin.Forms 細胞

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms 您可以將資料格加入至 Listview 和 TableViews。_

資料 *格* 是用於資料表中之專案的特製化元素，並描述如何轉譯清單中的每個專案。 [`Cell`](xref:Xamarin.Forms.Cell)類別衍生自 [`Element`](xref:Xamarin.Forms.Element) ， [`VisualElement`](xref:Xamarin.Forms.Element) 也衍生自。 儲存格本身不是視覺元素;它是用來建立視覺元素的範本。

`Cell` 專門搭配 [`ListView`](xref:Xamarin.Forms.ListView) 和控制項使用 [`TableView`](xref:Xamarin.Forms.TableView) 。 若要瞭解如何使用和自訂資料格，請參閱 [`ListView`](~/xamarin-forms/user-interface/listview/index.md) 和 [`TableView`](~/xamarin-forms/user-interface/tableview.md) 檔。

## <a name="cells"></a>資料格

Xamarin.Forms 支援下列資料格類型：

| 類型 | 描述 | 外觀 |
| --- | --- | --- |
| `TextCell` | 會 [`TextCell`](xref:Xamarin.Forms.TextCell) 顯示一或兩個文字字串。 將屬性（property） [`Text`](xref:Xamarin.Forms.TextCell.Text) 和屬性（property）（選擇性）設定 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) 為這些文字字串。<br /><br />[API 檔](xref:Xamarin.Forms.TextCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | [`ImageCell`](xref:Xamarin.Forms.ImageCell)會顯示相同的資訊， [`TextCell`](xref:Xamarin.Forms.TextCell) 但會包含您使用屬性設定的點陣圖 [`Source`](xref:Xamarin.Forms.Image.Source) 。<br /><br />[API 檔](xref:Xamarin.Forms.ImageCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)包含包含屬性的文字 [`Text`](xref:Xamarin.Forms.SwitchCell.Text) ，以及一開始以布林值屬性設定的開啟/關閉參數 [`On`](xref:Xamarin.Forms.SwitchCell.On) 。 處理 [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) 事件，以便在屬性變更時收到通知 `On` 。<br /><br />[API 檔](xref:Xamarin.Forms.SwitchCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref:Xamarin.Forms.EntryCell) [`Label`](xref:Xamarin.Forms.EntryCell.Label) 會定義屬性，這個屬性會識別儲存格，以及屬性中的一行可編輯文字 [`Text`](xref:Xamarin.Forms.EntryCell.Text) 。 處理 [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) 當使用者完成文字輸入時要通知的事件。<br /><br />[API 檔](xref:Xamarin.Forms.EntryCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 樣品](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 檔](/dotnet/api/xamarin.forms?view=xamarin-forms)