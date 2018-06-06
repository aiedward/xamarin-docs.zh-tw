---
title: 使用 tvOS 文字和 Xamarin 中搜尋欄位
description: 本文件說明如何使用 Xamarin 建置 tvOS 應用程式中的文字，並搜尋欄位使用。 它提供文字 和 搜尋 欄位的高階概觀，並討論鍵盤、 分鏡腳本整合、 搜尋資料模型，等等。
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 4bf71df62bd6ffd88f42d31809444cd7b3350b66
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789718"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>使用 tvOS 文字和 Xamarin 中搜尋欄位

在必要時 Xamarin.tvOS 應用程式可以小型的文字，向使用者要求 （例如使用者識別碼和密碼） 使用的文字欄位和螢幕小鍵盤：

[![](text-fields-and-search-images/intro01.png "範例搜尋欄位")](text-fields-and-search-images/intro01.png#lightbox)

您可以選擇提供應用程式的內容使用 [搜尋] 欄位中的關鍵字搜尋功能：

[![](text-fields-and-search-images/intro02.png "範例搜尋結果")](text-fields-and-search-images/intro02.png#lightbox)

本文件將涵蓋在 Xamarin.tvOS 應用程式中使用的文字和搜尋欄位的詳細資料。

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>關於文字和搜尋欄位

如上所述，必要時，您 Xamarin.tvOS 可以呈現一或多個文字欄位，來收集使用者使用少量文字螢幕 （或選擇性的藍芽鍵盤根據 tvOS 使用者安裝的版本）。 

此外，如果您的應用程式 （例如音樂、 電影或圖片集合） 對使用者顯示大量的內容，您可以在包含搜尋欄位中，可讓使用者輸入少量文字以篩選的項目清單。

<a name="Text-Fields" />

## <a name="text-fields"></a>文字欄位

TvOS，在文字欄位會以固定高度、 圓角的項目 方塊中，畫面上隨即顯示螢幕小鍵盤，當使用者按一下它：

[![](text-fields-and-search-images/text01.png "文字欄位中 tvOS")](text-fields-and-search-images/text01.png#lightbox)

當使用者移[焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)到指定的文字欄位，則會大幅成長，並顯示深的陰影。 您必須謹記這在設計您的使用者介面文字欄位可以重疊其他 UI 元素為焦點時。

Apple 已使用的文字欄位的下列建議：

- **盡量不要文字項目使用**-由於性質而螢幕小鍵盤，輸入很長的文字區段或填寫多個文字欄位過於冗長的使用者。 更好的解決方案是使用選擇清單來限制文字項目數量或[按鈕](~/ios/tvos/user-interface/buttons.md)。
- **使用提示來通訊的用途**-文字欄位空白時，可以顯示預留位置"提示 >。 情況適用時，使用提示來描述您的文字欄位，而不是個別的標籤的用途。
- **選取適當的預設鍵盤類型**-tvOS 提供數種不同的建置的目的鍵盤類型，您可以指定文字欄位。 比方說，電子郵件地址鍵盤可以簡化允許使用者從最近的輸入的地址清單中選取的項目。
- **在適當的情況下，使用安全的文字欄位**-A 保護的文字欄位顯示為小圓點 （而不是真正的字母） 輸入的字元。 收集密碼等機密資訊時，一律使用安全的文字欄位。

<a name="Keyboards" />

## <a name="keyboards"></a>鍵盤

每當使用者在文字欄位中的使用者介面，線性螢幕上會顯示鍵盤。 使用者使用觸控介面[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)從鍵盤選取個別的字母，然後輸入要求的資訊：

[![](text-fields-and-search-images/keyboard01.png "Siri 遠端鍵盤")](text-fields-and-search-images/keyboard01.png#lightbox)

如果目前的檢視上有多個文字欄位**下一步**来採取的下一個文字欄位的使用者，會自動顯示按鈕。 A**完成**按鈕會顯示最後一個文字欄位，將會結束文字項目，並回到上一個畫面中的使用者。 

在任何時間，使用者也可以按**功能表**Siri 遙控器結束文字項目，並再次傳回上一個螢幕上的按鈕。

Apple 有螢幕小鍵盤來處理下列建議：

- **選取適當的預設鍵盤類型**-tvOS 提供數種不同的建置的目的鍵盤類型，您可以指定文字欄位。 比方說，電子郵件地址鍵盤可以簡化允許使用者從最近的輸入的地址清單中選取的項目。
- **在適當的情況下，使用鍵盤附屬應用程式檢視**-除了標準一律為選擇性，附屬應用程式檢視 （例如影像或標籤） 的資訊可以加入至螢幕小鍵盤來釐清用途的文字項目上，或協助使用者輸入所需的資訊。

如需有關使用螢幕小鍵盤，請參閱 Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType)，[管理鍵盤](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1)，[自訂檢視 中的資料輸入](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1)和[文字程式設計指南 iOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html)文件。

<a name="Search" />

## <a name="search"></a>搜尋

搜尋欄位呈現提供文字欄位的特定的畫面和螢幕小鍵盤，可讓使用者篩選集合中的下面鍵盤所顯示的項目：

[![](text-fields-and-search-images/search01.png "範例搜尋結果")](text-fields-and-search-images/search01.png#lightbox)

當使用者在搜尋欄位中輸入字母，以下的結果將會自動反映搜尋的結果。 在任何時間，使用者可以焦點轉移到結果，並選取其中一個呈現的項目。

Apple 已使用搜尋欄位的下列建議：

- **提供最近搜尋**-因為輸入文字，使用 Siri 遠端就必須等待冗長，使用者傾向於重複搜尋要求，請考慮加入之前鍵盤區域下目前的結果區段的最新的搜尋結果。
- **可能的話，請將數字的結果限制**-因為大型清單的項目，可能難以讓使用者可用來剖析並瀏覽，請考慮限制傳回的結果數目。
- **如果可行，請提供搜尋結果篩選**-如果您的應用程式所提供的內容本身，請考慮加入範圍橫條圖，讓使用者以進一步篩選傳回的搜尋結果。

如需詳細資訊，請參閱 Apple [UISearchController 類別參考](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)。

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>使用文字欄位

Xamarin.tvOS 應用程式中使用的文字欄位的最簡單方式是將它們新增到使用 iOS 設計工具使用者介面設計。

請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，連按兩下`Main.storyboard`檔案，以開啟它進行編輯。
1. 拖放到一個或多個**文字欄位**int 設計介面拖曳至檢視： 

    [![](text-fields-and-search-images/text02.png "文字欄位")](text-fields-and-search-images/text02.png#lightbox)
1. 選取**文字欄位**並提供每個唯一**名稱**中**Widget**  索引標籤**屬性板**: 

    [![](text-fields-and-search-images/text03.png "屬性輸入板的小工具 索引標籤")](text-fields-and-search-images/text03.png#lightbox)
1. 在**文字欄位** 區段中，您可以定義元素，例如**預留位置**提示和預設**值**: 

    [![](text-fields-and-search-images/text04.png "文字欄位區段")](text-fields-and-search-images/text04.png#lightbox)
1. 向下捲動來定義屬性，例如**拼字檢查**，**大寫**和預設**鍵盤類型**: 

    [![](text-fields-and-search-images/text05.png "檢查拼字、 大小寫和預設鍵盤類型")](text-fields-and-search-images/text05.png#lightbox) 
1. 將變更儲存至分鏡腳本。
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在方案總管中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
1. 拖放到一個或多個**文字欄位**int 設計介面拖曳至檢視： 

    [![](text-fields-and-search-images/text02-vs.png "文字欄位")](text-fields-and-search-images/text02-vs.png#lightbox)
1. 選取**文字欄位**並提供每個唯一**名稱**中**Widget**  索引標籤**屬性總管**: 

    [![](text-fields-and-search-images/text03-vs.png "[小工具] 索引標籤")](text-fields-and-search-images/text03-vs.png#lightbox)
1. 在**文字欄位** 區段中，您可以定義元素，例如**預留位置**提示和預設**值**: 

    [![](text-fields-and-search-images/text04-vs.png "文字欄位區段")](text-fields-and-search-images/text04-vs.png#lightbox)
1. 向下捲動來定義屬性，例如**拼字檢查**，**大寫**和預設**鍵盤類型**: 

    [![](text-fields-and-search-images/text05-vs.png "檢查拼字、 大小寫和預設鍵盤類型")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. 將變更儲存至分鏡腳本。
    
-----

在程式碼中，您可以取得或設定值的文字欄位，使用其`Text`屬性：

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

您可以選擇性地使用`Started`和`Ended`文字輸入開始和結束回應的文字欄位事件。

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>使用搜尋欄位

使用搜尋欄位 Xamarin.tvOS 應用程式中最簡單方式是將它們新增到使用介面的設計工具使用者介面設計。

請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在**方案板**，連按兩下`Main.storyboard`檔案，以開啟它進行編輯。
1. 將新的集合檢視控制器拖曳到分鏡腳本來呈現使用者搜尋的結果： 

    [![](text-fields-and-search-images/search02.png "集合檢視控制器")](text-fields-and-search-images/search02.png#lightbox)
1. 在**Widget**  索引標籤**屬性板**，使用`SearchResultsViewController`如**類別**和`SearchResults`的**分鏡腳本識別碼**: 

    [![](text-fields-and-search-images/search03.png "[小工具] 索引標籤")](text-fields-and-search-images/search03.png#lightbox)
1. 選取**儲存格原型**設計介面上。
1. 在**Widget**  索引標籤**屬性總管**，使用`SearchResultCell`的**類別**和`ImageCell`如**識別碼**: 

    [![](text-fields-and-search-images/search04.png "[小工具] 索引標籤")](text-fields-and-search-images/search04.png#lightbox)
1. 設計版面配置的**儲存格原型**和公開每個項目具有唯一**名稱**中**Widget**  索引標籤**屬性總管**: 

    [![](text-fields-and-search-images/search05.png "版面配置的儲存格原型設計")](text-fields-and-search-images/search05.png#lightbox)
1. 將變更儲存至分鏡腳本。
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在方案總管中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
1. 將新的集合檢視控制器拖曳到分鏡腳本來呈現使用者搜尋的結果： 

    [![](text-fields-and-search-images/seach02-vs.png "集合檢視控制器")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. 在**Widget**  索引標籤**屬性總管**，使用`SearchResultsViewController`如**類別**和`SearchResults`如**分鏡腳本識別碼**: 

    [![](text-fields-and-search-images/search03-vs.png "[小工具] 索引標籤")](text-fields-and-search-images/search03-vs.png#lightbox)
1. 選取**儲存格原型**設計介面上。
1. 在**Widget**  索引標籤**屬性總管**，使用`SearchResultCell`的**類別**和`ImageCell`如**識別碼**: 

    [![](text-fields-and-search-images/search04-vs.png "[小工具] 索引標籤")](text-fields-and-search-images/search04-vs.png#lightbox)
1. 設計版面配置的**儲存格原型**和公開每個項目具有唯一**名稱**中**Widget**  索引標籤**屬性總管**: 

    [![](text-fields-and-search-images/search05-vs.png "版面配置的儲存格原型設計")](text-fields-and-search-images/search05-vs.png#lightbox)
1. 將變更儲存至分鏡腳本。
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>提供資料模型

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下來，您必須提供類別以做為使用者將會搜尋的資料模型的結果。 在**方案總管 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新的檔案...**  > **一般** > **空類別**並提供**名稱**: 

[![](text-fields-and-search-images/search06.png "選取空類別，並提供名稱")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下來，您必須提供類別以做為使用者將會搜尋的資料模型的結果。 在**方案總管 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新項目...**  >  **Apple** > **其他** > **類別**並提供**名稱**: 

[![](text-fields-and-search-images/search06-vs.png "選取類別並提供名稱")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

例如，應用程式，可讓使用者搜尋集合的標題和關鍵字的圖片看起來可能如下所示：

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

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>集合檢視儲存格

與就地資料模型，編輯**原型儲存格**(`SearchResultViewCell.cs`)，並將其外觀介於下列：

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

`UpdateUI`方法將會用來顯示個別的欄位**PictureInformation**項目 (`PictureInfo`屬性) 的具名的 UI 項目在每次更新的屬性。 例如，影像和圖片與相關聯的標題。

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>集合檢視控制器

接著，編輯 搜尋結果的集合檢視控制器 (`SearchResultsViewController.cs`)，並使其看起來如下：

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

首先，`IUISearchResultsUpdating`介面加入至類別以處理使用者正在更新的搜尋控制器篩選條件：

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

指定的識別碼也會定義常數**原型儲存格**（符合上述介面設計工具中定義的識別碼），稍後將會用集合控制器要求新的儲存格時：

```csharp
public const string CellID = "ImageCell";
```

會建立儲存體的完整清單的項目所搜尋的搜尋篩選條件文字，符合該詞彙的項目清單：

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

當`SearchFilter`已變更，相符的項目清單會更新，而且會重新載入集合檢視的內容。 `FindPictures`常式負責尋找符合新的搜尋詞彙的項目：

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

值`SearchFilter`（這會更新結果集合檢視） 會更新使用者何時變更搜尋控制器中的篩選條件：

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

`PopulatePictures`方法一開始會填入集合的項目：

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

為了此範例中，所有範例資料在記憶體中時建立載入集合檢視控制器。 在實際的應用程式，這項資料可能會讀取從資料庫或 web 服務，並只有在必要時，若要防止造成滿溢 Apple 電視遙控器有限的記憶體。

`NumberOfSections`和`GetItemsCount`方法提供的相符項目數：

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

`GetCell`方法會傳回新**原型儲存格**(根據`CellID`上述腳本中定義) 的集合檢視中的每個項目：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

`WillDisplayCell`方法呼叫之前讓它可以設定顯示的資料格：

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

`DidUpdateFocus`方法向使用者提供的視覺化回饋它們反白顯示的結果集合檢視中的項目：

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

最後，`ItemSelected`方法會處理使用者結果的集合檢視中選取項目 （按一下觸控介面上使用 Siri 遠端）：

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

如果 [搜尋] 欄位已呈現為強制回應對話方塊檢視 （透過呼叫該檢視的頂端），使用`DismissViewController`方法，以關閉 [搜尋] 檢視，當使用者選取項目。 本例而言，[搜尋] 欄位會顯示成索引標籤檢視索引標籤的內容，讓它不正在解除這裡。

如需有關集合檢視的詳細資訊，請參閱我們[集合檢視使用](~/ios/tvos/user-interface/collection-views.md)文件。

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>呈現搜尋欄位

有兩種主要方式可搜尋欄位 （和與其相關聯螢幕小鍵盤和搜尋結果） 可以呈現給使用者的 tvOS: 

- **強制回應對話方塊檢視**-檢視和檢視控制站指定為全螢幕強制回應對話方塊檢視，可以透過目前顯示搜尋欄位中。 這通常是回應使用者按一下按鈕或其他 UI 項目中完成。 當使用者從搜尋結果中選取項目時，就會關閉對話方塊。
- **檢視內容**-搜尋欄位是直接指定檢視的一部分。 例如，做為索引標籤檢視控制器 [搜尋] 索引標籤的內容。

針對可搜尋的圖片上述清單的範例中，[搜尋] 欄位會以在 [搜尋] 索引標籤中的檢視內容，並搜尋索引標籤檢視控制器看起來如下：

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

首先，常數定義，其符合**分鏡腳本識別碼**，已指派給搜尋結果的集合檢視中的控制器介面設計工具：

```csharp
public const string SearchResultsID = "SearchResults";
```

下一步`ShowSearchController`方法會建立新的搜尋檢視集合控制器，並顯示需要它：

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

在上述方法中，一次`SearchResultsViewController`有分鏡腳本，從已具現化新`UISearchController`建立呈現搜尋欄位中，並向使用者螢幕小鍵盤。 搜尋結果集合 (如同`SearchResultsViewController`) 這個鍵盤底下會顯示。

下一步`SearchBar`使用資訊來設定這類**預留位置**提示。 這會提供資訊給使用者有關所執行的搜尋類型。

接著 [搜尋] 欄位被呈現給使用者，以兩種方式之一：

- **強制回應對話方塊檢視**-`PresentViewController`呼叫方法，透過現有的檢視，顯示搜尋全螢幕。
- **檢視內容**-`UISearchContainerViewController`建立要包含在搜尋控制站。 A`UINavigationController`建立包含 [搜尋] 容器中，則瀏覽控制器加入至檢視控制器`AddChildViewController (navController)`，並呈現檢視`View.Add (navController.View)`。

最後，再根據簡報類型，可能是`ViewDidLoad`或`ViewDidAppear`方法會呼叫`ShowSearchController`方法，以向使用者顯示搜尋：

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

當應用程式執行時，使用者選取的搜尋 索引標籤，將會向使用者顯示的項目未篩選的完整清單：

[![](text-fields-and-search-images/intro02.png "預設搜尋結果")](text-fields-and-search-images/intro02.png#lightbox)

當使用者開始輸入搜尋字詞，結果清單會篩選由該詞彙並自動更新：

[![](text-fields-and-search-images/intro03.png "篩選的搜尋結果")](text-fields-and-search-images/intro03.png#lightbox)

在任何時間，使用者可以將焦點切換至搜尋結果中的項目，並按一下觸控 Siri 遙控器來選取它的介面。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和處理文字與搜尋欄位內 Xamarin.tvOS 應用程式。 說明如何建立介面設計工具中的文字和搜尋集合的內容，並說明兩個不同的方式搜尋欄位無法向 tvOS 中的使用者。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
