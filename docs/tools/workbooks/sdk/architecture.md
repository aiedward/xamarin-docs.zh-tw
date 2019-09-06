---
title: 架構概觀
description: 本檔說明 Xamarin Workbooks 的架構，檢查互動式代理程式和互動式用戶端如何搭配使用。
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7129d0bedddb272ef87e3d209cb05c2ca0c0acf4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285287"
---
# <a name="architecture-overview"></a>架構概觀

Xamarin Workbooks 有兩個必須彼此搭配使用的主要元件：_代理程式_和_用戶端_。

## <a name="interactive-agent"></a>互動式代理程式

代理程式元件是小型平臺特定的元件，會在 .NET 應用程式的內容中執行。

Xamarin Workbooks 為多個平臺（例如 iOS、Android、Mac 和 WPF）提供預先建立的「空白」應用程式。 這些應用程式會明確裝載代理程式。

在即時檢查（Xamarin Inspector）期間，代理程式會透過 IDE 偵錯工具插入到現有的應用程式中，做為一般開發 & 的「工作流程」的一部分。

## <a name="interactive-client"></a>互動式用戶端

用戶端是一種原生 shell （在 Mac 上為 Cocoa，在 Windows 上為 WPF），其裝載 web 瀏覽器介面來呈現活頁簿/複寫介面。 從 SDK 的觀點來看，所有用戶端整合都會以 JavaScript 和 CSS 來執行。

用戶端會負責將原始程式碼編譯成小型元件，並將它們傳送到連接的代理程式，以便執行。 執行結果會傳送回用戶端進行轉譯。 活頁簿中的每個資料格都會產生一個元件，以參考上一個儲存格的元件。

因為代理程式可以在任何類型的 .NET 平臺上執行，而且能夠存取執行中應用程式中的任何專案，所以必須小心以不限平臺的方式來序列化結果。
