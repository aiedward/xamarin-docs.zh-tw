---
title: 工具列
description: 這篇文章描述工具列 Xamarin.Mac 應用程式中使用。 它涵蓋了在安裝 Xcode 和介面產生器中，將其公開給程式碼，並以程式設計方式使用它們的建立和維護工具列。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 729c5c69d80c52047585d1026d7c675f3267f34e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="toolbars"></a>工具列

_這篇文章描述工具列 Xamarin.Mac 應用程式中使用。它涵蓋了在安裝 Xcode 和介面產生器中，將其公開給程式碼，並以程式設計方式使用它們的建立和維護工具列。_

使用 Visual Studio for Mac Xamarin.Mac 開發人員能夠存取 macOS 開發人員使用 Xcode，包括工具列控制項可使用相同的 UI 控制項。 因為 Xamarin.Mac 直接整合 Xcode，便可使用 Xcode 的介面產生器來建立和維護的工具列項目。 也可以在 C# 中建立這些工具列項目。

MacOS 工具列會加入至視窗的上方區段，並提供簡易存取其功能與相關的命令。 工具列可以隱藏、 顯示，或自訂應用程式的使用者，並呈現這些工具列項目以各種方式。

本文件涵蓋使用工具列和工具列項目 Xamarin.Mac 應用程式中的基本概念。 

繼續之前，請閱讀[Hello、 Mac](~/mac/get-started/hello-mac.md)文章 — 特別[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段 — 一樣重要概念和技術將用於本指南涵蓋。

也看一下[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件。 它說明`Register`和`Export`用來連接至 OBJECTIVE-C 類別的 C# 類別的屬性。

## <a name="introduction-to-toolbars"></a>工具列的簡介

MacOS 應用程式中的任何視窗可包含的工具列：

![工具列範例視窗](toolbar-images/info01.png "範例包含視窗工具列")

工具列會提供簡單的方式來快速存取重要的應用程式的使用者或常用的功能。 例如，文件編輯應用程式可能會提供設定的文字色彩、 變更字型，或列印目前文件的工具列項目。

工具列可以顯示三種方式的項目：

1. **圖示和文字** 

     ![圖示和文字包含的工具列](toolbar-images/info02.png "圖示和文字包含的工具列")

2. **僅圖示** 

     ![僅限圖示的工具列](toolbar-images/info03.png "僅圖示的工具列")

3. **僅限文字** 

     ![純文字的工具列](toolbar-images/info04.png "純文字的工具列")

這些模式，以滑鼠右鍵按一下工具列從內容功能表中選取的顯示模式之間切換：

![工具列的內容功能表](toolbar-images/info05.png "工具列的內容功能表")

使用相同的功能表來顯示工具列在較小的大小：

![使用小圖示的工具列](toolbar-images/info06.png "包含小圖示的工具列")

功能表也可以自訂工具列：

![使用自訂工具列的對話方塊](toolbar-images/info07.png "用於自訂工具列 對話方塊")

在 Xcode 的介面產生器中工具列設定，請開發人員可以提供額外的工具列項目不是其預設組態的一部分。 加入和移除這些預先定義的項目，視應用程式的使用者可以自訂工具列。 當然，工具列可以重設為其預設組態。

工具列會自動連接到**檢視**功能表上，這讓使用者可加以隱藏、 顯示，並進行自訂：

![[檢視] 功能表與工具列相關項目](toolbar-images/info08.png "中 [檢視] 功能表與工具列相關的項目")

請參閱[功能表的內建功能](~/mac/user-interface/menu.md)文件以取得詳細資料。

此外，工具列已正確設定介面產生器中，應用程式將會自動保存工具列自訂跨多個應用程式的啟動。

本指南的下一節說明如何建立和維護使用 Xcode 的介面產生器的工具列，以及如何在程式碼中使用它們。

## <a name="setting-a-custom-main-window-controller"></a>設定自訂的主視窗的控制站

若要公開 （expose） 至 C# 程式碼透過插座和動作、 Xamarin.Mac 應用程式的 UI 項目必須使用自訂視窗的控制站：

1. 在 Xcode 的介面產生器中開啟應用程式的分鏡腳本。
2. 選取設計介面上的視窗控制站。
3. 切換至**識別 Inspector**並輸入"WindowController"做為**類別名稱**: 

    [![設定視窗的控制站的自訂類別名稱](toolbar-images/windowcontroller01.png "設定視窗的控制站的自訂類別名稱")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 儲存變更並返回 Visual Studio for Mac 同步處理。
5. A **WindowController.cs**檔案會加入至您的專案中**方案板**適用於 Mac 的 Visual Studio 中： 

    ![選取方案板 WindowController.cs](toolbar-images/windowcontroller02.png "選取 WindowController.cs 方案板中")

6. 重新開啟分鏡腳本 Xcode 的介面產生器中。
7. **WindowController.h**檔案可供使用： 

    [![WindowController.h 檔案](toolbar-images/windowcontroller03.png "WindowController.h 檔案")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>建立和維護在 Xcode 中的工具列

建立和維護使用 Xcode 的介面產生器工具列。 若要將工具列新增至應用程式中，編輯 應用程式的主要分鏡腳本 (在此情況下**Main.storyboard**) 按兩下**方案板**:

![開啟方案板 Main.storyboard](toolbar-images/edit01.png "Main.storyboard 開啟方案板中")

在**程式庫偵測器**，進入 「 工具 」**搜尋方塊**讓您更輕鬆地查看所有可用工具列項目：

![程式庫偵測器，篩選顯示的工具列項目](toolbar-images/edit02.png "程式庫偵測器，篩選顯示的工具列項目")

在視窗中拖曳工具列**介面編輯器**。 與選取工具列上，可以在 設定屬性來設定其行為**屬性偵測器**:

![屬性偵測器工具列](toolbar-images/edit04.png "屬性偵測器工具列")

同時會提供下列屬性：

1. **顯示**-控制工具列是否顯示圖示、 文字或兩者
2. **在啟動時顯示**-如果選取，工具列會預設為可見。
3. **可自訂**-如果選取，使用者可以編輯和自訂工具列。
4. **分隔符號**-如果選取，精簡的水平列會分隔工具列和視窗的內容。
5. **大小**-設定工具列的大小
6. **自動儲存**-如果選取，應用程式會持續跨應用程式會啟動使用者的工具列設定變更。

選取**自動儲存**選項，並保留所有其他屬性的預設值。 

開啟中的工具列之後**介面階層架構**，顯示自訂對話方塊，選取工具列項目：

![自訂工具列](toolbar-images/edit05.png "自訂工具列")

若要設定屬性的項目工具列中，設計預設工具列，則應用程式的一部分，並提供額外的工具列項目，讓使用者選取 自訂工具列時，請使用這個對話方塊。 若要將項目加入至工具列中, 拖曳從**程式庫偵測器**:

![程式庫偵測器](toolbar-images/edit06.png "程式庫偵測器")

可以加入下列的工具列項目：

- **影像的工具列項目**-自訂映像以圖示的工具列項目。
- **彈性空間工具列項目**-用來以後續的工具列項目構成的彈性空間。 例如，彈性空間的工具列項目後面接著一個或多個工具列項目，而另一個工具列項目會釘選至右側工具列的最後一個項目。
- **空間工具列項目**-固定在工具列上的項目之間的空間
- **分隔符號工具列項目**-兩個或多個工具列項目，為群組之間顯示分隔符號
- **自訂工具列項目**-可讓使用者自訂工具列
- **工具列項目列印**-可讓使用者開啟文件列印
- **顯示色彩工具列項目**-顯示標準系統色彩選擇器： 

     ![系統色彩選擇器](toolbar-images/edit07.png "系統色彩選擇器")

- **顯示字型工具列項目**-顯示標準的系統字型對話方塊： 

     ![字型選擇器](toolbar-images/edit08.png "字型選擇器")

> [!IMPORTANT]
> 隨著看更新版本，也可以為工具列增加許多標準 Cocoa UI 控制項，例如搜尋欄位、 分割的控制項，以及水平的滑桿。

### <a name="adding-an-item-to-a-toolbar"></a>將項目加入至工具列

若要將項目加入至工具列，選取 在工具列**介面階層架構**按一下其中一個項目，導致出現的 自訂 對話方塊。 接下來，將新的項目從**程式庫偵測器**至**允許工具列項目**區域：

![允許工具列中的項目工具列自訂對話方塊](toolbar-images/add01.png "允許工具列中的項目工具列自訂對話方塊")

為了確定新的項目是預設工具列的一部分，請將它拖曳至**預設工具列項目**區域： 

![重新排列工具列項目拖曳](toolbar-images/add02.png "拖曳重新排列工具列項目")

若要重新排列預設工具列項目，請拖曳左或向右中。

接下來，使用**屬性偵測器**設定項目的預設屬性：

![自訂工具列項目使用屬性偵測器](toolbar-images/add03.png "自訂使用屬性偵測器工具列項目")

同時會提供下列屬性：

- **映像名稱**-映像來當做項目的圖示
- **標籤**-為項目工具列中顯示的文字
- **調色盤標籤**-顯示中的項目文字**允許工具列項目**區域
- **標記**-選用的唯一識別項，可協助您識別程式碼中的項目。
- **識別項**-定義的工具列項目類型。 自訂的值可以用來選取程式碼中的工具列項目。
- **可選取**-如果選取此選項，項目將會做為開啟/關閉按鈕。

> [!IMPORTANT]
> 將項目加入**允許工具列項目**區域，但不是提供適用於使用者的自訂選項的預設工具列。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>將其他 UI 控制項加入至工具列

數個 Cocoa UI 項目，例如搜尋欄位，也可以分割的控制項加入至工具列。

若要這麼做，開啟 在工具列**介面階層架構**和選取的工具列項目，以開啟 自訂 對話方塊。 拖曳**搜尋欄位**從**程式庫偵測器**至**允許工具列項目**區域：

![使用工具列自訂對話方塊](toolbar-images/add05.png "使用工具列自訂對話方塊")

從這裡開始，請設定 [搜尋] 欄位，並將它公開給透過動作或輸出的程式碼中使用介面產生器。

## <a name="built-in-toolbar-item-support"></a>內建工具列項目支援

根據預設數個 Cocoa UI 項目 [標準] 工具列項目互動。 例如，拖曳**文字檢視**到應用程式的視窗，並將它填滿內容區域：

[![應用程式中加入文字檢視](toolbar-images/edit09.png "應用程式中加入文字檢視")](toolbar-images/edit09-large.png#lightbox)

儲存文件 Visual studio for Mac 與 Xcode 同步，執行應用程式，輸入一些文字，、 選取它，然後按一下傳回**色彩**工具列項目。 請注意，[文字] 檢視使用色彩選擇器：

![使用文字檢視及色彩選擇器的內建工具列功能](toolbar-images/edit10.png "文字檢視及色彩選擇器的內建工具列功能")

## <a name="using-images-with-toolbar-items"></a>使用影像的工具列項目

使用**影像的工具列項目**，任何點陣圖影像加入至**資源**資料夾 (以及指定的建置動作為**配套資源**) 可以顯示為圖示工具列上：

1. 在 Visual Studio for Mac 中**方案板**，以滑鼠右鍵按一下**資源**資料夾，然後選取**新增** > **加入檔案**.
2. 從**加入檔案**對話方塊方塊中，瀏覽至所需的映像、 加以選取，然後按一下**開啟**按鈕： 

    [![選取要加入的映像](toolbar-images/edit11.png "選取要加入的映像")](toolbar-images/edit11-large.png#lightbox)

3. 選取**複製**，檢查**針對所有選取的檔案使用相同的動作**，然後按一下**確定**:

    ![選取複製動作已加入的映像](toolbar-images/edit12.png "選取複製動作已加入的映像")

4. 在**方案板**，連按兩下**MainWindow.xib**在 Xcode 中開啟它。

5. 選取的工具列中**介面階層架構**按一下其中一個項目，開啟 [自訂] 對話方塊。

6. 拖曳**影像的工具列項目**從**程式庫偵測器**工具列**允許工具列項目**區域： 

    ![影像的工具列項目加入至允許的工具列項目區域](toolbar-images/edit14.png "影像的工具列項目加入至允許的工具列項目區域")

7. 在**屬性偵測器**，選取剛加入 Visual Studio 中適用於 Mac 的映像： 

    ![設定自訂的工具列項目影像](toolbar-images/edit15.png "設定的自訂影像的工具列項目")

8. 設定**標籤**至 「 資源回收筒 」 和**調色盤標籤**」 清除文件 」: 

    ![設定工具列項目標籤和調色盤標籤](toolbar-images/edit16.png "標籤和調色盤標籤設定工具列項目")

9. 拖曳**分隔符號工具列項目**從**程式庫偵測器**工具列**允許工具列項目**區域： 

    [![分隔符號的工具列項目加入至允許的工具列項目區域](toolbar-images/edit17.png "分隔符號工具列項目加入至允許的工具列項目區域")](toolbar-images/edit17-large.png#lightbox)

10. 分隔符號項目和 「 資源回收筒 」 項目拖曳**預設工具列項目**區域和工具列的順序從項目的集由左到右，如下所示 （色彩、 字型、 分隔符號、 資源回收筒、 有彈性的空間、 列印）： 

    ![預設工具列項目](toolbar-images/edit18.png "預設工具列項目")

11. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

執行應用程式確認預設會顯示新工具列：

![自訂的預設項目包含的工具列](toolbar-images/edit19.png "自訂的預設項目包含的工具列")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>公開插座和動作的工具列項目

若要存取工具列或在程式碼中的工具列項目，必須將它附加至輸出或動作：

1. 在**方案板**，連按兩下**Main.storyboard**在 Xcode 中開啟它。
2. 請確認自訂的類別中的主視窗控制站已指派 」 WindowController"**識別 Inspector**:

    [![使用身分識別的偵測器視窗的控制站設定為自訂類別](toolbar-images/edit20a.png "使用識別偵測器視窗的控制站設定的自訂類別")](toolbar-images/edit20a-large.png#lightbox)

3. 接下來，選取中的工具列項目**介面階層架構**: 

    ![在介面階層架構中選取的工具列項目](toolbar-images/edit20.png "介面階層架構中選取的工具列項目")  

4. 開啟**助理檢視**，選取**WindowController.h**檔案，並從工具列項目控制項拖曳**WindowController.h**檔案。
5. 設定**連接**類型**動作**，輸入 「 trashDocument"**名稱**，然後按一下**連接**按鈕： 

    [![設定的動作是工具列項目](toolbar-images/edit23.png "設定的動作是工具列項目")](toolbar-images/edit23-large.png#lightbox)

6. 公開**文字檢視**稱為 「 documentEditor"插座為**ViewController.h**檔案： 

    [![設定文字檢視插座](toolbar-images/edit24.png "設定輸出的文字檢視")](toolbar-images/edit24-large.png#lightbox)

7. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

在 Visual Studio for Mac，編輯**ViewController.cs**檔案，然後加入下列程式碼：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接著，編輯**WindowController.cs**檔案，然後加入下列程式碼的底部`WindowController`類別：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

當執行應用程式，**資源回收筒**工具列項目將會啟用：

![使用中的資源回收筒項目包含的工具列](toolbar-images/edit25.png "作用中的資源回收筒項目包含的工具列")

請注意，**資源回收筒**工具列項目現在可以用來刪除的文字。

## <a name="disabling-toolbar-items"></a>停用工具列項目

若要停用工具列上的項目，建立自訂`NSToolbarItem`類別並覆寫`Validate`方法。 然後，在介面產生器中，將自訂類型指派給您想要啟用/停用的項目。

若要建立自訂`NSToolbarItem`類別，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**.選取**一般** > **空類別**，輸入 「 ActivatableItem"**名稱**，然後按一下**新增**按鈕： 

![在 Visual Studio 中加入空的類別，適用於 Mac](toolbar-images/custom01.png "加入 Visual Studio 中的空類別，適用於 Mac")

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

按兩下**Main.storyboard**在 Xcode 中開啟它。 選取**資源回收筒**工具列項目上面所建立，並將其類別變更為"ActivatableItem 」，在**識別 Inspector**:

![設定自訂的工具列項目類別](toolbar-images/custom02.png "設定自訂的工具列項目類別")

建立稱為插座`trashItem`如**資源回收筒**工具列項目。 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。 最後，開啟**MainWindow.cs**並更新`AwakeFromNib`方法來讀取，如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

執行應用程式，並注意**資源回收筒**項目現已停用工具列中：

![非使用中的資源回收筒項目包含的工具列](toolbar-images/custom03.png "非作用中的資源回收筒項目包含的工具列")

## <a name="summary"></a>總結

這篇文章已取得詳細的探討使用工具列和工具列 Xamarin.Mac 應用程式中的項目。 它說明如何建立和維護 Xcode 的介面產生器中的工具列、 一些 UI 控制項自動使用方式工具列項目、 如何使用 C# 程式碼中的工具列以及如何啟用和停用工具列項目。


## <a name="related-links"></a>相關連結

- [MacToolbar （範例）](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [工具列的人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具列的簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
