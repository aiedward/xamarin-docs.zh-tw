---
title: 適用於 Xamarin.iOS MonoTouch.Dialog 簡介
description: 本文件說明 MonoTouch.Dialog (MTD)，來利用 Xamarin.iOS 的快速、 宣告式 UI 開發的架構。 它討論如何使用 MonoTouch.Dialog Api 來建立一個介面的程式碼或 JSON，並使用提取以重新整理、 搜尋、 背景影像載入，等的功能。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
---

# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>適用於 Xamarin.iOS MonoTouch.Dialog 簡介

MonoTouch.Dialog，稱為 MT簡稱，D 是快速的 UI 開發工具組可讓開發人員建置應用程式畫面和瀏覽使用的詳細資訊，而不是建立檢視控制器、 資料表等的冗長工作。因此，它會提供大幅簡化的 UI 開發和程式碼降低。 例如，請考慮下列的螢幕擷取畫面：

 [![](images/image1.png "例如，請考慮這個螢幕擷取畫面")](images/image1.png#lightbox)

下列程式碼用來定義此整個畫面：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

當使用 iOS 中的資料表，通常會大量重複的程式碼。
例如，每次所需的資料表的資料來源需要來填入該資料表。 應用程式可透過瀏覽控制器連接兩個資料表為基礎的畫面中，每個畫面共用許多相同的程式碼。

MTD 簡化，將全部程式碼封裝成一個一般的 API，以建立資料表。 然後，它會提供繫結語法，可讓您更輕鬆地為宣告式物件可讓該 API 之上的抽象概念。 因此，有兩個 Api 用於 MTD:

-   **低層級的項目 API** –*項目 API*根據建立的項目代表螢幕和其元件的階層式樹狀結構。 項目 API 讓開發人員最大的彈性及控制建立 Ui。 此外，項目 API 具有 JSON，這非常快速地宣告，以及從伺服器動態 UI 的產生是用來透過進階支援宣告式的定義。 
-   **高層級的反映 API** – 也稱為 *繫結* *API* 中的類別已標註 UI 提示，然後 MTD 會自動建立以物件為基礎的畫面，並提供功能之間的繫結是顯示 （和選擇性地編輯） 在畫面上，以及基礎物件支援。 上述範例所示使用反映 API。 此 API 不提供精細的控制中的項目 API，但可減少複雜性更進一步所自動建立根據類別屬性的項目階層。 


MTD 是封裝使用的一大組內建的畫面建立的 UI 項目，但它也會辨識需要自訂的項目和進階的畫面版面配置。 因此，擴充性是第一級的功能內建 API。 開發人員可以擴充現有的項目或建立新的和無縫整合。

此外，MTD 具有多種常見的 iOS UX 功能，例如 「 提取-refresh 」 支援，非同步影像載入，並搜尋支援內建的。

這篇文章會使用 MT 的完整介紹D，包括：

-   **MTD 元件**– 這會著重於了解組成 MT 類別若要啟用 快速入門了解 D。 
-   **項目參考**– 內建的項目，MT 的完整清單D. 
-   **進階用法**– 這涵蓋進階的功能，例如提取以重新整理、 搜尋、 背景映像載入、 使用 LINQ 來建置項目階層，以及建立自訂的項目中的資料格，並控制站使用 MTD. 

## <a name="setting-up-mtd"></a>MT 的設定D

MTD Xamarin.iOS 一起散發。 若要使用它，以滑鼠右鍵按一下**參考**節點的 Xamarin.iOS 專案在 Visual Studio 2017 或 Visual Studio for Mac，並加入參考**MonoTouch.Dialog 1**組件。 然後，新增`using MonoTouch.Dialog`視您在來源中的陳述式的程式碼。

## <a name="understanding-the-pieces-of-mtd"></a>了解 MT 的項目D

即使是使用反映 API，MT就如同它已直接建立透過項目 API，D 就會建立在幕後，項目階層架構。 此外，在上一節中所述的 JSON 支援會一併建立項目。 基於這個理由，是一定要有基本的了解構成部分的 MTD.

MTD 會建置使用下列四個部分的畫面：

-  **DialogViewController**
-  **RootElement**
-  **區段**
-  **目**


### <a name="dialogviewcontroller"></a>DialogViewController

A *DialogViewController*，或*DVC*簡稱，繼承自`UITableViewController`並因此代表資料表使用的畫面。 DVCs 可以推就像一般 UITableViewController 瀏覽控制器。

### <a name="rootelement"></a>RootElement

A *RootElement*是進入 DVC 之項目的最上層的容器。 它包含區段，其中可以再包含項目。 此外，不會轉譯 RootElements;相反地，它們只是容器項目實際呈現。 RootElement 會指派給 DVC，而且接著 DVC 轉譯及其子系。

### <a name="section"></a>區段

區段是一組資料表中的儲存格。 如同一般資料表 區段中，它可以選擇性地可以頁首和頁尾，可以是文字或甚至是自訂檢視，如下列螢幕擷取畫面所示：

 [![](images/image2.png "如同一般資料表 區段中，它可以選擇性地可以頁首和頁尾，可以是文字或甚至是自訂檢視，如以下螢幕擷取畫面所示")](images/image2.png#lightbox)

### <a name="element"></a>元素

項目代表實際的資料格在資料表中。 MTD 會封裝與各種不同的項目代表不同的資料類型或不同的輸入。 例如，下列螢幕擷取畫面說明幾個可用的項目：

 [![](images/image3.png "例如，此螢幕擷取畫面會說明幾個可用的項目")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>多個區段和 RootElements

讓我們現在討論 RootElements 和各節詳細說明。

### <a name="rootelements"></a>RootElements

至少一個 RootElement，才能啟動 MonoTouch.Dialog 程序。

如果區段/項目值進行初始化 RootElement 則會使用此值來尋找子系項目，將提供的設定，這呈現在右側顯示的摘要。 例如，以下螢幕擷取畫面會與包含在右側，「 甜 」，以及所選的沙漠值的詳細資料畫面的標題儲存格左側顯示 資料表。

 [![](images/image4.png "此螢幕擷取畫面顯示在左側的資料表，以包含在右側，甜點，以及所選的沙漠值的詳細資料畫面的標題儲存格")](images/image4.png#lightbox) [![](images/image5.png "這以下螢幕擷取畫面顯示在左邊的資料表，以包含在右側，甜點，以及所選的沙漠值的詳細資料畫面的標題儲存格")](images/image5.png#lightbox)

根項目也可用在區段內以觸發 載入新的巢狀的組態頁面上，如上所示。 在此模式中使用時提供的標題會用來轉譯區段內而，也會用於為標題子頁。 例如: 

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner"){
            new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
                new Section () {
                    new RadioElement ("Ice Cream", "dessert"),
                    new RadioElement ("Milkshake", "dessert"),
                    new RadioElement ("Chocolate Cake", "dessert")
                }
            }
        }
    }
```

在上述範例中，當使用者點選 「 甜"，MonoTouch.Dialog 會建立新的頁面，並瀏覽至該 「 甜"，有三個值的選項群組的根。

在這個特定範例中，由於我們傳遞給 RadioGroup 的值"2"的選項群組時，會選取 「 甜 」 一節中的 「 巧克力蛋糕"。 這表示挑選清單 （零索引） 上的第 3 個項目。

呼叫 Add 方法，或使用 C# 4 初始設定式語法加入區段。
要插入的動畫的各節提供插入方法。

如果您建立群組執行個體 （而不是 RadioGroup) RootElement RootElement 一節中所出現的摘要值會累加計數的所有 BooleanElements 和 CheckboxElements 具有 Group.Key 值相同的索引鍵。

### <a name="sections"></a>章節

區段可用來在畫面中的群組項目，它們是唯一有效的直接子系的 RootElement。 區段可以包含任何標準的項目，包括新 RootElements。

RootElements 嵌入區段用來瀏覽至新的更深層級。

字串，或是 UIViews 頁首和頁尾可以有區段。
通常您只會使用字串，但若要建立自訂 Ui 可作為任何 UIView 頁首或頁尾。 您可以使用字串來建立它們就像這樣：

```csharp
var section = new Section ("Header", "Footer")
```

若要使用檢視，只傳遞給建構函式的檢視：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>取得通知

#### <a name="handling-nsaction"></a>處理 NSAction

MTD 表面`NSAction`為處理回呼委派。
例如，假設您想要處理 MT 所建立的資料表資料格的觸控事件D. 使用 MT 建立項目時D，只是提供回呼函式，如下所示：

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>擷取項目值

結合`Element.Value`屬性，回呼可以擷取其他項目中設定的值。 例如，請參考下列程式碼：

```csharp
var element = new EntryElement (task.Name, "Enter task description",
        task.Description);
                
var taskElement = new RootElement (task.Name){
        new Section () { element },
        new Section () { 
                new DateElement ("Due Date", task.DueDate)
        },
        new Section ("Demo Retrieving Element Value") {
                new StringElement ("Output Task Description", 
                        delegate { Console.WriteLine (element.Value); })
        }
};
```

此程式碼建立 UI，如下所示。 如本範例的完整逐步解說，請參閱 <<c0> [ 項目 API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教學課程。

 [![](images/image6.png "結合 Element.Value 屬性，回呼可以擷取其他項目中設定的值")](images/image6.png#lightbox)

當使用者按下底部資料表資料格時，匿名函式中的程式碼執行時，從值寫入`element`執行個體**應用程式輸出**中 Visual Studio for mac。

## <a name="built-in-elements"></a>內建的項目

MTD 隨附許多內建的資料表資料格的項目稱為項目。
這些項目用來在表格儲存格中，例如字串、 浮點數、 日期和偶數的映像，是一些顯示各種不同的類型。 每個項目負責適當地顯示的資料類型。 例如，布林值的項目會顯示交換器，以切換其值。 同樣地，float 項目會顯示滑桿來變更浮點值。

有更複雜的項目，以支援更豐富的資料類型，例如影像和 html。 例如，html 項目，以開啟 UIWebView 載入網頁上選取時，會顯示標題中的資料表資料格。

### <a name="working-with-element-values"></a>使用項目值

用來擷取使用者輸入的項目公開公用`Value`隨時保留項目的目前值的屬性。 它會自動更新的因為使用者使用應用程式。

這是所有 MonoTouch.Dialog，一部分的項目行為，但它都並不需要使用者建立的項目。

### <a name="string-element"></a>字串項目

A`StringElement`表格儲存格和資料格右邊的字串值的左邊顯示的標題。

 [![](images/image7.png "StringElement 表格儲存格和資料格右邊的字串值的左邊顯示標題")](images/image7.png#lightbox)

若要使用`StringElement`為按鈕，提供委派。

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "若要使用 StringElement 為按鈕，提供委派")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>已設定樣式的字串項目

A`StyledStringElement`允許必須存在於使用任一個內建的表格儲存格樣式的字串或自訂格式。

 [![](images/image9.png "StyledStringElement 允許必須存在於使用任一個內建的表格儲存格樣式的字串或自訂格式")](images/image9.png#lightbox)

`StyledStringElement`類別衍生自`StringElement`，但可讓開發人員自訂少數幾個屬性，例如字型、 文字色彩、 背景資料格顏色、 行中斷模式、 要顯示的行數，以及是否應該顯示附屬應用程式。

### <a name="multiline-element"></a>多行的項目

 [![](images/image10.png "多行的項目")](images/image10.png#lightbox)

### <a name="entry-element"></a>項目項目

`EntryElement`，如名稱所示，用來取得使用者輸入。 它支援一般字串或字元會隱藏其中的密碼。

 [![](images/image11.png "EntryElement 用來取得使用者輸入")](images/image11.png#lightbox)

初始化具有三個值：

-  會向使用者顯示之項目的標題。
-  （這是提供提示給使用者的灰色文字） 的預留位置文字。 
-  文字的值。


預留位置和值可以是 null。 不過，標題是必要的。

在任何時間點，在存取其值的屬性可以擷取的值`EntryElement`。

此外`KeyboardType`屬性可以設定在建立時，用於資料輸入所需之鍵盤類型樣式。 這可以用來設定使用的值的鍵盤`UIKeyboardType`，如下所示：

-  數值
-  Phone
-  URL
-  Email


### <a name="boolean-element"></a>布林值項目

 [![](images/image12.png "布林值項目")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>核取方塊項目

 [![](images/image13.png "核取方塊項目")](images/image13.png#lightbox)

### <a name="radio-element"></a>選項項目

A`RadioElement`需要`RadioGroup`中指定`RootElement`。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "RadioElement 需要以指定 RootElement RadioGroup")](images/image14.png#lightbox)

 `RootElements` 也可協調選項項目。 `RadioElement`成員可以跨越多個區段 （例如若要實作類似的信號音選取器和不同的自訂鈴聲從系統鈴聲）。 [摘要] 檢視會顯示目前選取的選項項目。 若要使用此功能，建立`RootElement`使用群組建構函式中，像這樣：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

在 群組名稱`RadioGroup`用來顯示選取的值中包含的頁面 （如果有的話） 和值，在此情況下為零，這是索引的第一個選取的項目。

### <a name="badge-element"></a>徽章元素

 [![](images/image15.png "徽章元素")](images/image15.png#lightbox)

### <a name="float-element"></a>浮點數的項目

 [![](images/image16.png "浮點數的項目")](images/image16.png#lightbox)

### <a name="activity-element"></a>活動項目

 [![](images/image17.png "活動項目")](images/image17.png#lightbox)

### <a name="date-element"></a>日期的項目

 ![](images/image18.png "日期的項目")

選取 DateElement 對應的資料格時，日期選擇器會顯示如下所示：

 [![](images/image19.png "日期選擇器選取 DateElement 對應的資料格時，會顯示所示")](images/image19.png#lightbox)

### <a name="time-element"></a>Time 元素

 [![](images/image20.png "Time 元素")](images/image20.png#lightbox)

選取 TimeElement 對應的資料格時，時間選擇器會顯示如下所示：

 [![](images/image21.png "時間選擇器選取 TimeElement 對應的資料格時，會顯示所示")](images/image21.png#lightbox)

### <a name="datetime-element"></a>日期時間的項目

 [![](images/image22.png "日期時間的項目")](images/image22.png#lightbox)

選取 DateTimeElement 對應的資料格時，日期時間選擇器會顯示如下所示：

 [![](images/image23.png "如所示，選取 DateTimeElement 對應的資料格時，會顯示日期時間選擇器")](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 項目

 [![](images/image24.png "HTML 項目")](images/image24.png#lightbox)

`HTMLElement`顯示的值及其`Caption`中的資料表資料格屬性。 選取，當`Url`指派給項目會載入`UIWebView`控制，如下所示：

 [![](images/image25.png "當選取，如下所示 UIWebView 控制項載入指派給項目的 Url")](images/image25.png#lightbox)

### <a name="message-element"></a>訊息項目

 [![](images/image26.png "訊息項目")](images/image26.png#lightbox)

### <a name="load-more-element"></a>載入更多的項目

若要允許使用者載入更多的項目清單中，使用此項目。 您可以自訂一般和載入標題，以及字型及文字的色彩。
`UIActivity`指標，會啟動 「 加上動畫，並載入標題會顯示當使用者點選的資料格，然後`NSAction`傳遞至建構函式會執行。 一次中的程式碼`NSAction`完成時，`UIActivity`指標會停止繪製動畫，並正常的標題就會再次顯示。

### <a name="uiview-element"></a>UIView 項目

此外，任何自訂`UIView`可以使用顯示`UIViewElement`。

### <a name="owner-drawn-element"></a>為主控描繪項目

這個項目必須子類別，因為它是抽象類別。 您應該覆寫`Height(RectangleF bounds)`方法中，您應該傳回之項目的高度，以及`Draw(RectangleF bounds, CGContext context, UIView view)`中這就應該指定界限內，所有自訂的繪圖使用內容和檢視的參數。 此項目一樣與日俱增的子類別化`UIView`，然後將它放在儲存格中傳回讓您只需要實作兩個簡單的覆寫。 您可以看到在範例應用程式中更好的範例實作`DemoOwnerDrawnElement.cs`檔案。

以下是一個非常簡單的範例實作的類別：

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
 {
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text
    {
        get;set;    
    }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
 }
```

### <a name="json-element"></a>JSON 元素

`JsonElement`是的子類別`RootElement`延伸`RootElement`要能夠從本機或遠端的 url 載入的巢狀的子系內容。

`JsonElement`是`RootElement`，可以具現化兩種形式。 其中一個版本建立`RootElement`，將隨選內容的載入。 這些藉由使用`JsonElement`採取額外的引數，在結束時，要載入的內容 url 的建構函式：

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

另一個表單建立將資料從本機檔案或現有`System.Json.JsonObject`您已剖析：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

如需詳細資訊，在 JSON 中使用 MTD，請參閱[JSON 元素的逐步解說](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough)教學課程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>若要重新整理提取支援

 *提取-到-* *重新整理*視覺效果原本位於*Tweetie2*應用程式，這變得很熱門的影響，在許多應用程式之間。

若要加入您的對話方塊中的自動提取以重新整理支援，您只需要做兩件事： 將使用者提取資料時收到通知的事件處理常式連結，並通知`DialogViewController`回到其預設狀態載入的資料。

通知連結很簡單;只要連`RefreshRequested`上的事件`DialogViewController`，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

接著，在您的方法`OnUserRequestedRefresh`，您會載入一些資料排入佇列，net，向要求某些資料或微調執行緒來計算的資料。 將資料載入之後，您必須通知`DialogViewController`新的資料，並以還原為其預設狀態的檢視，您可以呼叫`ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜尋支援

若要支援搜尋，`EnableSearch`屬性上的您`DialogViewController`。 您也可以設定`SearchPlaceholder`做為浮水印文字的搜尋列中的屬性。

搜尋會隨著使用者輸入檢視的內容。 它會搜尋在可見欄位，並顯示這些使用者。 `DialogViewController`會公開三種方法來以程式設計方式起始、 終止或觸發新的篩選作業的結果。 這些方法如下：

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


系統，所以如果您想要可以改變此行為。

### <a name="background-image-loading"></a>背景影像載入

MonoTouch.Dialog 併入[TweetStation](https://github.com/migueldeicaza/TweetStation)應用程式的影像載入程式。 此映像載入器可以用來載入映像，在背景中，快取的支援，並在載入影像時，可以通知您的程式碼。

它也會將限制連出網路連線的數目。

影像載入程式中實作`ImageLoader`類別，您只需要是呼叫`DefaultRequestImage`方法，您必須提供您想要載入，映像，以及執行個體的 Uri`IImageUpdated`介面將會時叫用的映像 ha已載入的 s。

下列程式碼，例如從 Url 載入影像`BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

ImageLoader 類別會公開您想要釋放所有目前快取在記憶體中的映像時，您可以呼叫的清除方法。 目前的程式碼有 50 的映像快取。 如果您想要使用不同的快取大小，（比方說，如果您預期得太大，50 的映像會太多的映像），您就可以建立 ImageLoader 的執行個體，並傳遞您想要保留在快取中的映像數目。

## <a name="using-linq-to-create-element-hierarchy"></a>若要建立項目階層架構中使用 LINQ

LINQ 和 C# 的初始化語法的聰明的使用方式，透過 LINQ 可用來建立項目階層。 比方說，下列程式碼從某些字串陣列中建立螢幕，並控制代碼儲存格的選取項目，透過匿名函式傳遞至每個`StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
from x in new string [] { "one", "two", "three" }
select new Section (x) {
from y in "Hello:World".Split (':')
select (Element) new StringElement (y,
delegate { Debug.WriteLine("cell tapped"); })
}
};
```

這可以輕鬆地使用 XML 資料存放區或幾乎完全從資料建立複雜的應用程式資料庫中的資料結合。

## <a name="extending-mtd"></a>擴充的 MTD

### <a name="creating-custom-elements"></a>建立自訂的項目

藉由繼承自其中一個現有的項目，或藉由衍生自根類別項目，您可以建立您自己的項目。

若要建立您自己的項目，您會想要覆寫下列方法：

```csharp
// To release any heavy resources that you might have
    void Dispose (bool disposing);

    // To retrieve the UITableViewCell for your element
    // you would need to prepare the cell to be reused, in the
    // same way that UITableView expects reusable cells to work
    UITableViewCell GetCell (UITableView tv)

    // To retrieve a "summary" that can be used with
    // a root element to render a summary one level up.  
    string Summary ()
    // To detect when the user has tapped on the cell
    void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path)
    // If you support search, to probe if the cell matches the user input
    bool Matches (string text)
```

如果您的項目可以有變數的大小，您需要實作`IElementSizing`介面，其中包含一種方法：

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

如果您打算實作您`GetCell`藉由呼叫的方法`base.GetCell(tv)`自訂傳回的儲存格，您需要也會覆寫`CellKey`屬性以傳回會是您的項目，唯一的索引鍵如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

這適用於大部分的項目，但不適用於`StringElement`和`StyledStringElement`為各種轉譯案例使用自己的金鑰集。 您必須將複寫這些類別中的程式碼。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

反映和項目 API 會使用相同`DialogViewController`。 有時候您會想要自訂檢視的外觀，或您可能想要使用的某些功能`UITableViewController`，超越基本的 Ui 建立。

`DialogViewController`是只的子類別`UITableViewController`您可以在您想自訂的相同方式加以自訂和`UITableViewController`。

例如，如果您想要變更是清單樣式`Grouped`或`Plain`，您可以將此值變更的屬性，當您建立的控制站，就像這樣：

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

如需其他進階自訂`DialogViewController`，例如設定它的背景，就像子類別並覆寫適當的方法，如下列範例所示：

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

另一個的自訂點是中的下列虛擬方法`DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

此方法應傳回的子類別`DialogViewController.Source`情況下，平均大小儲存格，或子類別的`DialogViewController.SizingSource`如果儲存格不一致。

您可以使用此覆寫來擷取任何`UITableViewSource`方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation)會使用此追蹤使用者已捲動至上方時，並據以更新的未讀取的推文數目。

## <a name="validation"></a>驗證

項目不會提供驗證本身為適合網頁的模型和桌面應用程式執行未直接對應至 iPhone 互動模型。

如果您想要執行資料驗證，您應該這樣做時使用者輸入的資料觸發動作。 比方說<span class="ui">完成</span>或是<span class="ui">下一步</span>按鈕，在頂端工具列中，或一些`StringElement`作為按鈕，移至下一個階段。

這是您會執行基本的輸入的驗證，而可能是更複雜像檢查有效性的使用者/密碼組合與伺服器的驗證。

您通知使用者發生錯誤的方式是特定的應用程式。 您可以快顯`UIAlertView`或顯示的提示。

## <a name="summary"></a>總結

本文涵蓋許多 MonoTouch.Dialog 的相關資訊。 它討論如何的基本概念 MTD 運作，而且涵蓋各種元件組成 MTD. 它也會示範各種項目和資料表 MT 所支援的自訂項目D，討論如何 MTD 可以擴充與自訂項目。 此外它說明 MT 的 JSON 支援可讓您以動態方式建立項目，從 JSON 的 D。


## <a name="related-links"></a>相關連結

- [螢幕錄製影片-Miguel de Icaza 建立 iOS 登入畫面 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [螢幕錄製影片-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [逐步解說：使用元素 API 建立應用程式](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [逐步解說：使用反射 API 建立應用程式](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [逐步解說：若要建立使用者介面中使用 JSON 元素](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch.Dialog JSON 標記](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [在 Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
