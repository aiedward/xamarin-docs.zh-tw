---
title: 在 Xamarin.iOS 中的檔案壓縮
description: 本文件說明如何在 Xamarin.iOS 中 libcompression API 使用。 它討論升空的方式回應，並，並支援不同的演算法。
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: f7a1df65047fd8040dd40e9f7f057d6bfe6dea61
ms.sourcegitcommit: 4c97f5d73be7eb2da153a85183be4258b6b11ca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58290921"
---
# <a name="file-compression-in-xamarinios"></a>在 Xamarin.iOS 中的檔案壓縮

Xamarin iOS 9.0 以上版本或 macOS 10.11 （及更新版本） 為目標的應用程式可以使用_壓縮 Framework_壓縮 （編碼） 和解壓縮 （解碼） 資料。 Xamarin.iOS 提供遵循 Stream API 此架構。 壓縮 framework 可讓開發人員互動的壓縮和解壓縮資料，如同它們是一般的資料流，而不需要使用回呼或委派。

壓縮 framework 提供支援下列演算法：

* LZ4
* 以 lz4 做為未經處理
* Lzfse
* Lzma
* Zlib

使用壓縮架構可讓開發人員執行壓縮作業，而不需要任何協力廠商程式庫或 Nuget。 這可減少外部相依性，並可確保，壓縮作業將會支援所有平台上 （只要它們符合最低作業系統需求）。

## <a name="general-file-decompression"></a>一般檔案解壓縮

壓縮架構會使用 Xamarin.iOS 和 Xamarin.Mac 中的資料流 API。 此 API 表示以壓縮的資料，開發人員將可以使用其他 IO Api，在.NET 中使用的一般模式。 下列範例示範如何解壓縮資料壓縮 framework，也就是在中找到的 API 類似`System.IO.Compression.DeflateStream`API:

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

`CompressionStream`會實作`IDisposable`介面，例如其他`System.IO.Streams`，因此開發人員應確定之後已不再需要，就會釋放資源。

## <a name="general-file-compression"></a>一般檔案壓縮

壓縮 API 也可讓開發人員可以壓縮資料遵循相同的 API。 資料可以使用提供的演算法中所述的其中一個壓縮`CompressionAlgorithm`列舉值。

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

`CompressionStream`支援所支援的所有非同步作業`System.IO.DeflateStream`，亦即開發人員可以使用 async 關鍵字，而不會封鎖 UI 執行緒執行壓縮/解壓縮的作業。