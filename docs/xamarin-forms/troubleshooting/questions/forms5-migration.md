---
title: 如何? 將我的應用程式遷移至 Xamarin.Forms 5.0？
description: 如何將您的應用程式遷移至 Xamarin.Forms 5.0，並將焦點放在 UWP 上的 Android 上。
ms.assetid: AD04FEE9-B8F5-4CA5-AB31-EF1225867E4B
ms.prod: xamarin
ms.technology: xamarin-forms
ms.topic: troubleshooting
author: davidbritch
ms.author: dabritch
ms.date: 10/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f93d20dac789abed57f8f41bf41778ad50a5fb5
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972314"
---
# <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>如何? 將我的應用程式遷移至 Xamarin.Forms 5.0？

Xamarin.Forms 5.0 包含下列重大變更：

- `Expander` 已移至 Xamarin 社區工具組。 如需詳細資訊，請參閱[移 Xamarin.Forms 自的功能](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms)。
- `MediaElement` 已移至 Xamarin 社區工具組。 如需詳細資訊，請參閱[移 Xamarin.Forms 自的功能](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms)。
- 已從移除 DataPages 和相關聯的專案 Xamarin.Forms 。
- `MasterDetailPage` 已經重新命名為 `FlyoutPage`。 同樣地，列舉型別已重新 `MasterBehavior` 命名為 `FlyoutLayoutBehavior` 。
- 已 `UIWebView` 從 iOS 移除的參考 Xamarin.Forms 。
- 已移除對 Visual Studio 2017 的支援。
- XFCorePostProcessor。已移除工作。 此專案插入了 IL，以維護 Xamarin.Forms 2.5 相容性。

此外，以5.0 建立的 Android 和 UWP 專案 Xamarin.Forms 都需要更新。

## <a name="android"></a>Android

以5.0 建立的 android 專案 Xamarin.Forms 需要您將 AndroidX (Android 10.0) 平臺安裝到您的開發環境。 這可以透過 Android SDK 管理員來完成。 如需 AndroidX 的詳細資訊，請參閱[中 Xamarin.Forms 的 AndroidX 遷移](~/xamarin-forms/platform/android/androidx-migration.md)。

Android 專案接著將需要數個更新才能正確建立。

### <a name="minimum-targetframeworkversion"></a>最小 TargetFrameworkVersion

Xamarin.Forms 5.0 需要 Android 專案的最低目標 framework 版本 10.0 (AndroidX) 。 您可以在 Visual Studio 或在 Android .csproj 檔案中設定目標 framework 版本：

```xml
<TargetFrameworkVersion>v10.0</TargetFrameworkVersion>
```

如果不符合此最低需求，則會產生組建錯誤：

```
error XF005: The $(TargetFrameworkVersion) for MyProject.Android (v9.0) is less than the minimum required $(TargetFrameworkVersion) for Xamarin.Forms (10.0). You need to increase the $(TargetFrameworkVersion) for MyProject.Android.
```

### <a name="minimum-targetsdkversion"></a>最小 TargetSDKVersion

AndroidX 需要您的 Android 資訊清單將設定 `targetSdkVersion` 為 29 +：

```xml
<uses-sdk android:minSdkVersion="21" android:targetSdkVersion="29" />
```

若未這麼做，則會將 `targetSdkVersion` 設定為 `minSdkVersion` 。 此外，在某些情況下， `Android.Views.InflateException` 如果未正確設定，則會產生 a `targetSdkVersion` ：

```
Android.View.InflateException has been thrown.

Binary XML file line #1 in com.companyname.myproject:layout/toolbar: Binary XML file line #1 in com.companyname.myproject:/layout/toolbar: Error inflating class android.support.v7.widget.Toolbar
```

> [!NOTE]
> 在 Visual Studio 2019 中， `targetSdkVersion` 當目標 framework 版本設定為10.0 時，會自動更新 Android 資訊清單以指定 API 29。

### <a name="automatic-migration-to-androidx"></a>自動遷移至 AndroidX

如果您的 Android 專案參考任何 Android 支援程式庫（可作為直接相依性或可轉移的相依性），則在建立程式期間，這些支援程式庫相依性和系結會自動交換 AndroidX 相依性。 如需自動 AndroidX 遷移的詳細資訊，請參閱[中 Xamarin.Forms 的自動遷移](~/xamarin-forms/platform/android/androidx-migration.md#automatic-migration-in-xamarinforms)。

### <a name="manual-migration-to-androidx"></a>手動遷移至 AndroidX

如果您的 Android 專案在 Android 支援程式庫上沒有直接或可轉移的相依性，但仍嘗試透過程式碼使用支援程式庫類型，則您必須手動將應用程式遷移至 AndroidX。 這可以藉由使用 AndroidX 類型，以及藉由移除任何未自訂的 .AXML 檔案來完成。

> [!TIP]
> 手動遷移至 AndroidX 將可為您的應用程式產生最快的組建流程。

#### <a name="use-androidx-types"></a>使用 AndroidX 類型

AndroidX 取代 Android 支援程式庫，因此 Android 支援程式庫類型的任何參考都必須取代為 AndroidX 類型的參考。

將您的 `using` 語句更新為使用 `AndroidX` 命名空間，而非命名空間，即可完成這項作業 `Android.Support` 。 下表列出從 Android 支援程式庫移至 AndroidX 時的一些常見命名空間變更：

| Android 支援程式庫命名空間 | AndroidX 命名空間 |
| --- | --- |
| `Android.Support.V4.App` | `AndroidX.Core.App` |
| `Android.Support.V4.Content` | `AndroidX.Core.Content` |
| `Android.Support.V4.App` | `AndroidX.Fragment.App` |
| `Android.Support.V7.App` | `AndroidX.AppCompat.App` |
| `Android.Support.V7.Widget` | `AndroidX.AppCompat.Widget` |

如需從支援程式庫到 AndroidX 之類別對應的完整清單，請參閱 developer.android.com 上的 [支援程式庫類別](https://developer.android.com/jetpack/androidx/migrate/class-mappings) 對應。

#### <a name="remove-axml-files"></a>移除 .AXML 檔案

您應該刪除 Android 專案中的任何 .AXML 檔案，但前提是它不使用自訂的 .AXML 檔案。 刪除之後，應從您的類別中移除下列幾行 `MainActivity` ：

```csharp
TabLayoutResource = Resource.Layout.Tabbar;
ToolbarResource = Resource.Layout.Toolbar;
```

> [!IMPORTANT]
> 您應更新具有自訂 .AXML 檔案的 Android 專案，讓這些檔案使用 AndroidX 類型。

## <a name="uwp"></a>UWP

Xamarin.Forms 5.0 建議 UWP 專案的目標平臺版本為 >= 10.0.18362.0。 您可以在 Visual Studio 或 UWP .csproj 檔案中設定目標平臺版本：

```xml
<TargetPlatformVersion Condition=" '$(TargetPlatformVersion)' == '' ">10.0.18362.0</TargetPlatformVersion>
```

如果您的 UWP 專案使用較低的目標平臺版本，則會產生組建警告。

## <a name="related-links"></a>相關連結

- [功能移動來源 Xamarin.Forms](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms)
- [AndroidX 遷移 Xamarin.Forms](~/xamarin-forms/platform/android/androidx-migration.md)
