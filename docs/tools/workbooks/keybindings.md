---
title: Xamarin Workbooks 編輯器鍵盤快速鍵
description: 本檔說明可在 [Xamarin Workbooks 編輯器] 中使用的鍵盤快速鍵。 特別是，它會查看傳回金鑰的各種使用方式。
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 9904d0f9fb1acfc3c3c197b9881c2add00aba534
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285319"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Xamarin Workbooks 編輯器鍵盤快速鍵

## <a name="the-return-key-and-its-nuances"></a>Return 鍵和其細微處

下表描述用來執行程式碼和撰寫 markdown 的各種按鍵系結。 我們非常謹慎地選擇既熟悉又一致的索引鍵系結。

|按鍵系結|程式碼資料格|Markdown 資料格|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>如果插入號位於資料格緩衝區的結尾，而且資料格可以成功剖析，就會執行它，而且結果會顯示在緩衝區下方，而新的程式碼資料格將會在執行的資料格之後插入和專注的儲存格。</p><p>如果剖析不成功，則會在緩衝區中插入新的一行。 如果剖析不成功，將不會產生編譯器診斷。</p>|<p>根據插入點的 Markdown 內容，<kbd>傳回表現不同</kbd>的行為。</p><ul><li>如果插入號是在 Markdown 程式碼區塊中，則會插入常值的新行。</li><li>如果插入號位於 Markdown 清單區塊中，請建立新的清單專案或分割目前的清單專案。</li><li>如果插入號是在任何其他類型的 Markdown 區塊中，請建立新的段落區塊或分割目前的區塊。</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>一律會嘗試剖析和執行資料格內容。 如果編譯成功，則會在緩衝區下方顯示結果（包括執行例外狀況），如果沒有後續的資料格，則會建立新的資料格，並將焦點放在其中。</p><p>如果發生任何編譯錯誤，將會顯示診斷，而緩衝區會維持以插入號位置不變的焦點。</p>|將新的程式碼資料格插入並聚焦于目前的 markdown 資料格之後。|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|在目前儲存格之後插入新的 markdown 資料格，並將焦點放在其中。|與<kbd>Return</kbd>相同的行為|
|<kbd>Shift‑Return</kbd>|無論插入號位置或內容為何，一律會插入新的一行。|在目前的 Markdown 區塊內插入硬式換行。|
