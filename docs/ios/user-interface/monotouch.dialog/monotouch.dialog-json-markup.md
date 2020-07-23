---
title: MonoTouch.Dialog Json 標記
description: 本檔說明可以用來建立使用 MonoTouch 之 Xamarin iOS 使用者介面的 JSON 語法。
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 023a85451ca83df6c15e8b3bbc3169f2884a0a46
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936561"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json 標記

此頁面說明 MonoTouch 所接受的 Json 標記。對話方塊的[JsonElement](xref:MonoTouch.Dialog.JsonElement)

讓我們從範例開始。 以下是可傳遞至 JsonElement 的完整 Json 檔案。

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

上述標記會產生下列 UI：

 [![指定標記所建立的 UI](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png)](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

樹狀結構中的每個元素都可以包含屬性 `"id"` 。 您可以在執行時間使用 JsonElement 索引子來參考個別區段或元素。 例如：

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax"></a>

## <a name="root-element-syntax"></a>根項目語法

根項目包含下列值：

- `title`
- `sections` (選擇性)

根項目可以出現在區段內，做為建立嵌套控制器的元素。 在此情況下，必須將額外的屬性 `"type"` 設定為`"root"`

 <a name="url"></a>

### <a name="url"></a>url

如果 `"url"` 已設定屬性，則當使用者按下此 RootElement 時，程式碼將會從指定的 url 要求檔案，並讓內容顯示新的資訊。 您可以使用這個來根據使用者所按的內容，從伺服器擴充使用者介面。

 <a name="group"></a>

### <a name="group"></a>group

如果設定，這會設定根項目的組名。 組名是用來挑選摘要，其會顯示為元素中其中一個嵌套元素的根項目值。 這可能是核取方塊的值或選項按鈕的值。

 <a name="radioselected"></a>

### <a name="radioselected"></a>radioselected

識別在 [嵌套專案] 中選取的選項按鈕

 <a name="title"></a>

### <a name="title"></a>title

如果存在，則會是用於 RootElement 的標題

 <a name="type"></a>

### <a name="type"></a>類型

`"root"`當出現在區段中時，必須設為（用於嵌套控制器）。

 <a name="sections"></a>

### <a name="sections"></a>區段

這是具有個別區段的 Json 陣列

 <a name="Section_Syntax"></a>

## <a name="section-syntax"></a>區段語法

區段包含：

- `header` (選擇性)
- `footer` (選擇性)
- `elements` 陣列

 <a name="header"></a>

### <a name="header"></a>header

如果有，則標頭文字會顯示為區段的標題。

 <a name="footer"></a>

### <a name="footer"></a>頁尾

如果有的話，頁尾會顯示在區段底部。

 <a name="elements"></a>

### <a name="elements"></a>元素

這是元素的陣列。 每個元素都必須包含至少一個金鑰， `"type"` 也就是用來識別要建立之專案類型的索引鍵。
某些元素會共用一些通用屬性，例如 `"caption"` 和 `"value"` 。 以下是支援的元素清單：

- `string`元素（包含和不含樣式）
- `entry`行（一般或密碼）
- `boolean`值（使用參數或影像）

字串元素可以做為按鈕使用，方法是提供在使用者點擊儲存格或附件時叫用的方法。

 <a name="Rendering_Elements"></a>

## <a name="rendering-elements"></a>轉譯元素

轉譯專案是以 c # StringElement 和 StyledStringElement 為基礎，而且可以各種方式轉譯資訊，而且可能會以各種不同的方式呈現。 最簡單的元素可以建立如下：

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

這會顯示一個簡單字串，其中包含所有預設值：字型、背景、文字色彩和裝飾。 您可以將動作連結至這些專案，並藉由設定屬性或屬性，使其行為類似按鈕 `"ontap"` `"onaccessorytap"` ：

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

上述將會叫用類別 "Acme. PhotoLibrary" 中的 "ShowPhotos" 方法。 `"onaccessorytap"`類似，但只有在使用者點擊附件而不是在儲存格上時，才會叫用。 若要啟用此動作，您也必須設定此附件：

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

轉譯元素可以同時顯示兩個字串，其中一個是標題，另一個則是值。 這些字串的轉譯方式取決於樣式，您可以使用屬性來進行設定 `"style"` 。 預設值將會在左邊顯示標題，並在右側顯示值。 如需詳細資訊，請參閱樣式一節。 色彩的編碼方式是使用 ' # ' 符號，後面接著十六進位數位，代表紅色、綠色、藍色和可能 Alpha 值的值。 內容可以用簡短形式（3或4個十六進位數位）編碼，代表 RGB 或 RGBA 值。 或是代表 RGB 或 RGBA 值的長格式（6或8位數）。 簡短版本是寫入相同十六進位數位兩次的縮寫。 因此 "#1bc" 常數會把為 red = 0x11，綠色 = 0xbb 和 blue = 0xcc。 如果 Alpha 值不存在，則色彩是不透明的。 以下是一些範例：

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory"></a>

### <a name="accessory"></a>配件

決定要在轉譯專案中顯示的附件類型，可能的值如下：

- `checkmark`
- `detail-disclosure`
- `disclosure-indicator`

如果值不存在，則不會顯示任何附件

 <a name="background"></a>

### <a name="background"></a>背景資訊

Background 屬性會設定儲存格的背景色彩。 值是影像的 URL （在此情況下，將會叫用非同步映射下載程式，並在下載影像後更新背景），或者可以是使用色彩語法指定的色彩。

 <a name="caption"></a>

### <a name="caption"></a>caption

要在轉譯元素上顯示的主要字串。 您可以藉由設定和屬性來自訂字型和色彩 `"textcolor"` `"font"` 。 轉譯樣式是由屬性所決定 `"style"` 。

 <a name="color_and_detailcolor"></a>

### <a name="color-and-detailcolor"></a>色彩和 detailcolor

要用於主要文字或詳細文字的色彩。

 <a name="detailfont_and_font"></a>

### <a name="detailfont-and-font"></a>detailfont 和字型

用於標題或詳細資料文字的字型。 字型規格的格式是字型名稱，並選擇性地加上虛線和點大小。
以下是有效的字型規格：

- Helvetica
- "Helvetica-14"

 <a name="linebreak"></a>

### <a name="linebreak"></a>linebreak

決定如何分解行。 可能的值包括：

- `character-wrap`
- `clip`
- `head-truncation`
- `middle-truncation`
- `tail-truncation`
- `word-wrap`

`character-wrap`和都 `word-wrap` 可以與設定為零的屬性一起使用，以將轉譯專案 `"lines"` 轉換成多行元素。

 <a name="ontap_and_onaccessorytap"></a>

### <a name="ontap-and-onaccessorytap"></a>ontap 和 onaccessorytap

這些屬性必須指向您的應用程式中的靜態方法名稱，以接受物件做為參數。 當您使用 JsonDialog. FromFile 或 JsonDialog 方法建立階層時，您可以傳遞選擇性的物件值。 這個物件值接著會傳遞至您的方法。 您可以使用這個來傳遞一些內容給您的靜態方法。 例如：

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines"></a>

### <a name="lines"></a>lines

如果此值設定為零，則會根據包含的字串內容，使元素變成自動大小。 若要讓此作業正常，您也必須將 `"linebreak"` 屬性設定為 `"character-wrap"` 或 `"word-wrap"` 。

 <a name="style"></a>

### <a name="style"></a>樣式

樣式會決定要用來呈現內容的儲存格樣式類型，並對應至 UITableViewCellStyle 列舉值。
可能的值包括：

- `"default"`
- `"value1"`
- `"value2"`
- `"subtitle"`：具有子標題的文字。

 <a name="subtitle"></a>

### <a name="subtitle"></a>subtitle

要用於子標題的值。 這是將樣式設定為 `"subtitle"` ，並將屬性設定為字串的快捷方式 `"value"` 。
這會使用單一專案來執行。

 <a name="textcolor"></a>

### <a name="textcolor"></a>textcolor

要用於文字的色彩。

 <a name="value"></a>

### <a name="value"></a>value

要顯示在轉譯元素上的次要值。 此設定會影響此 `"style"` 配置。 您可以藉由設定和自訂字型和 `"detailfont"` 色彩 `"detailcolor"` 。

 <a name="Boolean_Elements"></a>

## <a name="boolean-elements"></a>布林值元素

布林值元素應將類型設定為 `"bool"` 、可以包含 `"caption"` 要顯示的，而且 `"value"` 會設定為 true 或 false。 如果 `"on"` `"off"` 已設定和屬性，則會假設為影像。 映射會相對於應用程式中目前的工作目錄來解析。 如果您想要參考配套相關的檔案，您可以使用 `"~"` 做為代表應用程式套件組合目錄的快捷方式。 例如， `"~/favorite.png"` 將會是包含在組合檔案中的 favorite.png。 例如：

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type"></a>

### <a name="type"></a>類型

類型可以設定為 `"boolean"` 或 `"checkbox"` 。 如果設定為布林值，則會使用 UISlider 或影像（如果 `"on"` 同時 `"off"` 設定和）。 如果設定為 checkbox，則會使用 checkbox。 `"group"`屬性可以用來標記屬於特定群組的布林專案。 如果包含的根也具有 `"group"` 屬性做為根，將會以屬於相同群組的所有布林值（或核取方塊）的計數來匯總結果，這會很有用。

 <a name="Entry_Elements"></a>

## <a name="entry-elements"></a>Entry 元素

您可以使用 entry 元素，讓使用者輸入資料。 Entry 元素的類型為 `"entry"` 或 `"password"` 。 `"caption"`屬性會設定為要在右側顯示的文字，而 `"value"` 會設定為初始值以將專案設定為。 `"placeholder"`是用來向使用者顯示空白專案的提示（顯示為灰色）。 這裡有一些範例：

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect"></a>

### <a name="autocorrect"></a>校正

決定要用於專案的自動校正樣式。 可能的值為 true 或 false （或字串 `"yes"` 和 `"no"` ）。

 <a name="capitalization"></a>

### <a name="capitalization"></a>大小寫

要用於專案的大小寫樣式。 可能的值包括：

- `all`
- `none`
- `sentences`
- `words`

 <a name="caption"></a>

### <a name="caption"></a>caption

要用於專案的標題

 <a name="keyboard"></a>

### <a name="keyboard"></a>鍵盤

要用於資料輸入的鍵盤類型。 可能的值包括：

- `ascii`
- `decimal`
- `default`
- `email`
- `name`
- `numbers`
- `numbers-and-punctuation`
- `twitter`
- `url`

 <a name="placeholder"></a>

### <a name="placeholder"></a>預留位置 (placeholder)

當專案具有空白值時所顯示的提示文字。

 <a name="return-key"></a>

### <a name="return-key"></a>return 鍵

用於傳回索引鍵的標籤。 可能的值包括：

- `default`
- `done`
- `emergencycall`
- `go`
- `google`
- `join`
- `next`
- `route`
- `search`
- `send`
- `yahoo`

 <a name="value"></a>

### <a name="value"></a>value

專案的初始值

 <a name="Radio_Elements"></a>

## <a name="radio-elements"></a>單選元素

單選元素的類型為 `"radio"` 。 所選取的專案是由 `radioselected` 其包含根項目的屬性所選取。
此外，如果設定了屬性的值 `"group"` ，這個選項按鈕就屬於該群組。

 <a name="Date_and_Time_Elements"></a>

## <a name="date-and-time-elements"></a>日期和時間元素

專案類型 `"datetime"` `"date"` 和 `"time"` 會用來轉譯具有時間、日期或時間的日期。 這些元素會以標題和值做為參數。 此值可以使用 .NET DateTime. Parse 函式所支援的任何格式來撰寫。 範例：

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element"></a>

## <a name="htmlweb-element"></a>Html/Web 元素

當按下時，您可以建立資料格，以使用類型來內嵌呈現指定 URL 內容（本機或遠端）的 UIWebView `"html"` 。 這個元素的唯一兩個屬性是 `"caption"` 和 `"url"` ：

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
