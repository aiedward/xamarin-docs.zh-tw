---
title: 建立使用者介面物件
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 5229beb7a882049daf58d3a3e62da6fed25a1f48
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="creating-user-interface-objects"></a>建立使用者介面物件

Apple 群組關聯的部分功能"架構"，這等同於 Xamarin.iOS 命名空間。 `UIKit` 是包含適用於 iOS 的所有使用者介面控制項的命名空間。

每當您的程式碼需要參考使用者介面控制項，例如標籤或按鈕時，記得加入下列 using 陳述式：

```csharp
using UIKit;
```


本章所討論的所有控制項都都在 UIKit 的命名空間，而且每個使用者控制項類別名稱`UI`前置詞。

您可以編輯 UI 控制項和版面配置三種方式：

-  **[Xamarin iOS 設計工具](~/ios/user-interface/designer/index.md)** – 使用 Xamarin 的內建的配置設計工具設計螢幕。 按兩下 分鏡腳本或 XIB 檔案，以使用內建的設計工具來編輯。
-  **Xcode 介面產生器**– 將控制項拖曳到螢幕的配置與介面產生器。 在 Xcode 中開啟的分鏡腳本或 XIB 檔案，以滑鼠右鍵按一下中的檔案**方案板**，然後選擇**開啟 > Xcode 介面產生器**。
-  **使用 C#** – 控制項可以也以程式設計方式使用程式碼建構而且加入至檢視階層架構。

您可以加入 iOS 專案上按一下滑鼠右鍵，然後選擇新分鏡腳本和 XIB 檔**新增 > 新的檔案...**.

無論您使用的方法，控制屬性和事件仍然可以使用 C# 操縱您的應用程式邏輯中。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

若要開始建立您的使用者介面在 iOS 設計工具中，按兩下分鏡腳本檔案。 控制項可以拖曳至設計介面，從**工具箱**如下所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 [![](creating-ui-objects-images/image2b.png "工具箱的鍵台")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 [![](creating-ui-objects-images/image2b-vs.png "工具箱板-Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

在設計介面上選取控制項時**屬性板**會顯示該控制項的屬性。 **Widget > 識別 > 名稱**欄位，下列螢幕擷取畫面中已填入，當做*插座*名稱。 這是您可以在如何參考 C# 中的控制項：

 [![](creating-ui-objects-images/image3b.png "屬性的小工具板")](creating-ui-objects-images/image3b.png#lightbox)

使用 iOS 設計工具深入剖析，請參閱[iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode 介面產生器

如果您不熟悉使用介面產生器，請參閱 Apple[介面產生器](https://developer.apple.com/xcode/interface-builder/)文件。

若要在 Xcode 中開啟分鏡腳本，以滑鼠右鍵按一下存取分鏡腳本檔案的內容功能表並選擇以開啟**Xcode 介面產生器**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 [![](creating-ui-objects-images/imagexcode.png "分鏡腳本操作功能表： Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](creating-ui-objects-images/imagexcode-vs.png "分鏡腳本操作功能表： Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控制項可以拖曳至設計介面，從**物件程式庫**下圖說明：

 [![](creating-ui-objects-images/image5a.png "Xcode 物件程式庫")](creating-ui-objects-images/image5a.png#lightbox)

當您設計您的 UI 使用介面產生器，您必須建立**插座**針對每個您想要在 C# 中參考的控制項。 這是藉由開啟**助理編輯器**使用管理中心**編輯器**Xcode 工具列按鈕上的按鈕：

 [![](creating-ui-objects-images/image6a.png "小幫手編輯器 按鈕")](creating-ui-objects-images/image6a.png#lightbox)

按一下 在使用者介面物件。然後**控制項拖曳**到.h 檔案。 若要 * * 控制項拖曳 * *，按住 control 鍵然後按住透過使用者介面物件的建立輸出 （或動作）。 繼續按住 Control 鍵，而您將拖曳到標頭檔。 完成下面拖曳`@interface`定義。 藍線應該會出現標題插入輸出或輸出集合，如以下螢幕擷取畫面所示。

當您放開按一下將提示您提供的輸出，將程式碼中建立 C# 屬性可以參考使用的名稱：

 [![](creating-ui-objects-images/image8a.png "建立輸出")](creating-ui-objects-images/image8a.png#lightbox)

如需有關如何 Xcode 的介面產生器整合與 Visual Studio for Mac 的詳細資訊，請參閱[Xib 程式碼產生](~/ios/internals/xib-code-generation.md#generated)文件。

##  <a name="using-c"></a>使用 C#

如果您決定以程式設計方式建立使用者介面物件，使用 C# （在檢視或檢視的控制器，如範例中），請遵循下列步驟：

-  宣告類別層級的欄位，如使用者介面物件。 建立控制項本身一次，在`ViewDidLoad`例如。 然後可以在整個生命週期的方法檢視控制器 （例如參考的物件
`ViewWillAppear`)。
-  建立`CGRect`定義控制項 （其 X 和 Y 座標在畫面上，以及其寬度和高度） 的框架。 您必須確定您有`using CoreGraphics`這個指示詞。
-  呼叫建構函式來建立和指派控制項。
-  設定任何屬性或事件處理常式。
-  呼叫`Add()`將控制項新增至檢視階層架構。

以下是簡單的範例建立的`UILabel`檢視控制器中使用 C#:

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

## <a name="using-c-and-storyboards"></a>使用 C# 和分鏡腳本

當檢視控制器加入至設計介面時，會在專案中建立兩個對應 C# 檔案。 在此範例中，`ControlsViewController.cs`和`ControlsViewController.designer.cs`自動建立：

 [![](creating-ui-objects-images/image9b.png "ViewController 部分類別")](creating-ui-objects-images/image9b.png#lightbox)

`MainViewController.cs`檔案僅供*您的程式碼*。 這是 where`View`存留週期方法，例如`ViewDidLoad`和`ViewWillAppear`實作也可以新增您自己的屬性、 欄位和方法。

`ControlsViewController.designer.cs`產生的程式碼包含的部分類別。 當名稱設計上的控制項介面在 Visual Studio for Mac，或建立輸出或動作在 Xcode 中，對應的屬性或部分方法時，會加入至設計工具 (.designer.cs) 檔案。 下列程式碼顯示針對兩個按鈕和文字檢視中，產生的程式碼範例是其中一個按鈕也擁有`TouchUpInside`事件。

部分類別的這些項目會啟用您的程式碼參考的控制項，並回應在設計介面宣告的動作：

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

`designer.cs`檔案不應以手動方式編輯 – IDE (Visual Studio for Mac 或 Visual Studio) 負責分鏡腳本與保持同步。

當使用者介面物件會加入以程式設計方式至`View`或`ViewController`您具現化和管理您自己的物件參考，因此不需要任何的設計工具檔案。



## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
