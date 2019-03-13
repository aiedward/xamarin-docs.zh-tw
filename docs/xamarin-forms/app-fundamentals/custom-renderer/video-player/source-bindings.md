---
title: 將影片來源繫結至播放程式
description: 本文說明如何使用 Xamarin.Forms 將影片來源繫結至影片播放程式。
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0fe170aaa880aebcaff7899c440a0522e1f83a0c
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051117"
---
# <a name="binding-video-sources-to-the-player"></a>將影片來源繫結至播放程式

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)

當 `VideoPlayer` 檢視的 `Source` 屬性設定為新影片檔案時，會停止播放現有的影片並開始播放新影片。 如 [**VideoPlayerDemos**](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) 範例的 [Select Web Video] \(選取網路影片\) 頁面所示。 此頁面包含 `ListView`，以及從 **App.XAML** 檔案參考的三部影片標題：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

選取影片時，會執行程式碼後置檔案中的 `ItemSelected` 事件處理常式。 處理常式會移除標題中的任何空格與所有格符號，並以此作為索引鍵來取得 **App.XAML** 檔案中定義的其中一個資源。 然後，將該 `UriVideoSource` 物件設定為 `VideoPlayer` 的 `Source` 屬性。

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

當頁面第一次載入時，不會在 `ListView` 中選取任何項目，因此您必須選取一個才能開始播放影片：

[![選取網路影片](source-bindings-images/selectwebvideo-small.png "選取網路影片")](source-bindings-images/selectwebvideo-large.png#lightbox "選取網路影片")

`VideoPlayer` 的 `Source` 屬性受到可繫結屬性的支援，這表示它可以是資料繫結的目標。 如 [Bind to VideoPlayer] \(繫結至 VideoPlayer\) 頁面所示。 **BindToVideoPlayer.XAML** 檔案中的標記支援下列類別，其中封裝影片標題和對應的 `VideoSource` 物件：

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

**BindToVideoPlayer.XAML** 檔案中的 `ListView` 包含這些 `VideoInfo` 物件的陣列，每個物件會以影片標題及 **App.XAML** 資源字典中的 `UriVideoSource` 物件初始化：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

`VideoPlayer` 的 `Source` 屬性會繫結至 `ListView`。 繫結的 `Path` 會指定為 `SelectedItem.VideoSource`，這是由兩個屬性組成的複合路徑：`SelectedItem` 是 `ListView` 的屬性。 選取的項目類型為 `VideoInfo`，其中包含 `VideoSource` 屬性。

如同第一頁 [Select Web Video] \(選取網路影片\)，一開始不會從 `ListView` 選取任何項目，因此您必須選取其中一個影片才能開始播放。


## <a name="related-links"></a>相關連結

- [Video Player Demos (Samples)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) (視訊播放程式示範 (範例))
