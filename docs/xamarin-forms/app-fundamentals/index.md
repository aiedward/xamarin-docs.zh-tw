---
title: Xamarin.Forms 應用程式基本概念
description: 瀏覽 Xamarin.Forms 應用程式開發，包括所有必要的核心概念、 透過最後的裝點，例如協助工具和當地語系化的基本概念。
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 515dbd2683619cfcfb7a6c8ecac6bc147265ef7d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995608"
---
# <a name="xamarinforms-application-fundamentals"></a>Xamarin.Forms 應用程式基本概念

## <a name="accessibilityaccessibilityindexmd"></a>[協助工具選項](accessibility/index.md)

納入可存取的功能 （例如支援螢幕閱讀工具） 使用 Xamarin.Forms 的祕訣。

## <a name="app-classapplication-classmd"></a>[應用程式類別](application-class.md)

`Application`類別 Xamarin.Forms 的起始點，每個應用程式必須實作一個子類別`App`設定初始頁面。 它也提供`Properties`簡單的資料存放裝置的集合。 它可以在 C# 或 XAML 中定義。

## <a name="app-lifecycleapp-lifecyclemd"></a>[應用程式生命週期](app-lifecycle.md)

`Application`類別`OnStart`， `OnSleep`，和`OnResume`方法，以及強制回應導覽事件，可讓您處理自訂程式碼的應用程式生命週期事件。

## <a name="behaviorsbehaviorsindexmd"></a>[行為](behaviors/index.md)

使用者介面控制項可以輕鬆地擴充而不需要子類別化使用行為來加入功能。

## <a name="custom-rendererscustom-rendererindexmd"></a>[自訂轉譯器](custom-renderer/index.md)

自訂轉譯器可讓開發人員 'override' Xamarin.Forms 控制項，以自訂其外觀和行為 （如有需要，請使用原生 Sdk） 的每個平台上的預設轉譯。

## <a name="data-bindingdata-bindingindexmd"></a>[資料繫結](data-binding/index.md)

資料繫結連結兩個物件的屬性，允許變更自動反映在另一個屬性的其中一個屬性。 資料繫結是不可或缺的一部分 Model View ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) 應用程式架構。

## <a name="dependency-servicedependency-serviceindexmd"></a>[相依性服務](dependency-service/index.md)

`DependencyService`提供簡單的定位器，讓您可以共用程式碼中的程式碼介面，並提供平台特定實作會自動解決，方便您參考在 Xamarin.Forms 中的平台專屬功能。

## <a name="effectseffectsindexmd"></a>[Effects](effects/index.md)

效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。

## <a name="filesfilesmd"></a>[檔案](files.md)

使用 Xamarin.Forms 處理的檔案，可以使用程式碼，在.NET Standard 程式庫，或使用內嵌的資源來達成。

## <a name="gesturesgesturesindexmd"></a>[Gestures](gestures/index.md)

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer)類別支援使用者介面控制項上的點選、 縮小，以及移動瀏覽軌跡。

## <a name="localizationlocalizationindexmd"></a>[當地語系化](localization/index.md)

內建.NET 當地語系化的架構可用來建置跨平台與 Xamarin.Forms 的多語系應用程式。

## <a name="local-databasesdatabasesmd"></a>[本機資料庫](databases.md)

Xamarin.Forms 可支援使用 SQLite 資料庫引擎，讓您能夠載入，並將物件儲存在共用程式碼的資料庫導向應用程式。

## <a name="messaging-centermessaging-centermd"></a>[訊息中心](messaging-center.md)

Xamarin.Forms`MessagingCenter`可讓檢視模型和其他元件通訊，而不需要知道彼此資訊的任何項目，除了簡單的訊息合約。

## <a name="navigationnavigationindexmd"></a>[巡覽](navigation/index.md)

Xamarin.Forms 提供不同的網頁瀏覽體驗，視數`Page`輸入使用。

## <a name="templatestemplatesindexmd"></a>[範本](templates/index.md)

控制項範本提供的功能，輕鬆地設定和重新佈景主題應用程式頁面，在執行階段，而資料範本提供支援的控制項上定義資料的呈現方式的能力。

## <a name="triggerstriggersmd"></a>[觸發程序](triggers.md)

更新控制項的屬性變更及 XAML 中的事件回應。


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
