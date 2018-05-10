---
title: 互動式活頁簿
description: 使用活頁簿建立即時的文件的 C# 程式碼進行實驗，教導訓練或瀏覽。
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: de88bbc9bc45b8a6326924d964bdd9385acb82aa
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="interactive-workbooks"></a>互動式活頁簿

_使用活頁簿建立即時的文件的 C# 程式碼進行實驗，教導訓練或瀏覽。_

您可以使用活頁簿當做獨立應用程式，不同於您的 IDE。

若要開始建立新的活頁簿，執行活頁簿應用程式。 如果您尚未安裝這個已經，請瀏覽[安裝](~/tools/workbooks/install.md#install)頁面。 系統會提示您建立您的選擇，會自動連線到代理程式應用程式可讓您以視覺化方式檢視即時文件的平台的活頁簿。

如果活頁簿應用程式已在執行中，您可以建立新文件瀏覽至**檔案 > 新增**。

可儲存活頁簿，並稍後再開啟應用程式中。 您可以再與他人共用示範想法、 瀏覽新的 Api 或教授新的概念。

## <a name="code-editing"></a>編輯程式碼

編輯視窗的程式碼會提供程式碼完成、 語法著色、 內嵌即時診斷和多行陳述式支援。

[ ![](workbook-images/inspector-0.6.0-repl-small.png "編輯視窗的程式碼提供程式碼完成、 語法著色、 內嵌即時診斷和多行陳述式支援")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin 活頁簿會儲存在`.workbook`CommonMark 檔案具有一些頂端的中繼資料的檔案 (請參閱[活頁簿檔案類型](#workbooks-files-types)如需詳細資訊可以儲存活頁簿的方式)。

### <a name="nuget-package-support"></a>NuGet 套件支援

Xamarin 活頁簿中直接支援許多熱門的 NuGet 封裝。 您可以瀏覽至搜尋套件**檔案 > 加入封裝**。 新增套件會自動納入`#r`參考封裝組件，可讓您立即使用這些陳述式。

當您儲存活頁簿與封裝的參考時，這些參考會儲存起來。 如果您與其他人共用活頁簿，它將會自動下載參照的套件。

有一些已知的限制與活頁簿中的 NuGet 封裝支援：

  * 原生程式庫為 iOS，才支援和只有在與 managed 程式庫連結。
  * 這取決於封裝`.targets`檔案或 PowerShell 指令碼將可能無法如預期般運作。
  * 若要移除或修改封裝的相依性，請使用文字編輯器編輯活頁簿的資訊清單。 方式，是適當的封裝管理。

### <a name="xamarinforms-support"></a>Xamarin.Forms 支援

如果您的活頁簿中參考的 Xamarin.Forms NuGet 套件，活頁簿應用程式會變更其主要檢視 Xamarin.Forms 架構。 您可以存取透過`Xamarin.Forms.Application.Current.MainPage`。

檢視偵測器 索引標籤也有特殊的支援，以顯示 Xamarin.Forms 檢視階層架構，可協助您了解您的配置。

## <a name="rich-text-editing"></a>Rtf 文字編輯

您可以編輯的文字周圍使用豐富的文字編輯器，如下所示的程式碼：

![](workbook-images/inspector-0.6.2-editing.gif "編輯周圍使用內建的 rtf 文字編輯器的程式碼的文字")

### <a name="markdown-authoring"></a>Markdown 撰寫

活頁簿作者有時可能會發現您更輕鬆地直接使用其最愛的編輯器來編輯 CommonMark 活頁簿的 「 來源 」。

請注意，如果編輯並儲存您的活頁簿，將用戶端活頁簿中，可能會重新格式化 CommonMark 文字。

請注意，由於 CommonMark 擴充我們使用啟用 YAML 中繼資料的活頁簿檔案`---`針對該用途已保留。 如果您想要建立[參考書符號](http://spec.commonmark.org/0.27/#thematic-break)在您的文字，您應該使用`***`或`___`改為。 在活頁簿 1.2 和稍早因儲存 bug 時，就應該避免這類符號。

### <a name="improvements-in-workbooks-13"></a>1.3 活頁簿中的增強功能

我們也已擴充的 Markdown 區塊引號語法稍微改善簡報。 藉由加入 emoji 封鎖引號中的第一個字元，您可能會影響引號的背景色彩：

- `> [!NOTE]
>' 會轉譯為以藍色背景的附註
- `> [!IMPORTANT]
>' 會轉譯為包含背景為黃色的警告
- `> [!WARNING]
>' 將會呈現為具有紅色背景的問題

您也可以連結至活頁簿的文件中的標頭。 我們可以產生錨點，每個標頭，錨點識別碼的標頭文字，處理方式如下：

1. 標頭是較低的大小寫慣例。
1. 移除所有的字元，除了英數字元與連字號。
1. 以破折號取代全部為空格。

這表示標頭像 「 重要標頭 」 取得識別碼為`important-header`，您可以藉由插入連結連結至`#important-header`活頁簿中。

## <a name="document-structure"></a>文件結構

### <a name="cell"></a>資料格

不連續單位的內容，表示可執行程式碼或 markdown。 程式碼的儲存格包含高達四個子元件：

- 編輯器
  - 緩衝區
- 編譯器診斷
- 主控台輸出
- 執行結果

### <a name="editor"></a>編輯器

儲存格的互動式文字元件。 程式碼的儲存格，這是語法反白顯示等的實際程式碼編輯器。Markdown 資料格，這是 rtf 格式內容的區分內容的格式設定和編輯器撰寫工具列。

### <a name="buffer"></a>緩衝區
實際的文字編輯器的內容。

### <a name="compiler-diagnostics"></a>編譯器診斷

編譯程式碼，明確執行要求時，只轉譯時，所產生的任何診斷。 立即顯示在下方的儲存格編輯器。

### <a name="console-output"></a>主控台輸出

任何儲存格的執行期間寫入標準輸出或標準錯誤輸出。 會以黑色文字呈現標準輸出和標準錯誤將會轉譯為紅色。

### <a name="execution-results"></a>執行結果

豐富且互動可能表示資料格的結果會呈現在成功編譯時，提供執行實際產生的結果。 例外狀況會被視為在此內容中的結果，因為會產生這些構造實際執行編譯。

## <a name="workbooks-files-types"></a>活頁簿檔案類型

### <a name="plain-files"></a>一般檔案

根據預設，活頁簿儲存為純文字`.workbook`包含 CommonMark 格式的文字檔案。

### <a name="packages"></a>封裝

活頁簿封裝是使用名為目錄`.workbook`延伸模組。
在上 Mac 的搜尋，[Xamarin 活頁簿開啟的對話方塊和最近的檔案] 功能表中，如同一般檔案，也會辨識此目錄。

目錄必須包含`index.workbook`檔案，這是實際的純文字活頁簿，會載入 Xamarin 活頁簿中。 目錄也可以包含所需的資源`index.workbook`，包括影像或其他檔案。

如果純文字`.workbook`0.99.3 活頁簿中開啟從其相同的目錄會參考資源的檔案或更新版本中，儲存時，它將會轉換成`.workbook`封裝。 這是在 Mac 和 Windows 上，則為 true。

> [!NOTE]
> Windows 使用者會開啟`package.workbook\index.workbook`檔案直接，但是封裝就行為 mac 上的相同

### <a name="archives"></a>封存

活頁簿的封裝，正在目錄，就很難透過網際網路輕易地散佈。 解決方案是活頁簿的封存。 活頁簿封存是 zip 壓縮的活頁簿封裝時，名為`.workbook`延伸模組。

儲存活頁簿封裝時，在活頁簿 1.1 中，從開始，[儲存] 對話方塊提供選擇改為儲存為保存。 活頁簿 1.0 並沒有內建的方式，建立或儲存封存。

在活頁簿 1.0 中，當活頁簿封存已開啟時，它會明確地轉換到活頁簿封裝，而 zip 檔案已遺失。 在活頁簿 1.1 中，zip 檔案會保留。 當使用者儲存封存時，它會取代新的 zip 檔案。

您可以手動建立的活頁簿封存，活頁簿套件上按一下滑鼠右鍵，然後選取**壓縮**在 Mac 上，或**傳送到 > 壓縮的 (zipped) 資料夾**Windows 上。 然後將 zip 檔案重新命名`.workbook`檔案副檔名。 這只適用於活頁簿封裝，而不是一般的活頁簿檔案。

## <a name="related-links"></a>相關連結

- [歡迎使用活頁簿](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
