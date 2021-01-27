---
title: Xamarin.Essentials：檔案選擇器
description: 中的 FilePicker 類別 Xamarin.Essentials 可讓使用者從裝置挑選單一或多個檔案。
ms.assetid: 00bdbd57-56b1-47ca-8abe-cebe1b01f61a
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7a9701a1607333022b7356f4485f041d66b410c6
ms.sourcegitcommit: 3a68a8a48eb5a0ea7d6e0f861e2f7ecefcc49715
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810729"
---
# <a name="no-locxamarinessentials-file-picker"></a>Xamarin.Essentials：檔案選擇器

**FilePicker** 類別可讓使用者從裝置挑選單一或多個檔案。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **FilePicker** 功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

需要 `ReadExternalStorage` 權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties] 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]
```

或更新 Android 資訊清單：

開啟 [ **Properties** ] 資料夾底下的 **AndroidManifest.xml** 檔案，並在 **資訊清單** 節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [ **Android 資訊清單** ] 下，尋找 [ **必要許可權：** ] 區域並檢查此許可權。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

不需要進行額外設定。

-----

> [!TIP]
> 您必須在 UI 執行緒上呼叫所有方法，因為系統會自動處理許可權檢查和要求 Xamarin.Essentials 。

## <a name="pick-file"></a>挑選檔案

`FilePicker.PickAsync()` 方法可讓您的使用者從裝置挑選檔案。 `PickOptions`當您呼叫方法時，您可以指定要顯示的標題，以及允許使用者挑選的檔案類型，以提供特定的不同。 依照預設

```csharp
async Task<FileResult> PickAndShow(PickOptions options)
{
    try
    {
        var result = await FilePicker.PickAsync();
        if (result != null)
        {
            Text = $"File Name: {result.FileName}";
            if (result.FileName.EndsWith("jpg", StringComparison.OrdinalIgnoreCase) ||
                result.FileName.EndsWith("png", StringComparison.OrdinalIgnoreCase))
            {
                var stream = await result.OpenReadAsync();
                Image = ImageSource.FromStream(() => stream);
            }
        }
    }
    catch (Exception ex)
    {
        // The user canceled or something went wrong
    }
}
```

預設檔案類型隨附于 `FilePickerFileType.Images` 、 `FilePickerFileType.Png` 和 `FilePickerFilerType.Videos` 。 您可以在建立時指定自訂檔案類型 `PickOptions` ，而且可以針對每個平臺進行自訂。 例如，以下是您指定特定漫畫檔案類型的方式：

```csharp
var customFileType =
    new FilePickerFileType(new Dictionary<DevicePlatform, IEnumerable<string>>
    {
        { DevicePlatform.iOS, new[] { "public.my.comic.extension" } }, // or general UTType values
        { DevicePlatform.Android, new[] { "application/comics" } },
        { DevicePlatform.UWP, new[] { ".cbr", ".cbz" } },
        { DevicePlatform.Tizen, new[] { "*/*" } },
        { DevicePlatform.macOS, new[] { "cbr", "cbz" } }, // or general UTType values
    });
var options = new PickOptions
{
    PickerTitle = "Please select a comic file",
    FileTypes = customFileType,
};
```

## <a name="pick-multiple-files"></a>挑選多個檔案

如果您希望使用者挑選多個檔案，您可以呼叫 `FilePicker.PickMultipleAsync()` 方法。 它也會將 `PickOptions` 作為參數來指定其他資訊。 結果與相同 `PickAsync` ，但不會 `FileResult` `IEnumerable<FileResult>` 傳回可反復查看的單一。


## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

- 所產生檔案的 URI 可能不會在重新開機之間保存。

# <a name="ios"></a>[iOS](#tab/ios)

無平台差異。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="api"></a>API

- [FilePicker 來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/FilePicker)
- [FilePicker API 檔](xref:Xamarin.Essentials.FilePicker)
