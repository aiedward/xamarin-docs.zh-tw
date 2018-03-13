---
title: "使用警示"
description: "本文件涵蓋使用 UIAlertController Xamarin.tvOS 中對使用者顯示的警示訊息。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 6dabba30c5242d6e7e9ef42a4025f87826a5b89e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-alerts"></a>使用警示

_本文件涵蓋使用 UIAlertController Xamarin.tvOS 中對使用者顯示的警示訊息。_


如果您需要取得 tvOS 使用者注意或要求執行破壞性動作 （例如刪除檔案） 的權限，您可以呈現警示訊息使用`UIAlertViewController`:

[![](alerts-images/alert01.png "範例 UIAlertViewController")](alerts-images/alert01.png#lightbox)

如果除了顯示訊息，您可以加入按鈕和文字欄位，讓使用者回應動作，以及提供意見反應警示。

<a name="About-Alerts" />

## <a name="about-alerts"></a>關於警示

如前所述，警示可用來取得使用者注意並告知您的應用程式或要求意見反應的狀態。 警示必須出示標題，或者可以有一則訊息和一個或多個按鈕或文字欄位。

[![](alerts-images/alert04.png "範例警示")](alerts-images/alert04.png#lightbox)

Apple 已使用警示的下列建議：

- **警示盡量少用**-警示中斷使用者與應用程式的流程和中斷的使用者體驗，並在這種情況，應該只用於重要的情況下，例如錯誤通知、 應用程式內購買和破壞性動作。 
- **提供實用的選擇**-如果警示會提供選項給使用者，您應該確保每個選項提供重要資訊，並提供使用者取得有用的動作。

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>警示標題和訊息

Apple 已呈現警示的標題和選擇性訊息的下列建議：

- **使用 Multiword 標題**-警示的標題應該會收到跨清楚時仍保持簡單的情況的點。 單一文字標題很少會提供足夠的資訊。
- **使用不需要訊息的描述性標題**-可能的情況下，請考慮將警示的標題描述性足夠的選擇性訊息文字不是所需。 
- **讓訊息短，完整的句子**-選用的訊息是否需要取得的點之間的警示，保持越簡單越好並使其完整的句子與適當的大小寫和標點符號。

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>警示的按鈕

Apple 已加入警示中的按鈕的下列建議：

- **兩個按鈕的限制**-可能的情況下，限制為最多兩個按鈕的警示。 單一按鈕警示提供的資訊，但是沒有動作。 兩個按鈕的警示會提供簡單的是/否選擇的動作。
- **使用 Succinct，邏輯按鈕標題**-簡單清楚地描述按鈕的動作的一到兩個 word 按鈕標題的效果最佳。 如需詳細資訊，請參閱我們[處理按鈕](~/ios/tvos/user-interface/buttons.md)文件。
- **清楚地標記破壞性按鈕**-針對執行破壞性動作 （例如刪除檔案） 的按鈕清楚地標示它們與`UIAlertActionStyle.Destructive`樣式。

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>顯示警示

若要顯示警示，您建立的執行個體`UIAlertViewController`並設定加入動作 （按鈕），然後選取警示的樣式。 例如，下列程式碼會顯示 [確定] / [取消] 警示：

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

讓我們看看這段程式碼詳細資料中。 首先，我們會使用指定的標題和訊息建立新的警示：

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下來，我們想要在警示中顯示每個按鈕建立定義按鈕、 樣式和我們想要在按下按鈕時要採取的動作標題的動作：

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`列舉可讓您將按鈕的樣式設定為下列其中之一：

- **預設**-按鈕將會選取當警示顯示時的預設按鈕。
- **取消**-是警示的 [取消] 按鈕。
- **破壞性**-會破壞性動作，例如刪除檔案以反白顯示的按鈕。 目前，tvOS 呈現具有紅色背景破壞性的按鈕。

`AddAction`方法會加入至指定的動作`UIAlertViewController`最後`PresentViewController (alertController, true, null)`方法向使用者顯示給定的警示。

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>加入文字欄位

除了加入警示中的動作 （按鈕），您可以加入文字欄位，讓使用者以使用者識別碼和密碼等資訊填滿警示：

[![](alerts-images/alert02.png "文字欄位中警示")](alerts-images/alert02.png#lightbox)

如果使用者選取的文字欄位，將允許使用者輸入欄位的值顯示標準 tvOS 鍵盤：

[![](alerts-images/alert03.png "輸入文字")](alerts-images/alert03.png#lightbox)

下列程式碼會顯示 [確定] / [取消] 警示與單一的文字欄位的輸入值：

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

`AddTextField`方法會將新的文字欄位，您可以藉由設定屬性，例如預留位置文字 （空白欄位時顯示的文字）、 預設文字值和類型的鍵盤設定警示。 例如: 

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

讓我們可以稍後在文字欄位的值上執行，我們也會儲存一份使用下列程式碼：

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

使用者的文字欄位中輸入值之後，我們可以使用`field`變數來存取該值。

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>警示檢視控制器 Helper 類別

因為顯示常見的使用警示的簡單類型`UIAlertViewController`可能會導致許多重複的程式碼程式，您可用來減少重複的程式碼的協助程式類別。 例如: 

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

使用這個類別、 顯示和簡單的警示回應即可，如下所示：

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

這篇文章已涵蓋使用`UIAlertController`Xamarin.tvOS 中對使用者顯示的警示訊息。 首先，它會示範如何顯示一個簡單的警示，並加入按鈕。 接下來，它會示範如何將文字欄位加入至警示。 最後，它會示範如何使用協助程式類別，以減少重複顯示警示所需的程式碼數量。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
