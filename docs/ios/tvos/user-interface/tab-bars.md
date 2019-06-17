---
title: 使用 tvOS 在 Xamarin 中的索引標籤列控制器
description: 本文件說明如何使用 Xamarin 建置的 tvOS 應用程式中的索引標籤列控制器使用。 它提供的高階檢視 索引標籤列，並討論 索引標籤列項目、 分鏡腳本整合，以及索引標籤列項目。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: a0efc30fd9814e4da858c4e3e4e99990eccf102e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61164233"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>使用 tvOS 在 Xamarin 中的索引標籤列控制器

許多類型的 tvOS 應用程式，主要的瀏覽依現狀 索引標籤列在畫面頂端執行。 使用者會 swipes left 和 right 跨可能類別和下方所做的變更內容的區域，以反映使用者的選取範圍的清單。

[![](tab-bars-images/tab01.png "範例索引標籤列")](tab-bars-images/tab01.png#lightbox)

索引標籤列是半透明依預設，永遠會出現在畫面頂端。 在焦點中索引標籤將涵蓋在畫面頂端 140 像素，但會快速地消失時顯示焦點會轉移至下面的內容區域。

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>TvOS 中的索引標籤列

`UITabViewController`以類似方式運作，而且是 tvOS 的相似的目的，其方式就如同在 iOS 中，使用下列的主要差異：

- 不同於 iOS，它會顯示在畫面底部的  索引標籤 列在 tvOS 的索引標籤列佔用畫面頂端 140 像素，而預設半透明。
- 當焦點離開下方的區域內容 索引標籤列時，快速將會關閉畫面頂端的投影片索引標籤列，而被隱藏。 使用者可以點選 [功能表] 按鈕一次或上向上撥動[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)若要再次顯示索引標籤列。
- 向下撥動 Siri 遠端上會將焦點移至內容區域的第一個索引標籤列下方[可焦點化項目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)中所顯示的內容。 同樣地，這會隱藏索引標籤列，一旦焦點會轉移。
- 按一下以選取類別 索引標籤列中顯示，就會切換至，類別目錄的內容和焦點會切換至該檢視中的第一個可焦點化項目。
- 應該固定的索引標籤列中顯示的類別數目和所有類別應該在任何時間都都可存取、 指定的類別應該永遠不會停用。
- 索引標籤列不支援在 tvOS 的自訂。 此外，它們不會出現**更多**類別 （例如 iOS) 如果有多個類別，比可以放入索引標籤列。

Apple 已使用索引標籤列的下列建議：

- **使用索引標籤列，以邏輯方式組織內容**-使用索引標籤列以邏輯方式組織您的 tvOS 應用程式的運作方式與內容。 例如，精選、 上方圖表、 購買和搜尋。
- **加入通知使用者的新內容的徽章**-您可以選擇顯示徽章 (以白色的數字或驚嘆號的紅色 oval) 來通知使用者某個類別目錄中的新內容。
- **徽章盡量少用**-不會擾亂徽章 索引標籤列，並只會顯示它們，它們提供重要資訊給使用者。
- **限制類別目錄數目**-為降低複雜性並讓您的應用程式可管理、 未多載您的索引標籤列與使用類別並確保所有的類別為可見和不太過擁擠。 適合簡短標題。
- **不會停用類別**-所有的索引標籤 （類別） 應該永遠顯示並啟用在任何時間都。 如果指定的索引標籤會沒有任何內容，為什麼要提供給使用者的說明。 例如，[購買] 索引標籤會空白，如果使用者已不進行任何購買項目。

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>索引標籤列項目

在索引標籤列中的每個類別目錄 （索引標籤） 由索引標籤列項目 (`UITabBarItem`)。 Apple 會有下列建議，如使用索引標籤列項目：

- **使用文字基礎的索引標籤**-時的索引標籤列項目就能以圖示表示，Apple 建議使用的文字，只因為精簡的標題會比較容易解譯比圖示。
- **使用簡短、 有意義的名詞或是動詞**-索引標籤列項目應該清楚地轉送它包含，以及當它是一個簡單的名詞 （例如相片、 影片或音樂） 或動詞命令 （例如搜尋或 Play） 時最有用的內容。

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>索引標籤列和分鏡腳本

Xamarin.tvOS 應用程式中使用索引標籤列的最簡單方式是將它們新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 啟動新的 Xamarin.tvOS 應用程式，然後選取**tvOS** > **應用程式** > **索引標籤式應用程式**: 

    [![](tab-bars-images/tab02.png "選取標籤列應用程式")](tab-bars-images/tab02.png#lightbox)
1. 請依照所有提示字元建立新的 Xamarin.tvOS 方案。
1. 在  **Solution Pad**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 若要變更**圖示**或**標題**的指定類別目錄中，選取**索引標籤的工具列項目**如**檢視控制器**中**文件大綱**:

    [![](tab-bars-images/tab03a.png "索引標籤列項目中的文件大綱的檢視控制器")](tab-bars-images/tab03a.png#lightbox)
1. 然後在 [設定必要的屬性**小工具] 索引標籤**的**屬性總管**: 

    [![](tab-bars-images/tab03.png "小工具 索引標籤")](tab-bars-images/tab03.png#lightbox)
1. 若要新增新的類別 （索引標籤），請卸除**檢視控制器**拖曳到設計介面： 

    [![](tab-bars-images/tab04.png "檢視控制器")](tab-bars-images/tab04.png#lightbox)
1. 控制項按一下和拖曳**索引標籤檢視控制器**新**檢視控制器**。
1. 從快顯視窗中，選取**檢視控制器**加入新的檢視為 （類別） 的索引標籤： 

    [![](tab-bars-images/tab05.png "選取索引標籤")](tab-bars-images/tab05.png#lightbox)
1. IOS 設計工具中將 UI 項目來設計為正常現象，每個 Caterogies 內容區域的使用者介面版面配置。
1. 公開 （expose) 任何必要的事件，以使用您的 UI 控制項，在C#程式碼。
1. 命名您想要公開 （expose） 中的任何 UI 控制項C#程式碼。
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 啟動新的 Xamarin.tvOS 應用程式，然後選取**tvOS** > **應用程式** > **索引標籤式應用程式**: 

    [![](tab-bars-images/tab02vs.png "選取標籤列應用程式")](tab-bars-images/tab02vs.png#lightbox)
1. 請依照所有提示字元建立新的 Xamarin.tvOS 方案。
1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 若要變更**圖示**或**標題**的指定類別目錄中，選取**索引標籤的工具列項目**如**檢視控制器**中**文件大綱**:

    [![](tab-bars-images/tab03avs.png "文件大綱中的檢視控制器")](tab-bars-images/tab03avs.png#lightbox)
1. 然後在 [設定必要的屬性**小工具] 索引標籤**的**屬性總管**: 

    [![](tab-bars-images/tab03vs.png "小工具 索引標籤")](tab-bars-images/tab03vs.png#lightbox)
1. 若要新增新的類別 （索引標籤），將拖曳**檢視控制器**從**工具箱**拖放到設計介面： 

    [![](tab-bars-images/tab04vs.png "檢視控制器")](tab-bars-images/tab04vs.png#lightbox)
1. 控制項按一下和拖曳**索引標籤檢視控制器**新**檢視控制器**。
1. 從快顯視窗中，選取**檢視控制器**加入新的檢視為 （類別） 的索引標籤： 

    [![](tab-bars-images/tab05vs.png "選取索引標籤")](tab-bars-images/tab05vs.png#lightbox)
1. IOS 設計工具中加入 UI 項目設計為正常現象，每個 Caterogies 內容區域的使用者介面版面配置。
1. 公開 （expose) 任何必要的事件，以使用您的 UI 控制項，在C#程式碼。
1. 命名您想要公開 （expose） 中的任何 UI 控制項C#程式碼。
1. 儲存您的變更。
    
-----

> [!IMPORTANT]
> 雖然您可以指派事件，例如`TouchUpInside`UI 項目 (例如`UIButton`) 在 iOS 設計工具中，則會永遠不會呼叫因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用`Primary Action `事件建立事件處理常式 tvOS 的使用者介面項目時。

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>使用索引標籤列

使用`Items`的屬性`UITabBar`若要存取的集合`UITabBarItems`它包含零 (0) 索引陣列的形式。 `SelectedItem`屬性會傳回目前選取的索引標籤 (Category) 做為`UITabBarItem`。


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>使用索引標籤列項目

若要在指定的索引標籤 (紅色與白色文字 oval) 上顯示徽章，請使用下列程式碼：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

這會產生下列結果時執行：

[![](tab-bars-images/tab06.png "徽章 索引標籤列項目")](tab-bars-images/tab06.png#lightbox)

使用`Title`的屬性`UITabBarItem`若要變更標題和`Image`屬性變更的圖示。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的索引標籤列控制器。




## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
