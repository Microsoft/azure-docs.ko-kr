---
title: Azure Blockchain 워크 벤치 미리 보기 배포
description: Azure Blockchain 워크 벤치 미리 보기를 배포 하는 방법
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: fab61b5850815e480b4a380fdccd6c1df5b449cd
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189247"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Azure Blockchain 워크 벤치 미리 보기 배포

Azure Blockchain 워크 벤치 미리 보기는 Azure Marketplace 솔루션 템플릿을 사용 하 여 배포 됩니다. 템플릿은 블록체인 애플리케이션을 만드는 데 필요한 구성 요소의 배포를 간소화합니다. 배포되면 Blockchain Workbench는 사용자와 블록체인 애플리케이션을 만들고 관리할 수 있는 클라이언트 앱에 대한 액세스를 제공합니다.

Blockchain Workbench의 구성 요소에 대한 자세한 내용은 [Azure Blockchain Workbench 아키텍처](architecture.md)를 참조하세요.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>배포 준비

블록체인 Workbench를 사용하면 블록체인 기반 애플리케이션을 빌드하는 데 주로 사용되는 일련의 관련 Azure 서비스와 함께 블록체인 원장을 배포할 수 있습니다. 블록체인 Workbench를 배포하면 Azure 구독의 리소스 그룹 내에서 다음과 같은 Azure 서비스가 프로비전됩니다.

* App Service 계획 (표준)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (표준 S0) + SQL 논리 서버
* Azure Storage 계정 (표준 LRS)
* 용량이 1 인 가상 머신 확장 집합
* Virtual Network 리소스 그룹 (Load Balancer, 네트워크 보안 그룹, 공용 IP 주소, Virtual Network)
* Azure Blockchain 서비스. 이전 Blockchain 워크 벤치 배포를 사용 하는 경우 azure blockchain 서비스를 사용 하도록 Azure Blockchain 워크 벤치를 다시 배포 하는 것이 좋습니다.

다음은 **myblockchain** 리소스 그룹에서 만든 예제 배포입니다.

![예제 배포](media/deploy/example-deployment.png)

블록체인 Workbench의 비용은 기본 Azure 서비스 비용의 집계입니다. Azure 서비스에 대한 가격 책정 정보는 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 계산할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Blockchain Workbench를 사용하려면 Azure AD 구성 및 애플리케이션 등록이 필요합니다. 배포 또는 배포 후 스크립트를 실행하기 전에 Azure AD를 [구성을 수동으로](#azure-ad-configuration) 수행하도록 선택할 수 있습니다. Blockchain Workbench를 다시 배포하는 경우 [Azure AD 구성](#azure-ad-configuration)을 참조하여 Azure AD 구성을 확인합니다.

> [!IMPORTANT]
> Workbench는 Azure AD 애플리케이션을 등록하는 데 사용 중인 것과 동일한 테넌트에 배포될 필요가 없습니다. Workbench는 리소스를 배포할 수 있는 충분한 권한이 있는 테넌트에 배포되어야 합니다. Azure AD 테넌트에 대한 자세한 내용은 [Active Directory 테넌트를 가져오는 방법](../../active-directory/develop/quickstart-create-new-tenant.md) 및 [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)을 참조하세요.

## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench 배포

필수 단계가 완료되면 Blockchain Workbench를 배포할 준비가 되었습니다. 다음 섹션에서는 프레임워크를 배포하는 방법을 간략하게 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 오른쪽 위 모서리에서 사용자의 계정을 선택하고 Azure Blockchain Workbench를 배포할 원하는 Azure AD 테넌트로 전환합니다.
1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
1. **Blockchain** > **Azure Blockchain 워크 벤치 (미리 보기)** 를 선택 합니다.

    ![Azure Blockchain Workbench 만들기](media/deploy/blockchain-workbench-settings-basic.png)

    | 설정 | Description  |
    |---------|--------------|
    | 리소스 접두사 | 배포에 사용할 짧고 고유한 ID입니다. 이 값은 리소스 이름 지정에 대한 기준으로 사용됩니다. |
    | VM 사용자 이름 | 사용자 이름은 모든 VM(가상 머신)에 대한 관리자로 사용됩니다. |
    | 인증 유형 | VM에 연결하는 데 암호 또는 키 중에 무엇을 사용할지 선택합니다. |
    | 암호 | VM에 연결하는 데 암호가 사용됩니다. |
    | SSH | **ssh-rsa**로 시작하는 한 줄 형식의 RSA 공개 키를 사용하거나 여러 줄 PEM 형식을 사용합니다. Linux 및 OS X에서는 `ssh-keygen`을, Windows에서는 PuTTYGen을 사용하여 SSH 키를 생성할 수 있습니다. SSH 키에 대한 자세한 정보는 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md)을 참조하세요. |
    | 데이터베이스 및 Blockchain 암호 | 배포의 일부로 생성된 데이터베이스에 대한 액세스에 사용할 암호를 지정합니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 1 개의 소문자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 따옴표 (\`), 큰따옴표 ("), 작은따옴표 ('), 대시 (-) 및 semicolumn (;)) 사이 여야 합니다. |
    | 배포 지역 | Blockchain Workbench 리소스를 배포할 위치를 지정합니다. 최상의 가용성을 위해 이 위치는 **위치** 설정과 일치해야 합니다. |
    | Subscription | 배포에 사용할 Azure 구독을 지정합니다. |
    | 리소스 그룹 | **새로 만들기**를 선택하여 새 리소스 그룹을 만들고 고유한 리소스 그룹 이름을 지정합니다. |
    | 위치 | 프레임워크를 배포할 영역을 지정합니다. |

1. **확인**을 선택하여 기본 설정 구성 섹션을 완료합니다.

1. **고급 성능**에서 새 블록체인 네트워크를 만들지 아니면 기존 인증 증명 블록체인 네트워크를 사용할지를 선택합니다.

    **새로 만들기**를 사용하는 경우:

    *새로 만들기* 옵션은 기본 sku를 사용 하 여 Azure Blockchain 서비스 쿼럼 원장을 배포 합니다.

    ![새 블록체인 네트워크에 대한 고급 설정](media/deploy/advanced-blockchain-settings-new.png)

    | 설정 | Description  |
    |---------|--------------|
    | Azure Blockchain 서비스 가격 책정 계층 | 블록 체인 워크 벤치에 사용 되는 **기본** 또는 **표준** Azure blockchain 서비스 계층을 선택 합니다. |
    | Azure Active Directory 설정 | **나중에 추가**를 선택합니다.</br>참고: [Azure AD를 미리 구성](#azure-ad-configuration)하도록 선택했거나 재배포하는 경우 *지금 추가*하도록 선택합니다. |
    | VM 선택 | 블록 체인 네트워크에 대 한 기본 저장소 성능 및 VM 크기를 선택 합니다. Azure 무료 계층 같은 낮은 서비스 한도로 구독하는 경우 *표준 DS1 v2*와 같은 작은 크기의 VM을 선택합니다. |

    **기존 항목 사용**을 사용하는 경우:

    *기존 항목 사용* 옵션을 사용하면 Ethereum PoA(인증 증명) 블록체인 네트워크를 지정할 수 있습니다. 엔드포인트에는 다음과 같은 요구 사항이 적용됩니다.

   * 엔드포인트은 Ethereum PoA(인증 증명) 블록체인 네트워크여야 합니다.
   * 엔드포인트은 네트워크를 통해 공개적으로 액세스할 수 있어야 합니다.
   * PoA 블록체인 네트워크는 가스 가격이 0으로 설정되도록 구성되어야 합니다.

     > [!NOTE]
     > Blockchain Workbench 계정에는 자금이 지원되지 않습니다. 자금이 필요한 경우 트랜잭션은 실패합니다.

     ![기존 블록체인 네트워크에 대한 고급 설정](media/deploy/advanced-blockchain-settings-existing.png)

     | 설정 | Description  |
     |---------|--------------|
     | Ethereum RPC 엔드포인트 | 기존 PoA 블록체인 네트워크의 RPC 엔드포인트을 제공합니다. 엔드포인트는 https:// 또는 http://로 시작되고 포트 번호로 끝납니다. 예를 들어 `http<s>://<network-url>:<port>` |
     | Azure Active Directory 설정 | **나중에 추가**를 선택합니다.</br>참고: [Azure AD를 미리 구성](#azure-ad-configuration)하도록 선택했거나 재배포하는 경우 *지금 추가*하도록 선택합니다. |
     | VM 선택 | 블록 체인 네트워크에 대 한 기본 저장소 성능 및 VM 크기를 선택 합니다. Azure 무료 계층 같은 낮은 서비스 한도로 구독하는 경우 *표준 DS1 v2*와 같은 작은 크기의 VM을 선택합니다. |

1. **확인**을 선택하여 고급 설정을 완료합니다.

1. 매개 변수가 정확한지 확인하기 위해 요약을 검토합니다.

    ![요약](media/deploy/blockchain-workbench-summary.png)

1. **만들기**를 선택하여 약관에 동의하고 Azure Blockchain Workbench를 배포합니다.

배포는 최대 90분까지 걸릴 수 있습니다. Azure Portal을 사용하여 진행률을 모니터링할 수 있습니다. 새로 만든 리소스 그룹에서 **배포 > 개요**를 선택하여 배포된 아티팩트의 상태를 확인합니다.

> [!IMPORTANT]
> 배포 후 Active Directory 설정을 완료해야 합니다. **나중에 추가**하도록 선택한 경우 [Azure AD 구성 스크립트](#azure-ad-configuration-script)를 실행해야 합니다.  **지금 추가**하도록 선택한 경우 [회신 URL을 구성](#configuring-the-reply-url)해야 합니다.

## <a name="blockchain-workbench-web-url"></a>Blockchain 워크 벤치 웹 URL

Blockchain Workbench의 배포가 완료되면 새 리소스 그룹에 사용자의 Blockchain Workbench 리소스가 포함됩니다. Blockchain Workbench 서비스는 웹 URL을 통해 액세스됩니다. 다음 단계에서는 배포된 프레임워크의 웹 URL을 검색하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 창에서 **리소스 그룹**을 선택 합니다.
1. Blockchain Workbench를 배포할 때 지정한 리소스 그룹 이름을 선택합니다.
1. 목록을 형식 기준으로 사전순으로 정렬하려면 **형식** 열 제목을 선택합니다.
1. 형식이 **App Service**인 리소스가 두 개 있습니다. "-Api" 접미사가 *없는* App Service 유형의 리소스를 선택 합니다.

    ![App Service 목록](media/deploy/resource-group-list.png)

1. App Service **개요**에서 배포 된 블록 체인 워크 벤치에 대 한 웹 url을 나타내는 **URL** 값을 복사 합니다.

    ![App Service Essentials](media/deploy/app-service.png)

사용자 지정 도메인 이름을 Blockchain Workbench와 연결하려면 [Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성](../../app-service/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

## <a name="azure-ad-configuration-script"></a>Azure AD 구성 스크립트

Azure AD는 Blockchain Workbench 배포를 완료하도록 구성되어야 합니다. PowerShell 스크립트를 사용하여 구성을 수행합니다.

1. 브라우저에서 [Blockchain Workbench 웹 URL](#blockchain-workbench-web-url)로 이동합니다.
1. Cloud Shell을 사용하여 Azure AD를 설정하는 지침이 표시됩니다. 명령을 복사하고 Cloud Shell을 시작합니다.

    ![AAD 스크립트 시작](media/deploy/launch-aad-script.png)

1. Blockchain Workbench를 배포한 Azure AD 테넌트를 선택합니다.
1. Cloud Shell PowerShell 환경에서 명령을 붙여넣고 실행 합니다.
1. 메시지가 표시되면 Blockchain Workbench에 사용하려는 Azure AD 테넌트를 입력합니다. Blockchain Workbench에 대한 사용자를 포함하는 테넌트가 됩니다.

    > [!IMPORTANT]
    > 인증된 사용자는 Azure AD 애플리케이션 등록을 만들고 테넌트에서 위임된 애플리케이션 권한을 부여하는 권한이 필요합니다. 테넌트의 관리자에게 Azure AD 구성 스크립트를 실행하거나 새 테넌트를 만들도록 요청해야 합니다.

    ![Azure AD 테넌트 입력](media/deploy/choose-tenant.png)

1. 브라우저를 사용하여 Azure AD 테넌트에 인증하라는 메시지가 표시됩니다. 브라우저에서 웹 URL을 열고, 코드를 입력하고, 인증합니다.

    ![코드로 인증](media/deploy/authenticate.png)

1. 스크립트는 여러 개의 상태 메시지를 출력합니다. 테넌트가 성공적으로 프로비전된 경우 **성공** 상태 메시지가 표시됩니다.
1. Blockchain Workbench URL로 이동합니다. 디렉터리에 읽기 권한을 부여할지 동의를 묻는 메시지가 나타납니다. 이를 통해 Blockchain Workbench 웹앱은 테넌트의 사용자에 액세스할 수 있습니다. 테넌트 관리자인 경우 전체 조직에 대한 동의를 선택할 수 있습니다. 이 옵션은 테넌트의 모든 사용자에 대한 동의를 허용합니다. 그렇지 않으면 각 사용자에게 Blockchain Workbench 웹 애플리케이션을 처음 사용할 때 동의를 묻는 메시지가 표시됩니다.
1. **동의**를 선택하여 동의합니다.

     ![사용자 프로필 읽기에 대한 동의](media/deploy/graph-permission-consent.png)

1. 동의 후에 Blockchain Workbench 웹앱을 사용할 수 있습니다.

Azure Blockchain 워크 벤치 배포를 완료 했습니다. 배포 사용을 시작 하기 위한 제안 사항은 [다음 단계](#next-steps) 를 참조 하세요.

## <a name="azure-ad-configuration"></a>Azure AD 구성

배포 전에 Azure AD 설정을 수동으로 구성하거나 확인하도록 선택하는 경우 이 섹션의 모든 단계를 완료합니다. Azure AD 설정을 자동으로 구성하려는 경우 Blockchain Workbench를 배포한 후 [Azure AD 구성 스크립트](#azure-ad-configuration-script)를 사용합니다.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API 앱 등록

Blockchain Workbench 배포에는 Azure AD 애플리케이션의 등록이 필요합니다. Azure AD(Azure Active Directory) 테넌트에서 앱을 등록해야 합니다. 기존 테넌트를 사용하거나 새 테넌트를 만들 수 있습니다. 기존 Azure AD 테넌트를 사용하는 경우 Azure AD 테넌트 내에서 애플리케이션을 등록하고, Graph API 권한을 부여하고, 게스트 액세스를 허용할 수 있는 충분한 권한이 필요합니다. 기존 Azure AD 테 넌 트에서 충분 한 권한이 없는 경우 새 테 넌 트를 만듭니다.


1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 오른쪽 위 모서리에서 사용자의 계정을 선택하고 원하는 Azure AD 테넌트로 전환합니다. 테 넌 트는 Azure Blockchain 워크 벤치가 배포 되 고 응용 프로그램을 등록할 수 있는 권한이 있는 구독의 구독 관리자 테 넌 트 여야 합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **앱 등록** > **새 등록**을 선택 합니다.

    ![앱 등록](media/deploy/app-registration.png)

1. 표시 **이름을** 입력 하 고 **이 조직 디렉터리 에서만 계정을**선택 합니다.

    ![앱 등록 만들기](media/deploy/app-registration-create.png)

1. **등록** 을 선택 하 여 Azure AD 응용 프로그램을 등록 합니다.

### <a name="modify-manifest"></a>매니페스트 수정

다음으로 Blockchain Workbench 관리자를 지정하려면 Azure AD 내에서 애플리케이션 역할을 사용하도록 매니페스트를 수정해야 합니다.  애플리케이션 매니페스트에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 매니페스트](../../active-directory/develop/reference-app-manifest.md)를 참조하세요.


1. 매니페스트에 GUID가 필요 합니다. PowerShell 명령 `[guid]::NewGuid()` 또는 `New-GUID` cmdlet을 사용 하 여 GUID를 생성할 수 있습니다. 다른 옵션은 GUID 생성기 웹 사이트를 사용하는 것입니다.
1. 등록 한 응용 프로그램의 경우 **관리** 섹션에서 **매니페스트** 를 선택 합니다.
1. 다음으로 매니페스트의 **Approles** 섹션을 업데이트 합니다. `"appRoles": []`를 제공 된 JSON으로 바꿉니다. **id** 필드에 대한 값을 사용자가 생성한 GUID로 바꾸었는지 확인합니다. 

    ![매니페스트 편집](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > 값 **관리자**는 Blockchain Workbench 관리자를 식별하는 데 필요합니다.

1. 매니페스트에서 **Oauth2AllowImplicitFlow** 값도 **true**로 변경합니다.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. **저장**을 선택하여 매니페스트 변경 내용을 저장합니다.

### <a name="add-graph-api-required-permissions"></a>Graph API 필수 사용 권한 추가

API 애플리케이션은 디렉터리에 액세스하는 사용자로부터 사용 권한을 요청해야 합니다. API 애플리케이션에 대한 다음과 같은 필요한 권한을 설정합니다.

1. *Blockchain api* 앱 등록에서 **api 권한**을 선택 합니다. 기본적으로 Graph API **User. 읽기** 권한이 추가 됩니다.
1. 워크 벤치 응용 프로그램을 사용 하려면 사용자의 기본 프로필 정보에 대 한 읽기 권한이 있어야 합니다. *구성 된 사용 권한*에서 **사용 권한 추가**를 선택 합니다. **Microsoft api**에서 **Microsoft Graph**를 선택 합니다.
1. 워크 벤치 응용 프로그램은 인증 된 사용자 자격 증명을 사용 하므로 **위임 된 권한**을 선택 합니다.
1. *사용자* 범주에서 **User. Readbasic. All** 권한을 선택 합니다.

    ![Microsoft Graph User를 추가 하는 Azure AD 앱 등록 구성. ReadBasic. All 위임 된 권한](media/deploy/add-graph-user-permission.png)

    **권한 추가**를 선택합니다.

1. *구성 된 사용 권한*에서 도메인에 대 한 **관리자 동의 부여** 를 선택 하 고 확인 프롬프트에 대해 **예** 를 선택 합니다.

   ![권한 부여](media/deploy/client-app-grant-permissions.png)

   권한을 부여하면 Blockchain Workbench는 디렉터리의 사용자에 액세스할 수 있습니다. 읽기 권한은 Blockchain Workbench에서 구성원을 검색 및 추가하는 데 필요합니다.

### <a name="get-application-id"></a>애플리케이션 ID 가져오기

애플리케이션 ID 및 테넌트 정보는 배포를 위해 필요합니다. 배포하는 동안 사용에 대한 정보를 수집하고 저장합니다.

1. 등록 한 응용 프로그램에 대해 **개요**를 선택 합니다.
1. 나중에 배포 하는 동안 사용할 **응용 프로그램 ID** 값을 복사 하 고 저장 합니다.

    ![API 앱 속성](media/deploy/app-properties.png)

    | 저장할 설정  | 배포에서 사용 |
    |------------------|-------------------|
    | 응용 프로그램 (클라이언트) ID | Azure Active Directory 설정 &gt; 애플리케이션 ID |

### <a name="get-tenant-domain-name"></a>테넌트 도메인 이름 가져오기

애플리케이션이 등록된 Active Directory 테넌트 도메인 이름을 수집하고 저장합니다. 

왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **사용자 지정 도메인 이름**을 선택합니다. 도메인 이름을 복사하여 저장합니다.

![도메인 이름](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>게스트 사용자 설정

Azure AD 테넌트에 게스트 사용자가 있는 경우 Blockchain Workbench 사용자 할당 및 관리가 제대로 작동하는지 확인하도록 추가 단계를 수행합니다.

1. Azure AD 테넌트를 전환하고 **Azure Active Directory &gt; 사용자 설정 &gt; 협업 작업 설정 관리**를 선택합니다.
1. **게스트 사용자 권한이 제한됨**을 **아니요**로 설정합니다.
    ![외부 협업 설정](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>회신 URL 구성

Azure Blockchain Workbench가 배포되고 나면, 배포된 Blockchain Workbench 웹 URL의 Azure AD(Azure Active Directory) 클라이언트 애플리케이션 **회신 URL**을 구성해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 클라이언트 애플리케이션을 등록한 테넌트에 있는지 확인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **앱 등록**을 선택합니다.
1. 필수 구성 요소 섹션에 등록한 Azure AD 클라이언트 애플리케이션을 선택합니다.
1. **인증**을 선택 합니다.
1. [Blockchain 워크 벤치 웹 url](#blockchain-workbench-web-url) 섹션에서 검색 한 Azure Blockchain 워크 벤치 배포의 주 웹 url을 지정 합니다. 회신 URL은 `https://`를 접두사로 사용합니다. 예를 들어 `https://myblockchain2-7v75.azurewebsites.net`

    ![인증 회신 Url](media/deploy/configure-reply-url.png)

1. **고급 설정** 섹션에서 **액세스 토큰** 및 **ID 토큰**을 확인 합니다.

    ![인증 고급 설정](media/deploy/authentication-advanced-settings.png)

1. **저장**을 선택하여 클라이언트 등록을 업데이트합니다.

## <a name="remove-a-deployment"></a>배포 제거

배포가 더 이상 필요 없는 경우 블록체인 Workbench 리소스 그룹을 삭제하여 배포를 제거할 수 있습니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다. 
1. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

    ![리소스 그룹 삭제](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>다음 단계

이 방법 문서에서 Azure Blockchain Workbench를 배포했습니다. 블록 체인 애플리케이션을 만드는 방법을 알아보려면 다음 사용법 문서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench에서 블록체인 애플리케이션 만들기](create-app.md)
