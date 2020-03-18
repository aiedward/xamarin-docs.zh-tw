---
title: 在 Xamarin. Mac 中使用分鏡腳本
description: 本檔說明如何在 Xamarin 中使用分鏡腳本，檢查如何從程式碼、視圖控制器生命週期、回應者鏈、segue、視窗控制器、手勢辨識器等等載入它們。
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 06774d13cf053b661dd0b2d24b7df0c0b767b4db
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304909"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>在 Xamarin. Mac 中使用分鏡腳本

分鏡腳本會定義給定應用程式的所有 UI，並細分為其視圖控制器的功能總覽。 在 Xcode 的 Interface Builder 中，每個控制器都存在於自己的場景中。

[在 Xcode 的 Interface Builder 中 ![分鏡腳本](indepth-images/intro01.png)](indepth-images/intro01.png#lightbox)

分鏡腳本是資源檔（具有 `.storyboard`的延伸模組），會在編譯和出貨時包含在 Xamarin. Mac 應用程式的組合中。 若要為您的應用程式定義起始分鏡腳本，請編輯它的 `Info.plist` 檔案，然後從下拉式方塊中選取**主要介面**： 

[![plist 編輯器](indepth-images/sb01.png)](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>從程式碼載入

有時候您可能需要從程式碼載入特定的腳本，並手動建立視圖控制器。 您可以使用下列程式碼來執行此動作：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName` 會使用已包含在應用程式套件組合中的指定名稱來載入分鏡腳本檔案。 `InstantiateControllerWithIdentifier` 會使用指定的身分識別來建立 View 控制器的實例。 設計 UI 時，您會在 Xcode 的 Interface Builder 中設定身分識別：

[![設定分鏡腳本識別碼](indepth-images/sb02.png)](indepth-images/sb02.png#lightbox)

（選擇性）您可以使用 `InstantiateInitialController` 方法，載入已在 Interface Builder 中指派初始控制器的視圖控制器：

[設定初始控制器 ![](indepth-images/sb03.png)](indepth-images/sb03.png#lightbox)

它是由分鏡腳本**進入點**和上方的 [開啟結束] 箭號標記。

<a name="View-Controllers" />

## <a name="view-controllers"></a>視圖控制器

視圖控制器會定義 Mac 應用程式內的特定資訊視圖與提供該資訊的資料模型之間的關聯性。 分鏡腳本中的每個最上層場景都代表 Xamarin 應用程式代碼中的一個 View Controller。

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>View Controller 生命週期

`NSViewController` 類別中已加入數個新方法，以支援 macOS 中的分鏡腳本。 最重要的是，下列方法可用來回應指定視圖控制器所控制之視圖的生命週期：

- `ViewDidLoad`-從分鏡腳本檔案載入視圖時，會呼叫這個方法。
- `ViewWillAppear`-此方法只會在畫面上顯示視圖之前呼叫。
- `ViewDidAppear`-在畫面上顯示視圖之後，會直接呼叫此方法。
- `ViewWillDisappear`-此方法只會在從螢幕中移除視圖之前呼叫。
- `ViewDidDisappear`-從螢幕中移除視圖之後，會直接呼叫此方法。
- `UpdateViewConstraints`-當定義視圖自動版面配置位置和大小的條件約束需要更新時，會呼叫這個方法。
- `ViewWillLayout`-此方法只會在此視圖的子檢視于螢幕上配置之前呼叫。
- `ViewDidLayout`-在畫面上配置子檢視之後，就會直接呼叫此方法。

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>回應者鏈

此外，`NSViewControllers` 現在是視窗_回應者鏈_的一部分：

[![回應者鏈](indepth-images/vc01.png)](indepth-images/vc01.png#lightbox)

如此一來，它們就可以接收和回應事件，例如剪下、複製和貼上功能表項目選項。 只有在 macOS Sierra （10.12）和更新版本上執行的應用程式，才會進行此自動查看控制器的連線。

<a name="Containment" />

### <a name="containment"></a>內含項目

在分鏡腳本中，視圖控制器（例如 [分割視圖控制器] 和 [索引標籤視圖控制器]）現在_可以執行內含_專案，讓它們可以「包含」其他子視圖控制器：

[![View Controller 內含專案的範例](indepth-images/vc02.png)](indepth-images/vc02.png#lightbox)

子視圖控制器包含方法和屬性，可將它們系結回到其父視圖控制器，並使用在螢幕上顯示和移除視圖。

MacOS 內建的所有容器視圖控制器都具有特定的版面配置，Apple 建議您在建立自己的自訂容器視圖控制器時遵循下列各內容：

[![視圖控制器版面配置](indepth-images/vc03.png)](indepth-images/vc03.png#lightbox)

集合視圖控制器包含集合視圖專案的陣列，其中每一個都包含一個或多個包含其自有視圖的視圖控制器。

<a name="Segues" />

## <a name="segues"></a>Segue

Segue 提供定義應用程式 UI 的所有場景之間的關聯性。 如果您很熟悉如何在 iOS 中使用分鏡腳本，您知道 Segue for iOS 通常會定義全螢幕流覽之間的轉換。 這不同于 macOS, Segue 通常會定義「[內含](#Containment)專案」, 其中一個場景是父場景的子系。

在 macOS 中，大部分的應用程式通常會使用分割視圖和索引標籤等 UI 元素，在同一個視窗內將其 views 群組在一起。 不同于 iOS，因為實體顯示空間有限，所以必須在畫面上轉換 views。

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>簡報 Segue

針對內含專案 macOS 的喜好傾向，在某些情況下會使用_展示 segue_ ，例如強制回應視窗、工作表視圖和 Popovers。 macOS 提供下列內建的 segue 類型：

- **顯示**-將 Segue 的目標顯示為非強制回應視窗。 例如，使用這種類型的 Segue，在您的應用程式中顯示文件視窗的另一個實例。
- 強制**回應-將**Segue 的目標呈現為強制回應視窗。 例如，您可以使用這種類型的 Segue 來呈現應用程式的 [喜好設定] 視窗。
- **工作表**-將 Segue 的目標呈現為附加至父視窗的工作表。 例如，使用這種類型的 segue 來呈現「尋找和取代」工作表。
- **Popover** -在 Popover 視窗中顯示 Segue 的目標。 例如，當使用者按一下 UI 元素時，使用此 Segue 類型來呈現選項。
- **Custom** -使用開發人員所定義的自訂 Segue 類型，呈現 Segue 的目標。 如需詳細資訊，請參閱下面的 <<c0>建立自訂 segue一節。

使用 [簡報 Segue] 時，您可以覆寫父視圖控制器的 `PrepareForSegue` 方法以進行展示，以初始化和變數，並將任何資料提供給呈現的視圖控制器。

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>觸發的 Segue

觸發的 Segue 可讓您在 Interface Builder 中指定命名的 Segue （透過其**Identifier**屬性），並讓它們由事件觸發，例如使用者按一下按鈕或在程式碼中呼叫 `PerformSegue` 方法：

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

當您配置應用程式的 UI 時，Segue 識別碼會定義在 Xcode 的 Interface Builder 內：

[![輸入 Segue 名稱](indepth-images/sg02.png)](indepth-images/sg02.png#lightbox)

在做為 Segue 來源的 View Controller 中，您應該覆寫 `PrepareForSegue` 方法，並在執行 Segue 之前執行任何需要的初始化工作，並顯示指定的 View Controller：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

（選擇性）您可以覆寫 `ShouldPerformSegue` 方法，並控制是否實際透過程式C#代碼執行 Segue。 若為手動呈現的 View controller，請呼叫其 `DismissController` 方法，以在不再需要時，將其從顯示中移除。

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>建立自訂 Segue

有時候，您的應用程式可能需要在 macOS 中定義的內建 Segue 不提供的 Segue 類型。 如果是這種情況，您可以建立自訂 Segue，在配置應用程式的 UI 時，可以在 Xcode 的 Interface Builder 中加以指派。

例如，若要建立新的 Segue 類型來取代視窗中目前的 View Controller （而不是在新視窗中開啟目標場景），我們可以使用下列程式碼：

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

這裡有幾個要注意的事項：

- 我們會使用 `Register` 屬性，將此類別公開至目標-C/macOS。
- 我們會覆寫 `Perform` 方法，以實際執行自訂 Segue 的動作。
- 我們會使用 Segue 的目標（目的地）所定義的控制器來取代視窗的 `ContentViewController` 控制器。
- 我們會使用 `RemoveFromParentViewController` 方法來移除原始的視圖控制器，以釋放記憶體。

若要在 Xcode 的 Interface Builder 中使用這個新的 Segue 類型，我們必須先編譯應用程式，然後切換到 Xcode，然後在兩個場景之間新增 Segue。 將**樣式**設為**Custom** ，並將**Segue 類別**設定為 `ReplaceViewSegue` （我們的自訂 Segue 類別的名稱）：

[![設定 Segue 類別](indepth-images/sg01.png)](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>視窗控制器

視窗控制器包含和控制您的 macOS 應用程式可以建立的不同視窗類型。 在分鏡腳本中，它們具有下列功能：

1. 他們必須提供內容視圖控制器。 這會是子視窗所擁有的相同內容視圖控制器。
2. `Storyboard` 屬性將包含載入視窗控制器的分鏡腳本，否則 `null` （如果未從分鏡腳本載入）。
3. 您可以呼叫 `DismissController` 方法關閉指定的視窗，並將它從 view 中移除。

就像視圖控制器一樣，視窗控制器會執行 `PerformSegue`、`PrepareForSegue` 和 `ShouldPerformSegue` 方法，而且可用來做為 Segue 作業的來源。

視窗控制器負責 macOS 應用程式的下列功能：

- 他們會管理特定的視窗。
- 他們會管理視窗的標題列和工具列（如果有的話）。
- 他們會管理內容視圖控制器，以顯示視窗的內容。

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>手勢辨識器

MacOS 的手勢辨識器與 iOS 中的對應專案幾乎完全相同，可讓開發人員輕鬆地將筆勢（例如按一下滑鼠按鍵）新增至應用程式 UI 中的元素。

不過，iOS 中的手勢取決於應用程式的設計（例如透過兩個手指來點擊畫面），而 macOS 中的大部分手勢都是由硬體決定。

藉由使用手勢辨識器，您可以大幅減少將自訂互動新增至 UI 中的專案所需的程式碼數量。 因為它們可以自動在按兩下和按下滑鼠，所以請按一下並拖曳事件等等。

在使用分鏡腳本時，您應該使用手勢辨識器來處理使用者輸入事件，而不是覆寫 View Controller 中的 `MouseDown` 事件。

下列筆勢辨識器可在 macOS 中使用：

- `NSClickGestureRecognizer`-向下和向上註冊事件。
- `NSPanGestureRecognizer`-向下註冊滑鼠按鍵、拖曳和釋放事件。
- `NSPressGestureRecognizer`-針對指定的時間量事件，將滑鼠按鍵向下註冊。
- `NSMagnificationGestureRecognizer`-從軌跡板硬體註冊縮放事件。
- `NSRotationGestureRecognizer`-從軌跡板硬體註冊輪替事件。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用分鏡腳本參考

「分鏡腳本參考」可讓您進行大型、複雜的分鏡腳本設計，並將其分成較小的腳本，以從原始參考，進而移除複雜度，並讓產生的個別分鏡腳本更容易設計和維護。

此外，分鏡腳本參考可以將_錨點_提供給同一個分鏡腳本中的另一個場景，或不同的場景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>參考外部分鏡腳本

若要加入外部分鏡腳本的參考，請執行下列動作：

1. 在 **方案總管**中，以滑鼠右鍵按一下專案名稱，然後**選取** **新增 > 新**檔案 > **Mac** > **分**鏡腳本。 輸入新分鏡腳本的**名稱**，然後按一下 [**新增**] 按鈕： 

    [![新增分鏡腳本](indepth-images/ref01.png)](indepth-images/ref01.png#lightbox)
2. 在 **方案總管**中，按兩下新的分鏡腳本名稱，將其開啟，以在 Xcode 的 Interface Builder 中進行編輯。
3. 以您平常的方式設計新腳本的版面配置，並儲存您的變更： 

    [![設計介面](indepth-images/ref02.png)](indepth-images/ref02.png#lightbox)
4. 切換至您要在 Interface Builder 中加入參考的分鏡腳本。
5. 將分鏡腳本**參考**從**物件程式庫**拖曳至 Design Surface： 

    [![在文件庫中選取分鏡腳本參考](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
6. 在 [**屬性偵測器**] 中，選取您在上方建立之**腳本的名稱**： 

    [![設定參考](indepth-images/ref04.png)](indepth-images/ref04.png#lightbox)
7. 在現有的場景上，以滑鼠右鍵按一下 UI Widget （例如按鈕），然後建立新的 Segue 至您剛才建立的分鏡腳本**參考**。  從快顯功能表選取 [**顯示**] 以完成 Segue： 

    [![設定 Segue 類型](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存至分鏡腳本。
9. 返回 Visual Studio for Mac 以同步您的變更。

當應用程式執行時，如果使用者按一下您建立 Segue 的 UI 元素，則會顯示分鏡腳本參考中所指定之外部分鏡腳本的初始視窗控制器。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>參考外部分鏡腳本中的特定場景

若要將參考新增至外部分鏡腳本（而不是初始視窗控制器）的特定場景，請執行下列動作：

1. 在 **方案總管**中，按兩下外部分鏡腳本，將其開啟，以在 Xcode 的 Interface Builder 中進行編輯。
2. 加入新的場景，並以平常的方式設計它的版面配置： 

    [![在 Xcode 中設計版面配置](indepth-images/ref07.png)](indepth-images/ref07.png#lightbox)
3. 在 [身分**識別偵測器**] 中，輸入新場景視窗控制器的分鏡腳本**識別碼**： 

    [![設定分鏡腳本識別碼](indepth-images/ref08.png)](indepth-images/ref08.png#lightbox)
4. 開啟您要在 Interface Builder 中加入參考的分鏡腳本。
5. 將分鏡腳本**參考**從**物件程式庫**拖曳至 Design Surface： 

    [![從程式庫選取分鏡腳本參考](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
6. 在 [身分**識別偵測器**] 中，選取腳本的名稱，以及您先前建立之場景的**參考識別碼**（分鏡腳本識別碼）： **Storyboard** 

    [![設定參考識別碼](indepth-images/ref09.png)](indepth-images/ref09.png#lightbox)
7. 在現有的場景上，以滑鼠右鍵按一下 UI Widget （例如按鈕），然後建立新的 Segue 至您剛才建立的分鏡腳本**參考**。 從快顯功能表選取 [**顯示**] 以完成 Segue： 

    [![設定 Segue 類型](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存至分鏡腳本。
9. 返回 Visual Studio for Mac 以同步您的變更。

當應用程式執行時，如果使用者按一下您建立 Segue 時所用的 UI 元素，就會顯示具有指定分鏡腳本參考中所指定之分鏡腳本**識別碼**的場景。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>參考相同分鏡腳本中的特定場景

若要在相同的分鏡腳本中加入特定場景的參考，請執行下列動作：

1. 在 **方案總管**中，按兩下分鏡腳本，將其開啟以進行編輯。
2. 加入新的場景，並以平常的方式設計它的版面配置： 

    [![在 Xcode 中編輯腳本](indepth-images/ref11.png)](indepth-images/ref11.png#lightbox)
3. 在 [身分**識別偵測器**] 中，輸入新場景視窗控制器的分鏡腳本**識別碼**： 

    [![設定分鏡腳本識別碼](indepth-images/ref12.png)](indepth-images/ref12.png#lightbox)
4. 從 **工具箱** 將 分鏡腳本**參考** 拖曳至 Design Surface： 

    [![從程式庫選取分鏡腳本參考](indepth-images/ref03.png)](indepth-images/ref03.png#lightbox)
5. 在**屬性偵測器**中，為您在上面建立的場景選取 [**參考識別碼**（分鏡腳本識別碼）]： 

    [![設定參考識別碼](indepth-images/ref13.png)](indepth-images/ref13.png#lightbox)
6. 在現有的場景上，以滑鼠右鍵按一下 UI Widget （例如按鈕），然後建立新的 Segue 至您剛才建立的分鏡腳本**參考**。 從快顯功能表選取 [**顯示**] 以完成 Segue： 

    [![選取 Segue 類型](indepth-images/ref06.png)](indepth-images/ref06.png#lightbox) 
7. 將您的變更儲存至分鏡腳本。
8. 返回 Visual Studio for Mac 以同步您的變更。

當應用程式執行時，如果使用者按一下您建立 Segue 的 UI 元素，則會顯示在分鏡腳本參考中所指定的相同分鏡腳本中具有指定分鏡腳本**識別碼**的場景。

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需在 Xamarin. Mac 應用程式中使用分鏡腳本的複雜範例，請參閱[SourceWriter 範例應用程式](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
