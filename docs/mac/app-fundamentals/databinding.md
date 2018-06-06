---
title: 資料繫結和 Xamarin.Mac 撰寫程式碼索引鍵-值
description: 本文說明如何使用索引鍵-值編碼並觀察以便進行資料繫結至 UI 項目在 Xcode 的介面產生器中的索引鍵-值。
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 88567e47f488a94fcf7334584a678c9689b83306
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792134"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>資料繫結和 Xamarin.Mac 撰寫程式碼索引鍵-值

_本文說明如何使用索引鍵-值編碼並觀察以便進行資料繫結至 UI 項目在 Xcode 的介面產生器中的索引鍵-值。_

## <a name="overview"></a>總覽

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取的相同索引鍵-值撰寫程式碼和資料繫結技巧，在工作開發人員*Objective C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_資料繫結 UI 項目，而不是撰寫程式碼。

使用索引鍵-值撰寫程式碼和資料繫結 Xamarin.Mac 應用程式中的技術，您可以大幅減少您必須撰寫和維護以填入和 UI 項目所使用的程式碼數量。 您也可以進一步減少您的備份資料的優點 (_資料模型_) 從您的前端結束使用者介面 (_模型-檢視-控制器_)，而導致更輕鬆地維護，更有彈性的應用程式設計。

[![執行中應用程式的範例](databinding-images/intro01.png "執行的應用程式的範例")](databinding-images/intro01-large.png#lightbox)

在本文中，我們會使用索引鍵-值程式碼撰寫和 Xamarin.Mac 應用程式中的資料繫結的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>什麼是索引鍵-值撰寫程式碼

索引鍵-值撰寫程式碼 (KVC) 是用來間接存取物件的屬性、 使用以識別屬性，而非透過執行個體變數存取它們的索引鍵 （特殊格式化的字串） 或存取子方法的機制 (`get/set`)。 實作索引鍵-值編碼相容的存取子 Xamarin.Mac 應用程式中，您就可以存取其他 macOS （之前稱為 OS X） 功能，例如索引鍵-值觀察 (KVO)、 資料繫結、 核心資料、 Cocoa 繫結和 scriptabletype。

使用索引鍵-值撰寫程式碼和資料繫結 Xamarin.Mac 應用程式中的技術，您可以大幅減少您必須撰寫和維護以填入和 UI 項目所使用的程式碼數量。 您也可以進一步減少您的備份資料的優點 (_資料模型_) 從您的前端結束使用者介面 (_模型-檢視-控制器_)，而導致更輕鬆地維護，更有彈性的應用程式設計。 

例如，讓我們看看下列類別定義的 KVC 相容的物件：

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

首先，`[Register("PersonModel")]`屬性註冊類別，並使它公開給目標。 然後，類別必須繼承自`NSObject`(或子類別繼承自`NSObject`)，這會將數個基底類別允許要 KVC 相容的方法。 下一步`[Export("Name")]`屬性會公開`Name`屬性，並定義稍後可用於透過 KVC 和 KVO 技術存取屬性的機碼值。 

最後，若要能夠在觀察到的索引鍵-值變更為屬性的值，存取子必須包裝其值的變更`WillChangeValue`和`DidChangeValue`方法呼叫 (指定為索引鍵的相同`Export`屬性)。  例如: 

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

這是步驟_非常_很重要的資料繫結在 Xcode 中的介面產生器，（因為我們將在本文稍後看到）。

如需詳細資訊，請參閱 Apple[索引鍵-值撰寫程式碼的程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)。

### <a name="keys-and-key-paths"></a>索引鍵和機碼路徑

A_金鑰_會識別物件的特定屬性的字串。 通常，索引鍵會對應到相容的物件撰寫程式碼索引鍵-值中的存取子方法的名稱。 索引鍵必須使用 ASCII 編碼方式，通常以小寫字母開頭，而且不能包含空白字元。 因此，假設上例，`Name`會是索引鍵的值為`Name`屬性`PersonModel`類別。 索引鍵和它們所公開的屬性名稱不一定要相同，但在大部分情況下在。

A_金鑰路徑_點的字串，並分隔用來指定要周遊的階層物件屬性的索引鍵。 序列中的第一個索引鍵的屬性是相對於接收端，而且每個後續的索引鍵會評估相對於前一個屬性的值。 相同的方式中，您可以使用點標記法來周遊物件和其屬性，在 C# 類別。

例如，如果您展開`PersonModel`類別，並加入`Child`屬性：

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

機碼路徑是子系的名稱就是`self.Child.Name`或只是`Child.Name`（根據如何使用索引鍵的值而定）。

### <a name="getting-values-using-key-value-coding"></a>取得值，使用索引鍵-值撰寫程式碼

`ValueForKey`方法會傳回指定之索引鍵的值 (做為`NSString`)、 相對接收要求 KVC 類別的執行個體。 例如，如果`Person`的執行個體`PersonModel`上述定義的類別：

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

這會傳回的值`Name`屬性，該執行個體`PersonModel`。 

### <a name="setting-values-using-key-value-coding"></a>使用索引鍵-值撰寫程式碼的設定值

同樣地，`SetValueForKey`設定指定之索引鍵的值 (做為`NSString`)、 相對接收要求 KVC 類別的執行個體。 因此同樣地，使用的執行個體`PersonModel`類別，如下所示：

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

將值變更`Name`屬性`Jane Doe`。

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>觀察值的變更

使用索引鍵-值觀察 (KVO)，您可以將觀察者附加至特定索引鍵的 KVC 標準的類別，且會通知 （使用 KVC 技術或直接存取 C# 程式碼中指定的屬性），該索引鍵的值修改任何時間。 例如: 

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

現在，任何時候`Name`屬性`Person`的執行個體`PersonModel`修改類別時，新值會寫出至主控台。 

如需詳細資訊，請參閱 Apple[索引鍵-值觀察程式設計指南簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)。

## <a name="data-binding"></a>資料繫結

下列各節將示範如何使用索引鍵-值撰寫程式碼，觀察標準的類別的索引鍵-值將資料繫結至 UI 項目在 Xcode 的介面產生器，而不是讀取和寫入使用 C# 程式碼的值。 如此一來您區隔您_資料模型_從用來顯示它們的檢視，可確保 Xamarin.Mac 應用程式更有彈性且容易維護。 您也可大幅減少必須寫入的程式碼數量。

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>定義資料模型

您可以將繫結資料的介面產生器中的 UI 項目之前，您必須擁有 KVC/KVO 標準的類別定義中做為應用程式 Xamarin.Mac_資料模型_繫結。 資料模型提供的所有資料將會顯示在使用者介面和接收資料，使用者可在 UI 中執行應用程式時的任何修改。

例如，如果您已撰寫的應用程式，受管理的員工的群組，您可以使用下列類別定義資料模型：

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

這個類別的功能幾乎已涵蓋[什麼是索引鍵-值編碼](#What_is_Key-Value_Coding)上一節。 不過，讓我們看看一些特定的項目和一些新功能允許這個類別，以做為資料模型所做的**陣列控制器**和**樹狀控制站**（其中我們將使用更新版本的資料繫結**樹狀檢視**，**大綱檢視**和**集合檢視**)。

首先，員工可能會是管理員，因為我們使用`NSArray`(特別是`NSMutableArray`讓可以修改的值) 以允許他們管理附加至它們的員工：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

這裡要注意兩件事：

1. 我們使用`NSMutableArray`而不是標準的 C# 陣列或集合，因為這是資料繫結至 AppKit 控制項的需求，例如**資料表檢視**，**大綱檢視**和**集合**.
2. 我們公開陣列之員工的轉型以`NSArray`資料繫結用途，並變更其 C# 格式名稱， `People`，資料繫結，需要的其中一個`personModelArray`形式 **{class_name} 陣列**（附註第一個字元未進行小寫）。

接下來，我們需要加入一些特殊名稱公用方法來支援**陣列控制器**和**樹狀控制站**:

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

這些行為允許的控制站來要求並修改它們所顯示的資料。 要公開`NSArray`以上所述，它們有非常特定的命名慣例 （這不同於一般 C# 命名慣例）：

- `addObject:` -將物件加入至陣列。
- `insertObject:in{class_name}ArrayAtIndex:` -其中`{class_name}`是您的類別名稱。 這個方法會將物件插入指定索引處的陣列。
- `removeObjectFrom{class_name}ArrayAtIndex:` -其中`{class_name}`是您的類別名稱。 這個方法以指定的索引陣列中移除的物件。
- `set{class_name}Array:` -其中`{class_name}`是您的類別名稱。 這個方法可讓您取代新現有攜帶。

在這些方法，我們已包裝的陣列中的變更`WillChangeValue`和`DidChangeValue`KVO 相容性的訊息。

最後，因為`Icon`屬性依賴值`isManager`屬性，變更為`isManager`屬性可能不會反映在`Icon`（期間 KVO) 的資料繫結 UI 項目：

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

若要修正的問題，我們會使用下列程式碼：

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

請注意，除了它自己的金鑰，`isManager`存取子會同時傳送`WillChangeValue`和`DidChangeValue`訊息`Icon`讓它將會看到變更以及索引鍵。

我們將使用`PersonModel`這篇文章的其餘的資料模型。

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>簡單資料繫結

使用我們的資料模型中定義，讓我們看看 Xcode 的介面產生器中的資料繫結的簡單範例。 例如，我們將表單加入我們 Xamarin.Mac 應用程式，可以用來編輯`PersonModel`我們上述定義的。 我們會新增一些文字欄位和核取方塊來顯示和編輯模型的屬性。

首先，我們將加入新**檢視控制器**至我們**Main.storyboard**介面產生器中的檔案，並命名為其類別`SimpleViewController`: 

[![加入新的檢視控制器](databinding-images/simple01.png "加入新的檢視控制器")](databinding-images/simple01-large.png#lightbox)

接下來，回到 Visual Studio for Mac，編輯**SimpleViewController.cs**檔案 （檔案已自動加入至受測專案） 和公開的執行個體`PersonModel`我們會在資料繫結到表單。 加入下列程式碼：

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

接下來載入檢視時，讓我們建立的執行個體我們`PersonModel`並填入這段程式碼：

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

現在我們要建立表單，請按兩下**Main.storyboard**檔案以開啟介面產生器中進行編輯。 版面配置表單看起來像下面這樣：

[![編輯在 Xcode 中的分鏡腳本](databinding-images/simple02.png "編輯在 Xcode 中的分鏡腳本")](databinding-images/simple02-large.png#lightbox)

資料繫結到表單`PersonModel`，我們透過公開`Person`索引鍵，執行下列動作：

1. 選取**員工姓名**文字欄位並切換到**繫結 Inspector**。
2. 請檢查**繫結至**方塊，然後選取**簡單檢視控制器**從下拉式清單。 接著輸入`self.Person.Name`如**金鑰路徑**: 

    [![輸入此金鑰路徑](databinding-images/simple03.png "輸入機碼路徑")](databinding-images/simple03-large.png#lightbox)
3. 選取**職業**文字欄位，然後核取**繫結至**方塊，然後選取**簡單檢視控制器**從下拉式清單。 接著輸入`self.Person.Occupation`如**金鑰路徑**:  

    [![輸入此金鑰路徑](databinding-images/simple04.png "輸入機碼路徑")](databinding-images/simple04-large.png#lightbox)
4. 選取**員工是管理員**核取方塊，並檢查**繫結至**方塊，然後選取**簡單檢視控制器**從下拉式清單。 接著輸入`self.Person.isManager`如**金鑰路徑**:  

    [![輸入此金鑰路徑](databinding-images/simple05.png "輸入機碼路徑")](databinding-images/simple05-large.png#lightbox)
5. 選取**受管理的員工數目**文字欄位，然後核取**繫結至**方塊，然後選取**簡單檢視控制器**從下拉式清單。 接著輸入`self.Person.NumberOfEmployees`如**金鑰路徑**:  

    [![輸入此金鑰路徑](databinding-images/simple06.png "輸入機碼路徑")](databinding-images/simple06-large.png#lightbox)
6. 如果員工不是管理員，我們想要隱藏的員工管理標籤的數字和文字欄位。
7. 選取**受管理的員工數目**標籤，展開 **隱藏**turndown 並檢查**繫結至**方塊，然後選取**簡單檢視控制器**從下拉式清單。 接著輸入`self.Person.isManager`如**金鑰路徑**:  

    [![輸入此金鑰路徑](databinding-images/simple07.png "輸入機碼路徑")](databinding-images/simple07-large.png#lightbox)
8. 選取`NSNegateBoolean`從**值轉換器**下拉式清單中：  

    ![選取 NSNegateBoolean 索引鍵轉換](databinding-images/simple08.png "選取 NSNegateBoolean 索引鍵轉換")
9. 這樣做會告知資料繫結標籤將會隱藏，是否值`isManager`屬性是`false`。
10. 重複步驟 7 和 8**受管理的員工數目**文字欄位。
11. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

如果您執行應用程式中，將值從`Person`屬性便會自動填入表單：

[![顯示自動填入表單](databinding-images/simple09.png "顯示自動填入的表單")](databinding-images/simple09-large.png#lightbox)

使用者對表單的任何變更將會回寫至`Person`檢視控制器中的屬性。 例如，取消選取**員工是管理員**更新`Person`的執行個體我們`PersonModel`和**受管理的員工數目**不見自動 （透過 標籤和文字欄位資料繫結）：

[![隱藏非主管的員工人數](databinding-images/simple10.png "隱藏非主管的員工人數")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>資料表檢視的資料繫結

既然我們已經有資料的方式繫結的基本概念，讓我們來看更複雜的資料繫結工作使用_陣列控制器_和資料繫結的資料表檢視。 如需使用的資料表檢視的詳細資訊，請參閱我們[資料表檢視](~/mac/user-interface/table-view.md)文件。

首先，我們將加入新**檢視控制器**至我們**Main.storyboard**介面產生器中的檔案，並命名為其類別`TableViewController`:

[![加入新的檢視控制器](databinding-images/table01.png "加入新的檢視控制器")](databinding-images/table01-large.png#lightbox)

接下來，讓編輯**TableViewController.cs**檔案 （檔案已自動加入至受測專案），並公開陣列 (`NSArray`) 的`PersonModel`我們會在資料繫結至表單的類別。 加入下列程式碼：

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

如同我們在`PersonModel`上方類別中[定義資料模型](#Defining_your_Data_Model) 區段中，我們已公開的四個特殊命名的公用方法，讓我們集合的陣列控制器，讀取和寫入資料`PersonModels`。

接下來載入檢視時，我們需要填入我們的陣列，這個程式碼：

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

現在我們需要建立我們資料表檢視表，請按兩下**Main.storyboard**檔案以開啟介面產生器中進行編輯。 配置的資料表看起來如下所示：

[![新的資料表檢視表的版面配置](databinding-images/table02.png "配置新的資料表檢視")](databinding-images/table02-large.png#lightbox)

我們需要加入**陣列控制器**，提供繫結的資料的資料表，執行下列動作：

1. 拖曳**陣列控制器**從**程式庫偵測器**到**介面編輯器**:  

    ![從程式庫選取陣列控制器](databinding-images/table03.png "從程式庫選取陣列控制器")
2. 選取**陣列控制器**中**介面階層架構**並切換至**屬性偵測器**:  

    [![選取的屬性偵測器](databinding-images/table04.png "選取屬性偵測器")](databinding-images/table04-large.png#lightbox)
3. 輸入`PersonModel`如**類別名稱**，按一下 **加上**按鈕，然後新增三個索引鍵。 它們的名稱`Name`，`Occupation`和`isManager`:  

    ![新增必要的金鑰路徑](databinding-images/table05.png "加入必要的機碼路徑")
4. 這會告訴陣列控制器設定什麼它所管理的陣列，它應該公開的屬性 （透過索引鍵）。
5. 切換至**繫結 Inspector**下方和 **內容陣列**選取**繫結至**和**資料表檢視控制器**。 輸入**模型機碼路徑**的`self.personModelArray`:  

    ![輸入的機碼路徑](databinding-images/table06.png "輸入機碼路徑")
6. 這涉及到陣列的陣列控制器`PersonModels`我們公開我們檢視控制站上。

現在我們要將我們的表格檢視繫結至陣列控制器，請執行下列作業：

1. 選取 [資料表] 檢視和**繫結 Inspector**:  

    [![選取繫結檢查](databinding-images/table07.png "選取繫結器")](databinding-images/table07-large.png#lightbox)
2. 在下**目錄**turndown 選取**繫結至**和**陣列控制器**。 輸入`arrangedObjects`如**控制器金鑰**欄位：  

    ![定義控制器索引鍵](databinding-images/table08.png "定義控制站的索引鍵")
3. 選取**資料表檢視儲存格**下**員工**資料行。 在**繫結器**下**值**turndown 選取**繫結至**和**資料表資料格檢視**。 輸入`objectValue.Name`如**模型機碼路徑**:  

    [![設定模型的機碼路徑](databinding-images/table09.png "設定模型的機碼路徑")](databinding-images/table09-large.png#lightbox)
4. `objectValue` 為目前`PersonModel`受陣列控制器的陣列中。
5. 選取**資料表檢視儲存格**下**職業**資料行。 在**繫結器**下**值**turndown 選取**繫結至**和**資料表資料格檢視**。 輸入`objectValue.Occupation`如**模型機碼路徑**:  

    [![設定模型的機碼路徑](databinding-images/table10.png "設定模型的機碼路徑")](databinding-images/table10-large.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

當我們執行應用程式時，資料表將會填入的陣列`PersonModels`:

[![執行應用程式](databinding-images/table11.png "執行應用程式")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>大綱檢視資料繫結

大綱檢視針對資料繫結是非常類似於針對資料表檢視表的繫結。 主要差異是，我們將使用**樹狀控制器**而不是**陣列控制器**大綱檢視中提供的繫結的資料。 如需有關使用大綱檢視的詳細資訊，請參閱我們[大綱檢視](~/mac/user-interface/outline-view.md)文件。

首先，我們將加入新**檢視控制器**至我們**Main.storyboard**介面產生器中的檔案，並命名為其類別`OutlineViewController`: 

[![加入新的檢視控制器](databinding-images/outline01.png "加入新的檢視控制器")](databinding-images/outline01-large.png#lightbox)

接下來，讓編輯**OutlineViewController.cs**檔案 （檔案已自動加入至受測專案），並公開陣列 (`NSArray`) 的`PersonModel`我們會在資料繫結至表單的類別。 加入下列程式碼：

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

如同我們在`PersonModel`上方類別中[定義資料模型](#Defining_your_Data_Model) 區段中，我們已公開的四個特殊命名的公用方法，讓我們集合的樹狀控制站和讀取和寫入資料`PersonModels`。

接下來載入檢視時，我們需要填入我們的陣列，這個程式碼：

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

現在，我們必須建立我們大綱檢視中，按兩下**Main.storyboard**檔案以開啟介面產生器中進行編輯。 配置的資料表看起來如下所示：

[![建立大綱檢視](databinding-images/outline02.png "建立大綱檢視")](databinding-images/outline02-large.png#lightbox)

我們需要加入**樹狀控制器**繫結的資料提供給我們的外框，執行下列動作：

1. 拖曳**樹狀控制器**從**程式庫偵測器**到**介面編輯器**:  

    ![從程式庫選取樹狀目錄中控制站](databinding-images/outline03.png "選取樹狀目錄中控制站從程式庫")
2. 選取**樹狀控制器**中**介面階層架構**並切換至**屬性偵測器**:  

    [![選取的屬性偵測器](databinding-images/outline04.png "選取屬性偵測器")](databinding-images/outline04-large.png#lightbox)
3. 輸入`PersonModel`如**類別名稱**，按一下 **加上**按鈕，然後新增三個索引鍵。 它們的名稱`Name`，`Occupation`和`isManager`:  

    ![新增必要的金鑰路徑](databinding-images/outline05.png "加入必要的機碼路徑")
4. 這會告訴樹狀控制站功能它所管理的陣列，它應該公開的屬性 （透過索引鍵）。
5. 下**樹狀控制器**區段中，輸入`personModelArray`如**子系**，輸入`NumberOfEmployees`下**計數**並輸入`isEmployee`下**分葉**:  

    ![設定樹狀目錄中控制站的機碼路徑](databinding-images/outline05.png "設定樹狀目錄中控制站的機碼路徑")
6. 這會告訴樹狀控制站如何尋找任何子節點、 有多少子節點和如果目前的節點有子節點。
7. 切換至**繫結 Inspector**下方和 **內容陣列**選取**繫結至**和**檔案的擁有者**。 輸入**模型機碼路徑**的`self.personModelArray`:  

    ![編輯此金鑰路徑](databinding-images/outline06.png "編輯機碼路徑")
8. 這涉及樹狀結構的陣列控制器`PersonModels`我們公開我們檢視控制站上。

現在，我們必須將我們的大綱檢視繫結至樹狀目錄中控制站，執行下列作業：

1. 選取的大綱檢視和**繫結的偵測器**選取：  

    [![選取繫結檢查](databinding-images/outline07.png "選取繫結器")](databinding-images/outline07-large.png#lightbox)
2. 下**大綱檢視內容**turndown 選取**繫結至**和**樹狀控制器**。 輸入`arrangedObjects`如**控制器金鑰**欄位：  

    ![設定控制器的機碼](databinding-images/outline08.png "設定控制器的機碼")
3. 選取**資料表檢視儲存格**下**員工**資料行。 在**繫結器**下**值**turndown 選取**繫結至**和**資料表資料格檢視**。 輸入`objectValue.Name`如**模型機碼路徑**:  

    [![輸入模型的機碼路徑](databinding-images/outline09.png "輸入模型的機碼路徑")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` 為目前`PersonModel`由樹狀目錄中控制站所管理的陣列中。
5. 選取**資料表檢視儲存格**下**職業**資料行。 在**繫結器**下**值**turndown 選取**繫結至**和**資料表資料格檢視**。 輸入`objectValue.Occupation`如**模型機碼路徑**:  

    [![輸入模型的機碼路徑](databinding-images/outline10.png "輸入模型的機碼路徑")](databinding-images/outline10-large.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

外框如果我們執行應用程式時，將會填入的陣列`PersonModels`:

[![執行應用程式](databinding-images/outline11.png "執行應用程式")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>集合檢視的資料繫結

與集合檢視的資料繫結非常類似繫結資料表檢視中，當陣列控制器用來提供資料給集合。 集合檢視，沒有預設的顯示格式，因為所需工作愈提供使用者互動的意見反應，以及追蹤使用者選取項目。

> [!IMPORTANT]
> 在 Xcode 7 和 macOS 10.11 （和大於） 中的問題，因為集合檢視將無法使用在分鏡腳本 (.storyboard) 檔內。 如此一來，您必須繼續使用.xib 檔案以定義您的集合檢視 Xamarin.Mac 應用程式。 請參閱我們[集合檢視](~/mac/user-interface/collection-view.md)文件的詳細資訊。

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

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

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

## <a name="debugging-native-crashes"></a>偵錯原生的當機

在您的資料繫結進行錯誤可能會導致_原生損毀_在 unmanaged 程式碼，並且造成 Xamarin.Mac 應用程式完全失敗，並`SIGABRT`錯誤：

[![原生的損毀的對話方塊中的範例](databinding-images/debug01.png "的原生的損毀對話方塊範例")](databinding-images/debug01-large.png#lightbox)

資料繫結期間通常有四個的原生的損毀的主要原因：

1. 您的資料模型不是繼承自`NSObject`或的子類別`NSObject`。
2. 未公開屬性 Objective C 使用`[Export("key-name")]`屬性。
3. 未包裝的存取子中的值變更`WillChangeValue`和`DidChangeValue`方法呼叫 (指定為索引鍵的相同`Export`屬性)。
4. 中有錯誤或輸入錯誤的索引鍵**繫結的偵測器**介面產生器中。

### <a name="decoding-a-crash"></a>解碼損毀

讓我們，我們可以顯示如何尋找及修正此問題，請在我們的資料繫結造成原生損毀。 在介面產生器中，我們來變更我們的繫結的集合檢視範例中的第一個標籤的`Name`至`Title`:

[![編輯繫結索引鍵](databinding-images/debug02.png "編輯繫結索引鍵")](databinding-images/debug02-large.png#lightbox)

讓我們來儲存變更，請切換回到 Visual Studio for Mac 同步 Xcode，並執行我們的應用程式。 顯示集合檢視時，應用程式將會立刻顯示損毀與`SIGABRT`錯誤 (如中所示**應用程式輸出**適用於 Mac 的 Visual Studio 中) 因為`PersonModel`不會公開與索引鍵屬性`Title`:

[![繫結錯誤的範例](databinding-images/debug03.png "繫結錯誤的範例")](databinding-images/debug03-large.png#lightbox)

如果我們捲動至最上方的中的錯誤**應用程式輸出**我們可以看到要解決此問題的索引鍵：

[![在錯誤記錄檔中尋找問題](databinding-images/debug04.png "錯誤記錄檔中尋找問題")](databinding-images/debug04-large.png#lightbox)

這一行會告訴我們，金鑰`Title`我們要繫結的物件上不存在。 若我們變更繫結回`Name`介面產生器中，儲存、 同步處理，在重建並執行，應用程式將會如預期般執行不會發生問題。

## <a name="summary"></a>總結

這篇文章已取得使用資料繫結和 Xamarin.Mac 應用程式中的索引鍵-值撰寫程式碼的詳細的檢視。 首先，它看藉由使用鍵值撰寫程式碼 (KVC)，以及索引鍵-值觀察 (KVO) 公開 Objective C 的 C# 類別。 接下來，它會示範如何使用 KVO 標準的類別和資料繫結到 Xcode 的介面產生器中的 UI 項目。 最後，它顯示複雜資料繫結使用**陣列控制器**和**樹狀控制站**。


## <a name="related-links"></a>相關連結

- [MacDatabinding 分鏡腳本 （範例）](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding XIBs （範例）](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [標準控制項](~/mac/user-interface/standard-controls.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [集合檢視](~/mac/user-interface/collection-view.md)
- [索引鍵-值編碼程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [索引鍵-值觀察程式設計指南簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [介紹程式設計主題 Cocoa 繫結](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduction to Cocoa 繫結參考](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
