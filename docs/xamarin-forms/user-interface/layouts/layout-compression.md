---
title: 版面配置壓縮
description: 版面配置壓縮從視覺化樹狀結構移除指定的配置以改善網頁呈現效能。 本文說明如何啟用壓縮配置和它可以使的優點。
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 9c698d539ab671ee2a033ae5943a46e0cc870f76
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="layout-compression"></a>版面配置壓縮

_版面配置壓縮從視覺化樹狀結構移除指定的配置以改善網頁呈現效能。本文說明如何啟用壓縮配置和它可以使的優點。_

## <a name="overview"></a>總覽

Xamarin.Forms 會執行使用遞迴方法呼叫的兩個系列的版面配置：

- 開始與頁面的視覺化樹狀結構頂端的版面配置，並透過包含每個頁面上的視覺元素的視覺化樹狀結構的所有分支進行。 為其他項目的父代的項目負責大小及定位相對於本身及其子系。
- 失效是用頁面上的項目中的變更會觸發新的版面配置循環程序。 當不再有正確的大小或位置時，項目都視為無效。 每個項目子系的視覺化樹狀結構中會收到警示，當其中一個子系變更大小。 因此，視覺化樹狀結構中的項目大小的變更會導致樹狀結構向上 ripple 的變更。

如需 Xamarin.Forms 執行版面配置方式的詳細資訊，請參閱[建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)。

版面配置處理序的結果是階層的原生控制項。 不過，此階層包含其他容器轉譯器與包裝函式平台轉譯器而言，進一步因而誇大巢狀的檢視階層架構。 更深層的巢狀的層級，Xamarin.Forms 必須執行才能顯示一個頁面的工作數量愈大。 對於複雜的版面配置，檢視階層可以是深層和廣泛，具有多個巢狀層級。

例如，請考慮下面的按鈕登入 Facebook 應用程式範例中：

![](layout-compression-images/facebook-button.png "Facebook Button")

這個按鈕指定為具有下列的 XAML 檢視階層架構的自訂控制項：

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

所產生的巢狀的檢視階層可檢查與[Xamarin Inspector](~/tools/inspector/index.md)。 在 Android 上的巢狀的檢視階層架構包含 17 的檢視：

![](layout-compression-images/no-compression.png "Facebook 按鈕的檢視階層架構")

版面配置壓縮，也就是適用於 iOS 和 Android 平台上的 Xamarin.Forms 應用程式，以外的工具來檢視巢狀方式置於 視覺化樹狀目錄中，以改善網頁呈現效能，移除指定的版面配置壓平合併。 傳遞的效能優勢會根據頁面、 所使用的作業系統版本和應用程式執行所在裝置的複雜性而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

> [!NOTE]
> 雖然本文著重在將配置壓縮套用在 Android 上的結果，但是這同樣適用於 iOS。

## <a name="layout-compression"></a>版面配置壓縮

在 XAML 中，可以啟用配置壓縮設定`CompressedLayout.IsHeadless`附加屬性`true`配置類別上：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，它可以藉由指定版面配置執行個體做為第一個引數，若要啟用 C# 中`CompressedLayout.SetIsHeadless`方法：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 版面配置壓縮從視覺化樹狀結構移除的版面配置，因為它不適用於具有視覺外觀，或可取得具備觸控輸入配置。 因此，版面配置，設定[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)屬性 (例如[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)， [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/)， [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)， [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)， [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)) 或可接受手勢，不適合用於版面配置壓縮。 不過，版面配置上啟用壓縮的版面配置，設定視覺外觀屬性，或者接受手勢，不會導致建置或執行階段錯誤。 相反地，將會套用配置壓縮，而且視覺外觀屬性和筆勢辨識會以無訊息模式失敗。

[Facebook] 按鈕，可以在三個配置類別上啟用配置壓縮：

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

在 Android 上，這會導致巢狀的檢視階層架構的 14 檢視：

![](layout-compression-images/layout-compression.png "檢視具有版面配置壓縮的 Facebook 按鈕的階層架構")

相較於原始的巢狀的檢視階層的 17 的檢視，代表 17%的檢視表的數目減少。 由於此減少可能會出現不重要，透過整頁檢視減少可能更重要。

### <a name="fast-renderers"></a>快速轉譯器

快速的轉譯器減少所產生的原生檢視階層簡維擴大和 Xamarin.Forms 控制項在 Android 上的轉譯成本。 這進一步提升效能，藉由建立較少的物件，這會依次產生較不複雜的視覺化樹狀結構中和較低的記憶體使用量。 如需快速的轉譯器的詳細資訊，請參閱[快速的轉譯器](~/xamarin-forms/internals/fast-renderers.md)。

範例應用程式的 Facebook 按鈕，組合配置壓縮與快速的轉譯器產生的巢狀的檢視階層架構 8 檢視：

![](layout-compression-images/layout-compression-with-fast-renderers.png "檢視具有版面配置壓縮與快速的轉譯器的 Facebook 按鈕的階層架構")

相較於原始的巢狀的檢視階層的 17 的檢視，代表 52%降低。

範例應用程式包含實際的應用程式從擷取的頁面。 如果沒有配置壓縮和快速的轉譯器，頁面會產生 130 的檢視，在 Android 上的巢狀的檢視階層架構。 啟用快速的轉譯器與適當的配置類別的配置壓縮到 70 的檢視，減少 46%的減少的巢狀的檢視階層架構。

## <a name="summary"></a>總結

版面配置壓縮從視覺化樹狀結構移除指定的配置以改善網頁呈現效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。


## <a name="related-links"></a>相關連結

- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
