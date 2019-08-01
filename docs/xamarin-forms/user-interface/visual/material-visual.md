---
title: Xamarin.Forms 材質視覺效果
description: Xamarin.Forms 材質視覺效果可用來建立在 iOS 和 Android 上看起來相同或絕大部分都相同的 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 6c529c8df7ef1e4372285a157f489941d795d7f6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655226"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.Forms 材質視覺效果

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[材質設計](https://material.io)是 Google 所建立的固定設計系統, 其中規定了視圖和版面配置外觀與行為的大小、色彩、間距和其他層面。

[Xamarin]: 您可以使用 [表單材質] 視覺效果, 將材質設計規則套用至 Xamarin。表單應用程式, 建立在 iOS 和 Android 上看起來完全相同或大致相同的應用程式。 啟用材質視覺效果時, 支援的視圖會採用相同的設計跨平臺, 並建立一致的外觀與風格。 這是透過材質轉譯器來達成, 其適用于材質設計規則。

在您的應用程式中啟用 Xamarin. 表單材質視覺效果的流程為:

1. 將 [ [Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) ] NuGet 套件新增至您的 IOS 和 Android 平臺專案。 此 NuGet 套件會在 iOS 和 Android 上提供優化的材質設計轉譯器。 在 iOS 上, 套件會提供[MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可轉移相依性, 這是C# Google 的[ios 材質元件](https://material.io/develop/ios/)系結。 在 Android 上, 套件會提供組建目標, 以確保您的 TargetFramework 已正確設定。
1. 初始化每個平臺專案中的材質轉譯器。 如需詳細資訊, 請參閱[初始化材質](#initialize-material-renderers)轉譯器。
1. 在應該採用材質設計規則的[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)任何頁面`Material`上, 將屬性設定為, 以取用材質轉譯器。 如需詳細資訊, 請參閱[使用材質](#consume-material-renderers)轉譯器。
1. 選擇性自訂材質轉譯器。 如需詳細資訊, 請參閱[自訂材質](#customize-material-renderers)轉譯器。

> [!IMPORTANT]
> 在 Android 上, 材質轉譯器需要的最低版本為 5.0 (API 21) 或更高版本, 以及9.0 版 (API 28) 的 TargetFramework。 此外, 您的平臺專案需要28.0.0 或更高版本的 Android 支援程式庫, 而且其主題必須繼承自材質元件主題, 或繼續繼承自 AppCompat 主題。 如需詳細資訊，請參閱 <<c0> [ 開始使用資料元件適用於 Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

材質轉譯器目前包含在下列視圖的[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 套件中:

- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
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

## <a name="initialize-material-renderers"></a>初始化材質轉譯器

安裝[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 套件之後, 必須在每個平臺專案中初始化材質轉譯器。

在 iOS 上, 這應該會**在 AppDelegate.cs**中, `FormsMaterial.Init`藉由在`Xamarin.Forms.Forms.Init`方法*之後*叫用方法來進行:

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

在 Android 上, 這應該會**在 MainActivity.cs**中, `FormsMaterial.Init`藉由在`Xamarin.Forms.Forms.Init`方法*之後*叫用方法來進行:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>使用材質轉譯器

應用程式可以藉由將頁面、版面配置或[`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual)視圖上的屬性設定為, 選擇使用材質轉譯`Material`器來執行下列動作:

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

屬性可以設定為任何執行的類型`IVisual`, 並[`VisualMarker`](xref:Xamarin.Forms.VisualMarker)提供下列`IVisual`屬性的類別: [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

- `Default`–表示此視圖應該使用預設轉譯器來呈現。
- `MatchParent`–表示此視圖應該使用與其直接父系相同的轉譯器。
- `Material`–表示此視圖應該使用材質轉譯器來呈現。

> [!IMPORTANT]
> 屬性會定義[`VisualElement`](xref:Xamarin.Forms.VisualElement)在類別中`Visual` , 而 views 會從其父代繼承屬性值。 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 因此, `Visual` [`ContentPage`](xref:Xamarin.Forms.ContentPage)在上設定屬性可確保頁面中任何支援的視圖都會使用該視覺效果。 颾魤 ㄛ`Visual`可以覆寫檢視上的屬性。

下列螢幕擷取畫面顯示使用預設轉譯器呈現的使用者介面:

[![IOS 和 Android 上預設轉譯器的螢幕擷取畫面](material-visual-images/default-renderers.png "使用預設")轉譯器的視圖](material-visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示與使用材質轉譯器轉譯後之使用者介面相同的使用者介面：

[![IOS 和 Android 上的材質轉譯器螢幕擷取畫面](material-visual-images/material-renderers.png "使用材質")轉譯器的視圖](material-visual-images/material-renderers-large.png#lightbox)

預設轉譯器和材質轉譯器之間的主要可見差異 (如下所示), 是材質轉譯[`Button`](xref:Xamarin.Forms.Button)器會以大寫顯示文字, [`Frame`](xref:Xamarin.Forms.Frame)並將框線的角落四捨五入。 不過, 材質轉譯器會使用原生控制項, 因此, 字型、陰影、色彩和提高許可權等區域的平臺可能還是會有使用者介面的差異。

> [!NOTE]
> 材質設計元件會嚴格遵守 Google 的指導方針。 因此, 材質設計轉譯器會偏向大小和行為。 當您需要對樣式或行為有更大的控制權時, 您仍然可以建立自己的[效果](~/xamarin-forms/app-fundamentals/effects/index.md)、[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)或[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器, 以達到所需的詳細資料。

## <a name="customize-material-renderers"></a>自訂材質轉譯器

您可以選擇性地自訂材質轉譯器, 就像預設轉譯器一樣, 透過下列基類:

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

下列程式碼顯示自訂`MaterialProgressBarRenderer`類別的範例:

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

在此範例中, `ExportRendererAttribute`會指定`CustomMaterialProgressBarRenderer`將用來呈現[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)視圖的類別, 並`IVisual`將類型註冊為第三個引數。

> [!NOTE]
> 指定`IVisual`類型做為其`ExportRendererAttribute`一部分的轉譯器將用來轉譯加入宣告的視圖, 而不是預設轉譯器。 在轉譯器選取項目時，`Visual`屬性檢視的檢查，且包含在轉譯器選取項目程序。

如需自訂轉譯器的詳細資訊, 請參閱[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="related-links"></a>相關連結

- [材質視覺效果 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [建立 Xamarin. Forms 視覺效果轉譯器](create.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
