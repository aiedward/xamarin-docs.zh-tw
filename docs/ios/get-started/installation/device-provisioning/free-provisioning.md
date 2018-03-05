---
title: "免費佈建"
description: "隨著 Apple 的 Xcode 7 發行，有一項針對所有 iOS 和 Mac 開發人員的重要變更 – 免費佈建。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: baf1ae7d4cc533af0db482e8d7c31fc3c8b4edbf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="free-provisioning"></a>免費佈建

_隨著 Apple 的 Xcode 7 發行，有一個針對所有 iOS 和 Mac 開發人員的重要變更 – 免費佈建。_

免費佈建可讓開發人員**無須**加入任何 **Apple Developer Program** \(Apple 開發人員計劃\)，即可將其 Xamarin.iOS 應用程式部署到 iOS 裝置。 這對開發人員來說極為有利，因為在裝置上進行測試比在模擬器上進行測試多出許多優點，包括但不限於記憶體、儲存體、網路連線能力等。

若要在沒有 Apple 開發人員帳戶的情況下進行佈建，必須透過 Xcode，這會建立一個「簽署身分識別」(包含開發人員憑證和私密金鑰) 和一個「佈建設定檔」 (包含明確的應用程式識別碼和所連線 iOS 裝置的 UUID)。

## <a name="requirements"></a>需求

若要利用免費佈建將 Xamarin.iOS 應用程式部署至裝置，您必須使用 Xcode 7 或更新的版本。

**所使用的 Apple ID 必須未與任何 Apple Developer Program (Apple 開發人員計劃) 連接。**

您應用程式中使用的「套件組合識別碼」必須是唯一的，且不可先前在另一個應用程式中使用過。 任何與免費佈建搭配使用的「套件組合識別碼」都「無法」再重複使用。 如果您已經散發某個應用程式，就不能使用免費佈建來佈建該應用程式。 

如需詳細資訊，請參閱[應用程式散發指南](~/ios/deploy-test/app-distribution/index.md)。

如果您的應用程式使用「應用程式服務」，您將需要建立佈建設定檔，如[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md#appservices)所述。 如需了解進一步的限制，請參閱下面的[相關小節](#limitations)。


## <a name="a-namelaunching--launching-your-app"></a><a name="launching" />啟動您的應用程式

為了使用免費佈建將應用程式佈建至裝置，您將使用 Xcode 來建立簽署身分識別和佈建設定檔，然後使用 Visual Studio for Mac 或 Visual Studio 來選擇正確的設定檔以簽署應用程式。 請依照下面的逐步解說來進行這項操作：

1. 如果您沒有 Apple ID，請在 [appleid.apple.com](https://appleid.apple.com/account)建立一個。
2. 開啟 Xcode，然後瀏覽至 [Xcode] > [Preferences] \(偏好設定\)。
3. 在 [Accounts] \(帳戶\) 底下，使用 [+] 按鈕來新增您現有的 Apple ID。 看起來應該類似下面的螢幕擷取畫面：

  [ ![](free-provisioning-images/launchapp1.png "Xcode > Preferences (偏好設定) > Accounts (帳戶)")](free-provisioning-images/launchapp1.png)

4. 插入要作為部署目的地的 iOS 裝置，然後在 Xcode 中建立新的空白單一檢視 iOS 專案。 將 [Team] \(小組\) 下拉式清單設定為您剛新增的 Apple ID。 其格式應該類似 `your name (Personal Team - your Apple ID)`：

  [ ![](free-provisioning-images/launchapp2.png "建立簽署身分識別")](free-provisioning-images/launchapp2.png)

5. 在 [General] \(一般\) > [Identity] \(身分識別\) 區段底下，確定 [Bundle Identifier] \(套件組合識別碼\) 與 Xamarin.iOS 的套件組合識別碼「完全」相符，並確保部署目標等於或低於所連接的 iOS 裝置。 此步驟極為重要，因為 Xcode 將只會建立具有明確 App ID (應用程式識別碼) 的佈建設定檔：

  [![](free-provisioning-images/launchapp5.png "建立具有明確 App ID (應用程式識別碼) 的佈建設定檔")](free-provisioning-images/launchapp5.png)

6. 在 [Signing] \(簽署\) 區段中，選取 [Automatically Manage Signing] \(自動管理簽署\)，然後從下拉式清單中選取您的小組：

  [![](free-provisioning-images/launchapp6.png "選取 [Automatically Manage Signing] \(自動管理簽署\)，然後從下拉式清單中選取您的小組")](free-provisioning-images/launchapp6.png)

7. 上一個步驟將會自動為您產生佈建設定檔和簽署身分識別。 您可以按一下佈建設定檔旁邊的資訊圖示來檢視此資訊：

  [![](free-provisioning-images/launchapp7.png "檢視佈建設定檔")](free-provisioning-images/launchapp7.png)

8. 若要在 Xcode 中進行測試，請按一下 [Run] \(執行\) 按鈕，以將空白應用程式部署至您的裝置。

9. 返回您的 IDE，仍插著相同的裝置，然後在 Xamarin.iOS 專案名稱上按一下滑鼠右鍵來開啟 [Project Options] \(專案選項\) 對話方塊。 瀏覽至 [iOS Bundle Signing] \(iOS 套件組合簽署\) 區段，然後明確設定您的簽署身分識別和佈建設定檔：

  [![](free-provisioning-images/launchapp8.png "設定簽署身分識別和佈建設定檔")](free-provisioning-images/launchapp8.png)

如果在 IDE 中看不到您的簽署身分識別或正確的部署設定檔，則您可能需要重新啟動 IDE。


## <a name="a-namelimitations-limitations"></a><a name="limitations" />限制

Apple 針對使用免費佈建在 iOS 裝置上執行應用程式的時機和方式有一些限制，用以確保您只能部署至「自己的」裝置。 本節中會列出這些限制。

對 iTunes Connect 的存取也有限制，因此免費佈建應用程式的開發人員並無法使用發行至 App Store 和 TestFlight 等服務。 必須具備「Apple 開發人員帳戶」(企業或個人)，才能透過 Ad Hoc (臨機操作) 和 In-House (內部) 方式散發應用程式。

以此方式建立的「佈建設定檔」會在一週後到期，「簽署身分識別」則會在一年後到期。 此外，只會建立具有明確 App ID (應用程式識別碼) 的佈建設定檔，因此您將需要針對所要安裝的每個應用程式，依照[上述](#launching)指示進行操作。

使用免費佈建時，也無法執行大多數應用程式服務的佈建。 包括：

- Apple Pay
- Game Center
- iCloud
- App 內購買
- 推播通知
- 電子錢包 (舊稱為 Passbook)

Apple 在其[支援的功能](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/SupportedCapabilities/SupportedCapabilities.html#//apple_ref/doc/uid/TP40012582-CH38-SW1) \(英文\) 指南中提供了完整的清單。 若要佈建您的應用程式以與應用程式服務搭配使用，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。


## <a name="summary"></a>總結

本指南探索了使用免費佈建在 iOS 裝置上安裝應用程式的優點和限制。 此外，也提供了使用免費佈建來安裝 Xamarin.iOS 應用程式的逐步解說。

## <a name="related-links"></a>相關連結

- [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)
- [佈建應用程式服務](~/ios/get-started/installation/device-provisioning/index.md#appservices)
