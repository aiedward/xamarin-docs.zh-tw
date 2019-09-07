---
title: 將 Xamarin 統一應用程式更新為64位
description: 本指南說明如何將您的 Xamarin. Mac 應用程式更新為以64位為目標。 它也提供進行這項變更時可能會遇到的錯誤類型範例。
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 5539bab417c5efc0064cd1753cb74c7524463ee5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765915"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>將 Xamarin 統一應用程式更新為64位

自2018年1月起，Apple 要求新的[Mac App Store 提交目標為64位](https://developer.apple.com/news/?id=06282017a)。 已在 Mac App Store 上使用的應用程式，在6月2018日必須更新為目標64位。

[檔案] [**新增**] [Xamarin] 專案範本預設會建立64位應用程式，因此任何最近建立的應用程式都已與64位相容，而且不需要任何變更。  > 

## <a name="targeting-64-bit"></a>以64位為目標

1. 開啟您 Xamarin. Mac 應用程式的 [**專案選項**] 視窗：

   ![專案的內容功能表](mac-64-bit-images/1-contextual_menu-vsmac.png "專案的內容功能表")

2. 選取 [ **Mac 組建**]，並將**支援的架構**設定為**x86\_64**：

   [將![支援的架構設定為 x86_64]將(mac-64-bit-images/2-project_options-vsmac.png "支援的架構設定為 x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. 如果您的應用程式具有任何外部相依性（例如原生參考或系結專案），請將它們更新為目標64位。

### <a name="errors"></a>錯誤

當您第一次使用64位支援來建立或執行應用程式時，可能會遇到來自 clang 或執行時間問題的連結錯誤。 如果協力廠商相依性（例如，您的 Xamarin. Mac 或系結專案中的原生參考，或手動載入的全系統架構）尚未更新為64位，則可能會發生這些錯誤。

> [!TIP]
> 將您的專案轉換成64位是重大變更，可能會間接發現各種程式設計錯誤。 特別是，它可能會變更資料結構的大小和對齊，這會影響專案中連結的 p/invoke 簽章和原生程式碼。 請考慮查看任何指定的組建警告，然後在之後徹底測試您的應用程式，以攔截潛在問題。

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>因動態連結的協力廠商相依性未以64位為目標而產生的範例錯誤：

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

藉由`dlopen` `IntPtr.Zero`傳回而不是預期的控制碼，您可以在執行時間中遵循此錯誤。

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>因靜態連結的協力廠商相依性未以64位為目標而產生的範例錯誤：

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

若要成功建立並執行，請將這些相依性更新為64位，然後重新編譯您的應用程式。
