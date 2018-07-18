---
title: 處理 CocosSharp 在多種解析度
description: 本指南示範如何使用 CocosSharp 開發會正確顯示不同解析度的裝置的遊戲。
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 577a3edbd106b6fba298b3ee5999265ef955f9dd
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920815"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>處理 CocosSharp 在多種解析度

_本指南示範如何使用 CocosSharp 開發會正確顯示不同解析度的裝置的遊戲。_

CocosSharp 提供方法來標準化物件維度，在您的遊戲，不論實體裝置的顯示器上的像素數目。 傳統上，遊戲開發電腦和遊戲主控台可以針對單一的解析度。 現今的遊戲 – 特別適用於行動裝置的遊戲 – 必須建置和適應各式各樣的裝置。 本指南示範以標準化 CocosSharp 遊戲，不論實際顯示器的解析度特性。

解決方式的預設行為 CocosSharp 是遊戲中座標為符合實體像素為單位。 下表顯示如何在各種裝置所呈現的寬度和高度 368 x 240 背景環境精靈。 第一個資料列的是精靈的技術上而言不是精靈的實際裝置，不僅而不是精靈的預期，不論裝置解析度轉譯：


| **裝置** | **顯示器解析度** | **範例螢幕擷取畫面** |
|--- | --- |--- |
|所要的顯示|368 x 240 （與黑色的長寬比列）| ![368 x 240 （與黑色的長寬比列）](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960 x 640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

本文件涵蓋如何使用 CocosSharp 來修正問題，如上表所示。 也就是說，我們將討論如何進行任何轉譯的第一個資料列 – 無論螢幕解析度所示的裝置。


## <a name="working-with-setdesignresolutionsize"></a>使用 SetDesignResolutionSize

`CCScene`類別通常用於做為根容器所有的視覺物件，不過它也提供靜態方法呼叫`SetDesignResolutionSize`來指定所有場景的預設大小。 換句話說`SetDesignResolutionSize`方法可讓開發人員開發遊戲將會正確地顯示於各種不同的硬體解決方案。 CocosSharp 專案範本會使用這個方法，將預設專案大小為 1024 x 768，如下列程式碼所示：


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

您可以變更`desiredWidth`和`desiredHeight`變數上述修改以符合您的遊戲的所需的解析度顯示。 比方說，上述的程式碼無法，如下所示修改成以全螢幕顯示 368 x 240 背景：


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

`SetDesignResolutionSize` 可讓我們來指定如何在遊戲視窗調整，以所需的解析度。 下列章節將說明不同的 500 x 500 映像的顯示方式`CCSceneResolutonPolicy`值傳遞至`SetDesignResolutionSize`方法。 所提供的下列值`CCSceneResolutionPolicy`列舉：

 - `ShowAll` – 顯示整個要求的解析度，維持外觀比例。
 - `ExactFit` – 顯示整個要求的解析，但不會維持外觀比例。
 - `FixedWidth` – 顯示維持外觀比例的整個寬度要求。
 - `FixedHeight` – 顯示整個要求的高度維持外觀比例。
 - `NoBorder` – 顯示整個高度或整個寬度，維持外觀比例。 如果裝置的長寬比大於外觀比例的所需的解析度，然後會顯示整個寬度。 如果裝置的長寬比所需的解析度比例，會顯示整個高度。
 - `Custom` – 顯示相依於`Viewport`屬性的目前`CCScene`。

所有的螢幕擷取畫面在橫向 iPhone 4s 解析度 (960 x 640) 所產生，並使用此映像：

![](resolutions-images/image4.png "所有的螢幕擷取畫面在橫向 iPhone 4s 解析度 960 x 640 所產生和使用此映像")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` 指定整個遊戲的解析度就會顯示在螢幕上，但可能會顯示*黑邊*來調整不同的外觀比例 （黑色列）。 因為它可以保證將螢幕上顯示整個遊戲的檢視，沒有任何扭曲程度，通常會使用此原則。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

黑邊都看得到左邊和右邊的影像來說明實體的長寬比的寬度超過所需的解析度：

![](resolutions-images/image5.png "黑邊都看得到左邊和右邊的影像來說明實體的長寬比的寬度超過所需的解析度")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` 指定整個遊戲的解析度會與沒有黑邊螢幕上顯示。 可檢視區域可能會扭曲 （外觀比例不保留） 根據硬體外觀比例。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

沒有黑邊是可見的但因為裝置解析度是矩形會扭曲遊戲的檢視：

![](resolutions-images/image6.png "沒有黑邊是可見的但因為裝置解析度是矩形會扭曲遊戲的檢視")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` 指定檢視的寬度會符合寬度值傳遞至`SetDesignResolutionSize`，但可檢視的高度受限於實體裝置的外觀比例。 傳遞至的高度值`SetDesignResolutionSize`會被忽略，因為它會計算在執行階段根據實體裝置的外觀比例。 這表示可能小於所需的高度 （這會導致遊戲的檢視表的螢幕部分） 或導出的高度可能大於所需高度 （這會導致多個遊戲的檢視所顯示） 之計算的高度。 因為這可能會導致多個不會再顯示遊戲，然後可能會如同已發生黑邊;不過，額外的空間不一定會有黑色，如果有出現任何視覺物件。 

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s 具有外觀比例為 3:2，所以導出的高度是大約 333 單位：

![](resolutions-images/image7.png "IPhone 4s 都有的長寬比 3:2，因此導出的高度是大約 333 單位")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

就概念而言，`FixedHeight`的行為類似於`FixedWidth`– 遊戲都將遵循的高度值，傳遞至`SetDesignResolutionSize,`但會計算在執行階段根據實際的解析度的寬度。 如上所述，這表示顯示的寬度是小於或大於所需的寬度，所產生之遊戲的組件中關閉螢幕或多個顯示的分別遊戲。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下列螢幕擷取畫面在橫向模式中執行的應用程式建立，因為計算的寬度大於 500 – 特別 750。 此原則會保留 0 的 X 值靠左對齊，讓額外的解決方式是檢視螢幕的右側。

![](resolutions-images/image8.png "此原則會保存為 0 的 X 值靠左對齊，因此額外的解決方式是在螢幕的右側上檢視")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` 嘗試顯示沒有黑邊與應用程式，同時維持原始外觀比例 （不失真）。 如果要求的解析度比例符合裝置的實體的長寬比，會發生不裁剪。 如果不相符的長寬比，然後裁剪會發生。

程式碼範例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下列螢幕擷取畫面會顯示顯示剪輯，而會顯示所有 500 像素為單位的顯示寬度的上方和下方部分：

![](resolutions-images/image9.png "這個螢幕擷取畫面顯示頂端和底部的部分顯示剪輯，而會顯示所有 500 像素為單位的顯示寬度")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` 可讓每個`CCScene`來指定它自己自訂的檢視區相對於中所指定的解析度`SetDesignResolutionSize`。

場景定義其`Viewport`屬性以建構`CCViewport`，這又以建構`CCRect`。 如需有關`CCViewport`和`CCRect`看到[CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)。 建立的內容中`CCViewport``CCRect`建構函式的參數會指定 （依順序）：

 - x – 水平位移數量檢視區，其中每個單位都代表一整個螢幕的寬度。 例如，向右移動的螢幕寬度的一半場景中使用.5f 結果的值。
 - y – 要在檢視區，其中每個單位都代表一整個螢幕的高度的垂直位移的數量。 例如，向下移動的螢幕高度的一半場景中使用.5f 結果的值。
 - 寬度-場景應佔據的水平部分。 例如，使用值為 1 / 3.0f 導致場景水平佔用螢幕的三分之一。
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

上述程式碼會產生下列：

![](resolutions-images/image10.png "上述程式碼會產生這個螢幕擷取畫面")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio`可簡化在具有更高解析度螢幕裝置上使用更高解析度紋理。 具體而言，這個屬性可讓遊戲，而不需要使用更高解析度資產，來變更大小或位置的視覺項目。 

例如，遊戲可能包括封面資產遊戲的字元是 200 像素高，和 [遊戲] 畫面 (依指定`SetDesignResolutionSize`) 可能高度 400 像素。 在此情況下，字元會佔用螢幕的下半部。 不過，如果為 400 像素高的資產用於為更高解析度裝置的字元，字元會佔用顯示整個高度。 其目的是為了保留字元相同的大小，以善用較高解析度裝置，則某些額外的程式碼不需要保留字元精靈在螢幕的高度的一半。

上述的簡單範例代表遊戲開發 – 而不需要執行每個視覺項目，根據裝置解析度調整大小時，開發人員加入較大大小資產中常見的問題。 `DefaultTexelToContentSizeRatio` 調整大小的視覺項目使用的全域屬性。 這個值會調整特定類型的所有視覺項目所指派的值。

這個屬性會出現在下列類別： 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio for Mac 範本集`CCSprite.DefaultTexelToContentSizeRatio`根據裝置做為範例，如何使用它的寬度。 下列程式碼包含在`CCApplicationDelegate.ApplicationDidFinishLaunching`:


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

若要查看如何`DefaultTexelToContentSizeRatio`視覺效果的大小，將會影響項目，請考慮以上所顯示的程式碼：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

這會導致圖使用 500 x 500 紋理：

![](resolutions-images/image5.png "這會造成使用 500 x 500 紋理此映像")

依序執行實體解析度的 2048 x 1536 iPad。 這表示，如上方顯示遊戲會延展 500 像素為單位，透過 1536年實體像素。 遊戲可以利用這項額外解析藉由建立功能通常稱為*hd*資產 – 專為高解析度螢幕上執行的資產。 比方說，這場遊戲無法使用此 1000 x 1000 的大小調整的紋理 hd 版本。 不過，使用更大的影像會導致`CCSprite`具有 1000年單位的高度和寬度。 因為我們遊戲一律會顯示 500 單位 (因為`SetDesignResolutioSize`呼叫)，則我們 1000 x 1000 精靈會太大 （只有左下方的顯示）：

![](resolutions-images/image11.png "遊戲一律會顯示由於 SetDesignResolutioSize 呼叫 500 單位，因為 1000 x 1000 精靈會只左下方的顯示太大")

我們可以設定`CCSprite.DefaultTexelToContentSizeRatio`說明的是兩次為寬和高原始 500 x 500 紋理 1000 x 1000 紋理：


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

現在如果我們執行遊戲 1000 x 1000 紋理將會完整可見的：

![](resolutions-images/image12.png "現在如果我們執行遊戲 1000 x 1000 紋理是完全不可見")


### <a name="defaulttexeltocontentsizeratio-details"></a>DefaultTexelToContentSizeRatio 詳細資料

`DefaultTexelToContentSizeRatio`屬性是`static,`這表示應用程式中的所有小會共用相同的值。 遊戲資產對不同的解決方法的一般方法是包含一組完整的每個解決方式類別的資產。 根據預設 CocosSharp Visual Studio for Mac 範本提供**ld**和**hd**資產，可用於支援兩個集合的紋理的遊戲的資料夾。 具有內容的範例內容資料夾可能看起來像是：

![](resolutions-images/image13.png "具有內容的範例內容資料夾可能看起來像是")

請注意，預設範本也包含有條件地加入至應用程式的程式碼`ContentSearchPaths`:


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

這表示應用程式會根據是否以高解析度或一般的解析度模式執行路徑中檔案搜尋。 例如，如果**hd**和**ld**資料夾包含檔案，稱為**background.png**然後下列程式碼會執行，並選取正確的檔案進行解析：


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>總結

本文涵蓋如何建立正確顯示，不論裝置解析度的遊戲。 它會顯示不同的使用範例`CCSceneResolutionPolicy`調整大小的遊戲，根據裝置解析度的值。 它也提供如何的範例`DefaultTexelToContentSizeRatio`可用來容納多個內容集，而不需要個別調整視覺項目。

## <a name="related-links"></a>相關的連結

- [CocosSharp 簡介](~/graphics-games/cocossharp/index.md)
- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
