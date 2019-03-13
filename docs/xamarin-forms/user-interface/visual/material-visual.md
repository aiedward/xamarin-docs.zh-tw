---
title: Xamarin.Forms 使用 Material 視覺設計
description: Xamarin.Forms Material 視覺設計可用來建立 Xamarin.Forms 應用程式看起來完全相同，或是讓 iOS 和 Android 的 App 操作時有近似的體驗感。
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

Xamarin.Forms 套用 Material 視覺設計可用來建立 Xamarin.Forms 應用程式看起來完全相同，或是讓 iOS 和 Android 的 App 操作時有近似的體驗感。這可透過有實作 Material 視覺設計的外觀轉譯器 (renderers) 設計，並透過設定`Visual`屬性來達成 Material 視覺設計的外觀套用：

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
> `Visual`屬性定義於`VisualElement`類別，以檢視繼承`Visual`從其父項的屬性值。 因此，設定`Visual`屬性上的`ContentPage`可確保任何支援的檢視頁面中，將會使用該視覺外觀。當然也可以直接透由該 view 元件的`Visual`屬性設定來複寫。

Material 的轉譯器(renderers) 目前包含在 iOS 和 Android 上的下列元件：

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

基本上 Material 的轉譯器(renderers)動作對於預設的轉譯器(renderers)沒有影響。

在 iOS 和 Android 上，您的平台專案必須具有 [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/ https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) 安裝的 NuGet 套件。 並且需要在安裝 NuGet 套件之後再到每個平台專案中，於`Xamarin.Forms.Forms.Init`方法呼叫*之後*使用 Material Nuget 套件的初始化方法呼叫。 專案平台是 iOS 請使用下列程式碼：

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

專案平台是 Android 時請使用下列程式碼，且傳遞跟`Forms.Init`相同的參數傳遞至 Material Nuget 套件的初始化程式碼呼叫方法:

```csharp
global::Xamarin.Forms.Forms.Init(this, bundle);
FormsMaterial.Init(this, bundle);
```

> [!IMPORTANT]
> 在 Android 內容僅適用於 TargetFramework 9.0 (API 28)。 此外，平台專案必須使用 v28 支援程式庫，而且它的佈景主題，就必須繼承自資料元件佈景主題，或繼續繼承自 AppCompat 佈景主題。 如需詳細資訊，請參閱 <<c0> [ 開始使用資料元件適用於 Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

下列螢幕擷取畫面會顯示包含四個檢視，如需哪些 Material 轉譯器(renderers)存在，但使用的預設轉譯器(renderers)轉譯使用者介面：

[![預設的轉譯器，在 iOS 和 Android 上的螢幕擷取畫面](material-visual-images/default-renderers.png "檢視使用預設的轉譯器")](https://docs.microsoft.com/zh-tw/xamarin/xamarin-forms/user-interface/visual/material-visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示相同的使用者介面使用 Material 的轉譯器(renderers)轉譯後的介面外觀：

[![Material 的轉譯器，在 iOS 和 Android 上的螢幕擷取畫面](material-visual-images/material-renderers.png "檢視使用 Material 轉譯器")](https://docs.microsoft.com/zh-tw/xamarin/xamarin-forms/user-interface/visual/material-visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Material 轉譯器(renderers)轉譯的控制項保持原生控制項，與因此還是會有平台，例如字型、 shadows、 色彩和提高權限的區域之間的使用者介面不同。

## <a name="material-renderers"></a>Material 轉譯器(Renderers)
由於 Material 轉譯器(Renderers)是根據下列的基底類別設計的，跟預設轉譯器(Renderers)一樣，在 Material 轉譯器(Renderers)也可以再客製化自行定義：

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
