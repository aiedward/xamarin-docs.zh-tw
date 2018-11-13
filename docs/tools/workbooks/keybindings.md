---
title: Xamarin 活頁簿編輯器鍵盤快速鍵
description: 本文件說明可在 Xamarin Workbooks 編輯器中使用的鍵盤快速鍵。 特別是，它會查看的各種資訊，請使用 Return 鍵。
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 87af9f824117b20250c02a3e070652607626de44
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526139"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Xamarin 活頁簿編輯器鍵盤快速鍵

## <a name="the-return-key-and-its-nuances"></a>傳回索引鍵和其細節，

下表描述執行程式碼和撰寫 markdown 的各種不同的按鍵繫結。 我們已十分小心選擇相當直覺且一致的按鍵繫結所熟悉且流暢。

|索引鍵繫結|程式碼儲存格|Markdown 資料格|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>如果插入號位於資料格緩衝區的結尾，而且可以成功地剖析資料格，就會執行緩衝區，下方會顯示結果和新的程式碼儲存格將會插入和焦點之後執行的資料格的資料格。</p><p>如果剖析不成功，新的一行會插入至緩衝區。 如果未成功剖析，將不會產生編譯器診斷。</p>|<p><kbd>傳回</kbd>表現不同行為的插入號位置的 Markdown 內容而定。</p><ul><li>如果插入點，Markdown 程式碼區塊中會插入常值的新行。</li><li>如果插入點，Markdown 清單區塊中建立新的清單項目，或分割目前的清單項目。</li><li>如果插入號位於 Markdown 區塊的任何其他型別，建立新的段落區塊或分割目前的區塊。</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>一律會嘗試剖析及執行資料格內容。 下方的緩衝區，如果編譯成功，會顯示結果 （包括執行例外狀況） 和一個新沒有後續的資料格時，會建立並已取得焦點。</p><p>如果有任何編譯錯誤，將會顯示診斷和使用插入號位置保持不變，緩衝區將繼續專注。</p>|插入，並著重在目前的 markdown 儲存格後的新的程式碼儲存格。|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|插入，並著重的目前儲存格後的新的 markdown 儲存格。|相同的行為<kbd>傳回</kbd>|
|<kbd>Shift‑Return</kbd>|一律會將新的一行，無論插入號位置或內容。|插入目前的 Markdown 區塊內硬碟分行符號。|
