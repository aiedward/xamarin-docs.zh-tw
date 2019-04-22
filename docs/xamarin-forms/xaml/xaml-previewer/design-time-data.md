---
title: 使用 XAML 預覽程式的設計階段資料
description: 這篇文章說明如何使用以 XAML 預覽程式中顯示大量資料的版面配置，而不需執行您的應用程式的設計階段資料。
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 0ff9f8b5ee6f9468650b6535745706bee8f96536
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58866354"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>使用 XAML 預覽程式的設計階段資料

_某些版面配置是難想像沒有資料。若要充分利用預覽您的大量資料的頁面中 XAML 預覽程式中使用這些秘訣。_

## <a name="design-time-data-basics"></a>設計階段資料的基本概念

設計階段資料是假的資料，您將設定為在 XAML 預覽程式中更輕鬆地以視覺化方式檢視您的控制項。 若要開始，請將下列程式碼行加入 XAML 頁面的標頭：

```csharp
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

新增命名空間之後, 您可以放入`d:`前面的任何屬性或其顯示在 XAML 預覽程式中的控制項。 具有項目`d:`不會顯示在執行階段。

例如，您可以將文字加入通常具有資料繫結到它的標籤。

```csharp
<Label Text={Binding Name} d:Text="Name" />
```

[![設計時間資料標籤中的文字](xaml-previewer-images/designtimedata-label-sm.png "設計時間的文字資料標籤")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

 在此範例中，而不需要`d:Text`，XAML 預覽程式會顯示任何標籤。 相反地，它會顯示 「 名稱 」 其中的標籤時，會在執行階段具有實際資料。

您可以使用`d:`Xamarin.Forms 控制項，例如色彩、 字型大小、 和間距的任何屬性。 您甚至可以將它加入控制項本身：

```csharp
<d:Button Text="Design Time Button" />
```

[![設計階段資料與按鈕控制項](xaml-previewer-images/designtimedata-controls-sm.png "設計階段資料與按鈕控制項")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

在此範例中，按鈕才會出現在設計階段。 使用這個方法可放在預留位置[XAML 預覽程式不支援的自訂控制項](render-custom-controls.md)。

## <a name="preview-images-at-design-time"></a>在設計階段預覽影像

您可以設定設計時間來源所繫結至頁面，或以動態方式載入的映像。 在您的 Android 專案中，新增您想要在 XAML 預覽程式中顯示的映像**資源 > Drawable**資料夾。 在您的 iOS 專案中新增影像**資源**資料夾。 您接著可以在 XAML 預覽程式中顯示該映像，在設計階段：

```csharp
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```
[![設計階段資料與映像](xaml-previewer-images/designtimedata-image-sm.png "設計 iamges 時間資料")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Listview 的設計階段資料

Listview 是一種常見的方式顯示資料的行動應用程式。 不過，它們很難不包含實際的資料視覺化。 若要使用這些設計階段資料，您必須建立設計階段陣列作為 ItemsSource。 XAML 預覽程式會顯示功能的設計階段您 ListView 中的陣列。

```csharp
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

[![設計時間資料的 ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "設計時間資料的 ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

此範例中會顯示三個 TextCells 的 ListView 中 XAML 預覽程式。 您可以變更`x:String`到專案中現有的資料模型。

請參閱[James Montemagno Hanselman.Forms 應用程式](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L36-L57)更複雜的範例。


## <a name="alternative-hardcode-a-static-viewmodel"></a>替代程序：硬式編碼的靜態 ViewModel

如果您不想要新增為個別控制項的設計階段資料，您可以繫結至您的頁面設定的模擬 （mock） 的資料存放區。 請參閱 James Montemagno[新增設計階段資料的部落格文章](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)以了解如何將繫結至 XAML 中的靜態 ViewModel。

## <a name="troubleshooting"></a>疑難排解

### <a name="requirements"></a>需求

設計階段資料需要的最低版本為 Xamarin.Forms 3.6。

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense 會顯示曲線下的 我的設計階段資料

這是已知的問題，而且會在下一版的 Visual Studio 中修正。 專案仍會建置無誤。

### <a name="the-xaml-previewer-stopped-working"></a>XAML 預覽程式停止運作

請嘗試關閉並重新開啟 XAML 檔案，並清除和重建您的專案。
