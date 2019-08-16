---
title: 更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 64ceacc37a303e69b0dd7073ec6547ed344af51d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523437"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件

## <a name="about-the-android-support-libraries"></a>關於 Android 支援程式庫

Google 已建立支援程式庫, 以將新功能提供給舊版 Android。 一般而言, 支援程式庫的名稱中會有版本號碼, 這是相容的最低 Android API 層級 (例如:支援-v4 只能用在 API 層級4和更高版本上。 如需詳細資訊, 請[Stack Overflow 討論](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13))。 

有兩個支援程式庫`Support-v4` : `Support-v13`和不能在相同的應用程式中一起使用, 也就是說, 它們是互斥的。 這是因為`Support-v13`實際包含的所有類型和`Support-v4`實作為。 如果您嘗試同時在相同的專案中參考, 則會遇到重複的類型錯誤。

## <a name="problems-with-referencing"></a>參考的問題

由於`Support-v4`已經變得很熱門, 因此有許多協力廠商程式庫現在會依賴它。 他們可能已選擇依賴支援 v13, 但更常見的方式是依賴_v4_ , 因為這會讓使用這些協力廠商程式庫的任何應用程式都能選擇支援 API 層級, 直到到4為止。

如果 Xamarin 協力廠商程式庫參考`Xamarin.Android.Support.v4.dll`系結`Support-v4`至, 則任何使用此程式庫的應用`Xamarin.Android.Support.v4.dll`程式也必須參考。 當同一個應用程式也想要使用系結至`Xamarin.Android.Support.v13.dll` `Support-v13`的部分功能時, 這會造成問題。 如果您參考這兩個系結, 您將會遇到重複的類型錯誤。

## <a name="type-forwarded-v4-binding-assembly"></a>類型轉送的 v4 系結元件

為了解決這個問題, 我們建立了一個沒有執行`Xamarin.Android.Support.v4.dll`的特殊元件, 而只是`[assembly: TypeForwardedTo (..)]`將所有`Support-v4`類型轉送到`Xamarin.Android.Support.v13.dll`元件中之實作為的屬性。

這表示開發人員可以在其應用程式中參考這個_類型轉送_的元件, 以滿足任何`Xamarin.Android.Support.v4.dll`協力廠商程式庫的參考, 同時`Xamarin.Android.Support.v13.dll`仍然允許在應用程式中使用。

## <a name="nuget-assistance"></a>NuGet 協助

開發人員可以手動新增必要的正確參考, 但在安裝 NuGet 套件時, 我們可以使用 NuGet 協助選擇正確的元件 (一般_v4_系結或類型轉送的_v4_元件)。

因此, `Xamarin.Android.Support.v4` NuGet 套件現在包含下列邏輯:

如果您的應用程式是以 API 層級 13 (Gingerbread 3.2) 或更高版本為目標:

* `Xamarin.Android.Support.v13`NuGet 會自動新增為相依性
* 在專案中會參考_轉送類型_ `Xamarin.Android.Support.v4.dll`

如果您的應用程式是以低於 API 層級13的任何專案為目標`Xamarin.Android.Support.v4.dll` , 則會取得您的專案中所參考的一般系結。

## <a name="do-i-have-to-use-support-v13"></a>我是否必須使用支援-v13？

如果您的應用程式是以 API 層級13或更高版本為`Xamarin Android Support-v4`目標, 而且您選擇`Xamarin Android Support v13`使用 nuget 套件, 則 nuget 套件是必要的相依性。

我們覺得應用程式大小的大幅增加 (兩個 .jar 檔案不同于 17kb) 值得一提的是相容性, 也是較少的麻煩。

如果您要 adamant 有關在`Support-v4`以 API 層級13或更高版本為目標的應用程式中使用, `.nupkg`您可以一律手動下載、將它解壓縮, 然後參考元件。
