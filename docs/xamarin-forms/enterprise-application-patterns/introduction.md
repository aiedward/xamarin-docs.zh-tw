---
title: 企業應用程式開發簡介
description: 本章介紹開發企業應用程式，並介紹 eShopOnContainers 的行動裝置應用程式。
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9deb685c92092ceb0e1c775a1e53ac1bce5a4a57
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299976"
---
# <a name="introduction-to-enterprise-app-development"></a>企業應用程式開發簡介

平台，無論企業應用程式的開發人員會面臨一些挑戰：

-   隨著時間而改變的應用程式需求。
-   新的商機和挑戰。
-   進行中的範圍和應用程式的需求會大幅影響的開發期間的意見反應。

使用這些事項，請務必建置應用程式，可以輕鬆地修改或擴充一段時間。 這類可適性設計可能很困難，因為它需要的架構，允許獨立開發和測試隔離，而不會影響應用程式的其餘部分的應用程式的個別組件。

許多企業應用程式已夠複雜到需要多個開發人員。 它可以是相當大的挑戰，決定如何設計應用程式，讓多位開發人員可以有效地在應用程式的不同片段上獨立運作，同時確保，項目一起順暢地整合到應用程式時。

傳統的方法來設計和建置應用程式中內容的結果指*單體式*應用程式，其中的元件會緊密結合兩者之間沒有清楚的分隔。 一般而言，此整合型方法會導致是困難且效率不佳，維護，因為它可能難以解決的 bug，而不會中斷應用程式中的其他元件的應用程式，而且可能很難加入新功能，或取代現有的功能。

這些挑戰的有效補救措施，就是應用程式分割成獨立且鬆散偶合的元件可以輕鬆地整合在一起到應用程式。 這種方式提供數個優點：

-   它可讓開發、 測試、 擴充，並由不同人員或小組所維護的個別功能。
-   重複使用，並清楚分離關注點之間的應用程式的水平的功能，例如驗證和資料存取和垂直功能，例如應用程式特定的商務功能，會將它升級。 這可讓相依性，更輕鬆地管理應用程式元件之間的互動。
-   它可協助藉由使用不同的個人或小組，專注於特定工作或一項功能，根據其專業知識的維護角色區隔。 特別是，它會提供使用者介面和應用程式的商務邏輯之間的更清楚劃分開來。

不過，有許多分割成獨立且鬆散結合元件的應用程式時必須解決的問題。 它們包括：

-   決定如何提供清楚的分隔，使用者介面控制項和其邏輯之間的問題。 建立 Xamarin.Forms 企業應用程式時最重要決策之一為是否要將商務邏輯放在程式碼後置檔案中，或是否要建立清楚分離關注點之間的使用者介面控制項和其邏輯，以讓應用程式的多個方便維護和測試。 如需詳細資訊，請參閱 < [Model View ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
-   決定是否要使用相依性插入容器。 相依性插入容器減少結合所提供的功能，來建構類別的執行個體具有相依性插入，物件之間的相依性，並管理其存留期，根據容器的組態。 如需詳細資訊，請參閱 <<c0> [ 相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
-   選擇平台提供的事件之間和鬆散結合是很不方便，連結物件和型別參考的元件之間的訊息式通訊。 如需詳細資訊，請參閱 < 簡介[通訊之間鬆散結合元件](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)。
-   決定如何之間頁面，包括如何叫用導覽中，瀏覽和巡覽邏輯應該位於其中。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
-   決定如何驗證使用者輸入的正確性。 如何驗證使用者輸入，以及如何通知使用者有關驗證錯誤，必須包含決定。 如需詳細資訊，請參閱 <<c0> [ 驗證](~/xamarin-forms/enterprise-application-patterns/validation.md)。
-   決定如何執行驗證，以及如何保護具有授權的資源。 如需詳細資訊，請參閱 <<c0> [ 驗證和授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
-   決定如何從 web 存取遠端資料的服務，包括如何可靠地擷取資料，以及如何快取資料。 如需詳細資訊，請參閱 <<c0> [ 存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
-   決定如何測試應用程式。 如需詳細資訊，請參閱 < [Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南提供這些問題的指引，並著重於核心模式和建置跨平台企業應用程式使用 Xamarin.Forms 架構。 本指南旨在協助產生可調適性、 可維護性及可測試的程式碼，定址常見 Xamarin.Forms 企業應用程式的開發案例，以及區隔的簡報、 展示邏輯和實體透過支援的考量Model View ViewModel (MVVM) 模式。

## <a name="sample-application"></a>範例應用程式

本指南包含範例應用程式，eShopOnContainers 的線上商店，其中包含下列功能：

-   驗證和授權對後端服務。
-   瀏覽上衣、 咖啡馬克杯以及其他行銷商品目錄。
-   篩選目錄。
-   排序從類別目錄的項目。
-   檢視使用者的訂單記錄。
-   設定的組態。

### <a name="sample-application-architecture"></a>範例應用程式架構

圖 1-1 提供的範例應用程式架構的高階概觀。

![](introduction-images/architecture.png "eShopOnContainers 的高階架構")

**圖 1-1**: eShopOnContainers 的高階架構

範例應用程式隨附三個用戶端應用程式：

-   開發 ASP.NET Core MVC 應用程式。
-   單一頁面應用程式 (SPA) 開發 Typescript 與 Angular 2。 Web 應用程式使用此方式可避免執行每個作業的伺服器往返。
-   使用 Xamarin.Forms 時，可支援 iOS、 Android 和通用 Windows 平台 (UWP) 開發的行動裝置應用程式。

Web 應用程式的相關資訊，請參閱[架構和開發新式 Web 應用程式使用 ASP.NET Core 和 Microsoft Azure](http://aka.ms/WebAppEbook)。

範例應用程式包含下列的後端服務：

-   識別微服務，使用 ASP.NET Core 身分識別與 IdentityServer。
-   目錄微服務時，也就是資料驅動的建立、 讀取、 更新、 刪除 (CRUD) 服務取用使用 EntityFramework 核心的 SQL Server 資料庫。
-   訂購微服務，也就是領域導向的服務使用網域導向設計模式。
-   購物籃微服務，是使用 Redis 快取的資料驅動 CRUD 服務。

這些後端服務會實作成微服務使用 ASP.NET Core MVC，以及部署為單一 Docker 主機內的唯一容器。 整體而言，這些後端服務被指 eShopOnContainers 參考應用程式。 用戶端應用程式與後端服務透過 Representational State Transfer (REST) web 介面通訊。 如需有關微服務和 Docker 的詳細資訊，請參閱 <<c0> [ 容器化微服務](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

如需後端服務的實作資訊，請參閱[.NET 微服務：容器化 .NET 應用程式的架構](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>行動裝置應用程式

本指南著重於建置跨平台企業應用程式使用 Xamarin.Forms，並使用 eShopOnContainers 的行動裝置應用程式做為範例。 圖 1-2 顯示目的頁面，從 eShopOnContainers 的行動裝置應用程式，以提供稍早所述的功能。

[![](introduction-images/screenshots.png "EShopOnContainers 的行動裝置應用程式")](introduction-images/screenshots-large.png#lightbox "eShopOnContainers 的行動裝置應用程式")

**圖 1-2**:EShopOnContainers 的行動裝置應用程式

行動裝置應用程式會取用提供 eShopOnContainers 參考應用程式的後端服務。 不過，它可以設定為使用模擬 （mock） 的人想要避免部署後端服務的服務中的資料。

EShopOnContainers 的行動裝置應用程式會執行下列的 Xamarin.Forms 功能：

-   XAML
-   控制項
-   繫結
-   轉換器
-   樣式
-   Animations
-   命令
-   「行為」
-   觸發程序
-   效果
-   自訂轉譯器
-   MessagingCenter
-   自訂控制項

如需有關這項功能的詳細資訊，請參閱 < [Xamarin.Forms 文件](~/xamarin-forms/index.yml)，並[使用 Xamarin.Forms 建立行動應用程式](https://aka.ms/xamebook)。

此外，單元測試會提供部分 eShopOnContainers 的行動裝置應用程式中的類別。

#### <a name="mobile-app-solution"></a>應用程式的行動解決方案

在 eShopOnContainers 的行動裝置應用程式解決方案會將原始碼和其他資源組織成專案。 所有的專案，可以使用資料夾來組織成類別的原始程式碼和其他資源。 下表概述構成 eShopOnContainers 的行動裝置應用程式的專案：

|專案|描述|
|--- |--- |
|eShopOnContainers.Core|此專案是可攜式類別庫 (PCL) 專案，其中包含共用程式碼和共用的 UI。|
|eShopOnContainers.Droid|此專案容納 Android 特定程式碼，並為 Android 應用程式的進入點。|
|eShopOnContainers.iOS|這個專案容納 iOS 專用的程式碼，然後是 iOS 應用程式的進入點。|
|eShopOnContainers.UWP|此專案容納通用 Windows 平台 (UWP) 的特定程式碼，並為 Windows 應用程式的進入點。|
|eShopOnContainers.TestRunner.Droid|此專案是 eShopOnContainers.UnitTests 專案的 Android 測試執行器。|
|eShopOnContainers.TestRunner.iOS|此專案是 eShopOnContainers.UnitTests 專案的 iOS 測試執行器。|
|eShopOnContainers.TestRunner.Windows|此專案是 eShopOnContainers.UnitTests 專案的通用 Windows 平台測試執行器。|
|eShopOnContainers.UnitTests|此專案包含 eShopOnContainers.Core 專案的單元測試。|

從 eShopOnContainers 的行動裝置應用程式類別都可以在任何具有少量或沒有修改的 Xamarin.Forms 應用程式中重複使用。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core 專案

EShopOnContainers.Core PCL 專案中包含下列資料夾：

|資料夾|描述|
|--- |--- |
|Animations|包含類別，可讓在 XAML 中使用的動畫。|
|「行為」|包含會公開至檢視類別的行為。|
|控制項|包含應用程式使用的自訂控制項。|
|轉換器|包含自訂邏輯套用至繫結的值轉換器。|
|效果|包含`EntryLineColorEffect`類別，這用來變更特定的框線色彩`Entry`控制項。|
|例外狀況|包含自訂`ServiceAuthenticationException`。|
|延伸模組|包含擴充方法`VisualElement`和`IEnumerable`類別。|
|協助程式|包含應用程式的協助程式類別。|
|模型|包含應用程式的模型類別。|
|屬性|包含`AssemblyInfo.cs`，.NET 組件中繼資料檔案。|
|服務|包含介面和實作服務應用程式所提供的類別。|
|觸發程序|包含`BeginAnimation`觸發程序，用來叫用 XAML 中的動畫。|
|驗證|包含類別參與驗證資料輸入。|
|ViewModels|包含頁面公開的應用程式邏輯。|
|檢視|包含應用程式的網頁。|

##### <a name="platform-projects"></a>平台專案

平台專案包含效果實作、 自訂轉譯器的實作和其他平台專屬的資源。

## <a name="summary"></a>總結

Xamarin 的跨平台行動裝置應用程式開發工具與平台提供全方位的解決方案 B2E、 B2B 和 B2C 行動用戶端應用程式，以便能夠跨所有目標平台 （iOS、 Android 和 Windows） 和有助於降低共用程式碼擁有權總成本。 應用程式可以共用其使用者介面和應用程式邏輯程式碼，同時保有原生平台外觀與風格。

企業應用程式的開發人員會面臨一些挑戰，可以在開發期間修改應用程式的架構。 因此，務必建置的應用程式，使其可以修改或擴充一段時間。 這類可適性設計可能很困難，但通常牽涉到資料分割成獨立且鬆散偶合的元件可以輕鬆地整合在一起到應用程式的應用程式。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
