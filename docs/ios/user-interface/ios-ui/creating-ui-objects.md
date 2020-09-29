---
title: 在 Xamarin 中建立消費者介面物件
description: '本檔概述如何在 Xamarin 中建立使用者介面。 其中討論 iOS 設計工具、Xcode Interface Builder、c # 和分鏡腳本。'
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 5b1bd4a07f9df13bff517db28715d985fc33e322
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430257"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin 中建立消費者介面物件

Apple 將相關的功能片段分組為「架構」，其等同于 Xamarin. iOS 命名空間。 `UIKit` 是包含 iOS 所有使用者介面控制項的命名空間。

每當您的程式碼需要參考使用者介面控制項（例如標籤或按鈕）時，請記得包含下列 using 語句：

```csharp
using UIKit;
```

本章中討論的所有控制項都在 UIKit 命名空間中，而每個使用者控制項類別名稱都有 `UI` 前置詞。

您可以透過三種方式來編輯 UI 控制項和版面配置：

- **[Xamarin IOS 設計](~/ios/user-interface/designer/index.md)** 工具–使用 xamarin 的內建版面配置設計工具來設計螢幕。 按兩下 [分鏡腳本] 或 [XIB 檔案]，以使用內建設計工具進行編輯。
- **Xcode Interface Builder** –將控制項拖曳到 Interface Builder 的螢幕佈局。 以滑鼠右鍵按一下 [ **Solution Pad** 中的檔案，然後選擇 [ **開啟方式] > Xcode Interface Builder**，開啟 Xcode 中的腳本或 XIB 檔案。
- 您也可以**使用 c #** ，以程式設計方式使用程式碼來建立控制項，並將其加入至 view 階層。

以滑鼠右鍵按一下 iOS 專案，然後選擇 [ **加入 > 新**檔案 ...]，即可加入新的腳本和 XIB 檔案。

無論您使用哪種方法，您仍然可以在應用程式邏輯中使用 c # 來操作控制項屬性和事件。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

若要開始在 iOS 設計工具中建立使用者介面，請按兩下分鏡腳本檔案。 您可以從 [ **工具箱** ] 將控制項拖曳至設計介面，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 [![工具箱面板](creating-ui-objects-images/image2b.png)](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 [![工具箱面板-視覺效果 Studio](creating-ui-objects-images/image2b-vs.png)](creating-ui-objects-images/image2b.png#lightbox)

-----

在設計介面上選取控制項時， **Properties Pad** 會顯示該控制項的屬性。 **小工具 > 身分識別 > 名稱**欄位，在以下的螢幕擷取畫面中填入，可作為*輸出*的名稱。 以下是您可以在 c # 中參考控制項的方式：

 [![屬性 Widget 面板](creating-ui-objects-images/image3b.png)](creating-ui-objects-images/image3b.png#lightbox)

若要深入瞭解如何使用 iOS 設計工具，請參閱《 [Ios 設計](~/ios/user-interface/designer/introduction.md) 工具指南》的簡介。

## <a name="using-xcode-interface-builder"></a>使用 Xcode Interface Builder

如果您不熟悉如何使用 Interface Builder，請參閱 Apple 的 [Interface Builder](https://developer.apple.com/xcode/interface-builder/) 檔。

若要在 Xcode 中開啟分鏡腳本，請以滑鼠右鍵按一下以存取分鏡腳本檔案的內容功能表，然後選擇 [使用 **Xcode Interface Builder**開啟]：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 [![分鏡腳本內容功能表-Xcode](creating-ui-objects-images/imagexcode.png)](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![分鏡腳本內容功能表-Xcode](creating-ui-objects-images/imagexcode-vs.png)](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

您可以將控制項從 **物件程式庫** 拖曳至 Design Surface，如下所示：

 [![Xcode 物件程式庫](creating-ui-objects-images/image5a.png)](creating-ui-objects-images/image5a.png#lightbox)

當您使用 Interface Builder 來設計 UI 時，您必須為每個想要在 c # 中參考的控制項建立一個 **輸出** 。 這是藉由使用 [Xcode] 工具列按鈕上的 [中間**編輯器**] 按鈕開啟 [**助理編輯器**] 來完成的：

 [![助理編輯器按鈕](creating-ui-objects-images/image6a.png)](creating-ui-objects-images/image6a.png#lightbox)

按一下使用者介面物件;然後， **將控制項拖曳** 到 .h 檔案中。 若要 **控制拖曳**，請按住 ctrl 鍵，然後按一下您要建立其輸出 (或動作) 的使用者介面物件。 當您拖曳至標頭檔時，請按住 Ctrl 鍵。 完成定義下方的拖曳 `@interface` 。 藍色線應該會以標題插入插座或電源線集合出現，如下列螢幕擷取畫面所示。

當您放開時，系統會提示您提供輸出的名稱，此名稱將用來建立可在程式碼中參考的 c # 屬性：

 [![建立輸出](creating-ui-objects-images/image8a.png)](creating-ui-objects-images/image8a.png#lightbox)

如需有關 Xcode 的 Interface Builder 如何與 Visual Studio for Mac 整合的詳細資訊，請參閱 [Xib 程式碼產生](~/ios/internals/xib-code-generation.md#generated) 檔。

## <a name="using-c"></a>使用 C\#

如果您決定以程式設計方式使用 c # (在視圖或視圖控制器中建立使用者介面物件（例如) ），請遵循下列步驟：

- 宣告使用者介面物件的類別層級欄位。 例如，在中建立控制項一次 `ViewDidLoad` 。 然後可以在 View Controller 的整個生命週期方法中參考物件 (例如
`ViewWillAppear`).
- 建立 `CGRect` ，它會定義控制項的框架 (其在畫面上的 X 和 Y 座標，以及其寬度和高度) 。 您將需要確定您有 `using CoreGraphics` 這個的指示詞。
- 呼叫此函式來建立並指派控制項。
- 設定任何屬性或事件處理常式。
- 呼叫 `Add()` 以將控制項加入至視圖階層。

以下是 `UILabel` 使用 c # 在視圖控制器中建立的簡單範例：

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

<a name="partial_classes"></a>

## <a name="using-c-and-storyboards"></a>使用 c # 和分鏡腳本

將 View controller 新增至 Design Surface 時，會在專案中建立兩個對應的 c # 檔案。 在此範例中，已 `ControlsViewController.cs` `ControlsViewController.designer.cs` 自動建立並：

 [![ViewController 部分類別](creating-ui-objects-images/image9b.png)](creating-ui-objects-images/image9b.png#lightbox)

檔案適用 `ControlsViewController.cs` 于您的 *程式碼*。 這是 `View` 生命週期方法（例如和）的 `ViewDidLoad` 執行位置 `ViewWillAppear` ，而您可以在其中加入自己的屬性、欄位和方法。

`ControlsViewController.designer.cs`會產生包含部分類別的程式碼。 當您在設計介面上將控制項命名為 Visual Studio for Mac，或在 Xcode 中建立輸出或動作時，會將對應的屬性或部分方法加入至設計工具 (designer.cs) 檔。 下列程式碼顯示針對兩個按鈕和文字視圖產生的程式碼範例，其中一個按鈕也有 `TouchUpInside` 事件。

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

檔案 `designer.cs` 不應以手動方式編輯-IDE (Visual Studio for Mac 或 Visual Studio) 負責保持與腳本同步。

當使用者介面物件以程式設計方式加入至 `View` 或時 `ViewController` ，您可以自行具現化和管理物件參考，因此不需要設計工具檔。

## <a name="related-links"></a>相關連結

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)