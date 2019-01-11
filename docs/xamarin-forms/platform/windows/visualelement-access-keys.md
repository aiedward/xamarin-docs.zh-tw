---
title: 在 Windows 上的 VisualElement 存取金鑰
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Windows 平台特定，VisualElement 指定便捷鍵。
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f14014b257ee5061b6dd074719c3ca27577c6013
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209333"
---
# <a name="visualelement-access-keys-on-windows"></a>在 Windows 上的 VisualElement 存取金鑰

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

存取金鑰會藉由提供應用程式的顯示 UI，透過觸控式鍵盤而不是透過使用者快速瀏覽並與其互動以直覺的方式改善可用性和協助工具應用程式在通用 Windows 平台 (UWP) 中的鍵盤快速鍵或滑鼠。 也就是一或多個英數字元的索引鍵，通常依序按下 Alt 鍵的組合。 鍵盤快速鍵自動支援使用單一的英數字元的存取金鑰。

存取金鑰祕訣則浮動的控制項，包括存取金鑰旁邊顯示的徽章。 每個存取金鑰的提示包含英數字元的索引鍵的啟動相關聯的控制項。 當使用者按下 Alt 鍵時，則會顯示存取金鑰祕訣。

此 UWP 平台專屬用來指定存取金鑰[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 它由在 XAML 中設定[ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty)為英數字元值，並選擇性地設定附加屬性[ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty)附加屬性值為[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)列舉[ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) ; 附加屬性`double`，而[ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) ;附加屬性`double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

`VisualElement.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來設定存取金鑰值，如`VisualElement`。 [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement))方法，選擇性地指定要用於顯示存取金鑰祕訣，位置[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)列舉提供下列可能的值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – 表示 存取金鑰的小費放置將會由作業系統決定。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) -指出存取關鍵提示會出現上述的上邊緣`VisualElement`。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) -指出存取關鍵提示會出現如下的下邊緣`VisualElement`。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) -指出存取關鍵提示會出現右邊的右緣`VisualElement`。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – 表示存取關鍵提示會顯示左邊的左邊緣`VisualElement`。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) -指出的中心點會出現重疊的存取金鑰祕訣`VisualElement`。

> [!NOTE]
> 通常[ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto)關鍵提示放置已足夠，其中包括支援的自適性使用者介面。

[ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))並[ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))方法可用於更細微的控制的存取金鑰祕訣位置。 引數`SetAccessKeyHorizontalOffset`方法會指示如何向移動的存取金鑰提示左或向右和引數`SetAccessKeyVerticalOffset`方法指出目前存取關鍵提示上移或下移的方式。

>[!NOTE]
> 在設定的存取金鑰的位置時，就無法設定存取關鍵提示位移`Auto`。

颾魤 ㄛ [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))，以及[ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))可用方法若要擷取存取金鑰值和它的位置。

結果是存取重要的提示，可以顯示任何旁邊[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)定義的執行個體存取金鑰，藉由按下 Alt 鍵：

![VisualElement 存取金鑰的平台專屬](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement 存取金鑰的特定平台")

當使用者啟用存取金鑰，藉由按下 Alt 鍵後面跟著存取金鑰的預設動作`VisualElement`就會執行。 例如，當使用者啟動的存取金鑰上[ `Switch` ](xref:Xamarin.Forms.Switch)，則`Switch`切換。 當使用者啟動上的存取金鑰[ `Entry` ](xref:Xamarin.Forms.Entry)，則`Entry`取得焦點。 當使用者啟動上的存取金鑰[ `Button` ](xref:Xamarin.Forms.Button)，事件處理常式，如[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)執行事件。

如需存取金鑰的詳細資訊，請參閱[存取金鑰](/windows/uwp/design/input/access-keys#key-tip-positioning)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
