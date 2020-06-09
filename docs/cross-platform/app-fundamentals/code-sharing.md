---
title: 共用程式碼總覽
description: 本檔會比較跨平臺專案之間共用程式碼的不同方法：共用專案、可移植的類別庫和 .NET Standard，包括每一項的優點和缺點。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: 5edfd8216892eb28a2b1ad14d3ccee1668b21a43
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571216"
---
# <a name="sharing-code-overview"></a>共用程式碼總覽

_本檔會比較跨平臺專案之間共用程式碼的不同方法： .NET Standard、共用的專案和可移植的類別庫，包括各項的優點和缺點。_

跨平臺應用程式之間共用程式碼的方法有三種：

- [**.NET Standard 程式庫**](#Net_Standard)– .NET Standard 專案可以執行程式碼，以在多個平臺間共用，而且可以存取大量的 .net api （視版本而定）。 .NET Standard 1.0-1.6 會執行逐漸較大的 Api 集合，而 .NET Standard 2.0 可提供 .NET BCL 的最佳涵蓋範圍（包括 Xamarin 應用程式中提供的 .NET Api）。
- [**共用專案**](#Shared_Projects)–使用共用資產專案類型來組織您的原始程式碼，並 `#if` 視需要使用編譯器指示詞來管理平臺特定的需求。
- [**可移植的類別庫**](#Portable_Class_Libraries)（已淘汰）-可移植的類別庫（pcl）可以使用通用的 API 介面，以多個平臺為目標，並使用介面來提供平臺特定的功能。 Pcl 已在最新版本的 Visual Studio &ndash; 使用 .NET Standard 取代。

程式碼共用策略的目標是要支援此圖所示的架構，其中的單一程式碼基底可供多個平臺使用。

 ![共用程式碼應用程式架構](code-sharing-images/conceptualarchitecture.png "共用程式碼應用程式架構")

本文會比較可用的方法，協助您為應用程式選擇正確的專案類型。

<a name="Net_Standard"></a>

## <a name="net-standard-libraries"></a>.NET Standard 程式庫

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)程式庫提供一組定義完善的基類程式庫，可以在不同的專案類型中加以參考，包括像是 Xamarin 和 xamarin 等跨平臺專案。 建議使用 .NET Standard 2.0，以取得與現有 .NET Framework 程式碼的最大相容性。

![.NET Standard 圖](code-sharing-images/netstandard.png ".NET Standard 圖")

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 重構作業一律會更新所有受影響的參考。
- .NET 基類庫（BCL）的較大介面區可供使用 PCL 設定檔。 特別是，.NET Standard 2.0 與 .NET Framework 幾乎具有相同的 API 介面，建議用於新的應用程式並移植現有的 Pcl。

### <a name="disadvantages"></a>缺點

- 無法使用類似的編譯器指示詞 `#if __IOS__` 。

### <a name="remarks"></a>備註

.NET Standard 與[PCL 類似](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries)，但具有較簡單的平臺支援模型，以及來自 BCL 的更多類別。

<a name="Shared_Projects"></a>

## <a name="shared-projects"></a>共用的專案

[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)包含程式碼檔案和資產，其包含在參考它們的任何專案中。 共用專案不會自行產生編譯的輸出。

這個螢幕擷取畫面顯示包含三個應用程式專案（適用于 Android、iOS 和 Windows）的方案檔，以及包含通用 c # 原始程式碼檔案的**共用**專案：

![共用的專案解決方案](code-sharing-images/sharedsolution.png "共用的專案解決方案")

概念架構如下圖所示，其中每個專案都包含所有共用原始檔：

![共用的專案圖表](code-sharing-images/sharedassetproject.png "共用的專案圖表")

### <a name="example"></a>範例

支援 iOS、Android 和 Windows 的跨平臺應用程式需要每個平臺都有一個應用程式專案。 通用程式碼存在於共用的專案中。

範例解決方案會包含下列資料夾和專案（已為表達選擇專案名稱，您的專案不需要遵循這些命名方針）：

- **共用**–包含所有專案通用程式碼的共用專案。
- **AppAndroid** – Xamarin. Android 應用程式專案。
- **AppiOS** – Xamarin iOS 應用程式專案。
- **AppWindows** – Windows 應用程式專案。

如此一來，這三個應用程式專案就會共用相同的原始程式碼（共用的 c # 檔案）。 共用程式碼的任何編輯都會在所有三個專案之間共用。

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 共用程式碼可以使用編譯器指示詞，根據平臺進行分支（例如， `#if __ANDROID__`如[建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)檔所述，使用。
- 應用程式專案可以包含共用程式碼可以使用的平臺特定參考（例如， `Community.CsharpSqlite.WP7` 在 Windows Phone 的 Tasky 範例中使用）。

### <a name="disadvantages"></a>缺點

- 會影響「非作用中」編譯器指示詞內程式碼的重構，不會更新這些指示詞內的程式碼。
- 不同于其他大部分的專案類型，共用的專案沒有 ' output ' 元件。 在編譯期間，檔案會被視為參考專案的一部分，並編譯成該元件。 如果您想要以元件的形式共用程式碼，則 .NET Standard 或可移植的類別庫是較佳的解決方案。

<a name="Shared_Remarks"></a>

### <a name="remarks"></a>備註

適合應用程式開發人員的解決方案，其撰寫僅適用于在應用程式中共用（而不是散發給其他開發人員）的程式碼。

<a name="Portable_Class_Libraries"></a>

## <a name="portable-class-libraries"></a>可攜式類別庫

> [!TIP]
> 建議使用 .NET Standard 2.0 程式庫，而不是可移植的類別庫。

[這裡會詳細討論](~/cross-platform/app-fundamentals/pcl.md)可移植的類別庫。

![可移植類別庫圖表](code-sharing-images/portableclasslibrary.png "可移植類別庫圖表")

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 重構作業一律會更新所有受影響的參考。

### <a name="disadvantages"></a>缺點

- 在最新版本的 Visual Studio 中已被取代，建議改用 .NET Standard 程式庫。 請參閱這份有關 PCL 與 .NET Standard 之間[差異的說明](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries)。
- 無法使用編譯器指示詞。
- 只有 .NET framework 的子集可供使用，由選取的設定檔決定（如需詳細資訊，請參閱[PCL 簡介](~/cross-platform/app-fundamentals/pcl.md)）。

### <a name="remarks"></a>備註

在最新版本的 Visual Studio 中，PCL 範本會被視為已被取代。

## <a name="summary"></a>總結

您所選擇的程式碼共用策略，將由您的目標平臺驅動。 選擇最適用于您專案的方法。

.NET Standard 是建立可共用的程式碼程式庫的最佳選擇（特別是在 NuGet 上發佈）。 共用專案適用于規劃在其跨平臺應用程式中使用許多平臺特定功能的應用程式開發人員。

雖然 Visual Studio 中繼續支援 PCL 專案，但建議您針對新的專案 .NET Standard。

## <a name="related-links"></a>相關連結

- [建立跨平臺應用程式（主要檔）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 範例（github）](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [使用 PCL 的 Tasky 範例（github）](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
