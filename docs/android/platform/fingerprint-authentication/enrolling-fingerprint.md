---
title: "註冊使用指紋"
description: "如何設定螢幕鎖定和註冊 Android 裝置或模擬器上的指紋。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 494c808c8c65e0f3cf0a34b26d87069dd456718c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="enrolling-a-fingerprint"></a>註冊使用指紋

## <a name="enrolling-a-fingerprint-overview"></a>註冊指紋概觀

您僅能利用指紋驗證，如果裝置已設有指紋驗證 Android 應用程式。 本指南將討論如何註冊 Android 裝置或模擬器上的指紋。 模擬器不需要實際的硬體執行指紋掃描的速度，但可模擬指紋掃描 Android Debug Bridge （如下所述） 的協助。  本指南將討論如何啟用 Android 裝置上的 螢幕鎖定，以及註冊使用指紋進行驗證。

## <a name="requirements"></a>需求

若要註冊的指紋，您必須在 Android 裝置或模擬器執行應用程式開發介面層級 23 (Android 6.0)。

使用的 Android 偵錯橋接器 (ADB) 需要熟悉命令提示字元中，而**adb**可執行檔必須位於您 Bash，PowerShell 路徑中或命令提示字元環境。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>設定 螢幕鎖定，以及註冊使用指紋 

若要設定螢幕鎖定時，執行下列步驟：

1. 移至**設定 > 安全性**，然後選取**螢幕鎖定**:

    ![在 安全性 畫面上的 螢幕鎖定選取範圍的位置](enrolling-fingerprint-images/testing-01.png)

2. 下一個畫面出現可讓您選取並設定其中一種螢幕鎖定安全性方法： 

    ![選取撥動、 模式、 PIN 或密碼](enrolling-fingerprint-images/testing-02.png)

   選取並完成其中一個可用的螢幕鎖定方法。

3. Screenlock 設定之後，返回**設定 > 安全性**頁面，然後選取**指紋**:

    ![在 [安全性] 畫面上的指紋選取範圍的位置](enrolling-fingerprint-images/testing-03.png)

4. 從該處，請依照下列順序，以新增到裝置的指紋：

    [![序列的指紋加入裝置的螢幕擷取畫面](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png)

5. 在最後一個畫面會提示您將您的手指放在指紋掃描器上： 

    ![畫面上會提示您以將您的手指放在感應器](enrolling-fingerprint-images/testing-05.png)

    如果您使用 Android 裝置，以完成程序接觸掃描器手指。 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>模擬在模擬器上的指紋掃描

在 Android 模擬器中，您可使用 Android Debug Bridge 模擬指紋掃描。 Windows 上的終端機工作階段啟動命令提示字元或 Powershell 工作階段，並執行 OS X 開始`adb`:

```shell
$ adb -e emu finger touch 1
```

值**1**是_手指\_識別碼_「 掃描 」 指的。 它是您為每個虛擬指紋指派的唯一整數。 未來當您執行應用程式可以執行這個相同 ADB 每次模擬器的命令會提示您的指紋，您可以執行`adb`命令，並將它傳遞_手指\_識別碼_模擬指紋掃描.

指紋掃描完成後，Android 將會通知您已新增指紋：  

![畫面上顯示的指紋加入 ！](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>總結 

本指南涵蓋如何設定 螢幕鎖定，以及註冊 Android 裝置上或在 Android 模擬器中的指紋。 

