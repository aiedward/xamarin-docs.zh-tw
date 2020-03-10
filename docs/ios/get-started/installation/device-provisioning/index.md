---
title: Xamarin.iOS 的裝置佈建
description: 本文件描述如何佈建裝置，使其可用於測試應用程式。 它也會討論如何設定應用程式，以便使用推播通知等功能。
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: bb1ef1e948c796e6b0d91230be3c82c5a8c7d366
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910851"
---
# <a name="device-provisioning-for-xamarinios"></a>Xamarin.iOS 的裝置佈建

開發 Xamarin.iOS 應用程式時，除了模擬器之外，將應用程式部署至實體裝置來測試應用程式也是必要步驟。 由於硬體限制 (例如記憶體) 或網路連線能力的緣故，裝置專屬的 Bug 和效能問題可能在裝置上執行時顯露出來。 若要在實體裝置上進行測試，必須「佈建」裝置，且必須告知 Apple 將使用該裝置進行測試。

下圖中醒目提示的區段說明 iOS 佈建所需的設定步驟：

[![](images/provisioningdiagram.png "The highlighted sections in this image show the steps required to get set up for iOS provisioning")](images/provisioningdiagram.png#lightbox)

在這之後，下一步就是散發應用程式。 如需有關部署的詳細資訊，請參閱[應用程式散發](~/ios/deploy-test/app-distribution/index.md)指南。

在將應用程式部署至裝置之前，您必須具備 Apple Developer Program (Apple 開發人員計劃) 的作用中訂用帳戶，或是使用[免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)。 Apple 提供兩個計劃選項：

- **Apple Developer Program** \(Apple 開發人員計劃\) – 不論您是個人還是代表組織，Apple Developer Program (Apple 開發人員計劃) 都可讓您開發、測試及散發應用程式。
- **Apple Developer Enterprise Program** \(Apple 開發人員企業計劃\)– 企業計劃最適合只想要在內部開發及散發應用程式的組織。 企業計劃的成員無法存取 iTunes Connect，也無法將所建立的應用程式發行至 App Store。

若要註冊上述任一計劃，請瀏覽 [Apple Developer Portal](https://developer.apple.com/programs/enroll/) \(Apple 開發人員入口網站\) 來進行註冊。 請注意，若要註冊成為 Apple 開發人員，必須具有 [Apple ID](https://appleid.apple.com/)。 建立本指南時，即假設您**是** Apple Developer Program (Apple 開發人員計劃) 的成員。

或者，Apple 也在 Xcode 7 中導入了[免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)，可讓您「無須」成為 Apple Developer Program (Apple 開發人員計劃) 的成員，即可在單一裝置上執行單一應用程式。 以此方式佈建時有一些限制，如[這裡](~/ios/get-started/installation/device-provisioning/free-provisioning.md#limitations)所述。

任何在裝置上執行的應用程式都必須包含一組中繼資料 (或「指紋」)，當中包含有關應用程式和開發人員的資訊。 在使用者裝置上部署或執行應用程式時，Apple 會使用這個指紋來確認應用程式未經竄改。 做法是要求應用程式開發人員將其 Apple ID 註冊為開發人員，以及設定 Apple ID、要求憑證和註冊要部署應用程式的裝置。

將應用程式部署至裝置時，會一併在 iOS 裝置上安裝「佈建設定檔」。 「佈建設定檔」的存在目的是要確認在組建階段用來簽署應用程式的資訊，且此設定檔會由 Apple 以密碼編譯方式簽署。 「佈建設定檔」和「指紋」檢查會一起藉由檢查下列項目，來決定是否可以將應用程式部署至裝置：

- **身分** (憑證 – 是否已使用在佈建設定檔中具有對應之公開金鑰的私密金鑰來簽署應用程式？ 憑證也會將開發人員與開發小組建立關聯)
- **內容** (個人應用程式識別碼 – Info.plist 中設定的「套件組合識別碼」是否與佈建設定檔中的「應用程式識別碼」相符？)
- **位置** (裝置 – 佈建設定檔中是否包含裝置？)

這些步驟可確保在開發過程中所建立或使用的所有項目 (包括應用程式和裝置) 都可回溯至 Apple 開發人員帳戶。

## <a name="provisioning-your-device"></a>佈建您的裝置

佈建 iOS 裝置的方式有兩種：

- **自動 (建議使用)** – 在您的專案中選取 [自動化佈建] 配置，讓 Visual Studio 自動建立和管理您的簽署身分識別、應用程式識別碼及佈建設定檔。 如需有關如何自動管理佈建的資訊，請參閱[自動化佈建](automatic-provisioning.md)指南。 這是建議的 iOS 裝置佈建方式。

- **手動** – 可以透過 Apple Developer Portal (Apple 開發人員入口網站) 來建立和管理「簽署身分識別」、「應用程式識別碼」及「佈建設定檔」，如[手動佈建](manual-provisioning.md)指南所述。 您可以依照 [Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)指南所述，管理這些成品。

## <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供一組可針對 Xamarin.iOS 應用程式啟用的精選特別「應用程式服務」(也稱為功能)。 不論是在 iOS Provisioning Portal (iOS 佈建入口網站) 中於建立 **App ID** \(應用程式識別碼\) 時，還是在 Xamarin.iOS 應用程式專案所含的 **Entitlements.plist** 檔案中，都必須設定這些「應用程式服務」。 如需有關將「應用程式服務」新增至您應用程式的資訊，請參閱[功能簡介](~/ios/deploy-test/provisioning/capabilities/index.md)指南和[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 建立具有所需應用程式服務的 App ID (應用程式識別碼)。
- 建立包含此 App ID (應用程式識別碼) 的新[佈建設定檔](#provisioning-your-device)。
- 在 Xamarin.iOS 專案中設定權利

## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [應用程式散發](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) \(英文\)
