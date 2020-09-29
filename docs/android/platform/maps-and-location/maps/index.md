---
title: 如何搭配使用 Google Maps 和位置與 Xamarin. Android
description: 本文討論如何搭配使用 maps 和 location 與 Xamarin. Android。 其中涵蓋了利用內建地圖應用程式直接使用 Google Maps Android API V2 的所有專案。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 11feb8e3e10e12bfb2c647182ec4ba953ab34e24
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456895"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>如何搭配使用 Google Maps 和位置與 Xamarin. Android

_本文討論如何搭配使用 maps 和 location 與 Xamarin. Android。其中涵蓋了利用內建地圖應用程式直接使用 Google Maps Android API V2 的所有專案。_

## <a name="maps-overview"></a>地圖總覽

對應技術是行動裝置的一種普遍的補充。 桌上型電腦和膝上型電腦通常不會有內建的位置感知。 另一方面，行動裝置會使用這類應用程式來找出裝置，並顯示變更的位置資訊。 Android 具有強大的內建技術，可使用裝置上可能提供的位置硬體來顯示地圖上的位置資料。 本文涵蓋 Xamarin 的地圖應用程式所提供的各種功能，包括： 

- 使用內建的 maps 應用程式快速新增對應功能。
- 使用 Maps API 來控制地圖的顯示。
- 使用各種不同的技術來加入圖形重迭。

本節中的主題涵蓋廣泛的對應功能。
首先，他們會說明如何利用 Android 的內建地圖應用程式，以及如何顯示位置的全景街道視圖。 然後，他們會討論如何使用 Maps API，直接在應用程式中納入對應功能，同時涵蓋如何控制地圖的位置和顯示，以及如何加入圖形覆迭。

## <a name="related-links"></a>相關連結

- [MapsAndLocationDemo_v3 (範例) ](/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [意圖清單：在 Android 裝置上叫用 Google 應用程式](https://developer.android.com/guide/appendix/g-app-intents.html)
- [位置和地圖](https://developer.android.com/guide/topics/location/index.html)