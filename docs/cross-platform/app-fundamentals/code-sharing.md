---
title: 共用程式碼總覽
description: 本檔會比較跨平臺專案之間共用程式碼的不同方法：共用專案、可移植類別庫和 .NET Standard，包括各項各項的優點和缺點。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: b097a0549db6178576d9e10eb3282c88e96b9ddb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453294"
---
# <a name="sharing-code-overview"></a>共用程式碼總覽

_本檔會比較跨平臺專案之間共用程式碼的不同方法： .NET Standard、共用專案和可移植類別庫，包括各項的優點和缺點。_

有三種方法可以在跨平臺應用程式之間共用程式碼：

- [**.NET Standard 程式庫**](#Net_Standard) – .NET Standard 專案可以執行跨多個平臺共用的程式碼，而且可以視) 版本存取大量的 .net api (。 .NET Standard 1.0-1.6 可執行逐漸較大的 Api 集，而 .NET Standard 2.0 提供 .NET BCL 的最佳涵蓋範圍 (包括 Xamarin 應用程式) 中提供的 .NET Api。
- [**共用專案**](#Shared_Projects) –使用共用的資產專案類型來組織您的原始程式碼，並 `#if` 視需要使用編譯器指示詞來管理平臺特定需求。
- [**便攜類別庫**](#Portable_Class_Libraries) (已淘汰的) –可移植類別庫 (pcl) 可以使用通用的 API 介面，以多個平臺為目標，並使用介面來提供平臺特定的功能。 Visual Studio 使用 .NET Standard 的最新版本中的 Pcl 已被取代 &ndash; 。

程式碼共用策略的目標是要支援此圖所示的架構，其中單一程式碼基底可供多個平臺使用。

 ![共用程式碼應用程式架構](code-sharing-images/conceptualarchitecture.png "共用程式碼應用程式架構")

本文將比較可用的方法，協助您為應用程式選擇正確的專案類型。

<a name="Net_Standard"></a>

## <a name="net-standard-libraries"></a>.NET Standard 程式庫

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) 程式庫提供一組定義完善的基類庫，可在不同的專案類型中參考，包括 Xamarin. Android 和 xamarin 等跨平臺專案。 建議使用 .NET Standard 2.0，以達到與現有 .NET Framework 程式碼的最大相容性。

![.NET Standard 圖表](code-sharing-images/netstandard.png ".NET Standard 圖表")

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 重構作業一律會更新所有受影響的參考。
- .NET 基類庫 (BCL) 的較大介面區可供 PCL 設定檔使用。 尤其是，.NET Standard 2.0 與 .NET Framework 幾乎具有相同的 API 介面，建議用於新的應用程式並移植現有的 Pcl。

### <a name="disadvantages"></a>缺點

- 無法使用像這樣的編譯器指示詞 `#if __IOS__` 。

### <a name="remarks"></a>備註

.NET Standard 與 [PCL 相似](/dotnet/standard/net-standard#comparison-to-portable-class-libraries)，但使用較簡單的平臺支援模型，以及 BCL 中的類別數目更多。

<a name="Shared_Projects"></a>

## <a name="shared-projects"></a>共用的專案

[共用專案](~/cross-platform/app-fundamentals/shared-projects.md) 包含的程式碼檔案和資產會包含在任何參考它們的專案中。 共用專案不會自行產生編譯的輸出。

此螢幕擷取畫面顯示一個方案檔，其中包含適用于 Android、iOS 和 Windows)  (的三個應用程式專案，以及包含常見 c # 原始程式碼檔案的 **共用** 專案：

![共用的專案方案](code-sharing-images/sharedsolution.png "共用的專案方案")

概念架構如下圖所示，其中每個專案都包含所有共用的原始程式檔：

![共用的專案圖表](code-sharing-images/sharedassetproject.png "共用的專案圖表")

### <a name="example"></a>範例

支援 iOS、Android 和 Windows 的跨平臺應用程式需要每個平臺的應用程式專案。 通用程式碼存在於共用專案中。

範例解決方案會包含下列資料夾和專案 (專案名稱已針對表達選擇，您的專案不需要遵循下列命名方針) ：

- **共用** –共用的專案，包含所有專案通用的程式碼。
- **AppAndroid** – Xamarin. Android 應用程式專案。
- **AppiOS** – Xamarin iOS 應用程式專案。
- **AppWindows** – Windows 應用程式專案。

如此一來，三個應用程式專案就會共用相同的原始程式碼 (共用) 中的 c # 檔案。 共用程式碼的任何編輯都將在這三個專案之間共用。

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 您可以使用編譯器指示詞，以平臺為基礎來分支共用程式碼 (例如 使用  `#if __ANDROID__` ，如「  [建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) 」檔) 所述。
- 應用程式專案可以包含共用程式碼可利用的平臺特定參考 (例如  `Community.CsharpSqlite.WP7` 在適用于 Windows Phone) 的 Tasky 範例中使用。

### <a name="disadvantages"></a>缺點

- 會影響「非作用中」編譯器指示詞內程式碼的重構，不會更新這些指示詞內的程式碼。
- 不同于其他大部分的專案類型，共用的專案沒有 ' output ' 元件。 在編譯期間，會將檔案視為參考專案的一部分，並編譯至該元件。 如果您想要以元件的形式共用程式碼，.NET Standard 或便攜類別庫是較佳的解決方案。

<a name="Shared_Remarks"></a>

### <a name="remarks"></a>備註

適用于應用程式開發人員撰寫的程式碼，僅適用于在其應用程式中共用的程式碼 (，且不會散發給其他開發人員) 。

<a name="Portable_Class_Libraries"></a>

## <a name="portable-class-libraries"></a>可攜式類別庫

> [!TIP]
> 建議您透過便攜類別庫使用 .NET Standard 2.0 程式庫。

[這裡會詳細討論](~/cross-platform/app-fundamentals/pcl.md)便攜類別庫。

![便攜類別庫圖表](code-sharing-images/portableclasslibrary.png "便攜類別庫圖表")

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 重構作業一律會更新所有受影響的參考。

### <a name="disadvantages"></a>缺點

- Visual Studio 的最新版本已淘汰，建議您改為 .NET Standard 程式庫。 請參閱這項 PCL 與 .NET Standard 之間 [差異的說明](/dotnet/standard/net-standard#comparison-to-portable-class-libraries) 。
- 無法使用編譯器指示詞。
- 只有 .NET framework 的子集可供使用，取決於所選取的設定檔 (如需詳細資訊) ，請參閱  [PCL 簡介](~/cross-platform/app-fundamentals/pcl.md) 。

### <a name="remarks"></a>備註

Visual Studio 的最新版本中，PCL 範本會視為已淘汰。

## <a name="summary"></a>摘要

您所選擇的程式碼共用策略將由您的目標平臺驅動。 選擇最適合您專案的方式。

.NET Standard 是建立可共用的程式碼程式庫的最佳選擇， (特別是在 NuGet) 上發佈。 共用專案適用于計畫在其跨平臺應用程式中使用許多平臺特定功能的應用程式開發人員。

雖然 Visual Studio 中的 PCL 專案仍會繼續受到支援，但建議針對新的專案使用 .NET Standard。

## <a name="related-links"></a>相關連結

- [ (主檔案建立跨平臺應用程式) ](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky (github) 範例 ](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [使用 PCL (github) 的 Tasky 範例 ](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)