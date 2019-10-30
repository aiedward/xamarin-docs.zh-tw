---
title: 預設資源
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 7375121375cce3927e495dba33fe140f936ad0bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025064"
---
# <a name="default-resources"></a>預設資源

預設資源是不是任何特定裝置或外型規格特有的專案，因此，如果找不到更具體的資源，Android OS 就是預設選項。 因此，它們是最常建立的資源類型。 它們會根據其資源類型組織成**Resources**目錄的子目錄：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![預設資源檔](default-resources-images/01-resource-files-vs.png)

在上圖中，專案具有可繪製資源、版面配置和值（包含簡單值的 XML 檔案）的預設值。

以下提供完整的資源類型清單：

- **animator** &ndash; 描述屬性動畫的 XML 檔案。
   屬性動畫是在 API 層級11（Android 3.0）中引進，並提供物件屬性的動畫。 屬性動畫是更有彈性且功能更強大的方式，可描述任何物件類型上的動畫。

- **anim** &ndash; 描述*補間*動畫的 XML 檔案。 補間動畫是一系列的動畫指示，可執行 View 物件內容的轉換，例如，旋轉影像或增加文字大小。 補間動畫僅限於 View 物件。

- 描述色彩狀態清單 &ndash; XML 檔案的**色彩**。 若要瞭解色彩狀態清單，請考慮使用 UI widget，例如按鈕。
   可能會有不同的狀態，例如已按下或已停用，而且按鈕可能會隨著狀態的每項變更而變更色彩。 清單會以狀態清單表示。

- 可**繪製**&ndash; 繪製資源是圖形的一般概念，可以編譯到應用程式中，然後由 API 呼叫存取，或由其他 XML 資源參考。
   可繪製資源的一些範例包括點陣圖檔案（.png、.gif、.jpg）、特殊的可調整大小的點陣圖，稱為[九修補程式](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)、狀態清單、XML 中定義的一般圖形等等。

- 配置 **&ndash; 描述**使用者介面配置的 XML 檔案，例如清單中的活動或資料列。

- **功能表**&ndash; 描述應用程式功能表的 XML 檔案，例如 [*選項] 功能表*、*內容功能表*和*子功能表*。 如需功能表的範例，請參閱[快顯功能表示範](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)或[標準控制項](https://docs.microsoft.com/samples/xamarin/mobile-samples/standardcontrols/)範例。

- **原始**&ndash; 以原始的二進位格式儲存的任意檔案。 這些檔案會編譯成二進位格式的 Android 應用程式。

- **值**&ndash; 包含簡單值的 XML 檔案。 [值] 目錄中的 XML 檔案不會定義單一資源，而是可以定義多個資源。 例如，一個 XML 檔案可能會保存字串值的清單，而另一個 XML 檔案可能會保留色彩值的清單。

- **xml** &ndash; 與 .net 設定檔功能相似的 xml 檔案。 這些是可在執行時間由應用程式讀取的任意 XML。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![預設資源檔](default-resources-images/01-resource-files-xs.png)

在上圖中，專案具有可繪製資源、版面配置和值（包含簡單值的 XML 檔案）的預設值。

以下提供完整的資源類型清單：

- **animator** &ndash; 描述屬性動畫的 XML 檔案。
   屬性動畫是在 API 層級11（Android 3.0）中引進，並提供物件屬性的動畫。 屬性動畫是更有彈性且功能更強大的方式，可描述任何物件類型上的動畫。

- **anim** &ndash; 描述*補間*動畫的 XML 檔案。 補間動畫是一系列的動畫指示，可執行 View 物件內容的轉換，例如，旋轉影像或增加文字大小。 補間動畫僅限於 View 物件。

- 描述色彩狀態清單 &ndash; XML 檔案的**色彩**。 若要瞭解色彩狀態清單，請考慮使用 UI widget，例如按鈕。
   它可能會有不同的狀態，例如已按下或已停用，而且按鈕可能會隨著狀態的每項變更而變更色彩。 清單會以狀態清單表示。

- **字型**&ndash; 從 API 層級26開始，您可以在 Android 應用程式中將字體內嵌為資源。 支援程式庫26會將字型將至 API 層級14。 內嵌字型可讓應用程式直接從 XML 版面配置載入自訂字型，而不需要在使用前將它們匯入為資產。

- **mipmap** &ndash; 可繪製的資源是圖形的一般概念，可以編譯到應用程式中，然後由 API 呼叫存取，或由其他 XML 資源參考。
   可繪製資源的一些範例包括點陣圖檔案（.png、.gif、.jpg）、特殊的可調整大小的點陣圖，稱為[九修補程式](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)、狀態清單、XML 中定義的一般圖形等等。

- 配置 **&ndash; 描述**使用者介面配置的 XML 檔案，例如清單中的活動或資料列。

- **功能表**&ndash; 描述應用程式功能表的 XML 檔案，例如 [*選項] 功能表*、*內容功能表*和*子功能表*。 如需功能表的範例，請參閱[快顯功能表示範](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)或[標準控制項](https://docs.microsoft.com/samples/xamarin/mobile-samples/standardcontrols/)範例。

- **原始**&ndash; 以原始的二進位格式儲存的任意檔案。 這些檔案會編譯成二進位格式的 Android 應用程式。

- **值**&ndash; 包含簡單值的 XML 檔案。 [值] 目錄中的 XML 檔案不會定義單一資源，而是可以定義多個資源。 例如，一個 XML 檔案可能會保存字串值的清單，而另一個 XML 檔案可能會保留色彩值的清單。

- **xml** &ndash; 與 .net 設定檔功能相似的 xml 檔案。 這些是可在執行時間由應用程式讀取的任意 XML

-----
