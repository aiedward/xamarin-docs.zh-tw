---
title: 更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: a990d933c258812b2b3d3374fb6435af06f729ea
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671789"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件

## <a name="about-the-android-support-libraries"></a>關於 Android 支援程式庫

Google 已建立將新功能提供給舊版 Android 支援程式庫。 一般情況下，支援程式庫時，會在其名稱，也就是最低 Android API 層級相容於指定版本號碼 (例如：支援 v4 僅適用於在 API 層級 4 和更新版本。 在此的詳細資訊[Stack Overflow 討論](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13))。 

支援程式庫的兩個：`Support-v4`和`Support-v13`不適用於一起在相同的應用程式，也就是它們互斥。 這是因為`Support-v13`實際包含的所有類型和實作`Support-v4`。 如果您嘗試參考相同專案中，您會遇到重複的型別錯誤。

## <a name="problems-with-referencing"></a>參考的問題

因為`Support-v4`日益受歡迎的特質，第 3 個廠商程式庫很多都變成相依於它。 它們也可以選擇以相反的取決於支援 v13 但更常見取決於_v4_因為，讓任何使用這些第 3 個合作對象程式庫應用程式支援到 4 的 API 層級的選項。

如果 Xamarin 第 3 方程式庫參考`Xamarin.Android.Support.v4.dll`繫結至`Support-v4`，使用此程式庫的任何應用程式還必須參考`Xamarin.Android.Support.v4.dll`。 這會成為問題時相同的應用程式也要使用的功能的一些`Xamarin.Android.Support.v13.dll`繫結至`Support-v13`。 如果您參考兩種繫結時，您會遇到重複的型別錯誤。

## <a name="type-forwarded-v4-binding-assembly"></a>類型轉送 v4 繫結組件

若要解決此問題，我們建立了一個特殊`Xamarin.Android.Support.v4.dll`組件有任何實作，但只要`[assembly: TypeForwardedTo (..)]`屬性會轉送所有`Support-v4`內實作的型別`Xamarin.Android.Support.v13.dll`組件。

這表示開發人員可以參考這_類型轉送_的參考會同時滿足其應用程式中的組件`Xamarin.Android.Support.v4.dll`的任何第 3 個合作對象程式庫，同時仍然允許`Xamarin.Android.Support.v13.dll`用於應用程式。

## <a name="nuget-assistance"></a>NuGet 取得協助

雖然開發人員可以手動加入正確的參考必要的我們就能夠使用 NuGet 來協助選擇正確的組件 (可能是正常_v4_繫結或類型轉送_v4_組件) 時安裝 NuGet 套件。

因此， `Xamarin.Android.Support.v4` NuGet 封裝現在包含下列邏輯：

如果您的應用程式的目標 API 層級 13 (Gingerbread 3.2) 或更高版本：

*   `Xamarin.Android.Support.v13` NuGet 會自動新增為相依性
*   _型別轉送_`Xamarin.Android.Support.v4.dll`會參考專案中

如果您的應用程式的目標 API 層級 13 比任何項目，您會收到一般`Xamarin.Android.Support.v4.dll`在專案中參考的繫結。

## <a name="do-i-have-to-use-support-v13"></a>我必須使用支援 v13 嗎？

如果您的應用程式的目標 API 層級 13 或更高版本，而且您選擇使用`Xamarin Android Support-v4`NuGet 套件，則`Xamarin Android Support v13`NuGet 套件是必要的相依性。

我們覺得很小 （兩個的.jar 檔案相差 17 kb） 的應用程式大小增加是非常值得的相容性，則會導致較少的問題。

如果您是非常堅持不需使用`Support-v4`應用程式中的目標 API 層級 13 或更高版本，您可以一律手動下載`.nupkg`、 將它解壓縮，並參考組件。
