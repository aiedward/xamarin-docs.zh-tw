---
title: 為什麼我的 iOS 組建無法使用： keychain 中找到的任何有效的 iPhone 程式碼簽署金鑰？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fe267db1f83695b3d0e8f3d828f91e01b56ba8ee
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115428"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>為什麼我的 iOS 組建無法使用： keychain 中找到的任何有效的 iPhone 程式碼簽署金鑰？

## <a name="cause-of-the-error"></a>錯誤的原因
此錯誤訊息時發生問題專案需要有效的程式碼簽署憑證，但找不到它們。 程式碼簽署，才能測試和實體 iOS 裝置上的部署以及臨機操作及應用程式儲存組建。 


### <a name="provisioning-devices"></a>裝置佈建
如果您尚未佈建 iOS 裝置之前，下列指南將逐步帶領您完整的逐步程序：[裝置佈建指南](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>使用 iOS 模擬器時的 bug

> [!NOTE]
> 適用於 Visual Studio，已在最新版本的 Xamarin 中解決此問題。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。


而造成的 iOS 專案的 Xamarin.Forms 範本，以新增 codesign Entitlements.plist 模擬器來建置; Xamarin.Visual Studio 3.11 版時發生錯誤有效地封鎖使用模擬器進行測試。

### <a name="how-to-fix"></a>如何修正問題
您可以解決此問題，藉由移除`<CodesignEntitlements>`.csproj 檔案中的旗標，從 偵錯組建。 您可以依照下列方式來執行這項操作：

*警告：.csproj 檔案中的錯誤可能會中斷您的專案，因此最好備份您的檔案，然後再嘗試這個。*

1. 以滑鼠右鍵按一下 [解決方案] 窗格中的 iOS 專案，然後選取**卸載專案**
2. 以滑鼠右鍵按一下專案，然後選取**編輯 [ProjectName].csproj**
3. 找出之偵錯 PropertyGroups，他們應該開始看起來像這樣的旗標：
   - 偵錯： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 版本： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. 在每個使用模擬器的組建中，刪除或註解化下列屬性： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. 重新載入專案，而且您應該能夠部署至模擬器。

### <a name="next-steps"></a>後續步驟
如需進一步協助，請連絡我們，或如果此問題持續，即使利用上述的資訊，請參閱[適用於 Xamarin 有哪些支援選項？](~/cross-platform/troubleshooting/support-options.md)如需連絡選項，建議，以及如何如有需要請提出新錯誤報告。 
