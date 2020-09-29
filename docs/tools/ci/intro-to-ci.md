---
title: 與 Xamarin 的持續整合簡介
description: 本檔說明與 Xamarin 的持續整合。 它會討論版本控制和各種持續整合環境。
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: davidortinau
ms.author: daortin
ms.date: 07/19/2017
ms.openlocfilehash: 98606173913b57b7cf55f24c2581acbf82a587e3
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457857"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>與 Xamarin 的持續整合簡介

_持續整合是一種軟體工程實務，當開發人員在專案的版本控制存放庫中新增或變更程式碼時，自動化組建就會編譯並選擇性地測試應用程式。本文將討論持續整合的一般概念，以及可用於與 Xamarin 專案進行持續整合的一些選項。_

開發人員通常會在軟體專案上平行運作。 在某個時間點，您必須將所有平行的工作資料流程整合至組成最終產品的一個程式碼基底。 在軟體發展的早期階段，這項整合是在專案結束時執行，這是一個困難且有風險的程式。

持續整合 (CI) 以連續的方式將每個開發人員的變更合併到通用程式碼基底，以避免這種複雜性，通常是在任何開發人員簽入專案的共用程式碼存放庫變更時。 每次簽入時都會觸發自動化組建並執行自動化測試，以確認新引進的程式碼不會中斷任何現有的程式碼。  如此一來，CI 會立即顯示錯誤和問題，並確保所有小組成員都能保持最新狀態。 這會導致一致且穩定的程式碼基底。

持續整合系統有兩個主要部分：

- **版本控制** -版本控制 (VC) ，也稱為原始檔控制或原始程式碼管理，會將所有專案的程式碼合併至單一共用存放庫，並保留每個檔案的每個變更的完整歷程記錄。 此存放庫（通常稱為 *主線* 或 *主要* 分支）包含的原始程式碼，最終將用來建立應用程式的生產或發行版本。 這項工作有許多開放原始碼和商業產品，通常可讓小組或個人將程式碼的複本派生到次要分支，讓他們可以進行廣泛的變更或進行實驗，而不會有主要分支的風險。 一旦驗證次要分支中的變更之後，就可以將它們合併回主要分支。
- **持續整合伺服器** ：持續整合伺服器負責收集項目的所有成品 (原始程式碼、影像、影片、資料庫、自動化測試等 ) 、編譯應用程式，以及執行自動化測試。 同樣地，有許多開放原始碼和商用 CI 伺服器工具。

開發人員通常會在其工作站上擁有一或多個分支的工作複本，其中的工作會一開始就執行。 一旦完成適當的工作集，變更就會「簽入」或「認可」至適當的分支，然後將這些變更傳播到其他開發人員的工作複本。 這是小組確保它們全都在相同程式碼上運作的方式。

同樣地，透過持續整合，認可變更的動作會導致 CI 伺服器建立專案並執行自動化測試，以確認原始程式碼的正確性。 如果發生組建錯誤或測試失敗，CI 伺服器會透過電子郵件、IM、Twitter、Growl 等 ) 通知負責的開發人員 (，讓他或她可以更正問題。 如果發生失敗（稱為「閘道簽入」）， (CI 伺服器甚至可以拒絕認可。 ) 

下圖說明此程式：

[![此圖說明此流程](intro-to-ci-images/intro01-small.png)](intro-to-ci-images/intro01.png#lightbox)

行動裝置應用程式引進持續整合的獨特挑戰。 應用程式可能需要感應器，例如僅在實體裝置上提供的 GPS 或攝影機。 此外，模擬器或模擬器只是硬體的近似值，可能會隱藏或隱匿問題。 最後，您必須在實際硬體上測試行動應用程式，以確保它是真正的客戶就緒。

[App Center 測試](/appcenter/test-cloud)會直接在數百部實體裝置上測試應用程式，以解決此特定問題。 開發人員會撰寫自動化驗收測試，讓 UI 測試更加強大。 將這些測試上傳至 App Center 之後，CI 伺服器就可以在 CI 程式中自動執行這些測試，如下圖所示：

[![將這些測試上傳至 App Center 之後，CI 伺服器就可以在 CI 程式中自動執行這些測試，如下圖所示](intro-to-ci-images/intro02-small.png)](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>持續整合的元件

有廣泛的商業和開放原始碼工具生態系統，專為支援 CI 而設計。 本節將說明一些最常見的專案。

### <a name="version-control"></a>版本控制

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) 和 [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) 是 Microsoft 的共同作業工具，可用於持續整合組建服務、工作追蹤、agile 規劃和報告工具，以及版本控制。 使用版本控制時，Azure DevOps 和 TFS 可以使用自己的系統 (Team Foundation 版本控制或 TFVC) 或裝載于 GitHub 上的專案。

- Azure DevOps 透過雲端提供服務。 它的主要優點是，它不需要專用的硬體或基礎結構，也可以透過網頁瀏覽器和熱門的開發工具（例如 Visual Studio）從任何地方存取，使其更適合分散于各地的團隊。 這對五位開發人員或更少的團隊而言是免費的，在這之後，您可以購買額外的授權來容納不斷成長的團隊。
- TFS 是針對內部部署 Windows 伺服器所設計，並透過區域網路或 VPN 連線來存取該網路。 它的主要優點是您可以完全控制組建伺服器的設定，也可以安裝所需的任何其他軟體或服務。 TFS 具有適用于小型團隊的免費入門層級 Express edition。

TFS 和 Azure DevOps 都與 Visual Studio 緊密整合，並可讓開發人員從單一 IDE 內執行許多版本控制和 CI 工作。 適用于 Eclipse 的 Team Explorer Everywhere 外掛程式 (請參閱以下) 也可以使用。 Visual Studio for Mac 有 [TFVC 的預覽版本](/visualstudio/mac/tf-version-control/)。

[Azure DevOps 管線](/azure/devops/pipelines/languages/xamarin/) 具有 Xamarin 專案的直接支援，您可以在其中為您想要以 (Android、IOS 和 Windows) 為目標的每個平臺建立組建定義。 每個組建定義都需要適當的 Xamarin 授權。 您也可以將本機、支援 Xamarin 的 TFS 組建伺服器連接到 Azure DevOps 以達到此目的。 使用此設定時，會將已排入佇列的組建指派給 Azure DevOps，以委派給本機伺服器。 如需詳細資訊，請參閱 [組建和發行代理](/azure/devops/pipelines/agents/agents)程式。 或者，您也可以使用其他組建工具，例如 Jenkins 或小組城市。

所有應用程式生命週期管理 (ALM) Visual Studio、Azure DevOps 和 Team Foundation Server 功能的完整摘要，請參閱 [使用 Xamarin 應用程式進行 DevOps](/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps)。

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) 將 Team Foundation Server 和 Azure DevOps 的威力帶入在 Visual Studio 以外開發的團隊。 它可讓開發人員從 Eclipse 或適用于 OS X 和 Linux 的跨平臺命令列用戶端，連接到內部部署或雲端中的 team 專案。 Team Explorer Everywhere 提供版本控制 (的完整存取權，包括 Git) 、工作專案和非 Windows 平臺的組建功能。

#### <a name="git"></a>Git

[Git](https://git-scm.com) 是一種常用的開放原始碼版本控制解決方案，最初是為了管理 Linux 核心的原始程式碼而開發。 它是一種非常快速且彈性的系統，可在各種規模的軟體專案上受到歡迎。 您可以輕鬆地從單一開發人員進行調整，並將網際網路存取的大型團隊延伸到全球各地。 Git 也可讓分支變得非常簡單，進而鼓勵平行開發資料流程的風險最低。

Git 可以透過網頁瀏覽器，或在 Linux、Mac OSX 和 Windows 上執行的 [GUI 用戶端](https://git-scm.com/downloads/guis) 來運作。 公用存放庫免費;私人存放庫需要 [付費方案](https://github.com/pricing)。

適用于 Windows 和 Mac 的 Visual Studio 目前版本提供 Git 的原生支援。 Microsoft 針對舊版 Visual Studio 提供 [適用于 Git 的可下載延伸](https://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) 模組。 如先前所述，Azure DevOps 和 TFS 可以使用 Git 進行版本控制，而不使用 TFVC。

#### <a name="subversion"></a>Subversion

[Subversion](https://subversion.apache.org) (SVN) 是自2000起使用的熱門開放原始碼版本控制系統。 SVN 是在所有新式版本的 OS X、Windows、FreeBSD、Linux 和 Unix 上執行。 Visual Studio for Mac 具有 SVN 的原生支援。 有協力廠商擴充功能可將 SVN 支援帶 Visual Studio。

### <a name="continuous-integration-environments"></a>持續整合環境

設定持續整合環境，表示將版本控制系統與組建服務合併。  對於後者，最常見的兩種是：

- [Azure Pipelines](/azure/devops/pipelines/) 是 AZURE DEVOPS 和 TFS 的組建系統。 它與 Visual Studio 緊密整合，讓開發人員能夠輕鬆地觸發組建、自動執行測試，以及查看結果。
- Jenkins 是開放原始碼的 CI 伺服器，具有豐富的外掛程式生態系統，可支援各種類型的軟體發展。 它是在 Windows 上執行，而 Mac OS X Jenkins 未與任何特定的 IDE 整合。 相反地，它是透過 web 介面進行設定和管理。 Jenkins CI 也易於安裝及設定，使其吸引小型團隊。

您可以單獨使用 TFS/Azure DevOps，也可以搭配 TFS/Azure DevOps 或 Git 使用 Jenkins，如下列各節所述。

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

如前文所述，Azure DevOps 和 Team Foundation Server 提供版本控制和組建服務。 組建服務一律需要每個目標平臺的 Xamarin 商務或企業授權。

使用 Azure DevOps 時，您可以為每個目標平臺建立個別的組建定義，並在該處輸入適當的授權。 一旦設定之後，Azure DevOps 將會在雲端中執行組建和測試。 如需詳細資訊，請參閱 [Azure Pipelines](/azure/devops/pipelines/) 。

使用 Team Foundation Server，您可以針對特定的目標平臺設定組建電腦，如下所示：

- **Android 和 Windows：** 安裝適用于 Android 和 Windows 的 Visual Studio 和 Xamarin 工具 (，) 並以 Xamarin 授權進行設定。 您也必須將 Android SDK 移至 TFS 組建代理程式可以在伺服器上找到的共用位置。 如需詳細資訊，請參閱設定 [TFVC](/azure/devops/repos/tfvc/overview)。
- **iOS 和 Xamarin：** 使用適當的授權，在 Windows server 上安裝 Visual Studio 和 Xamarin tools。 然後將 Visual Studio for Mac 安裝在可存取網路的 Mac OS X 機上，此機器將作為組建主機，並建立最終的應用程式套件 (適用于 iOS 的 IPA、OS X) 的應用程式。

下圖說明這種拓撲：

[![此圖說明這種拓撲](intro-to-ci-images/intro03-small.png)](intro-to-ci-images/intro03.png#lightbox)

您也可以將本機 TFS 伺服器連結至 Azure DevOps 專案，以便將 Azure DevOps 組建委派給本機伺服器。 如需詳細資訊，請參閱 [組建和發行代理](/azure/devops/pipelines/agents/agents/)程式。

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps 和 Jenkins

如果您使用 Jenkins 來建立應用程式，您可以在 Azure DevOps 或 Team Foundation Server 中儲存程式碼，並繼續針對您的 CI 組建使用 Jenkins。 當您將程式碼推送至 team 專案的 Git 存放庫，或將程式碼簽入 TFVC 時，可以觸發 Jenkins 組建。 如需詳細資訊，請參閱 [使用 Azure DevOps Jenkins](/azure/devops/service-hooks/services/jenkins)。

[![如果您使用 Jenkins 來建立應用程式，您可以在 Azure DevOps 或 Team Foundation Server 中儲存程式碼，並繼續針對您的 CI 組建使用 Jenkins](intro-to-ci-images/intro04-small.png)](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git 和 Jenkins

另一個常見的 CI 環境可以完全以 OS X 為基礎。 此案例牽涉到使用 Git 進行原始程式碼控制，並 Jenkins 組建伺服器。 這兩個都是在安裝 Visual Studio for Mac 的單一 Mac OS X 電腦上執行。 這與上一節所討論的 Azure DevOps + Jenkins 環境非常類似：

[![這與上一節所討論的 Azure DevOps + Jenkins 環境非常類似](intro-to-ci-images/intro05-small.png)](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **[Microsoft 不支援](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)Jenkins。**