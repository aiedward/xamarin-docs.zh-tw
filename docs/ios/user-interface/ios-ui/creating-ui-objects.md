---
title: Creating User Interface Objects in Xamarin.iOS
description: This document provides an overview of how to create a user interface in Xamarin.iOS. It discusses the iOS Designer, Xcode Interface Builder, C#, and storyboards.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 58a1fd68dda2216a62fe6f30cf61d6d2ec7d40d5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003663"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Creating User Interface Objects in Xamarin.iOS

Apple groups related pieces of functionality into “frameworks” which equate to Xamarin.iOS namespaces. `UIKit` is the namespace that contains all the user interface controls for iOS.

Whenever your code needs to reference a user interface control, such as a label or button, remember to include the following using statement:

```csharp
using UIKit;
```

All the controls discussed in this chapter are in the UIKit namespace, and each user control class name has the `UI` prefix.

You can edit UI controls and layouts in three ways:

- **[Xamarin iOS Designer](~/ios/user-interface/designer/index.md)** – Use Xamarin’s built-in layout designer to design screens. Double-click storyboard or XIB files to edit with the built-in designer.
- **Xcode Interface Builder** – Drag controls onto your screen layouts with Interface Builder. Open the storyboard or XIB file in Xcode by right-clicking the file in the **Solution Pad** and choosing **Open With > Xcode Interface Builder**.
- **Using C#** – Controls can also be programmatically constructed with code and added to the view hierarchy.

New Storyboard and XIB files can be added by right-clicking on an iOS project and choosing **Add > New File...** .

Whichever method you use, control properties and events can still be manipulated with C# in your application logic.

## <a name="using-xamarin-ios-designer"></a>Using Xamarin iOS Designer

To start creating your user interface in the iOS Designer, double-click on a storyboard file. Controls can be dragged onto the design surface from the **Toolbox** as illustrated below:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Toolbox Pad")](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Toolbox Pad - Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)

-----

When a control is selected on the design surface the **Properties Pad** will show the attributes for that control. The **Widget > Identity > Name** field, which is populated in the screenshot below, is used as the *Outlet* name. This is how you can reference the control in C#:

 [![](creating-ui-objects-images/image3b.png "Properties Widget Pad")](creating-ui-objects-images/image3b.png#lightbox)

如需使用 iOS 設計工具的深入探討，請參閱[Ios 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode Interface Builder

如果您不熟悉如何使用 Interface Builder，請參閱 Apple 的[Interface Builder](https://developer.apple.com/xcode/interface-builder/)檔。

若要在 Xcode 中開啟腳本，請以滑鼠右鍵按一下以存取分鏡腳本檔案的內容功能表，然後選擇 [使用**Xcode Interface Builder**開啟]：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控制項可以從**物件程式庫**拖曳至 Design Surface，如下所示：

 [![](creating-ui-objects-images/image5a.png "Xcode Object Library")](creating-ui-objects-images/image5a.png#lightbox)

當您使用 Interface Builder 設計 UI 時，您必須為想要在C#其中參考的每個控制項建立一個插座。 這是藉由使用 [Xcode] 工具列按鈕上的 [center**編輯器**] 按鈕開啟 [**助理編輯器**] 來完成：

 [![](creating-ui-objects-images/image6a.png "Assistant Editor button")](creating-ui-objects-images/image6a.png#lightbox)

按一下使用者介面物件;然後，**將控制項拖曳**到 .h 檔案中。 若要**控制拖曳**，請按住 control 鍵，然後按一下並按住您要為其建立輸出（或動作）的使用者介面物件。 當您拖曳至標頭檔時，請按住 Control 鍵。 完成 `@interface` 定義的拖曳。 藍色線應該會出現，並顯示 [插入插座] 或 [插座] 集合，如下列螢幕擷取畫面所示。

當您放開時，系統會提示您提供輸出的名稱，這將用來建立可在程式碼中C#參考的屬性：

 [![](creating-ui-objects-images/image8a.png "Creating an outlet")](creating-ui-objects-images/image8a.png#lightbox)

如需有關 Xcode 的 Interface Builder 如何與 Visual Studio for Mac 整合的詳細資訊，請參閱[Xib 程式碼產生](~/ios/internals/xib-code-generation.md#generated)檔。

## <a name="using-c"></a>使用 C \#

如果您決定使用C#以程式設計方式建立使用者介面物件（例如，在 View 或 view 控制器中），請遵循下列步驟：

- 宣告使用者介面物件的類別層級欄位。 在 `ViewDidLoad` 中建立控制項本身一次，例如。 然後可以在視圖控制器的整個生命週期方法中參考物件（例如，
`ViewWillAppear`)。
- 建立 `CGRect`，以定義控制項的框架（其在畫面上的 X 和 Y 座標，以及其寬度和高度）。 您必須確定您有此的 `using CoreGraphics` 指示詞。
- 呼叫此函式，以建立並指派控制項。
- 設定任何屬性或事件處理常式。
- 呼叫 `Add()` 將控制項加入至視圖階層。

以下是使用C#在視圖控制器中建立 `UILabel` 的簡單範例：

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>使用C#和分鏡腳本

當 View controller 新增至 Design Surface 時，會在專案C#中建立兩個對應的檔案。 在此範例中，`ControlsViewController.cs` 和 `ControlsViewController.designer.cs` 已自動建立：

 [![](creating-ui-objects-images/image9b.png "ViewController partial class")](creating-ui-objects-images/image9b.png#lightbox)

`ControlsViewController.cs` 檔案適用于您的*程式碼*。 這是執行 `View` 生命週期方法（例如 `ViewDidLoad` 和 `ViewWillAppear`）的位置，以及您可以加入自己的屬性、欄位和方法的位置。

`ControlsViewController.designer.cs` 是產生的程式碼，內含部分類別。 當您在 Visual Studio for Mac 中的設計介面上命名控制項，或在 Xcode 中建立輸出或動作時，會將對應的屬性（或部分方法）新增至設計工具（designer.cs）檔案。 下列程式碼顯示針對兩個按鈕和一個文字視圖產生的程式碼範例，其中一個按鈕也有 `TouchUpInside` 事件。

部分類別的這些元素可讓您的程式碼參考控制項，並回應設計介面上所宣告的動作：

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

不應該手動編輯 `designer.cs` 檔案– IDE （Visual Studio for Mac 或 Visual Studio）會負責讓它與腳本同步處理。

當以程式設計方式將使用者介面物件新增至 `View` 或 `ViewController` 時，您會自行具現化和管理物件參考，因此不需要任何設計工具檔案。

## <a name="related-links"></a>相關連結

- [Controls (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
