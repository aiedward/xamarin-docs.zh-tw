---
title: 使用 Android 資產
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 795f80f69294abdfd7bf412225ab77cbbe5cb5b1
ms.sourcegitcommit: d2daaa6ca5fe630f80d5a8151985d9f96a2fc93b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512991"
---
# <a name="using-android-assets"></a>使用 Android 資產

_資產_ 提供一種方式，可在您的應用程式中包含文字、xml、字型、音樂和影片等任意檔案。 如果您嘗試將這些檔案包含為「資源」，則 Android 會將這些檔案處理到其資源系統，而且您將無法取得原始資料。 如果您不想要存取資料，資產就是其中一種方式。

新增至專案的資產會像檔案系統一樣顯示，您可以使用 [AssetManager](xref:Android.Content.Res.AssetManager)從應用程式讀取。
在這個簡單的示範中，我們要將文字檔資產新增至專案、使用讀取，然後將 `AssetManager` 它顯示在 TextView 中。

## <a name="add-asset-to-project"></a>將資產新增至專案

資產會移至 `Assets` 專案的資料夾中。 將新的文字檔加入至這個名為的資料夾 `read_asset.txt` 。 在其中放入一些文字，例如「我來自資產！」。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Visual Studio 應該將此檔案的 **組建動作** 設為 **AndroidAsset**：

![將組建動作設定為 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 應該將此檔案的 **組建動作** 設為 **AndroidAsset**：

[![將組建動作設定為 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

選取正確的 **BuildAction** 可確保檔案會在編譯時期封裝到 APK 中。

## <a name="reading-assets"></a>讀取資產

使用 [AssetManager](xref:Android.Content.Res.AssetManager)讀取資產。 的實例可透過 `AssetManager` 存取上的 [資產](xref:Android.Content.Context.Assets) 屬性來取得 `Android.Content.Context` ，例如活動。
在下列程式碼中，我們會開啟 **read_asset.txt** 資產、讀取內容，並使用 TextView 加以顯示。

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

### <a name="reading-binary-assets"></a>讀取二進位資產

`StreamReader`在上述範例中使用，適用于文字資產。 針對二進位資產，請使用下列程式碼：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Read the contents of our asset
    const int maxReadSize = 256 * 1024;
    byte[] content;
    AssetManager assets = this.Assets;
    using (BinaryReader br = new BinaryReader (assets.Open ("mydatabase.db")))
    {
        content = br.ReadBytes (maxReadSize);
    }

    // Do something with it...

}
```

## <a name="running-the-application"></a>執行應用程式

執行應用程式，您應該會看到下列內容：

![範例螢幕擷取畫面](android-assets-images/screenshot.png)

## <a name="related-links"></a>相關連結

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [內容](xref:Android.Content.Context)
