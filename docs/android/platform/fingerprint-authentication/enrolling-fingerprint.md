---
title: 註冊指紋
description: 如何設定螢幕鎖定及註冊 Android 裝置或模擬器上的指紋。
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 18903a7d8f6c4033dc3ac7c4c0187a247d023bc1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115909"
---
# <a name="enrolling-a-fingerprint"></a>註冊指紋

## <a name="enrolling-a-fingerprint-overview"></a>註冊指紋概觀

您只可運用指紋驗證，如果裝置已設有指紋驗證 Android 應用程式。 本指南會討論如何註冊 Android 裝置或模擬器上的指紋。 模擬器不需要實際的硬體執行指紋掃描，但很有可能模擬指紋掃描 Android Debug Bridge （如下所述） 的協助。  本指南會討論如何啟用 Android 裝置上的螢幕鎖定，以及註冊指紋驗證。

## <a name="requirements"></a>需求

若要註冊的指紋，您必須在 Android 裝置或模擬器執行 API 層級 23 (Android 6.0)。

使用的 Android Debug Bridge (ADB) 需要熟悉命令提示字元中，而**adb**可執行檔必須位於您 Bash、 PowerShell 路徑中或命令提示字元環境。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>設定螢幕鎖定，以及註冊指紋 

若要設定螢幕鎖定，執行下列步驟：

1. 移至**設定 > 安全性**，然後選取**螢幕鎖定**:

    ![在 [安全性] 畫面的螢幕鎖定選取的位置](enrolling-fingerprint-images/testing-01.png)

2. 下一個畫面出現可讓您選取和設定螢幕鎖定安全性方法的其中一個： 

    ![選取的撥動、 模式、 PIN 或密碼](enrolling-fingerprint-images/testing-02.png)

   選取並完成其中一個可用的螢幕鎖定方法。

3. Screenlock 設定之後，返回**設定 > 安全性**頁面，然後選取**指紋**:

    ![指紋選取範圍，在 [安全性] 畫面上的位置](enrolling-fingerprint-images/testing-03.png)

4. 從該處，請依照下列順序將指紋新增到裝置：

    [![將指紋新增至裝置的螢幕擷取畫面的順序](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. 在最後一個畫面會提示您將手指放在指紋掃描器上： 

    ![畫面中提示您將手指放在感應器](enrolling-fingerprint-images/testing-05.png)

    如果您使用 Android 裝置，請觸碰手指掃描器以完成程序。 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>模擬在模擬器上的指紋掃描

在 Android 模擬器，就可以使用 Android Debug Bridge 來模擬指紋掃描。 在 Windows 上的終端機工作階段啟動命令提示字元或 Powershell 工作階段並執行 OS X 啟動`adb`:

```shell
$ adb -e emu finger touch 1
```

值**1**是_手指\_識別碼_如 「 掃描 」 的手指。 它是唯一的整數，您指派給每個虛擬的指紋。 未來當您執行應用程式時，才可以執行這個相同的 ADB 每次模擬器的命令會提示您針對指紋，您可以執行`adb`命令，並將它傳遞_手指\_識別碼_模擬指紋掃描.

指紋掃描已完成之後，Android 會通知您已新增指紋：  

![畫面顯示的指紋加入 ！](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>總結 

本指南涵蓋如何設定螢幕鎖定，以及註冊 Android 裝置上或在 Android 模擬器中的指紋。 

