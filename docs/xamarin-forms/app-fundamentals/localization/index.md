---
標題：「 Xamarin.Forms 當地語系化」描述：「內建的 .net 當地語系化架構可以用來建立跨平臺多語系應用程式與 Xamarin.Forms 。 文字和影像可以當地語系化，而應用程式可以支援由右至左的流程方向。」
assetid： 97BF843B-BDAA-4CEA-8189-6DB54B291D7F ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：11/07/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-localization"></a>Xamarin.Forms翻譯

_內建的 .NET 當地語系化架構可用來使用建立跨平臺多語系應用程式 Xamarin.Forms 。_

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.Forms字串和影像當地語系化](text.md)

用於將 .NET 應用程式當地語系化的內建機制，使用 [RESX 檔案](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files)和 `System.Resources`、`System.Globalization` 命名空間中的類別。 包含翻譯字串的 RESX 檔案會內嵌在 Xamarin.Forms 元件中，以及編譯器所產生的類別，以提供對翻譯的強型別存取。 然後即可在程式碼中擷取已翻譯文字。

## <a name="right-to-left-localization"></a>[從右至左當地語系化](right-to-left.md)

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 由右至左的當地語系化會將由右至左流程方向的支援新增至 Xamarin.Forms 應用程式。
