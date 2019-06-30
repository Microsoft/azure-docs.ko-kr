---
title: Speech SDK 로깅-음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech SDK에서 로깅을 사용 하도록 설정 합니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: e8f470647c218188705cfa65cba7077e62d1b2ac
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148021"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Speech SDK에서 로깅을 사용 하도록 설정

파일에 로깅은 Speech SDK에 대 한 선택적 기능입니다. 개발 중 로깅 추가 정보 및 Speech SDK의 핵심 구성 요소에서 진단 정보를 제공합니다. 속성을 설정 하 여 활성화할 수 있습니다 `Speech_LogFilename` 음성 구성 개체를 로그 파일의 이름과 위치에 있습니다. 로깅과 인식기를 해당 구성에서 만들어지면 전역적으로 활성화할 수 있습니다. 나중에 비활성화할 수 없습니다. 로깅 세션을 실행 하는 동안 로그 파일의 이름을 변경할 수 없습니다.

> [!NOTE]
> 로깅은 JavaScript를 제외하고 모든 지원되는 음성 SDK 프로그래밍 언어에서 사용할 수 있습니다.

## <a name="sample"></a>샘플

로그 파일 이름은 구성 개체에 지정됩니다. `SpeechConfig`를 예로 들어 `config` 라는 인스턴스를 만들었다고 가정합니다.

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

config 개체에서 인식기를 만들 수 있습니다. 이렇게 하면 모든 인식기에 대한 로깅이 활성화됩니다.

> [!NOTE]
> config 개체에서 `SpeechSynthesizer`를 만드는 경우, 로깅이 활성화되지 않습니다. 로깅이 활성화되면 ,`SpeechSynthesizer`에서 진단 정보를 받게 됩니다.

## <a name="create-a-log-file-on-different-platforms"></a>다른 플랫폼에서 로그 파일 만들기

Windows 또는 Linux의 경우, 로그 파일은 사용자가 쓰기 권한을 가진 어떠한 경로에라도 있을 수 있습니다. 기타 운영 체제에서 파일 시스템 위치에 대한 쓰기 권한은 기본적으로 제한될 수 있습니다.

### <a name="universal-windows-platform-uwp"></a>UWP(유니버설 Windows 플랫폼)

UWP 응용 프로그램은 응용 프로그램 데이터 위치(로컬, 로밍, 또는 임시) 중 하나에 로그 파일 배치를 해야 합니다. 로컬 응용 프로그램 폴더에 로그 파일을 만들 수 있습니다.

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

UWP 응용 프로그램의 파일 액세스 권한에 대한 추가 정보는 [여기](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)에 제공됩니다.

### <a name="android"></a>Android

내부 저장소, 외부 저장소 또는 캐시 디렉터리에 로그 파일을 저장할 수 있습니다. 내부 저장소나 캐시 디렉터리에서 생성된 파일은 응용 프로그램 전용입니다. 외부 저장소에 로그 파일을 만드는 것이 좋습니다.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

위의 코드는 응용 프로그램별 디렉터리의 루트의 외부 저장소에 로그 파일을 저장합니다. 사용자는 파일 관리자를 사용하여 파일에 액세스할 수 있습니다(일반적으로 `Android/data/ApplicationName/logfile.txt`). 해당 파일은 응용 프로그램을 제거할 때 삭제됩니다.

또한 매니테스트 파일에서 `WRITE_EXTERNAL_STORAGE` 권한을 요청해야 합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Android 응용 프로그램의 데이터 및 파일 저장소에 대한 추가 정보는 [여기](https://developer.android.com/guide/topics/data/data-storage.html)에 제공됩니다.

#### <a name="ios"></a>iOS

응용 프로그램 샌드박스 내의 디렉터리에만 액세스할 수 있습니다. 문서, 라이브러리 및 임시 디렉터리에만 파일을 만들 수 있습니다. 문서 디렉터리의 파일은 사용자가 사용할 수 있게 할 수 있습니다. 다음 코드 조각은 응용 프로그램 문서 디렉터리에서 로그 파일을 생성하는 것을 보여 줍니다.

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

생성된 파일에 액세스하려면, 응용 프로그램의 `Info.plist` 속성 목록에 다음 속성을 추가합니다.

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

iOS의 파일 시스템에 대한 추가 정보는 [여기](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)에 제공됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)

