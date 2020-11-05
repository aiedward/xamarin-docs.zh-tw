---
title: 企業應用程式開發簡介
description: 本章提供企業應用程式開發的簡介，並介紹 eShopOnContainers 行動應用程式。
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e085006f3f7479d35e4991503e52ce19ced7f75b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374533"
---
# <a name="introduction-to-enterprise-app-development"></a>企業應用程式開發簡介

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

無論何種平臺，企業應用程式的開發人員都面臨幾項挑戰：

- 可能隨時間變化的應用程式需求。
- 新的商機和挑戰。
- 在開發期間進行持續的意見反應，可能會嚴重影響應用程式的範圍和需求。

考慮到這些概念，請務必建立可在一段時間內輕鬆修改或擴充的應用程式。 設計此類適應性可能很困難，因為它需要一個架構，讓應用程式的個別部分獨立開發和測試，而不會影響應用程式的其餘部分。

許多企業應用程式都很複雜，需要多個開發人員。 決定如何設計應用程式，讓多個開發人員能夠在獨立應用程式的不同部分上有效率地工作，同時確保這些元件在整合至應用程式時順暢地整合，可能是很大的挑戰。

設計和建立應用程式的傳統方法會導致所謂的整合 *型應用程式* ，其中元件會緊密地結合，而不會在兩者之間進行明確的分隔。 一般來說，這種整合型方法會導致應用程式難以維護，因為不需要中斷應用程式中的其他元件就能解決錯誤，而且可能很難加入新功能或取代現有的功能。

針對這些挑戰，有效的解決方式是將應用程式分割成離散、鬆散結合的元件，這些元件可以輕鬆地整合到應用程式中。 這種方法可提供數個優點：

- 它可讓不同的個人或小組開發、測試、擴充及維護個別的功能。
- 它會在應用程式的水準功能（例如驗證和資料存取）和垂直功能（例如應用程式專屬的商務功能）之間，促進重複使用和清楚分隔的考慮。 這可讓您更輕鬆地管理應用程式元件之間的相依性和互動。
- 它可讓不同的個人或小組根據其專長，專注于特定工作或功能，以協助維護角色的分隔。 尤其是，它會在使用者介面和應用程式的商務邏輯之間提供更清楚的分隔。

不過，將應用程式分割成離散、鬆散結合的元件時，必須解決許多問題。 它們包括：

- 決定如何在使用者介面控制項與其邏輯之間提供清楚的考慮。 建立企業應用程式時，其中一個最重要的決策 Xamarin.Forms 是要將商務邏輯放在程式碼後置檔案中，還是要在使用者介面控制項與其邏輯之間建立清楚的考慮，讓應用程式更容易維護和測試。 如需詳細資訊，請參閱 [模型視圖-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
- 判斷是否要使用相依性插入容器。 相依性插入容器藉由提供設備來建立類別的實例並插入其相依性，來減少物件之間的相依性，並根據容器的設定來管理其存留期。 如需詳細資訊，請參閱相依性 [插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
- 在平臺之間進行選擇，在不方便透過物件和類型參考連結的元件之間，提供事件和鬆散結合的訊息通訊。 如需詳細資訊，請參閱 [鬆散結合元件之間的通訊](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)簡介。
- 決定如何在頁面之間流覽，包括如何叫用導覽，以及導覽邏輯應該位於何處。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
- 判斷如何驗證使用者輸入的正確性。 決策必須包含如何驗證使用者輸入，以及如何通知使用者有驗證錯誤。 如需詳細資訊，請參閱 [驗證](~/xamarin-forms/enterprise-application-patterns/validation.md)。
- 決定如何執行驗證，以及如何使用授權保護資源。 如需詳細資訊，請參閱 [驗證和授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
- 判斷如何從 web 服務存取遠端資料，包括如何可靠地取出資料，以及如何快取資料。 如需詳細資訊，請參閱 [存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
- 決定如何測試應用程式。 如需詳細資訊，請參閱[單元測試](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南提供這些問題的指引，並著重于使用建立跨平臺企業應用程式的核心模式和架構 Xamarin.Forms 。 本指南旨在協助您產生可調整、可維護且可測試的程式碼，方法是處理常見的 Xamarin.Forms 企業應用程式開發案例，並藉由支援模型視圖 ViewModel (MVVM) 模式來分隔展示、展示邏輯和實體的考慮。

## <a name="sample-application"></a>範例應用程式

本指南包含 eShopOnContainers 的範例應用程式，這是包含下列功能的線上商店：

- 對後端服務進行驗證和授權。
- 流覽襯衫、咖啡馬克杯和其他行銷專案的目錄。
- 篩選目錄。
- 排序目錄中的專案。
- 正在查看使用者的訂單歷程記錄。
- 設定的設定。

### <a name="sample-application-architecture"></a>範例應用程式架構

圖1-1 提供範例應用程式架構的概要說明。

![eShopOnContainers 高層級架構](introduction-images/architecture.png)

**圖 1-1** ： eShopOnContainers 高層級架構

範例應用程式隨附三個用戶端應用程式：

- 使用 ASP.NET Core 開發的 MVC 應用程式。
- 單一頁面應用程式 (SPA) 以角2和 Typescript 開發。 這種適用于 web 應用程式的方法，可避免在每個作業中執行往返伺服器的往返。
- 以開發的行動應用程式 Xamarin.Forms ，支援 iOS、Android 和通用 Windows 平臺 (UWP) 。

如需 web 應用程式的相關資訊，請參閱 [使用 ASP.NET Core 和 Microsoft Azure 來設計和開發新式 Web 應用程式](https://aka.ms/WebAppEbook)。

範例應用程式包含下列後端服務：

- 身分識別微服務，使用 ASP.NET Core 身分識別和 IdentityServer。
- 目錄微服務是資料驅動的建立、讀取、更新、刪除 (CRUD) 服務，使用 EntityFramework Core 取用 SQL Server 資料庫。
- 訂購微服務，這是使用網域導向設計模式的網域驅動服務。
- 購物籃微服務，這是使用 Redis Cache 的資料驅動 CRUD 服務。

這些後端服務會使用 ASP.NET Core MVC 實作為微服務，並在單一 Docker 主機內部署為唯一的容器。 這些後端服務統稱為 eShopOnContainers 參考應用程式。 用戶端應用程式會透過具象狀態傳輸 (REST) web 介面與後端服務進行通訊。 如需微服務和 Docker 的詳細資訊，請參閱 [容器化微服務](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

如需後端服務之執行的相關資訊，請參閱 [.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>行動應用程式

本指南著重于使用建立跨平臺企業應用程式 Xamarin.Forms ，並使用 eShopOnContainers 行動應用程式作為範例。 圖1-2 顯示 eShopOnContainers 行動裝置應用程式中的頁面，其中提供稍早所述的功能。

[![EShopOnContainers 行動應用程式](introduction-images/screenshots.png)](introduction-images/screenshots-large.png#lightbox "EShopOnContainers 行動應用程式")

**圖 1-2** ： eShopOnContainers 行動應用程式

行動裝置應用程式會使用 eShopOnContainers 參考應用程式所提供的後端服務。 不過，您可以將它設定為使用模擬服務中的資料，以供想要避免部署後端服務的使用者使用。

EShopOnContainers mobile 應用程式會執行下列 Xamarin.Forms 功能：

- XAML
- 控制項
- 繫結
- 變換 器
- 樣式
- 動畫
- 命令
- 行為
- 觸發程序
- 效果
- 自訂轉譯器
- MessagingCenter
- 自訂控制項

如需此功能的詳細資訊，請參閱[ Xamarin.Forms 檔](~/xamarin-forms/index.yml)，以及[ Xamarin.Forms 使用建立 Mobile Apps ](https://aka.ms/xamformsebook)。

此外，也會為 eShopOnContainers 行動裝置應用程式中的某些類別提供單元測試。

#### <a name="mobile-app-solution"></a>行動裝置應用程式解決方案

EShopOnContainers 行動應用程式解決方案會將原始程式碼和其他資源組織成專案。 所有專案都使用資料夾，將原始程式碼和其他資源組織成類別目錄。 下表概述組成 eShopOnContainers 行動應用程式的專案：

|專案|描述|
|--- |--- |
|eShopOnContainers 核心|此專案是可移植的類別庫 (PCL) 專案，其中包含共用程式碼和共用 UI。|
|eShopOnContainers. >droid|此專案會保存 Android 特定的程式碼，而且是 Android 應用程式的進入點。|
|eShopOnContainers iOS|此專案會保存 iOS 特定程式碼，而是 iOS 應用程式的進入點。|
|eShopOnContainers UWP|此專案會保存通用 Windows 平臺 (UWP) 特定的程式碼，而且是 Windows 應用程式的進入點。|
|eShopOnContainers. TestRunner. >droid|此專案是適用于 eShopOnContainers. >--run-unittests 專案的 Android 測試執行器。|
|eShopOnContainers. TestRunner. iOS|此專案是 eShopOnContainers. >--run-unittests 專案的 iOS 測試執行器。|
|eShopOnContainers. TestRunner|此專案是 eShopOnContainers. >--run-unittests 專案的通用 Windows 平臺測試執行器。|
|eShopOnContainers. >--run-unittests|此專案包含 eShopOnContainers Core 專案的單元測試。|

EShopOnContainers 行動應用程式中的類別可以在任何應用程式中重複使用， Xamarin.Forms 幾乎不需要修改。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers Core 專案

EShopOnContainers 的 PCL 專案包含下列資料夾：

|資料夾|描述|
|--- |--- |
|動畫|包含類別，可讓您在 XAML 中使用動畫。|
|行為|包含公開給視圖類別的行為。|
|控制項|包含應用程式所使用的自訂控制項。|
|變換 器|包含將自訂邏輯套用至系結的值轉換器。|
|效果|包含 `EntryLineColorEffect` 類別，用來變更特定控制項的框線色彩 `Entry` 。|
|例外狀況|包含自訂 `ServiceAuthenticationException` 。|
|延伸模組|包含和類別的擴充 `VisualElement` 方法 `IEnumerable` 。|
|協助程式|包含應用程式的 helper 類別。|
|模型|包含應用程式的模型類別。|
|屬性|包含 `AssemblyInfo.cs` .net 元件中繼資料檔案。|
|服務|包含介面和類別，可執行為應用程式提供的服務。|
|觸發程序|包含 `BeginAnimation` 用來叫用 XAML 動畫的觸發程式。|
|驗證|包含與驗證資料輸入相關的類別。|
|ViewModels|包含公開給頁面的應用程式邏輯。|
|檢視|包含應用程式的頁面。|

##### <a name="platform-projects"></a>平臺專案

平臺專案包含效果的執行、自訂轉譯器的執行，以及其他平臺特定的資源。

## <a name="summary"></a>總結

Xamarin 的跨平臺行動應用程式開發工具和平臺為 B2E、B2B 和 B2C 行動用戶端應用程式提供全方位的解決方案，讓您能夠在所有目標平臺之間共用程式碼 (iOS、Android 和 Windows) ，並協助降低擁有權總成本。 應用程式可以共用其使用者介面和應用程式邏輯程式碼，同時保留原生平臺的外觀和操作。

企業應用程式的開發人員面臨幾項挑戰，可在開發期間改變應用程式的架構。 因此，請務必建立應用程式，讓它可以隨著時間修改或擴充。 針對這類適應性設計可能會很困難，但通常牽涉到將應用程式分割成離散、鬆散結合的元件，而這些元件可以輕鬆地整合到應用程式中。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
