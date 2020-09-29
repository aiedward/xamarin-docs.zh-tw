---
title: Xamarin 中的工具列
description: 本文說明如何在 Xamarin 應用程式中使用工具列。 它涵蓋了在 Xcode 和 Interface Builder 中建立和維護工具列、將它們公開給程式碼，並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 5a6c7a013a72d2ad0e80e305b8c4c550011a1737
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430776"
---
# <a name="toolbars-in-xamarinmac"></a>Xamarin 中的工具列

_本文說明如何在 Xamarin 應用程式中使用工具列。它涵蓋了在 Xcode 和 Interface Builder 中建立和維護工具列、將它們公開給程式碼，並以程式設計方式使用它們。_

使用 Visual Studio for Mac 的 Xamarin 開發人員可以存取相同的 UI 控制項，以供使用 Xcode 的 macOS 開發人員使用，包括工具列控制項。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的 Interface Builder 來建立和維護工具列專案。 您也可以在 c # 中建立這些工具列專案。

MacOS 中的工具列會新增至視窗的頂端區段，並可讓您輕鬆存取與其功能相關的命令。 您可以透過應用程式的使用者隱藏、顯示或自訂工具列，也可以透過各種方式來顯示工具列專案。

本文涵蓋在 Xamarin. Mac 應用程式中使用工具列和工具列專案的基本概念。 

繼續之前，請先閱讀 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章—特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，其中涵蓋本指南將使用的重要概念和技術。

另請參閱將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標-C 一節。 它說明 `Register` `Export` 用來將 c # 類別連接至目標 c 類別的和屬性。

## <a name="introduction-to-toolbars"></a>工具列簡介

MacOS 應用程式中的任何視窗都可以包含工具列：

![具有工具列的範例視窗](toolbar-images/info01.png "具有工具列的範例視窗")

工具列提供簡單的方法，讓您的應用程式使用者快速存取重要或常用的功能。 例如，檔編輯應用程式可能會提供用來設定文字色彩、變更字型或列印目前檔的工具列專案。

工具列可透過三種方式來顯示專案：

1. **圖示和文字** 

     ![具有圖示和文字的工具列](toolbar-images/info02.png "具有圖示和文字的工具列")

2. **僅圖示** 

     ![僅限圖示的工具列](toolbar-images/info03.png "僅限圖示的工具列")

3. **只有文字** 

     ![純文字工具列](toolbar-images/info04.png "純文字工具列")

在這些模式之間切換，方法是以滑鼠右鍵按一下工具列，然後從內容功能表中選取 [顯示模式]：

![工具列的內容功能表](toolbar-images/info05.png "工具列的內容功能表")

使用相同的功能表，以較小的大小顯示工具列：

![具有小圖示的工具列](toolbar-images/info06.png "具有小圖示的工具列")

此功能表也可讓您自訂工具列：

![用來自訂工具列的對話方塊](toolbar-images/info07.png "用來自訂工具列的對話方塊")

在 Xcode 的 Interface Builder 中設定工具列時，開發人員可以提供不是其預設設定一部分的額外工具列專案。 然後，應用程式的使用者可以自訂工具列，視需要新增和移除這些預先定義的專案。 當然，工具列可以重設為預設設定。

工具列會自動連接到 [ **View** ] 功能表，讓使用者可以隱藏、顯示並自訂：

![[View] 功能表中的工具列相關專案](toolbar-images/info08.png "[View] 功能表中的工具列相關專案")

如需詳細資訊，請參閱 [內建功能表功能](~/mac/user-interface/menu.md) 檔。

此外，如果在 Interface Builder 中正確設定工具列，應用程式將會自動在應用程式的多個啟動時保存工具列自訂。

本指南的後續各節將說明如何使用 Xcode 的 Interface Builder 建立和維護工具列，以及如何在程式碼中使用它們。

## <a name="setting-a-custom-main-window-controller"></a>設定自訂主視窗控制器

若要透過輸出和動作將 UI 元素公開到 c # 程式碼，Xamarin 應用程式必須使用自訂視窗控制器：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的分鏡腳本。
2. 選取設計介面上的視窗控制器。
3. 切換至身分 **識別偵測器** ，並輸入 "WindowController" 作為 **類別名稱**： 

    [![設定視窗控制器的自訂類別名稱](toolbar-images/windowcontroller01.png "設定視窗控制器的自訂類別名稱")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 儲存您的變更，並返回 Visual Studio for Mac 進行同步處理。
5. **WindowController.cs**檔案將會新增至您在 Visual Studio for Mac 中**Solution Pad**的專案中： 

    ![在 Solution Pad 中選取 [WindowController.cs]](toolbar-images/windowcontroller02.png "在 Solution Pad 中選取 [WindowController.cs]")

6. 在 Xcode 的 Interface Builder 中重新開啟分鏡腳本。
7. **WindowController .h**檔案將可供使用： 

    [![WindowController .h 檔案](toolbar-images/windowcontroller03.png "WindowController .h 檔案")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>在 Xcode 中建立和維護工具列

您可以使用 Xcode 的 Interface Builder 來建立和維護工具列。 若要將工具列加入至應用程式，請在此案例中編輯應用程式的主要分鏡腳本 (在此案例中，按兩下**Solution Pad**中**的主要腳本**) ：

![在 Solution Pad 中開啟 Main. 分鏡腳本](toolbar-images/edit01.png "在 Solution Pad 中開啟 Main. 分鏡腳本")

在 [連結 **庫偵測器**] 的 [ **搜尋** ] 方塊中輸入「工具」，讓您更容易看到所有可用的工具列專案：

![程式庫偵測器，篩選以顯示工具列專案](toolbar-images/edit02.png "程式庫偵測器，篩選以顯示工具列專案")

將工具列拖曳至 **介面編輯器**中的視窗。 選取工具列之後，設定屬性偵測 **器**中的屬性來設定其行為：

![工具列的屬性偵測器](toolbar-images/edit04.png "工具列的屬性偵測器")

同時會提供下列屬性：

1. **顯示** -控制工具列是否顯示圖示、文字或兩者
2. **啟動時可見** -如果選取此選項，預設會顯示工具列。
3. **可自訂** -如果選取此選項，使用者可以編輯和自訂工具列。
4. **分隔符號** -如果選取此選項，細水平線會將工具列與視窗的內容分開。
5. **大小** ：設定工具列的大小
6. **自動** 儲存-如果選取此選項，應用程式將會在應用程式啟動時保存使用者的工具列設定變更。

選取 [ **自動** 儲存] 選項，並保留所有其他屬性的預設設定。 

開啟 **介面**階層中的工具列之後，請選取工具列專案以顯示 [自訂] 對話方塊：

![自訂工具列](toolbar-images/edit05.png "自訂工具列")

您可以使用這個對話方塊來設定已是工具列一部分之專案的屬性、設計應用程式的預設工具列，以及提供額外的工具列專案，讓使用者在自訂工具列時選取。 若要將專案加入至工具列，請從連結 **庫偵測器**拖曳專案：

![程式庫偵測器](toolbar-images/edit06.png "程式庫偵測器")

您可以新增下列工具列專案：

- **影像工具列專案** -以自訂影像做為圖示的工具列專案。
- **彈性空間工具列專案** -用來調整後續工具列專案的彈性空間。 例如，一個或多個工具列專案，後面接著有彈性的空間工具列專案，另一個工具列專案會將最後一個專案釘選到工具列的右邊。
- **空間工具列專案** -工具列上專案之間的固定空間
- **分隔符號工具列專案** -兩個或多個工具列專案之間的可見分隔符號，用於群組
- **自訂工具列專案** -可讓使用者自訂工具列
- **列印工具列專案** -允許使用者列印開啟的檔
- **顯示色彩工具列專案** -顯示標準系統色彩選擇器： 

     ![系統色彩選擇器](toolbar-images/edit07.png "系統色彩選擇器")

- **顯示字型工具列專案** -顯示標準系統字型對話方塊： 

     ![字型選取器](toolbar-images/edit08.png "字型選取器")

> [!IMPORTANT]
> 如下所示，許多標準 Cocoa UI 控制項（例如搜尋欄位、分段的控制項和水準滑杆）也可以新增至工具列。

### <a name="adding-an-item-to-a-toolbar"></a>將專案新增至工具列

若要將專案加入至工具列，請選取 [ **介面** ] 階層中的工具列，然後按一下其中一個專案，導致 [自訂] 對話方塊出現。 接著，從連結 **庫偵測器** 將新專案拖曳至 [ **允許的工具列專案** ] 區域：

![工具列的 [自訂] 對話方塊中允許的工具列專案](toolbar-images/add01.png "工具列的 [自訂] 對話方塊中允許的工具列專案")

若要確定新專案是預設工具列的一部分，請將它拖曳到 **預設工具列專案** 區域： 

![拖曳以重新排列工具列專案](toolbar-images/add02.png "拖曳以重新排列工具列專案")

若要重新排列預設的工具列專案，請將其向左或向右拖曳。

接下來，使用 [屬性] 偵測 **器** 來設定專案的預設屬性：

![使用屬性偵測器自訂工具列專案](toolbar-images/add03.png "使用屬性偵測器自訂工具列專案")

同時會提供下列屬性：

- **影像名稱** -用來作為專案圖示的影像
- **標籤** -要在工具列中顯示專案的文字
- 選擇區**標籤**-**允許的工具列專案**區域中的專案所顯示的文字
- **標記** -可協助您在程式碼中識別專案的選擇性唯一識別碼。
- **識別碼** -定義工具列專案類型。 自訂值可以用來選取程式碼中的工具列專案。
- 可**選取**-如果已核取，則專案的作用就像開啟/關閉按鈕。

> [!IMPORTANT]
> 將專案加入至 [ **允許的工具列專案** ] 區域，而不是 [預設] 工具列，為使用者提供自訂選項。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>將其他 UI 控制項新增至工具列

您也可以將數個 Cocoa UI 元素（例如搜尋欄位和分段控制項）新增至工具列。

若要嘗試這項作業，請開啟 **介面** 階層中的工具列，然後選取工具列專案以開啟 [自訂] 對話方塊。 將 [ **搜尋] 欄位** 從 [連結 **庫** ] 偵測器拖曳到 [ **允許的工具列專案** ] 區域：

![使用工具列自訂對話方塊](toolbar-images/add05.png "使用工具列自訂對話方塊")

從這裡，使用 Interface Builder 設定搜尋欄位，並透過動作或輸出將其公開給程式碼。

## <a name="built-in-toolbar-item-support"></a>內建的工具列專案支援

依預設，有數個 Cocoa UI 元素會與標準工具列專案互動。 例如，將 [ **文字] 視圖** 拖曳到應用程式的視窗，並將其定位以填滿內容區域：

[![將文字視圖加入至應用程式](toolbar-images/edit09.png "將文字視圖加入至應用程式")](toolbar-images/edit09-large.png#lightbox)

儲存檔、返回 Visual Studio for Mac 與 Xcode 同步、執行應用程式、輸入一些文字、選取它，然後按一下 [ **色彩** ] 工具列專案。 請注意，文字視圖會自動與色彩選擇器搭配運作：

![具有文字視圖和色彩選擇器的內建工具列功能](toolbar-images/edit10.png "具有文字視圖和色彩選擇器的內建工具列功能")

## <a name="using-images-with-toolbar-items"></a>使用含有工具列專案的影像

使用 **影像工具列專案**時，新增至 [ **資源** ] 資料夾的任何點陣圖影像 (，並指定組合 **資源**) 的組建動作，可以在工具列上顯示為圖示：

1. 在 Visual Studio for Mac 的 [ **Solution Pad**中，以滑鼠右鍵按一下 [**資源**] 資料夾，然後選取 [**新增**  >  **新增**檔案]。
2. 從 [ **新增** 檔案] 對話方塊中，流覽至所需的影像，選取它們，然後按一下 [ **開啟** ] 按鈕： 

    [![選取要新增的影像](toolbar-images/edit11.png "選取要新增的影像")](toolbar-images/edit11-large.png#lightbox)

3. 選取 [ **複製**]，勾選 **[針對所有選取的檔案使用相同的動作**]，然後按一下 **[確定]**：

    ![選取已加入影像的複製動作](toolbar-images/edit12.png "選取已加入影像的複製動作")

4. 在 [ **Solution Pad**中，按兩下 [ **MainWindow xib** ]，在 Xcode 中開啟它。

5. 選取 **介面** 階層中的工具列，然後按一下其中一個專案，以開啟 [自訂] 對話方塊。

6. 將 **影像工具列專案** 從連結 **庫偵測器** 拖曳至工具列的 [允許的 **工具列專案** ] 區域： 

    ![新增至 [允許的工具列專案] 區域的影像工具列專案](toolbar-images/edit14.png "新增至 [允許的工具列專案] 區域的影像工具列專案")

7. 在 [ **屬性**] 偵測器中，選取剛在 Visual Studio for Mac 中新增的映射： 

    ![設定工具列專案的自訂影像](toolbar-images/edit15.png "設定工具列專案的自訂影像")

8. 將 **標籤** 設定為 [垃圾桶]，並將 [ **元件] 標籤** 設定為 [清除檔]： 

    ![設定工具列專案標籤和調色板標籤](toolbar-images/edit16.png "設定工具列專案標籤和調色板標籤")

9. 從連結**庫偵測器**將**分隔符號工具列專案**拖曳至工具列的 [**允許的工具列專案**] 區域： 

    [![新增至允許的工具列專案區域的分隔符號工具列專案](toolbar-images/edit17.png "新增至允許的工具列專案區域的分隔符號工具列專案")](toolbar-images/edit17-large.png#lightbox)

10. 將 [分隔符號] 專案和 [垃圾桶] 專案拖曳至 **預設工具列專案** 區域，然後將工具列專案的順序從左至右設定，如下所示 (色彩、字型、分隔符號、垃圾桶、彈性空間、列印) ： 

    ![預設的工具列專案](toolbar-images/edit18.png "預設的工具列專案")

11. 儲存變更並返回 Visual Studio for Mac，以與 Xcode 同步。

執行應用程式，以確認預設會顯示新的工具列：

![具有自訂預設專案的工具列](toolbar-images/edit19.png "具有自訂預設專案的工具列")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>使用輸出和動作公開工具列專案

若要存取程式碼中的工具列或工具列專案，它必須附加至輸出或動作：

1. 在 [ **Solution Pad**中，按兩下 [ **Main** ]，在 Xcode 中開啟。
2. 確定自訂類別 "WindowController" 已指派給身分 **識別偵測器**中的主視窗控制器：

    [![使用身分識別偵測器設定視窗控制器的自訂類別](toolbar-images/edit20a.png "使用身分識別偵測器設定視窗控制器的自訂類別")](toolbar-images/edit20a-large.png#lightbox)

3. 接下來，選取 **介面**階層中的工具列專案： 

    ![選取介面階層中的工具列專案](toolbar-images/edit20.png "選取介面階層中的工具列專案")  

4. 開啟 [ **助理] 視圖**，選取 **WindowController .h** 檔案，然後從工具列專案拖曳至 **WindowController .h** 檔案。
5. 將 [ **連線類型] 設定為 [** **動作**]，輸入 "trashDocument" 作為 **名稱**，然後按一下 [連線 **]** 按鈕： 

    [![設定工具列專案的動作](toolbar-images/edit23.png "設定工具列專案的動作")](toolbar-images/edit23-large.png#lightbox)

6. 將 **文字視圖** 公開為 **ViewController .h** 檔案中名為 "documentEditor" 的輸出： 

    [![設定文字視圖的輸出](toolbar-images/edit24.png "設定文字視圖的輸出")](toolbar-images/edit24-large.png#lightbox)

7. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

在 Visual Studio for Mac 中編輯 **ViewController.cs** 檔案，並新增下列程式碼：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接著，編輯 **WindowController.cs** 檔案，並將下列程式碼新增至 `WindowController` 類別底部：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

執行應用程式時， **垃圾桶** 工具列專案將會處於作用中狀態：

![具有作用中垃圾桶專案的工具列](toolbar-images/edit25.png "具有作用中垃圾桶專案的工具列")

請注意， **垃圾桶** 工具列專案現在可以用來刪除文字。

## <a name="disabling-toolbar-items"></a>停用工具列專案

若要停用工具列上的專案，請建立自訂 `NSToolbarItem` 類別並覆寫 `Validate` 方法。 然後，在 Interface Builder 中，將自訂類型指派給您想要啟用/停用的專案。

若要建立自訂 `NSToolbarItem` 類別，請以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 "ActivatableItem" 作為**名稱**，然後按一下 [**新增**] 按鈕： 

![在 Visual Studio for Mac 中新增空白類別](toolbar-images/custom01.png "在 Visual Studio for Mac 中新增空白類別")

接下來，編輯 **ActivatableItem.cs** 檔案，如下所示：

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

按兩下 [ **Main** ]，在 Xcode 中開啟。 選取上面建立的 **垃圾桶** 工具列專案，然後在身分 **識別偵測器**中將其類別變更為 "ActivatableItem"：

![設定工具列專案的自訂類別](toolbar-images/custom02.png "設定工具列專案的自訂類別")

建立 `trashItem` 針對 **垃圾桶** 工具列專案呼叫的輸出。 儲存變更並返回 Visual Studio for Mac，以與 Xcode 同步。 最後，開啟 **MainWindow.cs** ，並將 `AwakeFromNib` 方法更新為如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

執行應用程式，並注意工具列中的 **垃圾桶** 專案現在已停用：

![具有非作用中垃圾桶專案的工具列](toolbar-images/custom03.png "具有非作用中垃圾桶專案的工具列")

## <a name="summary"></a>摘要

本文將詳細說明如何在 Xamarin 應用程式中使用工具列和工具列專案。 它描述如何在 Xcode 的 Interface Builder 中建立和維護工具列、某些 UI 控制項如何自動使用工具列專案、如何在 c # 程式碼中使用工具列，以及如何啟用和停用工具列專案。

## <a name="related-links"></a>相關連結

- [MacToolbar (範例) ](/samples/xamarin/mac-samples/mactoolbar)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [適用于工具列的人類介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具列簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)