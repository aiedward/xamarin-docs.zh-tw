---
title: 在企業應用程式開發的前面
description: 本章提供使用的企業應用程式模式的前言 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1c5af73c7c7fd160f715d7dbfab26c72cb06a31
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371543"
---
# <a name="preface-to-enterprise-app-development"></a>在企業應用程式開發的前面

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

此電子書提供使用建立跨平臺企業應用程式的指引 Xamarin.Forms 。 Xamarin.Forms 是一種跨平臺 UI 工具組，可讓開發人員輕鬆地建立可跨平臺共用的原生使用者介面版面配置，包括 iOS、Android 和通用 Windows 平臺 (UWP) 。 它提供全方位的解決方案，讓企業對員工 (B2E) 、企業對企業 (B2B) ，以及企業對取用者 (B2C) 應用程式，讓您能夠在所有目標平臺之間共用程式碼，並協助降低擁有權總成本 (TCO) 。

本指南提供架構指引，以開發可調整、可維護且可測試的 Xamarin.Forms 企業應用程式。 提供指導方針，說明如何實行 MVVM、相依性插入、流覽、驗證和設定管理，同時維持鬆散結合性。 此外，也有指導方針，說明如何使用 IdentityServer 來執行驗證和授權、從容器化微服務存取資料，以及單元測試。

本指南隨附 [eShopOnContainers 行動應用程式](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)的原始程式碼，以及 [eShopOnContainers 參考應用程式](https://github.com/dotnet-architecture/eShopOnContainers)的原始程式碼。 EShopOnContainers 行動應用程式是使用開發的跨平臺企業應用程式 Xamarin.Forms ，它會連線到一連串的容器化微服務，稱為 eShopOnContainers 參考應用程式。 不過，eShopOnContainers 行動應用程式可以設定為使用來自模擬服務的資料，以供想要避免部署容器化微服務的人員使用。

## <a name="whats-left-out-of-this-guides-scope"></a>本指南範圍的剩餘內容

本指南的目標讀者已熟悉 Xamarin.Forms 。 如需的詳細簡介 Xamarin.Forms ，請參閱[ Xamarin.Forms 檔](~/xamarin-forms/index.yml)，以及[使用 Xamarin.Forms 建立 Mobile Apps ](https://aka.ms/xamformsebook)。

本指南可補充 [.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)，其著重于開發和部署容器化微服務。 其他需要閱讀的指南包括使用 ASP.NET Core 和 Microsoft Azure、[使用 Microsoft 平臺和工具的容器化 Docker 應用程式生命週期](https://aka.ms/dockerlifecycleebook)，以及[適用于行動應用程式開發的 Microsoft 平臺和工具，來](https://aka.ms/MobAppDev/StndPDF)[設計和開發新式 Web 應用程式](https://aka.ms/WebAppEbook)。

## <a name="who-should-use-this-guide"></a>誰應該使用本指南

本指南的物件主要是開發人員和架構設計人員，想要瞭解如何使用來設計和執行跨平臺的企業應用程式 Xamarin.Forms 。

次要物件是技術決策者，他們想要在決定使用哪種方法來進行跨平臺企業應用程式開發之前，先接收架構和技術總覽 Xamarin.Forms 。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南著重于使用建立跨平臺的企業應用程式 Xamarin.Forms 。 因此，您應該完整閱讀，以提供瞭解這類應用程式及其技術考慮的基礎。 本指南連同其範例應用程式，也可以做為建立新企業應用程式的起點或參考。 使用相關聯的範例應用程式做為新應用程式的範本，或瞭解如何組織應用程式的元件部分。 然後，請回頭參閱本指南以取得架構指引。

您可以隨時將本指南轉寄給小組成員，以協助確保使用的跨平臺企業應用程式開發有普遍的瞭解 Xamarin.Forms 。 讓每個人都能使用一組通用的術語和基礎原則，將有助於確保架構模式和實務的一致應用。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
