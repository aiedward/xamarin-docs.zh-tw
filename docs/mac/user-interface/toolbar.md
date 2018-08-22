---
title: Xamarin.Mac 中的工具列
description: 這篇文章說明如何使用 Xamarin.Mac 應用程式中的工具列。 其中涵蓋在 Xcode 和 Interface Builder，將其公開到程式碼，並以程式設計方式處理它們的建立和維護的工具列。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 06faaf16ffd0adc64063bfa5a264c1895b9ca9cb
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250993"
---
# <a name="toolbars-in-xamarinmac"></a>Xamarin.Mac 中的工具列

_這篇文章說明如何使用 Xamarin.Mac 應用程式中的工具列。其中涵蓋在 Xcode 和 Interface Builder，將其公開到程式碼，並以程式設計方式處理它們的建立和維護的工具列。_

使用 Visual Studio for Mac 的 Xamarin.Mac 開發人員可以存取使用 Xcode，包括工具列控制項的 macOS 開發人員可使用相同的 UI 控制項。 由於 Xamarin.Mac 直接與 Xcode 整合，您可使用 Xcode 的 Interface Builder 來建立和維護的工具列項目。 這些工具列項目也可以在 C# 中建立。

在 macOS 中的工具列會新增至視窗的上方區段，並提供輕鬆存取其功能與相關的命令。 工具列可以隱藏、 顯示，或自訂應用程式的使用者，並以各種方式呈現工具列項目。

本文章涵蓋使用 工具列和工具列項目，Xamarin.Mac 應用程式中的基本概念。 

繼續之前，請閱讀[Hello，Mac](~/mac/get-started/hello-mac.md)發行項 — 特別[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)區段 — 因為它涵蓋的重要概念和技術，將在本指南中使用。

也看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件。 它會說明`Register`和`Export`用來連接到 OBJECTIVE-C 類別的 C# 類別的屬性。

## <a name="introduction-to-toolbars"></a>工具列的簡介

在 macOS 應用程式中的任何視窗可以包含工具列：

![範例視窗工具列](toolbar-images/info01.png "範例視窗工具列")

工具列會提供簡單的方式，來快速存取重要的應用程式的使用者或常用的功能。 例如，文件編輯的應用程式可能會提供設定的文字色彩、 變更字型，或列印目前文件的工具列項目。

工具列可以顯示三種方式中的項目：

1. **圖示和文字** 

     ![包含圖示和文字的工具列](toolbar-images/info02.png "包含圖示和文字的工具列")

2. **僅圖示** 

     ![僅限圖示的工具列](toolbar-images/info03.png "僅圖示的工具列")

3. **僅限文字** 

     ![純文字的工具列](toolbar-images/info04.png "純文字的工具列")

這些模式工具列上按一下滑鼠右鍵，然後從操作功能表選取顯示模式之間切換：

![工具列的 [內容] 功能表](toolbar-images/info05.png "工具列的內容功能表")

若要在較小的大小顯示在工具列中使用相同的功能表：

![包含小圖示的工具列](toolbar-images/info06.png "包含小圖示的工具列")

功能表也可自訂工具列：

![使用自訂工具列的對話方塊](toolbar-images/info07.png "用來自訂 [工具列] 對話方塊")

當設定 Xcode 的 Interface Builder 中的工具列，開發人員可以提供額外的工具列項目不是預設組態的一部分。 新增和移除這些預先定義的項目，視應用程式的使用者可以自訂工具列。 當然，工具列可以重設為其預設組態。

工具列會自動連線到**檢視**功能表上，可讓使用者隱藏、 顯示，並進行自訂：

![工具列相關檢視 功能表中的項目](toolbar-images/info08.png "工具列相關檢視 功能表中的項目")

請參閱[功能表的內建功能](~/mac/user-interface/menu.md)文件，如需詳細資訊。

此外，工具列已正確設定介面產生器 中，如果應用程式會自動保存工具列自訂跨多個應用程式的 launch 活動。

本指南的下一節會說明如何建立和維護的 Xcode 的 Interface Builder 的工具列，以及如何在程式碼中使用它們。

## <a name="setting-a-custom-main-window-controller"></a>設定自訂的主視窗的控制站

若要公開到 C# 程式碼透過輸出和動作、 Xamarin.Mac 應用程式的 UI 項目必須使用自訂視窗的控制站：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的分鏡腳本。
2. 選取設計介面上的視窗控制站。
3. 切換至**身分識別偵測器**，然後輸入 「 WindowController"作為**類別名稱**: 

    [![設定視窗控制器的自訂類別名稱](toolbar-images/windowcontroller01.png "設定視窗控制器的自訂類別名稱")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 儲存變更並返回 Visual Studio for Mac 中進行同步處理。
5. A **WindowController.cs**檔案會新增至您的專案中**Solution Pad**在 Visual Studio for Mac: 

    ![在 Solution Pad 中選取 WindowController.cs](toolbar-images/windowcontroller02.png "Solution Pad 中選取 WindowController.cs")

6. 重新開啟在 Xcode 的 Interface Builder 分鏡腳本。
7. **WindowController.h**檔案可供使用： 

    [![WindowController.h 檔案](toolbar-images/windowcontroller03.png "WindowController.h 檔案")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>建立和維護在 Xcode 中的工具列

建立和維護的 Xcode 的 Interface Builder 工具列的。 若要將工具列新增至應用程式中，編輯 應用程式的主要分鏡腳本 (在此情況下**Main.storyboard**) 按兩下**Solution Pad**:

![在 Solution Pad 中開啟 Main.storyboard](toolbar-images/edit01.png "開啟 Solution Pad 中的 Main.storyboard")

在**程式庫偵測器**，輸入 「 工具 」 中**搜尋方塊**讓您更輕鬆地查看所有可用工具列項目：

![程式庫偵測器中，篩選成顯示工具列項目](toolbar-images/edit02.png "的程式庫偵測器，篩選成顯示工具列項目")

在視窗中拖曳工具列**介面編輯器**。 與所選取的工具列，可以在 設定屬性來設定其行為**屬性偵測器**:

![屬性偵測器工具列](toolbar-images/edit04.png "屬性偵測器工具列")

同時會提供下列屬性：

1. **顯示**-控制工具列是否顯示圖示、 文字，或兩者
2. **在啟動看得到**-如果選取，工具列會預設為可見。
3. **可自訂**-如果選取，使用者可以編輯和自訂工具列。
4. **分隔符號**-如果選取，精簡型的水平列會區隔工具列與視窗的內容。
5. **大小**-設定工具列的大小
6. **自動儲存**-應用程式如果選取，會保存到應用程式啟動的使用者的工具列組態變更。

選取 **自動儲存**選項，並保留所有其他屬性的預設設定。 

在開啟中的工具列**介面階層架構**，方法是選取工具列項目顯示 [自訂] 對話方塊：

![自訂工具列](toolbar-images/edit05.png "自訂工具列")

若要設定項目屬於已工具列，來設計應用程式的 預設 工具列的屬性，並提供額外的工具列項目，讓使用者選取 自訂工具列時，請使用此對話方塊。 若要加入工具列中的項目，將它們從拖曳**程式庫偵測器**:

![程式庫偵測器](toolbar-images/edit06.png "程式庫偵測器")

可以新增下列工具列項目：

- **映像工具列項目**-自訂映像做為圖示的工具列項目。
- **有彈性空間工具列項目**-用來調整後續的工具列項目的彈性空間。 比方說，一或多個工具列項目後面有彈性空間的工具列項目，而另一個工具列項目會釘選到右側工具列的最後一個項目。
- **空間 工具列項目**-已修正在工具列上的項目之間的空間
- **分隔符號工具列項目**-兩個以上的工具列項目，群組之間顯示分隔符號
- **自訂工具列項目**-可讓使用者自訂工具列
- **列印工具列項目**-可讓使用者開啟文件列印
- **顯示色彩工具列項目**-會顯示標準系統色彩選擇器： 

     ![系統色彩選擇器](toolbar-images/edit07.png "系統色彩選擇器")

- **顯示字型工具列項目**-會顯示標準的系統字型對話方塊： 

     ![字型選擇器](toolbar-images/edit08.png "字型選取器")

> [!IMPORTANT]
> 將會稍後所示，許多標準 Cocoa UI 控制項，例如搜尋欄位、 分段的控制項，以及水平的滑桿也可以加入至工具列。

### <a name="adding-an-item-to-a-toolbar"></a>將項目加入至工具列

若要加入工具列中的項目，請選取 在工具列**介面階層架構**按一下其中一個項目，導致出現的 自訂 對話方塊。 接下來，將新的項目，從**程式庫偵測器**要**允許的工具列項目**區域：

![允許的工具列中的項目工具列自訂對話方塊](toolbar-images/add01.png "允許工具列中的項目工具列自訂對話方塊")

若要確定新的項目是預設工具列的一部分，將它拖曳至**預設工具列項目**區域： 

![重新排列工具列項目，藉由拖曳](toolbar-images/add02.png "依拖曳重新排列工具列項目")

若要重新排列預設工具列項目，請拖曳向左或右中。

接下來，使用**屬性偵測器**設定項目的預設屬性：

![自訂工具列項目使用屬性偵測器](toolbar-images/add03.png "自訂工具列項目使用屬性偵測器")

同時會提供下列屬性：

- **映像名稱**-做為項目圖示的映像
- **標籤**-要顯示在工具列中的項目文字
- **調色盤標籤**-顯示中的項目文字**允許的工具列項目**區域
- **標記**-選用的唯一識別碼，可協助識別程式碼中的項目。
- **識別項**-定義工具列項目類型。 自訂的值可用來在程式碼中，選取工具列項目。
- **可選取**-項目若選取此選項，就像開啟/關閉 按鈕。

> [!IMPORTANT]
> 將項目加入**允許的工具列項目**區域，但不是提供適用於使用者的自訂選項的預設工具列。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>將其他 UI 控制項加入至工具列

數個 Cocoa UI 項目，例如搜尋欄位和分段的控制項也可以加入至工具列。

若要這麼做，開啟 在工具列**介面階層架構**，然後選取工具列項目，開啟 自訂 對話方塊。 拖曳**搜尋欄位**從**程式庫偵測器**來**允許的工具列項目**區域：

![使用工具列自訂對話方塊](toolbar-images/add05.png "使用工具列自訂對話方塊")

從這裡開始，請設定 [搜尋] 欄位，並將它公開給透過動作或輸出的程式碼中使用介面產生器。

## <a name="built-in-toolbar-item-support"></a>內建工具列項目支援

根據預設，與標準工具列項目互動許多 Cocoa UI 項目。 例如，拖曳**文字檢視**到應用程式的視窗，並放在以填滿內容區域：

[![將文字檢視新增至應用程式](toolbar-images/edit09.png "應用程式中加入文字檢視")](toolbar-images/edit09-large.png#lightbox)

儲存文件，回到 Visual Studio for Mac 與 Xcode 同步處理、 執行應用程式、 輸入一些文字，加以選取，然後按一下**色彩**工具列項目。 請注意，[文字] 檢視使用色彩選擇器：

![使用文字檢視和色彩選取器的內建工具列功能](toolbar-images/edit10.png "文字檢視及色彩選擇器的內建工具列功能")

## <a name="using-images-with-toolbar-items"></a>使用工具列項目中的映像

使用**影像的工具列項目**，任何點陣圖影像加入至**資源**資料夾 (和指定的建置動作**配套資源**) 可以顯示為圖示的工具列上：

1. 在 Visual Studio for Mac，在**Solution Pad**，以滑鼠右鍵按一下**資源**資料夾，然後選取**新增** > **加入檔案**.
2. 從**加入檔案**對話方塊，瀏覽至所需的映像、 選取它們然後按一下**開啟**按鈕： 

    [![選取要加入的映像](toolbar-images/edit11.png "選取要加入的映像")](toolbar-images/edit11-large.png#lightbox)

3. 選取**複製**，檢查**針對所有選取的檔案使用相同的動作**，然後按一下**確定**:

    ![選取複製動作已加入的映像](toolbar-images/edit12.png "選取複製動作已加入的映像")

4. 在  **Solution Pad**，按兩下**MainWindow.xib**在 Xcode 中開啟它。

5. 選取工具列**介面階層架構**按一下其中一個項目，開啟 [自訂] 對話方塊。

6. 拖曳**影像的工具列項目**從**程式庫偵測器**工具列**允許的工具列項目**區域： 

    ![影像的工具列項目新增至允許的工具列項目區域](toolbar-images/edit14.png "影像的工具列項目新增至允許的工具列項目區域")

7. 在 **屬性偵測器**，選取剛新增的 Mac 在 Visual Studio 中的映像： 

    ![設定工具列項目的自訂映像](toolbar-images/edit15.png "設定工具列項目的自訂映像")

8. 設定**標籤**至 「 資源回收筒 」，**調色盤標籤**以 「 清除文件 」: 

    ![設定工具列項目標籤和調色盤標籤](toolbar-images/edit16.png "標籤和調色盤標籤設定工具列項目")

9. 拖曳**分隔符號工具列項目**從**程式庫偵測器**工具列**允許的工具列項目**區域： 

    [![分隔符號的工具列項目新增至允許的工具列項目區域](toolbar-images/edit17.png "分隔符號工具列項目加入至允許的工具列項目區域")](toolbar-images/edit17-large.png#lightbox)

10. 將分隔符號項目和 「 資源回收筒 」 項目拖曳**預設工具列項目**區域，並設定中的項目工具列的順序由左到右，如下所示 （色彩、 字型、 分隔符號、 資源回收筒、 有彈性空間、 列印）： 

    ![預設工具列項目](toolbar-images/edit18.png "預設工具列項目")

11. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

執行應用程式，以確認預設會顯示新的工具列：

![使用自訂的預設項目工具列](toolbar-images/edit19.png "與自訂的預設項目工具列")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>公開與輸出和動作的工具列項目

若要存取某個工具列或在程式碼中的工具列項目，必須將它附加至輸出或動作：

1. 在  **Solution Pad**，按兩下**Main.storyboard**在 Xcode 中開啟它。
2. 請確認"WindowController 「 已指派給主視窗控制器中的自訂類別**身分識別偵測器**:

    [![使用身分識別偵測器視窗的控制站設定的自訂類別](toolbar-images/edit20a.png "使用身分識別偵測器視窗的控制站設定的自訂類別")](toolbar-images/edit20a-large.png#lightbox)

3. 接下來，選取中的工具列項目**介面階層架構**: 

    ![選取 介面階層架構中的 工具列項目](toolbar-images/edit20.png "介面階層架構中，選取工具列項目")  

4. 開啟**助理檢視**，選取**WindowController.h**檔案，並按住 control 再拖曳工具列項目，若要從**WindowController.h**檔案。
5. 設定**連接**類型**動作**，輸入 「 trashDocument"**名稱**，然後按一下**Connect**按鈕： 

    [![設定工具列項目的動作](toolbar-images/edit23.png "設定工具列項目的動作")](toolbar-images/edit23-large.png#lightbox)

6. 公開**文字檢視**輸出稱為 「 documentEditor"作為**ViewController.h**檔案： 

    [![設定文字檢視輸出](toolbar-images/edit24.png "設定文字檢視輸出")](toolbar-images/edit24-large.png#lightbox)

7. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

在 Visual Studio for Mac 中，編輯**ViewController.cs**檔案，並新增下列程式碼：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接著，編輯**WindowController.cs**檔案，並新增下列程式碼的底部`WindowController`類別：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

執行應用程式時**垃圾桶**工具列項目將會啟用：

![與作用中的資源回收筒項目工具列](toolbar-images/edit25.png "與作用中的資源回收筒項目工具列")

請注意，**垃圾桶**工具列項目現在可以用來刪除的文字。

## <a name="disabling-toolbar-items"></a>停用工具列項目

若要停用工具列上的項目，建立自訂`NSToolbarItem`類別並覆寫`Validate`方法。 然後，在介面產生器中，請將自訂型別指派給您想要啟用/停用的項目。

若要建立自訂`NSToolbarItem`類別，以滑鼠右鍵按一下專案，然後選取**新增** > **新檔...**.選取**一般** > **空類別**，輸入 「 ActivatableItem"**名稱**，然後按一下**新增**按鈕： 

![在 Visual Studio 中加入空的類別，for Mac](toolbar-images/custom01.png "for Mac 將空的類別在 Visual Studio 中新增。")

接著，編輯**ActivatableItem.cs**檔案讀取，如下所示：

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

按兩下**Main.storyboard**在 Xcode 中開啟它。 選取 **垃圾桶**工具列項目上面所建立，並將其類別變更為"ActivatableItem 」，在**身分識別偵測器**:

![設定自訂的工具列項目類別](toolbar-images/custom02.png "設定工具列項目的自訂類別")

建立稱為輸出`trashItem`for**垃圾桶**工具列項目。 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。 最後，開啟**MainWindow.cs** ，並更新`AwakeFromNib`方法來讀取，如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

執行應用程式，並注意**垃圾桶**項目現在已停用工具列中：

![與非作用中的資源回收筒項目工具列](toolbar-images/custom03.png "與非作用中的資源回收筒項目工具列")

## <a name="summary"></a>總結

本文所深入了解使用工具列和工具列項目，Xamarin.Mac 應用程式中。 它說明如何建立及維護 Xcode 的 Interface Builder 中的工具列、 某些 UI 控制項自動運作方式與工具列項目、 如何使用 C# 程式碼，工具列以及如何啟用和停用工具列項目。


## <a name="related-links"></a>相關連結

- [MacToolbar （範例）](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [工具列的人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具列的簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
