---
title: 程式設計 UrhoSharpF#
description: 本檔說明如何在 Visual Studio for Mac 中使用F#來建立簡單的 Hello world UrhoSharp 應用程式。
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: d87749bd74cf2c478e96284060fed7386d10b853
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69621010"
---
# <a name="programming-urhosharp-with-f"></a>使用 F 程式設計 UrhoSharp\#

UrhoSharp 可以F#使用程式C#設計人員所使用的相同程式庫和概念來進行程式設計。 [使用 UrhoSharp](~/graphics-games/urhosharp/using.md)一文提供 UrhoSharp 引擎的總覽, 而且應該在本文之前閱讀。

就像C++世界各地的許多程式庫一樣, 許多 UrhoSharp 函式會傳回布林值或整數, 指出成功或失敗。 您應該使用`|> ignore`來忽略這些值。

[範例程式](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)是從F#UrhoSharp 的「Hello World」。

## <a name="creating-an-empty-project"></a>建立空專案

尚未提供可用F#的 UrhoSharp 範本, 因此若要建立您自己的 UrhoSharp 專案, 您可以從[範例](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)開始, 或遵循下列步驟:

1. 從 Visual Studio for Mac 建立新的**方案**。 選擇 [ **iOS > 應用程式 > 單一視圖應用程式**], 然後選取**F#** 作為 [執行語言]。 
1. 刪除**主要**的分鏡腳本檔案。 開啟**plist**檔案, 然後在 [ **iPhone/iPod 部署資訊**] 窗格中, 刪除`Main` **主要介面**下拉式清單中的字串。
1. 也刪除**ViewController**檔案。

## <a name="building-hello-world-in-urho"></a>在 Urho 中建立 Hello World

您現在已準備好開始定義遊戲的類別。 您至少需要定義的子類別`Urho.Application` , 並覆寫其`Start`方法。 若要建立這個檔案, 請以滑鼠右鍵F#按一下您的專案, 選擇 [**加入新檔案 ...** ], F#然後將空的類別新增至您的專案。 新檔案將會新增至專案中的檔案清單結尾, 但您必須將它拖曳,*才*會在**AppDelegate**中使用它。

1. 新增 Urho NuGet 套件的參考。
1. 從現有的 Urho 專案中, 將 (大型) 目錄**CoreData/** 和**資料**複製到您專案的**資源/** 目錄。 在您F#的專案中, 以滑鼠右鍵按一下 [**資源**] 資料夾, 然後使用 [**新增/加入現有資料夾**] 將所有這些檔案新增至您的專案。

您的專案結構現在看起來應該像這樣:

![](fsharp-images/solutionpane.png "專案結構現在看起來應該像這樣")

將新建立的類別定義為的`Urho.Application`子類型, 並覆寫其`Start`方法:

```fsharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

程式碼非常簡單。 它會使用`Urho.Gui.Text`類別, 以特定字型和色彩大小來顯示置中對齊的字串。 

不過, 在此程式碼可以執行之前, 必須先初始化 UrhoSharp。 

開啟 AppDelegate 檔案並修改`FinishedLaunching`方法, 如下所示:

```fsharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

會`ApplicationOptions.Default`提供橫向模式應用程式的預設選項。 將這些`ApplicationOptions`傳遞給子`Application`類別的預設函式 (請注意, 當您`HelloWorld`定義類別時, `inherit Application(o)`這一行會呼叫基類的「函式」 (base class))。

的`Run` 方法`Application`會起始程式。 其定義為`int`傳回, 可以輸送至`ignore`。

產生的程式看起來應該像這樣的螢幕擷取畫面:

![產生之程式的螢幕擷取畫面](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>相關連結

- [流覽 GitHub (範例)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
