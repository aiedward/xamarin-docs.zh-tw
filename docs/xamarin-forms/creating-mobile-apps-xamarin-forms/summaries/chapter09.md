---
title: 第 9 章的摘要。 平台特定 API 呼叫
description: 使用 Xamarin.Forms 建立行動應用程式： 第 9 章的摘要。 平台特定 API 呼叫
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 89056bac86116161512ad32721c5d877850d75f2
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563221"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第 9 章的摘要。 平台特定 API 呼叫

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

它有時候是必要的平台執行一些程式碼，而有所不同。 本章將探討的技巧。

## <a name="preprocessing-in-the-shared-asset-project"></a>共用的資產專案中的前置處理

Xamarin.Forms 共用資產專案可以執行不同的程式碼，每個平台使用的 C# 前置處理器指示詞`#if`， `#elif`，和`endif`。 這示範於[ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![變數的三個螢幕擷取畫面會格式化段落](images/ch09fg01-small.png "裝置機型與作業系統")](images/ch09fg01-large.png#lightbox "裝置機型與作業系統")

不過，產生的程式碼可能會不美觀且難以讀取。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共用資產專案中的平行類別

更結構化的方法，來執行 SAP 平台特定程式碼所示[ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)範例。 每個平台專案包含同名的類別和方法，但針對該特定的平台實作。 SAP 然後只具現化類別，並呼叫方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService 和可攜式類別庫

> [!NOTE] 
> .NET Standard 程式庫已取代的可攜式類別庫。 活頁簿中的所有範例程式碼已經都轉換成使用.NET 標準程式庫。

程式庫通常無法存取應用程式專案中的類別。 這項限制似乎防止所示的技巧**PlatInfoSap2**文件庫中使用。 不過，Xamarin.Forms 包含類別，名為[ `DependencyService` ](xref:Xamarin.Forms.DependencyService) ，會使用.NET 反映來存取應用程式專案中的公用類別，從程式庫。

程式庫必須定義`interface`需要在每個平台使用的成員。 然後，每個平台包含該介面的實作。 實作介面的類別必須用來識別[DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute)組件層級。

程式庫，然後使用泛型[ `Get` ](xref:Xamarin.Forms.DependencyService.Get*)方法`DependencyService`取得平台類別可實作介面的執行個體。

這示範於[ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)範例。

## <a name="platform-specific-sound-generation"></a>產生特定平台音效

[ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)範例會新增至的嗶聲**MonkeyTap**藉由存取中每個平台的聲音產生功能的程式。

## <a name="related-links"></a>相關連結

- [第 9 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第 9 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
