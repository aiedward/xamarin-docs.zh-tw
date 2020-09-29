---
title: 適用于 Xamarin 的 Monotouch.dll 簡介。
description: 本檔說明 Monotouch.dll (MT。D) ，此架構可讓您使用 Xamarin 快速進行宣告式 UI 開發。 它會討論如何使用 Monotouch.dll Api，以程式碼或 JSON 建立介面，並使用提取至重新整理、搜尋、背景影像載入等功能。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: c57a6d02488272934ea77714b07c0328ac501d26
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431207"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>適用于 Xamarin 的 Monotouch.dll 簡介。

Monotouch.dll。對話方塊，稱為 MT。D. 簡單來說，它是一種快速的 UI 開發工具組，可讓開發人員使用資訊來建立應用程式畫面和導覽，而不是建立視圖控制器、資料表等冗長工作。因此，它可大幅簡化 UI 開發和程式碼的縮減。 例如，請考慮下列螢幕擷取畫面：

 [![例如，請考慮此螢幕擷取畫面](images/image1.png)](images/image1.png#lightbox)

下列程式碼是用來定義整個畫面：

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

使用 iOS 中的資料表時，通常會有大量重複性的程式碼。
例如，每次需要資料表時，都需要資料來源來填入該資料表。 在具有兩個透過流覽控制器連接之以資料表為基礎的螢幕上的應用程式中，每個畫面都會共用許多相同的程式碼。

MT.D 可將所有程式碼封裝成一般 API 來建立資料表，藉此簡化這項工作。 然後，它會在該 API 之上提供一個抽象概念，以允許宣告式物件系結語法，讓它更容易。 因此，MT 提供兩種 Api。D：

- **低層級專案 api** –  *元素 api* 是以建立元素的階層樹狀結構為基礎，以代表畫面及其元件。 Elements API 可讓開發人員在建立 Ui 時擁有最大的彈性和控制能力。 此外，Elements API 透過 JSON 支援宣告式定義，可讓您以極快速的方式宣告，以及從伺服器產生動態 UI。 
- **高階反映 api** （也稱為系結*Binding**api* ），其中的類別會以 UI 提示進行批註，然後是 MT。D 會根據物件自動建立畫面，並提供 (所顯示的系結，並選擇性地在螢幕上編輯) ，以及支援基礎物件。   上述範例說明了如何使用反映 API。 此 API 不會提供專案 API 所執行的細部控制項，但它會根據類別屬性自動建立專案階層，進而減少複雜度。 

MT.D 隨附一組大量的內建 UI 元素，可供建立螢幕，但也可辨識自訂元素和 advanced screen 版面配置的需求。 因此，擴充性是在 API 中的頂級精選內建。 開發人員可以擴充現有的元素，或建立新的專案，然後順暢地整合。

此外，MT。D 有一些內建的常見 iOS UX 功能，例如「提取至重新整理」支援、非同步映射載入和搜尋支援。

本文將全面探討如何使用 MT。D，包括：

- **MT。D 元件** –這將著重于瞭解組成 MT 的類別。D 可讓您快速取得速度。 
- **元素參考** – MT 內建元素的完整清單。 
- **Advanced Usage** –這涵蓋了先進的功能，例如，提取至重新整理、搜尋、背景影像載入、使用 LINQ 來建立元素階層，以及建立自訂元素、資料格和控制器來與 MT 一起使用。 

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它，請在 Visual Studio 2017 或 Visual Studio for Mac 中，以滑鼠右鍵按一下 Xamarin 專案的 [ **參考** ] 節點，然後加入 monotouch.dll 的參考 **。對話方塊-1** 元件。 然後， `using MonoTouch.Dialog` 視需要在您的原始程式碼中加入語句。

## <a name="understanding-the-pieces-of-mtd"></a>瞭解 MT 的片段。D

即使是在使用反映 API （MT）。D 會在幕後建立元素階層，就如同直接透過 Elements API 建立一樣。 此外，上一節所述的 JSON 支援也會建立元素。 基於這個理由，請務必對 MT 的組成部分有基本的瞭解。

MT.D 使用下列四個部分來建立畫面：

- **DialogViewController**
- **RootElement**
- **小節**
- **Element**

### <a name="dialogviewcontroller"></a>DialogViewController

*DialogViewController*（簡稱*DVC* ）會繼承自， `UITableViewController` 因此代表具有資料表的畫面。 DVCs 可以推送至流覽控制器，就像一般 UITableViewController 一樣。

### <a name="rootelement"></a>RootElement

*RootElement*是進入 DVC 之專案的最上層容器。 它包含可包含元素的區段。 RootElements 不會呈現;相反地，它們只是實際呈現內容的容器。 RootElement 會指派給 DVC，然後 DVC 會呈現其子系。

### <a name="section"></a>區段

區段是資料表中的一組資料格。 如同一般資料表區段，它可以選擇性地擁有可以是文字或甚至是自訂視圖的頁首和頁尾，如下列螢幕擷取畫面所示：

 [![如同一般資料表區段，它可以選擇性地擁有可以是文字或甚至是自訂視圖的頁首和頁尾，如下列螢幕擷取畫面所示](images/image2.png)](images/image2.png#lightbox)

### <a name="element"></a>項目

元素代表資料表中的實際資料格。 MT.D 隨附各種不同的元素，這些元素代表不同的資料類型或不同的輸入。 例如，下列螢幕擷取畫面說明一些可用的元素：

 [![例如，此螢幕擷取畫面說明一些可用的元素](images/image3.png)](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>深入瞭解區段和 RootElements

現在讓我們更詳細地討論 RootElements 和區段。

### <a name="rootelements"></a>RootElements

至少需要一個 RootElement，才能啟動 Monotouch.dll。對話進程。

如果以區段/專案值初始化 RootElement，則會使用這個值來尋找子專案，以提供設定的摘要，此設定會在顯示的右側呈現。 例如，下列螢幕擷取畫面顯示左邊的表格，其中包含右邊詳細資料畫面標題的資料格 "甜點"，以及所選取 desert 的值。

 [ ![ 這個螢幕擷取畫面會在左邊顯示一個資料表，其中包含右邊的詳細資料畫面標題甜點，以及所選取 desert 的值](images/image4.png)](images/image4.png#lightbox)。 [ ![ 下面的螢幕擷取畫面顯示左邊有一個資料表，其中包含右邊的詳細資料畫面標題、甜點，以及所選 desert 的值](images/image5.png)](images/image5.png#lightbox)

根項目也可以用在區段內，以觸發載入新的嵌套設定頁面，如上所示。 在此模式中使用時，會在呈現于區段內時使用提供的標題，而且也會當做子頁面的標題使用。 例如：

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

在上述範例中，當使用者按下 [甜點] 時，Monotouch.dll 將會建立新的頁面，並以根目錄為 "甜點" 的方式流覽至該頁面，並讓一個具有三個值的電臺群組。

在這個特定的範例中，因為我們將值 "2" 傳遞給 RadioGroup，所以選項按鈕會在 "甜點" 區段中選取「巧克力蛋糕」。 這表示挑選清單中的第三個專案 (零索引) 。

呼叫 Add 方法或使用 c # 4 初始化運算式語法會加入區段。
系統會提供插入方法來插入具有動畫的區段。

如果您使用群組 (實例來建立 RootElement，而不是使用 RadioGroup) 則在區段中顯示時，RootElement 的摘要值會是與群組相同索引鍵之所有 BooleanElements 和 CheckboxElements 的累計計數。

### <a name="sections"></a>章節

區段是用來將畫面中的元素群組在一起，而且它們是 RootElement 的唯一有效直接子系。 區段可以包含任何標準元素，包括新的 RootElements。

RootElements 內嵌于區段中，用來流覽至新的更深層層級。

區段可以有字串或 UIViews 形式的標頭和頁尾。
通常您只會使用字串，但若要建立自訂 Ui，您可以使用任何 UIView 做為頁首或頁尾。 您可以使用字串來建立它們，如下所示：

```csharp
var section = new Section ("Header", "Footer");
```

若要使用視圖，只需將 views 傳遞給函式：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>取得通知

#### <a name="handling-nsaction"></a>處理 NSAction

MT.D 會將 `NSAction` 做為處理回呼的委派。
例如，假設您想要為 MT 所建立的資料表單元格處理觸控事件。 使用 MT 建立元素時。D，只要提供回呼函數，如下所示：

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>正在抓取元素值

與屬性結合之後 `Element.Value` ，回呼可以取得在其他元素中設定的值。 例如，請參考下列程式碼：

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

此程式碼會建立 UI，如下所示。 如需此範例的完整逐步解說，請參閱 [ELEMENTS API 逐步](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) 解說教學課程。

 [![與 Element. Value 屬性結合之後，回呼可以取得在其他元素中設定的值](images/image6.png)](images/image6.png#lightbox)

當使用者按下資料表資料格時，會執行匿名函式中的程式碼，將實例中的值寫入 `element` Visual Studio for Mac 中的 **應用程式輸出** 填充碼。

## <a name="built-in-elements"></a>內建元素

MT.D 隨附一些內建的資料表單元格專案，稱為元素。
這些專案是用來在資料表資料格中顯示各種不同的類型，例如字串、浮動、日期甚至是影像等。 每個元素都能妥善地顯示資料類型。 例如，布林值元素將會顯示參數來切換其值。 同樣地，float 元素會顯示滑杆來變更浮點值。

有更複雜的元素可支援更豐富的資料類型，例如影像和 html。 例如，html 元素（將開啟 UIWebView 以載入網頁時），會在資料表單元格中顯示標題。

### <a name="working-with-element-values"></a>使用元素值

用來捕捉使用者輸入的專案會公開公用 `Value` 屬性，此屬性會隨時保留專案目前的值。 當使用者使用應用程式時，它會自動更新。

這是 Monotouch.dll 中所有專案的行為，但使用者建立的元素則不需要。

### <a name="string-element"></a>String 元素

`StringElement`顯示資料表資料格左側的標題，以及資料格右邊的字串值。

 [![StringElement 會在資料表資料格的左側顯示標題，並在資料格的右邊顯示字串值](images/image7.png)](images/image7.png#lightbox)

若要使用 `StringElement` 做為按鈕，請提供委派。

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![若要使用 StringElement 做為按鈕，請提供委派](images/image8.png)](images/image8.png#lightbox)

### <a name="styled-string-element"></a>樣式的字串元素

`StyledStringElement`允許使用內建資料表單元格樣式或自訂格式來顯示字串。

 [![StyledStringElement 可讓您使用內建的資料表單元格樣式或自訂格式來呈現字串](images/image9.png)](images/image9.png#lightbox)

`StyledStringElement`類別衍生自 `StringElement` ，但可讓開發人員自訂幾個屬性，例如字型、文字色彩、背景儲存格色彩、換行模式、要顯示的行數，以及是否應顯示配件。

### <a name="multiline-element"></a>多行元素

 [![多行元素](images/image10.png)](images/image10.png#lightbox)

### <a name="entry-element"></a>Entry 元素

`EntryElement`如名稱所示，是用來取得使用者輸入。 它支援可隱藏字元的一般字串或密碼。

 [![EntryElement 用來取得使用者輸入](images/image11.png)](images/image11.png#lightbox)

它會以三個值初始化：

- 將向使用者顯示之專案的標題。
- 預留位置文字 (這是可提供提示給使用者) 的灰色文字。 
- 文字的值。

預留位置和值可以是 null。 但需要標題。

在任何時間點，存取其 Value 屬性都可以取出的值 `EntryElement` 。

此外，您 `KeyboardType` 可以在建立時將屬性設定為資料輸入所需的鍵盤類型樣式。 您可以使用下列的值來設定鍵盤 `UIKeyboardType` ：

- 數值
- 電話
- Url
- Email

### <a name="boolean-element"></a>布林值元素

 [![布林值元素](images/image12.png)](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Checkbox 元素

 [![Checkbox 元素](images/image13.png)](images/image13.png#lightbox)

### <a name="radio-element"></a>單選元素

`RadioElement`需要 `RadioGroup` 在中指定 `RootElement` 。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![RadioElement 需要在 RootElement 中指定 RadioGroup](images/image14.png)](images/image14.png#lightbox)

 `RootElements` 也可用來協調單選元素。 這些 `RadioElement` 成員可以橫跨多個區段 (例如，執行類似于鈴聲選取器的內容，並將自訂響鈴聲從系統鈴聲) 中分離出來。 摘要視圖會顯示目前所選取的選項按鈕。 若要使用這種方式，請 `RootElement` 使用群組的函式建立，如下所示：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

中群組的名稱 `RadioGroup` 是用來在 [包含] 頁面中顯示選取的值 (如果有任何) 和值（在此案例中為零）是第一個選取專案的索引。

### <a name="badge-element"></a>徽章元素

 [![徽章元素](images/image15.png)](images/image15.png#lightbox)

### <a name="float-element"></a>Float 元素

 [![Float 元素](images/image16.png)](images/image16.png#lightbox)

### <a name="activity-element"></a>Activity 元素

 [![Activity 元素](images/image17.png)](images/image17.png#lightbox)

### <a name="date-element"></a>Date 元素

 ![Date 元素](images/image18.png)

選取對應于 DateElement 的資料格時，會顯示日期選擇器，如下所示：

 [![選取對應于 DateElement 的儲存格時，會顯示日期選擇器，如下所示](images/image19.png)](images/image19.png#lightbox)

### <a name="time-element"></a>Time 元素

 [![Time 元素](images/image20.png)](images/image20.png#lightbox)

選取對應于 TimeElement 的資料格時，會顯示時間選擇器，如下所示：

 [![選取對應于 TimeElement 的儲存格時，會顯示時間選擇器，如下所示](images/image21.png)](images/image21.png#lightbox)

### <a name="datetime-element"></a>DateTime 元素

 [![DateTime 元素](images/image22.png)](images/image22.png#lightbox)

選取對應于 DateTimeElement 的資料格時，會顯示日期時間選擇器，如下所示：

 [![選取對應于 DateTimeElement 的資料格時，會顯示日期時間選擇器，如下所示](images/image23.png)](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 元素

 [![HTML 元素](images/image24.png)](images/image24.png#lightbox)

會在 `HTMLElement` `Caption` 資料表單元格中顯示其屬性的值。 當選取時， `Url` 指派給元素的會載入至 `UIWebView` 控制項，如下所示：

 [![當選取時，指派給元素的 Url 會載入 UIWebView 控制項中，如下所示](images/image25.png)](images/image25.png#lightbox)

### <a name="message-element"></a>Message 元素

 [![Message 元素](images/image26.png)](images/image26.png#lightbox)

### <a name="load-more-element"></a>載入更多元素

使用此元素可讓使用者在您的清單中載入更多專案。 您可以自訂一般和載入的標題，以及字型和文字色彩。
`UIActivity`指標會開始建立動畫，而且當使用者按下儲存格時，就會顯示載入的標題，然後 `NSAction` 執行傳入的函式。 當您的程式碼 `NSAction` 完成後， `UIActivity` 指標會停止製作動畫，並再次顯示一般的標題。

### <a name="uiview-element"></a>UIView 元素

此外，您 `UIView` 也可以使用來顯示任何自訂 `UIViewElement` 。

### <a name="owner-drawn-element"></a>擁有者繪製的元素

此元素必須是子類別，因為它是抽象類別。 您應該覆寫 `Height(RectangleF bounds)` 應該傳回元素高度的方法，也就是 `Draw(RectangleF bounds, CGContext context, UIView view)` 使用內容和 view 參數，在指定的界限內進行所有的自訂繪圖。 這個元素會執行子類別化的繁重工作 `UIView` ，並將它放在要傳回的儲存格中，讓您只需要執行兩個簡單的覆寫。 您可以在檔案的範例應用程式中看到更好的範例執行 `DemoOwnerDrawnElement.cs` 。

以下是實作為類別的簡單範例：

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

`JsonElement`是的子類別 `RootElement` ，它會擴充， `RootElement` 以便從本機或遠端 url 載入嵌套子系的內容。

`JsonElement` `RootElement` 可以用兩種形式具現化。 其中一個版本 `RootElement` 會建立，以視需要載入內容。 這些都是使用在結尾採用額外引數的函式所建立，也就是用 `JsonElement` 來載入內容的 url：

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

另一種形式會從本機檔案或您已剖析的現有中建立資料 `System.Json.JsonObject` ：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

如需搭配使用 JSON 與 MT 的詳細資訊。D，請參閱 [JSON 元素逐步](./json-element-walkthrough.md) 解說教學課程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>提取至重新整理支援

 *提取至*重新 *整理是一種視覺* 效果，最初是在 *Tweetie2* 應用程式中發現，這在許多應用程式中都是普遍的效果。

若要在您的對話中新增自動提取至重新整理支援，您只需要執行兩項作業：當使用者提取資料時，請連接事件處理常式，並在 `DialogViewController` 載入資料時通知，以返回其預設狀態。

連結通知很簡單;只需要連接到 `RefreshRequested` 上的事件 `DialogViewController` ，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

然後，在您的方法 `OnUserRequestedRefresh` 中，您會將一些資料載入排入佇列、向網路要求一些資料，或微調執行緒以計算資料。 載入資料之後，您必須通知 `DialogViewController` 新資料已在中，若要將視圖還原為預設狀態，您可以藉由呼叫下列方法來執行此動作 `ReloadComplete` ：

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜尋支援

若要支援搜尋，請 `EnableSearch` 在上設定屬性 `DialogViewController` 。 您也可以將 `SearchPlaceholder` 屬性設定為在搜尋列中用來做為浮水印文字。

搜尋會將視圖的內容變更為使用者類型。 它會搜尋可見的欄位，並向使用者顯示這些欄位。 會 `DialogViewController` 公開三個方法，以程式設計方式起始、終止或觸發結果的新篩選作業。 這些方法如下所示：

- `StartSearch`
- `FinishSearch`
- `PerformFilter`

系統是可擴充的，因此您可以視需要變更此行為。

### <a name="background-image-loading"></a>背景影像載入

Monotouch.dll 會併入 [TweetStation](https://github.com/migueldeicaza/TweetStation) 應用程式的影像載入器。 此映射載入器可以用來在背景中載入影像、支援快取，並可在載入映射時通知您的程式碼。

它也會限制連出網路連接的數目。

映射載入器會在類別中執行 `ImageLoader` ，您只需要呼叫 `DefaultRequestImage` 方法，您就必須提供要載入之映射的 Uri，以及在 `IImageUpdated` 載入映射時所要叫用介面的實例。

例如，下列程式碼會從 Url 將影像載入至 `BadgeElement` ：

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

ImageLoader 類別會公開清除方法，當您想要釋放目前在記憶體中快取的所有影像時，您可以呼叫此方法。 目前的程式碼具有50影像的快取。 如果您想要使用不同的快取大小 (比方說，如果您預期影像可能太大而無法) 50 的影像太大，您可以只建立 ImageLoader 的實例，並傳遞您要保留在快取中的影像數目。

## <a name="using-linq-to-create-element-hierarchy"></a>使用 LINQ 建立元素階層

透過智慧型使用 LINQ 和 c # 的初始化語法，LINQ 可以用來建立元素階層。 例如，下列程式碼會從某個字串陣列建立畫面，並透過傳入每個的匿名函式來處理資料格選取 `StringElement` ：

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

您可以建立自己的專案，方法是繼承自現有的元素，或衍生自根類別專案。

若要建立您自己的元素，您需要覆寫下列方法：

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

如果您的元素可以有變數大小，您需要執行 `IElementSizing` 包含一個方法的介面：

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

如果您打算藉 `GetCell` 由呼叫 `base.GetCell(tv)` 和自訂傳回的資料格來執行方法，您也必須覆寫 `CellKey` 屬性以傳回對您專案而言是唯一的索引鍵，如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

這適用于大部分的專案，但不適用於和，因為它們在各種轉譯案例中都 `StringElement` `StyledStringElement` 使用自己的索引鍵集。 您必須複製這些類別中的程式碼。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs) 

反映和元素 API 都使用相同的 `DialogViewController` 。 有時候您會想要自訂視圖的外觀，或者您可能會想要使用 `UITableViewController` 超越 ui 基本建立的功能。

`DialogViewController`只是的子類別 `UITableViewController` ，您可以使用自訂的相同方式來自訂它 `UITableViewController` 。

例如，如果您想要將清單樣式變更為 `Grouped` 或 `Plain` ，您可以在建立控制器時變更屬性來設定此值，如下所示：

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

如需更先進的自訂 `DialogViewController` ，例如設定其背景，您會將它子類別化並覆寫適當的方法，如下列範例所示：

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

另一個自訂點是中的下列虛擬方法 `DialogViewController` ：

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

如果您的資料格是平均大小的案例，這個方法應該會傳回的子類別 `DialogViewController.Source` ，如果您的資料格不平均，則傳回的子類別 `DialogViewController.SizingSource` 。

您可以使用此覆寫來捕捉任何 `UITableViewSource` 方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation) 會使用此項來追蹤使用者何時滾動至頂端，並據此更新未讀取的推文數目。

## <a name="validation"></a>驗證

元素不會自行提供驗證，因為適用于網頁和桌面應用程式的模型不會直接對應至 iPhone 互動模型。

如果您想要進行資料驗證，則當使用者使用輸入的資料觸發動作時，您應該執行此動作。 例如，在頂端工具列上的 [ **完成** **] 或 [下一步]** 按鈕，或某些 `StringElement` 用來作為按鈕的按鈕，以移至下一個階段。

您可以在這裡執行基本輸入驗證，而且可能會有更複雜的驗證，例如檢查使用者/密碼與伺服器的有效性。

如何通知使用者發生錯誤的方法是應用程式特有的。 您可以彈出 `UIAlertView` 或顯示提示。

## <a name="summary"></a>摘要

本文涵蓋 Monotouch.dll 的許多相關資訊。 其中討論了 MT 的基本概念。D 可正常運作，並涵蓋組成 MT 的各種元件。 它也示範了 MT 所支援的元素和資料表自訂範圍的廣泛陣列。D 和討論了 MT 如何。您可以使用自訂元素來延伸 D。 此外，它也說明了 MT 中的 JSON 支援。D 可讓您從 JSON 動態建立元素。

## <a name="related-links"></a>相關連結

- [逐步解說：使用元素 API 建立應用程式](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [逐步解說：使用映射 API 建立應用程式](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [逐步解說：使用 JSON 元素建立使用者介面](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Monotouch.dll。對話方塊 JSON 標記](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 Monotouch.dll 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)