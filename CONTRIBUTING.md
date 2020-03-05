---
ms.openlocfilehash: 0071fcc72844e4816e707c5828464859e09db68c
ms.sourcegitcommit: 2503da076966d12a8bdbf7af209662b74913a15c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78261614"
---
# <a name="contributing"></a>參與

感謝您想要投稿 Xamarin 文件！

本頁面涵蓋更新 [Xamarin 文件](https://docs.microsoft.com/xamarin)中內容的基本程序。

- [參與者授權合約](LICENSE)

## <a name="process-for-contributing"></a>投稿程序

### <a name="small-changes--edits"></a>少量變更和編輯

若要更正和進行少量更新，您可以按一下任何頁面上的 [編輯] 按鈕並使用 GitHub 網站來投稿，或是遵循下列步驟：

1. 派生 `MicrosoftDocs/xamarin-docs` 存放庫。

2. 建立變更的 `branch`。

3. 撰寫內容。 請參閱[範本](contributing-guidelines/template.md)和[樣式指南](contributing-guidelines/voice-tone.md)。

4. 將提取要求 (PR) 從您的分支提交至 `MicrosoftDocs/xamarin-docs/live`。

5. 透過 PR 與小組討論後，對分支進行所有必要更新。

6. 套用意見反應而且您的變更也適切之後，維護人員即會合併 PR。 它之後很快就會出現在 docs.microsoft.com 上。

> [!NOTE]
> 若您的 PR 可解決現有的問題，請對認可訊息或 PR 描述新增 `Fixes #Issue_Number` 關鍵字，該問題即可於合併 PR 時，自動解決。 如需詳細資訊，請參閱[經由認可訊息結束問題](https://help.github.com/articles/closing-issues-via-commit-messages/)。

### <a name="big-changes-or-new-content"></a>大量變更或新內容

針對大量投稿和新內容，[開立問題](https://github.com/MicrosoftDocs/xamarin-docs/issues)會描述您想要撰寫的文章以及該文章與現有內容相關性。 docs 資料夾內的內容會整理為依產品區域 (例如 android 和 ios) 整理的章節。 請嘗試為您新的內容判斷正確的資料夾。 

**開始撰寫之前，透過問題取得您提議的意見反應。**

如果這是新主題，您可以使用[範本檔案](../contributing-guidelines/template.md)作為起點。 其包含撰寫指導方針，同時也會說明每篇文章所需的中繼資料，例如作者資訊。

針對影像及其他靜態資源，請將其新增至稱為 **\<mypage>-images** 的子資料夾。 如果您要為內容建立新的資料夾，請將 images 資料夾新增至新的資料夾。

#### <a name="example-structure"></a>範例結構

```
docs
    /android
        mypage.md
        /mypage-images
            some-image.png
```

請務必遵循正確的 Markdown 語法。 如需詳細資訊，請參閱[樣式指南](../contributing-guidelines/template.md)。

少量變更的實際提交步驟都相同 ([上述](#process-for-contributing))。

Xamarin 小組會檢閱您的 PR，並會讓您知道變更是否適合 (透過 PR 意見反應)，或是否需要任何其他更新/變更才能核准。

套用意見反應且您的變更也適切之後，維護人員接著會合併 PR。

我們會依特定的頻率，將所有認可項目從主要分支推送到即時網站，您如此即可於 https://docs.microsoft.com/xamarin/ 看到您的投稿。

### <a name="contributing-to-international-content"></a>參與國際內容

目前不接受電腦轉譯（MT）內容的貢獻。 為了改善 MT 內容的品質，我們已轉換成類神經 MT 引擎。 我們接受並鼓勵人為翻譯（HT）內容的貢獻，這是用來訓練類神經 MT 引擎。 過了一段時間，對 HT 內容的貢獻將可改善 HT 和 MT 的品質。 MT 主題會有免責聲明，表示主題的部分可能是 MT，而 [**編輯**] 按鈕則不會顯示為已停用。

## <a name="dos-and-donts"></a>可進行及不可進行的事項

以下是一份您投稿 .NET 文件時，應謹記在心的指導規則清單。

- 請**不要**提交大量提取要求，讓我們措手不及。 而是請您先提出問題並開始討論，我們會在您投入大量的時間之前，先行確認方向。
- **請務必**閱讀[樣式指南](contributing-guidelines/template.md)與[語態和語氣](contributing-guidelines/voice-tone.md)指導方針。
- **請務必**使用[範本](contributing-guidelines/template.md)檔案作為您開始工作的起點。
- **請務必**在對文章進行作業之前，先在分叉上建立您自己的分支。
- **請務必**遵循 [GitHub 流程的工作流程](https://guides.github.com/introduction/flow/)。
- **請務必**利用部落格及推特 (或任何其他形式)，頻繁地發表您的文章！

> [!NOTE]
> 您可能注意到有部份的主題未完全遵循此處所指定的指導方針，以及[樣式指南](contributing-guidelines/template.md)。 我們目前正在努力達到整個網站的一致性。 
