---
title: 如何檢視 PCL 中所支援的程式庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: davidortinau
ms.author: daortin
ms.date: 07/27/2018
ms.openlocfilehash: 9484bcac199118bb1beb1b520be8e231dc9181ce
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457844"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>如何檢視 PCL 中所支援的程式庫？

- 您可以在此頁面的 *支援功能* 部分下，找到各種 PCL 目標平臺支援的各種功能： [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- 另一個選項是使用 [.net 可攜性分析器](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) ，來評估您現有的程式庫是否可以轉換成 PCL 設定檔。

- 第三個可能的原因是流覽您可能使用的實際設定檔內容。 例如，使用設定檔78，您可能會在這裡： `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` 和查看其中的所有元件。

無論您選擇哪種方法，請注意，某些功能必須透過 NuGet 和 Microsoft BCL 程式庫下載。