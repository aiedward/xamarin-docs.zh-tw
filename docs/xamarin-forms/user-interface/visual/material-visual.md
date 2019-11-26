---
title: Xamarin. 表單材質視覺效果
description: '[Xamarin]：表單材質視覺效果可用來建立在 iOS 和 Android 上看起來大致相同的 Xamarin. Forms 應用程式。'
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/25/2019
ms.openlocfilehash: 4a960075ebea0fae4cde8d79bdc38641a0b1dc8a
ms.sourcegitcommit: 1fdc6215f00863735bf0d2bf0afcaa45b714b453
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465525"
---
# <a name="xamarinforms-material-visual"></a>Xamarin. 表單材質視覺效果

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[材質設計](https://material.io)是 Google 所建立的固定設計系統，其中規定了視圖和版面配置外觀與行為的大小、色彩、間距和其他層面。

[Xamarin]：表單材質視覺效果可用來將材質設計規則套用至 Xamarin。表單應用程式，建立在 iOS 和 Android 上看起來大致相同的應用程式。 啟用材質視覺效果時，支援的視圖會採用相同的設計跨平臺，並建立一致的外觀與風格。

[![材質視覺螢幕擷取畫面](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

在您的應用程式中啟用 Xamarin. 表單材質視覺效果的流程為：

1. 將 [ [Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) ] NuGet 套件新增至您的 IOS 和 Android 平臺專案。 此 NuGet 套件會在 iOS 和 Android 上提供優化的材質設計轉譯器。 在 iOS 上，套件會提供[MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可轉移相依性，這是C# Google 的[ios 材質元件](https://material.io/develop/ios/)系結。 在 Android 上，套件會提供組建目標，以確保您的 TargetFramework 已正確設定。
1. 在每個平臺專案中初始化材質視覺效果。 如需詳細資訊，請參閱[初始化材質視覺效果](#initialize-material-visual)。
1. 在應該採用材質設計規則的任何頁面上，將 [ [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) ] 屬性設定為 [`Material`]，以建立材質視覺控制項。 如需詳細資訊，請參閱[使用材質](#apply-material-visual)轉譯器。
1. 選擇性自訂材質控制項。 如需詳細資訊，請參閱[自訂材質控制項](#customize-material-visual)。

> [!IMPORTANT]
> 在 Android 上，材質視覺效果所需的最低版本為5.0 （API 21）或更高版本，以及9.0 版（API 28）的 TargetFramework。 此外，您的平臺專案需要28.0.0 或更高版本的 Android 支援程式庫，而且其主題必須繼承自材質元件主題，或繼續繼承自 AppCompat 主題。 如需詳細資訊，請參閱[開始使用適用于 Android 的材質元件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

材質視覺效果目前支援下列控制項：

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

材質控制項是透過材質轉譯器來實現，其適用于材質設計規則。 在功能上，材質轉譯器與預設轉譯器並無不同。 如需詳細資訊，請參閱[自訂材質視覺效果](#customize-material-visual)。

## <a name="initialize-material-visual"></a>初始化材質視覺效果

安裝[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 套件之後，必須在每個平臺專案中初始化材質轉譯器。

在 iOS 上，這應該會在**AppDelegate.cs**中發生，方法是在 `Xamarin.Forms.Forms.Init` 方法*之後*叫用 `Xamarin.Forms.FormsMaterial.Init` 方法：

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

在 Android 上，這應該會在**MainActivity.cs**中發生，方法是在 `Xamarin.Forms.Forms.Init` 方法*之後*叫用 `Xamarin.Forms.FormsMaterial.Init` 方法：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>套用材質視覺效果

應用程式可以藉由將頁面、版面配置或視圖上的 [ [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) ] 屬性設定為 `Material`，來啟用材質視覺效果：

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

對等的 C# 程式碼是：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

將 `VisualElement.Visual` 屬性設定為 `Material` 會指示您的應用程式使用材質視覺化轉譯器，而不是預設轉譯器。 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性可以設定為任何可執行 `IVisual`的型別，且[`VisualMarker`](xref:Xamarin.Forms.VisualMarker)類別提供下列 `IVisual` 屬性：

- `Default` –表示此視圖應該使用預設轉譯器來呈現。
- `MatchParent` –表示此視圖應該使用與其直接父系相同的轉譯器。
- `Material` –表示此視圖應該使用材質轉譯器來呈現。

> [!IMPORTANT]
> [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性是在[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別中定義，而且 views 會從其父代繼承 `Visual` 屬性值。 因此，在[`ContentPage`](xref:Xamarin.Forms.ContentPage)上設定 `Visual` 屬性，可確保頁面中任何支援的視圖都會使用該視覺效果。 此外，您可以在視圖上覆寫 `Visual` 屬性。

下列螢幕擷取畫面顯示使用預設轉譯器呈現的使用者介面：

[![IOS 和 Android 上預設轉譯器的螢幕擷取畫面](material-visual-images/default-renderers.png "使用預設轉譯器的視圖")](material-visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示使用材質轉譯器呈現的相同使用者介面：

[![IOS 和 Android 上的材質轉譯器螢幕擷取畫面](material-visual-images/material-renderers.png "使用材質轉譯器的視圖")](material-visual-images/material-renderers-large.png#lightbox)

預設轉譯器和材質轉譯器之間的主要可見差異（如下所示），是材質轉譯器會[`Button`](xref:Xamarin.Forms.Button)文字，並將[`Frame`](xref:Xamarin.Forms.Frame)框線的角落四捨五入。 不過，材質轉譯器會使用原生控制項，因此，字型、陰影、色彩和提高許可權等區域的平臺可能還是會有使用者介面的差異。

> [!NOTE]
> 材質設計元件會嚴格遵守 Google 的指導方針。 因此，材質設計轉譯器會偏向大小和行為。 當您需要對樣式或行為有更大的控制權時，您仍然可以建立自己的[效果](~/xamarin-forms/app-fundamentals/effects/index.md)、[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)或[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器，以達到所需的詳細資料。

## <a name="customize-material-visual"></a>自訂材質視覺效果

材質視覺效果 NuGet 套件是可實現 Xamarin 控制項的轉譯器集合。 自訂材質視覺控制項與自訂預設控制項相同。

當目標是要自訂現有的控制項時，效果是建議的技巧。 如果有材質視覺效果轉譯器，則自訂控制項的效果比較少，而不是將轉譯器設為子類別。 如需效果的詳細資訊，請參閱[Xamarin 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

當材質轉譯器不存在時，自訂轉譯器是建議的技術。 下列轉譯程式類別隨附于材質視覺效果中：

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
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

將材質轉譯器子類別化與非材質轉譯器幾乎相同。 不過，匯出子類別化材質轉譯器的轉譯器時，您必須將第三個引數提供給指定 `VisualMarker.MaterialVisual` 類型的 `ExportRenderer` 屬性：

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
    }
}
```

在此範例中，`ExportRendererAttribute` 指定將使用 `CustomMaterialProgressBarRenderer` 類別來呈現[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)視圖，並將 `IVisual` 類型註冊為第三個引數。

> [!NOTE]
> 指定 `IVisual` 類型做為其 `ExportRendererAttribute`之一部分的轉譯器，將用來轉譯選擇的視圖，而不是預設轉譯器。 在轉譯器選取時間，會檢查視圖的 `Visual` 屬性，並將其包含在轉譯器選取進程中。

如需自訂轉譯器的詳細資訊，請參閱[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="related-links"></a>相關連結

- [材質視覺效果（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [建立 Xamarin. Forms 視覺效果轉譯器](create.md)
- [Xamarin. 表單效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
