---
title: Firebase 雲端傳訊
description: Firebase 雲端通訊 (FCM) 是一項服務，可協助行動裝置應用程式和伺服器應用程式之間通訊。 這篇文章概述 FCM 的運作方式，並說明如何設定 Google 服務，讓您的應用程式可以使用 FCM。
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/31/2018
ms.openlocfilehash: 92c402085627bbe67d4cd8ccaf60a68aa979f3e7
ms.sourcegitcommit: bf05041cc74fb05fd906746b8ca4d1403fc5cc7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2018
ms.locfileid: "39514291"
---
# <a name="firebase-cloud-messaging"></a>Firebase 雲端傳訊

_Firebase 雲端通訊 (FCM) 是一項服務，可協助行動裝置應用程式和伺服器應用程式之間通訊。這篇文章概述 FCM 的運作方式，並說明如何設定 Google 服務，讓您的應用程式可以使用 FCM。_

[![Firebase 雲端通訊的英雄影像](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

本主題提供如何 Firebase 雲端通訊路由傳送您的 Xamarin.Android 應用程式和應用程式伺服器之間，訊息的高階概觀，並取得認證，好讓您的應用程式可以使用 FCM 服務提供的逐步程序。

## <a name="overview"></a>總覽

Firebase 雲端通訊 (FCM) 是跨平台服務，可處理傳送、 路由和伺服器應用程式和行動用戶端應用程式之間的訊息排入佇列。 FCM 後置項到 Google 雲端通訊 (GCM)，而此系統建置在 Google Play 服務。

下圖所示，FCM 會作為訊息的寄件者與用戶端之間的媒介。 A*用戶端應用程式*是裝置執行的 FCM 啟用應用程式。 *應用程式伺服器*（由您或貴公司提供） 是您的用戶端應用程式與通訊透過 FCM 的 FCM 啟用伺服器。 不同於 GCM，FCM 可讓您將訊息傳送至用戶端應用程式可直接透過 Firebase 主控台通知 GUI:

[![FCM 位於用戶端應用程式和應用程式伺服器之間](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

使用 FCM，應用程式伺服器可以傳送訊息至單一裝置、 一組裝置，或主題訂閱的裝置數目。 用戶端應用程式可以使用 FCM 訂閱 （例如，若要接收遠端通知） 的應用程式伺服器的下游訊息。 如需不同類型的 Firebase 訊息的詳細資訊，請參閱[關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)。

## <a name="fcm-in-action"></a>作用中的 firebase 雲端通訊

當下游的訊息傳送至用戶端應用程式從應用程式伺服器時，應用程式伺服器會將傳送的訊息*FCM 連接伺服器*由 Google; 提供 FCM 連線伺服器上，依次轉寄訊息到正在執行的裝置用戶端應用程式。 可以透過 HTTP 傳送訊息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可延伸傳訊和目前狀態通訊協定）。 因為用戶端應用程式不會一直連線或執行，FCM 連接伺服器將加入佇列並將訊息，將它們傳送到用戶端應用程式，因為它們重新連線，而且可用。 同樣地，FCM 將加入佇列應用程式伺服器到用戶端應用程式的上游來自應用程式伺服器無法使用時。 如需關於 FCM 連接伺服器的詳細資訊，請參閱[關於 Firebase 雲端傳訊伺服器](https://firebase.google.com/docs/cloud-messaging/server)。

FCM 會以下列認證來識別應用程式伺服器和用戶端應用程式，並使用這些認證來授權透過 FCM 訊息交易：

-   <a name="fcm-in-action-sender-id"></a>**寄件者識別碼** &ndash; *寄件者識別碼*是唯一的數值時建立您的 Firebase 專案指派。 寄件者識別碼用來識別每個應用程式伺服器，可以將訊息傳送至用戶端應用程式。 寄件者識別碼也是您的專案號碼;當您註冊您的專案時，您可以取得從 Firebase 主控台中的寄件者識別碼。 寄件者識別碼的範例是`496915549731`。

-   <a name="fcm-in-action-api-key"></a>**API 金鑰** &ndash; *API 金鑰*Firebase 服務; 會提供應用程式伺服器存取FCM 會使用此金鑰來驗證應用程式伺服器。 這個認證也稱為*伺服器金鑰*或*Web API 金鑰*。 API 金鑰的範例是`AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`。

-   <a name="fcm-in-action-app-id"></a>**應用程式識別碼**&ndash;用戶端應用程式 （而不是任何指定的裝置） 註冊以接收來自 FCM 訊息的身分識別。 舉例來說，應用程式識別碼是`1:415712510732:android:0e1eb7a661af2460`。

-   <a name="fcm-in-action-registration-token"></a>**註冊權杖** &ndash; *註冊語彙基元*(也稱為*執行個體識別碼*) 是您的用戶端應用程式，在指定的裝置上的 FCM 身分識別。 註冊權杖會在執行階段產生&ndash;時它會先註冊了 fcm 後在裝置上執行時，您的應用程式會收到註冊權杖。 註冊權杖授權 （在該特定裝置上執行） 的用戶端應用程式的執行個體，以接收來自 FCM 訊息。
    註冊權杖的範例是`fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS`（很長的字串）。

[設定註冊 Firebase 雲端通訊](#setup_fcm)（稍後在本指南中） 提供建立專案，並產生這些認證的詳細的指示。 當您建立新的專案中[Firebase 主控台](https://console.firebase.google.com/)的認證檔案稱為**google-services.json**建立&ndash;中所述，此檔案新增至Xamarin.Android專案[遠端通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

下列各節將說明當用戶端應用程式與透過 FCM 的應用程式伺服器通訊時，如何使用這些認證。


<a name="registration" />

### <a name="registration-with-fcm"></a>向 FCM 註冊

用戶端應用程式必須先向 FCM 再傳訊進行。 用戶端應用程式必須完成註冊步驟，如下圖所示：

[![應用程式註冊步驟的圖表](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  用戶端應用程式會連絡來取得註冊權杖，將寄件者識別碼、 API 金鑰和應用程式識別碼傳遞給 FCM FCM。

2.  FCM 註冊權杖傳回給用戶端應用程式。

3.  用戶端應用程式 （選擇性） 會將轉送至應用程式伺服器的註冊權杖。

應用程式伺服器會快取註冊權杖，供後續使用用戶端應用程式的通訊。 應用程式伺服器可以傳送通知給用戶端應用程式，以表示已收到註冊權杖。 此交握會發生之後，用戶端應用程式可以接收訊息 （或將訊息傳送至） 應用程式伺服器。 如果遭到入侵的舊的語彙基元，用戶端應用程式可能會收到新的註冊權杖 (請參閱[遠端使用 FCM 的通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)範例中的應用程式如何收到註冊權杖的更新)。

當用戶端應用程式不再想要從應用程式伺服器接收訊息時，它可以傳送要求到應用程式伺服器，以刪除註冊權杖。 從裝置解除安裝用戶端應用程式時，如果 FCM 會偵測到這，並自動通知應用程式伺服器，若要刪除的註冊權杖。



### <a name="downstream-messaging"></a>下游訊息處理

下圖說明如何 Firebase 雲端通訊將儲存和轉送下游的訊息：

[![FCM 用於儲存和轉寄下游訊息處理](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

當應用程式伺服器會將下游的訊息傳送至用戶端應用程式時，它會為列舉在上圖中使用下列步驟：

1.  應用程式伺服器會傳送至 FCM 訊息。

2.  如果用戶端裝置無法使用，FCM 伺服器會儲存供稍後傳輸佇列中的訊息。 訊息會保留 4 週最多的 FCM 儲存體中 (如需詳細資訊，請參閱 <<c0> [ 設定訊息的存留時間](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl))。

3.  當用戶端裝置可用時，FCM 會轉寄訊息到該裝置上的用戶端應用程式。

4.  用戶端應用程式從 FCM 收到訊息、 加以處理，並向使用者顯示。 比方說，如果訊息是遠端的通知，它會顯示在通知區域中的使用者。

在此傳訊案例中 （其中應用程式伺服器的訊息傳送至單一用戶端應用程式），訊息可以是長度為 4 kB。

如需接收在 Android 上的下游 FCM 訊息的詳細資訊，請參閱[遠端使用 FCM 的通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

### <a name="topic-messaging"></a>主題傳訊

*主題傳訊*能夠將訊息傳送至已選擇加入特定主題的多個裝置的應用程式伺服器。 您也可以撰寫和傳送主題訊息透過 Firebase 主控台通知 GUI。 FCM 會處理路由和主題訊息傳遞至訂閱用戶端。 這項功能可以用於訊息，例如天氣警示、 股票報價和頭條新聞。

[![主題傳訊的圖表](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

（在用戶端應用程式取得註冊權杖，如先前所述） 之後，下列步驟會使用主題傳訊中：

1.  將訂閱訊息傳送至 FCM 主題訂閱用戶端應用程式。

2.  應用程式伺服器會傳送主題訊息給 FCM 散發。

3.  FCM 會將主題訊息轉送到已訂閱該主題的用戶端。

如需 Firebase 主題通訊的詳細資訊，請參閱 Google[在 Android 上的主題傳訊](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)。


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Firebase Cloud Messaging 設定

您可以使用您的應用程式中的 FCM 服務之前，您必須建立新的專案 （或匯入現有的專案） 透過[Firebase 主控台](https://console.firebase.google.com/)。 若要建立您的應用程式的 Firebase 雲端通訊專案中使用下列步驟：

1.  登入[Firebase 主控台](https://console.firebase.google.com/)與您的 Google 帳戶 （亦即您 Gmail 的地址），然後按一下 **建立新專案**:

    [![建立新專案 按鈕](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    如果您有現有的專案，按一下**匯入 Google 專案**。

2.  在 [**建立專案**] 對話方塊中，輸入您的專案名稱，然後按一下**建立專案**。 在下列範例中，呼叫新的專案**XamarinFCM**建立：

    [![建立專案 對話方塊](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  在 Firebase 主控台**概觀**，按一下**將 Firebase 新增至 Android 應用程式**:

    [![將 Firebase 新增至 Android 應用程式](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  在下一個畫面中，輸入您的應用程式的封裝名稱。 在此範例中，封裝名稱是**com.xamarin.fcmexample**。 此值必須符合您的 Android 應用程式的封裝名稱。 也可以在中輸入應用程式的暱稱**應用程式的暱稱**欄位：

    [![為應用程式的暱稱輸入 FCM 範例](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  如果您的應用程式使用動態連結，邀請或 Google 驗證，您也必須輸入您的偵錯簽署憑證。 如需有關如何尋找您的簽署憑證的詳細資訊，請參閱 <<c0> [ 尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)。
    在此範例中，簽章的憑證會保留空白。

6.  按一下 **新增應用程式**:

    [![按一下 [新增應用程式] 按鈕](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    伺服器 API 金鑰與用戶端識別碼會自動產生應用程式。 這項資訊封裝在**google-services.json**當您按一下時自動下載的檔案**新增應用程式**。
    請務必將此檔案儲存在安全的地方。

如需詳細的範例，將加入的**google-services.json**接收 FCM 推播通知訊息，在 Android 上的應用程式專案，請參閱[遠端通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。



## <a name="for-further-reading"></a>如需進一步閱讀

-   Google [Firebase 雲端通訊](https://firebase.google.com/docs/cloud-messaging/)提供 Firebase 雲端通訊的重要功能的概觀、 說明其運作方式，和安裝指示。

-   Google[建置應用程式伺服器傳送要求](https://firebase.google.com/docs/cloud-messaging/send-message)說明如何使用您的應用程式伺服器傳送訊息。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)並[RFC 6121](https://tools.ietf.org/html/rfc6121)說明，並定義可延伸傳訊和目前狀態通訊協定 (protocol，XMPP)。

-   [關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)說明不同類型的使用 Firebase 雲端通訊可以傳送的訊息。

## <a name="summary"></a>總結

這篇文章提供概觀的 Firebase 雲端通訊 (FCM)。 它說明用來識別及授權應用程式伺服器和用戶端應用程式之間傳訊的各種認證。 它說明註冊和下游的傳訊案例，以及它詳細使用 fcm 後，以使用 FCM 服務註冊您的應用程式的步驟。


## <a name="related-links"></a>相關連結

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
