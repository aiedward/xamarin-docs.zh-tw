---
title: 使用 xamarin.mac 的分鏡腳本
description: 本文件說明如何使用 Xamarin.Mac、 檢查如何將它們從程式碼、 檢視控制器生命週期、 回應者鏈結中的分鏡腳本，segue，視窗的控制站、 筆勢辨識器，和更多功能。
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 61d598f90747cf47b613012328f77b4bd8953a41
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528454"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>使用 xamarin.mac 的分鏡腳本

分鏡腳本會定義所有指定的應用程式細分為功能的概觀，其檢視控制器的 UI。 在 Xcode 的 Interface Builder，每個這些控制站會存在於自己的場景。

[![](indepth-images/intro01.png "在 Xcode 的 Interface Builder 分鏡腳本")](indepth-images/intro01.png#lightbox)

分鏡腳本是資源檔 (副檔名是`.storyboard`)，取得包含在 Xamarin.Mac 應用程式的套件組合時進行編譯和出貨。 若要定義您的應用程式開始分鏡腳本，編輯它的`Info.plist`檔案，然後選取**主要介面**從下拉式清單方塊： 

[![](indepth-images/sb01.png "Info.plist 編輯器")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>從程式碼載入

可能有您需要從程式碼中載入特定的分鏡腳本，並以手動方式建立檢視控制器。 您可以使用下列程式碼執行此動作：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName`載入分鏡腳本檔案，以指定的名稱，已包含在應用程式的套件組合。 `InstantiateControllerWithIdentifier`建立具有指定的身分識別的檢視控制器執行個體。 設計 UI 時，您可以設定在 Xcode 的 Interface Builder 中的身分識別：

[![](indepth-images/sb02.png "設定分鏡腳本識別碼")](indepth-images/sb02.png#lightbox)

或者，您可以使用`InstantiateInitialController`方法來載入已被指派 Interface Builder 中初始的控制器將檢視控制器：

[![](indepth-images/sb03.png "設定初始的控制站")](indepth-images/sb03.png#lightbox)

它會標示**分鏡腳本的進入點**和上述的開放結束箭號。

<a name="View-Controllers" />

## <a name="view-controllers"></a>檢視控制器

檢視控制器會定義指定的檢視的 Mac 應用程式中的資訊和提供該資訊的資料模型之間的關聯性。 分鏡腳本中的每個最上層場景均代表一個 Xamarin.Mac 應用程式的程式碼中的檢視控制器。

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>檢視控制器生命週期

已新增數個新方法至`NSViewController`類別，以在 macOS 中支援分鏡腳本。 最重要的是會遵循方法用來回應生命週期的受控制所指定的檢視控制器的檢視：

- `ViewDidLoad` -從 分鏡腳本檔案載入檢視時，被呼叫此方法。
- `ViewWillAppear` -此方法稱為之前檢視會顯示在畫面上。
- `ViewDidAppear` -已顯示在畫面上的檢視之後，會直接呼叫此方法。
- `ViewWillDisappear` -從螢幕中移除檢視之前，會呼叫此方法。
- `ViewDidDisappear` -從螢幕中移除檢視後，會直接呼叫此方法。
- `UpdateViewConstraints` -定義檢視的條件約束會自動更新的版面配置位置和大小需求時，會呼叫此方法。
- `ViewWillLayout` -此方法稱為之前將子檢視這份檢視會顯示的畫面。
- `ViewDidLayout` -將子檢視的檢視所顯示的畫面之後，會直接呼叫此方法。

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>回應者鏈結

此外，`NSViewControllers`視窗的一部分_回應者鏈結_:

[![](indepth-images/vc01.png "回應者鏈結")](indepth-images/vc01.png#lightbox)

而且它們是有線-最多接收和回應事件，例如剪下、 複製和貼上功能表項目選項。 此自動檢視控制器的連線-向上才會發生在 macOS Sierra (10.12) 上執行的應用程式和更新版本。

<a name="Containment" />

### <a name="containment"></a>內含項目

分鏡腳本，在檢視控制器 （例如分割檢視控制器和索引標籤檢視控制器） 現在可以實作_內含項目_，使得它們可以 「 包含 」 其他子檢視控制器：

[![](indepth-images/vc02.png "檢視控制器的內含項目範例")](indepth-images/vc02.png#lightbox)

子檢視控制器包含方法和屬性，以將其繫結回至其父檢視控制器，以及使用 顯示並移除從畫面的檢視。

MacOS 內建的所有容器檢視控制器都有 Apple 建議您遵循是否建立您自己的自訂容器檢視控制器的特定配置：

[![](indepth-images/vc03.png "檢視控制器版面配置")](indepth-images/vc03.png#lightbox)

集合檢視控制器包含陣列的集合檢視項目，每個都包含一或多個檢視控制器包含自己的檢視。

<a name="Segues" />

## <a name="segues"></a>Segue

Segue 提供所有的場景，定義您的應用程式 UI 之間的關聯性。 如果您已熟悉在分鏡腳本，在 iOS 中，您必須知道 Segue 的 iOS 通常定義全螢幕檢視之間的轉換。 當 Segue 通常定義時，這不同於 macOS，「[內含項目](#Containment)"，其中一個場景是場景父系的子系。

在 macOS 中大部分的應用程式通常會將他們的看法，一起在相同的視窗中使用 UI 元素，例如分割檢視和索引標籤。 不同於 iOS，其中檢視需要開啟和關閉畫面轉換，因為受限制的實體顯示空間。

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>簡報 Segue

指定內含項目針對 macOS 的傾向，有很多情況下所在_簡報 Segue_使用，例如強制回應的 Windows、 檢視表和 Popovers。 macOS 提供下列內建的 segue 類型：

- **顯示**-為非強制回應視窗顯示 Segue 的目標。 例如，使用這種類型的 Segue 來呈現文件視窗，在您的應用程式中的另一個執行個體。
- **強制回應**-會顯示為強制回應視窗 Segue 的目標。 例如，使用這種類型的 Segue 來呈現您的應用程式的 [喜好設定] 視窗。
- **工作表**-呈現的 Segue 目標與工作表連結至父視窗。 例如，使用這種類型的 segue 呈現的尋找和取代的工作表。
- **Popover** -popover 視窗呈現 Segue 的目標。 例如，使用此 Segue 類型來顯示選項，當使用者按下 UI 項目。
- **自訂**-顯示使用開發人員所定義的自訂 Segue 類型的 Segue 的目標。 請參閱[建立自訂 Segue](#Creating-Custom-Segues)節以取得詳細資料。

當使用 Segue 簡報時，您可以覆寫`PrepareForSegue`簡報中，以便初始化和變數的父檢視控制器方法和任何資料提供給正在呈現的檢視控制器。

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>觸發 Segue

觸發的 Segue 可讓您指定具名的 Segue (透過其**識別碼**介面產生器中的屬性)，並讓它們觸發的事件，例如在使用者按一下按鈕，或藉由呼叫`PerformSegue`在程式碼中的方法：

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

Segue 識別碼會定義在 Xcode 的 Interface Builder 中，當應用程式的 UI 以配置：

[![](indepth-images/sg02.png "輸入名稱的 Segue")](indepth-images/sg02.png#lightbox)

在做為來源的 Segue 檢視控制器，您應該覆寫`PrepareForSegue`方法執行之前的 Segue 會執行所需任何初始設定和指定的檢視控制器會顯示：

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

（選擇性） 您可以覆寫`ShouldPerformSegue`方法和控制項的 Segue 是否實際執行透過C#程式碼。 針對以手動方式呈現的檢視控制器，呼叫其`DismissController`不再需要時從顯示移除它們的方法。

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>建立自訂的 Segue

可能有您的應用程式時需要 Segue 不所提供的類型定義在 macOS 中內建 Segue 的時間。 如果發生這種情況，您可以建立自訂的 Segue 可指派在 Xcode 的 Interface Builder 中您的應用程式 UI 的版面配置時。

例如，若要建立新的 Segue 類型，它會取代目前的檢視控制器 （而不是在新視窗開啟目標場景） 視窗內，我們可以使用下列程式碼：

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

- 我們會使用`Register`屬性來公開這個類別，以目標為 C/macOS。
- 我們要覆寫`Perform`實際執行的動作，我們自訂的 Segue 的方法。
- 我們要取代視窗的`ContentViewController`使用 Segue 的目標 （目的地） 所定義的控制器。
- 我們會移除原始的檢視控制器，以釋出記憶體使用`RemoveFromParentViewController`方法。

若要在 Xcode 的 Interface Builder 中使用這個新的 Segue 類型，我們需要編譯應用程式第一次，然後切換到 Xcode 並新增一個新的 Segue 兩個場景之間。 設定**樣式**要**自訂**並**Segue 類別**來`ReplaceViewSegue`（我們自訂的 Segue 類別的名稱）：

[![](indepth-images/sg01.png "設定 Segue 類別")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>視窗控制器

視窗的控制站包含，並控制您的 macOS 應用程式可以建立不同視窗類型。 分鏡腳本，它們具有下列功能：

1. 他們必須提供內容的檢視控制器。 這會是子視窗具有相同內容檢視控制器。
2. `Storyboard`屬性會包含視窗控制器從載入，否則將分鏡腳本`null`如果未載入的分鏡腳本。
3. 您可以呼叫`DismissController`方法來關閉指定的視窗，並從檢視中移除。

檢視與控制器類似，實作視窗的控制站`PerformSegue`，`PrepareForSegue`而`ShouldPerformSegue`方法及可用來當做 Segue 作業的來源。

視窗控制器負責 macOS 應用程式的下列功能：

- 他們管理特定的視窗。
- 他們管理視窗的標題列和工具列 （如果有的話）。
- 他們管理內容的檢視控制器，以顯示視窗的內容。

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>筆勢辨識器

適用於 macOS 的筆勢辨識器與其在 iOS 中幾乎完全相同，並在您的應用程式 UI 中的項目可讓開發人員輕鬆地加入筆勢 （例如按下滑鼠按鈕）。

不過，其中在 iOS 中的筆勢取決於應用程式的設計 （例如點選兩隻手指與螢幕），最 macOS 中的筆勢取決於硬體。

藉由使用筆勢辨識器，您可以大幅減少在 UI 中的項目中新增自訂的互動所需的程式碼數量。 因為它們可以自動判斷和單一的雙點擊之間，請按一下，然後拖曳事件等等。

而不是覆寫`MouseDown`檢視控制器中的事件，您應該使用筆勢辨識器來處理使用者的輸入的事件時使用分鏡腳本。

下列的筆勢辨識器可在 macOS 中：

- `NSClickGestureRecognizer` -註冊滑鼠向下和向上事件。
- `NSPanGestureRecognizer` 註冊會按下滑鼠，拖曳並發行事件。
- `NSPressGestureRecognizer` 按住滑鼠按鈕一段指定時間事件註冊。
- `NSMagnificationGestureRecognizer` -註冊軌跡板硬體的縮放比例事件。
- `NSRotationGestureRecognizer` -註冊軌跡板硬體旋轉事件。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用分鏡腳本的參考

分鏡腳本參考可讓您取得大型且複雜的分鏡腳本設計，並將它分割成較小的分鏡腳本取得參考從原始，因此移除複雜度，並且讓產生個別的分鏡腳本設計和維護變得更加容易。

此外，分鏡腳本參考可提供_錨點_至相同的分鏡腳本或不同的特定場景中的另一個場景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>參考的外部的分鏡腳本

若要新增的外部的分鏡腳本的參考，執行下列作業：

1. 在 **方案總管**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新檔案...**  >  **Mac** > **分鏡腳本**。 請輸入**名稱**新分鏡腳本，然後按一下 **新增**按鈕： 

    [![](indepth-images/ref01.png "加入新的分鏡腳本")](indepth-images/ref01.png#lightbox)
2. 在 [**方案總管] 中**，連按兩下以開啟它以在 Xcode 的 Interface Builder 中編輯新的分鏡腳本名稱。
2. 設計新分鏡腳本場景的版面配置，以及您通常會儲存您的變更： 

    [![](indepth-images/ref02.png "設計介面")](indepth-images/ref02.png#lightbox)
3. 切換到 分鏡腳本，您要在介面產生器中新增的參考。
4. 拖曳**參考分鏡腳本**從**物件程式庫**拖曳至設計介面： 

    [![](indepth-images/ref03.png "選取程式庫中的分鏡腳本的參考")](indepth-images/ref03.png#lightbox)
5. 在 **屬性偵測器**，選取的名稱**分鏡腳本**您上面所建立： 

    [![](indepth-images/ref04.png "設定參考")](indepth-images/ref04.png#lightbox)
6. UI 上的小工具 （例如按鈕） 現有的場景 control + 按一下，並建立以新的 Segue**分鏡腳本參考**您剛建立。  從快顯功能表中選取**顯示**完成 Segue: 

    [![](indepth-images/ref06.png "設定 Segue 類型")](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存到分鏡腳本。
9. 返回 Visual Studio for Mac 將會同步處理您的變更。

在執行應用程式，並在使用者按一下 UI 項目上您從初始視窗控制器從外部指定分鏡腳本參考中的分鏡腳本建立的 Segue 時將會顯示。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>參考特定的場景中的外部的分鏡腳本

若要將參考加入特定的場景的外部的分鏡腳本 （並沒有初始視窗控制器），執行下列作業：

1. 在 [**方案總管] 中**，連按兩下以開啟它以在 Xcode 的 Interface Builder 中編輯外部的分鏡腳本。
2. 加入新場景，並像平常一樣，設計其版面配置： 

    [![](indepth-images/ref07.png "設計在 Xcode 中的版面配置")](indepth-images/ref07.png#lightbox)
3. 在 **身分識別偵測器**，輸入**分鏡腳本識別碼**新場景的視窗控制站： 

    [![](indepth-images/ref08.png "設定分鏡腳本識別碼")](indepth-images/ref08.png#lightbox)
3. 開啟分鏡腳本，您要在介面產生器中新增的參考。
4. 拖曳**參考分鏡腳本**從**物件程式庫**拖曳至設計介面： 

    [![](indepth-images/ref03.png "從程式庫中選取 分鏡腳本參考")](indepth-images/ref03.png#lightbox)
5. 在 **身分識別偵測器**，選取的名稱**分鏡腳本**和**參考識別碼**（分鏡腳本識別碼） 您先前建立的場景： 

    [![](indepth-images/ref09.png "設定參考識別碼")](indepth-images/ref09.png#lightbox)
6. UI 上的小工具 （例如按鈕） 現有的場景 control + 按一下，並建立以新的 Segue**分鏡腳本參考**您剛建立。 從快顯功能表中選取**顯示**完成 Segue: 

    [![](indepth-images/ref06.png "設定 Segue 類型")](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存到分鏡腳本。
9. 返回 Visual Studio for Mac 將會同步處理您的變更。

當應用程式執行且使用者按下的 UI 項目，您所建立的 Segue 場景中填入從給定**分鏡腳本識別碼**將顯示從外部指定分鏡腳本參考中的分鏡腳本。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>參考特定的場景中相同的分鏡腳本

若要新增至特定的場景相同的分鏡腳本的參考，執行下列作業：

1. 在 [**方案總管] 中**，連按兩下以開啟它進行編輯分鏡腳本。
2. 加入新場景，並像平常一樣，設計其版面配置： 

    [![](indepth-images/ref11.png "編輯在 Xcode 中的分鏡腳本")](indepth-images/ref11.png#lightbox)
3. 在 **身分識別偵測器**，輸入**分鏡腳本識別碼**新場景的視窗控制站： 

    [![](indepth-images/ref12.png "設定分鏡腳本識別碼")](indepth-images/ref12.png#lightbox)
3. 拖曳**參考分鏡腳本**從**工具箱**拖曳至設計介面： 

    [![](indepth-images/ref03.png "從程式庫中選取 分鏡腳本參考")](indepth-images/ref03.png#lightbox)
5. 在 **屬性偵測器**，選取**參考識別碼**（分鏡腳本識別碼） 您先前建立的場景： 

    [![](indepth-images/ref13.png "設定參考識別碼")](indepth-images/ref13.png#lightbox)
6. UI 上的小工具 （例如按鈕） 現有的場景 control + 按一下，並建立以新的 Segue**分鏡腳本參考**您剛建立。 從快顯功能表中選取**顯示**完成 Segue: 

    [![](indepth-images/ref06.png "選取 Segue 類型")](indepth-images/ref06.png#lightbox) 
8. 將您的變更儲存到分鏡腳本。
9. 返回 Visual Studio for Mac 將會同步處理您的變更。

當應用程式執行且使用者按下的 UI 項目，您所建立的 Segue 場景中填入從給定**分鏡腳本識別碼**將顯示同一個分鏡腳本參考中所指定的分鏡腳本中。

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>複雜的分鏡腳本範例

如需使用分鏡腳本的 Xamarin.Mac 應用程式中的複雜範例，請參閱[SourceWriter 範例應用程式](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

## <a name="related-links"></a>相關連結

- [MacStoryboard （範例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
