---
title: Xamarin 中的分鏡腳本簡介
description: 本文提供在 Xamarin. Mac 應用程式中使用分鏡腳本的簡介。 其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 建立與維護應用程式的 UI。
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: b27a8d65ebaca6009d8310931b9dac3a4d7e12f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026140"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Xamarin 中的分鏡腳本簡介

_本文提供在 Xamarin. Mac 應用程式中使用分鏡腳本的簡介。其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 來建立和維護應用程式的 UI。_

分鏡腳本可讓您開發 Xamarin. Mac 應用程式的使用者介面，不僅包含視窗定義和控制項，還包含不同視窗之間的連結（透過 segue）和檢視狀態。

[![](images/intro01.png "A sample UI in Xcode")](images/intro01.png#lightbox)

本文將提供使用分鏡腳本來定義 Xamarin 應用程式使用者介面的簡介。

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>什麼是分鏡腳本？

藉由使用分鏡腳本，您可以在單一位置定義 Xamarin. Mac 應用程式的 UI，並在其個別專案和使用者介面之間進行所有導覽。 Xamarin 的分鏡腳本，與 Xamarin 的分鏡腳本非常類似。 不過，由於不同的介面慣用語，它們包含一組不同的_Segue 類型_。

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>使用場景

如上所述，分鏡腳本會定義給定應用程式的所有 UI，並細分為其_視圖控制器_的功能總覽。 在 Xcode 的 Interface Builder 中，每個控制器都存在於自己的_場景_中。

[![](images/intro02.png "An example view controller")](images/intro02.png#lightbox)

每個場景都代表一個給定的視圖和視圖控制器配對，其中包含一組線（稱為 Segue），可連接 UI 中的每個場景，進而顯示其關聯性。 某些 Segue 會定義一個 View Controller 如何包含一個或多個子視圖或視圖控制器。 其他 Segue，定義 View Controller （例如顯示 popover 或對話方塊）之間的轉換。 

[![](images/intro03.png "A sample segue")](images/intro03.png#lightbox)

最重要的一點是要注意的是，每個 Segue 都代表應用程式 UI 的指定元素之間某種形式的資料流程程。

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>使用視圖控制器

視圖控制器會定義 Mac 應用程式內的特定資訊視圖與提供該資訊的資料模型之間的關聯性。 分鏡腳本中的每個最上層場景都代表 Xamarin 應用程式代碼中的一個 View Controller。

[![](images/intro04.png "An example slips view controller")](images/intro04.png#lightbox)

如此一來，每個 View Controller 都是獨立、可重複使用的資訊的視覺標記法（View）和邏輯，以呈現及控制該資訊。

在指定場景內，您可以執行所有通常已由個別 `.xib` 檔案處理的專案： 

- 放置子檢視和控制項（例如按鈕和文字方塊）。
- 定義元素位置和自動版面配置條件約束。
- 連線動作和輸出，以向程式碼公開 UI 元素。

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>使用 Segue

如上所述，Segue 提供定義應用程式 UI 的所有場景之間的關聯性。 如果您很熟悉如何在 iOS 中使用分鏡腳本，您知道 Segue for iOS 通常會定義全螢幕流覽之間的轉換。 這不同于 macOS，Segue 通常會定義「內含專案」（其中一個場景是父場景的子系）。

在 macOS 中，大部分的應用程式通常會使用分割視圖和索引標籤等 UI 元素，在同一個視窗內將其 views 群組在一起。 不同于 iOS，因為實體顯示空間有限，所以必須在畫面上轉換 views。

針對內含專案 macOS 的喜好傾向，在某些情況下會使用_展示 segue_ ，例如強制回應視窗、工作表視圖和 Popovers。

使用 [簡報 Segue] 時，您可以覆寫父視圖控制器的 `PrepareForSegue` 方法以進行展示，以初始化和變數，並將任何資料提供給呈現的視圖控制器。

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>設計和執行時間

在設計階段（配置 Xcode 的 Interface Builder 中的 UI 時），應用程式 UI 的每個元素會細分為其組成專案：

- **場景**-由下列組成：
  - **View Controller** -定義 Views 與支援它們的資料之間的關聯性。
  - **Views 和子檢視**-組成使用者介面的實際元素。
  - 內含專案**segue** -定義場景之間的父子式關聯性。
- **簡報 segue** -定義個別的呈現模式。 

藉由以這種方式定義每個專案，它允許每個專案的延遲載入，只有在執行時間時才需要。 在 macOS 中，整個程式的設計目的是要讓開發人員能夠建立複雜且彈性的使用者介面，只需要最少的支援程式碼，就能使其正常執行，同時也能以更有效率的方式使用系統資源。

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>分鏡腳本快速入門

在分鏡腳本[快速入門](~/mac/platform/storyboards/quickstart.md)指南中，我們將建立簡單的 Xamarin. Mac 應用程式，其仲介紹使用分鏡腳本建立使用者介面的重要概念。 範例應用程式將包含包含_內容區域_和偵測_器區域_的分成視圖，並會顯示一個簡單的 [喜好設定] 對話方塊視窗。 我們會使用 Segue 將所有的使用者介面元素結合在一起。

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>使用 Storyboards

本節涵蓋在 Xamarin. Mac 應用程式中使用分鏡[腳本的深入](~/mac/platform/storyboards/indepth.md)詳細資料。 我們會深入探討場景，以及它們如何由視圖控制器和視圖組成。 然後，我們將探討幕後如何與 Segue 系結在一起。 最後，我們將探討如何使用自訂的 Segue 類型。 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需在 Xamarin. Mac 應用程式中使用分鏡腳本的複雜範例範例，請參閱[SourceWriter 範例應用程式](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

<a name="Summary" />

## <a name="summary"></a>總結

本文已快速探討如何在 Xamarin. Mac 應用程式中使用分鏡腳本。 我們已瞭解如何使用分鏡腳本建立新的應用程式，以及如何定義使用者介面。 我們也看到了如何使用 segue 在不同的視窗和檢視狀態之間流覽。

## <a name="related-links"></a>相關連結

- [Hello, Mac (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/hello-mac)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
