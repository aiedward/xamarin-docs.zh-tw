---
title: Xamarin. Mac 中的資料系結和索引鍵-值編碼
description: 本文涵蓋使用索引鍵/值編碼和索引鍵-值觀察，以允許資料系結至 Xcode 的 Interface Builder 中的 UI 元素。
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 81a1f63078a5f7a2a70f731d1790f85f4283d22f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304993"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Xamarin. Mac 中的資料系結和索引鍵-值編碼

_本文涵蓋使用索引鍵/值編碼和索引鍵-值觀察，以允許資料系結至 Xcode 的 Interface Builder 中的 UI 元素。_

## <a name="overview"></a>概觀

在 Xamarin. C# Mac 應用*程式中使用*和 .net 時，您可以存取開發人員在*Xcode*中運作的相同索引鍵值編碼和資料系結技術。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來與 UI 元素進行資料系結，而不是撰寫程式碼。

藉由在 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以進一步將您的支援資料（_資料模型_）與您的前端使用者介面（_模型視圖控制器_）分離，讓您更容易維護、更有彈性的應用程式設計。

[![執行中應用程式的範例](databinding-images/intro01.png "執行中應用程式的範例")](databinding-images/intro01-large.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在本文中使用的重要概念和技巧。

您可能想要看一下[Xamarin 內部](~/mac/internals/how-it-works.md)檔的 [ C# [公開C#類別/方法至目標-C](~/mac/internals/how-it-works.md) ] 區段，它會說明用來將類別連接至目標-c 物件和 UI 元素的 `Register` 和 `Export` 屬性。

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>什麼是索引鍵-值編碼

索引鍵/值編碼（KVC）是一種機制，可使用索引鍵（特殊格式的字串）來識別屬性，而不是透過執行個體變數或存取子方法（`get/set`）來存取物件的屬性。 藉由在您的 Xamarin 應用程式中執行符合索引鍵/值的存取子，您可以存取其他 macOS （先前稱為 OS X）功能，例如索引鍵/值觀察（KVO）、資料系結、核心資料、Cocoa 系結和 scriptability。

藉由在 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以進一步將您的支援資料（_資料模型_）與您的前端使用者介面（_模型視圖控制器_）分離，讓您更容易維護、更有彈性的應用程式設計。

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

首先，`[Register("PersonModel")]` 屬性會註冊類別，並將其公開至目標-C。 然後，類別必須繼承自 `NSObject` （或繼承自 `NSObject`的子類別），這會新增數個基底方法，讓類別 KVC 符合規範。 接下來，`[Export("Name")]` 屬性會公開 `Name` 屬性，並定義稍後用來透過 KVC 和 KVO 技術來存取屬性的金鑰值。

最後，若要能夠以索引鍵/值觀察屬性值的變更，存取子必須在 `WillChangeValue` 和 `DidChangeValue` 方法呼叫（指定與 `Export` 屬性相同的索引鍵）中，將變更的值換行。  例如：

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

此步驟對於 Xcode 的 Interface Builder 中的資料系結_非常_重要（如本文稍後所見）。

如需詳細資訊，請參閱 Apple 的索引[鍵-值編碼程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)。

### <a name="keys-and-key-paths"></a>金鑰和金鑰路徑

「索引_鍵_」是一個字串，可識別物件的特定屬性。 一般而言，索引鍵會對應至索引鍵/值編碼相容物件中的存取子方法名稱。 金鑰必須使用 ASCII 編碼，通常以小寫字母開頭，而且不能包含空白字元。 因此，假設上述範例，`Name` 會是 `PersonModel` 類別 `Name` 屬性的索引鍵值。 其公開之屬性的索引鍵和名稱不一定要相同，不過在大部分的情況下都是如此。

_金鑰路徑_是以點分隔的索引鍵字串，用來指定要進行遍歷的物件屬性階層。 序列中第一個索引鍵的屬性是相對於接收者，而後續的每個索引鍵都會根據前一個屬性的值進行評估。 以相同的方式，您可以使用點標記法來跨越C#類別中的物件和其屬性。

例如，如果您展開 `PersonModel` 類別，並加入 `Child` 屬性：

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

子系名稱的索引鍵路徑會 `self.Child.Name` 或單純 `Child.Name` （根據索引鍵值的使用方式而定）。

### <a name="getting-values-using-key-value-coding"></a>使用索引鍵-值編碼來取得值

`ValueForKey` 方法會傳回指定索引鍵的值（如 `NSString`），相對於接收要求之 KVC 類別的實例。 例如，如果 `Person` 是上面定義之 `PersonModel` 類別的實例：

```csharp
// Read value
var name = Person.ValueForKey (new NSString("Name"));
```

這會傳回 `PersonModel`實例的 `Name` 屬性值。

### <a name="setting-values-using-key-value-coding"></a>使用索引鍵-值編碼來設定值

同樣地，`SetValueForKey` 會將指定之索引鍵的值（如 `NSString`）設定為相對於接收要求之 KVC 類別的實例。 同樣地，使用 `PersonModel` 類別的實例，如下所示：

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

會將 `Name` 屬性的值變更為 `Jane Doe`。

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>觀察值變更

使用索引鍵/值觀察（KVO），您可以將觀察者附加至 KVC 相容類別的特定索引鍵，並在每次修改該索引鍵的值時收到通知（使用 KVC 技術或直接在程式碼C#中存取指定的屬性）。 例如：

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

現在，每當修改 `PersonModel` 類別之 `Person` 實例的 `Name` 屬性時，新的值就會寫出到主控台中。

如需詳細資訊，請參閱 Apple[簡介的索引鍵-值觀察程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)。

## <a name="data-binding"></a>資料繫結

下列各節將說明如何使用索引鍵/值編碼和索引鍵值觀察相容的類別，將資料系結至 Xcode 的 Interface Builder 中的 UI 元素，而不是使用C#程式碼來讀取和寫入值。 如此一來，您就可以將_資料模型_與用來顯示它們的視圖分開，讓 Xamarin. Mac 應用程式更具彈性且更容易維護。 您也可以大幅減少必須撰寫的程式碼數量。

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>定義您的資料模型

您必須先在 Xamarin. Mac 應用程式中定義 KVC/KVO 相容的類別，以作為系結的_資料模型_，才能在 Interface Builder 中系結 UI 元素。 資料模型會提供將顯示在使用者介面中的所有資料，並接收使用者在執行應用程式時，在 UI 中對資料所做的任何修改。

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

此類別的大部分功能都涵蓋在上述[什麼是索引鍵/值編碼](#What_is_Key-Value_Coding)一節中。 不過，讓我們來看幾個特定的元素，還有一些新增功能，讓這個類別做為**陣列控制器**和**樹狀結構**的資料模型（我們稍後會使用這些專案來進行資料系結**樹狀檢視**、**大綱視圖**和**集合視圖**）。

首先，因為員工可能是經理，所以我們使用了 `NSArray` （特別是 `NSMutableArray`，因此可以修改值），讓他們所管理的員工得以附加至他們：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

這裡有兩個要注意的事項：

1. 我們使用了 `NSMutableArray`，而不是C#標準的陣列或集合，因為這是資料系結至 AppKit 控制項（例如**資料表視圖**、**大綱視圖**和**集合**）的必要條件。
2. 我們會將員工的陣列轉換成 `NSArray` 以進行資料系結，並將其C#格式化的名稱（`People`）變更為資料系結所預期的格式，`personModelArray` 格式為 **{class_name} 陣列**（請注意，第一個字元的大小寫是小寫）。

接下來，我們需要新增一些特殊名稱的公用方法，以支援**陣列控制器**和**樹狀目錄控制器**：

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

這些控制器可以要求和修改所顯示的資料。 就像上面公開的 `NSArray`，這些都有非常明確的命名慣例（與一般C#的命名慣例不同）：

- `addObject:`-將物件加入至陣列。
- `insertObject:in{class_name}ArrayAtIndex:`-其中 `{class_name}` 是您的類別名稱。 這個方法會將物件插入陣列中指定的索引處。
- `removeObjectFrom{class_name}ArrayAtIndex:`-其中 `{class_name}` 是您的類別名稱。 這個方法會移除陣列中指定索引處的物件。
- `set{class_name}Array:`-其中 `{class_name}` 是您的類別名稱。 這個方法可讓您以新的執行取代現有的。

在這些方法中，我們已在 `WillChangeValue` 中包裝陣列的變更，並 `DidChangeValue` KVO 合規性的訊息。

最後，由於 `Icon` 屬性會依賴 `isManager` 屬性的值，因此 `isManager` 屬性的變更可能不會反映在資料系結 UI 元素的 `Icon` 中（KVO 期間）：

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

為了更正此錯誤，我們使用下列程式碼：

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

請注意，除了自己的金鑰外，`isManager` 存取子也會傳送 `Icon` 金鑰的 `WillChangeValue` 和 `DidChangeValue` 訊息，讓它也會看到變更。

在本文的其餘部分，我們將使用 `PersonModel` 資料模型。

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>簡單資料繫結

定義資料模型之後，讓我們看一下 Xcode 的 Interface Builder 中的簡單資料系結範例。 例如，讓我們在 Xamarin. Mac 應用程式中新增一個表單，用來編輯上面定義的 `PersonModel`。 我們會加入幾個文字欄位和一個核取方塊，以顯示和編輯模型的屬性。

首先，讓我們將新的**View Controller**新增至 Interface Builder 中的**主要**分鏡腳本檔案，並將其類別命名 `SimpleViewController`：

[![加入新的視圖控制器](databinding-images/simple01.png "加入新的視圖控制器")](databinding-images/simple01-large.png#lightbox)

接下來，返回 Visual Studio for Mac，編輯**SimpleViewController.cs**檔案（已自動新增至我們的專案），並公開要將我們的表單資料系結的 `PersonModel` 實例。 新增下列程式碼：

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

接下來，當您載入此視圖時，讓我們建立 `PersonModel` 的實例，並在其中填入此程式碼：

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

現在，我們需要建立表單，按兩下**主要**的分鏡腳本檔案，將它開啟，以便在 Interface Builder 中進行編輯。 將表單版面配置，以查看如下所示的內容：

[![在 Xcode 中編輯分鏡腳本](databinding-images/simple02.png "在 Xcode 中編輯分鏡腳本")](databinding-images/simple02-large.png#lightbox)

若要將表單的資料系結至我們透過 `Person` 索引鍵公開的 `PersonModel`，請執行下列動作：

1. 選取 [**員工名稱**] 文字欄位，並切換至 [系結] 偵測**器**。
2. 勾選 [系結**至**] 方塊，然後從下拉式清單中選取 [**簡單視圖控制器**]。 接下來，針對**金鑰路徑**輸入 `self.Person.Name`：

    [![輸入金鑰路徑](databinding-images/simple03.png "輸入金鑰路徑")](databinding-images/simple03-large.png#lightbox)
3. 選取 [**職業**] 文字欄位並核取 [系結**至**] 方塊，然後從下拉式清單中選取 [**簡單視圖控制器**]。 接下來，針對**金鑰路徑**輸入 `self.Person.Occupation`：

    [![輸入金鑰路徑](databinding-images/simple04.png "輸入金鑰路徑")](databinding-images/simple04-large.png#lightbox)
4. 選取 [**員工是經理**] 核取方塊，然後勾選 [系結**至**] 方塊，並從下拉式清單中選取 [**簡單視圖控制器**]。 接下來，針對**金鑰路徑**輸入 `self.Person.isManager`：

    [![輸入金鑰路徑](databinding-images/simple05.png "輸入金鑰路徑")](databinding-images/simple05-large.png#lightbox)
5. 選取 [**受管理的員工數目**] 文字欄位，然後勾選 [系結**至**] 方塊，並從下拉式清單中選取 [**簡單視圖控制器**]。 接下來，針對**金鑰路徑**輸入 `self.Person.NumberOfEmployees`：

    [![輸入金鑰路徑](databinding-images/simple06.png "輸入金鑰路徑")](databinding-images/simple06-large.png#lightbox)
6. 如果員工不是經理，我們想要隱藏 [員工管理的標籤] 和 [文字] 欄位的數目。
7. 選取 [**員工管理**的標籤數目]，展開**隱藏**的 turndown，然後勾選 [系結**至**] 方塊，並從下拉式清單中選取 [**簡單視圖控制器**]。 接下來，針對**金鑰路徑**輸入 `self.Person.isManager`：

    [![輸入金鑰路徑](databinding-images/simple07.png "輸入金鑰路徑")](databinding-images/simple07-large.png#lightbox)
8. 從 [值]**轉換器**下拉式清單中選取 [`NSNegateBoolean`]：

    ![選取 NSNegateBoolean 索引鍵轉換](databinding-images/simple08.png "選取 NSNegateBoolean 索引鍵轉換")
9. 這會告訴資料系結，如果 `isManager` 屬性的值為 `false`，標籤將會隱藏。
10. 針對 [**員工管理**的欄位數] 文字方塊，重複步驟7和8。
11. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

如果您執行應用程式，`Person` 屬性的值將會自動填入我們的表單：

[![顯示自動填入表單](databinding-images/simple09.png "顯示自動填入表單")](databinding-images/simple09-large.png#lightbox)

使用者對表單進行的任何變更都會寫回視圖控制器中的 `Person` 屬性。 例如，取消取消 [**員工] 就會**更新 `PersonModel` 的 `Person` 實例，而且會自動隱藏 [**員工管理**的標籤] 和 [文字] 欄位的數目（透過資料系結）：

[![隱藏非管理員的員工人數](databinding-images/simple10.png "隱藏非管理員的員工人數")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>資料表視圖資料系結

既然我們已經有資料系結的基本概念，讓我們使用_陣列控制器_和資料表視圖的資料系結，來查看更複雜的資料系結工作。 如需使用資料表視圖的詳細資訊，請參閱我們的[資料表視圖](~/mac/user-interface/table-view.md)檔。

首先，讓我們將新的**View Controller**新增至 Interface Builder 中的**主要**分鏡腳本檔案，並將其類別命名 `TableViewController`：

[![加入新的視圖控制器](databinding-images/table01.png "加入新的視圖控制器")](databinding-images/table01-large.png#lightbox)

接下來，我們要編輯**TableViewController.cs**檔案（該檔案已自動新增至我們的專案中），並公開 `PersonModel` 類別的陣列（`NSArray`），以將我們的表單系結至其中。 新增下列程式碼：

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

就像我們在[定義您的資料模型](#Defining_your_Data_Model)一節中的 `PersonModel` 類別所做的一樣，我們已公開四個特別命名的公用方法，讓陣列控制器和從我們的 `PersonModels`集合讀取和寫入資料。

接下來，當您載入此視圖時，我們必須使用下列程式碼填入陣列：

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

現在，我們需要建立資料表視圖，然後按兩下**主要**的分鏡腳本檔案，將它開啟，以便在 Interface Builder 中進行編輯。 配置資料表以查看如下所示的內容：

[![配置新的資料表視圖](databinding-images/table02.png "配置新的資料表視圖")](databinding-images/table02-large.png#lightbox)

我們需要新增**陣列控制器**來提供系結資料給我們的資料表，請執行下列動作：

1. 將**陣列控制器**從連結**庫偵測器**拖曳至**介面編輯器**：

    ![從程式庫選取陣列控制器](databinding-images/table03.png "從程式庫選取陣列控制器")
2. 在**介面**階層中選取 [**陣列控制器**]，並切換至**屬性偵測器**：

    [![選取屬性偵測器](databinding-images/table04.png "選取屬性偵測器")](databinding-images/table04-large.png#lightbox)
3. 針對 [**類別名稱**] 輸入 `PersonModel`，按一下**加號**按鈕並新增三個索引鍵。 將它們命名為 `Name`、`Occupation` 和 `isManager`：

    ![新增必要的機碼路徑](databinding-images/table05.png "新增必要的機碼路徑")
4. 這會告訴陣列控制器它管理的陣列，以及應該公開的屬性（透過金鑰）。
5. 切換至 系結 偵測**器**，然後在 **內容陣列** **底下選取** 系結 和 **資料表視圖控制器** 輸入 `self.personModelArray`的**模型索引鍵路徑**：

    ![輸入金鑰路徑](databinding-images/table06.png "輸入金鑰路徑")
6. 這會將陣列控制器與我們在視圖控制器上公開的 `PersonModels` 陣列結合。

現在，我們需要將我們的資料表視圖系結到陣列控制器，請執行下列動作：

1. 選取 [資料表] 視圖和 [系結偵測**器**]：

    [![選取系結偵測器](databinding-images/table07.png "選取系結偵測器")](databinding-images/table07-large.png#lightbox)
2. 在 [**資料表內容**] turndown 下，選取 [系結**至**] 和 [**陣列控制器**]。 針對 [**控制器金鑰**] 欄位輸入 `arrangedObjects`：

    ![定義控制器金鑰](databinding-images/table08.png "定義控制器金鑰")
3. 選取 [ **Employee** ] 資料行底下的 [**資料表視圖**] 資料格。 在 turndown**值**的 [系結] 偵測**器**中，選取 [系結**至**] 和 [**資料表資料格視圖**]。 輸入**模型索引鍵路徑**的 `objectValue.Name`：

    [![設定模型索引鍵路徑](databinding-images/table09.png "設定模型索引鍵路徑")](databinding-images/table09-large.png#lightbox)
4. `objectValue` 是陣列控制器所管理之陣列中的目前 `PersonModel`。
5. 選取 [**職業**] 資料行底下的 [**資料表視圖**] 資料格。 在 turndown**值**的 [系結] 偵測**器**中，選取 [系結**至**] 和 [**資料表資料格視圖**]。 輸入**模型索引鍵路徑**的 `objectValue.Occupation`：

    [![設定模型索引鍵路徑](databinding-images/table10.png "設定模型索引鍵路徑")](databinding-images/table10-large.png#lightbox)
6. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

如果我們執行應用程式，資料表將會填入 `PersonModels`的陣列：

[![執行應用程式](databinding-images/table11.png "執行應用程式")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>大綱檢視資料系結

針對大綱視圖的資料系結非常類似于針對資料表視圖進行系結。 主要的差別在於，我們將使用**樹狀控制器**（而不是**陣列控制器**），將系結的資料提供給大綱視圖。 如需使用大綱視圖的詳細資訊，請參閱我們的[大綱視圖](~/mac/user-interface/outline-view.md)檔。

首先，讓我們將新的**View Controller**新增至 Interface Builder 中的**主要**分鏡腳本檔案，並將其類別命名 `OutlineViewController`：

[![加入新的視圖控制器](databinding-images/outline01.png "加入新的視圖控制器")](databinding-images/outline01-large.png#lightbox)

接下來，我們要編輯**OutlineViewController.cs**檔案（該檔案已自動新增至我們的專案中），並公開 `PersonModel` 類別的陣列（`NSArray`），以將我們的表單系結至其中。 新增下列程式碼：

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

就像我們在[定義您的資料模型](#Defining_your_Data_Model)一節中的 `PersonModel` 類別所做的一樣，我們已公開四個特別命名的公用方法，讓樹系控制器和從我們的 `PersonModels`集合讀取和寫入資料。

接下來，當您載入此視圖時，我們必須使用下列程式碼填入陣列：

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

現在，我們需要建立大綱視圖，按兩下**主要**的分鏡腳本檔案，在 Interface Builder 中開啟以進行編輯。 配置資料表以查看如下所示的內容：

[![建立大綱視圖](databinding-images/outline02.png "建立大綱視圖")](databinding-images/outline02-large.png#lightbox)

我們需要新增**樹狀控制器**來提供系結資料給我們的大綱，請執行下列動作：

1. 將**樹狀控制器**從連結**庫偵測器**拖曳至**介面編輯器**：

    ![從程式庫選取樹狀結構控制器](databinding-images/outline03.png "從程式庫選取樹狀結構控制器")
2. 選取**介面**階層中的 [**樹狀結構控制器**]，並切換至 [屬性偵測**器**]：

    [![選取屬性偵測器](databinding-images/outline04.png "選取屬性偵測器")](databinding-images/outline04-large.png#lightbox)
3. 針對 [**類別名稱**] 輸入 `PersonModel`，按一下**加號**按鈕並新增三個索引鍵。 將它們命名為 `Name`、`Occupation` 和 `isManager`：

    ![新增必要的機碼路徑](databinding-images/outline05.png "新增必要的機碼路徑")
4. 這會告訴樹狀結構控制器它管理的陣列，以及應該公開的屬性（透過金鑰）。
5. 在 [**樹狀結構控制器**] 區段下，輸入 `personModelArray` 的 [**子**系]，然後在 [**計數**] 底下輸入 `NumberOfEmployees`，`isEmployee` 然後在 [分**葉**]

    ![設定樹狀控制器金鑰路徑](databinding-images/outline05.png "設定樹狀控制器金鑰路徑")
6. 這會告訴樹狀結構控制器要在何處尋找任何子節點、有多少個子節點，以及目前節點是否有子節點。
7. 切換至 系結 偵測**器**，然後在 **內容陣列** 下選取 系結**至** 和 檔案**擁有** 輸入 `self.personModelArray`的**模型索引鍵路徑**：

    ![編輯索引鍵路徑](databinding-images/outline06.png "編輯索引鍵路徑")
8. 這會將樹狀控制器系結至我們在視圖控制器上公開的 `PersonModels` 陣列。

現在，我們需要將大綱視圖系結到樹狀結構控制器，請執行下列動作：

1. 選取 [大綱] 視圖，然後在系結偵測**器**中選取：

    [![選取系結偵測器](databinding-images/outline07.png "選取系結偵測器")](databinding-images/outline07-large.png#lightbox)
2. 在 [**大綱視圖內容**] turndown 下，選取 [系結**至**] 和 [**樹狀結構控制器**]。 針對 [**控制器金鑰**] 欄位輸入 `arrangedObjects`：

    ![設定控制器金鑰](databinding-images/outline08.png "設定控制器金鑰")
3. 選取 [ **Employee** ] 資料行底下的 [**資料表視圖**] 資料格。 在 turndown**值**的 [系結] 偵測**器**中，選取 [系結**至**] 和 [**資料表資料格視圖**]。 輸入**模型索引鍵路徑**的 `objectValue.Name`：

    [![輸入模型索引鍵路徑](databinding-images/outline09.png "輸入模型索引鍵路徑")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` 是由樹系控制器所管理之陣列中的目前 `PersonModel`。
5. 選取 [**職業**] 資料行底下的 [**資料表視圖**] 資料格。 在 turndown**值**的 [系結] 偵測**器**中，選取 [系結**至**] 和 [**資料表資料格視圖**]。 輸入**模型索引鍵路徑**的 `objectValue.Occupation`：

    [![輸入模型索引鍵路徑](databinding-images/outline10.png "輸入模型索引鍵路徑")](databinding-images/outline10-large.png#lightbox)
6. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

如果我們執行應用程式，大綱將會填入我們的 `PersonModels`陣列：

[![執行應用程式](databinding-images/outline11.png "執行應用程式")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>集合視圖資料系結

具有集合視圖的資料系結非常類似于資料表視圖的系結，因為陣列控制器是用來提供集合的資料。 由於集合視圖沒有預設的顯示格式，因此需要更多工具來提供使用者互動意見反應，並追蹤使用者選取專案。

> [!IMPORTANT]
> 由於 Xcode 7 和 macOS 10.11 （和更新版本）中的問題，集合視圖無法在分鏡腳本（. 腳本）檔案內使用。 因此，您將需要繼續使用 xib 檔案來定義您的 Xamarin. Mac 應用程式的集合視圖。 如需詳細資訊，請參閱我們的[集合視圖](~/mac/user-interface/collection-view.md)檔。

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`:

![](databinding-images/collection01.png)

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

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1. **Collection View Item** -  That manages a single instance of an item in the collection.
2. **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>原生損毀的調試

在您的資料系結中犯錯誤可能會導致非受控碼發生_原生損毀_，並導致您的 Xamarin. Mac 應用程式完全失敗，並出現 `SIGABRT` 錯誤：

[![原生損毀對話方塊的範例](databinding-images/debug01.png "原生損毀對話方塊的範例")](databinding-images/debug01-large.png#lightbox)

在資料系結期間，原生損毀通常有四個主要原因：

1. 您的資料模型不會繼承自 `NSObject` 或 `NSObject`的子類別。
2. 您未使用 `[Export("key-name")]` 屬性將屬性公開給目標-C。
3. 您未在 `WillChangeValue` 和 `DidChangeValue` 方法呼叫中，將變更包裝到存取子的值（指定與 `Export` 屬性相同的索引鍵）。
4. Interface Builder 的系結偵測**器**中有錯誤或輸入的索引鍵。

### <a name="decoding-a-crash"></a>解碼損毀

讓我們在資料系結中造成原生損毀，讓我們可以示範如何找出並修正問題。 在 Interface Builder 中，讓我們將集合視圖範例中第一個標籤的系結從 `Name` 變更為 `Title`：

[![編輯系結機碼](databinding-images/debug02.png "編輯系結機碼")](databinding-images/debug02-large.png#lightbox)

讓我們儲存變更，切換回 Visual Studio for Mac 以與 Xcode 同步，然後執行我們的應用程式。 當 [集合] 視圖顯示時，應用程式會暫時當機，並出現 `SIGABRT` 錯誤（如 Visual Studio for Mac 中的**應用程式輸出**中所示），因為 `PersonModel` 不會使用索引鍵 `Title`來公開屬性：

[![系結錯誤的範例](databinding-images/debug03.png "系結錯誤的範例")](databinding-images/debug03-large.png#lightbox)

如果我們在**應用程式輸出**中滾動到錯誤的最上方，我們可以看到解決此問題的關鍵：

[![在錯誤記錄檔中找出問題](databinding-images/debug04.png "在錯誤記錄檔中找出問題")](databinding-images/debug04-large.png#lightbox)

這一行告訴我們，索引鍵 `Title` 不存在於所系結的物件上。 如果我們將系結回到 Interface Builder 中的 `Name`、儲存、同步處理、重建和執行，應用程式會如預期般執行，而不會發生問題。

## <a name="summary"></a>摘要

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用資料系結和索引鍵-值編碼。 首先，它探討了C#如何使用索引鍵/值編碼（KVC）和索引鍵-值觀察（KVO），將類別公開至目標-C。 接下來，它會示範如何使用 KVO 相容的類別，並將資料系結至 Xcode 的 Interface Builder 中的 UI 元素。 最後，它會使用**陣列控制器**和**樹狀結構**來顯示覆雜的資料系結。

## <a name="related-links"></a>相關連結

- [MacDatabinding 分鏡腳本（範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding Xib （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-xibs)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [標準控制項](~/mac/user-interface/standard-controls.md)
- [資料表視圖](~/mac/user-interface/table-view.md)
- [大綱視圖](~/mac/user-interface/outline-view.md)
- [集合視圖](~/mac/user-interface/collection-view.md)
- [索引鍵/值編碼程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [索引鍵/值簡介觀察程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Cocoa 系結程式設計主題簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Cocoa 系結參考簡介](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
