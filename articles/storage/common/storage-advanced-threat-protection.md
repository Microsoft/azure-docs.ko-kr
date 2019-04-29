---
title: Advanced Threat Protection for Azure Storage
description: 계정 작업에서 잘못된 부분을 검색하여 잠재적으로 해로운 계정 액세스 시도에 대해 알리도록 Azure Storage Advanced Threat Protection을 구성합니다.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: monhaber
ms.manager: shaik
ms.openlocfilehash: 78338ece1bc70d8410bd71183a34aaf1a52f2d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484223"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection for Azure Storage

Azure Storage를 위한 Advanced Threat Protection은 스토리지 계정에 대한 비정상적이며 잠재적으로 유해한 액세스 또는 악용 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 레이어를 사용하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결 수 있습니다. 

비정상 활동에서 발생 하는 경우 보안 경고가 트리거됩니다.  이러한 보안 경고와 통합 되어 [Azure Security Center](https://azure.microsoft.com/services/security-center/), 의심 스러운 활동을 조사 및 위협을 해결 하는 방법에 대 한 권장 사항 세부 정보를 사용 하 여 구독 관리자에 게 전자 메일을 통해도 전송 됩니다.

> [!NOTE]
> * Azure Storage에 대 한 advanced Threat Protection은 현재 Blob 저장소에 대해서만 사용할 수 있습니다.
> * 세부 정보를 30 일 무료 평가판을 비롯 한 가격 책정에 대 한 참조를 [Azure Security Center 가격 책정 페이지]( https://azure.microsoft.com/en-us/pricing/details/security-center/)합니다.
> * Azure storage 기능에 대 한 ATP는 현재 소 버린 클라우드 지역 및 Azure government에서 사용할 수 없습니다.

Azure Storage에 대 한 advanced Threat Protection 읽기, 쓰기 및 삭제 요청 위협 요소 탐지에 대 한 Blob storage로의 진단 로그를 수집합니다. Advanced Threat Protection에서 경고를 조사 하려면 저장소 분석 로깅을 사용 하 여 관련된 저장소 작업을 볼 수 있습니다. 자세한 내용은 참조 하는 방법 [저장소 분석 로깅을 구성](storage-monitor-storage-account.md#configure-logging)합니다.

## <a name="set-up-advanced-threat-protection"></a>Advanced Threat Protection 설정 

### <a name="using-the-portal"></a>포털 사용

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal을 시작합니다.

2. 보호할 Azure Storage 계정의 구성 페이지로 이동합니다. **설정** 페이지에서 **Advanced Threat Protection**을 선택합니다.

3. **Advanced Threat Protection** 구성 블레이드에서 다음을 수행합니다.
    * Advanced *Threat Protection*을 **켭니다**.
    * **저장**을 클릭하여 새로운 또는 업데이트된 Advanced Threat Protection 정책을 저장합니다. (이미지의 가격은 예를 들어 전용입니다.)

![Azure Storage Advanced Threat Protection 켜기](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Azure Security Center 사용
표준 계층을 구독 하는 경우 Azure Security Center에서 Advanced Threat Protection 저장소 계정에 설정 되어 있습니다. 자세한 내용은 참조 [보안 강화를 위해 Security Center의 표준 계층으로 업그레이드](https://docs.microsoft.com/azure/security-center/security-center-pricing)합니다. (이미지의 가격은 예를 들어 전용입니다.)

![ASC에서 표준 계층](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

Advanced Threat Protection을 사용 하 여 Azure Storage 계정을 배포 하는 Azure Resource Manager 템플릿을 사용 하 여 사용 하도록 설정 합니다.
자세한 내용은 [Advanced Threat Protection을 사용 하 여 저장소 계정](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)합니다.

### <a name="using-azure-policy"></a>Azure Policy를 사용 하 여

Azure 정책을 사용 하 여 특정 구독 또는 리소스 그룹에서 저장소 계정에서 Advanced Threat Protection을 사용 하도록 설정 합니다.

1. Azure 점심 **정책-정의** 페이지입니다.

1. 검색 된 **저장소 계정에서 Advanced Threat Protection 배포** 정책입니다.

     ![검색 정책](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Azure 구독 또는 리소스 그룹을 선택 합니다.

    ![구독 또는 그룹 선택](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 정책을 할당 합니다.

    ![정책 정의 페이지](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>REST API 사용
Rest API 명령을 사용 하 여 만들기, 업데이트 또는 특정 저장소 계정에 대 한 Advanced Threat Protection 설정을 가져옵니다.

* [Advanced Threat Protection - Create](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection - Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

다음 PowerShell cmdlet을 사용 합니다.

  * [Advanced Threat Protection을 사용 하도록 설정](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Advanced Threat Protection 가져오기](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Advanced Threat Protection을 사용 하지 않도록 설정](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>보안 문제를 탐색 합니다.

저장소 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

* 변칙의 특성
* 스토리지 계정 이름입니다.
* 이벤트 시간
* 저장소 유형
* 잠재적 원인 
* 조사 단계
* 수정 단계


또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Azure Security Center의 [보안 경고 타일](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)에서 현재 보안 경고를 검토 및 관리할 수 있습니다. 특정 경고를 클릭하면 현재 위협을 조사하고 향후 위협을 해결하기 위한 추가 세부 정보 및 조치가 제공됩니다.

![Azure Storage Advanced Threat Protection 경고 이메일](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>보호 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 저장소 계정 악용에 의해 생성됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

### <a name="anomalous-access-pattern-alerts"></a>비정상적인 액세스 패턴 경고

* **비정상적인 위치에서 액세스**: 저장소 계정에 대 한 액세스 패턴이 변경 될 때이 경고가 트리거됩니다. 사용자가 평소와 다른 지리적 위치에서 저장소 계정에 액세스하는 경우를 예로 들 수 있습니다.
가능한 원인:
   * 공격자가 저장소 계정 액세스
   * 합법적인 사용자가 새 위치에서 저장소 계정에 액세스
 
* **응용 프로그램 변칙**: 이 경고는 비정상적인 응용 프로그램에이 저장소 계정 액세스를 나타냅니다. 가능한 원인:
   * 공격자가 새 응용 프로그램을 사용 하 여 저장소 계정에 액세스 합니다.
   * 합법적인 사용자 하는 데 새 응용 프로그램/브라우저 저장소 계정에 액세스 합니다.

* **익명 액세스**: 이 경고는 저장소 계정에 대 한 액세스 패턴이 변경 중임을 나타냅니다. 이 계정이 되었습니다. 예를 들어,이 계정에 최근 액세스 패턴에 비해 예상 되지 않는 (즉, 모든 인증 없이) 익명으로 액세스 합니다.
가능한 원인:
   * 공격자는 컨테이너에 대 한 공용 읽기 액세스를 악용에 있습니다.
   * 올바른 사용자 또는 응용 프로그램을 컨테이너에 대 한 공용 읽기 액세스를 사용 했습니다.

### <a name="anomalous-extractupload-alerts"></a>비정상적인 추출/업로드 경고

* **데이터 반출**: 이 경고는이 저장소 컨테이너에서 최근 작업에 비해 비정상적으로 많은 양의 데이터를 추출 된 나타냅니다. 가능한 원인:
   * 공격자가 컨테이너에서 많은 양의 데이터를 추출 하 고. (예: 데이터 반출/위반, 데이터의 무단된 전송)
   * 합법적인 사용자 또는 응용 프로그램을 컨테이너에서 비정상적인 양의 데이터를 추출 했습니다. (예: 유지 관리 작업)

* **예기치 않은 삭제**: 이 경고는이 계정에 최근 작업과 비교 하 여 저장소 계정에서 예기치 않은 삭제 작업을 하나 이상이 발생 했음을 나타냅니다. 가능한 원인:
   * 공격자는 저장소 계정에서 데이터를 삭제 했습니다.
   * 합법적인 사용자는 비정상적인 삭제를 수행 했습니다.

* **Azure 클라우드 서비스 패키지 업로드**: 이 경고는 Azure 클라우드 서비스 패키지 (.cspkg 파일)이이 계정에 대 한 최근 활동에 비해 비정상적인 방식으로 저장소 계정에 업로드 된 있는지를 나타냅니다. 가능한 원인: 
   * 공격자가 악성 코드 저장소 계정에서 Azure 클라우드 서비스를 배포 준비 된에.
   * 합법적인 사용자에 합법적인 서비스 배포를 준비 되었습니다.

### <a name="suspicious-storage-activities-alerts"></a>저장소 의심 스러운 활동 경고

* **사용 권한 변경 액세스**: 이 경고를 비정상적인 방식으로이 저장소 컨테이너의 액세스 권한을 변경 된 것을 나타냅니다. 가능한 원인: 
   * 공격자는 해당 보안을 약화 컨테이너 권한을 변경 되었습니다.
   * 합법적인 사용자가 컨테이너 권한을 변경 했습니다.

* **액세스 검사**: 이 경고는 저장소 계정의 액세스 권한을이 계정에 대 한 최근 활동에 비해 비정상적인 방식으로 확인 된 것을 나타냅니다. 가능한 원인: 
   * 공격자는 향후 공격에 대 한 정찰을 수행 했습니다.
   * 합법적인 사용자 저장소 계정에 유지 관리를 수행한.

* **데이터 탐색**: 이 경고는 blob 또는 컨테이너를 저장소 계정에이 계정에 대 한 최근 활동에 비해 비정상적인 방식으로 열거를 나타냅니다. 가능한 원인: 
   * 공격자는 향후 공격에 대 한 정찰을 수행 했습니다.
   * 합법적인 사용자 또는 응용 프로그램 논리를 저장소 계정 내에서 데이터 탐색 했습니다.






## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [Azure Storage 계정에 로그인](/rest/api/storageservices/About-Storage-Analytics-Logging)

* [Azure Security Center](../../security-center/security-center-intro.md)에 대한 자세한 정보
