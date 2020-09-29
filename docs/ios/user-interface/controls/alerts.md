---
title: 在 Xamarin 中顯示警示
description: 本檔說明如何使用 iOS 8 中引進的 UIAlertController Api，在 Xamarin 中顯示警示。
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: b7dae8afbb5db378687f9ecb9469236dc68831ae
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435496"
---
# <a name="displaying-alerts-in-xamarinios"></a>在 Xamarin 中顯示警示

從 iOS 8 開始，UIAlertController 已完全取代 UIActionSheet 和 UIAlertView，兩者現在已被取代。

不同于它所取代的類別（也就是 UIView 的子類別），UIAlertController 是 UIViewController 的子類別。

用 `UIAlertControllerStyle` 來指出要顯示的警示類型。 這些警示類型為：

- **UIAlertControllerStyleActionSheet**
  - IOS 8 之前的 8 UIActionSheet
- **UIAlertControllerStyleAlert**
  - IOS 8 之前的 8 UIAlertView 

建立警示控制器時需要執行三個步驟：

- 使用下列內容建立並設定警示：
  - title
  - 訊息
  - preferredStyle

-  (選擇性) 新增文字欄位
- 新增必要的動作
- 顯示視圖控制器

最簡單的警示包含單一按鈕，如下列螢幕擷取畫面所示：

 ![具有一個按鈕的警示](alerts-images/alert1.png)

顯示簡單警示的程式碼如下所示：

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

顯示具有多個選項的警示會以類似的方式完成，但會新增兩個動作。 例如，下列螢幕擷取畫面顯示具有兩個按鈕的警示：

 ![有兩個按鈕的警示](alerts-images/alert2.png)

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

警示也可以顯示動作表，類似于下列螢幕擷取畫面：

 ![動作表警示](alerts-images/alert3.png)

按鈕會使用方法新增至警示 `AddAction` ：

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

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)
- [警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)