---
title: Xamarin 中的標準控制項
description: 本文涵蓋在 Xamarin. Mac 應用程式中使用標準的 AppKit 控制項, 例如按鈕、標籤、文字欄位、核取方塊和分割的控制項。 其中描述如何將這些專案新增至具有 Interface Builder 的介面, 並在程式碼中與其互動。
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e5868e2cfe9a054e84eeb67432a01c2d98b1c3e6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648353"
---
# <a name="standard-controls-in-xamarinmac"></a>Xamarin 中的標準控制項

_本文涵蓋在 Xamarin. Mac 應用程式中使用標準的 AppKit 控制項, 例如按鈕、標籤、文字欄位、核取方塊和分割的控制項。其中描述如何將這些專案新增至具有 Interface Builder 的介面, 並在程式碼中與其互動。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時, 您可以存取開發人員在*目標-C*和*Xcode*中運作的相同 AppKit 控制項。 因為 Xamarin 會直接與 Xcode 整合, 所以您可以使用 Xcode 的_Interface Builder_來建立和維護 Appkit 控制項 (或選擇直接在程式碼中C#建立它們)。

AppKit 控制項是用來建立 Xamarin. Mac 應用程式使用者介面的 UI 元素。 這些專案是由按鈕、標籤、文字欄位、核取方塊和分割的控制項等元素組成, 並會在使用者操作時造成立即動作或可見結果。

[![](standard-controls-images/intro01.png "範例應用程式主畫面")](standard-controls-images/intro01.png#lightbox)

在本文中, 我們將討論在 Xamarin. Mac 應用程式中使用 AppKit 控制項的基本概念。 強烈建議您先流覽[Hello, Mac](~/mac/get-started/hello-mac.md)文章, 特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介, 其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要查看[Xamarin. Mac 內部](~/mac/internals/how-it-works.md)檔`Register`的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md)一節, 它會說明用來將C#類別連線到`Export`的和命令。目標-C 物件和 UI 元素。

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>控制項和視圖簡介

macOS (先前稱為 Mac OS X) 會透過 AppKit 架構提供一組標準的使用者介面控制項。 這些專案是由按鈕、標籤、文字欄位、核取方塊和分割的控制項等元素組成, 並會在使用者操作時造成立即動作或可見結果。

所有的 AppKit 控制項都有一個適用于大部分用途的標準內建外觀, 有些則指定要在視窗框架區域或_Vibrance 效果_內容中使用的替代外觀, 例如在提要欄位或通知中心中機械.

使用 AppKit 控制項時, Apple 建議下列指導方針:

- 避免在相同的視圖中混用控制項大小。
- 一般來說, 請避免垂直調整控制項大小。
- 在控制項內使用系統字型和適當的文字大小。
- 在控制項之間使用適當的間距。

如需詳細資訊, 請 pleas 參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[關於控制項和 Views](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)一節。

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>在視窗框架中使用控制項

有一個 AppKit 控制項子集, 其中包括顯示樣式, 可讓它們包含在視窗的框架區域中。 如需範例, 請參閱郵件應用程式的工具列:

[![](standard-controls-images/mailapp.png "Mac 視窗框架")](standard-controls-images/mailapp.png#lightbox)

- **圓形按鈕**- `NSButton`具有樣式的`NSTexturedRoundedBezelStyle`。
- **紋理的圓角分割控制項**- `NSSegmentedControl`具有樣式的`NSSegmentStyleTexturedRounded`。
- **紋理的圓角分割控制項**- `NSSegmentedControl`具有樣式的`NSSegmentStyleSeparated`。
- **圓形紋理快顯功能表**- `NSPopUpButton`具有樣式的`NSTexturedRoundedBezelStyle`。
- **圓形的向下拖曳功能表**- `NSPopUpButton`具有樣式的`NSTexturedRoundedBezelStyle`。
- **搜尋**列-A `NSSearchField`。

在視窗框架中使用 AppKit 控制項時, Apple 建議下列指導方針:

- 不要在視窗主體中使用視窗框架特定的控制項樣式。
- 不要在視窗框架中使用視窗主體控制項或樣式。

如需詳細資訊, 請 pleas 參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[關於控制項和 Views](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)一節。

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>在 Interface Builder 中建立使用者介面

當您建立新的 Xamarin Cocoa 應用程式時, 預設會取得標準的空白視窗。 此視窗會在專案中`.storyboard`自動包含的檔案中定義。 若要編輯您的 windows 設計, 請在 **方案總管**中`Main.storyboard`按兩下該檔案:

[![](standard-controls-images/edit01.png "選取方案總管中的主要腳本")](standard-controls-images/edit01.png#lightbox)

這會在 Xcode 的 Interface Builder 中開啟視窗設計:

[![](standard-controls-images/edit02.png "在 Xcode 中編輯分鏡腳本")](standard-controls-images/edit02.png#lightbox)

若要建立您的使用者介面, 您可以將 UI 元素 (AppKit 控制項) 從連結**庫偵測器**拖曳至 Interface Builder 中的**介面編輯器**。 在下列範例中,**垂直分割視圖**控制項是來自連結**庫偵測器**的藥物, 並放在 [**介面編輯器**] 中的視窗上:

[![](standard-controls-images/edit03.png "從程式庫選取分割視圖")](standard-controls-images/edit03.png#lightbox)

如需有關在 Interface Builder 中建立使用者介面的詳細資訊, 請參閱我們的[Xcode 和 Interface Builder 檔簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)。

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>調整大小和位置

一旦控制項已包含在使用者介面中, 請使用 [**條件約束編輯器**] 來設定其位置和大小, 方法是手動輸入值, 並控制當父視窗或視圖調整大小時, 控制項的自動定位和大小:

[![](standard-controls-images/edit04.png "設定條件約束")](standard-controls-images/edit04.png#lightbox)

使用 [ **Autoresizing** ] 方塊外的**紅色字形狀**, 將控制項放在  指定的 (x, y) 位置。 例如： 

[![](standard-controls-images/edit05.png "編輯條件約束")](standard-controls-images/edit05.png#lightbox)

指定選取的控制項 (在 [階層**視圖** & **介面編輯器**] 中) 會在調整大小或移動時卡在視窗或視圖的頂端和右側位置。 

編輯器控制項屬性的其他元素, 例如 Height 和 Width:

[![](standard-controls-images/edit06.png "設定高度")](standard-controls-images/edit06.png#lightbox)

您也可以使用**對齊編輯器**, 控制具有條件約束之元素的對齊方式:

[![](standard-controls-images/edit07.png "對齊編輯器")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> 不同于 iOS, 其中 (0, 0) 是螢幕的左上角, 在 macOS (0, 0) 是左下角。 這是因為 macOS 會使用數學座標系統, 數值的數值會向上和向右增加。 將 AppKit 控制項放在使用者介面時, 您必須將這一點納入考慮。

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>設定自訂類別

有時候, 使用 AppKit 控制項時, 您將需要子類別化和現有控制項, 並建立該類別的自訂版本。 例如, 定義來源清單的自訂版本:

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

`SourceListView`其中, `[Register("SourceListView")]`指令會將類別公開至目標-C, 以便在 Interface Builder 中使用。 如需詳細資訊, 請參閱[Xamarin 內部](~/mac/internals/how-it-works.md)檔的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md)一節, 它會說明用`Register`來`Export`將C#類別連線到的和命令。目標-C 物件和 UI 元素。

在上述程式碼準備就緒之後, 您可以將所擴充之基底類型的 AppKit 控制項拖曳到設計介面 (在下列範例中為**來源清單**), 切換到身分**識別偵測器**, 並將**自訂類別**設定為以下的名稱:您已公開至目標-C ( `SourceListView`範例):

[![](standard-controls-images/edit10.png "在 Xcode 中設定自訂類別")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>公開輸出和動作

在程式碼中C#存取 AppKit 控制項之前, 必須先將它公開為「**輸出**」或「**動作**」。 若要這麼做, 請在**介面**階層或**介面編輯器**中選取指定的控制項, 並切換至 [**助理] 視圖**( `.h`確定您已選取要編輯之視窗的):

[![](standard-controls-images/edit11.png "選取要編輯的正確檔案")](standard-controls-images/edit11.png#lightbox)

從 [AppKit] 控制項拖曳至 [提供`.h`檔案], 以開始建立**輸出**或**動作**:

[![](standard-controls-images/edit12.png "拖曳以建立輸出或動作")](standard-controls-images/edit12.png#lightbox)

選取 建立並讓公開的型別 **插座** 或是 **動作** **名稱** : 

[![](standard-controls-images/edit13.png "設定輸出或動作")](standard-controls-images/edit13.png#lightbox)


如需使用**輸出**和**動作**的詳細資訊, 請參閱[Xcode 和 Interface Builder 檔簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)的[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)一節。

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>與 Xcode 同步處理變更

當您從 Xcode 切換回 Visual Studio for Mac 時, 您在 Xcode 中所做的任何變更都會自動與您的 Xamarin. Mac 專案同步處理。

`SplitViewController.designer.cs`如果您在**方案總管**中選取, 您就可以在我們C#的程式碼中看到您的**輸出口**和**動作**如何連接:

[![](standard-controls-images/sync01.png "與 Xcode 同步處理變更")](standard-controls-images/sync01.png#lightbox)

請注意檔案中`SplitViewController.designer.cs`的定義:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

在 Xcode 中, 使用檔案中`MainWindow.h`的定義來對齊:

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

如您所見, Visual Studio for Mac 會接聽檔案的變更`.h` , 然後自動同步處理個別`.designer.cs`檔案中的變更, 以將它們公開給您的應用程式。 您可能也會注意`SplitViewController.designer.cs`到, 是部分類別, 因此 Visual Studio for Mac 不需要進行修改`SplitViewController.cs` , 這會覆寫對類別所做的任何變更。

您通常不需要`SplitViewController.designer.cs`自行開啟, 只是為了教育目的而在此呈現。

> [!IMPORTANT]
> 在大多數情況下, Visual Studio for Mac 會自動查看在 Xcode 中所做的任何變更, 並將它們同步處理至您的 Xamarin. Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>使用按鈕

AppKit 提供數種類型的按鈕, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[按鈕](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)一節。 

[![](standard-controls-images/buttons01.png "不同按鈕類型的範例")](standard-controls-images/buttons01.png#lightbox)

如果按鈕已透過**插座**公開, 下列程式碼將會回應按下它:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

針對透過**動作**公開的按鈕, `public partial`系統會使用您在 Xcode 中選擇的名稱, 自動為您建立方法。 若要回應**動作**, 請在定義**動作**的類別中, 完成部分方法。 例如：

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

針對具有狀態的按鈕 (例如**On**和**Off**), 可以使用`State`屬性對`NSCellStateValue`列舉進行檢查或設定狀態。 例如：

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是:

- [**開啟**] 按鈕會推送, 或選取控制項 (例如核取方塊中的 [簽入])。
- **Off** -不會推送按鈕, 或未選取控制項。
- **混合**-**開啟**和**關閉**狀態的混合。

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>將按鈕標示為預設值, 並設定對等的金鑰

對於您已加入至使用者介面設計的任何按鈕, 您可以將該按鈕標示為_預設_按鈕, 以在使用者按下鍵盤上的**Return/Enter**鍵時啟用。 在 macOS 中, 這個按鈕預設會收到藍色背景色彩。

若要將按鈕設定為預設值, 請在 Xcode 的 Interface Builder 中選取它。 接下來, 在 [**屬性偵測器**] 中選取索引鍵對**等**欄位, 然後按**Return/Enter**鍵:

[![](standard-controls-images/buttons03.png "編輯對等的金鑰")](standard-controls-images/buttons03.png#lightbox)

同樣地, 您可以使用鍵盤而不是滑鼠, 指派可用來啟動按鈕的任何按鍵順序。 例如, 在上圖中按下的命令-C 鍵。

當應用程式執行時, 如果使用者按下命令-C, 則會啟動按鈕的動作 (如同使用者已按一下按鈕的情況), 而該視窗的按鈕為 [按鍵且焦點]。

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>使用核取方塊和選項按鈕

AppKit 提供數種類型的核取方塊和選項按鈕群組, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[按鈕](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)一節。 

[![](standard-controls-images/buttons02.png "可用核取方塊類型的範例")](standard-controls-images/buttons02.png#lightbox)


核取方塊和選項按鈕 (透過**輸出**公開) 具有狀態 (例如**On**和**Off**), 可以`State` `NSCellStateValue`使用屬性對列舉進行檢查或設定狀態。 例如：

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是:

- [**開啟**] 按鈕會推送, 或選取控制項 (例如核取方塊中的 [簽入])。
- **Off** -不會推送按鈕, 或未選取控制項。
- **混合**-**開啟**和**關閉**狀態的混合。

若要選取選項按鈕群組中的按鈕, 請公開選項按鈕以選取做為**輸出**, 並設定`State`其屬性。 例如：

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

若要取得做為群組的選項按鈕集合, 並自動處理選取的狀態, 請建立新的**動作**, 並將群組中的每個按鈕附加到其中:

![](standard-controls-images/buttons04.png "建立新動作")

接下來, 在屬性`Tag`偵測**器**中, 為每個選項按鈕指派唯一的:

![](standard-controls-images/buttons05.png "編輯選項按鈕標記")

儲存您的變更並返回 Visual Studio for Mac, 加入程式碼來處理所有選項按鈕附加到的**動作**:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

您可以使用`Tag`屬性來查看所選取的選項按鈕。

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>使用功能表控制項

AppKit 提供數種類型的功能表控制項, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[功能表控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1)一節。 

[![](standard-controls-images/menu01.png "範例功能表控制項")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>提供功能表控制項資料

可供 macOS 使用的功能表控制項可以設定為從內部清單填入下拉式清單 (可以在 Interface Builder 中預先定義或透過程式碼填入), 或提供您自己的自訂外部資料源。

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>使用內部資料

除了在 Interface Builder 中定義專案之外, Menu 控制項 (例如`NSComboBox`) 還提供一組完整的方法, 可讓您新增、編輯或刪除所維護之內部清單中的專案:

- `Add`-將新的專案加入至清單的結尾。
- `GetItem`-傳回指定索引處的專案。
- `Insert`-在清單中的指定位置插入新專案。
- `IndexOf`-傳回指定專案的索引。
- `Remove`-從清單中移除指定的專案。
- `RemoveAll`-移除清單中的所有專案。
- `RemoveAt`-移除指定索引處的專案。
- `Count`-傳回清單中的專案數。

> [!IMPORTANT]
> 如果您使用外部資料源 (`UsesDataSource = true`), 則呼叫上述任何方法將會擲回例外狀況。

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>使用外部資料源

您可以選擇性地使用外部資料源, 並為專案 (例如 SQLite 資料庫) 提供您自己的備份存放區, 而不是使用內建的內部資料來提供功能表控制項的列。

若要使用外部資料源, 您將建立功能表控制項的資料來源實例 (`NSComboBoxDataSource`例如), 並覆寫數個方法來提供必要的資料:

- `ItemCount`-傳回清單中的專案數。
- `ObjectValueForItem`-傳回指定之索引的專案值。
- `IndexOfItem`-傳回提供專案值的索引。
- `CompletedString`-傳回部分具類型專案值的第一個相符專案值。 只有在已啟用自動完成 (`Completes = true`) 時, 才會呼叫這個方法。

如需詳細資訊, 請參閱[使用資料庫](~/mac/app-fundamentals/databases.md)檔中的[資料庫和下拉式方塊](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes)一節。

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>調整清單的外觀

下列方法可用來調整功能表控制項的外觀:

- `HasVerticalScroller`-如果`true`為, 控制項將會顯示垂直捲動條。 
- `VisibleItems`-調整開啟控制項時顯示的專案數。 預設值為五 (5)。
- `IntercellSpacing`-藉由提供`NSSize` `Width` , 其中指定了左邊`Height`和右邊的邊界, 並指定專案前後的空格, 來調整指定專案周圍的空間量。
- `ItemHeight`-指定清單中每個專案的高度。

針對的下拉式類型`NSPopupButtons`, 第一個功能表項目會提供控制項的標題。 例如： 

[![](standard-controls-images/menu02.png "範例功能表控制項")](standard-controls-images/menu02.png#lightbox)

若要變更標題, 請將此專案公開為**插座**, 並使用如下所示的程式碼:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>操作選取的專案

下列方法和屬性可讓您操作功能表控制項清單中的選取專案:

- `SelectItem`-選取指定索引處的專案。
- `Select`-選取指定的專案值。
- `DeselectItem`-取消選擇指定索引處的專案。
- `SelectedIndex`-傳回目前選取專案的索引。
- `SelectedValue`-傳回目前所選取專案的值。

使用, 即可將專案顯示在清單頂端的指定索引處, 並在`ScrollItemAtIndexToVisible`顯示指定索引的專案之前, 將它向清單。 `ScrollItemAtIndexToTop`

<a name="Responding to Events" />

### <a name="responding-to-events"></a>回應事件

Menu 控制項提供下列事件來回應使用者互動:

- `SelectionChanged`-當使用者從清單中選取一個值時, 就會呼叫。
- `SelectionIsChanging`-在新的使用者選取的專案變成使用中的選取範圍之前呼叫。
- `WillPopup`-在顯示專案的下拉式清單之前呼叫。
- `WillDismiss`-在關閉專案的下拉式清單之前呼叫。

針對`NSComboBox`控制項, 它們包含與相同的所有事件`NSTextField`, 例如每次使用者編輯`Changed`下拉式方塊中的文字值時所呼叫的事件。

(選擇性) 您可以將專案附加至**動作**, 並使用如下所示的程式碼來回應使用者所觸發的**動作**, 以回應所選取 Interface Builder 中所定義的內部資料功能表項目:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

如需使用功能表和功能表控制項的詳細資訊, 請參閱[功能表](~/mac/user-interface/menu.md)和快顯[按鈕和下拉式清單](~/mac/user-interface/menu.md)檔。

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>使用選取控制項

AppKit 提供數種類型的選取控制項, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[選取控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1)一節。 

[![](standard-controls-images/select01.png "範例選擇控制項")](standard-controls-images/select01.png#lightbox)

有兩種方式可以追蹤選取控制項何時具有使用者互動, 方法是將其公開為**動作**。 例如：

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

或藉由將**委派**附加至`Activated`事件。 例如：

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

若要設定或讀取選取控制項的值, 請使用`IntValue`屬性。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

專用控制項 (例如色彩良好和影像) 具有其實數值型別的特定屬性。 例如：

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker`具有下列屬性, 可直接處理日期和時間:

- **DateValue** -目前的日期和時間值, 其`NSDate`為。
- [**本機**]-使用者的位置, `NSLocal`做為。
- **TimeInterval** -時間值, 其為`Double`。
- [**時區**]-使用者的時區, 做`NSTimeZone`為。

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>使用指標控制項

AppKit 提供數種類型的指標控制項, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[指標控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1)一節。 

[![](standard-controls-images/level01.png "範例指標控制項")](standard-controls-images/level01.png#lightbox)

有兩種方式可以追蹤指標控制項何時具有使用者互動, 方法是將它公開為**動作**或**輸出**, 然後將**委派**附加至`Activated`事件。 例如：

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

若要讀取或設定指標控制項的值, 請使用`DoubleValue`屬性。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

顯示時, 不應將不定和非同步進度指標繪製成動畫。 `StartAnimation`使用方法可在動畫顯示時啟動。 例如：

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

`StopAnimation`呼叫方法將會停止動畫。

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>使用文字控制項

AppKit 提供數種類型的文字控制項, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[文字控制項](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1)一節。 

[![](standard-controls-images/text01.png "範例文字控制項")](standard-controls-images/text01.png#lightbox)

針對文字欄位 (`NSTextField`), 可以使用下列事件來追蹤使用者互動:

- **已變更**-每當使用者變更欄位的值時, 就會引發。 例如, 在每個輸入的字元上。
- **EditingBegan** -當使用者選取要編輯的欄位時, 就會引發。
- **EditingEnded** -當使用者在欄位中按下 Enter 鍵或離開欄位時。

`StringValue`使用屬性來讀取或設定欄位的值。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

對於顯示或編輯數值的欄位, 您可以使用`IntValue`屬性。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView`提供具有內建格式的完整功能文本編輯和顯示區域。 `NSTextField`如同,`StringValue`使用屬性來讀取或設定區域的值。

如需在 Xamarin. Mac 應用程式中使用文字流覽的複雜範例範例, 請參閱[SourceWriter 範例應用程式](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是簡單的原始程式碼編輯器，可支援程式碼完成功能和簡單的語法反白顯示。

SourceWriter 程式碼有完整註解，在適當的情況下會提供從關鍵技術或方法到 Xamarin.Mac 指南文件中相關資訊的連結。

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>使用內容視圖

AppKit 提供數種類型的內容視圖, 可用於您的使用者介面設計。 如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[內容流覽](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)一節。

[![](standard-controls-images/content01.png "範例內容視圖")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Popover 是暫時性的 UI 元素, 可提供與特定控制項或螢幕區域直接相關的功能。 Popover 會浮動在包含其相關控制項或區域的視窗上方, 而且其框線會包含箭號來指示其出現的位置。

若要建立 popover, 請執行下列動作:

1. 開啟您想要新增 popover 的視窗檔案, 方法是在 [方案總管] 中按兩下該檔案  `.storyboard`
2. 將**View Controller**從連結**庫偵測器**拖曳至**介面編輯器**: 

    [![](standard-controls-images/content02.png "從程式庫選取視圖控制器")](standard-controls-images/content02.png#lightbox)
3. 定義**自訂視圖**的大小和版面配置: 

    [![](standard-controls-images/content04.png "編輯版面配置")](standard-controls-images/content04.png#lightbox)
4. 按一下滑鼠右鍵, 並從快顯視窗的來源拖曳至**View Controller**: 

    [![](standard-controls-images/content05.png "拖曳以建立 segue")](standard-controls-images/content05.png#lightbox)
5. 從快顯功能表中選取 [ **Popover** ]: 

    [![](standard-controls-images/content06.png "設定 segue 類型")](standard-controls-images/content06.png#lightbox)
6. 儲存您的變更, 並返回 Visual Studio for Mac 以與 Xcode 同步。

<a name="Tab_Views" />

### <a name="tab-views"></a>索引標籤視圖

索引標籤的流覽包含一個索引標籤清單 (看起來類似分割的控制項), 並結合一組稱為_窗格_的視圖。 當使用者選取新的索引標籤時, 將會顯示附加到該索引標籤的窗格。 每個窗格都包含自己的一組控制項。

在 Xcode 的 Interface Builder 中使用索引標籤視圖時, 請使用**屬性偵測器**來設定索引標籤的數目:

[![](standard-controls-images/content08.png "編輯索引標籤數目")](standard-controls-images/content08.png#lightbox)

選取**介面**階層中的每個索引標籤, 以設定其**標題**, 並將 UI 元素新增至其**窗格**:

[![](standard-controls-images/content09.png "編輯 Xcode 中的索引標籤")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>資料系結 AppKit 控制項

藉由在 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術, 您可以大幅減少必須撰寫和維護的程式碼數量, 以填入和使用 UI 元素。 您也可以進一步將您的支援資料 (_資料模型_) 與您的前端使用者介面 (_模型視圖控制器_) 分離, 讓您更容易維護、更有彈性的應用程式設計。

索引鍵/值編碼 (KVC) 是一種機制, 可讓您間接存取物件的屬性, 使用索引鍵 (特殊格式的字串) 來識別屬性, 而不是透過`get/set`執行個體變數或存取子方法 () 來存取它們。 藉由在您的 Xamarin. Mac 應用程式中執行符合索引鍵/值的存取子, 您可以存取其他 macOS 功能, 例如索引鍵/值觀察 (KVO)、資料系結、核心資料、Cocoa 系結和 scriptability。

如需詳細資訊, 請參閱資料系結[和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)檔的[簡單資料](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding)系結一節。


<a name="Summary" />

## <a name="summary"></a>總結

本文已詳細說明如何使用標準的 AppKit 控制項, 例如, 在 Xamarin 應用程式中使用按鈕、標籤、文字欄位、核取方塊和分割的控制項。 其中涵蓋將它們新增至 Xcode Interface Builder 中的使用者介面設計, 透過輸出和動作將其公開至程式碼, 並在程式C#代碼中使用 AppKit 控制項。

## <a name="related-links"></a>相關連結

- [MacControls (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccontrols)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [關於控制項和視圖](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
