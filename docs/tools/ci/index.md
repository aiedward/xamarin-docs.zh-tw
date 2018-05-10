---
title: 使用 Xamarin 的連續整合簡介
description: 持續整合是軟體工程作法自動化的組建會編譯並新增或變更專案的版本控制儲存機制中的開發人員程式碼時，您可選擇測試應用程式。 本文將討論連續整合的一般概念，以及一些可用的選項為持續整合與 Xamarin 專案。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: topgenorth
ms.author: toopge
ms.date: 05/04/2017
ms.openlocfilehash: 34838a1527cb3661e8e5ed51b5950f26026e9433
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>使用 Xamarin 的連續整合簡介

_持續整合是軟體工程作法自動化的組建會編譯並新增或變更專案的版本控制儲存機制中的開發人員程式碼時，您可選擇測試應用程式。本文將討論連續整合的一般概念，以及一些可用的選項為持續整合與 Xamarin 專案。_

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]


##  <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[連續整合簡介](~/tools/ci/intro-to-ci.md)

本節涵蓋使用連續整合和其關聯性相關的不同元件。 本文將概述特定的以下各節中討論的連續整合環境。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="working-with-continuous-integration-environments"></a>使用持續整合環境


### <a name="using-app-center-build-with-xamarinappcenterbuildxamarin"></a>[使用 App Center 組建搭配 Xamarin](/appcenter/build/xamarin/)

直接從 GitHub、 VSTS 或 Bitbucket 來建置應用程式中心 Xamarin.iOS 和 Xamarin.Android 方案。

### <a name="using-teamcity-with-xamarintoolsciteamcitymd"></a>[使用 TeamCity 搭配 Xamarin](~/tools/ci/teamcity.md)

本指南會討論與使用 TeamCity 編譯行動應用程式，然後將它們提交至應用程式中心測試所需的步驟。

### <a name="using-jenkins-with-xamarintoolscijenkins-walkthroughmd"></a>[使用 Jenkins 搭配 Xamarin](~/tools/ci/jenkins-walkthrough.md)

本指南說明如何設定為持續整合伺服器 Jenkins 和自動化編譯建立使用 Xamarin 的行動裝置應用程式。 說明如何安裝 Jenkins OS X 上、 設定，以及設定變更認可至版本控制系統時，編譯 Xamarin.iOS 和 Xamarin.Android 應用程式的作業。
