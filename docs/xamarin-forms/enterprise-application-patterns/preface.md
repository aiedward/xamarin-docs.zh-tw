---
title: ''
description: 本章提供使用的「企業應用程式模式」的首碼 Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b587bd8cacda9024103757a585c11fba2ed09fda
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130828"
---
# <a name="preface-to-enterprise-app-development"></a>在企業應用程式開發的前面

此電子書提供使用建立跨平臺企業應用程式的指引 Xamarin.Forms 。 Xamarin.Forms是一種跨平臺 UI 工具組，可讓開發人員輕鬆地建立可以跨平臺共用的原生使用者介面版面配置，包括 iOS、Android 和通用 Windows 平臺（UWP）。 它提供企業對員工（B2E）、企業對企業（B2B）和企業對消費者（B2C）應用程式的全方位解決方案，讓您能夠跨所有目標平臺共用程式碼，並協助降低擁有權總成本（TCO）。

本指南提供開發可調整、可維護及可測試之 Xamarin.Forms 企業應用程式的架構指引。 提供指導方針，說明如何執行 MVVM、相依性插入、流覽、驗證和設定管理，同時維持鬆散結合。 此外，也有指導方針，說明如何使用 IdentityServer 來執行驗證和授權、從容器化微服務存取資料，以及單元測試。

本指南隨附 EShopOnContainers 行動裝置[應用程式](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)的原始程式碼，以及[eShopOnContainers reference 應用程式](https://github.com/dotnet-architecture/eShopOnContainers)的原始碼。 EShopOnContainers 行動應用程式是使用所開發的跨平臺企業應用程式 Xamarin.Forms ，它會連接到一系列的容器化微服務，稱為 eShopOnContainers 參考應用程式。 不過，您可以將 eShopOnContainers 行動應用程式設定為使用模擬服務的資料，而那些想要避免部署容器化微服務的人員。

## <a name="whats-left-out-of-this-guides-scope"></a>本指南範圍的剩餘內容

本指南的目標讀者已熟悉 Xamarin.Forms 。 如需的詳細簡介 Xamarin.Forms ，請參閱[ Xamarin.Forms 檔](~/xamarin-forms/index.yml)，以及[使用 Xamarin.Forms 建立 Mobile Apps ](https://aka.ms/xamebook)。

本指南是[.Net 微服務的補充：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)，著重于開發和部署容器化微服務。 其他值得閱讀的指南包括使用 ASP.NET Core 和 Microsoft Azure、[容器化的 Docker 應用程式生命週期與 Microsoft 平臺和工具](https://aka.ms/dockerlifecycleebook)來[架構和開發現代化 Web 應用程式](https://aka.ms/WebAppEbook)，以及[用於行動應用程式開發的 microsoft 平臺和工具](https://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>誰應該使用本指南

本指南的物件主要是開發人員和架構設計人員，他們想要瞭解如何使用來架構和執行跨平臺的企業應用程式 Xamarin.Forms 。

次要物件是技術決策者，想要在決定使用哪一種方式進行跨平臺企業應用程式開發之前，先接收架構與技術的總覽 Xamarin.Forms 。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南著重于使用建立跨平臺的企業應用程式 Xamarin.Forms 。 因此，它應該完整閱讀，以提供瞭解這類應用程式及其技術考慮的基礎。 本指南及其範例應用程式也可以做為建立新企業應用程式的起點或參考。 使用相關聯的範例應用程式作為新應用程式的範本，或查看如何組織應用程式的元件部分。 然後，如需架構指引，請回頭參閱本指南。

歡迎您將本指南轉寄給小組成員，以協助確保對使用的跨平臺企業應用程式開發有共同的瞭解 Xamarin.Forms 。 讓每個人都能使用一組通用的術語和基礎原則，將有助於確保一致的架構模式和實務應用程式。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
