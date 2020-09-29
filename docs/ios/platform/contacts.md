---
title: Xamarin 中的連絡人和 ContactsUI
description: 本文說明如何在 Xamarin iOS 應用程式中使用新的「連絡人」和「連絡人」 UI 架構。 這些架構會取代舊版 iOS 中使用的現有通訊錄和通訊錄 UI。
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 6fe6c254daf23f5f3d2fb267f6ba4986b94bcbd7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431747"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Xamarin 中的連絡人和 ContactsUI

_本文說明如何在 Xamarin iOS 應用程式中使用新的「連絡人」和「連絡人」 UI 架構。這些架構會取代舊版 iOS 中使用的現有通訊錄和通訊錄 UI。_

隨著 iOS 9 的推出，Apple 已發行兩個新的架構， `Contacts` 並 `ContactsUI` 取代了 ios 8 和更早版本所使用的現有通訊錄和通訊錄 UI 架構。

這兩個新的架構包含下列功能：

- [**連絡人**](#contacts) -提供使用者連絡人清單資料的存取權。
  因為大部分的應用程式只需要唯讀存取，所以此架構已針對安全線程、唯讀存取進行優化。

- [**ContactsUI**](#contactsui) -提供可在 ios 裝置上顯示、編輯、選取及建立連絡人的 XAMARIN ios UI 元素。

[![IOS 裝置上的範例 Contact Sheet](contacts-images/add01.png)](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> `AddressBook` `AddressBookUI` Ios 8 (和先前的) 所使用的現有和架構已在 ios 9 中淘汰，而且應該儘快以新的和架構取代 `Contacts` `ContactsUI` 為任何現有的 Xamarin ios 應用程式。 新應用程式應針對新的架構撰寫。

在下列各節中，我們將探討這些新的架構，以及如何在 Xamarin iOS 應用程式中加以執行。

<a name="contacts"></a>

## <a name="the-contacts-framework"></a>連絡人架構

Contact Framework 可讓 Xamarin 存取使用者的連絡人資訊。 因為大部分的應用程式只需要唯讀存取，所以此架構已針對安全線程、唯讀存取進行優化。

<a name="Contact_Objects"></a>

### <a name="contact-objects"></a>Contact 物件

`CNContact`類別可提供安全線程、唯讀存取連絡人的屬性，例如姓名、位址或電話號碼。 `CNContact` 的函式（例如 `NSDictionary` ）包含多個唯讀的屬性集合， (例如位址或電話號碼) ：

[![Contact 物件總覽](contacts-images/contactobjects.png)](contacts-images/contactobjects.png#lightbox)

對於任何可以有多個值 (例如電子郵件地址或電話號碼) 的屬性，這些值會以物件的陣列表示 `NSLabeledValue` 。 `NSLabeledValue` 是由標籤和值的唯讀集組成的安全線程元組，其中標籤會定義使用者的值 (例如首頁或公司電子郵件) 。 「連絡人」架構會透過和靜態類別 (來提供預先定義的標籤 `CNLabelKey` `CNLabelPhoneNumberKey` ，您可以在應用程式中使用這些) ，也可以選擇根據您的需求定義自訂標籤。

對於需要調整現有連絡人的值 (或建立新的) 的任何 Xamarin iOS 應用程式，請使用 `NSMutableContact` 類別的版本及其子類別 (例如 `CNMutablePostalAddress`) 。

例如，下列程式碼會建立新的連絡人，並將其新增至使用者的連絡人集合：

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

如果此程式碼是在 iOS 9 裝置上執行，則會在使用者的集合中加入新的連絡人。 例如：

[![新增至使用者集合的連絡人](contacts-images/add01.png)](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>連絡人的格式和當地語系化

「連絡人」架構包含數個物件和方法，可協助您格式化及當地語系化要顯示給使用者的內容。 例如，下列程式碼會正確地格式化連絡人名稱和郵寄地址，以便顯示：

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

針對您要在應用程式 UI 中顯示的屬性標籤，Contact framework 也有方法可將這些字串當地語系化。 同樣地，這是以執行應用程式的 iOS 裝置目前的地區設定為基礎。 例如：

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>提取現有的連絡人

藉由使用類別的實例 `CNContactStore` ，您可以從使用者的 contact 資料庫提取連絡人資訊。 `CNContactStore`包含從資料庫中提取或更新連絡人和群組所需的所有方法。 因為這些方法是同步的，建議您在背景執行緒上執行這些方法，以防止 UI 遭到封鎖。

藉由使用 () 所建立的述詞 `CNContact` ，您可以篩選從資料庫提取連絡人時所傳回的結果。 若只要提取包含字串的連絡人 `Appleseed` ，請使用下列程式碼：

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Contacts 架構不支援泛型和複合述詞。

例如，若要將 fetch 限制為僅限連絡人的 **GivenName** 和 **FamilyName** 屬性，請使用下列程式碼：

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最後，若要搜尋資料庫並傳回結果，請使用下列程式碼：

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果這段程式碼是在上述「 **連絡人物件** 」一節中建立的範例之後執行，則會傳回我們剛才建立的「John Appleseed」連絡人。

### <a name="contact-access-privacy"></a>連絡人存取隱私權

由於使用者可以針對每個應用程式，授與或拒絕其連絡人資訊的存取權，所以當您第一次呼叫時 `CNContactStore` ，會顯示一個對話方塊，要求他們允許您的應用程式存取。

只有在應用程式第一次執行時，才會顯示許可權要求一次，後續執行或的呼叫 `CNContactStore` 將會使用使用者在該時間選取的許可權。

您應該設計您的應用程式，讓它能夠正常地處理拒絕存取其 contact 資料庫的使用者。

#### <a name="fetching-partial-contacts"></a>提取部分連絡人

_部分連絡人_是指只能從的連絡人存放區提取部分可用屬性的連絡人。 如果您嘗試存取先前未提取的屬性，將會導致例外狀況。

您可以使用實例的或方法，輕鬆地查看指定的連絡人是否具有所需的屬性 `IsKeyAvailable` `AreKeysAvailable` `CNContact` 。 例如：

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
> `GetUnifiedContact`類別的和 `GetUnifiedContacts` 方法只會傳回 `CNContactStore` 部分連絡人，限制為從提供的提取金鑰要求的屬性。 _only_

### <a name="unified-contacts"></a>整合連絡人

使用者可能會在其連絡人資料庫中有不同的連絡人資訊來源 (例如 iCloud、Facebook 或 Google Mail) 。 在 iOS 和 OS X 應用程式中，此連絡人資訊會自動連結在一起，並以單一 _整合連絡人_的形式顯示給使用者：

[![整合連絡人總覽](contacts-images/unified01.png)](contacts-images/unified01.png#lightbox)

此整合連絡人是連結連絡人資訊的暫時記憶體內部視圖，它會提供自己唯一的識別碼 (如果) 需要，您應該使用此資訊來重新擷取連絡人。 根據預設，Contact framework 會盡可能傳回統一的連絡人。

### <a name="creating-and-updating-contacts"></a>建立和更新連絡人

如我們在上面的「 [連絡人物件](#Contact_Objects) 」一節中所見，您可以使用 `CNContactStore` 和的實例 `CNMutableContact` 來建立新的連絡人，然後使用下列方式，將這些連絡人寫入至使用者的 Contact 資料庫 `CNSaveRequest` ：

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

`CNSaveRequest`也可以用來將多個連絡人和群組變更快取到單一作業，並將這些修改批次至 `CNContactStore` 。

若要更新從提取作業取得的不可變動連絡人，您必須先要求可變動的複本，然後再修改並儲存回連絡人存放區。 例如：

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

### <a name="contact-change-notifications"></a>連絡人變更通知

每次修改連絡人時，連絡人商店都會張貼 `CNContactStoreDidChangeNotification` 至預設的通知中心。 如果您已快取或目前正在顯示任何連絡人，您將需要從連絡人存放區 () 重新整理這些物件 `CNContactStore` 。

### <a name="containers-and-groups"></a>容器和群組

使用者的連絡人可以存在於使用者裝置的本機上，或是從一或多個伺服器帳戶（ (例如 Facebook 或 Google) ）同步至裝置的連絡人。 連絡人的每個集區都有自己的 _容器_ ，而指定的連絡人只能存在於一個容器中。

[![容器和群組總覽](contacts-images/containers01.png)](contacts-images/containers01.png#lightbox)

某些容器允許將連絡人排列成一或多個 _群組_ 或 _子群組_。 此行為取決於指定容器的備份存放區。 例如，iCloud 只有一個容器，但可以有許多群組 (但沒有子群組) 。 另一方面，Microsoft Exchange 並不支援群組，但可以有多個容器 () 的每個 Exchange 資料夾各一個。

[![容器和群組內的重迭](contacts-images/containers02.png)](contacts-images/containers02.png#lightbox)

<a name="contactsui"></a>

## <a name="the-contactsui-framework"></a>ContactsUI 架構

在您的應用程式不需要顯示自訂 UI 的情況下，您可以使用 ContactsUI 架構來呈現 UI 元素，以顯示、編輯、選取及建立您的 Xamarin iOS 應用程式中的連絡人。

使用 Apple 的內建控制項，不僅可減少您必須建立的程式碼數量，以支援您的 Xamarin iOS 應用程式中的連絡人，還能為應用程式的使用者提供一致的介面。

### <a name="the-contact-picker-view-controller"></a>連絡人選擇器視圖控制器

連絡人選擇器視圖控制器 (`CNContactPickerViewController`) 管理標準連絡人選擇器視圖，可讓使用者從使用者的連絡人資料庫中選取連絡人或連絡人屬性。 使用者可以根據其使用方式來選取一或多個連絡人 () ，而 [連絡人選擇器] View 控制器在顯示選擇器之前，不會提示您提供許可權。

呼叫 `CNContactPickerViewController` 類別之前，您可以定義使用者可以選取的屬性，並定義述詞來控制連絡人屬性的顯示和選取。

使用繼承自之類別的實例， `CNContactPickerDelegate` 以回應使用者與選擇器的互動。 例如：

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

若要允許使用者從其資料庫中的連絡人選取電子郵件地址，您可以使用下列程式碼：

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

### <a name="the-contact-view-controller"></a>連絡人視圖控制器

Contact View Controller (`CNContactViewController`) 類別會提供一個控制器，向終端使用者呈現標準的連絡人 View。 連絡人 view 可以顯示新的新的、未知或現有的連絡人，而且必須先指定類型，才能藉由呼叫正確的靜態函式 (`FromNewContact` 、) 來顯示 `FromUnknownContact` `FromContact` 。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>摘要

本文已詳細說明如何使用 Xamarin iOS 應用程式中的 Contact 和 Contact UI 架構。 首先，它涵蓋了 Contact framework 提供的不同物件類型，以及您如何使用它們來建立新的或存取現有的連絡人。 它也會檢查 Contact UI 架構，以選取現有的連絡人並顯示連絡人資訊。

## <a name="related-links"></a>相關連結

- [連絡人範例](/samples/xamarin/ios-samples/contacts/)
- [IOS 9 的新功能](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [連絡人架構參考](https://developer.apple.com/documentation/contacts?language=objc)
- [ContactsUI 架構參考](https://developer.apple.com/documentation/contactsui?language=objc)