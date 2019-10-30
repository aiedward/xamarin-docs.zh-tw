---
title: 使用 Android 資產
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 9c8db5ad7bcb012befb2fa8dcd1ecd13fa355a55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025421"
---
# <a name="using-android-assets"></a>使用 Android 資產

_資產_提供一種方式，可在您的應用程式中包含文字、xml、字型、音樂和影片等任意檔案。 如果您嘗試將這些檔案包含在「資源」中，Android 會將這些檔案處理到其資源系統中，而且您將無法取得原始資料。 如果您不想要存取資料，資產就是其中一種方式。

新增至專案的資產會顯示，就像是可使用[AssetManager](xref:Android.Content.Res.AssetManager)從您的應用程式讀取的檔案系統一樣。
在這個簡單的示範中，我們將在專案中新增文字檔資產、使用 `AssetManager`加以讀取，並將其顯示在 TextView 中。

## <a name="add-asset-to-project"></a>將資產新增至專案

Assets go in the `Assets` folder of your project. Add a new text file to this folder called `read_asset.txt`. Place some text in it like "I came from an asset!".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio should have set the **Build Action** for this file to **AndroidAsset**:

![Setting the build action to AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac should have set the **Build Action** for this file to **AndroidAsset**:

[![Setting the build action to AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Selecting the correct **BuildAction** ensures that the file will be packaged into the APK at compile time.

## <a name="reading-assets"></a>Reading Assets

Assets are read using an [AssetManager](xref:Android.Content.Res.AssetManager). An instance of the `AssetManager` is available by accessing the [Assets](xref:Android.Content.Context.Assets) property on an `Android.Content.Context`, such as an Activity.
In the following code, we open our **read_asset.txt** asset, read the contents, and display it using a TextView.

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

Run the application and you should see the following:

![Example screenshot](android-assets-images/screenshot.png)

## <a name="related-links"></a>相關連結

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [Context](xref:Android.Content.Context)
