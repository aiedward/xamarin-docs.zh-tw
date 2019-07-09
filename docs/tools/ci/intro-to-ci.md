---
title: 使用 Xamarin 的連續整合簡介
description: 本文件說明使用 Xamarin 的連續整合。 它討論版本控制，以及各種不同的連續整合環境。
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: lobrien
ms.author: laobri
ms.date: 07/19/2017
ms.openlocfilehash: 48dd2e1f2f5dd9ffdad0b726c5066a9a968d2396
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650542"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>使用 Xamarin 的連續整合簡介

_持續整合是軟體工程實務，自動化的組建會編譯並選擇性地新增或變更專案的版本控制儲存機制中的開發人員程式碼時，測試應用程式。本文將討論持續整合的一般概念，以及一些可用的選項進行持續整合與 Xamarin 專案。_

通常會以平行方式運作的開發人員的軟體專案上。 在某些時候，就必須將所有這些整合成一個工作的平行資料流將構成最終產品程式碼基底。 在軟體開發的初期，這項整合已執行的專案，是既困難又有風險的程序的結尾。

持續整合 (CI)，以避免這種複雜性每位開發人員的變更合併到持續為基礎的通用程式碼基底，通常在任何開發人員簽入只要變更專案的共用程式碼存放庫。 每次簽入時觸發自動化的組建並執行自動化的測試，以確認新引入的程式碼沒有破壞任何現有的程式碼。  如此一來，CI 會立即呈現錯誤和問題，並確保所有小組成員都保持在最新的其他人的工作。 這會導致凝聚力與穩定程式碼基底。

持續整合系統有兩個主要部分：

- **版本控制**– 版本控制 (VC)，也稱為原始檔控制或原始程式碼管理，將所有專案的程式碼合併到單一的共用存放庫，並保留每個檔案的每一項變更的完整歷程記錄。 此存放庫中，通常稱為*主線*或是*主要*分支中，包含最終會使用要建置生產或發行版本的應用程式的原始程式碼。 有許多開放原始碼和商業產品針對此工作，這通常會允許小組或個人派生至次要的分支，它們可以在其中進行廣泛的變更或進行實驗，而不致危及主要分支的 程式碼的複本。 第二個分支中的變更會經過驗證後，他們接著可以一起合併回主要分支。
- **持續整合伺服器**– 持續整合伺服器負責收集的所有專案成品 （原始碼、 影像、 影片、 資料庫、 自動化的測試等）、 編譯應用程式，和執行自動化的測試。 同樣地，有許多開放原始碼和商業的 CI 伺服器工具。

開發人員通常會在他們的工作站，一開始完成工作上需要一或多個分支的工作複本。 一組適合的工作完成之後，變更 「 簽入 」 或者 「 確認 」 至適當的分支中，將它們傳播到其他開發人員的工作複本。 這是小組如何確保他們是所有處理相同的程式碼。

同樣地，透過持續整合，在認可變更的動作會導致 CI 伺服器建置專案並執行自動化的測試，以確認原始碼的正確性。 如果沒有建置錯誤，或測試失敗，CI 伺服器會通知負責的開發人員 （透過電子郵件、 IM、 Twitter、 Growl，等） 讓他或她可以更正問題。 （CI 伺服器可甚至會拒絕認可如果發生失敗，也就所謂 「 閘道簽入 」。）

下圖說明此程序：

[![](intro-to-ci-images/intro01-small.png "此圖說明此程序")](intro-to-ci-images/intro01.png#lightbox)

行動裝置應用程式導入獨特的挑戰，如持續整合。 應用程式可能需要如 GPS 或相機才可用在實體裝置上的感應器。 此外，模擬器只可約略估算的硬體和可能隱藏或遮蔽的問題。 總而言之，就必須測試實際的硬體，確信它是真正可供客戶使用行動應用程式。

[App Center 測試](https://docs.microsoft.com/appcenter/test-cloud)測試直接在數百個實體裝置上的應用程式，以解決此特定問題。 開發人員撰寫自動化接受度測試，以允許進行功能強大的 UI 測試。 這些測試上傳到 App Center 之後, 在 CI 伺服器可以執行它們自動 CI 程序的一部分如下圖所示：

[![](intro-to-ci-images/intro02-small.png "這些測試上傳到 App Center 之後, 在 CI 伺服器可以自動執行 CI 程序的一部分，在此圖中所示")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>持續整合的元件

沒有專門設計來支援 CI 的商業和開放原始碼工具的豐富生態系統。 本章節將說明一些最常見的。

### <a name="version-control"></a>版本控制

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/)並[Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) 是 Microsoft 的共同作業工具進行持續整合組建服務、 追蹤工作、 agile 規劃和報告工具和版本控制。 使用版本控制，Azure DevOps 和 TFS 能與它自己的系統 （Team Foundation 版本控制或 TFVC） 或裝載於 GitHub 上的專案。

- Azure DevOps 提供透過雲端服務。 其主要優點是它不需要專用的硬體或基礎結構，並可從任何地方透過網頁瀏覽器和熱門的開發工具如 Visual Studio 中，因此很適合在地理區域分散的小組. 它是免費的五個開發人員的小組或更少之後可以購買哪些額外的授權，以容納團隊規模增大。
- TFS 是專為在內部部署 Windows 伺服器，並透過區域網路或 VPN 連線到該網路存取。 您完整控制組建伺服器的組態和可以安裝任何其他軟體或服務所需其主要優點。 TFS 有免費的入門級 Express 版為小型團隊。

TFS 和 Azure DevOps 與 Visual Studio 緊密整合，並讓開發人員執行許多版本控制和 CI 工作內的單一 IDE。 也可以使用 Team Explorer Everywhere 適用於 Eclipse 外掛程式 （如下所示）。 Visual studio for Mac[可以使用 TFVC 的預覽](/visualstudio/mac/tf-version-control/)。

[Azure 的 DevOps 管線](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)能直接支援 Xamarin 專案中，您可以在其中建立組建定義的每個平台 （Android、 iOS 和 Windows） 的目標。 適當的 Xamarin 授權所需的每個組建定義。 您也可連線本機、 Xamarin 支援的 TFS 組建伺服器，以 Azure DevOps 針對此目的。 透過此設定，Azure devops 已排入佇列的組建會委派給本機伺服器。 如需詳細資訊，請參閱[建置和發行代理程式](https://docs.microsoft.com/azure/devops/pipelines/agents/agents)。 或者，您可以使用其他建置工具，例如 Jenkins 或小組縣 （市）。

Visual Studio、 Azure DevOps 和 Team Foundation Server，請參閱所有的應用程式生命週期管理 (ALM) 功能的完整摘要[與 Xamarin 應用程式的 DevOps](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps)。

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)帶入 Visual Studio 之外進行開發的小組的 Team Foundation Server 和 Azure DevOps 的能力。 它可讓開發人員從 Eclipse 或跨平台命令列用戶端連線到內部部署或雲端中的 team 專案適用於 OS X 和 Linux。 提供完整的 Team Explorer Everywhere 版本控制 （包括 Git），以存取工作項目，，和建置適用於非 Windows 平台的功能。

#### <a name="git"></a>Git

[Git](http://git-scm.com)是熱門的開放原始碼版本控制的解決方案，來管理 Linux 核心的原始程式碼的原始開發。 它是非常快速且彈性的系統常用的各種規模的軟體專案。 它輕鬆地調整不佳的網際網路存取的單一開發人員從大型橫跨全球各地的小組。 Git 也非常可以輕易建立分支，其中又可以鼓勵開發風險降到最低的平行資料流。

Git 能完全是透過網頁瀏覽器，或[GUI 用戶端](http://git-scm.com/downloads/guis)Linux、 Mac OSX 和 Windows 上執行。 它是免費的公用存放庫;私用存放庫需要[付費方案](https://github.com/pricing)。

最新版的 Visual Studio for Windows 和 Mac 提供適用於 Git 的原生支援。 Microsoft 提供[可下載的延伸模組適用於 Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c)針對較舊版的 Visual Studio。 如先前所述，Azure DevOps 和 TFS 可以使用 Git 進行版本控制，而非 TFVC。

#### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) 是熱門的開放原始碼版本控制系統已自 2000年的使用中。 SVN 會所有現代化版本的 OS X、 Windows、 FreeBSD、 Linux 和 Unix 上執行。 Visual Studio for Mac 具有 SVN 的原生支援。 有帶入 Visual Studio 中的 SVN 支援的協力廠商延伸模組。

### <a name="continuous-integration-environments"></a>持續整合環境

設定連續整合環境表示結合組建服務的版本控制系統。  對於後者，兩個最常見的是：

- [Azure 的管線](https://docs.microsoft.com/azure/devops/pipelines/)是 Azure DevOps 和 TFS 的建置系統。 緊密整合以 Visual Studio 中，觸發程序的開發人員方便一邊建置，它能自動執行測試，並查看結果。
- Jenkins 是外掛程式的豐富的生態系統，以支援各種軟體開發的開放原始碼 CI 伺服器。 它會針對 Windows，Mac OS X Jenkins 未與任何特定的 IDE 整合。 相反地，它是設定，並透過 web 介面管理。 Jenkins CI 也很容易安裝及設定讓小型團隊，極具吸引力的。

您可以使用 TFS/Azure DevOps 本身，或您可以使用 Jenkins 搭配 TFS/Azure DevOps 或 Git，如下列各節中所述。

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

如所述，Azure DevOps 和 Team Foundation Server 提供兩個版本控制和建置服務。 組建服務一律會需要為每個目標平台的 Xamarin 商務或企業授權。

Azure devops，您會建立每個目標平台的個別組建定義，並輸入那里適當的授權。 一旦設定，Azure DevOps 會執行建置，並在雲端中測試。 請參閱[Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/)如需詳細資訊。

與 Team Foundation Server，您可以設定組建電腦，如下所示為特定的目標平台：

- **Android 和 Windows:** 安裝 Visual Studio 和 Xamarin 工具 （適用於 Android 和 Windows 這兩個），並設定您的 Xamarin 授權。 還有，移至其中的 TFS 組建代理程式的共用位置的伺服器上的 Android SDK 可以找到它所需。 如需詳細資訊，請參閱 <<c0> [ 設定 TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)。
- **iOS 和 Xamarin:** 安裝 Visual Studio 和 Xamarin 工具在 Windows 伺服器上使用適當的授權。 然後安裝 Visual Studio for Mac 在網路可存取的 Mac OS X 電腦，它會做為組建主機，建立最終的應用程式套件 (IPA 適用於 iOS、 OS x 的應用程式)。

下圖說明此拓撲：

[![](intro-to-ci-images/intro03-small.png "此圖說明此拓撲")](intro-to-ci-images/intro03.png#lightbox)

它也可在本機的 TFS 伺服器連結至 Azure DevOps 專案，以便 Azure DevOps 的組建會委派到本機伺服器。 如需詳細資訊，請參閱 <<c0> [ 建置和發行代理程式](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/)。

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps 與 Jenkins

如果您使用 Jenkins 來建置您的應用程式時，您可以將您的程式碼儲存在 Azure DevOps 或 Team Foundation Server，並繼續使用 Jenkins CI 組建的。 當您將程式碼推送至 team 專案的 Git 存放庫，或是當您程式碼簽入 TFVC，您可以觸發 Jenkins 組建。 如需詳細資訊，請參閱 < [Jenkins 與 Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins)。

[![](intro-to-ci-images/intro04-small.png "如果您使用 Jenkins 來建置您的應用程式時，您可以將您的程式碼儲存在 Azure DevOps 或 Team Foundation Server，並繼續使用 Jenkins CI 組建")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git 和 Jenkins

另一個常見的 CI 環境可以完全是 OS X 的基礎。 此案例牽涉到使用 Git 的原始程式碼控制和 Jenkins 組建伺服器。 這兩種電腦上執行單一 Mac OS X 使用 Visual Studio for Mac 安裝。 這是非常類似於 Azure DevOps + Jenkins 環境上一節所述：

[![](intro-to-ci-images/intro05-small.png "這是非常類似於 Azure DevOps + 上一節中所討論的 Jenkins 環境")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins 已[Microsoft 不支援](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)。**
