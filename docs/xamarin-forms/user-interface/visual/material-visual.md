---
title: Xamarin.Forms 資料視覺效果
description: Xamarin.Forms 資料視覺效果可用來建立 Xamarin.Forms 應用程式看起來完全相同，或基本相同，iOS 和 Android 上使用。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: cf6ab8266b0798ccbf29078313bbc7454125a1af
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972620"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.Forms 資料視覺效果

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

[材料設計](https://material.io)是由 Google、 大小、 色彩、 間距及如何檢視和配置的外觀及運作的其他層面都自有一套已有定見的設計系統。

Xamarin.Forms 資料視覺效果可用來將 Material Design 規則套用至 Xamarin.Forms 應用程式，iOS 和 Android 上完全相同，或基本相同，建立查詢的應用程式。 啟用資料視覺化，支援的檢視會採用相同設計跨平台，建立統一的外觀與風格。 這是與 material 轉譯器，套用 Material Design 規則來達成。

在您的應用程式中啟用 Xamarin.Forms 資料視覺化的程序是：

1. 新增[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 封裝加入您的 iOS 和 Android 平台專案。 這個 NuGet 封裝提供 iOS 和 Android 上的最佳化的 Material Design 轉譯器。 在 iOS 上，封裝會提供可轉移的相依性[Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)，也就是C#繫結至 Google[適用於 iOS 的材料元件](https://material.io/develop/ios/)。 在 Android 上，封裝會提供建置目標，以確保，您的 TargetFramework 已正確設定。
1. 初始化每個平台專案中的 material 轉譯器。 如需詳細資訊，請參閱 <<c0> [ 初始化 material 轉譯器](#initialize-material-renderers)。
1. 藉由設定使用的材料的轉譯器[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性設`Material`應該採用的 Material Design 規則的任何頁面上。 如需詳細資訊，請參閱 <<c0> [ 取用 material 轉譯器](#consume-material-renderers)。
1. [選用]自訂的 material 轉譯器。 如需詳細資訊，請參閱 <<c0> [ 來自訂 material 轉譯器](#customize-material-renderers)。

> [!IMPORTANT]
> 在 Android 上，材質的轉譯器需要的最低版本為 5.0 (API 21) 或更新版本，以及 (API 28) 的 TargetFramework 的 9.0 版。 此外，您的平台專案需要 Android 支援程式庫 28.0.0 或更新版本，和它的佈景主題，就必須繼承自資料元件佈景主題，或繼續繼承自 AppCompat 佈景主題。 如需詳細資訊，請參閱 <<c0> [ 開始使用資料元件適用於 Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

Material 轉譯器目前包含在[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 套件的下列檢視：

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

在功能上，材質的轉譯器為沒有不同的預設轉譯器。

## <a name="initialize-material-renderers"></a>初始化 material 轉譯器

在安裝後[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 套件，必須初始化轉譯器，每個平台專案中的資料。

在 iOS 上，應該會發生這個問題**AppDelegate.cs**藉由叫用`FormsMaterial.Init`方法*之後*`Xamarin.Forms.Forms.Init`方法：

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

在 Android 上，應該會發生這個問題**MainActivity.cs**藉由叫用`FormsMaterial.Init`方法*之後*`Xamarin.Forms.Forms.Init`方法：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>使用材質的轉譯器

應用程式可以選擇藉由設定使用的材料的轉譯器[ `VisualElement.Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性頁面、 版面配置或檢視，以`Material`:

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

[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性可以設定為可實作任何型別`IVisual`，使用[ `VisualMarker` ](xref:Xamarin.Forms.VisualMarker)類別提供下列`IVisual`屬性：

- `Default` – 表示檢視應該使用預設的轉譯器轉譯。
- `MatchParent` – 表示檢視應該使用相同的轉譯器為其直接父代。
- `Material` – 表示檢視應該使用材質的轉譯器轉譯。

> [!IMPORTANT]
> [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性已定義於[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)類別繼承的檢視`Visual`從其父項的屬性值。 因此，設定`Visual`上的屬性[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)可確保任何支援的檢視頁面中，將會使用該視覺效果。 颾魤 ㄛ`Visual`可以覆寫檢視上的屬性。

下列螢幕擷取畫面顯示使用的預設轉譯器轉譯使用者介面：

[![預設的轉譯器，在 iOS 和 Android 上的螢幕擷取畫面](material-visual-images/default-renderers.png "檢視 使用預設的轉譯器")](material-visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示相同的使用者介面使用的材料的轉譯器轉譯：

[![材質的轉譯器，在 iOS 和 Android 上的螢幕擷取畫面](material-visual-images/material-renderers.png "檢視使用資料轉譯器")](material-visual-images/material-renderers-large.png#lightbox)

Material 轉譯器，在這裡顯示，與預設轉譯器之間的主要可見的差異是 material 轉譯器改為大寫[ `Button` ](xref:Xamarin.Forms.Button)文字和圓角[ `Frame` ](xref:Xamarin.Forms.Frame)框線。 不過，material 轉譯器會使用原生控制項，並因此可能仍然會有平台，例如字型、 shadows、 色彩和提高權限的區域之間的使用者介面不同。

> [!NOTE]
> 材料設計元件密切遵守 Google 的指導方針。 如此一來，Material Design 轉譯器偏向該大小和行為。 當您需要更大的控制權樣式或行為時，您仍然可以建立您自己[生效](~/xamarin-forms/app-fundamentals/effects/index.md)，[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)，或[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)以達到您所需要的詳細資料。

## <a name="customize-material-renderers"></a>自訂 material 轉譯器

Material 轉譯器可以選擇性地自訂，就像的預設轉譯器，透過下列基底類別：

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

在此範例中，`ExportRendererAttribute`指定`CustomMaterialProgressBarRenderer`類別將用來呈現[ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)檢視中，使用`IVisual`類型註冊為第三個引數。

> [!NOTE]
> 指定的轉譯器`IVisual`一部分的型別，其`ExportRendererAttribute`，將會用來呈現檢視，而不是預設的轉譯器中選擇。 在轉譯器選取項目時，`Visual`屬性檢視的檢查，且包含在轉譯器選取項目程序。

如需有關自訂轉譯器的詳細資訊，請參閱 <<c0> [ 自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="related-links"></a>相關連結

- [材質的視覺效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [建立 Xamarin.Forms 視覺效果轉譯器](create.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
