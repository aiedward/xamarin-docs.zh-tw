---
title: 一般常見問題集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 06aa6569301d1bfdbf9f6fd1e7397a38a9beb6f6
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350816"
---
# <a name="general-frequently-asked-questions"></a>一般常見問題集

## <a name="portable-class-libraries"></a>可攜式類別庫

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[如何檢視 PCL 中所支援的程式庫？](pcl-support-libraries.md)
本指南列出資源和方法來判斷如果現有的程式庫支援各種的 PCL 目標平台，或可以轉換成的 PCL 設定檔。

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[PCL 映射 API](pcl-reflection.md)
Microsoft 開發了新的反映 API，以用於可攜式類別庫。 如果您有一些現有的反映程式碼，您想要移至 PCL，它可能無法運作。

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[PCL 案例研究：如何解決 Microsoft TPL Dataflow NuGet 套件有關 System.Diagnostics.Tracing 的問題？](pcl-case-study.md)
Xamarin.iOS 和 Xamarin.Android 不會實作的每個可做為參考的 PCL 設定檔的 100%。 為了方便在 Visual Studio for Mac 中，Visual Studio 和 NuGet 套件管理員的實用起見，Xamarin 專案會允許使用數個設定檔僅具有不完整的實作。 例如，Xamarin.iOS 和 Xamarin.Android 都不目前包含的類型中的完整實作`System.Diagnostics.Tracing`PCL 命名空間。 藉由切換應用程式專案，以參考 portable-net45 + win8 + wp8 + wpa81 解決這個 TPL 資料流程程式庫版本。

## <a name="nuget-packages--xamarin-components"></a>NuGet 套件和 Xamarin 元件
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[如何更新 NuGet？](nuget-update.md)
NuGet 更新、 擴充功能和增益集可以下找到**更新**索引標籤中**NuGet 套件管理員**。 若要尋找 Visual Studio 中的更新，針對 Mac 和 Visual Studio 的詳細的導覽是本指南中。

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[如何下載 NuGet 套件？](nuget-package-downgrade.md)
Visual Studio for Mac 及 Visual Studio 已選取較舊版本的套件並將它們，而安裝的功能類似於如何更新封裝的運作方式。

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[更新 Nuget 套件之後的遺失套件錯誤](nuget-packages-missing.md)
主要的 Xamarin.Forms 範例應用程式解決方案，報告此問題，但此問題的可能性可能位於任何使用 NuGet 套件的專案。

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[統一 Google Play 服務元件和 NuGet](gps-components-nuget.md)
那里使用數個 Google Play 服務元件和 NuGet 套件，但為了方便開發人員，我們已現在整合我們的元件和 NuGet 套件分成兩個。 在幾乎每個案例中，您應該使用 Google Play 服務。 若要使用 (Froyo) 封裝的唯一原因是如果您目前的目標 Froyo。

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[元件儲存在我電腦上的何處？](component-storage.md)
只要您的 Xamarin 元件安裝到應用程式專案時，它位於本指南中所列的兩個位置。


## <a name="troubleshooting"></a>疑難排解
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[可以在哪裡找到我的版本資訊和記錄檔？](version-logs.md)
本指南詳細說明哪裡可以找到大部分的診斷資訊，可用來針對 Xamarin 問題進行疑難排解。

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[提出錯誤報告的時機與方式為何？](howto-file-bug.md)
本指南會提供秘訣來提出高品質的問題報告，使我們的工程師能夠更有效率的問題判斷原因 （以及任何可能的修正）。

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Xamarin 為什麼不支援 Jenkins？](xamarin-jenkins.md)
Jenkins 是開放原始碼 CI 套件;因為這麼多直接由 Jenkins 所造成的問題*本身*需要被歸類為問題對您取得程式碼的位置; 例如[主要 Jenkins 儲存機制](https://github.com/jenkinsci/jenkins)，或存放庫[Jenkins.app](https://github.com/stisti/jenkins-app)。

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[偵錯工具需要哪些專案設定？](debugger-settings.md)
為了讓偵錯工具正常運作 （叫用的中斷點，顯示偵錯記錄檔等），開發人員檢測和偵錯資訊顯示必須同時啟用。 本指南詳細說明如何尋找並啟用這些設定。

