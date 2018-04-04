---
title: MonoTouch.Dialog 簡介
description: MonoTouch.Dialog （機動D） 工具組是快速的應用程式中 Xamarin.iOS UI 開發是不可或缺的架構。 周D 可快速又輕鬆地定義複雜的應用程式使用宣告式方法，而不是導覽控制站、 資料表等 tedium 的 UI。此外，機動D 具有彈性的一組 Api，開發人員提供完整的控制權或交給方法，以及其他功能，例如更新提取，背景影像載入，請搜尋支援和 JSON 資料透過 UI 的動態產生。 這個指南介紹使用機動不同的方式D，然後探討深度進階使用。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: be979b35ffdd597dae74f1f661a381ae44433b10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-monotouchdialog"></a>MonoTouch.Dialog 簡介

_MonoTouch.Dialog （機動D） 工具組是快速的應用程式中 Xamarin.iOS UI 開發是不可或缺的架構。周D 可快速又輕鬆地定義複雜的應用程式使用宣告式方法，而不是導覽控制站、 資料表等 tedium 的 UI。此外，機動D 具有彈性的一組 Api，開發人員提供完整的控制權或交給方法，以及其他功能，例如更新提取，背景影像載入，請搜尋支援和 JSON 資料透過 UI 的動態產生。這個指南介紹使用機動不同的方式D，然後探討深度進階使用。_


MonoTouch.Dialog，稱為機動簡稱，D 是可讓開發人員建置應用程式畫面和使用的詳細資訊，而不是建立檢視控制器、 資料表等 tedium 巡覽的快速 UI 開發工具組。因此，它會提供 UI 開發和程式碼減少大幅簡化。 例如，請考慮下列螢幕擷取畫面：

 [![](images/image1.png "例如，請考慮這個螢幕擷取畫面")](images/image1.png#lightbox)

下列程式碼用來定義此全螢幕：

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

當使用 iOS 中的資料表，通常會有大量的重複程式碼。
例如，每次需要的資料表，資料來源需要填入該資料表。 應用程式中，瀏覽控制器透過連線兩個資料表為基礎的螢幕，每個畫面共用許多相同的程式碼。

周D 簡化，該程式碼的所有封裝泛型 api 針對建立資料表。 然後，它會提供宣告式語法，能夠更輕鬆地繫結物件可讓該 API 之上的抽象概念。 因此，有兩個應用程式開發介面可供使用機動D:

-   **低層級的項目 API** –*元素 API*根據建立階層式樹狀結構表示畫面和其元件的項目。 項目 API 讓開發人員最大的彈性和中建立 Ui 控制項。 此外，項目 API 提供 JSON 非常快速的宣告，以及從伺服器動態 UI 產生可透過進階支援宣告式定義。 
-   **高層級反映 API** – 也稱為*繫結**API*中的類別必須標註 UI 提示，然後機動D 自動建立的物件為基礎的螢幕，並提供項目之間的繫結會顯示 （並選擇性地編輯） 在畫面上，且備份基礎物件。 上述範例說明使用反映 API。 這個 API 不會提供更細微的控制應用程式開發介面的項目存在，但它可減少複雜度更進一步所自動建立項目階層，根據類別屬性。 


周D 來自壓縮的大型集合與內建的畫面建立 UI 項目，但它也會辨識需要自訂的元素及進階的畫面配置。 因此，擴充性是第一級的精選出爐 api。 開發人員可以擴充現有的項目或建立新的和緊密整合。

此外，機動D 的常見的 iOS UX 功能，例如 「 提取更新 」 支援，非同步影像載入時，以及搜尋支援內建的數目。

這篇文章會查看全方位使用機動D，包括：

-   **周D 元件**– 這將著重於了解組成機動類別若要啟用快速開始了解 D。 
-   **項目參考**– 內建項目的周的完整清單D. 
-   **進階使用**– 這涵蓋進階的功能，例如提取-重新整理、 搜尋、 背景影像載入、 使用 LINQ 來建立項目階層，以及建立自訂的項目，資料格，並使用機動的網域控制站的D. 

## <a name="understanding-the-pieces-of-mtd"></a>了解機動的項目D

即使是使用反映 API，機動就如同它已透過建立項目 API 直接 D 會建立在幕後，項目階層架構。 此外，如上一節中所述的 JSON 支援會一併建立項目。 基於這個理由，務必了解基本的構成部分，機動D.

周D 建置螢幕，使用下列四個部分：

-  **DialogViewController**
-  **RootElement**
-  **區段**
-  **目**


### <a name="dialogviewcontroller"></a>DialogViewController

A *DialogViewController*，或*DVC*簡稱，繼承自`UITableViewController`因此代表與資料表的螢幕。 DVCs 可以推就像一般 UITableViewController 導覽控制站。

### <a name="rootelement"></a>RootElement

A *RootElement*是進入 DVC 之項目的上層的容器。 它包含區段，然後可以包含項目。 不會轉譯 RootElements;相反地，變更就只是容器的項目實際取得呈現。 RootElement 指派給 DVC，並接著 DVC 呈現其子系。

### <a name="section"></a>區段

區段是一組資料表中的儲存格。 與一般資料表的區段中，可以選擇性地有的頁首和頁尾，可以是文字或甚至自訂的檢視，如下列螢幕擷取畫面所示：

 [![](images/image2.png "與一般資料表的區段中，可以選擇性地有的頁首和頁尾，可以是文字或甚至自訂的檢視，如下列螢幕擷取畫面所示")](images/image2.png#lightbox)

### <a name="element"></a>項目

項目代表實際的資料格在資料表中。 周D 來自各種不同的項目代表不同的資料類型或不同的輸入與壓縮。 例如，下列螢幕擷取畫面說明幾個可用的項目：

 [![](images/image3.png "例如，此螢幕擷取畫面說明幾個可用的項目")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>在區段和 RootElements

我們現在討論 RootElements 以及各節更詳細地。

### <a name="rootelements"></a>RootElements

至少一個 RootElement 才能啟動 MonoTouch.Dialog 程序。

如果 RootElement/項目值以初始化這個值用來尋找子系項目，將會提供設定，轉譯的顯示畫面右側的摘要。 例如，下列螢幕擷取畫面顯示資料表左側包含詳細資料螢幕的右側，"甜"，與所選沙漠值一起螢幕標題的資料格。

 [![](images/image4.png "這個螢幕擷取畫面顯示在左邊的資料表，以包含詳細資料螢幕的右側，甜，與所選沙漠值一起螢幕標題儲存格")](images/image4.png#lightbox) [ ![ ](images/image5.png "這以下螢幕擷取畫面顯示左邊的資料表，以包含詳細資料螢幕的右側，甜，與所選沙漠值一起螢幕標題儲存格")](images/image5.png#lightbox)

根項目也可用在區段內觸發載入新的巢狀的組態頁面上，如上所示。 在此模式中使用時所提供的標題時呈現的區段內使用，也會當做標題使用的子頁面。 例如: 

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

在上述範例中，當使用者點選"甜 」，在 MonoTouch.Dialog 將建立新的頁面，並瀏覽至它在"甜"且具有選項群組具有三個值的根。

在此特定範例中，選項群組都會選取"甜 」 一節中的 「 巧克力蛋糕"，因為我們傳遞至 RadioGroup 的值"2"。 這表示挑選清單 （零索引） 上的第 3 個項目。

呼叫的 Add 方法或使用 C# 4 初始設定式語法新增區段。
插入方法可供插入具有動畫區段。

如果您建立群組執行個體 （而不是 RadioGroup) RootElement RootElement 區段中顯示時的摘要值會累加計數的所有 BooleanElements 和 CheckboxElements 具有 Group.Key 值與相同的金鑰。

### <a name="sections"></a>章節

區段可用來在螢幕中的群組項目，它們是 RootElement 的唯一有效的直接子系。 區段可以包含任何標準的項目，包括新 RootElements。

RootElements 內嵌於區段中可用來瀏覽至新的更深層級。

為字串，或是為 UIViews 頁首和頁尾可以有區段。
通常您只會使用字串，但若要建立自訂 Ui 您可以使用任何 UIView 頁首或頁尾。 您可以使用字串來建立它們，就像這樣：

```csharp
var section = new Section ("Header", "Footer")
```

若要使用的檢視，只傳遞至建構函式的檢視：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>獲得通知

#### <a name="handling-nsaction"></a>處理 NSAction

周D 介面`NSAction`處理回呼的委派。
例如，假設您想要處理機動所建立的資料表資料格的觸控事件D. 當使用機動建立項目D，只要提供回呼函式，如下所示：

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

此程式碼會建立 UI，如下所示。 這個範例的完整逐步解說，請參閱[元素 API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教學課程。

 [![](images/image6.png "與 Element.Value 屬性結合，回呼可以擷取其他項目中設定的值")](images/image6.png#lightbox)

當使用者按下底部資料表資料格時，匿名函式中的程式碼執行時，寫入的值從`element`執行個體**應用程式輸出**中 Visual Studio for mac。

## <a name="built-in-elements"></a>內建項目

周D 隨附的內建的表格儲存格的項目稱為項目數目。
這些元素用來顯示各種不同類型的資料表資料格，例如字串、 浮點數、 日期和名稱在短短幾甚至的映像中。 每個項目會負責正確地顯示的資料類型。 例如，布林值的項目會顯示，將其值切換參數。 同樣地，float 項目會顯示滑桿來變更浮點值。

有更複雜的項目，以支援更豐富的資料類型，例如影像和 html。 例如，html 項目，就會開啟 UIWebView 載入網頁，選取時，顯示標題中的表格儲存格。

### <a name="working-with-element-values"></a>使用項目值

用來擷取使用者輸入的項目公開公用`Value`屬性可保存的項目目前的值在任何時間。 它會自動更新的因為使用者使用應用程式。

這是所有屬於 MonoTouch.Dialog，項目的行為，但不是必要的使用者建立的項目。

### <a name="string-element"></a>字串項目

A`StringElement`左下方的資料表資料格和資料格右邊的字串值上顯示標題。

 [![](images/image7.png "StringElement 左下方的資料表資料格和資料格右邊的字串值上顯示標題")](images/image7.png#lightbox)

若要使用`StringElement`按鈕，提供委派。

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "若要使用 StringElement 做為按鈕，提供委派")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>已設定樣式的字串項目

A`StyledStringElement`允許字串的形式來呈現使用其中一個內建的表格儲存格樣式或自訂格式。

 [![](images/image9.png "StyledStringElement 允許字串的形式來呈現使用其中一個內建的表格儲存格樣式或自訂格式")](images/image9.png#lightbox)

`StyledStringElement`類別衍生自`StringElement`，但可讓開發人員自訂少數幾個屬性，例如字型、 文字色彩、 背景資料格顏色、 列中斷模式，要顯示的行數，以及是否應該顯示附屬應用程式。

### <a name="multiline-element"></a>多行項目

 [![](images/image10.png "多行項目")](images/image10.png#lightbox)

### <a name="entry-element"></a>項目

`EntryElement`，做為名稱表示，用來取得使用者輸入。 可支援一般的字串或密碼，會隱藏字元。

 [![](images/image11.png "EntryElement 用來取得使用者輸入")](images/image11.png#lightbox)

初始化具有三個值：

-  會向使用者顯示之項目的標題。
-  （這是提供給使用者的提示會呈現灰色外文字） 的預留位置文字。 
-  文字的值。


預留位置和值可以是 null。 不過，標題是必要的。

在任何時間點，在存取它的值屬性可以擷取的值`EntryElement`。

此外`KeyboardType`屬性可以在建立時，想要用於資料輸入鍵盤類型樣式設定。 這可以用來設定鍵盤上使用的值`UIKeyboardType`如下所示：

-  數值
-  電話
-  URL
-  Email


### <a name="boolean-element"></a>布林值項目

 [![](images/image12.png "布林值項目")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>核取方塊項目

 [![](images/image13.png "核取方塊項目")](images/image13.png#lightbox)

### <a name="radio-element"></a>選項元素

A`RadioElement`需要`RadioGroup`中指定`RootElement`。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "RadioElement 需要 RadioGroup 中 RootElement 指定")](images/image14.png#lightbox)

 `RootElements` 也可用來協調選項項目。 `RadioElement`成員可以跨越多個區段 （例如來實作類似的項目環形音調選擇器和不同的自訂鈴聲從系統鈴聲）。 [摘要] 檢視會顯示目前選取的選項項目。 若要使用此功能，建立`RootElement`與群組建構函式，就像這樣：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

在 群組名稱`RadioGroup`用來顯示選取的值中包含的頁面 （如果有的話） 和值，在此情況下為零，這是第一個選取之項目的索引。

### <a name="badge-element"></a>徽章元素

 [![](images/image15.png "徽章元素")](images/image15.png#lightbox)

### <a name="float-element"></a>浮點數的項目

 [![](images/image16.png "浮點數的項目")](images/image16.png#lightbox)

### <a name="activity-element"></a>活動項目

 [![](images/image17.png "活動項目")](images/image17.png#lightbox)

### <a name="date-element"></a>日期的項目

 ![](images/image18.png "日期的項目")

選取對應至 DateElement 的儲存格時，日期選擇器會如下所示：

 [![](images/image19.png "如所示，選取對應至 DateElement 的儲存格時，要呈現日期選擇器")](images/image19.png#lightbox)

### <a name="time-element"></a>Time 元素

 [![](images/image20.png "Time 元素")](images/image20.png#lightbox)

選取對應至 TimeElement 的儲存格時，時間選擇器會如下所示：

 [![](images/image21.png "如所示，選取對應至 TimeElement 的儲存格時，顯示時間選擇器")](images/image21.png#lightbox)

### <a name="datetime-element"></a>日期時間項目

 [![](images/image22.png "日期時間項目")](images/image22.png#lightbox)

選取對應至 DateTimeElement 的儲存格時，日期時間選擇器會如下所示：

 [![](images/image23.png "日期時間選擇器選取 DateTimeElement 相對應的資料格時，會呈現所示")](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 項目

 [![](images/image24.png "HTML 項目")](images/image24.png#lightbox)

`HTMLElement`顯示的值及其`Caption`中資料表資料格屬性。 何時選取，`Url`指派給項目的載入`UIWebView`控制如下所示：

 [![](images/image25.png "何時選取，如下所示 UIWebView 控制項載入指派給項目的 Url")](images/image25.png#lightbox)

### <a name="message-element"></a>訊息項目

 [![](images/image26.png "訊息項目")](images/image26.png#lightbox)

### <a name="load-more-element"></a>載入多個項目

若要允許使用者載入更多的項目在清單中使用這個元素。 您可以自訂法線和載入標題，以及字型和文字色彩。
`UIActivity`指標可讓您啟動建立動畫，並載入標題會顯示當使用者點選的資料格，然後`NSAction`傳遞至建構函式會執行。 一次您的程式碼中`NSAction`完成之後，`UIActivity`指標會停止建立動畫，並會再次顯示一般的標題。

### <a name="uiview-element"></a>UIView 項目

此外，任何自訂`UIView`可以使用顯示`UIViewElement`。

### <a name="owner-drawn-element"></a>為主控描繪項目

這個項目必須子類別，因為它是抽象類別。 您應該覆寫`Height(RectangleF bounds)`方法中，您應該傳回項目的高度，以及`Draw(RectangleF bounds, CGContext context, UIView view)`在其中您應該執行給定的範圍內所有自訂的繪圖使用內容和檢視的參數。 此項目一樣困難的子類別化`UIView`，並將其放在資料格傳回，讓您只需要實作兩個簡單的覆寫。 您可以查看範例應用程式中更好的範例實作`DemoOwnerDrawnElement.cs`檔案。

以下是非常簡單的實作類別的範例：

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

### <a name="json-element"></a>JSON 項目

`JsonElement`是子類別的`RootElement`延伸`RootElement`能夠從本機或遠端 url 載入巢狀的子系的內容。

`JsonElement`是`RootElement`，可以具現化兩種形式。 一個版本建立`RootElement`，將會載入上指定的內容。 這些建立使用`JsonElement`採取額外的引數在結束時，要載入的內容 url 的建構函式：

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

其他格式建立資料從本機檔案或現有`System.Json.JsonObject`已剖析的：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

如需詳細資訊，在 JSON 中使用機動D，請參閱[JSON 項目的逐步解說](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough)教學課程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>若要重新整理提取支援

 *提取-到-* *重新整理*視覺效果原本位於*Tweetie2*應用程式，變成在許多應用程式之間的熱門的效果。

若要將自動重新整理提取到支援加入至您的對話方塊中，您只需要做兩件事： 攔截 (hook) 會提取資料，使用者會收到通知的事件處理常式，並通知`DialogViewController`移回至其預設狀態載入的資料。

通知連結很簡單;只連線到`RefreshRequested`事件`DialogViewController`，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

然後在您的方法`OnUserRequestedRefresh`，會載入某些資料排入佇列，net，向要求某些資料或微調執行緒來計算資料。 一旦已載入的資料，您必須通知`DialogViewController`，新的資料，以還原為其預設狀態的檢視，您可以呼叫`ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜尋支援

若要支援搜尋，`EnableSearch`屬性您`DialogViewController`。 您也可以設定`SearchPlaceholder`来做為浮水印文字搜尋列中的屬性。

搜尋會隨著使用者型別檢視的內容。 它會搜尋在可見欄位，並顯示這些給使用者。 `DialogViewController`會公開三個方法來以程式設計方式起始、 結束處理序或觸發新的篩選作業的結果。 以下列出這些方法：

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


系統，所以如果您想要您可以變更此行為。

### <a name="background-image-loading"></a>背景影像載入

MonoTouch.Dialog 結合[TweetStation](https://github.com/migueldeicaza/TweetStation)應用程式的影像載入程式。 此影像載入程式可以用來載入映像，在背景中，快取支援，並可在載入影像時，通知您的程式碼。

它也會將限制傳出的網路連線的數目。

影像載入程式中實作`ImageLoader`類別，您只需要是呼叫`DefaultRequestImage`方法，您必須提供您想要載入，映像，以及執行個體的 Uri`IImageUpdated`介面將會叫用時影像 has 已載入。

下列程式碼，例如從到的 Url 載入影像`BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

ImageLoader 類別會公開您要發行的所有目前快取在記憶體中的影像時，您可以呼叫的清除方法。 目前的程式碼有 50 的映像快取。 如果您想要使用不同的快取大小，（比方說，如果您預期得太大，50 的映像會是太多的影像），可以只建立 ImageLoader 的執行個體並傳遞您想要保留在快取中的映像數目。

## <a name="using-linq-to-create-element-hierarchy"></a>若要建立項目階層架構中使用 LINQ

LINQ 和 C# 初始化語法聰明使用方式，透過 LINQ 可用來建立項目階層架構。 例如，下列程式碼會從某些字串陣列建立螢幕和控制代碼儲存格選取項目透過匿名函式傳遞至每個`StringElement`:

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

這可以輕鬆地與 XML 資料存放區或幾乎完全從資料建立複雜的應用程式資料庫中的資料結合。

## <a name="extending-mtd"></a>擴充機動D

### <a name="creating-custom-elements"></a>建立自訂的項目

繼承自現有的項目，或由衍生自根類別項目，您可以建立您自己的項目。

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

如果您打算實作您`GetCell`方法藉由呼叫`base.GetCell(tv)`和自訂傳回的儲存格，您必須也覆寫`CellKey`屬性，以傳回可將您的項目，唯一的索引鍵如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

這適用於大部分的項目，但不是適合用於`StringElement`和`StyledStringElement`那些用於各種轉譯案例使用自己的索引鍵集。 您必須要複寫這些類別中的程式碼。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

反映和項目的應用程式開發介面使用相同`DialogViewController`。 有時候您會想要自訂檢視的外觀，或您可能想要使用的某些功能`UITableViewController`，超越基本的 Ui 建立。

`DialogViewController`是只的子類別`UITableViewController`，您可以在自訂的相同方式進行自訂`UITableViewController`。

例如，如果您想要變更清單樣式，可以是`Grouped`或`Plain`，您無法將此值變更的屬性，當您建立的控制站，像這樣：

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

如需其他進階的自訂`DialogViewController`，例如，設定它的背景，就像子類別並覆寫適當的方法，如下列範例所示：

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

另一個的自訂點是在下列虛擬方法`DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

此方法應傳回的子類別`DialogViewController.Source`的情況下，其中平均大小儲存格或子類別`DialogViewController.SizingSource`如果儲存格不一致。

您可以使用此覆寫來擷取任何`UITableViewSource`方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation)會使用此使用者已捲動至頂端時加以追蹤，並據此更新未讀取推文的數目。

## <a name="validation"></a>驗證

項目不提供驗證本身為十分適合網頁的模型，而且桌面應用程式執行未直接對應至 iPhone 互動模型。

如果您想要執行資料驗證，您應該這樣做時使用者輸入的資料會觸發動作。 例如<span class="ui">完成</span>或<span class="ui">下一步</span>頂端的工具列，或某些按鈕`StringElement`做為按鈕移至下一個階段。

這是您會執行基本的輸入的驗證，而且可能更複雜像檢查有效性的使用者/密碼組合與伺服器驗證。

通知使用者發生錯誤的方式是特定的應用程式。 您無法快顯`UIAlertView`或顯示提示。

## <a name="summary"></a>總結

本文涵蓋許多 MonoTouch.Dialog 的相關資訊。 它討論如何的基本概念機動D 的運作方式和涵蓋各種元件組成機動D. 它也顯示的項目和資料表機動所支援的自訂項目D，討論如何機動D 可以延伸與自訂項目。 此外它說明機動的 JSON 支援可讓您動態建立的項目，從 JSON 的 D。


## <a name="related-links"></a>相關連結

- [錄影畫面-Miguel de Icaza iOS 登入畫面以建立 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [錄影畫面-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [逐步解說：使用元素 API 建立應用程式](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [逐步解說：使用映射 API 建立應用程式](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [逐步解說：使用 JSON 元素建立使用者介面](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch.Dialog JSON Markup](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 類別參考](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
