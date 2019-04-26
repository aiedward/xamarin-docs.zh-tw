---
title: 版面配置壓縮
description: 版面配置壓縮從視覺化樹狀結構移除指定的版面配置，以試圖改善頁面轉譯效能。 這篇文章說明如何啟用版面配置壓縮和也可能帶來的好處。
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 4609593337cc0d003c5b35ef4570f11e27b14f7b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371714"
---
# <a name="layout-compression"></a>版面配置壓縮

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)

_版面配置壓縮從視覺化樹狀結構移除指定的版面配置，以試圖改善頁面轉譯效能。這篇文章說明如何啟用版面配置壓縮和也可能帶來的好處。_

## <a name="overview"></a>總覽

Xamarin.Forms 會執行兩個數列的遞迴方法呼叫的配置：

- 版面配置開始與頁面的視覺化樹狀結構頂端，並繼續進行到包含每個頁面上的 visual 元素的視覺化樹狀結構的所有分支。 其他元素的父代的項目負責大小及定位相對於本身及其子系。
- 失效是由此頁面上的項目中的變更會觸發新的配置循環程序。 當他們不再擁有正確的大小或位置時，項目都視為無效。 具有子系的視覺化樹狀結構中的每個項目會收到警示，只要其中一個子系變更大小。 因此，視覺化樹狀結構中的項目大小的變更會導致 ripple 樹狀結構的變更。

如需有關 Xamarin.Forms 版面配置的執行方式的詳細資訊，請參閱 <<c0> [ 建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)。

版面配置程序的結果是原生控制項階層架構。 不過，此階層會包含其他容器產生器與包裝函式針對平台的轉譯器，進一步擴張巢狀檢視階層。 更深入的層級的巢狀，Xamarin.Forms 必須執行才能顯示頁面的工作數量越大。 對於複雜的版面配置，深度和廣泛的、 具有多個巢狀層級，可以是檢視階層。

例如，請考慮下面的按鈕登入的 Facebook 應用程式：

![](layout-compression-images/facebook-button.png "Facebook 按鈕")

此按鈕指定為下列 XAML 檢視階層的自訂控制項：

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

可檢查所產生的巢狀的檢視階層，具有[Xamarin Inspector](~/tools/inspector/index.md)。 在 Android 上，巢狀的檢視階層包含 17 的檢視：

![](layout-compression-images/no-compression.png "Facebook 按鈕的檢視階層")

版面配置壓縮，也就是適用於 iOS 和 Android 平台上的 Xamarin.Forms 應用程式，目標是壓平合併巢狀可以改善頁面轉譯效能的視覺化樹狀結構中移除指定的版面配置檢視中。 所傳遞的效能優勢會根據頁面、 所使用的作業系統版本和應用程式執行所在裝置的複雜度而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

> [!NOTE]
> 雖然這篇文章著重於將版面配置壓縮套用在 Android 上的結果，但卻同樣適用於 iOS。

## <a name="layout-compression"></a>版面配置壓縮

在 XAML，版面配置壓縮可以藉由設定啟用`CompressedLayout.IsHeadless`附加屬性`true`上配置類別：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，它可在 C# 中所做的第一個引數中指定的版面配置執行個體`CompressedLayout.SetIsHeadless`方法：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 版面配置壓縮會移除視覺化樹狀結構中的版面配置，因為它不適合具有視覺外觀，或可取得具備觸控輸入的版面配置。 因此，配置的設定[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)屬性 (例如[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)， [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)， [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)， [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)， [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)並[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)或可接受筆勢，不適合用於版面配置壓縮。 不過，啟用版面配置，設定視覺外觀的屬性，或可接受筆勢，版面配置壓縮不會導致組建或執行階段錯誤。 相反地，版面配置壓縮將會套用，且視覺外觀屬性，以及建置手勢辨識、 將會以無訊息模式失敗。

[Facebook] 按鈕，可以在三個配置類別上啟用版面配置壓縮：

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

在 Android 上，這會導致 14 檢視巢狀的檢視階層：

![](layout-compression-images/layout-compression.png "Facebook 按鈕與版面配置壓縮檢視階層")

相較於原始的巢狀的檢視階層的 17 的檢視，代表檢視 17%的次數降低。 雖然此減少可能會出現無意義的對整個頁面的檢視減少可能會更重要。

### <a name="fast-renderers"></a>快速轉譯器

快速轉譯器會減少膨脹和轉譯成本 Xamarin.Forms 控制項在 Android 上透過壓平所產生的原生檢視階層。 這進一步提升效能，藉由建立較少的物件，這會接著產生降低記憶體使用量並較不複雜的視覺化樹狀結構中。 如需有關快速轉譯器的詳細資訊，請參閱[快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)。

範例應用程式中的 [Facebook] 按鈕，結合版面配置壓縮與快速轉譯器會產生 8 檢視巢狀的檢視階層：

![](layout-compression-images/layout-compression-with-fast-renderers.png "版面配置壓縮與快速轉譯器的 [Facebook] 按鈕的檢視階層")

相較於原始的巢狀的檢視階層的 17 的檢視，這個屬性表示 52%的降低。

範例應用程式包含擷取自實際的應用程式頁面。 如果沒有版面配置壓縮和快速轉譯器，頁面會產生 130 的檢視，在 Android 上的巢狀的檢視階層。 啟用快速轉譯器和適當的配置類別上的版面配置壓縮到 70 的檢視，減少 46%的降低巢狀的檢視階層。

## <a name="summary"></a>總結

版面配置壓縮從視覺化樹狀結構移除指定的版面配置，以試圖改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。


## <a name="related-links"></a>相關連結

- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
