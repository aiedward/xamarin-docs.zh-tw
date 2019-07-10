---
title: 在 Xamarin.iOS 中顯示的警示
description: 本文件說明如何在 Xamarin.iOS 中顯示警示，使用 UIAlertController iOS 8 中導入的 Api。
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 576de9a46fc89fe01c564c05568d4331a32dbd96
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674403"
---
# <a name="displaying-alerts-in-xamarinios"></a>在 Xamarin.iOS 中顯示的警示

從 iOS 8 開始，已完成的已取代的 UIActionSheet UIAlertController 和其中 UIAlertView 現在已被取代。

不同於它所取代，也就是 UIView 的子類別，類別 UIAlertController 是 UIViewController 的子類別。

使用`UIAlertControllerStyle`來表示要顯示警示的類型。 這些警示類型包括：

- **UIAlertControllerStyleActionSheet**
    * 前 iOS 8 這會是 UIActionSheet
- **UIAlertControllerStyleAlert**
    * 前 iOS 8 這會是 UIAlertView 

有三個必要的步驟，需要時建立警示控制器：

- 建立和設定警示以 a:
    * 標題
    * 訊息
    * preferredStyle
    
- （選擇性）新增文字欄位
- 新增必要的動作
- 呈現的檢視控制器

最簡單的警示會包含一個按鈕，如以下螢幕擷取畫面所示：

 ![一個按鈕的警示](alerts-images/alert1.png)

顯示一個簡單的警示的程式碼如下所示：

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

顯示的警示有多個選項，以類似的方式完成，但新增兩個動作。 例如，下列螢幕擷取畫面顯示具有兩個按鈕的警示：

 ![兩個按鈕的警示](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

警示也可以顯示動作的工作表，類似於以下螢幕擷取畫面：

 ![動作的工作表的警示](alerts-images/alert3.png)

按鈕會新增至具有警示`AddAction`方法：

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/monotouch/Controls/)
- [警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
