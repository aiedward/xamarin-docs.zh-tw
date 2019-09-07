---
title: 使用 Xamarin 的企業應用程式模式電子書
description: 此電子書提供架構指引，讓您開發可調整、可維護和可測試的 Xamarin。表單企業應用程式。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a26fdd931f539da990e21166eec361fd1702de9c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760220"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>使用 Xamarin 的企業應用程式模式電子書

_開發可調整、可維護且可測試之 Xamarin 的架構指引。表單企業應用程式_

![](images/cover-sml.png "使用 Xamarin 的企業應用程式模式電子書")

此電子書提供如何執行模型 ViewModel （MVVM）模式、相依性插入、流覽、驗證和設定管理的指引，同時維持鬆散結合。 此外，也有指導方針，說明如何使用 IdentityServer 來執行驗證和授權、從容器化微服務存取資料，以及單元測試。

## <a name="prefaceprefacemd"></a>[Preface](preface.md)

本章說明本指南的目的和範圍，以及其目標物件。

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

企業應用程式的開發人員面臨幾個挑戰，可在開發期間改變應用程式的架構。 因此，請務必建立一個應用程式，讓它可以在一段時間內修改或擴充。 針對這種適應性進行設計可能會很難，但通常牽涉到將應用程式分割成可輕鬆整合到應用程式的離散、鬆散結合的元件。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

模型 ViewModel （MVVM）模式有助於將應用程式的商務和展示邏輯與其使用者介面（UI）完全分開。 維護應用程式邏輯與 UI 之間的清楚分隔，有助於解決許多開發問題，並可讓應用程式更容易測試、維護和發展。 它也可以大幅改善程式碼重複使用的機會，並可讓開發人員和 UI 設計工具在開發應用程式的個別部分時，更輕鬆地共同作業。

## <a name="dependency-injectiondependency-injectionmd"></a>[相依性插入](dependency-injection.md)

相依性插入可將具象的類型與依賴這些類型的程式碼分離。 它通常會使用容器來保存介面與抽象類別型之間的註冊和對應清單，以及用來執行或擴充這些類型的實體類型。

相依性插入容器會提供一個可具現化類別實例的功能，並根據容器的設定來管理其存留期，藉此減少物件之間的耦合。 建立物件時，容器會在其中插入物件所需的任何相依性。 如果尚未建立這些相依性，容器會先建立並解析其相依性。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[在相依性低的元件之間通訊](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會實作發行-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，協助減少元件之間的相依性，同時也允許獨立開發和測試元件。

## <a name="navigationnavigationmd"></a>[巡覽](navigation.md)

Xamarin 包含對頁面導覽的支援，這通常是由使用者與 UI 互動或應用程式本身所產生的，因為內部邏輯驅動狀態變更。 不過，在使用 MVVM 模式的應用程式中執行流覽可能會很複雜。

本章提供`NavigationService`類別，用來執行視圖模型的第一次導覽。 將導覽邏輯放在視圖模型類別中，表示邏輯可以透過自動化測試來執行。 此外，視圖模型接著可以執行邏輯來控制導覽，以確保強制執行特定商務規則。

## <a name="validationvalidationmd"></a>[驗證](validation.md)

任何接受使用者輸入的應用程式都應確保輸入有效。 如果沒有驗證，使用者可以提供會導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意資料。

在模型 ViewModel （MVVM）模式的內容中，通常需要視圖模型或模型來執行資料驗證，並向視圖發出任何驗證錯誤，讓使用者可以加以更正。

## <a name="configuration-managementconfiguration-managementmd"></a>[設定管理](configuration-management.md)

設定可讓您從程式碼中區隔的資料，來設定應用程式的行為，以在不重建應用程式的情況下變更行為。 應用程式設定是應用程式所建立及管理的資料，而使用者設定則是應用程式的可自訂設定，會影響應用程式的行為，而不需要經常重新調整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化的微服務](containerized-microservices.md)

微服務提供應用程式開發和部署的方法，其適用于現代化雲端應用程式的靈活性、規模和可靠性需求。 微服務的主要優點之一，就是可以獨立相應放大，這表示可以調整特定功能區域，而需要更多處理能力或網路頻寬來支援需求，而不需不必要的調整區域應用程式不會遇到增加的需求。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[驗證與授權](authentication-and-authorization.md)

有許多方法可以將驗證和授權整合到與 ASP.NET MVC web 應用程式通訊的 Xamarin. Forms 應用程式中。 在這裡，會使用使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 IdentityServer 是 ASP.NET Core 識別來執行 bearer 權杖驗證與整合的 ASP.NET Core 開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[存取遠端資料](accessing-remote-data.md)

許多現代化的 web 解決方案都會使用 web 伺服器所裝載的 web 服務，以提供遠端用戶端應用程式的功能。 Web 服務所公開的作業會構成 Web API，而且用戶端應用程式應該能夠利用 Web API，而不需要知道 API 所公開的資料或作業如何執行。

## <a name="unit-testingunit-testingmd"></a>[單元測試](unit-testing.md)

從 MVVM 應用程式測試模型和視圖模型，與測試任何其他類別相同，而且可以使用相同的工具和技術。 不過，有些模式通常是模型和視圖模型類別，可受益于特定的單元測試技術。

## <a name="feedback"></a>意見反應

此專案有一個「社區網站」，您可以在其中張貼問題並提供意見反應。 此社區網站位於[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，電子書的意見反應可以透過電子郵件[dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)傳送至。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
