---
title: Firebase 雲端訊息
description: Firebase 雲端傳訊 (FCM) 是一種服務，可促進行動裝置應用程式與伺服器應用程式之間的傳訊。 本文章提供 FCM 的運作方式的概觀並說明如何設定 Google 服務，好讓您的應用程式可以使用 FCM。
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: ef2c23d16545d03dc267054a96f8b0f8883afcf1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="firebase-cloud-messaging"></a>Firebase 雲端訊息

_Firebase 雲端傳訊 (FCM) 是一種服務，可促進行動裝置應用程式與伺服器應用程式之間的傳訊。本文章提供 FCM 的運作方式的概觀並說明如何設定 Google 服務，好讓您的應用程式可以使用 FCM。_

[![Firebase Cloud Messaging 英雄映像](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

本主題提供如何 Firebase Cloud Messaging 將訊息路由傳送 Xamarin.Android 應用程式和應用程式伺服器之間的高階概觀，並取得認證，好讓您的應用程式可以使用 FCM 服務提供的逐步程序。



## <a name="overview"></a>總覽

Firebase 雲端傳訊 (FCM) 是一種跨平台服務會處理傳送、 路由及伺服器應用程式和行動用戶端應用程式之間的訊息排入佇列。 FCM 後續至 Google Cloud Messaging (GCM)，並在 Google Play 服務上建置。

下圖所示，FCM 會做為訊息寄件者和用戶端之間的媒介。 A*用戶端應用程式*是裝置執行的 FCM 啟用應用程式。 *應用程式伺服器*（由您或您的公司所提供） 是用戶端應用程式與通訊透過 FCM FCM 啟用伺服器。 不同於 GCM，FCM 它可讓您將訊息傳送至用戶端應用程式直接透過 Firebase 主控台通知 GUI:

[![FCM 位於用戶端應用程式和應用程式伺服器之間](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

使用 FCM，應用程式伺服器可以將訊息傳送至單一裝置、 裝置、 群組或主題訂閱的裝置數目。 用戶端應用程式可以使用 FCM 訂閱 （例如，若要接收遠端通知） 的應用程式伺服器的下游訊息。 如需不同類型的 Firebase 訊息的詳細資訊，請參閱[關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)。



## <a name="firebase-cloud-messaging-in-action"></a>Firebase 雲端訊息的動作

應用程式伺服器的下游訊息傳送至用戶端應用程式中，從應用程式伺服器，當傳送訊息以*FCM 連線伺服器*提供 Google; FCM 連接伺服器，接著，轉寄訊息到正在執行的裝置用戶端應用程式。 可以透過 HTTP 傳送訊息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可延伸的傳訊和目前狀態的通訊協定）。 因為用戶端應用程式不會永遠連接或執行，FCM 連線伺服器儲和儲存訊息，將它們傳送到用戶端應用程式，因為它們重新連線，而且可用。 同樣地，FCM 將會在佇列中上游訊息從用戶端應用程式，應用程式伺服器無法使用應用程式伺服器時。 如需有關 FCM 連接伺服器的資訊，請參閱[有關 Firebase 雲端訊息伺服器](https://firebase.google.com/docs/cloud-messaging/server)。

FCM 使用下列認證來識別應用程式伺服器和用戶端應用程式，並使用這些認證來授權訊息交易 FCM 透過：

-   **寄件者識別碼** &ndash; *寄件者識別碼*是唯一的數值時建立 Firebase 專案指派。 寄件者識別碼用來識別每個應用程式伺服器，可以將訊息傳送至用戶端應用程式。 寄件者識別碼也是您專案的號碼;當您註冊您的專案時，您可以取得從 Firebase 主控台的寄件者識別碼。 舉例來說，寄件者識別碼是`496915549731`。

-   **API 金鑰** &ndash; *API 金鑰*Firebase 服務; 提供的應用程式伺服器存取FCM 使用此金鑰來驗證應用程式伺服器。 此認證也稱為*伺服器金鑰*或*Web API 金鑰*。 API 金鑰的範例是`AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`。

-   **應用程式識別碼**&ndash;用戶端應用程式 （獨立於任何指定的裝置） 註冊以接收來自 FCM 訊息的身分識別。 舉例來說，應用程式識別碼是`1:415712510732:android:0e1eb7a661af2460`。

-   **註冊語彙基元** &ndash; *註冊語彙基元*(也稱為*執行個體識別碼*) 是指定的裝置上的用戶端應用程式的 FCM 身分識別。 在執行階段產生註冊憑證&ndash;先向註冊時會 FCM 時在裝置上執行，您的應用程式會收到註冊權杖。 註冊憑證授權 （該特定裝置上執行） 的用戶端應用程式的執行個體從 FCM 接收訊息。
    註冊權杖的範例是`fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS`（很長的字串）。

[設定總 Firebase Cloud Messaging](#setup_fcm) （稍後在本指南中） 提供建立專案，並產生這些認證的詳細的指示。 當您建立新的專案中[Firebase 主控台](https://console.firebase.google.com/)，認證檔案呼叫**google services.json**建立&ndash;中所述，將這個檔案加入您的Xamarin.Android專案[遠端通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

下列章節說明與 FCM 透過應用程式伺服器通訊的用戶端應用程式時，如何使用這些認證。


<a name="registration" />

### <a name="registration-with-fcm"></a>FCM 的註冊

用戶端應用程式必須先向 FCM 之前訊息發生的情況。 用戶端應用程式必須完成註冊步驟，如下圖所示：

[![應用程式註冊步驟圖表](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  用戶端應用程式會連絡以取得註冊語彙基元，寄件者識別碼、 API 金鑰和應用程式識別碼傳遞至 FCM FCM。

2.  FCM 傳回用戶端應用程式註冊語彙基元。

3.  用戶端應用程式 （選擇性） 轉送至應用程式伺服器的註冊權杖。

應用程式伺服器會快取後續的通訊，與用戶端應用程式註冊語彙基元。 應用程式伺服器可以傳送回用戶端應用程式，以指示註冊語彙基元已收到的通知。 此信號交換發生後，用戶端應用程式可以接收訊息 （或傳送訊息至） 應用程式伺服器。 如果舊的權杖遭到入侵，用戶端應用程式可能會收到新的註冊權杖 (請參閱[遠端通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)的應用程式如何接收註冊權杖的更新範例)。

當用戶端應用程式不再想要從應用程式伺服器接收訊息時，它可以傳送要求到應用程式伺服器，以刪除註冊語彙基元。 從裝置解除安裝用戶端應用程式時，如果 FCM 偵測此項，並自動通知 要刪除的註冊憑證之應用程式伺服器。



### <a name="downstream-messaging"></a>下游訊息處理

下圖說明如何 Firebase Cloud Messaging 儲存和轉送下游訊息：

[![FCM 用於儲存與轉送下游訊息處理](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

當應用程式伺服器會將下游訊息傳送至用戶端應用程式時，它會為列舉上圖中使用下列步驟：

1.  應用程式伺服器會傳送訊息至 FCM。

2.  如果用戶端裝置無法使用，FCM 伺服器會儲存訊息以便稍後傳輸佇列中。 訊息會保留 4 週的最長 FCM 儲存體中 (如需詳細資訊，請參閱[設定訊息的使用期限](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl))。

3.  當用戶端裝置可用時，FCM 轉寄訊息到該裝置上的用戶端應用程式。

4.  用戶端應用程式從 FCM 接收訊息、 加以處理，並向使用者顯示。 例如，如果訊息是遠端的通知，就會向使用者在通知區域中。

在此傳訊案例中 （其中的應用程式伺服器會傳送訊息至單一用戶端應用程式），訊息可以是長度最多為 4 kB。

如需接收下游 FCM 訊息在 Android 上的詳細資訊，請參閱[遠端通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

### <a name="topic-messaging"></a>主題的訊息

*主題傳訊*可讓應用程式伺服器將訊息傳送到選擇加入特定主題的多個裝置。 您也可以撰寫和傳送主題訊息透過 Firebase 主控台通知 GUI。 FCM 處理路由與主題訊息傳遞至訂閱用戶端。 這項功能可以用於訊息，例如警示天氣、 股票報價及頭條新聞。

[![主題傳訊圖表](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

（在用戶端應用程式會取得稍早所述的註冊憑證） 之後的主題訊息使用下列步驟：

1.  由訂閱訊息傳送至 FCM 主題訂閱用戶端應用程式。

2.  應用程式伺服器會傳送至 FCM 發佈主題的訊息。

3.  FCM 將主題訊息轉寄給已訂閱該主題的用戶端。

如需 Firebase 主題傳訊的詳細資訊，請參閱 Google[主題訊息在 Android 上](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)。


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>設定 Firebase 雲端訊息

您可以在應用程式中使用 FCM 服務之前，您必須建立新的專案 （或匯入現有的專案） 透過[Firebase 主控台](https://console.firebase.google.com/)。 若要建立您的應用程式 Firebase Cloud Messaging 專案中使用下列步驟：

1.  登入[Firebase 主控台](https://console.firebase.google.com/)與您的 Google 帳戶 （也就是您 Gmail 的地址） 按一下**建立新專案**:

    [![建立新專案 按鈕](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    如果您有現有的專案，按一下**Google 專案匯入**。

2.  在**建立專案** 對話方塊中，輸入您的專案名稱，然後按一下**建立專案**。 在下列範例中，新的專案呼叫**XamarinFCM**建立：

    [![建立專案 對話方塊](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  在主控台中 Firebase**概觀**，按一下**加入您的 Android 應用程式的 Firebase**:

    [![Firebase 加入您的 Android 應用程式](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  在下一個畫面中，輸入您的應用程式的封裝名稱。 在此範例中，封裝名稱是**com.xamarin.fcmexample**。 此值必須符合您的 Android 應用程式的封裝名稱。 也可以在 輸入應用程式暱稱**應用程式的暱稱**欄位：

    [![為應用程式的暱稱輸入 FCM 範例](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  如果您的應用程式使用動態連結，邀請或 Google 驗證，您也必須輸入您的偵錯簽署憑證。 如需有關如何尋找您的簽署憑證的詳細資訊，請參閱[尋找金鑰存放區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)。
    在此範例中，簽章的憑證會保留空白。

6.  按一下**新增應用程式**:

    [![按一下 新增應用程式按鈕](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    為應用程式自動產生伺服器 API 金鑰與用戶端識別碼。 這項資訊會封裝在**google services.json**檔案，當您按一下 自動下載**新增應用程式**。
    請務必將此檔案儲存在安全的地方。

如需詳細的範例將加入的**google services.json**來接收 FCM 推播通知訊息，在 Android 上的應用程式專案，請參閱[遠端通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。



## <a name="for-further-reading"></a>進一步閱讀

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)提供 Firebase 雲端訊息的重要功能的概觀、 它的運作方式，以及安裝指示的說明。

-   Google[建置應用程式伺服器傳送要求](https://firebase.google.com/docs/cloud-messaging/send-message)說明如何將您的應用程式伺服器的訊息傳送。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)和[RFC 6121](https://tools.ietf.org/html/rfc6121)說明，並定義可延伸的傳訊和目前狀態通訊協定 (XMPP)。



## <a name="summary"></a>總結

本文提供的 Firebase 雲端傳訊 (FCM) 的概觀。 它說明可用來識別及授權應用程式伺服器和用戶端應用程式之間的傳訊的各種認證。 它所述的註冊和下游的傳訊案例，以及它詳細與 FCM 使用 FCM 服務註冊您的應用程式的步驟。


## <a name="related-links"></a>相關連結

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
