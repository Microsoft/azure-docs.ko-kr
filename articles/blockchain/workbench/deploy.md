---
title: Azure Blockchain Workbench 배포
description: Azure Blockchain Workbench 배포 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 5f488811e57ee20cb25db56b2d9e04202b17ffb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869813"
---
# <a name="deploy-azure-blockchain-workbench"></a>Azure Blockchain Workbench 배포

Azure Blockchain Workbench는 Azure Marketplace에서 솔루션 템플릿을 사용하여 배포됩니다. 템플릿은 블록체인 애플리케이션을 만드는 데 필요한 구성 요소의 배포를 간소화합니다. 배포되면 Blockchain Workbench는 사용자와 블록체인 애플리케이션을 만들고 관리할 수 있는 클라이언트 앱에 대한 액세스를 제공합니다.

Blockchain Workbench의 구성 요소에 대한 자세한 내용은 [Azure Blockchain Workbench 아키텍처](architecture.md)를 참조하세요.

## <a name="prepare-for-deployment"></a>배포 준비

블록체인 Workbench를 사용하면 블록체인 기반 애플리케이션을 빌드하는 데 주로 사용되는 일련의 관련 Azure 서비스와 함께 블록체인 원장을 배포할 수 있습니다. 블록체인 Workbench를 배포하면 Azure 구독의 리소스 그룹 내에서 다음과 같은 Azure 서비스가 프로비전됩니다.

* 1 Event Grid 항목
* 1 Service Bus 네임스페이스
* 1 Application Insights
* 1 SQL Database(표준 S0)
* 2 App Services(표준)
* 2 Azure Key Vaults
* 2 Azure Storage 계정(표준 LRS)
* 2 가상 머신 확장 집합(유효성 검사기 및 작업자 노드용)
* 2 Virtual Networks(각 가상 네트워크에 대한 부하 분산 장치, 네트워크 보안 그룹 및 공용 IP 주소 포함)
* 선택 사항: Azure Monitor

다음은 **myblockchain** 리소스 그룹에서 만든 예제 배포입니다.

![예제 배포](media/deploy/example-deployment.png)

블록체인 Workbench의 비용은 기본 Azure 서비스 비용의 집계입니다. Azure 서비스에 대한 가격 책정 정보는 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 계산할 수 있습니다.

> [!IMPORTANT]
> Azure 무료 계층 구독과 같은 낮은 서비스 한도로 구독을 사용하는 경우 VM 코어 할당량 부족으로 인해 배포가 실패할 수 있습니다. 배포하기 전에 [가상 머신 vCPU 할당량](../../virtual-machines/windows/quotas.md) 문서의 지침을 사용하여 할당량을 확인합니다. 기본적으로 VM 선택에는 6개의 VM 코어가 필요합니다. *표준 DS1 v2*와 같은 작은 크기로 VM을 변경하면 코어 수가 4로 줄어듭니다.

## <a name="prerequisites"></a>필수 조건

Azure Blockchain Workbench를 사용하려면 Azure AD 구성 및 애플리케이션 등록이 필요합니다. 배포 또는 배포 후 스크립트를 실행하기 전에 Azure AD를 [구성을 수동으로](#azure-ad-configuration) 수행하도록 선택할 수 있습니다. Blockchain Workbench를 다시 배포하는 경우 [Azure AD 구성](#azure-ad-configuration)을 참조하여 Azure AD 구성을 확인합니다.

> [!IMPORTANT]
> Workbench는 Azure AD 애플리케이션을 등록하는 데 사용 중인 것과 동일한 테넌트에 배포될 필요가 없습니다. Workbench는 리소스를 배포할 수 있는 충분한 권한이 있는 테넌트에 배포되어야 합니다. Azure AD 테넌트에 대한 자세한 내용은 [Active Directory 테넌트를 가져오는 방법](../../active-directory/develop/quickstart-create-new-tenant.md) 및 [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)을 참조하세요.



## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench 배포

필수 단계가 완료되면 Blockchain Workbench를 배포할 준비가 되었습니다. 다음 섹션에서는 프레임워크를 배포하는 방법을 간략하게 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 오른쪽 위 모서리에서 사용자의 계정을 선택하고 Azure Blockchain Workbench를 배포할 원하는 Azure AD 테넌트로 전환합니다.
3. 왼쪽 창에서 **리소스 만들기**를 선택합니다. **Marketplace 검색** 검색 창에서 `Azure Blockchain Workbench`를 검색합니다. 

    ![Marketplace 검색 창](media/deploy/marketplace-search-bar.png)

4. **Azure Blockchain Workbench**를 선택합니다.

    ![Marketplace 검색 결과](media/deploy/marketplace-search-results.png)

5. **만들기**를 선택합니다.
6. 기본 설정을 완료합니다.

    ![Azure Blockchain Workbench 만들기](media/deploy/blockchain-workbench-settings-basic.png)

    | 설정 | 설명  |
    |---------|--------------|
    | 리소스 접두사 | 배포에 사용할 짧고 고유한 ID입니다. 이 값은 리소스 이름 지정에 대한 기준으로 사용됩니다. |
    | VM 사용자 이름 | 사용자 이름은 모든 VM(가상 머신)에 대한 관리자로 사용됩니다. |
    | 인증 유형 | VM에 연결하는 데 암호 또는 키 중에 무엇을 사용할지 선택합니다. |
    | 암호 | VM에 연결하는 데 암호가 사용됩니다. |
    | SSH | **ssh-rsa**로 시작하는 한 줄 형식의 RSA 공개 키를 사용하거나 여러 줄 PEM 형식을 사용합니다. Linux 및 OS X에서는 `ssh-keygen`을, Windows에서는 PuTTYGen을 사용하여 SSH 키를 생성할 수 있습니다. SSH 키에 대한 자세한 정보는 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md)을 참조하세요. |
    | 데이터베이스 암호/데이터베이스 암호 확인 | 배포의 일부로 생성된 데이터베이스에 대한 액세스에 사용할 암호를 지정합니다. |
    | 배포 지역 | Blockchain Workbench 리소스를 배포할 위치를 지정합니다. 최상의 가용성을 위해 이 위치는 **위치** 설정과 일치해야 합니다. |
    | 구독 | 배포에 사용할 Azure 구독을 지정합니다. |
    | 리소스 그룹 | **새로 만들기**를 선택하여 새 리소스 그룹을 만들고 고유한 리소스 그룹 이름을 지정합니다. |
    | Location | 프레임워크를 배포할 영역을 지정합니다. |

7. **확인**을 선택하여 기본 설정 구성 섹션을 완료합니다.

8. **고급 성능**에서 새 블록체인 네트워크를 만들지 아니면 기존 인증 증명 블록체인 네트워크를 사용할지를 선택합니다.

    **새로 만들기**를 사용하는 경우:

    *새로 만들기* 옵션은 단일 구성원의 구독 내에서 Ethereum PoA(인증 증명) 노드 집합을 만듭니다. 

    ![새 블록체인 네트워크에 대한 고급 설정](media/deploy/advanced-blockchain-settings-new.png)

    | 설정 | 설명  |
    |---------|--------------|
    | 모니터링 | Azure Monitor를 사용하여 블록 체인 네트워크를 모니터링할 수 있도록 할지 여부를 선택합니다. |
    | Azure Active Directory 설정 | **나중에 추가**를 선택합니다.</br>참고: [Azure AD를 미리 구성](#azure-ad-configuration)하도록 선택했거나 다시 배포하는 경우 *지금 추가*하도록 선택합니다. |
    | VM 선택 | 블록체인 네트워크에 대한 기본 VM 크기를 선택합니다. Azure 무료 계층 같은 낮은 서비스 한도로 구독하는 경우 *표준 DS1 v2*와 같은 작은 크기의 VM을 선택합니다. |

    **기존 항목 사용**을 사용하는 경우:

    *기존 항목 사용* 옵션을 사용하면 Ethereum PoA(인증 증명) 블록체인 네트워크를 지정할 수 있습니다. 끝점에는 다음과 같은 요구 사항이 적용됩니다.

   * 끝점은 Ethereum PoA(인증 증명) 블록체인 네트워크여야 합니다.
   * 끝점은 네트워크를 통해 공개적으로 액세스할 수 있어야 합니다.
   * PoA 블록체인 네트워크는 가스 가격이 0으로 설정되도록 구성되어야 합니다.

     > [!NOTE]
     > Blockchain Workbench 계정에는 자금이 지원되지 않습니다. 자금이 필요한 경우 트랜잭션은 실패합니다.

     ![기존 블록체인 네트워크에 대한 고급 설정](media/deploy/advanced-blockchain-settings-existing.png)

     | 설정 | 설명  |
     |---------|--------------|
     | Ethereum RPC 끝점 | 기존 PoA 블록체인 네트워크의 RPC 끝점을 제공합니다. 엔드포인트는 https:// 또는 http://로 시작되고 포트 번호로 끝납니다. 예를 들어 `http<s>://<network-url>:<port>` |
     | Azure Active Directory 설정 | **나중에 추가**를 선택합니다.</br>참고: [Azure AD를 미리 구성](#azure-ad-configuration)하도록 선택했거나 다시 배포하는 경우 *지금 추가*하도록 선택합니다. |
     | VM 선택 | 블록체인 네트워크에 대한 기본 VM 크기를 선택합니다. |

9. **확인**을 선택하여 고급 설정을 완료합니다.

10. 매개 변수가 정확한지 확인하기 위해 요약을 검토합니다.

    ![요약](media/deploy/blockchain-workbench-summary.png)

11. **만들기**를 선택하여 약관에 동의하고 Azure Blockchain Workbench를 배포합니다.

배포는 최대 90분까지 걸릴 수 있습니다. Azure Portal을 사용하여 진행률을 모니터링할 수 있습니다. 새로 만든 리소스 그룹에서 **배포 > 개요**를 선택하여 배포된 아티팩트의 상태를 확인합니다.

> [!IMPORTANT]
> 배포 후 Active Directory 설정을 완료해야 합니다. **나중에 추가**하도록 선택한 경우 [Azure AD 구성 스크립트](#azure-ad-configuration-script)를 실행해야 합니다.  **지금 추가**하도록 선택한 경우 [회신 URL을 구성](#configuring-the-reply-url)해야 합니다.

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench 웹 URL

Blockchain Workbench의 배포가 완료되면 새 리소스 그룹에 사용자의 Blockchain Workbench 리소스가 포함됩니다. Blockchain Workbench 서비스는 웹 URL을 통해 액세스됩니다. 다음 단계에서는 배포된 프레임워크의 웹 URL을 검색하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **리소스 그룹**을 선택합니다.
3. Blockchain Workbench를 배포할 때 지정한 리소스 그룹 이름을 선택합니다.
4. 목록을 형식 기준으로 사전순으로 정렬하려면 **형식** 열 제목을 선택합니다.
5. 형식이 **App Service**인 리소스가 두 개 있습니다. “-api” 접미사가 *없는* 리소스 유형 **App Service**를 선택합니다.

    ![App Service 목록](media/deploy/resource-group-list.png)

6. App Service의 **Essentials** 섹션에서 배포된 Blockchain Workbench에 대한 웹 URL을 나타내는 **URL** 값을 복사합니다.

    ![App Service Essentials](media/deploy/app-service.png)

사용자 지정 도메인 이름을 Blockchain Workbench와 연결하려면 [Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성](../../app-service/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

## <a name="azure-ad-configuration-script"></a>Azure AD 구성 스크립트

Azure AD는 Blockchain Workbench 배포를 완료하도록 구성되어야 합니다. PowerShell 스크립트를 사용하여 구성을 수행합니다.

1. 브라우저에서 [Blockchain Workbench 웹 URL](#blockchain-workbench-web-url)로 이동합니다.
2. Cloud Shell을 사용하여 Azure AD를 설정하는 지침이 표시됩니다. 명령을 복사하고 Cloud Shell을 시작합니다.

    ![AAD 스크립트 시작](media/deploy/launch-aad-script.png)

3. Blockchain Workbench를 배포한 Azure AD 테넌트를 선택합니다.
4. Cloud Shell에서 명령을 붙여넣고 실행합니다.
5. 메시지가 표시되면 Blockchain Workbench에 사용하려는 Azure AD 테넌트를 입력합니다. Blockchain Workbench에 대한 사용자를 포함하는 테넌트가 됩니다.

    > [!IMPORTANT]
    > 인증된 사용자는 Azure AD 애플리케이션 등록을 만들고 테넌트에서 위임된 애플리케이션 권한을 부여하는 권한이 필요합니다. 테넌트의 관리자에게 Azure AD 구성 스크립트를 실행하거나 새 테넌트를 만들도록 요청해야 합니다.

    ![Azure AD 테넌트 입력](media/deploy/choose-tenant.png)

6. 브라우저를 사용하여 Azure AD 테넌트에 인증하라는 메시지가 표시됩니다. 브라우저에서 웹 URL을 열고, 코드를 입력하고, 인증합니다.

    ![코드로 인증](media/deploy/authenticate.png)

7. 스크립트는 여러 개의 상태 메시지를 출력합니다. 테넌트가 성공적으로 프로비전된 경우 **성공** 상태 메시지가 표시됩니다.
8. Blockchain Workbench URL로 이동합니다. 디렉터리에 읽기 권한을 부여할지 동의를 묻는 메시지가 나타납니다. 이를 통해 Blockchain Workbench 웹앱은 테넌트의 사용자에 액세스할 수 있습니다. 테넌트 관리자인 경우 전체 조직에 대한 동의를 선택할 수 있습니다. 이 옵션은 테넌트의 모든 사용자에 대한 동의를 허용합니다. 그렇지 않으면 각 사용자에게 Blockchain Workbench 웹 애플리케이션을 처음 사용할 때 동의를 묻는 메시지가 표시됩니다.
9. **동의**를 선택하여 동의합니다.

     ![사용자 프로필 읽기에 대한 동의](media/deploy/graph-permission-consent.png)

10. 동의 후에 Blockchain Workbench 웹앱을 사용할 수 있습니다.

## <a name="azure-ad-configuration"></a>Azure AD 구성

배포 전에 Azure AD 설정을 수동으로 구성하거나 확인하도록 선택하는 경우 이 섹션의 모든 단계를 완료합니다. Azure AD 설정을 자동으로 구성하려는 경우 Blockchain Workbench를 배포한 후 [Azure AD 구성 스크립트](#azure-ad-configuration-script)를 사용합니다.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API 앱 등록

Blockchain Workbench 배포에는 Azure AD 애플리케이션의 등록이 필요합니다. Azure AD(Azure Active Directory) 테넌트에서 앱을 등록해야 합니다. 기존 테넌트를 사용하거나 새 테넌트를 만들 수 있습니다. 기존 Azure AD 테넌트를 사용하는 경우 Azure AD 테넌트 내에서 애플리케이션을 등록하고, Graph API 권한을 부여하고, 게스트 액세스를 허용할 수 있는 충분한 권한이 필요합니다. 기존 Azure AD 테넌트에 충분한 권한이 없는 경우 새 테넌트를 만듭니다.


1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 오른쪽 위 모서리에서 사용자의 계정을 선택하고 원하는 Azure AD 테넌트로 전환합니다. 테넌트는 Workbench가 배포되는 구독의 구독 관리자 테넌트에 있어야 하며, 사용자에게 애플리케이션을 등록할 수 있는 충분한 권한이 있어야 합니다.
3. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

    ![앱 등록](media/deploy/app-registration.png)

4. 애플리케이션에 사용할 **이름** 및 **로그온 URL**을 입력합니다. 배포하는 동안 값이 변경되므로 자리 표시자 값을 사용할 수 있습니다. 

    ![앱 등록 만들기](media/deploy/app-registration-create.png)

    |설정  | 값  |
    |---------|---------|
    |이름 | `Blockchain API` |
    |애플리케이션 형식 |웹앱/API|
    |로그온 URL | `https://blockchainapi` |

5. **만들기**를 선택하여 Azure AD 애플리케이션을 등록합니다.

### <a name="modify-manifest"></a>매니페스트 수정

다음으로 Blockchain Workbench 관리자를 지정하려면 Azure AD 내에서 애플리케이션 역할을 사용하도록 매니페스트를 수정해야 합니다.  애플리케이션 매니페스트에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 매니페스트](../../active-directory/develop/reference-app-manifest.md)를 참조하세요.

1. 등록한 애플리케이션에 대해 등록된 애플리케이션 세부 정보 창에서 **매니페스트**를 선택합니다.
2. GUID를 생성합니다. PowerShell 명령 [guid] :: NewGuid () 또는 New-GUID cmdlet을 사용하여 GUID를 생성할 수 있습니다. 다른 옵션은 GUID 생성기 웹 사이트를 사용하는 것입니다.
3. 매니페스트의 **appRoles** 섹션을 업데이트하려고 합니다. 매니페스트 편집 창에서 **편집**을 선택하고 `"appRoles": []`를 제공된 JSON으로 바꿉니다. **id** 필드에 대한 값을 사용자가 생성한 GUID로 바꾸었는지 확인합니다. 

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

4. 매니페스트에서 **Oauth2AllowImplicitFlow** 값도 **true**로 변경합니다.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. **저장**을 선택하여 매니페스트 변경 내용을 저장합니다.

### <a name="add-graph-api-required-permissions"></a>Graph API 필수 사용 권한 추가

API 애플리케이션은 디렉터리에 액세스하는 사용자로부터 사용 권한을 요청해야 합니다. API 애플리케이션에 대한 다음과 같은 필요한 권한을 설정합니다.

1. Blockchain API 앱 등록에서 **설정 > 필요한 권한 > API 선택 > Microsoft Graph**를 선택합니다.

    ![API 선택](media/deploy/client-app-select-api.png)

    **선택**을 클릭합니다.

2. **위임된 권한**의 **액세스 허용**에서 **모든 사용자의 기본 프로필 읽기**를 선택합니다.

    ![액세스 허용](media/deploy/client-app-read-perms.png)

    **저장**을 선택한 다음, **완료**를 선택합니다.

3. **필요한 권한**에서 **사용 권한 부여**를 선택한 다음, 확인 프롬프트에서 **예**를 선택합니다.

   ![권한 부여](media/deploy/client-app-grant-permissions.png)

   권한을 부여하면 Blockchain Workbench는 디렉터리의 사용자에 액세스할 수 있습니다. 읽기 권한은 Blockchain Workbench에서 구성원을 검색 및 추가하는 데 필요합니다.

### <a name="get-application-id"></a>애플리케이션 ID 가져오기

애플리케이션 ID 및 테넌트 정보는 배포를 위해 필요합니다. 배포하는 동안 사용에 대한 정보를 수집하고 저장합니다.

1. 등록한 애플리케이션의 경우 **설정** > **속성**을 선택합니다.
2. **속성** 창에서 배포하는 동안 나중에 사용할 수 있도록 다음 값을 복사하여 저장합니다.

    ![API 앱 속성](media/deploy/app-properties.png)

    | 저장할 설정  | 배포에서 사용 |
    |------------------|-------------------|
    | 애플리케이션 UI | Azure Active Directory 설정 &gt; 애플리케이션 ID |

### <a name="get-tenant-domain-name"></a>테넌트 도메인 이름 가져오기

애플리케이션이 등록된 Active Directory 테넌트 도메인 이름을 수집하고 저장합니다. 

왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **사용자 지정 도메인 이름**을 선택합니다. 도메인 이름을 복사하여 저장합니다.

![도메인 이름](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>게스트 사용자 설정

Azure AD 테넌트에 게스트 사용자가 있는 경우 Blockchain Workbench 사용자 할당 및 관리가 제대로 작동하는지 확인하도록 추가 단계를 수행합니다.

1. Azure AD 테넌트를 전환하고 **Azure Active Directory > 사용자 설정 > 외부 공동 작업 설정 관리**를 선택합니다.
2. **게스트 사용자 권한이 제한됨**을 **아니요**로 설정합니다.
    ![외부 공동 작업 설정](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>회신 URL 구성

Azure Blockchain Workbench가 배포되고 나면, 배포된 Blockchain Workbench 웹 URL의 Azure AD(Azure Active Directory) 클라이언트 애플리케이션 **회신 URL**을 구성해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD 클라이언트 애플리케이션을 등록한 테넌트에 있는지 확인합니다.
3. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다. **앱 등록**을 선택합니다.
4. 필수 구성 요소 섹션에 등록한 Azure AD 클라이언트 애플리케이션을 선택합니다.
5. **설정 > 회신 URL**을 선택합니다.
6. **Azure Blockchain Workbench 웹 URL** 섹션에서 검색한 Azure Blockchain Workbench 배포의 주 웹 URL을 지정합니다. 회신 URL은 `https://`를 접두사로 사용합니다. 예를 들어 `https://myblockchain2-7v75.azurewebsites.net`

    ![회신 URL](media/deploy/configure-reply-url.png)

7. **저장**을 선택하여 클라이언트 등록을 업데이트합니다.

## <a name="remove-a-deployment"></a>배포 제거

배포가 더 이상 필요 없는 경우 블록체인 Workbench 리소스 그룹을 삭제하여 배포를 제거할 수 있습니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다. 
2. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

    ![리소스 그룹 삭제](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>다음 단계

이 방법 문서에서 Azure Blockchain Workbench를 배포했습니다. 블록 체인 애플리케이션을 만드는 방법을 알아보려면 다음 사용법 문서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench에서 블록체인 애플리케이션 만들기](create-app.md)
