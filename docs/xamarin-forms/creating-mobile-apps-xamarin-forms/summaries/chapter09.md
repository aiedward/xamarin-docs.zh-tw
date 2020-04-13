---
title: 第9章摘要。 平台特定 API 呼叫
description: 使用 Xamarin.表單創建行動應用程式:第 9 章摘要。 平台特定 API 呼叫
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334493"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第9章摘要。 平台特定 API 呼叫

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

有時需要運行一些因平臺而異的代碼。 本章探討這些技術。

## <a name="preprocessing-in-the-shared-asset-project"></a>共用資產專案中的預處理

Xamarin.Forms 共享資產專案可以使用 C#`#if``#elif`預處理器指令 和 為每個`endif`平台執行不同的代碼。 這體現在[**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1)中:

[![可變格式段落的三重螢幕擷取](images/ch09fg01-small.png "裝置型號與作業系統")](images/ch09fg01-large.png#lightbox "裝置型號與作業系統")

但是,由此產生的代碼可能很醜且難以閱讀。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共用資產專案中的併行類

[**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)範例展示在 SAP 執行特定於平台代碼的更結構化的方法。 每個平臺專案都包含一個名稱相同的類和方法,但為該特定平台實現。 然後,SAP 只需實例化類並調用方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>依賴項服務和可攜式類庫

> [!NOTE] 
> 便攜式類庫已被 .NET 標準庫替換。 書中的所有範例代碼已轉換為使用 .NET 標準庫。

庫通常無法訪問應用程式專案中的類。 此限制似乎阻止**PlatinfoSap2**中顯示的技術在庫中使用。 但是,Xamarin.Forms 包含一個[`DependencyService`](xref:Xamarin.Forms.DependencyService)名為的類,該類使用 .NET 反射從庫中訪問應用程式專案中的公共類。

函式庫必須使用每個`interface`平台中需要使用的成員定義 。 然後,每個平臺都包含該介面的實現。 實現介面的類必須在程式集級別上使用[依賴屬性](xref:Xamarin.Forms.DependencyAttribute)進行標識。

然後,庫使用的[`Get`](xref:Xamarin.Forms.DependencyService.Get*)`DependencyService`泛型方法獲取實現介面的平臺類的實例。

這在[**「顯示平台資訊」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)範例中進行了演示。

## <a name="platform-specific-sound-generation"></a>特定於平台的聲音產生

[**MonkeyTap WithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)範例透過存取每個平臺中的聲生成設施,為**MonkeyTap**程式添加蜂鳴音。

## <a name="related-links"></a>相關連結

- [第九章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第九章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [相依性服務](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
