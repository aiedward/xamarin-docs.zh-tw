---
title: 使用 ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c99b3d64f4f5033240c086af8677d31485a44f01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028925"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>使用 ContentProvider 搭配 Xamarin. Android

CursorAdapters 也可以用來顯示 ContentProvider 中的資料。
ContentProviders 可讓您存取其他應用程式所公開的資料（包括連絡人、媒體和行事曆資訊等 Android 系統資料）。

存取 ContentProvider 的慣用方法是使用 LoaderManager 的 CursorLoader。 LoaderManager 是在 Android 3.0 （API 層級11，Honeycomb）中引進，可將封鎖工作從主執行緒移出，而使用 CursorLoader 可讓資料載入執行緒中，然後再系結至 ListView 以供顯示。

如需詳細資訊，請參閱[ContentProviders 的簡介](~/android/platform/content-providers/index.md)。
