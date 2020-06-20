---
title: 第9章的摘要。 平台特定 API 呼叫
description: 建立 Mobile Apps Xamarin.Forms ：第9章的摘要。 平台特定 API 呼叫
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8feb636057f1e11c7df90236dee44697203d51c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136849"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第9章的摘要。 平台特定 API 呼叫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

有時必須執行一些依平臺而異的程式碼。 本章將探討這些技巧。

## <a name="preprocessing-in-the-shared-asset-project"></a>共用資產專案中的前置處理

Xamarin.Forms共用資產專案可以使用 c # 預處理器指示詞、和，為每個平臺執行不同的程式碼 `#if` `#elif` `endif` 。 這會在[**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1)中示範：

[![可變格式段落的三向螢幕擷取畫面](images/ch09fg01-small.png "裝置型號和作業系統")](images/ch09fg01-large.png#lightbox "裝置型號和作業系統")

不過，產生的程式碼可能會變得不好，而且很容易閱讀。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共用資產專案中的平行類別

在[**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)範例中會示範更具結構化的方式來執行 SAP 中的平臺特定程式碼。 每個平臺專案都包含名稱相同的類別和方法，但會針對該特定平臺進行執行。 然後，SAP 只會具現化類別，並呼叫方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService 和可移植的類別庫

> [!NOTE] 
> 可移植的類別庫已由 .NET Standard 程式庫取代。 本書中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

程式庫通常無法存取應用程式專案中的類別。 這項限制似乎是為了避免在程式庫中使用**PlatInfoSap2**中所顯示的技術。 不過， Xamarin.Forms 包含名為 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 的類別，它會使用 .net 反映從程式庫存取應用程式專案中的公用類別。

程式庫必須定義，其中 `interface` 包含每個平臺所需使用的成員。 然後，每個平臺都包含該介面的執行。 執行介面的類別必須以元件層級上的[DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute)來識別。

然後，程式庫會使用的泛型 [`Get`](xref:Xamarin.Forms.DependencyService.Get*) 方法 `DependencyService` 來取得平臺類別的實例，以執行介面。

這會在[**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)範例中示範。

## <a name="platform-specific-sound-generation"></a>平臺特定的音效產生

[**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)範例會藉由存取每個平臺中的音效產生功能，為**MonkeyTap**程式新增嗶聲。

## <a name="related-links"></a>相關連結

- [第9章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第9章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
