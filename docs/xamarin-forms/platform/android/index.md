---
title: "Android 平台功能"
description: "將 Android 特定功能加入至 Xamarin.Forms 應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2016
ms.openlocfilehash: 9b5e9a4c449bc99bd88fc415f5ebb969d2c2a08a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="android-platform-features"></a>Android 平台功能

## <a name="platform-support"></a>平台支援

Xamarin.Forms Android 專案預設會使用舊樣式的 Android 5.0 之前是很常見的控制項轉譯。 使用此範本所建置的應用程式有`FormsApplicationActivity`為其主要活動的基底類別。

## <a name="material-design-via-appcompat"></a>透過 AppCompat 材料設計

Xamarin.Forms 也有選擇性`FormsAppCompatActivity`使用**AppCompat** Android 實作材料設計主題所提供的功能。

若要 Xamarin.Forms Android 專案中加入材料設計佈景主題，請遵循[支援 AppCompat 的安裝指示](appcompat.md)

以下是**Todo**範例預設`FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Todo 範例應用程式沒有 AppCompat")](images/before-appcompat.png#lightbox "沒有 AppCompat Todo 範例應用程式")

這是相同的程式碼之後將專案升級成使用和`FormsAppCompatActivity`（和加入其他的佈景主題的資訊）：

[![](images/post-appcompat-sml.png "Todo 範例應用程式與 AppCompat 和佈景主題")](images/post-appcompat.png#lightbox "Todo 範例應用程式與 AppCompat 和佈景主題")

> [!NOTE]
> 當使用`FormsAppCompatActivity`、[基底類別的一些 Android 的自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)會不同。


## <a name="related-links"></a>相關連結

- [新增材料設計支援](appcompat.md)
