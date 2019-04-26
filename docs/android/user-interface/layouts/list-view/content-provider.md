---
title: 使用 ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189723"
---
# <a name="using-a-contentprovider"></a>使用 ContentProvider

CursorAdapters 也可用來顯示 ContentProvider 資料。
ContentProviders 可讓您存取其他應用程式所公開的資料 (包括 Android 系統資料，例如連絡人、 媒體和行事曆資訊)。

若要存取 ContentProvider 慣用的方法是使用 LoaderManager CursorLoader。 LoaderManager 移動關閉主執行緒，封鎖工作的 Android 3.0 （API 層級 11、 Honeycomb） 中導入，並使用 CursorLoader 允許要在執行緒中載入之前繫結至 ListView 顯示的資料。

請參閱[ContentProviders 簡介](~/android/platform/content-providers/index.md)如需詳細資訊。

