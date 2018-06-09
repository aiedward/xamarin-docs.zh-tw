---
title: Xamarin.Forms 應用程式基本概念
description: 瀏覽 Xamarin.Forms 應用程式開發，包含所有必要的核心概念，透過以修飾，例如可存取性和當地語系化的基本概念。
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: f843f4b1c20daba2a504bd0e70ae51f4d0405c06
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240242"
---
# <a name="xamarinforms-application-fundamentals"></a>Xamarin.Forms 應用程式基本概念

## <a name="accessibilityaccessibilityindexmd"></a>[協助工具選項](accessibility/index.md)

（例如支援的螢幕助讀工具） 可存取的功能併入 xamarin.forms 的祕訣。

## <a name="app-classapplication-classmd"></a>[應用程式類別](application-class.md)

`Application`類別是 Xamarin.Forms 的起始點，因為每個應用程式必須實作子類別`App`設定初始頁面。 它也提供`Properties`簡單的資料存放裝置的集合。 它可以在 C# 或 XAML 定義。

## <a name="app-lifecycleapp-lifecyclemd"></a>[應用程式生命週期](app-lifecycle.md)

`Application`類別`OnStart`， `OnSleep`，和`OnResume`方法，以及強制回應巡覽事件，可讓您處理自訂程式碼的應用程式生命週期事件。

## <a name="behaviorsbehaviorsindexmd"></a>[行為](behaviors/index.md)

使用者介面控制項可以輕易地延伸子類別化不使用行為來加入功能。

## <a name="custom-rendererscustom-rendererindexmd"></a>[自訂轉譯器](custom-renderer/index.md)

自訂轉譯器可讓開發人員 'override' Xamarin.Forms 控制項，以自訂其外觀和行為 （如有需要，請使用原生 Sdk） 的每個平台上的預設呈現。

## <a name="data-bindingdata-bindingindexmd"></a>[資料繫結](data-binding/index.md)

資料繫結連結兩個物件的屬性，允許變更自動反映在另一個屬性的其中一個屬性。 資料繫結是模型-檢視 ViewModel 不可或缺的一部分 ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) 應用程式架構。

## <a name="dependency-servicedependency-serviceindexmd"></a>[相依性服務](dependency-service/index.md)

`DependencyService`提供簡單的定位器，以便您可以在共用程式碼中的介面程式碼，並提供特定平台的實作，會自動進行解析，以便輕鬆地參照特定平台功能，在 Xamarin.Forms。

## <a name="effectseffectsindexmd"></a>[Effects](effects/index.md)

效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。

## <a name="gesturesgesturesindexmd"></a>[Gestures](gestures/index.md)

Xamarin.Forms [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)類別支援使用者介面控制項上的點選、 縮小和取景位置調整筆勢。

## <a name="localizationlocalizationindexmd"></a>[當地語系化](localization/index.md)

內建的.NET 當地語系化 framework 可用來建置跨平台與 Xamarin.Forms 多語系應用程式。

## <a name="local-databasesdatabasesmd"></a>[本機資料庫](databases.md)

Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。

## <a name="messaging-centermessaging-centermd"></a>[訊息中心](messaging-center.md)

Xamarin.Forms`MessagingCenter`啟用檢視模型和其他元件，以與通訊，而不需要知道任何關於彼此除了簡單的訊息合約。

## <a name="navigationnavigationindexmd"></a>[巡覽](navigation/index.md)

Xamarin.Forms 提供不同的網頁瀏覽體驗，視數`Page`輸入所使用。

## <a name="templatestemplatesindexmd"></a>[範本](templates/index.md)

控制項範本提供能夠輕鬆地佈景主題和 re 佈景主題應用程式頁面，在執行階段，而資料範本會提供支援的控制項上定義資料的呈現方式的能力。

## <a name="triggerstriggersmd"></a>[觸發程序](triggers.md)

回應屬性變更及 XAML 中的事件，以更新控制項。


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
