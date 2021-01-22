---
title: Xamarin 中的資料系結和索引鍵/值編碼
description: 本文說明如何使用索引鍵/值編碼和索引鍵/值觀察，以允許資料系結至 Xcode 的 Interface Builder 中的 UI 元素。
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 4f53fc12ca056e7fc8d115cf2f8874f54a0ebd0b
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697627"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Xamarin 中的資料系結和索引鍵/值編碼

_本文說明如何使用索引鍵/值編碼和索引鍵/值觀察，以允許資料系結至 Xcode 的 Interface Builder 中的 UI 元素。_

## <a name="overview"></a>概觀

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取相同的索引鍵/值編碼和資料系結技術，而開發人員會在 *目標 C* 和 *Xcode* 中運作。 由於 Xamarin 與 Xcode 直接整合，因此您可以使用 Xcode 的 _Interface Builder_ 與 UI 元素系結資料，而不是撰寫程式碼。

藉由在您的 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以從前端消費者介面 (_模型-視圖控制器_) ，進一步將支援資料 (_資料模型_ 分離) ，讓您更容易維護、更具彈性的應用程式設計。

[![執行中應用程式的範例](databinding-images/intro01.png "執行中應用程式的範例")](databinding-images/intro01-large.png#lightbox)

在本文中，我們將討論如何在 Xamarin 應用程式中使用索引鍵/值編碼和資料系結的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 c 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和屬性。

<a name="What_is_Key-Value_Coding"></a>

## <a name="what-is-key-value-coding"></a>什麼是索引鍵-值的編碼

索引鍵/值編碼 (KVC) 是一種機制，可讓您使用索引鍵 (特殊格式的字串來間接存取物件的屬性，) 識別屬性，而不是透過執行個體變數或存取子方法 () 來存取它們 `get/set` 。 藉由在您的 Xamarin 應用程式中執行符合索引鍵/值的存取子，您就可以存取之前稱為 OS X) 功能的其他 macOS (，例如索引鍵-值觀察 (KVO) 、資料系結、核心資料、Cocoa 系結和 scriptability。

藉由在您的 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以從前端消費者介面 (_模型-視圖控制器_) ，進一步將支援資料 (_資料模型_ 分離) ，讓您更容易維護、更具彈性的應用程式設計。

例如，讓我們看看下列 KVC 相容物件的類別定義：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

首先， `[Register("PersonModel")]` 屬性會註冊類別，並將它公開至目標 C。 然後，類別需要繼承自 `NSObject` (或繼承自) 的子類別 `NSObject` ，這會新增數個可讓類別 KVC 相容的基底方法。 接下來， `[Export("Name")]` 屬性會公開 `Name` 屬性，並定義稍後用來透過 KVC 和 KVO 技術來存取屬性的索引鍵值。

最後，若要 Key-Value 觀察到屬性值的變更，存取子必須在和方法呼叫中包裝其值的變更 `WillChangeValue` ， `DidChangeValue` (指定與屬性) 相同的索引鍵 `Export` 。  例如：

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

此步驟對於 Xcode 的 Interface Builder (中的資料系結來說 _非常_ 重要，因為我們將在本文稍後看到) 。

如需詳細資訊，請參閱 Apple 的索引 [鍵/值編碼程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)。

### <a name="keys-and-key-paths"></a>金鑰和金鑰路徑

索引 _鍵_ 是一種字串，可識別物件的特定屬性。 一般而言，索引鍵會對應至索引鍵/值編碼相容物件中的存取子方法名稱。 金鑰必須使用 ASCII 編碼，通常是以小寫字母開頭，且不能包含空格。 因此，假設上述範例，會 `Name` 是類別之屬性的索引鍵值 `Name` `PersonModel` 。 它們所公開之屬性的索引鍵和名稱不一定要相同，不過在大部分情況下都是如此。

_金鑰路徑_ 是以點分隔的索引鍵字串，用來指定要進行遍歷的物件屬性階層。 序列中第一個索引鍵的屬性是相對於接收者，而每個後續的索引鍵則會相對於前一個屬性的值進行評估。 同樣地，您可以使用點標記法來跨越 c # 類別中的物件和其屬性。

例如，如果您展開 `PersonModel` 類別並加入 `Child` 屬性：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

子系名稱的金鑰路徑會是 `self.Child.Name` 或單純 `Child.Name` (根據金鑰值的使用方式) 。

### <a name="getting-values-using-key-value-coding"></a>使用索引鍵/值編碼來取得值

方法會傳回指定之索引 `ValueForKey` 鍵的值 (為 `NSString`) ，相對於接收要求的 KVC 類別實例。 例如，如果 `Person` 是以上所定義之類別的實例 `PersonModel` ：

```csharp
// Read value
var name = Person.ValueForKey (new NSString("Name"));
```

這會傳回 `Name` 該實例的屬性值 `PersonModel` 。

### <a name="setting-values-using-key-value-coding"></a>使用索引鍵/值編碼設定值

同樣地，將指定之索引 `SetValueForKey` 鍵的值設 (為 `NSString`) ，相對於接收要求的 KVC 類別實例。 同樣地，使用類別的實例 `PersonModel` ，如下所示：

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

會將屬性的值變更 `Name` 為 `Jane Doe` 。

<a name="Observing_Value_Changes"></a>

### <a name="observing-value-changes"></a>觀察值變更

您可以使用索引鍵-值觀察 (KVO) ，將觀察者附加至符合 KVC 規範之類別的特定索引鍵，並在該索引鍵的值修改時收到通知 (使用 KVC 技術或直接存取 c # 程式碼) 中的指定屬性。 例如：

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

現在，每當 `Name` 類別實例的屬性遭到 `Person` `PersonModel` 修改時，就會將新的值寫出到主控台。

如需詳細資訊，請參閱 Apple 的 [Key-Value 觀察程式設計指南簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)。

## <a name="data-binding"></a>資料繫結

下列各節將說明如何使用索引鍵/值編碼和索引鍵值觀察符合規範的類別，將資料系結至 Xcode Interface Builder 中的 UI 元素，而不是使用 c # 程式碼來讀取和寫入值。 如此一來，您就可以將 _資料模型_ 與用來顯示它們的視圖分開，讓 Xamarin. Mac 應用程式更具彈性且更容易維護。 您也會大幅減少必須撰寫的程式碼數量。

<a name="Defining_your_Data_Model"></a>

### <a name="defining-your-data-model"></a>定義您的資料模型

在您可以將 Interface Builder 中的 UI 元素系結之前，您的 Xamarin 應用程式中必須定義符合 KVC/KVO 規範的類別，才能作為系結的 _資料模型_ 。 資料模型會提供將顯示在消費者介面中的所有資料，並在執行應用程式時，接收使用者在 UI 中所做資料的任何修改。

例如，如果您撰寫的應用程式會管理一組員工，您可以使用下列類別來定義資料模型：

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
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
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

此類別的大部分功能都涵蓋于上述的「索引 [鍵-值編碼](#What_is_Key-Value_Coding) 」一節中。 不過，讓我們看看幾個特定的元素，以及一些為了允許此類別做為 **陣列控制器** 和 **樹狀結構** 的資料模型所做的新增 (，我們稍後將使用這些專案來進行資料系結 **樹狀檢視**、 **大綱視圖** 和 **集合查看**) 。

首先，因為員工可能是經理，所以我們使用了 (， `NSArray` `NSMutableArray` 因此您可以修改值) 以允許他們所管理的員工附加至這些值：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

這裡有兩個要注意的事項：

1. 我們使用了 `NSMutableArray` 而非標準 c # 陣列或集合，因為這是資料系結至 AppKit 控制項（例如 **資料表視圖**、 **大綱視圖** 和 **集合**）的必要條件。
2. 我們公開了員工的陣列，方法是將它轉換成以進行資料系結， `NSArray` 並將其 c # 格式的名稱變更為資料系結所預期的名稱， `People` `personModelArray` 格式為 **{class_name} 陣列** (請注意，第一個字元的大小寫) 較低。

接下來，我們需要新增一些特殊名稱的公用方法，以支援 **陣列控制器** 和 **樹狀結構控制器**：

```csharp
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
```

這些控制器可讓控制器要求和修改其顯示的資料。 就像上面公開的一樣 `NSArray` ，這些都有一個非常明確的命名慣例 (，不同于一般的 c # 命名慣例) ：

- `addObject:` -將物件加入至陣列。
- `insertObject:in{class_name}ArrayAtIndex:` -其中 `{class_name}` 是您的類別名稱。 這個方法會將物件插入陣列中的指定索引處。
- `removeObjectFrom{class_name}ArrayAtIndex:` -其中 `{class_name}` 是您的類別名稱。 這個方法會移除陣列中指定索引處的物件。
- `set{class_name}Array:` -其中 `{class_name}` 是您的類別名稱。 這個方法可讓您將現有的包含取代為新的。

在這些方法中，我們已將中的陣列變更換行， `WillChangeValue` 以及 `DidChangeValue` KVO 合規性的訊息。

最後，由於屬性相依于 `Icon` 屬性的值，因此對 `isManager` 屬性的變更 `isManager` 可能不會反映在 `Icon` KVO) 期間 (資料系結 UI 元素中：

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
```

為了修正這個錯誤，我們使用下列程式碼：

```csharp
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
```

請注意，除了它自己的金鑰之外， `isManager` 存取子也會傳送 `WillChangeValue` 金鑰的和 `DidChangeValue` 訊息， `Icon` 讓它也會看到變更。

我們將在 `PersonModel` 本文的其餘部分使用資料模型。

<a name="Simple_Data_Binding"></a>

### <a name="simple-data-binding"></a>簡單資料繫結

定義我們的資料模型之後，讓我們看看 Xcode 的 Interface Builder 中的簡單資料系結範例。 例如，讓我們將表單新增至我們的 Xamarin 應用程式，可用來編輯 `PersonModel` 上述定義。 我們會新增幾個文字欄位，以及一個核取方塊來顯示和編輯模型的屬性。

首先，讓我們將新的 **視圖控制器** 新增至 Interface Builder 中的 **主要** 分鏡腳本檔案，並命名其類別 `SimpleViewController` ：

[![加入具有名為 SimpleViewController 之類別的新 view 控制器。](databinding-images/simple01.png "新增視圖控制器")](databinding-images/simple01-large.png#lightbox)

接下來，返回 Visual Studio for Mac，編輯已自動新增至專案) 的 **SimpleViewController.cs** 檔案 (，並公開 `PersonModel` 我們的表單將資料系結至的實例。 新增下列程式碼：

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

接下來，當您載入此視圖時，我們會建立的實例， `PersonModel` 並在其中填入此程式碼：

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

現在我們需要建立表單，按兩下 **主要** 的分鏡腳本檔案將它開啟，以便在 Interface Builder 中進行編輯。 將表單版面配置如下所示：

[![編輯 Xcode 中的分鏡腳本](databinding-images/simple02.png "編輯 Xcode 中的分鏡腳本")](databinding-images/simple02-large.png#lightbox)

若要將表單系結至 `PersonModel` 我們透過金鑰公開的， `Person` 請執行下列動作：

1. 選取 [ **員工名稱** ] 文字欄位，然後切換至 [系結] 偵測 **器**。
2. 勾選 [系結 **至** ] 方塊，然後從下拉式清單中選取 [ **簡單視圖控制器** ]。 接下來輸入 `self.Person.Name` **金鑰路徑**：

    [![輸入本身的點的名稱作為金鑰路徑。](databinding-images/simple03.png "輸入機碼路徑")](databinding-images/simple03-large.png#lightbox)
3. 選取 [ **職業** ] 文字欄位並勾選 [系結 **至** ] 方塊，然後從下拉式清單中選取 [ **簡單視圖控制器** ]。 接下來輸入 `self.Person.Occupation` **金鑰路徑**：

    [![為金鑰路徑輸入自我點的人為職業。](databinding-images/simple04.png "輸入機碼路徑")](databinding-images/simple04-large.png#lightbox)
4. 選取 [ **員工是管理員** ] 核取方塊，並核取 [系結 **至** ] 方塊，然後從下拉式清單中選取 [ **簡單視圖控制器** ]。 接下來輸入 `self.Person.isManager` **金鑰路徑**：

    [![輸入 Ismanager.exe 作為索引鍵路徑的點。](databinding-images/simple05.png "輸入機碼路徑")](databinding-images/simple05-large.png#lightbox)
5. 選取 [ **受管理的員工數目** ] 欄位，並勾選 [系結 **至** ] 方塊，然後從下拉式清單中選取 [ **簡單視圖控制器** ]。 接下來輸入 `self.Person.NumberOfEmployees` **金鑰路徑**：

    [![輸入 NumberOfEmployees 作為索引鍵路徑的點。](databinding-images/simple06.png "輸入機碼路徑")](databinding-images/simple06-large.png#lightbox)
6. 如果員工不是經理，我們想要隱藏員工管理的標籤和文字欄位數目。
7. 選取 [ **員工管理** 的標籤數目]，展開 **隱藏** 的 turndown 並核取 [系結 **至** ] 方塊，然後從下拉式清單中選取 [ **簡單視圖控制器** ]。 接下來輸入 `self.Person.isManager` **金鑰路徑**：

    [![針對非管理員的金鑰路徑輸入 Ismanager.exe 的點。](databinding-images/simple07.png "輸入機碼路徑")](databinding-images/simple07-large.png#lightbox)
8. `NSNegateBoolean`從 [**值] 轉換器** 下拉式清單中選取：

    ![選取 NSNegateBoolean 索引鍵轉換](databinding-images/simple08.png "選取 NSNegateBoolean 索引鍵轉換")
9. 這會告知資料系結，如果屬性的值為，則會隱藏標籤 `isManager` `false` 。
10. 針對 [ **員工管理** 的文字數目] 欄位重複步驟7和8。
11. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

如果您執行應用程式，屬性中的值 `Person` 會自動填入我們的表單：

[![顯示自動填入的表單](databinding-images/simple09.png "顯示自動填入的表單")](databinding-images/simple09-large.png#lightbox)

使用者對表單所做的任何變更，都會寫回 `Person` View Controller 中的屬性。 例如，取消標示 **員工是經理** 更新的 `Person` 實例 `PersonModel` ，而且會透過資料系結) 自動隱藏 **員工管理** 的標籤和文字欄位數目 (：

[![隱藏非管理員的員工數目](databinding-images/simple10.png "隱藏非管理員的員工數目")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding"></a>

### <a name="table-view-data-binding"></a>資料表視圖資料系結

既然我們已有資料系結的基本概念，讓我們看看更複雜的資料系結工作，方法是使用 _陣列控制器_ 和資料系結至資料表視圖。 如需有關使用資料表視圖的詳細資訊，請參閱我們的 [表格視圖](~/mac/user-interface/table-view.md) 檔。

首先，讓我們將新的 **視圖控制器** 新增至 Interface Builder 中的 **主要** 分鏡腳本檔案，並命名其類別 `TableViewController` ：

[![加入具有名為 TableViewController 之類別的新 view 控制器。](databinding-images/table01.png "新增視圖控制器")](databinding-images/table01-large.png#lightbox)

接下來，我們將編輯已自動新增至專案的 **TableViewController.cs** 檔 () 並公開陣列， (`NSArray` `PersonModel` 我們要將我們的表單系結至其中的類別) 。 新增下列程式碼：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
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
```

就像我們在 `PersonModel` [定義您的資料模型](#Defining_your_Data_Model) 一節中的類別一樣，我們已公開四個特殊命名的公用方法，讓陣列控制器和從的集合讀取和寫入資料 `PersonModels` 。

接下來，當您載入此視圖時，我們需要在陣列中填入下列程式碼：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

現在我們需要建立資料表視圖，按兩下 **主要** 的分鏡腳本檔案將它開啟，以便在 Interface Builder 中進行編輯。 將資料表版面配置如下所示：

[![配置新的資料表視圖](databinding-images/table02.png "配置新的資料表視圖")](databinding-images/table02-large.png#lightbox)

我們需要新增 **陣列控制器** 來提供系結資料到資料表，請執行下列動作：

1. 將 **陣列控制器** 從連結 **庫偵測器** 拖曳至 **介面編輯器**：

    ![從程式庫選取陣列控制器](databinding-images/table03.png "從程式庫選取陣列控制器")
2. 選取 **介面** 階層中的 [**陣列控制器**]，並切換至 [**屬性偵測器**]：

    [![選取屬性偵測器](databinding-images/table04.png "選取屬性偵測器")](databinding-images/table04-large.png#lightbox)
3. 輸入 `PersonModel` **類別名稱**，按一下 **加號** 按鈕，然後新增三個按鍵。 將它們 `Name` 命名 `Occupation` 為，然後 `isManager` ：

    ![將必要的索引鍵路徑加入至物件控制器。](databinding-images/table05.png "新增必要的金鑰路徑")
4. 這會告知陣列控制器管理陣列的內容，以及應該透過索引鍵) 公開 (的屬性。
5. 切換至 [系結] 偵測 **器**，並在 [**內容陣列**] 下選取 [系結 **至**] 和 [**資料表視圖** 輸入 **模型索引鍵路徑** `self.personModelArray` ：

    ![輸入金鑰路徑](databinding-images/table06.png "輸入金鑰路徑")
6. 這會將陣列控制器系結至 `PersonModels` 我們在 View Controller 上公開的陣列。

現在，我們需要將表格視圖系結至陣列控制器，請執行下列動作：

1. 選取資料表視圖和系結偵測 **器**：

    [![選取資料表視圖和系結偵測器。](databinding-images/table07.png "選取系結偵測器")](databinding-images/table07-large.png#lightbox)
2. 在 [ **資料表內容** ] turndown 下，選取 [系結 **至** ] 和 [ **陣列控制器**]。 輸入 `arrangedObjects` **控制器金鑰** 欄位：

    ![定義控制器金鑰](databinding-images/table08.png "定義控制器金鑰")
3. 選取 [**員工**] 資料行下的 [**資料表視圖] 儲存格**。 在 [系結] 偵測 **器** 的 **值** turndown 底下，選取 [系結 **至** ] 和 [ **資料表資料格視圖**]。 輸入 `objectValue.Name` 模型索引 **鍵路徑**：

    [![設定 Employee 資料行的模型索引鍵路徑。](databinding-images/table09.png "設定模型索引鍵路徑")](databinding-images/table09-large.png#lightbox)
4. `objectValue` 是陣列 `PersonModel` 控制器所管理之陣列中的目前。
5. 選取 [**職業**] 資料行下的 [**資料表視圖] 儲存格**。 在 [系結] 偵測 **器** 的 **值** turndown 底下，選取 [系結 **至** ] 和 [ **資料表資料格視圖**]。 輸入 `objectValue.Occupation` 模型索引 **鍵路徑**：

    [![設定 [職業] 資料行的模型索引鍵路徑。](databinding-images/table10.png "設定模型索引鍵路徑")](databinding-images/table10-large.png#lightbox)
6. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

如果執行應用程式，資料表將會填入我們的陣列 `PersonModels` ：

[![執行應用程式，該應用程式會填入 PersonModels 的陣列。](databinding-images/table11.png "執行應用程式")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding"></a>

### <a name="outline-view-data-binding"></a>大綱檢視資料系結

針對大綱視圖的資料系結非常類似于資料表視圖的系結。 主要差異在於，我們將使用 **樹狀控制器** 而非 **陣列控制器** 來提供系結資料給大綱視圖。 如需使用大綱視圖的詳細資訊，請參閱我們的 [大綱視圖](~/mac/user-interface/outline-view.md) 檔。

首先，讓我們將新的 **視圖控制器** 新增至 Interface Builder 中的 **主要** 分鏡腳本檔案，並命名其類別 `OutlineViewController` ：

[![加入具有名為 OutlineViewController 之類別的新 view 控制器。](databinding-images/outline01.png "新增視圖控制器")](databinding-images/outline01-large.png#lightbox)

接下來，我們將編輯已自動新增至專案的 **OutlineViewController.cs** 檔 () 並公開陣列， (`NSArray` `PersonModel` 我們要將我們的表單系結至其中的類別) 。 新增下列程式碼：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
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
```

就像我們在 `PersonModel` [定義您的資料模型](#Defining_your_Data_Model) 一節中的類別一樣，我們已公開四個特殊命名的公用方法，讓樹狀結構控制器和從的集合讀取和寫入資料 `PersonModels` 。

接下來，當您載入此視圖時，我們需要在陣列中填入下列程式碼：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

現在我們需要建立大綱視圖，按兩下 **主要** 的分鏡腳本檔案將其開啟，以在 Interface Builder 中進行編輯。 將資料表版面配置如下所示：

[![建立大綱視圖](databinding-images/outline02.png "建立大綱視圖")](databinding-images/outline02-large.png#lightbox)

我們需要新增 **樹狀結構控制器** 來提供系結資料給我們的大綱，請執行下列動作：

1. 將 **樹狀結構控制器** 從連結 **庫偵測器** 拖曳至 **介面編輯器**：

    ![從程式庫選取樹狀結構控制器](databinding-images/outline03.png "從程式庫選取樹狀結構控制器")
2. 選取 **介面** 階層中的樹狀結構 **控制器**，並切換至屬性偵測 **器**：

    [![選取屬性偵測器](databinding-images/outline04.png "選取屬性偵測器")](databinding-images/outline04-large.png#lightbox)
3. 輸入 `PersonModel` **類別名稱**，按一下 **加號** 按鈕，然後新增三個按鍵。 將它們 `Name` 命名 `Occupation` 為，然後 `isManager` ：

    ![新增 PersonModel 所需的金鑰路徑。](databinding-images/outline05.png "新增必要的金鑰路徑")
4. 這會告訴樹系控制器管理陣列的內容，以及應該透過索引鍵) 公開 (的屬性。
5. 在 [ **樹狀控制器** ] 區段下，輸入 [ `personModelArray` **子** 系]， `NumberOfEmployees` 在 **計數** 下輸入，然後在 [分 `isEmployee` **葉**] 下輸入：

    ![設定樹狀控制器金鑰路徑](databinding-images/outline05.png "設定樹狀控制器金鑰路徑")
6. 這會告訴樹系控制器哪裡可以找到任何子節點、有多少個子節點，以及目前節點是否有子節點。
7. 切換至 [系結] 偵測 **器** ，並在 [ **內容陣列** ] 下，選取 [系結 **至** **] 和 [** 檔案 輸入 **模型索引鍵路徑** `self.personModelArray` ：

    ![編輯金鑰路徑](databinding-images/outline06.png "編輯金鑰路徑")
8. 這會將樹狀控制器系結至 `PersonModels` 我們在 View Controller 上公開的陣列。

現在，我們需要將大綱視圖系結至樹狀結構控制器，請執行下列動作：

1. 選取大綱視圖，然後在系結偵測 **器** 中選取：

    [![選取大綱視圖和系結偵測器。](databinding-images/outline07.png "選取系結偵測器")](databinding-images/outline07-large.png#lightbox)
2. 在 [ **大綱視圖內容** ] turndown 下，選取 [系結 **至** ] 和 [ **樹狀結構控制器**]。 輸入 `arrangedObjects` **控制器金鑰** 欄位：

    ![設定控制器金鑰](databinding-images/outline08.png "設定控制器金鑰")
3. 選取 [**員工**] 資料行下的 [**資料表視圖] 儲存格**。 在 [系結] 偵測 **器** 的 **值** turndown 底下，選取 [系結 **至** ] 和 [ **資料表資料格視圖**]。 輸入 `objectValue.Name` 模型索引 **鍵路徑**：

    [![輸入模型索引鍵路徑值 objectValue 點名稱。](databinding-images/outline09.png "輸入模型索引鍵路徑")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` 是 `PersonModel` 由樹狀結構控制器管理的陣列中的目前。
5. 選取 [**職業**] 資料行下的 [**資料表視圖] 儲存格**。 在 [系結] 偵測 **器** 的 **值** turndown 底下，選取 [系結 **至** ] 和 [ **資料表資料格視圖**]。 輸入 `objectValue.Occupation` 模型索引 **鍵路徑**：

    [![輸入模型索引鍵路徑值 objectValue 的點職業。](databinding-images/outline10.png "輸入模型索引鍵路徑")](databinding-images/outline10-large.png#lightbox)
6. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

如果執行應用程式，將會填入下列陣列的大綱 `PersonModels` ：

[![執行應用程式，它會填入 PersonModels 的陣列。](databinding-images/outline11.png "執行應用程式")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>集合視圖資料系結

使用收集視圖的資料系結非常類似于資料表視圖的系結，因為陣列控制器可用來提供集合的資料。 因為收集視圖沒有預設的顯示格式，所以需要更多工作來提供使用者互動意見反應，以及追蹤使用者選擇。

> [!IMPORTANT]
> 由於 Xcode 7 和 macOS 10.11 (和更高) 的問題，因此無法在分鏡腳本 ( 中使用收集視圖。分鏡腳本) 檔。 因此，您將需要繼續使用 xib 檔案來定義您的 Xamarin 應用程式的集合視圖。 如需詳細資訊，請參閱我們的 [集合視圖](~/mac/user-interface/collection-view.md) 檔。

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`:

![Add a View Controller](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
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
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![Create the CollectionView](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1. **Collection View Item** -  That manages a single instance of an item in the collection.
2. **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![Add controls](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![NSBox](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![Array Controller](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![Attribute inspector](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![Set the class and key names](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![Bindings inspector](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![Binding inspector](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![Contents](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![Selection indexes](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![Binding inspector values](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![Label value](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![Label value](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![NSBox value](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![Populated table](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>調試原生損毀

在您的資料系結中犯錯誤，可能會導致非受控程式碼發生 _原生損毀_ ，並導致您的 Xamarin. Mac 應用程式完全失敗，並出現 `SIGABRT` 錯誤：

[![原生損毀對話方塊範例](databinding-images/debug01.png "原生損毀對話方塊範例")](databinding-images/debug01-large.png#lightbox)

在資料系結期間，原生損毀通常有四個主要原因：

1. 您的資料模型不會繼承自 `NSObject` 或的子類別 `NSObject` 。
2. 您未使用屬性將屬性公開至目標 C `[Export("key-name")]` 。
3. 您未在和方法呼叫中將變更包裝到存取子的值 `WillChangeValue` ， `DidChangeValue` (指定與) 屬性相同的索引鍵 `Export` 。
4. 您在 Interface Builder 的系結偵測 **器** 中有錯誤或輸入錯誤的索引鍵。

### <a name="decoding-a-crash"></a>解碼損毀

讓我們在資料系結中造成原生損毀，讓我們可以示範如何找出並修正它。 在 Interface Builder 中，讓我們將集合視圖範例中第一個標籤的系結從變更 `Name` 為 `Title` ：

[![編輯系結索引鍵](databinding-images/debug02.png "編輯系結索引鍵")](databinding-images/debug02-large.png#lightbox)

讓我們儲存變更、切換回 Visual Studio for Mac 以與 Xcode 同步處理，並執行我們的應用程式。 當收集視圖顯示時，應用程式會短暫當機，並出現 `SIGABRT` 錯誤 (如 Visual Studio for Mac) 的 **應用程式輸出** 中所示，因為 `PersonModel` 並不會公開具有該索引鍵的屬性 `Title` ：

[![系結錯誤的範例](databinding-images/debug03.png "系結錯誤的範例")](databinding-images/debug03-large.png#lightbox)

如果我們在 **應用程式輸出** 中看到錯誤的最上方，我們可以看到解決此問題的關鍵：

[![在錯誤記錄檔中尋找問題](databinding-images/debug04.png "在錯誤記錄檔中尋找問題")](databinding-images/debug04-large.png#lightbox)

這一行告訴我們，索引鍵不存在於我們要系結 `Title` 的物件上。 如果我們將系結重新變更為 `Name` Interface Builder、儲存、同步、重建和執行，則應用程式會如預期般執行，而不會發生問題。

## <a name="summary"></a>[摘要]

本文詳細說明如何在 Xamarin 應用程式中使用資料系結和索引鍵/值編碼。 首先，它會探討如何使用索引鍵/值編碼 (KVC) 和索引鍵-值觀察 (KVO) ，將 c # 類別公開至目標 C。 接下來，它會示範如何使用符合 KVO 規範的類別，並將資料系結至 Xcode 的 Interface Builder 中的 UI 元素。 最後，它會使用 **陣列控制器** 和 **樹狀結構** 來顯示覆雜的資料系結。

## <a name="related-links"></a>相關連結

- [MacDatabinding 分鏡腳本 (範例) ](/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding Xib (範例) ](/samples/xamarin/mac-samples/macdatabinding-xibs)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [標準控制項](~/mac/user-interface/standard-controls.md)
- [資料表視圖](~/mac/user-interface/table-view.md)
- [大綱視圖](~/mac/user-interface/outline-view.md)
- [集合視圖](~/mac/user-interface/collection-view.md)
- [索引鍵/值編碼程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Key-Value 觀察程式設計指南簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Cocoa 系結程式設計主題簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Cocoa 系結參考簡介](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS Human Interface Guidelines (人性化介面指導方針)](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)