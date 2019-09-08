---
title: 應用程式當地語系化和字串資源
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 45fe5c783e737fb913730082841e0dfafc555684
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755125"
---
# <a name="application-localization-and-string-resources"></a>應用程式當地語系化和字串資源

應用程式當地語系化是指提供以特定區域或地區設定為目標的替代資源。 例如，您可能會針對各種國家/地區提供當地語系化的語言字串，或者您可能會變更色彩或版面配置以符合特定文化特性。 Android 會在執行時間載入並使用適用于裝置地區設定的資源，而不會對原始程式碼進行任何變更。

例如，下圖顯示在三個不同的裝置地區設定中執行的相同應用程式，但每個按鈕中顯示的文字，都是每個裝置設定的地區設定所特有：

[![三種不同地區設定的範例](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

在此範例中，版面配置檔案的內容**axml**看起來像這樣：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

在上述範例中，會藉由提供字串的資源識別碼，從資源載入按鈕的字串：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![適用于三種語言的資源字串](application-localization-images/02-resource-strings-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![適用于三種語言的資源字串](application-localization-images/02-resource-strings-xs.png)

-----

## <a name="localizing-android-apps"></a>當地語系化 Android 應用程式

如需當地語系化行動應用程式的秘訣和指引，請參閱[當地語系化簡介](~/cross-platform/app-fundamentals/localization.md)。

[當地語系化 Android 應用程式](~/android/app-fundamentals/localization.md)指南包含更具體的範例，說明如何使用 Xamarin 來轉譯字串和當地語系化影像。

## <a name="related-links"></a>相關連結

- [當地語系化 Android 應用程式](~/android/app-fundamentals/localization.md)
- [跨平臺當地語系化總覽](~/cross-platform/app-fundamentals/localization.md)
