---
title: 在 Xamarin 中建立使用者介面物件
description: '本檔提供如何在 Xamarin 中建立使用者介面的總覽。 其中討論 iOS 設計工具、Xcode Interface Builder、c # 和分鏡腳本。'
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 729289c1764746f9777ef3d720e77865c9a71389
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937250"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin 中建立使用者介面物件

Apple 將相關的功能片段分組為「架構」，這會等同于 Xamarin. iOS 命名空間。 `UIKit`是包含所有 iOS 使用者介面控制項的命名空間。

每當您的程式碼需要參考使用者介面控制項（例如標籤或按鈕）時，請記得包含下列 using 語句：

```csharp
using UIKit;
```

本章所討論的所有控制項都是在 UIKit 命名空間中，而且每個使用者控制項類別名稱都有 `UI` 前置詞。

您可以透過三種方式來編輯 UI 控制項和版面配置：

- **[Xamarin IOS 設計](~/ios/user-interface/designer/index.md)** 工具–使用 xamarin 的內建版面配置設計工具來設計畫面。 按兩下 [分鏡腳本] 或 [XIB 檔案]，以使用內建的設計工具進行編輯。
- **Xcode Interface Builder** -使用 Interface Builder 將控制項拖曳至您的螢幕佈局。 在 [ **Solution Pad**中的檔案上按一下滑鼠右鍵，然後選擇 [**以 > Xcode Interface Builder 開啟**]，以開啟 Xcode 中的腳本或 XIB 檔案。
- **使用 c #** –控制項也可以透過程式碼以程式設計方式來建立，並加入至視圖階層。

以滑鼠右鍵按一下 iOS 專案，然後選擇 [**加入 > 新增**檔案 ...]，即可新增分鏡腳本和 XIB 檔案。

無論您使用哪種方法，控制項屬性和事件仍然可以在應用程式邏輯中使用 c # 來操作。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

若要開始在 iOS 設計工具中建立使用者介面，請按兩下分鏡腳本檔案。 您可以從 [**工具箱**] 將控制項拖曳至設計介面，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 [![工具箱面板](creating-ui-objects-images/image2b.png)](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 [![工具箱 Pad-視覺效果 Studio](creating-ui-objects-images/image2b-vs.png)](creating-ui-objects-images/image2b.png#lightbox)

-----

在設計介面上選取控制項時， **Properties Pad**會顯示該控制項的屬性。 [**識別 > 名稱**] 欄位 > 的 Widget，會在下方的螢幕擷取畫面中填入，做為*輸出*的名稱。 這就是您可以在 c # 中參考控制項的方式：

 [![屬性 Widget 面板](creating-ui-objects-images/image3b.png)](creating-ui-objects-images/image3b.png#lightbox)

如需使用 iOS 設計工具的深入探討，請參閱[Ios 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode Interface Builder

如果您不熟悉如何使用 Interface Builder，請參閱 Apple 的[Interface Builder](https://developer.apple.com/xcode/interface-builder/)檔。

若要在 Xcode 中開啟腳本，請以滑鼠右鍵按一下以存取分鏡腳本檔案的內容功能表，然後選擇 [使用**Xcode Interface Builder**開啟]：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 [![分鏡腳本內容功能表-Xcode](creating-ui-objects-images/imagexcode.png)](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![分鏡腳本內容功能表-Xcode](creating-ui-objects-images/imagexcode-vs.png)](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控制項可以從**物件程式庫**拖曳至 Design Surface，如下所示：

 [![Xcode 物件程式庫](creating-ui-objects-images/image5a.png)](creating-ui-objects-images/image5a.png#lightbox)

當您使用 Interface Builder 設計 UI 時，您必須為想要在 c # 中參考的每個控制項建立一個**插座**。 這是藉由使用 [Xcode] 工具列按鈕上的 [center**編輯器**] 按鈕開啟 [**助理編輯器**] 來完成：

 [![[助理編輯器] 按鈕](creating-ui-objects-images/image6a.png)](creating-ui-objects-images/image6a.png#lightbox)

按一下使用者介面物件;然後，**將控制項拖曳**到 .h 檔案中。 若要**控制拖曳**，請按住 control 鍵，然後按一下並按住您要為其建立輸出（或動作）的使用者介面物件。 當您拖曳至標頭檔時，請按住 Control 鍵。 完成在定義下拖曳 `@interface` 。 藍色線應該會出現，並顯示 [插入插座] 或 [插座] 集合，如下列螢幕擷取畫面所示。

當您放開時，系統會提示您提供輸出的名稱，這將用來建立可在程式碼中參考的 c # 屬性：

 [![建立插座](creating-ui-objects-images/image8a.png)](creating-ui-objects-images/image8a.png#lightbox)

如需有關 Xcode 的 Interface Builder 如何與 Visual Studio for Mac 整合的詳細資訊，請參閱[Xib 程式碼產生](~/ios/internals/xib-code-generation.md#generated)檔。

## <a name="using-c"></a>使用 C\#

如果您決定使用 c # 以程式設計方式建立使用者介面物件（例如，在 View 或 View 控制器中），請遵循下列步驟：

- 宣告使用者介面物件的類別層級欄位。 在中建立控制項本身一次，例如 `ViewDidLoad` 。 然後可以在視圖控制器的整個生命週期方法中參考物件（例如，
`ViewWillAppear`).
- 建立 `CGRect` ，以定義控制項的框架（其在畫面上的 X 和 Y 座標，以及其寬度和高度）。 您需要確定您有此的指示詞 `using CoreGraphics` 。
- 呼叫此函式，以建立並指派控制項。
- 設定任何屬性或事件處理常式。
- 呼叫 `Add()` ，將控制項加入至視圖階層。

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

當 View controller 新增至 Design Surface 時，會在專案中建立兩個對應的 c # 檔案。 在此範例中 `ControlsViewController.cs` ， `ControlsViewController.designer.cs` 已自動建立和：

 [![ViewController 部分類別](creating-ui-objects-images/image9b.png)](creating-ui-objects-images/image9b.png#lightbox)

檔案適用 `ControlsViewController.cs` 于您的*程式碼*。 這就是開發 `View` 週期方法（例如和）的位置 `ViewDidLoad` `ViewWillAppear` ，您可以在其中新增自己的屬性、欄位和方法。

`ControlsViewController.designer.cs`是產生的程式碼，內含部分類別。 當您在 Visual Studio for Mac 中的設計介面上命名控制項，或在 Xcode 中建立輸出或動作時，會將對應的屬性（或部分方法）新增至設計工具（designer.cs）檔案。 下列程式碼顯示針對兩個按鈕和一個文字視圖產生的程式碼範例，其中一個按鈕也有 `TouchUpInside` 事件。

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

檔案 `designer.cs` 不應手動編輯– IDE （Visual Studio for Mac 或 Visual Studio）會負責讓它與腳本同步處理。

當以程式設計方式將使用者介面物件新增至 `View` 或時 `ViewController` ，您會自行具現化和管理物件參考，因此不需要任何設計工具檔案。

## <a name="related-links"></a>相關連結

- [控制項（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
