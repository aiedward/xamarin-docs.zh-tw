---
title: 適用於 Objective-C 開發人員的 Xamarin
description: 如果您是 Objective-C 開發人員，您應該已經開始在 Xamarin 平台上運用自己的技巧和現有的 Objective-C 程式碼，並享受 C# 的程式碼重複使用優勢。 本節的目的是作為 Xamarin.iOS 的進入點，並指引您取得有關使用來自 C# 之現有 Objective-C 程式碼的豐富資訊。
ms.prod: xamarin
ms.assetid: 9F3C86A3-403E-4025-99CA-99FCA86DC828
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e29762fb258f7d796878c85bfe6f7aaa93207c5e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="xamarin-for-objective-c-developers"></a>適用於 Objective-C 開發人員的 Xamarin

_如果您是 Objective-C 開發人員，您應該已經開始在 Xamarin 平台上運用自己的技巧和現有的 Objective-C 程式碼，並享受 C# 的程式碼重複使用優勢。本節的目的是作為 Xamarin.iOS 的進入點，並指引您取得有關使用來自 C# 之現有 Objective-C 程式碼的豐富資訊。_

Xamarin 能提供以 iOS 為目標的開發人員將其非使用者介面程式碼移至平台無從驗證 C# 的路徑，使得該程式碼可用於任何可使用 C# 的地方，包括 Android (透過 Xamarin.Android) 及各種 Windows 版本。 不過，雖然您是搭配 Xamarin 使用 C#，這並不代表您不能運用自身既有的技巧及 Objective-C 程式碼。 事實上，了解 Objective-C 可讓您成為更佳的 Xamarin.iOS 開發人員，因為 Xamarin 會公開所有您熟悉且喜愛的原生 iOS 及 OS X 平台 API，例如 UIKit、Core Animation、Core Foundation 及 Core Graphics 等。 同時，您將能取得 C# 語言的強大功能 (包括 LINQ 及泛型等)，以及可用於原生應用程式的豐富 .NET 基底類別。

除此之外，Xamarin 可讓您透過名為繫結的技術，運用現有的 Objective-C 資產。 您只需以 Objective-C 建立靜態程式庫，然後將它透過繫結向 C# 公開，如下列圖表所示：

 [![](images/01-bindings.png "A 透過繫結向 C# 公開的 Objective-C 靜態程式庫")](images/01-bindings.png#lightbox)

這不僅只限制於非 UI 程式碼。 繫結也可以公開以 Objective-C 開發的使用者介面程式碼。

## <a name="transitioning-from-objective-c"></a>從 Objective-C 進行轉換

您可以在我們的文件網站找到可協助輕鬆轉換至 Xamarin 的豐富資訊，它們將能示範如何將 C# 程式碼與您既有的知識整合在一起。 可協助您開始的一些重點包括：

-   [適用於 Objective-C 開發人員的 C# 入門](primer.md)：適用於想要移至 Xamarin 和 C# 語言之 Objective-C 開發人員的入門。 
-   [逐步解說：對 Objective-C 程式庫進行繫結](~/ios/platform/binding-objective-c/walkthrough.md)：在 Xamarin.iOS 應用程式中重複使用現有 Objective-C 程式碼的逐步解說。 


## <a name="binding-objective-c"></a>繫結 Objective C

當您已了解 C# 與 Objective-C 之間的差異，並已完成上述的繫結逐步解說，您便已準備好轉換至 Xamarin 平台。 您接著可以在[繫結 Objective C](~/ios/platform/binding-objective-c/index.md) 一節中取得 Xamarin.iOS 繫結技術的詳細資訊，包括詳盡的繫結參考。

## <a name="cross-platform-development"></a>跨平台開發

最後，在移至 Xamarin.iOS 之後，您應該在[建置跨平台應用程式一節](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)中查看我們的跨平台指導方針，其中包括我們所開發之參考應用程式的案例研究，以及建立可重複使用之跨平台程式碼的最佳做法。
