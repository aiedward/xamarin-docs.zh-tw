---
title: :::no-loc(Xamarin.Forms):::資料格
description: ':::no-loc(Xamarin.Forms):::資料格可以加入至 Listview 和 TableViews。 本文列出包含在中的資料格 :::no-loc(Xamarin.Forms)::: 。'
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 21fca31ca9e1cf39843e04c3c381bb41ef77335f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997276"
---
# <a name="no-locxamarinforms-cells"></a>:::no-loc(Xamarin.Forms):::資料格

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_:::no-loc(Xamarin.Forms):::資料格可以加入至 Listview 和 TableViews。_

「資料*格*」是用於資料表中專案的特殊元素，並描述如何呈現清單中的每個專案。 [`Cell`](xref::::no-loc(Xamarin.Forms):::.Cell)類別衍生自 [`Element`](xref::::no-loc(Xamarin.Forms):::.Element) ，其 [`VisualElement`](xref::::no-loc(Xamarin.Forms):::.Element) 也衍生自。 儲存格本身不是視覺元素;而是用來建立視覺元素的範本。

`Cell`專門用於 [`ListView`](xref::::no-loc(Xamarin.Forms):::.ListView) 和 [`TableView`](xref::::no-loc(Xamarin.Forms):::.TableView) 控制項。 若要瞭解如何使用和自訂資料格，請參閱 [`ListView`](~/xamarin-forms/user-interface/listview/index.md) 和 [`TableView`](~/xamarin-forms/user-interface/tableview.md) 檔。

## <a name="cells"></a>資料格

:::no-loc(Xamarin.Forms):::支援下列資料格類型：

| 類型 | 描述 | 外觀 |
| --- | --- | --- |
| `TextCell` | 會 [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) 顯示一或兩個文字字串。 將 [`Text`](xref::::no-loc(Xamarin.Forms):::.TextCell.Text) 屬性和屬性（選擇性）設定 [`Detail`](xref::::no-loc(Xamarin.Forms):::.TextCell.Detail) 為這些文字字串。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.TextCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | [`ImageCell`](xref::::no-loc(Xamarin.Forms):::.ImageCell)會顯示與相同的資訊， [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) 但包含以屬性設定的點陣圖 [`Source`](xref::::no-loc(Xamarin.Forms):::.Image.Source) 。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.ImageCell)  / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | [`SwitchCell`](xref::::no-loc(Xamarin.Forms):::.SwitchCell)包含具有屬性的文字集 [`Text`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.Text) ，以及一開始設定了布林值屬性的開啟/關閉參數 [`On`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.On) 。 處理 [`OnChanged`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.OnChanged) 屬性變更時要通知的事件 `On` 。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.SwitchCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref::::no-loc(Xamarin.Forms):::.EntryCell) [`Label`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Label) 會定義屬性，以識別儲存格以及屬性中的一行可編輯文字 [`Text`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Text) 。 處理 [`Completed`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Completed) 使用者完成文字輸入時要通知的事件。<br /><br />[API 檔](xref::::no-loc(Xamarin.Forms):::.EntryCell)  / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[此頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  的 c # 程式碼[XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>相關連結

- [:::no-loc(Xamarin.Forms):::FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [:::no-loc(Xamarin.Forms):::範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=:::no-loc(Xamarin.Forms):::)
- [:::no-loc(Xamarin.Forms):::API 檔](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
