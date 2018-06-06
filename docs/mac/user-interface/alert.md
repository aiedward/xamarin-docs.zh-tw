---
title: Xamarin.Mac 中警示
description: 本文件涵蓋使用 Xamarin.Mac 應用程式中的警示。 它說明建立和顯示 C# 程式碼產生的警示，以及回應使用者互動。
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 1eb781fe02213de6a994f56e321316b93a128b60
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792638"
---
# <a name="alerts-in-xamarinmac"></a>Xamarin.Mac 中警示

_本文件涵蓋使用 Xamarin.Mac 應用程式中的警示。它說明建立和顯示 C# 程式碼產生的警示，以及回應使用者互動。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取的相同警示，工作的開發人員*OBJECTIVE-C*和*Xcode*沒有。 

警示是一種特殊的錯誤） （例如，就會發生嚴重的問題時出現的對話方塊或警告 （例如準備要刪除的檔案）。 因為警示是一個對話方塊，它也需要使用者回應可被關閉前。

[![](alert-images/alert06.png "範例警示")](alert-images/alert06.png#lightbox)

在本文中，我們將討論 Xamarin.Mac 應用程式中使用警示的基本概念。 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>警示的簡介

警示是一種特殊的錯誤） （例如，就會發生嚴重的問題時出現的對話方塊或警告 （例如準備要刪除的檔案）。 由於警示會中斷使用者，因為使用者可以繼續其工作之前必須關閉它們，避免顯示警告，除非絕對必要。

Apple 的建議下列指導方針：

- 不使用警示只是讓使用者資訊。
- 不會顯示警示的常見、 可復原的動作。 即使這種情況下可能會導致資料遺失。
- 如果警示的情況下，請避免使用任何其他 UI 項目或方法加以顯示。
- 注意圖示應該謹慎使用。
- 在警示訊息清楚且簡潔地說明警示狀況。
- 預設值 按鈕名稱應該對應至您在警示訊息中描述的動作。

如需詳細資訊，請參閱[警示](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>警示的結構

如前所述，應用程式的使用者時就會發生嚴重的問題或潛在資料遺失 （例如關閉未儲存的檔案） 的警告，應該會顯示警示。 在 Xamarin.Mac，警示會在建立 C# 程式碼，例如：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

上述程式碼會顯示警示與應用程式圖示上的警告圖示、 標題、 一則警告訊息和單一重疊**確定**按鈕：

[![](alert-images/alert01.png "具有 [確定] 按鈕的警示")](alert-images/alert01.png#lightbox)

Apple 提供數個屬性，可以用來自訂警示：

- **AlertStyle**定義警示的類型為下列其中之一：
    - **警告**-用來警告使用者的目前或即將發生的事件，並不重要。 這是預設樣式。
    - **告知性**-用來警告使用者目前或即將發生的事件。 目前沒有任何可見差異**警告**和**資訊**
    - **重大**-用來警告使用者即將發生的事件 （例如刪除檔案） 的嚴重的後果。 應該謹慎使用這種類型的警示。
- **MessageText** -這是主要訊息或警示的標題，而且應該快速定義給使用者的情況。
- **InformativeText** -這是主體的警示，您應該清楚地定義這種情況並向使用者顯示可用的選項。
- **圖示**-可讓自訂的圖示，以顯示給使用者。
- **HelpAnchor** & **ShowsHelp** -允許要繫結到 HelpBook 的應用程式，並顯示說明警示的警示。
- **按鈕**-根據預設，警示只有 **[確定]** 按鈕但**按鈕**集合可讓您將新增所需的更多選擇。
- **ShowsSuppressionButton** -如果`true`顯示使用者可以使用隱藏的警示觸發該事件後面出現的核取方塊。
- **AccessoryView** -可讓您將另一個子檢視附加至以提供額外的資訊，例如新增警示**文字欄位**用於資料輸入。 如果您設定新**AccessoryView**或修改現有，您需要呼叫`Layout()`調整警示的顯示配置的方法。

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>顯示警示

有兩種不同的方式，警示可以顯示、 自由浮動或當做工作表。 下列程式碼會顯示為自由浮動警示：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
如果執行此程式碼時，會顯示下列：

[![](alert-images/alert02.png "一個簡單的警示")](alert-images/alert02.png#lightbox)

下列程式碼會以工作表顯示的相同警示：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

如果執行此程式碼，則下列將會顯示：

[![](alert-images/alert03.png "警示顯示為工作表")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>使用 [警示] 按鈕

根據預設，會顯示警示只**確定** 按鈕。 不過，您不限於，您可以建立額外的按鈕將它們以附加**按鈕**集合。 下列程式碼建立的自由浮動警示**確定**，**取消**和**或許**按鈕：

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

加入的第一次按鈕會_預設值 按鈕_，將會啟動使用者按下 Enter 鍵。 傳回的值會是整數，表示的按鈕已按下的使用者。 在我們的案例將會傳回下列值：

- **確定**-1000年。
- **取消**1001年-。
- **或許**1002年-。

當我們執行程式碼，將會顯示下列：

[![](alert-images/alert04.png "具有三個按鈕選項的警示")](alert-images/alert04.png#lightbox)

以下是相同的警示，以工作表的程式碼：

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
如果執行此程式碼，則下列將會顯示：

[![](alert-images/alert05.png "以工作表顯示三個按鈕警示")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> 您不應該將三個以上的按鈕新增至警示。

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>顯示 [隱藏] 按鈕

如果警示的`ShowSuppressButton`屬性是`true`，警示會顯示使用者可以使用隱藏的警示觸發該事件後面出現的核取方塊。 下列程式碼會顯示與隱藏按鈕自由浮動警示：

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

如果值`alert.SuppressionButton.State`是`NSCellStateValue.On`使用者簽入隱藏的核取方塊，否則它們沒有。

如果程式碼執行時，以下將會顯示：

[![](alert-images/alert06.png "具有隱藏按鈕的警示")](alert-images/alert06.png#lightbox)

以下是相同的警示，以工作表的程式碼：

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

如果執行此程式碼，則下列將會顯示：

[![](alert-images/alert07.png "警示隱藏按鈕顯示的工作表")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>加入自訂的子檢視

警示有`AccessoryView`屬性，可用於自訂進一步的警示，以及加入一些事，像是**文字欄位**使用者輸入。 下列程式碼會加入的文字輸入欄位建立自由浮動警示：

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

索引鍵的行會是`var input = new NSTextField (new CGRect (0, 0, 300, 20));`以建立新**文字欄位**我們將會加入警示。 `alert.AccessoryView = input;` 哪些附加**文字欄位**警示並呼叫`Layout()`方法，才可調整大小以符合新的子檢視警示。

當我們執行程式碼，將會顯示下列：

[![](alert-images/alert08.png "當我們執行程式碼時，會顯示下列")](alert-images/alert08.png#lightbox)

以下是以工作表的相同警示：

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

當我們執行此程式碼，將會顯示下列：

[![](alert-images/alert09.png "使用自訂的檢視警示")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得 Xamarin.Mac 應用程式中使用警示的詳細的檢視。 我們了解不同類型和使用警示、 如何建立及自訂警示，以及如何使用 C# 程式碼中的警示。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用視窗](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
