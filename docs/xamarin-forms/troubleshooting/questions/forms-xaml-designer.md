---
title: 為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247792"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？

Xamarin.Forms 目前不支援視覺化設計工具的 XAML 檔案。 因為這個緣故，嘗試將其中一個 Visual Studio 中開啟表單的 XAML 檔案時*XAML UI 設計工具*或*XAML UI 設計工具，以編碼方式*，就會擲回下列錯誤訊息：

> 「 無法使用選取的編輯器開啟檔案。 請選擇另一個編輯器。 」

這項限制所述[概觀](~/xamarin-forms/xaml/xaml-basics/index.md#Overview)一節[Xamarin.Forms XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)指南：

> 「 沒有卻在 Xamarin.Forms 應用程式產生 XAML 的視覺化設計工具，因此，所有 XAML 必須都是手寫。 」

不過，顯示 Xamarin.Forms XAML 預覽程式，方法是選取**檢視 > 其他 Windows > Xamarin.Forms 預覽程式**功能表選項。
