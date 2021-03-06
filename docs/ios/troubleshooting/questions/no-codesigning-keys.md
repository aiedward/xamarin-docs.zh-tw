---
title: 為什麼我的 iOS 組建因為「Keychain 中找不到任何有效的 iPhone 程式碼簽署金鑰」而失敗？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0c777b8d5326963e959d8bb13d81d7058caa6bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030940"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>為什麼我的 iOS 組建因為「Keychain 中找不到任何有效的 iPhone 程式碼簽署金鑰」而失敗？

## <a name="cause-of-the-error"></a>錯誤的原因

當有問題的專案正在尋找有效的程式碼簽署認證，但找不到它們時，就會出現這個錯誤訊息。 在實體 iOS 裝置上進行測試和部署時，需要進行程式碼簽署;和臨機操作 & App store 組建。

### <a name="provisioning-devices"></a>布建裝置

如果您之前尚未布建 iOS 裝置，下列指南會引導您完成完整的逐步程式：裝置布建[指南](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>使用 iOS 模擬器時的錯誤

> [!NOTE]
> 此問題已在最新版本的 Xamarin for Visual Studio 中解決。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

在 Xamarin 中發生錯誤。 Visual Studio 3.11，導致 Xamarin Forms 範本中的 iOS 專案新增 codesign 權利 plist 至模擬器組建;使用模擬器有效率地封鎖測試。

### <a name="how-to-fix"></a>如何修正

若要解決這個問題，您可以從 .csproj 檔案中的 debug build 移除 `<CodesignEntitlements>` 旗標。 您可以依照下列方式來執行這項操作：

> [!WARNING]
> .Csproj 檔案中的錯誤可能會中斷您的專案，因此最好先備份您的檔案，然後再嘗試此動作。

1. 在解決方案窗格中，以滑鼠右鍵按一下 iOS 專案，然後選取 **[卸載專案**]
2. 再次以滑鼠右鍵按一下專案，然後選取 **[編輯] [專案名稱] .csproj**
3. 找出 Debug PropertyGroups，它們的開頭應該是如下所示的旗標：
   - Debug： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 版本： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. 在使用模擬器的每個組建中，刪除或批註下列屬性： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. 重載專案，您應該能夠部署至模擬器。

### <a name="next-steps"></a>後續步驟
如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱[什麼是適用于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議，以及如何在需要時提出新 bug 的相關資訊.
