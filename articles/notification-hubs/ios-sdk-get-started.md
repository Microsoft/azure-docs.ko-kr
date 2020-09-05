---
title: Azure Notification Hubs 및 iOS SDK를 사용하여 iOS에 푸시 알림 보내기
description: 이 자습서에서는 Azure Notification Hubs 및 Apple Push Notification Service를 사용하여 푸시 알림을 iOS 디바이스에 보내는 방법을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.date: 08/10/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: 7cdf095898bfe85e6f3b14fa1dcdb7b0c94ccde6
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042448"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 iOS 앱에 푸시 알림 보내기

이 자습서에서는 Azure Notification Hubs를 설정하고 [APNS(Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)를 통해 알림을 iOS 디바이스에 푸시하도록 자격 증명을 구성하는 방법을 보여 줍니다. 

이 자습서를 실행하는 것은 후속 Objective C 및 Swift iOS 자습서의 필수 구성 요소이며 다음 단계를 다룹니다.

- 인증서 서명 요청 파일을 생성합니다.
- 푸시 알림을 앱에 요청합니다.
- 앱에 대한 프로비저닝 프로필을 만듭니다.
- 알림 허브를 만듭니다.
- APNS 정보를 사용하여 알림 허브를 구성합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/free/)을 참조하세요.

다음 구성 요소도 필요합니다.

- 활성 [Apple Developer](https://developer.apple.com/) 계정
- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)를 실행하는 Mac
- iOS 버전 10 이상을 실행하는 iPhone 또는 iPad
- [Apple Portal](https://developer.apple.com/)에 등록되고 인증서와 연결된 물리적 디바이스

서비스에 익숙하지 않은 경우 [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md)를 참조하세요.

> [!NOTE]
> 알림 허브는 샌드박스 인증 모드만 사용하도록 구성됩니다. 이 인증 모드는 프로덕션 워크로드에 사용할 수 없습니다.

## <a name="generate-the-certificate-signing-request-file"></a>인증서 서명 요청 파일 생성

APNS(Apple Push Notification Service)는 인증서를 사용하여 푸시 알림을 인증합니다. 알림을 보내고 받는 데 필요한 푸시 인증서를 만들려면 다음 지침을 따르세요. 이러한 개념에 대한 자세한 내용은 [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 공식 설명서를 참조하세요.

Apple에서 서명된 푸시 인증서를 생성하는 데 사용하는 CSR(인증서 서명 요청) 파일을 생성합니다.

1. Mac에서 Keychain Access 도구를 실행합니다. 이는 실행 패드의 **Utilities(유틸리티)**  폴더 또는 **Other(기타)**  폴더에서 열 수 있습니다.

2.  **Keychain Access(키 집합 액세스)** 를 선택하고, **Certificate Assistant(인증서 도우미)** 를 펼친 다음, **Request a Certificate from a Certificate Authority(인증 기관에서 인증서 요청)** 를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="키 집합 액세스":::

   > [!NOTE]
   > 기본적으로 Keychain Access는 목록의 첫 번째 항목을 선택합니다. 이는 사용자가 **인증서** 카테고리에 있고, **Apple Worldwide Developer Relations Certification Authority**가 목록의 첫 번째 항목이 아닌 경우 문제가 될 수 있습니다. CSR(인증서 서명 요청)을 생성하기 전에 키가 아닌 항목이 있거나 **Apple Worldwide Developer Relations Certification Authority** 키가 선택되어 있는지 확인합니다.

3.  **User Email Address(사용자 이메일 주소)** 를 선택하고, **Common Name(일반 이름)**  값을 입력하고, **Saved to disk(디스크에 저장됨)** 를 지정했는지 확인한 다음, **Continue(계속)** 를 선택합니다.  **CA Email Address(CA 이메일 주소)** 는 필요하지 않으므로 비워둡니다.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="필요한 인증서 정보":::

4.  **Save As(다른 이름으로 저장)** 에서 CSR 파일의 이름을 입력하고, **Where(위치)** 에서 해당 위치를 선택한 다음, **Save(저장)** 를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="파일 이름 선택":::

   이 작업은 CSR 파일을 선택한 위치에 저장합니다. 기본 위치는 **Desktop(데스크톱)** 입니다. 이 파일에 대해 선택한 위치를 기억해 두세요.

다음으로 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만듭니다.

## <a name="register-your-app-for-push-notifications"></a>푸시 알림에 대해 앱 등록

푸시 알림을 iOS 앱에 보내려면 Apple에 애플리케이션을 등록하고 푸시 알림도 등록합니다.

1. 아직 앱을 등록하지 않은 경우 Apple Developer Center에서 [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동합니다. Apple ID를 사용하여 포털에 로그인하고, **Identifier(식별자)** 를 선택합니다. 그런 다음, **+** 를 선택하여 새 앱을 등록합니다.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="앱 ID 페이지":::

2.  **Register a New Identifier(새 식별자 등록)**  화면에서 **App ID(앱 ID)**  라디오 단추를 선택합니다. 그런 다음, **Continue(계속)** 를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="새 ID 등록 페이지":::

3. 새 앱에 대해 다음 세 가지 값을 업데이트한 다음, **Continue**를 선택합니다.

   - **설명**: 앱에 대한 설명이 포함된 이름을 입력합니다.
   - **Bundle ID**(번들 ID):  [앱 배포 가이드](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)에서 설명한 대로  **Organization Identifier.Product Name** 형식의 번들 ID를 입력합니다.  **Organization Identifie(조직 식별자)**  및 **Product Name(제품 이름)**  값은 XCode 프로젝트를 만들 때 사용하는 조직 식별자 및 제품 이름과 일치해야 합니다. 다음 스크린샷에서는 **NotificationHubs** 값이 조직 식별자로 사용되고, **GetStarted** 값이 제품 이름으로 사용됩니다. Xcode에서 올바른 게시 프로필을 사용하도록 **Bundle Identifier(번들 식별자)**  값이 Xcode 프로젝트의 값과 일치하는지 확인합니다.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="앱 ID 등록":::

   - **Push Notifications**:  **Capabilities(기능)**  섹션에서 **Push Notifications(푸시 알림)**  옵션을 확인합니다.

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="새 앱 ID 등록":::

      이 작업은 앱 ID를 생성하고 사용자에게 정보 확인을 요청합니다.  **Continue**를 선택한 다음, **Register(등록)** 를 선택하여 새 앱 ID를 확인합니다.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="새 앱 ID 확인":::

       **Register**가 선택되면 **Certificates, Identifiers & Profiles(인증서, 식별자 및 프로필)**  페이지에서 새 앱 ID가 줄 항목으로 표시됩니다.

4.  **Certificates, Identifiers & Profiles** 페이지의 **Identifiers(식별자)** 아래에서 방금 만든 앱 ID 줄 항목을 찾고, 해당 행을 선택하여 **Edit your App ID Configuration(앱 ID 구성 편집)**  화면을 표시합니다.

## <a name="create-a-certificate-for-notification-hubs"></a>Notification Hubs에 대한 인증서 만들기

> [!NOTE]
> iOS 13 릴리스부터는 토큰 기반 인증을 사용한 자동 알림만 받을 수 있습니다. APNS 자격 증명에 인증서 기반 인증을 사용하는 경우 토큰 기반 인증을 사용하도록 전환해야 합니다.

알림 허브가 **APNS**에서 작동하도록 설정하려면 인증서가 필요합니다. 이 작업은 다음 두 가지 방법 중 하나를 통해 수행할 수 있습니다.

- Notification Hubs에 직접 업로드할 수 있는 **.p12** 파일을 만듭니다.

-  [토큰 기반 인증](notification-hubs-push-notification-http2-token-authentication.md)에 사용할 수 있는 **.p8** 파일을 만듭니다(최신 방법).

두 번째 옵션에는 [APNS에 대한 토큰 기반(HTTP/2) 인증](notification-hubs-push-notification-http2-token-authentication.md)에서 설명한 대로 인증서를 사용하는 것에 비해 여러 가지 이점이 있습니다. 그러나 여기에는 두 가지 방법 모두에 대한 단계가 나와 있습니다.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>옵션 1: Notification Hubs에 직접 업로드할 수 있는 .p12 푸시 인증서 만들기

1. 선택한 **Push Notifications(푸시 알림)**  옵션까지 아래로 스크롤한 다음, **Configure(구성)** 를 선택하여 인증서를 만듭니다.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="앱 ID":::

2.  **Apple Push Notification service SSL Certificates(Apple Push Notification Service SSL 인증서)**  창이 표시됩니다.  **Development SSL Certificate(개발 SSL 인증서)**  섹션에서 **Create Certificate(인증서 만들기)**  단추를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="인증서 만들기":::

    **Create a new Certificate(새 인증서 만들기)**  화면이 표시됩니다.

   > [!NOTE]
   > 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인합니다.

3.  **Choose File(파일 선택)** 을 선택하고, 첫 번째 작업에서 CSR 파일을 저장한 위치로 이동한 다음, 인증서 이름을 두 번 클릭하여 로드합니다. 그런 다음, **Continue(계속)** 를 선택합니다.

4. 포털에서 인증서가 만들어지면 **Download(다운로드)**  단추를 선택합니다. 인증서를 저장하고 저장된 위치를 기억합니다.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="인증서 다운로드":::

   인증서가 다운로드되어 **Downloads** 폴더에 저장됩니다.

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="인증서 파일 찾기":::

   기본적으로 다운로드된 개발 인증서는 이름이 **aps_development.cer**로 지정됩니다.

5. 다운로드한 **aps\_development.cer** 푸시 인증서를 두 번 클릭합니다. 이 작업은 다음 이미지에 보이는 것처럼 새 인증서를 Keychain에 설치합니다.

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="키 집합 액세스":::

   인증서의 이름은 다를 수 있지만 **Apple 개발 iOS 푸시 서비스**가 앞에 옵니다.

6. Keychain Access(키 집합 액세스)에서 마우스 오른쪽 단추로 **Certificates(인증서)**  범주에서 만든 새 푸시 인증서를 클릭합니다.  **Export(내보내기)** 를 선택하고, 파일 이름을 지정한 다음, **.p12** 형식, **Save(저장)** 를 차례로 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="인증서 내보내기":::

   암호를 사용하여 인증서를 보호하도록 선택할 수 있지만 이는 선택 사항입니다. 암호 만들기를 무시하려면 **OK(확인)** 를 클릭합니다. 내보낸 .p12 인증서의 파일 이름과 위치를 적어둡니다. 이는 APNS를 통한 인증을 사용하도록 설정하는 데 사용됩니다.

   > [!NOTE]
   > .p12 파일 이름과 위치가 이 자습서에 표시된 것과 다를 수 있습니다.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>옵션 2: 토큰 기반 인증에 사용할 수 있는 .p8 인증서 만들기

1. 다음 세부 사항에 유의합니다.

   - **앱 ID 접두사**( **팀 ID**임) 
   - **번들 ID**

2.  **Certificates, Identifiers & Profiles(인증서, 식별자 및 프로필)** 로 돌아가서 **Keys(키)** 를 클릭합니다. **APNS**에 대해 구성된 키가 이미 있는 경우 만든 직후 바로 다운로드한 .p8 인증서를 다시 사용할 수 있습니다. 그렇다면 3-5단계를 무시할 수 있습니다.

3.  **+**  단추(또는 **Create a key(키 만들기)**  단추)를 클릭하여 새 키를 만듭니다.

4. 적절한 **Key Name(키 이름)**  값을 입력하고, **APNS(Apple Push Notification Service)**  옵션을 선택하고, **Continue(계속)** 를 클릭하고, 다음 화면에서 **Register(등록)** 를 클릭합니다.

5.  **Download(다운로드)** 를 클릭하고, **.p8** 파일( `AuthKey_`를 접두사로 사용)을 보안 로컬 디렉터리로 이동한 다음, **Done(완료)** 을 클릭합니다.

   > [!IMPORTANT]
   > .p8 파일을 안전한 장소에 보관하고 백업을 저장해야 합니다. 키가 다운로드되면 다시 다운로드할 수 없으므로 서버 복사본이 제거됩니다.

6.  **Keys(키)** 에서 방금 만든 키(또는 대신 사용하도록 선택한 경우 기존 키)를 클릭합니다.

7.  **Key ID(키 ID)**  값을 적어 둡니다.

8.  [Visual Studio Code](https://code.visualstudio.com/)와 같이 적합한 애플리케이션에서 .p8 인증서를 연 다음, 키 값을 적어 둡니다. 이는 **-----BEGIN PRIVATE KEY-----**  및 **-----END PRIVATE KEY-----**  사이의 값입니다.

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   이는 나중에 Notification Hubs를 구성하는 데 사용되는 토큰 값입니다.

이러한 단계가 완료되면 나중에 [APNS 정보를 사용하여 알림 허브 구성](#configure-the-notification-hub-with-apns-information)에서 사용할 수 있도록 다음 정보가 있어야 합니다.

- **팀 ID**(1단계 참조) 
- **번들 ID**(1단계 참조) 
- **키 ID**(7단계 참조) 
- **토큰 값**(.p8 키 값, 8단계 참조) 

## <a name="create-a-provisioning-profile"></a>프로비저닝 프로필 만들기

1.  [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)로 돌아가서 **Certificates, Identifiers & Profiles(인증서, 식별자 및 프로필)** 를 선택하고, 왼쪽 메뉴에서 **Profiles(프로필**)를 선택한 다음, **+** 를 선택하여 새 프로필을 만듭니다.  **Register a New Provisioning Profile(새 프로비저닝 프로필 등록)**  화면이 표시됩니다.

2.  **Development(개발)**  아래에서 프로비저닝 프로필 유형으로 **iOS App Development(iOS 앱 개발)** 를 선택한 다음, **Continue(계속)** 를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="프로비저닝 프로필 목록":::

3. 다음으로, **App ID(앱 ID)**  드롭다운 목록에서 만든 앱 ID를 선택한 다음, **Continue(계속)** 를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="앱 ID 선택":::

4.  **Select certificates(인증서 선택)**  창에서 코드 서명에 사용하는 개발 인증서를 선택하고, **Continue(계속)** 를 선택합니다. 이 인증서는 사용자가 만든 푸시 인증서가 아닙니다. 계정이 없으면 만들어야 합니다. 인증서가 있으면 다음 단계로 건너뜁니다. 개발 인증서가 없으면 해당 인증서를 만들기 위해 다음을 수행합니다.

   1.  **No Certificates are available(인증서를 사용할 수 없음)** 이라는 메시지가 표시되면 **Create Certificate(인증서 만들기)** 를 선택합니다.
   2.  **Software(소프트웨어)**  섹션에서 **Apple Development(Apple 개발)** 를 선택합니다. 그런 다음, **Continue(계속)** 를 선택합니다.
   3.  **Create a New Certificate(새 인증서 만들기)**  화면에서 **Choose File(파일 선택)** 을 선택합니다.
   4. 이전에 만든 **Certificate Signing Request(인증서 서명 요청)**  인증서를 찾아서 선택한 다음, **Open(열기)** 을 선택합니다.
   5.  **Continue**를 선택합니다.
   6. 개발 인증서를 다운로드하고, 저장된 위치를 기억합니다.

5.  **Certificates, Identifiers & Profiles(인증서, 식별자 및 프로필)**  페이지로 돌아가고, 왼쪽 메뉴에서 **Profiles(프로필)** 를 선택한 다음, **+** 를 선택하여 새 프로필을 만듭니다.  **Register a New Provisioning Profile(새 프로비저닝 프로필 등록)**  화면이 표시됩니다.

6.  **Select certificates(인증서 선택)**  창에서 방금 만든 개발 인증서를 선택합니다. 그런 다음, **Continue(계속)** 를 선택합니다.

7. 다음으로, 테스트에 사용할 디바이스를 선택하고, **Continue(계속)** 를 선택합니다.

8. 마지막으로, **Provisioning Profile Name(프로비저닝 프로필 이름)** 에서 프로필 이름을 선택한 다음, **Generate(생성)** 를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="프로비저닝 프로필 이름 선택":::

9. 새 프로비저닝 프로필이 만들어지면 **Download(다운로드)** 를 선택합니다. 저장된 위치를 기억합니다.

10. 프로비저닝 프로필의 위치를 찾은 다음, 해당 프로필을 두 번 클릭하여 Xcode 개발 머신에 설치합니다.

## <a name="create-a-notification-hub"></a>알림 허브 만들기

이 섹션에서는 .p12 푸시 인증서 또는 토큰 기반 인증을 사용하여 알림 허브를 만들고, APNS를 사용하여 인증을 구성합니다. 이미 만든 알림 허브를 사용하려는 경우 5단계로 건너뛸 수 있습니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 메뉴에서  **모든 서비스**를 선택한 다음,  **모바일** 섹션에서  **Notification Hubs**를 선택합니다. 서비스 이름 옆의 별표 아이콘을 선택하여 서비스를 왼쪽 메뉴의  **즐겨찾기** 섹션에 추가합니다.  **Notification Hubs**가  **즐겨찾기**에 추가되면 이를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure Portal":::

3.  **Notification Hubs** 페이지의 도구 모음에서 **추가**를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="추가 도구 모음 단추":::

4.  **Notification Hubs** 페이지에서 다음 단계를 수행합니다.

   1.  **알림 허브**에 이름을 입력합니다.
   2.  **새 네임스페이스 만들기**에 이름을 입력합니다. 네임스페이스에는 하나 이상의 알림 허브가 포함되어 있습니다.
   3.  **위치** 드롭다운에서 값을 선택합니다. 이 값은 알림 허브를 만들려는 위치를 지정합니다.
   4.  **리소스 그룹**에서 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
   5.  **만들기**를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="속성 설정":::

5.  **알림**(벨 아이콘)을 선택하고,  **리소스로 이동**을 선택합니다.  **Notification Hubs** 페이지의 목록을 새로 고치고 허브를 선택할 수도 있습니다.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="포털 알림":::

6. 목록에서  **액세스 정책**을 선택합니다. 사용할 수 있는 두 가지 연결 문자열을 적어 둡니다. 나중에 푸시 알림을 처리하는 데 필요합니다.

   > [!IMPORTANT]
   > 애플리케이션에서 **DefaultFullSharedAccessSignature** 정책을 사용하지 마세요. 이는 백 엔드에서만 사용할 수 있습니다.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="연결 문자열":::

## <a name="configure-the-notification-hub-with-apns-information"></a>APNS 정보를 사용하여 알림 허브 구성

 **Notification Services** 아래에서 **Apple(APNS)** 을 선택한 다음, 이전에 [Notification Hubs에 대한 인증서 만들기](#create-a-certificate-for-notification-hubs) 섹션에서 선택한 방법에 따라 적절한 단계를 수행합니다.

> [!NOTE]
> 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우에만 **애플리케이션 모드**에 대해 **프로덕션**을 사용합니다.

### <a name="option-1-use-a-p12-push-certificate"></a>옵션 1: .p12 푸시 인증서 사용

1.  **인증서**를 선택합니다.

2. 파일 아이콘을 선택합니다.

3. 이전에 내보낸 .p12 파일을 선택한 다음, **열기**를 선택합니다.

4. 필요한 경우 올바른 암호를 지정합니다.

5.  **샌드박스** 모드를 선택합니다.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="구성":::

6.  **저장**을 선택합니다.

### <a name="option-2-use-token-based-authentication"></a>옵션 2: 토큰 기반 인증 사용

1.  **토큰**을 선택합니다.

2. 앞서 얻은 다음 값을 입력합니다.

   - **키 ID**
   - **번들 ID**
   - **팀 ID**
   - **토큰**

3.  **샌드박스**를 선택합니다.

4.  **저장**을 선택합니다.

이제 APNS를 사용하여 알림 허브를 구성했습니다. 또한 앱을 등록하고 푸시 알림을 보내는 데 필요한 연결 문자열이 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure에서 알림 허브를 만들고, 구성하고, APNS(Apple Push Notification Service)를 통해 알림을 애플리케이션에 보낼 수 있도록 구성했습니다. 다음으로, iOS 애플리케이션 샘플을 만들고 Azure Notifications Hubs SDK를 통합하여 Azure Portal을 통해 보내는 푸시 알림을 받을 수 있습니다. 선택한 언어에 따라 다음 자습서로 계속 진행하세요.

- [자습서: Swift를 사용하여 iOS 앱과 통합]()
