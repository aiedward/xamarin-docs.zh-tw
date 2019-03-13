---
title: Xamarin.Mac 中的警示
description: 本文涵蓋在 Xamarin.Mac 應用程式中的警示。 建立和顯示的警示，它會描述C#程式碼和回應使用者互動。
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6545b1423b809e42293302baf3eba9521848edc1
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526389"
---
# <a name="alerts-in-xamarinmac"></a>Xamarin.Mac 中的警示

_本文涵蓋在 Xamarin.Mac 應用程式中的警示。建立和顯示的警示，它會描述C#程式碼和回應使用者互動。_

使用時C#和.NET Xamarin.Mac 應用程式中的，您可以存取相同警示工作的開發人員*OBJECTIVE-C*並*Xcode*沒有。 

表示警示是一種特殊的嚴重問題發生 （例如錯誤） 時，會出現的對話方塊或警告 （例如正在準備刪除檔案）。 由於警示是一個對話方塊，它還需要使用者回應之前予以關閉。

[![](alert-images/alert06.png "範例警示")](alert-images/alert06.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用警示的基本概念。 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>警示的簡介

表示警示是一種特殊的嚴重問題發生 （例如錯誤） 時，會出現的對話方塊或警告 （例如正在準備刪除檔案）。 因為警示干擾使用者，因為它們必須關閉使用者可以繼續進行其工作之前，請避免顯示警示，除非絕對必要。

Apple 建議下列指導方針：

- 請勿使用警示僅能為使用者提供的資訊。
- 不會顯示常見、 可復原的動作的警示。 即使這種情況下，可能會導致資料遺失。
- 如果值得發出警示的情況下，避免使用任何其他 UI 項目或方法來顯示它。
- 注意圖示，應該謹慎使用。
- 在警示訊息清楚且簡潔地說明警示狀況。
- 預設值 按鈕的名稱應該對應至您在警示訊息中描述的動作。

如需詳細資訊，請參閱 <<c0> [ 警示](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>警示的結構

如上所述，您的應用程式使用者時就會發生嚴重的問題或為警告 （例如關閉未儲存的檔案） 的潛在資料遺失，應該會顯示警示。 Xamarin.Mac，警示會在中建立C#程式碼，例如：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

上述程式碼會顯示警示，具有重疊的警告圖示、 標題、 一則警告訊息和單一應用程式圖示**確定**按鈕：

[![](alert-images/alert01.png "具有 [確定] 按鈕的警示")](alert-images/alert01.png#lightbox)

Apple 提供數個屬性，可用來自訂警示：

- **AlertStyle**定義警示的類型為下列其中之一：
    - **警告**-用來警告使用者目前或即將發生的事件，並不重要。 這是預設樣式。
    - **參考**-用來警告使用者目前或即將發生的事件。 目前沒有任何可見差異**警告**和**資訊**
    - **重要**-用來警告使用者即將發生的事件 （例如刪除檔案） 的嚴重的後果。 這類警示，應該謹慎使用。
- **MessageText** -這是主要的訊息或警示的標題，而且應該快速定義給使用者的情況。
- **InformativeText** -這是主體的警示，您應該在此清楚地定義這種情況，並向使用者顯示可用的選項。
- **圖示**-可讓自訂的圖示，以顯示給使用者。
- **HelpAnchor** & **ShowsHelp** -可讓警示可繫結到 HelpBook 應用程式，並顯示警示的說明。
- **按鈕**-根據預設，警示只有**確定**  按鈕，但**按鈕**集合可讓您新增更多的選擇，視需要。
- **ShowsSuppressionButton** -如果`true`顯示使用者可以使用隱藏的警示觸發的事件後面出現的核取方塊。
- **AccessoryView** -可讓您將另一個子檢視的警示，以提供額外的資訊，例如新增至**文字欄位**用於資料輸入。 如果您將新**AccessoryView**或修改現有的帳戶，您必須呼叫`Layout()`調整警示的顯示配置的方法。

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>顯示警示

有兩種不同的方式，警示可以顯示、 自由浮動或以工作表。 下列程式碼會顯示為自由浮動的警示：

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

下列程式碼會顯示相同的警示，以工作表：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

如果執行此程式碼時，將會顯示下列：

[![](alert-images/alert03.png "警示顯示為工作表")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>使用警示按鈕

根據預設，警示只會顯示**確定** 按鈕。 不過，您不限於，您可以建立額外的按鈕附加他們**按鈕**集合。 下列程式碼建立的自由浮動警示 **[確定]**，**取消**並**或許**按鈕：

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

加入的第一個按鈕將會_預設值 按鈕_，將啟用如果使用者按下 Enter 鍵。 傳回的值會是整數，代表的按鈕按下的使用者。 在我們的案例將會傳回下列值：

- **確定**-1000年。
- **取消**-1001年。
- **或許**1002年-。

如果我們執行程式碼，將會顯示下列：

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
如果執行此程式碼時，將會顯示下列：

[![](alert-images/alert05.png "以工作表顯示三個按鈕警示")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> 您應該永遠不會將警示超過三個按鈕加入。

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>顯示 [隱藏] 按鈕

如果警示`ShowSuppressButton`屬性是`true`，警示會顯示使用者可以使用隱藏的警示觸發的事件後面出現的核取方塊。 下列程式碼會顯示 [隱藏] 按鈕的自由浮動警示：

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

如果值`alert.SuppressionButton.State`是`NSCellStateValue.On`，使用者已簽入隱藏的核取方塊，否則它們沒有。

如果執行程式碼時，將會顯示下列：

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

如果執行此程式碼時，將會顯示下列：

[![](alert-images/alert07.png "隱藏按鈕的警示會顯示為工作表")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>新增自訂的子檢視

警示有`AccessoryView`屬性，可用於自訂進一步的警示，並加入一些事，像是**文字欄位**使用者輸入。 下列程式碼會建立已新增的文字輸入欄位的自由浮動的警示：

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

關鍵的程式碼都`var input = new NSTextField (new CGRect (0, 0, 300, 20));`以建立新**文字欄位**，我們將新增警示。 `alert.AccessoryView = input;` 哪些附加**文字欄位**警示並呼叫`Layout()`方法，才可調整大小以符合新的子檢視警示。

如果我們執行程式碼，將會顯示下列：

[![](alert-images/alert08.png "如果我們執行程式碼時，會顯示下列")](alert-images/alert08.png#lightbox)

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

如果我們執行此程式碼，將會顯示下列：

[![](alert-images/alert09.png "具有自訂檢視的警示")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解在 Xamarin.Mac 應用程式中使用的警示。 我們可了解不同類型和使用警示、 如何建立和自訂警示和如何使用中的警示C#程式碼。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
