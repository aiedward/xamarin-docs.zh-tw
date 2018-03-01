---
title: "第 9 章的摘要。 平台專屬的 API 呼叫"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 637096d3ebb7fb90321f7f459e0ca9e51572d935
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第 9 章的摘要。 平台專屬的 API 呼叫

有時候會需要執行一些不同的程式碼的平台。 這一章探討技術。

## <a name="preprocessing-in-the-shared-asset-project"></a>前置處理共用的資產專案中

Xamarin.Forms 共用資產專案可以執行不同的程式碼，每個平台使用的 C# 前置處理器指示詞`#if`， `#elif`，和`endif`。 這示範於[ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![變數的三個螢幕擷取畫面格式化段落](images/ch09fg01-small.png "裝置型號和作業系統")](images/ch09fg01-large.png "裝置型號和作業系統")

但是，產生的程式碼很雜亂，難以閱讀。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共用資產專案中的平行類別

更結構化的方式來執行 SAP 中的平台專屬程式碼會示範[ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)範例。 每個平台的專案包含同名的類別和方法，但實作該特定平台。 SAP 然後只具現化類別，並呼叫方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService 和可攜式類別庫

程式庫通常無法存取應用程式專案中的類別。 這項限制似乎防止中所示範的技術**PlatInfoSap2**使用 PCL 中。 不過，Xamarin.Forms 包含類別，名為[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) ，會使用.NET 反映，以從 PCL 存取應用程式專案中的公用類別。

必須定義 PCL`interface`與它需要在每個平台使用的成員。 然後，每個平台會包含該介面的實作。 實作介面的類別必須用來識別[DependencyAttribute](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyAttribute/)組件層級上。

PCL 接著會使用泛型[ `Get` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DependencyService.Get{T}/p/Xamarin.Forms.DependencyFetchTarget/)方法`DependencyService`取得平台類別實作之介面的執行個體。

這示範於[ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)範例。

## <a name="platform-specific-sound-generation"></a>平台專屬的聲音產生

[ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)範例會將嗶聲，以**MonkeyTap**藉由存取聲音產生設備中每個平台的程式。



## <a name="related-links"></a>相關連結

- [第 9 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第 9 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
