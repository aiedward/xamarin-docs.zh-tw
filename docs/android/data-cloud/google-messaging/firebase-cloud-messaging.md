---
title: Firebase 雲端傳訊
description: Firebase 雲端通訊（FCM）是一種服務，可在行動應用程式和伺服器應用程式之間進行訊息處理。 本文概述 FCM 的運作方式，並說明如何設定 Google 服務，讓您的應用程式可以使用 FCM。
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: c97c931445122cbaa613b87e3778f4dc9e92f4d0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023659"
---
# <a name="firebase-cloud-messaging"></a>Firebase 雲端傳訊

_Firebase 雲端通訊（FCM）是一種服務，可在行動應用程式和伺服器應用程式之間進行訊息處理。本文概述 FCM 的運作方式，並說明如何設定 Google 服務，讓您的應用程式可以使用 FCM。_

[![Firebase 雲端通訊主圖影像](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

本主題概要說明 Firebase 雲端通訊如何在您的 Xamarin Android 應用程式與應用程式伺服器之間路由傳送訊息，並提供逐步程式來取得認證，讓您的應用程式可以使用 FCM 服務。

## <a name="overview"></a>總覽

Firebase 雲端通訊（FCM）是一種跨平臺服務，可處理伺服器應用程式與行動用戶端應用程式之間的訊息傳送、路由和佇列。 FCM 是 Google 雲端通訊（GCM）的後繼，它是建置於 Google Play Services 上。

如下圖所示，FCM 會作為訊息傳送者與用戶端之間的媒介。 *用戶端應用程式*是在裝置上執行且已啟用 FCM 的應用程式。 *應用程式伺服器*（由您或您的公司提供）是 FCM 啟用的伺服器，您的用戶端應用程式會透過 FCM 與之通訊。 不同于 GCM，FCM 可讓您直接透過 Firebase 主控台通知 GUI 將訊息傳送至用戶端應用程式：

[![FCM 位於用戶端應用程式和應用程式伺服器之間](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

應用程式伺服器可以使用 FCM，將訊息傳送至單一裝置、裝置群組或數個已訂閱主題的裝置。 用戶端應用程式可以使用 FCM 來訂閱來自應用程式伺服器的下游訊息（例如，接收遠端通知）。 如需不同類型 Firebase 訊息的詳細資訊，請參閱[關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)。

## <a name="fcm-in-action"></a>Firebase 雲端通訊的運作方式

當下游訊息從應用程式伺服器傳送至用戶端應用程式時，應用程式伺服器會將訊息傳送至 Google 提供的*FCM 連線伺服器*;FCM 連線伺服器接著會將訊息轉送至執行用戶端應用程式的裝置。 訊息可以透過 HTTP 或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可擴充訊息和目前狀態通訊協定）傳送。 因為用戶端應用程式不一定會連線或執行，所以 FCM 連線伺服器會將並儲存訊息，並在用戶端應用程式重新連接並可供使用時，將它們傳送給它們。 同樣地，如果應用程式伺服器無法使用，FCM 會將用戶端應用程式的上游訊息加入至應用程式伺服器。 如需 FCM 連接伺服器的詳細資訊，請參閱[關於 Firebase 雲端通訊伺服器](https://firebase.google.com/docs/cloud-messaging/server)。

FCM 會使用下列認證來識別應用程式伺服器和用戶端應用程式，並使用這些認證透過 FCM 來授權訊息交易：

- <a name="fcm-in-action-sender-id"></a>寄件者**識別碼**&ndash; 傳送者*識別碼*是當您建立 Firebase 專案時所指派的唯一數值。 寄件者識別碼是用來識別每個可以將訊息傳送至用戶端應用程式的應用程式伺服器。 寄件者識別碼也是您的專案編號;當您註冊專案時，您會從 Firebase 主控台取得寄件者識別碼。 `496915549731`的寄件者識別碼範例。

- <a name="fcm-in-action-api-key"></a>**Api 金鑰 &ndash;** *api 金鑰*可讓應用程式伺服器存取 Firebase services;FCM 會使用此金鑰來驗證應用程式伺服器。 此認證也稱為*伺服器金鑰*或*Web API 金鑰*。 `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`API 金鑰的範例。

- <a name="fcm-in-action-app-id"></a>**應用程式識別碼**&ndash; 用戶端應用程式的身分識別（獨立于任何指定的裝置），其會註冊以接收來自 FCM 的訊息。 `1:415712510732:android:0e1eb7a661af2460`應用程式識別碼的範例。

- <a name="fcm-in-action-registration-token"></a>*註冊***權杖 &ndash; 註冊權杖（** 也稱為「*實例識別碼*」）是指定裝置上用戶端應用程式的 FCM 身分識別。 註冊權杖會在執行時間產生，&ndash; 當您的應用程式在裝置上執行時，第一次向 FCM 註冊時，就會收到註冊權杖。 註冊權杖會授權用戶端應用程式的實例（在該特定裝置上執行），以接收來自 FCM 的訊息。
    註冊權杖的範例是 `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` （非常長的字串）。

[設定 Firebase 雲端通訊](#setup_fcm)（本指南稍後）提供建立專案及產生這些認證的詳細指示。 當您在[Firebase 主控台](https://console.firebase.google.com/)中建立新的專案時，會建立名為**google-** FCM 的認證檔案 &ndash; 將此檔案新增至您的 Xamarin Android 專案，如[使用的遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)中所述。

下列各節說明當用戶端應用程式透過 FCM 與應用程式伺服器通訊時，如何使用這些認證。

<a name="registration" />

### <a name="registration-with-fcm"></a>向 FCM 註冊

用戶端應用程式必須先向 FCM 註冊，然後才能進行訊息。 用戶端應用程式必須完成下圖所示的註冊步驟：

[![應用程式註冊步驟圖表](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1. 用戶端應用程式會聯絡 FCM 來取得註冊權杖，並將傳送者識別碼、API 金鑰和應用程式識別碼傳遞至 FCM。

2. FCM 會將註冊權杖傳回到用戶端應用程式。

3. 用戶端應用程式（選擇性）會將註冊權杖轉送至應用程式伺服器。

應用程式伺服器會快取註冊權杖，以便後續與用戶端應用程式進行通訊。 應用程式伺服器可以將通知傳送回用戶端應用程式，以指出已收到註冊權杖。 進行此交握之後，用戶端應用程式可以從應用程式伺服器接收訊息（或傳送訊息至）。 如果舊的權杖遭到入侵，用戶端應用程式可能會收到新的註冊權杖（如需應用程式如何接收註冊權杖更新的範例，請參閱[使用 FCM 的遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)）。

當用戶端應用程式不再想要從應用程式伺服器接收訊息時，它可以將要求傳送至應用程式伺服器，以刪除註冊權杖。 如果從裝置卸載用戶端應用程式，FCM 會偵測到此情況，並自動通知應用程式伺服器刪除註冊權杖。

### <a name="downstream-messaging"></a>下游訊息

下圖說明 Firebase 雲端通訊如何儲存和轉送下游訊息：

[![FCM 會針對下游訊息使用儲存和轉寄](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

當應用程式伺服器將下游訊息傳送至用戶端應用程式時，它會使用上述圖表中列舉的下列步驟：

1. 應用程式伺服器會將訊息傳送至 FCM。

2. 如果用戶端裝置無法使用，FCM 伺服器會將訊息儲存在佇列中，以便稍後進行傳輸。 訊息會保留在 FCM 儲存體中，最多4周（如需詳細資訊，請參閱[設定訊息的](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)存留期）。

3. 用戶端裝置可供使用時，FCM 會將訊息轉送至該裝置上的用戶端應用程式。

4. 用戶端應用程式會接收來自 FCM 的訊息、加以處理，並向使用者顯示。 例如，如果訊息為遠端通知，則會在通知區域中向使用者顯示。

在此訊息傳遞案例中（應用程式伺服器會將訊息傳送至單一用戶端應用程式），訊息的長度上限為 4 Kb。

如需有關在 Android 上接收下游 FCM 訊息的詳細資訊，請參閱[使用 FCM 的遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

### <a name="topic-messaging"></a>主題訊息

*主題訊息*讓應用程式伺服器可以將訊息傳送至已選擇特定主題的多個裝置。 您也可以透過 Firebase 主控台的 [通知] GUI 來撰寫及傳送主題訊息。 FCM 會處理將主題訊息路由傳送至訂閱的用戶端。 這項功能可用於天氣警示、股票報價和頭條新聞等訊息。

[![主題消息圖表](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

下列步驟會用於主題訊息（用戶端應用程式取得註冊權杖之後，如先前所述）：

1. 用戶端應用程式會將訂閱訊息傳送至 FCM，以訂閱主題。

2. 應用程式伺服器會將主題訊息傳送至 FCM 以進行散發。

3. FCM 會將主題訊息轉送至已訂閱該主題的用戶端。

如需 Firebase 主題訊息的詳細資訊，請參閱 Google 的[Android 主題訊息](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)。

<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>設定 Firebase 雲端通訊

在您的應用程式中使用 FCM 服務之前，您必須透過[Firebase 主控台](https://console.firebase.google.com/)來建立新的專案（或匯入現有的專案）。 使用下列步驟來為您的應用程式建立 Firebase 雲端通訊專案：

1. 使用您的 Google 帳戶（亦即您的 Gmail 位址）登入[Firebase 主控台](https://console.firebase.google.com/)，然後按一下 [**建立新專案**]：

    [![建立新專案 按鈕](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    如果您有現有的專案，請按一下 [匯**入 Google 專案**]。

2. 在 [**建立專案**] 對話方塊中，輸入專案的名稱，然後按一下 [**建立專案**]。 在下列範例中，會建立名為**XamarinFCM**的新專案：

    [![建立專案 對話方塊](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3. 在**Firebase 主控台中**，按一下 [**將 Firebase 新增至 Android 應用程式**]：

    [![將 Firebase 新增至 Android 應用程式](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4. 在下一個畫面中，輸入您應用程式的 [套件名稱]。 在此範例中，套件名稱是**fcmexample**。 此值必須符合 Android 應用程式的套件名稱。 應用程式昵稱也可以在 [**應用程式昵稱**] 欄位中輸入：

    [![輸入 FCM 範例作為應用程式昵稱](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5. 如果您的應用程式使用動態連結、邀請或 Google 驗證，則您也必須輸入您的 debug 簽署憑證。 如需尋找簽署憑證的詳細資訊，請參閱[尋找金鑰儲存區的 MD5 或 SHA1](~/android/deploy-test/signing/keystore-signature.md)簽章。
    在此範例中，簽署憑證會保留空白。

6. 按一下 [**新增應用程式**]：

    [![按一下 [新增應用程式] 按鈕](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    系統會為應用程式自動產生伺服器 API 金鑰和用戶端識別碼。 這項資訊會封裝在您按一下 [**新增應用程式**] 時自動下載的**google-服務 json**檔案中。
    請務必將此檔案儲存在安全的地方。

如需如何將**google-服務 json**新增至應用程式專案以在 Android 上接收 FCM 推播通知訊息的詳細範例，請參閱[使用 FCM 的遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

## <a name="for-further-reading"></a>進一步閱讀

- Google 的[Firebase 雲端通訊](https://firebase.google.com/docs/cloud-messaging/)提供 Firebase 雲端通訊的主要功能、其運作方式說明，以及設定指示的總覽。

- Google 的[組建應用程式伺服器傳送要求](https://firebase.google.com/docs/cloud-messaging/send-message)說明如何使用您的應用程式伺服器來傳送訊息。

- [Rfc 6120](https://tools.ietf.org/html/rfc6120)和[rfc 6121](https://tools.ietf.org/html/rfc6121)說明並定義可延伸的訊息和目前狀態通訊協定（XMPP）。

- [關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)描述可透過 Firebase 雲端通訊傳送的不同訊息類型。

## <a name="summary"></a>總結

本文提供 Firebase 雲端通訊（FCM）的總覽。 它說明了各種認證，用來識別和授權應用程式伺服器與用戶端應用程式之間的訊息。 其中說明了註冊和下游訊息案例，並詳述向 FCM 註冊應用程式以使用 FCM 服務的步驟。

## <a name="related-links"></a>相關連結

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
