---
title: XAML 控制項
description: 所有在 Xamarin.Forms 中定義的檢視，可從 XAML 檔案參考。
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: 8217c6436cc8cfe8f4b9c6cc3445157319e8c0bf
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741108"
---
# <a name="xaml-controls"></a>XAML 控制項

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)

檢視是使用者介面物件，例如標籤、 按鈕和滑桿，一般稱為*控制項*或是*widget*其他圖形化的程式設計環境中。 支援的所有衍生自的 Xamarin.Forms 檢視[ `View` ](xref:Xamarin.Forms.View)類別。

所有在 Xamarin.Forms 中定義的檢視，可從 XAML 檔案參考。

## <a name="views-for-presentation"></a>檢視簡報

|     |     |
| --- | --- |
| <h3>BoxView</h3>會顯示特定色彩的矩形。<p align="center">![螢幕擷取畫面的 BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [指南](https://developer.xamarin.com/guides/xamarin-forms/user-interface/boxview/) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Image</h3>顯示點陣圖。<p align="center">![映像的螢幕擷取畫面](xaml-controls-images/Image.png "映像")</p>[API](xref:Xamarin.Forms.Image) / [指南](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>ThisAddIn</h3>會顯示一或多個文字行。<p align="center">![標籤的螢幕擷取畫面](xaml-controls-images/Label.png "標籤")</p>[API](xref:Xamarin.Forms.Label) / [指南](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>地圖</h3>顯示地圖。<p align="center">![對應的螢幕擷取畫面](xaml-controls-images/Map.png "對應")</p>[API](xref:Xamarin.Forms.Maps.Map) / [指南](~/xamarin-forms/user-interface/map.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>顯示網頁或 HTML 內容。<p align="center">![螢幕擷取畫面的 WebView](xaml-controls-images/WebView.png "web 檢視")</p>[API](xref:Xamarin.Forms.WebView) / [指南](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>啟動命令的檢視

|     |     |
| --- | --- |
| <h3>按鈕</h3>矩形物件中的顯示文字。<p align="center">![按鈕的螢幕擷取畫面](xaml-controls-images/Button.png "按鈕")</p>[API](xref:Xamarin.Forms.Button) / [指南](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>矩形物件中顯示影像。<p align="center">![螢幕擷取畫面的 ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [指南](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>搜尋列</h3>顯示搜尋列中的，執行搜尋。<p align="center">![螢幕擷取畫面的 SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>設定值的檢視

|     |     |
| --- | --- |
| <h3>滑桿</h3>允許選取`double`從連續範圍的值。<p align="center">![螢幕擷取畫面的滑桿](xaml-controls-images/Slider.png "滑桿")</p>[API](xref:Xamarin.Forms.Slider) / [指南](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Stepper</h3>允許選取`double`累加式的範圍內的值。<p align="center">![螢幕擷取畫面的步進](xaml-controls-images/Stepper.png "步進")</p>[API](xref:Xamarin.Forms.Stepper) / [指南](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>參數</h3>允許選取`boolean`值。<p align="center">![參數的螢幕擷取畫面](xaml-controls-images/Switch.png "交換器")</p>[API](xref:Xamarin.Forms.Switch) | <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>允許選取的日期。<p align="center">![螢幕擷取畫面的 DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [指南](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>允許選取的時間。<p align="center">![螢幕擷取畫面的 TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [指南](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>編輯文字的檢視

|     |     |
| --- | --- |
| <h3>進入</h3>可讓單行輸入及編輯的文字。<p align="center">![項目的螢幕擷取畫面](xaml-controls-images/Entry.png "項目")</p>[API](xref:Xamarin.Forms.Entry) / [指南](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>編輯器</h3>允許多行文字輸入和編輯。<p align="center">![編輯器的螢幕擷取畫面](xaml-controls-images/Editor.png "標籤")</p>[API](xref:Xamarin.Forms.Editor) / [指南](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>表示活動的檢視

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>顯示若要顯示的應用程式參與的冗長的活動，而不需要提供任何資訊指出進度的動畫。<p align="center">![螢幕擷取畫面的 ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>進度列</h3>顯示的動畫，以顯示應用程式正在進行長時間的活動。<p align="center">![螢幕擷取畫面的 ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>顯示集合的檢視

|     |     |
| --- | --- |
| <h3>CollectionView</h3>顯示可捲動清單項目可選取的資料，使用不同的版面配置規格。<p align="center">![螢幕擷取畫面的 CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[快速入門](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>顯示可捲動的可選取的資料項目清單。<p align="center">![ListView 的螢幕擷取畫面](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [指南](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Picker</h3>顯示選取項目 清單中的文字字串。<p align="center">![選擇器的螢幕擷取畫面](xaml-controls-images/Picker.png "選擇器")</p>[API](xref:Xamarin.Forms.Picker) / [指南](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>會顯示一份互動式的資料列。<p align="center">![螢幕擷取畫面的 TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [指南](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文件](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
