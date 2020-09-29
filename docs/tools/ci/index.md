---
title: 使用 Xamarin 的連續整合
description: 本檔連結的指南會說明與 Xamarin 的持續整合。 連結的內容提供持續整合的總覽，並討論 App Center 組建、TeamCity 和 Jenkins。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: davidortinau
ms.author: daortin
ms.date: 10/23/2018
ms.openlocfilehash: 357bf2c6e137aacd471b517852f3a7b424af3f7d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456570"
---
# <a name="continuous-integration-with-xamarin"></a>使用 Xamarin 的連續整合

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integration"></a>[持續整合簡介](~/tools/ci/intro-to-ci.md)

本節涵蓋與持續整合和其關聯性相關的不同元件。 它概述下列特定章節中所討論的持續整合環境。

## <a name="devops-with-xamarin"></a>[使用 Xamarin 應用程式進行 DevOps](~/tools/ci/devops.md)

本節將說明 Azure 中的哪些 DevOps 功能，以及您可以預期在 Xamarin 專案中使用的 Visual Studio。

## <a name="working-with-continuous-integration-environments"></a>使用持續整合環境

### <a name="build-xamarin-apps-with-azure-pipelines"></a>[使用 Azure Pipelines 建立 Xamarin 應用程式](/azure/devops/pipelines/languages/xamarin/)

使用 Azure Pipelines 自動建立適用于 Android 和 iOS 的 Xamarin 應用程式。

### <a name="build-xamarin-apps-using-app-center"></a>[使用 App Center 建立 Xamarin 應用程式](/appcenter/build/xamarin/)

使用 App Center、直接從 GitHub、Azure DevOps 或 Bitbucket，打造 Xamarin iOS 和 Xamarin Android 解決方案。

### <a name="build-xamarin-apps-with-teamcity"></a>[使用 TeamCity 建立 Xamarin 應用程式](~/tools/ci/teamcity.md)

本指南將討論使用 TeamCity 來編譯行動應用程式，然後將其提交至 App Center 測試所需的步驟。

### <a name="build-xamarin-apps-with-jenkins"></a>[使用 Jenkins 建立 Xamarin 應用程式](~/tools/ci/jenkins-walkthrough.md)

本指南說明如何將 Jenkins 設定為連續整合伺服器，並自動編譯以 Xamarin 建立的行動應用程式。 它說明如何在 OS X 上安裝 Jenkins、進行設定，以及設定作業，以在將變更認可至版本控制系統時，編譯 Xamarin 和 Xamarin. Android 應用程式。