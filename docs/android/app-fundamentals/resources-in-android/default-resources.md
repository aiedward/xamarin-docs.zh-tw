---
title: 預設資源
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 119aa8b967ace858ee56f521624f6356e08a8b80
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="default-resources"></a>預設資源

預設資源即不屬於任何特定裝置或表單係數，因此是 Android 作業系統的預設選擇任何更特定的項目可以找到的資源。 因此，它們的資源，以建立最常見的類型。 它們組織成子目錄的**資源**目錄根據其資源類型：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![預設資源檔](default-resources-images/01-resource-files-vs.png)

在上圖中，專案沒有 drawable 資源、 配置和值 （這些 XML 檔案包含簡單的值） 的預設值。

資源類型的完整清單如下：

-  **動畫**&ndash;描述屬性動畫的 XML 檔案。
   屬性動畫 API 層級 11 (Android 3.0) 中導入，並提供該動畫物件上的屬性。 屬性動畫會更有彈性且功能強大的方式，來描述動畫在任何類型的物件。

-  **anim** &ndash;描述的 XML 檔案*tween*動畫。 Tween 動畫是文字的一系列的動畫指示的檢視物件或範例中，旋轉的內容上執行轉換，映像或成長大小。 Tween 動畫會限制為僅檢視物件。

-  **色彩**&ndash;描述狀態色彩的清單的 XML 檔案。 若要了解色彩狀態清單，請考慮 UI 小工具 （例如按鈕）。
   它可能有不同的狀態，例如按下或停用，以及按鈕可能會變更色彩與每個狀態中的變更。 表示清單狀態清單中。

-  **drawable** &ndash; Drawable 資源是圖形可編譯成應用程式，然後透過 API 呼叫或參考其他 XML 資源的一般概念。
   Drawables 的一些範例包括點陣圖檔案 （.png、.gif、.jpg）、 特殊可調整大小的點陣圖，又稱為[九修補程式](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，狀態會列出，等 XML 中定義的泛型圖形。
 
-  **版面配置**&ndash;描述使用者介面版面配置，例如活動或清單中的資料列的 XML 檔案。

-  **功能表** &ndash; XML 檔案，例如描述應用程式功能表*選項功能表*，*操作功能表*，和*子功能表*。 如需功能表的範例，請參閱[快顯功能表示範](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[標準控制項](https://developer.xamarin.com/samples/mobile/StandardControls/)範例。

-  **原始**&ndash;會儲存在其原始的二進位形式的任意檔案。 這些檔案會編譯成二進位格式的 Android 應用程式。

-  **值**&ndash;包含簡單的值的 XML 檔案。 值目錄中的 XML 檔案不會定義單一資源，而改為可以定義多個資源。 例如一個 XML 檔可能含有字串值的清單，而另一個 XML 檔案可能含有的色彩值清單。

-  **xml** &ndash;在函式類似於.NET 設定檔的 XML 檔案。 這些是可以在執行階段讀取應用程式的任意 XML


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![預設資源檔](default-resources-images/01-resource-files-xs.png)

在上圖中，專案沒有 drawable 資源、 配置和值 （這些 XML 檔案包含簡單的值） 的預設值。

資源類型的完整清單如下：

-  **動畫**&ndash;描述屬性動畫的 XML 檔案。
   屬性動畫 API 層級 11 (Android 3.0) 中導入，並提供該動畫物件上的屬性。 屬性動畫會更有彈性且功能強大的方式，來描述動畫在任何類型的物件。

-  **anim** &ndash;描述的 XML 檔案*tween*動畫。 Tween 動畫是文字的一系列的動畫指示的檢視物件或範例中，旋轉的內容上執行轉換，映像或成長大小。 Tween 動畫會限制為僅檢視物件。

-  **色彩**&ndash;描述狀態色彩的清單的 XML 檔案。 若要了解色彩狀態清單，請考慮 UI 小工具 （例如按鈕）。
   可能會有不同的狀態，例如按下或停用，以及按鈕可能會變更色彩與每個狀態中的變更。 表示清單狀態清單中。

-  **字型** &ndash; API 層級 26 從開始，便可在 Android 應用程式中將字型內嵌為資源。 支援文件庫 26 將應用程式開發介面層級 14 backport 字型。 內嵌字型可讓應用程式

-  **mipmap** &ndash; Drawable 資源是圖形可編譯成應用程式，然後透過 API 呼叫或參考其他 XML 資源的一般概念。
   Drawables 的一些範例包括點陣圖檔案 （.png、.gif、.jpg）、 特殊可調整大小的點陣圖，又稱為[九修補程式](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，狀態會列出，等 XML 中定義的泛型圖形。

-  **版面配置**&ndash;描述使用者介面版面配置，例如活動或清單中的資料列的 XML 檔案。

-  **功能表** &ndash; XML 檔案，例如描述應用程式功能表*選項功能表*，*操作功能表*，和*子功能表*。 如需功能表的範例，請參閱[快顯功能表示範](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[標準控制項](https://developer.xamarin.com/samples/mobile/StandardControls/)範例。

-  **原始**&ndash;會儲存在其原始的二進位形式的任意檔案。 這些檔案會編譯成二進位格式的 Android 應用程式。

-  **值**&ndash;包含簡單的值的 XML 檔案。 值目錄中的 XML 檔案不會定義單一資源，而改為可以定義多個資源。 例如一個 XML 檔可能含有字串值的清單，而另一個 XML 檔案可能含有的色彩值清單。

-  **xml** &ndash;在函式類似於.NET 設定檔的 XML 檔案。 這些是可以在執行階段讀取應用程式的任意 XML

-----
