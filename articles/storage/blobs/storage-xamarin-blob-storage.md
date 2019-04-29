---
title: Xamarin에서 개체(Blob) 저장소를 사용하는 방법 | Microsoft Docs
description: Xamarin용 Azure Storage 클라이언트 라이브러리를 사용하면 개발자들이 기본 사용자 인터페이스를 가진 iOS, Android 및 Windows Store 앱을 만들 수 있습니다. 이 스토리지를 만든 후에 쉽게 액세스할 수 있도록 하려면 Azure File Storage 정보를 텍스트 파일에 저장하고 해당 위치에 대한 경로를 기록합니다.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b9c707dcc1628f685661f88aaed29612465a5469
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098085"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Xamarin에서 Blob Storage를 사용하는 방법

Xamarin을 사용하면 개발자들이 공유된 C# 코드베이스를 사용하여 기본 사용자 인터페이스를 가진 iOS, Android 및 Windows Store 앱을 만들 수 있습니다. 다음 섹션에서는 Azure File Storage를 로컬 시스템에 탑재하는 방법에 대한 지침을 제공합니다. Azure Storage에 대한 자세한 내용을 보려면 코드를 살펴보기 전에 [Microsoft Azure Storage 소개](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>새 Xamarin 애플리케이션 만들기
이 자습서에서는 Android, iOS 및 Windows를 대상으로 하는 앱을 만듭니다. 이 앱은 단순히 컨테이너를 만들고 이 컨테이너에 blob을 업로드합니다. Windows에서 Visual Studio를 사용하게 되지만 macOS에서 Xamarin Studio를 사용하여 앱을 만들 때도 동일한 방식을 적용할 수 있습니다.

다음 단계에 따라 애플리케이션을 만듭니다.

1. 아직 하지 않은 경우 [Visual Studio용 Xamarin](https://www.xamarin.com/download)을 다운로드하여 설치합니다.
2. Visual Studio를 열고 빈 앱 (네이티브 이식 가능)을 만듭니다. **파일 > 새로 만들기 > 프로젝트 > 플랫폼 간 > 비어 있는 앱 (네이티브 이식 가능)** 합니다.
3. 솔루션 탐색기 창에서 해당 솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션에 대한 NuGet 패키지 관리**를 선택합니다. **WindowsAzure.Storage**를 검색하고 솔루션의 모든 프로젝트에 안정적인 최신 버전을 설치합니다.
4. 프로젝트를 빌드한 후 실행합니다.

이제 단추를 클릭하고 카운터를 증가시켜주는 애플리케이션이 완료되어야 합니다.

## <a name="create-container-and-upload-blob"></a>컨테이너 만들기 및 blob 업로드
다음으로 `(Portable)` 프로젝트에서 `MyClass.cs`에 일부 코드를 추가합니다. 이 코드는 컨테이너를 만들고 이 컨테이너에 Blob을 업로드합니다. `MyClass.cs` 는 다음과 같이 표시되어야 합니다.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

"your_account_name_here" 및 "your_account_key_here"를 실제 계정 이름과 계정 키로 바꾸어야 합니다. 

iOS, Android 및 Windows Phone 프로젝트에는 모두 이식 가능한 프로젝트에 대한 참조가 있으므로 모든 공유 코드를 한 곳에서 작성하고 이 코드를 모든 프로젝트에서 사용할 수 있습니다. 이제 각 프로젝트에 `MyClass.performBlobOperation()` 코드 줄을 추가하여 활용할 수 있습니다.

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>애플리케이션 실행
이제 Android 또는 Windows Phone 에뮬레이터에서 이 애플리케이션을 실행할 수 있습니다. iOS 에뮬레이터에서 이 애플리케이션을 실행할 수도 있지만 Mac이 있어야 합니다. 이 작업을 수행하는 방법에 대한 특정 지침은 [Visual Studio를 Mac에 연결](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

앱을 일단 실행하면 Storage 계정에 컨테이너 `mycontainer` 가 만들어집니다. 여기에는 `Hello, world!` 텍스트를 포함하는 Blob `myblob`가 있어야 합니다. [Microsoft Azure Storage Explorer](https://storageexplorer.com/)를 사용하여 이 사실을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 특히 Blob Storage에서 한 시나리오에 초점을 맞춰서 Xamarin에서 Azure Storage를 사용하는 플랫폼 간 애플리케이션을 만드는 방법을 배웠습니다. 그러나 Blob Storage 뿐만 아니라 Table, File 및 Queue Storage로도 많은 작업을 수행할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [.NET을 사용하여 Azure Blob Storage 시작](storage-dotnet-how-to-use-blobs.md)
* [Azure Files 소개](../files/storage-files-introduction.md)
* [.NET을 사용하여 Azure Files 개발](../files/storage-dotnet-how-to-use-files.md)
* [.NET을 사용하여 Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET을 사용하여 Azure Queue Storage 시작](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]