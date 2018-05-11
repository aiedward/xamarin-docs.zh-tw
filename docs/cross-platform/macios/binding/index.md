---
title: 繫結 Objective C
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 721036993061d08dadf8b279e13981caaa51f91f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="binding-objective-c"></a>繫結 Objective C

本節包含各種涵蓋 Objective C 程式庫，建立繫結，所以您可以從 C# 建立的 Xamarin.iOS 或 Xamarin.Mac 應用程式呼叫的文件。

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[概觀](~/cross-platform/macios/binding/overview.md)

本文件包含一些如何繫結會發生的內部資訊。 它是進階的文件的某些技術的資訊。

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)

本文件說明用來建立 C# OBJECTIVE-C Api 和 Objective C 中的慣用語如何對應到.NET 中使用的慣用語繫結程序。
如果您要繫結只 C 應用程式開發介面，您應該使用這個 P/Invoke framework 的標準.NET 機制。

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[繫結定義參考指南](~/cross-platform/macios/binding/binding-types-reference.md)

這是描述所有繫結至磁碟機繫結產生程序的作者可以使用的屬性參考指南。


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目標 Sharpie 是協助啟動繫結的第一次傳遞的命令列工具。 可藉由剖析對應到公用 API 的原生程式庫的標頭檔來運作[繫結定義](~/cross-platform/macios/binding/objective-c-libraries.md)（也可以手動完成此程序）。

## <a name="ios"></a>iOS

[IOS 繫結頁面](~/ios/platform/binding-objective-c/index.md)連結回這些常見的繫結資源，除了下列範例。

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[逐步解說： 繫結 Objective C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)

本文章提供建立繫結專案使用開放原始碼的逐步解說[InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective C 專案做為範例。 InfColorPicker 程式庫提供可重複使用的檢視控制站可讓使用者選取根據其 HSB 表示法，讓更方便使用的色彩選擇的色彩。 目標 Sharpie 將用來協助進行繫結程序。

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[繫結範例](https://github.com/mono/monotouch-bindings)

建立新的繫結的專案時，可能是協力廠商繫結的集合會使用參考。

## <a name="mac"></a>Mac

在過去[Mac 繫結](~/mac/platform/binding.md)已經過非常手動程序。 目前沒有[可下載預覽](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview)的 Mac 繫結專案支援針對將來的版本的 Visual Studio for mac。



## <a name="related-links"></a>相關連結

- [iOS 繫結](~/ios/platform/binding-objective-c/index.md)
- [Mac 繫結](~/mac/platform/binding.md)
