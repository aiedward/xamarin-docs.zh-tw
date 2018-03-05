---
title: "Xamarin.Forms 資料格"
description: "Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 509ecc509754bba544115c140e619f634bd64eae
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 資料格

_Xamarin.Forms 資料格可以加入至 Listview 和 TableViews。_

<style>.tableimg {最大寬度： 無 ！ 重要;}</style>

## <a name="cells"></a>資料格

儲存格是在資料表中的項目所使用的特定項目，並說明如何應該繪製在清單中的每個項目。 資料格衍生自[ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)，從 VisualElement 也衍生。 資料格不是視覺項目，不過它只會描述用來建立視覺項目範本。 [`Cell`](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) 提供所有 Xamarin.Forms 資料格的基底類別和功能。 資料格都是項目加入至設計[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)或[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)控制項。

若要了解如何使用和自訂資料格，請參閱[ListView](~/xamarin-forms/user-interface/listview/index.md)和[TableView](~/xamarin-forms/user-interface/tableview.md)文件。

<table align="center" border="1" cellpadding="1" cellspacing="1">
<thead>
    <th>
      <strong>型別</strong>
    </th>
    <th>
      <strong>說明</strong>
    </th>
    <th style="min-width:400px">
      <strong>螢幕擷取畫面</strong>
    </th>
  </thead>
  <tbody>
    <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a>標籤與單一行文字輸入欄位。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="cells-images/EntryCell.png" title="EntryCell 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a>標籤與 on/off 開關。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchCellDemoPage.cs"><img src="cells-images/SwitchCell.png" title="SwitchCell 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a>主要和次要的文字。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TextCellDemoPage.cs"><img src="cells-images/TextCell.png" title="TextCell 範例" class="tableimg">
    </a></td>
  </tr>
      <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">文字儲存格</a>也包括映像。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageCellDemoPage.cs"><img src="cells-images/ImageCell.png" title="ImageCell 範例" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 圖庫 （範例）](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
