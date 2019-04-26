---
title: 在 Xamarin.iOS 中的搜尋列
description: 本文件說明如何在 Xamarin.iOS 中使用搜尋列。 它討論如何以程式設計方式和分鏡腳本建立搜尋列。
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 3c46aa1244699671d46560b0029197981a86d005
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341521"
---
# <a name="search-bars-in-xamarinios"></a>在 Xamarin.iOS 中的搜尋列

UISearchBar 用來搜尋值的清單。 

它包含三個主要元件： 

- 用來輸入文字的欄位。 使用者可以利用此方式來輸入他們的搜尋字詞。
- 清除按鈕，以移除 [搜尋] 欄位中的任何文字。
- [取消] 按鈕，以結束 [搜尋] 功能。

![搜尋列](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>實作搜尋列

若要實作藉由執行個體化一個新的搜尋列開始：

```csharp
searchBar = new UISearchBar();
```

並將其放置。 下列範例示範如何將它放在導覽列或資料表的 HeaderView:

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

在 [搜尋] 列上設定屬性：

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![搜尋列屬性](searchbar-images/image6.png)

引發`SearchButtonClicked`事件時按下搜尋按鈕。 這會呼叫您的搜尋邏輯：

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

如需管理的搜尋列和搜尋結果的呈現方式的資訊，請參閱[搜尋控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)配方。

## <a name="using-the-search-bar-in-the-designer"></a>在設計工具中使用搜尋列

設計工具提供兩個選項可在設計工具中實作搜尋列

- 搜尋列
- 搜尋列搜尋顯示控制站 （已過時）

![在設計工具中的搜尋列控制項](searchbar-images/image2.png)

在 [搜尋] 列上設定屬性使用 [屬性] 面板

![搜尋列屬性設計工具](searchbar-images/image3.png)

這些屬性的說明如下：

- **文字，預留位置，提示**– 這些屬性用來提供建議，並指示使用者如何使用 [搜尋] 列。 比方說，如果您的應用程式會顯示一份存放區可以使用 prompt 屬性，使用者可以 「 輸入縣 （市）、 故事名稱或郵遞區號 」 的建議
- **搜尋樣式**– 您可以設定讓 [搜尋] 列**Prominent**或是**最低**。 使用顯著，會設定一切在畫面上，除了列，導致將焦點轉給繪製到搜尋列搜尋的濃淡。 最小的樣式的 [搜尋] 列會融合其周圍環境而定。
- **功能**– 讓這些屬性只會顯示 UI 項目。 必須實作的功能，藉由引發正確的事件中所述的這些[搜尋列 API 文件](xref:UIKit.UISearchBar)
    - 顯示搜尋結果 / 書籤按鈕-在 [搜尋] 列上顯示搜尋結果] 或 [書籤圖示
    - 顯示 [取消] 按鈕-允許使用者結束搜尋函式。 建議，選取此項目。
    - 顯示範圍列 – 這可讓使用者以限制其搜尋範圍。 比方說，在音樂應用程式中搜尋時的使用者可以選取是否想要搜尋 Apple Music 或其程式庫的特定歌曲或演出者。 若要顯示的各種選項，新增的項目陣列**ScopeBarTitles**屬性。
    ![搜尋列範圍標題](searchbar-images/image4.png)

- **文字行為**– 這些選項用來解決這些輸入時，如何格式化使用者輸入。 大小寫會設定每個字或句子的開頭或為大寫的每個字元。 更正和拼字檢查對使用者提示的文字的拼字建議依照其輸入。
- **鍵盤**– 控制項的鍵盤樣式顯示，針對輸入，並因此何種都是可用鍵盤上。 這包括數字鍵台、 Phone 填補、 電子郵件、 URL 及其他選項。
- **外觀**– 控制項的鍵盤外觀樣式和的任一深或亮色調佈景主題。
- **傳回索引鍵**– 變更傳回的索引鍵，以更精確反映所要採取的動作標籤。 支援的值包括 Go、 聯結、 下一步，路由，完成之後和搜尋。
- **安全**– 識別是否已遮罩輸入 （例如密碼輸入）。

## <a name="related-links"></a>相關連結

- [搜尋控制站](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
