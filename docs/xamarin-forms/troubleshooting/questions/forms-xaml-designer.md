---
title: 為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dfc17ecabbf0dc079c3a8096d33bc1a6cfb3eed7
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602719"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>為什麼 Visual Studio XAML 設計工具不適用於 Xamarin.Forms XAML 檔案？

Xamarin.Forms 目前不支援 XAML 檔案的視覺化設計工具。 因此，當您嘗試使用編碼方式在 Visual Studio 的 *XAML Ui 設計* 工具或 *XAML ui 設計* 工具中開啟表單 XAML 檔案時，會擲回下列錯誤訊息：

> 「無法使用選取的編輯器開啟檔案。 請選擇其他編輯器」。

[ Xamarin.Forms XAML 基本概念](~/xamarin-forms/xaml/xaml-basics/index.md)指南中說明了這項限制：

> 「沒有可在應用程式中產生 XAML 的視覺化設計工具 Xamarin.Forms ，因此所有的 xaml 都必須以手動方式撰寫。」

不過，您 Xamarin.Forms 可以選取 [ **View > 其他 Windows > Xamarin.Forms 編輯器** ] 功能表選項來顯示 XAML 編輯器。
