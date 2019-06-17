---
title: 偵錯的模擬器上的 Android Wear
description: 這些文章說明如何在模擬器上的 Xamarin.Android Wear 應用程式進行偵錯。
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 699fb3cc3a5730e8ab2c677feb7cdfbdcf106aeb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308251"
---
# <a name="debug-android-wear-on-an-emulator"></a>偵錯的模擬器上的 Android Wear

_這些文章說明如何在模擬器上的 Xamarin.Android Wear 應用程式進行偵錯。_

## <a name="debug-wear-on-emulator-overview"></a>偵錯 Wear 模擬器概觀

開發 Android Wear 的應用程式需要執行應用程式，在實體硬體上或使用模擬器。 使用硬體是最佳方式，但並不一定是最務實的做法。 在許多情況下，它可以是簡單且更符合成本效益來模擬 Android Wear 的硬體，如下所述，使用模擬器。 如果您尚不熟悉部署和執行的程序的 Android Wear 的應用程式，請參閱[大家好，Wear](~/android/wear/get-started/hello-wear.md)。

## <a name="configure-the-android-emulator"></a>設定 Android 模擬器

若要在模擬器上執行您穿戴式應用程式，您必須安裝 Android SDK Android 模擬器，並設定適用於 Android Wear。 整體的 Android SDK 模擬器安裝和設定資訊，請參閱[Android Emulator 安裝](~/android/get-started/installation/android-emulator/index.md)。

當您建立在 Wear 虛擬裝置時，選取 Android Wear 裝置設定檔 (例如**Android Wear 正方形**)。 為了提升效能，使用 Wear **x86** CPU/ABI，在此範例中所示：

[![Wear 虛擬裝置組態範例](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>啟動 Wear 虛擬裝置 

建立 Android Wear 的虛擬裝置之後，您可以從裝置下拉式功能表在 IDE 中選擇它，然後再開始偵錯。 如果您的虛擬裝置無法使用裝置下拉式功能表中，確認您的專案是 Android *Wear*應用程式專案 （而非 Android 應用程式專案） 和它的目標 API 層級設為相同的 API 層級的虛擬裝置。 例如: 

[![在 Visual Studio [裝置] 功能表中選擇 Wear AVD](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

在 Android 模擬器啟動之後，Xamarin.Android 會將穿戴式應用程式部署至模擬器。 模擬器會以所設定的虛擬裝置映像來執行應用程式。

請不要感到驚訝，如果您看到此 （或另一個的插入式螢幕） 先。 監看式模擬器可能需要一段時間才能啟動： 

![觀看模擬器顯示一下...](debug-on-emulator-images/please-wait.png)

您可以讓模擬器保持執行；無須在每次執行應用程式時都將其關閉並重新啟動。

 
## <a name="summary"></a>總結
 
本指南說明如何設定 Android Wear 開發的模擬器並啟動 Wear 虛擬裝置來偵錯。
