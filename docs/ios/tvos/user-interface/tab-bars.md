---
title: 使用 tvOS Xamarin 中的索引標籤列控制站
description: 本文件說明如何使用索引標籤列的控制站在 tvOS 應用程式中使用 Xamarin 建置的。 它提供高層級的索引標籤列檢視，並討論 索引標籤列項目、 分鏡腳本整合和索引標籤列項目。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: ea782fc8d6a2ccef2cdd687ec467be6d49793fc0
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789316"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>使用 tvOS Xamarin 中的索引標籤列控制站

許多類型的 tvOS 應用程式，主要瀏覽會顯示成索引標籤列執行螢幕的頂端。 使用者 swipes left 和 right 跨可能類別和內容區域底下所做的變更以反映使用者的選取項目清單。

[![](tab-bars-images/tab01.png "範例索引標籤列")](tab-bars-images/tab01.png#lightbox)

索引標籤列是半透明依預設，永遠會出現在畫面頂端。 在焦點中索引標籤列將說明如何在螢幕頂端 140 像素，但會快速投影片離開焦點會轉移至下面的內容區域時。

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>TvOS 中的索引標籤列

`UITabViewController`類似的方式運作，以及 tvOS 上有類似的用途，如同在 iOS 中，具有下列主要差異：

- 不同索引標籤列上會出現在畫面底部的 iOS 中，於 tvOS 中的索引標籤列佔用螢幕頂端 140 像素，而且會依預設半透明。
- 當焦點離開以下內容區域的索引標籤列時，索引標籤列快速將投影片螢幕的頂端，而被隱藏。 使用者可以點選 [功能表] 按鈕一次或向上撥動上[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)再次顯示索引標籤列。
- 向下撥動 Siri 遙控器上會將焦點移至第一個索引標籤列下方的內容區域[可設定焦點的項目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)中所顯示的內容。 同樣地，這會隱藏索引標籤列一旦焦點會轉移。
- 按一下以選取類別 索引標籤列中顯示，將會切換至該類別目錄的內容和焦點將會切換到那個檢視中第一個可設定焦點的項目。
- 應該固定的索引標籤列中顯示的類別目錄數目和所有類別目錄應該在任何時間都可存取、 指定的類別應該永遠不會停用。
- 索引標籤列 tvOS 上不支援自訂。 此外，它們不會顯示**詳細**類別 （例如 iOS) 是否有更多的類別，比可放入索引標籤列。

Apple 具有下列索引標籤列所使用的建議：

- **以邏輯方式組織的內容使用 索引標籤列**-使用索引標籤列以邏輯方式組織 tvOS 應用程式的運作方式與內容。 例如，精選、 上方圖表、 購買和搜尋。
- **加入通知使用者的新內容的徽章**-（選擇性） 可以將徽章 (白色的數字或驚嘆號的紅色 oval) 顯示在通知中類別的新內容的使用者。
- **徽章盡量少用**-不干擾徽章與索引標籤列，並且只顯示它們提供重要資訊給使用者。
- **限制類別目錄數目**-若要降低複雜性和保留您的應用程式可管理、 未多載您類別的索引標籤列也確定所有的類別為可見，而不太過擁擠。 簡短標題的效果最佳。
- **請勿停用類別**-所有索引標籤 （類別） 應該永遠顯示並啟用在任何時間都。 如果指定的索引標籤上沒有任何內容，說明為什麼提供給使用者。 例如，[購買] 索引標籤會空白，如果使用者已不進行任何購買。

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>索引標籤列項目

在索引標籤列中的每個類別目錄 （索引標籤） 由索引標籤的工具列項目 (`UITabBarItem`)。 Apple 具有下列索引標籤列項目所使用的建議：

- **使用文字以基礎索引標籤**-時的索引標籤列項目是能夠以圖示表示，Apple 建議原因僅在於簡潔的標題為解譯比圖示變得更容易使用的文字。
- **使用簡短、 有意義的名詞或動詞**-索引標籤的工具列項目應該清楚地轉送包含並時 （例如相片、 電影或音樂） 簡單名詞和動詞命令 （例如搜尋或 Play） 最適合的內容。

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>索引標籤列和分鏡腳本

Xamarin.tvOS 應用程式中使用的索引標籤列的最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 啟動新的 Xamarin.tvOS 應用程式，然後選取**tvOS** > **應用程式** > **索引標籤式的應用程式**: 

    [![](tab-bars-images/tab02.png "選取索引標籤式的應用程式")](tab-bars-images/tab02.png#lightbox)
1. 遵循提示來建立新的 Xamarin.tvOS 方案的所有動作。
1. 在**方案板**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 若要變更**圖示**或**標題**的指定類別目錄中，選取**索引標籤的工具列項目**如**檢視控制器**中**文件大綱**:

    [![](tab-bars-images/tab03a.png "索引標籤列項目 檢視中的控制站文件大綱")](tab-bars-images/tab03a.png#lightbox)
1. 然後設定必要的屬性**Widget 索引標籤**的**屬性總管**: 

    [![](tab-bars-images/tab03.png "[小工具] 索引標籤")](tab-bars-images/tab03.png#lightbox)
1. 若要新增新的類別 （索引標籤），卸除**檢視控制器**拖曳到設計介面上： 

    [![](tab-bars-images/tab04.png "檢視控制器")](tab-bars-images/tab04.png#lightbox)
1. 控制項按一下和拖曳**索引標籤檢視控制器**新**檢視控制器**。
1. 從快顯視窗中，選取**檢視控制站**將新檢視 索引標籤 （類別） 為： 

    [![](tab-bars-images/tab05.png "選取索引標籤")](tab-bars-images/tab05.png#lightbox)
1. 將 UI 項目加入 iOS 設計工具中設計為 normal，每個 Caterogies 內容區域的使用者介面版面配置。
1. 公開 （expose) 任何必要的事件，若要使用 C# 程式碼將 UI 控制項。
1. 您想要在 C# 程式碼中公開 （expose） 任何 UI 控制項的名稱。
1. 儲存您的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 啟動新的 Xamarin.tvOS 應用程式，然後選取**tvOS** > **應用程式** > **索引標籤式的應用程式**: 

    [![](tab-bars-images/tab02vs.png "選取索引標籤式的應用程式")](tab-bars-images/tab02vs.png#lightbox)
1. 遵循提示來建立新的 Xamarin.tvOS 方案的所有動作。
1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 若要變更**圖示**或**標題**的指定類別目錄中，選取**索引標籤的工具列項目**如**檢視控制器**中**文件大綱**:

    [![](tab-bars-images/tab03avs.png "文件大綱 中檢視控制器")](tab-bars-images/tab03avs.png#lightbox)
1. 然後設定必要的屬性**Widget 索引標籤**的**屬性總管**: 

    [![](tab-bars-images/tab03vs.png "[小工具] 索引標籤")](tab-bars-images/tab03vs.png#lightbox)
1. 若要加入新的類別目錄 （索引標籤），拖曳**檢視控制器**從**工具箱**拖放到設計介面： 

    [![](tab-bars-images/tab04vs.png "檢視控制器")](tab-bars-images/tab04vs.png#lightbox)
1. 控制項按一下和拖曳**索引標籤檢視控制器**新**檢視控制器**。
1. 從快顯視窗中，選取**檢視控制站**將新檢視 索引標籤 （類別） 為： 

    [![](tab-bars-images/tab05vs.png "選取索引標籤")](tab-bars-images/tab05vs.png#lightbox)
1. 將 UI 項目加入 iOS 設計工具中設計為 normal，每個 Caterogies 內容區域的使用者介面版面配置。
1. 公開 （expose) 任何必要的事件，若要使用 C# 程式碼將 UI 控制項。
1. 您想要在 C# 程式碼中公開 （expose） 任何 UI 控制項的名稱。
1. 儲存您的變更。
    
-----

> [!IMPORTANT]
> 雖然您可以指派事件，例如`TouchUpInside`UI 項目 (例如`UIButton`) 中的 iOS 設計工具中，它會永遠不會呼叫因為 Apple TV 沒有觸控螢幕或支援觸控事件。 您應該一律使用`Primary Action `事件時建立事件處理常式 tvos 使用者介面項目。

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>使用索引標籤列

使用`Items`屬性`UITabBar`來存取集合的`UITabBarItems`它包含為零 (0) 索引的陣列。 `SelectedItem`屬性會傳回目前選取的索引標籤 （類別），做為`UITabBarItem`。


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>使用索引標籤列項目

若要顯示徽章上指定的索引標籤 (紅色與白色文字 oval)，請使用下列程式碼：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

這會產生下列結果時執行：

[![](tab-bars-images/tab06.png "索引標籤的工具列項目，與徽章")](tab-bars-images/tab06.png#lightbox)

使用`Title`屬性`UITabBarItem`要變更標題和`Image`屬性以變更的圖示。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的索引標籤列的控制站。




## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
