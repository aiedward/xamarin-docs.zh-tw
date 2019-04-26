---
title: Firebase 工作發送器
description: 本指南會討論如何使用 Google 的 Firebase 作業發送器程式庫的背景工作的排程。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 91bafbbdaee805ad128766bf0a770cb711597a85
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023346"
---
# <a name="firebase-job-dispatcher"></a>Firebase 工作發送器

_本指南會討論如何使用 Google 的 Firebase 作業發送器程式庫的背景工作的排程。_


## <a name="overview"></a>總覽

將 Android 應用程式回應使用者的最佳方式之一是確保複雜或長時間執行的工作在背景中執行。 不過，很重要，背景工作不會造成負面影響的使用者經驗與裝置。 

比方說，背景工作可能分鐘輪詢一次網站每隔三或四個查詢的特定資料集的變更。 不過，它會造成災難性影響電池壽命，這似乎良性的。 應用程式重複會喚醒裝置、 提高至更高的電源狀態的 CPU、 收音機開啟電源，請網路要求，然後處理結果。 因為裝置不會立即會在關閉電源，並返回 低電源閒置狀態時，它會更糟。 效能不佳的排程背景工作可能會不小心保護裝置的不必要和多餘電力需求的狀態中。 這個看似無害的活動 （輪詢網站） 會使裝置在相對較短的一段時間無法使用。

Android 提供下列 Api 可協助在背景中執行工作，但本身不足夠的智慧型作業排程。 

* **[Intent 服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意圖服務適合用於執行工作，但它們提供無法排定工作。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;這些 Api 只允許排程，但提供沒辦法實際執行工作的工作。 此外，AlarmManager 只允許以時間為基礎的條件約束，這表示在特定時間，或經過一段時間之後，發出警示。 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule 是絕佳的 API，搭配作業系統來排程工作。 不過，它才可用的 Android 的應用程式的目標 API 層級 21 或更新版本。 
* **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 應用程式可以設定來執行工作以回應系統事件或意圖的廣播的接收器。 不過，廣播的接收器不提供任何控制何時應執行作業。 Android 作業系統的變更也會限制當廣播的接收器是可行的或它們可以回應的工作種類。 

有兩個重要的功能，有效率地執行背景工作 (有時稱為_背景工作_或是_作業_):

1. **聰明的工作排程**&ndash;很重要，當應用程式負責在背景中的工作，它會以良好的公民。 在理想情況下，應用程式不應要求執行作業。 相反地，應用程式應指定作業可以執行，以及然後排程該條件符合時要執行的工作必須符合的條件。 這可讓 Android 以聰明的方式執行工作。 比方說，網路要求，可能會批次處理以發揮最大的負擔與網路有關的所有項目執行。
2. **封裝工作**&ndash;執行背景工作的程式碼應該封裝在個別的元件可以獨立使用者介面中執行，以及將相當容易重新排定工作無法完成時因為某些原因。

Firebase 作業發送器是 Google 可提供簡化的排程背景工作的 fluent API 從程式庫。 它被要取代的 Google 雲端管理員。 Firebase 作業發送器包含下列 Api:

* A`Firebase.JobDispatcher.JobService`是抽象類別必須使用會在背景工作中執行的邏輯擴充。
* A`Firebase.JobDispatcher.JobTrigger`宣告時應該啟動作業。 這通常表示為視窗的時間，例如，等候至少 30 秒後再啟動工作，但在 5 分鐘內執行作業。
* A`Firebase.JobDispatcher.RetryStrategy`包含應該要如何處理工作無法正確執行時的相關資訊。 重試策略指定想要再次執行該工作之前要等待的時間。 
* A`Firebase.JobDispatcher.Constraint`是選擇性的值，描述之前必須符合可執行工作，例如裝置 unmetered 網路上的條件或收費。
* `Firebase.JobDispatcher.Job`是一種 API，可統一--工作單位，可由排程中的前一個 Api `JobDispatcher`。 `Job.Builder`類別用來具現化`Job`。
* A`Firebase.JobDispatcher.JobDispatcher`排程與作業系統的工作，以及提供方法來取消作業，如有必要，請使用先前的三個 Api。

若要排程工作與 Firebase 作業發送器，Xamarin.Android 應用程式必須封裝此型別擴充中的程式碼`JobService`類別。 `JobService` 有三個可以呼叫作業的存留期間的生命週期方法：

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; 這個方法是，工作會發生，而且應該永遠。 在主執行緒上執行。 這個方法會傳回`true`剩餘，如果有工作或`false`如果工作已完成。 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; 這稱為因故停止工作時。 它應該傳回`true`如果應該重新排程作業，以便稍後使用。
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; 這個方法時，會呼叫`JobService`完成任何非同步工作。 

若要排程的作業，應用程式會具現化`JobDispatcher`物件。 接著`Job.Builder`用來建立`Job`物件，提供給`JobDispatcher`會試一次，並排程執行作業。

本指南會討論如何將 Firebase 作業發送器新增至 Xamarin.Android 應用程式，並使用它來排程背景工作。

## <a name="requirements"></a>需求

Firebase 作業發送器需要 9 或更新版本的 Android API 層級。 Firebase 作業發送器程式庫相依於 Google Play 服務; 所提供的某些元件裝置必須已安裝的 Google Play 服務。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>使用在 Xamarin.Android 中的發送器 Firebase 工作程式庫

若要開始使用 Firebase 作業發送器，先新增[Xamarin.Firebase.JobDispatcher NuGet 套件](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)到 Xamarin.Android 專案。 搜尋 NuGet 套件管理員**Xamarin.Firebase.JobDispatcher**套件 （這是仍在發行前版本）。

在之後將 Firebase 作業發送器程式庫，建立`JobService`類別，然後加以排程來執行的執行個體`FirebaseJobDispatcher`。


### <a name="creating-a-jobservice"></a>建立 JobService

Firebase 作業發送器程式庫所執行的所有工作都必須在此型別擴充`Firebase.JobDispatcher.JobService`抽象類別。 建立`JobService`非常類似於建立`Service`Android 架構： 

1. 擴充`JobService`類別
2. 裝飾具有子類別`ServiceAttribute`。 雖然並非絕對必要，建議您明確設定`Name`參數，以協助偵錯`JobService`。 
3. 新增`IntentFilter`來宣告`JobService`中**AndroidManifest.xml**。 這也有助於找出並叫用的 Firebase 作業發送器程式庫`JobService`。

下列程式碼是簡單的範例`JobService`應用程式，請使用 TPL 以非同步方式執行一些工作：

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>建立 FirebaseJobDispatcher

可以排程任何工作之前，就必須建立`Firebase.JobDispatcher.FirebaseJobDispatcher`物件。 `FirebaseJobDispatcher`負責排程`JobService`。 下列程式碼片段是一種方式建立的執行個體`FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在先前的程式碼片段中，`GooglePlayDriver`是類別，可協助`FirebaseJobDispatcher`互動的裝置上的一些在 Google Play 服務中排程的 Api。 參數`context`是任何 Android `Context`，例如的活動。 目前`GooglePlayDriver`是唯一`IDriver`Firebase 作業發送器文件庫中的實作。 

Firebase 作業發送器的 Xamarin.Android 繫結提供擴充方法來建立`FirebaseJobDispatcher`從`Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

一次`FirebaseJobDispatcher`已具現化，就可以建立`Job`並執行程式碼`JobService`類別。 `Job`由`Job.Builder`物件，並將在下一節中討論。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>使用 Job.Builder 建立 Firebase.JobDispatcher.Job

`Firebase.JobDispatcher.Job`類別會負責將中繼資料的封裝執行所需`JobService`。 A`Job`包含資訊，例如之前必須符合作業可以執行，如果任何條件約束`Job`重複發生，或會導致該工作會執行任何觸發程序。  以最低限度`Job`必須具有_標記_(可識別作業的唯一字串`FirebaseJobDispatcher`) 和類型`JobService`應該執行。 Firebase 作業發送器會具現化`JobService`時要執行此作業的時間。  A`Job`使用的執行個體建立`Firebase.JobDispatcher.Job.JobBuilder`類別。 

下列程式碼片段是如何建立的最簡單的範例`Job`使用 Xamarin.Android 繫結：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`會執行某些基本的驗證檢查輸入的值上作業。 如果它不可能，則擲回例外狀況`Job.Builder`來建立`Job`。  `Job.Builder`會建立`Job`具有下列的預設值：

* A`Job`的_存留期_（多久會排程來執行） 是裝置重新開機時，才&ndash;裝置重新開機後`Job`會遺失。
* A`Job`不週期性&ndash;它將只執行一次。
* A`Job`將儘速執行排程。
* 預設重試策略`Job`是使用_指數輪詢_(在下面一節中的更詳細討論[設定 RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>排程工作

在建立後`Job`，它必須排程`FirebaseJobDispatcher`執行之前。 有兩種方法可以排程`Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

所傳回的值`FirebaseJobDispatcher.Schedule`將會是其中一個下列的整數值：

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; `Job`已成功排程。
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; 發生未知的問題發生了阻礙`Job`進行排程。
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; 無效`IDriver`曾經或`IDriver`以某種方式變得無法使用。 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; `Trigger`不支援。
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; 服務未正確設定，或無法使用。
 
### <a name="configuring-a-job"></a>設定工作

您可自訂的工作。 如何自訂工作的範例包括：

* [將參數傳遞至作業](#Passing_Parameters_to_a_Job) &ndash; A`Job`可能需要額外的值，以執行其工作，例如下載檔案。
* [設定條件約束](#Setting_Constraints)&ndash;可能需要符合特定條件時，才執行作業。 比方說，只有執行`Job`當裝置正在充電。 
* [指定何時`Job`應該執行](#Setting_Job_Triggers) &ndash; Firebase 作業 Dispatcher 可讓應用程式，以指定何時應執行作業的時間。  
* [宣告失敗作業的重試策略](#Setting_a_RetryStrategy) &ndash; A_重試策略_提供指引來`FirebaseJobDispatcher`上該怎麼處理`Jobs`，無法完成。 

每個主題都將在下列各節中更多討論。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>將參數傳遞至作業

藉由建立的參數傳遞至作業`Bundle`連同傳遞`Job.Builder.SetExtras`方法：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle`從存取`IJobParameters.Extras`屬性上的`OnStartJob`方法：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>設定條件約束

條件約束可協助降低成本或電池清空，在裝置上的。 `Firebase.JobDispatcher.Constraint`類別會定義這些條件約束做為整數值：

* `Constraint.OnUnmeteredNetwork` &ndash; 當裝置連線到 unmetered 網路時，只能執行該工作。 這是可防止使用者產生資料費用。
* `Constraint.OnAnyNetwork` &ndash; 裝置連線到任何網路上執行的工作。 如果指定連同`Constraint.OnUnmeteredNetwork`，這個值將會優先採用。
* `Constraint.DeviceCharging` &ndash; 只有當裝置在充電時，請執行該工作。

設定條件約束`Job.Builder.SetConstraint`方法： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger`提供指引，關於工作應該開始的作業系統。 A`JobTrigger`已經_執行視窗_定義排程的時間，當`Job`應該執行。 [執行] 視窗具有_啟動視窗_值並_結束視窗_值。 開始時間範圍的裝置應該執行工作之前等待的秒數就必須結束視窗值最大執行之前要等待的秒數`Job`。 

A`JobTrigger`您可以使用建立`Firebase.Jobdispatcher.Trigger.ExecutionWindow`方法。  例如`Trigger.ExecutionWindow(15,60)`介於 15 到 60 秒從排定何時應執行工作的表示。 `Job.Builder.SetTrigger`方法是用來 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

預設值`JobTrigger`的值表示作業`Trigger.Now`，指定作業，這排程之後，儘速執行...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>設定 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`用來指定多少裝置延遲應該使用再嘗試重新執行失敗的作業。 A`RetryStrategy`已經_原則_，定義哪些時間基底演算法會用來重新排程失敗的作業，並指定的範圍，已排程作業執行視窗。 這_重新排定視窗_由兩個值所定義。 第一個值是重新排定工作之前要等待的秒數 (_初始輪詢_值)，和第二個數字是之前工作必須執行的秒數上限 (_最大輪詢_值)。 
 
這些整數值均重試原則的兩種類型：

* `RetryStrategy.RetryPolicyExponential` &ndash; _指數型輪詢_原則會以指數方式增加初始輪詢值之後每個失敗。 工作失敗，第一次程式庫會等候指定再重新排定工作的 （_i） 間隔&ndash;範例 30 秒。 作業失敗時，第二次程式庫會等候至少 60 秒，然後再嘗試執行此作業。 第三個嘗試失敗之後，程式庫會等待 120 秒，以此類推。 預設值`RetryStrategy`Firebase 作業發送器程式庫由`RetryStrategy.DefaultExponential`物件。 它有 30 秒的初始輪詢和 3600 秒的最大輪詢。
* `RetryStrategy.RetryPolicyLinear` &ndash; 此策略_線性輪詢_作業應該重新排程在執行設定的間隔，（直到成功）。 線性輪詢最適合針對必須儘速完成的工作或將快速自行解決問題。 Firebase 作業發送器程式庫定義`RetryStrategy.DefaultLinear`這已在至少 30 秒重新排定期間達 3600 秒。

您可定義自訂`RetryStrategy`與`FirebaseJobDispatcher.NewRetryStrategy`方法。 它會採用三個參數：

1. `int policy` &ndash; _原則_是其中一個先前`RetryStrategy`值`RetryStrategy.RetryPolicyLinear`，或`RetryStrategy.RetryPolicyExponential`。
2. `int initialBackoffSeconds` &ndash; _初始輪詢_延遲，以秒為單位，然後再嘗試再次執行該工作所需。 此設定的預設值為 30 秒。 
3. `int maximumBackoffSeconds` &ndash; _最大輪詢_值宣告的最大嘗試再次執行工作前所延遲的秒數。 預設值為 3600 秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>正在取消工作

它是可以取消已排程的所有作業或只是單一工作，使用`FirebaseJobDispatcher.CancelAll()`方法或`FirebaseJobDispatcher.Cancel(string)`方法：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

這兩種方法會傳回整數值：

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; 已成功取消作業。
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; 錯誤會導致無法取消作業。
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher`無法取消作業，因為沒有不具有效`IDriver`可用。

## <a name="summary"></a>總結

本指南會討論如何使用 Firebase 作業 Dispatcher 來以智慧方式在背景中執行的工作。 它討論如何將封裝執行的工作`JobService`以及如何使用`FirebaseJobDispatcher`排程工作，指定與條件`JobTrigger`和失敗應該如何處理包含`RetryStrategy`。


## <a name="related-links"></a>相關連結

- [在 NuGet 上的 Xamarin.Firebase.JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-作業-發送器在 GitHub 上](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher 繫結](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智慧型工作排程](https://developer.android.com/topic/performance/scheduling.html)
- [Android 使用電池及記憶體最佳化-Google I/O 2016 （影片）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
