---
title: 如何檢視 PCL 中所支援的程式庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: asb3993
ms.author: amburns
ms.date: 07/27/2018
ms.openlocfilehash: 7e1017baf7daed68b5e55319a9ce13a4a2df5f2e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341261"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>如何檢視 PCL 中所支援的程式庫？

- 您可以找到下的各種 PCL 目標平台所支援的各種功能的概觀*支援的功能*此頁面的一部分： [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- 另一個選項是使用[.NET Portability Analyzer](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b)來評估您現有的程式庫可以轉換成的 PCL 設定檔。

- 第三個可能的原因是瀏覽您可能會使用實際的設定檔的內容。 例如，使用設定檔 78，您可能會前往：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` 與檢視內的所有組件。

無論哪種方法您選擇，請注意，某些功能必須透過 NuGet 和 Microsoft BCL 程式庫下載。
