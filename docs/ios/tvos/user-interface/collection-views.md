---
title: 在 Xamarin 中使用 tvOS 收集視圖
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用集合視圖。 其中涵蓋了集合視圖配置、建立資料格和輔助視圖、回應使用者事件等等。
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 2adc161742e7891fcce65cf2025a1b7c531c7a39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435245"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 收集視圖

集合視圖可讓您使用任意版面配置來顯示一組內容。 使用內建的支援，可讓您輕鬆建立類似格線或線性的版面配置，同時也支援自訂的版面配置。

[![範例集合視圖](collection-views-images/collection01.png)](collection-views-images/collection01.png#lightbox)

集合視圖會使用委派和資料來源來維護專案的集合，以提供使用者互動和集合的內容。 由於收集視圖是以獨立于 View 本身的版面配置子系統為基礎，因此提供不同的版面配置可以輕鬆地即時變更收集視圖資料的呈現方式。

<a name="About-Collection-Views"></a>

## <a name="about-collection-views"></a>關於集合視圖

如上所述，收集視圖 (`UICollectionView`) 管理專案的已排序集合，並以可自訂的版面配置呈現這些專案。 集合視圖的運作方式類似于資料表視圖 (`UITableView`) ，不過它們可以使用版面配置來呈現多個專案，而不只是單一資料行。

當您在 tvOS 中使用收集視圖時，您的應用程式會負責使用 () 的資料來源，來提供與集合相關聯的資料 `UICollectionViewDataSource` 。 您可以選擇組織收集視圖資料，並將其呈現至不同的群組 (區段) 。

[收集視圖] 會使用儲存格來呈現螢幕上的個別專案 `UICollectionViewCell` ， (可提供集合中特定資訊片段的)  (例如影像和其標題) 。

您可以選擇性地將增補視圖加入至集合視圖的簡報，以作為區段和資料格的頁首和頁尾。 集合視圖的配置負責定義這些視圖的放置以及個別儲存格的位置。

收集視圖可以使用委派 () 來回應使用者互動 `UICollectionViewDelegate` 。 如果資料格已反白顯示或已選取某個資料格，則這個委派也會負責判斷指定的資料格是否可以取得焦點。 在某些情況下，委派會決定個別資料格的大小。

<a name="Collection-View-Layouts"></a>

## <a name="collection-view-layouts"></a>集合視圖版面配置

集合視圖的主要功能是其所呈現的資料與其配置之間的分隔。 集合視圖 `UICollectionViewLayout` 配置 () 負責提供組織和資料格位置 (，以及在集合視圖的螢幕簡報中) 的任何補充視圖。

個別資料格是由集合視圖從其附加的資料來源建立，然後由指定的集合視圖配置排列並顯示。

建立收集視圖時，通常會提供收集視圖版面配置。 不過，您可以隨時變更集合視圖配置，並且會使用提供的新配置自動更新收集視圖資料的螢幕呈現。

集合視圖配置提供數種方法，可用來在兩個不同的版面配置之間建立動畫的動畫 (預設不會) 執行任何動畫。 此外，收集視圖配置可以與手勢辨識器搭配使用，以進一步以動畫顯示變更配置的使用者互動。

<a name="Creating-Cells-and-Supplementary-Views"></a>

## <a name="creating-cells-and-supplementary-views"></a>建立資料格和輔助視圖

集合視圖的資料來源不僅負責提供支援集合專案的資料，也是用來顯示內容的資料格。

由於收集視圖是設計用來處理大型的專案集合，因此可以將個別資料格清除佇列並重複使用，以防止 overrunning 的記憶體限制。 清除佇列 views 有兩種不同的方法：

- `DequeueReusableCell` -建立或傳回指定類型的儲存格 (如應用程式的腳本) 中所指定。
- `DequeueReusableSupplementaryView` -建立或傳回指定類型 (的補充視圖，如應用程式的腳本) 中所指定。

在呼叫這些方法的其中一個之前，您必須先註冊用來建立資料格的類別、分鏡腳本或檔案， `.xib` 以收集視圖。 例如：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

其中 `typeof(CityCollectionViewCell)` 提供支援視圖的類別，並提供在資料 `CityViewDatasource.CardCellId` 格 (或 view) 清除佇列時所使用的識別碼。

清除資料格之後，您可以使用它所代表之專案的資料進行設定，並返回 [收集視圖] 以供顯示。

<a name="About-Collection-View-Controllers"></a>

## <a name="about-collection-view-controllers"></a>關於集合視圖控制器

集合視圖控制器 (`UICollectionViewController`) 是提供下列行為的特殊視圖控制器 (`UIViewController`) ：

- 它會負責從其分鏡腳本或檔案載入收集視圖 `.xib` ，並將視圖具現化。 如果是在程式碼中建立的，則會自動建立新的、未設定的集合視圖。
- 一旦載入收集視圖，控制器會嘗試從分鏡腳本或檔案載入其資料來源及委派 `.xib` 。 如果沒有可用的，它會將自己設定為兩者的來源。
- 確定在第一次顯示的情況下，載入收集視圖之前先載入資料，然後重載並清除每個後續顯示的 [選取]。

此外，收集視圖控制器提供可覆寫的方法，可用來管理集合視圖的生命週期，例如 `AwakeFromNib` 和 `ViewWillDisplay` 。

<a name="Collection-Views-and-Storyboards"></a>

## <a name="collection-views-and-storyboards"></a>集合視圖和分鏡腳本

在 tvOS 應用程式中使用收集視圖的最簡單方式，就是在其分鏡腳本中新增一個。 作為快速範例，我們將建立範例應用程式，以顯示影像、標題和選取按鈕。 如果使用者按一下 [選取] 按鈕，將會顯示集合視圖，讓使用者選擇新的影像。 選擇影像時，會關閉收集視圖，並顯示新的影像和標題。

讓我們執行下列作業：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中啟動新的 **單一 View TvOS 應用程式** 。
1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後在 iOS 設計工具中開啟該檔案。
1. 將影像視圖、標籤和按鈕新增至現有的視圖，並將其設定為如下所示： 

    [![範例版面配置](collection-views-images/collection02.png)](collection-views-images/collection02.png#lightbox)
1. 將**名稱**指派給影像視圖，並在**屬性瀏覽器**的 [ **Widget]** 索引標籤中指定標籤。 例如： 

    [![設定名稱](collection-views-images/collection03.png)](collection-views-images/collection03.png#lightbox)
1. 接下來，將收集視圖控制器拖曳至腳本： 

    [![集合視圖控制器](collection-views-images/collection04.png)](collection-views-images/collection04.png#lightbox)
1. 將按鈕從按鈕拖曳至 [集合視圖控制器]，然後從快顯視窗中選取 [ **推入** ]： 

    [![從快顯視窗中選取 Push](collection-views-images/collection05.png)](collection-views-images/collection05.png#lightbox)
1. 當應用程式執行時，當使用者按一下按鈕時，就會顯示 [收集視圖]。
1. 選取 [集合] 視圖，並在 [**屬性瀏覽器**] 的 [配置 **]** 索引標籤中輸入下列值： 

    [![屬性瀏覽器](collection-views-images/collection06.png)](collection-views-images/collection06.png#lightbox)
1. 這會控制個別儲存格的大小，以及儲存格與集合視圖外緣之間的框線。
1. 選取 [集合視圖控制器]，並 `CityCollectionViewController` 在 [ **Widget]** 索引標籤中將其類別設定為： 

    [![將類別設定為 CityCollectionViewController](collection-views-images/collection07.png)](collection-views-images/collection07.png#lightbox)
1. 選取 [集合] 視圖，並 `CityCollectionView` 在 [ **Widget]** 索引標籤中將其類別設定為： 

    [![將類別設定為 CityCollectionView](collection-views-images/collection08.png)](collection-views-images/collection08.png#lightbox)
1. 選取 [集合] View 儲存格，並 `CityCollectionViewCell` 在 [ **Widget]** 索引標籤中將其類別設定為： 

    [![將類別設定為 CityCollectionViewCell](collection-views-images/collection09.png)](collection-views-images/collection09.png#lightbox)
1. 在 [ **小工具]** 索引標籤 **中，確定配置為** `Flow` ，而且 **捲軸方向** 為 `Vertical` [收集視圖]： 

    [![[Widget] 索引標籤](collection-views-images/collection10.png)](collection-views-images/collection10.png#lightbox)
1. 選取 [集合] View 儲存格， **Identity**並 `CityCell` 在 [ **Widget]** 索引標籤中將其識別設定為： 

    [![將身分識別設定為 CityCell](collection-views-images/collection11.png)](collection-views-images/collection11.png#lightbox)
1. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中啟動新的 **單一 View TvOS 應用程式** 。
1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後在 iOS 設計工具中開啟該檔案。
1. 將影像視圖、標籤和按鈕新增至現有的視圖，並將其設定為如下所示： 

    [![設定版面配置](collection-views-images/collection02vs.png)](collection-views-images/collection02vs.png#lightbox)
1. 將**名稱**指派給影像視圖，並在**屬性瀏覽器**的 [ **Widget]** 索引標籤中指定標籤。 例如： 

    [![屬性瀏覽器](collection-views-images/collection03vs.png)](collection-views-images/collection03vs.png#lightbox)
1. 接下來，將收集視圖控制器拖曳至腳本： 

    [![集合視圖控制器](collection-views-images/collection04vs.png)](collection-views-images/collection04vs.png#lightbox)
1. 將按鈕從按鈕拖曳至 [集合視圖控制器]，然後從快顯視窗中選取 [ **推入** ]： 

    [![從快顯視窗中選取 Push](collection-views-images/collection05vs.png)](collection-views-images/collection05vs.png#lightbox)
1. 當應用程式執行時，當使用者按一下按鈕時，就會顯示 [收集視圖]。
1. 選取 [集合 **]** 視圖，並在 [屬性] **瀏覽器** 的 [配置] 索引標籤中，將 **寬度** 輸入為 _361_ ， **高度** 為 _256_ 
1. 這會控制個別儲存格的大小，以及儲存格與集合視圖外緣之間的框線。
1. 選取 [集合視圖控制器]，並 `CityCollectionViewController` 在 [ **Widget]** 索引標籤中將其類別設定為： 

    [![將類別設定為 CityCollectionViewController](collection-views-images/collection07vs.png)](collection-views-images/collection07vs.png#lightbox)
1. 選取 [集合] 視圖，並 `CityCollectionView` 在 [ **Widget]** 索引標籤中將其類別設定為： 

    [![將類別設定為 CityCollectionView](collection-views-images/collection08vs.png)](collection-views-images/collection08vs.png#lightbox)
1. 選取 [集合] View 儲存格，並 `CityCollectionViewCell` 在 [ **Widget]** 索引標籤中將其類別設定為： 

    [![將類別設定為 CityCollectionViewCell](collection-views-images/collection09vs.png)](collection-views-images/collection09vs.png#lightbox)
1. 在 [ **小工具]** 索引標籤 **中，確定配置為** `Flow` ，而且 **捲軸方向** 為 `Vertical` [收集視圖]： 

    [![[Widget] 索引標籤](collection-views-images/collection10vs.png)](collection-views-images/collection10vs.png#lightbox)
1. 選取 [集合] View 儲存格， **Identity**並 `CityCell` 在 [ **Widget]** 索引標籤中將其識別設定為： 

    [![將身分識別設定為 CityCell](collection-views-images/collection11vs.png)](collection-views-images/collection11vs.png#lightbox)
1. 儲存變更。

-----

如果我們選擇 `Custom` 收集視圖的 **版面**配置，則可以指定自訂版面配置。 Apple 提供內建的功能，可讓您 `UICollectionViewFlowLayout` `UICollectionViewDelegateFlowLayout` 輕鬆地在以方格為基礎的版面配置中呈現資料， (這些是由配置 `flow` 樣式) 使用。 

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Providing-Data-for-the-Collection-View"></a>

## <a name="providing-data-for-the-collection-view"></a>提供收集視圖的資料

既然我們已將收集視圖 (和集合視圖控制器) 新增至腳本，我們需要提供集合的資料。 

<a name="The-Data-Model"></a>

### <a name="the-data-model"></a>資料模型

首先，我們要建立資料的模型，以保存要顯示之影像的檔案名、標題和允許選取城市的旗標。

建立 `CityInfo` 類別，使其看起來如下所示：

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>集合視圖儲存格

現在我們需要定義每個資料格的資料呈現方式。 編輯 `CityCollectionViewCell.cs` (為您自動從分鏡腳本檔案建立的檔案) ，使其看起來如下：

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

針對我們的 tvOS 應用程式，我們將會顯示影像和選擇性的標題。 如果無法選取指定的城市，則會使用下列程式碼來讓影像視圖變暗：

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

當使用者將包含影像的儲存格帶入焦點時，我們會想要使用內建的視差效果來設定下列屬性：

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

如需有關流覽和焦點的詳細資訊，請參閱我們使用 [導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md) ，以及 [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 檔。

<a name="The-Collection-View-Data-Provider"></a>

### <a name="the-collection-view-data-provider"></a>集合視圖 Data Provider

建立我們的資料模型並定義儲存格版面配置之後，讓我們建立集合視圖的資料來源。 資料來源將負責不只提供支援資料，還會清除佇列資料格來顯示幕幕上的個別資料格。

建立 `CityViewDatasource` 類別，使其看起來如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

讓我們更詳細地查看這個類別。 首先，我們會繼承， `UICollectionViewDataSource` 並提供我們在 IOS 設計工具) 中指派的資料格識別碼 (的快捷方式：

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

接下來，我們會為集合資料提供儲存體，並提供用來填入資料的類別：

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

然後，我們會覆寫 `NumberOfSections` 方法，並傳回集合視圖)  (專案群組的區段數目。 在此情況下，只有一個：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

接下來，我們會使用下列程式碼來傳回集合中的專案數：

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

最後，當收集視圖要求具有下列程式碼時，我們會將可重複使用的儲存格清除佇列：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

取得類型的集合視圖儲存格之後 `CityCollectionViewCell` ，我們會在其中填入指定的專案。

<a name="Responding-to-User-Events"></a>

## <a name="responding-to-user-events"></a>回應使用者事件

因為我們想要讓使用者能夠從集合中選取專案，所以我們需要提供收集視圖委派來處理此互動。 我們必須提供方法讓通話的觀點知道使用者所選取的專案。

<a name="The-App-Delegate"></a>

### <a name="the-app-delegate"></a>應用程式委派

我們需要一種方法，將目前選取的專案從集合視圖關聯回呼叫視圖。 我們將在我們的上使用自訂屬性 `AppDelegate` 。 編輯檔案 `AppDelegate.cs` ，並新增下列程式碼：

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

這會定義屬性，並設定一開始會顯示的預設城市。 稍後，我們將使用這個屬性來顯示使用者的選取專案，並允許變更選取專案。

<a name="The-Collection-View-Delegate"></a>

### <a name="the-collection-view-delegate"></a>集合視圖委派

接下來，將新 `CityViewDelegate` 類別新增至專案，使其看起來如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

讓我們仔細看看這個類別。 首先，我們會繼承自 `UICollectionViewDelegateFlowLayout` 。 我們繼承自這個類別的原因 `UICollectionViewDelegate` 是我們使用內建的 `UICollectionViewFlowLayout` 來呈現專案，而不是自訂的版面配置類型。

接下來，我們會使用下列程式碼來傳回個別專案的大小：

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

接著，我們會使用下列程式碼來決定指定的儲存格是否可以取得焦點： 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

我們會檢查指定的支援資料片段是否已 `CanSelect` 將旗標設為 `true` ，並傳回該值。 如需有關流覽和焦點的詳細資訊，請參閱我們使用 [導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md) ，以及 [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 檔。

最後，我們會使用下列程式碼來回應使用者選取專案：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

在這裡，我們將的 `SelectedCity` 屬性設定 `AppDelegate` 為使用者選取的專案，並關閉 [收集視圖控制器]，並返回呼叫我們的觀點。 我們還沒有定義 `ParentController` 收集視圖的屬性，接下來我們要做。

<a name="Configuring-the-Collection-View"></a>

## <a name="configuring-the-collection-view"></a>設定收集視圖

現在我們需要編輯收集視圖，並指派資料來源和委派。 `CityCollectionView.cs`從我們的腳本) 中自動為我們建立 (的檔案，讓它看起來如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

首先，我們會提供快捷方式來存取我們的 `AppDelegate` ： 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

接下來，我們會提供集合視圖的資料來源的快捷方式，以及用來存取收集視圖控制器 (的屬性，以在使用者進行選取) 時關閉集合：

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

然後，我們會使用下列程式碼來初始化收集視圖，並指派我們的資料格類別、資料來源和委派：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

最後，我們想要只有當使用者將焦點放在焦點)  (時，才會顯示影像下的標題。 我們會使用下列程式碼來執行此作業：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

我們將前一個專案的 transparence 設為零 (0) ，而下一個專案的 transparence 則會將焦點放在100%。 這些轉換也會獲得動畫。

## <a name="configuring-the-collection-view-controller"></a>設定收集視圖控制器

現在，我們必須在集合視圖上進行最後的設定，並允許控制器設定我們所定義的屬性，以便在使用者進行選取之後關閉收集視圖。

`CityCollectionViewController.cs`從我們的腳本) 自動建立 (的檔案，使其看起來如下所示：

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>把它全部放在一起 

既然我們已將所有的元件放在一起，以填入並控制收集視圖，我們必須對主視圖進行最後的編輯，以將所有專案整合在一起。

`ViewController.cs`從我們的腳本) 自動建立 (的檔案，使其看起來如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

下列程式碼一開始會從的屬性中顯示選取的專案 `SelectedCity` `AppDelegate` ，並在使用者從集合視圖中進行選取時重新顯示它：

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app"></a>

## <a name="testing-the-app"></a>測試應用程式

一切就緒之後，如果您建立並執行應用程式，主視圖會顯示為預設的城市：

[![主畫面](collection-views-images/run01.png)](collection-views-images/run01.png#lightbox)

如果使用者按一下 [ **選取視圖** ] 按鈕，將會顯示 [收集視圖]：

[![集合視圖](collection-views-images/run02.png)](collection-views-images/run02.png#lightbox)

其 `CanSelect` 屬性設定為的任何城市 `false` 都會顯示為暗灰色，而且使用者將無法設定焦點。 當使用者反白顯示專案 (使其成為焦點時) 會顯示標題，而且可以使用視差效果奧妙在3D 中傾斜影像。

當使用者按一下選取的影像時，會關閉收集視圖，並以新的影像重新顯示主視圖：

[![主畫面上的新影像](collection-views-images/run03.png)](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items"></a>

## <a name="creating-custom-layout-and-reordering-items"></a>建立自訂版面配置和重新排列專案

使用「收集視圖」的其中一個重要功能就是能夠建立自訂的版面配置。 由於 tvOS 繼承自 iOS，因此建立自訂版面配置的程式相同。 如需詳細資訊，請參閱「 [收集視圖](~/ios/user-interface/controls/uicollectionview.md) 」檔的簡介。

最近新增至 iOS 9 的集合視圖，能夠輕鬆地允許重新排列集合中的專案。 同樣地，因為 tvOS 9 是 iOS 9 的子集，所以這是以同樣的方式來完成。 如需詳細資料，請參閱我們的 [收集視圖變更](~/ios/user-interface/controls/uicollectionview.md) 檔。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了如何設計和使用 tvOS 應用程式內的集合視圖。 首先，它會討論群組成收集視圖的所有元素。 接下來，它會示範如何使用分鏡腳本來設計和執行收集視圖。 最後，會提供有關建立自訂版面配置和重新排列專案之資訊的連結。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)