---
title: "Xamarin.Forms 版面配置"
description: "Xamarin.Forms 版面配置用來撰寫使用者介面控制項至邏輯結構。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ecea0f55360fcde7a50c52bb33c45a2c5fff5eeb
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-layouts"></a>Xamarin.Forms 版面配置

_Xamarin.Forms 版面配置用來撰寫使用者介面控制項至邏輯結構。_

<style>.tableimg {最大寬度： 無 ！ 重要;}</style>

## <a name="layouts"></a>版面配置

[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) Xamarin.Forms 中的類別是特製化的檢視，配置或檢視表，做為容器的其他子類型。 它通常會包含邏輯 Xamarin.Forms 應用程式中設定的位置和大小的子項目。

 [ ![](layouts-images/layouts-sml.png "Xamarin.Forms 版面配置類型")](layouts-images/layouts.png "Xamarin.Forms 版面配置類型")

<br clear="all" />

Xamarin.Forms 支援：

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a>
    </td>
    <td valign="top">
樣板化的檢視配置管理員。 用於<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/">ControlTemplate</a>來標記的呈現內容顯示的位置。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/Templates/ControlTemplates/SimpleTheme/SimpleTheme/App.xaml"><img src="layouts-images/ContentPresenter.png" title="ContentPresenter 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a>
    </td>
    <td valign="top">
具有單一內容的項目。 ContentView 會很少使用它自己。 其目的是要做為使用者定義的複合檢視的基底類別。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentViewDemoPage.cs"><img src="layouts-images/ContentView.png" title="ContentView 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">畫面格</a>
    </td>
    <td valign="top">
包含單一子系，有些框架選項使用的項目。 框架有一個預設<a href="https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/">Xamarin.Forms.Layout.Padding</a>為 20。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/FrameDemoPage.cs"><img src="layouts-images/Frame.png" title="框架範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>
    </td>
    <td valign="top">
項目可捲動內容後，才需要。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ScrollViewDemoPage.cs"><img src="layouts-images/ScrollView.png" title="ScrollView 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a>
    </td>
    <td valign="top">
顯示內容控制項範本，與基底類別的項目<a href=""/api/type/Xamarin.Forms.ContentView/">ContentView</a>。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="layouts-images/TemplatedView.png" title="TemplatedView 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a>
    </td>
    <td valign="top">
在絕對要求的位置上放置子項目。 指派範圍的錨點和使用者定義的位置和大小的控制項。
    </td>
    <td valign="top">
      <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/AbsoluteLayoutDemoPage.cs"><img src="layouts-images/AbsoluteLayout.png" title="AbsoluteLayout 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">Grid</a>
    </td>
    <td valign="top">
包含檢視的資料列和資料行中排列版面配置。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/GridDemoPage.cs"><img src="layouts-images/Grid.png" title="格線範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/%601">配置</a>使用<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/">條件約束</a>配置 s 其子系。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/RelativeLayoutDemoPage.cs"><img src="layouts-images/RelativeLayout.png" title="RelativeLayout 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/">配置</a>的垂直或水平可以導向的一行中放置子項目。 此配置會子範圍自動設定配置循環期間。 指派範圍的使用者將會覆寫，因此不能設定子項目使用者。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StackLayoutDemoPage.cs"><img src="layouts-images/StackLayout.png" title="StackLayout 範例" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 圖庫 （範例）](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/namespace/Xamarin.Forms)
