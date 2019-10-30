---
title: Xamarin 中的搜尋列
description: 本檔說明如何在 Xamarin 中使用搜尋列。 它討論如何以程式設計方式和分鏡腳本建立搜尋列。
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 8b129e0e70bf3ded787094d1b1f740e73a8cbca1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021973"
---
# <a name="search-bars-in-xamarinios"></a>Xamarin 中的搜尋列

UISearchBar 是用來搜尋值清單。

其中包含三個主要元件：

- 用來輸入文字的欄位。 使用者可以利用此來輸入其搜尋字詞。
- [清除] 按鈕，可移除搜尋欄位中的任何文字。
- [取消] 按鈕，用來結束搜尋功能。

![搜尋列](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>執行搜尋列

若要開始執行搜尋列，請先具現化新的：

```csharp
searchBar = new UISearchBar();
```

然後將它放入。 下列範例顯示如何將它放在流覽列或資料表的 HeaderView 中：

```csharp
NavigationItem.TitleView = searchBar;

// or

TableView.TableHeaderView = searchBar;
```

在搜尋列上設定屬性：

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![搜尋列屬性](searchbar-images/image6.png)

當按下 [搜尋] 按鈕時，引發 `SearchButtonClicked` 事件。 這會呼叫您的搜尋邏輯：

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

如需管理搜尋列和搜尋結果之呈現的相關資訊，請參閱[搜尋控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)配方。

## <a name="using-the-search-bar-in-the-designer"></a>使用設計工具中的搜尋列

設計工具提供兩個選項，可在設計工具中執行搜尋列

- 搜尋列
- 具有搜尋顯示控制器的搜尋列（已淘汰）

![設計師中的搜尋列控制項](searchbar-images/image2.png)

使用 [屬性] 面板來設定搜尋列上的屬性

![搜尋列屬性設計工具](searchbar-images/image3.png)

以下說明這些屬性：

- **文字、預留位置、提示**–這些屬性是用來建議並指示使用者應該如何使用搜尋列。 例如，如果您的應用程式顯示商店清單，您可以使用 prompt 屬性來通知使用者可以「輸入城市、故事名稱或郵遞區號」
- **搜尋樣式**–您可以將搜尋列設定為 [**明顯**] 或 [**最小**]。 使用 [明顯] 會在畫面上顯示其他所有專案，但不包括搜尋列，因而導致焦點繪製到搜尋列。 最小樣式搜尋列會與其周圍的混合使用。
- **功能**–啟用這些屬性只會顯示 UI 元素。 您必須藉由引發正確的事件（如[搜尋列 API](xref:UIKit.UISearchBar)檔中所述），來實作為這些功能
  - 顯示搜尋結果/書簽按鈕–在搜尋列上顯示搜尋結果或書簽圖示
  - 顯示 [取消] 按鈕–允許使用者離開搜尋功能。 建議選取此選項。
  - 顯示範圍列–這可讓使用者限制其搜尋範圍。 例如，在 [音樂] 應用程式中搜尋時，使用者可以選取要搜尋特定歌曲或演出者 Apple Music 或其媒體櫃。 若要顯示各種選項，請將標題陣列加入至**ScopeBarTitles**屬性。
  ![搜尋列範圍標題](searchbar-images/image4.png)

- **文字行為**–這些選項可用來解決使用者輸入在輸入時的格式。 大寫會設定每個單字或句子的開頭，或每個字元的大小寫。 更正和拼寫檢查，會提示使用者輸入文字時的建議拼寫。
- **鍵盤**–控制針對輸入顯示的鍵盤樣式，因此鍵盤上可用的按鍵。 這包括數位板、手機 Pad、電子郵件、URL 和其他選項。
- **外觀**–控制鍵盤的外觀樣式，而且會有深色或淺色的主題。
- 傳回**金鑰**–變更傳回金鑰上的標籤，以更清楚地反映將採取的動作。 支援的值包括 Go、Join、Next、Route、Done 和 Search。
- **Secure** –識別輸入是否已遮罩（例如輸入密碼）。

## <a name="related-links"></a>相關連結

- [搜尋控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
