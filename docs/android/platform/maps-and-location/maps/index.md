---
title: 如何使用谷歌地圖和位置與Xamarin.安卓
description: 本文討論了如何使用地圖和位置與Xamarin.Android。 它涵蓋了從利用內置地圖應用程式到直接使用谷歌地圖Android API V2的所有內容。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: d877f415bb96024bb41edc2be9aec108ae248e88
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020038"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>如何使用谷歌地圖和位置與Xamarin.安卓

_本文討論了如何使用地圖和位置與Xamarin.Android。它涵蓋了從利用內置地圖應用程式到直接使用谷歌地圖Android API V2的所有內容。_

## <a name="maps-overview"></a>地圖概述

映射技術是行動裝置無處不在的補充。 台式計算機和筆記型電腦往往沒有內置位置感知功能。 另一方面,行動裝置使用此類應用程式來定位設備並顯示不斷變化的位置資訊。 Android 擁有強大的內置技術,使用設備上可能提供的位置硬體在地圖上顯示位置數據。 本文介紹了 Xamarin.Android 下地圖應用程式所提供的地圖系列,包括: 

- 使用內建映射應用程式快速添加映射功能。
- 使用地圖 API 控制地圖的顯示。
- 使用各種技術添加圖形疊加。

本節中的主題涵蓋廣泛的映射功能。
首先,他們解釋了如何利用 Android 的內置地圖應用程式以及如何顯示位置的全景街景。 然後,他們討論如何使用地圖 API 直接合併應用程式中的映射要素,包括如何控制地圖的位置和顯示以及如何添加圖形疊加。

## <a name="related-links"></a>相關連結

- [MapsAndLocationDemo_v3(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [意圖清單:在 Android 裝置上調用 Google 應用程式](https://developer.android.com/guide/appendix/g-app-intents.html)
- [位置與地圖](https://developer.android.com/guide/topics/location/index.html)
