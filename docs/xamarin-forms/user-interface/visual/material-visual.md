---
title: Xamarin.Forms 材質視覺效果
description: Xamarin.Forms 材質視覺效果可用來建立在 iOS 和 Android 上看起來相同或絕大部分都相同的 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 46ffe29f898e2f7bd8fcbe759f5a2f54e3dd60e1
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557500"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.Forms 資料視覺效果

Xamarin.Forms 材質視覺效果可用來建立在 iOS 和 Android 上建立看起來相同或絕大部分都相同的 Xamarin.Forms 應用程式。這可以透過實作材質視覺效果的額外轉譯器來達成，惟應用程式必須透過 `Visual` 屬性來選擇套用外觀：

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>
```

對等的 C# 程式碼是：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

> [!IMPORTANT]
> `Visual` 屬性定義於 `VisualElement` 類別，其檢視從其父系繼承 `Visual` 屬性值。因此，在 `ContentPage` 上設定 `Visual` 屬性可確保頁面中任何支援的檢視都會使用該視覺效果外觀。此外，您也可以覆寫檢視上的 `Visual` 屬性。

針對 iOS 和 Android 上的下列檢視，目前包含材質轉譯器：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)

在功能上，材質轉譯器與預設轉譯器沒有不同。

在 iOS 和 Android 上，您的平台專案必須具有 [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 套件。安裝 NuGet 套件之後，每個平台專案的 `Xamarin.Forms.Forms.Init` 方法呼叫後都需要初始化程式碼。針對 iOS，請使用下列程式碼：

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

在 Android 上，您必須傳遞與傳遞到 `Forms.Init` 之參數相同的參數：
```csharp
global::Xamarin.Forms.Forms.Init(this, bundle);
FormsMaterial.Init(this, bundle);
```

> [!IMPORTANT]
> 在 Android 上，材質僅適用於 TargetFramework 9.0 (API 28)。此外，您的平台專案必須使用 v28 支援程式庫，而且其佈景主題必須繼承自「材質元件」佈景主題，或繼續繼承自 AppCompat 佈景主題。如需詳細資訊，請參閱<<c0>[開始使用適用於 Android 的材質元件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)><c0>。

下列螢幕擷取畫面會顯示包含四個檢視，如需哪些 Material 轉譯器(renderers)存在，但使用的預設轉譯器(renderers)轉譯使用者介面：

下列螢幕擷取畫面顯示包含四個檢視的使用者介面，這些檢視有材質轉譯器存在，但使用預設轉譯器轉譯：

下列螢幕擷取畫面顯示相同的使用者介面使用 Material 的轉譯器(renderers)轉譯後的介面外觀：

![iOS 和 Android 上材質轉譯器的螢幕擷取畫面](https://docs.microsoft.com/zh-tw/xamarin/xamarin-forms/user-interface/visual/material-visual-images/material-renderers-large.png#lightbox "使用材質轉譯器的檢視")

> [!NOTE]
> 下列螢幕擷取畫面顯示與使用材質轉譯器轉譯後之使用者介面相同的使用者介面：

## <a name="material-renderers"></a>材質轉譯器
使用材質轉譯器時，轉譯的控制項仍會維持為原生控制項，因此各平台之間的使用者介面仍會有差異 (例如字型、陰影、色彩與高度)。

- `MaterialButtonRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

下列程式碼顯示的自訂範例`MaterialProgressBarRenderer`類別：

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        ...
    }
}
```

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [在 Xamarin.Forms 使用 Material Design 讓雙平台 App 使用介面體驗一致化](https://dotblogs.com.tw/jamestsai/2019/03/09/xamarinforms-using-material-design)
