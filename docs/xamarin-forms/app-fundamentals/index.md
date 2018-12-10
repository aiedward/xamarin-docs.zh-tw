---
title: Xamarin.Forms 應用程式基本概念
description: 探索 Xamarin.Forms 應用程式開發的基本概念，包括所有必要的核心概念，以及諸如協助工具和當地語系化等最終步驟。
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2018
ms.openlocfilehash: 015a7dfa3d55b411e6626ae2ac70fd2930661e65
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898988"
---
# <a name="xamarinforms-application-fundamentals"></a>Xamarin.Forms 應用程式基本概念

## <a name="accessibilityaccessibilityindexmd"></a>[協助工具選項](accessibility/index.md)

使用 Xamarin.Forms 納入無障礙功能 (例如支援螢幕閱讀工具) 的提示。

## <a name="app-classapplication-classmd"></a>[應用程式類別](application-class.md)

`Application` 類別是 Xamarin.Forms 的起始點：每個應用程式都必須實作子類別 `App` 來設定初始頁面。 也會提供用於簡單資料儲存區的 `Properties` 集合。 可以在 C# 或 XAML 中定義。

## <a name="app-lifecycleapp-lifecyclemd"></a>[應用程式生命週期](app-lifecycle.md)

`Application` 類別 `OnStart`、`OnSleep` 和 `OnResume` 方法，以及強制回應導覽事件，可讓您以自訂程式碼處理應用程式生命週期事件。

## <a name="behaviorsbehaviorsindexmd"></a>[行為](behaviors/index.md)

使用者介面控制項可以輕鬆擴充，而不需要使用行為來新增功能以進行子類別化。

## <a name="custom-rendererscustom-rendererindexmd"></a>[自訂轉譯器](custom-renderer/index.md)

自訂轉譯器可讓開發人員「覆寫」Xamarin.Forms 控制項的預設轉譯，以在每個平台上自訂其外觀和行為 (如需要，可使用原生 SDK)。

## <a name="data-bindingdata-bindingindexmd"></a>[資料繫結](data-binding/index.md)

資料繫結會連結兩個物件的屬性，以便在其中一個屬性變更時，自動反映在另一個屬性上。 資料繫結是 Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) 應用程式架構不可或缺的一部分。

## <a name="dependency-servicedependency-serviceindexmd"></a>[相依性服務](dependency-service/index.md)

`DependencyService` 提供簡單的定位器，可讓您在共用程式碼中編寫介面，並提供會自動解析的平台特定實作，方便您在 Xamarin.Forms 中參考平台特定功能。

## <a name="effectseffectsindexmd"></a>[Effects](effects/index.md)

效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。

## <a name="filesfilesmd"></a>[檔案](files.md)

透過 .NET Standard 程式庫的程式碼或使用內嵌資源，可以使用 Xamarin.Forms 處理檔案。

## <a name="gesturesgesturesindexmd"></a>[Gestures](gestures/index.md)

Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) 類別支援使用者介面控制項上的點選、捏合以及平移手勢。

## <a name="localizationlocalizationindexmd"></a>[當地語系化](localization/index.md)

內建的 .NET 當地語系化架構，可用於透過 Xamarin.Forms 建置跨平台多語系應用程式。

## <a name="local-databasesdatabasesmd"></a>[本機資料庫](databases.md)

Xamarin.Forms 使用 SQLite 資料庫引擎來支援資料庫驅動型應用程式，讓您可以將物件載入和儲存至共用程式碼。

## <a name="messaging-centermessaging-centermd"></a>[訊息中心](messaging-center.md)

除了簡單的訊息合約外，Xamarin.Forms `MessagingCenter` 可讓檢視模型和其他元件能夠在不需要知道彼此資訊的情況下進行通訊。

## <a name="navigationnavigationindexmd"></a>[巡覽](navigation/index.md)

Xamarin.Forms 會根據所使用的 `Page` 類型，提供多種不同的網頁導覽體驗。

## <a name="shellshellmd"></a>[Shell](shell.md)

Xamarin.Forms Shell 是應用程式的容器，可提供大部分應用程式需要的基本 UI 功能，讓您專注於應用程式的核心工作負載。

## <a name="templatestemplatesindexmd"></a>[範本](templates/index.md)

控制項範本可在執行階段輕鬆設定、重新設定佈景主題應用程式頁面，而資料範本可在受支援的控制項上定義資料呈現方式。

## <a name="triggerstriggersmd"></a>[觸發程序](triggers.md)

回應屬性變更及 XAML 中的事件以更新控制項。


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
