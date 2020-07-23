---
title: Configuration Manager 中的 [部署] 核取方塊無法使用
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: 92fe421aee6b5add84a188468856172bf8c56d15
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939278"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Configuration Manager 中的 [部署] 核取方塊無法使用

自 Xamarin 3.5 之後，每當您按下 [**開始**] 工具列按鈕或挑選 [ **Debug > 開始調試**] 功能表項目時，就會自動部署 xamarin. iOS 專案。 在執行其中一個命令之前，您仍然需要將所需的 Xamarin iOS 應用程式專案設定為**啟始專案**。

因此，在 Xamarin iOS 專案的 Visual Studio Configuration Manager 中，會刻意停用 [**部署**] 核取方塊：

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

當您未將 [Xamarin iOS 應用程式] 專案設定為 [部署] 時，這項變更會排除可能出現在舊版 Xamarin （版本3.3 和較舊版本）中的錯誤：

![錯誤對話方塊：專案 iPhoneApp1 必須先部署，才可以啟動。 確認已選取要在 [方案 Configuration Manager 中部署的專案。](deploy-checkboxes-images/error.png)
