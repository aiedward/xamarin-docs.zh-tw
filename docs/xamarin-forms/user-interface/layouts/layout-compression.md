---
title: 版面配置壓縮
description: 版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以嘗試改善頁面轉譯效能。 本文說明如何啟用版面配置壓縮，以及它所能帶來的好處。
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9b22297fe06211b550ac2fdd62ee934b4ba849ee
ms.sourcegitcommit: 0a6b19004932c1ac82e16c95d5d3d5eb35a5b17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100255332"
---
# <a name="layout-compression"></a>版面配置壓縮

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以嘗試改善頁面轉譯效能。本文說明如何啟用版面配置壓縮，以及它所能帶來的好處。_

## <a name="overview"></a>概觀

Xamarin.Forms 使用兩系列的遞迴方法呼叫來執行配置：

- 版面配置是從具有頁面的視覺化樹狀結構頂端開始，並且會繼續執行視覺化樹狀結構的所有分支，以包含頁面上的每個視覺元素。 其他元素的父系元素會負責調整其子系的大小和位置（相對於本身的位置）。
- [失效] 是頁面上專案的變更觸發新配置週期的進程。 當元素不再具有正確的大小或位置時，會被視為無效。 只要其中一個子系變更大小，視覺樹狀結構中的每個專案都會收到子系的警示。 因此，視覺樹狀結構中元素大小的變更可能會導致在樹狀結構中出現變更。

如需有關如何執行版面配置的詳細資訊 Xamarin.Forms ，請參閱 [建立自訂版面](~/xamarin-forms/user-interface/layouts/custom.md)配置。

版面配置進程的結果是原生控制項的階層。 不過，此階層包含其他容器轉譯器和平臺轉譯器的包裝函式，進一步因而誇大視圖階層的嵌套。 最深的嵌套層級，需要 Xamarin.Forms 執行以顯示頁面的工作量就愈大。 針對複雜的版面配置，view 階層可以是深度和廣泛的，且具有多個嵌套層級。

例如，請考慮下列用於登入 Facebook 的範例應用程式按鈕：

![Facebook 按鈕](layout-compression-images/facebook-button.png)

此按鈕會指定為具有下列 XAML 視圖階層的自訂控制項：

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

您可以使用即時視覺化樹狀結構來檢查產生的嵌套視圖階層。 在 Android 上，嵌套視圖階層包含17個視圖：

![查看 Facebook 按鈕的階層](layout-compression-images/no-compression.png)

適用于 Xamarin.Forms iOS 和 Android 平臺上的應用程式的版面配置壓縮，目標是從視覺化樹狀結構中移除指定的配置來壓平合併視圖，以改善頁面轉譯效能。 所提供的效能優點會依頁面的複雜度、所使用的作業系統版本，以及應用程式執行所在的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

> [!NOTE]
> 雖然本文著重于在 Android 上套用版面配置壓縮的結果，但同樣適用于 iOS。

## <a name="layout-compression"></a>版面配置壓縮

在 XAML 中，您可以藉由 `CompressedLayout.IsHeadless` 在配置類別上將附加屬性設定為，來啟用版面配置壓縮 `true` ：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，您也可以使用 c # 來啟用它，方法是將配置實例指定為方法的第一個引數 `CompressedLayout.SetIsHeadless` ：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 由於版面配置壓縮會從視覺化樹狀結構移除配置，因此不適合具有視覺外觀或取得觸控輸入的版面配置。 因此，設定 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 屬性 (例如 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 、 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 、 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 、 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 、 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 以及 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 接受手勢的配置，不是配置壓縮的候選項目。 不過，在設定視覺外觀屬性或接受手勢的版面配置上啟用版面配置壓縮將不會產生組建或執行階段錯誤。 相反地，將會套用版面配置壓縮，而視覺外觀屬性和軌跡辨識將會以無訊息模式失敗。

針對 Facebook 按鈕，可以在三個配置類別上啟用版面配置壓縮：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

在 Android 上，這會產生14個視圖的嵌套視圖階層：

![使用版面配置壓縮來查看 Facebook 按鈕的階層](layout-compression-images/layout-compression.png)

相較于原始的嵌套視圖階層（17個視圖），這表示17% 的視圖數目減少。 雖然這樣的縮減可能顯得沒有意義，但在整個頁面上的縮減幅度可能更顯著。

### <a name="fast-renderers"></a>快速轉譯器

快速轉譯器會藉由簡維 Xamarin.Forms 產生的原生視圖階層，來減少 Android 上控制項的擴大和呈現成本。 這可讓您建立較少的物件，進而提高效能，進而產生較不復雜的視覺化樹狀結構，並減少記憶體的使用。 如需快速轉譯器的詳細資訊，請參閱 [快速](~/xamarin-forms/internals/fast-renderers.md)轉譯器。

針對範例應用程式中的 Facebook 按鈕，結合版面配置壓縮和快速轉譯器會產生8個視圖的嵌套視圖階層：

![使用版面配置壓縮和快速轉譯器來查看 Facebook 按鈕的階層](layout-compression-images/layout-compression-with-fast-renderers.png)

相較于原始的嵌套視圖階層17個視圖，這代表減少了52%。

範例應用程式包含從實際應用程式解壓縮的頁面。 如果沒有版面配置壓縮和快速轉譯器，頁面會在 Android 上產生130個視圖的嵌套視圖階層。 在適當的版面配置類別上啟用快速轉譯器和版面配置壓縮可將嵌套視圖階層縮減為70個視圖，減少46%。

## <a name="summary"></a>摘要

版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以嘗試改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

## <a name="related-links"></a>相關連結

- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)