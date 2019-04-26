---
title: 使用 Android 資產
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 1e9a71de7725c8382e133d85977407bcc859fc58
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013604"
---
# <a name="using-android-assets"></a>使用 Android 資產

_資產_提供像是文字、 xml、 字型、 音樂及視訊的任意檔案納入您的應用程式的方式。 如果您嘗試將包含這些檔案儲存為 「 資源 」，Android 會處理其資源的系統，並不能取得未經處理的資料。 如果您想要存取不變的資料，則資產會是一種方式執行它。

資產新增至您的專案將會顯示可讀取您的應用程式使用的檔案系統一樣[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。
在這個簡單的示範中，我們要將文字檔案資產新增至我們的專案中，讀取使用`AssetManager`，並將它顯示在 TextView。


## <a name="add-asset-to-project"></a>將資產加入至專案

資產進`Assets`您專案的資料夾。 將新的文字檔加入至這個資料夾稱為`read_asset.txt`。 像 「 我的來源資產 ！ 」，請將一些文字放在它。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 應該已設定**建置動作**這個檔案**AndroidAsset**:

![將建置動作設定為 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

應該設定 visual Studio for Mac**建置動作**這個檔案**AndroidAsset**:

[![將建置動作設定為 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

選取正確**建置動作**可確保檔案會封裝成 APK，在編譯時期。


## <a name="reading-assets"></a>讀取資產

使用讀取資產[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。 執行個體`AssetManager`可用來存取[資產](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/)上的屬性`Android.Content.Context`，例如的活動。
下列程式碼中，開啟我們**read_asset.txt**資產，讀取內容，並顯示它使用 TextView。

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
- [內容](https://developer.xamarin.com/api/type/Android.Content.Context/)
