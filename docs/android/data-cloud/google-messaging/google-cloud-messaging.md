---
title: Google 雲端通訊
description: Google 雲端通訊（GCM）是一種服務，可在行動應用程式和伺服器應用程式之間進行訊息處理。 本文概述 GCM 的運作方式，並說明如何設定 Google 服務，讓您的應用程式可以使用 GCM。
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: fef12ab6dc2c42f97e5a2725f58ba4392c21762f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754492"
---
# <a name="google-cloud-messaging"></a>Google 雲端通訊

> [!WARNING]
> 從2018年4月10日起，Google 已淘汰的 GCM。 下列檔和範例專案可能無法再進行維護。 Google 的 GCM 伺服器和用戶端 Api 將會在 2019 5 月29日之後移除。 Google 建議您將 GCM 應用程式遷移至 Firebase 雲端通訊（FCM）。 如需有關 GCM 取代和遷移的詳細資訊，請參閱[Google 已淘汰的雲端通訊](https://developers.google.com/cloud-messaging/)。
>
> 若要開始搭配 Xamarin 使用 Firebase 雲端通訊，請參閱[Firebase 雲端通訊](firebase-cloud-messaging.md)。

_Google 雲端通訊（GCM）是一種服務，可在行動應用程式和伺服器應用程式之間進行訊息處理。本文概述 GCM 的運作方式，並說明如何設定 Google 服務，讓您的應用程式可以使用 GCM。_

[![Google 雲端通訊標誌](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

本主題提供 Google 雲端通訊如何在您的應用程式和應用程式伺服器之間路由傳送訊息的高階總覽，並提供逐步程式來取得認證，讓您的應用程式可以使用 GCM 服務。

## <a name="overview"></a>總覽

Google 雲端通訊（GCM）是一項服務，可處理伺服器應用程式與行動用戶端應用程式之間的訊息傳送、路由和佇列。 *用戶端應用程式*是在裝置上執行且具備 GCM 功能的應用程式。 *應用程式伺服器*（由您或您的公司提供）是您的用戶端應用程式透過 gcm 與通訊的已啟用 gcm 伺服器：

[![GCM 位於用戶端應用程式和應用程式伺服器之間](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

使用 GCM，應用程式伺服器可以將訊息傳送至單一裝置、一組裝置，或已訂閱主題的幾個裝置。 您的用戶端應用程式可以使用 GCM 來訂閱來自應用程式伺服器的下游訊息（例如，接收遠端通知）。 此外，GCM 也可以讓用戶端應用程式將上游訊息傳回給應用程式伺服器。

如需有關為 GCM 執行應用程式伺服器的詳細資訊，請參閱[關於 gcm 連線伺服器](https://developers.google.com/cloud-messaging/server)。

## <a name="google-cloud-messaging-in-action"></a>Google 雲端通訊實際操作

當下游訊息從應用程式伺服器傳送到用戶端應用程式時，應用程式伺服器會將訊息傳送至*GCM 連線伺服器*;接著，GCM 連線伺服器會將訊息轉送至執行您用戶端應用程式的裝置。 訊息可以透過 HTTP 或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可擴充訊息和目前狀態通訊協定）傳送。 因為用戶端應用程式不一定會連線或執行，所以 GCM 連接伺服器會將並儲存訊息，並在用戶端應用程式重新連接和可供使用時，將它們傳送給它們。 同樣地，如果應用程式伺服器無法使用，GCM 會將來自用戶端應用程式的上游訊息加入至應用程式伺服器。

GCM 會使用下列認證來識別應用程式伺服器和您的用戶端應用程式，並使用這些認證透過 GCM 來授權訊息交易：

- **API 金鑰**API 金鑰可讓您的應用程式伺服器存取 Google 服務; &ndash;GCM 會使用此金鑰來驗證您的應用程式伺服器。
    在您可以使用 GCM 服務之前，您必須先從[Google 開發人員主控台](https://console.developers.google.com/)取得 API 金鑰，方法是建立*專案*。 API 金鑰應該保持安全;如需保護 API 金鑰的詳細資訊，請參閱[安全使用 api 金鑰的最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)。

- **寄件者識別碼**發件*人識別碼*會向您的用戶端應用&ndash;程式授與應用程式伺服器，它是唯一的編號，可識別允許將訊息傳送至用戶端應用程式的應用程式伺服器。 &ndash;
    寄件者識別碼也是您的專案編號;當您註冊專案時，您會從 Google 開發人員主控台取得寄件者識別碼。

- **註冊權杖**註冊權杖是您的用戶端應用程式在指定裝置上的 GCM 身分識別。 &ndash; 註冊權杖會在執行時間&ndash;產生，當應用程式在裝置上執行時，第一次向 GCM 註冊時，就會收到註冊權杖。 註冊權杖會授權用戶端應用程式的實例（在該特定裝置上執行），以接收來自 GCM 的訊息。

- **應用程式識別碼**&ndash;用戶端應用程式的身分識別（獨立于任何指定的裝置），其會註冊以接收來自 GCM 的訊息。 在 Android 上，應用程式識別碼是記錄在**androidmanifest.xml**中的套件名稱，例如`com.xamarin.gcmexample`。

[設定 Google 雲端通訊](#settingup)（稍後在本指南中）提供建立專案和產生這些認證的詳細指示。

下列各節說明當用戶端應用程式透過 GCM 與應用程式伺服器通訊時，如何使用這些認證。

### <a name="registration-with-gcm"></a>向 GCM 註冊

安裝在裝置上的用戶端應用程式必須先向 GCM 註冊，然後才能進行訊息。 用戶端應用程式必須完成下圖所示的註冊步驟：

[![應用程式註冊步驟](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1. 用戶端應用程式會聯絡 GCM 以取得註冊權杖，並將寄件者識別碼傳遞給 GCM。

2. GCM 會將註冊權杖傳回到用戶端應用程式。

3. 用戶端應用程式會將註冊權杖轉送至應用程式伺服器。

應用程式伺服器會快取註冊權杖，以便後續與用戶端應用程式進行通訊。 應用程式伺服器可以選擇性地將通知傳送回用戶端應用程式，以指出已收到註冊權杖。 進行此交握之後，用戶端應用程式可以從應用程式伺服器接收訊息（或傳送訊息至）。

當用戶端應用程式不再想要從應用程式伺服器接收訊息時，它可以將要求傳送至應用程式伺服器，以刪除註冊權杖。 如果用戶端應用程式正在接收主題訊息（本文稍後會說明），它可以取消訂閱主題。
如果從裝置卸載用戶端應用程式，GCM 會偵測到此情況，並自動通知應用程式伺服器刪除註冊權杖。

Google 的[註冊用戶端應用程式](https://developers.google.com/cloud-messaging/registration)會更詳細地說明註冊流程;它會說明取消註冊和取消訂閱，並說明卸載用戶端應用程式時的取消註冊程式。

### <a name="downstream-messaging"></a>下游訊息

當應用程式伺服器將下游訊息傳送至用戶端應用程式時，它會遵循下圖所示的步驟：

[![下游訊息存放區 和 正向的圖表](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1. 應用程式伺服器會將訊息傳送至 GCM。

2. 如果用戶端裝置無法使用，GCM 伺服器會將訊息儲存在佇列中，以便稍後進行傳輸。

3. 當用戶端裝置可供使用時，GCM 會將訊息傳送至該裝置上的用戶端應用程式。

4. 用戶端應用程式會接收來自 GCM 的訊息，並據此處理。 例如，如果訊息為遠端通知，則會向使用者顯示。

在此訊息傳遞案例中（應用程式伺服器會將訊息傳送至單一用戶端應用程式），訊息的長度上限為 4 Kb。

如需有關在 Android 上接收下游 GCM 訊息的詳細資訊（包括程式碼範例），請參閱[遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md)。

#### <a name="topic-messaging"></a>主題訊息

*主題訊息*是一種下游訊息，應用程式伺服器會將單一訊息傳送至多個訂閱主題的用戶端應用程式裝置（例如氣象預報）。 主題訊息的長度上限為2KB，而主題訊息可支援每個應用程式最多1000000個訂閱。 如果 GCM 僅用於主題訊息，則不需要用戶端應用程式將註冊權杖傳送至應用程式伺服器。 Google 的實行[主題訊息](https://developers.google.com/cloud-messaging/topic-messaging)說明如何將訊息從應用程式伺服器傳送到多個訂閱特定主題的裝置。

#### <a name="group-messaging"></a>群組訊息

*群組訊息*是一種下游訊息，應用程式伺服器會將單一訊息傳送至屬於群組的多個用戶端應用程式裝置（例如，屬於單一使用者的裝置群組）。 針對 iOS 裝置，群組訊息的長度最多為2KB，Android 裝置的長度上限為 4 KB。 群組限制為最多20個成員。 Google 的[裝置群組訊息](https://developers.google.com/cloud-messaging/notifications)說明應用程式伺服器如何將單一訊息傳送至在屬於群組的裝置上執行的多個用戶端應用程式實例。

### <a name="upstream-messaging"></a>上游訊息

如果您的用戶端應用程式連接到支援[XMPP](https://developers.google.com/cloud-messaging/ccs)的伺服器，它可以將訊息傳回給應用程式伺服器，如下圖所示：

[![上游訊息圖表](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1. 用戶端應用程式會將訊息傳送至 GCM XMPP 連線伺服器。

2. 如果應用程式伺服器已中斷連線，GCM 伺服器會將訊息儲存在佇列中，以供稍後轉送。

3. 當應用程式伺服器重新連接時，GCM 會將訊息轉送到應用程式伺服器。

4. 應用程式伺服器會剖析訊息來驗證用戶端應用程式的身分識別，然後將「ack」傳送至 GCM 以確認訊息接收。

5. 應用程式伺服器會處理訊息。

Google 的[上游訊息](https://developers.google.com/cloud-messaging/ccs#upstream)說明如何結構 JSON 編碼的訊息，並將它們傳送至執行 Google XMPP 型雲端連線伺服器的應用程式伺服器。

<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>設定 Google 雲端通訊

在您的應用程式中使用 GCM 服務之前，您必須先取得存取 Google GCM 伺服器的認證。 下列各節說明完成此程式所需的步驟：

### <a name="enable-google-services-for-your-app"></a>為您的應用程式啟用 Google 服務

1. 使用您的 Google 帳戶（亦即您的 gmail 位址）登入[Google 開發人員主控台](https://developers.google.com/mobile/add?platform=android)，並建立新的專案。 如果您有現有的專案，請選擇您想要啟用 GCM 的專案。 在下列範例中，會建立名為**XamarinGCM**的新專案：

    [![建立 XamarinGCM 專案](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2. 接下來，輸入您應用程式的 [套件名稱] （在此範例中，套件名稱是**gcmexample**），然後按一下 **[繼續] 以選擇並設定服務**：

    [![輸入封裝名稱](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    請注意，此套件名稱也是您應用程式的應用程式識別碼。

3. [**選擇及設定服務**] 區段會列出您可以新增至應用程式的 Google 服務。 按一下 [**雲端通訊**]：

    [![選擇雲端通訊](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4. 接下來，按一下 [**啟用 GOOGLE 雲端通訊**]：

    [![啟用 Google 雲端通訊](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5. 系統會為您的應用程式產生**伺服器 API 金鑰**和**寄件者識別碼**。 記錄這些值，然後按一下 [**關閉**]：

    [![顯示的伺服器 API 金鑰和寄件者識別碼](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    保護 API 金鑰&ndash; ，而不是供公開使用。 如果 API 金鑰遭到入侵，未經授權的伺服器就可以將訊息發佈至用戶端應用程式。
    [安全地使用 api 金鑰的最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)會提供實用的指導方針來保護您的 API 金鑰。

### <a name="view-your-project-settings"></a>查看您的專案設定

您可以隨時登入[Google Cloud 主控台](https://console.cloud.google.com/)並選取您的專案，以查看您的專案設定。 例如，您可以在頁面頂端的下拉式功能表中選取您的專案，以查看**寄件者識別碼**（在此範例中，專案稱為**XamarinGCM**）。 寄件者識別碼是此螢幕擷取畫面中所示的專案編號（此處的寄件者識別碼是**9349932736**）：

[![查看寄件者識別碼](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

若要查看**api 金鑰**，請按一下 [ **api 管理員**]，然後按一下 [**認證**]：

[![查看 API 金鑰](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)

## <a name="for-further-reading"></a>進一步閱讀

- Google 的[註冊用戶端應用程式](https://developers.google.com/cloud-messaging/registration)會更詳細地說明用戶端登錄進程，並提供設定自動重試和讓註冊狀態保持同步的相關資訊。

- [Rfc 6120](https://tools.ietf.org/html/rfc6120)和[rfc 6121](https://tools.ietf.org/html/rfc6121)說明並定義可延伸的訊息和目前狀態通訊協定（XMPP）。

## <a name="summary"></a>總結

本文提供 Google 雲端通訊（GCM）的總覽。 它說明了各種認證，用來識別和授權應用程式伺服器與用戶端應用程式之間的訊息。 其中說明最常見的訊息案例，並詳述向 GCM 註冊應用程式以使用 GCM 服務的步驟。

## <a name="related-links"></a>相關連結

- [雲端通訊](https://developers.google.com/cloud-messaging/)
