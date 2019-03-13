---
title: '使用 F # 進行 UrhoSharp 程式設計'
description: '本文件說明如何建立簡單的 hello world UrhoSharp 應用程式使用 F # 在 Visual Studio for mac。'
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 99e8f8bf04465d0d61086139ba9889eae141207e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123091"
---
# <a name="programming-urhosharp-with-f"></a>使用 F # 進行 UrhoSharp 程式設計

UrhoSharp 可以編寫使用 F # 使用相同的程式庫和 C# 程式設計人員所使用的概念。 [使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文章概述 UrhoSharp 引擎，並應該在這篇文章之前閱讀。

在 c + + 世界裡產生的許多程式庫，例如許多 UrhoSharp 函式會傳回布林值或整數，指出成功或失敗。 您應該使用`|> ignore`要略過這些值。

[範例程式](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)UrhoSharp 從 F # 中為"Hello World"。

## <a name="creating-an-empty-project"></a>建立空專案

UrhoSharp 沒有 F # 範本，但您可以使用開始可用的因此，若要建立您自己的 UrhoSharp 專案[範例](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)或遵循下列步驟：

1. 從 Visual Studio for Mac 中，建立新**解決方案**。 選擇**iOS > 應用程式 > 單一檢視應用程式**，然後選取**F #** 做為實作語言。 
1. 刪除**Main.storyboard**檔案。 開啟**Info.plist**檔案並在**iPhone / iPod 部署資訊**窗格中，刪除`Main`字串**主要介面**下拉式清單。
1. 刪除**ViewController.fs**檔案。

## <a name="building-hello-world-in-urho"></a>Urho 建置 Hello World

您現在已準備好開始定義您的遊戲類別。 至少，您必須定義的子類別`Urho.Application`，並覆寫其`Start`方法。 若要建立此檔案，以滑鼠右鍵按一下您的 F # 專案中，選擇**新增新的檔案...** 並將空的 F # 類別新增至您的專案。 新的檔案會新增至您的專案中的檔案清單的結尾，但您必須將它拖曳，使其出現*之前*它使用於**AppDelegate.fs**。

1. 加入 Urho NuGet 套件的參考。
1. 從現有 Urho 專案中，複製 （大） 的目錄**CoreData /** 並**資料 /** 至您的專案**資源 /** 目錄。 在 F # 專案中，以滑鼠右鍵按一下**資源**資料夾，然後使用**Add / 新增現有資料夾**將所有這些檔案新增至您的專案。

您的專案結構現在看起來應該類似：

![](fsharp-images/solutionpane.png "專案結構現在看起來應該像")

您新建立的類別定義為的子型別`Urho.Application`，並覆寫其`Start`方法：

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

程式碼是非常簡單。 它會使用`Urho.Gui.Text`類別，以顯示與特定字型和色彩大小置中對齊的字串。 

執行此程式碼之前，不過，必須先初始化 UrhoSharp。 

開啟並修改 AppDelegate.fs 檔案`FinishedLaunching`方法，如下所示：

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

`ApplicationOptions.Default`提供橫向模式應用程式的預設選項。 傳遞這些`ApplicationOptions`的預設建構函式您`Application`子類別 (請注意，當您定義`HelloWorld`類別，行`inherit Application(o)`呼叫基底類別建構函式)。 

`Run`方法的程式`Application`啟動程式。 它定義為傳回`int`，這可以輸送到`ignore`。 

產生的程式看起來應該像：

![](fsharp-images/helloworldfsharp.png "產生的程式應該看起來像")








## <a name="related-links"></a>相關連結

- [瀏覽 GitHub （範例）](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
