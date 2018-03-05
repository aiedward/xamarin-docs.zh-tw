---
title: "一般常見問題集"
ms.topic: article
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 0c01bfb08d525fd336b29f405304efaeaf749f35
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="general-frequently-asked-questions"></a>一般常見問題集

## <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 發行候選版本
### <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarinvisualstudio-2017-rcmd"></a>[可以使用 Visual Studio 2017 發行候選版本使用 Xamarin 嗎？](visualstudio-2017-rc.md)
如需如何安裝 Visual Studio2017 RC 中的 Xamarin 的詳細資訊與使用 Visual Studio 2017 Release Candidate (RC) 以及使用 Xamarin 的目前含意的說明。

## <a name="portable-class-libraries"></a>可攜式類別庫
### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[如何檢視 PCL 中支援哪些文件庫？](pcl-support-libraries.md)
本指南列出的資源和方法來判斷如果現有的程式庫支援的各種 PCL 目標平台，或可以轉換成 PCL 設定檔。

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[PCL 反映 API](pcl-reflection.md)
Microsoft 開發了一個新的反映 API 使用可攜式類別庫中。 如果您有想要移至 PCL 某些現有反映程式碼時，它可能無法運作。

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[PCL 案例研究： 如何解決 System.Diagnostics.Tracing Microsoft TPL 資料流程 NuGet 套件的相關問題？](pcl-case-study.md)
Xamarin.iOS 和 Xamarin.Android 不會實作每個可做為參考 PCL 設定檔的 100%。 為了在 Visual Studio for Mac、 Visual Studio 和 NuGet 套件管理員中的實際起見，Xamarin 專案可讓使用只具有完整實作的數個設定檔。 例如，Xamarin.iOS 和 Xamarin.Android 都不目前包含的完整實作中的型別`System.Diagnostics.Tracing`PCL 命名空間。 藉由切換應用程式專案，以參考可攜式 net45 + win8 + wp8 + wpa81 解決這 TPL 資料流程程式庫版本。

## <a name="nuget-packages--xamarin-components"></a>NuGet 封裝 （& s) Xamarin 元件
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[如何更新 NuGet？](nuget-update.md)
NuGet 更新、 延伸與增益集可以下找到**更新**索引標籤中**NuGet 套件管理員**。 詳細的導覽，Visual Studio 中找到的更新針對 Mac 和 Visual Studio 會在本指南中。

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[我要如何降級 NuGet 封裝？](nuget-package-downgrade.md)
Visual Studio for Mac 和 Visual Studio 有選取封裝的舊版本，以及自動; 安裝這些功能類似於更新封裝的運作方式。

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Nuget 封裝在更新之後，遺失封裝錯誤](nuget-packages-missing.md)
主要 Xamarin.Forms 範例應用程式方案，在報告此問題，但此問題的可能性可能發生在任何使用 NuGet 封裝的專案上。

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[元件和 NuGet 統一 Google Play 服務](gps-components-nuget.md)
那里使用數個 Google 播放服務元件並 NuGet 封裝，但是為了方便開發人員，我們已現在統一我們元件和 NuGet 套件分成兩個。 在大多數情況下，應該使用 Google Play 服務。 若要使用 (Froyo) 封裝的唯一理由是如果您的主動目標 Froyo。

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[元件儲存我的電腦？](component-storage.md)
每當您 Xamarin 元件安裝到應用程式專案時，它取得放在本指南中所列的兩個位置中。


## <a name="troubleshooting"></a>疑難排解
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[可以在哪裡找到我的版本資訊和記錄檔？](version-logs.md)
這個指南詳細說明如何尋找最多可以用來疑難排解 Xamarin 問題的診斷資訊。

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[時機和方式我應該提出問題報告？](howto-file-bug.md)
本指南提供秘訣提出高品質 bug 報告，讓我們的工程師可以更有效率地可解決問題的判斷可能的原因 （以及任何可能的修正）。

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[為什麼不 Jenkins 支援 Xamarin？](xamarin-jenkins.md)
Jenkins 是一個開放原始碼 CI 組合。因為這許多直接 Jenkins 所造成的問題，所以*本身*需要被歸類為針對您從哪裡取得程式碼; 這類問題[主要 Jenkins 儲存機制](https://github.com/jenkinsci/jenkins)，或為儲存機制[Jenkins.app](https://github.com/stisti/jenkins-app)。

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[偵錯工具需要何種專案設定？](debugger-settings.md)
為了讓偵錯工具可正常運作 （點擊的中斷點、 顯示偵錯記錄檔等），開發人員檢測和偵錯資訊顯示皆必須啟用。 本指南詳細說明如何尋找並啟用這些設定。
