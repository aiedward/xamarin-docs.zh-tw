---
title: 與 Xamarin 的持續整合簡介
description: 本檔描述與 Xamarin 的持續整合。 其中討論版本控制和各種持續整合環境。
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: davidortinau
ms.author: daortin
ms.date: 07/19/2017
ms.openlocfilehash: 21c8cba39031e133d96ed6a5ebeeab87f945af51
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938240"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>與 Xamarin 的持續整合簡介

_持續整合是一種軟體工程實務，在專案的版本控制存放庫中的開發人員加入或變更程式碼時，自動化組建會編譯並選擇性地測試應用程式。本文將討論持續整合的一般概念，以及可與 Xamarin 專案持續整合的一些選項。_

開發人員可以平行處理軟體專案，這是常見的情況。 在某個時間點，必須將所有這些平行資料流程整合到構成最終產品的一個程式碼基底。 在軟體發展的初期，這項整合是在專案結束時執行，這是一個艱難且有風險的程式。

持續整合（CI）藉由將每位開發人員的變更合併到通用程式碼基底（通常是每個開發人員簽入專案的共用程式碼存放庫變更）來避免這種複雜性。 每個簽入都會觸發自動化組建並執行自動化測試，以確認新引進的程式碼不會中斷任何現有程式碼。  如此一來，CI 會立即呈現錯誤和問題，並確保所有小組成員都能隨時保持最新狀態。 這會產生一致且穩定的程式碼基底。

持續整合系統有兩個主要部分：

- **版本控制**–版本控制（VC）（也稱為原始檔控制或原始程式碼管理）會將所有專案的程式碼合併至單一共用存放庫，並針對每個檔案的每項變更保存完整歷程記錄。 此存放庫（通常稱為「*主線*」或「*主要*」分支）包含最終將用來建立應用程式之生產或發行版本的原始程式碼。 這項工作有許多開放原始碼和商業產品，通常可讓小組或個人將程式碼的複本分叉成次要分支，讓他們進行大量變更或進行實驗，而不會有主要分支的風險。 一旦驗證次要分支中的變更之後，就可以將它們全部合併回到主要分支中。
- **持續整合伺服器**-持續整合伺服器負責收集項目的所有成品（原始程式碼、影像、影片、資料庫、自動化測試等）、編譯應用程式，以及執行自動化測試。 同樣地，有許多開放原始碼和商業 CI 伺服器工具。

開發人員通常會在其工作站上擁有一或多個分支的工作複本，其中的工作一開始就是執行。 一旦適當的工作集完成後，變更就會「簽入」或「已認可」至適當的分支，而將它們傳播至其他開發人員的工作複本。 這就是小組如何確保它們都是在相同的程式碼上運作。

同樣地，透過持續整合，認可變更的動作會導致 CI 伺服器建立專案並執行自動化測試，以確認原始程式碼的正確性。 如果發生組建錯誤或測試失敗，CI 伺服器會通知負責的開發人員（透過電子郵件、IM、Twitter、Growl 等等），讓他或她可以更正問題。 （如果發生失敗，CI 伺服器甚至會拒絕認可，這稱為「閘道簽入」）。

下圖說明此程式：

[![此圖說明此程式](intro-to-ci-images/intro01-small.png)](intro-to-ci-images/intro01.png#lightbox)

行動應用程式引進持續整合的獨特挑戰。 應用程式可能需要僅適用于實體裝置的感應器，例如 GPS 或攝影機。 此外，模擬器或模擬器只是硬體的近似值，而且可能隱藏或隱匿問題。 最後，您必須在實際硬體上測試行動應用程式，以確信它確實是由客戶準備的。

[App Center 測試](https://docs.microsoft.com/appcenter/test-cloud)會藉由直接在數百個實體裝置上測試應用程式，來解決這個特定的問題。 開發人員會撰寫自動化驗收測試，以提供功能強大的 UI 測試。 將這些測試上傳至 App Center 之後，CI 伺服器就可以在 CI 程式中自動執行它們，如下圖所示：

[![將這些測試上傳至 App Center 之後，CI 伺服器就可以在 CI 程式中自動執行它們，如下列圖表所示](intro-to-ci-images/intro02-small.png)](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>連續整合的元件

商業和開放原始碼工具有廣泛的生態系統，專為支援 CI 所設計。 本節將說明幾個最常見的範本。

### <a name="version-control"></a>版本控制

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/)和[Team Foundation Server](https://visualstudio.microsoft.com/tfs/) （TFS）是 Microsoft 的共同作業工具，可用於持續整合組建服務、工作追蹤、agile 規劃和報告工具，以及版本控制。 使用版本控制時，Azure DevOps 和 TFS 可以使用自己的系統（Team Foundation 版本控制或 TFVC），或與 GitHub 上裝載的專案搭配使用。

- Azure DevOps 透過雲端提供服務。 其主要優點是它不需要專用的硬體或基礎結構，而且可以透過網頁瀏覽器和熱門的開發工具（例如 Visual Studio）從任何地方存取，使其吸引地理位置分散的小組。 這適用于五位開發人員的小組免費，之後還可以購買額外的授權，以容納成長中的團隊。
- TFS 是針對內部部署 Windows 伺服器所設計，並透過區域網路或連到該網路的 VPN 連線來存取。 其主要優點是您可以完全控制組建伺服器的設定，而且可以安裝所需的任何其他軟體或服務。 TFS 對於小型團隊具有免費入門層級 Express edition。

TFS 和 Azure DevOps 都與 Visual Studio 緊密整合，並可讓開發人員在輕鬆地從單一 IDE 中執行許多版本控制和 CI 工作。 Eclipse 的 Team Explorer Everywhere 外掛程式（請參閱下文）也可供使用。 Visual Studio for Mac 具有[TFVC 的預覽版本](/visualstudio/mac/tf-version-control/)。

[Azure DevOps 管線](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)具有 Xamarin 專案的直接支援，您可以在其中為您想要設為目標的每個平臺（Android、IOS 和 Windows）建立組建定義。 每個組建定義都需要適當的 Xamarin 授權。 也可以將具備 Xamarin 功能的本機 TFS 組建伺服器連接到 Azure DevOps，以達到此目的。 在此設定中，已排入佇列至 Azure DevOps 的組建將會委派給本機伺服器。 如需詳細資訊，請參閱[組建和發行代理](https://docs.microsoft.com/azure/devops/pipelines/agents/agents)程式。 或者，您可以使用 Jenkins 或小組城市之類的另一個組建工具。

Visual Studio、Azure DevOps 和 Team Foundation Server 的所有應用程式生命週期管理（ALM）功能的完整摘要，請參閱[使用 Xamarin 應用程式 DevOps](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps)。

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)將 Team Foundation Server 和 Azure DevOps 的威力帶入 Visual Studio 外部開發的小組。 它可讓開發人員從 Eclipse 或適用于 OS X 和 Linux 的跨平臺命令列用戶端，連接到內部部署或雲端中的 team 專案。 Team Explorer Everywhere 提供非 Windows 平臺的版本控制（包括 Git）、工作專案和組建功能的完整存取權。

#### <a name="git"></a>Git

[Git](https://git-scm.com)是一種熱門的開放原始碼版本控制解決方案，最初是為了管理 Linux 核心的原始程式碼而開發。 這是一個非常快速、有彈性的系統，可提供各種大小的軟體專案。 它可以輕鬆地從單一開發人員進行調整，而不會對全球各地的大型小組進行不當的網際網路存取。 Git 也可以讓分支變得非常簡單，進而鼓勵平行開發串流，而不會產生最小的風險。

Git 可以透過網頁瀏覽器，或在 Linux、Mac OSX 和 Windows 上執行的[GUI 用戶端](https://git-scm.com/downloads/guis)來完全運作。 公用存放庫是免費的;私人存放庫需要[付費方案](https://github.com/pricing)。

Windows 和 Mac 目前的 Visual Studio 版本提供 Git 的原生支援。 Microsoft 為舊版的 Visual Studio 提供[適用于 Git 的可下載延伸](https://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c)模組。 如先前所述，Azure DevOps 和 TFS 可以使用 Git 進行版本控制，而不是 TFVC。

#### <a name="subversion"></a>Subversion

[Subversion](https://subversion.apache.org) （SVN）是受歡迎的開放原始碼版本控制系統，自2000起就已在使用中。 SVN 會在所有新式的 OS X、Windows、FreeBSD、Linux 和 Unix 版本上執行。 Visual Studio for Mac 具有 SVN 的原生支援。 有協力廠商延伸模組，可讓 SVN 支援 Visual Studio。

### <a name="continuous-integration-environments"></a>持續整合環境

設定持續整合環境表示將版本控制系統與組建服務結合。  對於後者，最常見的兩個是：

- [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/)是 AZURE DEVOPS 和 TFS 的組建系統。 它與 Visual Studio 緊密整合，讓開發人員更方便觸發組建、自動執行測試，以及查看結果。
- Jenkins 是一個開放原始碼 CI 伺服器，具有豐富的外掛程式生態系統，可支援各種軟體發展。 它會在 Windows 和 Mac OS X 上執行。 Jenkins 不會與任何特定 IDE 整合。 相反地，它是透過 web 介面進行設定和管理。 Jenkins CI 也很容易安裝和設定，使其吸引小型團隊。

您可以單獨使用 TFS/Azure DevOps，也可以搭配 TFS/Azure DevOps 或 Git 使用 Jenkins，如下列各節所述。

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

如先前所述，Azure DevOps 和 Team Foundation Server 同時提供版本控制和組建服務。 組建服務一律需要適用于每個目標平臺的 Xamarin 商務或企業授權。

使用 Azure DevOps，您可以為每個目標平臺建立個別的組建定義，並在該處輸入適當的授權。 一旦設定之後，Azure DevOps 將會在雲端中執行組建和測試。 如需詳細資訊，請參閱[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/) 。

使用 Team Foundation Server，您可以針對特定目標平臺設定組建電腦，如下所示：

- **Android 和 Windows：** 安裝 Visual Studio 和 Xamarin 工具（適用于 Android 和 Windows），並使用您的 Xamarin 授權進行設定。 您也必須將 Android SDK 移至 TFS 組建代理程式可以在伺服器上找到的共用位置。 如需詳細資訊，請參閱設定[TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)。
- **iOS 和 Xamarin：** 使用適當的授權，在 Windows server 上安裝 Visual Studio 和 Xamarin 工具。 然後將 Visual Studio for Mac 安裝在可存取網路的 Mac OS X 機上，這會做為組建主機，並建立最終的應用程式套件（適用于 iOS 的 .IPA，適用于 OS X 的應用程式）。

下圖說明這種拓撲：

[![此圖說明這種拓撲](intro-to-ci-images/intro03-small.png)](intro-to-ci-images/intro03.png#lightbox)

您也可以將本機 TFS 伺服器連結至 Azure DevOps 專案，以便將 Azure DevOps 組建委派給本機伺服器。 如需詳細資訊，請參閱[組建和發行代理](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/)程式。

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps 和 Jenkins

如果您使用 Jenkins 來建立您的應用程式，您可以將程式碼儲存在 Azure DevOps 或 Team Foundation Server，並繼續針對 CI 組建使用 Jenkins。 當您將程式碼推送至 team 專案的 Git 存放庫時，或當您將程式碼簽入 TFVC 時，可以觸發 Jenkins 組建。 如需詳細資訊，請參閱[Jenkins with Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins)。

[![如果您使用 Jenkins 來建立您的應用程式，您可以將程式碼儲存在 Azure DevOps 或 Team Foundation Server，並繼續針對 CI 組建使用 Jenkins](intro-to-ci-images/intro04-small.png)](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git 和 Jenkins

另一個常見的 CI 環境可以完全以 OS X 為基礎。 此案例牽涉到針對組建伺服器使用 Git 進行原始程式碼控制和 Jenkins。 這兩個都是在已安裝 Visual Studio for Mac 的單一 Mac OS X 電腦上執行。 這非常類似上一節中所討論的 Azure DevOps + Jenkins 環境：

[![這非常類似上一節所討論的 Azure DevOps + Jenkins 環境](intro-to-ci-images/intro05-small.png)](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **[Microsoft 不支援](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)Jenkins。**
