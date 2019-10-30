---
title: 使用 Xamarin 應用程式進行 DevOps
ms.assetid: ff978cc2-5a25-46d6-921b-e51adaa65992
author: davidortinau
ms.author: daortin
manager: crdun
ms.workload:
- xamarin
ms.date: 10/23/2018
ms.openlocfilehash: 57e3444ebcbd87607da248ecacca5e76a44f1577
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007442"
---
# <a name="devops-with-xamarin"></a>使用 Xamarin 應用程式進行 DevOps

Xamarin 可讓您使用 C#、.NET 及 Visual Studio，來建置目標為 Android、iOS 及 Windows 的跨平台行動裝置應用程式。 Xamarin 讓大部分的程式碼可在平台之間共用，只有極低比例的程式碼需要在特定平台上運作。

開發新型平台的應用程式比起只撰寫程式碼牽涉到更多活動。 這些稱為 DevOps (開發 + 營運) 的活動橫跨了應用程式的整個生命週期，包含規劃和追蹤進度、設計和實作程式碼、管理原始程式碼存放庫、執行組建、管理持續整合和部署、測試 (包含單元測試和 UI 測試)、在開發和生產環境中執行各種形式的診斷，以及透過遙測和分析即時監視應用程式效能和使用者行為。

Visual Studio 與 Azure DevOps Services 和 Team Foundation Server 共同提供各式各樣的 DevOps 功能。 其中有許多功能完全適用於跨平台專案。 這對 Xamarin 應用程式更是如此，因為這些應用程式使用 C# 和 .NET 來建置，也就是某些 DevOps 工具用以建置的途徑。 其他工具需要與建置和執行階段環境緊密整合。 因為 Xamarin 應用程式在非 Windows 平台上執行，並使用 .NET 的 Mono 實作，所以 Xamarin 會對某些特殊需求提供工具。

下表會區分 Visual Studio 中可與 Xamarin 專案順利搭配的 DevOps 功能，以及有其限制的功能。 請參閱連結文件以取得這項功能的詳細資訊。

## <a name="agile-tools"></a>Agile 工具

參考連結： **[有關敏捷式工具和敏捷式專案管理](/azure/devops/boards/backlogs/overview?view=vsts)**

一般註解：所有的計劃和追蹤功能都與專案類型和程式碼撰寫語言無關。

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|管理待處理項目和衝刺 (Sprint)|[是]||
|工作追蹤|[是]||
|小組聊天室共同作業|[是]||
|看板|[是]||
|報告和視覺化進度|[是]||

## <a name="modeling"></a>模型化

參考連結： **[分析架構並製作架構模型](/visualstudio/modeling/analyze-and-model-your-architecture)**

設計功能與程式碼撰寫語言無關，或者它會使用如 C# 等 .NET 語言。 若要了解哪些層面與程式碼相關，請參閱[架構與模型圖表在軟體開發中的角色](/visualstudio/modeling/scenario-change-your-design-using-visualization-and-modeling#ModelingDiagramsTools)。

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|順序圖表|[是]||
|相依性圖形|[是]||
|呼叫階層|[是]||
|類別設計工具|[是]||
|架構總管|[是]||
|UML 圖表 (使用案例、活動、類別、元件、序列和 DSL)|[是]||
|圖層圖表|[是]||
|圖層驗證|[是]||

## <a name="code"></a>程式碼

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|[使用 Team Foundation 版本控制 (TFVC)](/azure/devops/repos/tfvc/overview?view=vsts) 或 Azure Repos|[是]||
|[開始使用 Azure Repos 中的 Git](/azure/devops/repos/git/gitquickstart?view=vsts&tabs=visual-studio)|[是]||
|[改善程式碼品質](/visualstudio/test/improve-code-quality)|[是]||
|[尋找程式碼變更和其他記錄](/visualstudio/ide/find-code-changes-and-other-history-with-codelens)|[是]|除了跨越這些到執行階段仍未解析實作的平台特定界限以外。|
|[使用 Code Map 偵錯您的應用程式](/visualstudio/modeling/use-code-maps-to-debug-your-applications)|[是]||

## <a name="build"></a>組建

參考連結： **[Azure Pipelines](/azure/devops/pipelines/index?view=vsts)**

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|內部部署 TFS 伺服器|[是]|組建電腦必須安裝 Xamarin，而且必須可連結到 OSX 電腦，以便為 iOS 進行建置。 請參閱[使用 TFVC](/azure/devops/repos/tfvc/overview?view=vsts)|
|連結至 Azure Pipelines 的內部部署組建伺服器|[是]|相關指示，請參閱[建置和發行代理程式](/azure/devops/pipelines/agents/agents?view=vsts)。|
|Azure Pipelines 裝載的控制器服務|[是]|[建置您的 Xamarin 應用程式 (英文)](/azure/devops/pipelines/languages/xamarin?view=vsts&tabs=vsts)。|
|具有預先定義和後置指令碼的組建定義|[是]||
|包括閘道簽入的連續整合|[是]|TFVC 的閘道簽入，只適用於 Git 在提取要求模型上運作的時候，而不是簽入運作時。|

## <a name="test"></a>測試

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|規劃測試、建立測試案例和組織測試套件|[是]||
|手動測試|[是]||
|測試管理員 (錄製和播放測試)|[是]|僅限來自 Visual Studio 的 Windows 裝置及 Android 模擬器。|
|程式碼涵蓋範圍|N/A||
|[對程式碼進行單元測試](/visualstudio/test/unit-test-your-code/)|[是]|對於 Windows 和 Android 的目標，可以使用內建的 MSTest 工具。 若要在 Windows、Android 和 iOS 上執行單元測試，Xamarin 會建議使用 NUnit。 請參閱[使用 TFVC](/azure/devops/repos/tfvc/overview?view=vsts)。|
|[使用 UI 自動化來測試您的程式碼](/visualstudio/test/use-ui-automation-to-test-your-code/)|僅限 Windows|Visual Studio 的 UI 測試錄製器僅適用 Windows。 針對所有平台，請參閱 [Xamarin.UITest](/appcenter/test-cloud/uitest/)。|

## <a name="improve-code-quality"></a>改善程式碼品質

參考連結︰ **[改善程式碼品質](/visualstudio/test/improve-code-quality)**

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|[分析受控程式碼品質](/visualstudio/code-quality/analyzing-managed-code-quality-by-using-code-analysis)|[是]||
|[使用程式碼複製品偵測來尋找重複程式碼](https://msdn.microsoft.com/library/hh205279.aspx)|[是]||
|[測量受控程式碼的複雜度和維護性](/visualstudio/code-quality/measuring-complexity-and-maintainability-of-managed-code)|[是]||
|[效能總管](/visualstudio/profiling/performance-explorer)|否|請改為透過 Visual Studio for Mac 使用[Xamarin Profiler](/xamarin/tools/profiler/) 。 請注意 Xamarin 分析工具目前為預覽狀態，而且尚未適用於 Windows 目標。|
|[分析 .NET Framework 記憶體問題](https://msdn.microsoft.com/library/dn342825.aspx)|否|Visual Studio 工具並不會為程式碼剖析而連結 Mono 架構。|

## <a name="release-management"></a>版本管理

參考連結︰ **[在 Azure Pipelines 和 TFS 中建置和發行](/azure/devops/pipelines/overview?view=vsts)**

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|管理發行處理序|[是]||
|部署至伺服器以便透過指令碼進行側面載入|[是]||
|上傳至應用程式存放區|Partial|您可以針對某些應用程式存放區，使用擴充功能來自動化此程序。  請參閱 [Extensions for Azure DevOps Services](https://marketplace.visualstudio.com/VSTS) (適用於 Azure DevOps Services 的延伸模組)；例如 [extension for Google Play](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play) (適用於 Google Play 的延伸模組)。|

## <a name="monitor-with-hockeyapp"></a>使用 HockeyApp 監視

參考連結︰ **[使用 HockeyApp 監視 (英文)](https://www.hockeyapp.net/features/)**

|特殊功能|支援 Xamarin|其他註解|
|-------------|----------------------------|-------------------------|
|當機分析、遙測和 Beta 發佈|[是]||
