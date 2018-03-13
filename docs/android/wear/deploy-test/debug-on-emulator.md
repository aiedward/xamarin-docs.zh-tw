---
title: "偵錯 Android 模擬器上損耗"
description: "這些文件說明如何偵錯 Xamarin.Android 損耗上的應用程式的模擬器。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: dc6146670c4b5f7d29491335b4719e032487dcce
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="debug-android-wear-on-an-emulator"></a>偵錯 Android 模擬器上損耗

_這些文件說明如何偵錯 Xamarin.Android 損耗上的應用程式的模擬器。_

## <a name="debug-wear-on-emulator-overview"></a>偵錯損耗上模擬器概觀

開發 Android 戴上的應用程式需要執行應用程式，請在實體硬體上，或使用模擬器。 使用硬體是最佳方式，但並不一定是最務實的做法。 在許多情況下，它可以是簡單且更符合成本效益模擬/模擬 Android 戴上的硬體如下所述，使用模擬器。 如果您尚不熟悉的程序部署和執行的 Android 戴上的應用程式，請參閱[Hello，穿](~/android/wear/get-started/hello-wear.md)。

## <a name="configure-the-android-sdk-emulator"></a>設定 Android SDK 模擬器

損耗上執行應用程式模擬器，您必須安裝 Android SDK Android 模擬器和 Android 戴上設定它。 整體的 Android SDK 模擬器安裝和組態資訊，請參閱[Android SDK 模擬器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)。

當您建立損耗虛擬裝置時，選取 Android 戴上的裝置設定檔 (例如**Android 損耗方**)。 為了增進效能，使用損耗**x86** CPU/ABI，如本範例所示：

[![範例損耗虛擬裝置設定](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>啟動損耗虛擬裝置 

建立 Android 戴上的虛擬裝置之後，您可以從裝置下拉功能表，在 IDE 中選擇它之前您開始偵錯。 如果您的虛擬裝置無法使用裝置的下拉式清單中，確認您的專案是 Android*穿*應用程式專案 （非 Android 應用程式專案） 和它的目標應用程式開發介面層級，設定為相同的 API 層級做為虛擬裝置。 例如: 

[![在 Visual Studio 裝置功能表中選擇穿 AVD](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Android 模擬器啟動之後，Xamarin.Android 會損耗應用程式部署至模擬器中。 模擬器會以所設定的虛擬裝置映像來執行應用程式。

不會感到驚訝，如果您看到這個 （或另一個插入式螢幕） 一開始。 監看式模擬器可能需要一段時啟動： 

![監看模擬器會顯示一分鐘時間...](debug-on-emulator-images/please-wait.png)

您可以讓模擬器保持執行；無須在每次執行應用程式時都將其關閉並重新啟動。

 
## <a name="summary"></a>總結
 
本指南說明如何設定用於損耗開發的 Android SDK 模擬器並啟動偵錯損耗虛擬裝置。
