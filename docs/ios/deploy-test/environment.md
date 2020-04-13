---
title: Xamarin.iOS 應用程式的執行環境
description: 本文件描述如何設定 Xamarin.iOS 應用程式的暫時性和永久性環境變數。 這些變數可在專案內容中指定，或當成 mtouch 封裝工具的額外引數。
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: b2452c50fa978f3cabb718afe7330bf0fb701ce3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73030235"
---
# <a name="execution-environment-for-xamarinios-apps"></a>Xamarin.iOS 應用程式的執行環境

「執行環境」** 是一組環境變數，會影響程式的執行。 環境變數可在專案屬性中暫時設定，或透過將額外引數指定給 mtouch 封裝工具來永久設定。

## <a name="temporary-environment-variables"></a>暫存環境變數

暫存環境變數在執行 **>常規**部分中的項目**屬性**/**選項選項**。 這些環境變數只會在應用程式使用 Visual Studio for Mac 啟動時才會生效。若應用程式是透過手動點選啟動的，則將不會設定這些環境變數。

## <a name="permanent-environment-variables"></a>永久環境變數

永久環境變數可藉由將額外的引數指定給 mtouch 封裝工具來設定。 這些環境變數會編譯到可執行檔中，即使應用程式不是從 Visual Studio for Mac 啟動也會設定這些環境變數。

## <a name="example"></a>範例

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```
