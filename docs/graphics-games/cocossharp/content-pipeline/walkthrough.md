---
title: 使用 MonoGame PipelineTool
description: MonoGame 管線工具用來建立及管理 MonoGame 內容專案。 由 Monogame 管線工具處理並且輸出為.xnb CocosSharp 及 MonoGame 應用程式中使用的檔案內容的專案中的檔案。
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 50e6c611e285cde9184eed242353ad08b2a941ee
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="using-the-monogame-pipeline-tool"></a>使用 MonoGame 管線工具

_MonoGame 管線工具用來建立及管理 MonoGame 內容專案。由 Monogame 管線工具處理並且輸出為.xnb CocosSharp 及 MonoGame 應用程式中使用的檔案內容的專案中的檔案。_

MonoGame 管線工具提供方便使用環境轉換到的內容檔案的 **.xnb** CocosSharp 和 MonoGame 應用程式中使用的檔案。 如需內容的管線，以及為何有用遊戲開發資訊，請參閱[上內容的管線本簡介](~/graphics-games/cocossharp/content-pipeline/introduction.md)

本逐步解說涵蓋下列：

 - 安裝 MonoGame 管線工具
 - 建立 CocosSharp 專案
 - 建立內容的專案
 - 處理 MonoGame 管線工具中的檔案
 - 在執行階段使用的檔案

本逐步解說會使用 CocosSharp 專案示範如何 **.xnb**檔案可以載入和應用程式中使用。 MonoGame 的使用者也可以參考此逐步解說中，如 CocosSharp 和 MonoGame 都使用相同 **.xnb**內容檔案。

完成的應用程式將會顯示單一精靈顯示從紋理 **.xnb**檔案和單一標籤，顯示從精靈字型 **.xnb**檔案：

![](walkthrough-images/image1.png "完成的應用程式將會顯示單一精靈顯示紋理從.xnb 檔案")


## <a name="monogame-pipeline-tool-discussion"></a>討論 MonoGame 管線工具

Windows、 OS X 和 Linux 上可用 MonoGame 管線工具。 本逐步解說將在 Windows 中，執行此工具，但它後面 Mac 和 Linux 上也。 如需取得最大或 Linux 上設定此工具的資訊，請參閱[本頁](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/)。

MonoGame 管線工具是能夠建立內容，針對 iOS 應用程式即使當在 Windows 上執行，因此開發人員使用[Xamarin Mac 代理程式](~/ios/get-started/installation/windows/connecting-to-mac/index.md)將能夠繼續開發在 Windows 上。


## <a name="installing-the-monogame-pipeline-tool"></a>安裝 MonoGame 管線工具

我們將開始安裝 MonoGame，其中包括 MonoGame 內容管線。 請注意，MonoGame 內容管線個別下載 for mac。 所有 MonoGame 安裝程式可以都找到上[MonoGame 下載頁面](http://www.monogame.net/downloads/)。 我們將下載 MonoGame for Visual Studio，但一旦安裝開發人員也可以使用 MonoGame 在 Visual Studio for Mac:

![](walkthrough-images/image2.png "Visual Studio 中，但一旦安裝開發人員使用 MonoGame 在 Visual Studio for Mac 太下載 MonoGame")

在下載後，我們會透過安裝程式執行，並接受預設選項。 安裝程式完成之後，MonoGame 管線工具已安裝，且可以在 [開始] 功能表搜尋找到：

![](walkthrough-images/image3.png "安裝程式完成之後，MonoGame 管線工具已安裝，且可以在 [開始] 功能表搜尋找到")

啟動 MonoGame 管線工具：

![](walkthrough-images/image4.png "啟動 MonoGame 管線工具")

當 MonoGame 管線工具執行時，我們可以啟動，讓我們遊戲和內容的專案。


## <a name="creating-an-empty-cocossharp-project"></a>建立空 CocosSharp 專案

下一個步驟是建立 CocosSharp 專案。 請務必確認我們 CocosSharp 會先建立專案，讓我們可以將內容專案 CocosSharp 專案所建立的資料夾結構中。 若要了解 CocosSharp 專案的結構，看看[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)，這將會使用本指南中。 不過，如果您有想要將內容加入到現有 CocosSharp 專案，可以自由使用該專案，而不是 BouncingGame。

一旦建立專案之後，我們將執行它，並確定它建置，我們的所有項目已正確設定：

![](walkthrough-images/image5.png "一旦建立專案之後，執行它，確認它建置，並確認一切正確設定")


## <a name="creating-a-content-project"></a>建立內容的專案

現在，我們已經使用遊戲專案，我們可以建立 MonoGame 管線專案。 若要這樣做，請在 MonoGame 管線工具選取**檔案 > 新...** 並瀏覽至您的專案內容資料夾。 適用於 Android 資料夾位於 **[專案 root]\BouncingGame.Android\Assets\Content\**。 對於 iOS，資料夾位於 **[專案 root]\BouncingGame.iOS\Content\**。

變更**檔案名稱**至**ContentProject**按一下**儲存**按鈕：

![](walkthrough-images/image8.png "ContentProject 來變更檔案名稱，然後按一下 [儲存] 按鈕")

一旦建立專案時，MonoGame 管線工具會顯示專案的相關資訊時根**ContentProject**選取項目：

![](walkthrough-images/image9.png "選取根 ContentProject 項目時，MonoGame 管線工具一旦建立專案時，會顯示專案相關資訊")

讓我們看看一些最重要的選項為內容的專案。


### <a name="output-folder"></a>輸出資料夾

這是其中的資料夾 （相對於內容專案本身） 輸出 **.xnb**檔案會儲存。 為了簡單起見，我們會使用相同的資料夾，可保存我們輸入及輸出檔案。 換句話說，我們將會變更**輸出資料夾**是 **。\** :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>平台

這會定義內容的目標平台。 請注意，這是**Windows**根據預設，因此我們會想將它變更為我們的目標平台即**Android** （或如果遵循 iOS 專案以及 iOS）。

![](walkthrough-images/image11.png "請注意，這是預設 Windows 中，因此將它變更為目標平台為 Android 或 iOS，如果下列以及 iOS 專案")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>處理 MonoGame 管線工具中的檔案

接下來，我們將持續加入內容至我們**ContentProject**。 此專案中，我們將會將檔案加入至專案的根目錄，但較大型的專案通常會組織在資料夾及其內容。

我們會將兩個檔案加入專案：

 - A **.png**檔案會用來繪製精靈。 這個檔案可以[這裡下載](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)。
 - A **.spritefont**將用來在螢幕上繪製文字的檔案。 此內容的管線工具支援建立新的.spritefont 檔案，因此沒有要下載的檔案。


### <a name="adding-a-png-file"></a>若要加入.png 檔案

若要加入 **.png**檔案加入專案中，我們會先將它複製到與管線專案中，具有相同的目錄 **.mgcb**延伸模組。

![](walkthrough-images/image12.png ".Png 檔案加入專案")

接下來，我們會將檔案新增至管線專案。 若要執行此動作 MonoGame 管線工具中，選取**編輯 > 加入項目...**，選取**ball.png**檔案，然後按一下 **開啟**。 檔案現在會內容專案的一部分，並且選取時，會顯示其屬性：

![](walkthrough-images/image13.png "檔案會現在會內容專案的一部分，並且選取時，會顯示其屬性")

我們會因為不需要載入中 CocosSharp.xnb 檔案進行變更，正在離開所有值在其預設值。 我們可以建立檔案選取**建置 > 建置**功能表選項，這會啟動建置，並顯示有關組建輸出。 我們可以確認組建正常運作方式是檢查**內容**新資料夾**ball.xnb**檔案：

![](walkthrough-images/image14.png "請確認組建檢查新 ball.xnb 檔案內容資料夾正常運作")


### <a name="adding-a-spritefont-file"></a>若要加入.spritefont 檔案

我們可以建立透過 MonoGame 管線工具.spritefont 檔案。 CocosSharp 需要處於字型**字型**資料夾，然後自動建立自動字型資料夾 CocosSharp 範本。 我們可以將此資料夾加入 MonoGame 管線工具選取**編輯 > 新增 > 現有資料夾...**.瀏覽至**內容**資料夾，然後選取**字型**資料夾，然後按一下**確定**:

![](walkthrough-images/browsetofonts.png "瀏覽至內容的資料夾並選取 [字型] 資料夾，然後按一下 [確定]")

若要加入新的.sprintefont 檔案，以滑鼠右鍵按一下 [字型] 資料夾，然後選取**新增 > 新的項目...**，選取**SpriteFont 描述**選項，請輸入名稱**新細明體 36**，然後按一下**確定**。 CocosSharp 需要非常特定的命名方式字型檔-它們必須是格式為 [FontType]-[FontSize]。 如果字型不符合此命名格式，它將不會載入 CocosSharp 在執行階段。

![](walkthrough-images/image15.png "如果字型不符合此命名格式，它將不會載入 CocosSharp 在執行階段")

.Spritefont 檔案是實際的 XML 檔案的可編輯任何文字編輯器中，包括 Visual Studio for mac。 編輯.spritefont 檔案中的最常見變數包括`FontName`和`Size`屬性：


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

我們會以任何文字編輯器開啟檔案。 做為我們**新細明體 36.spritefont**名所示，我們會將保留`FontName`為`Arial`但變更`Size`值設定為`36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>在執行階段使用的檔案

.Xnb 檔案現在建置，並準備好使用我們的受測專案。 我們將持續加入檔案至 Visual Studio for Mac，則我們會將程式碼加入我們`GameScene.cs`載入這些檔案和顯示這些檔案。


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>.Xnb 將檔案加入至 Visual Studio for Mac

首先我們會將檔案加入專案。 在 Visual Studio for Mac，我們會依序展開**BouncingGame.Android**專案中，展開 **資產**資料夾中，以滑鼠右鍵按一下**內容**資料夾，然後選取**新增 > 新增檔案...** 首先，我們將在其中選取**ball.xnb**我們稍早建立及按一下**開啟**。 然後重複上述步驟，但加入**新細明體 36.xnb**檔案。 我們將選取**保留其目前的子目錄中的檔案**選項如果 Visual Studio for Mac 詢問如何將檔案加入。 一旦完成這兩個檔案應該是受測專案的一部分：

![](walkthrough-images/image20.png "一旦完成這兩個檔案應該是專案的一部分")


### <a name="adding-gamescenecs"></a>加入**GameScene.cs**

我們將建立一種類別稱為`GameScene,`其中包含我們精靈 」 和 「 文字 」 物件。 若要這樣做，以滑鼠右鍵按一下**BouncingGame** (不 BouncingGame.Android) 專案，然後選取**新增 > 新的檔案...**.選取**一般**類別目錄中，選取**空類別**選項，然後再輸入 名稱**GameScene**。

一旦建立之後，我們會修改`GameScene.cs`檔案以包含下列程式碼：


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

我們將不會討論上述程式碼後使用像是 CCSprite 和 CCLabelTtf CocosSharp 視覺物件在講述[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)。

我們也要加入的程式碼載入我們新建`GameScene`。 若要這樣做會開啟 我們`GameAppDelegate.cs`檔案 (位於**BouncingGame** PCL) 和修改`ApplicationDidFinishLaunching`方法，使它看起來像：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

執行時，我們遊戲看起來像：

![](walkthrough-images/image1.png "遊戲執行時，看起來像")


## <a name="summary"></a>總結

本逐步解說示範如何使用 MonoGame 管線工具來建立.xnb 檔案從輸入的.png 檔案，以及如何從新建立的.sprintefont 檔案建立新的.xnb 檔案。 它也將討論如何建構 CocosSharp 專案以使用.xnb 檔案以及如何將這些檔案在執行階段載入。

## <a name="related-links"></a>相關的連結

- [MonoGame 下載](http://www.monogame.net/downloads/)
- [MonoGame 管線文件](http://www.monogame.net/documentation/?page=Pipeline)
- [啟動 BouncingGame 專案 for Android （範例）](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [ball.png 圖形 （範例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
