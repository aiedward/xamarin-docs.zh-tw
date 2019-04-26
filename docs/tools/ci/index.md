---
title: 使用 Xamarin 的連續整合簡介
description: 描述使用 Xamarin 的連續整合指南的這個文件連結。 連結的內容會提供設定連續整合的概觀，並討論 App Center 建置、 TeamCity 和 Jenkins。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: lobrien
ms.author: laobri
ms.date: 10/23/2018
ms.openlocfilehash: 073fc5abace2e0cb923394a359437528f703f338
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371075"
---
# <a name="continuous-integration-with-xamarin"></a>使用 Xamarin 的連續整合

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[連續整合簡介](~/tools/ci/intro-to-ci.md)

本節涵蓋使用持續整合和其關聯性相關的不同元件。 其中概述特定的以下各節中討論的連續整合環境。

## <a name="devops-with-xamarintoolscidevopsmd"></a>[使用 Xamarin 的 DevOps](~/tools/ci/devops.md)

本節會說明哪些 DevOps 功能在 Azure 和 Visual Studio，您可以預期與 Xamarin 專案搭配良好。

## <a name="working-with-continuous-integration-environments"></a>使用持續整合環境

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[建置 Xamarin 應用程式與 Azure 的管線](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

若要自動建置 Android 和 iOS 的 Xamarin 應用程式中使用 Azure 的管線。

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[建置 Xamarin 應用程式使用 App Center](https://docs.microsoft.com/appcenter/build/xamarin/)

直接從 GitHub、 Azure DevOps 或 Bitbucket 建置 Xamarin.iOS 和 Xamarin.Android 使用 App Center 的解決方案。

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[建置具有 TeamCity Xamarin 應用程式](~/tools/ci/teamcity.md)

本指南會討論使用 TeamCity 編譯行動應用程式，並再將它們提交到 App Center 測試所需的步驟。

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[建置 Xamarin 應用程式與 Jenkins](~/tools/ci/jenkins-walkthrough.md)

本指南說明如何設定 Jenkins 持續整合伺服器，並將自動化編譯建立使用 Xamarin 的行動裝置應用程式。 它說明如何在 OS X 上安裝 Jenkins、 加以設定，以及設定來變更認可至版本控制系統時，編譯 Xamarin.iOS 和 Xamarin.Android 應用程式的作業。
