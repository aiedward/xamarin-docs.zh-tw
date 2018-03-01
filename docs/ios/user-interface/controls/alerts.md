---
title: "顯示警示"
ms.topic: article
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 09f4178d5d6e12388771fa63875fbe1f489c959a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="displaying-alerts"></a>顯示警示

從 iOS 8 開始，已完成取代的 UIActionSheet UIAlertController 和其中 UIAlertView 現在已被取代。

不同於它所取代，也就是 UIView 的子類別，類別 UIAlertController 是 UIViewController 的子類別。

使用`UIAlertControllerStyle`來指出要顯示的警示類型。 這些警示類型包括：

- **UIAlertControllerStyleActionSheet**
    * 前 iOS 8 這已經 UIActionSheet
- **UIAlertControllerStyleAlert**
    * 前 iOS 8 這已經 UIAlertView 

有三個必要的步驟，才能建立警示的控制站：

- 建立並使用 a： 設定警示
    * 標題
    * 訊息
    * preferredStyle
    
- （選擇性）新增文字欄位
- 加入必要的動作
- 呈現檢視控制器

最簡單的警示會包含單一按鈕，這個螢幕擷取畫面所示：

 ![具有一個按鈕警示](alerts-images/alert1.png)

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

顯示警示有多個選項，以類似的方式完成，但是加入兩個動作。 例如，下列螢幕擷取畫面會顯示兩個按鈕的警示：

 ![ 具有兩個按鈕警示](alerts-images/alert2.png)

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

警示也可以顯示動作表類似下面的螢幕擷取畫面：

 ![動作的工作表警示](alerts-images/alert3.png)

加入按鈕，為警示加`AddAction`方法：

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Three pressed.")));

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

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
- [警示的控制站](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
