---
title: 互動式活頁簿
description: 本檔說明如何使用 Xamarin Workbooks 來建立包含C#程式碼的即時檔，以進行實驗、教學、訓練或探索。
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: a6ca347c231d001cab521d7280a66b714b6a5aef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029568"
---
# <a name="interactive-workbooks"></a>互動式活頁簿

您可以使用活頁簿做為獨立應用程式，並與 IDE 分開。

若要開始建立新的活頁簿，請執行活頁簿應用程式。 如果您尚未安裝此版本，請造訪[安裝](~/tools/workbooks/install.md#install)頁面。 系統會提示您在您選擇的平臺中建立活頁簿，這會自動連接到代理程式應用程式，讓您能夠即時視覺化您的檔。

如果活頁簿應用程式已在執行中，您可以流覽至 [檔案] **> [新增**] 來建立新的檔。

稍後可以在應用程式內儲存和開啟活頁簿。 然後您可以與其他人共用這些專案，以示範想法、探索新的 Api，或教新的概念。

## <a name="code-editing"></a>程式碼編輯

[程式碼編輯] 視窗提供程式碼完成、語法著色、內嵌即時診斷和多行語句支援。

[![](workbook-images/inspector-0.6.0-repl-small.png "The code editing window provides code completion, syntax coloring, inline live-diagnostics, and multi-line statement support")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks 儲存在 `.workbook` 檔案中，也就是在頂端有一些中繼資料的且 commonmark 檔案（如需如何儲存活頁簿的詳細資訊，請參閱活頁[簿檔案類型](#workbooks-files-types)）。

### <a name="nuget-package-support"></a>NuGet 套件支援

Xamarin Workbooks 中直接支援許多常用的 NuGet 套件。 您可以流覽至 [檔案] **> [新增套件**] 來搜尋套件。 新增封裝會自動帶入參考封裝元件 `#r` 語句，讓您可以立即使用它們。

當您使用封裝參考儲存活頁簿時，這些參考也會一併儲存。 如果您與其他人共用該活頁簿，它就會自動下載參考的套件。

活頁簿中的 NuGet 套件支援有一些已知的限制：

- 只有在 iOS 上才支援原生程式庫，而且只有在與 managed 程式庫連結時才支援。
- 相依于 `.targets` 檔案或 PowerShell 腳本的套件可能會無法如預期般運作。
- 若要移除或修改封裝相依性，請使用文字編輯器編輯活頁簿的資訊清單。 有適當的套件管理。

### <a name="xamarinforms-support"></a>Xamarin. 表單支援

如果您在活頁簿中參考 [Xamarin] NuGet 套件，活頁簿應用程式會將其主要視圖變更為 [以 Xamarin 為基礎]。 您可以透過 `Xamarin.Forms.Application.Current.MainPage`來存取它。

[View Inspector] 索引標籤也有特殊的支援，可顯示 [Xamarin] 視圖階層，協助您瞭解您的版面配置。

## <a name="rich-text-editing"></a>Rich Text 編輯

您可以使用包含的 rich 文字編輯器來編輯程式碼周圍的文字，如下所示：

![](workbook-images/inspector-0.6.2-editing.gif "Edit the text around the code using the built-in rich text editor")

### <a name="markdown-authoring"></a>Markdown 撰寫

活頁簿作者有時可能會發現，使用他們最愛的編輯器直接編輯活頁簿的且 commonmark 「來源」也比較容易。

請注意，如果您接著在活頁簿用戶端中編輯並儲存活頁簿，您的且 commonmark 文字可能會重新格式化。

請注意，由於我們使用且 commonmark 延伸模組來啟用活頁簿檔案中的 YAML 中繼資料，因此 `---` 是針對該目的而保留。 如果您想要在文字中建立[主題中斷](https://spec.commonmark.org/0.27/#thematic-break)，您應該改用 `***` 或 `___`。 在活頁簿1.2 和更早版本中，應避免這類中斷，因為儲存期間發生錯誤。

### <a name="improvements-in-workbooks-13"></a>活頁簿1.3 的改良功能

我們也稍微延伸了 Markdown 組塊引號語法來改善簡報。 藉由新增表情做為區塊報價中的第一個字元，您可以影響報價的背景色彩：

- `> [!NOTE]`
    > 會轉譯成具有藍色背景的附注
- `> [!IMPORTANT]`
    > 將呈現為具有黃色背景的警告
- `> [!WARNING]`
    > 會呈現為紅色背景的問題

您也可以連結到活頁簿檔中的標頭。 我們會產生每個標頭的錨點，其中錨點識別碼是標頭文字，處理方式如下：

1. 標頭的大小寫小寫。
1. 除了英數位元和虛線以外的所有字元都會被移除。
1. 所有空格都會取代為連字號。

這表示「重要標頭」之類的標頭會取得 `important-header` 的識別碼，而且可以藉由將連結插入活頁簿中的 `#important-header` 來連結。

## <a name="document-structure"></a>檔結構

### <a name="cell"></a>格值

代表可執行程式碼或 markdown 的離散內容單位。 程式碼資料格由最多四個子元件所組成：

- 編輯器
  - 緩衝區
- 編譯器診斷
- 主控台輸出
- 執行結果

### <a name="editor"></a>編輯器

儲存格的互動式文字元件。 對於程式碼資料格而言，這是實際的程式碼編輯器，其中包含語法醒目提示等等。對於 markdown 資料格而言，這是具有內容相關格式和撰寫工具列的 rtf 文字編輯器。

### <a name="buffer"></a>緩衝區

編輯器的實際文字內容。

### <a name="compiler-diagnostics"></a>編譯器診斷

編譯器代碼時所產生的任何診斷，只有在要求明確執行時才會轉譯。 顯示在資料格編輯器正下方。

### <a name="console-output"></a>主控台輸出

在儲存格執行期間，任何寫入標準輸出或標準錯誤的輸出。 標準輸出將會以黑色文字呈現，而標準錯誤則會以紅色文字呈現。

### <a name="execution-results"></a>執行結果

針對資料格的結果，豐富且可能有互動的標記法將會在編譯成功時轉譯，前提是執行實際上會產生結果。 例外狀況會被視為此內容中的結果，因為它們是因為實際執行編譯而產生的。

## <a name="workbooks-files-types"></a>活頁簿檔案類型

### <a name="plain-files"></a>一般檔案

根據預設，活頁簿會儲存為純文字 `.workbook` 檔案，其中包含且 commonmark 格式的文字。

### <a name="packages"></a>package

活頁簿封裝是以 `.workbook` 延伸模組命名的目錄。
在 Mac 的搜尋工具和 [Xamarin Workbooks 開啟] 對話方塊和 [最近使用的檔案] 功能表中，此目錄會被辨識為檔案。

此目錄必須包含 `index.workbook` 檔案，這是將在 Xamarin Workbooks 中載入的實際純文字活頁簿。 目錄也可以包含 `index.workbook`所需的資源，包括影像或其他檔案。

如果在活頁簿0.99.3 或更新版本中，從相同目錄參考資源的純文字 `.workbook` 檔案已開啟，則儲存該檔案時，它會轉換成 `.workbook` 套件。 這在 Mac 和 Windows 上都是如此。

> [!NOTE]
> Windows 使用者會直接開啟 `package.workbook\index.workbook` 檔案，但套件的行為會與 Mac 上的相同。

### <a name="archives"></a>封存

活頁簿封裝是目錄，很難透過網際網路輕鬆散發。 解決方案是活頁簿封存。 活頁簿封存是 zip 壓縮的活頁簿封裝，以 `.workbook` 副檔名命名。

從活頁簿1.1 開始，儲存活頁簿封裝時，[儲存] 對話方塊會改為提供儲存為封存的選項。 活頁簿1.0 沒有內建的方式可建立或儲存封存。

在活頁簿1.0 中，當活頁簿封存開啟時，它會以透明方式轉換成活頁簿套件，而 zip 檔案也會遺失。 在活頁簿1.1 中，zip 檔案仍會保留。 當使用者儲存封存時，會以新的 zip 檔案取代。

您可以用滑鼠右鍵按一下活頁簿套件，然後選取 [在 Mac 上**壓縮**] 或 [**傳送至 Windows 上 > 壓縮（zipped）] 資料夾，以**手動方式建立活頁簿保存。 然後重新命名 zip 檔案，使其具有 `.workbook` 副檔名。 這只適用于活頁簿封裝，而不是純活頁簿檔案。

## <a name="related-links"></a>相關連結

- [歡迎使用活頁簿](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
