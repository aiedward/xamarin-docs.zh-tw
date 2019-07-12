---
title: 檢查即時應用程式
description: 本文件說明如何使用 Xamarin Inspector 檢查應用程式。 它也會討論 Xamarin Inspector 工具的限制。
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2def0a01bdd28af5eefb76afc19a0e49fd1df355
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831557"
---
# <a name="inspecting-live-applications"></a>檢查即時應用程式

使用適用於企業客戶的即時應用程式檢查。

1. 開啟任何[支援應用程式專案](~/tools/inspector/install.md#supported-platforms)Visual Studio for Mac 或 Visual Studio 中。
1. 以偵錯模式執行您的應用程式。
1. 按一下 [**檢查**IDE 工具列中的按鈕 (在 Visual Studio 中，**檢查目前的應用程式。]** 功能表項目也會提供**工具**或是**偵錯**功能表)。

[![](inspect-images/mac-heres-the-button.png "按一下 [檢查] 按鈕，在 IDE 工具列")](inspect-images/mac-heres-the-button.png#lightbox)

新的 Xamarin Inspector 用戶端視窗隨即開啟，以全新的 REPL 提示字元。

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "新的 Xamarin Inspector 用戶端視窗隨即開啟，以全新的 REPL 提示字元")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

此視窗出現時，您有互動式C#提示字元中，您可以使用它來執行，並評估C#陳述式及運算式。 特點這就是程式碼會評估目標處理序的內容中。 在此情況下，我們會顯示針對顯示的 iOS 應用程式執行的程式碼。

您對應用程式狀態的任何變更會闡述它在目標處理序中，因此您可以使用C#若要變更即時應用程式，或您可以檢查即時應用程式的狀態。

例如，在 iOS 上，我們可能想要找出我們 uiapplication # 委派類別，也就是我們 （我們儲存大量的應用程式狀態） 的主要驅動程式：

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

（請注意，每次送出，就會發生在多行編輯器中。 `Shift + Enter` 會建立新的一行，並`Cmd + Enter`(`Ctrl + Enter`在 Windows 上) 會提交評估的程式碼。 `Enter` 自動提交安全時。）

移至您的應用程式的視覺化項目更方便的方式是使用 「 檢查 」 按鈕。 一旦您按下此按鍵，您可以選取 UI 項目，按一下您的應用程式。 變數`selectedView`會指派給指向在螢幕上的實際項目。 在上面的螢幕擷取畫面，您可以看到我們如何存取，然後編輯`selectedView.BarTintColor`上`UISearchBar`我們已選取。

即時視覺化樹狀結構也是很有幫助的。 它代表目前的快照集的檢視階層。 您可以選取要設定資料列`selectedView`REPL 中，並查看檢視的屬性值。 在 Mac 上，您可以與多層式檢視的 3D 分裂式視覺效果互動。 在 Windows 中，您可以以視覺化方式編輯檢視的屬性值。

## <a name="known-limitations"></a>已知的限制

- 檢視的選取範圍只有主顯示器上。
- 屬性方格中編輯不適用於 Mac 和 Windows 上僅限於少數的資料類型。 使用複寫功能更強大的編輯。
- 只要偵測器增益集/延伸模組已安裝，並啟用您的 IDE 中，我們會將程式碼插入您的應用程式每次啟動偵錯模式中也可以。 如果您在應用程式中發現任何奇怪的行為，請嘗試停用或解除安裝 Inspector 增益集/延伸模組、 重新啟動該 IDE，然後重新檢查。 並請[提報 bug](~/tools/inspector/install.md#reporting-bugs) ，讓我們知道 ！
- 如果檢查 UI 項目造成還是變更中，請[讓我們知道](~/tools/inspector/install.md#reporting-bugs)，因為這可能是 bug。

