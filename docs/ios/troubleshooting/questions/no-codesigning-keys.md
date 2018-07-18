---
title: 為什麼我的 iOS 組建失敗與： keychain 中找到的任何有效的 iPhone 程式碼簽署金鑰？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5334e3009906896644caa47c715f912fa379c627
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776686"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>為什麼我的 iOS 組建失敗與： keychain 中找到的任何有效的 iPhone 程式碼簽署金鑰？

## <a name="cause-of-the-error"></a>錯誤的原因
當程式碼簽署的有效認證尋找有問題的專案時，就會發生這個錯誤訊息但找不到它們。 程式碼簽署做是為了測試及部署實體 iOS 裝置。與臨機操作 （& s） 應用程式儲存組建。 


### <a name="provisioning-devices"></a>佈建裝置
如果您尚未佈建 iOS 裝置之前，下列指南會帶您完成完整的逐步程序：[裝置佈建指南](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>使用 iOS 模擬器時的 bug

> [!NOTE]
> 適用於 Visual Studio，已在最新版本的 Xamarin 解決此問題。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。


Xamarin.Visual Studio 3.11 導致 iOS 專案中加入 codesign Entitlements.plist 模擬器來建置; Xamarin.Forms 範本時發生錯誤有效地封鎖使用模擬器測試。

### <a name="how-to-fix"></a>如何修正問題
您可以解決此問題，藉由移除`<CodesignEntitlements>`.csproj 檔案中的旗標，從 偵錯組建。 您可以依照下列方式來執行這項操作：

*警告：.csproj 檔案中的錯誤，可能破壞您的專案，因此最好備份您的檔案，才能嘗試進行這。*

1. 以滑鼠右鍵按一下 [方案] 窗格中的 iOS 專案，然後選取**卸載專案**
2. 以滑鼠右鍵按一下專案，然後選取**編輯.csproj [ProjectName]**
3. 找出偵錯 PropertyGroups、 它們的開頭應看起來像這樣的旗標：
   - 偵錯： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 發行： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. 在每個使用模擬器的組建，刪除或標記為註解下列屬性： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. 重新載入專案，而且您應該能夠部署至模擬器。

### <a name="next-steps"></a>後續步驟
如需進一步協助，請連絡我們，或如果此問題仍然發生即使利用上述的資訊，請參閱[xamarin 有哪些支援的選項？](~/cross-platform/troubleshooting/support-options.md)有關連絡人的選項，建議，以及如何如有需要的檔案新的 bug。 
