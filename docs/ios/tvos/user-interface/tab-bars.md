---
title: 使用 Xamarin 中的 [tvOS] 索引標籤列控制器
description: 本檔說明如何使用以 Xamarin 建立的 tvOS 應用程式中的索引標籤列控制器。 它提供索引標籤欄的高階，並討論索引標籤列專案、分鏡腳本整合和索引標籤列專案。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a4adc692680c0556a3cfb07b1a9f45963a81d199
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022132"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>使用 Xamarin 中的 [tvOS] 索引標籤列控制器

針對許多類型的 tvOS 應用程式，主要導覽會以在畫面頂端執行的索引標籤列形式呈現。 使用者會在可能的類別清單和變更底下的內容區域之間撥動，以反映使用者的選取專案。

[![](tab-bars-images/tab01.png "Sample Tab Bar")](tab-bars-images/tab01.png#lightbox)

索引標籤列預設為半透明，而且一律會出現在畫面頂端。 在焦點時，索引標籤列會涵蓋螢幕的前140圖元，但當焦點移到下方的內容區域時，將會快速地滑出。

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>TvOS 中的索引標籤列

`UITabViewController` 以類似的方式運作，並與在 iOS 中的 tvOS 類似，但有下列主要差異：

- 不同于出現在畫面底部的 [iOS] 索引標籤列，tvOS 中的索引標籤列會佔用螢幕的前140圖元，且預設為半透明。
- 當焦點離開下方內容區域的索引標籤列時，索引標籤列會快速地滑出畫面頂端並加以隱藏。 使用者可以按下功能表按鈕一次，或在[Siri 遙控器](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)上向上滑動以再次顯示索引標籤列。
- Siri 遙控器上的向下輕量會將焦點移至索引標籤列下方的內容區域，以顯示內容中的第一個可[焦點專案](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)。 同樣地，這會在焦點轉移後隱藏索引標籤列。
- 按一下以選取顯示在索引標籤列中的類別將會切換至該類別的內容，並將焦點切換至該視圖中的第一個可設定焦點專案。
- 索引標籤列中顯示的類別目錄數目應該是固定的，而且所有的類別都應該隨時可供存取，而不應該停用指定的類別。
- 在 tvOS 上不支援自訂索引標籤列。 此外，如果 [] 索引標籤列的類別數目超過 [可容納]，則不會顯示 [**更多**] 類別（例如 iOS）。

Apple 具有下列使用索引標籤列的建議：

- 使用索引標籤**欄以邏輯方式組織內容**-使用索引標籤列，以邏輯方式組織您的 tvOS 應用程式所使用的內容。 例如，精選、熱門圖表、已購買和搜尋。
- 新增**徽章以通知使用者新內容**-您可以選擇是否要顯示徽章（紅色的橢圓形，加上白色數位或驚嘆號），以通知使用者類別中的新內容。
- **謹慎使用徽章**-不要讓索引標籤列與徽章混淆，而且只會在其提供重要資訊給使用者時顯示。
- **限制類別的數目**-若要降低複雜性並讓應用程式易於管理，請不要以類別多載您的索引標籤列，並確保所有的類別都是可見的，而且不會擁擠。 簡單、簡短的標題最適合。
- **不要停用類別**-所有索引標籤（類別）都應該隨時可見並啟用。 如果指定的索引標籤沒有內容，請為使用者提供原因說明。 例如，如果使用者未進行購買，則 [購買] 索引標籤會是空的。

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>索引標籤列專案

索引標籤欄中的每個類別目錄（索引標籤）都是以索引標籤欄專案（`UITabBarItem`）表示。 Apple 具有下列使用 Tab 鍵列專案的建議：

- **使用以文字為基礎**的索引標籤-在索引標籤欄專案可以表示為圖示時，Apple 建議只使用文字，因為簡單的標題比圖示更容易解讀。
- **使用簡短、有意義的名詞或動詞**-索引標籤欄專案應該清楚地轉送它所包含的內容，而且當它是簡單的名詞（例如相片、電影或音樂）或動詞（例如搜尋或播放）時，它的效果最好。

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>索引標籤列和分鏡腳本

在 tvOS 應用程式中使用索引標籤列的最簡單方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動新的 tvOS 應用程式，然後選取 [ **tvOS** ] > **應用程式** > 索引標籤**式應用程式**： 

    [![](tab-bars-images/tab02.png "Select Tabbed App")](tab-bars-images/tab02.png#lightbox)
1. 遵循所有提示來建立新的 tvOS 方案。
1. 在  **Solution Pad**中，按兩下 `Main.storyboard` 檔案，然後將它開啟以供編輯。
1. 若要變更指定分類的**圖示**或標題，請在 [**檔大綱**] 中選取**視圖控制器**的索引卷**標**列**專案**：

    [![](tab-bars-images/tab03a.png "The Tab Bar Item for the View Controller in the Document Outline")](tab-bars-images/tab03a.png#lightbox)
1. 然後在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中設定必要的屬性： 

    [![](tab-bars-images/tab03.png "The Widget Tab")](tab-bars-images/tab03.png#lightbox)
1. 若要加入新的類別目錄（索引標籤），請將**視圖控制器**放在設計介面上： 

    [![](tab-bars-images/tab04.png "A View Controller")](tab-bars-images/tab04.png#lightbox)
1. 從 [索引標籤**視圖控制器]** 中，按一下並拖曳至新的**視圖控制器**。
1. 從快顯中，選取 [ **view** controller]，將新的 view 新增為索引標籤（Category）： 

    [![](tab-bars-images/tab05.png "Select Tab")](tab-bars-images/tab05.png#lightbox)
1. 在 iOS 設計工具中新增 UI 元素，以將每個 Caterogies 內容區域的 UI 版面配置設計為正常。
1. 公開任何必要的事件，以便在程式碼中C#使用您的 UI 控制項。
1. 為您想要在程式碼中C#公開的任何 UI 控制項命名。
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動新的 tvOS 應用程式，然後選取 [ **tvOS** ] > **應用程式** > 索引標籤**式應用程式**： 

    [![](tab-bars-images/tab02vs.png "Select Tabbed App")](tab-bars-images/tab02vs.png#lightbox)
1. 遵循所有提示來建立新的 tvOS 方案。
1. 在 **方案總管**中，按兩下 `Main.storyboard` 檔案，然後將它開啟以供編輯。
1. 若要變更指定分類的**圖示**或標題，請在 [**檔大綱**] 中選取**視圖控制器**的索引卷**標**列**專案**：

    [![](tab-bars-images/tab03avs.png "The View Controller in the Document Outline")](tab-bars-images/tab03avs.png#lightbox)
1. 然後在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中設定必要的屬性： 

    [![](tab-bars-images/tab03vs.png "The Widget Tab")](tab-bars-images/tab03vs.png#lightbox)
1. 若要加入新的類別目錄（索引標籤），請從 [**工具箱**] 將 [ **View Controller** ] 拖曳至設計介面： 

    [![](tab-bars-images/tab04vs.png "A View Controller")](tab-bars-images/tab04vs.png#lightbox)
1. 從 [索引標籤**視圖控制器]** 中，按一下並拖曳至新的**視圖控制器**。
1. 從快顯中，選取 [ **view** controller]，將新的 view 新增為索引標籤（Category）： 

    [![](tab-bars-images/tab05vs.png "Select Tab")](tab-bars-images/tab05vs.png#lightbox)
1. 在 iOS 設計工具中新增 UI 元素，以將每個 Caterogies 內容區域的 UI 版面配置設計為正常。
1. 公開任何必要的事件，以便在程式碼中C#使用您的 UI 控制項。
1. 為您想要在程式碼中C#公開的任何 UI 控制項命名。
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以將 `TouchUpInside` 之類的事件指派給 iOS 設計工具中的 UI 專案（例如 `UIButton`），但絕不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 建立 tvOS 使用者介面元素的事件處理常式時，您應該一律使用 `Primary Action` 事件。

如需使用分鏡腳本的詳細資訊，請參閱我們的[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>使用索引標籤欄

使用 `UITabBar` 的 `Items` 屬性，存取其包含為零（0）索引陣列的 `UITabBarItems` 集合。 `SelectedItem` 屬性會以 `UITabBarItem`的形式傳回目前選取的索引標籤（類別）。

<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>使用索引標籤欄專案

若要在指定的索引標籤上顯示徽章（具有白色文字的紅色橢圓形），請使用下列程式碼：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

當執行時，會產生下列結果：

[![](tab-bars-images/tab06.png "A Tab Bar Item with badge")](tab-bars-images/tab06.png#lightbox)

使用 `UITabBarItem` 的 [`Title`] 屬性來變更 [標題] 和 [`Image`] 屬性，以變更圖示。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋設計和使用 tvOS 應用程式內的索引標籤列控制器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
