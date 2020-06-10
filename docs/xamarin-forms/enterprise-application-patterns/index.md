---
標題：「使用電子書的企業應用程式模式」 Xamarin.Forms 描述：「此電子書提供開發可調整、可維護和可測試之 Xamarin.Forms 企業應用程式的架構指引。」
assetid： 28cfed6c-6175-4223-a8cc-798d40bf0832 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 08/07/2017 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>使用電子書的企業應用程式模式 Xamarin.Forms

_開發可調整、可維護且可測試之 Xamarin.Forms 企業應用程式的架構指引_

![](images/cover-sml.png "Enterprise Application Patterns using Xamarin.Forms eBook")

此電子書提供如何執行模型 ViewModel （MVVM）模式、相依性插入、流覽、驗證和設定管理的指引，同時維持鬆散結合。 此外，也有指導方針，說明如何使用 IdentityServer 來執行驗證和授權、從容器化微服務存取資料，以及單元測試。

## <a name="preface"></a>[Preface](preface.md)

本章說明本指南的目的和範圍，以及其目標物件。

## <a name="introduction"></a>[簡介](introduction.md)

企業應用程式的開發人員面臨幾個挑戰，可在開發期間改變應用程式的架構。 因此，請務必建立一個應用程式，讓它可以在一段時間內修改或擴充。 針對這種適應性進行設計可能會很難，但通常牽涉到將應用程式分割成可輕鬆整合到應用程式的離散、鬆散結合的元件。

## <a name="mvvm"></a>[MVVM](mvvm.md)

模型 ViewModel （MVVM）模式有助於將應用程式的商務和展示邏輯與其使用者介面（UI）完全分開。 維護應用程式邏輯與 UI 之間的清楚分隔，有助於解決許多開發問題，並可讓應用程式更容易測試、維護和發展。 它也可以大幅改善程式碼重複使用的機會，並可讓開發人員和 UI 設計工具在開發應用程式的個別部分時，更輕鬆地共同作業。

## <a name="dependency-injection"></a>[相依性插入](dependency-injection.md)

相依性插入可將具象的類型與依賴這些類型的程式碼分離。 它通常會使用容器來保存介面與抽象類別型之間的註冊和對應清單，以及用來執行或擴充這些類型的實體類型。

相依性插入容器會提供一個可具現化類別實例的功能，並根據容器的設定來管理其存留期，藉此減少物件之間的耦合。 建立物件時，容器會在其中插入物件所需的任何相依性。 如果尚未建立這些相依性，容器會先建立並解析其相依性。

## <a name="communicating-between-loosely-coupled-components"></a>[在偶合程度低的元件之間通訊](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發行-訂閱模式，允許不方便透過物件和類型參考連結的元件之間以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，協助減少元件之間的相依性，同時也允許獨立開發和測試元件。

## <a name="navigation"></a>[瀏覽](navigation.md)

Xamarin.Forms包含頁面導覽的支援，這通常是因使用者與 UI 的互動，或來自應用程式本身的結果，因為內部邏輯驅動狀態變更的結果。 不過，在使用 MVVM 模式的應用程式中執行流覽可能會很複雜。

本章提供 `NavigationService` 類別，用來執行視圖模型的第一次導覽。 將導覽邏輯放在視圖模型類別中，表示邏輯可以透過自動化測試來執行。 此外，視圖模型接著可以執行邏輯來控制導覽，以確保強制執行特定商務規則。

## <a name="validation"></a>[驗證](validation.md)

任何接受使用者輸入的應用程式都應確保輸入有效。 如果沒有驗證，使用者可以提供會導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意資料。

在模型 ViewModel （MVVM）模式的內容中，通常需要視圖模型或模型來執行資料驗證，並向視圖發出任何驗證錯誤，讓使用者可以加以更正。

## <a name="configuration-management"></a>[組態管理](configuration-management.md)

設定可讓您從程式碼中區隔的資料，來設定應用程式的行為，以在不重建應用程式的情況下變更行為。 應用程式設定是應用程式所建立及管理的資料，而使用者設定則是應用程式的可自訂設定，會影響應用程式的行為，而不需要經常重新調整。

## <a name="containerized-microservices"></a>[容器化的微服務](containerized-microservices.md)

微服務提供應用程式開發和部署的方法，其適用于現代化雲端應用程式的靈活性、規模和可靠性需求。 微服務的主要優點之一，就是可以獨立地相應放大，這表示可以調整特定功能區域，而需要更多處理能力或網路頻寬來支援需求，而不需在不需要增加需求的情況下調整應用程式的區域。

## <a name="authentication-and-authorization"></a>[驗證和授權](authentication-and-authorization.md)

有許多方法可以將驗證和授權整合到 Xamarin.Forms 與 ASP.NET MVC web 應用程式通訊的應用程式中。 在這裡，會使用使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 IdentityServer 是開放原始碼 OpenID Connect 和 OAuth 2.0 架構，適用于與 ASP.NET Core 身分識別整合以執行持有人權杖驗證的 ASP.NET Core。

## <a name="accessing-remote-data"></a>[存取遠端資料](accessing-remote-data.md)

許多現代化的 web 解決方案都會使用 web 伺服器所裝載的 web 服務，以提供遠端用戶端應用程式的功能。 Web 服務所公開的作業會構成 Web API，而且用戶端應用程式應該能夠利用 Web API，而不需要知道 API 所公開的資料或作業如何執行。

## <a name="unit-testing"></a>[單元測試](unit-testing.md)

從 MVVM 應用程式測試模型和視圖模型，與測試任何其他類別相同，而且可以使用相同的工具和技術。 不過，有些模式通常是模型和視圖模型類別，可受益于特定的單元測試技術。

## <a name="feedback"></a>意見反應

此專案有一個「社區網站」，您可以在其中張貼問題並提供意見反應。 此社區網站位於[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，電子書的意見反應可以透過電子郵件傳送至 [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com) 。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
