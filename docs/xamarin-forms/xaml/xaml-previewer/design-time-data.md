---
title: 將設計階段資料與 XAML 預覽器搭配使用
description: 本文說明如何使用設計階段資料, 在 XAML 預覽程式中顯示資料繁重的配置, 而不需要執行您的應用程式。
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: a6a34615adc9cf290ff6bf9dd344487e5f29cfa2
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887846"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>將設計階段資料與 XAML 預覽器搭配使用

_有些版面配置難以視覺化而不會有資料。使用這些秘訣, 讓您充分利用 XAML 預覽程式中的資料繁重頁面。_

## <a name="design-time-data-basics"></a>設計階段資料基本概念

設計階段資料是您設定的假資料, 讓您的控制項更容易在 XAML 預覽程式中視覺化。 若要開始使用, 請將下列幾行程式碼新增至 XAML 頁面的標頭:

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

加入命名空間之後, 您可以將`d:`它放在任何屬性或控制項的前方, 以在 XAML 預覽中顯示它。 具有的`d:`專案不會在執行時間顯示。

例如, 您可以將文字新增至通常已系結資料的標籤。

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![以標籤中的文字設計階段資料](xaml-previewer-images/designtimedata-label-sm.png "以文字加上標籤的設計階段資料")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

在此範例中, `d:Text`如果沒有, XAML 預覽程式將不會對標籤顯示任何內容。 相反地, 它會顯示「Name!」 其中的標籤在執行時間會有實際資料。

您可以將`d:`與任何屬性用於 Xamarin. form 控制項, 例如色彩、字型大小和間距。 您甚至可以將它新增至控制項本身:

```xaml
<d:Button Text="Design Time Button" />
```

[![使用 Button 控制項設計階段資料](xaml-previewer-images/designtimedata-controls-sm.png "使用 Button 控制項設計階段資料")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

在此範例中, 按鈕只會在設計階段出現。 您可以使用這個方法, 針對[XAML 預覽程式不支援的自訂控制項](render-custom-controls.md)放入中的預留位置。

## <a name="preview-images-at-design-time"></a>在設計階段預覽影像

您可以設定系結至頁面或以動態方式載入之影像的設計階段來源。 在您的 Android 專案中, 將您想要在 XAML 預覽程式中顯示的影像新增至**資源 > 可繪製**資料夾。 在您的 iOS 專案中, 將影像新增至 [**資源**] 資料夾。 接著, 您可以在設計階段于 XAML 預覽程式中顯示該影像:

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![以影像設計階段資料](xaml-previewer-images/designtimedata-image-sm.png "使用 Iamges 設計階段資料")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Listview 的設計階段資料

Listview 是在行動裝置應用程式中顯示資料的熱門方式。 不過, 如果沒有實際的資料, 就很容易將它們視覺化。 若要搭配使用設計階段資料, 您必須建立設計階段陣列做為 ItemsSource 使用。 XAML 預覽程式會在設計階段顯示 ListView 中該陣列的內容。

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![使用 ListView 設計階段資料](xaml-previewer-images/designtimedata-itemssource-sm.png "使用 ListView 設計階段資料")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

這個範例會在 XAML 預覽器中顯示 ListView 的三個 TextCells。 您可以變更`x:String`為專案中現有的資料模型。

如需更複雜的範例, 請參閱[James Montemagno 的 Hanselman 應用程式](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L26-L47)。

## <a name="alternative-hardcode-a-static-viewmodel"></a>判斷硬式編碼靜態 ViewModel

如果您不想要將設計階段資料新增至個別控制項, 您可以設定模擬資料存放區來系結至您的頁面。 請參閱 James Montemagno 的[關於新增設計階段資料的 blog 文章](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data), 以瞭解如何在 XAML 中系結至靜態 ViewModel。

## <a name="troubleshooting"></a>疑難排解

### <a name="requirements"></a>需求

設計階段資料需要最小版本的 Xamarin. 表單3.6。

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense 在 [我的設計階段資料] 底下顯示波浪線

這是已知的問題, 將會在即將推出的 Visual Studio 版本中修正。 專案仍會建立, 而不會發生錯誤。

### <a name="the-xaml-previewer-stopped-working"></a>XAML 預覽器已停止運作

請嘗試關閉並重新開啟 XAML 檔案, 並清除和重建您的專案。
