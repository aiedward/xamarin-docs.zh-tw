---
title: 企業應用程式開發的前置詞
description: 此章節將提供使用 Xamarin.Forms 企業應用程式模式的前置詞。
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fd085d2fb12e82233f6d3be2e2773a84539f837b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298859"
---
# <a name="preface-to-enterprise-app-development"></a>企業應用程式開發的前置詞

此電子書會提供有關建置跨平台企業應用程式使用 Xamarin.Forms 的指引。 Xamarin.Forms 是可讓開發人員輕鬆地建立原生使用者介面版面配置，可以跨平台，包括 iOS、 Android 和通用 Windows 平台 (UWP) 共用的跨平台 UI 工具組。 它提供全方位的解決方案的公司員工 (B2E)，企業對企業 (B2B) 和企業對消費者 (B2C) 應用程式，以便能夠跨所有目標平台共用程式碼，並協助降低總擁有成本 (TCO)。

本指南提供開發具可調適性、 可維護性及可測試的 Xamarin.Forms 企業應用程式的架構指引。 提供有關如何實作 MVVM、 相依性插入、 導覽、 驗證和組態管理，同時保持鬆散結合的指引。 此外，還有指引上執行驗證與授權與 IdentityServer，從容器化微服務和單元測試中存取資料。

本指南隨附的原始程式碼[eShopOnContainers 行動裝置應用程式](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)，和來源的程式碼[eShopOnContainers 參考應用程式](https://github.com/dotnet-architecture/eShopOnContainers)。 EShopOnContainers 的行動裝置應用程式是使用 Xamarin.Forms，連接到一系列稱為 eShopOnContainers 參考應用程式的容器化微服務的開發跨平台企業應用程式。 不過，eShopOnContainers 的行動裝置應用程式可以設定為使用模擬 （mock） 的人想要避免部署容器化微服務的服務中的資料。

## <a name="whats-left-out-of-this-guides-scope"></a>剩下超出本指南的範圍

本指南被針對讀者已熟悉使用 Xamarin.Forms。 Xamarin.Forms 的詳細簡介，請參閱 < [Xamarin.Forms 文件](~/xamarin-forms/index.yml)，並[使用 Xamarin.Forms 建立行動應用程式](https://aka.ms/xamebook)。

本指南是為了補充[.NET 微服務：容器化.NET 應用程式架構](https://aka.ms/microservicesebook)，其中著重於開發及部署容器化微服務。 其他值得一讀的指南還包括[架構和開發新式 Web 應用程式使用 ASP.NET Core 和 Microsoft Azure](http://aka.ms/WebAppEbook)，[容器化 Docker 應用程式生命週期 Microsoft 平台和工具](http://aka.ms/dockerlifecycleebook)，並[Microsoft 平台和行動裝置應用程式開發工具](http://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>誰應該使用本指南

本指南的對象主要是開發人員和架構設計人員想要了解如何設計和實作使用 Xamarin.Forms 的跨平台企業應用程式。

次要的對象是技術決策者，其想要接收之前決定什麼方式選取要使用 Xamarin.Forms 開發跨平台企業應用程式的架構和技術的概觀。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南著重於建置跨平台企業應用程式使用 Xamarin.Forms。 因此，它應閱讀完整狀態，以提供的基礎了解這類應用程式和其技術考量。 本指南與其範例應用程式，也可以做為起始的 「 點 」 或 「 建立新的企業應用程式的參考。 新的應用程式，或若要查看如何組織應用程式的元件組件，則您可以作為範本相關聯的範例應用程式。 然後，回頭參考此指南中供架構指引。

請隨意本指南轉寄給小組成員，以協助確保使用 Xamarin.Forms 的跨平台企業應用程式開發的共識。 讓所有人都使用一組共用術語和基礎原則，將有助於確保一致的應用程式的架構模式和作法。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
