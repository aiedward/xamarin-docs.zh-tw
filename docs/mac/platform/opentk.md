---
title: Xamarin.mac OpenTK 簡介
description: 本文提供在 Xamarin.Mac 應用程式中使用的 OpenTK 簡介。 它涵蓋了建立與維護遊戲 視窗中，呈現簡單的物件並向使用者顯示的物件。
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 835b8cd0f2e689c4d7d4cace1d846543863b7393
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032657"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Xamarin.mac OpenTK 簡介

OpenTK （開啟工具組） 是一個進階的低層級 C# 程式庫，輕鬆使用 OpenGL、 OpenCL 和 OpenAL。 OpenTK 可以用於遊戲、 科學應用程式或其他專案需要 3D 圖形、 聲音或計算功能。 本文提供在 Xamarin.Mac 應用程式中使用 OpenTK 簡介。

[![](opentk-images/intro01.png "執行範例應用程式")](opentk-images/intro01.png#lightbox)

在本文中，我們將討論 OpenTK Xamarin.Mac 應用程式中的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="About_OpenTK" />

## <a name="about-opentk"></a>關於 OpenTK

如上所述，OpenTK （開啟工具組） 是進階的低層級 C# 程式庫，輕鬆使用 OpenGL、 OpenCL 和 OpenAL。 在 Xamarin.Mac 應用程式中使用 OpenTK 提供下列功能：

- **快速開發**-OpenTK 提供強大的資料類型和內嵌文件來改善您的程式碼撰寫工作流程，並攔截錯誤，更容易和更快。
- **輕鬆整合**-OpenTK 用意是要輕鬆地與.NET 應用程式整合。
- **許可授權**-OpenTK MIT/X11 授權下發佈，而是完全免費。
- **豐富、 型別安全繫結**-OpenTK 支援最新版的 OpenGL，OpenGL | ES、 OpenAL 和 OpenCL 擴充功能自動載入，錯誤檢查和內嵌的文件。
- **彈性的 GUI 選項**-OpenTK 提供原生、 高效能遊戲視窗專為遊戲和 Xamarin.Mac。
- **完全受控、 符合 CLS 標準的程式碼**-OpenTK 支援 32 位元和 64 位元版本的 macOS 沒有未受管理的程式庫。
- **3D 數學 Toolkit** OpenTK 提供`Vector`， `Matrix`，`Quaternion`和`Bezier`透過其 3D 的數學運算工具組的結構。

OpenTK 可以用於遊戲、 科學應用程式或其他專案需要 3D 圖形、 聲音或計算功能。

如需詳細資訊，請參閱[的開放工具組](http://www.opentk.com)網站。

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>OpenTK 快速入門

在 Xamarin.Mac 應用程式中使用 OpenTK 快速介紹，我們要建立簡單的應用程式，開啟 [遊戲] 檢視時，一個簡單的三角形，在該檢視] 和 [attachs 遊戲檢視呈現至 Mac 應用程式的主視窗，以向使用者顯示的三角形。

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>開始新的專案

啟動 Visual Studio for Mac，並建立新的 Xamarin.Mac 方案。 選取  **Mac** > **應用程式** > **一般** > **Cocoa 應用程式**:

[![](opentk-images/sample01.png "新增新的 Cocoa 應用程式")](opentk-images/sample01.png#lightbox)

請輸入`MacOpenTK`for**專案名稱**:

[![](opentk-images/sample02.png "設定專案名稱")](opentk-images/sample02.png#lightbox)

按一下 **建立**按鈕，即可建立新的專案。

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>包括 OpenTK

您可以開啟 TK 用於 Xamarin.Mac 應用程式之前，您需要包含 OpenTK 組件的參考。 在 **方案總管**，以滑鼠右鍵按一下**參考**資料夾，然後選取**編輯參考...**.

藉由勾選`OpenTK`，按一下  **確定**按鈕：

[![](opentk-images/sample03.png "編輯專案參考")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>使用 OpenTK

建立新專案的情況下，按兩下`MainWindow.cs`檔案中**方案總管 中**以開啟它進行編輯。 請`MainWindow`類別外觀如下所示：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

看一下這段程式碼的詳細說明如下。

<a name="Required_APIs" />

### <a name="required-apis"></a>必要的 Api

數個參考，才能使用 OpenTK Xamarin.Mac 類別中。 我們在定義的開頭包含下列`using`陳述式：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
這個最少必須使用 OpenTK 任何類別。

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>加入遊戲的檢視

接下來，我們需要建立遊戲的檢視，以包含所有 OpenTK 與我們互動，並顯示結果。 我們使用下列程式碼：

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

這裡我們做了遊戲檢視我們的主要 Mac 視窗相同的大小和視窗的 內容檢視取代新`MonoMacGameView`。 因為我們取代現有的視窗內容時，主要 Windows 調整大小時自動調整大小我們提供的檢視。

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>回應事件

有數個回應每個遊戲檢視的預設事件。 這一節將討論所需的主要事件。

<a name="The_Load_Event" />

### <a name="the-load-event"></a>載入事件

`Load`事件是從磁碟映像、 紋理或音樂等載入資源的位置。 如需我們的簡單測試應用程式中，我們不使用`Load`事件，但已加入它的參考：

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>調整大小事件

`Resize`應該呼叫事件，每次 [遊戲] 檢視會調整大小。 我們的範例應用程式，我們將進行 GL 檢視區 （這會是自動由 Mac 主視窗調整大小） 我們遊戲檢視相同的大小為下列程式碼：

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>UpdateFrame 事件

`UpdateFrame`事件用來處理使用者輸入資訊，請更新物件的位置、 執行的物理或 AI 計算。 如需我們的簡單測試應用程式中，我們不使用`UpdateFrame`事件，但已加入它的參考： 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> OpenTK Xamarin.Mac 實作不包含`Input API`，因此您必須使用 Apple 提供 Api，將鍵盤和滑鼠支援。 或者，您也可以建立的自訂執行個體`MonoMacGameView`，並覆寫`KeyDown`和`KeyUp`方法。

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>RenderFrame 事件

`RenderFrame`事件中包含的程式碼用來轉譯 （繪製），您的圖形。 我們的範例應用程式，我們會將 [遊戲] 檢視填入一個簡單的三角形： 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

轉譯程式碼通常會藉由呼叫的是`GL.Clear`以移除任何現有的項目之前繪製新的項目。

> [!IMPORTANT]
> Xamarin.Mac 版本的 OpenTK**不這麼做**呼叫`SwapBuffers`方法您`MonoMacGameView`結尾的轉譯程式的執行個體。 這樣會導致快速而不是顯示您的轉譯的檢視 strobe 遊戲檢視。

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>執行 [遊戲] 檢視

所有必要的事件定義和遊戲檢視附加至我們的應用程式的 Main Mac 視窗，我們會讀取執行 [遊戲] 檢視，並顯示我們的圖形。 請使用下列程式碼：

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

我們在理想畫面播放速率傳遞我們想要在更新的 [遊戲] 檢視中，我們的範例，我們已選擇`60`每個畫面格的第二個 （重新整理費率與相同標準電視）。

讓我們執行我們的應用程式，並查看輸出：

[![](opentk-images/intro01.png "應用程式輸出的範例")](opentk-images/intro01.png#lightbox)

如果我們調整大小視窗時，[遊戲] 檢視也會位於和三角形將會調整大小，並一併即時的更新。

<a name="Where_to_Next" />

### <a name="where-to-next"></a>下一步 在何處？

使用 OpenTk 在 Xamarin.mac 應用程式中完成的基本概念，以下是一些建議所要試試看 [下一步]:

- 請嘗試變更三角形的色彩和背景色彩 [遊戲] 檢視中的`Load`和`RenderFrame`事件。
- 請變更色彩，當使用者按下的索引鍵中的三角形`UpdateFrame`並`RenderFrame`事件或進行您自己的自訂`MonoMacGameView`類別並覆寫`KeyUp`和`KeyDown`方法。
- 請在使用中的了解索引鍵在畫面之間移動的三角形`UpdateFrame`事件。 提示： 使用`Matrix4.CreateTranslation`方法用來建立轉移矩陣並呼叫`GL.LoadMatrix`方法來載入在`RenderFrame`事件。
- 使用`for`迴圈來呈現數個三角形`RenderFrame`事件。
- 旋轉觀景窗來提供不同的檢視，在 3D 空間中的三角形。 提示： 使用`Matrix4.CreateTranslation`方法用來建立轉移矩陣並呼叫`GL.LoadMatrix`方法，以將其載入。 您也可以使用`Vector2`， `Vector3`，`Vector4`和`Matrix4`相機操作的類別。

如需其他範例，請參閱[OpenTK 範例 GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples)存放庫。 它包含使用 OpenTK 的範例的官方清單。 您必須調整 OpenTK Xamarin.Mac 版本搭配使用這些範例。

OpenTK 實作的更複雜的 Xamarin.Mac 範例，請參閱我們[MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)範例。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已在 Xamarin.Mac 應用程式中使用的 OpenTK 快速瀏覽。 我們了解如何建立遊戲 視窗中，如何將遊戲視窗連接至 Mac 視窗以及如何轉譯遊戲 視窗中的簡單圖形。

## <a name="related-links"></a>相關連結

- [MacOpenTK （範例）](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView （範例）](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [開啟的工具組](http://www.opentk.com)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
