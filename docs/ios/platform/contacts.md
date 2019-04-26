---
title: 連絡人和 ContactsUI 在 Xamarin.iOS 中
description: 本文章涵蓋使用新的連絡人和連絡人 UI 中的 Xamarin.iOS 應用程式的架構。 這些架構會取代現有的通訊錄和先前版本的 iOS 中使用的位址書籍 UI。
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: e3f1533605d08df58d8d257714dd8135690c0e5d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388917"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>連絡人和 ContactsUI 在 Xamarin.iOS 中

_本文章涵蓋使用新的連絡人和連絡人 UI 中的 Xamarin.iOS 應用程式的架構。這些架構會取代現有的通訊錄和先前版本的 iOS 中使用的位址書籍 UI。_

使用 iOS 9 的引進，Apple 已發行兩個新的架構`Contacts`和`ContactsUI`、 該取代現有的通訊錄及 iOS 8 及更早版本所使用的地址通訊錄 UI 架構。

這兩個新的架構包含下列功能：

- [**連絡人**](#contacts) -提供使用者的連絡人清單資料的存取權。
    因為大部分的應用程式只需要唯讀存取權，這個架構已經過最佳化的執行緒安全的唯讀存取。

- [**ContactsUI** ](#contactsui) -提供 Xamarin.iOS UI 項目，以顯示、 編輯、 選取和 iOS 裝置上建立的連絡人。

[![](contacts-images/add01.png "IOS 裝置上縮圖工作表範例")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> 現有`AddressBook`並`AddressBookUI`架構供 iOS 8 （與舊版） iOS 9 中已被取代，並應該取代成新`Contacts`和`ContactsUI`儘速任何現有的 Xamarin.iOS 應用程式的架構。 針對新的架構，應撰寫新的應用程式。




在下列章節中，我們將探討這些新的架構，以及如何在 Xamarin.iOS 應用程式中實作它們。

<a name="contacts" />

## <a name="the-contacts-framework"></a>連絡人 Framework

連絡人 Framework 提供 Xamarin.iOS 存取使用者的連絡資訊。 因為大部分的應用程式只需要唯讀存取權，這個架構已經過最佳化的執行緒安全的唯讀存取。

<a name="Contact_Objects" />

### <a name="contact-objects"></a>連絡人物件

`CNContact`類別提供執行緒安全的唯讀存取連絡人的屬性，例如名稱、 地址或電話號碼。 `CNContact` 之類的函式`NSDictionary`且包含多個唯讀集合的屬性 （例如地址或電話號碼）：

[![](contacts-images/contactobjects.png "連絡人物件概觀")](contacts-images/contactobjects.png#lightbox)

可以有多個值 （例如電子郵件地址或電話號碼） 的任何屬性，它們會表示為陣列`NSLabeledValue`物件。 `NSLabeledValue` 是唯讀的集合的標籤所組成的執行緒安全的 tuple，以及值標籤用來定義使用者 （例如家用或工作電子郵件） 的值。 連絡人 framework 提供的預先定義的標籤 (透過`CNLabelKey`和`CNLabelPhoneNumberKey`靜態類別)，您可以使用您的應用程式中，或您可以選擇定義自訂標籤，針對您的需求。

要調整現有的連絡人 （或建立新的） 任何 Xamarin.iOS 應用程式中，使用`NSMutableContact`版本的類別和它的子類別 (例如`CNMutablePostalAddress`)。

例如，下列程式碼會建立新的連絡人，並將它新增至連絡人的使用者的集合：

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

如果 iOS 9 裝置上執行此程式碼時，新的連絡人會新增至使用者的集合中。 例如：

[![](contacts-images/add01.png "新的連絡人新增至使用者的集合")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>連絡人的格式化和當地語系化

連絡人 framework 包含數個物件和方法，可協助您格式化和當地語系化內容顯示給使用者。 例如，下列程式碼會正確格式化的連絡人名稱和郵寄地址的顯示：

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

您會在您的應用程式 UI 中顯示的屬性標籤，連絡架構會有當地語系化這些字串也的方法。 同樣地，這根據目前的地區設定的 iOS 裝置執行應用程式。 例如：

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>正在擷取現有的連絡人

使用的執行個體`CNContactStore`類別，您可以從使用者的連絡人資料庫擷取連絡資訊。 `CNContactStore`包含所有提取或更新連絡人和群組從資料庫所需的方法。 因為這些方法是同步的建議您執行這些在背景執行緒，以防止封鎖 UI。

使用述詞 (從建置`CNContact`類別)，您可以篩選從資料庫擷取的連絡人時傳回的結果。 若要擷取包含字串的連絡人`Appleseed`，使用下列程式碼：

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> 連絡人 framework 不支援泛型和複合的述詞。

例如，若要限制為僅限提取**GivenName**並**FamilyName**屬性的連絡人，使用下列程式碼：

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最後，若要搜尋資料庫，並傳回結果，請使用下列程式碼：

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果此程式碼執行之後所建立的範例**連絡人物件**上一節，它會傳回我們剛剛建立的"John Appleseed"連絡人。

### <a name="contact-access-privacy"></a>連絡人存取隱私權

因為使用者可以授與或拒絕存取他們的連絡資訊，每個應用程式為基礎，第一次您呼叫以`CNContactStore`，會看到一個對話方塊，要求他們允許您的應用程式的存取權。

權限要求只會一次看到，第一次應用程式會執行，及後續執行，或呼叫`CNContactStore`會使用使用者選取的權限。

您應該設計您的應用程式，讓它依正常程序會處理拒絕存取其連絡人資料庫的使用者。

#### <a name="fetching-partial-contacts"></a>正在擷取部分的連絡人

A_部分請連絡_是只有部分可用的屬性已經提取的連絡人存放區中的連絡人。 如果您嘗試存取尚未先前擷取的屬性，它會導致例外狀況。

您可以輕鬆地檢查指定的連絡人是否具有所需的屬性，使用其中一種`IsKeyAvailable`或是`AreKeysAvailable`方法`CNContact`執行個體。 例如: 

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> `GetUnifiedContact`並`GetUnifiedContacts`方法`CNContactStore`類別_只有_傳回部分連絡限於要求從提供的擷取索引鍵的屬性。

### <a name="unified-contacts"></a>統一的連絡人

使用者可能會有不同 （例如 iCloud、 Facebook 或 Google Mail) 其連絡資料庫中的單一人員連絡資訊的來源。 在 iOS 和 OS X 應用程式，此連絡人的資訊會自動會連結在一起，並顯示給使用者的單一_Unified 連絡_:

[![](contacts-images/unified01.png "統一的連絡人概觀")](contacts-images/unified01.png#lightbox)

此統一的連絡人 」 是暫時、 以記憶體中的檢視會提供自己的唯一識別碼 （這應該用來重新擷取連絡人，如有必要） 的連結連絡資訊。 根據預設，連絡人架構將傳回盡可能統一的連絡人。

### <a name="creating-and-updating-contacts"></a>建立和更新連絡人

如我們在中所見[連絡物件](#Contact_Objects)您使用上一節，`CNContactStore`和執行個體`CNMutableContact`若要建立新的連絡人，然後寫入至使用者的連絡資料庫使用`CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

A`CNSaveRequest`也可用來快取多個連絡人和群組變更成單一作業和批次的修改`CNContactStore`。

若要更新不可變的連絡人，取自 fetch 作業，您必須先要求的可變動的副本，然後修改並儲存回連絡人儲存區。 例如: 

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>連絡人的變更通知

每次修改連絡人時，請連絡存放區文章`CNContactStoreDidChangeNotification`預設通知中心。 如果您已快取，或目前所顯示的任何連絡人，您必須重新整理這些物件從 Contact 存放區 (`CNContactStore`)。

### <a name="containers-and-groups"></a>容器和群組

使用者的連絡人可以存在於在本機使用者的裝置上，或為連絡人同步到裝置 （例如 Facebook 或 Google） 的一或多個伺服器帳戶。 連絡人的每個集區都有它自己_容器_和一個容器中只能存在一個指定的連絡人。

[![](contacts-images/containers01.png "容器和群組概觀")](contacts-images/containers01.png#lightbox)

某些容器允許排列成一或多個連絡人_群組_或是_子群組_。 此行為是依據指定的容器的備份存放區。 比方說，iCloud 有只有一個容器，但可以有多個群組 （但沒有任何子群組）。 相反地，Microsoft Exchange 不支援群組，但可以有多個容器 （一個用於每個 Exchange 資料夾）。

[![](contacts-images/containers02.png "在容器和群組重疊")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>ContactsUI Framework

您的應用程式不提供自訂 UI，需要的情況下，您可以使用 ContactsUI 架構來呈現 UI 項目，來顯示、 編輯、 選取和您的 Xamarin.iOS 應用程式中建立的連絡人。

使用 Apple 的內建控制項，您不僅減少您必須建立要在您的 Xamarin.iOS 應用程式，支援連絡人的程式碼數量，但您以一致的介面呈現給應用程式的使用者。

### <a name="the-contact-picker-view-controller"></a>連絡人選擇器檢視控制器

連絡人選擇器檢視控制器 (`CNContactPickerViewController`) 管理標準的連絡人選擇器檢視，可讓使用者選取 從使用者的連絡人資料庫的 連絡人 或 連絡人屬性。 使用者可以選取一或多個連絡人 （根據其使用方式），並連絡選擇器檢視控制器不會再顯示選擇器的權限提示。

在呼叫之前`CNContactPickerViewController`類別，定義哪些使用者可以選取，並定義述詞來控制顯示和選取的 連絡人屬性的屬性。

使用繼承自類別的執行個體`CNContactPickerDelegate`回應選擇器的使用者的互動。 例如：

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

若要允許使用者選取從其資料庫中的連絡人的電子郵件地址，您可以使用下列程式碼：

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>連絡人的檢視控制器

請連絡檢視控制器 (`CNContactViewController`) 類別會提供向使用者顯示標準的 [連絡人] 檢視的控制器。 [連絡人] 檢視可以顯示新的新增功能、 未知的或現有連絡人並檢視顯示藉由呼叫正確的靜態建構函式之前，必須指定類型 (`FromNewContact`， `FromUnknownContact`， `FromContact`)。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>總結

本文所深入了解使用 Xamarin.iOS 應用程式中的連絡人和連絡人 UI 架構。 首先，它涵蓋了不同類型的連絡人 framework 提供的物件，以及您如何使用它們來建立新的或存取現有的連絡人。 它也會檢查來選取現有的連絡人，並顯示連絡人資訊連絡的 UI 架構。


## <a name="related-links"></a>相關連結

- [QuickContacts 範例](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [新 iOS 9 功能](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [連絡人 Framework 參考](https://developer.apple.com/documentation/contacts?language=objc)
- [ContactsUI Framework 參考](https://developer.apple.com/documentation/contactsui?language=objc)
