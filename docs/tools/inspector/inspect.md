---
title: 檢查即時應用程式
description: 本檔說明如何使用 Xamarin Inspector 來檢查應用程式。 同時討論 Xamarin Inspector 工具的限制。
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: 2ccf6966e85eddaa10b5651e1b0b48dec9203b28
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772207"
---
# <a name="inspecting-live-applications"></a>檢查即時應用程式

即時應用程式檢查適用于企業客戶。

1. 在 Visual Studio for Mac 或 Visual Studio 中開啟任何[支援的應用程式專案](~/tools/inspector/install.md#supported-platforms)。
1. 以偵錯模式執行您的應用程式。
1. 按一下 IDE 工具列中的 [**檢查**] 按鈕（在 Visual Studio 中，您也可以從 [**工具**] 或 [**調試**程式] 功能表取得 [**檢查目前的應用程式 ...** ] 功能表項目）。

[![](inspect-images/mac-heres-the-button.png "按一下 IDE 工具列中的 [檢查] 按鈕")](inspect-images/mac-heres-the-button.png#lightbox)

新的 Xamarin Inspector 用戶端視窗隨即開啟，並出現全新的複寫提示。

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "新的 Xamarin Inspector 用戶端視窗隨即開啟，並出現全新的複寫提示")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

這個視窗出現之後，您就會有C#一個互動式提示，可讓您用來C#執行和評估語句和運算式。 這是唯一的，那就是程式碼是在目標進程的內容中進行評估。 在此情況下，我們會顯示針對 iOS 應用程式所顯示的程式碼。

您對應用程式狀態所做的任何變更實際上都是在目標進程上進行，因此您可以使用C#來即時變更應用程式，也可以檢查應用程式的即時狀態。

例如，在 iOS 上，我們可能會想要找出我們的 UIApplication 委派類別，這是我們主要的驅動程式（我們會在其中儲存許多應用程式狀態）：

```csharp
var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
del.Database.GetAllCustomers ()
...
del.Database.AddCustomer (...)
```

（請注意，每次提交都會出現在多行編輯器中。 `Shift + Enter`會建立新的一行，而`Cmd + Enter` （`Ctrl + Enter`在 Windows 上）會提交程式碼進行評估。 `Enter`當安全時自動提交）。

若要取得應用程式的視覺元素，更方便的方式是使用 [檢查] 按鈕。 一旦您按下此按鈕，就可以按一下應用程式來選取 UI 元素。 系統會`selectedView`指派變數以指向螢幕上的實際元素。 在上述螢幕擷取畫面中，您可以看到我們在選取的`selectedView.BarTintColor` `UISearchBar`之後，如何存取和編輯。

即時視覺化樹狀結構也非常有用。 它代表您的視圖階層目前的快照集。 您可以選取要在複寫`selectedView`中設定的資料列，並查看該視圖的屬性值。 在 Mac 上，您可以與多層式視圖的3D 分解視覺效果互動。 在 Windows 上，您可以透過視覺化方式編輯檢視的屬性值。

## <a name="known-limitations"></a>已知的限制

- 只有主顯示器才支援視圖選取。
- Mac 無法使用屬性方格編輯，而在 Windows 上，則限制為少數資料類型。 使用複寫功能進行更強大的編輯。
- 只要您的 IDE 中已安裝並啟用偵測器增益集/擴充功能，我們就會在每次以「偵測模式」啟動時，將程式碼插入您的應用程式中。 如果您注意到您的應用程式中有任何奇怪的行為，請嘗試停用或卸載偵測器的增益集/擴充功能、重新開機 IDE，然後重新檢查。 請提出[bug](~/tools/inspector/install.md#reporting-bugs) ，讓我們知道！
- 如果檢查 UI 元素會使其在中變更，請[讓我們知道](~/tools/inspector/install.md#reporting-bugs)，因為這可能表示有錯誤。
