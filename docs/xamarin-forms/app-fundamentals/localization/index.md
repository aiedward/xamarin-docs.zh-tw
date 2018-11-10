---
title: Xamarin.Forms 當地語系化
description: 內建.NET 當地語系化的架構可用來建置跨平台與 Xamarin.Forms 的多語系應用程式。 可當地語系化文字和影像，而應用程式可支援由右至左的流向。
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285569"
---
# <a name="xamarinforms-localization"></a>Xamarin.Forms 當地語系化

_內建.NET 當地語系化的架構可用來建置跨平台與 Xamarin.Forms 的多語系應用程式。_

## <a name="string-and-image-localizationtextmd"></a>[字串及影像當地語系化](text.md)

當地語系化 .NET 應用程式使用的內建機制[RESX 檔案](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files)類別和類別`System.Resources`和`System.Globalization`命名空間。 包含已翻譯的字串的 RESX 檔案內嵌在 Xamarin.Forms 組件，以及編譯器所產生的類別，提供翻譯的強型別存取。 然後可以在程式碼中擷取翻譯的文字。

## <a name="right-to-left-localizationright-to-leftmd"></a>[由右至左當地語系化](right-to-left.md)

資料流程方向是所在頁面的 UI 項目會掃描出的方向。 從右至左當地語系化會新增到 Xamarin.Forms 應用程式的支援由右至左的流向。
