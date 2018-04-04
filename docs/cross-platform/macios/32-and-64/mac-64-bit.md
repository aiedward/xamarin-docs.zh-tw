---
title: 更新 Xamarin.Mac 整合到 64 位元的應用程式
description: 本指南說明如何更新您的目標為 64 位元 Xamarin.Mac 應用程式
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/22/2018
ms.openlocfilehash: e365fe1af47338f41aebe4bc0d81d289466a9b6c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>更新 Xamarin.Mac 整合到 64 位元的應用程式

為準，年 1 月 2018 Apple 要求新[Mac App Store 提交目標的 64 位元](https://developer.apple.com/news/?id=06282017a)。 應用程式已經可以使用 Mac App Store 上的必須由年 6 月 2018年目標 64 位元更新。

**檔案** > **新增**Xamarin.Mac 專案範本會建立 64 位元應用程式依預設，因此任何最近建立的應用程式已與 64 位元相容，而且不需要任何變更。

## <a name="targeting-64-bit"></a>目標為 64 位元

1. 開啟**專案選項**為您的視窗是 Xamarin.Mac 應用程式：

   ![專案的內容功能表](mac-64-bit-images/1-contextual_menu-vsmac.png "專案內容功能表")

2. 選取**Mac 建置**並設定**支援的架構**至**x86\_64**:

   [![將支援的架構設定為 x86_64](mac-64-bit-images/2-project_options-vsmac.png "x86_64 設定支援的架構")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. 如果您的應用程式有任何外部的相依性，例如原生參考或繫結的專案，請將其更新為目標的 64 位元。

### <a name="errors"></a>錯誤

第一次建置或執行您的應用程式，64 位元的支援，您可能會發生連結錯誤從 clang 或執行階段問題。 如果第三方，可能會發生這些錯誤相依性 — 例如，原生參考您 Xamarin.Mac 或繫結的專案或手動載入整個系統的架構中 — 尚未更新為 64 位元。

> [!TIP]
> 將專案轉換為 64 位元是一項重大變更，並可能間接發掘各種程式設計錯誤。 尤其是它可能會變更大小和 p/invoke 的簽章和連結您的專案中的原生程式碼影響的資料結構的對齊方式。 請考慮檢閱任何指定的建置警告並進行測試您的應用程式 throughly 之後，找出潛在的問題。

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>範例錯誤導致未鎖定 64 位元的動態連結協力廠商相依性：

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

這個錯誤，無法在執行階段後接`dlopen`傳回`IntPtr.Zero`而不是預期的控制代碼。

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>範例錯誤導致未鎖定 64 位元以靜態方式連結協力廠商相依性：

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

若要建置並執行成功，這些相依性更新至 64 位元並重新編譯您的應用程式。

