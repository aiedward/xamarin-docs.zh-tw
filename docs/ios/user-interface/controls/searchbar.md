---
title: "搜尋列"
ms.topic: article
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 03f90a506d22752c9158650de3f3a109d07b4396
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="search-bar"></a>搜尋列

UISearchBar 用來搜尋值的清單。 

它包含三個主要元件： 

- 用來輸入文字的欄位。 使用者可以利用此選項以輸入他們的搜尋字詞。
- 清除按鈕，即可移除搜尋欄位中的任何文字。
- [取消] 按鈕，以結束 [搜尋] 功能。

![搜尋列](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>實作搜尋列

若要實作搜尋列開始一個新具現化：

```csharp
searchBar = new UISearchBar();
```

並將其放置。 下列範例會示範如何將它放在導覽列或資料表的 HeaderView 中：

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

![搜尋列內容](searchbar-images/image6.png)

引發`SearchButtonClicked`事件時按下 [搜尋] 按鈕。 這會呼叫您的搜尋邏輯：

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

管理呈現的搜尋列中，搜尋結果的資訊，請參閱[搜尋控制器](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)配方。

## <a name="using-the-search-bar-in-the-designer"></a>在設計工具中使用 [搜尋] 列

在設計工具提供兩個選項可在設計工具中實作搜尋列

- 搜尋列
- 搜尋列中以搜尋顯示控制站 （已被取代）

![在設計工具中的搜尋列控制項](searchbar-images/image2.png)

若要設定屬性，在 [搜尋] 列上使用 [屬性] 面板

![搜尋列屬性設計工具](searchbar-images/image3.png)

以下將說明這些屬性：

- **文字，預留位置，提示**– 這些屬性用來提供建議，並指示使用者應該如何使用 [搜尋] 列。 例如，如果您的應用程式顯示一份存放區可以使用提示的屬性通知，使用者可以 「 輸入縣 （市）、 劇本名稱或郵遞區號 」
- **搜尋樣式**– 您可以設定是在搜尋列**Prominent**或**最少**。 使用顯著將濃淡其他在畫面上，除了搜尋 列，造成要繪製成 搜尋 列的焦點項目。 最小樣式搜尋列會混合在一起以隔離。
- **功能**– 啟用這些屬性只會顯示 UI 項目。 功能必須實作這些藉由引發正確的事件中所述[搜尋列 API 文件](https://developer.xamarin.com/api/type/UIKit.UISearchBar/)
    - 顯示搜尋結果 / 書籤按鈕 – [搜尋] 列上顯示搜尋結果或書籤圖示
    - 顯示取消按鈕-允許使用者移到搜尋函式結束。 建議，選取此選項。
    - 這可讓使用者限制搜尋範圍會顯示領域列 –。 比方說，搜尋音樂應用程式中時的使用者可以選取是否要搜尋 Apple 音樂或特定歌曲或演出者其程式庫。 若要顯示不同的選項，加入陣列的標題**ScopeBarTitles**屬性。
    ![搜尋列範圍標題](searchbar-images/image4.png)

- **文字行為**– 這些選項用來定址這些輸入時，如何格式化使用者輸入。 大小寫會設定每個字或句子開頭或每個字元為大寫。 更正和拼字檢查，以提示使用者字的拼字建議，依照它們的輸入。
- **鍵盤**– 控制項輸入顯示鍵盤樣式，因此哪些金鑰鍵盤上可用。 這包括數字鍵台、 電話填補、 電子郵件、 URL 以及其他選項。
- **外觀**– 控制鍵盤的外觀樣式和的任一暗色調或淺佈景主題。
- **傳回索引鍵**– 變更索引標籤上傳回的索引鍵，以更精確反映所要採取的動作。 支援的值包括 Go、 聯結、 下一步，路由，請完成和搜尋。
- **安全**– 識別是否為已遮罩輸入 （例如密碼輸入）。

## <a name="related-links"></a>相關連結

- [搜尋控制站](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
