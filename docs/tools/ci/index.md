---
title: 使用 Xamarin 的連續整合
description: 本檔連結的指南會描述與 Xamarin 的持續整合。 連結內容提供持續整合的總覽，並討論 App Center Build、TeamCity 和 Jenkins。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: davidortinau
ms.author: daortin
ms.date: 10/23/2018
ms.openlocfilehash: 9c87a65481ca58b2861c40a420459d629852f6b6
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997419"
---
# <a name="continuous-integration-with-xamarin"></a>使用 Xamarin 的連續整合

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integration"></a>[持續整合簡介](~/tools/ci/intro-to-ci.md)

本節涵蓋持續整合和其關聯性所牽涉到的不同元件。 其中概述下列特定章節所討論的持續整合環境。

## <a name="devops-with-xamarin"></a>[使用 Xamarin 應用程式進行 DevOps](~/tools/ci/devops.md)

本節將識別 Azure 中的哪些 DevOps 功能，以及您可以預期與 Xamarin 專案搭配運作的 Visual Studio。

## <a name="working-with-continuous-integration-environments"></a>使用持續整合環境

### <a name="build-xamarin-apps-with-azure-pipelines"></a>[使用 Azure Pipelines 建立 Xamarin 應用程式](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

使用 Azure Pipelines 自動建立適用于 Android 和 iOS 的 Xamarin 應用程式。

### <a name="build-xamarin-apps-using-app-center"></a>[使用 App Center 組建 Xamarin 應用程式](https://docs.microsoft.com/appcenter/build/xamarin/)

使用 App Center、直接從 GitHub、Azure DevOps 或 Bitbucket 建立 Xamarin. iOS 和 Xamarin Android 解決方案。

### <a name="build-xamarin-apps-with-teamcity"></a>[使用 TeamCity 組建 Xamarin 應用程式](~/tools/ci/teamcity.md)

本指南討論使用 TeamCity 來編譯行動應用程式，然後將其提交至 App Center 測試所牽涉到的步驟。

### <a name="build-xamarin-apps-with-jenkins"></a>[使用 Jenkins 組建 Xamarin 應用程式](~/tools/ci/jenkins-walkthrough.md)

本指南說明如何將 Jenkins 設定為持續整合伺服器，並自動編譯以 Xamarin 建立的行動應用程式。 其中說明如何在 OS X 上安裝 Jenkins、加以設定，以及在對版本控制系統認可變更時，設定作業來編譯 Xamarin. iOS 和 Xamarin 應用程式。
