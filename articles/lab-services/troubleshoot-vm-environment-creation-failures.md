---
title: VM 및 환경을 만드는 Azure DevTest Labs 오류 문제 해결 | Microsoft Docs
description: 가상 머신 (VM) 및 Azure DevTest Labs에서 환경 만들기 실패 문제를 해결 하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: a653a785e99619c3e256613d6a4d2c7592f54c8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848492"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>가상 머신 (VM) 및 Azure DevTest Labs에서 환경 만들기 실패 문제 해결
DevTest Labs는 랩 정책을 위반 하려는 경우 또는 컴퓨터 이름에 유효 하지 않은 경우 경고를 제공 합니다. 빨간색 표시 하는 경우가 `X` 랩 VM 또는 환경 상태 문제가 발생 했음을 알리는 옆에 있는 합니다.  이 문서에서는 기본 문제를 찾고 다행히 나중에 이러한 문제를 방지 하는 데 사용할 수 있는 몇 가지 트릭을 제공 합니다.

## <a name="portal-notifications"></a>포털 알림
먼저 확인 하는 Azure portal을 사용 하는 경우는 **알림 패널**합니다.  알림 패널을 클릭 하 여 기본 명령 모음에서 사용할 수는 **벨 아이콘**, 알려 랩 VM 또는 환경 만들기 성공 했는지 여부입니다.  오류가 있으면 만들기 실패와 관련 된 오류 메시지가 표시 됩니다. 세부 정보 문제를 해결 하는 데 유용한 정보 더 자주 제공입니다. 다음 예제에서는 가상 머신을 만들지는 코어의 부족으로 인해 실패 했습니다. 자세한 메시지 문제를 해결 하 여 코어 할당량 증가 요청 하는 방법을 알려줍니다.

![Azure portal 알림](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>활동 로그
VM의 환경 만들기를 시도 이후에 오류를 조사 하는 경우 활동 로그를 확인 합니다. 이 섹션에서는 Vm 및 환경에 대 한 로그를 찾는 방법을 보여 줍니다.

## <a name="activity-logs-for-virtual-machines"></a>가상 머신에 대 한 활동 로그

1. 랩에 대 한 홈 페이지에서 시작 하려면 VM을 선택 합니다 **가상 머신** 페이지입니다.
2. 에 **가상 머신** 페이지를 **모니터링** 선택 왼쪽된 메뉴의 섹션 **활동 로그** VM과 연결 된 모든 로그를 확인 하려면.
3. 활동 로그 항목에서 실패 한 작업을 선택 합니다. 일반적으로 실패 한 작업이 호출 될 `Write Virtualmachines`합니다.
4. 오른쪽 창의 JSON 탭으로 전환 합니다. 로그의 JSON 보기에서 세부 정보를 표시 합니다.

    ![VM에 대 한 활동 로그](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. JSON 로그를 찾을 때까지 살펴봅니다는 `statusMessage` 속성입니다. 제공 기본 오류 메시지 및 추가 세부 정보를 해당 하는 경우. 다음 JSON는이 문서의 앞부분에 표시를 초과 하는 따옴표 붙은 core에 대 한 예제 오류입니다.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>환경에 대 한 활동 로그

환경 만들기에 대 한 활동 로그를 확인 하려면 다음이 단계를 수행 합니다.

1. 랩에 대 한 홈 페이지에서 선택 **구성 및 정책** 왼쪽된 메뉴에서.
2. 에 **구성 및 정책** 페이지에서 **활동 로그** 메뉴.
3. 로그에 작업 목록에서 실패 한 찾아서 선택 합니다.
4. 오른쪽 창에서 JSON 탭으로 전환 하 고 검색할 합니다 **상태 메시지**합니다.

    ![환경 작업 로그](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager 템플릿 배포 로그
사용자 환경 또는 가상 컴퓨터를 만든 경우 자동화를 통해 오류 정보를 검색할 마지막 위치 하나 있습니다. Azure Resource Manager 템플릿 배포 로그입니다. 자동화를 통해 랩 리소스를 만들면 Azure Resource Manager 템플릿 배포를 통해 주로 수행 됩니다. 참조[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) DevTest Labs 리소스를 만드는 샘플 Azure Resource Manager 템플릿에 대 한 합니다.

랩 템플릿 배포 로그를 확인 하려면 다음이 단계를 수행 합니다.

1. 랩이 존재 하는 리소스 그룹에 대 한 페이지를 시작 합니다.
2. 선택 **배포** 의 왼쪽된 메뉴에서 **설정**합니다.
3. 실패 한 상태와 함께 배포 찾아서 선택 합니다.
4. 에 **배포** 페이지에서 **작업 세부 정보** 실패 한 작업에 대 한 링크입니다.
5. 실패 한 작업에 대 한 정보를 표시 합니다 **작업 세부 정보** 창입니다.

## <a name="next-steps"></a>다음 단계
참조 [아티팩트 실패 문제 해결](devtest-lab-troubleshoot-artifact-failure.md)