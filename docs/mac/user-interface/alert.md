---
title: Xamarin 中的警示
description: 本文說明如何在 Xamarin. Mac 應用程式中使用警示。 其中說明如何從C#程式碼建立和顯示警示，並回應使用者互動。
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 2ef8e81390b5d64971aa3883a05da9fb8795a416
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001523"
---
# <a name="alerts-in-xamarinmac"></a>Xamarin 中的警示

_本文說明如何在 Xamarin. Mac 應用程式中使用警示。其中說明如何從C#程式碼建立和顯示警示，並回應使用者互動。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時，您可以存取開發人員在*目標-C*和*Xcode*中工作的相同警示。 

警示是一種特殊類型的對話方塊，會在發生嚴重問題（例如錯誤）或警告（例如準備刪除檔案）時出現。 因為警示是對話方塊，所以它也需要使用者回應才能關閉。

[![](alert-images/alert06.png "An example alert")](alert-images/alert06.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用警示的基本概念。 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>警示簡介

警示是一種特殊類型的對話方塊，會在發生嚴重問題（例如錯誤）或警告（例如準備刪除檔案）時出現。 由於警示會中斷使用者，因為它們必須先解除，使用者才能繼續執行其工作，除非絕對必要，否則請避免顯示警示。

Apple 建議下列指導方針：

- 請勿只使用警示來提供使用者資訊。
- 不要顯示常見、可執行檔動作的警示。 即使這種情況可能會造成資料遺失。
- 如果情況需要警示，請避免使用任何其他 UI 元素或方法來顯示它。
- 小心的圖示應該謹慎使用。
- 在警示訊息中清楚且簡潔地描述警示狀況。
- 預設的按鈕名稱應對應至您在警示訊息中所描述的動作。

如需詳細資訊，請參閱 Apple [OS X 人力介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[警示](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1)一節。

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>警示的剖析

如上所述，當嚴重問題發生時，您的應用程式使用者應該會看到警示，或是可能遺失資料的警告（例如關閉未儲存的檔案）。 在 Xamarin. Mac 中，會在程式碼C#中建立警示，例如：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Critical,
  InformativeText = "We need to save the document here...",
  MessageText = "Save Document",
};
alert.RunModal ();
```

上述程式碼會顯示警示，其中的 [應用程式] 圖示會疊加在警告圖示、標題、警告訊息和單一 **[確定]** 按鈕上：

[![](alert-images/alert01.png "An alert with a OK button")](alert-images/alert01.png#lightbox)

Apple 提供數個可用於自訂警示的屬性：

- **AlertStyle**會將警示的類型定義為下列其中一項：
  - **警告**-用來警告使用者目前或即將發生的事件不重要。 這是預設樣式。
  - **資訊**-用來警告使用者目前或即將發生的事件。 目前，**警告**和**資訊**之間沒有明顯的差異
  - **重大**-用來警告使用者即將發生的事件（例如刪除檔案）的嚴重後果。 這種類型的警示應該謹慎使用。
- **MessageText** -這是警示的主要訊息或標題，應快速定義使用者的情況。
- **InformativeText** -這是警示的主體，您應該在其中明確地定義情況，並向使用者呈現可行的選項。
- **圖示**-允許對使用者顯示自訂圖示。
- **HelpAnchor** & **ShowsHelp** -允許將警示系結至應用程式 HelpBook，並顯示警示的 [說明]。
- **按鈕**-根據預設，警示只有 [**確定]** 按鈕，但 [**按鈕**] 集合可讓您視需要新增更多選擇。
- **ShowsSuppressionButton** -如果 `true` 會顯示一個核取方塊，讓使用者可以用來隱藏觸發該警示之事件的後續出現位置。
- **AccessoryView** -可讓您將另一個子視圖附加至警示，以提供額外的資訊，例如加入資料輸入的**文字欄位**。 如果您設定新的**AccessoryView**或修改現有的，則需要呼叫 `Layout()` 方法，以調整警示的可見配置。

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>顯示警示

有兩種不同的方式可以顯示警示、自由浮動或當做工作表。 下列程式碼會將警示顯示為自由浮動：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.RunModal ();
```

如果執行此程式碼，則會顯示下列內容：

[![](alert-images/alert02.png "A simple alert")](alert-images/alert02.png#lightbox)

下列程式碼會顯示與工作表相同的警示：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

如果執行此程式碼，將會顯示下列內容：

[![](alert-images/alert03.png "An alert displayed as a sheet")](alert-images/alert03.png#lightbox)

<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>使用警示按鈕

根據預設，警示只會顯示 [**確定]** 按鈕。 不過，您不一定要這樣做，您可以將其附加至**按鈕**集合，以建立額外的按鈕。 下列程式碼會建立具有 **[確定]、[** **取消**] 和 [**可能**] 按鈕的免費浮動警示：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

新增的第一個按鈕會是_預設按鈕_，當使用者按下 Enter 鍵時，將會啟用此按鈕。 傳回的值會是一個整數，代表使用者所按的按鈕。 在我們的案例中，將會傳回下列值：

- **確定**-1000。
- **取消**-1001。
- **或許**是-1002。

如果我們執行程式碼，將會顯示下列內容：

[![](alert-images/alert04.png "An alert with three button options")](alert-images/alert04.png#lightbox)

以下是與工作表相同之警示的程式碼：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}", result);
});
```

如果執行此程式碼，將會顯示下列內容：

[![](alert-images/alert05.png "A three button alert displayed as a sheet")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> 您絕對不應該將三個以上的按鈕新增至警示。

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>顯示隱藏按鈕

如果警示的 [`ShowSuppressButton`] 屬性為 [`true`]，警示會顯示一個核取方塊，供使用者用來隱藏觸發它的後續事件的警示。 下列程式碼會顯示包含隱藏按鈕的免費浮動警示：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

如果 `alert.SuppressionButton.State` 的值為 `NSCellStateValue.On`，則使用者已核取 [隱藏] 核取方塊，否則為 [否]。

如果程式碼已執行，則會顯示下列內容：

[![](alert-images/alert06.png "An alert with a suppress button")](alert-images/alert06.png#lightbox)

以下是與工作表相同之警示的程式碼：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果執行此程式碼，將會顯示下列內容：

[![](alert-images/alert07.png "An alert with a suppress button display as a sheet")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>新增自訂子視圖

警示具有 `AccessoryView` 屬性，可用來進一步自訂警示，並新增類似使用者輸入**文字欄位**的專案。 下列程式碼會使用已加入的文字輸入欄位來建立免費浮動的警示：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

此處的索引鍵行 `var input = new NSTextField (new CGRect (0, 0, 300, 20));`，其會建立新的**文字欄位**，讓我們新增警示。 `alert.AccessoryView = input;`，會將**文字欄位**附加至警示，並呼叫 `Layout()` 方法，這是調整警示大小以納入新的子視圖所需的。

如果我們執行程式碼，將會顯示下列內容：

[![](alert-images/alert08.png "If we run the code, the following will be displayed")](alert-images/alert08.png#lightbox)

以下是與工作表相同的警示：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果我們執行此程式碼，將會顯示下列內容：

[![](alert-images/alert09.png "An alert with a custom view")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用警示。 我們看到了不同類型和使用的警示、如何建立和自訂警示，以及如何在程式碼中C#使用警示。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
