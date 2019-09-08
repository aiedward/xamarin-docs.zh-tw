---
title: 使用 Android 資產
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: e3b7ccf74773e5a391b8f133ccc241ca0e18bfcb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755162"
---
# <a name="using-android-assets"></a>使用 Android 資產

_資產_提供一種方式，可在您的應用程式中包含文字、xml、字型、音樂和影片等任意檔案。 如果您嘗試將這些檔案包含在「資源」中，Android 會將這些檔案處理到其資源系統中，而且您將無法取得原始資料。 如果您不想要存取資料，資產就是其中一種方式。

新增至專案的資產會顯示，就像是可使用[AssetManager](xref:Android.Content.Res.AssetManager)從您的應用程式讀取的檔案系統一樣。
在這個簡單的示範中，我們將在專案中新增文字檔資產、使用`AssetManager`讀取它，並將它顯示在 TextView 中。

## <a name="add-asset-to-project"></a>將資產新增至專案

資產會移至`Assets`專案的資料夾中。 將新的文字檔新增至名`read_asset.txt`為的資料夾。 將一些文字放在其中，例如「我來自資產！」。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 應該將此檔案的**組建動作**設定為**AndroidAsset**：

![將組建動作設定為 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 應該將此檔案的**組建動作**設定為**AndroidAsset**：

[![將組建動作設定為 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

選取正確的**BuildAction**可確保檔案會在編譯時期封裝到 APK 中。

## <a name="reading-assets"></a>讀取資產

資產會使用[AssetManager](xref:Android.Content.Res.AssetManager)來讀取。 的實例`AssetManager`可透過存取上`Android.Content.Context`的[資產](xref:Android.Content.Context.Assets)屬性（例如活動）來取得。
在下列程式碼中，我們會開啟我們的**read_asset** ，閱讀內容，並使用 TextView 加以顯示。

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

執行應用程式，您應該會看到下列內容：

![範例螢幕擷取畫面](android-assets-images/screenshot.png)

## <a name="related-links"></a>相關連結

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [內容](xref:Android.Content.Context)
