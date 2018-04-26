---
title: 如何檢視 PCL 中支援哪些文件庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 7875fc47b1caac025488b8b71bdbd909844e7823
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>如何檢視 PCL 中支援哪些文件庫？

- 您可以找到下的各種 PCL 目標平台所支援的各種功能的概觀*支援的功能*此頁面的一部分： [http://msdn.microsoft.com/library/gg597391.aspx](https://msdn.microsoft.com/library/gg597391.aspx)

- 另一個選項是使用[.NET Portability Analyzer](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b)來評估是否可以轉換現有的程式庫，加入 PCL 設定檔。

- 第三個可能的原因是瀏覽您可能會使用實際的設定檔的內容。 使用設定檔 78，例如，您可能會這裡：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\`並檢視內的所有組件。

無論方法您選擇，請注意，某些功能已透過 NuGet 和 Microsoft BCL 文件庫下載。
