---
title: Azure Migrate를 사용 하 여 Azure로의 마이그레이션에 대 한 많은 수의 Hyper-v Vm 평가 Microsoft Docs
description: Azure Migrate 서비스를 사용 하 여 Azure로 마이그레이션하기 위한 많은 수의 Hyper-v Vm을 평가 하는 방법을 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279450"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Azure로의 마이그레이션에 대 한 많은 수의 Hyper-v Vm 평가

이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 Azure로 마이그레이션하기 위해 많은 수의 온-프레미스 Hyper-v Vm을 평가 하는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 


이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 규모에 대 한 평가 계획.
> * Azure 사용 권한을 구성 하 고 평가를 위해 Hyper-v를 준비 합니다.
> * Azure Migrate 프로젝트를 만들고 평가를 만듭니다.
> * 마이그레이션을 계획할 때 평가를 검토 합니다.


> [!NOTE]
> 규모를 평가 하기 전에 몇 가지 Vm을 평가 하는 개념 증명을 사용해 보려면 [자습서 시리즈](tutorial-prepare-hyper-v.md) 를 따르세요.

## <a name="plan-for-assessment"></a>평가 계획

많은 수의 Hyper-v Vm에 대 한 평가를 계획할 때 고려해 야 할 몇 가지 사항이 있습니다.

- **계획 Azure Migrate 프로젝트**: Azure Migrate 프로젝트를 배포 하는 방법을 파악 합니다. 예를 들어 데이터 센터가 다른 지역에 있거나 검색, 평가 또는 마이그레이션 관련 메타 데이터를 다른 지리에 저장 해야 하는 경우 여러 프로젝트가 필요할 수 있습니다.
- **계획 어플라이언스**: Azure Migrate은 Hyper-v VM으로 배포 된 온-프레미스 Azure Migrate 어플라이언스를 사용 하 여 평가 및 마이그레이션을 위해 Vm을 지속적으로 검색 합니다. 어플라이언스는 Vm, 디스크 또는 네트워크 어댑터를 추가 하는 등의 환경 변경을 모니터링 합니다. 또한 Azure에 대 한 메타 데이터 및 성능 데이터를 전송 합니다. 배포할 어플라이언스 수를 파악 해야 합니다.


## <a name="planning-limits"></a>제한 계획
 
계획을 위해이 표에 요약 된 제한을 사용 합니다.

**계획** | **제한**
--- | --- 
**Azure Migrate 프로젝트** | 프로젝트에서 최대 35000 개의 Vm을 평가 합니다.
**Azure Migrate 어플라이언스** | 어플라이언스는 최대 5000 개의 Vm을 검색할 수 있습니다.<br/> 어플라이언스는 최대 300 Hyper-v 호스트에 연결할 수 있습니다.<br/> 어플라이언스는 단일 Azure Migrate 프로젝트에만 연결할 수 있습니다.<br/> 모든 수의 어플라이언스를 단일 Azure Migrate 프로젝트에 연결할 수 있습니다. <br/><br/> 
**그룹** | 단일 그룹에서 최대 35000 개의 Vm을 추가할 수 있습니다.
**Azure Migrate 평가** | 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.



## <a name="other-planning-considerations"></a>기타 계획 고려 사항

- 어플라이언스에서 검색을 시작 하려면 각 Hyper-v 호스트를 선택 해야 합니다. 
- 다중 테넌트 환경을 실행 하는 경우 현재 특정 테넌트에 속하는 Vm만 검색할 수 없습니다. 

## <a name="prepare-for-assessment"></a>평가 준비

서버 평가를 위해 Azure 및 Hyper-v 준비. 

1. [Hyper-v 지원 요구 사항 및 제한 사항을](migrate-support-matrix-hyper-v.md)확인 하세요.
2. Azure Migrate와 상호 작용 하도록 Azure 계정에 대 한 사용 권한 설정
3. Hyper-v 호스트 및 Vm 준비

[이 자습서](tutorial-prepare-hyper-v.md) 의 지침에 따라 이러한 설정을 구성 합니다.

## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행 합니다.

1. Azure Migrate 프로젝트를 만듭니다.
2. 프로젝트에 Azure Migrate 서버 평가 도구를 추가 합니다.

[자세히 알아보기](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>평가 만들기 및 검토

1. Hyper-v Vm에 대 한 평가를 만듭니다.
1. 마이그레이션 계획 준비에 대 한 평가를 검토 합니다.

평가 만들기 및 검토에 [대해 자세히 알아보세요](tutorial-assess-hyper-v.md) .
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * Hyper-v Vm에 대 한 Azure Migrate 평가를 확장할 계획
> * 평가를 위해 준비 된 Azure 및 Hyper-v
> * Azure Migrate 프로젝트를 만들고 평가를 실행 했습니다.
> * 마이그레이션 준비 과정에서 평가를 검토 했습니다.

이제 평가를 계산 하 [는 방법](concepts-assessment-calculation.md) 및 [평가를 수정](how-to-modify-assessment.md) 하는 방법을 알아봅니다.
