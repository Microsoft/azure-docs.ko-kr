---
title: Azure에서 Windows Vm에 대 한 예약 된 이벤트 모니터링
description: 예약 된 이벤트에 대 한 Azure 가상 머신을 모니터링 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073307"
---
# <a name="monitoring-scheduled-events"></a>모니터링 Scheduled Events

업데이트는 매일 Azure의 서로 다른 부분에 적용 되어 서비스를 안전 하 게 실행 하 고 최신 상태로 유지 합니다. 계획 된 업데이트 외에도 계획 되지 않은 이벤트가 발생할 수 있습니다. 예를 들어 하드웨어 성능 저하 또는 오류가 감지 되 면 Azure 서비스는 계획 되지 않은 유지 관리를 수행 해야 할 수 있습니다. 실시간 마이그레이션을 사용 하 여 메모리를 유지 하는 업데이트를 유지 하 고 일반적으로 업데이트의 영향에 대해 엄격한 막대를 유지 합니다. 대부분의 경우 이러한 이벤트는 고객에 게 거의 영향을 주지 않으며, 영향을 주지 않거나 최대 몇 초 동안 가상 머신이 중지 될 수 있습니다. 그러나 일부 응용 프로그램의 경우 가상 머신 고정의 몇 초 까지도 영향을 받을 수 있습니다. 이러한 응용 프로그램에 대 한 최상의 환경을 보장 하기 위해 예정 된 Azure 유지 관리를 미리 파악 하는 것이 중요 합니다. [Scheduled Events 서비스](scheduled-events.md) 는 예정 된 유지 관리에 대 한 알림이 제공 되는 프로그래밍 인터페이스를 제공 하 고 유지 관리를 정상적으로 처리할 수 있도록 합니다. 

이 문서에서는 예약 된 이벤트를 사용 하 여 Vm에 영향을 미칠 수 있는 유지 관리 이벤트에 대 한 통지를 받고 모니터링 및 분석에 도움이 되는 몇 가지 기본 자동화를 구축할 수 있는 방법을 보여 줍니다.


## <a name="routing-scheduled-events-to-log-analytics"></a>Log Analytics 예약 된 이벤트 라우팅

Scheduled Events는 azure [Instance Metadata Service](instance-metadata-service.md)의 일부로 제공 되며, 모든 azure 가상 머신에서 사용할 수 있습니다. 고객은 가상 머신의 엔드포인트을 쿼리하여 예약 된 유지 관리 알림을 찾고, 상태를 저장 하 고 가상 머신을 순환에서 제외 하는 등의 완화 작업을 수행할 수 있도록 자동화를 작성할 수 있습니다. Azure 유지 관리 이벤트의 감사 로그를 사용할 수 있도록 Scheduled Events를 기록 하는 자동화를 작성 하는 것이 좋습니다. 

이 문서에서는 Log Analytics에 유지 관리 Scheduled Events를 캡처하는 방법을 안내 합니다. 그런 다음 팀에 전자 메일을 보내고 가상 컴퓨터에 영향을 준 모든 이벤트의 기록 보기를 가져오는 등의 몇 가지 기본적인 알림 작업을 트리거합니다. 이벤트 집계 및 자동화를 위해 [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace)를 사용 하지만 모든 모니터링 솔루션을 사용 하 여 이러한 로그를 수집 하 고 자동화를 트리거할 수 있습니다.

![이벤트 수명 주기를 보여 주는 다이어그램](./media/notifications/events.png)

## <a name="prerequisites"></a>선행 조건

이 예에서는 [가용성 집합에 Windows 가상 머신을](tutorial-availability-sets.md)만들어야 합니다. 가용성 집합, 클라우드 서비스, 가상 머신 확장 집합 또는 독립 실행형 Vm의 가상 머신에 영향을 줄 수 있는 변경 내용에 대 한 알림을 제공 Scheduled Events 합니다. 가용성 집합의 다른 모든 Vm에 대 한 이벤트를 가져오기 위해 수집기 역할을 하는 Vm 중 하나에서 예약 된 이벤트를 폴링하는 [서비스](https://github.com/microsoft/AzureScheduledEventsService) 를 실행 합니다.    

자습서의 끝에서 그룹 리소스 그룹을 삭제 하지 마세요.

또한 가용성 집합의 Vm에서 정보를 집계 하는 데 사용할 [Log Analytics 작업 영역을 만들어야](/azure/azure-monitor/learn/quick-create-workspace) 합니다.

## <a name="set-up-the-environment"></a>환경 설정

이제 가용성 집합에 두 개의 초기 Vm이 있어야 합니다. 이제 동일한 가용성 집합에 myCollectorVM 라는 세 번째 VM을 만들어야 합니다. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)에서 프로젝트의 설치 .zip 파일을 다운로드 합니다.

**MyCollectorVM** 에 연결 하 고 .zip 파일을 가상 머신에 복사 하 고 모든 파일을 추출 합니다. VM에서 PowerShell 프롬프트를 엽니다. 프롬프트를 `SchService.ps1`포함 된 폴더로 이동 합니다 (예: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`, 서비스 설정).

```powershell
.\SchService.ps1 -Setup
```

서비스를 시작 합니다.

```powershell
.\SchService.ps1 -Start
```

이제 서비스는 예약 된 이벤트에 대해 10 초 마다 폴링을 시작 하 고 이벤트를 승인 하 여 유지 관리를 신속 하 게 진행 합니다.  중지, 다시 부팅, 다시 배포 및 Preempt는 일정 이벤트에 의해 캡처된 이벤트입니다.   이벤트를 승인 하기 전에 스크립트를 확장 하 여 일부 완화를 트리거할 수 있습니다.

서비스 상태를 확인 하 고 실행 중인지 확인 합니다.

```powershell
.\SchService.ps1 -status  
```

`Running`를 반환 해야 합니다.

이제 서비스는 예약 된 이벤트에 대해 10 초 마다 폴링을 시작 하 고 이벤트를 승인 하 여 유지 관리를 신속 하 게 진행 합니다.  중지, 다시 부팅, 다시 배포 및 Preempt는 일정 이벤트에 의해 캡처된 이벤트입니다. 이벤트를 승인 하기 전에 스크립트를 확장 하 여 일부 완화를 트리거할 수 있습니다.

위의 이벤트가 일정 이벤트 서비스로 캡처될 경우 응용 프로그램 이벤트 로그 이벤트 상태, 이벤트 유형, 리소스 (가상 머신 이름) 및 NotBefore (최소 알림 기간)에 기록 됩니다. 응용 프로그램 이벤트 로그에서 ID가 1234 인 이벤트를 찾을 수 있습니다.

서비스를 설정 하 고 시작 하면 Windows 응용 프로그램 로그에 이벤트가 기록 됩니다.   이 작업을 확인 하려면 가용성 집합의 가상 머신 중 하나를 다시 시작 합니다. 그러면 VM이 다시 시작 되었음을 나타내는 응용 프로그램 로그 > Windows 로그의 이벤트 뷰어에 이벤트가 기록 됩니다. 

![이벤트 뷰어의 스크린샷](./media/notifications/event-viewer.png)

이벤트가 일정 이벤트 서비스에 의해 캡처될 때 이벤트 상태, 이벤트 유형, 리소스 (VM 이름) 및 NotBefore (최소 알림 기간)이 포함 된 로그도 응용 프로그램에 기록 됩니다. 응용 프로그램 이벤트 로그에서 ID가 1234 인 이벤트를 찾을 수 있습니다.

> [!NOTE] 
> 이 예제에서는 가상 컴퓨터가 가용성 집합에 있으며,이를 통해 단일 가상 컴퓨터를 수신 대기 하 고 log analytics 작업 공간으로 라우팅하는 데 사용할 수 있는 단일 가상 컴퓨터를 수집기로 지정할 수 있습니다. 독립 실행형 가상 컴퓨터가 있는 경우 모든 가상 컴퓨터에서 서비스를 실행 한 다음 log analytics 작업 영역에 개별적으로 연결할 수 있습니다.
>
> 설치를 위해 Windows를 선택 했지만 Linux에서 비슷한 솔루션을 디자인할 수 있습니다.

언제 든 지 `–stop` 및 `–remove`스위치를 사용 하 여 예약 된 이벤트 서비스를 중지/제거할 수 있습니다.

## <a name="connect-to-the-workspace"></a>작업 영역에 연결


이제 Log Analytics 작업 영역을 수집기 VM에 연결 하려고 합니다. Log Analytics 작업 영역은 리포지토리 역할을 하며, 수집기 VM에서 응용 프로그램 로그를 캡처하도록 이벤트 로그 수집을 구성 합니다. 

 서비스에서 응용 프로그램 로그로 저장 되는 이벤트 로그에 Scheduled Events를 라우팅하려면 Log Analytics 작업 영역에 가상 컴퓨터를 연결 해야 합니다.  
 
1. 만든 작업 영역에 대 한 페이지를 엽니다.
1. **데이터 원본에 연결** 아래에서 **Azure vm (가상 머신)** 을 선택 합니다.

    ![데이터 원본으로 VM에 연결](./media/notifications/connect-to-data-source.png)

1. **MyCollectorVM**를 검색 하 고 선택 합니다. 
1. **MyCollectorVM**에 대 한 새로 만들기 페이지에서 **연결**을 선택 합니다.

그러면 가상 컴퓨터에 [Microsoft Monitoring agent](/azure/virtual-machines/extensions/oms-windows) 가 설치 됩니다. VM을 작업 영역에 연결 하 고 확장을 설치 하는 데 몇 분 정도 걸립니다. 

## <a name="configure-the-workspace"></a>작업 영역 구성

1. 작업 영역에 대 한 페이지를 열고 **고급 설정**을 선택 합니다.
1. 왼쪽 메뉴에서 **데이터** 를 선택 하 고 **Windows 이벤트 로그**를 선택 합니다.
1. **다음 이벤트 로그에서 수집**을 시작 하 고 *응용 프로그램* 을 입력 한 다음 목록에서 **응용 프로그램** 을 선택 합니다.

    ![고급 설정 선택](./media/notifications/advanced.png)

1. **오류**, **경고**및 **정보** 를 선택한 채로 두고 **저장** 을 선택 하 여 설정을 저장 합니다.


> [!NOTE]
> 약간의 지연이 있으며 로그를 사용할 수 있을 때까지 최대 10 분이 소요 될 수 있습니다. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Azure Monitor를 사용 하 여 경고 규칙 만들기 


이벤트가 Log Analytics 푸시 된 후 다음 [쿼리](/azure/azure-monitor/log-query/get-started-portal) 를 실행 하 여 일정 이벤트를 찾을 수 있습니다.

1. 페이지 위쪽에서 **로그** 를 선택 하 고 텍스트 상자에 다음을 붙여넣습니다.

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. **저장**을 선택한 다음 이름에 *대해 logquery* 를 입력 하 고 **쿼리** 를 유형으로 유지 한 다음 *Vmlogs* 를 **범주로**입력 하 고 **저장**을 선택 합니다. 

    ![쿼리 저장](./media/notifications/save-query.png)

1. **새로운 경고 규칙**을 선택합니다. 
1. **규칙 만들기** 페이지에서 `collectorworkspace`를 **리소스로**유지 합니다.
1. **조건**아래에서 *고객 로그 검색이 <login undefined>될 때마다* 항목을 선택 합니다. **신호 논리 구성** 페이지가 열립니다.
1. **임계값**에서 *0* 을 입력 한 다음 **완료**를 선택 합니다.
1. **작업**아래에서 **작업 그룹 만들기**를 선택 합니다. **작업 그룹 추가** 페이지가 열립니다.
1. **작업 그룹 이름**에 *myactiongroup*을 입력 합니다.
1. **약식 이름**에 **myactiongroup**을 입력 합니다.
1. **리소스 그룹**에서 **Myresourcegroupavailability**를 선택 합니다.
1. 작업 아래의 **동작 이름** 에 **전자 메일**을 입력 하 고 **전자 메일/SMS/푸시/음성**을 선택 합니다. **이메일/SMS/푸시/음성** 페이지가 열립니다.
1. 전자 **메일을 선택 하**고 전자 메일 주소를 입력 한 다음 **확인**을 선택 합니다.
1. **작업 그룹 추가** 페이지에서 **확인**을 선택 합니다. 
1. **규칙 만들기** 페이지의 **경고 세부 정보**에서 **경고 규칙 이름**에 *Myalert* 를 입력 하 고 **설명**에 *전자 메일 경고 규칙* 을 입력 합니다.
1. 작업이 완료 되 면 **경고 규칙 만들기**를 선택 합니다.
1. 가용성 집합에서 Vm 중 하나를 다시 시작 합니다. 몇 분 이내에 경고가 트리거된 전자 메일을 받게 됩니다.

경고 규칙을 관리 하려면 리소스 그룹으로 이동 하 여 왼쪽 메뉴에서 **경고** 를 선택한 다음 페이지 맨 위에서 **경고 규칙 관리** 를 선택 합니다.

     
## <a name="next-steps"></a>다음 단계

자세히 알아보려면 GitHub의 [예약 된 이벤트 서비스](https://github.com/microsoft/AzureScheduledEventsService) 페이지를 참조 하세요.
