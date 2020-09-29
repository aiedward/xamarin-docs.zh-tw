---
title: 使用 Xamarin 中的 [tvOS] 索引標籤列控制器
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用索引標籤列控制器。 它提供了索引標籤列的高階視圖，並討論索引標籤列專案、分鏡腳本整合和索引標籤列專案。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 813a4de1966aa53e9c1447804deda8f55e5672f3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430275"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>使用 Xamarin 中的 [tvOS] 索引標籤列控制器

對於許多類型的 tvOS 應用程式，主要導覽會顯示為在畫面頂端執行的索引標籤列。 使用者會在可能的類別清單中向左和向右撥動，而變更底下的內容區域會反映使用者的選取專案。

[![範例索引標籤列](tab-bars-images/tab01.png)](tab-bars-images/tab01.png#lightbox)

依預設，索引標籤列是半透明的，且一律會出現在畫面頂端。 當焦點放在焦點時，索引標籤列會涵蓋畫面的前140圖元，但當焦點移至下方的內容區域時，將會快速地滑出。

<a name="Tab-Bars-in-tvOS"></a>

## <a name="tab-bars-in-tvos"></a>TvOS 中的索引標籤列

`UITabViewController`會以類似的方式運作，並在 tvOS 上提供類似的用途，因為它在 iOS 中有下列重要差異：

- 不同于出現在螢幕底部的 [iOS] 索引標籤列，tvOS 中的索引標籤列會佔用畫面的前140圖元，且預設為半透明。
- 當焦點離開下方內容區域的索引標籤列時，索引標籤列將會快速滑出畫面頂端並隱藏。 使用者可以按一下功能表按鈕一次，或在 [Siri 遙控器](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) 上向上滑動，以再次顯示索引標籤列。
- 在 Siri 遙控器上向下輕量會將焦點移至索引標籤列下方的內容區域，以顯示內容中的第一個可 [設定焦點專案](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) 。 同樣地，這會在焦點轉移之後隱藏索引標籤列。
- 按一下以選取顯示在索引標籤列中的類別，將會切換至該類別的內容，並將焦點切換至該視圖中的第一個可設定焦點專案。
- 在索引標籤列中顯示的分類數目應該是固定的，而且所有類別都應該隨時都可以存取，而不應該停用指定的類別。
- 索引標籤列不支援在 tvOS 上進行自訂。 此外，它們不會顯示 **更多** 類別 (例如 iOS) 如果有超過索引標籤列的分類。

Apple 有下列建議可使用索引標籤列：

- **使用 Tab 鍵以邏輯方式組織內容** -使用索引標籤列，以邏輯方式組織您的 tvOS 應用程式所使用的內容。 例如，精選、熱門圖表、購買和搜尋。
- 新增**徽章以通知使用者有新的內容**-您可以選擇性地以白數位或) 驚嘆號 (紅色橢圓來顯示徽章，以通知使用者類別中的新內容。
- **請謹慎使用徽章** -請勿讓索引標籤列與徽章混淆，並且只會顯示他們提供重要資訊給使用者的位置。
- **限制類別數目** ，以降低複雜度並讓您的應用程式易於管理，不要多載具有類別的索引標籤列，並確保所有類別都是可見的，而不是擁擠。 簡單、簡短標題的效果最佳。
- **請勿停用 [類別** ]-[所有] 索引標籤 (類別目錄) 應該隨時都可以看見和啟用。 如果指定的索引標籤沒有內容，請提供使用者原因的說明。 例如，如果使用者未進行任何購買，[購買] 索引標籤將會是空的。

<a name="Tab-Bar-Items"></a>

## <a name="tab-bar-items"></a>Tab 列專案

索引標籤列 (索引標籤) 的每個類別，都是以 () 的索引標籤列專案來表示 `UITabBarItem` 。 Apple 有下列建議可使用索引標籤列專案：

- **使用以文字為基礎** 的索引標籤-雖然可以將索引標籤列專案表示為圖示，但 Apple 建議只使用文字，因為精確的標題比圖示更容易解讀。
- **使用簡短、有意義的名詞或動詞** -索引標籤列專案應該會清楚地轉送它所包含的內容，而且當它是簡單的名詞 (例如相片、電影或音樂) 或動詞 (例如搜尋或播放) 時，效果最好。

<a name="Tab-Bars-and-Storyboards"></a>

## <a name="tab-bars-and-storyboards"></a>Tab 鍵和分鏡腳本

在 tvOS 應用程式中使用索引標籤列最簡單的方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動新的 tvOS 應用程式，然後選取**tvOS**  >  **應用程式**索引標籤  >  **式應用程式**： 

    [![選取索引標籤式應用程式](tab-bars-images/tab02.png)](tab-bars-images/tab02.png#lightbox)
1. 遵循所有提示來建立新的 tvOS 解決方案。
1. 在 [ **Solution Pad**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 若要變更指定類別的**圖示**或標題，請在 [**檔大綱**] 中選取**視圖控制器**的索引卷**標**列**專案**：

    [![[檔大綱] 中視圖控制器的索引標籤列專案](tab-bars-images/tab03a.png)](tab-bars-images/tab03a.png#lightbox)
1. 然後，在 [**屬性瀏覽器**] 的 [ **Widget]** 索引標籤中設定必要的屬性： 

    [![[Widget] 索引標籤](tab-bars-images/tab03.png)](tab-bars-images/tab03.png#lightbox)
1. 若要加入新類別 (索引標籤) ，請將 **視圖控制器** 放在設計介面上： 

    [![視圖控制器](tab-bars-images/tab04.png)](tab-bars-images/tab04.png#lightbox)
1. 從索引標籤 **視圖控制器** 按一下並拖曳至新的 **視圖控制器**。
1. 從快顯視窗中選取 [ **視圖控制器** ]，將新的視圖新增為索引標籤 (分類) ： 

    [![選取索引標籤](tab-bars-images/tab05.png)](tab-bars-images/tab05.png#lightbox)
1. 在 iOS 設計工具中加入 UI 專案，為每個 Caterogies 內容區域設計 UI 的版面配置。
1. 公開任何必要的事件，以使用 c # 程式碼中的 UI 控制項。
1. 為您想要在 c # 程式碼中公開的任何 UI 控制項命名。
1. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 啟動新的 tvOS 應用程式，然後選取**tvOS**  >  **應用程式**索引標籤  >  **式應用程式**： 

    [![選取索引標籤式應用程式](tab-bars-images/tab02vs.png)](tab-bars-images/tab02vs.png#lightbox)
1. 遵循所有提示來建立新的 tvOS 解決方案。
1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 若要變更指定類別的**圖示**或標題，請在 [**檔大綱**] 中選取**視圖控制器**的索引卷**標**列**專案**：

    [![檔大綱中的 View 控制器](tab-bars-images/tab03avs.png)](tab-bars-images/tab03avs.png#lightbox)
1. 然後，在 [**屬性瀏覽器**] 的 [ **Widget]** 索引標籤中設定必要的屬性： 

    [![[Widget] 索引標籤](tab-bars-images/tab03vs.png)](tab-bars-images/tab03vs.png#lightbox)
1. 若要加入新的分類 (索引標籤) ，請從 [**工具箱**] 將 [ **View] 控制器**拖曳至設計介面上： 

    [![視圖控制器](tab-bars-images/tab04vs.png)](tab-bars-images/tab04vs.png#lightbox)
1. 從索引標籤 **視圖控制器** 按一下並拖曳至新的 **視圖控制器**。
1. 從快顯視窗中選取 [ **視圖控制器** ]，將新的視圖新增為索引標籤 (分類) ： 

    [![選取索引標籤](tab-bars-images/tab05vs.png)](tab-bars-images/tab05vs.png#lightbox)
1. 在 iOS 設計工具中新增 UI 元素，為每個 Caterogies 內容區域設計 UI 的版面配置。
1. 公開任何必要的事件，以使用 c # 程式碼中的 UI 控制項。
1. 為您想要在 c # 程式碼中公開的任何 UI 控制項命名。
1. 儲存變更。

-----

> [!IMPORTANT]
> 雖然可以將事件（例如在 iOS 設計工具中的) ）指派給 `TouchUpInside` UI 元素 (`UIButton` ，但永遠不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 `Primary Action`建立 tvOS 使用者介面元素的事件處理常式時，您應該一律使用此事件。

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars"></a>

## <a name="working-with-tab-bars"></a>使用 Tab 鍵列

使用的 `Items` 屬性 `UITabBar` ，即可存取它的集合， `UITabBarItems` 其包含零 (0) 索引陣列。 `SelectedItem`屬性會傳回目前選取的索引標籤 (分類) 為 `UITabBarItem` 。

<a name="Working-with-Tab-Bar-Items"></a>

## <a name="working-with-tab-bar-items"></a>使用選項卡列專案

若要在指定的索引標籤上顯示徽章 (具有白色文字) 的紅色橢圓，請使用下列程式碼：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

這會在執行時產生下列結果：

[![具有徽章的索引標籤列專案](tab-bars-images/tab06.png)](tab-bars-images/tab06.png#lightbox)

使用的 `Title` 屬性， `UITabBarItem` 即可變更標題和 `Image` 屬性來變更圖示。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了如何在 tvOS 應用程式內設計及使用定位線控制器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)