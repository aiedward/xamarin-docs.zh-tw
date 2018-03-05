---
title: "使用者介面"
description: "描述各種 macOS UI 控制項的指南的這個文件連結。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: a8cb9488849dafc2cd720ecf59d654009a9ad781
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="user-interface"></a>使用者介面

_描述各種 macOS UI 控制項的指南的這個文件連結。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同使用者介面控制項，工作的開發人員*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的使用者介面 （或您可以選擇直接在 C# 程式碼中建立它們）。 

下面所列的指南提供使用 macOS Xamarin.Mac 應用程式中的 UI 元素的詳細的資訊。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中，因為它涵蓋重要概念和技術，我們將使用每個發行項中。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

本文涵蓋 Windows 與面板 Xamarin.Mac 應用程式中的工作。 其中涵蓋建立及維護安裝 Xcode 和介面產生器中，載入 Windows 和從面板中的 Windows 和面板`.storyboard`或`.xib`使用 Windows 和 Windows 回應在 C# 程式碼中的檔案。

## <a name="dialogsmacuser-interfacedialogmd"></a>[對話方塊](~/mac/user-interface/dialog.md)

本文件涵蓋使用對話方塊和強制回應視窗 Xamarin.Mac 應用程式中。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「強制回應視窗」、使用標準對話方塊、在 C# 程式碼中顯示和回應「視窗」。

## <a name="alertsmacuser-interfacealertmd"></a>[警示](~/mac/user-interface/alert.md)

本文涵蓋了使用警示 Xamarin.Mac 應用程式中。 其中涵蓋從 C# 程式碼建立和顯示「警示」及回應「警示」。

## <a name="menusmacuser-interfacemenumd"></a>[功能表](~/mac/user-interface/menu.md)

Mac 應用程式的使用者介面; 各個組件中使用功能表從應用程式的主功能表頂端的螢幕快顯和關聯式功能表會出現在視窗中的任何位置。 功能表是 Mac 應用程式使用者經驗不可或缺的一部分。 本文涵蓋在 Xamarin.Mac 應用程式中使用「Cocoa 功能表」。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[標準控制項](~/mac/user-interface/standard-controls.md)

使用標準 AppKit 控制項例如按鈕、 標籤、 文字欄位中，核取方塊和分段控制項 Xamarin.Mac 應用程式中。 本指南涵蓋將它們新增至使用者介面的設計在 Xcode 的介面產生器中，將其公開給透過插座和動作的程式碼和使用 C# 程式碼 AppKit 控制項。

 
## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具列](~/mac/user-interface/toolbar.md)

本文涵蓋工具列 Xamarin.Mac 應用程式中的使用。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「工具列」、如何使用「輸出」和「動作」將「工具列項目」公開到程式碼、啟用和停用「工具列項目」，最後在 C# 程式碼中回應「工具列項目」。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[資料表檢視](~/mac/user-interface/table-view.md)

本文涵蓋 Xamarin.Mac 應用程式中使用的資料表檢視。 建立及維護安裝 Xcode 和介面產生器中的資料表檢視，其中涵蓋如何公開 （expose） 資料表檢視項目，程式碼使用插座和動作、 填入資料表檢視以及最後回應在 C# 程式碼中資料表的檢視項目。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大綱檢視](~/mac/user-interface/outline-view.md)

本文件涵蓋使用大綱檢視 Xamarin.Mac 應用程式中。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「大綱檢視」、如何使用「輸出」和「動作」將「大綱檢視項目」公開到程式碼、填入「大綱項目」，最後在 C# 程式碼中回應「大綱檢視項目」。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[來源清單](~/mac/user-interface/source-list.md)

本文涵蓋的來源清單 Xamarin.Mac 應用程式中的工作。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「來源清單」、如何使用「輸出」和「動作」將「來源清單項目」公開到程式碼、填入「來源清單項目」，最後在 C# 程式碼中回應「來源清單項目」。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合檢視](~/mac/user-interface/collection-view.md)

本文涵蓋 Xamarin.Mac 應用程式中的使用集合檢視。 其中涵蓋在 Xcode 和 Interface Builder 中建立與維護「集合檢視」、如何使用「輸出」和「動作」將「集合檢視」元素公開到程式碼、填入「集合檢視」，最後在 C# 程式碼中回應「集合檢視」。

## <a name="creating-custom-user-controlsmacuser-interfacecustom-controlsmd"></a>[建立自訂使用者控制項](~/mac/user-interface/custom-controls.md)

本文涵蓋建立自訂使用者介面控制項 (透過繼承自`NSControl`) 繪製控制項的自訂介面，建立適用於 Xcode 的介面產生器的自訂動作。

## <a name="mac-samples-gallery"></a>Mac 範例庫

我們也建議看一下[Mac 範例庫](http://developer.xamarin.com/samples/mac/all/)，它包含了許多可協助您快速取得 Xamarin.Mac 專案踏出的已備妥要使用程式碼。

## <a name="related-links"></a>相關連結

- [macOS 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
