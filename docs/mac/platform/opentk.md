---
title: Xamarin. Mac 中的 Opentk 簡介簡介
description: 本文提供在 Xamarin. Mac 應用程式中使用 Opentk 簡介的簡介。 其中涵蓋建立和維護遊戲視窗、呈現簡單的物件，並向使用者顯示物件。
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e2d459650c4e5ea38d5e54aef64cc3d7dcb5625c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029883"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Xamarin. Mac 中的 Opentk 簡介簡介

Opentk 簡介（開放工具組）是一種高階的高階連結C#庫，可讓您更輕鬆地使用 OpenGL、OpenCL 和 OpenAL。 Opentk 簡介可用於需要3D 圖形、音訊或計算功能的遊戲、科學應用程式或其他專案。 這篇文章提供在 Xamarin. Mac 應用程式中使用 Opentk 簡介的簡介。

[![](opentk-images/intro01.png "An example app run")](opentk-images/intro01.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中 Opentk 簡介的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要看一下[Xamarin 內部](~/mac/internals/how-it-works.md)檔的「 C# [公開C#類別/方法到目標-C](~/mac/internals/how-it-works.md) 」一節，它會說明用來將類別連線到目標-c 的`Register`和`Export`命令物件和 UI 元素。

<a name="About_OpenTK" />

## <a name="about-opentk"></a>關於 Opentk 簡介

如上面所述，Opentk 簡介（開放工具組）是一個先進的低層C#級程式庫，可讓您更輕鬆地使用 OpenGL、OpenCL 和 OpenAL。 在 Xamarin. Mac 應用程式中使用 Opentk 簡介可提供下列功能：

- **快速開發**-opentk 簡介提供強式資料類型和內嵌檔，以改善您的程式碼撰寫工作流程，並更輕鬆且更快速地攔截錯誤。
- **簡單整合**-opentk 簡介的設計可輕鬆地與 .net 應用程式整合。
- **寬鬆授權**opentk 簡介是以 MIT/X11 授權的散發，完全免費。
- **豐富的型別安全**系結-opentk 簡介支援最新版本的 OpenGL、OPENGL | ES、OpenAL 和 OpenCL，其中包含自動擴充功能載入、錯誤檢查和內嵌檔。
- **彈性的 GUI 選項**-opentk 簡介提供專為遊戲和 Xamarin 所設計的原生高效能遊戲視窗。
- **完全受控、符合 CLS 標準**的程式碼 opentk 簡介支援32位和64位版本的 macOS，而不含任何未受管理的程式庫。
- **3D 數學工具**組Opentk 簡介透過其3D 數學工具組，提供 `Vector`、`Matrix`、`Quaternion` 和 `Bezier` 結構。

Opentk 簡介可用於需要3D 圖形、音訊或計算功能的遊戲、科學應用程式或其他專案。

如需詳細資訊，請參閱[開放工具](http://www.opentk.com)組網站。

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Opentk 簡介快速入門

在 Xamarin. Mac 應用程式中使用 Opentk 簡介的快速簡介，我們將建立一個簡單的應用程式來開啟遊戲視圖、在該視圖中轉譯一個簡單的三角形，然後將遊戲視圖 attachs 到 Mac 應用程式的主視窗，以向使用者顯示三角形。

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>啟動新的專案

啟動 Visual Studio for Mac，並建立新的 Xamarin. Mac 解決方案。 選取**Mac** > **應用程式** > **一般** > **Cocoa 應用程式**：

[![](opentk-images/sample01.png "Adding a new Cocoa App")](opentk-images/sample01.png#lightbox)

針對 [**專案名稱**] 輸入 `MacOpenTK`：

[![](opentk-images/sample02.png "Setting the project name")](opentk-images/sample02.png#lightbox)

按一下 [**建立**] 按鈕以建立新的專案。

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>包括 Opentk 簡介

在 Xamarin. Mac 應用程式中使用 Open TK 之前，您必須包含 Opentk 簡介元件的參考。 在 **方案總管**中，以滑鼠右鍵按一下 **參考** 資料夾，然後選取 **編輯參考 ...** 。

勾選 [`OpenTK`]，然後按一下 [**確定]** 按鈕：

[![](opentk-images/sample03.png "Editing the project references")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>使用 Opentk 簡介

建立新的專案後，按兩下**方案總管**中的 `MainWindow.cs` 檔案，將它開啟以供編輯。 讓 `MainWindow` 類別看起來如下所示：

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

<a name="Required_APIs" />

### <a name="required-apis"></a>必要的 Api

需要有數個參考，才能在 Xamarin. Mac 類別中使用 Opentk 簡介。 在定義的開頭，我們包含了下列 `using` 語句：

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

使用 Opentk 簡介的任何類別都需要這個最小的集合。

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>新增遊戲視圖

接下來，我們需要建立遊戲視圖，以包含我們與 Opentk 簡介的所有互動，並顯示結果。 我們使用了下列程式碼：

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

我們在這裡讓遊戲視圖的大小與主要 Mac 視窗相同，並以新的 `MonoMacGameView`取代視窗的內容視圖。 因為我們取代了現有的視窗內容，所以當調整主視窗大小時，我們的提供視圖會自動調整大小。

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>回應事件

每個遊戲視圖應該回應的預設事件有好幾個。 本節將涵蓋所需的主要事件。

<a name="The_Load_Event" />

### <a name="the-load-event"></a>Load 事件

`Load` 事件是從磁片載入資源的位置，例如影像、材質或音樂。 針對我們的簡單測試應用程式，我們不會使用 `Load` 事件，但已包含它以供參考：

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>調整大小事件

每次調整遊戲視圖的大小時，都應該呼叫 `Resize` 事件。 針對我們的範例應用程式，我們使用下列程式碼，讓 GL 視口的大小與遊戲視圖相同（由 Mac 主視窗自動調整大小）：

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>UpdateFrame 事件

`UpdateFrame` 事件是用來處理使用者輸入、更新物件位置、執行物理或 AI 計算。 針對我們的簡單測試應用程式，我們不會使用 `UpdateFrame` 事件，但已包含它以供參考：

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> Opentk 簡介的 Xamarin. Mac 執行不包含 `Input API`，因此您必須使用 Apple 提供的 Api 來新增鍵盤和滑鼠支援。 （選擇性）您可以建立 `MonoMacGameView` 的自訂實例，並覆寫 `KeyDown` 和 `KeyUp` 方法。

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>RenderFrame 事件

`RenderFrame` 事件包含用來呈現（繪製）圖形的程式碼。 針對我們的範例應用程式，我們使用簡單的三角形來填滿遊戲視圖：

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

通常轉譯程式碼會呼叫 `GL.Clear`，以在繪製新專案之前移除任何現有的專案。

> [!IMPORTANT]
> 在 Opentk 簡介的 Xamarin 版本中，**不會**在您的轉譯程式碼結尾呼叫 `MonoMacGameView` 實例的 `SwapBuffers` 方法。 這麼做會使遊戲視圖快速地顯示，而不會顯示您的轉譯視圖。

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>執行遊戲視圖

當所有必要的事件都定義並將遊戲視圖附加至應用程式的主要 Mac 視窗後，我們就會閱讀執行遊戲視圖並顯示我們的圖形。 請使用下列程式碼：

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

我們會傳入想要讓遊戲視圖更新的所需畫面播放速率，我們在範例中選擇了每秒 `60` 的畫面數（與一般電視的重新整理頻率相同）。

讓我們執行應用程式，並查看輸出：

[![](opentk-images/intro01.png "A sample of the apps output")](opentk-images/intro01.png#lightbox)

如果我們調整視窗大小，遊戲視圖也會同時存在，而且三角形也會進行大小調整並即時更新。

<a name="Where_to_Next" />

### <a name="where-to-next"></a>下一步是什麼？

有了在 Xamarin. mac 應用程式中使用 Opentk 簡介的基本概念，以下是接下來要嘗試的一些建議：

- 請嘗試在 [`Load`] 和 [`RenderFrame`] 事件中變更三角形的色彩和遊戲視圖的背景色彩。
- 當使用者在 `UpdateFrame` 中按下按鍵，並 `RenderFrame` 事件，或建立您自己的自訂 `MonoMacGameView` 類別，並覆寫 `KeyUp` 和 `KeyDown` 方法時，請讓三角形變更色彩。
- 使用 `UpdateFrame` 事件中的感知鍵，讓三角形在畫面之間移動。 提示：使用 `Matrix4.CreateTranslation` 方法來建立轉譯矩陣，並呼叫 `GL.LoadMatrix` 方法，將它載入 `RenderFrame` 事件中。
- 使用 `for` 迴圈，在 `RenderFrame` 事件中呈現數個三角形。
- 旋轉相機，在3D 空間中為三角形提供不同的視圖。 提示：使用 `Matrix4.CreateTranslation` 方法來建立轉譯矩陣，並呼叫 `GL.LoadMatrix` 方法來載入它。 您也可以使用 [`Vector2`]、[`Vector3`]、[`Vector4`] 和 [`Matrix4`] 類別來進行相機操作。

如需更多範例，請參閱[Opentk 簡介範例 GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples)存放庫。 其中包含使用 Opentk 簡介的官方範例清單。 您必須調整這些範例，以便搭配 Opentk 簡介的 Xamarin 版本使用。

如需更複雜的 Xamarin. Mac Opentk 簡介範例，請參閱我們的[MonoMacGameView](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow)範例。

<a name="Summary" />

## <a name="summary"></a>總結

本文已快速探討如何在 Xamarin. Mac 應用程式中使用 Opentk 簡介。 我們已瞭解如何建立遊戲視窗、如何將遊戲視窗附加至 Mac 視窗，以及如何在遊戲視窗中轉譯簡單的圖形。

## <a name="related-links"></a>相關連結

- [MacOpenTK （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [開啟的工具組](http://www.opentk.com)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
