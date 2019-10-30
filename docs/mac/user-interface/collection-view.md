---
title: Xamarin 中的集合視圖
description: 本文說明如何在 Xamarin. Mac 應用程式中使用集合視圖。 其中涵蓋在 Xcode 和 Interface Builder 中建立和維護收集視圖，並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/24/2017
ms.openlocfilehash: 565441762bc7d9dcf7f73b42a34e3feb0bff86f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025826"
---
# <a name="collection-views-in-xamarinmac"></a>Xamarin 中的集合視圖

_本文說明如何在 Xamarin. Mac 應用程式中使用集合視圖。其中涵蓋在 Xcode 和 Interface Builder 中建立和維護收集視圖，並以程式設計方式使用它們。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時，開發人員可以存取相同的 AppKit 集合 View 控制項，而開發人員是以*目標-C*和*Xcode*進行操作。 因為 Xamarin 會直接與 Xcode 整合，所以開發人員會使用 Xcode 的_Interface Builder_來建立和維護集合視圖。

`NSCollectionView` 會顯示使用 `NSCollectionViewLayout`組織的子檢視方格。 方格中的每個子視圖都是由一個 `NSCollectionViewItem` 表示，它會管理從 `.xib` 檔案載入視圖內容的方式。

[![範例應用程式執行](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

本文涵蓋在 Xamarin. Mac 應用程式中使用集合視圖的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了所使用的重要概念和技術。本文中。

您可能想要看一下[Xamarin 內部](~/mac/internals/how-it-works.md)檔的「 C# [公開C#類別/方法到目標-C](~/mac/internals/how-it-works.md) 」一節，它會說明用來將類別連線到目標-c 的`Register`和`Export`命令物件和 UI 元素。

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>關於集合視圖

集合視圖（`NSCollectionView`）的主要目標是要使用集合視圖配置（`NSCollectionViewLayout`）以組織的方式，以視覺方式排列一組物件，其中每個個別物件（`NSCollectionViewItem`）都會在較大的集合中取得自己的視圖。 集合視圖會透過資料系結和索引鍵/值編碼技術來執行，因此您應該先閱讀資料系結[和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)檔，再繼續進行本文。

集合視圖沒有標準的內建集合視圖專案（例如大綱或資料表視圖），因此開發人員必須負責使用其他 AppKit 控制項（例如影像欄位、文字欄位、標籤）來設計和執行_原型視圖_。等.這個原型視圖將用來顯示及使用由集合視圖所管理的每個專案，並儲存在 `.xib` 檔案中。

由於開發人員負責收集視圖專案的外觀與風格，因此 [集合] 視圖並沒有內建的支援，可反白顯示方格中選取的專案。 本文將涵蓋此功能的執行。

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>定義資料模型

在 Interface Builder 系結集合視圖的資料之前，必須在 Xamarin. Mac 應用程式中定義索引鍵/值編碼（KVC）/Key-Value 觀察（KVO）相容的類別，以作為系結的_資料模型_。 資料模型會提供將顯示在集合中的所有資料，並接收使用者在執行應用程式時，在 UI 中對資料所做的任何修改。

如需管理一組員工的應用程式範例，可以使用下列類別來定義資料模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

本文的其餘部分將使用 `PersonModel` 資料模型。

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>使用集合視圖

具有集合視圖的資料系結非常類似于資料表視圖的系結，因為 `NSCollectionViewDataSource` 是用來提供集合的資料。 由於集合視圖沒有預設的顯示格式，因此需要更多工具來提供使用者互動意見反應，並追蹤使用者選取專案。

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>建立資料格原型

由於集合視圖不包含預設的資料格原型，因此開發人員必須將一或多個 `.xib` 檔案加入至 Xamarin. Mac 應用程式，以定義個別資料格的配置和內容。

請執行下列動作：

1. 在 **方案總管**中，以滑鼠右鍵按一下專案名稱，然後選取 **加入** > **新增檔案 ...**
2. 選取 [ **Mac** > **View Controller**]，指定名稱（例如此範例中的 `EmployeeItem`），然後按一下 [**新增**] 按鈕以建立： 

    ![加入新的視圖控制器](collection-view-images/proto01.png)

    這會將 `EmployeeItem.cs`、`EmployeeItemController.cs` 和 `EmployeeItemController.xib` 檔案加入至專案的方案。
3. 按兩下 `EmployeeItemController.xib` 檔案，將它開啟，以在 Xcode 的 Interface Builder 中進行編輯。
4. 將 [`NSBox`]、[`NSImageView`] 和兩個 `NSLabel` 控制項加入至視圖，並將其排列，如下所示：

    ![設計資料格原型的版面配置](collection-view-images/proto02.png)
5. 開啟 [**助理編輯器**] 並建立 `NSBox` 的 [**輸出**]，讓它可以用來表示資料格的選取狀態：

    ![公開插座中的 NSBox](collection-view-images/proto03.png)
6. 返回**標準編輯器**，然後選取影像視圖。
7. 在系結偵測**器**中，選取 [系結**至** > 檔案**的擁有**者]，然後輸入 `self.Person.Icon`的**模型機碼路徑**：

    ![系結圖示](collection-view-images/proto04.png)
8. 選取第一個標籤，然後在系結偵測**器**中選取 [系結 **] > 檔案** **的擁有**者，並輸入 `self.Person.Name`的**模型機碼路徑**：

    ![系結名稱](collection-view-images/proto05.png)
9. 選取第二個標籤，然後在系結偵測**器**中選取 [系結 **] > 檔案** **的擁有**者，並輸入 `self.Person.Occupation`的**模型機碼路徑**：

    ![系結職業](collection-view-images/proto06.png)
10. 儲存 `.xib` 檔案的變更，並返回 Visual Studio 以同步變更。

編輯 `EmployeeItemController.cs` 檔案，使其看起來如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

查看此程式碼的詳細資訊，類別繼承自 `NSCollectionViewItem`，因此它可以做為集合視圖儲存格的原型。 `Person` 屬性會公開用來將資料系結至影像視圖的類別，以及 Xcode 中的標籤。 這是上面所建立 `PersonModel` 的實例。

`BackgroundColor` 屬性是 `NSBox` 控制項 `FillColor` 的快捷方式，將用來顯示儲存格的選取狀態。 藉由覆寫 `NSCollectionViewItem`的 `Selected` 屬性，下列程式碼會設定或清除此選取狀態：

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>建立集合視圖資料來源

集合視圖資料來源（`NSCollectionViewDataSource`）會提供集合視圖的所有資料，並根據集合中每個專案的需要，建立並填入集合視圖儲存格（使用 `.xib` 原型）。

新增專案的新類別，並 `CollectionViewDataSource` 呼叫，使其看起來如下所示：

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

查看此程式碼的詳細資訊，類別繼承自 `NSCollectionViewDataSource`，並透過其 `Data` 屬性公開 `PersonModel` 實例的清單。

由於此集合只有一個區段，因此程式碼會覆寫 `GetNumberOfSections` 方法，而且一律會傳回 `1`。 此外，`GetNumberofItems` 方法會在它傳回 `Data` 屬性清單中的專案數時遭到覆寫。

每當需要新的儲存格時，就會呼叫 `GetItem` 方法，如下所示：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

系統會呼叫集合視圖的 `MakeItem` 方法，以建立或傳回 `EmployeeItemController` 的可重複使用實例，而且其 `Person` 屬性會設定為在要求的儲存格中顯示的專案。 

`EmployeeItemController` 必須使用下列程式碼，預先向集合視圖控制器註冊：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

`MakeItem` 呼叫中使用的**識別碼**（`EmployeeCell`）_必須_符合向集合視圖註冊的視圖控制器名稱。 下面將詳細說明此步驟。

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>處理專案選取

若要處理集合中專案的選取和 deselection，則需要 `NSCollectionViewDelegate`。 由於此範例會使用內建的 `NSCollectionViewFlowLayout` 版面配置類型，因此將需要此委派的 `NSCollectionViewDelegateFlowLayout` 特定版本。

將新類別新增至專案，並 `CollectionViewDelegate` 呼叫，使其看起來如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

`ItemsSelected` 和 `ItemsDeselected` 方法會遭到覆寫，並用來設定或清除 View Controller 的 `PersonSelected` 屬性，該控制器會在使用者選取或取消選取專案時處理集合視圖。 下面將詳細說明這一點。

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>在 Interface Builder 中建立集合視圖

當所有必要的支援部分都備妥時，可以編輯主要的分鏡腳本，並在其中加入集合視圖。

請執行下列動作：

1. 按兩下**方案總管**中的 `Main.Storyboard` 檔案，將它開啟，以在 Xcode 的 Interface Builder 中進行編輯。
2. 將 [集合] 視圖拖曳到主視圖中，並調整其大小以填滿此視圖：

    ![將集合視圖加入至版面配置](collection-view-images/collection01.png)
3. 選取 [集合] 視圖後，使用 [條件約束編輯器]，在調整大小時將其釘選到視圖：

    ![加入條件約束](collection-view-images/collection02.png)
4. 請確定已在  **Design Surface**中選取 集合 視圖（而不是以框出的**滾動**條或包含它的**剪切**視圖），切換至 **助理編輯器**，並建立 集合 視圖的 **插座**：

    ![加入條件約束](collection-view-images/collection03.png)
5. 儲存變更並返回 Visual Studio 以進行同步處理。

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>整合在一起

所有支援的專案現在都已放入一個類別，以作為資料模型（`PersonModel`）、加入 `NSCollectionViewDataSource` 以提供資料、建立 `NSCollectionViewDelegateFlowLayout` 來處理專案選取，以及將 `NSCollectionView` 新增至主要腳本和公開為插座（`EmployeeCollection`）。

最後一個步驟是編輯包含集合視圖的 View Controller，並將所有元件全部整合在一起，以填入集合並處理專案選擇。

編輯 `ViewController.cs` 檔案，使其看起來如下所示：

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

請仔細查看這段程式碼，其中會定義 `Datasource` 屬性來保存 `CollectionViewDataSource` 的實例，以提供集合視圖的資料。 定義 `PersonSelected` 屬性來保存代表集合視圖中目前所選取專案的 `PersonModel`。 當選取範圍變更時，這個屬性也會引發 `SelectionChanged` 事件。

`ConfigureCollectionView` 類別是用來向使用下列這一行的集合視圖，註冊做為資料格原型的視圖控制器：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

請注意，用來註冊原型的**識別碼**（`EmployeeCell`）與上面定義之 `CollectionViewDataSource` 的 `GetItem` 方法中所呼叫的相符：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，視圖控制器的類型**必須** **符合定義原型之**`.xib` 檔的名稱。 在此範例的情況下，`EmployeeItemController` 和 `EmployeeItemController.xib`。

集合視圖中專案的實際配置是由集合視圖配置類別所控制，而且可以藉由將新的實例指派給 `CollectionViewLayout` 屬性，在執行時間動態變更。 變更此屬性會更新集合視圖外觀，而不會製作變更的動畫。

Apple 隨附兩個內建的版面配置類型，其中包含將處理最常見用法的集合視圖： `NSCollectionViewFlowLayout` 和 `NSCollectionViewGridLayout`。 如果開發人員需要自訂格式，例如將專案配置在圓形中，則可以建立 `NSCollectionViewLayout` 的自訂實例，並覆寫必要的方法以達到所要的效果。

這個範例會使用預設的流程配置，因此它會建立 `NSCollectionViewFlowLayout` 類別的實例，並如下所示設定它：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize` 屬性會定義集合中每個個別資料格的大小。 [`SectionInset`] 屬性會從集合的邊緣定義要配置資料格的插入。 `MinimumInteritemSpacing` 定義專案之間的最小間距，`MinimumLineSpacing` 定義集合中各行之間的最小間距。

配置會指派給 [集合] 視圖，而 `CollectionViewDelegate` 的實例則會附加至 [處理專案] 選取範圍：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData` 方法會建立 `CollectionViewDataSource`的新實例、填入資料、將它附加至集合視圖，以及呼叫 `ReloadData` 方法來顯示專案：

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

會覆寫 `ViewDidLoad` 方法，並呼叫 `ConfigureCollectionView` 和 `PopulateWithData` 方法，以向使用者顯示最終的集合視圖：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用集合視圖。 首先，它探討了C#如何使用索引鍵/值編碼（KVC）和索引鍵-值觀察（KVO），將類別公開至目標-C。 接下來，它會示範如何使用 KVO 相容的類別，並將資料系結至 Xcode 的 Interface Builder 中的集合 Views。 最後，它會示範如何在程式碼中C#與集合視圖互動。

## <a name="related-links"></a>相關連結

- [MacCollectionNew （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
