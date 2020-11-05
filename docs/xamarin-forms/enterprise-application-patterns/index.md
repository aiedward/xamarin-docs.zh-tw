---
title: 使用電子書的企業應用程式模式 Xamarin.Forms
description: 此電子書提供架構指引，以開發可調整、可維護且可測試的 Xamarin.Forms 企業應用程式。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1b8d33ba98dfeda8db23e516a989ebbfe1db9a66
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372999"
---
# <a name="enterprise-application-patterns-using-no-locxamarinforms-ebook"></a>使用電子書的企業應用程式模式 Xamarin.Forms

_適用于開發可調整、可維護且可測試之 Xamarin.Forms 企業應用程式的架構指引_

![使用：：： no loc (Xamarin 的企業應用程式模式) ：：：電子書](images/cover-sml.png)

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

此電子書提供有關如何在 ViewModel (MVVM) 模式、相依性插入、流覽、驗證和設定管理等方面的指引，同時維持鬆散結合性。 此外，也有指導方針，說明如何使用 IdentityServer 來執行驗證和授權、從容器化微服務存取資料，以及單元測試。

## <a name="preface"></a>[Preface](preface.md)

本章將說明本指南的用途和範圍，以及其目標。

## <a name="introduction"></a>[簡介](introduction.md)

企業應用程式的開發人員面臨幾項挑戰，可在開發期間改變應用程式的架構。 因此，請務必建立應用程式，讓它可以隨著時間修改或擴充。 針對這類適應性設計可能會很困難，但通常牽涉到將應用程式分割成離散、鬆散結合的元件，而這些元件可以輕鬆地整合到應用程式中。

## <a name="mvvm"></a>[MVVM](mvvm.md)

ViewModel (MVVM) 模式的模型視圖可協助您將應用程式的商務和展示邏輯與使用者介面 (UI) 完全分開。 在應用程式邏輯和 UI 之間保持乾淨的分隔有助於解決許多開發問題，並可讓應用程式更容易進行測試、維護和演進。 它也可以大幅改善程式碼重複使用的機會，並讓開發人員和 UI 設計工具在開發應用程式的個別元件時，更輕鬆地共同作業。

## <a name="dependency-injection"></a>[相依性插入](dependency-injection.md)

相依性插入可讓您從相依于這些型別的程式碼中分離出具象類型。 它通常會使用容器來保存介面和抽象型別之間的註冊和對應清單，以及執行或擴充這些型別的實體型別。

相依性插入容器藉由提供可具現化類別實例的工具，並根據容器的設定來管理其存留期，藉以減少物件之間的結合。 在建立物件期間，容器會插入物件所需的任何相依性。 如果尚未建立這些相依性，容器會先建立並解析其相依性。

## <a name="communicating-between-loosely-coupled-components"></a>[在偶合程度低的元件之間通訊](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發佈-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，有助於減少元件之間的相依性，同時也允許獨立開發和測試元件。

## <a name="navigation"></a>[導覽](navigation.md)

Xamarin.Forms 包含對頁面導覽的支援，通常是因為使用者與 UI 的互動，或來自應用程式本身，而導致內部邏輯導向狀態變更。 不過，在使用 MVVM 模式的應用程式中執行流覽可能很複雜。

本章提供 `NavigationService` 類別，用來執行視圖模型的第一次導覽。 將導覽邏輯放在 view 模型類別中，表示邏輯可透過自動化測試來執行。 此外，view 模型還可以執行邏輯來控制導覽，以確保強制執行某些商務規則。

## <a name="validation"></a>[驗證](validation.md)

接受使用者輸入的任何應用程式都應該確保輸入有效。 如果沒有驗證，使用者可以提供導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意資料。

在模型視圖 ViewModel (MVVM) 模式的情況下，通常需要有一個視圖模型或模型來執行資料驗證，並將任何驗證錯誤告知視圖，讓使用者可以更正這些錯誤。

## <a name="configuration-management"></a>[組態管理](configuration-management.md)

設定允許將設定應用程式行為的資料區隔，而不需要重建應用程式。 應用程式設定是應用程式所建立和管理的資料，而使用者設定是可自訂的應用程式設定，其會影響應用程式的行為，而不需要經常重新調整。

## <a name="containerized-microservices"></a>[容器化的微服務](containerized-microservices.md)

微服務提供一種應用程式開發和部署的方法，適用于新式雲端應用程式的靈活性、規模和可靠性需求。 微服務的主要優點之一，就是可以獨立相應放大，這表示特定功能區域可進行調整，需要更多處理能力或網路頻寬來支援需求，而不會有不必要地調整應用程式的區域，而不會增加需求。

## <a name="authentication-and-authorization"></a>[驗證和授權](authentication-and-authorization.md)

有許多方法可將驗證和授權整合至 Xamarin.Forms 與 ASP.NET MVC web 應用程式通訊的應用程式。 在這裡，系統會使用使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 IdentityServer 是開放原始碼 OpenID Connect 和 OAuth 2.0 架構，適用于與 ASP.NET Core 身分識別整合以執行持有人權杖驗證的 ASP.NET Core。

## <a name="accessing-remote-data"></a>[存取遠端資料](accessing-remote-data.md)

許多新式的 web 型解決方案都利用 web 伺服器所裝載的 web 服務，來提供遠端用戶端應用程式的功能。 Web 服務所公開的作業構成了 web API，而用戶端應用程式應該能夠利用 web API，而不需要知道 API 所公開的資料或作業如何實行。

## <a name="unit-testing"></a>[單元測試](unit-testing.md)

從 MVVM 應用程式測試模型和查看模型與測試任何其他類別相同，而且可以使用相同的工具和技術。 不過，有一些模式通常是模型和視圖模型類別，可受益于特定的單元測試技術。

## <a name="community-site"></a>社區網站

這個專案有一個小組網站，您可以在其中張貼問題，並提供意見反應。 此社區網站位於 [GitHub](https://github.com/dotnet-architecture/eShopOnContainers)上。 或者，您也可以透過電子郵件傳送電子書的意見反應 [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com) 。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
