---
title: macOS 使用者介面
description: 描述各種 macOS UI 控制項的指南的這個文件連結。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: d40faa29f2fe278377bf4eae42a032f3dc9086ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="macos-user-interface"></a>macOS 使用者介面

_描述各種 macOS UI 控制項的指南的這個文件連結。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同的使用者介面控制項，在工作開發人員*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的使用者介面 （或您可以選擇直接在 C# 程式碼中建立它們）。

下面所列的指南提供使用 macOS Xamarin.Mac 應用程式中的 UI 元素的詳細的資訊。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中，因為它涵蓋重要概念和技術，我們將使用每個發行項中。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`用來連線總 C# 類別 OBJECTIVE-C 物件和 UI 項目屬性。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

本文涵蓋了使用 windows 和 Xamarin.Mac 應用程式中的面板。 其中涵蓋建立與維護 windows 和 Xcode 和介面產生器中，載入 windows 中的面板以及面板從.storyboard 或.xib 檔案、 使用 windows 中，並回應在 C# 程式碼中的 windows。

## <a name="dialogsmacuser-interfacedialogmd"></a>[對話方塊](~/mac/user-interface/dialog.md)

本文件涵蓋使用對話方塊和 Xamarin.Mac 應用程式中的強制回應視窗。 其中涵蓋建立及維護安裝 Xcode 和介面產生器中，使用標準的對話方塊，並顯示和回應 windows C# 程式碼中的強制回應視窗。

## <a name="alertsmacuser-interfacealertmd"></a>[警示](~/mac/user-interface/alert.md)

本文件涵蓋使用 Xamarin.Mac 應用程式中的警示。 其中涵蓋建立和顯示 C# 程式碼產生的警示，以及對警示做出回應。

## <a name="menusmacuser-interfacemenumd"></a>[功能表](~/mac/user-interface/menu.md)

Mac 應用程式的使用者介面; 各個組件中使用功能表從應用程式的主功能表頂端的螢幕快顯功能表和可以出現在視窗中的任何位置的內容功能表。 功能表會 Mac 應用程式的使用者經驗中不可或缺的一部分。 本文件涵蓋使用 Cocoa Xamarin.Mac 應用程式中的功能表。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[標準控制項](~/mac/user-interface/standard-controls.md)

使用標準的 AppKit 控制項，例如按鈕、 標籤、 文字欄位中，核取方塊和分段的 Xamarin.Mac 應用程式中的控制項。 本指南涵蓋它們加入 Xcode 的介面產生器中的使用者介面設計、 將其公開給程式碼透過插座和動作，以及使用 AppKit C# 程式碼中的控制項。

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具列](~/mac/user-interface/toolbar.md)

本文涵蓋對 Xamarin.Mac 應用程式中的工具列。 建立及維護安裝 Xcode 和介面產生器中的工具列，其中涵蓋如何公開程式碼使用插座和動作、 啟用和停用工具列項目，以及最後回應工具列項目，在 C# 程式碼中的工具列項目。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[資料表檢視](~/mac/user-interface/table-view.md)

本文件涵蓋使用 Xamarin.Mac 應用程式中的資料表檢視。 建立及維護安裝 Xcode 和介面產生器中，在資料表檢視，其中涵蓋如何公開資料表檢視項目使用插座和動作的程式碼，請填入表格檢視，以及回應在 C# 程式碼中的資料表檢視項目。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大綱檢視](~/mac/user-interface/outline-view.md)

本文件涵蓋使用 Xamarin.Mac 應用程式中的大綱檢視。 建立和維護 Xcode 及介面產生器 中的大綱檢視，其中涵蓋如何公開大綱檢視項目使用插座和動作的程式碼，請填入大綱檢視，以及回應大綱檢視中 C# 程式碼項目。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[來源清單](~/mac/user-interface/source-list.md)

本文件涵蓋使用 Xamarin.Mac 應用程式中的來源清單。 建立和維護 Xcode 和介面產生器中的來源清單，其中涵蓋如何公開使用插座和動作、 擴展來源清單，以及 C# 程式碼中的來源清單項目回應的程式碼的來源清單項目。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合檢視](~/mac/user-interface/collection-view.md)

本文件涵蓋使用 Xamarin.Mac 應用程式中的集合檢視。 建立和維護 Xcode 及介面產生器 中的集合檢視，其中涵蓋如何公開集合檢視項目使用插座和動作的程式碼，請填入集合檢視，以及回應到 C# 程式碼中的集合檢視。

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[建立自訂控制項](~/mac/user-interface/custom-controls.md)

本文涵蓋建立自訂使用者介面控制項 (透過繼承自`NSControl`) 繪製控制項的自訂介面，建立適用於 Xcode 的介面產生器的自訂動作。

## <a name="mac-samples-gallery"></a>Mac 範例庫

我們也建議看一下[Mac 範例庫](https://developer.xamarin.com/samples/mac/all/)。 它包含了許多可協助您快速取得 Xamarin.Mac 專案踏出的已備妥要使用程式碼。

## <a name="related-links"></a>相關的連結

- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
