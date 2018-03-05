---
title: "Xamarin.Forms 檢視"
description: "Xamarin.Forms 檢視是跨平台行動裝置的使用者介面的建置組塊。"
ms.topic: article
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: df8c8463b2556035c5369c70cb10dbc3dc6b6743
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-views"></a>Xamarin.Forms 檢視

_Xamarin.Forms 檢視是跨平台行動裝置的使用者介面的建置組塊。_

<style>.tableimg {最大寬度： 無 ！ 重要;}</style>

## <a name="views"></a>檢視

Xamarin.Forms 使用 word*檢視*指視覺化的物件，例如按鈕、 標籤或文字項目方塊-可能會更常稱為控制項的 widget。

這些 UI 項目通常會是子類別的[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)。

<br clear="right" />

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a>
    </td>
    <td valign="top">
用來指出項目正在進行中的視覺控制項。 這個控制項的項目發生，而不需進度資訊的使用者提供一個視覺線索。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ActivityIndicatorDemoPage.cs"><img src="views-images/ActivityIndicator.png" title="ActivityIndicator 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>用來繪製實心彩色的矩形。 BoxView 執行初始原型時的實用替代的映像或自訂的項目。 BoxView 有 40 x 40 的預設大小要求。 如果您需要不同的大小，指派<a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/">VisualElement.WidthRequest</a>和<a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/">VisualElement.HeightRequest</a>。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/BoxViewDemoPage.cs"><img src="views-images/BoxView.png" title="BoxView 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Button</a>
    </td>
    <td align="center" valign="top">
按鈕<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>觸控事件的回應。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ButtonDemoPage.cs"><img src="views-images/Button.png" title="按鈕範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>，可讓日期挑選。 日期選擇器的視覺表示法是非常類似於其中<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">項目</a>，不同之處在於挑選一個日期的特殊控制項出現取代鍵盤 </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/DatePickerDemoPage.cs"><img src="views-images/DatePicker.png" title="日期選擇器範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a>
    </td>
    <td valign="top">
可以編輯多行文字的控制項。 針對單一行項目，請參閱<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">項目</a>。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EditorDemoPage.cs"><img src="views-images/Editor.png" title="編輯器範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Entry</a>
    </td>
    <td valign="top">
控制項可以編輯單行文字。 項目是單行文字項目。 它是最適合用來收集資訊，例如使用者名稱和密碼的離散小型。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="views-images/Entry.png" title="項目範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">映像</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>保存映像。
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">映像 API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/images.md">使用映像</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageDemoPage.cs">示範來源</a>
    </td>
    <td>
    <img src="views-images/Image.png" title="影像範例" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">標籤</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>會顯示的文字中讀取的唯一格式。 標籤用來顯示單行文字項目，以及多行文字區塊。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/LabelDemoPage.cs"><img src="views-images/Label.png" title="標籤範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a>
    </td>
    <td valign="top">
<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/">ItemView</a>會以垂直清單顯示的資料集合。
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView 應用程式開發介面</a>
    <br />
    <a href="~/xamarin-forms/user-interface/listview/index.md">ListView 文件</a>
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ListViewDemoPage.cs"><img src="views-images/ListView.png" title="ListView 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/">OpenGLView</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>顯示 OpenGL 內容。
    <ul>
      <li>僅適用於 iOS 和 Android 專案 （不支援 Windows Phone）。
      <li>需要參考<b>OpenTK 1.0</b> iOS 和 Android 專案中的組件。</li>
      <li>最適合用於共用專案的專案。如果使用 PCL 中，則 DependencyService 也為必要。</li>
    </ul>
    </td>
    <td>
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/"><img src="views-images/OpenGL.png" title="OpenGlView 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Picker</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>控制項清單中挑選項目。 選擇器的視覺表示法是類似於<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">項目</a>，但選擇器控制項顯示取代鍵盤。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/PickerDemoPage.cs"><img src="views-images/Picker.png" title="選擇器範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">ProgressBar</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>指出進度的控制項。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ProgressBarDemoPage.cs"><img src="views-images/ProgressBar.png" title="進度列範例類別 ="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>提供搜尋方塊的控制項。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SearchBarDemoPage.cs"><img src="views-images/SearchBar.png" title="SearchBar 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">滑桿</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>輸入線性值的控制項。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SliderDemoPage.cs"><img src="views-images/Slider.png" title="滑桿範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Stepper</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>輸入離散值，控制限制為一個範圍。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StepperDemoPage.cs"><img src="views-images/Stepper.png" title="Stepper 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">參數</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>提供已切換之的值的控制項。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchDemoPage.cs"><img src="views-images/Switch.png" title="參數範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>保存的資料列<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">儲存格</a>s。
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView 應用程式開發介面</a>
    <br />
    <a href="~/xamarin-forms/user-interface/tableview.md">TableView 文件</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TableViewFormDemoPage.cs">示範來源</a>
    </td>
    <td>
    <img src="views-images/TableViewNewest.png" title="TableView 範例" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>提供時間挑選控制項。 Timeupdown 的視覺表示法是非常類似於其中<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">項目</a>，不同之處在於挑選一次的特殊控制項出現取代鍵盤。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TimePickerDemoPage.cs"><img src="views-images/TimePicker.png" title="Timeupdown 範例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">網頁檢視</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">檢視</a>呈現 HTML 內容。
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView 應用程式開發介面</a>
    <br />
    <a href="~/xamarin-forms/user-interface/webview.md">網頁檢視文件</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/WebViewDemoPage.cs">示範來源</a>
    </td>
    <td>
    <img src="views-images/WebView.png" title="WebView 範例" class="tableimg">
    </td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 圖庫 （範例）](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/root/Xamarin.Forms/)
