---
title: 使用 tvOS 在 Xamarin 中的警示
description: 本文件說明如何使用 Xamarin 的 tvOS 警示。 它討論顯示警示，新增的文字欄位及協助程式類別。
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 13c5ae3fac76ec1ec1a0ade135d5919403066226
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111280"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>使用 tvOS 在 Xamarin 中的警示

_這篇文章涵蓋使用 UIAlertController Xamarin.tvOS 中向使用者顯示警示訊息。_

如果您需要取得 tvOS 使用者注意或要求執行破壞性動作 （例如刪除檔案） 的權限，您可以呈現警示訊息使用`UIAlertViewController`:

[![](alerts-images/alert01.png "範例 UIAlertViewController")](alerts-images/alert01.png#lightbox)

如果除了顯示一則訊息，您可以將按鈕和文字欄位加入警示，以允許使用者回應動作，並提供意見反應。

<a name="About-Alerts" />

## <a name="about-alerts"></a>關於警示

如上所述，警示可取得使用者的注意力，並告知您的應用程式或要求意見反應的狀態。 警示必須出示標題，或者可以有一則訊息和一個或多個按鈕的文字欄位。

[![](alerts-images/alert04.png "範例警示")](alerts-images/alert04.png#lightbox)

Apple 已使用警示的下列建議：

- **警示盡量少用**-警示會中斷應用程式的使用者的流程中斷使用者經驗和此情況下，應該只用於重要的情況下，例如錯誤通知、 應用程式內購買和破壞性動作。 
- **提供有用的選擇**-如果警示會提供選項給使用者，您應該確保每個選項提供重要資訊，並提供有用的動作，才會為使用者。

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>警示標題和訊息

Apple 已呈現警示的標題和選擇性訊息的下列建議：

- **使用 Multiword 標題**-警示的標題應該取得清楚時仍保持簡單的情況之間的點。 單字標題很少會提供足夠的資訊。
- **使用不需要訊息的描述性標題**-可能的話，請考慮讓警示的標題描述性足夠不是選擇性的訊息文字所需。 
- **讓訊息簡短的完整句子**-選用的訊息是否需要取得跨警示點保持越簡單越好，讓完整的句子，使用適當的大小寫和標點符號。

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>警示按鈕

Apple 已加入警示中的按鈕的下列建議：

- **兩個按鈕的限制**-可能的情況下，限制為兩個按鈕的最大值警示。 單一按鈕警示提供的資訊，但是沒有任何動作。 兩個按鈕的警示會提供簡單的選擇是/否 」 的動作。
- **使用 Succinct，邏輯的按鈕標題**-簡單的一到兩個 word 按鈕的項目，清楚地描述按鈕的動作可獲得最佳效果。 如需詳細資訊，請參閱我們[處理按鈕](~/ios/tvos/user-interface/buttons.md)文件。
- **清楚標示破壞性按鈕**-針對執行破壞性動作 （例如刪除檔案） 的按鈕清楚地標記它們`UIAlertActionStyle.Destructive`樣式。

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>顯示警示

若要顯示警示，您建立的執行個體`UIAlertViewController`並設定它以新增動作 （按鈕），然後選取警示的樣式。 例如，下列程式碼會顯示確定/取消警示：

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

在 詳細資料，讓我們看看此程式碼。 首先，我們會使用指定的標題和訊息建立新的警示：

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下來，我們想要在警示中顯示每個按鈕建立定義的按鈕、 其樣式和我們想要在按下按鈕時要採取的動作標題的動作：

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`列舉可讓您將按鈕的樣式設定為下列其中之一：

- **預設**-按鈕會選取在出現警示時的預設按鈕。
- **取消**-是警示的 [取消] 按鈕。
- **破壞性**-會反白顯示做為破壞性動作，例如刪除檔案的按鈕。 目前，tvOS 呈現紅色背景破壞性的按鈕。

`AddAction`方法會加入至指定的動作`UIAlertViewController`最後`PresentViewController (alertController, true, null)`方法向使用者顯示指定的警示。

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>將文字欄位

除了新增警示的動作 （按鈕），您可以新增警示，讓使用者填入資訊，例如使用者識別碼與密碼的文字欄位：

[![](alerts-images/alert02.png "文字欄位中警示")](alerts-images/alert02.png#lightbox)

如果使用者選取的文字欄位，就將以便輸入欄位的值會顯示標準的 tvOS 鍵盤：

[![](alerts-images/alert03.png "輸入文字")](alerts-images/alert03.png#lightbox)

下列程式碼會顯示確定/取消警示與單一的文字欄位中輸入值：

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

`AddTextField`方法會將新的文字欄位加入的警示，然後您可以藉由設定屬性，例如預留位置文字 （空白欄位，就會出現的文字）、 預設的文字值和類型的鍵盤設定。 例如: 

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

讓我們可於稍後在文字欄位的值，我們也會儲存一份使用下列程式碼：

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

## <a name="alert-view-controller-helper-class"></a>警示檢視控制器協助程式類別

因為顯示簡單、 常見的類型，使用警示的`UIAlertViewController`可能會導致一堆重複的程式碼，您可以使用協助程式類別，以減少重複的程式碼數量。 例如: 

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

使用這個類別、 顯示和回應簡單警示就可以完成，如下所示：

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

這篇文章涵蓋使用`UIAlertController`Xamarin.tvOS 中向使用者顯示警示訊息。 首先，它會示範如何顯示一個簡單的警示，並加入按鈕。 接下來，它會示範如何將文字欄位新增至警示。 最後，它會示範如何使用協助程式類別，以減少重複成顯示警示所需的程式碼數量。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
