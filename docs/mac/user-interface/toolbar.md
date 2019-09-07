---
title: Xamarin 中的工具列
description: 本文說明如何在 Xamarin. Mac 應用程式中使用工具列。 其中涵蓋在 Xcode 和 Interface Builder 中建立和維護工具列、將其公開至程式碼，以及以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: cd2490bfad880d128f5eaeebd4aac58ad3a4d8fa
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772715"
---
# <a name="toolbars-in-xamarinmac"></a>Xamarin 中的工具列

_本文說明如何在 Xamarin. Mac 應用程式中使用工具列。其中涵蓋在 Xcode 和 Interface Builder 中建立和維護工具列、將其公開至程式碼，以及以程式設計方式使用它們。_

使用 Visual Studio for Mac 的 Xamarin 開發人員可以存取與使用 Xcode 的 macOS 開發人員相同的 UI 控制項，包括工具列控制項。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的 Interface Builder 來建立和維護工具列專案。 這些工具列專案也可以在中C#建立。

MacOS 中的工具列會新增至視窗的頂端區段，並可讓您輕鬆存取與其功能相關的命令。 應用程式的使用者可以隱藏、顯示或自訂工具列，而且可以用各種方式呈現工具列專案。

本文涵蓋在 Xamarin. Mac 應用程式中使用工具列和工具列專案的基本概念。 

在繼續之前，請先閱讀[Hello，Mac](~/mac/get-started/hello-mac.md)文章—特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋本指南將使用的重要概念和技術。

另請查看[Xamarin 內部](~/mac/internals/how-it-works.md)檔的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md)一節。 它會說明`Register`用`Export`來將類別連接C#至目標-C 類別的和屬性。

## <a name="introduction-to-toolbars"></a>工具列簡介

MacOS 應用程式中的任何視窗都可以包含工具列：

![包含工具列的範例視窗](toolbar-images/info01.png "包含工具列的範例視窗")

工具列提供簡單的方法，讓您的應用程式使用者快速存取重要或常用的功能。 例如，檔編輯應用程式可能會提供工具列專案來設定文字色彩、變更字型，或列印目前檔。

工具列可以用三種方式來顯示專案：

1. **圖示和文字** 

     ![具有圖示和文字的工具列](toolbar-images/info02.png "具有圖示和文字的工具列")

2. **僅圖示** 

     ![僅圖示工具列](toolbar-images/info03.png "僅圖示工具列")

3. **僅文字** 

     純![文本工具列]純(toolbar-images/info04.png "文本工具列")

在這些模式之間切換，方法是以滑鼠右鍵按一下工具列，然後從內容功能表中選取 [顯示模式]：

![工具列的內容功能表](toolbar-images/info05.png "工具列的內容功能表")

使用相同的功能表，以較小的大小顯示工具列：

![具有小圖示的工具列](toolbar-images/info06.png "具有小圖示的工具列")

此功能表也可讓您自訂工具列：

![用來自訂工具列的對話方塊](toolbar-images/info07.png "用來自訂工具列的對話方塊")

在 Xcode 的 Interface Builder 中設定工具列時，開發人員可以提供不屬於其預設設定的額外工具列專案。 然後，應用程式的使用者就可以視需要自訂工具列、新增和移除這些預先定義的專案。 當然，工具列可以重設為其預設設定。

工具列會自動連接到 [ **View** ] 功能表，讓使用者可以隱藏、顯示及自訂它：

![[視圖] 功能表中的工具列相關專案](toolbar-images/info08.png "[視圖] 功能表中的工具列相關專案")

如需詳細資訊，請參閱[內建的功能表功能](~/mac/user-interface/menu.md)檔。

此外，如果已在 Interface Builder 中正確設定工具列，應用程式將會自動儲存跨多個啟動應用程式的工具列自訂。

本指南的下一節將說明如何使用 Xcode 的 Interface Builder，以及如何在程式碼中使用它們，來建立和維護工具列。

## <a name="setting-a-custom-main-window-controller"></a>設定自訂主視窗控制器

若要透過輸出和C#動作將 UI 專案公開至程式碼，則 Xamarin 應用程式必須使用自訂視窗控制器：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的腳本。
2. 選取設計介面上的 [視窗控制器]。
3. 切換至身分**識別偵測器**，並輸入 "WindowController" 做為**類別名稱**： 

    [![設定視窗控制器的自訂類別名稱](toolbar-images/windowcontroller01.png "設定視窗控制器的自訂類別名稱")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 儲存您的變更，並返回 Visual Studio for Mac 以進行同步處理。
5. 在 Visual Studio for Mac 的**Solution Pad**中，會將**WindowController.cs**檔案新增至您的專案： 

    ![在 Solution Pad 中選取 WindowController.cs](toolbar-images/windowcontroller02.png "在 Solution Pad 中選取 WindowController.cs")

6. 在 Xcode 的 Interface Builder 中重新開啟分鏡腳本。
7. **WindowController**將可供使用： 

    [![WindowController .h]檔案(toolbar-images/windowcontroller03.png "WindowController .h")檔案](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>在 Xcode 中建立和維護工具列

工具列會使用 Xcode 的 Interface Builder 來建立及維護。 若要將工具列新增至應用程式，請在 [ **Solution Pad**] 中按兩下，以編輯應用程式的主要分鏡腳本（在此案例中為**主要**腳本）：

![在 Solution Pad 中開啟 Main.]腳本(toolbar-images/edit01.png "在 Solution Pad 中開啟 Main.")腳本

在 [連結**庫偵測器**] 中，于**搜尋**方塊中輸入 "tool"，讓您更輕鬆地查看所有可用的工具列專案：

連結![庫偵測器，已篩選成顯示工具列專案]連結(toolbar-images/edit02.png "庫偵測器，已篩選成顯示工具列專案")

將工具列拖曳至 [**介面編輯器**] 中的視窗。 選取工具列後，在**屬性偵測器**中設定屬性，以設定其行為：

![工具列的屬性偵測器](toolbar-images/edit04.png "工具列的屬性偵測器")

同時會提供下列屬性：

1. **顯示**-控制工具列是否顯示圖示、文字或兩者
2. **在啟動時顯示**-如果已選取，則預設會顯示工具列。
3. **可自訂**-如果已選取，使用者可以編輯和自訂工具列。
4. **分隔符號**-如果選取此選項，細條水平線會將工具列與視窗內容隔開。
5. **大小**-設定工具列的大小
6. **自動**儲存-如果選取此選項，應用程式將會在應用程式啟動時保存使用者的工具列設定變更。

選取 [**自動**儲存] 選項，並保留所有其他屬性的預設設定。 

在**介面**階層中開啟工具列之後，選取工具列專案來顯示自訂對話方塊：

![自訂工具列](toolbar-images/edit05.png "自訂工具列")

使用此對話方塊設定已包含在工具列中的專案屬性、設計應用程式的預設工具列，以及提供額外的工具列專案供使用者自訂工具列時選取。 若要將專案新增至工具列，請將它們從連結**庫偵測器**拖曳：

連結![庫偵測器]連結(toolbar-images/edit06.png "庫偵測器")

可以加入下列工具列專案：

- **影像工具列專案**-具有自訂影像做為圖示的工具列專案。
- **彈性空間工具列專案**-用來對齊後續工具列專案的彈性空間。 例如，一個或多個工具列專案，後面接著彈性空間工具列專案，另一個工具列專案會將最後一個專案釘選到工具列的右邊。
- **空間工具列專案**-工具列上專案之間的固定間距
- **分隔符號工具列專案**-兩個或多個工具列專案之間的可見分隔符號，用於分組
- **自訂工具列專案**-允許使用者自訂工具列
- **列印工具列專案**-允許使用者列印開啟的檔
- **顯示色彩工具列專案**-顯示標準系統色彩選擇器： 

     ![系統色彩選擇器](toolbar-images/edit07.png "系統色彩選擇器")

- **顯示字型工具列專案**-顯示標準系統字型對話方塊： 

     ![字型選取器](toolbar-images/edit08.png "字型選取器")

> [!IMPORTANT]
> 如稍後所見，許多標準 Cocoa UI 控制項（例如搜尋欄位、分段控制項和水準滑杆）也可以加入至工具列。

### <a name="adding-an-item-to-a-toolbar"></a>將專案加入至工具列

若要將專案加入至工具列，請選取 [**介面**] 階層中的工具列，然後按一下其中一個專案，使 [自訂] 對話方塊出現。 接下來，將新專案從 [程式庫] 偵測**器**拖曳至 [**允許的工具列專案**] 區域：

![工具列自訂對話方塊中允許的工具列專案](toolbar-images/add01.png "工具列自訂對話方塊中允許的工具列專案")

若要確定新專案是預設工具列的一部分，請將它拖曳至 [**預設工具列專案**] 區域： 

藉![由拖曳來重新排列工具列專案的]順序藉(toolbar-images/add02.png "由拖曳來重新排列工具列專案的")順序

若要重新排序預設工具列專案，請將其向左或向右拖曳。

接下來，使用 [**屬性偵測器**] 來設定專案的預設屬性：

![使用屬性偵測器自訂工具列專案](toolbar-images/add03.png "使用屬性偵測器自訂工具列專案")

同時會提供下列屬性：

- **影像名稱**-用來做為專案圖示的影像
- **標籤**-要在工具列中顯示之專案的文字
- [**調色板標籤**]-[**允許的工具列專案**] 區域中為專案顯示的文字
- **Tag** -選擇性的唯一識別碼，可協助您在程式碼中識別專案。
- **Identifier** -定義工具列專案類型。 您可以使用自訂值，在程式碼中選取工具列專案。
- 可**選取**-如果已核取，則專案的作用就像開啟/關閉按鈕。

> [!IMPORTANT]
> 將專案新增至 [**允許的工具列專案**] 區域，而不是預設的工具列，以提供使用者的自訂選項。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>將其他 UI 控制項新增至工具列

您也可以將數個 Cocoa UI 專案（例如搜尋欄位和分割的控制項）新增至工具列。

若要嘗試這麼做，請開啟**介面**階層中的工具列，然後選取工具列專案以開啟 [自訂] 對話方塊。 將 [程式庫] 偵測**器**的**搜尋欄位**拖曳至 [**允許的工具列專案**] 區域：

![使用工具列自訂對話方塊](toolbar-images/add05.png "使用工具列自訂對話方塊")

從這裡，使用 Interface Builder 來設定搜尋欄位，並透過動作或輸出將它公開給程式碼。

## <a name="built-in-toolbar-item-support"></a>內建工具列專案支援

有數個 Cocoa UI 元素預設會與標準工具列專案互動。 例如，將 [**文字] 視圖**拖曳至應用程式的視窗，並將它放在 [內容] 區域中：

[![將文字視圖加入應用程式](toolbar-images/edit09.png "將文字視圖加入應用程式")](toolbar-images/edit09-large.png#lightbox)

儲存檔、返回 Visual Studio for Mac 以與 Xcode 同步、執行應用程式、輸入一些文字、選取它，然後按一下 [**色彩**] 工具列專案。 請注意，[文字] 視圖會自動與色彩選擇器搭配運作：

![具有文字視圖和色彩選擇器的內建工具列功能](toolbar-images/edit10.png "具有文字視圖和色彩選擇器的內建工具列功能")

## <a name="using-images-with-toolbar-items"></a>使用含有工具列專案的影像

使用 [**影像] 工具列專案**，新增至 [**資源**] 資料夾的任何點陣圖影像（並指定配套**資源**的組建動作），都可以在工具列上顯示為圖示：

1. 在 Visual Studio for Mac 的**Solution Pad**中，以滑鼠右鍵按一下 [**資源**] 資料夾，然後選取 [**新增** > ] [**新增**檔案]。
2. 從 [**新增**檔案] 對話方塊中，流覽至所需的影像，選取它們，然後按一下 [**開啟**] 按鈕： 

    [![選取要新增的影像](toolbar-images/edit11.png "選取要新增的影像")](toolbar-images/edit11-large.png#lightbox)

3. 選取 [**複製**]，勾選 **[針對所有選取的檔案使用相同的動作**]，然後按一下 **[確定]** ：

    ![為新增的影像選取複製動作](toolbar-images/edit12.png "為新增的影像選取複製動作")

4. 在  **Solution Pad**中，按兩下**mainwindow.xaml Xib** ，在 Xcode 中開啟它。

5. 選取**介面**階層中的工具列，然後按一下其中一個專案，開啟 [自訂] 對話方塊。

6. 將 [**影像] 工具列專案**從 [連結**庫偵測器**] 拖曳至工具列的 [**允許的工具列專案**] 區域： 

    ![新增至 [允許的工具列專案] 區域的影像工具列專案](toolbar-images/edit14.png "新增至 [允許的工具列專案] 區域的影像工具列專案")

7. 在 [**屬性偵測器**] 中，選取剛才在 Visual Studio for Mac 中新增的映射： 

    ![設定工具列專案的自訂影像](toolbar-images/edit15.png "設定工具列專案的自訂影像")

8. 將**標籤**設為「垃圾桶」，並將 [**調色板] 標籤**設定為 [清除檔]： 

    ![設定工具列專案標籤和調色板標籤](toolbar-images/edit16.png "設定工具列專案標籤和調色板標籤")

9. 將 [連結**庫偵測器**] 中的 [**分隔符號] 工具列專案**拖曳至工具列的 [**允許的工具列專案**] 區域： 

    [![加入至 [允許的工具列專案] 區域的分隔符號工具列專案](toolbar-images/edit17.png "加入至 [允許的工具列專案] 區域的分隔符號工具列專案")](toolbar-images/edit17-large.png#lightbox)

10. 將分隔符號專案和「垃圾桶」專案拖曳到**預設工具列專案**區域，並依下列方式設定工具列專案的順序（色彩、字型、分隔符號、垃圾桶、彈性空間、列印）： 

    ![預設工具列專案](toolbar-images/edit18.png "預設工具列專案")

11. 儲存變更並返回 Visual Studio for Mac，以與 Xcode 同步。

執行應用程式，以確認預設會顯示新的工具列：

![具有自訂預設專案的工具列](toolbar-images/edit19.png "具有自訂預設專案的工具列")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>以輸出和動作公開工具列專案

若要存取程式碼中的工具列或工具列專案，必須將其附加至 [輸出] 或 [動作]：

1. 在  **Solution Pad**中，按兩下  **Main.** 腳本，在 Xcode 中開啟它。
2. 確定自訂類別 "WindowController" 已指派給身分**識別偵測器**中的主視窗控制器：

    [![使用身分識別偵測器來設定視窗控制器的自訂類別](toolbar-images/edit20a.png "使用身分識別偵測器來設定視窗控制器的自訂類別")](toolbar-images/edit20a-large.png#lightbox)

3. 接下來，選取**介面**階層中的工具列專案： 

    ![選取介面階層中的工具列專案](toolbar-images/edit20.png "選取介面階層中的工具列專案")  

4. 開啟 [**助理] 視圖**，選取 [ **WindowController** ] 檔案，然後從 [工具列] 專案中，將 [控制] 拖曳至 [ **WindowController** ] 檔案。
5. 將 [**連線類型] 設定為 [** **動作**]，輸入 "trashDocument" 作為**名稱**，然後按一下 [連線 **]** 按鈕： 

    [設定![工具列專案的動作]設定(toolbar-images/edit23.png "工具列專案的動作")](toolbar-images/edit23-large.png#lightbox)

6. 將**文字視圖**公開為**ViewController**中名為 "documentEditor" 的插座： 

    [設定![文本視圖的輸出]設定(toolbar-images/edit24.png "文本視圖的輸出")](toolbar-images/edit24-large.png#lightbox)

7. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

在 Visual Studio for Mac 中，編輯**ViewController.cs**檔案，並新增下列程式碼：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接著，編輯**WindowController.cs**檔案，並將下列程式碼新增至`WindowController`類別的底部：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

執行應用程式時，**垃圾桶**工具列專案將會處於作用中狀態：

![具有作用中垃圾桶專案的工具列](toolbar-images/edit25.png "具有作用中垃圾桶專案的工具列")

請注意，**垃圾桶**工具列專案現在可以用來刪除文字。

## <a name="disabling-toolbar-items"></a>停用工具列專案

若要停用工具列上的專案，請建立`NSToolbarItem`自訂類別並`Validate`覆寫方法。 然後，在 Interface Builder 中，將自訂類型指派給您要啟用/停用的專案。

若要建立自`NSToolbarItem`定義類別，請以滑鼠右鍵按一下專案，然後選取 [**加入** > **新**檔案 ...]。選取 **[一般] [**  > **空白類別**]，輸入 "ActivatableItem" 作為**名稱**，然後按一下 [**新增**] 按鈕： 

![在 Visual Studio for Mac 中新增空的類別](toolbar-images/custom01.png "在 Visual Studio for Mac 中新增空的類別")

接下來，編輯**ActivatableItem.cs**檔案以進行讀取，如下所示：

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

按兩下 [ **Main.** 腳本]，在 Xcode 中開啟它。 選取上方建立的**垃圾桶**工具列專案，並在身分**識別偵測器**中將其類別變更為 "ActivatableItem"：

![設定工具列專案的自訂類別](toolbar-images/custom02.png "設定工具列專案的自訂類別")

為垃圾桶工具列專案`trashItem`建立名為的插座。 儲存變更並返回 Visual Studio for Mac，以與 Xcode 同步。 最後，開啟**MainWindow.cs** ，並更新`AwakeFromNib`方法以進行讀取，如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

執行應用程式，並注意工具列中的**垃圾桶**專案現在已停用：

![具有非作用中垃圾桶專案的工具列](toolbar-images/custom03.png "具有非作用中垃圾桶專案的工具列")

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用工具列和工具列專案。 其中說明如何在 Xcode 的 Interface Builder 中建立和維護工具列、某些 UI 控制項如何自動使用工具列專案、如何在程式碼中C#使用工具列，以及如何啟用和停用工具列專案。

## <a name="related-links"></a>相關連結

- [MacToolbar （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/mactoolbar)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [工具列的人工介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具列簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
