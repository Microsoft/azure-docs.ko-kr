---
title: 일반적인 클라우드 서비스 관리 작업 | Microsoft Docs
description: Azure Portal에서 Cloud Services를 관리하는 방법에 대해 알아봅니다. 이 예제는 Azure 포털을 사용합니다.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: d3d1ae759f0f3fa5edd417da61f1fa50b5d9cde7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433969"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Azure Portal에서 Cloud Services 관리
Azure Portal의 **Cloud Services** 영역에서 다음을 수행할 수 있습니다.

* 서비스 역할 또는 배포를 업데이트합니다.
* 스테이징된 배포의 수준을 프로덕션으로 승격시킵니다.
* 리소스 종속성을 확인하고 리소스를 함께 확장할 수 있도록 클라우드 서비스에 리소스를 연결합니다.
* 클라우드 서비스 또는 배포를 삭제합니다.

클라우드 서비스 규모를 조정하는 데 관한 자세한 내용은 [포털에서 클라우드 서비스 크기 자동 조정 구성](cloud-services-how-to-scale-portal.md)을 참조하세요.

## <a name="update-a-cloud-service-role-or-deployment"></a>클라우드 서비스 역할 또는 배포 업데이트
클라우드 서비스에 대한 애플리케이션 코드를 업데이트 해야 하는 경우 클라우드 서비스 블레이드에서 **업데이트** 를 사용합니다. 단일 역할이나 모든 역할을 업데이트할 수 있습니다. 업데이트하려면 새 서비스 패키지 및 서비스 구성 파일을 업로드하면 됩니다.

1. [Azure Portal][Azure portal]에서 업데이트하려는 클라우드 서비스를 선택합니다. 이렇게 하면 클라우드 서비스 인스턴스 블레이드가 열립니다.

2. 블레이드에서 **업데이트**를 선택합니다.

    ![업데이트 단추](./media/cloud-services-how-to-manage-portal/update-button.png)

3. 새 서비스 패키지 파일 (.cspkg) 및 서비스 구성 파일 (.cscfg)으로 배포를 업데이트 합니다.

    ![배포 업데이트](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. 필요에 따라 저장소 계정 및 배포 레이블을 업데이트합니다.

5. 역할에 역할 인스턴스가 하나만 있는 경우 **단일 인스턴스가 포함된 역할이 하나 이상 있는 경우에도 배포** 확인란을 선택하여 업그레이드가 계속 진행되도록 합니다.

    Azure는 각 역할에 둘 이상의 역할 인스턴스(가상 머신)가 있는 경우에만 클라우드 서비스 업데이트 중 99.95%의 서비스 가용성을 보장할 수 있습니다. 두 개의 역할 인스턴스가 있는 경우 가상 머신 하나가 업데이트되는 동안 다른 가상 머신에서 클라이언트 요청을 처리합니다.

6. 패키지의 업로드가 완료된 후 업데이트가 적용되도록 하려면 **배포 시작** 확인란을 선택합니다.

7. **확인**을 클릭하여 서비스 업데이트를 시작합니다.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>배포를 교환하여 스테이징된 배포의 수준을 프로덕션으로 승격시킵니다.
새 클라우드 서비스 릴리스를 배포할 때는 클라우드 서비스 스테이징 환경에서 새 릴리스를 스테이징하고 테스트할 수 있습니다. **교환** 을 사용하여 두 배포의 주소로 사용 중인 URL을 전환하고 새 릴리스를 프로덕션으로 승격합니다.

**Cloud Services** 페이지나 대시보드에서 배포를 교환할 수 있습니다.

1. [Azure Portal][Azure portal]에서 업데이트하려는 클라우드 서비스를 선택합니다. 이렇게 하면 클라우드 서비스 인스턴스 블레이드가 열립니다.

2. 블레이드에서 **교환**을 선택합니다.

    ![Cloud Services 교환 단추](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. 다음과 같은 확인 메시지가 열립니다.

    ![Cloud Services 교환](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. 배포 정보를 확인한 후 **확인**을 클릭하여 배포를 교환합니다.

    변경되는 것은 배포의 VIP(가상 IP) 주소뿐이기 때문에 배포 교환은 신속하게 이루어집니다.

    계산 비용을 절약하려면 프로덕션 배포가 예상대로 작동한다는 것이 확인된 후 스테이징 배포를 삭제하면 됩니다.

### <a name="common-questions-about-swapping-deployments"></a>배포 교환에 대한 일반적인 질문

**배포 교환을 위한 필수 조건**

성공적인 배포 교환을 위한 2가지 핵심 필수 조건은 다음과 같습니다.

- 프로덕션 슬롯에 정적 IP 주소를 사용하려는 경우에는 스테이징 슬롯에 대해서도 하나를 예비해 두어야 합니다. 그러지 않으면 교체가 실패합니다.

- 역할의 모든 인스턴스는 교체를 수행하기 전에 실행해야 합니다. Azure Portal의 **개요** 블레이드에서 인스턴스의 상태를 확인할 수 있습니다. 또는 Windows PowerShell에서 [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) 명령을 사용할 수 있습니다.

게스트 OS 업데이트 및 서비스 복구 작업으로 인해 배포 교환이 실패할 수도 있습니다. 자세한 내용은 [클라우드 서비스 배포 문제 해결](cloud-services-troubleshoot-deployment-problems.md)을 참조하세요.

**교체 시 애플리케이션 가동 중지가 발생할 수 있나요? 어떻게 처리해야 합니까?**

이전 섹션에서 설명한 대로 배포 교환은 Azure Load Balancer에서의 구성 변경일 뿐이므로, 일반적으로 빠릅니다. 경우에 따라 10초 이상 걸리며 일시적인 연결 오류가 발생할 수 있습니다. 고객에게 미치는 영향을 최소화하려면 [고객 재시도 논리](../best-practices-retry-general.md) 구현을 고려해 보세요.

## <a name="delete-deployments-and-a-cloud-service"></a>배포 및 클라우드 서비스 삭제
클라우드 서비스를 삭제하려면 먼저 각각의 기존 배포를 삭제해야 합니다.

계산 비용을 절약하려면 프로덕션 배포가 예상대로 작동한다는 것이 확인된 후 스테이징 배포를 삭제하면 됩니다. 중지된 배포된 역할 인스턴스의 계산 비용이 청구됩니다.

배포 또는 클라우드 서비스를 삭제하려면 다음 절차를 따르세요.

1. [Azure Portal][Azure portal]에서 삭제하려는 클라우드 서비스를 선택합니다. 이렇게 하면 클라우드 서비스 인스턴스 블레이드가 열립니다.

2. 블레이드에서 **삭제**를 선택합니다.

    ![Cloud Services 삭제 단추](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. 전체 클라우드 서비스를 삭제하려면 **클라우드 서비스 및 해당 배포** 확인란을 선택합니다. 또는 **프로덕션 배포** 또는 **스테이징 배포** 확인란을 선택할 수 있습니다.

    ![Cloud Services 삭제](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. 맨 아래에 **삭제**를 선택합니다.

5. 클라우드 서비스를 삭제하려면 **클라우드 서비스 삭제**를 선택합니다. 그런 다음, 확인 메시지가 나타나면 **예**를 선택합니다.

> [!NOTE]
> 클라우드 서비스를 삭제할 때 자세한 정보 표시 모니터링이 구성된 경우에는 저장소 계정에서 데이터를 수동으로 삭제해야 합니다. 메트릭 테이블을 찾을 수 있는 위치에 대한 정보는 [클라우드 서비스 모니터링 소개](cloud-services-how-to-monitor.md)를 참조하세요.


## <a name="find-more-information-about-failed-deployments"></a>실패한 배포에 대한 자세한 정보 보기
**개요** 블레이드의 위쪽에는 상태 표시줄이 있습니다. 표시줄을 선택하면 새 블레이드가 열리고 오류 정보가 표시됩니다. 배포에 오류가 없으면 정보 블레이드는 비어 있습니다.

![Cloud Services 개요](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>다음 단계
* [클라우드 서비스의 일반 구성](cloud-services-how-to-configure-portal.md)
* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy-portal.md)하는 방법을 알아봅니다.
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name-portal.md)구성
* [SSL 인증서](cloud-services-configure-ssl-certificate-portal.md)를 구성합니다.
