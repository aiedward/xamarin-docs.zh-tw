---
title: 在 Xamarin 中使用 tvOS 文字和搜尋欄位
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用文字和搜尋欄位。 它提供文字和搜尋欄位的高階總覽，並討論鍵盤、分鏡腳本整合、搜尋資料模型等等。
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 83edaf46519790d9674b7b537fc424ddd7a09a1c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566145"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>在 Xamarin 中使用 tvOS 文字和搜尋欄位

必要時，您的 tvOS 應用程式可以使用文字欄位和螢幕小鍵盤，向使用者要求一小段文字（例如使用者識別碼和密碼）：

[![](text-fields-and-search-images/intro01.png "Sample Search Field")](text-fields-and-search-images/intro01.png#lightbox)

您可以使用搜尋欄位，選擇性地提供應用程式內容的關鍵字搜尋功能：

[![](text-fields-and-search-images/intro02.png "Sample Search Results")](text-fields-and-search-images/intro02.png#lightbox)

本檔將涵蓋在 tvOS 應用程式中使用文字和搜尋欄位的詳細資料。

<a name="About-Text-and-Search-Fields"></a>

## <a name="about-text-and-search-fields"></a>關於文字和搜尋欄位

如上所述，必要時，您的 tvOS 可以顯示一或多個文字欄位，以使用螢幕（或選用的藍牙鍵盤，視使用者已安裝的 tvOS 版本而定，從使用者收集少量文字。

此外，如果您的應用程式向使用者呈現大量的內容（例如音樂、電影或圖片集合），您可能會想要包含搜尋欄位，讓使用者輸入少量的文字來篩選可用專案的清單。

<a name="Text-Fields"></a>

## <a name="text-fields"></a>文字欄位

在 tvOS 中，文字欄位是以固定高度、圓角的輸入方塊來呈現，當使用者按一下時，會顯示幕幕小鍵盤：

[![](text-fields-and-search-images/text01.png "Text Fields In tvOS")](text-fields-and-search-images/text01.png#lightbox)

當使用者將[焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)移到指定的文字欄位時，它會放大，並顯示深度陰影。 在設計您的使用者介面時，您必須牢記這一點，因為文字欄位可以在焦點時與其他 UI 元素重迭。

Apple 具有下列使用文字欄位的建議：

- **謹慎使用文字輸入**-因為螢幕小鍵盤的本質，輸入長段文字或填滿多個文字欄位，對使用者而言很繁瑣。 較佳的解決方案是使用挑選清單或[按鈕](~/ios/tvos/user-interface/buttons.md)來限制文字輸入的數量。
- **使用提示來傳達目的**-文字欄位可以在空白時顯示預留位置「提示」。 適用時，請使用提示來描述文字欄位的用途，而不是個別的標籤。
- **選取適當的預設鍵盤類型**-tvOS 提供數個不同的目的建立鍵盤類型，可讓您為文字欄位指定。 例如，電子郵件地址鍵盤可以讓使用者從最近輸入的地址清單中進行選取，藉此簡化專案。
- **適當時使用安全文字欄位**-安全的文字欄位會以點（而不是實數）來呈現輸入的字元。 在收集機密資訊（例如密碼）時，一律使用安全的文字欄位。

<a name="Keyboards"></a>

## <a name="keyboards"></a>鍵盤

每當使用者按一下使用者介面中的文字欄位時，就會顯示線性的螢幕小鍵盤。 使用者使用[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)的觸控介面，從鍵盤選取個別的字母，然後輸入所要求的資訊：

[![](text-fields-and-search-images/keyboard01.png "The Siri Remote keyboard")](text-fields-and-search-images/keyboard01.png#lightbox)

如果目前的視圖上有一個以上的文字欄位，將會自動顯示 **[下一步]** 按鈕，讓使用者進入下一個文字欄位。 最後一個文字欄位將會顯示 [**完成**] 按鈕，它會結束文字輸入，並將使用者返回上一個畫面。

使用者也可以隨時按下 [Siri 遠端] 和 [結束文字] 專案上的**功能表**按鈕，再次返回上一個畫面。

Apple 對於使用螢幕鍵盤有下列建議：

- **選取適當的預設鍵盤類型**-tvOS 提供數個不同的目的建立鍵盤類型，可讓您為文字欄位指定。 例如，電子郵件地址鍵盤可以讓使用者從最近輸入的地址清單中進行選取，藉此簡化專案。
- **適當時，使用鍵盤附屬視圖**-除了一律顯示的標準資訊之外，您還可以將選擇性的附件視圖（例如影像或標籤）新增至螢幕小鍵盤，以澄清文字輸入的用途，或協助使用者輸入所需的資訊。

如需使用螢幕小鍵盤的詳細資訊，請參閱 Apple 的[UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType)、[管理鍵盤](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1)、[資料輸入的自訂視圖](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1)和 IOS 檔的[文字程式設計指南](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html)。

<a name="Search"></a>

## <a name="search"></a>搜尋

[搜尋] 欄位會顯示提供文字欄位和螢幕小鍵盤的特殊螢幕，可讓使用者篩選鍵盤下方所顯示專案的集合：

[![](text-fields-and-search-images/search01.png "Sample search results")](text-fields-and-search-images/search01.png#lightbox)

當使用者在搜尋欄位中輸入字母時，下方的結果會自動反映搜尋結果。 使用者隨時都可以將焦點移至結果，並選取其中一個呈現的專案。

Apple 針對使用搜尋欄位提供下列建議：

- **提供最新的搜尋**-因為在 Siri 遠端輸入文字可能十分繁瑣，而且使用者通常會重複搜尋要求，請考慮在鍵盤區域下的目前結果之前，新增最近搜尋結果的區段。
- **可能的話，請限制結果的數目**-因為較大的專案清單可能很容易讓使用者進行剖析和流覽，請考慮限制傳回的結果數目。
- **如果適用，請提供搜尋結果篩選器**-如果您的應用程式所提供的內容有其本身，請考慮新增範圍列，以允許使用者進一步篩選傳回的搜尋結果。

如需詳細資訊，請參閱 Apple 的[UISearchController 類別參考](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)。

<a name="Working-with-Text-Fields"></a>

## <a name="working-with-text-fields"></a>使用文字欄位

在 tvOS 應用程式中使用文字欄位最簡單的方式，就是使用 iOS 設計工具將它們新增至使用者介面設計。

執行下列動作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，按兩下檔案 `Main.storyboard` 以開啟它進行編輯。
1. 將一個或多個**文字欄位**拖曳至設計介面，並放在一個視圖上：

    [![](text-fields-and-search-images/text02.png "A Text Field")](text-fields-and-search-images/text02.png#lightbox)
1. 選取**文字欄位**，並在 [ **Properties Pad**的 [ **Widget** ] 索引標籤中指定每個唯一的**名稱**：

    [![](text-fields-and-search-images/text03.png "The Widget tab of the Properties Pad")](text-fields-and-search-images/text03.png#lightbox)
1. 在 [**文字欄位**] 區段中，您可以定義元素，例如**預留位置**提示和預設**值**：

    [![](text-fields-and-search-images/text04.png "The Text Field section")](text-fields-and-search-images/text04.png#lightbox)
1. 向下滾動以定義屬性，例如**拼寫檢查**、**大小寫**和預設**鍵盤類型**：

    [![](text-fields-and-search-images/text05.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05.png#lightbox)
1. 將變更儲存至您的分鏡腳本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在方案總管**** 中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
1. 將一個或多個**文字欄位**拖曳至設計介面，並放在一個視圖上：

    [![](text-fields-and-search-images/text02-vs.png "A Text Field")](text-fields-and-search-images/text02-vs.png#lightbox)
1. 選取**文字欄位**，並在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中提供每個唯一的**名稱**：

    [![](text-fields-and-search-images/text03-vs.png "The Widget tab")](text-fields-and-search-images/text03-vs.png#lightbox)
1. 在 [**文字欄位**] 區段中，您可以定義元素，例如**預留位置**提示和預設**值**：

    [![](text-fields-and-search-images/text04-vs.png "The Text Field section")](text-fields-and-search-images/text04-vs.png#lightbox)
1. 向下滾動以定義屬性，例如**拼寫檢查**、**大小寫**和預設**鍵盤類型**：

    [![](text-fields-and-search-images/text05-vs.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05-vs.png#lightbox)
1. 將變更儲存至您的分鏡腳本。

-----

在程式碼中，您可以使用其屬性來取得或設定文字欄位的值 `Text` ：

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

您可以選擇性地使用 `Started` 和 `Ended` 文字欄位事件來回應文字輸入的開始和結束。

<a name="Working-with-Search-Fields"></a>

## <a name="working-with-search-fields"></a>使用搜尋欄位

在 tvOS 應用程式中使用搜尋欄位最簡單的方式，就是使用介面設計工具將它們加入至使用者介面設計。

執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，按兩下檔案 `Main.storyboard` 以開啟它進行編輯。
1. 將新的 [集合視圖控制器] 拖曳至分鏡腳本，以呈現使用者搜尋的結果：

    [![](text-fields-and-search-images/search02.png "A Collection View Controller")](text-fields-and-search-images/search02.png#lightbox)
1. 在**Properties Pad**的 [ **Widget** ] 索引標籤中， `SearchResultsViewController` 針對**類別**和分鏡腳本 `SearchResults` **識別碼**使用：

    [![](text-fields-and-search-images/search03.png "The Widget tab")](text-fields-and-search-images/search03.png#lightbox)
1. 選取設計介面上的 [資料**格原型**]。
1. 在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中， `SearchResultCell` 針對**類別**和 `ImageCell` **識別碼**使用：

    [![](text-fields-and-search-images/search04.png "The Widget tab")](text-fields-and-search-images/search04.png#lightbox)
1. 配置資料**格原型**的設計，並在**屬性瀏覽器**的 [ **Widget** ] 索引標籤中使用唯一的**名稱**來公開每個元素：

    [![](text-fields-and-search-images/search05.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05.png#lightbox)
1. 將變更儲存至您的分鏡腳本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在方案總管**** 中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
1. 將新的 [集合視圖控制器] 拖曳至分鏡腳本，以呈現使用者搜尋的結果：

    [![](text-fields-and-search-images/seach02-vs.png "A Collection View Controller")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. 在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中， `SearchResultsViewController` 針對**類別**和分鏡腳本 `SearchResults` **識別碼**使用：

    [![](text-fields-and-search-images/search03-vs.png "The Widget tab")](text-fields-and-search-images/search03-vs.png#lightbox)
1. 選取設計介面上的 [資料**格原型**]。
1. 在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中， `SearchResultCell` 針對**類別**和 `ImageCell` **識別碼**使用：

    [![](text-fields-and-search-images/search04-vs.png "The Widget tab")](text-fields-and-search-images/search04-vs.png#lightbox)
1. 配置資料**格原型**的設計，並在**屬性瀏覽器**的 [ **Widget** ] 索引標籤中使用唯一的**名稱**來公開每個元素：

    [![](text-fields-and-search-images/search05-vs.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05-vs.png#lightbox)
1. 將變更儲存至您的分鏡腳本。

-----

<a name="Provide-a-Data-Model"></a>

### <a name="provide-a-data-model"></a>提供資料模型

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

接下來，您必須提供一個類別，做為使用者將搜尋之結果的資料模型。 在 [**方案總管**中，以滑鼠右鍵按一下專案名稱，**然後選取 [**  >  **新增檔案 ...**  >  ]。**一般**  > **空的類別**，並提供**名稱**：

[![](text-fields-and-search-images/search06.png "Select Empty Class and provide a Name")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

接下來，您必須提供一個類別，做為使用者將搜尋之結果的資料模型。 在 [**方案總管**中，以滑鼠右鍵按一下專案名稱，然後選取 [**加入**  >  **新專案**  >  ]。**Apple**  > **其他**  > **類別**並提供**名稱**：

[![](text-fields-and-search-images/search06-vs.png "Select Class and provide a Name")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

例如，允許使用者依標題和關鍵字搜尋圖片集合的應用程式，看起來可能如下所示：

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell"></a>

### <a name="the-collection-view-cell"></a>[集合視圖] 資料格

備妥資料模型之後，編輯**原型儲存格**（ `SearchResultViewCell.cs` ），讓它看起來如下所示：

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

`UpdateUI`每當屬性更新時，方法將用來顯示**PictureInformation** `PictureInfo` 已命名 UI 元素中 PictureInformation 專案（屬性）的個別欄位。 例如，與圖片相關聯的影像和標題。

<a name="The-Collection-View-Controller"></a>

### <a name="the-collection-view-controller"></a>集合視圖控制器

接下來，編輯 [搜尋結果] 集合視圖控制器（ `SearchResultsViewController.cs` ），使其看起來如下所示：

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

首先，將 `IUISearchResultsUpdating` 介面新增至類別，以處理使用者所更新的搜尋控制器篩選：

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

常數也會定義為指定**原型資料格**的識別碼（與上述介面設計工具中所定義的識別碼相符），稍後當集合控制器要求新的資料格時，將會用到：

```csharp
public const string CellID = "ImageCell";
```

系統會針對所搜尋的完整專案清單、搜尋篩選準則和符合該詞彙的專案清單建立儲存體：

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

當 `SearchFilter` 變更時，會更新相符專案的清單，並重載集合視圖的內容。 此 `FindPictures` 常式會負責尋找符合新搜尋詞彙的專案：

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

`SearchFilter`當使用者變更搜尋控制器中的篩選時，將會更新的值（這會更新結果集合視圖）：

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

`PopulatePictures`方法一開始會填入可用專案的集合：

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

基於此範例的目的，當收集視圖控制器載入時，會在記憶體中建立所有的範例資料。 在實際的應用程式中，這項資料可能會從資料庫或 web 服務讀取，而且只有在 overrunning Apple TV 的有限記憶體時才需要。

`NumberOfSections`和 `GetItemsCount` 方法提供相符專案的數目：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

`GetCell`方法會針對集合視圖中的每個專案，傳回新的**原型資料格**（根據在分鏡腳本 `CellID` 中定義的）：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

`WillDisplayCell`方法會在顯示的儲存格之前呼叫，因此可以進行設定：

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

`DidUpdateFocus`方法會在使用者反白顯示結果集合視圖中的專案時，提供視覺效果的意見反應：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

最後， `ItemSelected` 方法會處理使用者在結果集合視圖中選取專案（按一下具有 Siri 遠端的觸控介面）：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

如果搜尋欄位呈現為強制回應對話方塊視圖（呼叫它的視圖頂端），請使用 `DismissViewController` 方法，以在使用者選取專案時關閉搜尋視圖。 在此範例中，[搜尋] 欄位會顯示為 [索引標籤視圖] 索引標籤的內容，因此不會在此解除。

如需集合視圖的詳細資訊，請參閱我們[的使用集合視圖](~/ios/tvos/user-interface/collection-views.md)檔。

<a name="Presenting the Search Field"></a>

### <a name="presenting-the-search-field"></a>呈現搜尋欄位

有兩個主要的方式可以在 tvOS 中向使用者呈現搜尋欄位（以及其相關聯的螢幕小鍵盤和搜尋結果）：

- **強制回應對話方塊視圖**-[搜尋] 欄位可以透過 [全螢幕模式] 對話方塊視圖，在目前的 View 和 View 控制器上呈現。 這通常是為了回應使用者按一下按鈕或其他 UI 元素而完成。 當使用者從搜尋結果中選取專案時，就會關閉對話方塊。
- **視圖內容**-[搜尋] 欄位是指定之視圖的直接部分。 例如，做為索引標籤視圖控制器中 [搜尋] 索引標籤的內容。

如需上述指定的圖片清單範例，搜尋欄位在 [搜尋] 索引標籤中會顯示為 [View 內容]，而 [搜尋] 索引標籤視圖控制器看起來會像下面這樣：

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

首先，定義的常數會符合在介面設計工具中指派給搜尋結果集合視圖控制器的分鏡腳本**識別碼**：

```csharp
public const string SearchResultsID = "SearchResults";
```

接下來， `ShowSearchController` 方法會建立新的搜尋視圖集合控制器，並顯示它所需的內容：

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

在上述方法中，從分鏡腳本 `SearchResultsViewController` 具現化之後，會建立新的， `UISearchController` 以向使用者呈現搜尋欄位和螢幕小鍵盤。 搜尋結果集合（如所定義 `SearchResultsViewController` ）將會顯示在此鍵盤底下。

接下來， `SearchBar` 會使用**預留位置**提示之類的資訊來設定。 這會提供有關所要進行之搜尋類型的資訊給使用者。

接著，搜尋欄位會以下列兩種方式的其中一種呈現給使用者：

- **強制回應對話方塊視圖**- `PresentViewController` 呼叫方法，以在現有的視圖上（全螢幕）呈現搜尋。
- **View 內容**- `UISearchContainerViewController` 建立以包含搜尋控制器。 `UINavigationController`建立以包含搜尋容器，然後將導覽控制器新增至視圖控制器 `AddChildViewController (navController)` ，並顯示視圖 `View.Add (navController.View)` 。

最後，根據呈現類型， `ViewDidLoad` 或 `ViewDidAppear` 方法會呼叫 `ShowSearchController` 方法來向使用者呈現搜尋：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

當應用程式執行時，如果使用者選取了 [搜尋] 索引標籤，就會向使用者顯示完全未篩選的專案清單：

[![](text-fields-and-search-images/intro02.png "Default search results")](text-fields-and-search-images/intro02.png#lightbox)

當使用者開始輸入搜尋字詞時，結果清單會依該期限篩選並自動更新：

[![](text-fields-and-search-images/intro03.png "Filtered search results")](text-fields-and-search-images/intro03.png#lightbox)

使用者隨時都可以將焦點切換到搜尋結果中的專案，然後按一下 Siri 遠端的觸控介面來選取它。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式內設計和使用文字和搜尋欄位。 它示範如何在介面設計工具中建立文字和搜尋集合內容，並示範了兩種不同的方式，可讓您在 tvOS 中向使用者呈現搜尋欄位。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
