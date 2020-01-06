---
title: Xamarin. 表單資料格
description: 可以將 [Xamarin] 資料格加入至 Listview 和 TableViews。 本文列出 Xamarin 中包含的資料格。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: e8f3d8af7aab2a7a73787021f114470726d74b72
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488084"
---
# <a name="xamarinforms-cells"></a>Xamarin. 表單資料格

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_可以將 [Xamarin] 資料格加入至 Listview 和 TableViews。_

「資料*格*」是用於資料表中專案的特殊元素，並描述如何呈現清單中的每個專案。 [`Cell`](xref:Xamarin.Forms.Cell)類別衍生自[`VisualElement`](xref:Xamarin.Forms.Element)也衍生的[`Element`](xref:Xamarin.Forms.Element)。 儲存格本身不是視覺元素;而是用來建立視覺元素的範本。

`Cell` 僅搭配[`ListView`](views.md#listview)和[`TableView`](views.md#tableview)控制項使用。 若要瞭解如何使用和自訂資料格，請參閱[`ListView`](~/xamarin-forms/user-interface/listview/index.md)和[`TableView`](~/xamarin-forms/user-interface/tableview.md)檔。

## <a name="cells"></a>儲存格

Xamarin 支援下列資料格類型：

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| [`TextCell`](xref:Xamarin.Forms.TextCell)顯示一或兩個文字字串。 將 [ [`Text`](xref:Xamarin.Forms.TextCell.Text) ] 屬性和 [ [`Detail`](xref:Xamarin.Forms.TextCell.Detail) ] 屬性（選擇性）設定為這些文字字串。<br /><br />[API 檔](xref:Xamarin.Forms.TextCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![TextCell 範例](cells-images/TextCell.png "TextCell 範例")](cells-images/TextCell-Large.png#lightbox "TextCell 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [`ImageCell`](xref:Xamarin.Forms.ImageCell)會顯示與[`TextCell`](#textCell)相同的資訊，但包含以[`Source`](xref:Xamarin.Forms.Image.Source)屬性設定的點陣圖。<br /><br />[API 檔](xref:Xamarin.Forms.ImageCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![ImageCell 範例](cells-images/ImageCell.png "ImageCell 範例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)包含具有[`Text`](xref:Xamarin.Forms.SwitchCell.Text)屬性的文字集，以及一開始設定了布林值[`On`](xref:Xamarin.Forms.SwitchCell.On)屬性的開啟/關閉參數。 處理 `On` 屬性變更時，要收到通知的[`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged)事件。<br /><br />[API 檔](xref:Xamarin.Forms.SwitchCell) / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 範例](cells-images/SwitchCell.png "SwitchCell 範例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [`EntryCell`](xref:Xamarin.Forms.EntryCell)會定義[`Label`](xref:Xamarin.Forms.EntryCell.Label)屬性，以識別資料格，以及[`Text`](xref:Xamarin.Forms.EntryCell.Text)屬性中的一行可編輯文字。 處理使用者完成文字輸入時要通知的[`Completed`](xref:Xamarin.Forms.EntryCell.Completed)事件。<br /><br />[API 檔](xref:Xamarin.Forms.EntryCell) / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 範例](cells-images/EntryCell.png "EntryCell 範例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 範例")<br />[ 此頁面的 C# 程式碼](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  / [XAML 頁面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
