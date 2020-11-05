---
title: Xamarin.Forms 材質視覺效果
description: Xamarin.Forms 材質視覺效果可以用來建立 Xamarin.Forms 在 iOS 和 Android 上大致上相同的應用程式。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ecda3c6d0806894ce1ce657bc9e1c31cdfca7bf2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374403"
---
# <a name="no-locxamarinforms-material-visual"></a>Xamarin.Forms 材質視覺效果

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

「[材質設計](https://material.io)」是 Google 所建立的固定設計系統，可針對視圖和版面配置的外觀和行為，規定大小、色彩、間距及其他方面。

Xamarin.Forms 材質視覺效果可以用來將材質設計規則套用至 Xamarin.Forms 應用程式，以建立在 iOS 和 Android 上大致上相同的應用程式。 啟用材質視覺效果時，支援的視圖會採用相同的設計跨平臺，以建立一致的外觀與風格。

[![材質視覺螢幕擷取畫面](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

Xamarin.Forms在您的應用程式中啟用材質視覺效果的流程如下：

1. 加入[ Xamarin.Forms 。](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)適用于 iOS 和 Android 平臺專案的視覺材質 NuGet 套件。 此 NuGet 套件可在 iOS 和 Android 上提供優化的材質設計轉譯器。 在 iOS 上，套件會提供 [MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可轉移相依性，這是適用于 IOS 的 Google [材質元件](https://material.io/develop/ios/)的 c # 系結。 在 Android 上，套件會提供組建目標，以確保您的 TargetFramework 已正確設定。
1. 在每個平臺專案中初始化材質視覺效果。 如需詳細資訊，請參閱 [初始化材質視覺效果](#initialize-material-visual)。
1. [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) `Material` 在任何應採用材質設計規則的頁面上，將屬性設定為，以建立材質視覺效果控制項。 如需詳細資訊，請參閱取用 [材質](#apply-material-visual)轉譯器。
1. 參數自訂材質控制項。 如需詳細資訊，請參閱 [自訂材質控制項](#customize-material-visual)。

> [!IMPORTANT]
> 在 Android 上，材質視覺效果需要最低版本 5.0 (API 21) 或更高版本，以及9.0 版 (API 28) 的 TargetFramework。 此外，您的平臺專案需要 Android 支援程式庫28.0.0 或更高版本，而且其主題必須繼承自材質元件主題，或繼續繼承自 AppCompat 主題。 如需詳細資訊，請參閱 [適用于 Android 的材質元件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)使用者入門。

材質視覺效果目前支援下列控制項：

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- [`CheckBox`](xref:Xamarin.Forms.CheckBox)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

材質轉譯器可利用材質轉譯器來實現材質，以套用材質設計規則。 功能上，材質轉譯器與預設轉譯器並無不同。 如需詳細資訊，請參閱 [自訂材質視覺效果](#customize-material-visual)。

## <a name="initialize-material-visual"></a>初始化材質視覺效果

安裝之後[ Xamarin.Forms 。](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)您必須在每個平臺專案中初始化材質 NuGet 套件、材質轉譯器。

在 iOS 上，這應該會在 **AppDelegate.cs** 中執行，方法是在 `Xamarin.Forms.FormsMaterial.Init` 方法 *之後* 叫用方法 `Xamarin.Forms.Forms.Init` ：

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

在 Android 上，在方法之後叫用方法，就會在 **MainActivity.cs** 中進行這項 `Xamarin.Forms.FormsMaterial.Init` *after* `Xamarin.Forms.Forms.Init` 操作：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>套用材質視覺效果

應用程式可以將 [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) 頁面、版面配置或視圖上的屬性設定為，以啟用材質視覺效果 `Material` ：

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

設定 `VisualElement.Visual` 屬性以 `Material` 指示您的應用程式使用材質視覺轉譯器，而不是預設轉譯器。 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性可以設定為任何實作為的型別 `IVisual` ，而 [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) 類別提供下列 `IVisual` 屬性：

- `Default` –表示該視圖應使用預設轉譯器來呈現。
- `MatchParent` –表示視圖應使用與它的直接父系相同的轉譯器。
- `Material` –表示視圖應使用材質轉譯器轉譯。

> [!IMPORTANT]
> [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性是在類別中定義 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，而且會有 `Visual` 從其父代繼承屬性值的視圖。 因此， `Visual` 在上設定屬性， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可確保頁面中任何支援的視圖都會使用該視覺效果。 此外， `Visual` 也可以在視圖上覆寫屬性。

下列螢幕擷取畫面顯示使用預設轉譯器呈現的使用者介面：

[![IOS 和 Android 上預設轉譯器的螢幕擷取畫面](material-visual-images/default-renderers.png "使用預設轉譯器的視圖")](material-visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示使用材質轉譯器呈現的相同使用者介面：

[![IOS 和 Android 上的材質轉譯器螢幕擷取畫面](material-visual-images/material-renderers.png "使用材質轉譯器的視圖")](material-visual-images/material-renderers-large.png#lightbox)

預設轉譯器和材質轉譯器之間的主要可見差異（如下所示）是材質轉譯器會將 [`Button`](xref:Xamarin.Forms.Button) 文字大寫，並將框線的邊角四捨五入 [`Frame`](xref:Xamarin.Forms.Frame) 。 不過，材質轉譯器會使用原生控制項，因此平臺之間可能仍會有使用者介面差異，例如字型、陰影、色彩和提高許可權。

> [!NOTE]
> 材質設計元件會嚴格遵守 Google 的指導方針。 如此一來，材質設計轉譯器就會偏向該大小和行為。 當您需要更充分掌控樣式或行為時，您仍然可以建立自己的 [效果](~/xamarin-forms/app-fundamentals/effects/index.md)、 [行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)或 [自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) 轉譯器，以達成所需的詳細資料。

## <a name="customize-material-visual"></a>自訂材質視覺效果

材質視覺效果 NuGet 套件是可實現控制項的轉譯器集合 Xamarin.Forms 。 自訂材質視覺效果控制項與自訂預設控制項相同。

當目標是要自訂現有的控制項時，會產生效果建議的技巧。 如果有材質視覺效果轉譯器，則自訂控制項的效果會比將轉譯器子類別的工作更少。 如需效果的詳細資訊，請參閱[ Xamarin.Forms 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

當材質轉譯器不存在時，建議使用自訂轉譯器的技巧。 下列轉譯器類別包含在材質視覺效果中：

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

子類別化的材質轉譯器與非材質轉譯器幾乎完全相同。 不過，在匯出子類別化材質轉譯器的轉譯器時，您必須提供第三個引數給 `ExportRenderer` 指定類型的屬性 `VisualMarker.MaterialVisual` ：

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

在此範例中， `ExportRendererAttribute` `CustomMaterialProgressBarRenderer` 會指定將用來轉譯視圖的類別 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) ，並將型別 `IVisual` 註冊為第三個引數。

> [!NOTE]
> `IVisual`將型別指定為其一部分的轉譯器， `ExportRendererAttribute` 將會用來呈現選擇的視圖，而不是預設轉譯器。 轉譯器選取時， `Visual` 會檢查視圖的屬性，並將其包含在轉譯器選取進程中。

如需自訂轉譯器的詳細資訊，請參閱 [自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="related-links"></a>相關連結

- [材質 Visual (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [建立 Xamarin.Forms 視覺效果轉譯器](create.md)
- [Xamarin.Forms 影響](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)