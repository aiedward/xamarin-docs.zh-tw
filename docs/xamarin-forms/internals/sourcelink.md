---
標題：「來源連結與「 Xamarin.Forms 描述：」本文說明如何使用「來源連結」來進行調試 Xamarin.Forms 。」
zone_pivot_groups： "platform" ms-chap： xamarin assetId： 1E13FCD9-5607-46E8-80E4-87A58B389BEB ms-chap： xamarin-forms author： profexorgeek ms-chap： jusjohns ms. date： 09/26/2019 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="source-link-with-xamarinforms"></a>來源連結與Xamarin.Forms

Xamarin.FormsNuGet 套件包含來源連結對應。 來源連結會將 NuGet 套件中包含的已編譯程式庫對應至原始程式碼存放庫。 Visual Studio 會在進行偵錯工具期間下載原始程式碼檔案，並允許開發人員逐步執行程式碼，而不需要從來源建立即可進行封裝的偵錯工具。

如需有關使用來源連結的詳細資訊，請參閱[來源連結檔](/dotnet/standard/library-guidance/sourcelink)。

::: zone pivot="windows"

> [!WARNING]
> Visual Studio 2019 支援 **.net 偵錯工具**的來源連結，但目前不支援**Mono 偵錯工具**的來源連結。 因此，您可以使用 [來源] 連結來對 UWP 應用程式（但不是 Android 或 iOS 應用程式）進行 debug。 在偵測 UWP 應用程式時，您必須確定您要 debug 的程式庫 PDB 檔案已複製到您的應用程式編譯所在**bin**目錄中的**AppX**資料夾。

## <a name="enable-source-link"></a>啟用來源連結

使用來源連結必須啟用外部程式碼的偵錯工具，否則偵錯工具會對目前方案中未包含的程式碼執行先前的呼叫。 在 Visual Studio 2019 中，這可以在 [**調試**] 區段的 [**選項**] 功能表中找到：

[![啟用 Visual Studio 2019 中的來源連結](sourcelink-images/sourcelink-enable-pc-cropped.png)](sourcelink-images/sourcelink-enable-pc.png#lightbox)

確定 [**僅啟用我**的程式碼] 已停用，而且已啟用 [**啟用來源連結支援**]。

::: zone-end
::: zone pivot="macos"

## <a name="enable-source-link"></a>啟用來源連結

使用來源連結必須啟用外部程式碼的偵錯工具，否則偵錯工具會對目前方案中未包含的程式碼執行先前的呼叫。 此選項可以在 [**偵錯工具**] 區段的 [**喜好**設定] 視窗中找到：

[![啟用 Visual Studio for Mac 中的來源連結](sourcelink-images/sourcelink-enable-mac-cropped.png)](sourcelink-images/sourcelink-enable-mac.png#lightbox)

請確定已啟用 [**逐步執行至外部程式碼**]。

::: zone-end

## <a name="debug-xamarinforms-using-source-link"></a>Xamarin.Forms使用來源連結進行 Debug

如果已啟用對外部封裝的偵錯工具，Visual Studio 將會使用 NuGet 套件中包含的來源連結對應來下載並逐步執行外部原始程式碼。 這可以透過在所提供的方法呼叫上設定中斷點來進行測試 Xamarin.Forms ：

[![方法上設定的中斷點 Xamarin.Forms](sourcelink-images/breakpoint-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

根據您在**偵錯工具**選項中指定的設定，Visual Studio 會警告您它正在下載來源檔案：

[![Visual Studio 外部程式碼警告](sourcelink-images/external-code-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

一旦您允許 Visual Studio 下載檔案之後，偵錯工具就會逐步執行外部程式碼。

::: zone pivot="windows"

## <a name="source-link-caching"></a>來源連結快取

來源連結會使用快取來取得效能。 [來源連結的快取目錄] 定義于 [**符號**] 區段中 [**調試**程式] 下的 [**選項**] 功能表中：

[![Visual Studio 來源連結快取](sourcelink-images/sourcelink-caching-pc-cropped.png)](sourcelink-images/sourcelink-caching-pc.png#lightbox)

此功能表可讓您指定所有 debug 符號的快取目錄，以及在遇到快取符號的問題時清除快取。

::: zone-end
::: zone pivot="macos"

## <a name="source-link-caching"></a>來源連結快取

來源連結會使用快取來取得效能。 MacOS 上的來源連結快取目錄是 `/Users/<username>/Library/Caches/VisualStudio/8.0/Symbols` 。 此資料夾包含的子資料夾會儲存用來下載來源檔案的儲存機制。 如果 NuGet 封裝的支援儲存機制已變更，您可能需要手動刪除這些資料夾以重新整理快取。

::: zone-end

## <a name="related-links"></a>相關連結

- [來源連結檔](/dotnet/standard/library-guidance/sourcelink)
- [GitHub 上的來源連結](https://github.com/dotnet/sourcelink)
