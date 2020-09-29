---
title: Xamarin 中的集合視圖
description: 本文說明如何在 Xamarin 應用程式中使用集合視圖。 其中涵蓋了在 Xcode 和 Interface Builder 中建立和維護收集視圖，並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/24/2017
ms.openlocfilehash: 0d95ecb1997ba70a2994d74bacdedd334f2b4c61
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429731"
---
# <a name="collection-views-in-xamarinmac"></a>Xamarin 中的集合視圖

_本文說明如何在 Xamarin 應用程式中使用集合視圖。其中涵蓋了在 Xcode 和 Interface Builder 中建立和維護收集視圖，並以程式設計方式使用它們。_

在 Xamarin 應用程式中使用 c # 和 .NET 時，開發人員可以存取相同的 AppKit 收集視圖控制項，開發人員可在 *目標 C* 和 *Xcode* 中運作。 因為 Xamarin 與 Xcode 直接整合，所以開發人員會使用 Xcode 的 _Interface Builder_ 來建立和維護集合視圖。

會 `NSCollectionView` 顯示使用子檢視組織的方格 `NSCollectionViewLayout` 。 格線中的每個子視圖都會以 `NSCollectionViewItem` 管理從檔案載入視圖內容的來表示 `.xib` 。

[![執行範例應用程式](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

本文涵蓋在 Xamarin 應用程式中使用集合視圖的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋本文中所使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和命令。

<a name="About_Collection_Views"></a>

## <a name="about-collection-views"></a>關於集合視圖

「收集視圖」 () 的主要目標 `NSCollectionView` 是使用集合視圖配置 () ，以組織方式排列一組物件 `NSCollectionViewLayout` ，而每個個別物件 (`NSCollectionViewItem`) 在較大的集合中取得自己的觀點。 集合視圖可透過資料系結和索引鍵/值編碼技術來運作，因此，您應該先閱讀資料系結 [和索引鍵-值的編碼](~/mac/app-fundamentals/databinding.md) 檔，再繼續進行本文。

集合視圖沒有任何標準的內建集合視圖專案 (例如大綱或資料表視圖) ，因此開發人員必須負責使用其他 AppKit 控制項（例如影像欄位、文字欄位、標籤等）來設計和執行 _原型視圖_ 。此原型視圖將用來顯示及使用集合視圖所管理的每個專案，並儲存在檔案中 `.xib` 。

由於開發人員會負責收集視圖專案的外觀和操作，因此，收集視圖沒有內建的支援，無法反白顯示方格中的選取專案。 本文將討論如何執行這項功能。

<a name="Defining_your_Data_Model"></a>

## <a name="defining-the-data-model"></a>定義資料模型

在資料系結 Interface Builder 中的收集視圖之前，您必須在 Xamarin 應用程式中定義索引鍵/值編碼 (KVC) /Key-Value 觀察 (KVO) 相容的類別，以作為系結的 _資料模型_ 。 資料模型會提供將顯示在集合中的所有資料，並在執行應用程式時，接收使用者在 UI 中所做資料的任何修改。

採用管理一組員工的應用程式範例，下列類別可用來定義資料模型：

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

`PersonModel`本文的其餘部分將使用資料模型。

<a name="Working_with_a_Collection_View"></a>

## <a name="working-with-a-collection-view"></a>使用集合視圖

使用收集視圖的資料系結非常類似于資料表視圖的系結，因為 `NSCollectionViewDataSource` 它是用來提供集合的資料。 因為收集視圖沒有預設的顯示格式，所以需要更多工作來提供使用者互動意見反應，以及追蹤使用者選擇。

<a name="Creating-the-Cell-Prototype"></a>

### <a name="creating-the-cell-prototype"></a>建立資料格原型

由於收集視圖不包含預設資料格原型，因此開發人員必須將一個或多個檔案加入 `.xib` 至 Xamarin 應用程式，以定義個別儲存格的配置和內容。

執行下列動作：

1. 在 [**方案總管**中，以滑鼠右鍵按一下專案名稱，然後選取 [**加入**  >  **新**檔案]。
2. 選取 [ **Mac**  >  **視圖控制器**]，為它命名 (例如 `EmployeeItem` 在此範例中) 然後按一下 [**新增**] 按鈕以建立： 

    ![新增視圖控制器](collection-view-images/proto01.png)

    這會將 `EmployeeItem.cs` 和檔案 `EmployeeItemController.cs` 新增 `EmployeeItemController.xib` 至專案的方案。
3. 按兩下檔案 `EmployeeItemController.xib` 將其開啟，以在 Xcode 的 Interface Builder 中進行編輯。
4. 將 `NSBox` 、 `NSImageView` 和兩個 `NSLabel` 控制項新增至視圖，並將其配置如下：

    ![設計資料格原型的版面配置](collection-view-images/proto02.png)
5. 開啟 [ **助理編輯器** ]，並建立的 **輸出** ， `NSBox` 讓它可用來指出儲存格的選取狀態：

    ![在輸出中公開 NSBox](collection-view-images/proto03.png)
6. 返回 [ **標準編輯器** ]，然後選取影像視圖。
7. 在系結偵測**器**中，選取 [系結**至**檔案  >  **的擁有**者]，並輸入的**模型索引鍵路徑** `self.Person.Icon` ：

    ![系結圖示](collection-view-images/proto04.png)
8. 選取第一個標籤，然後在 [系結偵測**器**] 中，選取 [系結**至**檔案  >  **的擁有**者]，並輸入的**模型索引鍵路徑** `self.Person.Name` ：

    ![系結名稱](collection-view-images/proto05.png)
9. 選取第二個標籤，然後在 [系結偵測**器**] 中選取 [系結**至**檔案  >  **的擁有**者]，並輸入的**模型索引鍵路徑** `self.Person.Occupation` ：

    ![系結職業](collection-view-images/proto06.png)
10. 將變更儲存至檔案 `.xib` ，並返回 Visual Studio 以同步處理變更。

編輯檔案 `EmployeeItemController.cs` ，使其看起來如下所示：

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

更詳細地查看此程式碼，類別繼承自， `NSCollectionViewItem` 因此可作為集合視圖儲存格的原型。 `Person`屬性會公開用來將資料系結至 Xcode 中之影像視圖和標籤的類別。 這是上面所建立的實例 `PersonModel` 。

`BackgroundColor`屬性是控制項的快捷方式 `NSBox` `FillColor` ，將用來顯示儲存格的選取狀態。 藉由覆寫的 `Selected` 屬性 `NSCollectionViewItem` ，下列程式碼會設定或清除此選取狀態：

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

<a name="Creating-the-Collection-View-Data-Source"></a>

### <a name="creating-the-collection-view-data-source"></a>建立收集視圖資料來源

集合視圖資料來源 (`NSCollectionViewDataSource`) 提供收集視圖的所有資料，並 `.xib` 根據集合中每個專案的需求，使用原型) 來建立和擴展集合視圖資料格 (。

在專案中加入新的類別，並呼叫它， `CollectionViewDataSource` 使其看起來如下所示：

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

詳細查看此程式碼，類別繼承自， `NSCollectionViewDataSource` 並 `PersonModel` 透過其屬性公開實例清單 `Data` 。

因為這個集合只有一個區段，所以程式碼會覆寫 `GetNumberOfSections` 方法，而且一律會傳回 `1` 。 此外， `GetNumberofItems` 方法會在傳回屬性清單中的專案數時遭到覆寫 `Data` 。

`GetItem`每當需要新的儲存格時，就會呼叫方法，如下所示：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

`MakeItem`系統會呼叫收集視圖的方法來建立或傳回可重複使用的實例 `EmployeeItemController` ，而且其 `Person` 屬性會設定為在要求的儲存格中顯示的專案。 

`EmployeeItemController`必須使用下列程式碼，事先向收集視圖控制器註冊：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

呼叫中所使用的 **識別碼** (`EmployeeCell`) `MakeItem` _必須_ 符合已向收集視圖註冊的視圖控制器名稱。 以下將詳細說明此步驟。

<a name="Handling-Item-Selection"></a>

### <a name="handling-item-selection"></a>處理專案選取專案

若要處理集合中專案的選取和 deselection， `NSCollectionViewDelegate` 則需要。 由於此範例將使用內建的 `NSCollectionViewFlowLayout` 版面配置類型，因此 `NSCollectionViewDelegateFlowLayout` 將需要此委派的特定版本。

將新類別新增至專案，並呼叫它， `CollectionViewDelegate` 使其看起來如下所示：

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

`ItemsSelected` `ItemsDeselected` 當使用者選取或取消選取專案時，和方法會被覆寫並用來設定或清除 `PersonSelected` 處理集合視圖的視圖控制器的屬性。 以下將詳細說明這一點。

<a name="Creating-the-Collection-View-in-Interface-Builder"></a>

### <a name="creating-the-collection-view-in-interface-builder"></a>在 Interface Builder 中建立收集視圖

有了所有必要的支援元件之後，就可以編輯主要的腳本，並在其中加入集合視圖。

執行下列動作：

1. 按兩下 `Main.Storyboard` **方案總管** 中的檔案，將其開啟，以在 Xcode 的 Interface Builder 中進行編輯。
2. 將收集視圖拖曳至主視圖，並調整其大小以填滿視圖：

    ![將收集視圖加入至版面配置](collection-view-images/collection01.png)
3. 選取 [收集視圖] 之後，請使用 [條件約束編輯器]，在調整大小時將它釘選到視圖：

    ![加入條件約束](collection-view-images/collection02.png)
4. 確定已在 [ **Design Surface** ] (中選取 [收集視圖]，而不是在包含它) 的上框線 **滾動** 條或 **剪切視圖** 中選取，請切換至 [ **助理編輯器** ]，並建立集合視圖的 **輸出** ：

    ![加入條件約束](collection-view-images/collection03.png)
5. 儲存變更並返回 Visual Studio 進行同步處理。

<a name="Bringing-it-all-Together"></a>

## <a name="bringing-it-all-together"></a>全部整合

所有的支援部分現在都已放在一個類別中，以做為資料模型 (`PersonModel`) 、已 `NSCollectionViewDataSource` 加入至提供資料、已 `NSCollectionViewDelegateFlowLayout` 建立用來處理專案選取專案，以及已新增至主要分鏡腳本， `NSCollectionView` 並公開為 () 的輸出 `EmployeeCollection` 。

最後一個步驟是編輯包含收集視圖的視圖控制器，並將所有元件結合在一起，以填入集合並處理專案選取專案。

編輯檔案 `ViewController.cs` ，使其看起來如下所示：

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

仔細看一下此程式碼， `Datasource` 定義一個屬性來保存的實例，該實例 `CollectionViewDataSource` 會提供收集視圖的資料。 `PersonSelected`定義屬性，以保存 `PersonModel` 代表集合視圖中目前所選取專案的。 當選取專案變更時，這個屬性也會引發 `SelectionChanged` 事件。

`ConfigureCollectionView`類別可用來註冊 View 控制器，以使用下列這一行的集合視圖作為資料格原型：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

請注意， **Identifier** `EmployeeCell` 用來註冊原型的識別碼 () 符合 `GetItem` 上述定義之方法中所呼叫的識別碼 `CollectionViewDataSource` ：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，視圖控制器的類型 **必須** `.xib` **完全**符合定義原型的檔案名。 在此範例中為 `EmployeeItemController` 和 `EmployeeItemController.xib` 。

集合視圖中專案的實際版面配置是由集合視圖配置類別所控制，而且可以在執行時間動態變更，方法是將新的實例指派給 `CollectionViewLayout` 屬性。 變更此屬性會更新集合視圖外觀，而不會將變更動畫。

Apple 提供兩個內建的版面配置類型，以及將處理最常見用途的收集視圖： `NSCollectionViewFlowLayout` 和 `NSCollectionViewGridLayout` 。 如果開發人員需要自訂格式，例如將專案排放在圓形中，則可以建立的自訂實例， `NSCollectionViewLayout` 並覆寫必要的方法以達成所要的效果。

此範例會使用預設流程配置，因此它會建立類別的實例 `NSCollectionViewFlowLayout` ，並依照下列方式進行設定：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize`屬性會定義集合中每個個別資料格的大小。 `SectionInset`屬性會定義要在其中放置儲存格之集合邊緣的內建。 `MinimumInteritemSpacing` 定義專案之間的最小間距，並 `MinimumLineSpacing` 定義集合中各行之間的最小間距。

配置會指派給集合視圖，並附加的實例 `CollectionViewDelegate` 以處理專案選取：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData`方法會建立的新實例 `CollectionViewDataSource` ，並在其中填入資料、將其附加至集合視圖，然後呼叫 `ReloadData` 方法來顯示專案：

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

`ViewDidLoad`方法會被覆寫並呼叫 `ConfigureCollectionView` 和 `PopulateWithData` 方法，以顯示最終的收集視圖給使用者：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文詳細說明如何在 Xamarin 應用程式中使用集合視圖。 首先，它會探討如何使用索引鍵/值編碼 (KVC) 和索引鍵-值觀察 (KVO) ，將 c # 類別公開至目標 C。 接下來，它會示範如何使用符合 KVO 規範的類別，並將資料系結至 Xcode 的 Interface Builder 中的集合視圖。 最後，它會示範如何在 c # 程式碼中與集合視圖互動。

## <a name="related-links"></a>相關連結

- [MacCollectionNew (範例) ](/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和機碼值編碼](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)