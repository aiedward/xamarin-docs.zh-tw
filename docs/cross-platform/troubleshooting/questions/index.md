---
title: 一般常見問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: de70eda2898f29a1e7afed9440d5f5fae496e069
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765117"
---
# <a name="general-frequently-asked-questions"></a>一般常見問題

## <a name="portable-class-libraries"></a>可攜式類別庫

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[如何檢視 PCL 中所支援的程式庫？](pcl-support-libraries.md)
本指南列出用來判斷各種 PCL 目標平臺是否支援您現有程式庫，或可以轉換成 PCL 設定檔的資源和方法。

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[PCL 映射 API](pcl-reflection.md)
Microsoft 開發了新的反映 API，以便在可移植的類別庫中使用。 如果您有一些您想要移至 PCL 的現有反映程式碼，可能會無法正常執行。

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[PCL 案例研究：如何解決與診斷相關的問題。 Microsoft TPL 資料流程 NuGet 套件的追蹤功能？](pcl-case-study.md)
Xamarin. iOS 和 Xamarin 不會將其允許的每個 PCL 設定檔的 100% 實作為參考。 為了在 Visual Studio for Mac、Visual Studio 和 NuGet 套件管理員中實用的便利性，Xamarin 專案允許使用只具有不完整之執行的數個設定檔。 例如，xamarin 或 xamarin 目前都不包含`System.Diagnostics.Tracing` PCL 命名空間中的完整類型。 若要解決此情況，您可以將應用程式專案切換為參考 TPL 資料流程程式庫的 net45 + win8 + wp8 + wpa81 版本。

## <a name="nuget-packages--xamarin-components"></a>& Xamarin 元件的 NuGet 套件
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[如何更新 NuGet？](nuget-update.md)
在**Nuget 套件管理員**的 [**更新**] 索引標籤底下，可以找到 nuget 更新、擴充功能和增益集。 本指南中的詳細導覽以尋找 Visual Studio for Mac & Visual Studio 中的更新。

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[如何下載 NuGet 套件？](nuget-package-downgrade.md)
Visual Studio for Mac & Visual Studio 都具有選取舊版套件並自動安裝的功能;與更新封裝的運作方式類似。

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[更新 Nuget 套件之後的遺失套件錯誤](nuget-packages-missing.md)
這個問題主要是在 Xamarin 上回報。表單範例應用程式解決方案，但此問題的潛能可能發生在任何使用 NuGet 套件的專案上。

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[統一 Google Play 服務元件和 NuGet](gps-components-nuget.md)
其中有數個 Google Play Services 元件和 NuGet 套件，但為了讓開發人員更容易，我們現在已將元件和 NuGet 套件整合成兩個。 在幾乎每個案例中，都應該使用 Google Play Services。 使用（Froyo）套件的唯一理由是，如果您主動將目標設為 Froyo。

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[元件儲存在我電腦上的何處？](component-storage.md)
每當您將 Xamarin 元件安裝到應用程式專案時，它會放在本指南所列的兩個位置中。

## <a name="troubleshooting"></a>疑難排解
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[可以在哪裡找到我的版本資訊和記錄檔？](version-logs.md)
本指南詳細說明哪裡可以找到可用於針對 Xamarin 問題進行疑難排解的大部分診斷資訊。

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[提出錯誤報告的時機與方式為何？](howto-file-bug.md)
本指南提供如何為高品質的錯誤報表進行歸檔的秘訣，讓我們的工程師能夠更有效率地判斷問題的原因（以及任何可能的修正程式）。

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Xamarin 為什麼不支援 Jenkins？](xamarin-jenkins.md)
Jenkins 是開放原始碼 CI 套件;因為這許多由 Jenkins*本身*直接造成的問題，都必須針對您的程式碼所在位置提出問題。例如[主要 Jenkins](https://github.com/jenkinsci/jenkins)存放庫，或[Jenkins 應用程式](https://github.com/stisti/jenkins-app)的存放庫。

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[偵錯工具需要哪些專案設定？](debugger-settings.md)
為了讓偵錯工具如預期般運作（命中中斷點、顯示偵錯工具記錄等），必須同時啟用開發人員檢測和偵錯工具資訊顯示。 本指南將詳細說明如何尋找和啟動這些設定。
