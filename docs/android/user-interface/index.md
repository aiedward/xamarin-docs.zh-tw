---
title: 使用 Xamarin 建立使用者介面
description: 建立 Xamarin Android 應用程式的使用者介面
ms.prod: xamarin
ms.assetid: F67B7C33-BC53-2BB6-CDA7-16E4AB4A9EFB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 405fb0e30881ee64514d3984842ac820d5663382
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764479"
---
# <a name="user-interface"></a>使用者介面

下列各節說明各種用來在 Xamarin Android 應用程式中撰寫使用者介面的工具和建立區塊。

## <a name="android-designerandroiduser-interfaceandroid-designerindexmd"></a>[Android Designer](~/android/user-interface/android-designer/index.md)

本節說明如何使用 Android Designer，以視覺化方式配置控制項並編輯屬性。 它也會說明如何使用設計工具來處理各種設定（例如主題、語言和裝置設定）的使用者介面和資源，以及如何針對橫向和縱向等替代視圖進行設計。

## <a name="material-themeandroiduser-interfacematerial-thememd"></a>[材料佈景主題](~/android/user-interface/material-theme.md)

*材質主題*是使用者介面樣式，可決定 Android 中視圖和活動的外觀與風格。 材質主題內建于 Android 中，因此系統 UI 以及應用程式會使用它。 本指南介紹材質設計原則，並說明如何使用內建材質主題或自訂主題來主題應用程式。

## <a name="user-profileandroiduser-interfaceuser-profilemd"></a>[使用者設定檔](~/android/user-interface/user-profile.md)

本指南說明如何存取裝置擁有者的個人設定檔，包括連絡人資料，例如裝置擁有者的名稱和電話號碼。

## <a name="splash-screenandroiduser-interfacesplash-screenmd"></a>[啟動顯示畫面](~/android/user-interface/splash-screen.md)

Android 應用程式需要一些時間才能啟動，特別是當應用程式第一次在裝置上啟動時。 啟動顯示畫面可能會顯示使用者的開始進度。 本指南說明如何為您的應用程式建立啟動顯示畫面。

## <a name="layoutsandroiduser-interfacelayoutsindexmd"></a>[版面配置](~/android/user-interface/layouts/index.md)

版面配置是用來定義使用者介面的視覺化結構。
`ListView` 和`RecyclerView`之類的配置是 Android 應用程式最基本的建立區塊。 一般而言，版面配置會使用`Adapter`來做為配置中的橋樑，而這些資料是用來填入配置中的資料項目。 本節說明如何使用`LinearLayout`、 `RelativeLayout`、 `TableLayout`、 `RecyclerView`和`GridView`等版面配置。

## <a name="controlsandroiduser-interfacecontrolsindexmd"></a>[控制項](~/android/user-interface/controls/index.md)

Android 控制項（*也稱為 widget*）是您用來建立使用者介面的 UI 元素。 本節說明如何使用按鈕、工具列、日期/時間選擇器、行事曆、spinners、交換器、快顯功能表、view 呼機和 web views 等控制項。
