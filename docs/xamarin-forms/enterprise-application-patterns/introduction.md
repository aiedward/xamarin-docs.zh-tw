---
title: "簡介"
ms.topic: article
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d5d5de21c4f397f203b4da5ea86e1f9a0e4714e9
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="introduction"></a>簡介

平台，不論企業應用程式的開發人員會面臨一些挑戰：

-   應用程式可能會隨時間變更的需求。
-   新的商機和挑戰。
-   進行中的範圍和應用程式的需求會大幅影響的開發期間的意見反應。

這些考量，是建置應用程式可以輕鬆地修改或擴充一段時間。 針對這類適應性設計可能不容易，因為它需要的架構，可讓個別的組件的獨立開發和測試中隔離，而不會影響應用程式的 rest 應用程式。

許多企業應用程式是不夠複雜，需要一個以上的開發人員。 它可以決定如何設計應用程式，讓多位開發人員可以有效地在應用程式的不同片段中獨立工作，同時確保該片段一起順暢地整合到應用程式時相當大的挑戰。

設計和建置應用程式所導致的傳統方法稱為*龐大*應用程式，其中元件緊密結合與它們之間沒有清楚的區隔。 一般而言，這種整合的方法會導致應用程式時，會發生困難而且效率不佳，維護，很難解決的 bug，而不會中斷應用程式中的其他元件，而且可能很難為了加入新功能，還是要取代現有的功能。

這些挑戰的有效補救措施是分割成離散、 鬆散偶合的元件可以輕鬆地整合在一起到應用程式的應用程式。 這種方法提供多項優點：

-   它可讓開發、 測試、 擴充，並由不同的個人或團隊所維護的個別功能。
-   它會升級重複使用及清理隔離的應用程式的水平的功能，例如驗證和資料存取與垂直的功能，例如應用程式特定的商務功能之間的考量。 這可讓相依性，更輕鬆地管理應用程式元件之間的互動。
-   它可協助維護隔離的角色，藉由使用不同的個人或小組，專注於特定工作或功能，根據其專業知識。 特別是，它會提供使用者介面和應用程式的商務邏輯之間清楚劃分開來。

不過，有許多應用程式分割成離散、 鬆散偶合的元件時，必須先解析的問題。 它們包括：

-   決定如何提供清楚分隔的使用者介面控制項和其邏輯之間的重要性。 其中一個最重要的決策時建立 Xamarin.Forms 企業應用程式是是否要將商務邏輯放在程式碼後置檔案，或是否要建立的使用者介面控制項和其邏輯，讓多個應用程式之間的重要性清楚分隔可維護性和可測試性。 如需詳細資訊，請參閱[模型-檢視-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
-   判斷是否要使用相依性插入容器。 相依性插入容器降低耦合物件提供用來建構類別的執行個體插入的程式，及其相依性的設備之間的相依性，並管理其存留期，根據容器的設定。 如需詳細資訊，請參閱[相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
-   選擇之間平台提供的事件和鬆散結合不方便，連結物件和型別參考的元件之間的訊息為基礎的通訊。 如需詳細資訊，請參閱 < 簡介[通訊之間鬆散偶合元件](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)。
-   決定如何巡覽頁面，包括如何叫用瀏覽，並瀏覽邏輯所在。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
-   決定如何驗證使用者輸入正確。 如何驗證使用者輸入，以及如何通知使用者相關驗證錯誤，必須包含決策。 如需詳細資訊，請參閱[驗證](~/xamarin-forms/enterprise-application-patterns/validation.md)。
-   決定如何執行驗證，以及如何保護資源的授權。 如需詳細資訊，請參閱[驗證和授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
-   決定如何從 web 存取遠端資料服務，包括如何可靠地擷取資料，以及如何快取資料。 如需詳細資訊，請參閱[存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
-   決定如何測試應用程式。 如需詳細資訊，請參閱[單元測試](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南提供這些問題的指引，並著重在核心模式及架構建置跨平台企業應用程式使用 Xamarin.Forms。 本指南旨在協助定址常見 Xamarin.Forms 企業應用程式的開發案例，以及區隔簡報，呈現邏輯和實體的支援考量產生可調適性、 可維護性，以及可測試的程式碼，模型-檢視-ViewModel (MVVM) 模式。

## <a name="sample-application"></a>範例應用程式

本指南包含範例應用程式，eShopOnContainers 線上存放區包含下列功能：

-   驗證和授權對後端服務。
-   瀏覽上衣、 咖啡馬克及其他行銷目的的目錄。
-   篩選目錄。
-   排序的項目從類別目錄。
-   檢視使用者的訂單記錄。
-   設定的組態設定。

### <a name="sample-application-architecture"></a>範例應用程式架構

圖 1-1 提供的範例應用程式架構的高階概觀。

![](introduction-images/architecture.png "eShopOnContainers 高階架構")

**圖 1-1**: eShopOnContainers 高階架構

範例應用程式隨附三個用戶端應用程式：

-   MVC 應用程式開發 ASP.NET Core。
-   單一頁面應用程式 (SPA) 開發角度 2 和 Typescript。 Web 應用程式的這種方法可以避免執行每個作業的伺服器往返。
-   透過 Xamarin.Forms，支援 iOS、 Android 和通用 Windows 平台 (UWP) 開發行動裝置應用程式。

Web 應用程式的相關資訊，請參閱[Architecting 和開發現代化 Web 應用程式使用 ASP.NET Core 和 Microsoft Azure](http://aka.ms/WebAppEbook)。

範例應用程式包含下列後端服務：

-   識別微服務，會使用 ASP.NET Core 身分識別和 IdentityServer。
-   目錄微服務，也就是資料驅動的建立、 讀取、 更新、 刪除 (CRUD) 服務取用使用 EntityFramework 核心 SQL Server 資料庫。
-   排序微服務，這是使用網域導向設計模式的網域導向的服務。
-   購物籃微服務，是使用 Redis 快取的資料導向 CRUD 服務。

這些後端服務會實作為 microservices 使用 ASP.NET Core MVC，並會部署為單一的 Docker 主機中的唯一容器。 整體而言，這些後端服務稱為 eShopOnContainers 參考應用程式。 用戶端應用程式與後端服務通訊透過 Representational State Transfer (REST) web 介面。 如需 microservices 和 Docker 的詳細資訊，請參閱[容器化 Microservices](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

後端服務實作的相關資訊，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>行動裝置應用程式

本指南著重於建置跨平台企業應用程式使用 Xamarin.Forms，並使用 eShopOnContainers 行動裝置應用程式為例。 圖 1-2 顯示 eShopOnContainers 行動應用程式中的頁面，以提供之前所述的功能。

[![](introduction-images/screenshots.png "EShopOnContainers 行動裝置應用程式")](introduction-images/screenshots-large.png#lightbox "eShopOnContainers 行動裝置應用程式")

**圖 1-2**: eShopOnContainers 行動裝置應用程式

行動裝置應用程式取用 eShopOnContainers 參考應用程式所提供的後端服務。 不過，可以設定為使用模擬服務的人員會想要避免部署後端服務的資料。

EShopOnContainers 行動裝置應用程式會執行下列 Xamarin.Forms 功能：

-   XAML
-   控制項
-   繫結
-   轉換程式
-   樣式
-   Animations
-   命令
-   「行為」
-   觸發程序
-   效果
-   自訂轉譯器
-   MessagingCenter
-   自訂控制項

如需有關這項功能的詳細資訊，請參閱[Xamarin.Forms 文件](~/xamarin-forms/index.yml)，和[建立行動應用程式使用 Xamarin.Forms](https://aka.ms/xamebook)。

此外，單元測試提供某些 eShopOnContainers 行動裝置應用程式中的類別。

#### <a name="mobile-app-solution"></a>行動應用程式方案

EShopOnContainers 行動裝置應用程式方案會組織專案的原始碼和其他資源。 所有的專案，使用資料夾來組織成類別的原始碼和其他資源。 下表列出構成 eShopOnContainers 行動裝置應用程式的專案：

|專案|描述|
|--- |--- |
|eShopOnContainers.Core|這個專案不包含共用程式碼和共用的 UI 的可攜式類別庫 (PCL) 專案。|
|eShopOnContainers.Droid|此專案會保存 Android 的特定程式碼並 Android 應用程式的進入點。|
|eShopOnContainers.iOS|此專案保存 iOS 特定程式碼並將 iOS 應用程式的進入點。|
|eShopOnContainers.UWP|此專案包含通用 Windows 平台 (UWP) 的特定程式碼並 for Windows 應用程式的進入點。|
|eShopOnContainers.TestRunner.Droid|這個專案是 Android 測試執行器 eShopOnContainers.UnitTests 專案。|
|eShopOnContainers.TestRunner.iOS|這個專案是 iOS 測試執行器 eShopOnContainers.UnitTests 專案。|
|eShopOnContainers.TestRunner.Windows|這個專案是通用 Windows 平台測試執行器 eShopOnContainers.UnitTests 專案。|
|eShopOnContainers.UnitTests|此專案包含 eShopOnContainers.Core 專案的單元測試。|

EShopOnContainers 行動應用程式中的類別可以在使用少量或沒有修改任何 Xamarin.Forms 應用程式中重複使用。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core Project

EShopOnContainers.Core PCL 專案中包含下列資料夾：

|資料夾|描述|
|--- |--- |
|Animations|包含類別，可讓在 XAML 中使用動畫。|
|「行為」|包含會公開至檢視類別的行為。|
|控制項|包含應用程式所使用的自訂控制項。|
|轉換程式|包含自訂邏輯套用至繫結的值轉換器。|
|效果|包含`EntryLineColorEffect`類別，用來變更特定的框線色彩`Entry`控制項。|
|例外狀況|包含自訂`ServiceAuthenticationException`。|
|延伸模組|包含的擴充方法`VisualElement`和`IEnumerable`類別。|
|協助程式|包含應用程式的協助程式類別。|
|模型|包含應用程式的模型類別。|
|屬性|包含`AssemblyInfo.cs`，.NET 組件中繼資料檔案。|
|服務|包含介面和實作服務提供給應用程式的類別。|
|觸發程序|包含`BeginAnimation`觸發程序，用來叫用 XAML 中的動畫。|
|驗證|包含類別參與驗證資料輸入。|
|ViewModels|包含公開網頁的應用程式邏輯。|
|檢視|包含應用程式的網頁。|

##### <a name="platform-projects"></a>平台專案

平台專案包含效果實作、 自訂轉譯器的實作和其他平台專屬的資源。

## <a name="summary"></a>總結

Xamarin 的跨平台行動裝置應用程式開發工具及平台提供全面的解決方案 B2E、 B2B 和 B2C 行動用戶端應用程式，以便能夠跨所有目標平台 （iOS、 Android 和 Windows） 和有助於降低共用程式碼擁有權總成本。 應用程式可以共用其使用者介面和應用程式邏輯程式碼，同時保留原生平台外觀與風格。

企業應用程式的開發人員面對可以在開發期間修改架構的應用程式的幾項挑戰。 因此，務必建置的應用程式，如此即可修改或擴充一段時間。 針對這類適應性設計可能很困難，但通常牽涉到資料分割成離散、 鬆散偶合的元件可以輕鬆地整合在一起到應用程式的應用程式。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
