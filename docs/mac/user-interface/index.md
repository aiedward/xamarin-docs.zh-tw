---
title: 在 Xamarin 中 macOS 使用者介面控制項
description: 本檔連結的指南會說明適用于 Xamarin 開發人員的各種使用者介面控制項。 連結的內容會查看視窗、對話方塊、警示、功能表、工具列、資料表視圖、大綱視圖等等。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 7ae33883b83a4080903b524ea78e0da009ad0a3a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431520"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>在 Xamarin 中 macOS 使用者介面控制項

_本文將連結至描述各種 macOS UI 控制項的指南。_

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *c* 和 *Xcode* 中運作的相同使用者介面控制項。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的 _Interface Builder_ 來建立和維護您的使用者介面 (或選擇性地在 c # 程式碼) 中直接建立它們。

下列指南提供有關在 Xamarin. Mac 應用程式中使用 macOS UI 元素的詳細資訊。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，尤其是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在每篇文章中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，因為它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和屬性。

## <a name="windows"></a>[Windows](~/mac/user-interface/window.md)

本文說明如何在 Xamarin 應用程式中使用 windows 和麵板。 其中涵蓋了在 Xcode 和 Interface Builder 中建立和維護視窗和麵板、從分鏡腳本或 xib 檔案載入 windows 和麵板、使用 windows，以及在 c # 程式碼中回應 windows。

## <a name="dialogs"></a>[對話方塊](~/mac/user-interface/dialog.md)

本文說明如何在 Xamarin 應用程式中使用對話方塊和強制回應視窗。 其中涵蓋了在 Xcode 和 Interface Builder 中建立和維護強制回應視窗、使用標準對話，以及在 c # 程式碼中顯示和回應 windows。

## <a name="alerts"></a>[警示](~/mac/user-interface/alert.md)

本文說明如何在 Xamarin 應用程式中使用警示。 它涵蓋了如何從 c # 程式碼建立及顯示警示，以及回應警示。

## <a name="menus"></a>[功能表](~/mac/user-interface/menu.md)

功能表用於 Mac 應用程式使用者介面的各個部分;從畫面頂端的應用程式主功能表，到可顯示在視窗任何位置的快顯功能表和內容功能表。 功能表是 Mac 應用程式使用者體驗不可或缺的一部分。 本文說明如何在 Xamarin 應用程式中使用 Cocoa 功能表。

## <a name="standard-controls"></a>[標準控制項](~/mac/user-interface/standard-controls.md)

在 Xamarin 應用程式中使用標準 AppKit 控制項，例如按鈕、標籤、文字欄位、核取方塊和分段控制項。 本指南說明如何將它們新增至 Xcode Interface Builder 中的使用者介面設計、透過輸出和動作將它們公開給程式碼，以及在 c # 程式碼中使用 AppKit 控制項。

## <a name="toolbars"></a>[工具列](~/mac/user-interface/toolbar.md)

本文說明如何在 Xamarin 應用程式中使用工具列。 它涵蓋了如何在 Xcode 和 Interface Builder 中建立和維護工具列、如何使用「輸出」和「動作」將工具列專案公開到程式碼、啟用和停用工具列專案，最後回應 c # 程式碼中的工具列專案。

## <a name="table-views"></a>[資料表視圖](~/mac/user-interface/table-view.md)

本文說明如何在 Xamarin 應用程式中使用資料表視圖。 它涵蓋在 Xcode 和 Interface Builder 中建立和維護資料表視圖、如何使用輸出和動作將資料表視圖專案公開至程式碼、填入資料表視圖，以及在 c # 程式碼中回應資料表視圖專案。

## <a name="outline-views"></a>[大綱視圖](~/mac/user-interface/outline-view.md)

本文說明如何在 Xamarin 應用程式中使用大綱視圖。 它涵蓋在 Xcode 和 Interface Builder 中建立和維護大綱視圖、如何使用輸出和動作將大綱視圖專案公開到程式碼、填入大綱視圖，以及回應 c # 程式碼中的大綱視圖專案。

## <a name="source-lists"></a>[來源清單](~/mac/user-interface/source-list.md)

本文說明如何使用 Xamarin 應用程式中的來源清單。 它涵蓋了在 Xcode 和 Interface Builder 中建立和維護來源清單、如何使用輸出和動作將來源清單專案公開給程式碼、填入來源清單，以及在 c # 程式碼中回應來源清單專案。

## <a name="collection-views"></a>[集合視圖](~/mac/user-interface/collection-view.md)

本文說明如何在 Xamarin 應用程式中使用集合視圖。 它涵蓋在 Xcode 和 Interface Builder 中建立和維護收集視圖、如何使用輸出和動作將收集視圖專案公開到程式碼、填入集合視圖，以及回應 c # 程式碼中的集合視圖。

## <a name="creating-custom-controls"></a>[建立自訂控制項](~/mac/user-interface/custom-controls.md)

本文說明如何建立自訂使用者介面控制項， (繼承自 `NSControl`) 、繪製控制項的自訂介面，以及建立可與 Xcode 的 Interface Builder 搭配使用的自訂動作。

## <a name="mac-samples-gallery"></a>Mac 範例庫

我們也建議您查看 [Mac 範例資源庫](/samples/browse/?products=xamarin&term=Xamarin.Mac)。 它包含許多現成可用的程式碼，可協助您快速地將 Xamarin. Mac 專案離線。

## <a name="related-links"></a>相關連結

- [macOS Human Interface Guidelines (人性化介面指導方針)](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)