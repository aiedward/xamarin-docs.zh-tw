---
title: Xamarin.iOS 應用程式的免費佈建
description: 本文件描述 Xamarin.iOS 開發人員如何在實體裝置上測試其應用程式，而不必註冊 Apple 的付費開發人員計畫。
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 07/16/2018
ms.openlocfilehash: 533ab09a73116402e39da65933f26b8f3065357c
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855064"
---
# <a name="free-provisioning-for-xamarinios-apps"></a>Xamarin.iOS 應用程式的免費佈建

免費佈建可讓 Xamarin.iOS 開發人員**無須**加入任何 **Apple 開發人員計劃**，即可將其應用程式部署到 iOS 裝置，並加以測試。
雖然模擬器測試很有用且很方便，但是仍然需要在實體 iOS 裝置上測試應用程式，才能驗證應用程式在實際記憶體、儲存體和網路連線能力的限制下是否能正確地運作。

若要使用免費佈建來將應用程式部署至裝置上：

- 使用 Xcode 來建立必要*簽署身分識別* (開發人員憑證和私密金鑰) 和*佈建設定檔* (包含明確的應用程式識別碼和已連接之 iOS 裝置的 UDID)。
- 在 Visual Studio for Mac 或 Visual Studio 2019 中，使用 Xcode 所建立簽署身分識別和佈建設定檔來部署您的 Xamarin.iOS 應用程式。

> [!IMPORTANT]
> [自動佈建](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)可讓 Visual Studio for Mac 或 Visual Studio 2019 自動設定裝置以進行開發人員測試。 不過，自動佈建與免費佈建不相容。 若要使用自動佈建，您必須擁有付費的 Apple 開發人員計劃帳戶。

## <a name="requirements"></a>需求

若要使用免費佈建來將 Xamarin.iOS 應用程式部署到裝置：

- 所使用的 Apple ID 必須未與任何 Apple 開發人員計劃連接。
- 您的 Xamarin.iOS 應用程式必須使用明確的應用程式識別碼，而不是萬用應用程式識別碼。
- 您 Xamarin.iOS 應用程式中使用的套件組合識別碼必須是唯一的，且先前不曾在其他應用程式中使用。 任何與免費佈建搭配使用的套件組合識別碼都**無法**再重複使用。
- 如果您已經散發某個應用程式，就不能使用免費佈建來部署該應用程式。
- 如果您的應用程式使用「應用程式服務」，則將需要建立佈建設定檔，如[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)所述。 

如需與免費佈建相關限制的詳細資訊，請查看本文件的[限制](#limitations)一節，如需散發 iOS 應用程式的詳細資訊，請參閱[應用程式散發指南](~/ios/deploy-test/app-distribution/index.md)。

## <a name="testing-on-device-with-free-provisioning"></a>使用免費佈建在裝置上進行測試

請遵循下列步驟使用免費佈建測試您的 Xamarin.iOS 應用程式。

### <a name="use-xcode-to-create-a-signing-identity-and-provisioning-profile"></a>使用 Xcode 來建立簽署身分識別和佈建設定檔

1. 如果您沒有 Apple ID，請[建立一個](https://appleid.apple.com)。
2. 開啟 Xcode，然後瀏覽至 [Xcode] > [偏好設定]。
3. 在 [Accounts] \(帳戶\) 底下，使用 [+] 按鈕來新增您現有的 Apple ID。 看起來應該類似下面的螢幕擷取畫面：

    ![Xcode 喜好設定 – 帳戶](free-provisioning-images/launchapp1.png "Xcode 喜好設定 – 帳戶")

4. 關閉 Xcode 喜好設定。
5. 插入您想要部署應用程式的 iOS 裝置。
6. 在 Xcode 中，建立新的專案。 選擇 [檔案] > [新增] > [專案]，然後選取 [單一檢視應用程式]。
7. 在 [新增專案] 對話方塊中，將 [小組] 設定為剛剛新增的 Apple ID。 在下拉式清單中，它看起來應該類似於 [您的名稱 (個人小組)]：

    ![建立新的應用程式](free-provisioning-images/launchapp2.png "建立新的應用程式")

8. 建立新的專案之後，選擇以 iOS 裝置 (而不是模擬器) 為目標的 Xcode 組建配置。

    ![選取 Xcode 組建配置](free-provisioning-images/xcodescheme.png "選取 Xcode 組建配置")

9. 在 Xcode 的 [專案導覽器] 中選擇其最上層的節點，以開啟您的應用程式專案設定。
10. 在 [一般] > [身分識別] 底下，請確定**套件組合識別碼**_完全符合_ Xamarin.iOS 應用程式的套件組合識別碼。

    ![設定套件組合識別碼](free-provisioning-images/launchapp5.png "設定套件組合識別碼")

    > [!IMPORTANT]
    > Xcode 僅會為明確的應用程式識別碼來建立佈建設定檔，並且必須與您的 Xamarin.iOS 應用程式的應用程式識別碼相同。
    > 如果兩者不同，您將無法使用免費佈建來部署您的 Xamarin.iOS 應用程式。

11. 在 [部署資訊] 下，確認部署目標與已連接的 iOS 裝置上安裝的 iOS 版本相符或低於該版本。
12. 在 [簽署] 下，選取 [自動管理簽署]，然後從下拉式清單中選取您的小組：

    ![自動管理簽署](free-provisioning-images/launchapp6.png "自動管理簽署")

    Xcode 將會自動為您產生佈建設定檔和簽署身分識別。 您可以按一下佈建設定檔旁邊的資訊圖示來檢視此資訊：

    ![檢視佈建設定檔](free-provisioning-images/launchapp7.png "檢視佈建設定檔")

    > [!TIP]
    > 如果 Xcode 嘗試產生佈建設定檔時出現錯誤，請確認 Xcode 目前選取的組建配置以已連接的 iOS 裝置為目標，而不是模擬器。

13. 若要在 Xcode 中進行測試，請按一下 [Run] \(執行\) 按鈕，以將空白應用程式部署至您的裝置。

### <a name="deploy-your-xamarinios-app"></a>部署您的 Xamarin.iOS 應用程式

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 透過 USB 或[無線方式](~/ios/deploy-test/wireless-deployment.md)將 iOS 裝置連接到 Mac 組建主機。
2. 在 Visual Studio for Mac [Solution Pad] 中，按兩下**Info.plist**。
3. 在 [簽署] 中，選取 [手動佈建]。
4. 按一下 [iOS 套件組合簽署] 按鈕。
5. 針對 [組態]，選取 [除錯]。
6. 針對 [平台]，選取 [iPhone]。
7. 選取 Xcode 所建立的**簽署身分識別**。
8. 選取 Xcode 所建立的**佈建設定檔**。

    ![設定簽署身分識別和佈建設定檔](free-provisioning-images/launchapp8.png "設定簽署身分識別和佈建設定檔")

    > [!TIP]
    > 如果看不到您的簽署身分識別或正確的部署設定檔，則可能需要重新啟動 Visual Studio for Mac。

9. 按一下 [確定] 以儲存並關閉 [專案選項]。
10. 選取您的 iOS 裝置，並執行應用程式。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 請確認 Visual Studio 2019 或 Visual Studio 2017 已[與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 透過 USB 或[無線方式](~/ios/deploy-test/wireless-deployment.md)將 iOS 裝置連接到 Mac 組建主機。
3. 在 Visual Studio 2019 或 Visual Studio 2017 的 [方案總管] 中，以滑鼠右鍵按一下 Xamarin.iOS 專案，然後選取 [屬性]。
4. 巡覽至 [iOS 套件組合簽署]。
5. 針對 [組態]，選取 [除錯]。
6. 針對 [平台]，選取 [iPhone]。
7. 選取 [手動佈建]。
8. 選取 Xcode 所建立的**簽署身分識別**。
9. 選取 Xcode 所建立的**佈建設定檔**。
    
    ![設定簽署身分識別和佈建設定檔](free-provisioning-images/setprofile-w157.png "設定簽署身分識別和佈建設定檔")

    > [!TIP]
    > Xcode 會建立此簽署身分識別和佈建設定檔，並將其儲存在 Mac 組建主機上。 Visual Studio 2019 或 Visual Studio 2017 可存取兩者，因為已[配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)到 Mac 組建主機。 如果未列出，則您可能需要重新啟動 Visual Studio 2019 或 Visual Studio 2017。

10. 儲存並關閉專案屬性。
11. 選取您的 iOS 裝置，並執行應用程式。

-----

## <a name="limitations"></a>限制

Apple 針對使用免費佈建在 iOS 裝置上執行應用程式的時機和方式有一些限制，用以確保您只能部署至「自己的」裝置：

- 對 iTunes Connect 的存取有限制，因此免費佈建應用程式的開發人員並無法使用發行至 App Store 和 TestFlight 等服務。 必須具備「Apple 開發人員帳戶」(企業或個人)，才能透過 Ad Hoc (臨機操作) 和 In-House (內部) 方式散發應用程式。
- 使用免費佈建建立的「佈建設定檔」會在一週後到期，而「簽署身分識別」則會在一年後到期。 
- 因為 Xcode 只會針對明確 App ID (應用程式識別碼) 建立佈建設定檔，您將需要針對所要安裝的每個應用程式，依照[上述指示](#testing-on-device-with-free-provisioning)進行操作。
- 使用免費佈建時，無法執行大多數應用程式服務的佈建。 這包括 Apple Pay、Game Center、iCloud、App 內購買、推播通知和電子錢包。 Apple 在[支援的功能 (iOS)](https://help.apple.com/developer-account/#/dev21218dfd6) 指南中提供了完整的功能清單。 若要佈建您的應用程式以與應用程式服務搭配使用，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

## <a name="summary"></a>總結

本指南探索了使用免費佈建在 iOS 裝置上安裝應用程式的優點和限制。 它提供了逐步解說，以示範如何使用免費佈建來安裝 Xamarin.iOS 應用程式。

## <a name="related-links"></a>相關連結

- [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)
- [應用程式服務的佈建](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)
