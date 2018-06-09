---
title: 容器化的 Microservices
description: 本章節將說明如何使用 microservices 和容器來建置敏捷式軟體開發、 可擴充和可靠的現代化雲端應用程式。
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 33be84bc17f72c8b70d117a0742b001f1f763d3d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242257"
---
# <a name="containerized-microservices"></a>容器化的 Microservices

開發用戶端-伺服器應用程式會導致著重於建立使用特定技術每一層的分層式應用程式。 這類應用程式通常稱為*龐大*應用程式，並封裝到預先調整 尖峰負載的硬體。 這個開發方法的主要缺點是，無法輕鬆地調整個別的元件，每個層內的元件和測試的成本之間的緊密結合。 簡單的 update 可以有預料之外的結果上的階層，其餘部分，使其與應用程式元件的變更需要重新測試也無須重新部署其整個層。

特別是關於在雲端的時代，個別的元件不能輕鬆地縮放。 整合的應用程式包含網域專屬的功能，和通常除以例如前端、 商務邏輯和資料存放區的功能層級。 整合的應用程式縮放藉由複製到多部電腦，整個應用程式的說明圖 8-1。

![](containerized-microservices-images/monolithicapp.png "整合應用程式調整方法")

**圖 8-1**： 整合型應用程式延展的方法

## <a name="microservices"></a>Microservices

Microservices 提供應用程式開發和部署到不同的方法，適用於的靈活度、 小數位數和可靠性需求的現代化雲端應用程式的方法。 Microservices 應用程式會分解成協同運作以提供應用程式的整體功能的獨立元件。 詞彙微服務強調，應用程式應該服務所組成夠小，以反映獨立考量，使每個微服務實作單一函式。 此外，每個微服務都具有妥善定義的合約，讓其他 microservices 可以通訊，並分享資料。 Microservices 的典型的例子包括購物車、 庫存處理、 子系統和付款處理程序。

Microservices 可以向外延展獨立作業，相較於大型整合的應用程式一起調整規模。 這表示，可以調整特定功能區域中，需要更多支援要求的處理電源或網路頻寬，而不是不必要地向外延展應用程式的其他區域。 圖 8-2 說明這種方式，其中 microservices 部署和獨立擴充，在電腦之間建立的服務執行個體。

![](containerized-microservices-images/microservicesapp.png "Microservices 應用程式調整方法")

**圖 8-2**: Microservices 應用程式延展的方法

向外延展微服務可以是幾乎是瞬間完成，允許以適應變更載入的應用程式。 例如，應用程式的直接存取 web 之功能的單一微服務可能需要向外延展以處理其他的連入流量的應用程式中唯一的微服務。

應用程式的延展性的傳統模型是永續性資料儲存的共用外部資料存放區的負載平衡的、 無狀態層。 可設定狀態的 microservices 管理他們自己的持續性資料，通常它們被放在伺服器上本機儲存，以避免額外負荷的網路存取和複雜度跨服務作業。 這可讓資料的最快可能處理，並可免除需要快取系統。 此外，可擴充的可設定狀態 microservices 通常分割其執行個體，管理超出可支援單一伺服器的資料大小和傳輸輸送量之間的資料。

Microservices 也支援獨立更新。 這種鬆散偶合 microservices 之間提供快速且可靠的應用程式發展。 獨立、 分散式本質上支援輪流更新，其中只有一個單一的微服務的執行個體的子集將會更新在任何指定時間。 因此，如果偵測到問題時，無法正確更新可以回復，才能使用錯誤的程式碼或組態來更新所有執行個體。 同樣地，microservices 通常使用結構描述版本設定，讓用戶端會在套用更新時，不論哪個微服務執行個體要進行通訊與時，請參閱一致性版本。

因此，微服務應用程式會透過整合的應用程式有許多優點：

-   每個微服務是相對較小、 容易管理及改進。
-   每個微服務可以開發並部署與其他服務無關。
-   每個微服務可以獨立是向外延展。 例如，類別目錄服務或購物籃服務可能需要多個已排序的服務會向外延展。 因此，產生的基礎結構會更有效率地耗用資源時向外延展。
-   每個微服務隔離的任何問題。 例如，如果在服務中發生問題時它只會影響該服務。 其他服務可以繼續處理要求。
-   每個微服務可以使用最新的技術。 由於 microservices 自發和執行由並存，最新技術與架構可以使用，而不需強迫使用整合型應用程式可能使用較舊架構。

不過，基礎的微服務方案也會有潛在的缺點：

-   選擇資料分割成 microservices 應用程式的方式會有點困難，因為每個微服務必須是完全自動、 端對端，包括其資料來源的責任。
-   開發人員必須實作服務間的通訊，這會加入至應用程式的複雜性和延遲。
-   不可部分完成的交易，通常是多個 microservices 之間不可能做到。 因此，商務需求也必須不怕最終 microservices 之間的一致性。
-   在生產環境中，沒有操作的複雜性，在部署和管理系統，許多的獨立服務的危害。
-   直接到微服務的用戶端通訊難以重構 microservices 的合約。 例如，一段時間系統分割成服務的方式可能需要變更。 單一服務可能會分成兩個或多個服務，並可能會合併兩個服務。 當用戶端會直接與 microservices 通訊時，這項重構工作可以中斷與用戶端應用程式的相容性。

## <a name="containerization"></a>容器化

集裝箱化是在其中應用程式和其建立版本的相依性，加上部署資訊清單檔案，以區隔其環境設定集封裝在一起做為一個單位，測試容器映像的軟體開發方法和部署至主機的作業系統。

容器是隔離的資源控制的可攜式作業環境，但沒有碰觸的其他容器或主機資源的應用程式可讓執行。 因此，容器會看起來和的作用就像在新安裝的實體電腦或虛擬機器。

有許多相似之處，容器和虛擬機器之間的說明圖 8-3。

![](containerized-microservices-images/containersvsvirtualmachines.png "Microservices 應用程式調整方法")

**圖 8-3**： 虛擬機器和容器的比較

容器執行作業系統、 具有檔案系統，而且可以透過網路存取，就好像實體或虛擬機器。 不過，在技術和概念容器所使用的是從虛擬機器非常不同。 虛擬機器包含應用程式、 必要的相依性和完整的客體作業系統。 容器包括應用程式和其相依性，但執行獨立處理序為主機作業系統 （除了特殊的虛擬機器，每個容器內執行 HYPER-V 容器） 上的其他容器與共用的作業系統。 因此，容器共用資源，而且通常需要較少的資源比虛擬機器。

容器導向的開發和部署方法的優點是它會排除大部分的不一致的環境安裝與使用它們的問題所引起的問題。 此外，容器會將執行個體中新的容器，視需要允許快速的應用程式擴充功能。

建立和使用的容器時的重要概念是：

-   容器主機： 實體或虛擬機器設定主機的容器。 容器主機將會執行一個或多個容器。
-   容器映像： 映像包含聯集的多層式檔案系統彼此交互堆疊，而是容器的基礎。 映像沒有狀態，並永遠不會變更為將它部署至不同的環境。
-   容器： 容器會是映像的執行階段執行個體。
-   容器 OS 映像： 容器會從映像進行部署。 容器作業系統映像會構成容器可能有多個映像層中的第一個圖層。 在容器的作業系統不可變的且無法修改。
-   容器儲存機制： 每次建立容器映像時，將映像和其相依性會儲存在本機儲存機制。 這些映像可以在容器主機上重複使用多次。 容器映像也儲存在公用或私人登錄中，例如[Docker Hub](https://hub.docker.com/)，好讓它們可以用於跨不同的容器主機。

企業正逐漸採用容器，實作微服務架構的應用程式，且 Docker 有會變成已採用大部分的軟體平台和雲端廠商的標準容器實作。

EShopOnContainers 參考應用程式會使用 Docker 來裝載四個容器化的後端 microservices，圖 8-4 的說明。

![](containerized-microservices-images/microservicesarchitecture.png "eShopOnContainers 參考應用程式後端 microservices")

**圖 8-4**: eShopOnContainers 參考應用程式後端 microservices

中參考應用程式後端服務的架構會分解成多個自發子系統中共同作業 microservices 和容器的表單。 每個微服務提供功能的單一區域： 識別服務、 目錄服務、 排序的服務和購物籃服務。

每個微服務有它自己的資料庫，讓它可以完全與其他 microservices 分離。 在需要時，從不同 microservices 資料庫之間的一致性被達成使用應用程式層級事件。 如需詳細資訊，請參閱[通訊之間 Microservices](#communication_between_microservices)。

如需參考應用程式的詳細資訊，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>用戶端與 Microservices 之間的通訊

EShopOnContainers 行動裝置應用程式通訊以進行容器化的後端 microservices 使用*直接用戶端的微服務*顯示圖 8-5 的通訊。

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Microservices 應用程式調整方法")

**圖 8-5**： 直接用戶端的微服務通訊

與直接以微服務的用戶端通訊，行動裝置應用程式會以直接透過其公用端點，透過不同的 TCP 連接埠每微服務每個微服務提出要求。 在生產環境中，端點會通常會對應到微服務的負載平衡器，將要求分散到可用的執行個體。

> [!TIP]
> 請考慮使用 API 閘道通訊。 當建置大型且複雜的微服務架構的應用程式，但更適合用於小型應用程式時，直接通訊，用戶端的微服務可以有缺點。 當設計大型的微服務為基礎的 microservices 數千萬應用程式時，請考慮使用 API 閘道通訊。 如需詳細資訊，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Microservices 之間的通訊

Microservices 基礎應用程式是分散式的系統，可能會在多部電腦上執行。 每個服務執行個體通常就是一個處理序。 因此，服務必須使用處理序間的通訊協定，例如 HTTP、 TCP、 進階訊息佇列通訊協定 (AMQP) 或二進位通訊協定，根據每個服務的本質互動。

微服務-微服務通訊的兩種常見方法是以查詢資料，以及輕量型非同步傳訊時透過多個 microservices 通訊更新時，HTTP 基礎 REST 通訊。

跨多個 microservices 傳播變更時，重要的非同步傳訊基礎的事件導向通訊。 使用這個方法，微服務發行事件時值得注意時發生，例如，它會更新的商務實體。 其他 microservices 訂閱這些事件。 然後，當微服務收到事件時，它會更新自己商務實體，接著可能會導致更多的事件發行。 此發行-訂閱功能通常與事件匯流排達成。

事件匯流排 」 可讓發行-訂閱 microservices，而不需要留意明確彼此，如圖 8-6 所示的元件之間的通訊。

![](containerized-microservices-images/eventbus.png "與事件匯流排發行-訂閱")

**圖 8-6:** 發行-訂閱與事件匯流排

從應用程式的觀點而言，事件匯流排是只要發行-訂閱介面透過公開的通道。 不過，事件匯流排實作的方式而有所不同。 例如，事件匯流排實作無法使用 RabbitMQ、 Azure 服務匯流排、 或 MassTransit NServiceBus 等其他服務匯流排。 圖 8-7 顯示事件匯流排 eShopOnContainers 參考應用程式中的使用方式。

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "事件驅動的非同步通訊，參考應用程式中")

**圖 8-7:** 參考應用程式中的非同步事件導向通訊

EShopOnContainers 事件匯流排實作使用 RabbitMQ，提供一對多非同步發行-訂閱功能。 這表示，發行事件之後, 可以有多個接聽相同事件的訂閱者。 圖 8-9 說明這個關聯性。

![](containerized-microservices-images/eventdrivencommunication.png "一對多通訊")

**圖 8-9**:-一對多通訊

此一對多的通訊方法會使用事件來實作跨越多項服務，確保在服務之間的最終一致性的商務交易。 最終一致性的交易是由一系列的分散式步驟所組成。 因此，當使用者設定檔的微服務收到 UpdateUser 命令時，它會更新其資料庫中使用者的詳細資料並將 UserUpdated 事件發行到事件匯流排。 購物籃微服務和排序的微服務已經訂閱接收此事件，並在回應中更新其買方資訊在其個別資料庫中。

> [!NOTE]
> EShopOnContainers 事件匯流排實作使用 RabbitMQ，旨在僅做概念證明。 對生產系統，應該考量替代事件匯流排實作。

事件匯流排實作的相關資訊，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

## <a name="summary"></a>總結

Microservices 都會提供一種應用程式開發及部署適用於現代化雲端應用程式的靈活度、 小數位數和可靠性需求。 其中一個將 microservices 的主要優點是它們可以是向外延展獨立作業，這表示，特定的功能區域可以調整，需要更多處理電源或網路頻寬以支援的需求，而不會不必要地縮放的區域應用程式沒有遭遇日益增加的需求。

容器是隔離的資源控制的可攜式作業環境，但沒有碰觸的其他容器或主機資源的應用程式可讓執行。 企業正逐漸採用容器，實作微服務架構的應用程式，且 Docker 有會變成已採用大部分的軟體平台和雲端廠商的標準容器實作。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
