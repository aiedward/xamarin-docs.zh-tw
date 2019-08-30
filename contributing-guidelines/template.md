---
title: 本文的標題
description: 本文良好、可靠的描述。 此處保留的內容是 Microsoft Docs 系統傳回的搜索結果，因此最好是對此文件檔案的用途與內容的完整描述
keywords: 關鍵字, 清單, 適用於, 內部, 搜尋
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: e4c8e404f447dd88b338123335a291bc9f23e449
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119011"
---
# <a name="metadata-and-markdown-template"></a>中繼資料和 Markdown 範本

此範本包含 Markdown 語法，以及設定中繼資料的指導方針。 若要善加利用它，您必須檢閱**原始的 Markdown (英文)** 和**轉譯後的檢視 (英文)** (例如，原始的 Markdown 會顯示中繼資料區塊，而轉譯後的檢視則不會)。

在建立 Markdown 檔案的時候，您應將此範本複製到新的檔案，填入下列指定的中繼資料，將上方的 H1 標題設為文章的標題，然後刪除內容。

## <a name="metadata"></a>中繼資料

帶有一堆註解的完整中繼資料區塊，如下所示：

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

重點提示：

- 請「務必」  在中繼資料項目的值和冒號 (:) 之間加上一個空格。
- 選擇性的中繼資料元素沒有值，請將它刪除 (請勿保留空白，或者使用 "na")。
- 在值中出現冒號 (例如標題) 會中斷中繼資料剖析器。 在此情況下，請使用雙引號括住標題 (例如，`title: "Writing .NET Core console apps: An advanced step-by-step guide"`)。
- **title**：此標題會出現在搜尋引擎的搜尋結果中。 此標題不需要與 H1 標題中的標題相同，且應包含最多 60 個字元。
- **author**、**manager**、**ms.reviewer**：author 欄位應包含該作者的 **GitHub 使用者名稱**，而不是別名。  "manager" 和 "ms.reviewer" 欄位則應包含 Microsoft 別名。 ms.reviewer 指定與文章或功能相關的 PM/開發人員。
- **ms.assetid**：這是文章的 GUID，用於商業智慧 (BI) 等內部追蹤目的。 在建立新的 Markdown 檔案時，請從 [https://www.guidgenerator.com](https://www.guidgenerator.com) 取得 GUID。

## <a name="basic-markdown-gfm-and-special-characters"></a>基本 Markdown、GFM 和特殊字元

支援所有基本和 GitHub Flavored Markdown (GFM)。 如需詳細資訊，請參閱：

- [基準 Markdown 語法 (英文)](https://daringfireball.net/projects/markdown/syntax)
- [DocFX Flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)
- [GFM 文件 (英文)](https://guides.github.com/features/mastering-markdown)

Markdown 使用 \*、\` 和 \# 等特殊字元來設定格式。 如果要在內容中包含這些字元，請遵循下列其中一種作法：

- 在該特殊字元前面加上反斜線來將它「逸出」(例如，\* 逸出為 `\*`)
- 使用該字元的 [HTML 實體代碼 (英文)](http://www.ascii.cl/htmlcodes.htm) (例如，&#42; 為 `&#42;`)。

## <a name="file-name"></a>檔案名稱

檔案名稱使用下列規則︰
- 只包含小寫字母、數字和連字號。
- 不使用空格或標點符號。 使用連字號來分隔檔名中的文字和數字。
- 使用明確的動作動詞，例如 develop、buy、build、troubleshoot。 不使用 -ing 字詞。
- 不使用短字詞，例如 a、and、the、in、or 等等。
- 必須為 Markdown 格式且使用副檔名 .md。
- 盡可能保持檔名簡短。 它們會是文章 URL 的一部分。



## <a name="headings"></a>標題

使用句子樣式的大寫。 下列情況一律大寫：
- 標題的第一個字。
- 標題中冒號後的第一個字 (例如，"How to:Sort an array")。

標題應使用 atx 樣式，也就是在行開頭以 1 至 6 個井字號 (#) 來表示標題，這會對應到 HTML 標題層級 H1 至 H6。 上面使用的是第一和第二層級標題的範例。

請「務必」  確定主題中只有一個第一層級標題 (H1)，該標題會顯示為頁面標題。

如果您的標題結尾是 `#` 字元，則您必須在結尾另外加上一個 `#` 以使標題正確轉譯。 例如，`# Async Programming in F# #`。

第二層級的標題會產生頁面目錄，並顯示在頁面標題下的 [本文內容] 區段中。

### <a name="third-level-heading"></a>第三層級標題
#### <a name="fourth-level-heading"></a>第四層級標題
##### <a name="fifth-level-heading"></a>第五層級標題
###### <a name="sixth-level-heading"></a>第六層級標題

## <a name="text-styling"></a>文字樣式

「斜體」  用於檔案、資料夾、路徑 (較長的項目請分割為各行) - 新詞彙 - URL (除非轉譯為連結，這是預設值)。

**粗體** 用於 UI 元素。

## <a name="links"></a>「連結」

### <a name="internal-links"></a>內部連結

若要連結到同一個 Markdown 檔案中的標題 (又稱錨點連結)，您需要找出該標題的識別碼。 若要確認識別碼，請檢視轉譯文章的原始程式碼，以找出該標題的識別碼 (例如，`id="blockquote"`)，然後使用「# + 識別碼」來連結 (例如，`#blockquote`)。
此識別碼是依據標題文字而自動產生。 舉例來說，若某章節名稱為 `## Step 2`，則識別碼會是 `id="step-2"`。

- 範例：`[Chapter 1](#chapter-1)`

若要連結到同一個存放庫中的 Markdown 檔案，請使用[相對連結](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2)，且檔案名稱結尾包含 ".md"。

- 範例：`[Readme file](../readme.md)`
- 範例：`[Welcome to .NET](../docs/welcome.md)`

若要連結到相同存放庫中 Markdown 檔案內的標題，請使用「相對連結 + 井字號」來連結。

- 範例：`[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>外部連結

若要連結到外部檔案，請使用完整 URL 作為連結。

- 範例：`[GitHub](http://www.github.com)`

如果 Markdown 檔案中出現 URL，系統會將它轉換成可點擊的連結。

- 範例：`http://www.github.com`

### <a name="links-to-apis"></a>API 的連結

組建系統有些延伸模組可讓我們在不需使用外部連結的情況下，連結到 .NET API。
當連結到 API 時，您可以使用從原始程式碼自動產生的唯一識別碼 (UID)。

您可以使用下列其中一個語法：
1. Markdown 連結：`[link_text](xref:UID)`
2. 自動連結︰`<xref:UID>`
3. 簡短格式：`@UID`

- 範例：`@System.String`
- 範例：`[String class](xref:System.String)`

如需使用此標記法的詳細資訊，請參閱[使用交互參照 (英文)](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference)。

> 目前沒有簡單的方法可以尋找 UID。 尋找 UID 的最佳方法是在此存放庫中搜尋：[docascode/coreapi (英文)](https://github.com/docascode/coreapi)。 我們正努力讓系統變得更好。

當 UID 包含特殊字元 \`或 \# 時，該 UID 值必須分別編碼為 HTML 代碼 %60 和 %23，如下列範例：
- 範例：@System.Threading.Tasks.Task\`1 變成 `@System.Threading.Tasks.Task%601`
- 範例：@System.Exception.\#ctor 變成 `@System.Exception.%23ctor`

## <a name="lists"></a>清單

### <a name="ordered-lists"></a>排序清單

1. 這
1. 是
1. 一個
1. 排序
1. 清單


#### <a name="ordered-list-with-an-embedded-list"></a>包含內嵌清單的排序清單

1. 這裡
1. 有
1. 一個
1. 內嵌
    1. Miss Scarlett
    1. Professor Plum
1. 排序
1. 清單


### <a name="unordered-lists"></a>未排序清單

- 這個
- 是
- 一個
- 項目符號
- 清單


##### <a name="unordered-list-with-an-embedded-list"></a>包含內嵌清單的未排序清單

- 這個
- 項目符號
- 清單
    - Mrs. Peacock
    - Mr. Green
- 包含
- 另一個
    1. Colonel Mustard
    1. Mrs. White
- 清單


## <a name="horizontal-rule"></a>水平線

---

## <a name="tables"></a>資料表

| 資料表        | 很           | 酷  |
| ------------- |:-------------:| -----:|
| 欄 3 為      | 靠右對齊 | $1600 |
| 欄 2 為      | 置中      |   $12 |
| 欄 1 為預設 | 靠左對齊     |    $1 |

您可以使用 [Markdown 表格產生工具 (英文)](http://www.tablesgenerator.com/markdown_tables) 來輕鬆產生表格。


### <a name="inline-code-blocks-with-language-identifier"></a>具有語言識別碼的內嵌程式碼區塊

使用「三個反引號 (\`\`\`) + 語言識別碼」，來在程式碼區塊套用該語言的語法著色。 這裡有 [GFM 語言識別碼 (英文)](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs) 的完整清單。

##### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>一般程式碼區快

使用三個反引號 (&#96;&#96;&#96;) 撰寫一般程式碼區塊。

> 建議的做法是搭配語言識別碼使用程式碼區塊 (如上節所述)，以確保語法在文件網站上適當地醒目顯示。 必要時才使用一般程式碼區塊。

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>內嵌程式碼

針對 `inline code` 使用反引號 (&#96;)。 針對命令列命令、資料庫資料表和資料行的名稱，以及語言關鍵字使用內嵌程式碼。

## <a name="blockquotes"></a>引文

> The drought had lasted now for ten million years, and the reign of the terrible lizards had long since ended. Here on the Equator, in the continent which would one day be known as Africa, the battle for existence had reached a new climax of ferocity, and the victor was not yet in sight. In this barren and desiccated land, only the small or the swift or the fierce could flourish, or even hope to survive.

## <a name="images"></a>影像

### <a name="static-image-or-animated-gif"></a>靜態影像或動畫 GIF

```
![this is the alt text](images/dotnet.png)
```

![回應式設計](images/responsivedesign.gif)

### <a name="linked-image"></a>連結影像

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>視訊

### <a name="channel-9"></a>Channel 9

使用這種特殊的區塊引述語法：

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

Channel 9 影片 URL 應以 `https` 開頭，以 `/player` 結尾，否則只會內嵌整個網頁而非只有視訊。

### <a name="youtube"></a>YouTube

使用這種特殊的區塊引述語法：

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

YouTube 影片應包含 `/embed/` (亦即，遵循上述格式)，否則會嘗試呈現整個頁面，且可能無法運作。

## <a name="docsmicrosoft-extensions"></a>docs.microsoft 延伸模組

docs.microsoft 為 GitHub Flavored Markdown 提供幾個額外的延伸模組。

### <a name="alerts"></a>警示

請務必使用下列的警示樣式，這樣它們才能在文件網站中轉譯為適當的樣式。 不過，GitHub 的轉譯引擎無法分辨這些樣式。

#### <a name="note"></a>注意事項

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>警告

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>提示

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>重要事項

```
> [!IMPORTANT]
> This is IMPORTANT
```

它們轉譯之後看起來像這樣：

![警示樣式](images/alerts.png)

### <a name="buttons"></a>按鈕

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

您可以在 [Intune 文件](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices)中看到按鈕的實際範例。

### <a name="selectors"></a>選取器

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

您可以在 [Intune 文件](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps)中看到選取器的實際範例。

### <a name="step-by-steps"></a>逐步執行

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

您可以在 [Advanced Threat Analytics 文件](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2)中看到逐步執行的實際範例。
