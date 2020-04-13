---
title: 註冊指紋
description: 如何設置螢幕鎖並在 Android 設備或模擬器上註冊指紋。
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027587"
---
# <a name="enrolling-a-fingerprint"></a>註冊指紋

## <a name="enrolling-a-fingerprint-overview"></a>註冊指紋概述

僅當設備已配置了指紋身份驗證時,Android 應用程式才可能利用指紋身份驗證。 本指南將討論如何在 Android 設備或模擬器上註冊指紋。 模擬器沒有實際硬體來執行指紋掃描,但可以在 Android 調試橋的説明下模擬指紋掃描(如下所述)。  本指南將討論如何在 Android 設備上啟用螢幕鎖定,並註冊指紋進行身份驗證。

## <a name="requirements"></a>需求

要註冊指紋,您必須有一個 Android 設備或一個模擬器運行 API 級別 23 (Android 6.0)。

使用 Android 除錯橋 (ADB) 需要熟悉命令提示符,並且**adb**可執行檔必須位於 Bash、PowerShell 或命令提示器環境的 PATH 中。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>設定螢幕鎖定並註冊指紋 

要設定螢幕鎖,執行以下步驟:

1. 跳到 **「設定>安全**」,然後選擇**螢幕鎖定**:

    ![安全螢幕上螢幕鎖定選擇的位置](enrolling-fingerprint-images/testing-01.png)

2. 顯示的下一個螢幕會允許您選擇與設定螢幕鎖定安全方法之一: 

    ![選擇「輕掃、模式、PIN」或"密碼"](enrolling-fingerprint-images/testing-02.png)

   選擇並完成一種可用的屏幕鎖定方法。

3. 設定螢幕鎖後,傳回**到 「設定>安全**」頁並選擇 **「指紋**」 :

    ![安全螢幕上的指紋選擇位置](enrolling-fingerprint-images/testing-03.png)

4. 從那裡,按照序列向設備添加指紋:

    [![新增指紋的螢幕擷取](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. 在最後的螢幕上,系統會提示您將手指放在指紋掃描器上: 

    ![提示您將手指放在感測器上的螢幕](enrolling-fingerprint-images/testing-05.png)

    如果您使用的是 Android 設備,則透過觸控手指到掃描器來完成此過程。 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>模擬模擬器上的指紋掃描

在 Android 模擬器上,可以使用 Android 調試橋模擬指紋掃描。 在 OS X 上,在 Windows 上啟動指令提示`adb`符或 Powershell 工作階段並執行 時啟動終端工作階段:

```shell
$ adb -e emu finger touch 1
```

**值 1**是"掃描"手指_\_的手指 ID。_ 它是您為每個虛擬指紋分配的唯一整數。 將來,當應用程式運行時,每次模擬器提示您輸入指紋時,都可以運行相同的 ADB 命令,您可以`adb`運行 該命令並傳遞該命令的手指_\_ID_以模擬指紋掃描。

指紋掃描完成後,Android 將通知您已添加指紋:  

![螢幕顯示指紋添加!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>總結 

本指南介紹了如何在 Android 設備或 Android 模擬器中設置螢幕鎖並註冊指紋。 
