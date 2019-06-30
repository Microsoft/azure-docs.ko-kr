---
title: 고객 Lockbox Microsoft azure
description: Microsoft는 고객 데이터에 액세스 해야 하는 경우 클라우드 공급자 액세스 제어를 제공 하는 Microsoft Azure에 대 한 고객 Lockbox의 기술 개요입니다.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 06/20/2019
ms.openlocfilehash: 0ee2dde5a941d069f5b745eafb35df780f657a47
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312630"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>고객 Lockbox Microsoft azure

> [!NOTE]
> 이 기능을 사용 하려면 조직에 있어야 합니다는 [Azure 지원 플랜이](https://azure.microsoft.com/support/plans/) 최소 수준의 **개발자**합니다.

Microsoft Azure에 대 한 고객 Lockbox 검토 및 승인 하거나 고객 데이터 액세스 요청을 거부 하는 고객에 대 한 인터페이스를 제공 합니다. Microsoft 엔지니어 로부터 지원 요청을 하는 동안 고객 데이터에 액세스 해야 하는 경우에 사용 됩니다.

이 문서에서는 고객 Lockbox 요청은 시작, 추적 및 이상 검토 및 감사에 대 한 저장 하는 방법에 대해 설명 합니다.

고객 Lockbox 일반적으로 사용 가능 하 고 가상 컴퓨터에 원격 데스크톱 액세스에 대 한 현재 사용 하도록 설정 되었습니다.

## <a name="workflow"></a>워크플로

다음 단계를 고객 Lockbox 요청에 대 한 일반적인 워크플로 간략하게 설명합니다.

1. 조직에서 사용자가 Azure 워크 로드를 사용 하 여 문제입니다.

2. 지원 티켓을 열이 사용자가 문제를 해결 합니다. 하지만 해결할 수 없는 후 합니다 [Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)합니다. 티켓을 Azure 고객 지원 엔지니어에 게 할당 됩니다.

3. Azure 지원 엔지니어는 서비스 요청을 검토 하 고 문제를 해결 하려면 다음 단계를 결정 합니다.

4. 지원 엔지니어에 게는 표준 도구 및 원격 분석을 사용 하 여 문제를 해결 없습니다, 하는 경우 다음 단계는 jit (JUST-IN-TIME) 액세스 서비스를 사용 하 여 권한 요청입니다. 이 요청에서 원래 지원 엔지니어에 게 될 수 있습니다. 또는 문제가 Azure DevOps 팀으로 에스컬레이션 된 있으므로 다른 엔지니어를 수 있습니다.

5. Just In Time Azure 엔지니어가 요청이 전송 되는 액세스 한 후 서비스와 같은 요인을 고려 수행 요청을 평가 합니다.
    - 리소스의 범위
    - 격리 된 id 또는 multi-factor authentication 인증을 사용 하 여 요청자에 게 표시 되는 인지
    - 사용 권한 수준
    
    JIT 규칙에 따라이 요청 내부 Microsoft 승인자의 승인을 포함할 수도 있습니다. 예를 들어 잠재 고객의 고객 지원 또는 DevOps Manager 승인자 수 있습니다.

6. 고객 데이터에 직접 액세스 해야 하는 요청을 하면 고객 Lockbox 요청 시작 됩니다. 예를 들어 원격 데스크톱 액세스를 고객의 가상 컴퓨터.
    
    요청 중인 이제는 **고객 알림을** 액세스 권한을 부여 하기 전에 고객 승인 대기 상태입니다.

7. 사용자에 게 고객 조직에는 [소유자 역할](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) azure 구독 보류 중인 액세스 요청에 대 한 알림을 받도록 Microsoft에서 전자 메일을 받게 됩니다. 고객 Lockbox 요청에 대 한이 사용자 지정 된 승인자가 있습니다.
    
    전자 메일 예:
    
    ![Azure 고객 Lockbox-전자 메일 알림](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. 전자 메일 알림의 링크를 제공 합니다 **고객 Lockbox** 블레이드에서 Azure portal에서 합니다. 이 링크를 사용 하 여 지정 된 승인자 Azure 포털로 로그인 보류 중인 요청이 고객 Lockbox에 대 한 조직에 있는 보기:
    
    ![Azure 고객 Lockbox-방문 페이지](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   요청 4 일에 대 한 고객 큐에 남아 있습니다. 이 시간 이후에 액세스 요청을 자동으로 만료 및 Microsoft 엔지니어에 게 허가 된 액세스가 없습니다.

9. 지정 된 승인자 수에서 lockbox 요청을 선택 하는 데 보류 중인 요청의 세부 정보를 가져오려면 **보류 중인 요청**:
    
    ![Azure 고객 Lockbox-보류 중인 요청 보기](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. 지정 된 승인자를 선택할 수도 있습니다는 **서비스 요청 ID** 원래 사용자가 만든 지원 티켓 요청을 볼 수 있습니다. 이 정보는 Microsoft 지원에 참여 하는 이유는 무엇에 대 한 컨텍스트 및 기록을 보고 된 문제를 제공 합니다. 예를 들면 다음과 같습니다.
    
    ![Azure 고객 Lockbox-지원 티켓 요청 보기](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. 지정 된 승인자가 요청을 검토 한 후 다음을 선택 합니다. **승인** 하거나 **거부**:
    
    ![Azure 고객 Lockbox-선택 승인 또는 거부](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    결과로 선택:
    - **승인**:  액세스는 Microsoft 엔지니어에 게 부여 됩니다. 8 시간의 기본 기간에 대 한 액세스를 부여 됩니다.
    - **거부**: Microsoft 엔지니어가 액세스 권한 요청은 거부 됩니다 및 아무런 추가 동작이 발생 합니다.

감사를 목적으로,이 워크플로에서 수행 된 작업에 기록 됩니다 [고객 Lockbox 요청 로그](#auditing-logs)합니다.

## <a name="auditing-logs"></a>감사 로그

고객 Lockbox 로그는 활동 로그에 저장 됩니다. Azure portal에서 선택 **활동 로그** 고객 Lockbox 요청에 관련 된 감사 정보를 볼 수 있습니다. 와 같은 특정 작업에 대 한 필터링 할 수 있습니다.
- **Lockbox 요청 거부**
- **Lockbox 요청 만들기**
- **Lockbox 요청 승인**
- **Lockbox 요청 만료**

예를 들어

![Azure 고객 Lockbox-활동 로그](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>지원 되는 서비스 및 시나리오

다음 서비스 및 시나리오는 현재 일반 공급 고객 Lockbox에 대 한 합니다.

### <a name="remote-desktop-access-to-virtual-machines"></a>Virtual machines에 원격 데스크톱 액세스

고객 Lockbox 가상 컴퓨터에 원격 데스크톱 액세스 요청에 대 한 사용 중입니다. 다음 워크 로드가 지원 됩니다.
- Platform-as-a-service (PaaS)-Azure Cloud Services (웹 역할 및 작업자 역할)
- 인프라 서비스 (IaaS)-Windows 및 Linux (Azure Resource Manager에만 해당)
- 가상 머신 확장 집합-Windows 및 Linux

> [!NOTE]
> 고객 Lockbox에서 IaaS 클래식 인스턴스가 지원 되지 않습니다. IaaS 클래식 인스턴스에서 실행 중인 워크 로드에 있는 경우로 마이그레이션하려면 클래식에서 Resource Manager 배포 모델에는 것이 좋습니다. 자세한 내용은 [Azure Resource Manager로 클래식에서 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-overview.md)합니다.

#### <a name="detailed-audit-logs"></a>자세한 감사 로그

원격 데스크톱 액세스를 포함 하는 시나리오에 대 한 Microsoft 엔지니어에서 수행한 작업을 검토 하려면 Windows 이벤트 로그를 사용할 수 있습니다. Azure Security Center를 사용 하 여 이벤트 로그를 수집 및 분석을 위해 작업 영역에 데이터를 복사 하는 것이 좋습니다. 자세한 내용은 [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md)합니다.

## <a name="exclusions"></a>제외

고객 Lockbox 요청 되지 같은 엔지니어링 지원 시나리오에서 트리거됩니다.

- Microsoft 엔지니어를 표준 운영 절차를 벗어난 작업을 수행 해야 합니다. 예를 들어, 복구 또는 예기치 않은 또는 예기치 않은 시나리오에서 서비스를 복원 합니다.

- Microsoft 엔지니어는 문제 해결의 일부로 Azure 플랫폼을 액세스 하 고 실수로 고객 데이터에 액세스할 수 있습니다. 예를 들어 Azure 네트워크 팀 네트워크 장치에서 패킷 캡처를 초래 하는 문제 해결을 수행 합니다. 그러나 전송 하는 동안 데이터를 암호화 하는 고객, 엔지니어가 데이터를 읽을 수 없습니다.

## <a name="next-steps"></a>다음 단계

고객 Lockbox 자동으로 사용할 수 있는 모든 고객은는 [Azure 지원 플랜이](https://azure.microsoft.com/support/plans/) 최소 수준의 **개발자**합니다.

적합 한 지원 계획을 경우 아무 작업도 고객 Lockbox를 사용 하도록 설정 하려면 사용자가 필요 합니다. 고객 Lockbox 요청은이 작업에서 조직의 누군가가 필드는 지원 티켓을 진행 해야 하는 경우 Microsoft 엔지니어에 의해 시작 됩니다.
