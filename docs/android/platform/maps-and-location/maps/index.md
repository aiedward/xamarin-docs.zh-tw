---
title: 如何搭配使用 Google 地圖和位置與 Xamarin. Android
description: 本文討論如何搭配使用對應和位置與 Xamarin. Android。 其中涵蓋了將內建地圖應用程式直接運用在 Google Maps Android API V2 的所有內容。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: 80ec7a2ae13b3c76bccae80d2c6956533b027047
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643270"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>如何搭配使用 Google 地圖和位置與 Xamarin. Android

_本文討論如何搭配使用對應和位置與 Xamarin. Android。其中涵蓋了將內建地圖應用程式直接運用在 Google Maps Android API V2 的所有內容。_

## <a name="maps-overview"></a>對應總覽

對應技術是行動裝置的普遍補充。 桌上型電腦和筆記本電腦不會內建位置感知功能。 另一方面, 行動裝置會使用這類應用程式來尋找裝置, 並顯示變更的位置資訊。 Android 具有強大的內建技術, 會使用裝置上可能提供的位置硬體, 在地圖上顯示位置資料。 這篇文章涵蓋了 Xamarin 所提供的應用程式的各種對應, 包括: 

-  使用內建的 maps 應用程式來快速新增對應功能。
-  使用 Maps API 來控制地圖的顯示。
-  使用各種不同的技術來新增圖形重迭。

本節中的主題涵蓋各種對應功能。
首先, 他們會說明如何運用 Android 的內建地圖應用程式, 以及如何顯示位置的全景街道視圖。 然後, 他們會討論如何使用 Maps API 直接在應用程式內併入對應功能, 其中涵蓋如何控制地圖的位置和顯示, 以及如何新增圖形化重迭。


## <a name="related-links"></a>相關連結

- [MapsAndLocationDemo_v3 (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [意圖清單:在 Android 裝置上叫用 Google 應用程式](https://developer.android.com/guide/appendix/g-app-intents.html)
- [位置和地圖](https://developer.android.com/guide/topics/location/index.html)
