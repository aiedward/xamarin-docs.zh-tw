---
title: 為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 1f82f16429ca23a4ba6806f775310dd90126096e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？

Xamarin.Forms 目前不支援 XAML 檔案的視覺化設計工具。 因為這個緣故，嘗試在其中一個 Visual Studio 中開啟表單 XAML 檔案時*XAML UI 設計工具*或*XAML UI 設計工具使用編碼方式*，會擲回下列錯誤訊息：

> 「 無法使用選取的編輯器開啟檔案。 請選擇其他編輯器。 」

這項限制述[概觀](~/xamarin-forms/xaml/xaml-basics/index.md#Overview)區段[Xamarin.Forms XAML 基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)指南：

> 「 有尚未產生 XAML Xamarin.Forms 應用程式中的視覺化設計工具，讓所有 XAML 必須都是手寫。 」
