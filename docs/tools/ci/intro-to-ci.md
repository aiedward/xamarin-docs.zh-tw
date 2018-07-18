---
title: 使用 Xamarin 的連續整合簡介
description: 本文件說明使用 Xamarin 的連續整合。 它說明版本控制和各種持續整合環境。
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: topgenorth
ms.author: toopge
ms.date: 07/19/2017
ms.openlocfilehash: 67fc32fc9f79d54274642fbab2d0c2f8afd14d8c
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066503"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>使用 Xamarin 的連續整合簡介

_持續整合是軟體工程作法自動化的組建會編譯並新增或變更專案的版本控制儲存機制中的開發人員程式碼時，您可選擇測試應用程式。本文將討論連續整合的一般概念，以及一些可用的選項為持續整合與 Xamarin 專案。_

通常會在平行工作的開發人員的軟體專案。 在某個時間點，就必須將這些整合成一個工作的平行資料流將會在最終產品組成程式碼基底。 在軟體開發的早期，這項整合已執行的專案，會發生困難而且有風險的程序的結尾。

連續整合 (CI) 來避免這種複雜性每個開發人員變更合併到持續為基礎的通用程式碼基底，通常只要任何開發人員簽入變更專案的共用程式碼儲存機制。 每次簽入觸發程序自動化的建置並執行自動化的測試以確認新引入的程式碼不會中斷任何現有的程式碼。  如此一來，CI 立即呈現錯誤和問題，並可確保所有小組成員都會維持最新狀態，與每個其他工作。 這會導致凝聚力與穩定程式碼基底。

持續整合系統有兩個主要部分：

-  **版本控制**– 版本控制 (VC)，也稱為原始檔控制或原始程式碼管理，將所有專案的程式碼合併到單一的共用儲存機制，並將每一項變更的完整歷程記錄保留每個檔案。 這個儲存機制，通常稱為*主線*或*主要*分支，就會包含最後會用來建立生產環境或發行版本的應用程式的原始程式碼。 有許多開放原始碼和商業產品這項工作，這通常可讓團隊或個人分岔成次要的分支，它們可以在其中大量變更或進行實驗而不必擔心到主要分支程式碼的複本。 第二個分支中的變更會經過驗證後，他們接著可以一起合併至 「 主要 」 分支。
-  **連續整合伺服器**– 連續整合伺服器負責收集的所有專案的成品 （原始碼、 影像、 視訊、 資料庫、 自動化的測試等等）、 編譯應用程式，以及執行自動化的測試。 同樣地，有許多開放原始碼和商業 CI 伺服器工具。

開發人員通常會需要在其工作站，一開始完成工作上的一個或多個分支的工作副本。 一組適合的工作完成後，變更 「 簽入 」 或者 「 認可 」 至適當的分公司中，將它們傳播到其他開發人員的工作副本。 這是如何小組時，可確保所有使用相同的程式碼上。

同樣地，使用持續整合，認可變更的動作會導致建置專案，並執行自動化的測試，確認原始碼正確性 CI 伺服器。 如果有建置錯誤，或測試失敗，CI 伺服器通知負責的開發人員 （透過電子郵件，IM、 Twitter、 Growl，等等） 讓他或她可以更正問題。 （CI 伺服器可以甚至拒絕認可如果發生失敗，稱為 「 閘道簽入 」。）

下圖說明此程序：

[![](intro-to-ci-images/intro01-small.png "此圖說明此程序")](intro-to-ci-images/intro01.png#lightbox)

行動裝置應用程式會導入連續整合唯一的挑戰。 應用程式可能需要如 GPS 或相機，才可以使用實體裝置上的感應器。 此外，模擬器只可約略估算的硬體且可能會隱藏或遮蔽的問題。 在結束時，就需要測試確信是真正可供客戶使用實際的硬體上的行動裝置應用程式。

[應用程式中心測試](https://docs.microsoft.com/appcenter/test-cloud)解決此特定問題的方式來測試應用程式，直接在數百個實體裝置上的。 開發人員撰寫自動化的 acceptances 測試，可讓功能強大的 UI 測試。 一旦這些測試上傳至應用程式中心，CI 伺服器可以自動執行 CI 程序如下列圖表所示：

[![](intro-to-ci-images/intro02-small.png "一旦這些測試上傳至應用程式中心，CI 伺服器可以自動執行 CI 程序的一部分此圖表中所示")](intro-to-ci-images/intro02.png#lightbox)

# <a name="components-of-continuous-integration"></a>持續整合的元件

沒有專門設計來支援 CI 商業和開放原始碼工具的廣泛生態系統。 本章節將說明幾個最常見的。

## <a name="version-control"></a>版本控制

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services 和 Team Foundation Server

[Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/) (VSTS) 和[Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) 是 Microsoft 的共同作業工具，如持續整合建置服務、 追蹤工作、 敏捷式計劃和報告工具，以及版本控制項。 與版本控制 VSTS 和 TFS 可以處理它自己的系統 （Team Foundation 版本控制或 TFVC） 或裝載於 GitHub 上的專案。

 - Visual Studio Team Services 提供透過雲端服務。 它的主要優點是它不需要專用的硬體或基礎結構，並可從任何地方透過網頁瀏覽器和款知名開發工具，例如 Visual Studio 中，讓您吸引分佈各地的小組散發。 它是免費的五個開發人員的小組或更少之後可以購買哪些額外的授權來容納不斷增長的小組。
 - TFS 是設計在內部部署 Windows 伺服器，並透過區域網路或該網路的 VPN 連線存取。 其主要的優點是您完全控制的組建伺服器組態和可以安裝任何其他軟體或服務所需。 TFS 有免費的入門級 Express 版本對於小型小組。

TFS 和 VSTS 與 Visual Studio 緊密整合，可讓開發人員執行許多的版本控制和 CI 工作的單一 IDE 中。 也可以使用 Team Explorer Everywhere plugin for Eclipse （請參閱下文）。 Visual Studio for Mac 不提供任何支援 TFS 或 VSTS。

Visual Studio Team 服務的建置系統可直接支援 Xamarin 專案中，您可以在其中建立您想要目標 （Android、 iOS 和 Windows） 的每個平台的組建定義。 適當的 Xamarin 授權所需的每個組建定義。 您也可連線本機、 Xamarin 支援 TFS 組建伺服器，以針對此用途的 Visual Studio Team Services。 使用這個設定，組建已排入佇列 VSTS 委派到本機伺服器。 如需詳細資訊，請參閱[部署和設定組建伺服器](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts)。 或者，您可以使用其他建置工具，例如 Jenkins 或小組縣 （市）。

所有的應用程式生命週期管理 (ALM) 功能的 Visual Studio、 Visual Studio Team Services 和 Team Foundation Server，請參閱完整的摘要[與 Xamarin 應用程式的應用程式生命週期管理](https://msdn.microsoft.com/library/mt162217(v=vs.140).aspx)MSDN 上。


### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](http://msdn.microsoft.com/library/gg413285.aspx)將 Team Foundation Server 和 Visual Studio Team Services 功能帶給開發 Visual Studio 外部的小組。 它可讓開發人員從 Eclipse 或跨平台命令列用戶端連線到 team 專案，在內部部署或雲端中 OS X 和 Linux。 Team Explorer Everywhere 提供完整存取版本控制 （包括 Git），工作項目，並建立非 Windows 平台的功能。


### <a name="git"></a>Git

[Git](http://git-scm.com)是熱門的開放原始碼版本控制方案原本特別開發來管理 Linux 核心的原始程式碼。 它是一種非常快速的彈性系統常用的各種規模的軟體專案。 它輕鬆地縮放單一開發人員不佳的網際網路存取從大型團隊跨越地球。 Git 也非常可以輕易建立分支，其中又可以鼓勵開發風險降到最低的平行資料流。

Git 可完全透過網頁瀏覽器或進行操作[GUI 用戶端](http://git-scm.com/downloads/guis)Linux、 Mac OSX 及 Windows 上執行。 它是免費的公用儲存機制;私用儲存機制需要[付費計劃](https://github.com/pricing)。

Visual Studio 2015 和 Visual Studio for Mac 提供原生支援 Git。對於較舊版本中，Microsoft 提供[可下載副檔名 Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c)。 如先前所述，Visual Studio Team Services 與 TFS 可以使用 Git 進行版本控制，而不是 TFVC。


### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) 是熱門的開放原始版本控制系統已在使用自 2000年中。 SVN 會在所有現代化版本 OS X、 Windows、 FreeBSD、 Linux 及 Unix 上執行。 適用於 Mac 的 visual Studio 的 SVN 的原生支援。 有 SVN 支援帶入 Visual Studio 的第三方擴充功能。


## <a name="continuous-integration-environments"></a>持續整合環境

設定持續整合環境表示結合組建服務的版本控制系統。  對於後者，兩個最常見的是：

- [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview)是 Visual Studio Team Services 與 TFS 的建置系統。 它與觸發程序的開發人員方便它組建是會自動執行測試，並查看結果的 Visual Studio 緊密整合。
- Jenkins 是開放原始碼 CI 伺服器，使用豐富的生態系統的外掛程式，以支援軟體開發的所有類型。 在 Windows 上執行和 Mac OS X Jenkins 未與任何特定的 IDE 整合。 相反地，其設定和管理透過 web 介面。 Jenkins CI 十分容易安裝及設定讓小型小組，極具吸引力。

您可以使用 TFS/VSTS 單獨使用時，或者您可以使用 Jenkins 搭配 TFS/VSTS 或 Git 下列各節中所述。

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services 和 Team Foundation Server

Visual Studio Team Services 和 Team Foundation Server 所述，提供兩個版本控制和建置服務。 組建服務永遠會為每個目標平台需要 Xamarin 商務或企業授權。

使用 Visual Studio Team Services，您會建立每個目標平台的個別組建定義，並輸入那里適當的授權。 VSTS 設定之後，將會執行組建和測試在雲端中的。 請參閱[Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview)如需詳細資訊。

使用 Team Foundation Server，您可以設定組建電腦，如下所示的特定目標平台：

- **Android 和 Windows:** 安裝 Visual Studio 和 Xamarin 工具 （適用於 Android 和 Windows 這兩個），並使用您的 Xamarin 授權設定。 它也是必要移動到其中的 TFS 組建代理程式的共用位置在伺服器上的 Android SDK 可以找到它。 如需詳細資訊，請參閱[設定 TFVC](https://docs.microsoft.com/vsts/tfvc/overview)。
- **iOS 和 Xamarin:** 安裝 Visual Studio 和 Xamarin 工具在 Windows 伺服器上使用適當的授權。 Visual Studio for Mac 上安裝網路存取的 Mac OS X 電腦，這會做為組建主機，並建立最終應用程式封裝 (IPA 進行 iOS、 OS x 應用程式)。

下圖說明此拓撲：

[![](intro-to-ci-images/intro03-small.png "此圖說明此拓撲")](intro-to-ci-images/intro03.png#lightbox)

它也可將本機的 TFS 伺服器連結至 Visual Studio Team Services 專案，以便 VSTS 組建會委派給本機伺服器。 如需詳細資訊，請參閱[部署和設定組建伺服器](http://msdn.microsoft.com/library/ms181712.aspx)MSDN 上。

### <a name="visual-studio-team-services-and-jenkins"></a>Visual Studio Team Services 和 Jenkins

如果您使用 Jenkins 建置您的應用程式時，您可以在 Visual Studio Team Services 或 Team Foundation Server 中儲存您的程式碼，並繼續使用 Jenkins CI 組建。 您可以在程式碼推送至您的 team 專案的 Git 儲存機制或當您檢查程式碼 TFVC 時觸發 Jenkins 組建。 如需詳細資訊，請參閱[與 Visual Studio Team Services Jenkins](https://docs.microsoft.com/en-us/vsts/service-hooks/services/jenkins?view=vsts)。

[![](intro-to-ci-images/intro04-small.png "如果您使用 Jenkins 建置您的應用程式時，您可以在 Visual Studio Team Services 或 Team Foundation Server 中儲存您的程式碼，並繼續使用 Jenkins CI 組建")](intro-to-ci-images/intro04.png#lightbox)

### <a name="git-and-jenkins"></a>Git 和 Jenkins

另一個常見的 CI 環境可以完全是 OS X 基礎。 此案例牽涉到使用 Git 的原始程式碼控制和 Jenkins 組建伺服器。 這兩種電腦上執行單一 Mac OS X 與 Visual Studio 一起安裝的 mac。 這是非常類似於 Visual Studio Team Services + Jenkins 環境上一節所述：

[![](intro-to-ci-images/intro05-small.png "這是非常類似於 Visual Studio Team Services + 上一節中所討論的 Jenkins 環境")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **注意： Jenkins 是[不支援 Xamarin](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)。**


# <a name="summary"></a>總結

持續整合概念，以及它為軟體開發團隊帶來的優勢，導入了這份文件。 版本控制的重要性先前討論的內容以及角色和責任的組建伺服器。 文件然後發生，討論的一些工具，可以是使用原始程式碼控制的組建伺服器。 我們也引進應用程式中心測試，可協助開發人員發行強大的應用程式執行將證明自己的應用程式的功能與品質的自動化的測試。 在提交應用程式和測試應用程式中心，您可以找到的文件的詳細[這裡](https://docs.microsoft.com/appcenter/test-cloud)。 最後，協助您了解如何所有這些工具和元件互相配合，我們概述幾個不同的 CI 環境組織可能會建立連續整合。 Xamarin 專案中使用 Visual Studio Team Services 和 Team Foundation Server 的詳細資訊，請參閱[設定 TFVC](https://docs.microsoft.com/vsts/tfvc/overview)和這[連續整合簡介](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1)。 同樣地，如果您使用 Jenkins，請參閱[使用 Jenkins xamarin](~/tools/ci/jenkins-walkthrough.md)的連續整合所設定的詳細資訊。
