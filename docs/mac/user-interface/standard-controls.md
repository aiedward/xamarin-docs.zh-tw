---
title: 標準控制項
description: 本文涵蓋了使用標準的 AppKit 控制項，例如按鈕、 標籤、 文字欄位、 核取方塊，並區隔 Xamarin.Mac 應用程式中的控制項。 它會描述新增至介面產生器的介面，並與其互動的程式碼中。
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3fe155508b60cbe502c3beca58426528d6f49c9d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="standard-controls"></a>標準控制項

_本文涵蓋了使用標準的 AppKit 控制項，例如按鈕、 標籤、 文字欄位、 核取方塊，並區隔 Xamarin.Mac 應用程式中的控制項。它會描述新增至介面產生器的介面，並與其互動的程式碼中。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同 AppKit 控制項開發人員使用*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護 Appkit 控制項 （或您可以選擇直接在 C# 程式碼中建立它們）。

AppKit 控制項是用來建立使用者介面 Xamarin.Mac 應用程式的 UI 項目。 它們包含的項目，例如按鈕、 標籤、 文字欄位中，核取方塊和分割的控制項，並會造成立即動作或顯示的結果時發生使用者管理它們。

[![](standard-controls-images/intro01.png "範例應用程式主畫面")](standard-controls-images/intro01.png#lightbox)

在本文中，我們將討論使用 AppKit 控制項 Xamarin.Mac 應用程式中的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>控制項和檢視簡介

macOS （之前稱為 Mac OS X） 提供一組標準的 AppKit Framework 透過使用者介面控制項。 它們包含的項目，例如按鈕、 標籤、 文字欄位中，核取方塊和分割的控制項，並會造成立即動作或顯示的結果時發生使用者管理它們。

所有 AppKit 控制項具有標準的內建的外觀會適用於大部分用途，某些指定替代的外觀，使用中視窗框架區域或在_Vibrance 效果_內容，例如 for-each [資訊看板] 區域或在通知中心 widget。

Apple 的使用 AppKit 控制項時建議下列指導方針：

- 避免混用相同的檢視中的控制項大小。
- 一般情況下，避免垂直調整控制項的大小。
- 使用系統字型以及控制項中的適當的文字大小。
- 使用控制項的正常間距。

如需詳細資訊，請參閱[相關控制項和檢視表](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>使用中視窗框架的控制項

有 AppKit 控制項包含可讓它們包含在視窗的畫面格的區域中的顯示樣式的子集。 如需範例，請參閱郵件應用程式工具列：

[![](standard-controls-images/mailapp.png "Mac 視窗框架")](standard-controls-images/mailapp.png#lightbox)

- **捨入材質按鈕**-`NSButton`與樣式`NSTexturedRoundedBezelStyle`。
- **材質捨入的分段控制項**-`NSSegmentedControl`與樣式`NSSegmentStyleTexturedRounded`。
- **材質捨入的分段控制項**-`NSSegmentedControl`與樣式`NSSegmentStyleSeparated`。
- **捨入材質快顯功能表**-`NSPopUpButton`與樣式`NSTexturedRoundedBezelStyle`。
- **捨入材質下拉式選單**-`NSPopUpButton`與樣式`NSTexturedRoundedBezelStyle`。
- **搜尋列**- `NSSearchField`。

Apple 的使用中視窗框架的 AppKit 控制項時建議下列指導方針：

- 請勿使用視窗內的視窗框架特定控制項的樣式。
- 請勿使用視窗主體控制項或樣式視窗框架中。

如需詳細資訊，請參閱[相關控制項和檢視表](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>在介面產生器中建立的使用者介面

當您建立新的 Xamarin.Mac Cocoa 應用程式時，預設會取得標準的空白，視窗。 此 windows 中所定義`.storyboard`自動包含在專案中的檔案。 若要編輯您的 windows 設計中**方案總管] 中**，按兩下 [`Main.storyboard`檔案：

[![](standard-controls-images/edit01.png "在 [方案總管] 中選取主要腳本")](standard-controls-images/edit01.png#lightbox)

這會在 Xcode 的介面產生器中開啟視窗設計：

[![](standard-controls-images/edit02.png "編輯在 Xcode 中的分鏡腳本")](standard-controls-images/edit02.png#lightbox)

若要建立您的使用者介面，您會從拖曳 UI 項目 （AppKit 控制項）**程式庫偵測器**至**介面編輯器**介面產生器中。 在下列範例中，**垂直分割檢視**控制項已經從藥物**程式庫偵測器**並放在視窗上**介面編輯器**:

[![](standard-controls-images/edit03.png "從程式庫中選取的分割檢視")](standard-controls-images/edit03.png#lightbox)

如需有關如何在介面產生器中建立的使用者介面的詳細資訊，請參閱我們[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)文件。

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>調整大小和位置

一旦控制項已包含在使用者介面中，使用**條件約束編輯器**手動輸入值，設定其位置和大小，並控制控制項如何自動定位和調整大小的時機的父視窗或檢視表重新調整大小：

[![](standard-controls-images/edit04.png "設定條件約束")](standard-controls-images/edit04.png#lightbox)

使用**紅色我的資料交換**的周圍**Autoresizing**方塊_搖桿_控制項 (x，y) 指定的位置。 例如:  

[![](standard-controls-images/edit05.png "編輯條件約束")](standard-controls-images/edit05.png#lightbox)

指定選取的控制項 (在**階層檢視** & **介面編輯器**) 是調整大小或移動，所以會停滯框線和右視窗或檢視表的位置。 

編輯器控制項屬性，例如高度和寬度的其他項目：

[![](standard-controls-images/edit06.png "設定高度")](standard-controls-images/edit06.png#lightbox)

您也可以使用條件約束與控制的項目對齊**對齊編輯器**:

[![](standard-controls-images/edit07.png "對齊編輯器")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> 不同於 iOS 其中 (0，0) 是上層 macOS (0，0) 中的畫面的左下角是左下角。 這是因為 macOS 使用數學座標系統，使用向上和向右值增加的數字值。 您必須將這點納入考量放置 AppKit 使用者介面上的控制項時。

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>設定自訂的類別

但有些的時候，當使用 AppKit 控制項，您將需要子類別和現有的控制項和建立您自己的自訂版本，該類別。 例如，定義自訂版本的來源清單：

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

其中`[Register("SourceListView")]`指令會公開`SourceListView`類別以 OBJECTIVE-C 讓其可以用於介面產生器。 如需詳細資訊，請參閱[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`所使用的命令以 OBJECTIVE-C 物件和 UI 項目網路接您的 C# 類別。

就地上述程式碼，您可以拖曳至設計介面拖曳 AppKit 控制項，可延伸的基底類型 (在下列範例中，**來源清單**)，切換至**身分識別檢查**和設定**自訂類別**您公開至 Objective C 的名稱 (範例`SourceListView`):

[![](standard-controls-images/edit10.png "在 Xcode 中設定的自訂類別")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>公開插座和動作

AppKit 控制可以存取 C# 程式碼之前，它必須公開為**插座**或和**動作**。 若要執行此選取在指定的控制項**介面階層架構**或**介面編輯器**並切換至**助理檢視**(請確定您有`.h`您選取要編輯的視窗):

[![](standard-controls-images/edit11.png "選取要編輯的正確檔案")](standard-controls-images/edit11.png#lightbox)

從 AppKit 控制項拖曳至提供的控制項拖曳`.h`檔案以啟動 建立**插座**或**動作**:

[![](standard-controls-images/edit12.png "拖曳以建立輸出或動作")](standard-controls-images/edit12.png#lightbox)

選取的風險，來建立，以及類型**插座**或**動作****名稱**: 

[![](standard-controls-images/edit13.png "設定輸出或動作")](standard-controls-images/edit13.png#lightbox)


如需有關使用**插座**和**動作**，請參閱[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段我們[Xcode 和介面簡介產生器](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)文件。

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>與 Xcode 同步處理變更

當您切換回 Visual Studio for Mac 從 Xcode 時，在 Xcode 中，您所做的任何變更將會自動同步處理與 Xamarin.Mac 專案。

如果您選取`SplitViewController.designer.cs`中**方案總管 中**您將能夠看到如何您**插座**和**動作**我們 C# 程式碼有線：

[![](standard-controls-images/sync01.png "Xcode 與同步處理變更")](standard-controls-images/sync01.png#lightbox)

請注意如何在定義`SplitViewController.designer.cs`檔案：

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

中的定義與對齊`MainWindow.h`在 Xcode 中的檔案：

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

如您所見，Visual Studio for Mac 接聽變更`.h`檔案，並會自動同步這些變更中的個別`.designer.cs`檔案，以公開至您的應用程式。 您可能也注意`SplitViewController.designer.cs`是部分類別，如此不需要修改 Visual Studio for Mac`SplitViewController.cs `這會覆寫，我們對類別的任何變更。

您通常會永遠不需要開啟`SplitViewController.designer.cs`自己，它已介紹教學之用。

> [!IMPORTANT]
> 在大部分情況下，適用於 Mac 的 Visual Studio 會自動看到在 Xcode 中進行任何變更，並同步它們 Xamarin.Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>使用按鈕

AppKit 提供數種可用於您的使用者介面設計的按鈕。 如需詳細資訊，請參閱[按鈕](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/buttons01.png "不同的按鈕類型的範例")](standard-controls-images/buttons01.png#lightbox)

如果已透過公開按鈕**插座**，下列程式碼會回應按下它：

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

已經透過公開的按鈕**動作**、`public partial`方法將會自動會為您建立並與您選擇在 Xcode 中的名稱。 若要回應**動作**，完成此類別中的部分方法的**動作**上已定義。 例如: 

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

有一個狀態的按鈕 (像是**上**和**關閉**)，可檢查或設定與狀態`State`內容和`NSCellStateValue`列舉。 例如: 

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是：

- **在**-推入 按鈕，或選取控制項 （例如核取核取方塊）。
- **關閉**-未推入 按鈕，或未選取的控制項。
- **混合**-混合**上**和**關閉**狀態。

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>標示的按鈕，預設值，然後設定索引鍵的對等項目

針對任何您加入至使用者介面設計的按鈕，您可以將標記為該按鈕_預設_會被啟用，當使用者按下按鈕**傳回/Enter**鍵盤上的索引鍵。 在 macOS，此按鈕會收到預設的藍色背景色彩。

若要設定按鈕為預設值，在 Xcode 的介面產生器中選取它。 接下來，在**屬性偵測器**，選取**索引鍵相等**欄位，然後按**傳回/Enter**機碼：

[![](standard-controls-images/buttons03.png "編輯索引鍵的對等項目")](standard-controls-images/buttons03.png#lightbox)

同樣地，您可以指派任何可以用來啟用按鈕而非滑鼠使用鍵盤的按鍵組合。 例如，藉由按下命令-C 索引鍵在上圖中。

當應用程式執行和按鈕的視窗是索引鍵和已取得焦點，而使用者按下命令 C，按鈕的動作將會啟動 (做為-如果使用者已按一下按鈕)。

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>使用核取方塊和選項按鈕

AppKit 提供數種類型的核取方塊和可用於您的使用者介面設計的選項按鈕群組。 如需詳細資訊，請參閱[按鈕](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/buttons02.png "可用的核取方塊類型的範例")](standard-controls-images/buttons02.png#lightbox)


核取方塊和選項按鈕 (透過公開**插座**) 有狀態 (例如**上**和**關閉**)，可檢查或設定與狀態`State`針對屬性`NSCellStateValue`列舉。 例如: 

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是：

- **在**-推入 按鈕，或選取控制項 （例如核取核取方塊）。
- **關閉**-未推入 按鈕，或未選取的控制項。
- **混合**-混合**上**和**關閉**狀態。

若要選取選項按鈕群組中的按鈕，公開選項按鈕，即可選取作為**插座**並設定其`State`屬性。 例如：

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

若要取得的集合做為群組，並自動處理所選的狀態的選項按鈕，建立新**動作**和附加至這個群組中的每個按鈕：

![](standard-controls-images/buttons04.png "建立新的動作")

接下來，將指派唯一`Tag`中每個選項按鈕**屬性偵測器**:

![](standard-controls-images/buttons05.png "編輯選項按鈕標籤")

儲存變更並返回 Visual Studio for Mac，加入程式碼來處理**動作**所有選項按鈕附加至：

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

您可以使用`Tag`屬性來查看所選取的按鈕。

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>使用功能表控制項

AppKit 提供數種可用於您的使用者介面設計的功能表控制項。 如需詳細資訊，請參閱[功能表控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/menu01.png "範例功能表控制項")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>提供功能表控制項的資料

功能表控制項可 macOS 可以設定為從內部清單 （也可以是預先定義的介面產生器中，或透過程式碼填入） 填入下拉式清單或藉由提供您自己自訂的外部資料來源。

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>使用內部的資料

除了定義介面產生器中，功能表控制項中的項目 (例如`NSComboBox`)，提供一組完整的方法可讓您新增、 編輯或刪除從內部維護的清單項目：

- `Add` -加入新項目至清單的結尾。
- `GetItem` -傳回指定索引處的項目。
- `Insert` -在指定的位置清單中插入新項目。
- `IndexOf` -傳回指定項目的索引。
- `Remove` -從清單中移除指定的項目。
- `RemoveAll` -從清單中移除所有項目。
- `RemoveAt` -移除指定索引處的項目。
- `Count` -傳回清單中的項目數目。

> [!IMPORTANT]
> 如果您使用外部資料來源 (`UsesDataSource = true`)，呼叫上述方法之一，將會擲回例外狀況。

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>使用外部資料來源

而不是使用內建的內部資料來提供功能表控制項中的資料列，您可以選擇性地使用外部資料來源，並提供您自己的備份存放區 （例如 SQLite 資料庫） 的項目。

若要使用外部資料來源，您將建立的功能表控制項的資料來源執行個體 (`NSComboBoxDataSource`例如) 並覆寫數個方法，以提供必要的資料：

- `ItemCount` -傳回清單中的項目數目。
- `ObjectValueForItem` -傳回給定索引項目的值。
- `IndexOfItem` -傳回指定項目值的索引。
- `CompletedString` -傳回部分型別項目值的第一個相符項目值。 如果已啟用 「 自動完成 」，只會呼叫這個方法 (`Completes = true`)。

請參閱[資料庫和下拉式方塊](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes)區段[使用資料庫](~/mac/app-fundamentals/databases.md)文件以取得詳細資料。

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>調整清單的外觀

下列方法可用來調整功能表控制項的外觀：

- `HasVerticalScroller` -如果`true`，控制項將顯示垂直捲軸。 
- `VisibleItems` 調整控制項開啟時顯示的項目數目。 預設值是五 （5)。
- `IntercellSpacing` -指定的項目周圍的空間量調整藉由提供`NSSize`其中`Width`指定左、 右邊界和`Height`之前和之後的項目指定的空間。
- `ItemHeight` -指定在清單中的每個項目的高度。

下拉式清單類型的`NSPopupButtons`，第一個功能表項目提供標題控制項。 例如： 

[![](standard-controls-images/menu02.png "範例功能表控制項")](standard-controls-images/menu02.png#lightbox)

若要變更標題，公開 （expose) 此項目標示為**插座**並使用程式碼如下所示：

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>處理選取的項目

下列方法和屬性，可讓您操作功能表控制項的清單中選取的項目：

- `SelectItem` -選取的指定索引處的項目。
- `Select` -選取的指定項目的值。
- `DeselectItem` -會取消選取的指定索引處的項目。
- `SelectedIndex` -傳回目前選取之項目的索引。
- `SelectedValue` -傳回目前選取之項目的值。

使用`ScrollItemAtIndexToTop`來呈現清單頂端的指定索引處的項目和`ScrollItemAtIndexToVisible`捲動至清單，直到看見指定的索引處的項目。

<a name="Responding to Events" />

### <a name="responding-to-events"></a>回應事件

功能表控制項提供下列事件以回應使用者互動：

- `SelectionChanged` -這是在使用者從清單選取值時呼叫。
- `SelectionIsChanging` -這是在新的使用者選取項目會變成作用中的選取項目之前呼叫。
- `WillPopup` -這是在下拉式清單的項目會顯示之前呼叫。
- `WillDismiss` -這被呼叫關閉下拉式清單的項目之前。

如`NSComboBox`控制項，它們包含的所有相同事件中當做`NSTextField`，例如`Changed`每當使用者編輯的下拉式方塊中的文字值，就會呼叫的事件。

（選擇性） 您可以回應在藉由附加至項目被選取的介面產生器中定義內部的資料功能表項目**動作**並使用下列程式碼來回應**動作**正在由使用者觸發：

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

如需使用功能表和功能表控制項的詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)和[快顯的按鈕和下拉式清單列出](~/mac/user-interface/menu.md)文件。

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>使用 選取控制項

AppKit 提供數種可用於您的使用者介面設計的選取控制項。 如需詳細資訊，請參閱[選取控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/select01.png "範例選取控制項")](standard-controls-images/select01.png#lightbox)

有兩種方式來追蹤何時選取控制項有使用者互動，藉由公開為**動作**。 例如: 

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

或藉由附加**委派**至`Activated`事件。 例如: 

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

若要設定或讀取選取項目控制項的值，請使用`IntValue`屬性。 例如: 

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

特殊控制項 （例如也色彩和影像也） 具有其實值類型的特定屬性。 例如：

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker`具有直接使用日期和時間的下列屬性：

- **DateValue** -目前的日期和時間值做為`NSDate`。
- **本機**位使用者的位置為`NSLocal`。
- **TimeInterval** -時間值，做為`Double`。
- **時區**位使用者的時區`NSTimeZone`。

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>使用指標控制項

AppKit 提供數種可用於您的使用者介面設計的指示器控制項。 如需詳細資訊，請參閱[指示器控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/level01.png "範例指示器控制項")](standard-controls-images/level01.png#lightbox)

有兩種方式來追蹤何時指示器控制項具有使用者互動，藉由公開為**動作**或**插座**和附加**委派**至`Activated`事件。 例如: 

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

若要讀取或設定指標控制項的值，請使用`DoubleValue`屬性。 例如: 

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

顯示時，應該繪製未定和非同步進度指標。 使用`StartAnimation`方法顯示時啟動動畫。 例如: 

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

呼叫`StopAnimation`方法將會停止動畫。

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>使用文字控制項

AppKit 提供數種可以用於使用者介面的設計中的文字控制項。 如需詳細資訊，請參閱[文字控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/text01.png "範例文字控制項")](standard-controls-images/text01.png#lightbox)

文字欄位 (`NSTextField`)，下列事件可用來追蹤使用者互動：

- **變更**-的每當使用者變更欄位的值，會引發。 例如，在輸入每個字元。
- **EditingBegan** -當使用者選取編輯欄位時會引發。
- **EditingEnded** -當使用者按下的 Enter 鍵，在欄位中，或離開欄位。

使用`StringValue`屬性來讀取或設定欄位的值。 例如: 

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

顯示或編輯數值的欄位，您可以使用`IntValue`屬性。 例如: 

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView`提供完整的精選的文字編輯和顯示區域具有內建的格式。 像`NSTextField`，使用`StringValue`屬性來讀取或設定區域的值。

如需複雜的範例，使用文字檢視 Xamarin.Mac 應用程式中的範例，請參閱[SourceWriter 範例應用程式](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>使用內容檢視

AppKit 提供數種類型的內容檢視，可用於使用者介面的設計。 如需詳細資訊，請參閱[內容檢視](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

[![](standard-controls-images/content01.png "範例內容檢視")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Popover 是暫時性的 UI 項目，可提供功能直接相關的控制項特定或螢幕上的區域。 Popover 浮動於視窗，其中包含的控制項或區域相關，以及其框線箭頭，表示它出現的點。

若要建立 popover，執行下列作業：

1. 開啟`.storyboard`檔案，您想要加入至 popover 按兩下視窗的**方案總管**
2. 拖曳**檢視控制器**從**程式庫偵測器**到**介面編輯器**: 

    [![](standard-controls-images/content02.png "從程式庫選取檢視控制器")](standard-controls-images/content02.png#lightbox)
4. 定義大小和配置**自訂檢視**: 

    [![](standard-controls-images/content04.png "編輯版面配置")](standard-controls-images/content04.png#lightbox)
5. 控制項按一下和拖曳來源拖曳至快顯視窗的**檢視控制器**: 

    [![](standard-controls-images/content05.png "若要建立 segue 拖曳")](standard-controls-images/content05.png#lightbox)
6. 選取**Popover**從快顯功能表： 

    [![](standard-controls-images/content06.png "設定 segue 類型")](standard-controls-images/content06.png#lightbox)
7. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

<a name="Tab_Views" />

### <a name="tab-views"></a>索引標籤檢視

包含索引標籤清單 （看起來類似於分割的控制項） 結合一組稱為的檢視 索引標籤檢視_窗格_。 當使用者選取新的索引標籤時，將會顯示已附加至該窗格。 每個窗格包含它自己組控制項。

當使用檢視 Xcode 的介面產生器中的索引標籤上，使用**屬性偵測器**來設定索引標籤數目：

[![](standard-controls-images/content08.png "編輯標籤數目")](standard-controls-images/content08.png#lightbox)

選取每個索引標籤中的**介面階層架構**設定其**標題**加入至 UI 項目和其**窗格**:

[![](standard-controls-images/content09.png "編輯在 Xcode 中的索引標籤")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>資料繫結 AppKit 控制項

Xamarin.Mac 應用程式中使用索引鍵-值撰寫程式碼和資料繫結技術，您可以大幅減少您必須撰寫和維護以填入和 UI 項目所使用的程式碼數量。 您也可以進一步減少您的備份資料的優點 (_資料模型_) 從您的前端結束使用者介面 (_模型-檢視-控制器_)，而導致更輕鬆地維護，更有彈性的應用程式設計。

索引鍵-值撰寫程式碼 (KVC) 是用來間接存取物件的屬性、 使用以識別屬性，而非透過執行個體變數存取它們的索引鍵 （特殊格式化的字串） 或存取子方法的機制 (`get/set`)。 實作索引鍵-值撰寫程式碼相容的存取子 Xamarin.Mac 應用程式中，您就可以存取其他 macOS 功能，例如索引鍵-值觀察 (KVO)、 資料繫結、 核心資料、 Cocoa 繫結，以及 scriptabletype。

如需詳細資訊，請參閱[簡單資料繫結](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding)區段我們[資料繫結和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)文件。


<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得使用 Xamarin.Mac 應用程式中例如按鈕、 標籤、 文字欄位中，核取方塊和分段控制項標準 AppKit 控制項的詳細的檢視。 它涵蓋加入 Xcode 的介面產生器中的使用者介面設計、 將其公開給透過插座和動作的程式碼和使用 C# 程式碼 AppKit 控制項。

## <a name="related-links"></a>相關連結

- [MacControls （範例）](https://developer.xamarin.com/samples/mac/MacControls/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [關於控制項和檢視](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
