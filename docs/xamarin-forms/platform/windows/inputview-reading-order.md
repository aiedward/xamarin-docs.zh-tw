---
title: 在 Windows 上的 InputView 讀取順序
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Windows 平台特定，可讓已偵測到動態雙向文字的讀取順序。
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f926425a3d2e2fb6494f42e962bc5f2fa1ba3b1c
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208843"
---
# <a name="inputview-reading-order-on-windows"></a>在 Windows 上的 InputView 讀取順序

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此通用 Windows 平台特定平台-可讓雙向文字中的讀取順序 （由左到右或從右至左） [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)動態偵測到的執行個體。 它由在 XAML 中設定[ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (如`Entry`並`Editor`執行個體) 或[ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) ; 附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否在內容中偵測到的讀取順序[ `InputView` ](xref:Xamarin.Forms.InputView). 颾魤 ㄛ`InputView.SetDetectReadingOrderFromContent`方法可用來切換讀取順序從內容呼叫來偵測是否[ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView}))方法來傳回目前的值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

結果是[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)執行個體可以有動態偵測到其內容的讀取順序：

[![偵測從平台專屬內容的讀取順序的 InputView](inputview-reading-order-images/editor-readingorder.png "InputView 偵測從平台專屬內容的讀取順序")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView 偵測從的讀取順序平台專屬的內容")

> [!NOTE]
> 不同於設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性中，偵測從其文字內容的讀取順序不會影響檢視內的文字的對齊方式的檢視表的邏輯。 相反地，它就會調整配置版面的雙向文字區塊的順序。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
