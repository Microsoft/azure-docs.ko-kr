---
title: 자습서 - Azure Portal을 사용하여 네트워크 통신 모니터링
description: 이 자습서에서는 Azure Network Watcher의 연결 모니터 기능이 있는 두 가상 머신 간의 네트워크 통신을 모니터링하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 188efd630f1b497815f86a9249d31e823f45a8ce
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112081716"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 두 개의 가상 머신 간의 네트워크 통신 모니터링

> [!NOTE]
> 이 자습서에서는 연결 모니터(클래식)를 다룹니다. 향상된 연결 모니터링을 경험하려면 새롭고 향상된 [연결 모니터](connection-monitor-overview.md)를 사용해 보세요.

> [!IMPORTANT]
> 2021년 7월 1일부터 연결 모니터(클래식)에 새 연결 모니터를 추가할 수 없지만, 2021년 7월 1일 이전에 만든 기존 연결 모니터는 계속 사용할 수 있습니다. 현재 워크로드에 대한 서비스 중단을 최소화하려면 2024년 2월 29일 이전에 연결 모니터(클래식)에서 [Azure Network Watcher의 새 연결 모니터로 마이그레이션](migrate-to-connection-monitor-from-connection-monitor-classic.md)합니다.

VM(가상 머신)과 다른 VM과 같은 엔드포인트 간의 통신의 성공은 조직에 대해 중요할 수 있습니다. 경우에 따라 통신을 중단할 수 있는 구성 변경 내용이 도입됩니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 두 대의 VM 만들기
> * Network Watcher의 연결 모니터 기능을 사용하여 VM 간의 통신 모니터링
> * 연결 모니터 메트릭에 대한 경고 생성
> * 두 VM 간의 통신 문제를 진단하고 해결 방법에 대해 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.



## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-vms"></a>VM 만들기

두 대의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기** 를 선택합니다.
2. **컴퓨팅** 을 선택한 다음, 운영 체제를 선택합니다. 이 자습서에서는 **Windows Server 2016 Datacenter** 를 사용합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인** 을 선택합니다.

    |설정|값|
    |---|---|
    |속성|myVm1|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.yml?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다.|
    |Subscription| 구독을 선택합니다.|
    |Resource group| **새로 만들기** 를 선택하고 **myResourceGroup** 을 입력합니다.|
    |위치| **미국 동부** 를 선택합니다.|

4. VM에 대한 크기를 선택한 다음, **선택** 을 선택합니다.
5. **설정** 에서 **확장** 을 선택합니다. 다음 그림에 표시된 대로 **확장 추가** 를 선택하고, **Windows용 Network Watcher 에이전트** 를 선택합니다.

    ![Network Watcher 에이전트 확장](./media/connection-monitor/nw-agent-extension.png)

6. **Windows용 Network Watcher 에이전트** 에서 **만들기** 를 선택하고, **확장 설치** 에서 **확인** 을 선택한 다음, **확장** 에서 **확인** 을 선택합니다.
7. 나머지 **설정** 에 대한 기본값은 수락하고, **확인** 을 선택합니다.
8. **요약** 의 **만들기** 아래에서 **만들기** 를 선택하여 VM 배포를 시작합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

다음 변경 내용으로 [첫 번째 VM 만들기](#create-the-first-vm)의 단계를 다시 완료합니다.

|단계|설정|값|
|---|---|---|
| 1 | **Ubuntu Server** 버전 선택 |                                                                         |
| 3 | 속성                                  | myVm2                                                                   |
| 3 | 인증 유형                   | SSH 공개 키를 붙여넣거나 **암호** 를 선택하고 암호를 입력합니다. |
| 3 | Resource group                        | **기존 항목 사용** 을 선택하고, **myResourceGroup** 을 선택합니다.                 |
| 6 | 확장                            | **Linux용 Network Watcher 에이전트**                                             |

VM을 배포하는 데 몇 분이 걸립니다. 나머지 단계를 계속하기 전에 VM이 배포를 완료하도록 기다립니다.

## <a name="create-a-connection-monitor"></a>연결 모니터 만들기

연결 모니터를 만들어서 TCP 포트 22를 통해 *myVm1* 에서 *myVm2* 로 통신을 모니터링합니다.

1. 포털의 왼쪽에서 **모든 서비스** 를 선택합니다.
2. **필터** 상자에 *Network Watcher* 를 입력합니다. 검색 결과에 **Network Watcher** 가 나타나면 이를 선택합니다.
3. **모니터링** 아래에서 **연결 모니터** 를 선택합니다.
4. **+추가** 를 선택합니다.
5. 모니터링하려는 연결에 대한 정보를 입력하거나 선택한 다음, **추가** 를 선택합니다. 다음 그림에 나와 있는 예제에서는 포트 22를 통해 *myVm1* VM에서 *myVm2* VM으로 연결을 모니터링합니다.

    | 설정                  | 값               |
    | ---------                | ---------           |
    | 속성                     | myVm1-myVm2(22)     |
    | 원본                   |                     |
    | 가상 머신          | myVm1               |
    | 대상              |                     |
    | 가상 머신 선택 |                     |
    | 가상 머신          | myVm2               |
    | 포트                     | 22                  |

    ![연결 모니터 추가](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>연결 모니터 보기

1. 연결 모니터링을 보려면 [연결 모니터 만들기](#create-a-connection-monitor)의 1-3단계를 완료합니다. 다음 그림에 나와 있는 대로 기존 연결 모니터의 목록이 표시됩니다.

    ![연결 모니터](./media/connection-monitor/connection-monitors.png)

2. 다음 그림과 같이 **myVm1-myVm2(22)** 라는 이름의 모니터를 선택하여 모니터에 대한 자세한 내용을 확인합니다.

    ![모니터 세부 정보](./media/connection-monitor/vm-monitor.png)

    다음 정보에 유의하세요.

    | 항목                     | 값                      | 세부 정보                                                     |
    | ---------                | ---------                  |--------                                                     |
    | 상태                   | 연결 가능                  | 엔드포인트가 연결할 수 있는지 여부를 알 수 있습니다.|
    | 평균 라운드트립          | 연결하기 위한 라운드트립 시간(밀리초)을 알 수 있습니다. 연결 모니터는 시간에 따른 대기 시간을 모니터링할 수 있도록 60초마다 연결을 조사합니다.                                         |
    | Hops                     | 연결 모니터를 통해 두 개의 엔드포인트 간에 홉을 알 수 있습니다. 이 예제에서는 동일한 가상 네트워크의 두 VM 간이 연결됩니다. 따라서 10.0.0.5 IP 주소에 대한 하나의 홉만이 있습니다. 예를 들어 기존 시스템 또는 사용자 지정 경로, VPN Gateway를 통한 VM 간의 경로 트래픽 또는 가상 네트워크 어플라이언스의 경우 추가 홉이 나열됩니다.                                                                                                                         |
    | 상태                   | 각 엔트포인트에 대한 녹색 확인 표시는 각 엔드포인트의 상태가 정상인지를 알려줍니다.    ||

## <a name="generate-alerts"></a>경고 생성

경고는 Azure Monitor에서 경고 규칙에 의해 생성되고 정기적으로 저장된 쿼리 또는 사용자 지정 로그 검색을 자동으로 실행할 수 있습니다. 생성된 경고는 누군가에게 알려주거나 다른 프로세스를 시작하기 위해 하나 이상의 작업을 자동으로 실행할 수 있습니다. 경고 규칙을 설정하면 대상 리소스에 따라 경고를 생성하는 데 사용할 수 있는 메트릭 목록이 달라집니다.

1. Azure Portal에서 **모니터** 서비스를 선택한 다음, **경고** > **새 경고 규칙** 을 선택합니다.
2. **대상 선택** 을 클릭한 다음, 대상으로 지정하려는 리소스를 선택합니다. **구독** 을 선택하고 **리소스 종류** 를 설정하여 사용하려는 연결 모니터로 필터링합니다.

    ![대상이 선택된 경고 화면](./media/connection-monitor/set-alert-rule.png)
1. 대상으로 지정할 리소스를 선택했으면 **조건 추가** 를 선택합니다. Network Watcher에는 [경고를 만들 때 사용할 수 있는 메트릭](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)이 있습니다. **사용 가능한 신호** 를 ProbesFailedPercent 및 AverageRoundtripMs 메트릭으로 설정합니다.

    ![신호가 선택된 경고 페이지](./media/connection-monitor/set-alert-signals.png)
1. 경고 규칙 이름, 설명, 심각도 등의 경고 세부 정보를 입력합니다. 또한 경고에 작업 그룹을 추가하여 경고 응답을 자동화하고 사용자 지정할 수도 있습니다.

## <a name="view-a-problem"></a>문제 보기

기본적으로 Azure을 사용하면 동일한 가상 네트워크의 VM 간에 모든 포트를 통한 통신을 허용합니다. 시간에 따라 조직의 사용자는 통신 오류로 인해 발생한 Azure의 기본 규칙을 재정의할 수 있습니다. 통신 문제를 만든 다음, 연결 모니터를 다시 보려면 다음 단계를 완료합니다.

1. 포털 맨 위에 있는 검색 상자에 *myResourceGroup* 을 입력합니다. 검색 결과에서 **myResourceGroup** 리소스 그룹이 표시되면 이를 선택합니다.
2. **myVm2-nsg** 네트워크 보안 그룹을 선택합니다.
3. 다음 그림에 표시된 대로 **인바운드 보안 규칙** 을 선택한 다음, **추가** 를 선택합니다.

    ![인바운드 보안 규칙](./media/connection-monitor/inbound-security-rules.png)

4. 가상 네트워크에 있는 모든 VM 간의 통신을 허용하는 기본 규칙은 **AllowVnetInBound** 라는 규칙입니다. 포트 22를 통한 인바운드 통신을 거부하는 **AllowVnetInBound** 규칙보다 높은 우선 순위(낮은 번호)를 가진 규칙을 만듭니다. 다음 정보를 선택하거나 입력하고, 나머지 기본값을 그대로 적용한 다음, **추가** 를 선택합니다.

    | 설정                 | 값          |
    | ---                     | ---            |
    | 대상 포트 범위 | 22             |
    | 작업                  | 거부           |
    | 우선 순위                | 100            |
    | 속성                    | DenySshInbound |

5. 연결 모니터가 60초 간격으로 조사하므로 몇 분 정도를 대기한 다음, 포털의 왼쪽에서 **Network Watcher** 및 **연결 모니터** 를 선택한 다음, **myVm1-myVm2(22)** 모니터링을 다시 선택합니다. 다음 그림에 나와 있는 대로 결과가 이제 달라집니다.

    ![세부 정보 오류 모니터링](./media/connection-monitor/vm-monitor-fault.png)

    **myvm2529** 네트워크 인터페이스의 상태 열에 빨간색 느낌표 아이콘을 확인할 수 있습니다.

6. 상태가 변경된 이유를 알아보려면 이전 그림에서 10.0.0.5를 선택합니다. 연결 모니터는 통신 오류에 대한 이유가 *다음 네트워크 보안 그룹 규칙에 의해 차단된 트래픽: UserRule_DenySshInbound* 임을 알립니다.

    누군가가 4단계에서 만든 보안 규칙을 구현했음을 모르는 경우 규칙이 통신 문제를 일으키는 연결 모니터로부터 알아봅니다. 그런 다음, VM 간의 통신을 복원하려면 규칙을 변경하거나, 재정의하거나, 제거할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup* 을 입력합니다. 검색 결과에 **myResourceGroup** 이 보이면 선택합니다.
2. **리소스 그룹 삭제** 를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup* 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 두 VM 간의 연결을 모니터링하는 방법을 알아보았습니다. 네트워크 보안 그룹 규칙이 VM에 대한 통신을 방해했음을 알아보았습니다. 모니터가 반환할 수 있는 모든 다른 응답에 대해 자세히 알아보려면 [응답 형식](network-watcher-connectivity-overview.md#response)을 참조하세요. 또한 VM, 정규화된 도메인 이름, 균일한 리소스 식별자 또는 IP 주소 간의 연결을 모니터링할 수 있습니다.

가상 네트워크의 리소스가 Azure 가상 네트워크 게이트웨이에 의해 연결된 다른 네트워크 리소스에 있는 리소스와 통신할 수 없는 경우가 있습니다. 가상 네트워크 게이트웨이에서 발생한 문제를 진단하는 방법을 알아보는 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [네트워크 간 통신 문제 진단](diagnose-communication-problem-between-networks.md)
