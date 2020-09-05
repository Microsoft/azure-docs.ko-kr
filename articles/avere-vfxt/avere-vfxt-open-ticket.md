---
title: Avere vFXT for Azure에 대한 지원을 받는 방법
description: Azure Portal를 통해 지원 티켓을 만들어 Azure 용 Avere vFXT을 배포 하거나 사용 하는 동안 발생할 수 있는 문제를 해결 하는 방법에 대해 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 522d29505d7d10f5f6d97136f270f07a63053d19
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271110"
---
# <a name="get-help-with-your-system"></a>시스템 지원 받기

Avere vFXT for Azure system에 대 한 도움말은 다음을 참조 하세요.

* **Avere vFXT 문제** - [아래](#open-a-support-ticket-for-your-avere-vfxt)에서 설명한 대로 Azure Portal을 사용하여 Avere vFXT에 대한 지원 티켓을 엽니다.
* **할당량** - 할당량 관련 문제가 있으면 [할당량 증가를 요청](#request-a-quota-increase)합니다.
* **설명서 및 예제** - 이 설명서 또는 예제에 문제가 있으면, 문제가 있는 페이지에서 아래쪽으로 스크롤하고 **피드백** 섹션을 사용하여 기존 문제를 검색하고 필요한 경우 새 문제를 제출합니다.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT에 대한 지원 티켓 열기

Avere vFXT를 배포하거나 사용하는 동안 문제가 발생하면 Azure Portal을 통해 지원을 요청합니다.

다음 단계에 따라 지원 티켓의 태그가 클러스터의 리소스로 지정되었는지 확인합니다. 티켓에 태그를 지정하면 올바른 지원 리소스로 라우팅하는 데 도움이 됩니다.

1. 에서 [https://portal.azure.com](https://portal.azure.com) **리소스 그룹**을 선택 합니다. 문제가 발생 한 vFXT 클러스터가 포함 된 리소스 그룹을 찾아 Avere 클러스터 가상 머신 중 하나를 클릭 합니다.

    ![원으로 표시된 특정 VM이 있는 Azure Portal 리소스 그룹 "개요" 패널의 스크린샷](media/avere-vfxt-ticket-vm.png)

1. VM 페이지의 왼쪽 패널에서 아래쪽으로 스크롤하고 **새 지원 요청**을 클릭합니다.

    ![이전 스크린샷의 VM에 대한 Azure Portal VM 페이지의 스크린샷 왼쪽 메뉴는 아래쪽으로 스크롤됩니다. ' 새 지원 요청 '은 원으로 되어 있습니다.](media/avere-vfxt-ticket-request.png)

1. 지원 요청의 첫 페이지에서 문제 유형을 선택 하 고 올바른 구독을 선택 했는지 확인 합니다.

   **서비스**에서 **모든 서비스** 를 클릭 하 고 **저장소** 아래에서 **Avere vFXT**를 선택 합니다.

   간단한 요약을 추가 하 고 문제 유형을 선택 합니다.

    ![Azure Portal의 새 지원 요청 화면 스크린샷 기본 탭이 선택 되어 있습니다. 화면 항목에는 문제 유형, 구독, 서비스, 요약 및 문제 유형이 포함 됩니다.](media/ticket-basics.png)

   **다음**을 클릭하여 계속합니다.

1. 지원 양식의 두 번째 페이지에는 요약 설명에 따라 문제를 해결 하기 위한 제안 사항이 포함 되어 있습니다. 지원 티켓을 만들어야 하는 경우 아래쪽의 **다음** 단추를 클릭 합니다.

   ![솔루션 탭이 선택 된 새 지원 요청 화면의 스크린샷 중간의 텍스트 필드는 ' 권장 솔루션 ' 제목을 가지 며 가능한 해결 방법을 설명 합니다.](media/ticket-solutions.png)

1. 세 번째 페이지에서 세부 정보를 제공 합니다. 여기에는 클러스터에 대 한 정보, 문제가 발생 한 시간, 심각도 및 사용자에 게 연락 하는 방법이 포함 됩니다. 정보를 입력 하 고 맨 아래에 있는 **다음** 단추를 클릭 합니다.

   ![세부 정보 탭이 선택 된 새 지원 요청 화면의 스크린샷 정보 필드는 ' 문제 세부 정보 ', ' 지원 방법 ' 및 "연락처 정보 ' 범주로 구성 됩니다.](media/ticket-details.png)

1. 최종 페이지의 정보를 검토 하 고 **만들기**를 클릭 합니다. 확인 및 티켓 번호가 사용자의 이메일 주소로 전송되고, 지원 담당자가 연락을 드립니다.

## <a name="request-a-quota-increase"></a>할당량 증가 요청

Avere vFXT for Azure를 배포하는 데 필요한 구성 요소를 확인하려면 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요. Azure Portal에서 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)할 수 있습니다.
