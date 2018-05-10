---
title: 使用 CCTextureCache 紋理快取
description: CocosSharp 的 CCTextureCache 類別提供的標準方式來組織，快取，及卸載內容。 它特別適用於大型的遊戲可能無法容納完全簡化程序的分組和處置紋理的 RAM。
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: bb75efea0914827f1d59a8e0943584597f91803a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="texture-caching-using-cctexturecache"></a>使用 CCTextureCache 紋理快取

_CocosSharp 的 CCTextureCache 類別提供的標準方式來組織，快取，及卸載內容。它特別適用於大型的遊戲可能無法容納完全簡化程序的分組和處置紋理的 RAM。_

`CCTextureCache`類別是 CocosSharp 遊戲開發中不可或缺的一部分。 大部分 CocosSharp 遊戲使用`CCTextureCache`物件，即使未明確地數量 CocosSharp 方法在內部使用*共用*紋理快取。

本指南涵蓋`CCTextureCache`和原因是很重要的遊戲開發。 具體來說涵蓋：

 - 為什麼紋理快取的事情
 - 紋理的使用期限
 - 使用 SharedTextureCache
 - 消極式載入和預先載入 AddImage 與比較
 - 處置紋理


## <a name="why-texture-caching-matters"></a>為什麼紋理快取的事情

紋理快取是遊戲開發的重要考量，因為紋理載入耗時的作業，而且紋理需要大量的 RAM 在執行階段。

如同任何檔案作業時，從磁碟載入材質可以是昂貴的作業。 紋理載入可能需要額外的時間，如果所載入的檔案需要處理，例如正在解壓縮 （在本例中為 png 和 jpg 影像）。 紋理快取可以減少應用程式必須從磁碟載入檔案的次數。

如上面所述，紋理也會佔用大量的執行階段記憶體。 例如背景影像的大小調整成 iPhone 6 (1344 x 750) 的解析度會佔用 4 mb 的 RAM – 即使 PNG 檔案只有幾 kb 的大小。 紋理快取會提供方法以共用應用程式中的紋理參考以及遊戲的不同狀態之間轉換時卸除所有內容的簡單方法。


## <a name="texture-lifespan"></a>紋理的使用期限

CocosSharp 紋理可能會保留在記憶體中的應用程式執行的整個範圍，或者可能是短暫的。 記憶體降到最低紋理不再需要時應該處置使用量應用程式。 當然，這表示紋理可能會處置並重新載入在稍後的時間，可以增加負載的時間，或在載入期間的效能會降低。 

紋理載入通常需要的記憶體使用量和載入時間之間的利弊取捨 / 執行階段效能。 使用小的紋理記憶體數量的遊戲可以保留在記憶體中視需要的所有紋理，但較大的遊戲可能要卸載以釋出空間的紋理。

下圖顯示簡單的遊戲，而會視需要載入材質和它們保留在記憶體中執行的完整長度：

![](texture-cache-images/image1.png "下圖顯示簡單的遊戲，而會視需要載入材質和它們保留在記憶體中執行的完整長度")

前兩個長條表示紋理所需立即開始遊戲執行時。 下列三個橫條都代表紋理的每個層級，視需要載入。

如果遊戲大型夠它最終會載入足夠材質填滿所有裝置和作業系統所提供的 RAM。 若要解決這個問題，不再需要時遊戲時，可能會卸載紋理資料。 例如下, 圖顯示當不再需要然後載入下一個層級 Level2Texture 卸載 Level1Texture 的遊戲。 最終結果是只有三個紋理會保留在記憶體中，在任何指定時間： 

![](texture-cache-images/image2.png "最終結果是只有三個紋理會保留在記憶體中，在任何指定時間")


上圖表示紋理的記憶體使用量可以減少卸載，但如果播放程式決定要重新執行層級，這可能需要額外的載入時間。 它也值得注意的是 UITexture 和 MainCharacter 紋理會載入，並永遠不會卸載。 這表示這些紋理會需要在所有層級，因此一律保留在記憶體中。 


## <a name="using-sharedtexturecache"></a>使用 SharedTextureCache

CocosSharp 自動快取紋理，當載入它們透過`CCSprite`建構函式。 例如下列程式碼只會建立一個紋理的執行個體：


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

自動快取 CocosSharp`star.png`紋理，以避免昂貴的另一種建立許多相同`CCTexture2D`執行個體。 這會透過`AddImage`呼叫上共用`CCTextureCache`執行個體，特別是`CCTextureCache.SharedTextureCache.Shared`。 若要了解如何`SharedTextureCache`用我們來看看下列的程式碼功能上等同於呼叫`CCSprite`字串參數的建構函式：


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` 如果會檢查這些引數檔案 (在此情況下`star.png`) 已經載入。 如果是的話，則會傳回快取的執行個體。 如果不會再從檔案系統中，載入它，而且紋理的參考會儲存在內部針對後續`AddImage`呼叫。 換句話說`star.png`映像只能載入一次，而且後續的呼叫需要任何額外的磁碟存取或其他紋理的記憶體。


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>消極式載入和預先載入 AddImage 與比較

`AddImage` 可讓撰寫相同程式碼是否要求的紋理已經載入也是與否。 這表示，內容將不會載入，直到需要為止。不過，這也可能導致效能問題，因為無法預期的內容載入執行階段。

如需範例，請考慮的遊戲玩家的武器可以進行升級。 升級時，武器和射會明顯地變更，因而產生新的紋理正在使用中。 如果內容為延遲載入然後升級武器相關聯的紋理將不會載入一開始，而是在稍後當播放程式會取得升級。 

這個遊戲 mid 載入可能會導致遊戲*pop*，也就是在執行中的短，但也顯著凍結。 若要避免這個問題，程式碼可以預測哪些紋理時可能需要事先並預先載入它們。 例如，下列可用來預先載入紋理：


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

這個預先載入可能會造成浪費的記憶體中，而且可能增加啟動時間。 例如，播放程式可能實際上永遠不會取得由電源總`powerup3.png`紋理，所以將不必要地載入它。 當然，這可能是必要的成本，付費以避免潛在的快顯的遊戲，因此如果它符合 RAM 是通常最好預先載入內容。


## <a name="disposing-textures"></a>處置紋理

如果遊戲不需要更多的紋理記憶體而不是最小規格的裝置上的可用，則不需要處置紋理。 相反地，較大的遊戲可能需要釋放紋理的記憶體，以騰出空間給新的內容。 例如遊戲可能使用大量的儲存環境的紋理的記憶體。 如果環境只用於特定層級則它應該卸載層級結束時。


### <a name="disposing-a-single-texture"></a>處置單一的紋理

需要先移除單一材質呼叫`Dispose`方法，然後手動移除`CCTextureCache`。

以下顯示如何完全移除背景精靈以及其紋理：


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

直接處置紋理時可能會有效處理較少的紋理，但這可能會變得容易發生錯誤時處理較大的紋理集。

材質可以分為 （非共用） 的自訂`CCTextureCache`簡化紋理清除的執行個體。

例如，請考慮使用範例的內容已經預先載入使用層級指定`CCTextureCache`執行個體。 `CCTextureCache`執行個體可能定義在定義層級的類別 (它可能是`CCLayer`或`CCScene`):


```csharp
CCTextureCache levelTextures; 
```

`levelTextures`然後預先載入特定層級的紋理使用執行個體： 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

最後的層級結束時，紋理可以是一次透過所有處置`CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

Dispose 方法將處置所有內部的紋理，清除這些紋理所使用的記憶體。 結合`CCTextureCache.Shared`與層級或遊戲模式特定`CCTextureCache`結果保存整個遊戲，以及一些正在卸載模組層級結束，類似於圖表顯示本指南的開頭為透過某些紋理中的執行個體： 

![](texture-cache-images/image2.png "結合層級或遊戲模式特定 CCTextureCache 執行個體的結果中保存整個遊戲，以及一些正在卸載模組層級結束，類似於圖表顯示本指南的開頭為透過某些材質 CCTextureCache.Shared")




## <a name="summary"></a>總結

本指南示範如何使用`CCTextureCache`類別，以平衡記憶體使用量和執行階段效能。 `CCTexturCache.SharedTextureCache` 可以明確或隱含地用來載入及快取的應用程式生命週期的紋理，而`CCTextureCache`執行個體可以用來卸載紋理，來減少記憶體使用量。

## <a name="related-links"></a>相關的連結

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
