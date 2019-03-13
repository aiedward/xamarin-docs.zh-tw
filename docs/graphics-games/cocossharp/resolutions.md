---
title: 處理 CocosSharp 中的多個解決方法
description: 本指南說明如何搭配 cocossharp 使用 Tiled 開發會正確顯示不同解析度的裝置的遊戲。
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 6803dc2668b89ee2d037da8b34e202191dd5465d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118678"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>處理 CocosSharp 中的多個解決方法

_本指南說明如何搭配 cocossharp 使用 Tiled 開發會正確顯示不同解析度的裝置的遊戲。_

CocosSharp 提供方法來標準化物件尺寸，在您的遊戲，不論實體裝置的顯示器上的像素數目。 傳統上，為電腦和遊戲機開發的遊戲可能目標的單一解決方案。 現今的遊戲 – 特別是行動裝置遊戲 – 必須建置和以配合各式各樣的裝置。 本指南說明如何標準化 CocosSharp 的遊戲，不論實際的顯示器的解析度特性。

CocosSharp 的預設解析行為是至遊戲中座標為符合實體像素為單位。 下表顯示不同的裝置會呈現與 368 x 240 的寬度和高度的背景環境 sprite。 第一個資料列會就技術上而言不實際的裝置，但而不是預期的轉譯的 sprite，無論裝置解析度：


| **裝置** | **顯示器解析度** | **範例螢幕擷取畫面** |
|--- | --- |--- |
|所要的顯示|368 x 240 （具有黑色長條的外觀比例）| ![368 x 240 （具有黑色長條的外觀比例）](resolutions-images/image1.png) |
|iPhone 4 秒|960x640| ![iPhone 4 秒 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

本文件涵蓋如何使用 CocosSharp 來修正此問題，如上表所示。 也就是說，我們將討論如何進行轉譯的第一個資料列 – 不論螢幕解析度所示的任何裝置。


## <a name="working-with-setdesignresolutionsize"></a>使用 SetDesignResolutionSize

`CCScene`類別通常用於做為根容器所有視覺物件，但它也提供呼叫的靜態方法`SetDesignResolutionSize`來指定所有的場景的預設大小。 亦即`SetDesignResolutionSize`方法可讓開發人員開發遊戲，這將會正確顯示在各種不同的硬體解決方案。 CocosSharp 專案範本會使用這個方法，將預設專案大小為 1024 x 768 解析度，如下列程式碼所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

您可以變更`desiredWidth`和`desiredHeight`上述修改以符合您的遊戲所需的解析度顯示的變數。 例如，上述程式碼就可以修改，如下所示以全螢幕顯示 368 x 240 背景：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` 讓我們可以指定如何在遊戲視窗調整，以所需的解析度。 下列各節示範的 500 x 500 映像具有不同的顯示方式`CCSceneResolutonPolicy`值傳遞至`SetDesignResolutionSize`方法。 會提供下列值`CCSceneResolutionPolicy`列舉：

 - `ShowAll` – 顯示整個要求的解析，維持外觀比例。
 - `ExactFit` – 顯示整個要求的解析度，但不會維持外觀比例。
 - `FixedWidth` – 顯示整個要求的寬度，維持外觀比例。
 - `FixedHeight` – 顯示整個要求的高度，維持外觀比例。
 - `NoBorder` – 顯示整個高度或整個寬度，維持外觀比例。 如果裝置的長寬比大於長寬比的所需的解析度，則系統會顯示整個寬度。 如果裝置的外觀比例小於所需的解析度外觀比例，則系統會顯示整個高度。
 - `Custom` – 顯示相依於`Viewport`屬性與目前`CCScene`。

所有的螢幕擷取畫面在橫向 iPhone 4 秒解析度 (960x640) 所產生，並使用此映像：

![](resolutions-images/image4.png "所有的螢幕擷取畫面會以橫向 iPhone 4 秒解析度 960x640 產生和使用此映像")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` 指定螢幕上，會顯示整個遊戲的解析度，但可能會顯示*上下黑邊*（黑色列） 來調整不同的外觀比例。 通常使用此原則，因為它可以保證將螢幕上顯示整個遊戲的檢視，而不需要任何扭曲。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

上下黑邊是左邊和右邊的 映像來處理實體的長寬比正在超出所需的解析度顯示：

![](resolutions-images/image5.png "上下黑邊是左邊和右邊的 映像來處理實體的長寬比正在超出所需的解析度顯示")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` 指定整個遊戲的解析會與沒有出現上下黑邊螢幕上顯示。 可檢視區域可能會扭曲的程度 （外觀比例可能無法維護） 根據硬體的外觀比例。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

沒有出現上下黑邊是可見的但由於裝置解析度是矩形會扭曲遊戲的檢視：

![](resolutions-images/image6.png "沒有出現上下黑邊是可見的但由於裝置解析度是矩形會扭曲遊戲的檢視")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` 指定檢視的寬度會比對傳遞至的寬度值`SetDesignResolutionSize`，但可檢視的高度受限於實體裝置的外觀比例。 高度值傳遞至`SetDesignResolutionSize`會被忽略，因為它將會在執行階段根據實體裝置的外觀比例計算。 這表示可能小於所需的高度 （這會導致遊戲的檢視表的幕外的部分） 或導出的高度可能大於所需的高度 （這會導致多個遊戲的檢視所顯示） 之計算的高度。 因為這可能會導致多個顯示的遊戲，然後看起來好像發生上下黑邊;不過，額外的空間不一定會黑色，如果有任何視覺物件會出現。 

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4 秒會有外觀比例為 3:2，讓導出的高度大約 333 單位：

![](resolutions-images/image7.png "IPhone 4 秒會有外觀比例為 3:2，讓導出的高度大約 333 單位")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

就概念而言，`FixedHeight`行為類似於`FixedWidth`– 遊戲都將遵循高度值傳遞至`SetDesignResolutionSize,`但會計算在執行階段實際的解析度為基礎的寬度。 如上所述，這表示在顯示的寬度是小於或大於所需的寬度，在遊戲所產生，可關閉螢幕或多個顯示，分別在遊戲。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下列螢幕擷取畫面以橫向模式中執行的應用程式建立的因為計算的寬度大於 500 – 特別是 750。 此原則會保留 0 的 X 值靠左對齊，因此額外的解析度就可以在畫面右邊。

![](resolutions-images/image8.png "此原則會保留 0 的 X 值靠左對齊，因此額外的解析度就可以在畫面右側")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` 嘗試顯示沒有出現上下黑邊與應用程式，同時維持原始外觀比例 （不失真）。 如果要求的解析度外觀比例符合裝置的實體的外觀比例，就會發生不裁剪。 如果不相符的長寬比，然後裁剪會發生。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下列螢幕擷取畫面會顯示顯示剪輯，而會顯示所有 500 像素為單位的顯示寬度的頂端和底部部分：

![](resolutions-images/image9.png "這個螢幕擷取畫面會顯示頂端和底部的部分顯示剪輯，而會顯示所有 500 像素為單位的顯示寬度")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` 可讓每個`CCScene`來指定它自己自訂的檢視區相對於中所指定的解析度`SetDesignResolutionSize`。

場景定義他們`Viewport`屬性來建構`CCViewport`，這又建構與`CCRect`。 如需詳細資訊`CCViewport`並`CCRect`請參閱[CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)。 建立的內容中`CCViewport``CCRect`建構函式的參數會指定 （依順序）：

 - x – 水平位移量檢視區，其中每個單位都代表一整個螢幕的寬度。 比方說，使用.5f 結果的值向右移位的螢幕寬度的一半，場景中。
 - y – 垂直位移量檢視區，其中每個單位都代表一整個螢幕的高度。 比方說，向下移動的螢幕高度的一半，場景中使用.5f 結果的值。
 - 寬度 – 場景應佔據的水平部分。 例如，使用值為 1 / 3.0f 導致場景水平佔用螢幕的三分之一。
 - 高度 – 場景應佔據的垂直部分。 例如，使用值為 1 / 3.0f 導致場景垂直佔用螢幕的三分之一。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

上述程式碼是由下列項目所導致：

![](resolutions-images/image10.png "上述程式碼會產生此螢幕擷取畫面")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio`簡化了在具有較高解析度螢幕裝置上使用更高解析度的紋理。 具體來說，這個屬性可讓遊戲，而不需要使用更高解析度資產，若要變更的大小或位置的視覺項目。 

例如，遊戲可能包括藝術資產的遊戲的字元，也就是 200 像素高，和遊戲畫面 (所指定`SetDesignResolutionSize`) 可能高度 400 像素。 在此情況下，字元會佔用一半畫面。 不過，如果之字元的更高解析度裝置所使用的 400 像素高的資產，字元會佔用整個顯示畫面的高度。 目的是要保留的字元相同的大小，善用較高解析度裝置，則某些額外的程式碼不需要在螢幕的半高度保留字元 sprite。

以上所顯示的簡單範例表示一個常見的問題，遊戲開發人員 – 新增較大型的資產，而不需要開發人員執行每個視覺項目，根據裝置解析度調整大小。 `DefaultTexelToContentSizeRatio` 針對調整大小的視覺項目使用的通用的屬性。 這個值會調整特定類型的所有視覺項目所指派的值。

此屬性位於下列類別： 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio for Mac 範本集`CCSprite.DefaultTexelToContentSizeRatio`根據為了舉例說明如何使用它在裝置的寬度。 下列程式碼包含在`CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>DefaultTexelToContentSizeRatio 範例

若要查看如何`DefaultTexelToContentSizeRatio`會影響視覺效果的大小項目，請考慮上述程式碼：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

這會導致下列使用 500 x 500 紋理的影像：

![](resolutions-images/image5.png "這會導致使用 500 x 500 紋理此映像")

執行實體的 2048 x 1536 解析度的 Retina iPad。 這表示，如上方顯示的遊戲會延展 500 像素，透過 1536年實體像素為單位。 遊戲可以利用這個額外的解決方案藉由建立項目通常稱為*hd*資產 – 這設計用來執行在更高解析度畫面上的資產。 比方說，這個遊戲中可以使用此大小調整為 1000 x 1000 的材質的 hd 版本。 不過，使用較大的映像會導致`CCSprite`寬度和高度的 1000 個單位。 因為我們遊戲一律會顯示 500 的單位 (因為`SetDesignResolutioSize`呼叫)，則我們 1000 x 1000 sprite 會是太大 （只有左下方的它會顯示）：

![](resolutions-images/image11.png "遊戲一律會顯示由於 SetDesignResolutioSize 呼叫 500 的單位，因為 1000 x 1000 sprite 會太大而只左下方的顯示")

我們可以設定`CCSprite.DefaultTexelToContentSizeRatio`負責 1000 x 1000 紋理一樣寬且高度為原始的 500 x 500 材質，請在兩次：


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

現在如果我們執行遊戲 1000 x 1000 紋理會是完整可見的：

![](resolutions-images/image12.png "現在如果我們執行遊戲 1000 x 1000 紋理會是完全可見")


### <a name="defaulttexeltocontentsizeratio-details"></a>DefaultTexelToContentSizeRatio 詳細資料

`DefaultTexelToContentSizeRatio`屬性是`static,`這表示應用程式中的所有 sprite 會共用相同的值。 對不同解析度的資產遊戲的典型方法是包含一組完整的資產以供每個解決方式類別。 根據預設 CocosSharp Visual Studio for Mac 範本提供**ld**並**hd**資產，可用於支援材質的兩個集合的遊戲的資料夾。 使用內容的範例內容資料夾可能看起來像：

![](resolutions-images/image13.png "使用內容的範例內容資料夾可能看起來像是")

請注意，預設範本也會包含有條件地將應用程式的程式碼`ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```

這表示應用程式會根據是否正在以高解析度或一般的解析度模式中執行的路徑中的檔案搜尋。 例如，如果**hd**並**ld**資料夾包含名為的檔案**background.png**然後會執行下列程式碼，並將其選取正確的檔案進行解析：


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>總結

這篇文章說明如何建立正確顯示，不論裝置解析度的遊戲。 它會顯示使用的範例不同`CCSceneResolutionPolicy`調整大小的遊戲，根據裝置解析度的值。 它也提供如何的範例`DefaultTexelToContentSizeRatio`可以用來容納多個內容集，而不需要個別調整的視覺項目。

## <a name="related-links"></a>相關連結

- [CocosSharp 簡介](~/graphics-games/cocossharp/index.md)
- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
