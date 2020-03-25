---
title: Android 版面配置診斷
description: 說明 Android 版面配置診斷和如何開始使用
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 5c29a1a80d8c1f599f0bbc750d22d8334ddb3494
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247738"
---
# <a name="android-layout-diagnostics"></a>Android 版面配置診斷

Android 版面配置診斷的設計是藉由反白顯示常見的品質問題和實用的優化，協助改善 Android 版面配置檔案的品質。 這項功能適用于 Visual Studio 16.5 + 和 Visual Studio for Mac 8.5 +。

一組預設的分析器是針對各種不同的問題所提供，而且每個都可以自訂，以涵蓋專案的特定需求。 分析器會以 Android linting 系統為根據而鬆散。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>啟用 Visual Studio 2019 上的 Android 版面配置診斷

請確定已啟用 [配置診斷] 設定 [**啟用配置診斷**]。 若要存取此選項頁面，請選擇 **工具** > **選項**，然後選擇 **文字編輯器** > **Android XML** > **Advanced**：

![顯示如何啟用診斷選項的 [選項] 對話方塊](diagnostics-images/AndroidDiagnosticsEnableOption.png)

啟用之後，Android 版面配置編輯器會顯示問題：

![已在 Visual Studio 2019 上啟用 Android 診斷](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>在 Visual Studio for Mac 上啟用 Android 版面配置診斷

請確定已啟用 [配置診斷] 設定 [**啟用配置診斷**]。 若要存取此選項頁面，請選擇 [ **Visual Studio** > **喜好設定 ...** ]，然後選擇 [**文字編輯器**] > [ **Android XML**]：

![顯示如何啟用診斷選項的喜好設定對話方塊](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

啟用之後，Android 版面配置編輯器會顯示問題：

![已在 Visual Studio for Mac 上啟用 Android 診斷](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>特性

下列各節將概述 Android 版面配置診斷中的可用功能。

### <a name="analyzers"></a>分析器

分析器是用來協助偵測版面配置檔案中的問題。 有些方法可以減少硬式編碼的值、改善效能和旗標錯誤。

### <a name="diagnostic-configuration"></a>診斷設定

您可以使用 XML 檔案來設定分析器，讓您可以變更預設的嚴重性層級、忽略特定的檔案，以及傳入變數。

如果您想要在多個 Android 應用程式之間共用一組設定，您可以使用基準檔案。 若要使用這項功能，請建立新的設定檔，並將 `-baseline` 附加至檔案名。 基準設定會先套用，再套用其餘的設定檔。

> [!TIP]
> 如果您想要忽略新的或現有 Android 應用程式上的一組問題，這會很有用。

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
> 目前唯一的變數是 `MAX_VIEW_COUNT` （預設值：80）和 `MAX_DEPTH` （預設值：10）分別用於 `TooManyViews` 和 `TooDeepLayout`。

嚴重性層級如下：

- 建議
- 資訊
- 警告
- 錯誤
- 略過

### <a name="add-a-configuration-file"></a>新增組態檔

在 Android 應用程式專案的根目錄中建立新的 XML 檔案。 檔案的名稱並不重要，但此範例使用 `AndroidLayoutDiagnostics.xml`：

![新增項目](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

新增 XML 檔案之後，它應該會出現在 Android 應用程式專案樹狀結構中：

![Android 應用程式專案樹狀結構](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

請確定 [屬性] 面板中的 [組建] 動作已設定為 [ **AndroidResourceAnalysisConfig** ]。
若要提取新檔案的屬性面板，最簡單的方式是以滑鼠右鍵按一下檔案，然後選取 [屬性]。 在顯示 [屬性] 面板之後，您應該將**組建動作**變更為**AndroidResourceAnalysisConfig**：

![在專案屬性中設定組建動作](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

既然您有一個空白的 XML 檔案，就必須加入 `<configuration>` 根項目。 此時，您可以調整任何受支援問題的預設行為。
如果您想要確保將硬式編碼的字串視為錯誤，請新增：

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![診斷設定檔案](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

現在，硬式編碼的文字會視為錯誤，現在會在配置編輯器中以紅色波浪線標示：

![使用診斷設定的版面配置](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> 若要讓任何新的設定檔案變更生效，任何目前開啟的版面配置檔案都必須重新開啟。
>

## <a name="troubleshooting"></a>疑難排解

以下是一些可能的常見問題。

- 請確定沒有 XML 格式錯誤。
- 組建動作已正確設定為**AndroidResourceAnalysisConfig**。

## <a name="known-issues"></a>已知問題

- 第一次變更檔案之後，才會填入錯誤 pad。

## <a name="related-links"></a>相關連結

- [Android 不起毛檢查](http://tools.android.com/tips/lint-checks)
- [使用不起毛的檢查來改善您的程式碼](https://developer.android.com/studio/write/lint)
