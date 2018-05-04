---
title: 共用程式碼選項
description: 這份文件會比較跨平台專案之間共用程式碼的不同方法： 共用的專案 」、 「 可攜式類別庫，和 「.NET 標準，包括優點和缺點。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 97e6167304ebf50a149fce379eb34e8a46c8d3e8
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="sharing-code-overview"></a>共用程式碼的概觀

_這份文件會比較跨平台專案之間共用程式碼的不同方法： 共用的專案 」、 「 可攜式類別庫，和 「.NET 標準，包括優點和缺點。_

有三種跨平台應用程式之間共用程式碼的替代方法：

-   [**共用的專案**](#Shared_Projects) – 使用共用資產專案類型來組織您的原始程式碼，並視需要使用 #if 編譯器指示詞來管理特定平台需求。
-   [**可攜式類別庫**](#Portable_Class_Libraries) – 建立可攜式類別程式庫 (PCL) 以您想要支援的平台，並使用介面來提供特定平台功能。
-   [**.NET 標準程式庫**](#Net_Standard) – 標準.NET 專案 PCLs，需要使用的介面，以插入特定平台功能來運作方式雷同。

程式碼共用策略的目標是要支援此圖表中所示的結構，其中單一的程式碼基底可以利用多個平台。

 ![](code-sharing-images/conceptualarchitecture.png "共用程式碼應用程式架構")

這篇文章會比較三個方法可協助您選擇您的應用程式的正確的專案類型。

<a name="Shared_Projects" />

## <a name="shared-projects"></a>共用的專案

共用程式碼檔案最簡單的方式是使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

這個螢幕擷取畫面顯示 （適用於 Android、 iOS 和 Windows Phone），包含三個應用程式專案的方案檔**共用**包含通用 C# 原始程式碼檔的專案：

 ![](code-sharing-images/sharedsolution.png "共用的專案的方案")

概念性架構是以下列圖表中，其中每個專案都包含所有共用的來源檔案所示：

 ![](code-sharing-images/sharedassetproject.png "共用專案的圖表")


### <a name="example"></a>範例

針對每個平台支援 iOS、 Android 和 Windows Phone 的跨平台應用程式需要的應用程式專案。 常見的程式碼位於共用的專案。

下列資料夾和專案 （專案名稱已被選擇表達，為您的專案沒有遵循這些命名指導方針），就會包含範例方案：

-   **共用**– 包含所有專案通用程式碼的共用專案。
-   **AppAndroid** – Xamarin.Android 應用程式專案。
-   **AppiOS** – Xamarin.iOS 應用程式專案。
-   **AppWinPhone** – Windows Phone 應用程式專案。


在這種方式中的三個應用程式專案共用相同的原始碼 （C# 中的檔案共用）。 將共用的共用程式碼的任何編輯，所有的三個專案。


### <a name="benefits"></a>優點

-  可讓您在多個專案之間共用程式碼。
-  根據使用編譯器指示詞 （例如平台分支共用程式碼 使用`#if __ANDROID__`，如下所述[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文件)。
-  應用程式專案可以包含共用程式碼可以利用的平台專屬參考 (例如使用`Community.CsharpSqlite.WP7`Tasky 範例適用於 Windows Phone 中)。



### <a name="disadvantages"></a>缺點

-  不像其他大部分專案類型，共用的專案有沒有 '輸出' 組件。 在編譯期間，會參考專案的一部分來處理檔案，並將其編譯為該組件中。 如果您想要共用您的程式碼做為組件可攜式類別庫或.NET 標準則更好的解決方案。
-  會影響 'inactive' 編譯器指示詞內的程式碼的重構作業不會更新程式碼。


 <a name="Shared_Remarks" />

### <a name="remarks"></a>備註

應用程式開發人員撰寫僅適用於其應用程式中的共用 （以及不散發給其他開發人員） 的程式碼很好的解決方案。

 <a name="Portable_Class_Libraries" />


## <a name="portable-class-libraries"></a>可攜式類別庫


可攜式類別庫是[此處詳細討論](~/cross-platform/app-fundamentals/pcl.md)。

 ![](code-sharing-images/portableclasslibrary.png "可攜式類別庫圖表")


### <a name="benefits"></a>優點

-  可讓您在多個專案之間共用程式碼。
-  重構作業一定要更新所有受影響的參考。


### <a name="disadvantages"></a>缺點

-  無法使用編譯器指示詞。
-  只有.NET framework 的子集可供使用，取決於選取的設定檔 (請參閱[簡介 PCL](~/cross-platform/app-fundamentals/pcl.md)如需詳細資訊)。


### <a name="remarks"></a>備註

如果您打算與其他開發人員共用產生的組件的最佳解決方案。



<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>.NET 標準程式庫

標準.NET 是[此處詳細討論](~/cross-platform/app-fundamentals/net-standard.md)。

![](code-sharing-images/netstandard.png "標準.NET 圖表")

### <a name="benefits"></a>優點

-  可讓您在多個專案之間共用程式碼。
-  重構作業一定要更新所有受影響的參考。
-  較大的表面區域.NET 基底類別程式庫 (BCL) 可比 PCL 設定檔。

### <a name="disadvantages"></a>缺點

 -  無法使用編譯器指示詞。

### <a name="remarks"></a>備註

標準.NET 是類似 PCL 中，但與平台支援及類別從 BCL 更多的較簡單的模型。



## <a name="summary"></a>總結

程式碼共用您所選擇的策略會驅動您的目標平台。 選擇最適合您專案的方法。

PCL 或.NET 標準是很好的選擇來建立可共用的程式碼程式庫 （特別需由 NuGet 發行）。 共用的專案，適用於應用程式開發人員計劃跨平台應用程式中使用大量 plaform 特有的功能。


## <a name="related-links"></a>相關連結

- [建置跨平台應用程式 （主要的文件）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 範例 (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Tasky 範例使用 PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
