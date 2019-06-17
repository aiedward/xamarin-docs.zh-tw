---
title: 應用程式當地語系化和字串資源
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: d9d90e371199c8587d61199240523cf0a23f5efd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013200"
---
# <a name="application-localization-and-string-resources"></a>應用程式當地語系化和字串資源

應用程式當地語系化是指提供替代資源為目標的特定區域或地區設定。 例如，您可能會在各種不同的國家/地區，提供當地語系化的語言字串，或您可能會變更色彩或配置以符合特定的文化特性。 Android 會載入，並在執行階段時，不會變更原始碼中使用適用於裝置的地區設定的資源。

比方說下, 圖顯示在三個不同的裝置地區設定中，執行相同的應用程式，但每個按鈕中顯示的文字是每個裝置會設為地區設定特定：

[![三個不同的地區設定的範例](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

在此範例中，版面配置檔案的內容**Main.axml**看起來像這樣：

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

在上述範例中，按鈕的字串從資源載入所提供的資源識別碼的字串：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![三種語言的資源字串](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![三種語言的資源字串](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>當地語系化的 Android 應用程式

讀取[當地語系化簡介](~/cross-platform/app-fundamentals/localization.md)祕訣和當地語系化行動裝置應用程式的指引。

[當地語系化的 Android 應用程式](~/android/app-fundamentals/localization.md)指南包含有關如何解譯字串化和當地語系化使用 Xamarin.Android 的映像更具體的範例。



## <a name="related-links"></a>相關連結

- [當地語系化的 Android 應用程式](~/android/app-fundamentals/localization.md)
- [跨平台當地語系化概觀](~/cross-platform/app-fundamentals/localization.md)
