---
title: 繫結到播放程式的視訊來源
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: bebf6fd905dd374822eb6974b28f1ac92a36c1bc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="binding-video-sources-to-the-player"></a>繫結到播放程式的視訊來源

當`Source`屬性`VideoPlayer`檢視設為新的視訊檔、 現有的視訊停止播放並開始新的視訊。 這示範**選取 Web 視訊**頁面[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)範例。 此頁面包含`ListView`從參考的三個視訊的標題與**App.xaml**檔案：

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

選取視訊時，`ItemSelected`程式碼後置檔案中的事件處理常式會執行。 移除任何空格和所有格符號的標題中的處理常式，並使用該值做為索引鍵來取得的資源定義中的其中一個**App.xaml**檔案。 確認`UriVideoSource`物件然後將設定為`Source`屬性`VideoPlayer`。

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

當第一次載入頁面時，選取任何項目`ListView`，因此您必須選取一個開始播放的視訊：

[![選取 Web 視訊](source-bindings-images/selectwebvideo-small.png "選取 Web 視訊")](source-bindings-images/selectwebvideo-large.png#lightbox "選取 Web 視訊")

`Source`屬性`VideoPlayer`受到可繫結的屬性，表示它可以是資料繫結的目標。 這示範**繫結至 VideoPlayer**頁面。 中的標記**BindToVideoPlayer.xaml**檔案會受到下列類別會封裝的視訊和對應的標題`VideoSource`物件：

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

`ListView`中**BindToVideoPlayer.xaml**檔案包含的這些陣列`VideoInfo`物件，每個視訊標題以初始化和`UriVideoSource`物件從資源字典中**App.xaml**:

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

`Source`屬性`VideoPlayer`繫結至`ListView`。 `Path`繫結會指定為`SelectedItem.VideoSource`，這是兩個屬性所組成的複合路徑：`SelectedItem`屬性`ListView`。 選取的項目屬於型別`VideoInfo`，其具有`VideoSource`屬性。

如同第一個**選取 Web 視訊** 頁面上，從一開始選取任何項目`ListView`，因此您必須選取其中一個視訊後，才開始播放。


## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
