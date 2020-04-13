---
title: 更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: c74cac6a6d669385855999a565711a3fdc85f3b7
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019534"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件

## <a name="about-the-android-support-libraries"></a>關於 Android 支援庫

谷歌已經創建了支援庫,使新功能提供給舊版本的Android。 通常,支援庫的名稱中給出一個版本號,這是它們相容的最低 Android API 級別(例如:支援 v4 只能在 API 級別 4 及以上上使用。 在此[堆疊溢出討論中](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)的詳細資訊。 

兩個支援庫:`Support-v4``Support-v13`不能 在同一應用中一起使用,也就是說它們是互斥的。 這是因為`Support-v13`實際上`Support-v4`包含的所有類型和實現。 如果在同一項目中嘗試並引用兩者,則會遇到重複的類型錯誤。

## <a name="problems-with-referencing"></a>引言問題

由於`Support-v4`已經變得如此受歡迎,許多第三方圖書館現在依賴於它。 他們本可以選擇轉而依賴 Support-v13,但更常依賴_v4,_ 因為這使使用這些第三方庫的任何應用可以選擇支援 API 級別,一直支援到 4。

如果 Xamarin 第三方`Xamarin.Android.Support.v4.dll`庫引用`Support-v4`繫結到 ,則使用此函式庫`Xamarin.Android.Support.v4.dll`的任何應用程式也必須參考 。 當同一應用還希望使用綁定到`Xamarin.Android.Support.v13.dll``Support-v13`的一些功能時,這將成為問題。 如果引用這兩個綁定,則會遇到重複的類型錯誤。

## <a name="type-forwarded-v4-binding-assembly"></a>型態轉發 v4 系統執行程式集

為了解決這個問題,我們創建了一`Xamarin.Android.Support.v4.dll`個沒有實現的特殊程式集,而只是`[assembly: TypeForwardedTo (..)]`將所有`Support-v4`類型轉發到`Xamarin.Android.Support.v13.dll`程式集中的實現的屬性。

這意味著開發人員可以在其應用中引用此_類型轉發_程式集,這將滿足任何第三方庫`Xamarin.Android.Support.v4.dll`對的引用,同時仍`Xamarin.Android.Support.v13.dll`允許在應用中使用。

## <a name="nuget-assistance"></a>NuGet 援助

雖然開發人員可以手動添加所需的正確引用,但我們可以在安裝 NuGet 包時使用 NuGet 幫助選擇正確的程式集(正常_v4_綁定或類型轉發_v4_程式集)。

因此,NuGet`Xamarin.Android.Support.v4`套件現在包含以下邏輯:

如果應用的目標是 API 級別 13(金餅 3.2)或更高版本:

* `Xamarin.Android.Support.v13`NuGet 將自動新增為相依項
* 在項目中參考_已輸入的型態轉寄_`Xamarin.Android.Support.v4.dll`

如果應用的目標是低於 API 級別 13 的任何內容,您將`Xamarin.Android.Support.v4.dll`獲得專案中引用的正常綁定。

## <a name="do-i-have-to-use-support-v13"></a>我必須使用支援 v13 嗎?

如果應用的目標是 API 級別 13 或更高`Xamarin Android Support-v4`版本,並且選擇`Xamarin Android Support v13`使用 NuGet 包 ,則 NuGet 包是必需的依賴項。

我們覺得應用程式大小的非常小的增加(兩個 .jar 檔相差 17kb)非常值得相容性和更少的麻煩,它導致。

如果您堅持在針對 API`Support-v4`級別 13 或更高的應用中使用,則始終`.nupkg`可以手動下載 ,提取它並引用程式集。
