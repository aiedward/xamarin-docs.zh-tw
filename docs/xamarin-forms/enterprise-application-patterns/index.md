---
title: 使用 Xamarin.Forms 電子書的企業應用程式模式
description: 此電子書提供開發具可調適性、 可維護性及可測試的 Xamarin.Forms 企業應用程式的架構指引。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ecfe99f66e16eafabc3117036ff065e3a35259c3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994344"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>使用 Xamarin.Forms 電子書的企業應用程式模式

_開發具可調適性、 可維護性及可測試的 Xamarin.Forms 企業應用程式的架構指引_

![](images/cover-sml.png "使用 Xamarin.Forms 電子書的企業應用程式模式")

此電子書將提供如何實作 Model View ViewModel (MVVM) 模式、 相依性插入、 導覽、 驗證和組態管理，同時保持鬆散結合的指引。 此外，還有指引上執行驗證與授權與 IdentityServer，從容器化微服務和單元測試中存取資料。

## <a name="prefaceprefacemd"></a>[Preface](preface.md)

本章節說明的用途和範圍的指南中，而它的目標是人員。

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

企業應用程式的開發人員會面臨一些挑戰，可以在開發期間修改應用程式的架構。 因此，務必建置的應用程式，使其可以修改或擴充一段時間。 這類可適性設計可能很困難，但通常牽涉到資料分割成獨立且鬆散偶合的元件可以輕鬆地整合在一起到應用程式的應用程式。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

Model View ViewModel (MVVM) 模式可協助以明確區分從其使用者介面 (UI) 的應用程式的商務和呈現邏輯。 維護應用程式邏輯和 UI 清楚劃分幫助您解決許多開發問題，並可讓您更輕鬆地測試、 維護及改進的應用程式。 它可以也可大幅提升程式碼重複使用機會，並可讓開發人員和開發的應用程式及其個別組件時，UI 設計工具，能夠更輕易地共同作業。

## <a name="dependency-injectiondependency-injectionmd"></a>[相依性插入](dependency-injection.md)

相依性插入可分離的具象類型取決於這些類型的程式碼。 它通常會使用保存的註冊與介面和抽象的型別之間的對應清單的容器和實作或擴充這些類型的具象型別。

相依性插入容器會降低藉由提供具現化類別執行個體，並管理其存留期，根據容器的組態功能的物件之間的結合程度。 在物件建立時，容器會將任何物件需要的相依性插入至它。 如果這些相依性有尚未建立，容器會建立，並先解析其相依性。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[在相依性低的元件之間通訊](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別會實作 「 發佈-訂閱模式，允許訊息式都不太方便，連結物件和型別參考的元件之間的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要的參考，協助降低元件，同時也允許獨立開發和測試的元件之間的相依性。

## <a name="navigationnavigationmd"></a>[巡覽](navigation.md)

Xamarin.Forms 包含頁面巡覽，通常會從使用者的互動的 UI，或從應用程式本身，因為邏輯驅動的內部狀態的變更而造成的支援。 不過，瀏覽可以在使用 MVVM 模式的應用程式中實作複雜。

本章介紹`NavigationService`類別，用來執行從檢視模型的檢視模型首次瀏覽。 將瀏覽邏輯放在檢視模型類別，表示邏輯，可以透過自動化的測試中運用。 此外，檢視模型然後可實作邏輯，以控制瀏覽，以確保特定的商務規則會強制執行。

## <a name="validationvalidationmd"></a>[驗證](validation.md)

接受使用者輸入的任何應用程式應該確定輸入有效。 不需要驗證，使用者可以提供會導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者將惡意的資料。

在內容的模型-ViewModel (MVVM) 模式，檢視模型或模型通常必須執行資料驗證，並發出信號至檢視的任何驗證錯誤，以便讓使用者更正。

## <a name="configuration-managementconfiguration-managementmd"></a>[設定管理](configuration-management.md)

設定可讓設定程式碼中，從應用程式行為的資料分離，允許變更而不需重建應用程式的行為。 應用程式設定資料的應用程式建立和管理，而使用者設定可自訂的應用程式設定會影響應用程式的行為，不需要經常重新調整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化的微服務](containerized-microservices.md)

微服務都會提供一種應用程式開發和部署適用於現代化雲端應用程式的靈活度、 小數位數和可靠性需求。 其中一個微服務的主要優點是，它們可以獨立相應放大，這表示，特定的功能區域可以調整需要多個處理電源或網路頻寬來支援需求，而不會不必要地縮放的區域不發生日益增加的需求的應用程式。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[驗證與授權](authentication-and-authorization.md)

有多種方法可將驗證與授權整合至 Xamarin.Forms 應用程式與 ASP.NET MVC web 應用程式進行通訊。 在這裡，使用 IdentityServer 4 的容器化的身分識別微服務會執行驗證和授權。 IdentityServer 是 ASP.NET Core 識別來執行 bearer 權杖驗證與整合的 ASP.NET Core 開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[存取遠端資料](accessing-remote-data.md)

許多現代化的 web 型解決方案進行的遠端用戶端應用程式提供功能的網頁伺服器所裝載的 web 服務使用。 Web 服務所公開的作業構成 web API，且用戶端應用程式應該要能夠利用 web API，而不需要知道如何實作資料或 API 會公開的作業。

## <a name="unit-testingunit-testingmd"></a>[單元測試](unit-testing.md)

測試模型和檢視模型從 MVVM 應用程式等同於測試任何其他類別，並使用相同的工具和技術。 不過，有一些模式的一般模型和檢視模型類別，可從特定的單元測試技術獲益。

## <a name="feedback"></a>意見反應

此專案有社群網站，您可以張貼問題，並提供意見反應。 社群網站位於[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，意見反應電子書可以透過電子郵件傳送至[ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
