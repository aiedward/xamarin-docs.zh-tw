---
title： "AndroidX 遷移 in Xamarin.Forms " 描述： "本文說明 AndroidX 存在的原因，以及如何在您的應用程式中遷移至 AndroidX Xamarin.Forms 。
assetid： 98884003-E65A-4EB4-842D-66CFE27344A4 ms. 技術： xamarin-表單作者： profexorgeek ms. author： jusjohns ms. date： 01/22/2020 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="androidx-migration-in-xamarinforms"></a>中的 AndroidX 遷移Xamarin.Forms

AndroidX 取代了 Android 支援程式庫。 本文說明為什麼 AndroidX 存在、它會如何影響 Xamarin.Forms ，以及如何將應用程式遷移以使用 AndroidX 程式庫。

## <a name="history-of-androidx"></a>AndroidX 的歷程記錄

Android 支援程式庫是為了在較舊版本的 Android 上提供較新的功能而建立的。 它是一種相容性層，可讓開發人員使用可能不存在於所有 Android 作業系統版本上的功能，並針對較舊的版本進行正常的回退。 支援程式庫也包括便利性和 helper 類別、偵錯工具和公用程式工具，以及相依于其他支援程式庫類別以運作的複雜類別。

雖然支援程式庫原本是一個二進位檔，但它已成長並進化成一套程式庫，這對現代化應用程式開發而言幾乎是必要的。 以下是支援程式庫中一些常用的功能：

- `Fragment`支援類別。
- `RecyclerView`，用於管理長清單。
- 使用超過65536個方法 Multidex 應用程式的支援。
- `ActivityCompat` 類別。

AndroidX 是取代支援程式庫的功能，不再維護此功能-所有新的程式庫開發都會在 AndroidX 程式庫中進行。 AndroidX 是重新設計的程式庫，它會使用語義版本設定、更清楚的封裝名稱，以及對常見應用程式架構模式的更佳支援。 AndroidX 1.0.0 版是二進位檔，等同于支援程式庫版本28.0.0。 如需從支援程式庫至 AndroidX 之類別對應的完整清單，請參閱 developer.android.com 上的[支援程式庫類別](https://developer.android.com/jetpack/androidx/migrate/class-mappings)對應。

Google 已使用 AndroidX 建立名為 Jetifier 的遷移程式。 此 Jetifier 會在建立程式期間檢查 jar 位元組程式碼，並在應用程式程式碼和相依性中，將支援程式庫參考重新對應到其對等的 AndroidX。

在 Xamarin.Forms 應用程式中，就像在 Android JAVA 應用程式中一樣，jar 相依性必須遷移至 AndroidX。 不過，您也必須遷移 Xamarin 系結，以指向正確的基礎 jar 檔案。 Xamarin.Forms已新增在4.5 版中自動 AndroidX 遷移的支援。

如需 AndroidX 的詳細資訊，請參閱 developer.android.com 上的[AndroidX 總覽](https://developer.android.com/jetpack/androidx)。

## <a name="automatic-migration-in-xamarinforms"></a>中的自動遷移Xamarin.Forms

若要自動遷移至 AndroidX， Xamarin.Forms 專案必須：

- 以 Android API 版本29或更高的目標。
- 使用 Xamarin.Forms 4.5 或更高版本。

在您的專案中確認這些設定之後，請在 Visual Studio 2019 中建立 Android 應用程式。 在建立過程中，會檢查中繼語言（IL），並支援程式庫相依性和系結與 AndroidX 相依性交換。 如果您的應用程式具有組建所需的所有 AndroidX 相依性，您將會注意到組建程式中沒有任何差異。

> [!NOTE]
> 您必須在專案中保留支援程式庫的參考。 這些是用來在遷移程式檢查產生的 IL 並轉換相依性之前，用來編譯應用程式。

如果偵測到的 AndroidX 相依性不是專案的一部分，則會報告組建錯誤，指出遺失哪些 AndroidX 套件。 範例組建錯誤如下所示：

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

您可以透過 Visual Studio 中的 NuGet 套件管理員安裝遺失的 NuGet 套件，或編輯您的 Android .csproj 檔案來安裝，以包含 `PackageReference` 錯誤中列出的 XML 專案。

解決遺漏的套件之後，重建專案會載入遺失的封裝，而您的專案會使用 AndroidX 相依性來編譯，而不是支援程式庫相依性。

> [!NOTE]
> 如果您的專案和專案相依性不參考 Android 支援程式庫，則遷移程式不會執行任何動作，也不會執行。

## <a name="related-links"></a>相關連結

- Developer.android.com 上的[Android 支援程式庫總覽](https://developer.android.com/topic/libraries/support-library/index)
- Developer.android.com 上的[AndroidX 總覽](https://developer.android.com/jetpack/androidx)
