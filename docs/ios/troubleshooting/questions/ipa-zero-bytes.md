---
title: IPA 檔案為 0 個位元組
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 10fc13124a1c97cc7534d8cf14b7717b2ddc4fa1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291045"
---
# <a name="ipa-file-is-0-bytes"></a>IPA 檔案為 0 個位元組

> [!IMPORTANT]
> 此問題已在最新版本的 Xamarin 中解決。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。



舊版 Xamarin 中有一些已知問題，可能導致 Windows 上的 .IPA 檔案為0個位元組。 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>已在 Xamarin for Visual Studio 3.11.584 中修正 
- [Bug 24416-從命令列建立「臨機操作」設定並不會將 .IPA 檔案複製到 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-變更 [專案屬性-> iOS .IPA 選項-> 套件名稱] 可防止將 .IPA 複製回 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS iOS 3.11] 設定「組建」編號與「版本」數目不同，導致 .IPA 不會複製到 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>已在 Xamarin for Visual Studio 4.1.0.496 中修正
- [Bug 27989-如果元件名稱與專案名稱不相符，在組建伺服器上顯示 .ipa 檔案就會失敗](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
