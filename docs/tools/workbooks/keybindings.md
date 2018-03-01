---
title: "Xamarin 活頁簿編輯器鍵盤快速鍵"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: cbbf070a9c94221d98dedcd1df884a897ff7df3e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Xamarin 活頁簿編輯器鍵盤快速鍵

## <a name="the-return-key-and-its-nuances"></a>傳回索引鍵和其細節

下表描述各種索引鍵繫結的執行程式碼，並撰寫 markdown。 我們已十分小心選擇合理且一致的熟悉且流暢按鍵繫結。

<table>
  <tr>
    <th>索引鍵繫結</th>
    <th>程式碼的儲存格</th>
    <th>Markdown 資料格</th>
  </tr>
  <tr>
    <td><kbd>Return</kbd></td>
    <td>
      <p>如果插入號是在儲存格緩衝區的結尾，而且可以成功地剖析資料格，將會執行結果將會顯示以下緩衝區，及新的程式碼儲存格就插入和焦點之後執行的資料格的資料格。</p>
      
      <p>如果剖析不成功，將緩衝區中插入新行。 如果未成功剖析，將不會產生編譯器診斷。</p>
    </td>
    <td>
      <p>
        <kbd>傳回</kbd>會表現不同行為根據插入號的 Markdown 內容。
      </p>
      <ul>
        <li>
如果插入號是在 Markdown 程式碼區塊，則會插入常值的新行。
        </li>
        <li>
如果插入號是在 Markdown 清單區塊中，建立新的清單項目，或分割目前的清單項目。
        </li>
        <li>
如果插入號位於其他任何類型的 Markdown 區塊，建立新的段落區塊或分割目前的區塊。
        </li>
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Return</kbd></dd>
      </dl>
    </td>
    <td>
      <p>一律會嘗試剖析和執行資料格內容。 以下緩衝區，如果編譯成功，會顯示結果 （包括執行例外狀況），然後新如果有任何後續的資料格，會建立並已取得焦點。</p>
      
      <p>如果沒有任何編譯錯誤，將會顯示診斷和緩衝區會保留不變的插入號位置已取得焦點。</p>
    </td>
    <td>
插入，並著重在目前的 markdown 儲存格後新的程式碼儲存格。
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Shift‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Shift‑Return</kbd></dd>
      </dl>
    </td>
    <td>
插入，並將新的 markdown 儲存格著重之後的目前儲存格。
    </td>
    <td>
相同的行為<kbd>傳回</kbd>
    </td>
  </tr>
  <tr>
    <td><kbd>Shift‑Return</kbd></td>
    <td>
一律插入新行，不論插入號位置或內容。
    </td>
    <td>
插入目前的 Markdown 區塊內硬碟分行符號。
    </td>
  </tr>
</table>
