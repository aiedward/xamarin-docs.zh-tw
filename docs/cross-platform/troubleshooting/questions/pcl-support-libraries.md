---
title: 如何檢視 PCL 中所支援的程式庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: conceptdev
ms.author: crdun
ms.date: 07/27/2018
ms.openlocfilehash: 31dc5114a04deaf1a35bbd24f71cfa552f61d226
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290975"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>如何檢視 PCL 中所支援的程式庫？

- 您可以在此頁面的*支援功能*部分底下，找到各種 PCL 目標平臺所支援的各種功能總覽：[https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- 另一個選項是使用[.net 可攜性分析器](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b)，評估您現有的程式庫是否可以轉換成 PCL 設定檔。

- 第三種可能性是流覽您可能使用的實際設定檔內容。 例如，您可以在這裡找到使用設定檔78：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\`並在其中查看所有元件。

無論您選擇哪一種方法，請注意，某些功能必須透過 NuGet 和 Microsoft BCL 程式庫下載。
