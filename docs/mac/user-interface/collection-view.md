---
title: Xamarin.mac 的集合檢視
description: 這篇文章說明如何使用 Xamarin.Mac 應用程式中的集合檢視。 它涵蓋了建立和維護在 Xcode 和 Interface Builder 中的集合檢視，並以程式設計方式處理它們。
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: c8b4b5ff8bebf5fbbded410ae84d1aefcca2d6cc
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250954"
---
# <a name="collection-views-in-xamarinmac"></a>Xamarin.mac 的集合檢視

_這篇文章說明如何使用 Xamarin.Mac 應用程式中的集合檢視。它涵蓋了建立和維護在 Xcode 和 Interface Builder 中的集合檢視，並以程式設計方式處理它們。_

當使用 C# 和.NET 在 Xamarin.Mac 應用程式，開發人員可以存取相同 AppKit 集合檢視控制項工作的開發人員*OBJECTIVE-C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，開發人員會使用 Xcode 的_Interface Builder_來建立和維護集合檢視。

A`NSCollectionView`顯示的組織使用的子檢視方格`NSCollectionViewLayout`。 在方格中的每一個子檢視由`NSCollectionViewItem`會管理從檢視表的內容載入`.xib`檔案。

[![執行範例應用程式](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

本文涵蓋在 Xamarin.Mac 應用程式中使用的集合檢視的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋的重要概念和這篇文章中使用的技術。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>如需集合檢視

集合檢視的主要目標 (`NSCollectionView`) 是以視覺化方式排列一組有組織的方式使用集合檢視版面配置的物件 (`NSCollectionViewLayout`)，與每個個別的物件 (`NSCollectionViewItem`) 較大的集合中取得自己的檢視。 透過資料繫結和鍵值編碼技術工作的集合檢視，和此情況下，您應該先閱讀[資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)再繼續進行這篇文章的文件。

集合檢視有沒有標準的內建集合檢視項目 （類似的外框 」 或 「 資料表檢視），讓開發人員負責設計和實作_原型檢視_使用其他完整 AppKit 控制項，例如影像欄位文字欄位、 標籤、 等等。此原型檢視將用於顯示，並使用受集合檢視每個項目，而且會儲存在`.xib`檔案。

因為開發人員負責的外觀及操作集合檢視項目，此集合檢視已反白顯示選取的項目在方格中的內建支援。 這篇文章將涵蓋實作這項功能。

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>定義資料模型

資料繫結介面產生器中，索引鍵-值撰寫程式碼 (KVC) 中的集合檢視之前 / 索引鍵-值觀察 (KVO) 規範的類別必須定義在 Xamarin.Mac 應用程式，以作為_資料模型_繫結。 資料模型提供的所有資料，將會顯示集合中，並接收到的資料，使用者在 UI 中執行的應用程式時進行任何修改。

管理一群員工的應用程式為例，下列類別可用來定義資料模型：

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

`PersonModel`這篇文章的其餘部分將會使用資料模型。

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>使用集合檢視

集合檢視使用資料繫結為資料表檢視中，非常 like 繫結`NSCollectionViewDataSource`用來提供資料的集合。 因為集合檢視，沒有預設的顯示格式，所需工作來提供使用者互動的意見反應，並追蹤使用者選取項目。

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>建立的儲存格原型

因為集合檢視不包含預設資料格原型，開發人員必須將一或多個`.xib`Xamarin.Mac 應用程式定義的配置和個別的儲存格內容的檔案。

請執行下列動作：

1. 在 **方案總管**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **新檔案...**
2. 選取**Mac** > **檢視控制器**，給它一個名稱 (例如`EmployeeItem`在這個範例中)，按一下 [**新增**] 按鈕來建立： 

    ![加入新的檢視控制器](collection-view-images/proto01.png)

    這會新增`EmployeeItem.cs`，`EmployeeItemController.cs`和`EmployeeItemController.xib`檔案至專案的方案。
3. 按兩下`EmployeeItemController.xib`檔案將它開啟以在 Xcode 的 Interface Builder 中編輯。
4. 新增`NSBox`，`NSImageView`並將兩個`NSLabel`控制項檢視和配置它們，如下所示：

    ![設計版面配置的儲存格原型](collection-view-images/proto02.png)
5. 開啟**輔助編輯器**並建立**插座**如`NSBox`，讓它可以用來表示儲存格的選取項目狀態：

    ![公開在輸出中 NSBox](collection-view-images/proto03.png)
6. 返回**標準編輯器**選取的映像檢視。
7. 在 **繫結偵測器**，選取**繫結至** > **檔案的擁有者**，然後輸入**模型機碼路徑**的`self.Person.Icon`:

    ![繫結圖示](collection-view-images/proto04.png)
8. 選取第一個標籤和在**繫結偵測器**，選取**繫結至** > **檔案的擁有者**，然後輸入**模型索引鍵路徑**的`self.Person.Name`:

    ![繫結名稱](collection-view-images/proto05.png)
9. 選取第二個標籤並在**繫結偵測器**，選取**繫結至** > **檔案的擁有者**，然後輸入**模型索引鍵路徑**的`self.Person.Occupation`:

    ![繫結的職業](collection-view-images/proto06.png)
10. 變更儲存到`.xib`檔案，並返回 Visual Studio，將變更同步。

編輯`EmployeeItemController.cs`檔案，並使它看起來如下：

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

查看這個程式碼的詳細資料，該類別繼承自`NSCollectionViewItem`讓其可做為集合檢視儲存格的原型。 `Person`屬性會公開用來資料繫結至在 Xcode 中的標籤與映像檢視的類別。 這是執行個體`PersonModel`上面所建立。

`BackgroundColor`屬性是捷徑`NSBox`控制項的`FillColor`，將會用來顯示資料格的選取項目狀態。 藉由覆寫`Selected`屬性`NSCollectionViewItem`，下列程式碼設定或清除此選取項目狀態：

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

集合檢視的資料來源 (`NSCollectionViewDataSource`) 的集合檢視中提供的所有資料和建立及擴展集合檢視儲存格 (使用`.xib`原型) 視需要針對集合中的每個項目。

將新類別加入專案中，呼叫它`CollectionViewDataSource`，使它看起來如下所示：

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

查看這個程式碼的詳細資料，該類別繼承自`NSCollectionViewDataSource`，並公開一份`PersonModel`執行個體透過其`Data`屬性。

這個集合只有一個區段，因為程式碼會覆寫`GetNumberOfSections`方法，一律會傳回`1`。 此外，`GetNumberofItems`會覆寫方法，它會傳回在中的項目數`Data`屬性清單。

`GetItem`方法稱為每當需要新的儲存格，並看起來如下：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

`MakeItem`集合檢視的方法呼叫來建立，或傳回的可重複使用執行個體`EmployeeItemController`及其`Person`屬性設定為項目顯示在要求的資料格。 

`EmployeeItemController`必須向事先使用下列程式碼的集合檢視控制器：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

**識別碼**(`EmployeeCell`) 中使用`MakeItem`呼叫_必須_符合檢視控制器已向此集合檢視的名稱。 此步驟會在下方詳細說明。

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>處理項目選取

若要處理的選取項目和項目在集合中，取消選取`NSCollectionViewDelegate`都必須使用。 因為此範例會使用內建中`NSCollectionViewFlowLayout`版面配置類型`NSCollectionViewDelegateFlowLayout`就必須使用此委派的特定版本。

將新類別加入專案中，呼叫它`CollectionViewDelegate`，使它看起來如下所示：

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

`ItemsSelected`並`ItemsDeselected`方法會覆寫，並可用來設定或清除`PersonSelected`屬性的檢視控制器會處理集合檢視，當使用者選取或取消選取項目。 這會顯示的詳細說明如下。

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>在 介面產生器中建立集合檢視

所有需要支援部分之後，可以編輯主要分鏡腳本與集合檢視加入至它。

請執行下列動作：

1. 按兩下`Main.Storyboard`檔案中**方案總管 中**開啟它進行編輯在 Xcode 的 Interface Builder。
2. 集合檢視拖曳至 [Main] 檢視，並調整其大小以填滿檢視：

    ![將集合檢視加入至版面配置](collection-view-images/collection01.png)
3. 與所選取的集合檢視，請將它釘選到 檢視會調整大小時使用條件約束編輯器：

    ![新增條件約束](collection-view-images/collection02.png)
4. 確定已選取 [集合] 檢視中**設計介面**(而非**起捲動檢視**或是**剪輯檢視**包含它)，切換至**輔助編輯器**並建立**插座**集合檢視：

    ![新增條件約束](collection-view-images/collection03.png)
5. 儲存變更並返回 Visual Studio 進行同步處理。

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>將它全部整合

所有支援的部分現在已放置位置做為資料模型的類別 (`PersonModel`)，則`NSCollectionViewDataSource`來提供資料，我們已`NSCollectionViewDelegateFlowLayout`之前建立來處理項目選取和`NSCollectionView`已新增至主要分鏡腳本並公開為輸出 (`EmployeeCollection`)。

最後一個步驟是編輯檢視控制器包含此集合檢視，並將所有填入集合，並處理的項目選取項目。

編輯`ViewController.cs`檔案，並使它看起來如下：

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

在詳細資料，看一下這段程式碼`Datasource`屬性定義為保留的執行個體`CollectionViewDataSource`，將會提供集合檢視中的資料。 A`PersonSelected`屬性定義為保留`PersonModel`表示集合檢視中目前選取的項目。 這個屬性也會引發`SelectionChanged`事件選取範圍變更時。

`ConfigureCollectionView`類別用來註冊與集合檢視使用下面這一行可做為資料格原型檢視控制器：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

請注意，**識別碼**(`EmployeeCell`) 用來註冊呼叫中的其中一個原型相符項目`GetItem`方法`CollectionViewDataSource`上面所定義：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，將檢視控制器的型別**必須**比對的名稱`.xib`定義的原型檔案**完全**。 在此範例中，`EmployeeItemController`和`EmployeeItemController.xib`。

實際的版面配置的集合檢視中的項目控制由集合檢視版面配置的類別，可以動態變更在執行階段所指派的新執行個體`CollectionViewLayout`屬性。 變更此屬性更新集合檢視的外觀，而不以動畫顯示變更。

Apple 提供具有將處理最常見的使用集合檢視的兩個內建的版面配置類型：`NSCollectionViewFlowLayout`和`NSCollectionViewGridLayout`。 如果開發人員所需的自訂格式，例如放置在圓形，項目，他們就可以建立自訂的執行個體的`NSCollectionViewLayout`並覆寫所需的方法，來達成所要的效果。

這個範例會使用預設的流程版面配置，因此它會建立的執行個體`NSCollectionViewFlowLayout`類別，並將它設定，如下所示：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize`屬性定義集合中的每個個別的資料格的大小。 `SectionInset`屬性會定義內凹，從資料格會在配置的集合中的邊緣。 `MinimumInteritemSpacing` 定義項目之間的最小間距和`MinimumLineSpacing`定義集合中的行之間的最小間距。

版面配置指派給集合檢視和執行個體`CollectionViewDelegate`附加至處理項目選取項目：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData`方法建立的新執行個體`CollectionViewDataSource`、 填入資料、 將它附加到集合檢視，並呼叫`ReloadData`方法顯示的項目：

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

`ViewDidLoad`方法會覆寫，並呼叫`ConfigureCollectionView`和`PopulateWithData`方法，以向使用者顯示最終的集合檢視：

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

本文所深入了解在 Xamarin.Mac 應用程式中使用的集合檢視。 首先，它會探討使用索引鍵-值撰寫程式碼 (KVC) 和索引鍵-值觀察 (KVO) 公開以 OBJECTIVE-C 的 C# 類別。 接下來，它示範了如何使用 KVO 規範的類別和資料繫結至在 Xcode 的 Interface Builder 中的集合檢視。 最後，它會說明如何以 C# 程式碼中使用 「 集合檢視互動。

## <a name="related-links"></a>相關連結

- [MacCollectionNew （範例）](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
