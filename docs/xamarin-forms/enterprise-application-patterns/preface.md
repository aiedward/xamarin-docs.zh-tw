---
title: 在企業應用程式開發的前面
description: 本章提供使用 Xamarin 的企業應用程式模式的首碼。
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4ce04ec5216872cb56424e8847eec357a5b3ac0e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770747"
---
# <a name="preface-to-enterprise-app-development"></a>在企業應用程式開發的前面

本電子書提供使用 Xamarin 建立跨平臺企業應用程式的指引。 Xamarin 是一種跨平臺 UI 工具組，可讓開發人員輕鬆地建立可以跨平臺共用的原生使用者介面版面配置，包括 iOS、Android 和通用 Windows 平臺（UWP）。 它提供企業對員工（B2E）、企業對企業（B2B）和企業對消費者（B2C）應用程式的全方位解決方案，讓您能夠跨所有目標平臺共用程式碼，並協助降低擁有權總成本（TCO）。

本指南提供開發可調整、可維護和可測試之 Xamarin 的架構指引。表單企業應用程式。 提供指導方針，說明如何執行 MVVM、相依性插入、流覽、驗證和設定管理，同時維持鬆散結合。 此外，也有指導方針，說明如何使用 IdentityServer 來執行驗證和授權、從容器化微服務存取資料，以及單元測試。

本指南隨附 EShopOnContainers 行動裝置[應用程式](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)的原始程式碼，以及[eShopOnContainers reference 應用程式](https://github.com/dotnet-architecture/eShopOnContainers)的原始碼。 EShopOnContainers 行動應用程式是使用 Xamarin 開發的跨平臺企業應用程式，它會連接到一系列稱為「eShopOnContainers 參考」應用程式的容器化微服務。 不過，您可以將 eShopOnContainers 行動應用程式設定為使用模擬服務的資料，而那些想要避免部署容器化微服務的人員。

## <a name="whats-left-out-of-this-guides-scope"></a>本指南範圍的剩餘內容

本指南的目標讀者已熟悉 Xamarin. 表單。 如需有關 Xamarin 的詳細介紹，請參閱[xamarin 檔](~/xamarin-forms/index.yml)，以及[使用 xamarin 建立 Mobile Apps](https://aka.ms/xamebook)。

本指南是[.net 微服務的互補之處：容器化 .net 應用程式](https://aka.ms/microservicesebook)的架構，著重于開發和部署容器化微服務。 其他值得閱讀的指南包括使用[ASP.NET Core 和 Microsoft Azure 的架構和開發現代化 Web 應用](https://aka.ms/WebAppEbook)程式、[使用 Microsoft 平臺和工具的容器化 Docker 應用程式生命週期](https://aka.ms/dockerlifecycleebook)，以及[microsoft 平臺和適用于行動應用程式開發的工具](https://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>誰應該使用本指南

本指南的物件主要是開發人員和架構設計人員，他們想要瞭解如何使用 Xamarin 來架構和執行跨平臺的企業應用程式。

次要物件是技術決策者，想要在決定使用 Xamarin 建立跨平臺企業應用程式開發的何種方法之前，先接收架構與技術的總覽。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南著重于使用 Xamarin 建立跨平臺的企業應用程式。 因此，它應該完整閱讀，以提供瞭解這類應用程式及其技術考慮的基礎。 本指南及其範例應用程式也可以做為建立新企業應用程式的起點或參考。 使用相關聯的範例應用程式作為新應用程式的範本，或查看如何組織應用程式的元件部分。 然後，如需架構指引，請回頭參閱本指南。

歡迎您將本指南轉寄給小組成員，以協助確保對使用 Xamarin 的跨平臺企業應用程式開發有共同的瞭解。 讓每個人都能使用一組通用的術語和基礎原則，將有助於確保一致的架構模式和實務應用程式。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
