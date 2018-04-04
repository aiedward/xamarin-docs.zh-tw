---
title: 更聰明的 Xamarin Android 支援 v4 / v13 NuGet 封裝
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 47ae63fbd7062be97be04bfc1f1244ec63a1c5bd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更聰明的 Xamarin Android 支援 v4 / v13 NuGet 封裝

## <a name="about-the-android-support-libraries"></a>Android 支援程式庫

Google 已建立要用於 Android 的舊版本的新功能的支援程式庫。 一般情況下，支援程式庫時，會在其名稱，這是最低 Android 它們彼此相容的應用程式開發介面層級獲得版本號碼 (例如： 支援 v4 僅能在應用程式開發介面層級 4 和更新版本。 在這個進一歩[堆疊溢位討論](http://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13))。 

支援程式庫的兩個：`Support-v4`和`Support-v13`不能使用一起在相同的應用程式，也就是說，它們是互斥。 這是因為`Support-v13`實際包含的所有類型和實作`Support-v4`。 如果您再次嘗試同時在相同的專案參考您將會遇到重複的型別錯誤。

## <a name="problems-with-referencing"></a>參考的問題

因為`Support-v4`變得因此受歡迎，許多第 3 個合作對象程式庫現在依存於此。 它們也可以選擇支援 v13 倚賴相反地，但更常見取決於_v4_因為，授與任何使用這些第 3 個合作對象文件庫的應用程式支援應用程式開發介面層級，一直到 4 的選項。

如果 Xamarin 3rd 合作對象程式庫參考`Xamarin.Android.Support.v4.dll`繫結至`Support-v4`，任何使用此程式庫的應用程式必須參照`Xamarin.Android.Support.v4.dll`。 這會成為問題時相同的應用程式也會想要使用的某些功能從`Xamarin.Android.Support.v13.dll`繫結至`Support-v13`。 如果您參考兩種繫結時，您將會遇到重複的型別錯誤。

## <a name="type-forwarded-v4-binding-assembly"></a>類型轉送 v4 繫結組件

若要解決這個問題，我們建立了一個特殊`Xamarin.Android.Support.v4.dll`組件的不具任何實作中，但只要`[assembly: TypeForwardedTo (..)]`轉送的所有屬性`Support-v4`內實作的型別`Xamarin.Android.Support.v13.dll`組件。

這表示開發人員可以參考這個_類型轉送_會滿足參考其應用程式中的組件`Xamarin.Android.Support.v4.dll`由任何第 3 個合作對象的程式庫，同時，仍允許`Xamarin.Android.Support.v13.dll`應用程式中使用。

## <a name="nuget-assistance"></a>NuGet 的協助

雖然開發人員無法手動新增所需之正確的參考，我們就能使用 NuGet 來協助選擇正確的組件 (可能是一般_v4_繫結或類型轉送_v4_組件) 時安裝 NuGet 套件。

因此， `Xamarin.Android.Support.v4` NuGet 封裝現在包含下列邏輯：

如果您的應用程式的目標應用程式開發介面層級 13 (Gingerbread 3.2) 或更高版本：

*   `Xamarin.Android.Support.v13` NuGet 會自動新增為相依性
*   _類型轉送_`Xamarin.Android.Support.v4.dll`會參考專案中

如果您的應用程式的目標應用程式開發介面層級 13 低於任何項目，則會收到一般`Xamarin.Android.Support.v4.dll`在專案中參考的繫結。

## <a name="do-i-have-to-use-support-v13"></a>必須使用支援 v13 嗎？

如果您的應用程式的目標應用程式開發介面層級 13 或更高版本，而且您選擇使用`Xamarin Android Support-v4`NuGet 封裝，然後在`Xamarin Android Support v13`NuGet 封裝是必要的相依性。

我們覺得很小 （兩個 d 檔案不同 17 kb） 的應用程式大小增加是值得相容性，則會導致較少的困擾。

如果您是使用相關 adamant`Support-v4`應用程式中為目標的應用程式開發介面層級 13 或更高版本，您可以手動下載`.nupkg`、 將它解壓縮，並參考組件。
