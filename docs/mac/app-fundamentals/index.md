---
title: Xamarin. Mac 應用程式基本概念
description: 本檔連結的指南會說明開發 Xamarin. Mac 應用程式時所需瞭解的各種概念。
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2015
ms.openlocfilehash: 2603360162ee9918e83b9f5c74b8086f71d02df8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030104"
---
# <a name="xamarinmac-application-fundamentals"></a>Xamarin. Mac 應用程式基本概念

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[常見模式和慣用語](~/mac/app-fundamentals/patterns.md)

在透過公開的所有 Apple C#api 中，特定的慣用語和模式會重複出現。 如果您有使用 Xamarin 進行程式設計的經驗，這些功能看起來可能很熟悉。 檔通常會參考這些模式並重複慣用語，因此充分瞭解它們將有助於您瞭解所找到的檔。

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[瞭解 Mac Api](~/mac/app-fundamentals/mac-apis.md)

在大部分的C#情況下，使用 Xamarin 進行開發時，您可以考慮、閱讀和撰寫，而不需要擔心基礎的目標-C api。 不過，有時候您必須閱讀 Apple 的 API 檔，將答案從 Stack Overflow 轉譯為問題的解決方案，或與現有的範例進行比較。

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[主控台應用程式](~/mac/app-fundamentals/console.md)

您也可以建立「無周邊」主控台應用程式，以使用 Xamarin 來存取原生 macOS Api。

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[使用 xib 檔案](~/mac/app-fundamentals/xib.md)

本文涵蓋如何使用在 Xcode 的 Interface Builder 中建立的 xib 檔案，來建立和維護 Xamarin. Mac 應用程式的使用者介面。

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[. 分鏡腳本/. xib less 使用者介面設計](~/mac/app-fundamentals/xibless-ui.md)

本文說明如何直接從C#程式碼建立 Xamarin. Mac 應用程式的使用者介面，而不需使用 Xcode 的 Interface Builder 搭配. 腳本或 xib 檔案。

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[使用影像](~/mac/app-fundamentals/image.md)

本文說明如何在 Xamarin. Mac 應用程式中使用影像和圖示。 其中包括建立和維護建立應用程式圖示所需的影像，以及在程式C#代碼和 Xcode 的 Interface Builder 中使用影像。

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[資料系結和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)

本文涵蓋使用索引鍵/值編碼和索引鍵-值觀察，以允許資料系結至 Xcode 的 Interface Builder 中的 UI 元素。 使用這項技術，您可以大幅減少需要C#為 Xamarin. Mac 應用程式撰寫的程式碼數量。 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[使用資料庫](~/mac/app-fundamentals/databases.md)

本文說明如何使用索引鍵/值編碼和索引鍵-值觀察，以允許資料系結至 Xcode 的 Interface Builder 中的 UI 元素直接存取 SQLite 資料庫。 它也涵蓋如何使用 SQLite.NET ORM 來提供 SQLite 資料的存取權。

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[使用複製和貼上](~/mac/app-fundamentals/copy-paste.md)

本文說明如何使用貼夾，以在 Xamarin. Mac 應用程式中提供複製和貼上。 它會示範如何使用可在多個應用程式之間共用的標準資料類型，以及如何支援「提供應用程式」中的自訂資料。

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[沙箱化 Xamarin. Mac 應用程式](~/mac/app-fundamentals/sandboxing.md)

本文說明如何在 App Store 上進行用於發行的 Xamarin. Mac 應用程式沙箱。 其中涵蓋所有進入沙箱的元素：容器目錄、權利、使用者決定的許可權、許可權區隔，以及核心強制。

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[使用 Avaudioplayer 播放音效播放音效](~/mac/app-fundamentals/sounds.md)

本文說明如何使用協助程式類別，以使用 Avaudioplayer 播放音效來控制音效的播放。

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[報告錯誤](~/mac/app-fundamentals/troubleshooting.md)

有時候，在處理專案時，我們全都會停滯，因為無法取得 API 以我們想要的方式運作，或嘗試解決 bug。 我們在 Xamarin 的目標是要讓您成功撰寫行動和桌面應用程式，並提供一些資源來協助您。
