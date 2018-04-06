---
title: 無線部署
description: 此功能可讓您透過網路連線來部署至 iOS 或 Apple TV 裝置
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/09/2018
ms.openlocfilehash: b331ea61915b4f202aa971658a5a54d1a8038d64
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="wireless-deployment"></a>無線部署

開發人員工作流程中有一項重要工作是部署至裝置。 Xcode 9 引進了透過網路部署至 iOS 裝置或 Apple TV 的選項，使您不用在每次想要針對應用程式進行部署及偵錯時，都必須以實體纜線連接您的裝置。 此功能已於 Visual Studio for Mac 7.4 和 Visual Studio 15.6 版本中導入。

本指南會詳細說明如何配對，然後透過網路部署至裝置。

## <a name="requirements"></a>需求

Visual Studio for Mac 和 Visual Studio 皆提供無線部署功能。

您必須擁有下列項目才能使用無線部署：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

- macOS 10.12.4
- 最新版本的 Visual Studio for Mac
- Xcode 9.0 或更新版本
- 具有 iOS 11.0 或 tvOS 11.0 和更新版本的裝置

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- 最新版本的 Visual Studio
- 具有 iOS 11.0 或 tvOS 11.0 和更新版本的裝置

Mac 組建主機上應該安裝下列元件：

- macOS 10.12.4
- Visual Studio for Mac
- Xcode 9.0 或更新版本

-----

## <a name="connecting-a-device"></a>連接裝置

若要在裝置上以無線方式部署和偵錯，您必須讓 iOS 裝置或 Apple TV 與 Mac 上的 Xcode 配對。 在配對後，您就可以從 Visual Studio 中的裝置目標清單中選取它。 

下列配對程序應該只需在每個裝置上進行一次。 Xcode 會保留連線設定。

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>讓 iOS 裝置與 Xcode 配對

1. 開啟 Xcode，然後移至 [視窗] > [裝置和模擬器]。
2. 使用 Lightning 纜線將 iOS 裝置插入 Mac。 您可能需要在裝置上選取 [信任這台電腦]。
3. 選取您的裝置，然後選取 [透過網路連線] 核取方塊來為裝置配對：![顯示 [透過網路連線] 選項的 [裝置和模擬器] 視窗](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>讓 Apple TV 與 Xcode 配對

1. 確定 Mac 和 Apple TV 已連線至相同網路。

2. 開啟 Xcode，然後移至 [視窗] > [裝置和模擬器]。

3. 在 Apple TV 中，移至 [設定] > [遙控器和裝置] > [遠端應用程式和裝置]。

4. 在 Xcode 的 [找到的項目] 區域中選取 Apple TV，然後輸入 Apple TV 上顯示的驗證碼。

5. 按一下 [連線] 按鈕。 成功配對時，Apple TV 旁邊會出現網路連線圖示。

## <a name="deploy-to-a-device"></a>部署到裝置

裝置已無線連線並準備好用於部署時，就會出現在裝置目標清單中，彷彿裝置是透過 USB 所連線的。

若要在實體裝置上進行測試，則必須[佈建](~/ios/get-started/installation/device-provisioning/index.md)裝置。 請務必先執行這個動作，再嘗試部署至裝置。 

若要部署至 iOS 或 tvOS 裝置，請使用下列步驟：

1. 確定部署機器與目標裝置都位於相同的無線網路。 

2. 從目標裝置清單選取您的裝置，然後執行應用程式。

2. 如果裝置已鎖定，系統會提示您將裝置解除鎖定。 裝置解除鎖定後，應用程式就會部署至裝置。

完成無線部署後，系統會自動啟用無線偵錯，讓您可以使用先前設定的中斷點，繼續如往常一般地進行工作流程偵錯。

## <a name="troubleshooting"></a>疑難排解

1. 一律要確定 iOS 裝置或 Apple TV 已連線至和 Mac 相同的網路。

2. 如果 Visual Studio 中未顯示裝置，請檢查 Xcode 的 [裝置和模擬器] 視窗。 

    * 如果 Xcode **未**顯示裝置為已連線狀態，請試著再次[配對](#pair)裝置。

    * 如果 Xcode 顯示裝置為已連線狀態，請試著重新啟動 Visual Studio 和您的裝置。

3. 如果您還未這樣做，則必須[佈建](~/ios/get-started/installation/device-provisioning/index.md)您的裝置。

4. 如果上述步驟無法修正您有關於這項功能的問題，請在[開發人員社群](https://developercommunity.visualstudio.com/spaces/41/index.html)中提出問題。

## <a name="related-links"></a>相關連結

- [讓無線裝置與 Xcode 配對](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)
