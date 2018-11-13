---
title: 更新的統一的 Xamarin.Mac 應用程式，到 64 位元
description: 本指南說明如何更新您的 Xamarin.Mac 應用程式，以目標為 64 位元。 它也提供進行這項變更時可能遇到的錯誤類型的範例。
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 9bd70fec5d6d3bbbc4855980e1542bd4e486acaa
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528516"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>更新的統一的 Xamarin.Mac 應用程式，到 64 位元

自 2018 年 1 月的起，Apple 要求新[Mac App Store 提交目標的 64 位元](https://developer.apple.com/news/?id=06282017a)。 可用的應用程式已在 Mac App Store 上必須更新為目標的 64 位元，2018 年 6 月。

**檔案** > **新增**Xamarin.Mac 專案範本會建立 64 位元應用程式依預設，因此任何最近建立的應用程式已與 64 位元相容，而且不需要任何變更。

## <a name="targeting-64-bit"></a>目標為 64 位元

1. 開啟**專案選項**Xamarin.Mac 應用程式的視窗：

   ![專案操作功能表](mac-64-bit-images/1-contextual_menu-vsmac.png "專案操作功能表")

2. 選取  **Mac 組建**並設定**支援的架構**來**x86\_64**:

   [![將支援的架構設定為 x86_64](mac-64-bit-images/2-project_options-vsmac.png "設 x86_64 中支援的架構")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. 如果您的應用程式有任何外部的相依性，例如原生參考或繫結專案，更新為目標的 64 位元。

### <a name="errors"></a>錯誤

第一次建置或執行您的應用程式，64 位元的支援，您可能會發生連結錯誤從 clang 或執行階段問題。 可能會發生這些錯誤，如果協力廠商相依性 — 比方說，原生參考 Xamarin.Mac 或繫結專案或手動載入整個系統的架構中，尚未更新為 64 位元。

> [!TIP]
> 將您的專案轉換成 64 位元是一項重大變更，並間接可能會發現各種不同的程式設計錯誤。 特別是它可能會變更的大小和 p/invoke 簽章，並在您的專案中連結的原生程式碼會影響的資料結構的對齊方式。 請考慮檢閱任何建置指定的警告，並且徹底測試您的應用程式之後，以攔截潛在問題。

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>範例錯誤所產生的未鎖定 64 位元的動態連結協力廠商相依性：

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

此錯誤，無法在執行階段後接`dlopen`傳回`IntPtr.Zero`而不是預期的控制代碼。

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>範例錯誤所產生的未鎖定 64 位元以靜態方式連結協力廠商相依性：

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

若要建置並執行成功，這些相依性更新至 64 位元並重新編譯您的應用程式。

