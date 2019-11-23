---
title: Xamarin。表單啟動顯示畫面
description: 本文說明如何為 Xamarin. Forms 應用程式建立啟動顯示畫面。
ms.prod: xamarin
ms.assetId: 338C8F60-90F2-4B3D-BB47-7F846AE8DC6C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/1/2019
ms.openlocfilehash: 2624c3f35a9cfac122a0b36ea8c1684d30f57824
ms.sourcegitcommit: 5110d1279809a2af58d3d66cd14c78113bb51436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035752"
---
# <a name="xamarinforms-splash-screen"></a>Xamarin。表單啟動顯示畫面

應用程式完成其初始化處理常式時，通常會有啟動延遲。 開發人員可能會想要在應用程式啟動時，提供品牌化的體驗，通常稱為啟動顯示畫面。 本文說明如何為 Xamarin. Forms 應用程式建立啟動顯示畫面。

在原生啟動順序完成之後，會在每個平臺上初始化 Xamarin。 Xamarin. 表單已初始化：

- 在 Android 上 `MainActivity` 類別的 `OnCreate` 方法中。
- 在 iOS 上 `AppDelegate` 類別的 `FinishedLaunching` 方法中。
- 在 UWP 的 `App` 類別的 `OnLaunched` 方法中。

啟動應用程式時，啟動顯示畫面應該會儘快顯示，但是 Xamarin. 表單在啟動順序中的延遲之前都不會初始化，這表示啟動顯示畫面必須在每個平臺上的 Xamarin 外部執行。 下列各節說明如何在每個平臺上建立啟動顯示畫面。

## <a name="xamarinforms-android-splash-screen"></a>[Xamarin] 表單 Android 啟動顯示畫面

若要在 Android 上建立啟動顯示畫面，必須建立啟動顯示 `Activity` 作為具有特殊主題的 `MainLauncher`。 啟動顯示 `Activity` 開始時，它會啟動含有一般應用程式主題的主要 `Activity`。

如需有關在 Xamarin 上啟動顯示畫面的詳細資訊，請參閱 [ [xamarin] 啟動](~/android/user-interface/splash-screen.md)顯示畫面。

## <a name="xamarinforms-ios-splash-screen"></a>[Xamarin] iOS 啟動顯示畫面

IOS 上的啟動顯示畫面稱為啟動畫面。 若要在 iOS 上建立啟動畫面，您必須建立一個分鏡腳本來定義啟動畫面的 UI，然後將分鏡腳本設定為**plist**中的啟動畫面。

如需在 Xamarin 上啟動畫面的詳細資訊，請參閱 [Xamarin Ios 啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)。

## <a name="xamarinforms-uwp-splash-screen"></a>[Xamarin] [表單 UWP] 啟動顯示畫面

在 UWP 上， **package.appxmanifest.xml**包含具有 [**啟動顯示畫面**] 子功能表的 [**視覺資產**] 索引標籤。 您可以在此功能表中指定啟動顯示畫面圖形：

[![在 UWP 上設定啟動顯示畫面](splashscreen-images/uwp-splashscreen-cropped.png)](splashscreen-images/uwp-splashscreen.png#lightbox)

## <a name="related-links"></a>相關連結

- [Xamarin Android 啟動顯示畫面](~/android/user-interface/splash-screen.md)
- [Xamarin iOS 啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)
