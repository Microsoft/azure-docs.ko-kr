---
title: Azure Storage 탐색기 문제 해결 가이드 | Microsoft Docs
description: Azure Storage 탐색기에 대 한 디버깅 기술 개요
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3e26365c4273611c81682a760695522575f3875d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483383"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage 탐색기 문제 해결 가이드

Microsoft Azure Storage 탐색기는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 작업할 수 있게 하는 독립 실행형 앱입니다. 앱은 Azure, National Clouds 및 Azure Stack에서 호스트되는 저장소 계정에 연결할 수 있습니다.

이 가이드에는 Storage 탐색기에 나타나는 일반적인 문제에 대한 솔루션이 요약되어 있습니다.

## <a name="role-based-access-control-permission-issues"></a>역할 기반 액세스 제어 권한 문제

[역할 기반 액세스 제어 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 에 대 한 사용 권한 집합을 결합 하 여 Azure 리소스의 세분화 된 액세스 관리 기능을 제공 _역할_입니다. Storage 탐색기에서 작업 하는 RBAC를 가져오기 위해 수행할 수 있는 몇 가지 제안 사항 다음과 같습니다.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Storage 탐색기에서 내 리소스를 확인 하나요?

RBAC를 사용 하 여 저장소 리소스에 액세스 하는 데 문제가 있는 경우 적절 한 역할 할당 되지 않은 때문에 수 있습니다. 다음 섹션에서는 Storage 탐색기는 현재 저장소 리소스에 액세스 하려면 필요한 권한을 설명 합니다.

확실 하지 않은 적절 한 역할 또는 권한이 있는 경우 Azure 계정 관리자에 게 문의 합니다.

#### <a name="read-listget-storage-accounts"></a>읽기: 저장소 계정 나열/가져오기

저장소 계정 나열할 수 있는 권한이 있어야 합니다. "읽기 권한자" 역할 할당이 사용 권한을 가져올 수 있습니다.

#### <a name="list-storage-account-keys"></a>저장소 계정 키 나열

Storage 탐색기 요청을 인증 하려면 계정 키를 이용할 수 있습니다. "참가자" 역할 등의 더 강력한 역할을 사용 하 여 키에 대 한 액세스를 가져올 수 있습니다.

> [!NOTE]
> 액세스 키는 보유 하는 사람에 게 무제한의 사용 권한을 부여 합니다. 따라서는 일반적으로 것 계정 사용자에 게 전달할 수 있습니다. 액세스 키를 해지 해야 할 경우 다시 생성할 수 있습니다 하는 [Azure Portal](https://portal.azure.com/)합니다.

#### <a name="data-roles"></a>데이터 역할

리소스에서 데이터를 읽으려고 하는 액세스 권한을 부여 하는 역할을 하나 이상 할당 되어야 합니다. 예를 들어, 목록 또는 blob을 다운로드 해야 할 경우 해야 적어도 "Storage Blob 데이터 읽기 권한자" 역할.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Storage 탐색기에서 내 리소스를 보려면 관리 계층 역할을 하나요?

Azure Storage에 대 한 액세스의 두 계층: _management_ 하 고 _데이터_입니다. 구독 및 저장소 계정 관리 계층을 통해 액세스 됩니다. 컨테이너, blob 및 다른 데이터 리소스는 데이터 계층을 통해 액세스 됩니다. 예를 들어, Azure에서 저장소 계정의 목록을 가져오려는 경우 관리 끝점에 요청을 보냅니다. 계정에서 blob 컨테이너의 목록을 원하는 경우 적절 한 서비스 끝점에 요청을 보냅니다.

RBAC 역할 관리 또는 데이터 계층 액세스에 대 한 권한을 포함할 수 있습니다. 예를 들어 "읽기 권한자" 역할에는 계층 리소스를 관리 하는 읽기 전용 액세스 부여합니다.

엄격히 말해, "읽기 권한자" 역할 데이터 계층 권한 없음 제공 및 데이터 계층에 액세스 하기 위한 필요 하지 않습니다.

Storage 탐색기 쉽게 수에 대 한 Azure 리소스에 연결 하는 데 필요한 정보를 수집 하 여 리소스에 액세스 합니다. 예를 들어, blob 컨테이너를 표시 하려면 Storage 탐색기는 목록 컨테이너 요청을 blob 서비스 끝점에 보냅니다. 해당 끝점을 가져오려면 Storage 탐색기는 구독 목록을 검색 하 고 저장소 계정에 액세스할 수 있는 키를 누릅니다. 하지만 저장소 계정과 구독을 찾으려면 Storage 탐색기에 액세스할 수 있어야 관리 계층입니다.

관리 계층 사용 권한을 부여 하는 역할에 없는 경우 Storage 탐색기는 데이터 계층에 연결 하는 데 필요한 정보를 가져올 수 없습니다.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>내 관리자 로부터 필요 관리 계층 권한을 받을 수 없는 경우에 어떻게 합니까?

이 이번에는 RBAC 관련 솔루션 아직 없습니다. 이 문제를 해결 하는 SAS URI를 요청할 수 있습니다 [리소스를 연결할](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas)합니다.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>오류: 인증서 체인의 자체 서명된 인증서(및 유사 오류)

인증서 오류는 다음과 같은 두 상황 중 하나에서 발생합니다.

1. 앱이 "투명 프록시"를 통해 연결됩니다. 즉, 회사 서버와 같은 서버가 HTTPS 트래픽을 가로 채고 해독한 다음, 자체 서명된 인증서를 사용하여 암호화하는 것을 의미합니다.
2. 수신한 HTTPS 메시지에 자체 서명된 SSL 인증서를 삽입하는 애플리케이션을 실행하고 있습니다. 인증서 삽입을 수행하는 애플리케이션에는 바이러스 백신, 네트워크 트래픽 검사 소프트웨어 등이 포함됩니다.

Storage 탐색기가 자체 서명된 인증서나 신뢰할 수 없는 인증서를 발견하면, 수신된 HTTPS 메시지가 변경되었는지 여부를 더 이상 알 수 없습니다. 자체 서명된 인증서의 복사본이 있으면 다음 단계를 통해 Storage 탐색기가 이를 신뢰하게 할 수 있습니다.

1. 인증서의 Base-64 인코딩 X.509(.cer) 사본 가져오기
2. **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음, 파일 선택기를 사용하여 해당 .cer 파일을 찾고 선택하고 엽니다.

이 문제는 여러 인증서(루트 및 중간)의 결과로 발생할 수도 있습니다. 오류를 해결하려면 인증서를 모두 추가해야 합니다.

인증서의 출처를 확신하지 못할 경우 다음 단계를 통해 확인할 수 있습니다.

1. OpenSSL을 설치합니다.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html)(라이트 버전이면 충분함)
    * Mac 및 Linux: 운영 체제에 포함되어야 합니다.
2. OpenSSL을 실행합니다.
    * Windows: 설치 디렉터리를 열고 **/bin/** 을 클릭한 다음 **openssl.exe**를 두 번 클릭합니다.
    * Mac 및 Linux: 터미널에서 **openssl**을 실행합니다.
3. `s_client -showcerts -connect microsoft.com:443` 실행
4. 자체 서명된 인증서를 찾습니다. 자체 서명 된 인증서는 잘 모르는 경우 검색할 어디서 나 주체 `("s:")` 발급자 `("i:")` 동일 합니다.
5. 자체 서명된 인증서를 찾았으면 각 인증서에 대해 **----- BEGIN CERTIFICATE -----** 부터 **----- END CERTIFICATE -----** 까지 모든 줄을 복사하여 새 .cer 파일에 붙여넣습니다.
6. Storage 탐색기를 열고 **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음 파일 선택기를 사용하여 만든 .cer 파일을 찾고 선택하고 엽니다.

위의 단계를 사용하여 자체 서명된 인증서를 찾을 수 없는 경우 사용자 의견 도구를 통해 당사에 문의하여 도움을 받습니다. 또는 명령줄에서 `--ignore-certificate-errors` 플래그를 사용하여 Storage 탐색기를 시작합니다. 이 플래그로 시작하면 Storage 탐색기가 인증서 오류를 무시하게 됩니다.

## <a name="sign-in-issues"></a>로그인 문제

### <a name="blank-sign-in-dialog"></a>빈 로그인 대화 상자

빈 로그인 대화 상자는 가장 많은 원인은 ADFS Storage 탐색기를 묻는 전자에서 지원 되지 않습니다 리디렉션을 수행 하 합니다. 이 문제를 해결하기 위해 디바이스 코드 흐름을 로그인에 사용할 수 있습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. "미리 보기로 이동"-"장치 로그인 코드 사용" > 있습니다.
2. 왼쪽 세로 막대의 플러그 아이콘 또는 계정 패널의 “계정 추가”를 통해 연결 대화 상자를 엽니다.
3. 에 로그인 하려면 어떤 환경을 선택 합니다.
4. “로그인” 단추를 클릭합니다.
5. 다음 패널의 지침을 따릅니다.

기본 브라우저가 이미 다른 계정에 로그인 하기 때문에 사용 하려는 계정에 로그인 하는 데 문제가 있다면 수 있습니다.

1. 링크 및 코드를 브라우저의 개인 세션에 수동으로 복사합니다.
2. 링크 및 코드를 다른 브라우저에 수동으로 복사합니다.

### <a name="reauthentication-loop-or-upn-change"></a>재인증 루프 또는 UPN 변경

재인증 루프에 있거나 계정 중 한 계정의 UPN을 변경한 경우 다음을 수행하세요.

1. 모든 계정을 제거한 다음, Storage 탐색기를 닫습니다
2. 컴퓨터에서 .IdentityService 폴더를 삭제합니다. Windows에서 폴더는 `C:\users\<username>\AppData\Local`에 있습니다. Mac 및 Linux의 경우 사용자 디렉토리의 루트에서 폴더를 찾을 수 있습니다.
3. Mac 또는 Linux를 사용하는 경우 OS의 키 저장소에서 Microsoft.Developer.IdentityService 항목을 삭제해야 합니다. Mac의 경우 키 저장소가 "Gnome 키 집합" 애플리케이션입니다. Linux의 경우 일반적으로 애플리케이션은 "키링"으로 불리는데 이름은 배포에 따라 다를 수 있습니다.

### <a name="conditional-access"></a>조건부 액세스

Windows 10, Linux 또는 macOS에서 Storage 탐색기를 사용하는 경우 조건부 액세스가 지원되지 않습니다. Storage 탐색기에서 사용되는 AAD 라이브러리의 제한 사항 때문입니다.

## <a name="mac-keychain-errors"></a>Mac 키 집합 오류

macOS 키 집합은 Storage 탐색기의 인증 라이브러리에서 문제를 유발하는 상태가 될 수 있습니다. 이 상태에서 키 집합을 가져오려면 다음 단계를 수행 합니다.

1. Storage 탐색기를 닫습니다.
2. 키 집합을 엽니다(**cmd+space**, keychain 입력, enter 키 누름).
3. "로그인" 키 집합을 선택합니다.
4. 자물쇠 아이콘을 클릭하여 키 집합을 잠급니다(완료되면 자물쇠가 잠긴 모양으로 바뀌며 열려 있는 앱에 따라 몇 초 정도 걸릴 수 있음).

    ![Image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage 탐색기를 시작합니다.
6. "서비스 허브가 키 집합에 액세스하려고 합니다" 같은 팝업 메시지가 나타납니다. 그럴 때 Mac 관리자 계정의 암호를 입력하고 **항상 허용**(또는 **항상 허용**을 사용할 수 경우 **허용**)을 클릭합니다.
7. 로그인을 시도합니다.

### <a name="general-sign-in-troubleshooting-steps"></a>일반적인 로그인 문제 해결 단계

* MacOS에 있는 경우를 "Waiting 인증용..."를 통해 로그인 창이 나타나지 않으며 대화 상자에서 시도 [이 단계](#mac-keychain-errors)
* Storage 탐색기 다시 시작
* 인증 창이 비어 있는 경우 1분 이상 기다렸다가 인증 대화 상자를 닫습니다.
* 컴퓨터와 Storage 탐색기에 대해 프록시와 인증서 설정이 올바르게 구성되었는지 확인합니다.
* 동일한 컴퓨터에 Visual Studio 2017에 대 한 액세스 권한이 및 로그인에 Windows, Visual Studio 2017에 로그인 하십시오. Visual Studio 2017에 성공적인 로그인 후 Storage 탐색기를 열고 계정 패널에서 계정을 확인할 수 있어야 합니다.

이러한 방법으로 문제를 해결하지 못한 경우 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다.

### <a name="missing-subscriptions-and-broken-tenants"></a>누락된 구독 및 손상된 테넌트

성공적으로 로그인한 후 구독을 검색할 수 없는 경우 다음 단계에 따라 문제를 해결합니다.

* 해당 계정이 원하는 구독에 액세스할 수 있는지 확인합니다. 사용 하려는 Azure 환경에 대 한 포털에 로그인 하 여 액세스를 확인할 수 있습니다.
* 올바른 Azure 환경(Azure, Azure 중국 21Vianet, Azure 독일, Azure 미국 정부 또는 사용자 지정 환경)을 사용하여 로그인했는지 확인합니다.
* 프록시 뒤에 있는 경우 Storage 탐색기 프록시를 제대로 구성했는지 확인합니다.
* 계정을 제거하고 다시 추가해 봅니다.
* "자세한 정보" 링크가 있는 경우 실패하는 테넌트에 대해 어떤 오류 메시지가 보고되는지 찾아보고 참조하세요. 확인한 오류 메시지로 무엇을 할지 확실하지 않은 경우 자유롭게 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)하세요.

## <a name="cannot-remove-attached-account-or-storage-resource"></a>연결된 계정 또는 저장소 리소스를 제거할 수 없음

연결된 계정이나 저장소 리소스를 UI를 통해 제거할 수 없는 경우 다음 폴더를 삭제하여 연결된 리소스를 모두 수동으로 삭제할 수 있습니다.

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> 위의 폴더를 삭제하기 전에 Storage 탐색기를 닫습니다.

> [!NOTE]
> SSL 인증서를 가져온 적이 있는 경우 `certs` 디렉터리의 콘텐츠를 백업합니다. 다음에는 백업을 사용하여 SSL 인증서를 다시 가져올 수 있습니다.

## <a name="proxy-issues"></a>프록시 문제

먼저 입력한 다음 정보가 모두 올바른지 확인합니다.

* 프록시 URL 및 포트 번호
* 프록시가 요구하는 경우 사용자 이름과 암호

> [!NOTE]
> Storage 탐색기 프록시 설정 구성에 대 한 프록시 자동 구성 파일을 지원 하지 않습니다.

### <a name="common-solutions"></a>일반적인 솔루션

그래도 문제가 계속되면 다음 문제 해결 방법을 시도해 봅니다.

* 프록시를 사용하지 않고 인터넷에 연결할 수 있으면 프록시 설정을 사용하지 않고 Storage 탐색기가 작동하는지 확인합니다. 이 경우 프록시 설정에 문제가 있을 수 있습니다. 프록시 관리자와 함께 문제를 식별합니다.
* 프록시 서버를 사용하는 다른 애플리케이션이 예상대로 작동하는지 확인합니다.
* 사용할 Azure 환경에 대한 포털에 로그인하여 확인
* 서비스 엔드포인트에서 응답을 수신할 수 있는지 확인합니다. 엔드포인트 URL 중 하나를 브라우저에 입력합니다. 연결할 수 있으면 InvalidQueryParameterValue 또는 유사한 XML 응답을 수신해야 합니다.
* 다른 사용자가 Storage 탐색기와 프록시 서버를 함께 사용하고 있는 경우 연결할 수 있는지 확인합니다. 연결할 수 있다면 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

### <a name="tools-for-diagnosing-issues"></a>문제를 진단하기 위한 도구

Windows용 Fiddler와 같은 네트워킹 도구가 있으면 다음과 같은 문제를 진단할 수 있습니다.

* 프록시를 통해 작업해야 하는 경우 프록시를 통해 연결하도록 네트워킹 도구를 구성해야 할 수 있습니다.
* 네트워킹 도구에서 사용하는 포트 번호를 확인합니다.
* Storage 탐색기에 로컬 호스트 URL과 네트워킹 도구의 포트 번호를 프록시 설정으로 입력합니다. 올바르게 수행되면 네트워킹 도구가 Storage 탐색기에서 만든 네트워크 요청을 관리 및 서비스 엔드포인트에 기록하기 시작합니다. 예를 들어 브라우저에서 Blob 엔드포인트에 대해 https://cawablobgrs.blob.core.windows.net/을 입력하면 다음과 유사한 응답이 수신됩니다. 이 응답을 통해 리소스에 액세스할 수는 없지만 리소스가 있음을 알 수 있습니다.

![코드 샘플](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>프록시 서버 관리자 문의

프록시 설정이 올바른 경우 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

* 프록시가 Azure 관리 또는 리소스 엔드포인트에 대한 트래픽을 차단하지 않는지 확인합니다.
* 프록시 서버에서 사용하는 인증 프로토콜을 확인합니다. Storage 탐색기는 현재 NTLM 프록시를 지원하지 않습니다.

## <a name="unable-to-retrieve-children-error-message"></a>"하위 항목을 검색할 수 없음" 오류 메시지

프록시를 통해 Azure에 연결된 경우 프록시 설정이 올바른지 확인합니다. 리소스에 대 한 액세스는 구독 또는 계정 소유자 로부터 부여 된 경우에 읽기 또는 해당 리소스에 대 한 사용 권한을 나열 하는 확인 합니다.

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>연결 문자열에는 전체 구성 설정이 없음

이 오류 메시지를 수신하는 경우 Storage 계정의 키를 가져올 필요한 사용 권한이 없을 수 있습니다. 이 경우인지 확인하려면 포털로 이동하고 Storage 계정을 찾습니다. 저장소 계정에 대 한 노드를 마우스 오른쪽 단추로 클릭 하 고 "의 포털 열기"를 클릭 하 여 신속 하 게 수행할 수 있습니다. 이렇게 하면 "액세스 키" 블레이드로 이동합니다. 키를 볼 수 있는 권한이 없으면 다음 페이지가 표시 됩니다는 "사용자 권한이 없습니다" 메시지와 함께 합니다. 이 문제를 해결 하려면 다른 곳에서 계정 키를 확보 하 고 이름 및 키를 사용 하 여 연결 하거나 sas 저장소 계정에 게 요청 하 고 사용 하 여 저장소 계정에 연결할 수 있습니다.

계정 키를 표시 하는 경우는 문제를 해결 하 지원할 수 있도록 GitHub에서 문제를 제출 합니다.

## <a name="issues-with-sas-url"></a>SAS URL 문제

SAS URL을 사용하여 서비스에 연결하고 이 오류가 발생하는 경우:

* URL이 리소스를 읽거나 나열하는 데 필요한 권한을 제공하는지 확인합니다.
* URL이 만료되지 않았는지 확인합니다.
* SAS URL이 액세스 정책을 기반으로 하는 경우 액세스 정책이 철회되지 않았는지 확인합니다.

실수로 잘못된 SAS URL을 사용하여 첨부하고 분리할 수 없는 경우 다음 단계를 따릅니다.

1. Storage 탐색기를 실행 하는 경우 F12 키를 눌러 개발자 도구 창을 엽니다.
2. [애플리케이션] 탭을 클릭한 다음, 왼쪽 트리에서 로컬 스토리지 &gt; file://을 클릭합니다.
3. 문제가 있는 SAS URI의 서비스 유형과 연결된 키를 찾습니다. 예를 들어 잘못된 SAS URI가 Blob 컨테이너에 대한 것이면 `StorageExplorer_AddStorageServiceSAS_v1_blob`으로 명명된 키를 찾아봅니다.
4. 키의 값은 JSON 배열이어야 합니다. 잘못된 URI와 연결된 개체를 찾아 제거합니다.
5. Storage 탐색기를 다시 로드하려면 Ctrl+R 키를 누릅니다.

## <a name="linux-dependencies"></a>Linux 종속성

일반적으로 Linux에서 Storage 탐색기를 실행 하려면 다음 패키지가 필요 합니다.

* [.NET core 2.0 런타임](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgnome-keyring-common` 및 `libgnome-keyring-dev`
* `libgconf-2-4`

배포에 따라 다른 있거나 설치 해야 하는 패키지가 더 이상 표시 합니다.

Storage 탐색기는 Ubuntu 18.04, 14.04 및 16.04에 공식적으로 지원 됩니다. 클린 컴퓨터에 대 한 설치 단계는 다음과 같습니다.

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Storage 탐색기 다운로드
2. .NET Core 런타임을 설치, 확인 된 가장 최신 버전은: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) (최신 버전을 이미 설치한 경우 해야 Storage 탐색기를 패치, 아래 참조)
3. `sudo apt-get install libgconf-2-4` 실행
4. `sudo apt install libgnome-keyring-common libgnome-keyring-dev` 실행

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Storage 탐색기 다운로드
2. .NET Core 런타임을 설치, 확인 된 가장 최신 버전은: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) (최신 버전을 이미 설치한 경우 해야 Storage 탐색기를 패치, 아래 참조)
3. `sudo apt install libgnome-keyring-dev` 실행

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Storage 탐색기 다운로드
2. .NET Core 런타임을 설치, 확인 된 가장 최신 버전은: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) (최신 버전을 이미 설치한 경우 해야 Storage 탐색기를 패치, 아래 참조)
3. `sudo apt install libgnome-keyring-dev` 실행

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Storage 탐색기 최신 버전의.NET Core에 대 한 패치 
.NET core 2.0 설치 및 Storage 탐색기 1.7.0 버전을 실행 하는 보다 큰 또는 이전 버전이 있는 경우 다음 단계를 완료 하 여 Storage 탐색기를 패치 하는 가능성이 필요 합니다.
1. 1.5.43 StreamJsonRpc의 버전을 다운로드 [nuget에서](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)합니다. 페이지의 오른쪽에 있는 "패키지 다운로드" 링크를 찾아보십시오.
2. 패키지를 다운로드 한 후 해당 파일 확장명을 변경 `.nupkg` 를 `.zip`
3. 패키지를 압축을 풉니다.
4. `streamjsonrpc.1.5.43/lib/netstandard1.1/`으로 이동합니다.
5. 복사 `StreamJsonRpc.dll` Storage 탐색기 폴더 안에 다음 위치에 있습니다.
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>오픈의 탐색기에서 Azure portal에서 작동 하지 않습니다.

Azure portal에서 "에서 탐색기 열기" 단추를 작동 하지 않으면 호환 브라우저를 사용 하 고 있는지 확인 합니다. 다음 브라우저 호환성을 위해 테스트 되었습니다.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>다음 단계

어떤 솔루션으로도 문제를 해결하지 못할 경우 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다. 왼쪽 아래 모서리의 "GitHub에 문제 보고" 단추를 사용하면 신속하게 GitHub로 이동할 수 있습니다.

![사용자 의견](./media/storage-explorer-troubleshooting/feedback-button.PNG)
