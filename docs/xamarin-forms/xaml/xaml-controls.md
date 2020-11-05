---
title: XAML 控制項
description: 中定義的所有視圖都 Xamarin.Forms 可以從 XAML 檔案參考。
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 16186ffcdda5e9d67c736556aa8da3dd8c305732
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372193"
---
# <a name="xaml-controls"></a>XAML 控制項

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/formsgallery)

Views 是使用者介面物件，例如標籤、按鈕和滑杆，在其他圖形化程式設計環境中通常稱為 *控制項* 或 *小* 工具。 Xamarin.Forms所有衍生自類別的所支援的視圖 [`View`](xref:Xamarin.Forms.View) 。

中定義的所有視圖都 Xamarin.Forms 可以從 XAML 檔案參考。

## <a name="views-for-presentation"></a>簡報的檢視

| 檢視 | 範例 |
| --- | --- |
| <h3>BoxView</h3>顯示特定色彩的矩形。<p align="center">![BoxView 的螢幕擷取畫面](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView)  / [指南](~/xamarin-forms/user-interface/boxview.md) | <p valign="center"><pre>&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>橢圓形</h3>顯示橢圓形或圓形。<p align="center">![省略號的螢幕擷取畫面](xaml-controls-images/Ellipse.png "橢圓形")</p>[API](xref:Xamarin.Forms.Shapes.Ellipse)  / [指南](~/xamarin-forms/user-interface/shapes/ellipse.md) | <p valign="center"><pre>&lt;Ellipse Fill="Red"<br />         WidthRequest="150"<br />         HeightRequest="50"<br />         HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>展開工具</h3>提供可展開的容器來裝載任何內容。<p align="center">![擴充項的螢幕擷取畫面](xaml-controls-images/Expander.png "展開工具")</p>[指南](~/xamarin-forms/user-interface/expander.md) | <pre>&lt;Expander&gt;<br />    &lt;Expander.Header&gt;<br />        &lt;Label Text="Baboon" /&gt;<br />    &lt;/Expander.Header&gt;<br />    &lt;Image Source="Baboon.png"<br />           Aspect="AspectFill" /&gt;<br />&lt;/Expander&gt;</pre></p> |
| <h3>映像</h3>顯示點陣圖。<p align="center">![影像的螢幕擷取畫面](xaml-controls-images/Image.png "Image")</p>[API](xref:Xamarin.Forms.Image)  / [指南](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>標籤</h3>顯示一或多行文字。<p align="center">![標籤的螢幕擷取畫面](xaml-controls-images/Label.png "標籤")</p>[API](xref:Xamarin.Forms.Label)  / [指南](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>線條</h3>顯示線條。<p align="center">![線條的螢幕擷取畫面](xaml-controls-images/Line.png "線條")</p>[API](xref:Xamarin.Forms.Shapes.Line)  / [指南](~/xamarin-forms/user-interface/shapes/line.md) | <p valign="center"><pre>&lt;Line X1="40"<br />      Y1="0"<br />      X2="0"<br />      Y2="120"<br />      Stroke="Red"<br />      HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>對應</h3>顯示地圖。<p align="center">![地圖的螢幕擷取畫面](xaml-controls-images/Map.png "對應")</p>[API](xref:Xamarin.Forms.Maps.Map)  / [指南](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>播放影片或音訊。<p align="center">![MediaElement 的螢幕擷取畫面](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement)  / [指南](~/xamarin-forms/user-interface/mediaelement.md) | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>路徑</h3>顯示曲線和複雜圖形。<p align="center">![路徑的螢幕擷取畫面](xaml-controls-images/Path.png "路徑")</p>[API](xref:Xamarin.Forms.Shapes.Path)  / [指南](~/xamarin-forms/user-interface/shapes/path.md) | <p valign="center"><pre>&lt;Path Stroke="Black"<br />      Aspect="Uniform"<br />      HorizontalOptions="Center"<br />      HeightRequest="100"<br />      WidthRequest="100"<br />      Data="M13.9,16.2<br />            L32,16.2 32,31.9 13.9,30.1Z<br />            M0,16.2<br />            L11.9,16.2 11.9,29.9 0,28.6Z<br />            M11.9,2<br />            L11.9,14.2 0,14.2 0,3.3Z<br />            M32,0<br />            L32,14.2 13.9,14.2 13.9,1.8Z" /&gt;</pre></p> |
| <h3>Polygon</h3>顯示多邊形。<p align="center">![多邊形的螢幕擷取畫面](xaml-controls-images/Polygon.png "多邊形")</p>[API](xref:Xamarin.Forms.Shapes.Polygon)  / [指南](~/xamarin-forms/user-interface/shapes/polygon.md) | <p valign="center"><pre>&lt;Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96,<br/>                 50 96, 48 192, 150 200 144 48"<br />         Fill="Blue"<br />         Stroke="Red"<br />         StrokeThickness="3"<br />         HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>聚合線條</h3>顯示一連串連接的直線。<p align="center">![折線的螢幕擷取畫面](xaml-controls-images/Polyline.png "聚合線條")</p>[API](xref:Xamarin.Forms.Shapes.Polyline)  / [指南](~/xamarin-forms/user-interface/shapes/Polyline.md) | <p valign="center"><pre>&lt;Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0<br />                  43,60 48,30 100,30"<br />          Stroke="Red"<br />          HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>矩形</h3>顯示矩形或正方形。<p align="center">![矩形的螢幕擷取畫面](xaml-controls-images/Rectangle.png "矩形")</p>[API](xref:Xamarin.Forms.Shapes.Rectangle)  / [指南](~/xamarin-forms/user-interface/shapes/rectangle.md) | <p valign="center"><pre>&lt;Rectangle Fill="Red"<br />           WidthRequest="150"<br />           HeightRequest="50"<br />           HorizontalOptions="Center" /&gt;</pre></p> |  
| <h3>WebView</h3>顯示網頁或 HTML 內容。<p align="center">![Web 程式圖的螢幕擷取畫面](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView)  / [指南](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>起始命令的檢視

| 檢視 | 範例 |
| --- | --- |
| <h3>按鈕</h3>顯示矩形物件中的文字。<p align="center">![按鈕的螢幕擷取畫面](xaml-controls-images/Button.png "按鈕")</p>[API](xref:Xamarin.Forms.Button)  / [指南](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>在矩形物件中顯示影像。<p align="center">![ImageButton 的螢幕擷取畫面](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton)  / [指南](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RadioButton</h3>允許從集合中選取一個選項。<p align="center">![選項按鈕的螢幕擷取畫面](xaml-controls-images/RadioButton.png "RadioButton")</p>[指南](~/xamarin-forms/user-interface/radiobutton.md) | <p valign="center"><pre>&lt;RadioButton Text="Pineapple"<br/>             CheckedChanged="OnRadioButtonCheckedChanged" /&gt;</pre></p> |
| <h3>RefreshView</h3>針對可滾動的內容提供提取重新整理功能。<p align="center">![Refreshview 拖動的螢幕擷取畫面](xaml-controls-images/RefreshView.png "RefreshView")</p>[指南](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>搜尋列</h3> 接受用來執行搜尋的使用者輸入。<p align="center">![SearchBar 的螢幕擷取畫面](xaml-controls-images/SearchBar.png "搜尋列")</p>[指南](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder="Enter search term"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> 提供滑動手勢所顯示的內容功能表項目。<p align="center">![SwipeView 的螢幕擷取畫面](xaml-controls-images/SwipeView.png "SwipeView")</p>[指南](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>設定值的檢視

| 檢視 | 範例 |
| --- | --- |
| <h3>CheckBox</h3>允許選取 `boolean` 值。<p align="center">![核取方塊的螢幕擷取畫面](xaml-controls-images/CheckBox.png "CheckBox")</p> [指南](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>滑桿</h3>允許 `double` 從連續範圍中選取值。<p align="center">![滑杆的螢幕擷取畫面](xaml-controls-images/Slider.png "滑桿")</p>[API](xref:Xamarin.Forms.Slider)  / [指南](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Stepper</h3>允許 `double` 從增量範圍中選取值。<p align="center">![分檔器的螢幕擷取畫面](xaml-controls-images/Stepper.png "Stepper")</p>[API](xref:Xamarin.Forms.Stepper)  / [指南](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>參數</h3>允許選取 `boolean` 值。<p align="center">![交換器的螢幕擷取畫面](xaml-controls-images/Switch.png "參數")</p>[API](xref:Xamarin.Forms.Switch)  / [指南](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>允許選取日期。<p align="center">![DatePicker 的螢幕擷取畫面](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker)  / [指南](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>允許選取一段時間。<p align="center">![TimePicker 的螢幕擷取畫面](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker)  / [指南](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>編輯文字的檢視

| 檢視 | 範例 |
| --- | --- |
| <h3>進入</h3>允許輸入和編輯一行文字。<p align="center">![專案的螢幕擷取畫面](xaml-controls-images/Entry.png "進入")</p>[API](xref:Xamarin.Forms.Entry)  / [指南](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>編輯器</h3>允許輸入和編輯多行文字。<p align="center">![編輯器的螢幕擷取畫面](xaml-controls-images/Editor.png "標籤")</p>[API](xref:Xamarin.Forms.Editor)  / [指南](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>表示活動的檢視

| 檢視 | 範例 |
| --- | --- |
| <h3>ActivityIndicator</h3>顯示一個動畫，顯示應用程式正在參與冗長的活動，而不會提供任何進度指示。<p align="center">![ActivityIndicator 的螢幕擷取畫面](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator)  / [指南](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>進度列</h3>顯示動畫，以顯示應用程式正在進行長時間的活動。<p align="center">![ProgressBar 的螢幕擷取畫面](xaml-controls-images/ProgressBar.png "進度列")</p>[API](xref:Xamarin.Forms.ProgressBar)  / [指南](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>顯示集合的檢視

| 檢視 | 範例 |
| --- | --- |
| <h3>CarouselView</h3>顯示可滾動的資料項目清單。<p align="center">![CarouselView 的螢幕擷取畫面](xaml-controls-images/CarouselView.png "CarouselView")</p>[指南](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>使用不同的版面配置規格，顯示可選取資料項目的可滾動清單。<p align="center">![CollectionView 的螢幕擷取畫面](xaml-controls-images/CollectionView.png "CollectionView")</p>[指南](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />                ItemsLayout="VerticalGrid, 2" /&gt;</pre></p> |
| <h3>IndicatorView</h3>顯示表示中專案數目的指標 `CarouselView` 。<p align="center">![IndicatorView 的螢幕擷取畫面](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[指南](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>顯示可選取資料項目的可滾動清單。<p align="center">![ListView 的螢幕擷取畫面](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView)  / [指南](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Picker</h3>顯示文字字串清單中的選取專案。<p align="center">![選擇器的螢幕擷取畫面](xaml-controls-images/Picker.png "Picker")</p>[API](xref:Xamarin.Forms.Picker)  / [指南](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>顯示互動式資料列的清單。<p align="center">![TableView 的螢幕擷取畫面](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView)  / [指南](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>相關連結

- [Xamarin.Forms FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 樣品](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 檔](/dotnet/api/xamarin.forms?view=xamarin-forms)