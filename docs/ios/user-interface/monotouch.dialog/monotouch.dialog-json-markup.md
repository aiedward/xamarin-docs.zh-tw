---
title: MonoTouch.Dialog Json 標記
description: 本文件說明可用來建置使用 MonoTouch.Dialog Xamarin.iOS 使用者介面的 JSON 語法。
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 775f378be383674e2bf90e2e8a72d82033ef0fbf
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675229"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json 標記

此頁面描述接受 MonoTouch.Dialog 的 Json 標記[JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

讓我們開始的範例。 以下是完整的 Json 檔案，才能傳遞至 JsonElement。

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

上述標記會產生下列 UI:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "建立所指定標記的 UI")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

在樹狀目錄中的每個項目可以包含屬性`"id"`。 您可在執行階段參考個別章節或使用 JsonElement 索引子的項目。 與下列類似：

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


根項目可顯示區段內，為要建立的巢狀的控制站的項目。 在此情況下，額外的屬性`"type"`必須設為 `"root"`

 <a name="url" />


### <a name="url"></a>URL

如果`"url"`設定屬性時，如果使用者點選此 RootElement，程式碼會從指定的 url 要求檔案，並將內容顯示的新資訊。 您可以使用此選項來建立從伺服器會根據使用者的點選延伸使用者介面。

 <a name="group" />


### <a name="group"></a>群組

如果設定，這會將根項目的群組名稱。 群組名稱用來挑選會顯示為根項目，其中一個項目中的巢狀項目值的摘要。 這是值的核取方塊或選項按鈕的值。

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

識別在巢狀項目中選取的選項項目

 <a name="title" />


### <a name="title"></a>標題

如果有的話，它會用於 RootElement 標題

 <a name="type" />


### <a name="type"></a>類型

必須設定為`"root"`時這會出現在 （這用來建立巢狀控制站） 一節。

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

如果有的話，標頭文字會顯示與區段的標題。

 <a name="footer" />


### <a name="footer"></a>頁尾

如果有的話，顯示頁尾區段的底部。

 <a name="elements" />


### <a name="elements"></a>項目

這是元素的陣列。 每個項目必須包含至少一個索引鍵，`"type"`用來識別要建立項目類型的索引鍵。
某些項目共用一些通用的屬性，例如`"caption"`和`"value"`。 以下是支援的項目清單：

-  `string` 項目 （兩者都包含或不含樣式）
-  `entry` 行 （「 一般 」 或 「 密碼 」）
-  `boolean` 值 （使用參數或影像）


字串項目可用來當做按鈕所提供的方法，在使用者點選附屬應用程式或儲存格上時要叫用

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>轉譯項目

轉譯項目根據C#StringElement 和 StyledStringElement 可以轉譯以各種方式的資訊，並就能夠以各種方式呈現它們。 您可以建立最簡單的項目，像這樣：

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

這會顯示一個簡單的字串，包含所有預設值： 字型、 背景、 文字色彩和裝飾。 您可對這些項目的動作連結，並將其設定的行為與按鈕一樣`"ontap"`屬性或`"onaccessorytap"`屬性：

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

上述會叫用 「 ShowPhotos 」 類別中的方法"Acme.PhotoLibrary 」。 `"onaccessorytap"`很類似，但它將才會叫用使用者點選來代替點選的資料格附屬應用程式。 若要這麼做，您也必須設定 附屬應用程式：

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

轉譯項目可以顯示兩個字串一次，其中一個是標題，另一個是值。 如何這些字串會轉譯樣式而定，您可以將此使用`"style"`屬性。 預設值會顯示標題左側和右側的值。 如需詳細資訊的樣式，請參閱節。 色彩是使用 '#' 符號，後面接著十六進位的數字，代表紅色、 綠色、 藍色和 alpha 可能值的值來編碼。 以簡短形式 （3 或 4 個十六進位數字） 代表 RGB 或 RGBA 值，可以編碼內容。 或代表 RGBA 或 RGB 值的長表單 （6 或 8 位數）。 簡短版本是兩次撰寫相同的十六進位數字的縮寫。 "#1bc"常數所以 intepreted 為紅色 = 0x11，綠色 = 0xbb 而藍色 = 0xcc。 如果不存在的 alpha 值，色彩就是不透明。 一些範例如下：

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>附屬應用程式

會決定要顯示在轉譯元素中，可能的值為附屬應用程式的類型：

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


如果值不存在，會不顯示任何附屬應用程式

 <a name="background" />


### <a name="background"></a>背景

背景屬性會設定儲存格的背景色彩。 值是其中一個映像的 URL （在此情況下，會叫用非同步影像下載程式和映像下載完成後，將會更新背景） 也可以使用色彩語法指定的色彩。

 <a name="caption" />


### <a name="caption"></a>標題

要轉譯項目上會顯示主要的字串。 可以藉由設定自訂的字型和色彩`"textcolor"`和`"font"`屬性。 轉譯樣式由`"style"`屬性。

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>色彩和 detailcolor

要作為主要的文字與詳細的文字色彩。

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>detailfont 和字型

若要使用的標題或詳細資料的文字字型。 字型規格的格式是後面接著選擇性地以連字號和點大小的字型名稱。
以下是有效的字型規格：

-  "新細明體"
-  "新細明體 14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

決定行進行分解。 可能值為：

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


兩者`character-wrap`並`word-wrap`可用並搭配`"lines"`屬性設定為 0 時可將轉譯項目轉換成多行項目。

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ontap 和 onaccessorytap

這些屬性必須指向接受物件做為參數的應用程式中的靜態方法名稱。 當您建立階層使用 JsonDialog.FromFile 或 JsonDialog.FromJson 方法時，您可以傳遞選擇性的物件值。 此物件的值然後傳遞至您的方法。 您可以使用此傳遞一些內容到您的靜態方法。 例如: 

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

如果此值設為零，它就會根據所含的字串內容項目自動調整大小。 針對此目的，您也必須設定`"linebreak"`屬性，以`"character-wrap"`或`"word-wrap"`。

 <a name="style" />


### <a name="style"></a>樣式

樣式會決定將用來呈現內容的儲存格樣式的類型和它們對應到 UITableViewCellStyle 列舉值。
可能值為：

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` ： 子標題的文字。


 <a name="subtitle" />


### <a name="subtitle"></a>子標題

要用於翻譯字幕的值。 這是要將樣式設定為捷徑`"subtitle"`並設定`"value"`屬性設為字串。
這會同時與單一項目。

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

若要使用的文字色彩。

 <a name="value" />


### <a name="value"></a>value

要轉譯項目上顯示的次要值。 這個配置會受到`"style"`設定。 可以藉由設定自訂的字型和色彩`"detailfont"`和`"detailcolor"`。

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>布林值項目

布林值項目應將 type 設為`"bool"`，可以包含`"caption"`來顯示和`"value"`設為 true 或 false。 如果`"on"`和`"off"`設定屬性，它們會假設為映像。 映像，在解決相對於目前工作目錄的應用程式中。 如果您想要參考相對套件組合的檔案，您可以使用`"~"`來代表應用程式套件組合目錄的捷徑。 例如`"~/favorite.png"`會包含在組合檔案 favorite.png。 例如: 

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

類型可以設定為`"boolean"`或`"checkbox"`。 如果設定為布林值，則會使用 UISlider 或映像 (如果有兩個`"on"`和`"off"`設定)。 如果設定為核取方塊，則會使用一個核取方塊。 `"group"`屬性可以用來標記為屬於特定群組的布林值項目。 這是很有用，如果也有包含根`"group"`屬性做為將摘要說明隸屬於相同的群組結果中的所有布林值 （或核取方塊） 的計數。

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>項目項目

您可以使用項目的項目，讓使用者輸入的資料。 項目元素的類型是`"entry"`或`"password"`。 `"caption"`屬性設定為文字以顯示在右側，而`"value"`為初始值設定為設定的項目。 `"placeholder"`用來顯示提示使用者輸入 （它會顯示灰色） 的空白項目。 以下是一些範例：

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


### <a name="autocorrect"></a>自動校正

決定要使用的項目自動校正樣式。 可能的值為 true 或 false (或字串`"yes"`和`"no"`)。

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

要用於資料輸入的鍵盤類型。 可能值為：

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

當項目具有空值會顯示提示文字。

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

選項項目具有型別`"radio"`。 已選取的項目由所選取`radioselected`其包含的根項目上的屬性。
此外，如果值為`"group"`屬性，這個選項按鈕所屬的群組。

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>日期和時間項目

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

您可以建立一個儲存格，當點選會將內嵌轉譯指定的 URL，內容 UIWebView 本機或遠端使用`"html"`型別。 只有兩個屬性，這個項目的`"caption"`和`"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
