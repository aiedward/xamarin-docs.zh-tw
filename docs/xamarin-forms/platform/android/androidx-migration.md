---
title: AndroidX 遷移 Xamarin.Forms
description: 本文說明 AndroidX 存在的原因，以及如何在您的應用程式中遷移至 AndroidX Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 98884003-E65A-4EB4-842D-66CFE27344A4
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/13/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b24ab21bf7ae5812916a968da3a59169b975e33a
ms.sourcegitcommit: 86663f94f8eddb808eb4504cd32ddaf217b6406c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166624"
---
# <a name="androidx-migration-in-no-locxamarinforms"></a>AndroidX 遷移 Xamarin.Forms

AndroidX 取代 Android 支援程式庫。 本文說明 AndroidX 的存在、其影響的方式 Xamarin.Forms ，以及如何遷移您的應用程式以使用 AndroidX 程式庫。

> [!IMPORTANT]
> 如果您要將應用程式遷移至 Xamarin.Forms 5.0，請參閱 [如何? 將我的應用程式遷移至 Xamarin.Forms 5.0？](~/xamarin-forms/troubleshooting/questions/forms5-migration.md)。

## <a name="history-of-androidx"></a>AndroidX 的歷程記錄

Android 支援程式庫的建立是為了提供較舊版本 Android 的較新功能。 它是一種相容性層級，可讓開發人員使用在所有 Android 作業系統版本上可能不存在的功能，並針對較舊的版本進行正常的回退。 支援程式庫也包括便利性和 helper 類別、偵錯工具和公用程式工具，以及相依于其他支援程式庫類別的複雜類別。

雖然支援程式庫最初是單一的二進位檔，但它已成長並演進成一套程式庫，這對新式應用程式開發而言幾乎是不可或缺的。 以下是支援程式庫常用的一些功能：

- `Fragment`支援類別。
- `RecyclerView`用來管理長清單的。
- 使用超過65536方法的應用程式 Multidex 支援。
- `ActivityCompat` 類別。

AndroidX 取代了不再維護的支援程式庫-所有新的程式庫開發都會出現在 AndroidX 程式庫中。 AndroidX 是經過重新設計的程式庫，它會使用語義版本控制、更清楚的封裝名稱，以及更好的一般應用程式架構模式支援。 AndroidX 1.0.0 版是相當於支援程式庫版本28.0.0 的二進位檔。 如需從支援程式庫到 AndroidX 的類別對應完整清單，請參閱 developer.android.com 上的 [支援程式庫類別](https://developer.android.com/jetpack/androidx/migrate/class-mappings) 對應。

Google 建立了一個名為 Jetifier with AndroidX 的遷移程式。 Jetifier 會在組建程式期間檢查 jar 位元組程式碼，並在應用程式程式碼和相依性中，重新對應支援程式庫參考至其 AndroidX 對等專案。

在 Xamarin.Forms 應用程式中（如同在 Android JAVA 應用程式中），jar 相依性必須遷移至 AndroidX。 不過，您也必須遷移 Xamarin 系結，以指向正確的基礎 jar 檔案。 Xamarin.Forms 已新增4.5 版中自動 AndroidX 遷移的支援。

如需 AndroidX 的詳細資訊，請參閱 developer.android.com 上的 [AndroidX 總覽](https://developer.android.com/jetpack/androidx) 。

## <a name="automatic-migration-in-no-locxamarinforms"></a>中的自動遷移 Xamarin.Forms

若要自動遷移至 AndroidX， Xamarin.Forms Android 平臺專案必須：

- 將 Android API 的目標設為29或更高版本。
- 使用 Xamarin.Forms 4.5 版或更高版本。
- 對 Android 支援程式庫具有直接或可轉移的相依性。

當您在專案中確認這些設定之後，請在 Visual Studio 2019 中建立 Android 應用程式。 在建立程式期間，會檢查中繼語言 (IL) ，而且支援程式庫相依性和系結會以 AndroidX 相依性交換。 如果您的應用程式具有組建所需的所有 AndroidX 相依性，您將會注意到組建程式沒有任何差異。

> [!IMPORTANT]
> 手動遷移至 AndroidX 將會為您的應用程式產生最快的組建流程，而且是 AndroidX 遷移的建議方法。 這包括以 AndroidX 相依性取代支援程式庫相依性，以及更新您的程式碼以取用 AndroidX 類型。 如需詳細資訊，請參閱 [使用 AndroidX 類型](~/xamarin-forms/troubleshooting/questions/forms5-migration.md#use-androidx-types)。

如果偵測到不是專案一部分的 AndroidX 相依性，則會報告組建錯誤，指出哪些 AndroidX 封裝遺失。 範例組建錯誤如下所示：

```
Could not find 37 AndroidX assemblies, make sure to install the following NuGet packages:
- Xamarin.AndroidX.Lifecycle.LiveData
- Xamarin.AndroidX.Browser
- Xamarin.Google.Android.Material
- Xamarin.AndroidX.Legacy.Supportv4
You can also copy and paste the following snippit into your .csproj file:
 <PackageReference Include="Xamarin.AndroidX.Lifecycle.LiveData" Version="2.1.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Browser" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.Google.Android.Material" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Legacy.Support.V4" Version="1.0.0-rc1" />
```

遺漏的 NuGet 套件可以透過 Visual Studio 中的 NuGet 封裝管理員安裝，或藉由編輯您的 Android .csproj 檔案來安裝，以包含 `PackageReference` 錯誤中所列的 XML 專案。

解析遺漏的封裝之後，重建專案會載入缺少的封裝，而您的專案會使用 AndroidX 相依性進行編譯，而不是支援程式庫相依性。

> [!NOTE]
> 如果您的專案和專案相依性不參考 Android 支援程式庫，則遷移程式不會執行任何動作，也不會執行。

## <a name="related-links"></a>相關連結

- [如何? 將我的應用程式遷移至 Xamarin.Forms 5.0？](~/xamarin-forms/troubleshooting/questions/forms5-migration.md)
- Developer.android.com 上的[Android 支援程式庫總覽](https://developer.android.com/topic/libraries/support-library/index)
- Developer.android.com 的[AndroidX 總覽](https://developer.android.com/jetpack/androidx)
- [AndroidX 類別對應](https://github.com/xamarin/AndroidX/blob/master/mappings/androidx-class-mapping.csv)
- [AndroidX 元件](https://github.com/xamarin/AndroidX/blob/master/mappings/androidx-assemblies.csv)
