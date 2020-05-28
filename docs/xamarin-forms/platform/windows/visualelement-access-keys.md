---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1bfd61e79a2b4697e884afb45e4b9080ee939b87
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136522"
---
# <a name="visualelement-access-keys-on-windows"></a>Windows 上的 VisualElement 存取金鑰

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

存取金鑰是鍵盤快速鍵，可提供直覺的方式，讓使用者透過鍵盤（而非觸控或滑鼠）快速流覽和互動應用程式的可見 UI，藉此改善通用 Windows 平臺（UWP）上應用程式的可用性和協助工具。 它們是 ALT 鍵和一或多個英數位元的組合，通常會依序按順序。 使用單一英數位元的存取金鑰會自動支援鍵盤快速鍵。

存取金鑰提示是顯示在包含存取金鑰的控制項旁邊的浮動徽章。 每個存取金鑰提示都包含可啟動相關聯控制項的英數位元鍵。 當使用者按下 ALT 鍵時，會顯示存取金鑰提示。

此 UWP 平臺特定是用來指定的存取金鑰 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它會在 XAML 中使用，方法是將 [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) 附加屬性設定為英數位元，並選擇性地將 [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) 附加屬性設定為列舉的值 [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) 、 [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) 附加屬性設為 `double` ，以及將 [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) 附加屬性 `double` 設為：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`VisualElement.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 [ `VisualElement.SetAccessKey` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKey （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}，System.string）方法，在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中，是用來設定的存取金鑰值 `VisualElement` 。 [ `VisualElement.SetAccessKeyPlacement` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyPlacement （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}、 Xamarin.Forms 。AccessKeyPlacement））方法，可選擇性地指定要用於顯示存取金鑰提示的位置，並以 [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) 列舉提供下列可能的值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto)–表示存取金鑰提示位置將由作業系統決定。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top)-表示存取金鑰提示會出現在的上邊緣上方 `VisualElement` 。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom)-表示存取金鑰提示會出現在的下邊緣之下 `VisualElement` 。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right)-表示存取金鑰提示會出現在右邊緣的右邊 `VisualElement` 。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left)-表示存取金鑰提示會出現在左邊緣的左邊 `VisualElement` 。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center)-表示存取金鑰提示會在的中央出現重迭 `VisualElement` 。

> [!NOTE]
> 通常， [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) 主要秘訣放置就已足夠，這包括支援自我調整使用者介面。

[ `VisualElement.SetAccessKeyHorizontalOffset` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyHorizontalOffset （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}、System. Double）和 [ `VisualElement.SetAccessKeyVerticalOffset` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyVerticalOffset （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}、system.string）方法可以用來對存取金鑰提示位置進行更細微的控制。 方法的引數會 `SetAccessKeyHorizontalOffset` 指出要將存取金鑰提示向左或向右移動多久，而方法的引數會 `SetAccessKeyVerticalOffset` 指出向上或向下移動存取金鑰提示的距離。

>[!NOTE]
> 設定存取金鑰的位置時，無法設定存取金鑰提示位移 `Auto` 。

此外，[ `GetAccessKey` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKey （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}）），[ `GetAccessKeyPlacement` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyPlacement （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}）），[ `GetAccessKeyHorizontalOffset` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyHorizontalOffset （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}））和 [ `GetAccessKeyVerticalOffset` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyVerticalOffset （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}））方法可以用來抓取存取金鑰值和其位置。

結果是，只要 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 按下 ALT 鍵，就可以在定義存取金鑰的任何實例旁顯示存取金鑰提示：

![VisualElement 存取金鑰平臺特定](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement 存取金鑰平臺特定")

當使用者啟用存取金鑰時，只要按下 ALT 鍵並接著存取金鑰， `VisualElement` 就會執行的預設動作。 例如，當使用者在上啟用存取金鑰時 [`Switch`](xref:Xamarin.Forms.Switch) ， `Switch` 會切換。 當使用者在上啟用存取金鑰時 [`Entry`](xref:Xamarin.Forms.Entry) ， `Entry` 取得焦點。 當使用者在上啟用存取金鑰時 [`Button`](xref:Xamarin.Forms.Button) ， [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 就會執行事件的事件處理常式。

如需存取金鑰的詳細資訊，請參閱[存取金鑰](/windows/uwp/design/input/access-keys#key-tip-positioning)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
