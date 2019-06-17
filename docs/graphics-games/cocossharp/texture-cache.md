---
title: 使用 CCTextureCache 紋理快取
description: CocosSharp 的 CCTextureCache 類別提供組織快取，和卸載內容的標準方法。 它是特別適用於大型的遊戲可能不適合完全讀入 RAM，簡化的分組和紋理處置程序。
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 232363d6ce1cb93499716b2c1247c48403cf6cea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61302331"
---
# <a name="texture-caching-using-cctexturecache"></a>使用 CCTextureCache 紋理快取

_CocosSharp 的 CCTextureCache 類別提供組織快取，和卸載內容的標準方法。它是特別適用於大型的奧運賽可能不適合完全讀入 RAM，簡化的分組和紋理處置程序。_

`CCTextureCache`類別是 CocosSharp 的遊戲開發中不可或缺的一部分。 大部分的 CocosSharp 的遊戲使用`CCTextureCache`物件，即使在內部使用多個 CocosSharp 方法的不明確地*共用*紋理快取。

本指南涵蓋`CCTextureCache`以及為什麼很重要的遊戲開發。 特別是它涵蓋了：

 - 為什麼紋理快取的大小事
 - 材質的使用期限
 - 使用 SharedTextureCache
 - 消極式載入與 AddImage 與預先載入的比較
 - 處置紋理


## <a name="why-texture-caching-matters"></a>為什麼紋理快取的大小事

紋理快取是很重要的考量，遊戲開發人員，因為紋理載入耗時的作業，而且紋理需要大量的 RAM 在執行階段。

如同任何檔案作業時，從磁碟載入紋理可以是昂貴的作業。 紋理載入可能需要額外的時間，如果所載入的檔案需要處理，例如正在解壓縮 （在此情況下為 png 和 jpg 影像）。 紋理快取可以減少應用程式必須從磁碟載入檔案的次數。

如先前所述，紋理也會佔用大量的執行階段記憶體。 例如解析度的 iPhone 6 (1344 x 750) 調整大小的背景影像會佔用 4 mb 的 RAM –，即使該 PNG 檔案只有幾 kb 的大小。 紋理快取提供共用應用程式中的紋理參考的方式，以及輕鬆地在不同的遊戲狀態之間轉換時，卸載所有內容。


## <a name="texture-lifespan"></a>材質的使用期限

CocosSharp 紋理可能會保留在記憶體中的應用程式執行的整個長度，或它們可能會短暫存在。 記憶體降到最低使用量應用程式應該處置時不再需要的紋理。 當然，這表示紋理可能會處置並重新載入在稍後的時間，可以增加負載的時間或降低載入期間的效能。 

紋理載入通常需要權衡考量記憶體使用量和負載時間 / 執行階段效能。 使用少量的紋理記憶體的遊戲可以保留在記憶體中視需要的所有紋理，但較大的遊戲可能要卸載以釋出空間的紋理。

下圖顯示一個簡單的遊戲，而會視需要載入紋理和使其保持在記憶體中執行的整個長度：

![](texture-cache-images/image1.png "下圖顯示一個簡單的遊戲，而會視需要載入紋理和使其保持在記憶體中執行的整個長度")

前兩個長條表示該遊戲的執行時立即所需的紋理。 下列三個橫條代表針對每個層級，視需要載入的紋理。

大型遊戲是否夠它最終會載入足夠材質填滿所有裝置和作業系統所提供的 RAM。 若要解決這個問題，遊戲可能卸載紋理資料，當不再需要時。 例如下, 圖顯示當它不再需要則載入下一個層級 Level2Texture 卸載 Level1Texture 的遊戲。 最終結果是只有三個紋理會保留在記憶體中，在任何指定時間： 

![](texture-cache-images/image2.png "最終結果是只有三個紋理會保留在記憶體中，在任何指定時間")


如上所示的圖表指出可藉由卸載，降低紋理記憶體使用量，但如果播放程式決定要重新執行層級，這可能需要額外的載入時間。 另外值得注意的是 UITexture 和 MainCharacter 紋理會載入，永遠不會卸載。 這表示這些紋理需要在所有層級，因此一律保留在記憶體中。 


## <a name="using-sharedtexturecache"></a>使用 SharedTextureCache

CocosSharp 自動快取的紋理，當載入它們透過`CCSprite`建構函式。 例如下列程式碼只會建立一個紋理的執行個體：


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

自動快取 CocosSharp`star.png`紋理，以避免耗費資源的另一種建立許多相同`CCTexture2D`執行個體。 這透過`AddImage`呼叫在共用`CCTextureCache`執行個體，特別是`CCTextureCache.SharedTextureCache.Shared`。 若要了解如何`SharedTextureCache`會使用我們來看看下列程式碼在功能上等同於呼叫`CCSprite`使用字串參數的建構函式：


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` 如果會檢查這些引數檔案 (在此情況下`star.png`) 已載入。 如果是的話，則會傳回快取的執行個體。 如果沒有，即會從檔案系統中中, 載入和紋理的參考會儲存在內部針對後續`AddImage`呼叫。 亦即`star.png`映像只能載入一次，以及後續的呼叫需要任何額外的磁碟存取或其他紋理記憶體。


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>消極式載入與 AddImage 與預先載入的比較

`AddImage` 允許寫入相同的程式碼是否要求的紋理已經載入也是與否。 這表示內容將不會載入，直到需要為止;不過，這也可能導致在執行階段，因為無法預期的內容載入的效能問題。

例如，請考慮的遊戲，玩家的武器可以進行升級。 升級時，武器和砲彈會明顯變更，因而產生新的紋理所使用。 如果內容是消極式載入然後升級武器相關聯的材質不會載入一開始，而是稍後當播放程式取得升級。 

這個遊戲 mid 載入可能會導致遊戲*pop*，也就是簡短但明顯的凍結執行中。 若要避免這個問題，程式碼可以預測可能需要的紋理，前面，並預先載入。 例如，下列可用來預先載入紋理：


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

這個預先載入可能會造成浪費記憶體，而且可能會增加啟動時間。 比方說，播放程式可能不會實際取得所表示電源總`powerup3.png`紋理，所以將不必要地載入它。 當然，這可能是必要的成本來支付以避免潛在的 pop 中的遊戲，因此如果它將會放入 RAM 很通常最好預先載入內容。


## <a name="disposing-textures"></a>處置紋理

如果遊戲並不需要更多的紋理記憶體比最小規格的裝置上的可用紋理就需要處置。 相反地，較大的遊戲可能需要釋出紋理記憶體，以騰出空間給新的內容。 例如遊戲可能使用大量的記憶體儲存環境的紋理。 如果環境是只在特定層級則應該將它卸載層級結束時。


### <a name="disposing-a-single-texture"></a>處置單一材質

移除單一材質第一次需要呼叫`Dispose`方法，然後手動移除`CCTextureCache`。

以下顯示如何完全移除背景 sprite 以及其材質：


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

直接處置紋理時可能會有效處理一小部分的紋理，但這可能會變得容易發生錯誤時處理的較大的紋理集。

紋理可以群組成 （非共用） 的自訂`CCTextureCache`簡化紋理清除的執行個體。

例如，假設範例，內容已經預先載入使用層級特定`CCTextureCache`執行個體。 `CCTextureCache`執行個體可能會定義在類別中定義層級 (可能`CCLayer`或`CCScene`):


```csharp
CCTextureCache levelTextures; 
```

`levelTextures`然後表示要預先載入特定層級的紋理使用執行個體： 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

最後的層級結束時，紋理可以位於所有處置次透過`CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

Dispose 方法將會處置所有內部的紋理，清除這些紋理所使用的記憶體。 結合`CCTextureCache.Shared`層級或遊戲模式特定`CCTextureCache`結果保存整個遊戲，以及一些正在卸載模組層級結束，類似於本指南的開頭所顯示的圖表，透過一些紋理中的執行個體： 

![](texture-cache-images/image2.png "結合層級或遊戲模式特定 CCTextureCache 執行個體的結果中保存整個遊戲，以及一些正在卸載模組層級結束，類似於本指南的開頭所顯示的圖表，透過一些紋理 CCTextureCache.Shared")




## <a name="summary"></a>總結

本指南說明如何使用`CCTextureCache`類別來平衡記憶體使用量和執行階段效能。 `CCTexturCache.SharedTextureCache` 可以明確或隱含地用來載入及快取的應用程式生命週期的紋理，而`CCTextureCache`執行個體可用來卸載來減少記憶體使用量的紋理。

## <a name="related-links"></a>相關連結

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
