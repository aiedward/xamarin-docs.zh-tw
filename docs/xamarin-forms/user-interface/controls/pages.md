---
title: Xamarin.Forms Pages
description: "Xamarin.Forms 頁面代表跨平台行動裝置應用程式畫面。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 35822dbbb7d5694e7f1f0a3f35f10df404206af9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms Pages

_Xamarin.Forms 頁面代表跨平台行動裝置應用程式畫面。_

<style>.tableimg {最大寬度： 無 ！ 重要;}</style>

## <a name="pages"></a>頁面

[ `Page` ](http://iosapi.xamarin.com/?link=T%3aXamarin.Forms.Page)類別是視覺化的項目會佔用大部分或所有的螢幕，而包含單一子系。 A`Xamarin.Forms.Page`代表檢視中的控制站 iOS 或 Windows Phone 中的頁面。 在 Android 上每一頁會佔用活動，例如畫面，但 Xamarin.Forms 頁會使用*不*活動。

 [ ![](pages-images/pages-sml.png "Xamarin.Forms 頁面類型")](pages-images/pages.png "Xamarin.Forms 頁面類型")

<br clear="all" />

Xamarin.Forms 支援：

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <tr>
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
  </thead></tr>
  <tbody>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>
    </td>
    <td align="center" valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>顯示單一<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>，通常這類容器<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a>或<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentPageDemoPage.cs"><img src="pages-images/ContentPage.png" title="ContentPage 範例" class="tableimg">
    </a></td>
  </tr><tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">頁面</a>管理兩個窗格的資訊。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/MasterDetailPageDemoPage.cs"><img src="pages-images/MasterDetailPage.png" title="MasterDetailPage 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">頁面</a>管理的瀏覽和堆疊的其他頁面的使用者體驗。  
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/NavigationPageDemoPage.cs"><img src="pages-images/NavigationPage.png" title="NavigationPage 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">頁面</a>，網頁，可以使用索引標籤可讓瀏覽不同的子系。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TabbedPageDemoPage.cs"><img src="pages-images/TabbedPage.png" title="TabbedPage 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">頁面</a>顯示全螢幕控制項範本，與基底類別的內容<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>。
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="pages-images/TemplatedPage.png" title="TemplatedPage 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">頁面</a>允許子頁面，例如主機庫之間撥動手勢。
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CarouselPageDemoPage.cs"><img src="pages-images/CarouselPage.png" title="CarouselPage 範例" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 圖庫 （範例）](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Xamarin.Forms 應用程式開發介面文件](http://iosapi.xamarin.com/?link=N%3aXamarin.Forms)
