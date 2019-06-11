---
title: 使用 MonoGame 管線工具
description: MonoGame 管線工具用來建立及管理 MonoGame 內容專案。 內容專案中的檔案會處理 MonoGame 管線工具，以及輸出為.xnb CocosSharp 和 MonoGame 應用程式中使用的檔案。
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 2227e2f56071b92c209c5d9fda994faa5a97a03d
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827767"
---
# <a name="using-the-monogame-pipeline-tool"></a>使用 MonoGame 管線工具

_MonoGame 管線工具用來建立及管理 MonoGame 內容專案。內容專案中的檔案會處理 MonoGame 管線工具，以及輸出為.xnb CocosSharp 和 MonoGame 應用程式中使用的檔案。_

MonoGame 管線工具提供簡單易用的環境轉換到的內容檔案 **.xnb** CocosSharp 和 MonoGame 的應用程式中使用的檔案。 如需內容的管線，以及為何遊戲開發人員很有用的資訊，請參閱[上內容的管線本簡介](~/graphics-games/cocossharp/content-pipeline/introduction.md)

本逐步解說將探討以下內容：

 - 安裝 MonoGame 管線工具
 - 建立 CocosSharp 專案
 - 建立內容的專案
 - 處理 MonoGame 管線工具中的檔案
 - 在執行階段使用的檔案

本逐步解說使用 CocosSharp 專案來示範如何 **.xnb**可載入和使用應用程式中的檔案。 MonoGame 的使用者也可以參考本逐步解說中，如 CocosSharp 和 MonoGame 都使用相同 **.xnb**內容檔。

完成的應用程式將會顯示單一顯示從材質的 sprite **.xnb**檔案和單一標籤，顯示從 sprite 字型 **.xnb**檔案：

![](walkthrough-images/image1.png "完成的應用程式將會顯示單一顯示.xnb 檔案從材質的 sprite")


## <a name="monogame-pipeline-tool-discussion"></a>MonoGame 管線工具討論

MonoGame 管線工具適用於 Windows、 OSX 和 Linux。 本逐步解說將在 Windows 中，執行此工具，但它可以跟著在 Mac 和 Linux 上也。 如需取得最大或 Linux 上設定此工具的資訊，請參閱[本頁](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/)。

MonoGame 管線工具都可以建立內容，針對 iOS 應用程式甚至當在執行 Windows，使用的是開發人員[Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md)將能夠繼續在 Windows 上進行開發。


## <a name="installing-the-monogame-pipeline-tool"></a>安裝 MonoGame 管線工具

我們將開始安裝 MonoGame，其中包括 MonoGame Content Pipeline。 請注意，MonoGame 內容管線個別下載 for mac。 所有 MonoGame 安裝程式都可於[MonoGame 下載頁面](http://www.monogame.net/downloads/)。 我們將下載 MonoGame for Visual Studio，但一旦安裝開發人員也可以使用 MonoGame 在 Visual Studio for Mac:

![](walkthrough-images/image2.png "Visual Studio 中，但一旦安裝開發人員使用 MonoGame 於 Visual Studio for Mac 太下載 MonoGame")

下載完成後，我們將執行安裝程式，並接受預設選項。 安裝程式完成之後，MonoGame 管線工具已安裝，並可在 [開始] 功能表搜尋：

![](walkthrough-images/image3.png "安裝程式完成之後，MonoGame 管線工具已安裝，並且可在 [開始] 功能表搜尋")

啟動 MonoGame 管線工具：

![](walkthrough-images/image4.png "啟動 MonoGame 管線工具")

當 MonoGame 管線工具執行時，我們可以開始讓我們的遊戲和內容專案。


## <a name="creating-an-empty-cocossharp-project"></a>建立空白的 CocosSharp 專案

下一個步驟是建立 CocosSharp 專案。 很重要，我們建立 CocosSharp 專案第一次，讓我們可以將我們內容的專案儲存在 CocosSharp 專案所建立的資料夾結構。 若要了解 CocosSharp 專案的結構，看看[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)，這將會使用本指南中。 不過，如果您有想要將內容加入至現有 CocosSharp 專案時，可以自由使用該專案，而不是 BouncingGame 的。

一旦建立專案之後，我們會執行它以確認它所建置，一切都正確設定：

![](walkthrough-images/image5.png "一旦建立專案之後，執行它來驗證它建置和，所有項目設定正確")


## <a name="creating-a-content-project"></a>建立內容的專案

既然我們已經使用遊戲專案，我們可以建立 MonoGame 管線專案。 若要這樣做，請在 MonoGame 管線工具選取**檔案 > 新增...** 並瀏覽至您的專案內容的資料夾。 若是 Android，資料夾是位於 **[專案 root]\BouncingGame.Android\Assets\Content\\** 。 適用於 iOS、 資料夾則位於 **[專案 root]\BouncingGame.iOS\Content\\** 。

變更**檔名**要**ContentProject**然後按一下**儲存**按鈕：

![](walkthrough-images/image8.png "將檔案名稱變更為 ContentProject，然後按一下 [儲存] 按鈕")

MonoGame 管線工具專案建立之後，會顯示專案的相關資訊時根目錄**ContentProject**選取項目：

![](walkthrough-images/image9.png "選取根 ContentProject 項目時，MonoGame 管線工具專案建立之後，會顯示專案的相關資訊")

讓我們看看部分內容的專案最重要的選項。


### <a name="output-folder"></a>輸出資料夾

這是其中的資料夾 （相對於內容專案本身） 的輸出 **.xnb**將儲存檔案。 為了簡單起見，我們將保留我們的輸入和輸出檔案使用相同的資料夾。 換句話說，我們將會變更**輸出資料夾**要 **。\\** :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>Platform

這會定義內容的目標平台。 請注意，這是**Windows**根據預設，因此我們會想要將它變更為我們的目標平台即**Android** （或如果密切注意 iOS 專案的 iOS）。

![](walkthrough-images/image11.png "請注意，這是預設 Windows，因此將它變更為目標平台為 Android 或 iOS，如果密切注意 iOS 專案")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>處理 MonoGame 管線工具中的檔案

接下來，我們會新增內容到我們**ContentProject**。 此專案中，我們會新增檔案至專案的根目錄，但較大型的專案通常會組織其資料夾中的內容。

我們會在我們的專案中新增兩個檔案：

 - A **.png**檔案將用來繪製 sprite。 這個檔案可以[從這裡下載](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)。
 - A **.spritefont**將用來在螢幕上繪製文字的檔案。 內容管線工具支援建立新的.spritefont 檔案，因此沒有下載檔案。


### <a name="adding-a-png-file"></a>新增.png 檔案

若要新增 **.png**檔案加入專案中，我們會先將它複製到與管線專案中，具有相同的目錄 **.mgcb**延伸模組。

![](walkthrough-images/image12.png ".Png 檔案加入專案")

接下來，我們會將檔案加入管線專案。 若要執行此動作 MonoGame 管線工具中，選取**編輯 > 加入項目...** ，選取**ball.png**檔案，然後按一下**開啟**。 檔案現在會是內容專案的一部分，並且選取時，會顯示其屬性：

![](walkthrough-images/image13.png "檔案會就會變成內容專案的一部分，並且選取時，會顯示其屬性")

我們會因為不不需要載入.xnb 檔案 CocosSharp 中的任何變更，所保留的所有值保留預設值。 我們可以建立檔案選取**建置 > 建置**功能表選項，將會啟動建置，並顯示有關組建的輸出。 我們可以確定建置正確運作方式是檢查**內容**新的資料夾**ball.xnb**檔案：

![](walkthrough-images/image14.png "確定建置正確運作方式是檢查新的 ball.xnb 檔案的內容資料夾")


### <a name="adding-a-spritefont-file"></a>加入.spritefont 檔案

我們可以建立透過 MonoGame 管線工具.spritefont 檔案。 CocosSharp 需要位於字型**字型**資料夾，然後 CocosSharp 範本自動建立自動字型資料夾。 我們可以將此資料夾加入 MonoGame 管線工具選取**編輯 > 新增 > 現有資料夾...** .瀏覽至**內容**資料夾，然後選取**字型**資料夾，然後按一下**確定**:

![](walkthrough-images/browsetofonts.png "瀏覽至 [Content] 資料夾並選取 [字型] 資料夾，然後按一下 [確定]")

若要加入新的.sprintefont 檔案，以滑鼠右鍵按一下 [字型] 資料夾，然後選取**新增 > 新增項目...** ，選取**SpriteFont 描述**選項，請輸入名稱**新細明體 36**，然後按一下**Ok**。 CocosSharp 需要非常特定的命名方式字型檔案-它們必須是格式為 [FontType]-[FontSize]。 如果字型不符合此命名格式，它將不會載入 CocosSharp 在執行階段。

![](walkthrough-images/image15.png "如果字型不符合此命名格式，它將不會載入 CocosSharp 在執行階段")

.Spritefont 檔案實際上是 XML 檔案可以編輯任何文字編輯器中，包括 Visual Studio for mac。 最常見的變數在.spritefont 檔中編輯`FontName`和`Size`屬性：


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

我們會以任何文字編輯器開啟檔案。 為我們**新細明體 36.spritefont**名所示，我們將保留`FontName`作為`Arial`但變更`Size`值`36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>在執行階段使用的檔案

.Xnb 檔案現在已內建，並準備好用於我們的專案。 我們會新增檔案至 Visual Studio for Mac，則我們將新增程式碼，以我們`GameScene.cs`載入這些檔案，並顯示它們的檔案。


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>.Xnb 檔案加入至 Visual Studio for Mac

首先我們會將檔案加入我們的專案。 在 Visual Studio for Mac 中，我們會依序展開**BouncingGame.Android**專案中，展開**資產**資料夾，以滑鼠右鍵按一下**內容**資料夾，然後選取**新增 > 新增檔案...** 首先，我們將在其中選取**ball.xnb**我們稍早建置，然後按一下**開啟**。 然後重複上述步驟，但新增**新細明體 36.xnb**檔案。 我們要選取**將檔案保留在其目前的子目錄**選項如果 Visual Studio for Mac 會詢問如何將檔案加入。 一旦完成這兩個檔案應該是我們的專案的一部分：

![](walkthrough-images/image20.png "一旦完成這兩個檔案應該是專案的一部分")


### <a name="adding-gamescenecs"></a>新增**GameScene.cs**

我們將建立一個名為`GameScene,`其中包含我們 sprite 和文字的物件。 若要這樣做，以滑鼠右鍵按一下**BouncingGame** (不 BouncingGame.Android) 專案，然後選取**新增 > 新增檔案...** .選取 **一般**類別目錄中，選取**空類別**選項，然後再輸入 名稱**GameScene**。

建立之後，我們要修改`GameScene.cs`檔案，以包含下列程式碼：


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

我們不會討論上述程式碼因為涵蓋使用 CocosSharp 的視覺物件，例如 CCSprite 和 CCLabelTtf [BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)。

我們也需要加入將我們新建立的程式碼`GameScene`。 若要這樣我們就會開啟`GameAppDelegate.cs`檔案 (位於**BouncingGame** PCL)，並修改`ApplicationDidFinishLaunching`方法，讓它看起來像：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

執行時，我們的遊戲看起來像：

![](walkthrough-images/image1.png "執行時，遊戲將會看起來像")


## <a name="summary"></a>總結

本逐步解說示範如何使用 MonoGame 管線工具來建立.xnb 檔案從輸入的.png 檔案，以及如何從新建立的.sprintefont 檔案建立新的.xnb 檔案。 此外，它也會討論如何建構使用.xnb 檔案 CocosSharp 專案以及如何將這些檔案在執行階段。

## <a name="related-links"></a>相關連結

- [MonoGame 下載](http://www.monogame.net/downloads/)
- [MonoGame 管線文件](http://www.monogame.net/documentation/?page=Pipeline)
- [啟動 BouncingGame 專案，適用於 Android （範例）](https://developer.xamarin.com/samples/monodroid/BouncingGameCompleteAndroid/)
- [ball.png 圖形 （範例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
