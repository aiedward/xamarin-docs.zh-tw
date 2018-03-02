---
title: "序言"
ms.topic: article
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 5e10c331ef19efe04eab5e59702db07078b0624a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="preface"></a>序言

這個電子書提供指引建置跨平台企業應用程式使用 Xamarin.Forms。 Xamarin.Forms 是可讓開發人員輕鬆地建立原生使用者介面版面配置，可共用的跨平台，包括 iOS、 Android 和通用 Windows 平台 (UWP) 的跨平台 UI 工具組。 它提供全面的解決方案，商務用員工 (B2E)，企業對企業 (B2B)，和商務用消費者 (B2C) 應用程式，提供所有目標平台共用的程式碼的能力，並協助降低總擁有成本 (TCO)。

本指南提供開發可調適性、 可維護性，以及可測試 Xamarin.Forms 企業應用程式的架構指引。 提供有關如何實作 MVVM、 相依性插入、 導覽、 驗證和設定管理，同時維持鬆散偶合的指導方針。 此外，還有指引上執行驗證與授權搭配 IdentityServer，從容器化的 microservices 和單元測試存取資料。

本指南會隨附原始程式碼[eShopOnContainers 行動裝置應用程式](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)，和原始碼[eShopOnContainers 參考應用程式](https://github.com/dotnet-architecture/eShopOnContainers)。 EShopOnContainers 行動裝置應用程式是使用 Xamarin.Forms，連接到一系列稱為 eShopOnContainers 參考應用程式容器化 microservices 開發跨平台企業應用程式。 不過，eShopOnContainers 行動裝置應用程式可以設定為使用模擬服務的人員會想要避免部署容器化的 microservices 的資料。

## <a name="whats-left-out-of-this-guides-scope"></a>不會變超出本指南的範圍

本指南旨在讀者已熟悉 Xamarin.Forms。 如要 Xamarin.Forms 詳細的簡介，請參閱[Xamarin.Forms 文件](~/xamarin-forms/index.yml)，和[xamarin.forms 建立行動應用程式](https://aka.ms/xamebook)。

本指南旨在補足[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)，它著重於開發和部署進行容器化的 microservices。 包含其他值得讀取指南[Architecting 和開發現代化 Web 應用程式使用 ASP.NET Core 和 Microsoft Azure](http://aka.ms/WebAppEbook)，[容器化 Docker 應用程式生命週期的 Microsoft 平台和工具](http://aka.ms/dockerlifecycleebook)，和[Microsoft 平台和適用於行動裝置應用程式開發工具](http://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>應該使用本指南的對象

本指南的對象主要是開發人員和架構設計人員想要了解如何設計和實作使用 Xamarin.Forms 的跨平台企業應用程式。

次要的對象是技術決策者希望接收之前決定什麼方式來選取使用 Xamarin.Forms 開發跨平台企業應用程式的架構和技術的概觀。

## <a name="how-to-use-this-guide"></a>如何使用本指南

此指南著重於建置跨平台企業應用程式使用 Xamarin.Forms。 在這種情況，它應該提供的基礎了解這類應用程式和技術的考慮事項的完整讀取。 指南和範例應用程式，也可以做為起始點或建立新的企業應用程式的參考。 使用相關聯的範例應用程式做為範本，新的應用程式中，或了解如何組織應用程式的元件組件。 然後，回頭參考此指南的架構指引。

請隨意轉寄給小組成員，以協助確保使用 Xamarin.Forms 跨平台企業應用程式開發的一般理解本指南。 擁有人從一組常用的術語和基礎的原則，將有助於確保一致的應用程式的架構模式和作法。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
