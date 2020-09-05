---
title: Azure Security Center에 대한 릴리스 정보
description: Azure Security Center의 새로운 기능과 변경된 기능에 대한 설명입니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: 0c0e286ac9f94768541bb40b9ccca01e0469e0c8
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177247"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능

Azure 보안은 현재 개발 중이며 지속적으로 향상된 기능을 수용합니다. 최신 개발 정보를 확인할 수 있도록 이 페이지에서는 다음과 같은 정보를 제공합니다.

- 새로운 기능
- 버그 수정
- 사용되지 않는 기능

이 페이지는 정기적으로 업데이트되므로 자주 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Security Center의 새로운 기능 아카이브](release-notes-archive.md)에서 찾을 수 있습니다.


## <a name="august-2020"></a>2020년 8월

8 월의 업데이트는 다음과 같습니다.

- [자산 인벤토리-자산의 보안 상태에 대 한 강력한 새 뷰입니다.](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Azure Active Directory 보안 기본값 (multi-factor authentication의 경우)에 대 한 지원이 추가 됨](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [추가 된 서비스 주체 권장 사항](#service-principals-recommendation-added)
- [Vm에 대 한 취약성 평가-통합 권장 사항 및 정책 통합](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [ASC_default 이니셔티브에 추가 된 새 AKS 보안 정책-비공개 미리 보기 고객만 사용 하기 위한 것입니다.](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>자산 인벤토리-자산의 보안 상태에 대 한 강력한 새 뷰입니다.

Security Center의 자산 인벤토리 (현재 미리 보기 상태)는 Security Center에 연결한 리소스의 보안 상태를 확인 하는 방법을 제공 합니다.

Security Center는 Azure 리소스의 보안 상태를 주기적으로 분석 하 여 잠재적인 보안 취약성을 식별 합니다. 그런 다음 이러한 취약성을 해결 하는 방법에 대 한 권장 사항을 제공 합니다. 리소스에 해결 되지 않은 권장 사항이 있는 경우 인벤토리에 표시 됩니다.

보기와 해당 필터를 사용 하 여 보안 상태 데이터를 탐색 하 고 검색 결과에 따라 추가 작업을 수행할 수 있습니다.

[자산 인벤토리에](asset-inventory.md)대해 자세히 알아보세요.


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Azure Active Directory 보안 기본값 (multi-factor authentication의 경우)에 대 한 지원이 추가 됨

Security Center는 Microsoft의 무료 id 보안 보호를 위해 [보안 기본값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)에 대 한 완전 한 지원을 추가 했습니다.

보안 기본값은 일반적인 id 관련 공격 으로부터 조직을 보호 하기 위해 미리 구성 된 id 보안 설정을 제공 합니다. 보안 기본값은 이미 500만 개 이상의 테 넌 트를 보호 합니다. 5만 테 넌 트는 Security Center에 의해 보호 됩니다.

이제 Security Center는 보안 기본값을 사용 하지 않고 Azure 구독을 식별할 때마다 보안 권장 사항을 제공 합니다. 지금 까지는 Azure Active Directory (AD) premium 라이선스의 일부인 조건부 액세스를 사용 하 여 multi-factor authentication을 사용 하도록 권장 Security Center. Azure AD free를 사용 하는 고객의 경우 이제 보안 기본값을 사용 하는 것이 좋습니다. 

Microsoft의 목표는 더 많은 고객이 MFA를 사용 하 여 클라우드 환경을 보호 하 고 [보안 점수](https://docs.microsoft.com/azure/security-center/secure-score-security-controls)를 가장 많이 사용 하는 위험 중 하나를 완화 하는 것입니다.

[보안 기본값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)에 대해 자세히 알아보세요.


### <a name="service-principals-recommendation-added"></a>추가 된 서비스 주체 권장 사항

관리 인증서를 사용 하 여 서비스 주체에 대 한 구독을 관리 하는 고객이 관리 인증서를 사용 하는 Security Center 권장 하는 새로운 권장 사항이 추가 되었습니다.

권장 사항으로, 관리 인증서 대신 서비스 주체를 사용 하 여 구독을 **보호 하는 데 서비스** 사용자 또는 Azure Resource Manager을 사용 하 여 구독을 보다 안전 하 게 관리 하는 것이 좋습니다. 

[Azure Active Directory에서 응용 프로그램 및 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)에 대해 자세히 알아보세요.


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Vm에 대 한 취약성 평가-통합 권장 사항 및 정책 통합

Security Center Vm을 검사 하 여 취약성 평가 솔루션을 실행 중인지 여부를 검색 합니다. 취약점 평가 솔루션을 찾을 수 없는 경우 Security Center 배포를 간소화 하기 위한 권장 사항을 제공 합니다.

취약점이 발견 되 면 Security Center는 필요한 경우 조사 하 고 수정할 수 있는 결과를 요약 하는 권장 사항을 제공 합니다.

사용 중인 스캐너 유형에 관계 없이 모든 사용자에 게 일관 된 환경을 보장 하기 위해 다음 두 가지 권장 사항을 통합 합니다.

|통합 권장 사항|변경 내용 설명|
|----|:----|
|**가상 머신에서 취약점 평가 솔루션을 사용 하도록 설정 해야 합니다.**|는 다음과 같은 두 가지 권장 사항을 대체 합니다.<br> **•** 가상 머신에서 기본 제공 취약점 평가 솔루션을 사용 하도록 설정 (Qualys (현재 사용 되지 않음) (표준 계층에 포함 됨)<br> **•** 취약성 평가 솔루션을 가상 머신에 설치 해야 합니다 (현재 사용 되지 않음) (표준 및 무료 계층).|
|**가상 컴퓨터의 취약성을 재구성 해야 함**|는 다음과 같은 두 가지 권장 사항을 대체 합니다.<br>**•** 가상 머신에서 발견 된 취약점 해결 (Qualys) (현재는 사용 되지 않음)<br>**•** 취약성 평가 솔루션 (현재는 사용 되지 않음)으로 취약성을 재구성 해야 합니다.|
|||

이제 동일한 권장 사항을 사용 하 여 Qualys 또는 Rapid7와 같은 파트너 로부터 Security Center의 취약성 평가 확장 또는 개인적으로 사용이 허가 된 솔루션 ("BYOL")을 배포 합니다.

또한 취약점을 발견 하 고 Security Center에 보고 하는 경우 한 가지 권장 사항은 식별 된 취약점 평가 솔루션에 관계 없이 결과를 알려 주는 것입니다.

#### <a name="updating-dependencies"></a>종속성 업데이트

이전 권장 구성 또는 정책 키/이름을 참조 하는 스크립트, 쿼리 또는 자동화가 있는 경우 아래 표를 사용 하 여 참조를 업데이트 합니다.

##### <a name="before-august-2020"></a>8 월 2020 일 전

|권장|범위|
|----|:----|
|**가상 머신에서 기본 제공 취약점 평가 솔루션을 사용 하도록 설정 (Qualys 기반)**<br>키: 550e890b-e652-4d22-8274-60b3bdb24c63|기본 제공|
|**가상 머신에서 발견한 취약성 수정(Qualys 제공)**<br>키: 1195afff-c881-495e-9bc5-1486211ae03f|기본 제공|
|**가상 머신에 취약성 평가 솔루션을 설치해야 함**<br>키: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**취약성 평가 솔루션으로 취약성을 수정해야 합니다.**<br>키: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|정책|범위|
|----|:----|
|**가상 머신에서 취약성 평가를 사용하도록 설정해야 함**<br>정책 ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|기본 제공|
|**취약성 평가 솔루션에서 취약성을 재구성 해야 함**<br>정책 ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>8 월 2020부터

|권장|범위|
|----|:----|
|**가상 머신에서 취약점 평가 솔루션을 사용 하도록 설정 해야 합니다.**<br>키: ffff0522-1e88-47fc-8382-2a80ba848f5d|기본 제공 + BYOL|
|**가상 컴퓨터의 취약성을 재구성 해야 함**<br>키: 1195afff-c881-495e-9bc5-1486211ae03f|기본 제공 + BYOL|
||||

|정책|범위|
|----|:----|
|[**가상 컴퓨터에서 취약성 평가를 사용 하도록 설정 해야 합니다.**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>정책 ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |기본 제공 + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>ASC_default 이니셔티브에 추가 된 새 AKS 보안 정책-비공개 미리 보기 고객만 사용 하기 위한 것입니다.

Kubernetes 작업은 Security Center 기본적으로 보안을 유지 하기 위해 Kubernetes 수준 정책을 추가 하 고 Kubernetes 허용 제어를 사용 하는 적용 옵션을 비롯 한 권장 사항을 강화 합니다.

이 프로젝트의 초기 단계에는 비공개 미리 보기와 ASC_default 이니셔티브에 대 한 새로운 (기본적으로 사용 하지 않도록 설정 된) 정책이 포함 됩니다.

이러한 정책을 무시 해도 되지만 사용자 환경에는 영향을 주지 않습니다. 사용 하도록 설정 하려면에서 미리 보기에 등록 https://aka.ms/SecurityPrP 하 고 다음 옵션 중에서 선택 합니다.

1. **단일 미리 보기** –이 비공개 미리 보기만 조인 합니다. "ASC 연속 검사"를 조인 하려는 미리 보기로 명시적으로 언급 합니다.
1. **진행 중인 프로그램** –이 및 향후 비공개 미리 보기에 추가 됩니다. 프로필 및 개인 정보 계약을 완료 해야 합니다.


## <a name="july-2020"></a>2020년 7월

7 월의 업데이트는 다음과 같습니다.
- [이제 가상 머신에 대 한 취약성 평가를 비 마켓플레이스 이미지에 사용할 수 있습니다.](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure Files 및 Azure Data Lake Storage Gen2 (미리 보기)를 포함 하도록 위협 방지 Azure Storage 확장 됨](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [위협 방지 기능을 사용 하도록 설정 하는 8 가지 새로운 권장 사항](#eight-new-recommendations-to-enable-threat-protection-features)
- [컨테이너 보안 개선-더 빠른 레지스트리 검색 및 새로 고침 설명서](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [적응 응용 프로그램 컨트롤이 새로운 권장 사항 및 경로 규칙의 와일드 카드 지원으로 업데이트 됨](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [SQL 고급 데이터 보안에 대 한 6 가지 정책 사용 되지 않음](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>이제 가상 머신에 대 한 취약성 평가를 비 마켓플레이스 이미지에 사용할 수 있습니다.

취약성 평가 솔루션을 배포할 때 이전에는를 배포 하기 전에 유효성 검사를 수행 Security Center 합니다. 확인은 대상 가상 머신의 marketplace SKU를 확인 하는 것입니다. 

이 업데이트에서 확인이 제거 되었으며 이제 ' 사용자 지정 ' Windows 및 Linux 컴퓨터에 취약성 평가 도구를 배포할 수 있습니다. 사용자 지정 이미지는 marketplace 기본값에서 수정한 이미지입니다.

이제 더 많은 컴퓨터에서 통합 취약성 평가 확장 (Qualys 기반)을 배포할 수 있지만, [표준 계층 vm에 통합 취약성 스캐너 배포](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-standard-tier-vms) 에 나열 된 OS를 사용 하는 경우에만 지원을 사용할 수 있습니다.

[가상 컴퓨터에 대 한 통합 취약성 스캐너](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)에 대해 자세히 알아보세요 (표준 계층에만 해당).

Qualys 또는 Rapid7에서 개인적으로 사용이 허가 된 취약점 평가 솔루션을 사용 하는 방법에 대 한 자세한 내용은 [파트너 취약점 검사 솔루션 배포](deploy-vulnerability-assessment-vm.md)를


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure Files 및 Azure Data Lake Storage Gen2 (미리 보기)를 포함 하도록 위협 방지 Azure Storage 확장 됨

Azure Storage에 대 한 위협 방지는 Azure Storage 계정에서 잠재적으로 유해한 작업을 검색 합니다. Security Center는 저장소 계정에 대 한 액세스 또는 악용 시도를 감지한 경우 경고를 표시 합니다. 

Blob 컨테이너, 파일 공유 또는 데이터 레이크 저장 되어 있는지에 관계 없이 데이터를 보호할 수 있습니다. 

[Azure Storage에 대 한 위협 방지](threat-protection.md#threat-protection-for-azure-storage-)에 대해 자세히 알아보세요.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>위협 방지 기능을 사용 하도록 설정 하는 8 가지 새로운 권장 사항

가상 컴퓨터, App Service 계획, Azure SQL Database 서버, 컴퓨터의 SQL server, Azure Storage 계정, Azure Kubernetes 서비스 클러스터, Azure Container Registry 레지스트리 및 Azure Key Vault 자격 증명 모음에 대 한 Azure Security Center의 위협 방지 기능을 사용 하도록 설정 하는 간단한 방법을 제공 하기 위해 8 개의 새로운 권장 사항이 추가 되었습니다.

새로운 권장 사항은 다음과 같습니다.

- **Azure SQL Database 서버에서 Advanced Data Security를 사용하도록 설정해야 함**
- **머신의 SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 함**
- **Azure App Service 계획에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Container Registry 레지스트리에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Key Vault 자격 증명 모음에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Kubernetes Service 클러스터에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Storage 계정에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **가상 머신에서 고급 위협 방지를 사용 하도록 설정 해야 합니다.**

이러한 새 권장 사항은 **Advanced Threat Protection 보안 설정** 제어에 속합니다.

권장 사항에는 빠른 수정 기능도 포함 되어 있습니다. 

> [!IMPORTANT]
> 이러한 권장 사항에 수정 관련 리소스를 보호 하는 요금이 부과 됩니다. 현재 구독에 관련 리소스가 있는 경우 이러한 요금은 즉시 시작 됩니다. 나중에 추가 하는 경우 나중에 추가 합니다.
> 
> 예를 들어 구독에 Azure Kubernetes Service 클러스터가 없고 위협 방지를 사용 하도록 설정 하는 경우 요금이 발생 하지 않습니다. 나중에 동일한 구독에서 클러스터를 추가 하는 경우 자동으로 보호 되 고 해당 시간에 요금이 청구 됩니다.

[보안 권장 사항 참조 페이지](recommendations-reference.md)에서 각각에 대해 자세히 알아보세요.

[Azure Security Center에서 위협 방지](https://docs.microsoft.com/azure/security-center/threat-protection)에 대해 자세히 알아보세요.




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>컨테이너 보안 개선-더 빠른 레지스트리 검색 및 새로 고침 설명서

컨테이너 보안 도메인에 대 한 지속적인 투자의 일환으로 Azure Container Registry에 저장 된 컨테이너 이미지에 대 한 Security Center의 동적 검색에서 상당한 성능 향상을 공유 합니다. 검색은 이제 약 2 분 안에 완료 됩니다. 일부 경우에는 15 분 정도 걸릴 수 있습니다.

Azure Security Center의 컨테이너 보안 기능과 관련 된 명확 성과 지침은 컨테이너 보안 설명서 페이지를 새로 고쳤습니다. 

다음 문서에서 Security Center의 컨테이너 보안에 대해 자세히 알아보세요.

- [Security Center 컨테이너 보안 기능 개요](https://docs.microsoft.com/azure/security-center/container-security)
- [Azure Container Registry와의 통합에 대 한 세부 정보](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Azure Kubernetes Service와의 통합에 대 한 세부 정보](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [방법-레지스트리 검색 및 Docker 호스트 강화](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Azure Kubernetes Service 클러스터에 대 한 위협 방지 기능의 보안 경고](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Azure Kubernetes 서비스 호스트에 대 한 위협 방지 기능의 보안 경고](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [컨테이너에 대 한 보안 권장 사항](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>적응 응용 프로그램 컨트롤이 새로운 권장 사항 및 경로 규칙의 와일드 카드 지원으로 업데이트 됨

적응 응용 프로그램 컨트롤 기능은 다음과 같은 두 가지 중요 한 업데이트를 받았습니다.

* 새 권장 사항은 이전에는 허용 되지 않은 잠재적으로 합법적인 동작을 식별 합니다. 적응 응용 프로그램 제어 정책에서 새로운 권장 사항, **Allowlist 규칙을 업데이트 해야 하며**, 적응 응용 프로그램 제어 위반 경고에서 가양성 수를 줄이기 위해 기존 정책에 새 규칙을 추가 하 라는 메시지를 표시 합니다.

* 이제 경로 규칙에서 와일드 카드를 지원 합니다. 이 업데이트에서 와일드 카드를 사용 하 여 허용 되는 경로 규칙을 구성할 수 있습니다. 지원 되는 두 가지 시나리오는 다음과 같습니다.

    * 경로의 끝에 와일드 카드를 사용 하 여이 폴더와 하위 폴더에 있는 모든 실행 파일을 허용 합니다.

    * 경로 중간에 와일드 카드를 사용 하 여 변경 된 폴더 이름 (예: 알려진 실행 파일이 있는 개인 사용자 폴더, 자동으로 생성 된 폴더 이름 등)이 있는 알려진 실행 파일 이름을 사용할 수 있습니다.


[적응형 애플리케이션 제어에 대해 자세히 알아봅니다](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>SQL 고급 데이터 보안에 대 한 6 가지 정책 사용 되지 않음

SQL 컴퓨터의 고급 데이터 보안과 관련 된 6 가지 정책은 더 이상 사용 되지 않습니다.

- SQL 관리 되는 인스턴스 고급 데이터 보안 설정에서 advanced threat protection 유형을 ' 모두 '로 설정 해야 합니다.
- SQL server 고급 데이터 보안 설정에서 advanced threat protection 유형을 ' 모두 '로 설정 해야 합니다.
- SQL 관리형 인스턴스에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL 관리형 인스턴스 Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.
- SQL Server Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.

[기본 제공 정책](security-center-policy-definitions.md)에 대해 자세히 알아보세요.





## <a name="june-2020"></a>2020년 6월

6 월의 업데이트는 다음과 같습니다.
- [보안 점수 API (미리 보기)](#secure-score-api-preview)
- [SQL 컴퓨터 (Azure, 다른 클라우드 및 온-프레미스)에 대 한 고급 데이터 보안 (미리 보기)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Azure Arc 컴퓨터에 Log Analytics 에이전트를 배포 하기 위한 두 가지 새로운 권장 사항 (미리 보기)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [대규모 연속 내보내기 및 워크플로 자동화 구성을 만드는 새 정책](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [NSGs를 사용 하 여 인터넷에 연결 되지 않은 가상 머신을 보호 하기 위한 새로운 권장 사항](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [위협 방지 및 고급 데이터 보안을 사용 하기 위한 새로운 정책](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>보안 점수 API (미리 보기)

이제 [보안 점수 API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (현재 미리 보기 상태)를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공 하 고 시간에 따른 보안 점수에 대 한 자체 보고 메커니즘을 작성 합니다. 예를 들어 **보안 점수** API를 사용 하 여 특정 구독에 대 한 점수를 가져올 수 있습니다. 또한 보안 **점수 컨트롤** API를 사용 하 여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

보안 점수 API를 사용 하 여 가능한 외부 도구의 예는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조 하세요.

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>SQL 컴퓨터 (Azure, 다른 클라우드 및 온-프레미스)에 대 한 고급 데이터 보안 (미리 보기)

SQL 컴퓨터에 대 한 Azure Security Center의 고급 데이터 보안은 이제 Azure에서 호스트 되는 SQL Server, 다른 클라우드 환경 및 온-프레미스 컴퓨터를 보호 합니다. 이렇게 하면 하이브리드 환경을 완벽 하 게 지원 하기 위해 Azure 네이티브 SQL Server에 대 한 보호 기능이 확장 됩니다.

Advanced data security는 SQL 컴퓨터에 대 한 취약성 평가 및 고급 위협 방지 기능을 배치 합니다.

설치에는 두 단계가 포함 됩니다.

1. SQL Server의 호스트 컴퓨터에 Log Analytics 에이전트를 배포 하 여 Azure 계정에 대 한 연결을 제공 합니다.

1. Security Center의 가격 책정 및 설정 페이지에서 선택적 번들을 사용 하도록 설정 합니다.

[SQL 컴퓨터의 고급 데이터 보안](security-center-iaas-advanced-data.md)에 대해 자세히 알아보세요.



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Azure Arc 컴퓨터에 Log Analytics 에이전트를 배포 하기 위한 두 가지 새로운 권장 사항 (미리 보기)

Azure Arc 컴퓨터에 [Log Analytics 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 를 배포 하는 데 도움이 되는 두 가지 새로운 권장 사항이 추가 되었고 Azure Security Center으로 보호 되는지 확인 합니다.

- **Log Analytics 에이전트는 Windows 기반 Azure Arc 컴퓨터 (미리 보기)에 설치 되어야 합니다.**
- **Linux 기반 Azure Arc 컴퓨터 (미리 보기)에 Log Analytics 에이전트를 설치 해야 합니다.**

이러한 새 권장 사항은 기존 (관련) 권장 사항과 동일한 4 가지 보안 제어에 표시 되며, **컴퓨터에 모니터링 에이전트를 설치 해야**합니다. 보안 구성 수정, 적응 응용 프로그램 제어 적용, 시스템 업데이트 적용 및 endpoint protection 사용.

권장 사항에는 배포 프로세스를 가속화 하는 데 도움이 되는 빠른 수정 기능도 포함 되어 있습니다. 

이러한 두 가지 새로운 권장 사항에 대 한 자세한 내용은 [계산 및 앱 권장 사항](recommendations-reference.md#recs-computeapp) 테이블을 확인 하세요.

Azure Security Center에서 에이전트를 사용 하는 방법에 대 한 자세한 내용은 [Log Analytics 에이전트?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent)를 사용 하십시오.

[Azure Arc 컴퓨터용 확장](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)에 대해 자세히 알아보세요.



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>대규모 연속 내보내기 및 워크플로 자동화 구성을 만드는 새 정책

조직의 모니터링 및 인시던트 응답 프로세스를 자동화 하면 보안 인시던트를 조사 하 고 완화 하는 데 걸리는 시간을 크게 향상 시킬 수 있습니다.

조직 전체에 automation 구성을 배포 하려면 이러한 기본 제공 ' DeployIfdNotExist ' Azure 정책을 사용 하 여 [연속 내보내기](continuous-export.md) 및 [워크플로 자동화](workflow-automation.md) 프로시저를 만들고 구성 합니다.

Azure policy에서 정책을 찾을 수 있습니다.


|목표  |정책  |정책 ID  |
|---------|---------|---------|
|이벤트 허브로 연속 내보내기|[Azure Security Center 경고 및 권장 사항에 대한 Event Hub로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Log Analytics 작업 영역으로 연속 내보내기|[Azure Security Center 경고 및 권장 사항에 대한 Log Analytics 작업 영역으로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|보안 경고에 대 한 워크플로 자동화|[Azure Security Center 경고에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|보안 권장 사항에 대 한 워크플로 자동화|[Azure Security Center 권장 사항에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

[워크플로 자동화 템플릿을](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)시작 합니다.

[정책을 통해 경고 및 권장 사항 Azure Security Center 지속적으로 내보내기](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745)에서 두 개의 내보내기 정책을 사용 하는 방법에 대해 자세히 알아보세요.


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>NSGs를 사용 하 여 인터넷에 연결 되지 않은 가상 머신을 보호 하기 위한 새로운 권장 사항

"보안 모범 사례 구현" 보안 제어에는 이제 다음과 같은 새로운 권장 사항이 포함 됩니다.

- **네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함**

기존 권장 사항인 **인터넷 연결 가상 머신은 네트워크 보안 그룹으로 보호 해야**하며 인터넷 연결 및 인터넷이 아닌 vm을 구분 하지 않습니다. 둘 다에서 VM이 네트워크 보안 그룹에 할당 되지 않은 경우에는 심각도가 높은 권장 구성이 생성 됩니다. 이 새로운 권장 사항은 거짓 긍정을 줄이고 불필요 한 심각도가 높은 경고를 방지 하기 위해 비 인터넷 연결 컴퓨터를 분리 합니다.

[네트워크 권장 사항](recommendations-reference.md#recs-network) 테이블에서 자세히 알아보세요.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>위협 방지 및 고급 데이터 보안을 사용 하기 위한 새로운 정책

아래 새 정책은 ASC 기본 이니셔티브에 추가 되었으며 관련 리소스 유형에 대해 위협 방지 또는 고급 데이터 보안을 사용 하도록 지원 하도록 설계 되었습니다.

Azure policy에서 정책을 찾을 수 있습니다.


| 정책                                                                                                                                                                                                                                                                | 정책 ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database 서버에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [머신의 SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Azure Storage 계정에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Azure Key Vault 자격 증명 모음에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Azure App Service 계획에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Azure Container Registry 레지스트리에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Azure Kubernetes Service 클러스터에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Virtual Machines에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

[Azure Security Center에서 위협 방지](https://docs.microsoft.com/azure/security-center/threat-protection)에 대해 자세히 알아보세요.





## <a name="may-2020"></a>2020년 5월

의 업데이트에는 다음이 포함 될 수 있습니다.
- [중복된 경고 제거 규칙(미리 보기)](#alert-suppression-rules-preview)
- [공급되는 가상 머신 취약성 평가 일반 공급](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [JIT(just-in-Time) VM(가상 머신) 액세스의 변경 사항](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [사용자 지정 권장 사항이 별도의 보안 컨트롤로 이동됨](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [설정/해제가 컨트롤의 권장 사항 보기에 또는 단순 목록으로 추가됨](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [확장된 보안 컨트롤 "보안 모범 사례 구현"](#expanded-security-control-implement-security-best-practices)
- [이제 일반 공급되는 사용자 지정 메타데이터가 포함된 사용자 지정 정책](#custom-policies-with-custom-metadata-are-now-generally-available)
- [파일리스 공격 탐지로 마이그레이션하는 크래시 덤프 분석 기능](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>중복된 경고 제거 규칙(미리 보기)

이 새로운 기능(현재 미리 보기 상태)은 경고 피로를 줄이는 데 도움이 됩니다. 규칙을 사용하여 조직 내 정상적인 작업에 무해하거나 관련이 있는 것으로 알려진 경고를 자동으로 숨깁니다. 이렇게 하면 가장 관련성이 높은 위협에 집중할 수 있습니다. 

활성화된 중복된 경고 제거 규칙과 일치하는 경고는 계속 생성되지만 해당 상태는 해제됨으로 설정됩니다. Azure Portal에서 상태를 보거나 Security Center 보안 경고에 액세스할 수 있습니다.

중복된 경고 제거 규칙은 경고를 자동으로 해제할 조건을 정의합니다. 일반적으로 다음과 같은 경우에는 중복된 경고 제거 규칙을 사용합니다.

- 거짓 긍정으로 식별한 경고를 표시 안 함

- 너무 자주 트리거되는 경고를 표시 안 함

[Azure Security Center의 위협 방지에서 경고 표시 안 함](alerts-suppression-rules.md)에 대해 자세히 알아보세요.


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>공급되는 가상 머신 취약성 평가 일반 공급

가상 머신에 대한 통합 취약성 평가가 이제 Security Center의 표준 계층에 추가 비용 없이 포함됩니다. 이 확장은 Qualys에서 제공하지만 검색 결과를 다시 Security Center에 보고합니다. Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 항목이 Security Center 내에서 원활하게 처리됩니다.

새 솔루션은 가상 머신을 지속적으로 스캔하여 취약성을 찾고 Security Center에 검색 결과를 제공할 수 있습니다. 

솔루션을 배포하려면 새 보안 권장 사항을 사용합니다.

"가상 머신에서 기본 제공 취약성 평가 솔루션 사용(Qualys에서 제공)"

[가상 머신에 대한 Security Center의 통합 취약성 평가](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)에 대해 자세히 알아보세요.



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>JIT(just-in-Time) VM(가상 머신) 액세스의 변경 사항

Security Center에는 VM의 관리 포트를 보호하는 선택적 기능이 포함되어 있습니다. 이는 가장 일반적인 형태의 무차별 암호 대입 공격에 대한 방어를 제공합니다.

이 업데이트는 이 기능을 다음과 같이 변경합니다.

- VM에서 JIT를 사용하도록 설정하라는 권장 사항의 이름이 변경되었습니다. 이전의 "가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 함"은 이제 다음과 같습니다. "가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함".

- 권장 사항은 열려 있는 관리 포트가 있는 경우에만 트리거됩니다.

[JIT 액세스 기능](security-center-just-in-time.md)에 대해 자세히 알아보세요.


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>사용자 지정 권장 사항이 별도의 보안 컨트롤로 이동됨

보안 점수가 향상 된 보안 제어 중 하나는 "보안 모범 사례 구현" 이었습니다. 구독에 대해 생성된 사용자 지정 권장 사항은 자동으로 해당 컨트롤에 배치되었습니다. 

사용자 지정 권장 사항을 더 쉽게 찾을 수 있도록 이를 전용 보안 컨트롤인 "사용자 지정 권장 사항"으로 이동했습니다. 이 컨트롤은 보안 점수에 영향을 미치지 않습니다.

[Azure Security Center에서 향상된 보안 점수(미리 보기)](secure-score-security-controls.md)의 보안 컨트롤에 대해 자세히 알아보세요.


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>설정/해제가 컨트롤의 권장 사항 보기에 또는 단순 목록으로 추가됨

보안 컨트롤은 관련된 보안 권장 사항의 논리적 그룹입니다. 취약한 공격 노출 영역을 반영합니다. 컨트롤은 이러한 권장 사항을 구현하는 데 도움이 되는 지침을 포함하는 보안 권장 사항 집합입니다.

조직이 각 개별 공격 노출 영역을 얼마나 잘 보호하고 있는지 즉시 확인하려면 각 보안 컨트롤의 점수를 검토합니다.

기본적으로 권장 사항은 보안 컨트롤에 표시됩니다. 이 업데이트에서 목록으로 표시할 수도 있습니다. 영향을 받는 리소스의 상태를 기준으로 정렬된 간단한 목록으로 표시하려면 새 ' 컨트롤 그룹화' 토글을 사용합니다. 토글은 포털의 목록 상단에 있습니다.

보안 컨트롤 및 이 토글은 새로운 보안 점수 환경의 일부입니다. 포털 내에서 사용자 의견을 보내 주시기 바랍니다.

[Azure Security Center에서 향상된 보안 점수(미리 보기)](secure-score-security-controls.md)의 보안 컨트롤에 대해 자세히 알아보세요.

![권장 사항에 대 한 "컨트롤 그룹화" 토글](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>확장된 보안 컨트롤 "보안 모범 사례 구현" 

향상 된 보안 점수를 사용 하 여 도입 된 보안 제어 중 하나는 "보안 모범 사례 구현"입니다. 이 컨트롤에 권장 사항이 있으면 보안 점수에 영향을 주지 않습니다. 

이 업데이트를 사용하면 세 가지 권장 사항이 원래 배치된 컨트롤에서 이 모범 사례 제어로 이동했습니다. 이러한 세 가지 권장 사항에 대한 위험이 처음에 생각된 것보다 낮은 것으로 판단했기 때문에 이 단계를 수행했습니다.

또한 이 컨트롤에는 두 가지 새로운 권장 사항이 도입되어 추가되었습니다.

이동한 세 가지 권장 사항은 다음과 같습니다.

- **구독에 대한 읽기 권한이 있는 계정에서 MFA를 사용하도록 설정해야 합니다**(원래 "MFA 사용" 컨트롤).
- **읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다**(원래 "액세스 및 권한 관리" 컨트롤).
- **구독에 대해 최대 3명의 소유자를 지정해야 합니다**(원래 "액세스 및 권한 관리" 컨트롤).

컨트롤에 추가된 두 가지 새로운 권장 사항은 다음과 같습니다.

- **게스트 구성 확장은 Windows 가상 컴퓨터 (미리 보기)에 설치 되어야 합니다** . [게스트 구성 Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) 사용 하 여 가상 컴퓨터 내에서 서버 및 응용 프로그램 설정에 대 한 가시성을 제공 합니다 (Windows에만 해당).

- **컴퓨터에 Windows Defender Exploit guard를 사용 하도록 설정 해야 함 (미리 보기)** -Windows Defender exploit Guard Azure Policy 게스트 구성 에이전트를 활용 합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 4가지 구성 요소가 있습니다(Windows에만 해당).

[Exploit Guard 정책 만들기 및 배포](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)에서 Windows Defender Exploit Guard에 대해 자세히 알아보세요.

보안 [강화 점수 (미리 보기)](secure-score-security-controls.md)의 보안 제어에 대해 자세히 알아보세요.



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>이제 일반 공급되는 사용자 지정 메타데이터가 포함된 사용자 지정 정책

사용자 지정 정책은 이제 Security Center 권장 사항 환경, 보안 점수 및 규정 준수 표준 대시보드의 일부입니다. 이 기능은 이제 일반 공급되며, Security Center에서 조직의 보안 평가 적용 범위를 확장할 수 있습니다. 

Azure 정책에서 사용자 지정 이니셔티브를 만들고, 여기에 정책을 추가하고 Azure Security Center에 온보딩하고, 권장 사항으로 시각화합니다.

이제 사용자 지정 권장 구성 메타데이터를 편집하는 옵션도 추가했습니다. 메타데이터 옵션에는 심각도, 수정 단계, 위협 정보 등이 포함됩니다.  

[세부 정보를 사용하여 사용자 지정 권장 사항 향상](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)에 대해 자세히 알아보세요.



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>파일리스 공격 탐지로 마이그레이션하는 크래시 덤프 분석 기능 

Microsoft는 CDA(Windows 크래시 덤프 분석) 탐지 기능을 [파일리스 공격 탐지](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)에 통합하고 있습니다. 파일리스 공격 탐지 분석은 Windows 컴퓨터에 대해 다음과 같은 보안 경고의 향상된 버전을 제공합니다. 코드 주입 검색됨, 모조 Windows 모듈 탐지됨, Shellcode 검색됨 및 의심스러운 코드 세그먼트 탐지됨.

이러한 전환의 이점 중 일부는 다음과 같습니다.

- **자동 관리 및 적시** 에 발생 하는 맬웨어 검색-충돌이 발생 하기를 기다린 다음, 분석을 실행 하 여 악의적인 아티팩트를 찾는 데 관련 된 CDA 접근 방법입니다. 파일리스 공격 탐지를 사용하면 실행되는 동안 메모리 내 위협이 사전 예방적으로 식별됩니다. 

- **보강 경고** - 파일리스 공격 탐지의 보안 경고에는 CDA에서 사용할 수 없는 강화(예: 활성 네트워크 연결 정보)가 포함됩니다. 

- **경고 집계** - CDA는 단일 크래시 덤프 내에서 여러 공격 패턴을 탐지한 경우 여러 보안 경고를 트리거했습니다. 파일리스 공격 탐지는 동일한 프로세스에서 식별된 모든 공격 패턴을 단일 경고로 결합하여 여러 경고를 상호 연결할 필요가 없도록 합니다.

- **Log Analytics 작업 영역에 대한 요구 사항 감소** - 잠재적으로 중요한 데이터가 포함된 크래시 덤프는 Log Analytics 작업 영역에 더 이상 업로드되지 않습니다.



## <a name="april-2020"></a>2020년 4월

4 월의 업데이트는 다음과 같습니다.
- [이제 일반 공급되는 동적 규정 준수 패키지](#dynamic-compliance-packages-are-now-generally-available)
- [이제 Azure Security Center 무료 계층에 포함되는 ID 권장 사항](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>이제 일반 공급되는 동적 규정 준수 패키지

Azure Security Center 규제 준수 대시보드에는 이제 추가 산업 및 규제 표준을 추적하는 **동적 규정 준수 패키지**(현재 일반 공급)가 포함되어 있습니다.

Security Center 보안 정책 페이지에서 구독 또는 관리 그룹에 동적 규정 준수 패키지를 추가할 수 있습니다. 표준 또는 벤치 마크를 온보딩하면 규정 준수 대시보드에 평가로 매핑된 모든 관련 준수 데이터가 포함된 표준이 표시됩니다. 온보딩된 표준에 대한 요약 보고서는 다운로드할 수 있습니다.

이제 다음과 같은 표준을 추가할 수 있습니다.

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **영국 공식 및 영국 NHS**
- **캐나다 연방 PBMM**
- **Azure CIS 1.1.0(신규)** (Azure CIS 1.1.0을 보다 완벽하게 표현)

또한 최근 일반 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침인 **Azure 보안 벤치마크**를 추가했습니다. 추가 표준은 사용할 수 있게 되면 대시보드에서 지원될 예정입니다.  
 
[규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)에 대해 자세히 알아보세요.


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>이제 Azure Security Center 무료 계층에 포함되는 ID 권장 사항

Azure Security Center 무료 계층에 대한 ID 및 액세스에 대한 보안 권장 사항이 이제 일반 공급됩니다. 이는 CSPM(클라우드 보안 상태 관리) 기능을 무료로 제공하는 노력의 일부입니다. 지금까지 이러한 권장 사항은 표준 가격 책정 계층에서만 사용할 수 있었습니다.

ID 및 액세스 권장 사항의 예는 다음과 같습니다.

- "구독에 대한 소유자 권한이 있는 계정에 대해 다단계 인증을 사용하도록 설정해야 합니다."
- "구독에 대해 최대 3명의 소유자를 지정해야 합니다."
- "더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다."

무료 가격 책정 계층에 대한 구독이 있는 경우 해당 보안 점수는 ID 및 액세스 보안에 대해 평가하지 않았기 때문에 이 변경의 영향을 받습니다.

[ID 및 액세스 권장 사항](recommendations-reference.md#recs-identity)에 대해 자세히 알아보세요.

[ID 및 액세스 모니터링](security-center-identity-access.md)에 대해 자세히 알아보세요.


## <a name="march-2020"></a>2020년 3월

3 월의 업데이트는 다음과 같습니다.
- [이제 일반 공급되는 워크플로 자동화](#workflow-automation-is-now-generally-available)
- [Windows Admin Center와 Azure Security Center 통합](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes Service에 대한 보호](#protection-for-azure-kubernetes-service)
- [Just-in-Time 환경 개선](#improved-just-in-time-experience)
- [사용되지 않는 웹 애플리케이션에 대한 두 가지 보안 권장 사항](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>이제 일반 공급되는 워크플로 자동화

Azure Security Center의 워크플로 자동화 기능이 이제 일반 공급됩니다. 이를 사용하여 보안 경고 및 권장 사항에 대한 Logic Apps를 자동으로 트리거합니다. 또한 빠른 수정 옵션을 사용할 수 있는 경고 및 모든 권장 사항에 대해 수동 트리거를 사용할 수 있습니다.

모든 보안 프로그램에는 인시던트 응답을 위한 여러 워크플로가 포함되어 있습니다. 이러한 프로세스에는 관련 이해 관계자에게 알리고, 변경 관리 프로세스를 시작하고, 특정 수정 단계를 적용하는 것이 포함될 수 있습니다. 보안 전문가는 가능한 한 해당 절차의 여러 단계를 자동화할 것을 권장합니다. 자동화는 오버헤드를 줄이고 프로세스 단계가 미리 정의된 요구 사항에 따라 빠르고 일관되게 수행되도록 하여 보안을 향상시킬 수 있습니다.

워크플로를 실행하기 위한 자동 및 수동 Security Center 기능에 대한 자세한 내용은 [워크플로 자동화](workflow-automation.md)를 참조하세요.

[Logic Apps 만들기](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)에 대해 자세히 알아보세요.


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Windows Admin Center와 Azure Security Center 통합

이제 Windows Admin Center에서 Azure Security Center로 온-프레미스 Windows 서버를 바로 이동할 수 있습니다. 그러면 Security Center는 온-프레미스 서버, 가상 머신 및 추가 PaaS 워크로드를 포함하여 모든 Windows Admin Center 리소스에 대한 보안 정보를 볼 수 있는 단일 창이 됩니다.

Windows Admin Center에서 Azure Security Center로 서버를 이동한 후 다음을 수행할 수 있습니다.

- Windows Admin Center의 Security Center 확장에서 보안 경고 및 권장 사항을 확인합니다.
- 보안 상태를 확인하고 Azure Portal 또는 API를 통해 Security Center에서 Windows Admin Center 관리 서버에 대한 추가 세부 정보를 검색합니다.

[Windows Admin Center와 Azure Security Center를 통합하는 방법](windows-admin-center-integration.md)에 대해 자세히 알아보세요.


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대한 보호

Azure Security Center는 AKS(Azure Kubernetes Service)를 보호하기 위해 컨테이너 보안 기능을 확장하고 있습니다.

널리 사용되는 오픈 소스 플랫폼 Kubernetes는 이제 컨테이너 오케스트레이션에 대한 업계 표준으로 널리 채택되고 있습니다. 이러한 광범위한 구현에도 불구하고, Kubernetes 환경을 보호하는 방법에 대한 이해가 부족합니다. 컨테이너화된 애플리케이션의 공격 노출 영역을 방어하려면 인프라가 안전하게 구성되고 잠재적 위협에 대해 지속적으로 모니터링되도록 하는 전문 지식이 필요합니다.

Security Center 방어에는 다음이 포함됩니다.

- **검색 및 가시성** - Security Center에 등록된 구독 내에서 관리되는 AKS 인스턴스를 지속적으로 검색합니다.
- **보안 권장 사항** - AKS에 대한 보안 모범 사례를 준수하는 데 도움이 되는 실행 가능한 권장 사항입니다. 이러한 권장 사항은 조직의 보안 상태의 일부로 표시되도록 보안 점수에 포함됩니다. AKS 관련 권장 사항의 예는 "역할 기반 액세스 제어를 사용하여 Kubernetes 서비스 클러스터에 대한 액세스를 제한해야 합니다"를 참조하세요.
- **위협 방지** - AKS 배포에 대한 지속적인 분석을 통해 Security Center는 호스트 및 AKS 클러스터 수준에서 탐지된 위협 및 악의적인 활동을 경고합니다.

[Security Center와 Azure Kubernetes Services 통합](azure-kubernetes-service-integration.md)에 대해 자세히 알아보세요.

[Security Center의 컨테이너 보안 기능](container-security.md)에 대해 자세히 알아보세요.


### <a name="improved-just-in-time-experience"></a>Just-in-Time 환경 개선

관리 포트를 보호하는 Azure Security Center의 Just-in-Time 도구의 기능, 작업 및 UI는 다음과 같이 향상되었습니다. 

- **양쪽 맞춤 필드** - Azure Portal의 Just-in-Time 페이지를 통해 VM(가상 머신)에 대한 액세스를 요청할 때 새 선택적 필드를 사용하여 요청에 대한 근거를 입력할 수 있습니다. 이 필드에 입력한 정보는 활동 로그에서 추적할 수 있습니다. 
- **중복된 JIT(Just-in-Time) 규칙 자동 정리** - JIT 정책을 업데이트할 때마다 정리 도구가 자동으로 실행되어 전체 규칙 집합의 유효성을 검사합니다. 이 도구는 정책의 규칙과 NSG의 규칙 간 불일치를 검색합니다. 정리 도구에서 불일치를 발견하면 원인을 확인하고 안전한 경우 더 이상 필요하지 않은 기본 제공 규칙을 제거합니다. 클리너는 만든 규칙을 삭제하지 않습니다. 

[JIT 액세스 기능](security-center-just-in-time.md)에 대해 자세히 알아보세요.


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>사용되지 않는 웹 애플리케이션에 대한 두 가지 보안 권장 사항

사용되지 않는 웹 애플리케이션에 대한 두 가지 보안 권장 사항은 다음과 같습니다. 

- IaaS NSG의 웹 애플리케이션에 대한 규칙을 강화해야 합니다.
    (관련 정책: IaaS의 웹 애플리케이션에 대한 NSG 규칙을 강화해야 합니다.)

- App Services에 대한 액세스를 제한해야 합니다.
    (관련 정책: App Services에 대한 액세스를 제한해야 합니다[미리 보기].)

이러한 권장 사항은 Security Center 권장 사항 목록에 더 이상 표시되지 않습니다. 관련 정책은 "Security Center 기본값"이라는 이니셔티브에 더 이상 포함되지 않습니다.

[보안 권장 사항](recommendations-reference.md)에 대해 자세히 알아보세요.

