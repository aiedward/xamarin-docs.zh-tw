---
title: "集合檢視"
description: "這篇文章描述 Xamarin.Mac 應用程式中的集合檢視使用。 其中涵蓋建立和維護 Xcode 和介面產生器中的集合檢視，並以程式設計方式使用它們。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: d8fa54f23dfea063fa25f6e26e2df2c2ed82101e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="collection-views"></a>集合檢視

_這篇文章描述 Xamarin.Mac 應用程式中的集合檢視使用。其中涵蓋建立和維護 Xcode 和介面產生器中的集合檢視，並以程式設計方式使用它們。_

使用 C# 和.NET 是 Xamarin.Mac 應用程式，開發人員可以存取相同時 AppKit 集合檢視控制項處於開發人員*OBJECTIVE-C*和*Xcode*沒有。 因為 Xamarin.Mac 會直接與 Xcode 整合，開發人員會使用 Xcode 的_介面產生器_建立和維護集合檢視。

A`NSCollectionView`顯示的組織使用 subviews 方格`NSCollectionViewLayout`。 在方格中的每一個子檢視由`NSCollectionViewItem`它負責管理檢視的內容載入`.xib`檔案。

[ ![執行範例應用程式](collection-view-images/intro01.png)](collection-view-images/intro01.png)

本文涵蓋在 Xamarin.Mac 應用程式中使用的集合檢視的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技巧，整篇文章。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>關於集合檢視

集合檢視的主要目標 (`NSCollectionView`) 是以視覺化方式排列物件使用集合檢視配置的方式組織群組 (`NSCollectionViewLayout`)，與每個個別物件 (`NSCollectionViewItem`) 較大的集合中取得其本身的檢視。 集合檢視使用透過資料繫結和索引鍵-值程式碼撰寫技術和因此，您應該閱讀[資料繫結和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)再繼續進行本文章的文件。

集合檢視有沒有標準的內建集合檢視的項目 （類似的外框或資料表的檢視表），讓開發人員負責設計和實作_原型檢視_使用其他 AppKit 控制項，例如影像欄位文字欄位、 標籤、 等等。此原型檢視將用來顯示，並使用受集合檢視每個項目，並儲存在`.xib`檔案。

開發人員負責集合檢視項目的外觀及操作，因為集合檢視已反白顯示選取的項目在方格中的內建支援。 實作這項功能將涵蓋在本文中。

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>定義資料模型

之前介面產生器中，索引鍵-值撰寫程式碼 (KVC) 中的繫結集合檢視的資料 / 索引鍵-值觀察 (KVO) 標準的類別必須定義 Xamarin.Mac 應用程式，以做為_資料模型_繫結。 資料模型提供的所有資料將會顯示在集合和接收資料，使用者可在 UI 中執行應用程式時的任何修改。

應用程式管理的員工群組的範例，下列類別可以用來定義資料模型：

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

`PersonModel`整個本文的其餘部分將會使用資料模型。

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>使用集合檢視

與集合檢視的資料繫結是非常 like 繫結來搭配 資料表檢視中，做為`NSCollectionViewDataSource`用來提供資料給集合。 集合檢視，沒有預設的顯示格式，因為所需工作愈提供使用者互動的意見反應，以及追蹤使用者選取項目。

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>建立資料格原型

因為集合檢視不包含預設儲存格原型，開發人員必須將一或多個`.xib`Xamarin.Mac 應用程式定義的配置和個別的儲存格內容的檔案。

請執行下列動作：

1. 在**方案總管 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新的檔案...**
2. 選取**Mac** > **檢視控制器**，給它的名稱 (例如`EmployeeItem`在此範例中)，按一下 [**新增**] 按鈕來建立： 

    ![加入新的檢視控制器](collection-view-images/proto01.png)

    這會新增`EmployeeItem.cs`，`EmployeeItemController.cs`和`EmployeeItemController.xib`檔案加入專案的方案。
3. 按兩下`EmployeeItemController.xib`檔案以開啟 Xcode 的介面產生器中進行編輯。
4. 新增`NSBox`，`NSImageView`和兩個`NSLabel`控制項檢視和進行配置，如下所示：

    ![設計版面配置的儲存格原型](collection-view-images/proto02.png)
5. 開啟**助理編輯器**並建立**插座**如`NSBox`，使它可以用來表示儲存格的選取項目狀態：

    ![公開在出口 NSBox](collection-view-images/proto03.png)
6. 返回**標準編輯器**選取的映像的檢視。
7. 在**繫結的偵測器**，選取**繫結至** > **檔案的擁有者**輸入**模型金鑰路徑**的`self.Person.Icon`:

    ![繫結圖示](collection-view-images/proto04.png)
8. 選取第一個標籤和**繫結的偵測器**，選取**繫結至** > **檔案的擁有者**輸入**模型金鑰路徑**的`self.Person.Name`:

    ![繫結的名稱](collection-view-images/proto05.png)
9. 選取第二個標籤和**繫結的偵測器**，選取**繫結至** > **檔案的擁有者**輸入**模型金鑰路徑**的`self.Person.Occupation`:

    ![繫結職業](collection-view-images/proto06.png)
10. 變更儲存到`.xib`檔案，並返回 Visual Studio，同步處理變更。

編輯`EmployeeItemController.cs`檔案，並讓它看起來如下所示：

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

查看這個程式碼的詳細資料，在類別繼承自`NSCollectionViewItem`以便它可以做為集合檢視的資料格的原型。 `Person`屬性會公開用來資料繫結到的影像檢視和標籤在 Xcode 中的類別。 這是執行個體`PersonModel`上面所建立。

`BackgroundColor`屬性是捷徑`NSBox`控制項的`FillColor`，將用來顯示資料格的選取狀態。 藉由覆寫`Selected`屬性`NSCollectionViewItem`，下列程式碼設定或清除此選取項目狀態：

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

### <a name="creating-the-collection-view-data-source"></a>建立集合檢視的資料來源

集合檢視的資料來源 (`NSCollectionViewDataSource`) 提供的所有資料的集合檢視和建立及擴展集合檢視的儲存格 (使用`.xib`原型) 的集合中每個項目的必要項。

加入新的類別專案中，呼叫它`CollectionViewDataSource`並看起來如下所示：

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

查看這個程式碼的詳細資料，在類別繼承自`NSCollectionViewDataSource`公開一份`PersonModel`透過執行個體其`Data`屬性。

因為此集合只具有一個區段，所以程式碼會覆寫`GetNumberOfSections`方法，一律會傳回`1`。 此外，`GetNumberofItems`會覆寫方法在傳回的項目數中`Data`屬性清單。

`GetItem`方法稱為每當新的儲存格需要而看起來如下：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

`MakeItem`集合檢視的方法呼叫來建立或傳回的可重複使用執行個體`EmployeeItemController`及其`Person`屬性設定為項目顯示在要求的資料格。 

`EmployeeItemController`必須先使用下列程式碼的集合檢視控制器中註冊：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

**識別碼**(`EmployeeCell`) 中使用`MakeItem`呼叫_必須_符合檢視控制器已向集合檢視的名稱。 將下面將詳細討論此步驟。

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>處理的項目選取

若要處理的選取和取消選取的項目在集合中，`NSCollectionViewDelegate`就必須執行。 因為此範例會使用內建在`NSCollectionViewFlowLayout`版面配置類型`NSCollectionViewDelegateFlowLayout`將需要此委派的特定版本。

將新類別加入專案中，呼叫`CollectionViewDelegate`並看起來如下所示：

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

`ItemsSelected`和`ItemsDeselected`方法是覆寫，用來設定或清除`PersonSelected`檢視控制站，當使用者選取或取消選取項目時處理集合檢視的屬性。 這會顯示下面將詳細。

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>在介面產生器中建立集合檢視

所有必要的支援部分備妥，可編輯的主要腳本和集合檢視加入。

請執行下列動作：

1. 按兩下`Main.Storyboard`檔案**方案總管 中**開啟進行編輯在 Xcode 中的介面產生器。
2. 集合檢視拖曳至 [Main] 檢視和調整大小以填滿檢視：

    ![加入至配置的集合檢視](collection-view-images/collection01.png)
3. 集合檢視選取的情況下，使用條件約束編輯器將其釘選至檢視會調整大小時：

    ![加入條件約束](collection-view-images/collection02.png)
4. 確定已選取集合檢視中**設計介面**(而非**起捲動檢視**或**剪輯檢視**包含它)，切換至**小幫手編輯器**並建立**插座**集合檢視：

    ![加入條件約束](collection-view-images/collection03.png)
5. 儲存變更並返回 Visual Studio 來同步處理。

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>使其全部整合

所有支援的項目現在已放置位置做為資料模型的類別 (`PersonModel`)、`NSCollectionViewDataSource`已加入至提供資料，`NSCollectionViewDelegateFlowLayout`用於處理項目選取項目和`NSCollectionView`已加入至 Main 分鏡腳本公開為插座 (`EmployeeCollection`)。

最後一個步驟是編輯包含集合檢視的檢視控制器，並讓所有的部分填入集合並處理項目選取項目。

編輯`ViewController.cs`檔案，並讓它看起來如下所示：

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

在詳細資料，看看這段程式碼一個`Datasource`屬性定義為保留的執行個體`CollectionViewDataSource`會提供資料的集合檢視。 A`PersonSelected`屬性定義以保留`PersonModel`表示集合檢視中目前選取的項目。 這個屬性也會引發`SelectionChanged`事件選取範圍變更時。

`ConfigureCollectionView`類別用來註冊檢視控制器做為資料格原型，與集合檢視使用下列行：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

請注意，**識別碼**(`EmployeeCell`) 用來註冊一個呼叫中的原型相符項目`GetItem`方法`CollectionViewDataSource`上面定義：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，檢視控制器類型**必須**符合名稱`.xib`定義原型檔案**完全**。 在此範例中，`EmployeeItemController`和`EmployeeItemController.xib`。

集合檢視中的項目實際配置控制由集合檢視配置的類別，且可以動態變更在執行階段指派到的新執行個體`CollectionViewLayout`屬性。 變更此屬性更新集合檢視的外觀，而不建立動畫的變更。

Apple 隨附兩個內建的配置類型與集合檢視將處理最常見的用法：`NSCollectionViewFlowLayout`和`NSCollectionViewGridLayout`。 如果開發人員所需的自訂格式，例如用來配置的項目中的圓形，他們可以建立自訂的執行個體的`NSCollectionViewLayout`並覆寫所需的方法，來達成所要的效果。

因此，它會建立的執行個體，這個範例會使用預設流程配置`NSCollectionViewFlowLayout`類別，並將它設定，如下所示：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize`屬性集合中定義的每個儲存格大小。 `SectionInset`屬性會定義從資料格會配置中集合的邊緣內凹。 `MinimumInteritemSpacing` 定義項目之間的最小間距和`MinimumLineSpacing`定義集合中的行之間的最小間距。

版面配置已指派給集合檢視和執行個體`CollectionViewDelegate`附加至處理項目選取項目：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData`方法建立的新執行個體`CollectionViewDataSource`、 填入資料、 將其附加至集合檢視，並呼叫`ReloadData`方法顯示的項目：

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

`ViewDidLoad`方法會覆寫，並呼叫`ConfigureCollectionView`和`PopulateWithData`向使用者顯示最終集合檢視的方法：

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

這篇文章已取得在 Xamarin.Mac 應用程式中使用的集合檢視的詳細的檢視。 第一次，則查閱使用的索引鍵-值撰寫程式碼 (KVC) 和索引鍵-值觀察 (KVO) 公開 Objective C 的 C# 類別。 接下來，它會示範如何使用 KVO 標準的類別和資料繫結到 Xcode 的介面產生器中的集合檢視。 最後，它會示範如何在 C# 程式碼與集合檢視互動。

## <a name="related-links"></a>相關連結

- [MacCollectionNew （範例）](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
