---
title: 在 Xamarin. Mac 中 macOS 使用者介面控制項
description: 本檔連結的指南會描述 Xamarin. Mac 開發人員可使用的各種使用者介面控制項。 連結的內容會查看視窗、對話方塊、警示、功能表、工具列、表格視圖、大綱視圖等等。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: f1168378104fd40fa7de78297f1f683d0caa0afa
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283113"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>在 Xamarin. Mac 中 macOS 使用者介面控制項

_本文會連結描述各種 macOS UI 控制項的指南。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時，您可以存取開發人員在*目標-C*和*Xcode*中工作的相同使用者介面控制項。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來建立和維護使用者介面（或選擇直接在程式碼中C#建立）。

下列指南提供有關在 Xamarin. Mac 應用程式中使用 macOS UI 元素的詳細資訊。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在中使用的重要概念和技巧。每篇文章。

您可能想要查看[Xamarin. Mac 內部](~/mac/internals/how-it-works.md)檔`Register`的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c)一節，因為它會說明用來連接您C#的類別`Export`的和屬性。目標-C 物件和 UI 元素。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

本文說明如何在 Xamarin. Mac 應用程式中使用 windows 和麵板。 它涵蓋了如何在 Xcode 和 Interface Builder 中建立和維護 windows 和麵板、從分鏡腳本或 xib 檔案載入視窗和麵板、使用 windows，以及在C#程式碼中回應 windows。

## <a name="dialogsmacuser-interfacedialogmd"></a>[對話方塊](~/mac/user-interface/dialog.md)

本文涵蓋在 Xamarin. Mac 應用程式中使用對話方塊和強制回應視窗。 其中涵蓋了如何在 Xcode 和 Interface Builder 中建立和維護模式視窗、使用標準對話方塊，以及在程式碼中C#顯示和回應視窗。

## <a name="alertsmacuser-interfacealertmd"></a>[警示](~/mac/user-interface/alert.md)

本文說明如何在 Xamarin. Mac 應用程式中使用警示。 其中包含從C#程式碼建立和顯示警示，以及回應警示。

## <a name="menusmacuser-interfacemenumd"></a>[功能表](~/mac/user-interface/menu.md)

功能表用於 Mac 應用程式使用者介面的各個部分;從畫面頂端的應用程式主功能表，到可顯示在視窗任何位置的快顯功能表。 功能表是 Mac 應用程式使用者體驗不可或缺的一部分。 本文說明如何在 Xamarin. Mac 應用程式中使用 Cocoa 功能表。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[標準控制項](~/mac/user-interface/standard-controls.md)

使用標準 AppKit 控制項（例如按鈕、標籤、文字欄位、核取方塊，以及 Xamarin. Mac 應用程式中的分段控制項）。 本指南說明如何將這些專案新增至 Xcode Interface Builder 中的使用者介面設計、透過輸出和動作將其公開至程式碼，以及在C#程式碼中使用 AppKit 控制項。

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具列](~/mac/user-interface/toolbar.md)

本文說明如何在 Xamarin. Mac 應用程式中使用工具列。 它涵蓋了如何建立和維護 Xcode 和 Interface Builder 中的工具列，如何使用輸出和動作將工具列專案公開至程式碼、啟用和停用工具列專案，最後回應程式C#代碼中的工具列專案。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[資料表視圖](~/mac/user-interface/table-view.md)

本文說明如何在 Xamarin. Mac 應用程式中使用資料表的 views。 其中涵蓋在 Xcode 和 Interface Builder 中建立和維護資料表的視圖、如何使用輸出和動作將資料表視圖專案公開至程式碼、填入資料表視圖，以及回應程式碼中C#的資料表視圖專案。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大綱視圖](~/mac/user-interface/outline-view.md)

本文說明如何在 Xamarin. Mac 應用程式中使用大綱視圖。 其中涵蓋在 Xcode 和 Interface Builder 中建立和維護大綱視圖、如何使用輸出和動作將大綱視圖專案公開至程式碼、填入大綱視圖，以及回應程式碼中的C#大綱視圖專案。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[來源清單](~/mac/user-interface/source-list.md)

本文說明如何在 Xamarin. Mac 應用程式中使用來源清單。 它涵蓋了如何建立和維護 Xcode 和 Interface Builder 中的來源清單、如何使用輸出和動作將來源清單專案公開給程式碼、填入來源清單，以及回應程式C#代碼中的來源清單專案。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合視圖](~/mac/user-interface/collection-view.md)

本文說明如何在 Xamarin. Mac 應用程式中使用集合視圖。 其中涵蓋在 Xcode 和 Interface Builder 中建立和維護集合視圖、如何使用輸出和動作將集合視圖專案公開至程式碼、填入集合視圖，以及回應程式碼中C#的集合視圖。

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[建立自訂控制項](~/mac/user-interface/custom-controls.md)

本文說明如何建立自訂使用者介面控制項（藉由`NSControl`繼承）、繪製控制項的自訂介面，以及建立可與 Xcode 的 Interface Builder 搭配使用的自訂動作。

## <a name="mac-samples-gallery"></a>Mac 範例庫

我們也建議您看一下[Mac 範例庫](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)。 其中包含許多立即可用的程式碼，可協助您快速地將 Xamarin. Mac 專案離線。

## <a name="related-links"></a>相關連結

- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
