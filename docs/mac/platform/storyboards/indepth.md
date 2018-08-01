---
title: 使用分鏡腳本中 Xamarin.Mac
description: 本文件描述如何使用 Xamarin.Mac，檢查如何將它們載入從程式碼、 檢視控制器生命週期、 回應鏈結中的分鏡腳本，segues 視窗的控制站、 筆勢辨識器，以及更多。
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 72986ed4247c3b6f66f6f1813d74bf0a95d0de53
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792836"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>使用分鏡腳本中 Xamarin.Mac

分鏡腳本定義所有已細分成其檢視控制站的功能概觀給定應用程式的使用者介面。 在 Xcode 的介面產生器中，每一個這些控制器存在於它自己的場景。

[![](indepth-images/intro01.png "在 Xcode 的介面產生器中的分鏡腳本")](indepth-images/intro01.png#lightbox)

分鏡腳本是資源檔 (副檔名是`.storyboard`)，取得包含在 Xamarin.Mac 應用程式套件組合時進行編譯和出貨。 若要定義您的應用程式起始的分鏡腳本，編輯它的`Info.plist`檔案，然後選取**主要介面**從下拉式清單方塊： 

[![](indepth-images/sb01.png "Info.plist 編輯器")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>從程式碼載入

有些時候可能會當您需要從程式碼載入特定的分鏡腳本，並以手動方式建立檢視控制器。 若要執行此動作，您可以使用下列程式碼：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName`載入分鏡腳本檔案，具有給定名稱的已包含在應用程式套件組合。 `InstantiateControllerWithIdentifier`建立檢視控制器執行個體使用指定的身分識別。 設計 UI 時，您可以設定在 Xcode 的介面產生器中的身分識別：

[![](indepth-images/sb02.png "設定分鏡腳本識別碼")](indepth-images/sb02.png#lightbox)

或者，您可以使用`InstantiateInitialController`方法以載入已被指派初始控制器介面產生器中檢視控制器：

[![](indepth-images/sb03.png "設定初始的控制站")](indepth-images/sb03.png#lightbox)

所標示**分鏡腳本進入點**和上述開啟結束箭號。

<a name="View-Controllers" />

## <a name="view-controllers"></a>檢視控制器

檢視控制站之間定義關聯性指定的檢視中的 Mac 應用程式的資訊和資料模型，以提供該資訊。 在腳本中的每個最上層場景代表 Xamarin.Mac 應用程式的程式碼中的一個檢視控制站。

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>檢視控制器生命週期

已加入數個新方法`NSViewController`類別，以支援在 macOS 分鏡腳本。 最重要的是為後續方法用來檢視所指定之控制器檢視受控制的生命週期回應：

- `ViewDidLoad` -從 分鏡腳本檔案載入檢視時，會呼叫此方法。
- `ViewWillAppear` -此方法被呼叫之前檢視隨即顯示在畫面上。
- `ViewDidAppear` -檢視已顯示在螢幕上之後，會直接呼叫此方法。
- `ViewWillDisappear` -此方法被呼叫之前檢視從螢幕中移除。
- `ViewDidDisappear` -從螢幕中移除的檢視之後，會直接呼叫此方法。
- `UpdateViewConstraints` -定義檢視的條件約束會自動配置的位置和大小需要更新時，會呼叫此方法。
- `ViewWillLayout` -此方法被呼叫之前 subviews 這份檢視會顯示的畫面。
- `ViewDidLayout` -此方法是直接呼叫後的檢視，查看 subviews 所顯示的畫面。

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>回應者鏈結

此外，`NSViewControllers`現在已納入 視窗_回應鏈結_:

[![](indepth-images/vc01.png "回應者鏈結")](indepth-images/vc01.png#lightbox)

而且在這種情況是有線向上來接收和回應事件，例如剪下、 複製和貼上的功能表項目選項。 此自動檢視控制器連線總才會發生在 macOS 利也 (10.12) 上執行的應用程式和更新版本。

<a name="Containment" />

### <a name="containment"></a>內含項目

在腳本檢視控制器 （例如分割檢視控制器和索引標籤檢視控制器） 現在可以實作_內含項目_，好讓它們可以 「 包含 」 其他子檢視控制器：

[![](indepth-images/vc02.png "檢視控制站的內含項目範例")](indepth-images/vc02.png#lightbox)

子檢視控制器包含方法和屬性，以將它們繫結回至其父檢視控制站，以及搭配顯示並移除從螢幕檢視。

MacOS 內建的所有容器檢視控制器都有 Apple 建議您遵循是否建立您自己的自訂容器檢視控制器的特定配置：

[![](indepth-images/vc03.png "檢視控制器版面配置")](indepth-images/vc03.png#lightbox)

集合檢視控制器包含集合檢視包含項目，其中每一個都包含自己的檢視表的一個或多個檢視控制器的陣列。

<a name="Segues" />

## <a name="segues"></a>Segues

Segues 提供所有的場景，定義您的應用程式的 UI 之間的關聯性。 如果您很熟悉使用中 iOS 分鏡腳本，您已了解 Segues iOS 通常會定義全螢幕檢視之間的轉換。 當 Segues 通常定義時，這不同於 macOS"[內含項目](#Containment)"，其中一個場景是場景父系的子系。

MacOS 中大部分的應用程式通常群組及其在相同的視窗中使用 UI 項目，例如分割檢視和索引標籤的檢視。 不同於 iOS，其中檢視需要開啟和關閉螢幕轉換，因為受限制的實體顯示空間。

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>簡報 Segues

指定內含項目優先 macOS 的勤者傾向，有很多情況下其中_簡報 Segues_使用，例如強制回應視窗、 檢視表和 Popovers。 提供下列內建話題 macOS 類型：

- **顯示**-為非強制回應視窗顯示 Segue 的目標。 例如，使用這種類型的 Segue 來呈現應用程式中的文件視窗的另一個執行個體。
- **強制回應**-顯示為強制回應視窗 Segue 的目標。 例如，使用這種類型的 Segue 來呈現應用程式喜好設定 視窗。
- **工作表**-呈現 Segue 的目標，因為工作表連結至父視窗。 例如，使用這種類型的話題呈現尋找和取代的工作表。
- **Popover** -popover 視窗呈現 Segue 的目標。 例如，使用此 Segue 類型來顯示選項，當使用者按下 UI 項目。
- **自訂**-顯示使用開發人員所定義的自訂話題類型 Segue 的目標。 請參閱[建立自訂 Segues](#Creating-Custom-Segues)下面章節以取得詳細資料。

當使用 Segues 簡報時，您可以覆寫`PrepareForSegue`初始化簡報和變數的父檢視控制器方法，並提供要呈現的檢視控制站的任何資料。

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>觸發 Segues

觸發的 Segues 可讓您指定具名的 Segues (透過其**識別碼**介面產生器中的屬性)，並讓他們觸發事件，例如使用者按下按鈕，或呼叫`PerformSegue`程式碼中的方法：

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

當應用程式的 UI 以配置話題識別碼定義內 Xcode 的介面產生器：

[![](indepth-images/sg02.png "輸入話題名稱")](indepth-images/sg02.png#lightbox)

在做為來源的 Segue 檢視控制器，您應該覆寫`PrepareForSegue`方法執行之前執行 Segue 所需任何初始設定和指定的檢視控制器會顯示：

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

（選擇性） 您可以覆寫`ShouldPerfromSegue`方法和控制項 Segue 是否實際執行透過 C# 程式碼。 手動呈現檢視控制站，請呼叫其`DismissController`方法不再需要從顯示的位置將它們移除。

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>建立自訂 Segues

有些時候可能會當應用程式需要未提供的定義中 macOS 內建 Segues Segue 型別。 如果這種情況，您可以建立可以指派自訂話題 Xcode 的介面產生器中時用來配置您的應用程式 UI。

例如，若要建立新的 Segue 類型，它會取代目前的檢視控制器 （而不是在新視窗中開啟目標場景） 視窗內，我們可以使用下列程式碼：

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

幾個這裡要注意的事項：

- 我們使用`Register`將此類別公開給目標的 C/macOS 屬性。
- 我們正在覆寫`Perform`實際執行的動作，我們自訂 Segue 的方法。
- 我們會取代 視窗`ContentViewController`Segue 目標 （目的地） 所定義的控制器。
- 我們會移除原始檢視控制站，以釋出記憶體使用`RemoveFromParentViewController`方法。

若要使用這個新的 Segue 類型 Xcode 的介面產生器中，我們需要編譯應用程式第一次，然後切換到 Xcode 並加入新 Segue 之間兩個場景。 設定**樣式**至**自訂**和**話題類別**至`ReplaceViewSegue`（我們的自訂 Segue 類別名稱）：

[![](indepth-images/sg01.png "設定 Segue 類別")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>視窗的控制站

包含視窗的控制站，並控制 macOS 應用程式可以建立不同的視窗型別。 分鏡腳本，它們有下列功能：

1. 他們必須提供內容的檢視控制器。 這會是子視窗具有相同內容檢視控制器。
2. `Storyboard`屬性會包含視窗控制器已從載入，否則分鏡腳本`null`如果未載入的分鏡腳本。
3. 您可以呼叫`DismissController`方法來關閉指定的視窗，並從檢視中移除。

檢視控制站，像是視窗控制器實作`PerformSegue`，`PrepareForSegue`和`ShouldPerfromSegue`方法，可用來當作 Segue 作業的來源。

視窗的控制站負責 macOS 應用程式的下列功能：

- 他們管理特定的視窗。
- 他們管理視窗的標題列和工具列 （如果有的話）。
- 他們管理內容的檢視控制站，以顯示視窗的內容。

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>筆勢辨識器

MacOS 的筆勢辨識器是與其在 iOS 中幾乎相同，且您的應用程式 UI 中的項目可讓開發人員輕鬆地加入手勢 （例如按下滑鼠按鈕）。

不過，其中在 iOS 中的筆勢取決於應用程式的設計 （例如點選螢幕具有兩隻手指放），最筆勢中 macOS 取決於硬體。

藉由使用筆勢辨識器，您可大幅減少在 UI 中的項目加入自訂的互動所需的程式碼數量。 它們可以自動決定 double 和單一按鍵之間，按一下，然後拖曳事件等等。

而不是覆寫`MouseDown`檢視控制器中的事件，您應該使用筆勢辨識器來處理使用者輸入的事件時使用分鏡腳本。

下列的筆勢辨識器可提供 macOS:

- `NSClickGestureRecognizer` 暫存器滑鼠下和向上事件。
- `NSPanGestureRecognizer` 註冊按下滑鼠，拖曳發行與事件。
- `NSPressGestureRecognizer` 按住滑鼠按鈕一段指定時間的事件註冊。
- `NSMagnificationGestureRecognizer` -註冊 trackpad 硬體的縮放比例事件。
- `NSRotationGestureRecognizer` -註冊 trackpad 硬體的旋轉事件。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用分鏡腳本參考

分鏡腳本參考可讓您的大型、 複雜的分鏡腳本設計，並將它切為取得參考原始的較小的分鏡腳本，因此移除移除複雜性及使所產生的個別 分鏡腳本更容易設計和維護。

此外，可提供分鏡腳本參考_錨點_至相同的分鏡腳本或不同的特定場景中的另一個場景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>參考外部的分鏡腳本

若要加入外部的分鏡腳本的參考，執行下列作業：

1. 在**方案總管 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新的檔案...**  >  **Mac** > **分鏡腳本**。 輸入**名稱**新分鏡腳本並按一下**新增**按鈕： 

    [![](indepth-images/ref01.png "加入新的分鏡腳本")](indepth-images/ref01.png#lightbox)
2. 在**方案總管 中**，按兩下要開啟它進行編輯 Xcode 的介面產生器中的新分鏡腳本名稱。
2. 設計新分鏡腳本場景的版面配置，以及您通常會儲存您的變更： 

    [![](indepth-images/ref02.png "設計介面")](indepth-images/ref02.png#lightbox)
3. 切換至您要加入的參考介面產生器中的分鏡腳本。
4. 拖曳**分鏡腳本參考**從**物件程式庫**拖曳至設計介面： 

    [![](indepth-images/ref03.png "選取程式庫中的分鏡腳本的參考")](indepth-images/ref03.png#lightbox)
5. 在**屬性偵測器**，選取名稱**分鏡腳本**先前建立的： 

    [![](indepth-images/ref04.png "設定參考")](indepth-images/ref04.png#lightbox)
6. 控制項按一下 UI 上的小工具 （例如按鈕） 現有的場景，並建立以新 Segue**分鏡腳本參考**您剛建立。  從快顯功能表選取**顯示**完成 Segue: 

    [![](indepth-images/ref06.png "設定 Segue 類型")](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存到分鏡腳本。
9. 返回 Visual Studio for Mac 同步您的變更。

當執行應用程式，且使用者按一下 UI 項目上，從外部分鏡腳本參考中所指定的分鏡腳本從初始的視窗控制站建立 Segue 隨即出現。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>參考外部的分鏡腳本中的特定場景

將參考加入至特定的場景外部的分鏡腳本 （並不初始的視窗控制站） 上，執行下列作業：

1. 在**方案總管 中**，連按兩下以開啟檔案進行編輯 Xcode 的介面產生器中的外部分鏡腳本。
2. 加入新的場景，並設計其版面配置，像平常一樣： 

    [![](indepth-images/ref07.png "設計版面配置在 Xcode 中")](indepth-images/ref07.png#lightbox)
3. 在**識別 Inspector**，輸入**分鏡腳本識別碼**新場景的視窗控制站： 

    [![](indepth-images/ref08.png "設定分鏡腳本識別碼")](indepth-images/ref08.png#lightbox)
3. 開啟您要在介面產生器中的參考加入分鏡腳本。
4. 拖曳**分鏡腳本參考**從**物件程式庫**拖曳至設計介面： 

    [![](indepth-images/ref03.png "從程式庫中選取 分鏡腳本參考")](indepth-images/ref03.png#lightbox)
5. 在**識別 Inspector**，選取名稱**分鏡腳本**和**參考識別碼**（分鏡腳本識別碼） 先前建立的場景： 

    [![](indepth-images/ref09.png "設定參考識別碼")](indepth-images/ref09.png#lightbox)
6. 控制項按一下 UI 上的小工具 （例如按鈕） 現有的場景，並建立以新 Segue**分鏡腳本參考**您剛建立。 從快顯功能表選取**顯示**完成 Segue: 

    [![](indepth-images/ref06.png "設定 Segue 類型")](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存到分鏡腳本。
9. 返回 Visual Studio for Mac 同步您的變更。

當應用程式執行且使用者按一下您從與場景建立 Segue 此 UI 項目指定**分鏡腳本識別碼**將顯示來自外部的分鏡腳本分鏡腳本參考中所指定。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>參考在相同的分鏡腳本的特定場景

若要新增特定場景相同分鏡腳本的參考，執行下列作業：

1. 在**方案總管 中**，連按兩下以開啟檔案進行編輯分鏡腳本。
2. 加入新的場景，並設計其版面配置，像平常一樣： 

    [![](indepth-images/ref11.png "編輯在 Xcode 中的分鏡腳本")](indepth-images/ref11.png#lightbox)
3. 在**識別 Inspector**，輸入**分鏡腳本識別碼**新場景的視窗控制站： 

    [![](indepth-images/ref12.png "設定分鏡腳本識別碼")](indepth-images/ref12.png#lightbox)
3. 拖曳**分鏡腳本參考**從**工具箱**拖曳至設計介面： 

    [![](indepth-images/ref03.png "從程式庫中選取 分鏡腳本參考")](indepth-images/ref03.png#lightbox)
5. 在**屬性偵測器**，選取**參考識別碼**（分鏡腳本識別碼） 先前建立的場景： 

    [![](indepth-images/ref13.png "設定參考識別碼")](indepth-images/ref13.png#lightbox)
6. 控制項按一下 UI 上的小工具 （例如按鈕） 現有的場景，並建立以新 Segue**分鏡腳本參考**您剛建立。 從快顯功能表選取**顯示**完成 Segue: 

    [![](indepth-images/ref06.png "選取 Segue 類型")](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存到分鏡腳本。
9. 返回 Visual Studio for Mac 同步您的變更。

當應用程式執行且使用者按一下您從與場景建立 Segue 此 UI 項目指定**分鏡腳本識別碼**將顯示在相同的分鏡腳本分鏡腳本參考中所指定。

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需使用分鏡腳本 Xamarin.Mac 應用程式中的複雜範例，請參閱[SourceWriter 範例應用程式](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

## <a name="related-links"></a>相關連結

- [MacStoryboard （範例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用視窗](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
