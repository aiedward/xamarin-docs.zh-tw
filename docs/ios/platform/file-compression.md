---
title: Xamarin 中的檔案壓縮
description: 本檔說明如何在 Xamarin 中使用 libcompression API。 其中討論 deflating、因而誇大和各種支援的演算法。
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: bcc63aa4e1926f5502d571bf47c83b0c8ea7e429
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199518"
---
# <a name="file-compression-in-xamarinios"></a>Xamarin 中的檔案壓縮

以 iOS 9.0 或 macOS 10.11 (和更新版本) 為目標的 Xamarin 應用程式可以使用_壓縮架構_來壓縮 (編碼) 和解壓縮 (解碼) 資料。 Xamarin 會遵循串流 API 來提供此架構。 壓縮架構可讓開發人員與壓縮和解壓縮的資料互動, 如同它們是正常的資料流程, 而不需要使用回呼或委派。

壓縮架構提供下列演算法的支援:

* LZ4
* LZ4 原始
* Lzfse
* Lzma
* Zlib

使用壓縮架構可讓開發人員執行壓縮作業, 而不需要任何協力廠商程式庫或 Nuget。 這會減少外部相依性, 並確保所有平臺都支援壓縮作業 (只要它們符合最低作業系統需求)。

## <a name="general-file-decompression"></a>一般檔案解壓縮

壓縮架構會使用 Xamarin. iOS 和 Xamarin 中的資料流程 API。 此 API 表示若要壓縮資料, 開發人員可以使用 .NET 中其他 IO Api 所使用的一般模式。 下列範例顯示如何使用壓縮架構來解壓縮資料, 這類似于`System.IO.Compression.DeflateStream` api 中找到的 api:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

會執行介面, 如同其他`System.IO.Streams`, 因此開發人員應確保資源在不再需要時釋放。 `IDisposable` `CompressionStream`

## <a name="general-file-compression"></a>一般檔案壓縮

壓縮 API 也可讓開發人員在相同的 API 之後壓縮資料。 您可以使用`CompressionAlgorithm`列舉值中所述的其中一種演算法來壓縮資料。

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>非同步支援

支援支援的所有非同步作業, 這表示開發人員可以使用 async 關鍵字來執行壓縮/解壓縮作業, 而不封鎖 UI 執行緒。 `System.IO.DeflateStream` `CompressionStream`
