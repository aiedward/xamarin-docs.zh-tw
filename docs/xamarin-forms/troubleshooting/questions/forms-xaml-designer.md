---
title: 為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: ce318faab1af07b95a7769d81a506979b37a34e4
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842994"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？

Xamarin 目前不支援 XAML 檔案的視覺化設計工具。 因此，嘗試以編碼方式在 Visual Studio 的*XAML Ui 設計*工具或*xaml ui 設計*工具中開啟表單 XAML 檔案時，會擲回下列錯誤訊息：

> 「無法使用選取的編輯器開啟檔案。 請選擇其他編輯器」。

這項限制會在[Xamarin. 表單 XAML 基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)指南中說明：

> 「還沒有視覺化設計工具可用來在 Xamarin 中產生 XAML。表單應用程式，因此必須以手動方式撰寫所有 XAML。」

不過，您可以選取 [ **View] > 其他 Windows > [表單預覽**] 功能表選項來顯示 [XAMARIN] XAML 預覽程式。
