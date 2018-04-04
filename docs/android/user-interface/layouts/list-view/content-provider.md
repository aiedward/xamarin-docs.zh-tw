---
title: 使用 ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b9b6340d4aaf386c7b4be8ebf366589582771be2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="using-a-contentprovider"></a>使用 ContentProvider

CursorAdapters 也可用來顯示 ContentProvider 資料。
ContentProviders 可讓您存取其他應用程式所公開的資料 (包括 Android 的系統資料，例如連絡人、 媒體和行事曆資訊)。

若要存取 ContentProvider 慣用的方法是使用 LoaderManager CursorLoader。 LoaderManager 首見於 Android 3.0 （API 層級 11、 Honeycomb），以移動關閉主執行緒，封鎖的工作，並使用 CursorLoader 允許要在執行緒中載入之前繫結到 ListView 顯示的資料。

請參閱[簡介 ContentProviders](~/android/platform/content-providers/index.md)如需詳細資訊。

