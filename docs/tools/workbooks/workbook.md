---
title: 互動式活頁簿
description: 本文件說明如何使用 Xamarin 活頁簿來建立即時的文件包含C#的實驗、 教學、 訓練或瀏覽程式碼。
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a900d427ad6ac2a0e211ef4f00d2f014b13e5d1c
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674410"
---
# <a name="interactive-workbooks"></a>互動式活頁簿

您可以使用活頁簿當做獨立應用程式，個別從您的 IDE。

若要開始建立新的活頁簿，執行活頁簿應用程式。 如果您還沒有已安裝，請瀏覽[安裝](~/tools/workbooks/install.md#install)頁面。 系統會提示您建立的活頁簿中您的選擇，會自動連線到代理程式應用程式可讓您以視覺化方式檢視您的文件，即時平台。

如果活頁簿應用程式已在執行中，您可以瀏覽至建立新的文件**檔案 > 新增**。

可以儲存活頁簿，並稍後再重新開啟應用程式中。 您可以再與他人共用示範概念、 探索新的 Api，或讓新的概念。

## <a name="code-editing"></a>程式碼編輯

編輯視窗的程式碼提供程式碼完成、 語法著色、 內嵌即時診斷和多行陳述式支援。

[![](workbook-images/inspector-0.6.0-repl-small.png "編輯視窗的程式碼提供程式碼完成、 語法著色、 內嵌即時診斷和多行陳述式支援")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin 活頁簿就會存入`.workbook`CommonMark 檔案具有頂端的一些中繼資料的檔案 (請參閱 <<c2> [ 活頁簿檔案類型](#workbooks-files-types)如需詳細資訊可以儲存活頁簿的方式)。

### <a name="nuget-package-support"></a>NuGet 套件支援

Xamarin 活頁簿中的直接支援許多熱門的 NuGet 套件。 您可以瀏覽至搜尋封裝**檔案 > 新增套件**。 新增套件會自動納入`#r`參考封裝組件，可讓您立即使用這些陳述式。

當您儲存活頁簿，與套件參考時，這些參考會一併儲存。 如果您與其他人共用活頁簿，它會自動下載參考的套件。

有一些已知的限制，活頁簿中的 NuGet 套件支援：

- 原生程式庫會支援僅在 iOS 上，並只有在與 managed 程式庫連結。
- 而這取決於套件`.targets`檔案或 PowerShell 指令碼將可能無法如預期般運作。
- 若要移除或修改封裝的相依性，請使用文字編輯器編輯活頁簿的資訊清單。 適當的套件管理即將推出。

### <a name="xamarinforms-support"></a>Xamarin.Forms 的支援

如果您在您的活頁簿中參考的 Xamarin.Forms NuGet 套件時，活頁簿應用程式會變更其主要的檢視，以 Xamarin.Forms 為基礎。 您可以存取透過`Xamarin.Forms.Application.Current.MainPage`。

檢視偵測器索引標籤也有顯示 Xamarin.Forms 檢視階層，以協助您了解您的版面配置的特殊支援。

## <a name="rich-text-editing"></a>編輯的 Rtf 文字

您可以編輯的文字，您使用 rtf 編輯器包含，如下所示的程式碼：

![](workbook-images/inspector-0.6.2-editing.gif "編輯周圍使用內建的 rtf 編輯器程式碼的文字")

### <a name="markdown-authoring"></a>Markdown 編寫

活頁簿作者有時可能會發現您更輕鬆地直接使用其偏好的編輯器來編輯 CommonMark 活頁簿的 「 來源 」。

請注意如果您再編輯並儲存您的活頁簿內的活頁簿用戶端，可能會重新格式化 CommonMark 文字。

請注意，由於 CommonMark 擴充我們使用 YAML 活頁簿檔案中的中繼資料，以便`---`已保留，針對該目的。 如果您想要建立[主題的符號](http://spec.commonmark.org/0.27/#thematic-break)在您的文字，您應該使用`***`或`___`改。 在活頁簿 1.2 和稍早 bug 的期間儲存，就應該避免這類符號。

### <a name="improvements-in-workbooks-13"></a>活頁簿 1.3 中的增強功能

我們還擴充的 Markdown 區塊引述語法稍微改善簡報。 藉由加入 emoji 為您在區塊引述的第一個字元，您可能會影響報價的背景色彩：

- `> [!NOTE]`
    > 會轉譯為藍色背景的附註
- `> [!IMPORTANT]`
    > 會轉譯為具有背景為黃色的警告
- `> [!WARNING]`
    > 會轉譯為紅色背景的問題

您也可以連結至活頁簿的文件中的標頭。 我們正在處理，如下所示的標頭文字的錨點識別碼產生每個標頭，的起點：

1. 標頭是小寫。
1. 英數字元和連字號除外的所有字元會被都移除。
1. 所有的空格取代虛線。

這表示標頭像 「 重要的標頭 」 取得的識別碼`important-header`，而且會連結到所插入的連結`#important-header`活頁簿中。

## <a name="document-structure"></a>文件結構

### <a name="cell"></a>資料格

離散單位的內容，表示可執行程式碼或 markdown。 程式碼儲存格是由最多四個的子元件所組成：

- 編輯器
  - 緩衝區
- 編譯器診斷
- 主控台輸出
- 執行結果

### <a name="editor"></a>編輯器

互動式的文字儲存格的元件。 程式碼儲存格，這是實際的程式碼編輯器與語法醒目提示，等等。Markdown 資料格，這是 rtf 文字內容的編輯器，以區分內容的格式設定，以及撰寫工具列。

### <a name="buffer"></a>緩衝區

實際的文字編輯器的內容。

### <a name="compiler-diagnostics"></a>編譯器診斷

編譯程式碼，明確執行要求時，只轉譯時，就會產生任何診斷。 立即顯示在下方的儲存格編輯器。

### <a name="console-output"></a>主控台輸出

任何儲存格的執行期間寫入至標準輸出或標準錯誤輸出。 會以黑色文字呈現標準輸出和標準錯誤會呈現紅色文字。

### <a name="execution-results"></a>執行結果

豐富且互動可能表示資料格的結果會呈現在成功編譯時，提供執行實際產生的結果。 例外狀況會被視為此內容中的結果，因為會產生這些構造實際執行編譯。

## <a name="workbooks-files-types"></a>活頁簿檔案類型

### <a name="plain-files"></a>一般檔案

根據預設，活頁簿儲存為純文字`.workbook`包含 CommonMark 格式化的文字檔案。

### <a name="packages"></a>package

活頁簿套件是一個目錄與名為`.workbook`延伸模組。
在 Mac 的搜尋工具以及在最新的 [檔案] 功能表與 Xamarin 活頁簿開啟對話方塊，如同它是一個檔案，也會辨識此目錄。

此目錄必須包含`index.workbook`檔案，這是實際的純文字活頁簿，會載入 Xamarin 活頁簿中。 目錄也包含所需的資源`index.workbook`，包括影像或其他檔案。

如果純文字`.workbook`活頁簿 0.99.3 中開啟從其相同的目錄參考資源的檔案，或更新版本中，儲存時，它將會轉換成`.workbook`封裝。 這是在 Mac 和 Windows 上，則為 true。

> [!NOTE]
> Windows 使用者會開啟`package.workbook\index.workbook`檔案直接，但否則封裝會如同在 mac 上

### <a name="archives"></a>封存

活頁簿的封裝，在目錄中，可能難以輕易地透過網際網路散佈。 解決方法是活頁簿封存。 活頁簿封存是 zip 壓縮的活頁簿的套件，名為`.workbook`延伸模組。

儲存活頁簿封裝時，在活頁簿 1.1 中，從開始，[儲存] 對話方塊會提供選擇改為將儲存為封存。 活頁簿 1.0 有沒有內建的方式，建立或儲存封存。

在活頁簿 1.0 中，當開啟活頁簿封存時，它以透明的方式轉換成 活頁簿封裝時，而 zip 檔案已遺失。 在活頁簿 1.1 中，會保留的 zip 檔案。 當使用者儲存封存時，會將它取代為新的 zip 檔案。

您可以手動建立的活頁簿封存，活頁簿套件上按一下滑鼠右鍵，然後選取**壓縮**在 Mac 上，或**傳送至 > 壓縮 (zipped) 資料夾**在 Windows 上。 然後將 zip 檔案重新命名`.workbook`副檔名。 這僅適用於活頁簿封裝，不單純的活頁簿檔案。

## <a name="related-links"></a>相關連結

- [歡迎使用活頁簿](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
