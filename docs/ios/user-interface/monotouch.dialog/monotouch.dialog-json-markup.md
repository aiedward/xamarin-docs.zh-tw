---
title: MonoTouch.Dialog Json Markup
ms.topic: article
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f784497173db6bc3ffa87617765e63fc8d904e5f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json Markup

此頁面描述接受 MonoTouch.Dialog 的 Json 標記[JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

讓我們開始著手。 以下是完整的 Json 檔案，可傳遞到 JsonElement。

```csharp
{     
  "title": "Json Sample",
  "sections": [ 
      {
          "header": "Booleans",
          "footer": "Slider or image-based",
          "id": "first-section",
          "elements": [
              { 
                  "type" : "boolean",
                  "caption" : "Demo of a Boolean",
                  "value"   : true
              }, {
                  "type": "boolean",
                  "caption" : "Boolean using images",
                  "value"   : false,
                  "on"      : "favorite.png",
                  "off"     : "~/favorited.png"
              }, {
                      "type": "root",
                      "title": "Tap for nested controller",
                      "sections": [ {
                         "header": "Nested view!",
                         "elements": [
                           {
                             "type": "boolean",
                             "caption": "Just a boolean",
                             "id": "the-boolean",
                             "value": false
                           },
                           {
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

上述的標記會產生下列 UI:

 [ ![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "建立所指定標記的 UI")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png)

在樹狀目錄中的每個項目可以包含屬性`"id"`。 在執行階段可能會參考個別章節或使用 JsonElement 索引子的項目。 與下列類似：

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>根項目語法

根項目包含下列值：

-  `title`
-  `sections` (選擇性)


根項目可以出現的項目中建立巢狀的控制 > 一節。 在此情況下，額外的屬性`"type"`必須設為 `"root"`

 <a name="url" />


### <a name="url"></a>URL

如果`"url"`屬性設定，如果使用者點選這個 RootElement 上時，此程式碼會從指定的 url 要求檔案，並將內容顯示的新資訊。 這個函數可用來建立從伺服器會根據使用者的點選延伸使用者介面。

 <a name="group" />


### <a name="group"></a>群組

如果設定，這會設定為根項目群組名稱。 群組名稱會用來取得摘要顯示為其中一個巢狀項目，項目中的根元素的值。 這是一個核取方塊的值或選項按鈕的值。

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

識別巢狀項目中選取選項項目

 <a name="title" />


### <a name="title"></a>標題

如果有的話，它將會用於 RootElement 標題

 <a name="type" />


### <a name="type"></a>類型

必須設定為`"root"`時這會出現在 （這用來建立巢狀控制站） 的區段。

 <a name="sections" />


### <a name="sections"></a>區段

這是 Json 陣列，具有個別的區段

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>區段語法

此部分包含：

-  `header` (選擇性)
-  `footer` (選擇性)
-  `elements` 陣列


 <a name="header" />


### <a name="header"></a>標頭

如果有的話，標頭文字會顯示為一節的標題。

 <a name="footer" />


### <a name="footer"></a>頁尾

如果有的話，頁尾會顯示底部的區段。

 <a name="elements" />


### <a name="elements"></a>項目

這是元素的陣列。 每個項目必須包含至少一個索引鍵，`"type"`用來識別要建立項目類型的索引鍵。
某些項目共用某些常見的屬性，例如`"caption"`和`"value"`。 以下是支援的項目清單：

-  `string` 項目 （兩者逾時或無樣式）
-  `entry` 行 （「 標準 」 或 「 密碼 」）
-  `boolean` （使用參數或影像） 的值


字串項目可用來當作按鈕所提供的方法，在使用者點選附屬應用程式或儲存格上時要叫用

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>轉譯項目

轉譯項目為基礎的 C# StringElement 和 StyledStringElement 和它們可能會造成不同的方式的資訊且可以各種方式中加以轉譯。 您可以建立最簡單的項目，像這樣：

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

這會顯示所有預設值是簡單字串： 字型、 背景、 文字色彩及裝飾。 可對這些項目的動作相連結，使它們與按鈕的行為方式設定`"ontap"`屬性或`"onaccessorytap"`屬性：

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

上述會叫用 「 ShowPhotos 」 類別中的方法"Acme.PhotoLibrary"。 `"onaccessorytap"`很類似，但它將只會叫用如果使用者在存取子來代替點選的資料格上點選。 若要啟用此功能，您也必須設定存取子：

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

轉譯項目可以顯示兩個字串一次，一個是標題，另一個值。 如何這些字串會轉譯樣式而定，您可以將此使用`"style"`屬性。 預設會顯示標題左邊和右邊的值。 如需詳細資訊的樣式，請參閱 > 一節。 色彩編碼使用的 '#' 符號，後面接著十六進位的數字，代表紅色、 綠色、 藍色和可能的 alpha 值的值。 內容可以編碼以簡短形式 （3 或 4 個十六進位數字） 代表 RGBA 或 RGB 值。 或完整格式 （6 或 8 位數） 代表 RGBA 或 RGB 值。 簡短版本是速記兩次撰寫相同的十六進位數字。 "#1bc"常數是為紅色 intepreted = 0x11，綠色 = 0xbb 和藍色 = 0xcc。 如果 alpha 值不存在，色彩就是不透明。 一些範例如下：

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>accessory

決定附屬應用程式中顯示您呈現的項目，可能的值的種類：

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


如果值不存在，就會不顯示任何附屬應用程式

 <a name="background" />


### <a name="background"></a>背景

背景屬性將設定儲存格的背景色彩。 值是影像 URL （在此情況下，將會叫用非同步影像下載程式和下載映像後，將更新的背景） 也可以使用色彩語法指定的色彩。

 <a name="caption" />


### <a name="caption"></a>標題

在呈現項目上顯示主要的字串。 可以藉由設定自訂的字型和色彩`"textcolor"`和`"font"`屬性。 呈現樣式由`"style"`屬性。

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>色彩和 detailcolor

要用於主要文字或詳細的文字色彩。

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>detailfont 和字型

若要使用的標題或詳細資料的文字字型。 字型規格的格式是後面接著選擇性地以破折號和點大小的字型名稱。
以下是有效的字型規格：

-  "新細明體"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

決定線條會分解。 可能值為：

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


同時`character-wrap`和`word-wrap`可用並搭配`"lines"`屬性設定為零，呈現項目變成多行項目。

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ontap 和 onaccessorytap

這些屬性必須指向接受做為參數物件的應用程式中的靜態方法名稱。 當您建立階層 JsonDialog.FromFile 或 JsonDialog.FromJson 方法時，您可以傳遞選擇性的物件值。 此物件值，然後會傳遞至您的方法。 您可以使用此傳遞某些內容加入至靜態方法。 例如: 

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

 <a name="lines" />


### <a name="lines"></a>線條

如果此值設為零，它就會根據所含的字串的內容項目自動調整大小。 此工作，您也必須設定`"linebreak"`屬性`"character-wrap"`或`"word-wrap"`。

 <a name="style" />


### <a name="style"></a>樣式

樣式會決定將用來呈現內容的儲存格樣式的種類和它們對應於 UITableViewCellStyle 列舉值。
可能值為：

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` ： 有一個子標題文字。


 <a name="subtitle" />


### <a name="subtitle"></a>副標題

要使用的子標題的值。 這是設定的樣式的捷徑`"subtitle"`並設定`"value"`屬性設為字串。
這樣會同時與單一項目。

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

若要使用的文字色彩。

 <a name="value" />


### <a name="value"></a>value

要轉譯項目上顯示的次要的值。 此配置會受到`"style"`設定。 可以藉由設定自訂的字型和色彩`"detailfont"`和`"detailcolor"`。

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>布林值的項目

布林值的項目應將 type 設為`"bool"`，可以包含`"caption"`顯示和`"value"`設為 true 或 false。 如果`"on"`和`"off"`設定屬性，則會被認為是映像。 映像為相對於目前工作目錄的應用程式中解決。 如果您想要參考相對配套的檔案，您可以使用`"~"`快速的方式來表示應用程式資源存放區目錄。 例如`"~/favorite.png"`會組合檔案中包含 favorite.png。 例如: 

```csharp
{ 
    "type" : "boolean",
    "caption" : "Demo of a Boolean",
    "value"   : true
},

{
    "type": "boolean",
    "caption" : "Boolean using images",
    "value"   : false,
    "on"      : "favorite.png",
    "off"     : "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>類型

類型可以設定為 `"boolean"`或`"checkbox"`。 如果設定為布林值就會使用 UISlider 或影像 (如果有兩個`"on"`和`"off"`設定)。 如果設定為核取方塊，它會使用核取方塊。 `"group"`屬性可用來標記為屬於特定群組的布林值項目。 這非常有用，如果包含根也有`"group"`屬性做為根將摘要說明屬於相同群組的所有布林值 （或核取方塊） 的計數結果。

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>項目項目

您可以使用項目項目，讓使用者輸入的資料。 項目元素的類型是`"entry"`或`"password"`。 `"caption"`屬性設定為文字顯示在右側，而`"value"`初始值設定為設定的項目。 `"placeholder"`用來顯示一個提示 （它會顯示灰色） 的空白項目的使用者。 以下是一些範例：

```csharp
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

 <a name="autocorrect" />


### <a name="autocorrect"></a>autocorrect

決定要使用的項目自動更正樣式。 可能的值為 true 或 false (或字串`"yes"`和`"no"`)。

 <a name="capitalization" />


### <a name="capitalization"></a>大小寫

若要使用之項目的大小寫樣式。 可能值為：

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>標題

若要使用之項目的標題

 <a name="keyboard" />


### <a name="keyboard"></a>鍵盤

要用於資料輸入鍵盤型別。 可能值為：

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>預留位置

所顯示的項目時具有空值的提示文字。

 <a name="return-key" />


### <a name="return-key"></a>傳回索引鍵

傳回索引鍵所用的標籤。 可能值為：

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>value

項目的初始值

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>選項項目

選項項目具有型別`"radio"`。 已選取的項目會挑出由`radioselected`其包含根項目上的屬性。
此外，如果值設定為`"group"`屬性，這個選項按鈕屬於該群組。

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>日期和時間的項目

項目型別`"datetime"`，`"date"`和`"time"`用來呈現日期與時間、 日期或時間。 標題和值，這些項目會採用做為參數。 值可以撰寫.NET DateTime.Parse 函數所支援的任何格式。 範例：

```csharp
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

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Html/Web 項目

您可以建立一個儲存格，當點選會內嵌轉譯指定的 URL，內容 UIWebView 本機或遠端使用`"html"`型別。 這個項目只有兩個屬性會`"caption"`和`"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
