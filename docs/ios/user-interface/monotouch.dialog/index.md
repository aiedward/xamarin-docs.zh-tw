---
title: 適用于 Xamarin 的 MonoTouch 簡介。
description: 本檔說明 MonoTouch （MT）。D），這是使用 Xamarin 進行快速、宣告式 UI 開發的架構。 它討論如何使用 MonoTouch Api，以程式碼或 JSON 建立介面，並使用提取重新整理、搜尋、背景影像載入等功能。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 68f8349fd6c8f90b36fb5edb2838dfec352a5800
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002578"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>適用于 Xamarin 的 MonoTouch 簡介。

MonoTouch，稱為 MT。D. 簡單的說，是快速的 UI 開發工具組，可讓開發人員使用資訊來建立應用程式畫面和導覽，而不是冗長建立視圖控制器、資料表等等。因此，它可大幅簡化 UI 開發和程式碼縮減。 例如，請考慮下列螢幕擷取畫面：

 [![](images/image1.png "For example, consider this screenshot")](images/image1.png#lightbox)

下列程式碼是用來定義這個整個畫面：

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

在 iOS 中使用資料表時，通常會有許多重複性的程式碼。
例如，每次需要資料表時，都需要資料來源來填入該資料表。 在具有兩個透過流覽控制器連接之以資料表為基礎之螢幕的應用程式中，每個畫面都會共用許多相同的程式碼。

MT.D 藉由將所有程式碼封裝成用於建立資料表的一般 API，來簡化此工作。 然後，它會在該 API 之上提供抽象概念，以允許宣告式物件系結語法，讓它更容易。 因此，MT 中有兩個可用的 Api。D

- **低層級元素 api** -*元素 api*是以建立元素的階層式樹狀結構為基礎，這些專案代表螢幕及其元件。 元素 API 可讓開發人員在建立 Ui 時擁有最大的彈性和控制能力。 此外，元素 API 具有透過 JSON 的宣告式定義的先進支援，這可讓您進行非常快速的宣告，以及從伺服器產生動態 UI。 
- **高階反映 api** （也稱為系結*api* ），其中的類別會以 UI 提示和 MT 來標注。D 會根據物件自動建立畫面，並在螢幕上顯示（並選擇性編輯）的內容，以及支援的基礎物件之間提供系結。   上述範例說明了如何使用反映 API。 此 API 不會提供元素 API 所執行的細微控制，但它會根據類別屬性自動建立元素階層，以更進一步降低複雜度。 

MT.D 封裝了一組大量的內建 UI 元素，用於建立螢幕，但它也能辨識自訂專案和高級螢幕佈局的需求。 因此，擴充性是 API 的第一類精選內建。 開發人員可以擴充現有的專案，或建立新的專案，然後順暢地進行整合。

此外，MT。D 有一些內建的常見 iOS UX 功能，例如「提取至重新整理」支援、非同步映射載入和搜尋支援。

本文將全面探討如何使用 MT。D，包括：

- **MT。D 元件**–這將著重于瞭解組成 MT 的類別。D，讓您快速掌握速度。 
- **元素參考**– MT 內建元素的完整清單。D. 
- **Advanced Usage** –這涵蓋了一項先進的功能，例如提取至重新整理、搜尋、背景影像載入、使用 LINQ 建立元素階層，以及建立自訂專案、儲存格和控制器以用於 MT。D. 

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它，請以滑鼠右鍵按一下 Visual Studio 2017 或 Visual Studio for Mac 中之 Xamarin 專案的 [**參考**] 節點，然後新增**MonoTouch**元件的參考。 然後，視需要在您的原始程式碼中加入 `using MonoTouch.Dialog` 語句。

## <a name="understanding-the-pieces-of-mtd"></a>瞭解 MT 的各部分。D

即使是使用反映 API，MT 也是一樣。D 會在幕後建立元素階層，就像是直接透過 Elements API 建立一樣。 此外，上一節所述的 JSON 支援也會建立元素。 基於這個理由，請務必對 MT 的構成部分具備基本瞭解。D.

MT.D 組建畫面使用下列四個部分：

- **DialogViewController**
- **RootElement**
- **區段**
- **目**

### <a name="dialogviewcontroller"></a>DialogViewController

*DialogViewController*（簡稱*DVC* ）會繼承自 `UITableViewController`，因此代表含有資料表的畫面。 DVCs 可以推入至流覽控制器，就像一般 UITableViewController 一樣。

### <a name="rootelement"></a>RootElement

*RootElement*是進入 DVC 之專案的最上層容器。 其中包含區段，其中可以包含專案。 RootElements 不會呈現;相反地，它們只是實際呈現內容的容器。 RootElement 會指派給 DVC，然後 DVC 會呈現其子系。

### <a name="section"></a>區段

「區段」（section）是資料表中的一組資料格。 如同一般資料表區段，它可以選擇性地擁有可以是文字的頁首和頁尾，或甚至是自訂的視圖，如下列螢幕擷取畫面所示：

 [![](images/image2.png "As with a normal table section, it can optionally have a header and footer that can either be text, or even custom views, as in this screenshot")](images/image2.png#lightbox)

### <a name="element"></a>項目

元素代表資料表中的實際資料格。 MT.D 會以各種不同的元素封裝，這些專案代表不同的資料類型或不同的輸入。 例如，下列螢幕擷取畫面說明一些可用的元素：

 [![](images/image3.png "For example, this screenshots illustrate a few of the available elements")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>深入瞭解區段和 RootElements

現在讓我們更詳細地討論 RootElements 和章節。

### <a name="rootelements"></a>RootElements

至少需要一個 RootElement，才能啟動 MonoTouch。對話進程。

如果 RootElement 是使用區段/元素值進行初始化，則會使用這個值來尋找將提供設定摘要的子專案，這會在顯示的右側呈現。 例如，下列螢幕擷取畫面顯示左側的資料表，其中包含右邊的詳細資料螢幕標題 "甜點"，以及所選沙漠的值。

 [![](images/image4.png "這個螢幕擷取畫面顯示左邊有一個資料表，其中包含右邊的詳細資料螢幕標題、甜點，以及所選沙漠的值。")](images/image4.png#lightbox)[![](images/image5.png "下列螢幕擷取畫面顯示左側的資料表，其中包含右邊的詳細資料螢幕標題甜點，以及所選沙漠的值。")](images/image5.png#lightbox)

根項目也可以在區段內使用，以觸發載入新的嵌套設定頁面，如上所示。 在此模式中使用時，所提供的標題會在區段內部轉譯時使用，而且也會當做子頁面的標題使用。 例如:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner") {
        new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
            new Section () {
                new RadioElement ("Ice Cream", "dessert"),
                new RadioElement ("Milkshake", "dessert"),
                new RadioElement ("Chocolate Cake", "dessert")
            }
        }
    }
};
```

在上述範例中，當使用者按下 [甜點] 時，[MonoTouch] 會建立新的頁面，並流覽至根為 "甜點"，並具有具有三個值的選項按鈕。

在此特定範例中，選項按鈕群組會在 "甜點" 區段中選取「巧克力蛋糕」，因為我們已將值 "2" 傳遞給 RadioGroup。 這表示挑選清單中的第三個專案（零索引）。

呼叫 Add 方法或使用C# 4 個初始化運算式語法會加入區段。
提供插入方法來插入含有動畫的區段。

如果您使用群組實例建立 RootElement （而不是 RadioGroup），則在區段中顯示時，RootElement 的摘要值將會是與群組具有相同索引鍵之所有 BooleanElements 和 CheckboxElements 的累計計數。

### <a name="sections"></a>章節

區段是用來將螢幕中的元素群組在一起，而且是 RootElement 的唯一有效直接子系。 區段可以包含任何標準元素，包括新的 RootElements。

區段中的 RootElements 內嵌是用來流覽至新的更深入層級。

區段可以是字串形式的標頭和頁尾，或做為 UIViews。
通常您只會使用字串，但若要建立自訂 Ui，您可以使用任何 UIView 做為頁首或頁尾。 您可以使用字串來建立它們，如下所示：

```csharp
var section = new Section ("Header", "Footer");
```

若要使用 views，只要將 views 傳遞給此函式：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>取得通知

#### <a name="handling-nsaction"></a>處理 NSAction

MT.D 會將 `NSAction` 呈現為處理回呼的委派。
例如，假設您想要處理 MT 所建立之資料表單元格的觸控事件。D. 使用 MT 建立元素時。D，只需提供回呼函式，如下所示：

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>正在抓取元素值

與 `Element.Value` 屬性結合，回呼可以抓取其他元素中設定的值。 例如，請參考下列程式碼：

```csharp
var element = new EntryElement (task.Name, "Enter task description", task.Description);
                
var taskElement = new RootElement (task.Name) {
    new Section () { element },
    new Section () { new DateElement ("Due Date", task.DueDate) },
    new Section ("Demo Retrieving Element Value") {
        new StringElement ("Output Task Description", delegate { Console.WriteLine (element.Value); })
    }
};
```

此程式碼會建立 UI，如下所示。 如需此範例的完整逐步解說，請參閱[ELEMENTS API 逐步](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)解說教學課程。

 [![](images/image6.png "Combined with the Element.Value property, the callback can retrieve the value set in other elements")](images/image6.png#lightbox)

當使用者按下資料表資料格時，匿名函式中的程式碼會執行，並將 `element` 實例中的值寫入 Visual Studio for Mac 中的**應用程式輸出**面板。

## <a name="built-in-elements"></a>內建元素

MT.D 隨附一些內建的資料表資料格專案，稱為元素。
這些元素是用來在資料表單元格中顯示各種不同的類型，例如字串、浮動、日期甚至影像等。 每個元素都會負責適當地顯示資料類型。 例如，布林專案會顯示參數來切換其值。 同樣地，float 元素會顯示滑杆來變更浮點值。

還有更複雜的元素可支援更豐富的資料類型，例如影像和 html。 例如，html 專案會在選取時開啟 UIWebView 以載入網頁，會在資料表單元格中顯示標題。

### <a name="working-with-element-values"></a>使用元素值

用來捕獲使用者輸入的專案會公開一個公開 `Value` 屬性，以保存目前專案的目前值。 當使用者使用應用程式時，它會自動更新。

這是 MonoTouch 中所有元素的行為，但使用者建立的元素並不需要。

### <a name="string-element"></a>String 元素

`StringElement` 會在資料表單元格的左邊顯示標題，並在資料格的右邊顯示字串值。

 [![](images/image7.png "A StringElement shows a caption on the left side of a table cell and the string value on the right side of the cell")](images/image7.png#lightbox)

若要使用 `StringElement` 做為按鈕，請提供委派。

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![](images/image8.png "To use a StringElement as a button, provide a delegate")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>樣式字串元素

`StyledStringElement` 允許使用內建的資料表單元格樣式或自訂格式來呈現字串。

 [![](images/image9.png "A StyledStringElement allows strings to be presented using either built-in table cell styles or with custom formatting")](images/image9.png#lightbox)

`StyledStringElement` 類別衍生自 `StringElement`，但可讓開發人員自訂一些屬性，例如字型、文字色彩、背景儲存格色彩、分行符號模式、要顯示的行數，以及是否應該顯示附件。

### <a name="multiline-element"></a>多行元素

 [![](images/image10.png "Multiline Element")](images/image10.png#lightbox)

### <a name="entry-element"></a>Entry 元素

如名稱所示，`EntryElement`是用來取得使用者輸入。 它支援一般字串或密碼，其中字元會隱藏起來。

 [![](images/image11.png "The EntryElement is used to get user input")](images/image11.png#lightbox)

它會以三個值進行初始化：

- 將向使用者顯示之專案的標題。
- 預留位置文字（這是提供提示給使用者的灰色文字）。 
- 文字的值。

預留位置和值可以是 null。 不過，標題是必要的。

無論何時，存取其 Value 屬性都可以抓取 `EntryElement`的值。

此外，`KeyboardType` 屬性可以在建立時設定為資料輸入所需的鍵盤類型樣式。 這可以用來設定使用 `UIKeyboardType` 值的鍵盤，如下所示：

- 數值
- Phone
- URL
- Email

### <a name="boolean-element"></a>布林值元素

 [![](images/image12.png "Boolean Element")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Checkbox 元素

 [![](images/image13.png "Checkbox Element")](images/image13.png#lightbox)

### <a name="radio-element"></a>單選元素

`RadioElement` 需要在 `RootElement`中指定 `RadioGroup`。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![](images/image14.png "A RadioElement requires a RadioGroup to be specified in the RootElement")](images/image14.png#lightbox)

 `RootElements` 也用來協調無線電元素。 `RadioElement` 成員可以跨越多個區段（例如，執行類似信號音調選取器的專案，並將自訂響鈴音調與系統鈴聲分開）。 [摘要] 視圖會顯示目前選取的選項按鈕。 若要使用這種方式，請使用群組的函式建立 `RootElement`，如下所示：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

`RadioGroup` 中的群組名稱是用來在包含的頁面（如果有的話）中顯示選取的值，而值（在此案例中為零）則是第一個選取專案的索引。

### <a name="badge-element"></a>徽章元素

 [![](images/image15.png "Badge Element")](images/image15.png#lightbox)

### <a name="float-element"></a>Float 元素

 [![](images/image16.png "Float Element")](images/image16.png#lightbox)

### <a name="activity-element"></a>Activity 元素

 [![](images/image17.png "Activity Element")](images/image17.png#lightbox)

### <a name="date-element"></a>Date 元素

 ![](images/image18.png "Date Element")

選取對應至 DateElement 的儲存格時，會顯示日期選擇器，如下所示：

 [![](images/image19.png "When the cell corresponding to the DateElement is selected, a date picker is presented as shown")](images/image19.png#lightbox)

### <a name="time-element"></a>Time 元素

 [![](images/image20.png "Time Element")](images/image20.png#lightbox)

選取對應至 TimeElement 的資料格時，會顯示時間選擇器，如下所示：

 [![](images/image21.png "When the cell corresponding to the TimeElement is selected, a time picker is presented as shown")](images/image21.png#lightbox)

### <a name="datetime-element"></a>DateTime 元素

 [![](images/image22.png "DateTime Element")](images/image22.png#lightbox)

選取對應至 DateTimeElement 的資料格時，會顯示日期時間選擇器，如下所示：

 [![](images/image23.png "When the cell corresponding to the DateTimeElement is selected, a datetime picker is presented as shown")](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 元素

 [![](images/image24.png "HTML Element")](images/image24.png#lightbox)

`HTMLElement` 會在資料表單元格中顯示其 `Caption` 屬性的值。 已選取當，指派給元素的 `Url` 會載入 `UIWebView` 控制項，如下所示：

 [![](images/image25.png "Whe selected, the Url assigned to the element is loaded in a UIWebView control as shown below")](images/image25.png#lightbox)

### <a name="message-element"></a>Message 元素

 [![](images/image26.png "Message Element")](images/image26.png#lightbox)

### <a name="load-more-element"></a>載入更多元素

使用此元素可讓使用者在您的清單中載入更多專案。 您可以自訂一般和載入的標題，以及字型和文字色彩。
`UIActivity` 指標會開始建立動畫，而當使用者按下儲存格時，就會顯示載入標題，然後執行傳遞至此函式的 `NSAction`。 一旦 `NSAction` 中的程式碼完成，`UIActivity` 指示器就會停止動畫，而一般的標題會再次顯示。

### <a name="uiview-element"></a>UIView 元素

此外，您可以使用 `UIViewElement`來顯示任何自訂的 `UIView`。

### <a name="owner-drawn-element"></a>主控描繪元素

此元素必須子類別化，因為它是抽象類別。 您應該覆寫應該傳回專案高度的 `Height(RectangleF bounds)` 方法，以及您應該使用內容和視圖參數，在指定界限內執行所有自訂繪圖的 `Draw(RectangleF bounds, CGContext context, UIView view)`。 這個元素會執行子類別化 `UIView`的繁重工作，並將它放在要傳回的儲存格中，讓您只需要執行兩個簡單的覆寫。 在 `DemoOwnerDrawnElement.cs` 檔案中，您可以在範例應用程式中看到較佳的範例執行。

以下是執行類別的簡單範例：

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
{
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text { get; set; }

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

`JsonElement` 是 `RootElement` 的子類別，可擴充 `RootElement`，以便從本機或遠端 url 載入嵌套子系的內容。

`JsonElement` 是可以用兩種形式具現化的 `RootElement`。 其中一個版本會建立 `RootElement`，以視需要載入內容。 這些是使用 `JsonElement` 在結尾接受額外引數的函式來建立，也就是用來載入內容的 url：

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

另一個表單會從本機檔案或您已剖析的現有 `System.Json.JsonObject` 建立資料：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

如需搭配使用 JSON 與 MT 的詳細資訊。D，請參閱[JSON 元素逐步](https://docs.microsoft.com/xamarin/ios/user-interface/monotouch.dialog/json-element-walkthrough)解說教學課程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>下拉更新支援

 「*提取至*重新整理 *」是一*種視覺效果，最初是在*Tweetie2*應用程式中找到，這在許多應用程式中都是常見的效果。

若要在對話中加入自動的提取重新整理支援，您只需要執行兩個動作：連結事件處理常式，以便在使用者提取資料時收到通知，並在載入資料以回到其預設狀態時通知 `DialogViewController`。

連結通知很簡單;只要連接到 `DialogViewController`上的 `RefreshRequested` 事件，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

然後，在您的方法上 `OnUserRequestedRefresh`，您會將某些資料載入排入佇列、向網路要求一些資料，或微調執行緒來計算資料。 載入資料之後，您必須通知 `DialogViewController` 新資料所在，而若要將此視圖還原為其預設狀態，您可以呼叫 `ReloadComplete`來執行此動作：

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜尋支援

若要支援搜尋，請在您的 `DialogViewController`上設定 [`EnableSearch`] 屬性。 您也可以設定 `SearchPlaceholder` 屬性，做為搜尋列中的浮水印文字。

搜尋會將此視圖的內容變更為使用者類型。 它會搜尋可見欄位，並向使用者顯示這些欄位。 `DialogViewController` 會公開三種方法，以程式設計方式起始、終止或觸發結果的新篩選作業。 這些方法如下所列：

- `StartSearch`
- `FinishSearch`
- `PerformFilter`

系統是可擴充的，因此您可以視需要改變此行為。

### <a name="background-image-loading"></a>背景影像載入

MonoTouch。對話方塊會併入[TweetStation](https://github.com/migueldeicaza/TweetStation)應用程式的映射載入器。 這個影像載入器可以用來在背景載入影像、支援快取，並且可以在載入影像時通知程式碼。

它也會限制傳出的網路連接數目。

映射載入器是在 `ImageLoader` 類別中實作為，您只需要呼叫 `DefaultRequestImage` 方法，就必須提供您想要載入之影像的 Uri，以及當影像時將叫用的 `IImageUpdated` 介面實例。已載入。

例如，下列程式碼會將影像從 Url 載入 `BadgeElement`：

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

當您想要釋放目前在記憶體中快取的所有影像時，ImageLoader 類別會公開可供呼叫的清除方法。 目前的程式碼具有50映射的快取。 如果您想要使用不同的快取大小（例如，如果您預期影像太大，以致于50映射會太多），您可以只建立 ImageLoader 的實例，並傳遞您想要保留在快取中的映射數目。

## <a name="using-linq-to-create-element-hierarchy"></a>使用 LINQ 建立元素階層

透過聰明的 LINQ 和C#初始化語法用法，linq 可以用來建立元素階層架構。 例如，下列程式碼會從某些字串陣列建立畫面，並透過傳遞至每個 `StringElement`的匿名函式來處理資料格選取：

```csharp
var rootElement = new RootElement ("LINQ root element") {
    from x in new string [] { "one", "two", "three" }
    select new Section (x) {
        from y in "Hello:World".Split (':')
        select (Element) new StringElement (y, delegate { Debug.WriteLine("cell tapped"); })
    }
};
```

這可以輕鬆地與 XML 資料存放區或資料庫中的資料結合，以幾乎完全從資料建立複雜的應用程式。

## <a name="extending-mtd"></a>擴充 MT。D

### <a name="creating-custom-elements"></a>建立自訂元素

您可以建立自己的專案，方法是從現有的專案繼承，或從根類別元素衍生。

若要建立您自己的元素，您會想要覆寫下列方法：

```csharp
// To release any heavy resources that you might have
void Dispose (bool disposing);

// To retrieve the UITableViewCell for your element
// you would need to prepare the cell to be reused, in the
// same way that UITableView expects reusable cells to work
UITableViewCell GetCell (UITableView tv);

// To retrieve a "summary" that can be used with
// a root element to render a summary one level up.  
string Summary ();

// To detect when the user has tapped on the cell
void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path);

// If you support search, to probe if the cell matches the user input
bool Matches (string text);
```

如果您的專案可以有變數大小，您必須執行 `IElementSizing` 介面，其中包含一個方法：

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

如果您打算藉由呼叫 `base.GetCell(tv)` 並自訂傳回的儲存格，來執行 `GetCell` 方法，您也必須覆寫 `CellKey` 屬性，以便傳回對元素而言是唯一的索引鍵，如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

這適用于大部分的元素，但不適用於 `StringElement` 和 `StyledStringElement`，因為它們會針對各種轉譯案例使用自己的索引鍵集。 您必須在這些類別中複製程式碼。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers （DVCs）

反映和元素 API 都會使用相同的 `DialogViewController`。 有時候您會想要自訂視圖的外觀，或者您可能想要使用 `UITableViewController` 的部分功能，而不是 Ui 的基本建立。

`DialogViewController` 只是 `UITableViewController` 的子類別，您可以使用自訂 `UITableViewController`的相同方式進行自訂。

例如，如果您想要將清單樣式變更為 `Grouped` 或 `Plain`，您可以在建立控制器時變更屬性來設定此值，如下所示：

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

如需 `DialogViewController`的更先進自訂（例如設定其背景），您可以將它設為子類別，並覆寫適當的方法，如下列範例所示：

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

另一個自訂點是 `DialogViewController`中的下列虛擬方法：

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

如果您的資料格大小平均，這個方法應該會傳回 `DialogViewController.Source` 的子類別，如果您的資料格不平均，則會傳回 `DialogViewController.SizingSource` 的子類別。

您可以使用此覆寫來捕捉任何 `UITableViewSource` 方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation)會使用此來追蹤使用者何時滾動到頂端，並據以更新未讀取的推文數目。

## <a name="validation"></a>驗證

元素本身並不會提供驗證，因為適用于網頁和桌面應用程式的模型不會直接對應到 iPhone 互動模型。

如果您想要進行資料驗證，當使用者使用輸入的資料觸發動作時，您應該執行這種作法。 例如，在頂端工具列上的 [**完成** **] 或 [下一步]** 按鈕，或使用一些 `StringElement` 做為按鈕以移至下一個階段。

這是您在其中執行基本輸入驗證的位置，也可能是更複雜的驗證，例如檢查使用者/密碼與伺服器的組合是否有效。

通知使用者發生錯誤的方式為應用程式特定。 您可以彈出 `UIAlertView` 或顯示提示。

## <a name="summary"></a>總結

本文涵蓋了許多 MonoTouch 的相關資訊。 其中討論了 MT 的基本概念。D 可以運作並涵蓋組成 MT 的各種元件。D. 它也顯示了 MT 支援的各種元素和資料表自訂。D. 和討論 MT 如何。D 可以使用自訂元素來擴充。 此外，它也說明了 MT 中的 JSON 支援。D，允許從 JSON 動態建立元素。

## <a name="related-links"></a>相關連結

- [逐步解說：使用元素 API 建立應用程式](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [逐步解說：使用映射 API 建立應用程式](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [逐步解說：使用 JSON 元素建立使用者介面](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch。對話方塊 JSON 標記](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
