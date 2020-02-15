---
title: 온-프레미스 데이터 게이트웨이 설치
description: Azure Logic Apps에서 온-프레미스의 데이터에 액세스하려면 온-프레미스 데이터 게이트웨이를 다운로드하고 설치해야 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: 797cd82327d68003d4e5f007d1f16e9534092ac0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191350"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps에 온-프레미스 데이터 게이트웨이 설치

[Azure Logic Apps에서 온-프레미스 데이터 원본에 연결](../logic-apps/logic-apps-gateway-connection.md)하기 전에 로컬 컴퓨터에 [온-프레미스 데이터 게이트웨이](https://aka.ms/on-premises-data-gateway-installer) 를 다운로드 하 여 설치 합니다. 게이트웨이는 온-프레미스 데이터 원본과 논리 앱 간에 빠른 데이터 전송 및 암호화를 제공 하는 브리지로 작동 합니다. Power BI, 전원 자동화, Power Apps 및 Azure Analysis Services와 같은 다른 클라우드 서비스와 동일한 게이트웨이 설치를 사용할 수 있습니다. 이러한 서비스에서 게이트웨이를 사용 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Microsoft Power 자동 온-프레미스 데이터 게이트웨이](/power-automate/gateway-reference)
* [Microsoft Power BI 온-프레미스 데이터 게이트웨이](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 온-프레미스 데이터 게이트웨이](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)

이 문서에서는 온-프레미스 데이터 게이트웨이를 다운로드, 설치 및 설정 하 여 Azure Logic Apps에서 온-프레미스 데이터 원본에 액세스할 수 있도록 하는 방법을 보여 줍니다. 이 항목의 뒷부분에서 [데이터 게이트웨이가 작동 하는 방식](#gateway-cloud-service) 에 대해 자세히 알아볼 수도 있습니다. 게이트웨이에 대 한 자세한 내용은 [온-프레미스 게이트웨이](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)란?을 참조 하세요.

<a name="requirements"></a>

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 있는 Azure 계정이 없는 경우 [무료 azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

  * Azure 계정은 단일 [Azure Active Directory (AZURE AD) 테 넌 트 또는 디렉터리](../active-directory/fundamentals/active-directory-whatis.md#terminology)에 속해야 합니다. 로컬 컴퓨터에 게이트웨이를 설치 하 고 관리 하려면 동일한 Azure 계정을 사용 해야 합니다.

  * 게이트웨이 설치 중에 azure 계정으로 로그인 합니다. 그러면 azure 계정 및 해당 계정에만 연결 됩니다. 나중에 Azure Portal에서 게이트웨이 설치를 등록 하 고 클레임 하는 Azure 게이트웨이 리소스를 만들 때 동일한 Azure 계정 및 Azure AD 테 넌 트를 사용 해야 합니다. Azure Logic Apps 온-프레미스 트리거 및 작업은 온-프레미스 데이터 원본에 연결 하기 위해 게이트웨이 리소스를 사용 합니다.

    > [!NOTE]
    > 하나의 게이트웨이 설치와 하나의 Azure 게이트웨이 리소스를 서로 연결할 수 있습니다. 동일한 게이트웨이 설치를 여러 Azure 계정 또는 Azure 게이트웨이 리소스에 연결할 수 없습니다. 그러나 Azure 계정은 여러 게이트웨이 설치 및 Azure gateway 리소스에 연결할 수 있습니다. 온-프레미스 트리거 또는 작업에서 다양 한 Azure 구독을 선택 하 고 연결 된 게이트웨이 리소스를 선택할 수 있습니다.

  * 회사 계정 또는 학교 계정으로 로그인 해야 합니다. *조직* 계정이 라고도 합니다 .이 계정에는 `username@contoso.com`같습니다. Azure B2B (게스트) 계정 또는 개인 Microsoft 계정 (예: @hotmail.com 또는 @outlook.com)을 사용할 수 없습니다.

    > [!TIP]
    > Office 365 제품에 등록 하 고 회사 메일 주소를 제공 하지 않은 경우 주소는 `username@domain.onmicrosoft.com`처럼 보일 수 있습니다. 사용자 계정은 Azure Active Directory (Azure AD)의 테 넌 트 내에 저장 됩니다. 대부분의 경우 Azure AD 계정의 UPN (사용자 계정 이름)은 전자 메일 주소와 동일 합니다.
    >
    > Microsoft 계정에 연결 된 [Visual Studio 표준 구독](https://visualstudio.microsoft.com/vs/pricing/) 을 사용 하려면 먼저 [Azure AD에서 테 넌 트를 만들거나](../active-directory/develop/quickstart-create-new-tenant.md) 기본 디렉터리를 사용 합니다. 디렉터리에 암호를 가진 사용자를 추가한 다음 해당 사용자에 게 Azure 구독에 대 한 액세스 권한을 부여 합니다. 그런 다음 이 사용자 이름 및 암호를 사용하여 게이트웨이 설치 중에 로그인할 수 있습니다.

* 로컬 컴퓨터에 대한 요구 사항은 다음과 같습니다.

  **최소 요구 사항**

  * .NET Framework 4.7.2
  * 64비트 버전의 Windows 7 또는 Windows Server 2008 R2 이상

  **권장 요구 사항**

  * 8코어 CPU
  * 8GB 메모리
  * 64 비트 버전의 Windows Server 2012 R2 이상
  * 스풀링을 위한 SSD (반도체 드라이브) 저장소

  > [!NOTE]
  > 게이트웨이가 Windows Server Core를 지원 하지 않습니다.

* **관련 고려 사항**

  * 도메인 컨트롤러가 아닌 로컬 컴퓨터에만 온-프레미스 데이터 게이트웨이를 설치 합니다. 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치할 필요가 없습니다. 모든 데이터 원본에 대 한 게이트웨이는 하나만 필요 하므로 각 데이터 원본에 대 한 게이트웨이를 설치할 필요가 없습니다.

    > [!TIP]
    > 대기 시간을 최소화하려면 권한이 있다고 가정하여 가능하면 데이터 원본과 가깝게 또는 동일한 컴퓨터에 게이트웨이를 설치하면 됩니다.

  * 유선 네트워크에 있고 인터넷에 연결 되어 있고 항상 켜져 있는 컴퓨터에 게이트웨이를 설치 하 고 절전 모드로 전환 하지 않습니다. 그렇지 않으면 게이트웨이를 실행할 수 없으며 무선 네트워크를 통해 성능이 저하 될 수 있습니다.

  * Windows 인증을 사용 하려는 경우 데이터 원본과 동일한 Active Directory 환경의 멤버인 컴퓨터에 게이트웨이를 설치 해야 합니다.

  * 게이트웨이 설치를 위해 선택한 지역은 나중에 논리 앱에 대 한 Azure 게이트웨이 리소스를 만들 때 선택 해야 하는 위치와 동일 합니다. 기본적으로이 지역은 azure 계정을 관리 하는 Azure AD 테 넌 트와 동일한 위치입니다. 그러나 게이트웨이를 설치 하는 동안에는 위치를 변경할 수 있습니다.

  * 게이트웨이 설치를 최신 버전으로 업데이트 하는 경우 클리너 환경을 위해 현재 게이트웨이를 먼저 제거 합니다.

  * 게이트웨이에는 Power BI에만 적용 되는 표준 모드와 개인 모드의 두 가지 모드가 있습니다. 동일한 컴퓨터에서 동일한 모드로 실행 되는 게이트웨이가 둘 이상 있을 수 없습니다.

  * Azure Logic Apps는 게이트웨이를 통해 읽기 및 쓰기 작업을 지원 합니다. 그러나 이러한 작업 [은 페이로드 크기에 제한이](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)있습니다.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>데이터 게이트웨이 설치

1. [로컬 컴퓨터에서 게이트웨이 설치 관리자를 다운로드하고 실행합니다](https://aka.ms/on-premises-data-gateway-installer).

1. 최소 요구 사항을 검토 하 고, 기본 설치 경로를 유지 하 고, 사용 약관에 동의한 다음, **설치**를 선택 합니다.

   ![요구 사항을 검토 하 고 사용 약관에 동의 합니다.](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. 게이트웨이가 성공적으로 설치 되 면 Azure 계정에 대 한 전자 메일 주소를 입력 하 고 **로그인**을 선택 합니다. 예를 들면 다음과 같습니다.

   ![회사 또는 학교 계정으로 로그인](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   게이트웨이 설치는 하나의 Azure 계정에만 연결할 수 있습니다.

1. **이 컴퓨터에 새 게이트웨이 등록** > **다음**을 선택 합니다. 이 단계에서는 게이트웨이 [클라우드 서비스](#gateway-cloud-service)에 게이트웨이 설치를 등록 합니다.

   ![로컬 컴퓨터에 게이트웨이 등록](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 게이트웨이 설치에 대해 다음 정보를 제공합니다.

   * Azure AD 테 넌 트 전체에서 고유한 게이트웨이 이름
   * 사용 하려는 복구 키 (8 자 이상 이어야 함)
   * 복구 키 확인

   ![게이트웨이 설치에 대 한 정보 제공](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 복구 키를 저장한 후 안전한 장소에 보관합니다. 위치를 변경 하거나, 이동 하거나, 복구 하거나, 게이트웨이 설치를 수행 하려는 경우이 키가 필요 합니다.

   [고가용성 시나리오](#high-availability)에 대 한 추가 게이트웨이를 설치할 때 선택 하는 **기존 게이트웨이 클러스터에 추가**하는 옵션을 확인 합니다.

1. 게이트웨이를 설치 하는 데 사용 되는 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) 게이트웨이 클라우드 서비스에 대 한 지역을 확인 합니다. 기본적으로이 지역은 Azure 계정에 대 한 Azure AD 테 넌 트와 동일한 위치입니다.

   ![게이트웨이 서비스 및 서비스 버스에 대 한 영역 확인](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 기본 지역을 수락 하려면 **구성**을 선택 합니다. 그러나 기본 지역이 가장 근접 한 지역이 아니면 지역을 변경할 수 있습니다.

   *게이트웨이 설치에 대한 지역을 변경하는 이유는 무엇인가요?*

   예를 들어, 대기 시간을 줄이기 위해 논리 앱과 동일한 지역으로 게이트웨이 지역을 변경할 수 있습니다. 또는 온-프레미스 데이터 원본에 가장 가까운 지역을 선택할 수 있습니다. *Azure의 게이트웨이 리소스*와 논리 앱은 서로 다른 위치에 있을 수 있습니다.

   1. 현재 지역 옆에 있는 **지역 변경**을 선택합니다.

      ![현재 게이트웨이 영역 변경](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 다음 페이지에서 **지역 선택** 목록을 열고 원하는 지역을 선택 하 고 **완료**를 선택 합니다.

      ![게이트웨이 서비스의 다른 지역 선택](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 최종 확인 창에서 정보를 검토 합니다. 이 예에서는 Logic Apps, Power BI, Power Apps 및 파워 자동화에 동일한 계정을 사용 하므로 이러한 모든 서비스에 대해 게이트웨이를 사용할 수 있습니다. 준비가 되 면 **닫기**를 선택 합니다.

   ![데이터 게이트웨이 정보 확인](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 이제 [게이트웨이 설치에 대 한 Azure 리소스를 만듭니다](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>통신 설정 확인 또는 조정

온-프레미스 데이터 게이트웨이는 클라우드 연결에 대 한 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 에 따라 다르며 게이트웨이의 연결 된 Azure 지역에 해당 하는 아웃 바운드 연결을 설정 합니다. 회사 환경에서 인터넷에 액세스 하기 위해 트래픽이 프록시 또는 방화벽을 통과 해야 하는 경우이 제한으로 인해 온-프레미스 데이터 게이트웨이가 게이트웨이 클라우드 서비스에 연결 하지 못할 수 있으며 Azure Service Bus. 게이트웨이에는 조정할 수 있는 여러 통신 설정이 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [온-프레미스 데이터 게이트웨이에 대 한 통신 설정 조정](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [온-프레미스 데이터 게이트웨이에 대 한 프록시 설정 구성](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>고가용성 지원

온-프레미스 데이터 액세스에 대 한 단일 실패 지점이 발생 하지 않도록 하려면 서로 다른 컴퓨터에서 여러 게이트웨이 설치 (표준 모드에만 해당)를 사용 하 고 클러스터 또는 그룹으로 설정할 수 있습니다. 이렇게 하면 기본 게이트웨이를 사용할 수 없는 경우 데이터 요청이 두 번째 게이트웨이로 라우팅됩니다. 컴퓨터에 표준 게이트웨이를 하나만 설치할 수 있으므로 클러스터에 있는 각 추가 게이트웨이를 다른 컴퓨터에 설치 해야 합니다. 온-프레미스 데이터 게이트웨이를 사용 하는 모든 커넥터는 고가용성을 지원 합니다.

* 기본 게이트웨이와 동일한 Azure 계정 및 해당 설치에 대 한 복구 키를 사용 하 여 하나 이상의 게이트웨이 설치가 이미 있어야 합니다.

* 기본 게이트웨이에서는 2017년 11월 또는 이후의 게이트웨이 업데이트가 실행되고 있어야 합니다.

기본 게이트웨이를 설정한 후 다른 게이트웨이 설치로 이동 하는 경우 **기존 게이트웨이 클러스터에 추가**를 선택 하 고, 기본 게이트웨이 (설치한 첫 번째 게이트웨이)를 선택 하 고, 해당 게이트웨이에 대 한 복구 키를 제공 합니다. 자세한 내용은 참조 [온-프레미스 데이터 게이트웨이에 대한 고가용성 클러스터](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)를 참조하세요.

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>위치 변경, 기존 게이트웨이 마이그레이션, 복원 또는 인수

게이트웨이 위치를 변경하거나, 게이트웨이 설치를 새 컴퓨터로 이동하거나, 손상된 게이트웨이를 복구하거나, 기존 게이트웨이에 대한 소유권을 가져와야 할 경우 게이트웨이 설치 동안 제공된 복구 키가 필요합니다.

1. 기존 게이트웨이가 있는 컴퓨터에서 게이트웨이 설치 관리자를 실행 합니다. 최신 게이트웨이 설치 관리자가 없으면 [최신 게이트웨이 버전을 다운로드](https://aka.ms/on-premises-data-gateway-installer)합니다.

   > [!NOTE]
   > 원본 게이트웨이가 설치 된 컴퓨터에서 게이트웨이를 복원 하기 전에 먼저 해당 컴퓨터에서 게이트웨이를 제거 해야 합니다. 이 작업을 수행 하면 원래 게이트웨이의 연결이 끊어집니다.
   > 클라우드 서비스에 대 한 게이트웨이 클러스터를 제거 하거나 삭제 하는 경우 해당 클러스터를 복원할 수 없습니다.

1. 설치 관리자가 열리면 게이트웨이를 설치 하는 데 사용 된 것과 동일한 Azure 계정으로 로그인 합니다.

1. **다음**예와 같이 **기존 게이트웨이 > 마이그레이션, 복원 또는 인수** 를 선택 합니다.

   ![Migrate, restore, or takeover an existing gateway(기존 게이트웨이 마이그레이션, 복원 또는 인수) 선택](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 사용 가능한 클러스터 및 게이트웨이에서를 선택 하 고 선택한 게이트웨이의 복구 키를 입력 합니다. 예를 들면 다음과 같습니다.

   ![게이트웨이를 선택 하 고 복구 키 제공](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 지역을 변경 하려면 **지역 변경**을 선택 하 고 새 지역을 선택 합니다.

1. 준비가 되 면 작업을 완료할 수 있도록 **구성** 을 선택 합니다.

## <a name="tenant-level-administration"></a>테 넌 트 수준 관리

Azure AD 테 넌 트의 모든 온-프레미스 데이터 게이트웨이에 대 한 가시성을 확보 하기 위해 테 넌 트의 전역 관리자는 테 넌 트 관리자로 [Power Platform 관리 센터](https://powerplatform.microsoft.com) 에 로그인 하 고 **데이터 게이트웨이** 옵션을 선택할 수 있습니다. 자세한 내용은 [온-프레미스 데이터 게이트웨이에 대 한 테 넌 트 수준 관리](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)를 참조 하세요.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>게이트웨이 다시 시작

기본적으로 로컬 컴퓨터의 게이트웨이 설치는 "온-프레미스 데이터 게이트웨이 서비스" 라는 Windows 서비스 계정으로 실행 됩니다. 그러나 게이트웨이 설치에는 "다음 계정으로 로그온" 계정 자격 증명에 `NT SERVICE\PBIEgwService` 이름이 사용 되 고 "서비스로 로그온" 권한이 있습니다.

> [!NOTE]
> Windows 서비스 계정은 온-프레미스 데이터 원본에 연결 하는 데 사용 되는 계정과 클라우드 서비스에 로그인 할 때 사용 하는 Azure 계정에 차이가 있습니다.

다른 Windows 서비스와 마찬가지로 게이트웨이를 다양 한 방식으로 시작 하 고 중지할 수 있습니다. 자세한 내용은 [온-프레미스 데이터 게이트웨이 다시 시작](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)을 참조 하세요.

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>게이트웨이 작동 원리

조직의 사용자는 이미 권한이 부여 된 액세스 권한이 있는 온-프레미스 데이터에 액세스할 수 있습니다. 그러나 이러한 사용자가 온-프레미스 데이터 원본에 연결 하려면 먼저 온-프레미스 데이터 게이트웨이를 설치 하 고 설정 해야 합니다. 일반적으로 관리자는 게이트웨이를 설치 하 고 설정 하는 사람입니다. 이러한 작업을 수행 하려면 서버 관리자 권한 또는 온-프레미스 서버에 대 한 특별 한 지식이 필요할 수 있습니다.

게이트웨이를 사용 하면 더 빠르고 안전 하 게 백그라운드에서 통신할 수 있습니다. 이 통신은 클라우드의 사용자, 게이트웨이 클라우드 서비스 및 온-프레미스 데이터 원본 간에 전달 됩니다. 게이트웨이 클라우드 서비스는 데이터 원본 자격 증명 및 게이트웨이 세부 정보를 암호화하고 저장합니다. 또한 서비스는 사용자, 게이트웨이 및 온-프레미스 데이터 원본 간에 쿼리 및 해당 결과를 라우팅합니다.

게이트웨이는 방화벽과 함께 작동하며 아웃바운드 연결만 사용합니다. 모든 트래픽은 게이트웨이 에이전트에서 보호 되는 아웃 바운드 트래픽으로 발생 합니다. 게이트웨이는 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)를 통해 암호화 된 채널의 온-프레미스 원본에서 데이터를 릴레이 합니다. 이 Service Bus는 게이트웨이와 호출 서비스 간에 채널을 만들지만 데이터를 저장하지는 않습니다. 게이트웨이를 통해 전송되는 모든 데이터는 암호화됩니다.

![온-프레미스 데이터 게이트웨이에 대 한 아키텍처](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 클라우드 서비스에 따라 게이트웨이에 대 한 데이터 원본을 설정 해야 할 수 있습니다.

이러한 단계는 온-프레미스 데이터 원본에 연결 된 요소와 상호 작용할 때 발생 하는 상황을 설명 합니다.

1. 클라우드 서비스는 데이터 원본에 대해 암호화 된 자격 증명과 함께 쿼리를 만듭니다. 그런 다음 서비스는 처리를 위해 쿼리 및 자격 증명을 게이트웨이 큐에 보냅니다.

1. 게이트웨이 클라우드 서비스에서 쿼리를 분석 하 고 Azure Service Bus 요청을 푸시합니다.

1. Azure Service Bus는 보류 중인 요청을 게이트웨이로 보냅니다.

1. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독 하 고, 해당 자격 증명을 사용 하 여 하나 이상의 데이터 원본에 연결 합니다.

1. 게이트웨이는를 실행 하기 위해 쿼리를 데이터 원본으로 보냅니다.

1. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 게이트웨이 클라우드 서비스로 전송됩니다. 게이트웨이 클라우드 서비스에서 해당 결과를 사용합니다.

### <a name="authentication-to-on-premises-data-sources"></a>온-프레미스 데이터 원본에 대 한 인증

저장 된 자격 증명은 게이트웨이에서 온-프레미스 데이터 원본에 연결 하는 데 사용 됩니다. 사용자에 관계 없이 게이트웨이는 저장 된 자격 증명을 사용 하 여 연결 합니다. Power BI에서 Analysis Services에 대 한 DirectQuery, LiveConnect 등의 특정 서비스에 대 한 인증 예외가 있을 수 있습니다.

### <a name="azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)

Microsoft 클라우드 서비스는 [AZURE AD](../active-directory/fundamentals/active-directory-whatis.md) 를 사용 하 여 사용자를 인증 합니다. Azure AD 테 넌 트에는 사용자 이름 및 보안 그룹이 포함 됩니다. 일반적으로 로그인 하는 데 사용 하는 전자 메일 주소는 계정에 대 한 UPN (사용자 계정 이름)과 동일 합니다.

### <a name="what-is-my-upn"></a>UPN 이란?

도메인 관리자가 아닌 경우 UPN을 모를 수 있습니다. 계정에 대 한 UPN을 찾으려면 워크스테이션에서 `whoami /upn` 명령을 실행 합니다. 결과는 전자 메일 주소와 유사 하지만 결과는 로컬 도메인 계정에 대 한 UPN입니다.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Azure AD와 온-프레미스 Active Directory 동기화

온-프레미스 Active Directory 계정 및 Azure AD 계정에 대 한 UPN은 동일 해야 합니다. 따라서 각 온-프레미스 Active Directory 계정이 Azure AD 계정과 일치 하는지 확인 합니다. 클라우드 서비스는 Azure AD 내의 계정에 대해서만 알고 있습니다. 따라서 온-프레미스 Active Directory에 계정을 추가할 필요가 없습니다. 계정이 Azure AD에 존재 하지 않는 경우 해당 계정을 사용할 수 없습니다.

Azure AD와 온-프레미스 Active Directory 계정을 일치 시킬 수 있는 방법은 다음과 같습니다.

* Azure AD에 계정을 수동으로 추가 합니다.

  Azure Portal 또는 Microsoft 365 관리 센터에 계정을 만듭니다. 계정 이름이 온-프레미스 Active Directory 계정의 UPN과 일치 하는지 확인 합니다.

* Azure Active Directory Connect 도구를 사용 하 여 Azure AD 테 넌 트에 로컬 계정을 동기화 합니다.

  Azure AD Connect 도구는 디렉터리 동기화 및 인증 설정에 대 한 옵션을 제공 합니다. 이러한 옵션에는 암호 해시 동기화, 통과 인증 및 페더레이션이 포함 됩니다. 테 넌 트 관리자 또는 로컬 도메인 관리자가 아닌 경우 IT 관리자에 게 문의 하 여 Azure AD Connect 설정 하세요. Azure AD Connect Azure AD UPN이 로컬 Active Directory UPN과 일치 하는지 확인 합니다. 이러한 일치는 SSO (Power BI 또는 Single Sign-On) 기능을 사용 하 여 Analysis Services 라이브 연결을 사용 하는 경우에 유용 합니다.

  > [!NOTE]
  > 계정을 Azure AD Connect 도구와 동기화 하면 Azure AD 테 넌 트에 새 계정이 만들어집니다.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ 및 문제 해결

자세한 내용은 다음 항목을 참조하세요.

* [온-프레미스 데이터 게이트웨이 FAQ](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [온-프레미스 데이터 게이트웨이 문제 해결](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [게이트웨이 성능 모니터링 및 최적화](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>다음 단계

* [논리 앱에서 온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)
* [엔터프라이즈 통합 기능](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)
