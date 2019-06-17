---
title: Configuration Manager 中的 [部署] 核取方塊無法使用
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 35efb00a721062ad3217300f7e3a5430b1bd1560
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357748"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Configuration Manager 中的 [部署] 核取方塊無法使用

因為 Xamarin 3.5 Xamarin.iOS 專案自動部署只要按下**開始**工具列按鈕或挑選**偵錯 > 啟動偵錯**功能表項目。 您仍然必須設定為所需的 Xamarin.iOS 應用程式專案**啟始專案**之前執行這些命令。

基於這個原因，**部署**核取方塊會刻意停用 Visual Studio 組態管理員中的 Xamarin.iOS 專案：

![](deploy-checkboxes-images/configuration.png "顯示停用 Xamarin 3.5 中的 Xamarin.iOS 專案的 [部署] 核取方塊的 visual Studio 組態管理員")

這項變更可以解決的錯誤，可能會出現在舊版的 Xamarin （3.3 和更早版本） 時的 Xamarin.iOS 應用程式專案未設定為部署：

![](deploy-checkboxes-images/error.png "錯誤對話方塊：專案 iPhoneApp1 需要它才能啟動部署。確認已選取 [部署方案組態管理員] 中的 專案。")
