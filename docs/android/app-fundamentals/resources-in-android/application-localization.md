---
title: "應用程式的當地語系化和字串資源"
ms.topic: article
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: a8d25d8780a62e54780d7aa03d81f89fa0f668a4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="application-localization-and-string-resources"></a>應用程式的當地語系化和字串資源

應用程式當地語系化為提供替代資源為目標的特定區域或地區設定的動作。 例如，您可能會在不同國家 （地區），提供當地語系化的語言字串，或您可能會變更色彩或配置以符合特定文化特性而有所不同。 Android 將會載入並執行階段不需要變更任何原始碼中使用適用於裝置的地區設定的資源。

例如下, 圖顯示三個不同裝置的地區設定，以執行相同的應用程式，但是中每個按鈕顯示的文字會設為每個裝置的地區設定所特有：

[![三個不同的地區設定的範例](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

在此範例中，配置檔案，內容**Main.axml**看起來像這樣：

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

在上述範例中，按鈕的字串從資源載入字串中提供的資源識別碼：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![三種語言的資源字串](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![三種語言的資源字串](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>當地語系化 Android 應用程式

讀取[簡介當地語系化](~/cross-platform/app-fundamentals/localization.md)秘訣和指導方針，將行動裝置應用程式。

[當地語系化 Android 應用程式](~/android/app-fundamentals/localization.md)指南包含有關如何解譯字串和當地語系化映像使用 Xamarin.Android 更具體的範例。



## <a name="related-links"></a>相關連結

- [當地語系化 Android 應用程式](~/android/app-fundamentals/localization.md)
- [跨平台當地語系化概觀](~/cross-platform/app-fundamentals/localization.md)
