---
title: 分鏡腳本的簡介
description: 本文章提供在 Xamarin.Mac 應用程式中使用的分鏡腳本的簡介。 其中涵蓋使用分鏡腳本和 Xcode 的 Interface Builder 建立與維護應用程式的 UI。
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: c5d69e1d11fb59afe3cdcbe11391af7eced733b3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-storyboards"></a>分鏡腳本的簡介

_本文章提供在 Xamarin.Mac 應用程式中使用的分鏡腳本的簡介。其中涵蓋建立和維護應用程式的 UI 使用分鏡腳本和 Xcode 的介面產生器。_

分鏡腳本可讓您開發使用者介面為 Xamarin.Mac 應用程式不只會包含視窗定義和控制項，但也包含不同的視窗之間的連結 (透過 segues) 和檢視狀態。

[![](images/intro01.png "Ui 돨 튌 절 在 Xcode 中")](images/intro01.png#lightbox)

這篇文章會提供使用分鏡腳本定義 Xamarin.Mac 應用程式的使用者介面的簡介。

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>分鏡腳本有哪些？

藉由使用分鏡腳本，所有 Xamarin.Mac 應用程式的 UI 可以定義在單一位置與所有其個別項目與使用者介面之間導覽。 Xamarin.Mac 的分鏡腳本以分鏡腳本非常類似的方式用於 Xamarin.iOS。 不過，它們包含一組不同的_話題類型_因為不同的介面語言。

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>使用場景

如同前面所述，將分鏡腳本定義所有細分成多的功能概觀給定應用程式的使用者介面及其_檢視控制器_。 在 Xcode 的介面產生器中，每個這些控制站位於自己_場景_。

[![](images/intro02.png "此範例檢視控制器")](images/intro02.png#lightbox)

每個場景代表一組線條 （稱為 Segues），連接每個場景，在 UI 中，因此顯示其關聯性指定的檢視和檢視控制站組。 某些 Segues 定義如何檢視控制器包含一個以上的子檢視或檢視控制器。 其他 Segues，定義檢視控制器 （例如顯示 popover 或對話方塊方塊） 之間的轉換。 

[![](images/intro03.png "範例 segue")](images/intro03.png#lightbox)

最重要的附註是每個 Segue 代表某種形式的資料指定的應用程式的 UI 元素之間的流量。

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>使用檢視控制器

檢視控制站之間定義關聯性指定的檢視中的 Mac 應用程式的資訊和資料模型，以提供該資訊。 在腳本中的每個最上層場景代表 Xamarin.Mac 應用程式的程式碼中的一個檢視控制站。

[![](images/intro04.png "範例後還未結束檢視控制器")](images/intro04.png#lightbox)

如此一來，每個檢視的控制器是獨立的可重複使用的配對資訊的視覺表示法 （檢視） 和控制該資訊及呈現邏輯。

內給定的場景，您可以執行的動作會通常已處理個別的事情`.xib`檔案： 

 - Subviews 和控制 （例如按鈕和文字方塊）。
 - 定義項目位置及自動配置條件約束。
 - 連接動作和插座公開給程式碼的 UI 項目。

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>使用 Segues

如前所述，Segues 提供所有的場景，定義您的應用程式的 UI 之間的關聯性。 如果您很熟悉使用中 iOS 分鏡腳本，您已了解 Segues iOS 通常會定義全螢幕檢視之間的轉換。 這不同於 macOS，當 Segues 通常定義"containment"（其中一個場景是場景父系的子系）。

MacOS 中大部分的應用程式通常群組及其在相同的視窗中使用 UI 項目，例如分割檢視和索引標籤的檢視。 不同於 iOS，其中檢視需要開啟和關閉螢幕轉換，因為受限制的實體顯示空間。

指定內含項目優先 macOS 的勤者傾向，有很多情況下其中_簡報 Segues_使用，例如強制回應視窗、 檢視表和 Popovers。

當使用 Segues 簡報時，您可以覆寫`PrepareForSegue`初始化簡報和變數的父檢視控制器方法，並提供要呈現的檢視控制站的任何資料。

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>設計和執行時間

在設計階段 (當出 Xcode 的介面產生器中的 UI 配置)，應用程式的 UI 中的每個項目已細分成它的構成項目：

- **場景**，亦會組成：
    - **檢視控制器**-定義檢視和支援它們的資料之間的關聯性。
    - **檢視和 Subviews** -組成的使用者介面的實際項目。
    - **內含項目 Segues** -定義之間場景的父子式關聯性。
- **簡報 Segues** -定義個別的呈現模式。 

藉由定義每個項目，如此一來，它允許延遲載入的每個項目只視它是在執行階段。 在 macOS，整個程序設計用來讓開發人員建立需要的備份，使其工作，程式碼的最低限度的複雜、 有彈性的使用者介面時儘可能正在使用系統資源的效率。

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>分鏡腳本快速入門

在[分鏡腳本快速入門](~/mac/platform/storyboards/quickstart.md)指南中，我們將建立簡單的 Xamarin.Mac 應用程式，將會介紹使用腳本來建立使用者介面的重要概念。 範例應用程式將會包含一個濺檢視，其中包含_內容區域_和_Inspector 區域_並提供簡單的喜好設定 對話方塊視窗。 我們會將所有的使用者介面項目一起繫結用 Segues。

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>使用分鏡腳本

此章節將涵蓋的深入詳細資料[使用分鏡腳本](~/mac/platform/storyboards/indepth.md)Xamarin.Mac 應用程式中。 我們需要深入了解場景，以及其同時檢視控制器與檢視組成。 然後，我們看看場景 Segues 以及如何繫結。 最後，我們將探討使用自訂 Segue 型別。 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需使用分鏡腳本 Xamarin.Mac 應用程式中的複雜範例的範例，請參閱[SourceWriter 範例應用程式](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得快速查看在 Xamarin.Mac 應用程式中使用的分鏡腳本。 我們了解如何建立使用分鏡腳本的新應用程式以及如何定義使用者介面。 我們也可了解如何在不同的視窗之間巡覽和 segues 使用的檢視狀態。


## <a name="related-links"></a>相關連結

- [Hello, Mac (範例)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用視窗](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
