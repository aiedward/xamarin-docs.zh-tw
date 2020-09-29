---
title: 在 Xamarin 中 Opentk 簡介簡介
description: 本文提供在 Xamarin. Mac 應用程式中使用 Opentk 簡介的簡介。 它涵蓋了建立和維護遊戲視窗、轉譯簡單的物件，並向使用者顯示物件。
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7f600593d7a3bb180ef8daca6639dbbea4bf07aa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430071"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>在 Xamarin 中 Opentk 簡介簡介

Opentk 簡介 (開放工具組) 是一種先進的低層級 c # 程式庫，可讓您更輕鬆地使用 OpenGL、OpenCL 和 OpenAL。 Opentk 簡介可用於需要3D 圖形、音訊或計算功能的遊戲、科學應用程式或其他專案。 本文提供在 Xamarin 應用程式中使用 Opentk 簡介的簡介。

[![執行範例應用程式](opentk-images/intro01.png)](opentk-images/intro01.png#lightbox)

在本文中，我們將討論 Opentk 簡介在 Xamarin 應用程式中的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和命令。

<a name="About_OpenTK"></a>

## <a name="about-opentk"></a>關於 Opentk 簡介

如上所述，Opentk 簡介 (開放工具組) 是高階的低層級 c # 程式庫，可讓您更輕鬆地使用 OpenGL、OpenCL 和 OpenAL。 在 Xamarin 應用程式中使用 Opentk 簡介提供下列功能：

- **快速開發** -opentk 簡介提供強大的資料類型和內嵌檔，以改善您的程式碼撰寫工作流程，並更輕鬆且更快速地捕捉錯誤。
- **Easy Integration** -opentk 簡介的設計可輕鬆與 .net 應用程式整合。
- **寬鬆授權** -opentk 簡介是以 MIT/X11 授權散發，而且完全免費。
- **豐富的型別安全** 系結-opentk 簡介支援最新版本的 OpenGL、OPENGL | ES、OpenAL 和 OpenCL，以及自動擴充功能載入、錯誤檢查和內嵌檔。
- **彈性的 GUI 選項** -opentk 簡介提供原生、高效能的遊戲視窗，專為遊戲和 Xamarin 所設計。
- **完全受控、符合 CLS 標準** 的程式碼-opentk 簡介支援32位和64位版本的 macOS，而且沒有非受控程式庫。
- **3D 數學工具** 組Opentk 簡介透過 `Vector` `Matrix` `Quaternion` `Bezier` 其3d 數學工具組提供、和結構。

Opentk 簡介可用於需要3D 圖形、音訊或計算功能的遊戲、科學應用程式或其他專案。

如需詳細資訊，請參閱 [開放工具](https://opentk.net) 組網站。

<a name="OpenTK_Quickstart"></a>

## <a name="opentk-quickstart"></a>Opentk 簡介快速入門

在 Xamarin 應用程式中使用 Opentk 簡介的快速簡介中，我們將建立一個簡單的應用程式來開啟遊戲視圖、在該視圖中轉譯簡單的三角形，然後將遊戲視圖 attachs 至 Mac 應用程式的主視窗，以向使用者顯示三角形。

<a name="Starting_a_New_Project"></a>

### <a name="starting-a-new-project"></a>開始新專案

開始 Visual Studio for Mac 並建立新的 Xamarin. Mac 方案。 選取**Mac**  >  **應用程式**  >  **一般**  >  **Cocoa 應用程式**：

[![新增 Cocoa 應用程式](opentk-images/sample01.png)](opentk-images/sample01.png#lightbox)

輸入 `MacOpenTK` **專案名稱**：

[![設定專案名稱](opentk-images/sample02.png)](opentk-images/sample02.png#lightbox)

按一下 [ **建立** ] 按鈕以建立新專案。

<a name="Including_OpenTK"></a>

### <a name="including-opentk"></a>包含 Opentk 簡介

在 Xamarin 應用程式中使用 Open TK 之前，您必須先包含 Opentk 簡介元件的參考。 在 [ **方案總管**中，以滑鼠右鍵按一下 [ **參考** ] 資料夾，然後選取 [ **編輯參考 ...**]。

勾選 [ `OpenTK` 確定] 按鈕，然後按一下 [ **確定]** 按鈕：

[![編輯專案參考](opentk-images/sample03.png)](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK"></a>

### <a name="using-opentk"></a>使用 Opentk 簡介

建立新專案之後，按兩下方案總管中的檔案， `MainWindow.cs` 將它**Solution Explorer**開啟以供編輯。 讓 `MainWindow` 類別看起來像下面這樣：

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

我們將在下面詳細說明此程式碼。

<a name="Required_APIs"></a>

### <a name="required-apis"></a>必要的 Api

需要有數個參考，才能在 Xamarin. Mac 類別中使用 Opentk 簡介。 在定義的開頭，我們已包含下列 `using` 語句：

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

使用 Opentk 簡介的任何類別都需要這個基本設定。

<a name="Adding_the_Game_View"></a>

### <a name="adding-the-game-view"></a>新增遊戲視圖

接下來，我們需要建立一個遊戲視圖，以包含我們與 Opentk 簡介的所有互動，並顯示結果。 我們使用了下列程式碼：

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

在此，我們讓遊戲視圖的大小與主要 Mac 視窗相同，並將視窗的內容視圖取代為新的 `MonoMacGameView` 。 因為我們取代了現有的視窗內容，所以在調整主視窗大小時，會自動調整大小。

<a name="Responding_to_Events"></a>

### <a name="responding-to-events"></a>回應事件

每個遊戲視圖都應該回應數個預設事件。 本節將討論所需的主要事件。

<a name="The_Load_Event"></a>

### <a name="the-load-event"></a>Load 事件

此 `Load` 事件是從磁片（例如影像、材質或音樂）載入資源的位置。 針對我們的簡單測試應用程式，我們不會使用該 `Load` 事件，而是將它包含在參考中：

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event"></a>

### <a name="the-resize-event"></a>調整大小事件

`Resize`每次重新調整遊戲視圖的大小時，都應該呼叫事件。 在我們的範例應用程式中，我們會讓 GL 區的大小與我們的遊戲視圖相同， (使用下列程式碼，以 Mac 主視窗) 自動調整大小：

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event"></a>

### <a name="the-updateframe-event"></a>UpdateFrame 事件

`UpdateFrame`事件是用來處理使用者輸入、更新物件位置、執行物理或 AI 計算。 針對我們的簡單測試應用程式，我們不會使用該 `UpdateFrame` 事件，而是將它包含在參考中：

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> Opentk 簡介的 Xamarin 將不包含 `Input API` ，因此您必須使用 Apple 提供的 api 來新增鍵盤和滑鼠支援。 您可以選擇性地建立的自訂實例 `MonoMacGameView` ，並覆寫 `KeyDown` 和 `KeyUp` 方法。

<a name="The_RenderFrame_Event"></a>

### <a name="the-renderframe-event"></a>RenderFrame 事件

`RenderFrame`事件包含用來呈現圖形)  (繪製的程式碼。 在我們的範例應用程式中，我們使用簡單的三角形來填滿遊戲視圖：

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

通常轉譯程式程式碼會呼叫， `GL.Clear` 以移除任何現有的元素，然後再繪製新的專案。

> [!IMPORTANT]
> Opentk 簡介的 Xamarin 版本 **不會** `SwapBuffers` 在轉譯 `MonoMacGameView` 程式碼結束時呼叫實例的方法。 這樣做會讓遊戲視圖快速顯示，而不是顯示您的轉譯視圖。

<a name="Running_the_Game_View"></a>

### <a name="running-the-game-view"></a>執行遊戲視圖

當所有必要的事件都定義，並且將遊戲視圖附加到應用程式的主要 Mac 視窗時，我們就會讀到執行遊戲視圖，並顯示圖形。 使用下列程式碼：

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

我們會傳入希望遊戲視圖更新的所需畫面播放速率，在我們的範例中，我們選擇 `60` 每秒畫面格 (與一般電視) 相同的重新整理頻率。

讓我們執行應用程式並查看輸出：

[![應用程式輸出的範例](opentk-images/intro01.png)](opentk-images/intro01.png#lightbox)

如果我們調整視窗的大小，遊戲視圖也會存在，而三角形也會調整大小和更新。

<a name="Where_to_Next"></a>

### <a name="where-to-next"></a>接下來該怎麼做？

有了在 Xamarin 應用程式中使用 Opentk 簡介的基本概念，以下是接下來要嘗試的一些建議：

- 請嘗試在和事件中變更三角形的色彩和遊戲視圖的背景色彩 `Load` `RenderFrame` 。
- 當使用者按下和事件中的按鍵 `UpdateFrame` `RenderFrame` ，或是建立您自己的自訂 `MonoMacGameView` 類別並覆寫 `KeyUp` 和 `KeyDown` 方法時，請讓三角形變更色彩。
- 使用事件中的感知按鍵，讓三角形在螢幕上移動 `UpdateFrame` 。 提示：請使用 `Matrix4.CreateTranslation` 方法來建立轉譯矩陣，並呼叫 `GL.LoadMatrix` 方法以在事件中載入它 `RenderFrame` 。
- 使用 `for` 迴圈來呈現事件中的數個三角形 `RenderFrame` 。
- 旋轉相機以提供3D 空間中三角形的不同視圖。 提示：請使用 `Matrix4.CreateTranslation` 方法來建立轉譯矩陣，並呼叫 `GL.LoadMatrix` 方法來載入它。 您也可以使用 `Vector2` 、 `Vector3` `Vector4` 和 `Matrix4` 類別進行攝影機操作。

如需更多範例，請參閱 [Opentk 簡介範例 GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) 存放庫。 其中包含使用 Opentk 簡介之範例的官方清單。 您必須使用 Opentk 簡介的 Xamarin 版本，來調整這些範例。

如需更複雜的 Opentk 簡介實作為 Xamarin 範例，請參閱我們的 [MonoMacGameView](/samples/xamarin/mac-samples/monomacgamewindow) 範例。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文將快速瞭解如何在 Xamarin 應用程式中使用 Opentk 簡介。 我們已瞭解如何建立遊戲視窗、如何將遊戲視窗附加至 Mac 視窗，以及如何在遊戲視窗中呈現簡單的圖形。

## <a name="related-links"></a>相關連結

- [MacOpenTK (範例) ](/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView (範例) ](/samples/xamarin/mac-samples/monomacgamewindow)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [開啟的工具組](https://opentk.net)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)