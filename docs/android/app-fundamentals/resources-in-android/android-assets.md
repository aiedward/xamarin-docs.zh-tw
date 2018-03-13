---
title: "使用 Android 資產"
ms.topic: article
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: 83e58625438a0b50d89ca8dac3e940c8742e5aec
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="using-android-assets"></a>使用 Android 資產

_資產_提供一個方式來併入應用程式中的任意檔案，例如文字、 xml、 字型、 音樂及視訊。 如果您嘗試將包含這些檔案儲存為 「 資源 」，Android 將會處理其資源的系統，並不能取得未經處理資料。 如果您想要存取資料的不變，資產是一種方式執行。

資產加入至您的專案就像您使用應用程式可以讀取的檔案系統將會顯示[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。
在這個簡單的示範中，我們會將文字檔案資產加入至專案中，讀取使用`AssetManager`，並顯示於 TextView。


## <a name="add-asset-to-project"></a>將資產加入至專案

資產進入`Assets`專案的資料夾。 將新的文字檔加入至這個資料夾稱為`read_asset.txt`。 在其中輸入一些文字，例如 「 我來自資產 ！"。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 必須已設定**建置動作**此檔案**AndroidAsset**:

![將建置動作設定為 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

必須已設定 visual Studio for Mac**建置動作**此檔案**AndroidAsset**:

[![將建置動作設定為 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

選取正確**BuildAction**可確保檔案會封裝到 APK，在編譯時間。


## <a name="reading-assets"></a>讀取資產

使用讀取資產[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。 執行個體`AssetManager`可以存取[資產](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/)屬性`Android.Contet.Context`，例如的活動。
下列程式碼中，開啟我們**read_asset.txt**資產，讀取內容，並將其使用 TextView 顯示。

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```


## <a name="running-the-application"></a>執行應用程式

執行應用程式，您應該會看到下列：

![範例螢幕擷取畫面](android-assets-images/screenshot.png)


## <a name="related-links"></a>相關連結

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
