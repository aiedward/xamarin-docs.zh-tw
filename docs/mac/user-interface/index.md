---
title: Xamarin.Mac 中的 macOS 使用者介面控制項
description: 描述各種使用者介面控制項 Xamarin.Mac 開發人員可使用的指南的這個文件連結。 連結的內容將探討 windows、 對話方塊、 警示、 功能表、 工具列、 資料表檢視、 大綱檢視等等。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: a12553cf0b7b9584bb8ff7bc04ed326ad4a7ad2a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107829"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>Xamarin.Mac 中的 macOS 使用者介面控制項

_本文會連結至說明各種 macOS UI 控制項的指南。_

當在 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取到相同的使用者介面控制項工作的開發人員*OBJECTIVE-C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的使用者介面 （或選擇直接在 C# 程式碼中建立它們）。

下列指南提供使用 macOS UI 項目，Xamarin.Mac 應用程式中的詳細的資訊。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中，因為它涵蓋重要概念和技術，我們將使用每篇文章中。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`用來連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 元素的屬性。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

這篇文章涵蓋使用 「 windows 」 和 「 在 Xamarin.Mac 應用程式中的面板。 它涵蓋了建立與維護 windows server 和在 Xcode 和 Interface Builder，載入 windows 中的面板及面板從.storyboard 或.xib 檔案、 使用 windows，並回應 「 視窗在 C# 程式碼。

## <a name="dialogsmacuser-interfacedialogmd"></a>[對話方塊](~/mac/user-interface/dialog.md)

本文涵蓋在 Xamarin.Mac 應用程式中的強制回應視窗與對話方塊。 它涵蓋了建立和維護在 Xcode 和 Interface Builder，使用標準的對話方塊，並顯示和回應 「 視窗在 C# 程式碼中的強制回應視窗。

## <a name="alertsmacuser-interfacealertmd"></a>[警示](~/mac/user-interface/alert.md)

本文涵蓋在 Xamarin.Mac 應用程式中的警示。 它涵蓋了建立和顯示警示，從 C# 程式碼和回應警示。

## <a name="menusmacuser-interfacemenumd"></a>[功能表](~/mac/user-interface/menu.md)

Mac 應用程式的使用者介面; 的各個部分中使用功能表從應用程式的主功能表頂端的螢幕快顯功能表和可以出現在視窗中的任何位置的內容功能表。 功能表是 Mac 應用程式使用者體驗不可或缺的一部分。 本文涵蓋使用 「 Cocoa 功能表在 Xamarin.Mac 應用程式。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[標準控制項](~/mac/user-interface/standard-controls.md)

使用標準的完整 AppKit 控制項，例如按鈕、 標籤、 文字欄位中，核取方塊和在 Xamarin.Mac 應用程式的分段的控制項。 本指南涵蓋將它們新增至 Xcode 的 Interface Builder 中的使用者介面設計、 將其公開到程式碼，透過輸出和動作，以及使用 C# 程式碼的完整 AppKit 控制項。

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具列](~/mac/user-interface/toolbar.md)

本文章涵蓋使用 「 在 Xamarin.Mac 應用程式中的工具列。 其中涵蓋建立與維護 「 工具列在 Xcode 和 Interface Builder 中的如何公開到程式碼使用輸出和動作、 啟用和停用工具列項目，以及最後在 C# 程式碼中的工具列項目回應的工具列項目。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[資料表檢視](~/mac/user-interface/table-view.md)

本文涵蓋在 Xamarin.Mac 應用程式中的資料表檢視。 其中涵蓋建立與維護 「 資料表檢視，在 Xcode 和 Interface Builder 中的如何公開 （expose） 資料表檢視項目使用輸出和動作的程式碼，填入資料表檢視 」，在 C# 程式碼中的資料表檢視項目回應。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大綱檢視](~/mac/user-interface/outline-view.md)

本文涵蓋在 Xamarin.Mac 應用程式中的大綱檢視。 其中涵蓋建立與維護 「 大綱檢視在 Xcode 和 Interface Builder 中的如何公開大綱檢視項目使用輸出和動作的程式碼，填入 「 大綱檢視 」，回應大綱檢視中 C# 程式碼項目。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[來源清單](~/mac/user-interface/source-list.md)

本文涵蓋在 Xamarin.Mac 應用程式中的來源清單。 它涵蓋了建立與維護 「 來源清單，在 Xcode 和 Interface Builder 中的如何公開到程式碼使用輸出和動作、 填入 「 來源清單，在 C# 程式碼中的來源清單項目回應的來源清單項目。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合檢視](~/mac/user-interface/collection-view.md)

本文涵蓋在 Xamarin.Mac 應用程式中的集合檢視使用。 其中涵蓋建立與維護 「 集合檢視，在 Xcode 和 Interface Builder 中的如何公開集合檢視項目使用輸出和動作的程式碼，填入 「 集合檢視，以及在 C# 程式碼中的集合檢視回應。

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[建立自訂控制項](~/mac/user-interface/custom-controls.md)

這篇文章說明如何建立自訂使用者介面控制項 (藉由繼承自`NSControl`)、 繪製控制項的自訂介面，以及建立可與 Xcode 的 Interface Builder 的自訂動作。

## <a name="mac-samples-gallery"></a>Mac 範例庫

我們也建議您看一下[Mac 範例庫](https://developer.xamarin.com/samples/mac/all/)。 它包含豐富的已準備好使用程式碼，可協助您快速開始 Xamarin.Mac 專案開始運作。

## <a name="related-links"></a>相關連結

- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
