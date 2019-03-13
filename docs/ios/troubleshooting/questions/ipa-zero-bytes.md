---
title: IPA 檔案為 0 個位元組
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4835c980b6b11c92ec1c81dea69f229aa5652275
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102648"
---
# <a name="ipa-file-is-0-bytes"></a>IPA 檔案為 0 個位元組

> [!IMPORTANT]
> 這個問題已經解決最新版 Xamarin。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。



在舊版的 Xamarin，可能會導致要 0 個位元組的 Windows 上的 IPA 檔案發生一些已知的問題。 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Xamarin for Visual Studio 3.11.584 修正 
- [Bug 24416-建置 「 臨機操作 」 設定，從命令列會不複製 IPA 檔案，以 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-變更 「 專案屬性]-> [iOS IPA 選項]-> [封裝名稱 「 防止 IPA 複製回 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS.iOS 3.11] 設定 「 建置 」 的數字不是要複製到 Windows 「 版本 」 數字會導致 IPA](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Xamarin for Visual Studio 4.1.0.496 修正
- [Bug 27989-顯示組建伺服器發生故障的 ipa 檔案，如果組件名稱不符合專案名稱](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
