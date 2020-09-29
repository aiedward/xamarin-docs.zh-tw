---
title: Xamarin 中的分鏡腳本簡介
description: 本文提供在 Xamarin. Mac 應用程式中使用分鏡腳本的簡介。 其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 建立與維護應用程式的 UI。
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 24a1e1af5b56a3cd10557658a0cad0c4e73ebac8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433371"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Xamarin 中的分鏡腳本簡介

_本文提供在 Xamarin. Mac 應用程式中使用分鏡腳本的簡介。其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 來建立和維護應用程式的 UI。_

分鏡腳本可讓您開發 Xamarin 應用程式的消費者介面，而不只包含視窗定義和控制項，還包含不同 windows (透過 segue) 與檢視狀態之間的連結。

[![Xcode 中的範例 UI](images/intro01.png)](images/intro01.png#lightbox)

本文將提供使用分鏡腳本來定義 Xamarin 應用程式使用者介面的簡介。

<a name="What-are-Storyboards"></a>

## <a name="what-are-storyboards"></a>什麼是分鏡腳本？

使用分鏡腳本，所有的 Xamarin 應用程式 UI 都可以定義在單一位置，並在其個別專案和使用者介面之間進行所有導覽。 Xamarin 的分鏡腳本，與適用于 Xamarin 的分鏡腳本運作方式非常類似。 但因為不同的介面慣用語，所以它們包含一組不同的 _Segue 類型_ 。

<a name="Working-with-Scenes"></a>

### <a name="working-with-scenes"></a>使用場景

如上所述，分鏡腳本會將指定應用程式的所有 UI，定義細分為其 _視圖控制器_的功能總覽。 在 Xcode 的 Interface Builder 中，這些控制器都存在於自己的 _場景_中。

[![範例視圖控制器](images/intro02.png)](images/intro02.png#lightbox)

每個場景都代表一組指定的視圖和觀賞控制器配對， (稱為 Segue) ，可連接 UI 中的每個場景，進而顯示其關聯性。 某些 Segue 會定義一個 View 控制器包含一或多個子視圖或 View 控制器的方式。 其他 Segue，定義 View Controller (之間的轉換，例如顯示 popover 或對話方塊) 。 

[![範例 segue](images/intro03.png)](images/intro03.png#lightbox)

最重要的一點是，每個 Segue 代表應用程式 UI 的指定專案之間的某種形式的流程。

<a name="Working-with-View-Controllers"></a>

### <a name="working-with-view-controllers"></a>使用視圖控制器

視圖控制器會定義 Mac 應用程式內指定資訊視圖之間的關聯性，以及提供該資訊的資料模型之間的關聯性。 分鏡腳本中的每個最上層場景都代表 Xamarin 應用程式程式碼中的一個 View Controller。

[![範例進度的查看控制器](images/intro04.png)](images/intro04.png#lightbox)

如此一來，每個 View 控制器都是一種獨立、可重複使用的資訊視覺標記法 (View) 以及用來呈現和控制該資訊的邏輯。

在指定的場景內，您可以執行所有通常由個別檔案處理的專案 `.xib` ： 

- 將子檢視和控制項 (例如按鈕和文字方塊) 。
- 定義元素位置和自動設定條件約束。
- 連接動作和輸出，以將 UI 專案公開給程式碼。

<a name="Working-with-Segues"></a>

### <a name="working-with-segues"></a>使用 Segue

如上所述，Segue 提供定義應用程式 UI 的所有場景之間的關聯性。 如果您很熟悉如何在 iOS 的分鏡腳本中工作，您知道 Segue for iOS 通常會定義全螢幕視圖之間的轉換。 這與 macOS 的不同之處在于，Segue 通常會定義「內含專案」 (其中一個場景是父場景) 的子系。

在 macOS 中，大部分的應用程式通常會使用 UI 元素（例如分割視圖和索引標籤），在同一個視窗中將其查看群組在一起。 不同于 iOS，因為實體顯示空間有限，所以需要在畫面上轉換視圖。

假設 macOS 對內含專案的傾向，在某些情況下會使用 _展示 segue_ ，例如強制回應視窗、工作表視圖和 Popovers。

使用「展示 Segue」時，您可以覆寫 `PrepareForSegue` 父視圖控制器的方法，以初始化和變數，並將任何資料提供給呈現的視圖控制器。

<a name="Design-and-Run-Times"></a>

### <a name="design-and-run-times"></a>設計和執行時間

在設計階段 (當您在 Xcode 的 Interface Builder) 中配置 UI 時，應用程式 UI 的每個元素都會細分為其組成專案：

- **場景** -由下列各項組成：
  - **視圖控制器** -定義視圖之間的關聯性，以及支援它們的資料。
  - **Views 和子檢視** -組成使用者介面的實際元素。
  - 內含專案**segue** -定義場景之間的父子式關聯性。
- **簡報 segue** -定義個別的簡報模式。 

藉由以這種方式定義每個專案，它只允許每個元素的延遲載入，因為在執行時間中需要它。 在 macOS 中，整個程式的設計目的是為了讓開發人員能夠建立複雜且彈性的使用者介面，而這些介面需要最少的支援程式碼才能讓它們正常運作，而且可以盡可能有效率地使用系統資源。

<a name="Storyboard-Quick-Start"></a>

## <a name="storyboard-quick-start"></a>分鏡腳本快速入門

在腳本 [快速入門](~/mac/platform/storyboards/quickstart.md) 指南中，我們將建立一個簡單的 Xamarin. Mac 應用程式，以介紹使用分鏡腳本建立消費者介面的重要概念。 範例應用程式會包含包含 _內容區域_ 和偵測 _器區域_ 的分成視圖，而且會顯示簡單的 [喜好設定] 對話方塊視窗。 我們將使用 Segue 將所有的消費者介面元素結合在一起。

<a name="Working-with-Storyboards"></a>

## <a name="working-with-storyboards"></a>使用 Storyboards

本節涵蓋在 Xamarin 應用程式中使用分鏡 [腳本的深入](~/mac/platform/storyboards/indepth.md) 詳細資料。 我們將深入探討幕後的場景，以及它們如何由 View controller 和 View 組成。 然後，我們將探討幕後如何與 Segue 系結在一起。 最後，我們將探討如何使用自訂 Segue 類型。 

<a name="Complex-Storyboard-Example"></a>

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需在 Xamarin 應用程式中使用分鏡腳本的複雜範例範例，請參閱 [SourceWriter 範例應用程式](/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文將快速瞭解如何在 Xamarin 應用程式中使用分鏡腳本。 我們看到如何使用分鏡腳本建立新的應用程式，以及如何定義使用者介面。 我們也已瞭解如何使用 segue 在不同的視窗和檢視狀態之間流覽。

## <a name="related-links"></a>相關連結

- [Hello, Mac (範例)](/samples/xamarin/mac-samples/hello-mac)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)