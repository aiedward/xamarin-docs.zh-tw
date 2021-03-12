---
title: 搭配使用設計階段資料與 XAML 預覽
description: 本文說明如何使用設計階段資料，在不執行應用程式的情況下，于 XAML 預覽程式中顯示資料繁重的版面配置。
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4d6eb3c1c983cb1add671d03d71cbb0499f1d89
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602043"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>搭配使用設計階段資料與 XAML 預覽

_某些版面配置很難在沒有資料的情況下視覺化。您可以使用這些秘訣，充分利用 XAML 預覽程式中的大量資料頁面預覽。_

> [!WARNING]
> XAML 預覽程式在 Visual Studio 2019 16.8 版和 Visual Studio for Mac 8.8 版中已淘汰，並由 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中的 XAML 熱重載功能所取代。
> 深入瞭解 [檔中的](~/xamarin-forms/xaml/hot-reload.md)XAML 熱重載。

> [!NOTE]
> 如果您使用 Windows Presentation Foundation (WPF) 或 UWP，請參閱將[設計階段資料與適用于桌面應用程式的 XAML 設計](/visualstudio/xaml-tools/xaml-designtime-data)工具搭配使用

## <a name="design-time-data-basics"></a>設計階段資料基本概念

設計階段資料是您設定的假資料，可讓您在 XAML 預覽程式中更輕鬆地將控制項視覺化。 若要開始使用，請將下列程式程式碼新增至您的 XAML 頁面標頭：

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

新增命名空間之後，您可以 `d:` 在任何屬性或控制項之前放置，以將其顯示在 XAML 預覽程式中。 `d:`未在執行時間顯示的元素。

例如，您可以將文字新增至通常有資料系結的標籤。

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![使用標籤中的文字設計階段資料](xaml-previewer-images/designtimedata-label-sm.png "以文字 a 標籤設計階段資料")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

在此範例中，如果沒有 `d:Text` ，XAML 預覽程式就不會顯示標籤的任何內容。 相反地，它會顯示「名稱！」 其中的標籤會在執行時間有實際的資料。

您可以使用 `d:` 與控制項的任何屬性 Xamarin.Forms ，例如色彩、字型大小和間距。 您甚至可以將它新增至控制項本身：

```xaml
<d:Button Text="Design Time Button" />
```

[![使用按鈕控制項設計階段資料](xaml-previewer-images/designtimedata-controls-sm.png "使用按鈕控制項設計階段資料")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

在此範例中，按鈕只會出現在設計階段。 您可以使用這個方法，為 [XAML 預覽程式不支援的自訂控制項](render-custom-controls.md)放入預留位置。

## <a name="preview-images-at-design-time"></a>在設計階段預覽影像

您可以針對系結至頁面或動態載入的影像，設定設計階段來源。 在您的 Android 專案中，將您想要在 XAML 預覽程式中顯示的影像加入至 **資源 > 可繪製** 的資料夾中。 在您的 iOS 專案中，將映射新增至 [ **Resources** ] 資料夾。 然後，您可以在 XAML 預覽程式中的設計階段顯示該映射：

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![使用影像設計階段資料](xaml-previewer-images/designtimedata-image-sm.png "使用 iamges 設計階段資料")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Listview 的設計階段資料

Listview 是在行動應用程式中顯示資料的常用方式。 但是，不需要實際的資料就能進行視覺化。 若要搭配使用設計階段資料，您必須建立設計階段陣列，以做為 ItemsSource 使用。 XAML 預覽程式會在設計階段顯示 ListView 中該陣列的內容。

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

此範例會在 XAML 預覽程式中顯示三個 TextCells 的 ListView。 您可以變更 `x:String` 為專案中現有的資料模型。

您也可以建立資料物件的陣列。 例如，您 `Monkey` 可以將資料物件的公用屬性視為設計階段資料：

```csharp
namespace Monkeys.Models
{
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
    }
}
```

若要在 XAML 中使用類別，您必須將命名空間匯入根節點：

```xaml
xmlns:models="clr-namespace:Monkeys.Models"
```

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type models:Monkey}">
                <models:Monkey Name="Baboon" Location="Africa and Asia"/>
                <models:Monkey Name="Capuchin Monkey" Location="Central and South America"/>
                <models:Monkey Name="Blue Monkey" Location="Central and East Africa"/>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="models:Monkey">
                <TextCell Text="{Binding Name}"
                          Detail="{Binding Location}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

這裡的好處是您可以系結至您打算使用的實際模型。

## <a name="alternative-hardcode-a-static-viewmodel"></a>替代方法：硬式編碼靜態 ViewModel

如果您不想要將設計階段資料加入至個別控制項，可以設定模擬資料存放區以系結至您的頁面。 請參閱 James Montemagno 的 [blog 文章](https://montemagno.com/xamarin-forms-design-time-data-tips-best-practices/) ，以瞭解如何在 XAML 中系結至靜態 ViewModel。

## <a name="troubleshooting"></a>疑難排解

### <a name="requirements"></a>規格需求

設計階段資料需要的最低版本為 Xamarin.Forms 3.6。

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense 會在 [我的設計階段資料] 下顯示波浪線

這是已知的問題，將在即將推出的 Visual Studio 版本中修正。 專案仍會建立，而不會發生錯誤。

### <a name="the-xaml-previewer-stopped-working"></a>XAML 預覽器已停止運作

請嘗試關閉並重新開啟 XAML 檔案，並清除並重建您的專案。
