---
title: Android 佈局診斷
description: 解釋 Android 佈局診斷以及如何入門
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 746f74e68fa4816f1f7979980af9506dc0173542
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987578"
---
# <a name="android-layout-diagnostics"></a>Android 佈局診斷

Android 佈局診斷旨在通過突出顯示常見的質量問題和有用的優化來説明提高 Android 佈局檔的品質。 此功能適用於適用於 Visual Studio 16.5+ 和適用於 Mac 8.5+ 的可視化工作室。

為各種問題提供了一組預設的分析器,每個分析器都可以自定義以滿足專案的特定需求。 分析儀鬆散地基於 Android 林丁系統。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>在視覺工作室 2019 啟用 Android 佈局診斷

開啟要啟動佈局診斷設定,**啟用佈局診斷**。 要存取這個選項頁,請選擇 **「工具** > **選項**」,然後選擇**文字編輯器** > **Android XML** > **進階**:

![展示如何啟用診斷選項的選項對話框](diagnostics-images/AndroidDiagnosticsEnableOption.png)

開啟後,Android 佈局編輯器將顯示問題:

![在視覺工作室 2019 啟用的 Android 診斷](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>在 Mac 視覺工作室啟用 Android 佈局診斷

開啟要啟動佈局診斷設定,**啟用佈局診斷**。 要存取此選項頁面,請選擇**視覺化工作室** > **偏好設定...** **Text Editor**  >  **Android XML**

![顯示如何啟用診斷選項的偏好設定對話框](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

開啟後,Android 佈局編輯器將顯示問題:

![在 Mac 視覺工作室啟用的 Android 診斷](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>特性

以下各節概述了 Android 佈局診斷中的可用功能。

### <a name="analyzers"></a>分析器

分析器用於幫助檢測佈局檔中的問題、減少硬編碼值、提高性能和標記錯誤。 有關分析儀清單,請參閱 Android[設計器診斷分析儀](diagnostic-analyzers.md)

### <a name="diagnostic-configuration"></a>診斷設定

可以使用 XML 檔配置分析器,允許您更改預設嚴重性級別、忽略某些檔以及傳遞變數。

如果您有一組要跨多個 Android 應用共用的配置,則可以使用基準檔。 要使用此功能,請建立新的設定檔並追加`-baseline`檔案名。 首先應用基線配置,然後應用其餘配置檔。

> [!TIP]
> 如果要忽略新或現有 Android 應用上的一組問題,這非常有用。

其格式為：

```xml
<?xml version="1.0" encoding="utf-8" ?> 
<configuration>
    <issue id="DuplicateIDs" severity="warning">
        <ignore path="Resources/layout/layout1.xml" />
    </issue>
    <issue id="HardcodedText" severity="informational">
        <ignore path="Resources/layout/layout1.xml" />
        <ignore path="Resource/layout/layout2.xml" />
    </issue>
    <issue id="TooManyViews">
        <variable name="MAX_VIEW_COUNT" value="12" />
    </issue>
    <issue id="TooDeepLayout">
        <variable name="MAX_DEPTH" value="12" />
    </issue>
</configuration>
```

> [!NOTE]
> 目前,唯一的變數`MAX_VIEW_COUNT`是 (預設值: 80)`MAX_DEPTH`和`TooManyViews` `TooDeepLayout` (預設值 : 10) 和

嚴重性層級如下：

- 建議
- 資訊
- 警告
- 錯誤
- 略過

### <a name="add-a-configuration-file"></a>新增組態檔

在 Android 應用專案的根目錄中創建新的 XML 檔。 檔案的名稱並不重要,但此範例使用`AndroidLayoutDiagnostics.xml`:

![新增項目](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

新增新的 XML 檔案後,該檔應出現在 Android 應用專案樹中:

![Android 應用程式專案樹](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

確保生成操作設置為屬性面板中的**AndroidResourceAnalysis Config。**
拉上新文件的屬性面板的最簡單方法是右鍵單擊該檔並選擇屬性。 顯示屬性面板後,應將**產生操作**變更為**Android 資源分析配置**:

![在項目屬性中設定產生操作](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

現在,您有一個空白的XML檔,您需要添加`<configuration>`根元素。 此時,您可以調整任何受支持問題的默認行為。
如果要確保硬編碼字串被視為錯誤,請新增:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![診斷設定檔](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

現在硬編碼文字被視為錯誤,現在它在佈局編輯器中用紅色波浪標記:

![使用診斷設定進行佈局](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> 要使任何新的設定檔更改生效,需要重新打開任何當前打開的佈局檔。
>

## <a name="troubleshooting"></a>疑難排解

下面是一些可能的常見問題。

- 確保沒有 XML 格式錯誤。
- 產生操作正確設定為**Android 資源分析分析設定**。

## <a name="known-issues"></a>已知問題

- 直到第一次更改檔後,才會填充錯誤鍵盤。

## <a name="related-links"></a>相關連結

- [安卓林特檢查](http://tools.android.com/tips/lint-checks)
- [使用絨毛檢查改進程式碼](https://developer.android.com/studio/write/lint)
