---
title: 在 Windows 上 VisualElement 存取金鑰
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用指定 VisualElement 存取金鑰的 Windows 平臺特定。
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7df914ae5b6de38b5179f5b1139b9c41e9559198
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367448"
---
# <a name="visualelement-access-keys-on-windows"></a>在 Windows 上 VisualElement 存取金鑰

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

存取金鑰是鍵盤快速鍵，可透過鍵盤（而不是透過觸控或滑鼠），提供直覺的方式，讓使用者快速流覽應用程式的可見 UI 並與其互動，藉此改善應用程式在通用 Windows 平臺 (UWP) 的可用性和可存取性。 它們是 ALT 鍵和一或多個英數位元的組合，通常會按順序排列。 使用單一英數位元的存取金鑰會自動支援鍵盤快速鍵。

存取金鑰提示是在包含存取金鑰的控制項旁邊顯示的浮動徽章。 每個存取金鑰提示都包含可啟動相關聯控制項的英數位鍵。 當使用者按下 ALT 鍵時，會顯示存取金鑰提示。

此 UWP 平臺特定用來指定的存取金鑰 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它是在 XAML 中使用，方法是將 [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) 附加屬性設定為英數位元值，並選擇性地將 [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) 附加屬性設定為 [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) 列舉值、 [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) 附加屬性至 `double` ，以及將 [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) 附加屬性 `double` 設定為：

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

或者，您也可以使用流暢的 API，從 c # 中使用它：

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

`VisualElement.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 [ `VisualElement.SetAccessKey` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKey (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} 是命名空間中的 System.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 用來設定的存取金鑰值 `VisualElement` 。 [ `VisualElement.SetAccessKeyPlacement` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyPlacement (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}， Xamarin.Forms 。AccessKeyPlacement) # A3 方法，選擇性地指定要用於顯示存取金鑰提示的位置，並以 [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) 列舉提供下列可能的值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) –表示存取金鑰提示放置將由作業系統決定。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) –表示存取金鑰提示會出現在的上邊緣上方 `VisualElement` 。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) –表示存取金鑰提示會出現在的下邊緣下方 `VisualElement` 。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) –表示存取金鑰提示會出現在右側邊緣的右邊 `VisualElement` 。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) –表示存取金鑰提示會出現在左邊緣的左邊 `VisualElement` 。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) –表示存取金鑰提示會在的中央顯示為已重迭 `VisualElement` 。

> [!NOTE]
> 通常， [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) 關鍵秘訣的放置就已足夠，包括對自我調整使用者介面的支援。

[ `VisualElement.SetAccessKeyHorizontalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyHorizontalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}、System.object) # A3 和 [ `VisualElement.SetAccessKeyVerticalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyVerticalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}，system.string) # A7 方法可以用來更細微地控制存取金鑰提示的位置。 方法的引數會 `SetAccessKeyHorizontalOffset` 指出要將存取金鑰提示往左或向右移動的距離，而方法的引數會 `SetAccessKeyVerticalOffset` 指出將存取金鑰提示向上或向下移動的距離。

>[!NOTE]
> 設定存取金鑰位置時，無法設定存取金鑰提示位移 `Auto` 。

此外，[ `GetAccessKey` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKey (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A3，[ `GetAccessKeyPlacement` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyPlacement (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A7，[ `GetAccessKeyHorizontalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyHorizontalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A11 和 [ `GetAccessKeyVerticalOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyVerticalOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A15 方法可以用來取得存取金鑰值和其位置。

結果是，只要 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 按下 ALT 鍵，即可在定義存取金鑰的任何實例旁顯示存取金鑰提示：

![VisualElement 存取金鑰平臺特定](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement 存取金鑰平臺特定")

當使用者啟動存取金鑰時，按下 ALT 鍵後面接著存取金鑰， `VisualElement` 將會執行的預設動作。 例如，當使用者啟動上的存取金鑰時 [`Switch`](xref:Xamarin.Forms.Switch) ， `Switch` 就會切換。 當使用者啟動上的存取金鑰時 [`Entry`](xref:Xamarin.Forms.Entry) ，會 `Entry` 取得焦點。 當使用者啟動上的存取金鑰時 [`Button`](xref:Xamarin.Forms.Button) ， [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 就會執行事件的事件處理常式。

如需存取金鑰的詳細資訊，請參閱 [存取金鑰](/windows/uwp/design/input/access-keys#key-tip-positioning)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)