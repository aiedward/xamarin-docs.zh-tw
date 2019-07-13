---
title: 在 Xamarin.iOS 中建立使用者介面物件
description: 本文件提供如何在 Xamarin.iOS 中建立使用者介面的概觀。 它討論 iOS 設計工具，Xcode 介面產生器中， C#，和分鏡腳本。
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a80bf876e239e1788a371a1f09d36d73247d4611
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865006"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin.iOS 中建立使用者介面物件

Apple 群組關聯的部分功能重構成 「 架構 」，這等同於 Xamarin.iOS 命名空間。 `UIKit` 是包含適用於 iOS 的所有使用者介面控制項的命名空間。

每當您的程式碼需要參考使用者介面控制項，例如標籤或按鈕時，請記得加入下列 using 陳述式：

```csharp
using UIKit;
```

這一章所述的所有控制項都是在 UIKit 的命名空間，且每個使用者控制項類別名稱`UI`前置詞。

您可以編輯 UI 控制項和版面配置，以三種方式：

-  **[Xamarin iOS 設計工具](~/ios/user-interface/designer/index.md)** -使用 Xamarin 的內建的版面配置設計工具設計螢幕。 按兩下分鏡腳本] 或 [使用內建的設計工具編輯 XIB 檔案。
-  **Xcode 的 Interface Builder** – 將控制項拖曳到您的畫面版面配置的 Interface Builder。 在 Xcode 中開啟的分鏡腳本或 XIB 檔案中的檔案上按一下滑鼠右鍵**Solution Pad** ，然後選擇**開啟 > Xcode Interface Builder**。
-  **使用C#**  – 控制項可以也以程式設計方式建構程式碼並加入至檢視階層。

您可以加入 iOS 專案上按一下滑鼠右鍵，然後選擇新的分鏡腳本和 XIB 檔案**新增 > 新增檔案...** .

無論您使用，哪種方法的控制項屬性和事件仍然可操作與C#應用程式邏輯中。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

若要開始在 iOS 設計工具中建立您的使用者介面，請按兩下分鏡腳本檔案。 可以在控制項拖曳至設計介面，從**工具箱**如下所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Toolbox Pad")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Toolbox Pad-Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

當在設計介面上選取控制項**Properties Pad**會顯示該控制項的屬性。 **小工具 > 身分識別 > 名稱**欄位，已填入以下螢幕擷取畫面中，做*插座*名稱。 這是您如何可以參考中的控制項C#:

 [![](creating-ui-objects-images/image3b.png "屬性的小工具板")](creating-ui-objects-images/image3b.png#lightbox)

使用 iOS 設計工具深入了解，請參閱[iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode 的 Interface Builder

如果您不熟悉如何使用介面產生器，請參閱 Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/)文件。

若要在 Xcode 中開啟分鏡腳本，以滑鼠右鍵按一下存取分鏡腳本檔案的內容功能表並選擇開立**Xcode Interface Builder**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "分鏡腳本內容功能表中 Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "分鏡腳本內容功能表中 Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

可以在控制項拖曳至設計介面，從**物件程式庫**如下所示：

 [![](creating-ui-objects-images/image5a.png "Xcode 物件程式庫")](creating-ui-objects-images/image5a.png#lightbox)

當您設計的 Interface Builder，您必須建立 UI**插座**您想要參考中的每個控制項的C#。 這是藉由開啟**輔助編輯器**使用管理中心**編輯器**Xcode 工具列按鈕上的按鈕：

 [![](creating-ui-objects-images/image6a.png "助理編輯器 按鈕")](creating-ui-objects-images/image6a.png#lightbox)

按一下使用者介面物件;然後**控制項拖曳**.h 檔案。 若要**控制項拖曳**、 按住 control 鍵，然後按一下並按住您建立的輸出 （或動作） 的使用者介面物件。 繼續按住 Control 鍵，您將項目拖曳到標頭檔。 完成拖曳下方`@interface`定義。 藍線應該會出現標題插入輸出或輸出集合，如以下螢幕擷取畫面所示。

當您釋放將提示您提供的輸出，將用來建立名稱按一下C#可以在程式碼中參考的屬性：

 [![](creating-ui-objects-images/image8a.png "建立輸出")](creating-ui-objects-images/image8a.png#lightbox)

如需有關如何 Xcode 的 Interface Builder 整合 Visual Studio for Mac 的詳細資訊，請參閱[Xib 程式碼產生](~/ios/internals/xib-code-generation.md#generated)文件。

## <a name="using-c"></a>使用C#

如果您決定以程式設計方式建立使用者介面物件使用C#（在檢視或檢視控制器，例如），請遵循下列步驟：

-  宣告類別層級的欄位，為使用者介面物件。 建立控制項本身一次，在`ViewDidLoad`例如。 然後在整個生命週期方法，將檢視控制器 （例如參考的物件。
`ViewWillAppear`)。
-  建立`CGRect`定義控制項 （其 X 和 Y 座標上的畫面，以及其寬度和高度） 的框架。 您必須確定您有`using CoreGraphics`這個指示詞。
-  呼叫建構函式來建立及指派控制項。
-  設定任何屬性或事件處理常式。
-  呼叫`Add()`將控制項加入至檢視階層。

以下是建立一個簡單範例`UILabel`在檢視控制器中使用C#:

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

當檢視控制器加入至設計介面中，兩個對應C#專案中建立檔案。 在此範例中，`ControlsViewController.cs`和`ControlsViewController.designer.cs`自動建立：

 [![](creating-ui-objects-images/image9b.png "ViewController 部分類別")](creating-ui-objects-images/image9b.png#lightbox)

`MainViewController.cs`檔案供*您的程式碼*。 這正是`View`生命週期方法，如`ViewDidLoad`和`ViewWillAppear`實作，您可以在其中加入您自己的屬性、 欄位和方法。

`ControlsViewController.designer.cs`產生的程式碼包含的部分類別。 當名稱上設計的控制項呈現在 Visual Studio for Mac，或 Xcode、 對應的屬性或部分方法中建立的輸出或動作時，會加入至設計工具 (designer.cs) 檔案。 下列程式碼顯示針對兩個按鈕和文字檢視中，產生的程式碼範例，其中一個按鈕也有`TouchUpInside`事件。

部分類別的這些項目可讓您的程式碼參考的控制項並回應在設計介面宣告的動作：

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

`designer.cs`檔案不應以手動方式編輯-IDE (Visual Studio for Mac 或 Visual Studio) 負責讓它保持在同步處理使用分鏡腳本。

當使用者介面物件會加入以程式設計方式`View`或`ViewController`具現化，並自行管理的物件參考，因此不需要任何的設計工具檔案。



## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/monotouch/Controls/)
