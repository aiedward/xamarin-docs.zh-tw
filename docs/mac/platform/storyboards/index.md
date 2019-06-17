---
title: Xamarin.mac 的分鏡腳本簡介
description: 本文提供在 Xamarin.Mac 應用程式中使用的分鏡腳本的簡介。 其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 建立與維護應用程式的 UI。
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 166a50021c22aa09be3eecdb8b745a70e75c3d51
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031457"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Xamarin.mac 的分鏡腳本簡介

_本文提供在 Xamarin.Mac 應用程式中使用的分鏡腳本的簡介。它涵蓋了建立和維護應用程式的 UI 使用分鏡腳本和 Xcode 的 Interface Builder。_

分鏡腳本可讓您開發使用者介面，讓您不只包含 [定義] 視窗和控制項，但也包含不同的視窗之間的連結的 Xamarin.Mac 應用程式 (透過 segue) 和檢視狀態。

[![](images/intro01.png "Ui 돨 튌 절 在 Xcode 中")](images/intro01.png#lightbox)

這篇文章會提供定義 Xamarin.Mac 應用程式的使用者介面中使用分鏡腳本的簡介。

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>分鏡腳本有哪些？

使用分鏡腳本，所有的 Xamarin.Mac 應用程式的 UI 可以定義在單一位置與所有其個別項目和使用者介面之間導覽。 Xamarin.Mac 的分鏡腳本以分鏡腳本非常類似的方式用於 Xamarin.iOS。 不過，它們包含一組不同的_Segue 類型_因為不同的介面的慣用語。

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>使用 場景

如上方所述，分鏡腳本會定義所有指定的應用程式細分為功能的概觀的 UI 及其_檢視控制器_。 在 Xcode 的 Interface Builder，這些控制站的每個存在於自己_場景_。

[![](images/intro02.png "此範例中的檢視控制器")](images/intro02.png#lightbox)

每個場景均代表連接的線條 （稱為 Segue） 每個場景，在 UI 中，因此顯示其關聯性的一組指定的 「 檢視 」 和 「 檢視控制器配對。 某些 Segue 定義一個檢視控制器包含一或多個子檢視 」 或 「 檢視控制器。 其他的 Segue，定義 （例如顯示 popover 或對話方塊的方塊） 的檢視控制器之間的轉換。 

[![](images/intro03.png "範例 segue")](images/intro03.png#lightbox)

最值得注意的是每個 Segue 代表某種形式的應用程式的 UI 的指定項目之間的資料的流程。

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>使用檢視控制器

檢視控制器會定義指定的檢視的 Mac 應用程式中的資訊和提供該資訊的資料模型之間的關聯性。 分鏡腳本中的每個最上層場景均代表一個 Xamarin.Mac 應用程式的程式碼中的檢視控制器。

[![](images/intro04.png "範例下滑至檢視控制器")](images/intro04.png#lightbox)

如此一來，每個檢視控制器會是獨立的可重複使用的配對資訊的視覺表示法 （檢視） 和邏輯，以呈現，並控制這項資訊。

內給定的場景，您可以執行所有會通常已處理個別的項目`.xib`檔案： 

 - 位置 subviews，和控制項 （例如按鈕和文字方塊）。
 - 定義項目位置及自動版面配置條件約束。
 - 連接動作與出口公開到程式碼的 UI 項目。

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>使用 Segue

如上方所述，Segue 會提供所有的場景，定義您的應用程式 UI 之間的關聯性。 如果您已熟悉在分鏡腳本，在 iOS 中，您必須知道 Segue 的 iOS 通常定義全螢幕檢視之間的轉換。 這不同於 macOS Segue 通常會定義 「 內含項目 」 （其中一個場景是場景父系的子系） 時所示。

在 macOS 中大部分的應用程式通常會將他們的看法，一起在相同的視窗中使用 UI 元素，例如分割檢視和索引標籤。 不同於 iOS，其中檢視需要開啟和關閉畫面轉換，因為受限制的實體顯示空間。

指定內含項目針對 macOS 的傾向，有很多情況下所在_簡報 Segue_使用，例如強制回應的 Windows、 檢視表和 Popovers。

當使用 Segue 簡報時，您可以覆寫`PrepareForSegue`簡報中，以便初始化和變數的父檢視控制器方法和任何資料提供給正在呈現的檢視控制器。

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>設計和執行時間

在設計階段 (當在 Xcode 的 Interface Builder 中的 ui 的版面配置)，應用程式的 UI 的每個項目細分成它的構成項目：

- **場景**，亦是所組成：
    - **檢視控制器**-定義檢視和支援它們的資料之間的關聯性。
    - **檢視和子檢視**-構成使用者介面的實際項目。
    - **內含項目 Segue** -定義場景之間的父子式關聯性。
- **簡報 Segue** -定義個別的簡報模式。 

藉由定義每個項目，如此一來，它可讓消極式載入的每個項目只視它是在執行階段。 在 macOS，整個程序設計成可讓開發人員建立需要的備份程式碼，使其工作時，最低限度的複雜且彈性的使用者介面同時盡可能在效率與系統資源。

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>分鏡腳本的快速入門

在 [分鏡腳本快速入門](~/mac/platform/storyboards/quickstart.md)指南中，我們將建立簡單的 Xamarin.Mac 應用程式，將會介紹使用分鏡腳本建立的使用者介面的重要概念。 範例應用程式將會包含一個濺檢視，其中包含_內容區域_並_Inspector 區域_，它會顯示簡單的喜好設定 對話方塊視窗。 我們將使用 Segue 來結合所有的使用者介面項目。

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>使用 Storyboards

本節涵蓋的深入詳細資料[使用分鏡腳本](~/mac/platform/storyboards/indepth.md)Xamarin.Mac 應用程式中。 我們會深入探討場景，以及檢視控制器和檢視所組成。 然後，我們將探討如何場景會繫結以及 Segue。 最後，我們將看看如何使用 Segue 的自訂型別。 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需使用分鏡腳本的 Xamarin.Mac 應用程式中的複雜的範例的範例，請參閱[SourceWriter 範例應用程式](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已快速查看在 Xamarin.Mac 應用程式中使用的分鏡腳本。 我們看到如何建立新的應用程式使用分鏡腳本，以及如何定義使用者介面。 我們還瞭解到如何在不同的視窗之間瀏覽和檢視狀態使用 segue。


## <a name="related-links"></a>相關連結

- [Hello, Mac (範例)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
