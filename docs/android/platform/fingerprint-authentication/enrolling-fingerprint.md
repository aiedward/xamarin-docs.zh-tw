---
title: 註冊指紋
description: 如何設定螢幕鎖定，並在 Android 裝置或模擬器上註冊指紋。
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: f52be16a81f3c8047997e1f4a88e13f6b940db14
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756424"
---
# <a name="enrolling-a-fingerprint"></a>註冊指紋

## <a name="enrolling-a-fingerprint-overview"></a>註冊指紋總覽

只有當裝置已設定指紋驗證時，Android 應用程式才有可能利用指紋驗證。 本指南將討論如何在 Android 裝置或模擬器上註冊指紋。 模擬器沒有實際的硬體來執行指紋掃描，但您可以使用 Android Debug Bridge 的協助來模擬指紋掃描（如下所述）。  本指南將討論如何在 Android 裝置上啟用螢幕鎖定，並註冊指紋以進行驗證。

## <a name="requirements"></a>需求

若要註冊指紋，您必須具有 Android 裝置或執行 API 層級23的模擬器（Android 6.0）。

使用 Android Debug Bridge （ADB）需要熟悉命令提示字元，而**ADB**可執行檔必須位於 Bash、PowerShell 或命令提示字元環境的路徑中。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>設定螢幕鎖定和註冊指紋 

若要設定螢幕鎖定，請執行下列步驟：

1. 移至 [**設定] > [安全性**]，然後選取 [**螢幕鎖定**]：

    ![[安全性] 畫面上螢幕鎖定選項的位置](enrolling-fingerprint-images/testing-01.png)

2. 出現的下一個畫面可讓您選取並設定其中一個螢幕鎖定安全性方法： 

    ![選取 [輕刷]、[模式]、[PIN] 或 [密碼]](enrolling-fingerprint-images/testing-02.png)

   選取並完成其中一個可用的螢幕鎖定方法。

3. 設定 screenlock 之後，請返回 [設定] **> [安全性**] 頁面，然後選取 [**指紋**]：

    ![[安全性] 畫面上指紋選取的位置](enrolling-fingerprint-images/testing-03.png)

4. 從該處，遵循順序，將指紋新增至裝置：

    [![將指紋新增至裝置的螢幕擷取畫面順序](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. 在最後一個畫面中，系統會提示您將手指放在指紋掃描器上： 

    ![提示您將手指放在感應器上的畫面](enrolling-fingerprint-images/testing-05.png)

    如果您使用 Android 裝置，請將手指碰觸到掃描器來完成此程式。 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>在模擬器上模擬指紋掃描

在 Android 模擬器上，您可以使用 Android Debug Bridge 來模擬指紋掃描。 在 OS X 上，于 Windows 上啟動終端機會話啟動命令提示字元或 Powershell 會話， `adb`然後執行：

```shell
$ adb -e emu finger touch 1
```

**1**的值是「掃描」手指的_手指\_識別碼_。 這是您為每個虛擬指紋指派的唯一整數。 在未來執行應用程式時，您可以在每次模擬器提示您提供指紋時執行相同的 ADB 命令，您可以執行`adb`命令，並 _\__ 將指紋識別碼傳遞給它以模擬指紋掃描。

完成指紋掃描之後，Android 會通知您已新增指紋：  

![顯示已新增指紋的畫面！](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>總結 

本指南涵蓋如何設定螢幕鎖定，並在 Android 裝置或 Android 模擬器中註冊指紋。 
