---
title: 企業應用程式開發簡介
description: 本章提供企業應用程式開發的簡介，並介紹 eShopOnContainers 的行動應用程式。
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9bde1140f6590daa4b1d40a8b56edec314bfc66d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760228"
---
# <a name="introduction-to-enterprise-app-development"></a>企業應用程式開發簡介

無論平臺為何，企業應用程式的開發人員都面臨幾項挑戰：

- 可能隨著時間變更的應用程式需求。
- 新的商機和挑戰。
- 在開發期間持續的意見反應可能會大幅影響應用程式的範圍和需求。

記住這些功能之後，請務必建立可在一段時間內輕鬆修改或擴充的應用程式。 這種適應性的設計很棘手，因為它需要一個架構，讓應用程式的個別部分獨立開發和測試，而不會影響應用程式的其餘部分。

許多企業應用程式都很複雜，需要多個開發人員。 決定如何設計應用程式，讓多個開發人員能夠在獨立應用程式的不同部分上有效率地工作，同時確保各部分在整合至應用程式時順暢整合，這是很大的挑戰。

設計和建立應用程式的傳統方法，會導致所謂的整合*型應用程式*，其中元件會緊密結合，而不會有清楚的分隔。 一般來說，此整合型方法會導致難以維護的應用程式，因為難以解決錯誤，而不會中斷應用程式中的其他元件，而且可能很容易加入新功能或取代現有的功能。

這些挑戰的有效補救方式，是將應用程式分割成可輕鬆整合到應用程式的離散、鬆散結合的元件。 這種方法提供數個優點：

- 它可讓不同的個人或小組開發、測試、擴充及維護個別的功能。
- 它會提升重複使用性，並在應用程式的水準功能（例如驗證和資料存取）和垂直功能（例如應用程式特有的商務功能）之間清楚區分問題。 這可讓您更輕鬆地管理應用程式元件之間的相依性和互動。
- 它可讓不同的個人或小組專注于特定工作或根據其專長的功能，來協助維護角色的分隔。 特別是，它會在使用者介面和應用程式的商務邏輯之間提供更清楚的分隔。

不過，將應用程式分割成離散、鬆散結合的元件時，必須解決許多問題。 它們包括：

- 決定如何在使用者介面控制項與其邏輯之間提供清楚的顧慮分隔。 建立 Xamarin 時最重要的決策之一，就是要將商務邏輯放在程式碼後置檔案中，還是要在使用者介面控制項與其邏輯之間建立清楚的顧慮區隔，讓應用程式更多維護性和可測試性。 如需詳細資訊，請參閱[ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
- 判斷是否要使用相依性插入容器。 相依性插入容器會藉由提供設施來建立類別的實例，並插入其相依性，以減少物件之間的相依性，並根據容器的設定來管理其存留期。 如需詳細資訊，請參閱相依性[插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
- 在平臺之間選擇：在元件之間提供事件和鬆散結合的訊息型通訊，不方便以物件和型別參考連結。 如需詳細資訊，請參閱[鬆散結合的元件之間的通訊](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)簡介。
- 決定如何在頁面之間流覽，包括如何叫用導覽，以及導覽邏輯的所在位置。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
- 決定如何驗證使用者輸入是否正確無誤。 決策必須包括如何驗證使用者輸入，以及如何通知使用者有關驗證錯誤。 如需詳細資訊，請參閱[驗證](~/xamarin-forms/enterprise-application-patterns/validation.md)。
- 決定如何執行驗證，以及如何使用授權來保護資源。 如需詳細資訊，請參閱[驗證和授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
- 決定如何從 web 服務存取遠端資料，包括如何可靠地抓取資料，以及如何快取資料。 如需詳細資訊，請參閱[存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
- 決定如何測試應用程式。 如需詳細資訊，請參閱[單元測試](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南提供有關這些問題的指引，並著重于使用 Xamarin 建立跨平臺企業應用程式的核心模式和架構。 本指南旨在協助產生可調整、可維護且可測試的程式碼，方法是處理常見的 Xamarin。表單企業應用程式開發案例，以及透過支援來分隔簡報、簡報邏輯和實體的顧慮模型視圖-ViewModel （MVVM）模式。

## <a name="sample-application"></a>範例應用程式

本指南包含範例應用程式 eShopOnContainers，這是線上商店，其中包含下列功能：

- 對後端服務進行驗證和授權。
- 流覽襯衫、咖啡馬克杯和其他行銷專案的目錄。
- 篩選目錄。
- 從目錄排序專案。
- 查看使用者的訂單歷程記錄。
- 設定。

### <a name="sample-application-architecture"></a>範例應用程式架構

圖1-1 提供範例應用程式架構的高層級總覽。

![](introduction-images/architecture.png "eShopOnContainers 高層級架構")

**圖 1-1**： eShopOnContainers 高層級架構

範例應用程式隨附三個用戶端應用程式：

- 以 ASP.NET Core 開發的 MVC 應用程式。
- 以角度2和 Typescript 開發的單一頁面應用程式（SPA）。 這種 web 應用程式的方法可避免在每次作業時來回執行伺服器。
- 以 Xamarin 開發的行動應用程式，可支援 iOS、Android 和通用 Windows 平臺（UWP）。

如需 web 應用程式的相關資訊，請參閱[使用 ASP.NET Core 和 Microsoft Azure 架構和開發新式 Web 應用程式](https://aka.ms/WebAppEbook)。

範例應用程式包含下列後端服務：

- 身分識別微服務，使用 ASP.NET Core 身分識別和 IdentityServer。
- 目錄微服務，這是資料驅動的建立、讀取、更新、刪除（CRUD）服務，會使用 EntityFramework Core 來取用 SQL Server 資料庫。
- 訂購微服務，這是使用領域驅動設計模式的網域驅動服務。
- 購物籃微服務，這是使用 Redis 快取的資料驅動 CRUD 服務。

這些後端服務會使用 ASP.NET Core MVC 實作為微服務，並部署為單一 Docker 主機內的唯一容器。 這些後端服務統稱為 eShopOnContainers 參考應用程式。 用戶端應用程式會透過具像狀態傳輸（REST） web 介面與後端服務通訊。 如需微服務和 Docker 的詳細資訊，請參閱[容器化微服務](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

如需後端服務的執行資訊，請參閱[.net 微服務：容器化 .NET 應用程式的架構](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>行動應用程式

本指南著重于使用 Xamarin 建立跨平臺的企業應用程式，並使用 eShopOnContainers 行動應用程式做為範例。 圖1-2 顯示 eShopOnContainers 行動應用程式中的頁面，其中提供稍早所述的功能。

EShopOnContainers 行動應用程式[ ![(introduction-images/screenshots.png "")] ](introduction-images/screenshots-large.png#lightbox "EShopOnContainers 行動應用程式")

**圖 1-2**：EShopOnContainers 行動應用程式

行動應用程式會使用 eShopOnContainers reference 應用程式所提供的後端服務。 不過，您可以將它設定為針對想要避免部署後端服務的使用者，使用 mock 服務的資料。

EShopOnContainers 行動應用程式會練習下列的 Xamarin 功能：

- XAML
- 控制項
- 繫結
- 而言
- 樣式
- Animations
- 命令
- 「行為」
- 觸發程序
- 效果
- 自訂轉譯器
- MessagingCenter
- 自訂控制項

如需此功能的詳細資訊，請參閱 [ [xamarin] 檔](~/xamarin-forms/index.yml)，並使用 [ [xamarin] 建立 Mobile Apps](https://aka.ms/xamebook)。

此外，也會針對 eShopOnContainers 行動應用程式中的某些類別提供單元測試。

#### <a name="mobile-app-solution"></a>行動應用程式解決方案

EShopOnContainers 行動應用程式解決方案會將原始程式碼和其他資源組織成專案。 所有專案都會使用資料夾，將原始程式碼和其他資源組織成類別目錄。 下表概述組成 eShopOnContainers 行動應用程式的專案：

|專案|說明|
|--- |--- |
|eShopOnContainers.Core|此專案是可移植的類別庫（PCL）專案，其中包含共用程式碼和共用 UI。|
|eShopOnContainers.Droid|此專案包含 Android 特定程式碼，而且是 Android 應用程式的進入點。|
|eShopOnContainers.iOS|此專案包含 iOS 特定的程式碼，而且是 iOS 應用程式的進入點。|
|eShopOnContainers.UWP|此專案包含通用 Windows 平臺（UWP）的特定程式碼，而且是 Windows 應用程式的進入點。|
|eShopOnContainers.TestRunner.Droid|此專案是 eShopOnContainers Run-unittests 專案的 Android 測試執行器。|
|eShopOnContainers.TestRunner.iOS|此專案是 eShopOnContainers Run-unittests 專案的 iOS 測試執行器。|
|eShopOnContainers.TestRunner.Windows|此專案是 eShopOnContainers Run-unittests 專案的通用 Windows 平臺測試執行器。|
|eShopOnContainers.UnitTests|此專案包含 eShopOnContainers 專案的單元測試。|

EShopOnContainers 行動應用程式中的類別可重複用於任何 Xamarin 應用程式中，幾乎不需要修改。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers Core 專案

EShopOnContainers PCL 專案包含下列資料夾：

|資料夾|說明|
|--- |--- |
|Animations|包含可讓動畫在 XAML 中使用的類別。|
|「行為」|包含對 view 類別公開的行為。|
|控制項|包含應用程式所使用的自訂控制項。|
|而言|包含將自訂邏輯套用至系結的值轉換器。|
|效果|包含類別，用來變更特定`Entry`控制項的框線色彩。 `EntryLineColorEffect`|
|例外狀況|包含自訂`ServiceAuthenticationException`。|
|延伸模組|包含`VisualElement` 和`IEnumerable`類別的擴充方法。|
|助手|包含應用程式的 helper 類別。|
|模型|包含應用程式的模型類別。|
|屬性|包含`AssemblyInfo.cs`，也就是 .net 元件中繼資料檔。|
|服務|包含介面和類別，可執行提供給應用程式的服務。|
|觸發程序|`BeginAnimation`包含觸發程式，用來叫用 XAML 中的動畫。|
|驗證|包含與驗證資料輸入相關的類別。|
|ViewModels|包含公開給頁面的應用程式邏輯。|
|檢視|包含應用程式的頁面。|

##### <a name="platform-projects"></a>平臺專案

平臺專案包含效果的執行、自訂轉譯器，以及其他平臺特定資源。

## <a name="summary"></a>總結

Xamarin 的跨平臺行動應用程式開發工具和平臺提供 B2E、B2B 和 B2C 行動用戶端應用程式的全方位解決方案，讓您能夠跨所有目標平臺（iOS、Android 和 Windows）共用程式碼，並協助降低擁有權總成本。 應用程式可以共用其使用者介面和應用程式邏輯程式碼，同時保有原生平臺的外觀與風格。

企業應用程式的開發人員面臨幾個挑戰，可在開發期間改變應用程式的架構。 因此，請務必建立一個應用程式，讓它可以在一段時間內修改或擴充。 針對這種適應性進行設計可能會很難，但通常牽涉到將應用程式分割成可輕鬆整合到應用程式的離散、鬆散結合的元件。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
