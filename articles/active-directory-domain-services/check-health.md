---
title: Azure Active Directory Domain Services 상태 확인 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인의 상태를 확인 하 고 상태 메시지를 이해 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: f0719542eb693e52f9a7996e28699b7425b0e0fe
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509141"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리 되는 도메인의 상태를 확인 합니다.

Azure Active Directory Domain Services (Azure AD DS)는 관리 되는 도메인을 최신 상태로 유지 하기 위해 몇 가지 백그라운드 작업을 실행 합니다. 이러한 작업에는 백업 수행, 보안 업데이트 적용 및 Azure AD의 데이터 동기화가 포함 됩니다. Azure AD DS 관리 되는 도메인에 문제가 있는 경우 이러한 작업이 성공적으로 완료 되지 않을 수 있습니다. 문제를 검토 하 고 해결 하려면 Azure Portal를 사용 하 여 Azure AD DS 관리 되는 도메인의 상태를 확인할 수 있습니다.

이 문서에서는 Azure AD DS 상태를 확인 하 고 표시 되는 정보 또는 경고를 이해 하는 방법을 보여 줍니다.

## <a name="view-the-health-status"></a>상태 보기

Azure Portal를 사용 하 여 Azure AD DS 관리 되는 도메인의 상태를 볼 수 있습니다. 마지막 백업 시간 및 Azure AD와의 동기화에 대한 정보는 관리 되는 도메인의 상태 문제를 나타내는 경고와 함께 볼 수 있습니다. Azure AD DS 관리 되는 도메인의 상태를 확인 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색 하 고 선택 합니다.
1. Azure AD DS 관리 되는 도메인 (예: *aadds.contoso.com*)을 선택 합니다.
1. Azure AD DS 리소스 창의 왼쪽에서 **상태**를 선택 합니다. 다음 예제 스크린샷에서는 정상적인 Azure AD DS 관리 되는 도메인과 마지막 백업 및 Azure AD 동기화의 상태를 보여 줍니다.

    ![상태 페이지 개요 Azure Active Directory Domain Services 상태를 보여 주는 Azure Portal](./media/check-health/health-page.png)

상태 페이지의 *마지막으로 평가* 된 타임 스탬프는 Azure AD DS 관리 되는 도메인을 마지막으로 확인 한 시간을 표시 합니다. Azure AD DS 관리 되는 도메인의 상태는 매시간 평가 됩니다. Azure AD DS 관리 되는 도메인을 변경 하는 경우 업데이트 된 상태를 볼 수 있도록 다음 평가 주기까지 기다립니다.

오른쪽 위의 상태는 Azure AD DS 관리 되는 도메인의 전반적인 상태를 나타냅니다. 상태는 도메인의 모든 기존 경고에 대한 요소입니다. 다음 표에서는 사용 가능한 상태 표시기에 대해 자세히 설명 합니다.

| 상태 | 아이콘 | 설명 |
| --- | :----: | --- |
| 실행 중 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS 관리 되는 도메인이 올바르게 실행 중 이며 위험 또는 경고 경고가 없습니다. 도메인에 정보 알림이 있을 수 있습니다. |
| 주의가 필요 함 (경고) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS 관리 되는 도메인에 중요 한 경고가 없지만 해결 해야 하는 경고 경고가 하나 이상 있습니다. |
| 주의가 필요 함 (위험) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Azure AD DS 관리 되는 도메인에 주소가 지정 되어야 하는 중요 한 경고가 하나 이상 있습니다. 경고 및/또는 정보 제공 알림이 있을 수도 있습니다. |
| 배포 중 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Azure AD DS 도메인을 배포 하 고 있습니다. |

## <a name="understand-monitors-and-alerts"></a>모니터 및 경고 이해

Azure AD DS 관리 되는 도메인의 상태에는 두 가지 유형의 정보 *모니터*와 *경고가*표시 됩니다. 모니터는 코어 백그라운드 작업이 완료 된 시간을 표시 합니다. 경고는 관리 되는 도메인의 안정성을 향상 시킬 수 있는 정보나 제안 사항을 제공 합니다.

### <a name="monitors"></a>모니터

모니터는 정기적으로 확인 되는 Azure AD DS 관리 되는 도메인의 영역입니다. Azure AD DS 관리 되는 도메인에 대한 활성 경고가 있는 경우이로 인해 모니터 중 하나가 문제를 보고할 수 있습니다. 현재 Azure AD Domain Services에는 다음 영역에 대한 모니터가 있습니다.

* Backup
* Azure AD와 동기화

#### <a name="backup-monitor"></a>백업 모니터

백업 모니터는 Azure AD DS 관리 되는 도메인의 자동화 된 정기 백업이 성공적으로 실행 되었는지 확인 합니다. 다음 표에서는 사용 가능한 백업 모니터 상태를 자세히 설명 합니다.

| 세부 정보 값 | 설명 |
| --- | --- |
| 백업 안 함 | 이 상태는 새 Azure AD DS 관리 되는 도메인에 대해 정상입니다. 첫 번째 백업은 Azure AD DS 관리 되는 도메인이 배포 된 후 24 시간 후에 생성 됩니다. 이 상태가 지속 되 면 [Azure 지원 요청을 여세요][azure-support]. |
| 1-14일 전에 마지막 백업 수행 | 이 시간 범위는 백업 모니터의 예상 상태입니다. 자동 정기 백업은이 기간에 발생 해야 합니다. |
| 마지막 백업이 수행된 후 14일이 넘었습니다. | Timespan이 2 주 보다 길면 자동화 된 정기 백업에 문제가 있음을 나타냅니다. 중요 한 경고가 발생 하면 Azure AD DS 관리 되는 도메인이 백업 되지 않을 수 있습니다. Azure AD DS 관리 되는 도메인에 대한 활성 경고를 해결 합니다. 백업 모니터가 최근 백업을 보고 하기 위해 상태를 업데이트 하지 않는 경우 [Azure 지원 요청을 엽니다][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Azure AD monitor와 동기화

Azure AD DS 관리 되는 도메인은 Azure Active Directory와 정기적으로 동기화 됩니다. 사용자 및 그룹 개체의 수와 마지막 동기화 이후 Azure AD 디렉터리에서 변경 된 횟수는 동기화 하는 데 걸리는 시간에 영향을 줍니다. Azure AD DS 관리 되는 도메인이 3 일 전에 마지막으로 동기화 된 경우 모든 활성 경고를 확인 하 고 해결 합니다. 활성 경고를 해결 한 후 동기화 모니터가 상태를 업데이트 하 여 최근 동기화가 표시 되지 않으면 [Azure 지원 요청을 엽니다][azure-support].

### <a name="alerts"></a>경고

Azure AD DS 관리 되는 도메인에서 서비스가 올바르게 실행 될 수 있도록 주소가 지정 되어야 하는 문제에 대한 경고가 생성 됩니다. 각 경고는 문제를 설명 하 고 문제 해결을 위한 특정 단계를 설명 하는 URL을 제공 합니다. 가능한 경고 및 해결 방법에 대한 자세한 내용은 [경고 문제 해결](troubleshoot-alerts.md)을 참조 하세요.

상태 경고는 다음과 같은 심각도 수준으로 분류 됩니다.

 * **중요 한 알림은** Azure AD DS 관리 되는 도메인에 심각한 영향을 주는 문제입니다. 이러한 경고는 즉시 해결 해야 합니다. Azure 플랫폼은 문제가 해결 될 때까지 관리 되는 도메인을 모니터링, 관리, 패치 및 동기화 할 수 없습니다.
 * **경고** 경고는 문제가 지속 되는 경우 Azure AD DS 관리 도메인 작업에 영향을 줄 수 있는 문제를 알려 줍니다. 이러한 경고는 관리 되는 도메인을 보호 하기 위한 권장 사항도 제공 합니다.
 * **정보 알림은** Azure AD DS 관리 되는 도메인에 부정적인 영향을 주지 않는 알림입니다. 정보 알림은 관리 되는 도메인에서 발생 하는 상황에 대한 통찰력을 제공 합니다.

## <a name="next-steps"></a>다음 단계

상태 페이지에 표시 되는 경고에 대한 자세한 내용은 [관리 되는 도메인의 경고 해결][troubleshoot-alerts] 을 참조 하세요.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
