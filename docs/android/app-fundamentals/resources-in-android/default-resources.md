---
title: 預設資源
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 20865b71cce16f57b84a1c54986bd84180d3e190
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013166"
---
# <a name="default-resources"></a>預設資源

預設資源是不屬於任何特定裝置或外型規格，因此 Android os 的預設選擇如果沒有更特定的項目可以找到資源。 因此，它們要建立的資源最常見的類型。 它們會組織成目錄的子目錄**資源**目錄根據其資源類型：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![預設資源檔](default-resources-images/01-resource-files-vs.png)

在上圖中，專案會有可繪製資源、 配置和值 （包含簡單值 XML 檔案） 的預設值。

以下提供的資源類型的完整清單：

-  **動畫**&ndash;描述屬性動畫的 XML 檔案。
   屬性動畫 API 層級 11 (Android 3.0) 中導入，並提供的物件上屬性的動畫。 屬性動畫會更有彈性且功能強大的方式，來描述任何類型之物件的動畫。

-  **anim** &ndash;描述的 XML 檔案*tween*動畫。 Tween 動畫是一系列的動畫指示檢視物件或範例中，循環的內容上執行轉換，映像，或增加文字的大小。 Tween 動畫會限制為只能檢視物件。

-  **色彩**&ndash;描述色彩狀態清單的 XML 檔案。 若要了解色彩狀態清單，請考慮 UI widget，例如按鈕。
   它可能會有不同的狀態，例如按下或停用，以及按鈕可能會變更色彩與每個狀態中的變更。 清單是以表示狀態清單。

-  **可繪製**&ndash;可繪製資源是圖形，可以編譯成應用程式，然後存取的 API 呼叫或參考其他 XML 資源的一般概念。
   可繪製資源來的一些範例包括點陣圖檔 （.png、.gif、.jpg）、 特殊且可調整大小的點陣圖，稱為[九個修補程式](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，狀態會列出，泛型等 XML 中定義的圖形。
 
-  **版面配置**&ndash;描述使用者介面版面配置，例如活動或清單中的資料列的 XML 檔案。

-  **功能表** &ndash; XML 檔案，例如描述應用程式功能表*選項功能表*，*快顯功能表*，以及*子功能表*。 如需功能表的範例，請參閱 <<c0> [ 快顯功能表示範](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[標準控制項](https://developer.xamarin.com/samples/mobile/StandardControls/)範例。

-  **原始**&ndash;會儲存在其原始的二進位格式的任意檔案。 這些檔案會編譯成二進位格式的 Android 應用程式。

-  **值**&ndash;包含簡單值的 XML 檔案。 值的目錄中的 XML 檔案不會定義單一資源，但可以改為定義多個資源。 例如一個 XML 檔案中可能含有字串值的清單，而另一個 XML 檔案可能會保存一份色彩值。

-  **xml** &ndash;函式類似於.NET 設定檔的 XML 檔案。 這些是可以在執行階段讀取應用程式的任意 XML。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![預設資源檔](default-resources-images/01-resource-files-xs.png)

在上圖中，專案會有可繪製資源、 配置和值 （包含簡單值 XML 檔案） 的預設值。

以下提供的資源類型的完整清單：

-  **動畫**&ndash;描述屬性動畫的 XML 檔案。
   屬性動畫 API 層級 11 (Android 3.0) 中導入，並提供的物件上屬性的動畫。 屬性動畫會更有彈性且功能強大的方式，來描述任何類型之物件的動畫。

-  **anim** &ndash;描述的 XML 檔案*tween*動畫。 Tween 動畫是一系列的動畫指示檢視物件或範例中，循環的內容上執行轉換，映像，或增加文字的大小。 Tween 動畫會限制為只能檢視物件。

-  **色彩**&ndash;描述色彩狀態清單的 XML 檔案。 若要了解色彩狀態清單，請考慮 UI widget，例如按鈕。
   它可能會有不同的狀態，例如按下或停用，以及按鈕可能會變更色彩與每個狀態中的變更。 清單是以表示狀態清單。

-  **字型**&ndash;從 API 層級 26，它是 Android 應用程式中內嵌為資源的字型。 支援程式庫 26 會向下移植到 API 層級為 14 的字型。 內嵌字型可讓應用程式，將自訂字型直接從 XML 版面配置，而不需要將它們匯入為資產，才能使用。

-  **mipmap** &ndash;可繪製資源是圖形，可以編譯成應用程式，然後存取的 API 呼叫或參考其他 XML 資源的一般概念。
   可繪製資源來的一些範例包括點陣圖檔 （.png、.gif、.jpg）、 特殊且可調整大小的點陣圖，稱為[九個修補程式](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，狀態會列出，泛型等 XML 中定義的圖形。

-  **版面配置**&ndash;描述使用者介面版面配置，例如活動或清單中的資料列的 XML 檔案。

-  **功能表** &ndash; XML 檔案，例如描述應用程式功能表*選項功能表*，*快顯功能表*，以及*子功能表*。 如需功能表的範例，請參閱 <<c0> [ 快顯功能表示範](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[標準控制項](https://developer.xamarin.com/samples/mobile/StandardControls/)範例。

-  **原始**&ndash;會儲存在其原始的二進位格式的任意檔案。 這些檔案會編譯成二進位格式的 Android 應用程式。

-  **值**&ndash;包含簡單值的 XML 檔案。 值的目錄中的 XML 檔案不會定義單一資源，但可以改為定義多個資源。 例如一個 XML 檔案中可能含有字串值的清單，而另一個 XML 檔案可能會保存一份色彩值。

-  **xml** &ndash;函式類似於.NET 設定檔的 XML 檔案。 這些是可以在執行階段讀取應用程式的任意 XML

-----
