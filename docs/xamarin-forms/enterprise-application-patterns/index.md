---
title: 使用 Xamarin.Forms 電子書的企業應用程式模式
description: 這個電子書提供開發可調適性、 可維護性，以及可測試 Xamarin.Forms 企業應用程式的架構指引。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: c401465d8a57abe1d5a1cfaf9ee2616888332ea3
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242159"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>使用 Xamarin.Forms 電子書的企業應用程式模式

_開發具有可調適性、 可維護性，以及可測試 Xamarin.Forms 企業應用程式的架構指引_

![](images/cover-sml.png "使用 Xamarin.Forms 電子書的企業應用程式模式")

這個電子書如何實作模型-檢視-ViewModel (MVVM) 模式、 相依性插入、 導覽、 驗證和設定管理，同時維持鬆散偶合提供指引。 此外，還有指引上執行驗證與授權搭配 IdentityServer，從容器化的 microservices 和單元測試存取資料。

## <a name="prefaceprefacemd"></a>[Preface](preface.md)

本章節說明的用途和指南，和它的目標是使用者的範圍。

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

企業應用程式的開發人員面對可以在開發期間修改架構的應用程式的幾項挑戰。 因此，務必建置的應用程式，如此即可修改或擴充一段時間。 針對這類適應性設計可能很困難，但通常牽涉到資料分割成離散、 鬆散偶合的元件可以輕鬆地整合在一起到應用程式的應用程式。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

模型-檢視-ViewModel (MVVM) 模式，有助於清楚分隔應用程式從其使用者介面 (UI) 的商務和呈現邏輯。 維護應用程式邏輯和 UI 清楚分隔有助於解決許多開發的問題，並可以讓您更輕鬆地測試、 維護及發展的應用程式。 它可以也可大幅改善程式碼重複使用的機會，並可讓開發人員和開發應用程式中其各自的一部分時，UI 設計工具可以更輕易地共同作業。

## <a name="dependency-injectiondependency-injectionmd"></a>[相依性插入](dependency-injection.md)

相依性插入 」 可讓從程式碼相依於這些類型的具象型別分開處理。 通常會使用保留一份註冊的介面和抽象的型別之間的對應的容器以及實作或擴充這些類型的具象型別。

相依性插入容器會降低藉由提供具現化類別執行個體和管理其存留期的容器設定為基礎的功能物件之間的連接。 物件在建立期間，容器會將任何物件需要的相依性插入至它。 如果這些相依性具有尚未建立，容器就會建立，並先解決它們的相依性。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[在相依性低的元件之間通訊](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別會實作發行-訂閱模式，讓不方便，連結物件和型別參考的元件之間的訊息為基礎的通訊。 此機制可讓發行者和訂閱者進行通訊，而不需要的參考，幫助減少元件，同時也允許以獨立開發和測試的元件之間的相依性。

## <a name="navigationnavigationmd"></a>[巡覽](navigation.md)

Xamarin.Forms 包含頁面導覽，通常是從使用者互動具有 UI，或從應用程式本身，因為邏輯驅動的內部狀態的變更而支援。 但是，瀏覽很複雜，所以無法使用 MVVM 模式應用程式中實作。

本章提供`NavigationService`類別，可用來在檢視模型檢視模型優先 （contract-first） 瀏覽。 將巡覽邏輯放在檢視模型類別，表示邏輯，可以運用到自動化測試。 此外，檢視模型然後實作邏輯，以確保特定的商務規則會強制執行控制項巡覽。

## <a name="validationvalidationmd"></a>[驗證](validation.md)

可接受使用者輸入的任何應用程式應該確定輸入有效。 不需要驗證，使用者可以提供會導致失敗的應用程式的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意的資料。

在內容的模型 ViewModel 模型 (MVVM) 模式，檢視模型或模型通常需要執行資料驗證，並發出訊號檢視任何驗證錯誤，以便讓使用者更正。

## <a name="configuration-managementconfiguration-managementmd"></a>[設定管理](configuration-management.md)

設定可讓設定程式碼中，從應用程式的行為的資料分離允許變更而不需重建應用程式的行為。 應用程式設定應用程式建立及管理的資料，而使用者設定應用程式的可自訂設定會影響應用程式的行為，不需要頻繁的重新調整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化的微服務](containerized-microservices.md)

Microservices 都會提供一種應用程式開發及部署適用於現代化雲端應用程式的靈活度、 小數位數和可靠性需求。 其中一個將 microservices 的主要優點是它們可以是向外延展獨立作業，這表示，特定的功能區域可以調整，需要更多處理電源或網路頻寬以支援的需求，而不會不必要地縮放的區域應用程式沒有遭遇日益增加的需求。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[驗證與授權](authentication-and-authorization.md)

有許多方法可以將驗證和授權整合到 Xamarin.Forms 應用程式與 ASP.NET MVC web 應用程式進行通訊。 在這裡，驗證和授權會執行與使用 IdentityServer 4 進行容器化的身分識別微服務。 IdentityServer 是 ASP.NET Core 識別來執行 bearer 權杖驗證與整合的 ASP.NET Core 開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[存取遠端資料](accessing-remote-data.md)

許多新式 web 為基礎的解決方案進行的遠端用戶端應用程式提供功能的網頁伺服器所裝載的 web 服務使用。 Web 服務公開的作業組成的 web 應用程式開發介面，和用戶端應用程式應該要能夠利用 web API，而不需要知道如何實作資料或應用程式開發介面會公開的作業。

## <a name="unit-testingunit-testingmd"></a>[單元測試](unit-testing.md)

測試模型和檢視模型 MVVM 應用程式從相同測試的其他任何類別，並使用相同的工具和技術。 不過，有一些一般模型的模式和檢視模型類別，可從特定的單元測試技術獲益。

## <a name="feedback"></a>意見反應

這個專案有社群網站，您可以在其張貼問題，並提供意見反應。 社群網站位於[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，可以電子書的意見傳送至[ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
