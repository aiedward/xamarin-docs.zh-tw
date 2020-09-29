---
title: 在 Xamarin 中使用 tvOS 警示
description: 本檔說明如何在 Xamarin 中使用 tvOS 警示。 其中討論顯示警示、新增文字欄位，以及協助程式類別。
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 83831b71ebb349873b2a77bbd77ba08e08002d4a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436614"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>在 Xamarin 中使用 tvOS 警示

_本文說明如何使用 UIAlertController，在 tvOS 中顯示使用者的警示訊息。_

如果您需要注意 tvOS 使用者或要求執行破壞性動作的許可權 (例如刪除檔案) ，您可以使用下列內容呈現警示訊息 `UIAlertViewController` ：

[![範例 UIAlertViewController](alerts-images/alert01.png)](alerts-images/alert01.png#lightbox)

如果另外顯示訊息，您可以將按鈕和文字欄位新增至警示，以允許使用者回應動作並提供意見反應。

<a name="About-Alerts"></a>

## <a name="about-alerts"></a>關於警示

如上所述，警示可用來讓使用者注意，並通知他們您的應用程式狀態或要求意見反應。 警示必須要有標題，而且可以選擇性地有一則訊息，以及一或多個按鈕或文字欄位。

[![範例警示](alerts-images/alert04.png)](alerts-images/alert04.png#lightbox)

Apple 針對使用警示有下列建議：

- **謹慎使用警示** -警示會中斷使用者與應用程式的流程，並中斷使用者體驗，如此一來，僅適用于錯誤通知、應用程式內購買和破壞性動作等重要情況。
- **提供有用** 的選項-如果警示提供選項給使用者，您應該確定每個選項都提供重要資訊，並提供可讓使用者採取的實用動作。

<a name="Alert-Titles-and-Messages"></a>

### <a name="alert-titles-and-messages"></a>警示標題和訊息

Apple 提供下列建議來呈現警示的標題和選擇性訊息：

- **使用 Multiword 標題** -警示的標題應該可以清楚地取得情況的點，同時仍然保持簡單。 單一單字標題很少會提供足夠的資訊。
- **使用不需要訊息的描述性標題** （如果可能的話），請考慮讓警示標題的描述足以描述不需要選擇性的郵件內文。
- 將**訊息設為簡短的完整句子**-如果需要選擇性的訊息，以取得警示的點，請盡可能保持簡單，並將其設為適當大小寫和標點符號的完整句子。

<a name="Alert-Buttons"></a>

### <a name="alert-buttons"></a>警示按鈕

Apple 有下列建議可將按鈕新增至警示：

- **限制為兩個按鈕** -盡可能將警示限制為最多兩個按鈕。 單一按鈕警示可提供資訊，但不提供任何動作。 有兩個按鈕警示可提供簡單的 [是]/[否] 選擇動作。
- **使用簡潔的邏輯按鈕標題** -簡單的一到兩個單字按鈕標題，清楚描述按鈕的動作效果最佳。 如需詳細資訊，請參閱我們使用 [按鈕](~/ios/tvos/user-interface/buttons.md) 檔。
- **清楚標示破壞性按鈕** -對於執行破壞性動作的按鈕 (例如刪除檔案) 清楚地以樣式標示它們 `UIAlertActionStyle.Destructive` 。

<a name="Displaying-an-Alert"></a>

## <a name="displaying-an-alert"></a>顯示警示

若要顯示警示，您可以建立的實例， `UIAlertViewController` 並將動作新增 (按鈕) 並選取警示的樣式來進行設定。 例如，下列程式碼會顯示「確定/取消」警示：

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

讓我們詳細看看此程式碼。 首先，我們會建立具有指定標題和訊息的新警示：

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下來，針對我們想要在警示中顯示的每個按鈕，建立一個動作來定義按鈕的標題、其樣式，以及在按下按鈕時所要採取的動作：

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ =>
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`列舉可讓您將按鈕的樣式設定為下列其中一項：

- **預設值** -按鈕會是顯示警示時選取的預設按鈕。
- **取消** -此按鈕是警示的 [取消] 按鈕。
- **破壞性** -將按鈕醒目顯示為破壞性動作，例如刪除檔案。 目前，tvOS 會呈現具有紅色背景的破壞性按鈕。

`AddAction`方法會將指定的動作加入至 `UIAlertViewController` ，最後， `PresentViewController (alertController, true, null)` 方法會向使用者顯示指定的警示。

<a name="Adding-Text-Fields"></a>

## <a name="adding-text-fields"></a>加入文字欄位

除了將動作新增 (按鈕) 至警示之外，您還可以將文字欄位新增至警示，以允許使用者填入使用者識別碼和密碼等資訊：

[![警示中的文字欄位](alerts-images/alert02.png)](alerts-images/alert02.png#lightbox)

如果使用者選取文字欄位，則會顯示標準 tvOS 鍵盤，讓他們輸入欄位的值：

[![輸入文字](alerts-images/alert03.png)](alerts-images/alert03.png#lightbox)

下列程式碼會顯示一個「確定/取消」警示，內含輸入值的單一文字欄位：

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

`AddTextField`方法會將新的文字欄位加入至警示，您接著可以設定屬性，例如預留位置文字， (欄位為空白時所顯示的文字) 、預設文字值和鍵盤類型。 例如：

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

如此一來，我們就可以在稍後針對文字欄位的值採取動作，也可以使用下列程式碼來儲存的複本：

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

當使用者在文字欄位中輸入值之後，就可以使用 `field` 變數來存取該值。

<a name="Alert-View-Controller-Helper-Class"></a>

## <a name="alert-view-controller-helper-class"></a>警示查看控制器 Helper 類別

因為使用來顯示簡單的常見警示類型 `UIAlertViewController` 可能會產生相當多的重複程式碼，所以您可以使用協助程式類別來減少重複的程式碼數量。 例如：

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

使用這個類別，顯示和回應簡單警示的方式如下：

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

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文已討論過如何使用 `UIAlertController` ，在 tvOS 中向使用者顯示警示訊息。 首先，它會示範如何顯示簡單的警示和新增按鈕。 接下來，它會示範如何將文字欄位新增至警示。 最後，它會示範如何使用 helper 類別來減少顯示警示所需的重複程式碼數量。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)