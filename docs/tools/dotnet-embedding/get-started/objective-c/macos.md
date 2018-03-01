---
title: "開始使用 macOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 5e2f14e7b29f85e838563914089743f56239d7bb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-macos"></a>開始使用 macOS


## <a name="what-you-will-need"></a>您必須

* 請依照下列指示中的我們[入門 OBJECTIVE-C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。

* 若要使用的.NET 組件**Embeddinator 4000**。

* MacOS Cocoa 應用程式

請繼續執行中的指示之後, 我們[入門 OBJECTIVE-C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。 如果您已經是.NET 組件可以略過直接**使用 Embeddinator-4000** > 一節。

## <a name="creating-a-net-assembly"></a>建立.NET 組件

若要建置.NET 組件，您必須開啟[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)並建立新**.NET 程式庫專案**透過操作*檔案 > 新的方案 > 其他 >.NET > 程式庫*. 按 下一步並賦予*天氣*為*專案名稱*，然後按一下 *建立*。

請遵循下一個步驟：

1. 刪除**MyClass.cs**檔案和**屬性**資料夾。

2. 以滑鼠右鍵按一下*天氣專案 > 新增 > 新的檔案。*

3. 選取*空類別*並用**XAMWeatherFetcher**做為名稱，然後按一下 [新增]。

4. 取代內容*XAMWeatherFetcher.cs*為下列程式碼：

```csharp
using System;
using System.Json;
using System.Net;

public class XAMWeatherFetcher {

    static string urlTemplate = @"https://query.yahooapis.com/v1/public/yql?q=select%20item.condition%20from%20weather.forecast%20where%20woeid%20in%20(select%20woeid%20from%20geo.places(1)%20where%20text%3D%22{0}%2C%20{1}%22)&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys";
    public string City { get; private set; }
    public string State { get; private set; }

    public XAMWeatherFetcher (string city, string state)
    {
        City = city;
        State = state;
    }

    public XAMWeatherResult GetWeather ()
    {
        try {
            using (var wc = new WebClient ()) {
                var url = string.Format (urlTemplate, City, State);
                var str = wc.DownloadString (url);
                var json = JsonValue.Parse (str)["query"]["results"]["channel"]["item"]["condition"];
                var result = new XAMWeatherResult (json["temp"], json["text"]);
                return result;
            }
        }
        catch (Exception ex) {
            // Log some of the exception messages
            Console.WriteLine (ex.Message);
            Console.WriteLine (ex.InnerException?.Message);
            Console.WriteLine (ex.InnerException?.InnerException?.Message);

            return null;
        }

    }
}

public class XAMWeatherResult {
    public string Temp { get; private set; }
    public string Text { get; private set; }

    public XAMWeatherResult (string temp, string text)
    {
        Temp = temp;
        Text = text;
    }
}
```

您會注意到`Using System.Json;`時發生錯誤; 若要修正此我們需要執行下列動作：

1. 按兩下**參考**資料夾。

2. 按一下**封裝** 索引標籤。

3. 請檢查**System.Json**。

4. Click **Ok**.

完成上述是我們要做為建置我們的.NET 組件上的 [*建置] 功能表 > 建置所有*或 ⌘ + b。 建置的成功訊息應該會出現在頂端的狀態列。

現在以滑鼠右鍵按一下*天氣*專案節點，然後選取*搜尋工具中顯示*。 在搜尋中，移至*bin/Debug*資料夾; 內部您會發現它**Weather.dll。**

## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

如果您安裝 Embeddinator 4000 使用我們的 byok-kek-pkg 安裝並啟動新的終端機工作階段，在安裝之後，您應該能夠使用**objcgen**命令 (否則，您可以使用它的絕對路徑： `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`);**objcgen**是我們要從.NET 組件產生原生程式庫的工具。

開啟的終端機`cd`Weather.dll，其中包含的資料夾，然後執行**objcgen**以引數，如下所示：

```shell
cd /Users/Alex/Projects/Weather/Weather/bin/Debug

objcgen --debug --outdir=output -c Weather.dll
```

您需要將會放置到的所有項目**輸出**目錄下一步*Weather.dll*。 如果您有自己的.NET 組件時，取代*Weather.dll*且遵循上述相同步驟。

## <a name="using-the-generated-output-in-an-xcode-project"></a>在 Xcode 專案中使用產生的輸出

開啟 Xcode，並建立新**macOS Cocoa 應用程式**並將其命名**MyWeather**。 以滑鼠右鍵按一下*MyWeather 專案節點*，選取*將檔案新增至 「 MyWeather"*，瀏覽至**輸出**所建立的目錄*Embeddinator 4000*，並加入下列檔案：

* bindings.h
* embeddinator.h
* glib.h
* mono-support.h
* mono_embeddinator.h
* objc-support.h
* libWeather.dylib
* Weather.dll

請確定**複製的項目，視**選項面板的 [檔案] 對話方塊中，核取。

現在，我們必須先確定**libWeather.dylib**和**Weather.dll**進入應用程式套件組合：

* 按一下*MyWeather 專案節點*。
* 選取*建置階段* 索引標籤。
* 加入新*複製檔案階段*。
* 在*目的地*選取**架構**並加入**libWeather.dylib**。
* 加入新*複製檔案階段*。
* 在*目的地*選取**可執行檔**，新增**Weather.dll** ，並確定*複本上的程式碼簽署*已核取。

現在開啟**ViewController.m**和取代其內容：

```objective-c
#import "ViewController.h"
#import "bindings.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    XAMWeatherFetcher * fetcher = [[XAMWeatherFetcher alloc] initWithCity:@"Boston" state:@"MA"];
    XAMWeatherResult * weather = [fetcher getWeather];

    NSString * result;
    if (weather)
        result = [NSString stringWithFormat:@"%@ °F - %@", weather.temp, weather.text];
    else
        result = @"An error occured";

    NSTextField * textField = [NSTextField labelWithString:result];
    [self.view addSubview:textField];
}

@end
```

最後執行 Xcode 專案，並像下面這樣將會顯示：

![MyWeather 執行範例](macos-images/weather-from-csharp-macos.png)

更完整且更尋找範例可用於[這裡](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather)。
