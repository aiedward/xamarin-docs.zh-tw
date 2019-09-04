---
title: 在 Xamarin 中使用 tvOS 警示
description: 本檔說明如何在 Xamarin 中使用 tvOS 警示。 它討論如何顯示警示、新增文字欄位, 以及協助程式類別。
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: de7c8918ff500cb2353214fd84eaa4c97713493e
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227273"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>在 Xamarin 中使用 tvOS 警示

_本文涵蓋如何使用 UIAlertController, 在 tvOS 中向使用者顯示警示訊息。_

如果您需要 tvOS 使用者的注意, 或要求執行破壞性動作 (例如刪除檔案) 的許可權, 您可以使用`UIAlertViewController`來顯示警示訊息:

[![](alerts-images/alert01.png "範例 UIAlertViewController")](alerts-images/alert01.png#lightbox)

如果除了顯示訊息以外, 您可以將按鈕和文字欄位新增至警示, 讓使用者能夠回應動作並提供意見反應。

<a name="About-Alerts" />

## <a name="about-alerts"></a>關於警示

如上所述, 會使用警示來取得使用者的注意, 並通知他們應用程式的狀態或要求意見反應。 警示必須顯示標題, 而且可以選擇性地擁有一則訊息, 以及一或多個按鈕或文字欄位。

[![](alerts-images/alert04.png "範例警示")](alerts-images/alert04.png#lightbox)

Apple 具有下列使用警示的建議:

- **謹慎使用警示**-警示會中斷使用者與應用程式的流程, 並中斷使用者體驗, 因此, 應該只用于重要的情況, 例如錯誤通知、應用程式內購買和破壞性動作。
- **提供有用的選擇**-如果警示向使用者顯示選項, 您應該確保每個選項都提供重要的資訊, 並提供實用的動作供使用者採取。

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>警示標題和訊息

Apple 提供下列建議來呈現警示的標題和選擇性的訊息:

- **使用 Multiword phrases 標題**-警示的標題應該可以清楚地取得情況的點, 同時仍然保持在最簡單的狀態。 單一字組標題很少會提供足夠的資訊。
- **使用不需要訊息的描述性標題**-任何可能的情況下, 請考慮讓警示的標題具有足夠的描述, 而不需要選擇性的郵件內文。
- 將**訊息設為簡短、完整的句子**-如果需要選擇性的訊息以取得整個警示點, 請盡可能保持簡單, 並以適當的大小寫和標點符號做為完整的句子。

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>警示按鈕

Apple 具有將按鈕新增至警示的下列建議:

- **限制為兩個按鈕**-任何可能的情況下, 將警示限制為最多兩個按鈕。 單一按鈕警示會提供資訊, 但沒有動作。 兩個按鈕警示提供簡單的 [是/否] 動作。
- **使用簡潔的邏輯按鈕標題**-簡單的一到兩個單字按鈕標題, 清楚地描述按鈕的動作效果。 如需詳細資訊, 請參閱我們[的使用按鈕](~/ios/tvos/user-interface/buttons.md)檔。
- **清楚標示破壞性按鈕**-對於執行破壞性動作 (例如刪除檔案) 的按鈕, 請將`UIAlertActionStyle.Destructive`其清楚地標記為樣式。

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>顯示警示

若要顯示警示, 請建立的`UIAlertViewController`實例, 並藉由新增動作 (按鈕) 並選取警示的樣式來設定它。 例如, 下列程式碼會顯示 [確定]/[取消] 警示:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...

var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

讓我們詳細查看這段程式碼。 首先, 我們會建立具有指定標題和訊息的新警示:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下來, 針對要顯示在警示中的每個按鈕, 我們會建立一個動作來定義按鈕的標題、其樣式, 以及當按下按鈕時要採取的動作:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ =>
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`列舉可讓您將按鈕的樣式設定為下列其中一項:

- **預設值**-當顯示警示時, 按鈕會是已選取的預設按鈕。
- **取消**-按鈕是警示的 [取消] 按鈕。
- **破壞性**-將按鈕反白顯示為破壞性動作, 例如刪除檔案。 目前, tvOS 會呈現具有紅色背景的破壞性按鈕。

方法會將指定的動作加入`UIAlertViewController`至, 最後, `PresentViewController (alertController, true, null)`方法會向使用者顯示指定的警示。 `AddAction`

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>加入文字欄位

除了將動作 (按鈕) 新增至警示之外, 您還可以將文字欄位新增至警示, 以允許使用者填入使用者識別碼和密碼等資訊:

[![](alerts-images/alert02.png "警示中的文字欄位")](alerts-images/alert02.png#lightbox)

如果使用者選取文字欄位, 將會顯示標準 tvOS 鍵盤, 讓他們輸入欄位的值:

[![](alerts-images/alert03.png "輸入文字")](alerts-images/alert03.png#lightbox)

下列程式碼會顯示 [確定]/[取消] 警示, 其中包含用於輸入值的單一文字欄位:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

`AddTextField`方法會將新的文字欄位新增至警示, 您可以設定屬性, 例如預留位置文字 (欄位為空白時所顯示的文字)、預設文字值和鍵盤類型。 例如：

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

我們稍後可以根據文字欄位的值採取動作, 我們也會使用下列程式碼來儲存的複本:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

當使用者在文字欄位中輸入值之後, 就可以使用該`field`變數來存取該值。

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>警示視圖控制器 Helper 類別

由於使用`UIAlertViewController`來顯示簡單的常見警示類型可能會產生相當多的重複程式碼, 因此您可以使用 helper 類別來減少重複的程式碼數量。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

您可以使用此類別來顯示及回應簡單的警示, 如下所示:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋`UIAlertController`了如何使用, 在 tvOS 中向使用者顯示警示訊息。 首先, 它會示範如何顯示簡單的警示並新增按鈕。 接下來, 它會示範如何將文字欄位新增至警示。 最後, 它會示範如何使用 helper 類別來減少顯示警示所需的重複程式碼數量。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
