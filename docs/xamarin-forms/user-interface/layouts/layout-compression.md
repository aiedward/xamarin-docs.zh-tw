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
ms.openlocfilehash: 40af5aeaa51025dae70113faa6f7ff83edf43c73
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138021"
---
# <a name="layout-compression"></a>版面配置壓縮

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以嘗試改善頁面轉譯效能。本文說明如何啟用版面配置壓縮，以及它可以帶來的好處。_

## <a name="overview"></a>概觀

Xamarin.Forms使用兩個遞迴方法呼叫序列來執行版面配置：

- 版面配置會從視覺化樹狀結構的頂端開始，並具有頁面，並會繼續執行視覺化樹狀結構的所有分支，以包含頁面上的每個視覺元素。 身為其他專案之父系的元素會負責調整大小，並將其子系相對於其本身。
- 「失效」是頁面上專案中的變更會觸發新版面配置週期的進程。 當元素不再具有正確的大小或位置時，會將其視為無效。 視覺樹狀結構中具有子系的每個專案都會在其中一個子系變更大小時收到警示。 因此，視覺化樹狀結構中元素大小的變更可能會導致在樹狀結構上出現變更。

如需如何執行版面配置的詳細資訊 Xamarin.Forms ，請參閱[建立自訂版面](~/xamarin-forms/user-interface/layouts/custom.md)配置。

版面配置程式的結果就是原生控制項的階層。 不過，此階層包含平臺轉譯器的其他容器轉譯器和包裝函式，可進一步因而誇大視圖階層嵌套。 更深入的嵌套層級，顯示頁面所需執行的工作量就越大 Xamarin.Forms 。 針對複雜的版面配置，視圖階層可以是深度和廣泛，而且具有多個嵌套層級。

例如，針對登入 Facebook 的範例應用程式，請考慮下列按鈕：

![](layout-compression-images/facebook-button.png "Facebook Button")

這個按鈕會指定為具有下列 XAML 視圖階層的自訂控制項：

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

您可以使用[Xamarin Inspector](~/tools/inspector/index.md)來檢查產生的嵌套視圖階層。 在 Android 上，嵌套視圖階層包含17個視圖：

![](layout-compression-images/no-compression.png "View Hierarchy for Facebook Button")

配置壓縮適用于 Xamarin.Forms iOS 和 Android 平臺上的應用程式，目的是要從視覺化樹狀結構中移除指定的版面配置來壓平合併視圖，這樣可以改善頁面轉譯效能。 所提供的效能優勢會根據頁面的複雜度、使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

> [!NOTE]
> 雖然本文著重于在 Android 上套用版面配置壓縮的結果，但同樣適用于 iOS。

## <a name="layout-compression"></a>版面配置壓縮

在 XAML 中，可以藉由將配置 `CompressedLayout.IsHeadless` 類別上的附加屬性設定為來啟用版面配置壓縮 `true` ：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，您也可以將配置實例指定為方法的第一個引數，以在 c # 中啟用它 `CompressedLayout.SetIsHeadless` ：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 由於版面配置壓縮會從視覺化樹狀結構中移除版面配置，因此不適用於具有視覺外觀或取得觸控輸入的版面配置。 因此，設定屬性的配置 [`VisualElement`](xref:Xamarin.Forms.VisualElement) （例如 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 、 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 、 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 、 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 、 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 接受手勢的配置）不是版面配置壓縮的候選項目。 不過，針對設定視覺外觀屬性或接受手勢的配置啟用版面配置壓縮，將不會導致組建或執行階段錯誤。 相反地，會套用版面配置壓縮，而視覺外觀屬性和手勢辨識則會以無訊息模式失敗。

針對 [Facebook] 按鈕，可以在三個版面配置類別上啟用版面配置壓縮：

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

![](layout-compression-images/layout-compression.png "View Hierarchy for Facebook Button with Layout Compression")

相較于17個視圖的原始嵌套視圖階層，這表示減少17% 的視圖數目。 雖然這種縮減可能很重要，但整個頁面的視圖縮減也會更顯著。

### <a name="fast-renderers"></a>快速轉譯器

快速轉譯器藉由簡維 Xamarin.Forms 產生的原生視圖階層來減少 Android 上控制項的擴大和呈現成本。 這會藉由建立較少的物件來提升效能，進而導致較不復雜的視覺化樹狀結構和較少的記憶體使用。 如需快速轉譯器的詳細資訊，請參閱[快速](~/xamarin-forms/internals/fast-renderers.md)轉譯器。

針對範例應用程式中的 Facebook 按鈕，結合版面配置壓縮和快速轉譯器會產生8個 views 的嵌套視圖階層：

![](layout-compression-images/layout-compression-with-fast-renderers.png "View Hierarchy for Facebook Button with Layout Compression and Fast Renderers")

相較于17個視圖的原始嵌套視圖階層，這表示減少了52%。

範例應用程式包含從實際應用程式解壓縮的頁面。 如果沒有版面配置壓縮和快速轉譯器，頁面會在 Android 上產生130視圖的嵌套視圖階層。 在適當的版面配置類別上啟用快速轉譯器和版面配置壓縮，可將嵌套視圖階層縮減為70個視圖，減少46%。

## <a name="summary"></a>摘要

版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以嘗試改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

## <a name="related-links"></a>相關連結

- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)
