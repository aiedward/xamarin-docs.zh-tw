---
title: 內容提供者的工作原理
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020522"
---
# <a name="how-content-providers-work"></a>內容提供者的工作原理

互動中涉及兩個`ContentProvider`類:

- **內容提供程式**&ndash;實現以標準方式公開一組數據的 API。 主要方法是查詢、插入、更新和刪除。

- **ContentResolver**&ndash;一種靜態代理,用於`ContentProvider`與通訊以造訪其資料,來自同一應用程式或其他應用程式。

內容提供程式通常由 SQLite 資料庫支援,但 API 意味著使用程式碼不需要瞭解有關基礎 SQL 的任何內容。 查詢透過 Uri 使用常量來引用列名稱(以減少對基礎資料結構的依賴項),並`ICursor`返回用於使用的代碼以反覆運算。

## <a name="consuming-a-contentprovider"></a>使用內容提供者

`ContentProviders`通過在發佈數據的應用程式的**AndroidManifest.xml**中註冊的 Uri 公開其功能。 有一種約定,Uri 和公開的數據列應作為常量可用,以便輕鬆綁定到數據。 Android 的內`ContentProviders`建 功能都提供了具有引用命名空間中數據結構的常[`Android.Providers`](xref:Android.Provider)量的便利類。

### <a name="built-in-providers"></a>內建提供者

Android 提供存取廣泛的系統與使用者`ContentProviders`資料使用 :

- *瀏覽器*&ndash;書籤和瀏覽器歷史記錄(需要`READ_HISTORY_BOOKMARKS`許可權 和`WRITE_HISTORY_BOOKMARKS`/或 )。

- *呼叫日誌*&ndash;最近隨設備發出或接聽的呼叫。

- *連絡人*&ndash;從使用者的聯繫人清單中的詳細資訊,包括人員、電話、照片&組。

- *MediaStore*&ndash;用戶設備的內容:音訊(專輯、藝術家、流派、播放清單)、圖像(包括縮略圖)&视频。

- *設定*&ndash;系統範圍的設備設置和首選項。

- 使用者定義的字典的使用者*詞典*&ndash;內容用於預測文本輸入。

- *Voicemail*語音郵件郵件的語音郵件歷史&ndash;記錄。

## <a name="classes-overview"></a>類概述

使用`ContentProvider`時使用 的主類如下所示:

[![內容提供應用程式應用程式和使用應用程式互動的類圖](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此圖中,`ContentProvider`實現其他應用程式用於查找資料的查詢和寄存器 URI。 充當`ContentResolver``ContentProvider`的 「代理」(查詢、插入、更新和刪除方法)。 `SQLiteOpenHelper`包含使用的數據`ContentProvider`,但不會直接暴露給正在使用的應用。
傳遞`CursorAdapter`返回的游標`ContentResolver`以顯示在`ListView`中。 是`UriMatcher`處理查詢時解析URI的幫助器類。

下面描述了每個類的目的:

- **ContentProvider**&ndash;實現此抽象類的方法來公開數據。 API 透過新增為類定義的 Uri 屬性提供給其他類和應用程式。

- **SQLiteOpenHelper**&ndash;協助存取 由公開的 SQLite 資料儲存`ContentProvider`。

- **UriMatcher**&ndash;`UriMatcher`在`ContentProvider`您的 實現中使用來説明管理用於查詢內容的 Uris。

- **內容解析器**&ndash;使用代碼`ContentResolver`使用`ContentProvider`訪問 實例。 這兩個類一起處理進程間通信問題,允許數據在應用程序之間輕鬆共用。 使用代碼從不顯式創建`ContentProvider`類;使用代碼從不顯式創建類。相反,通過基於`ContentProvider`應用程式公開的Uri創建遊標來訪問數據。

- **游標卡卡使用**&ndash;`CursorAdapter``SimpleCursorAdapter`或顯示透過訪問`ContentProvider`存取的資料。

API`ContentProvider`允許使用者對資料執行各種操作,例如:

- 查詢數據以返回清單或單個記錄。
- 修改單個記錄。
- 添加新記錄。
- 刪除記錄。

本文檔包含一個使用系統`ContentProvider`提供的範例,以及實現自`ContentProvider`訂的簡單唯讀示例。
