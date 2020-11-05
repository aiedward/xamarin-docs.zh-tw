---
title: 第9章的摘要。 平台特定 API 呼叫
description: 使用：第 Xamarin.Forms 9 章的摘要建立 Mobile Apps。 平台特定 API 呼叫
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6dec0c3e3fc4d25aecfe4e4141c4cc285fd7f8d8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366187"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第9章的摘要。 平台特定 API 呼叫

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

有時必須執行一些依平臺而異的程式碼。 本章將探索這些技術。

## <a name="preprocessing-in-the-shared-asset-project"></a>共用資產專案中的前置處理

Xamarin.Forms共用資產專案可以使用 c # 預處理器指示詞、和，為每個平臺執行不同的程式碼 `#if` `#elif` `endif` 。 這會在 [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1)中示範：

[![可變格式化段落的三個螢幕擷取畫面](images/ch09fg01-small.png "裝置型號和作業系統")](images/ch09fg01-large.png#lightbox "裝置型號和作業系統")

不過，產生的程式碼可能不太容易理解。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共用資產專案中的平行類別

[**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)範例示範在 SAP 中執行平臺特定程式碼的更結構化方法。 每個平臺專案都包含名稱相同的類別和方法，但會針對該特定平臺執行。 然後，SAP 只會具現化類別，並呼叫方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService 和便攜類別庫

> [!NOTE]
> 便攜類別庫已由 .NET Standard 程式庫取代。 書籍中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

程式庫通常不能存取應用程式專案中的類別。 這項限制似乎會防止 **PlatInfoSap2** 中所顯示的技巧在程式庫中使用。 不過， Xamarin.Forms 包含名為 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 的類別，該類別會使用 .net 反映從程式庫存取應用程式專案中的公用類別。

程式庫必須針對 `interface` 每個平臺所需使用的成員定義。 然後，每個平臺都包含該介面的實作為。 您必須使用元件層級上的 [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) 來識別實介面的類別。

然後，程式庫會使用的泛型 [`Get`](xref:Xamarin.Forms.DependencyService.Get*) 方法 `DependencyService` ，取得平臺類別的實例來執行介面。

[**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)範例會示範這一點。

## <a name="platform-specific-sound-generation"></a>平臺特定的音效產生

[**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)範例會在每個平臺中存取音效產生工具，以將聲段新增至 **MonkeyTap** 程式。

## <a name="related-links"></a>相關連結

- [第9章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第9章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
