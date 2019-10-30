---
title: 在模擬器上進行 Android 磨損的偵錯工具
description: 這些文章說明如何在模擬器上進行 Xamarin. Android 磨損應用程式的偵錯工具。
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: ca0a6884c05686bded25a2e515456ab192002a24
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028676"
---
# <a name="debug-android-wear-on-an-emulator"></a>在模擬器上進行 Android 磨損的偵錯工具

_這些文章說明如何在模擬器上進行 Xamarin. Android 磨損應用程式的偵錯工具。_

## <a name="debug-wear-on-emulator-overview"></a>模擬器上的偵錯工具總覽

開發 Android 磨損應用程式需要在實體硬體上或使用模擬器或模擬器執行應用程式。 使用硬體是最佳方式，但並不一定是最務實的做法。 在許多情況下，使用模擬器來模擬/模擬 Android 磨損硬體可能更簡單且更符合成本效益，如下所述。 如果您還不熟悉部署和執行 Android 磨損應用程式的流程，請參閱[Hello，磨損](~/android/wear/get-started/hello-wear.md)。

## <a name="configure-the-android-emulator"></a>設定 Android Emulator

若要在模擬器上執行您的磨損應用程式，您必須安裝 Android SDK Android Emulator 並設定它以進行 Android 磨損。 如需整體 Android SDK 模擬器安裝和設定資訊，請參閱[Android Emulator 安裝程式](~/android/get-started/installation/android-emulator/index.md)。

當您建立磨損虛擬裝置時，請選取 Android 磨損裝置設定檔（例如**Android 磨損**）。 為了改善效能，請使用磨損**x86** CPU/ABI，如下列範例所示：

[![範例磨損虛擬裝置設定](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)

## <a name="launch-the-wear-virtual-device"></a>啟動磨損虛擬裝置 

建立 Android 磨損虛擬裝置之後，您可以從 IDE 的 [裝置] 下拉式功能表中選擇它，然後再開始進行偵錯工具。 如果裝置上的虛擬裝置無法使用，請確認您的專案是 Android*磨損*應用程式專案（而非 android 應用程式專案），且其目標 api 層級已設定為與虛擬裝置相同的 api 層級。 例如:

[![選擇 Visual Studio 裝置 功能表中的磨損 AVD](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Android 模擬器啟動之後，Xamarin 就會將此應用程式部署至模擬器。 模擬器會以所設定的虛擬裝置映像來執行應用程式。

如果您一開始就看到此（或另一個插入式畫面），請不要感到驚訝。 監看式模擬器可能需要一段時間才能啟動： 

![監看模擬器只會顯示一分鐘 。](debug-on-emulator-images/please-wait.png)

您可以讓模擬器保持執行；無須在每次執行應用程式時都將其關閉並重新啟動。

## <a name="summary"></a>總結

本指南說明如何設定用於磨損開發的 Android Emulator，並啟動用來進行偵錯工具的磨損虛擬裝置。
