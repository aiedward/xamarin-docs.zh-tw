---
title: 檢查應用程式
description: 本文件說明如何使用 Xamarin 偵測器來檢查應用程式。 它也會討論 Xamarin Inspector 工具的限制。
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: 16a0474c7fc9a496409e2133714b6d4374691dbc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793475"
---
# <a name="inspecting-live-applications"></a>檢查應用程式

企業客戶可使用即時應用程式檢查。

1. [安裝 Xamarin 活頁簿 （& s） 偵測器。](~/tools/inspector/install.md)
1. 開啟任何[支援應用程式專案](~/tools/inspector/install.md#supported-platforms)Mac 或 Visual Studio 的 Visual Studio 中。
1. 以偵錯模式執行您的應用程式。
1. 按一下**檢查**IDE 工具列按鈕 (在 Visual Studio**檢查目前的應用程式。** 功能表項目也會提供**工具**或**偵錯**功能表)。

[![](inspect-images/mac-heres-the-button.png "按一下 [檢查] 按鈕，IDE 工具列中")](inspect-images/mac-heres-the-button.png#lightbox)

隨即會開啟新的 Xamarin 偵測器用戶端視窗，以全新的 REPL 提示。

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "隨即會開啟新的 Xamarin 偵測器用戶端視窗，以全新的 REPL 提示")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

此視窗隨即出現，您便可以互動式 C# 提示可用來執行，C# 陳述式和運算式評估。 唯一的是，目標處理序的內容中評估程式碼。 在此情況下，我們會說明根據顯示的 iOS 應用程式執行的程式碼。

目標處理序上實際發生您對應用程式狀態的任何變更，因此您可以使用 C#，以變更應用程式即時，或您可以檢查即時應用程式的狀態。

例如，在 iOS 上，我們可能想要找出 uiapplication # 委派類別，也就是我們 （我們儲存大量的應用程式狀態） 的主要驅動程式：

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

（請注意，每次送出，就會發生在多行編輯器中。 `Shift + Enter` 將會建立新的一行，和`Cmd + Enter`(`Ctrl + Enter` Windows 上) 會提交的程式碼進行評估。 `Enter` 自動提交安全時。）

若要取得您的應用程式的視覺化項目較方便的方式是使用 「 檢查 」 按鈕。 一旦您按下此按鍵，您可以選取 UI 項目，按一下您的應用程式。 變數`selectedView`將指派給指向在螢幕上的實際項目。 在上面的螢幕擷取畫面，您可以看到我們如何存取，然後編輯`selectedView.BarTintColor`上`UISearchBar`我們已選取。

即時視覺化樹狀結構也是非常有用。 它代表目前的快照集的檢視階層。 您可以選取要設定資料列`selectedView`REPL 並查看檢視的屬性值。 在 Mac 上，您可以與多層式檢視的 3D 分裂式視覺效果互動。 在 Windows 中，您可以視覺化方式編輯檢視的屬性值。

## <a name="known-limitations"></a>已知的限制

 - 在主顯示器上才支援檢視的選取範圍。
 - 屬性方格中編輯不適用於 Mac 和 Windows 上受限於幾個資料類型。 使用複寫功能更強大的編輯。
 - 只要偵測器增益集/擴充功能安裝並啟用您的 IDE 中，我們會插入的程式碼到您的應用程式每次啟動偵錯模式中。 如果您注意到您的應用程式不正常的行為，請再試一次停用或解除安裝偵測器增益集/擴充功能、 重新啟動 IDE 時，並重新檢查便箋可以。 並請[檔案 bug](~/tools/inspector/install.md#reporting-bugs) ，讓我們知道 ！
 - 如果檢查 UI 項目造成還是變更中，請[讓我們知道](~/tools/inspector/install.md#reporting-bugs)，因為這可能表示 bug。

