---
title: Azure에서 Linux VM에 예정된 이벤트 | Microsoft Docs
description: Linux 가상 머신에서 Azure 메타데이터 서비스를 사용하여 이벤트를 예약합니다.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 6337477b55addefb7579d6f328473428ba72ba24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799258"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata 서비스: Linux VM의 예약된 이벤트

예약된 이벤트는 애플리케이션이 VM(가상 머신) 유지 관리를 준비할 시간을 부여하는 Azure Metadata Service입니다. 향후 유지 관리 이벤트(예: 재부팅)에 대한 정보를 제공하여 애플리케이션이 이에 대비하고 서비스 중단을 제한할 수 있도록 합니다. 이 기능은 Windows와 Linux 모두에서 PaaS 및 IaaS를 포함한 모든 Azure Virtual Machines 유형에 사용할 수 있습니다. 

Windows에서 예약된 이벤트에 대한 자세한 내용은 [Windows VM에 예약된 이벤트](../windows/scheduled-events.md)를 참조하세요.

> [!Note] 
> 예약된 이벤트는 모든 Azure 지역에서 일반 공급됩니다. 최신 릴리스 정보는 [버전 및 지역 가용성](#version-and-region-availability)을 참조하세요.

## <a name="why-use-scheduled-events"></a>예약된 이벤트를 사용하는 이유는?

많은 애플리케이션에서 VM 유지 관리를 준비하는 시간을 활용할 수 있습니다. 이 시간은 가용성, 안정성 및 서비스 가능성을 향상시키는 다음을 비롯한 특정 애플리케이션 작업을 수행하는 데 사용할 수 있습니다. 

- 검사점 및 복원
- 연결 드레이닝
- 주 복제본 장애 조치(failover)
- 부하 분산 장치 풀에서 제거
- 이벤트 로깅
- 정상 종료

예약된 이벤트를 사용하면 애플리케이션에서 유지 관리가 발생하는 시간을 검색하고 이로 인한 영향을 제한하는 작업을 트리거할 수 있습니다.  

예약된 이벤트는 다음과 같은 경우에 이벤트를 제공합니다.

- 플랫폼 시작 유지 관리(예: 호스트 OS 업데이트)
- 성능이 저하 된 하드웨어
- 사용자가 시작하는 유지 관리(예: 사용자가 VM을 다시 시작하거나 다시 배포)
- [우선 순위가 낮은 VM 제거](https://azure.microsoft.com/en-us/blog/low-priority-scale-sets) 크기 조정 설정

## <a name="the-basics"></a>기본 사항  

  메타데이터 서비스는 VM 내에서 액세스할 수 있는 REST 엔드포인트를 사용하여 VM을 실행하는 정보를 공개합니다. 이 정보는 라우팅할 수 없는 IP를 통해 사용할 수 있으므로 VM 외부에 공개되지 않습니다.

### <a name="scope"></a>범위
예약된 이벤트는 다음으로 전달됩니다.

- 클라우드 서비스의 모든 VM
- 가용성 집합의 모든 VM
- 확장 집합 배치 그룹의 모든 VM 

따라서 이벤트의 `Resources` 필드를 확인하여 영향을 받는 VM을 식별합니다.

### <a name="endpoint-discovery"></a>엔드포인트 검색
VNET 사용 VM의 경우 메타데이터 서비스를 정적 경로 조정 불가능 IP `169.254.169.254`에서 사용할 수 있습니다. 예약된 이벤트의 최신 버전에 대한 전체 엔드포인트는 다음과 같습니다. 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

클라우드 서비스 및 클래식 VM의 기본 사례처럼 VM이 가상 네트워크에 생성되지 않은 경우 사용할 IP 주소를 검색하려면 추가 논리가 필요합니다. [호스트 엔드포인트를 검색](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)하는 방법을 알아보려면 이 샘플을 참조하세요.

### <a name="version-and-region-availability"></a>버전 및 지역 가용성
예약된 이벤트 서비스의 버전이 지정됩니다. 버전은 필수이며 현재 버전은 `2017-11-01`입니다.

| Version | 릴리스 종류 | 영역 | 릴리스 정보 | 
| - | - | - | - | 
| 2017-11-01 | 일반 공급 | 모두 | <li> 우선 순위가 낮은 VM 제거 EventType 'Preempt'에 대 한 지원 추가<br> | 
| 2017-08-01 | 일반 공급 | 모두 | <li> IaaS VM의 리소스 이름에서 앞에 붙은 밑줄이 제거됨<br><li>모든 요청에 대해 메타데이터 헤더 요구 사항이 적용됨 | 
| 2017-03-01 | 미리 보기 | 모두 | <li>최초 릴리스


> [!NOTE] 
> 예약된 이벤트의 이전 미리 보기 릴리스는 api-version으로 {latest}를 지원했습니다. 이 형식은 더 이상 지원되지 않으며 향후 사용되지 않을 예정입니다.

### <a name="enabling-and-disabling-scheduled-events"></a>예약된 이벤트 사용 및 사용 안 함
예약된 이벤트는 이벤트에 대한 요청을 처음 수행하는 서비스에 대해 사용할 수 있습니다. 최대 2분인 첫 번째 호출에서 지연된 응답을 예상해야 합니다.

24시간 동안 요청을 수행하지 않으면 예약된 이벤트를 서비스에 사용할 수 없습니다.

### <a name="user-initiated-maintenance"></a>사용자 시작 유지 관리
Azure Portal, API, CLI 또는 PowerShell을 통한 사용자 시작 VM 유지 관리로 인해 예정된 이벤트가 발생합니다. 그러면 애플리케이션에서 유지 관리 준비 논리를 테스트할 수 있으며 애플리케이션에서는 사용자 시작 유지 관리를 준비할 수 있습니다.

VM을 다시 시작하는 경우 `Reboot` 형식인 이벤트가 예약됩니다. VM을 다시 배포하는 경우 `Redeploy` 형식인 이벤트가 예약됩니다.

## <a name="use-the-api"></a>API 사용

### <a name="headers"></a>헤더
메타데이터 서비스를 쿼리할 때 요청이 실수로 리디렉션되지 않도록 `Metadata:true` 헤더를 제공해야 합니다. `Metadata:true` 헤더는 모든 예약된 이벤트 요청에 필요합니다. 헤더를 요청에 포함하지 않으면 메타데이터 서비스에서 "잘못된 요청" 응답이 발생합니다.

### <a name="query-for-events"></a>이벤트 쿼리
다음과 같이 호출하여 예약된 이벤트를 쿼리할 수 있습니다.

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

응답에는 예약된 이벤트의 배열이 포함됩니다. 빈 배열은 현재 예약된 이벤트가 없음을 의미합니다.
예약된 이벤트가 있는 경우 응답에 다음과 같은 이벤트의 배열이 포함됩니다. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>이벤트 속성
|자산  |  설명 |
| - | - |
| EventId | 이 이벤트의 GUID(Globally Unique Identifier)입니다. <br><br> 예제: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 이 이벤트로 인해 발생하는 결과입니다. <br><br> 값 <br><ul><li> `Freeze`: 몇 초 동안 Virtual Machine을 일시 중지하도록 예약됩니다. CPU가 일시 중단되지만 메모리, 열려 있는 파일 또는 네트워크 연결에는 영향을 미치지 않습니다. <li>`Reboot`: Virtual Machine을 다시 부팅하도록 예약합니다(비영구 메모리가 손실됨). <li>`Redeploy`: Virtual Machine을 다른 노드로 이동하도록 예약합니다(임시 디스크가 손실됨). <li>`Preempt`: 우선 순위가 낮은 가상 컴퓨터를 삭제 하는 중 (임시 디스크 손실 됨).|
| ResourceType | 이 이벤트가 영향을 주는 리소스 형식입니다. <br><br> 값 <ul><li>`VirtualMachine`|
| 리소스| 이 이벤트가 영향을 주는 리소스 목록입니다. 이 목록은 하나의 [업데이트 도메인](manage-availability.md)에서 컴퓨터를 포함하도록 보장하지만 UD의 모든 컴퓨터를 포함할 수는 없습니다. <br><br> 예제: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | 이 이벤트의 상태입니다. <br><br> 값 <ul><li>`Scheduled`: `NotBefore` 속성에 지정된 시간 이후 시작하도록 이 이벤트를 예약합니다.<li>`Started`: 이 이벤트가 시작되었습니다.</ul> `Completed` 또는 유사한 상태가 제공되지 않았습니다. 이벤트가 완료되면 더 이상 반환되지 않습니다.
| NotBefore| 이 시간이 지난 후 이 이벤트가 시작될 수 있습니다. <br><br> 예제: <br><ul><li> 2016년 9월 19일 월요일 18:29:47 GMT  |

### <a name="event-scheduling"></a>이벤트 예약
각 이벤트는 이벤트 유형에 따라 향후 최소한의 시간으로 예약됩니다. 이 시간은 이벤트의 `NotBefore` 속성에 반영됩니다. 

|EventType  | 최소 공지 |
| - | - |
| 중지| 15분 |
| Reboot | 15분 |
| 재배포 | 10분 |
| 선점 | 30초 |

### <a name="start-an-event"></a>이벤트 시작 

예정된 이벤트에 대해 알게 되고 정상 종료를 위한 논리를 완료한 후에 `EventId`로 메타데이터 서비스에 대한 `POST` 호출을 실행하여 처리 중인 이벤트를 승인할 수 있습니다. 이 호출은 가능한 경우 Azure에 최소 알림 시간을 단축할 수 있음을 나타냅니다. 

다음과 같은 JSON 샘플은 `POST` 요청 본문에 필요합니다. 요청에 `StartRequests` 목록이 포함되어야 합니다. 각 `StartRequest`는 빠르게 처리할 이벤트의 `EventId`를 포함합니다.
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash 샘플
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> 이벤트를 승인하면 해당 이벤트를 승인한 VM뿐만 아니라 이벤트의 모든 `Resources`에 대해 이벤트가 진행됩니다. 따라서 승인을 조정할 리더를 선택할 수 있으며, 이는 `Resources` 필드의 첫 번째 컴퓨터처럼 간단할 수 있습니다.

## <a name="python-sample"></a>Python 샘플 

다음 샘플은 예약된 이벤트에 대한 메타데이터 서비스를 쿼리하고 처리 중인 각 이벤트를 승인합니다.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>다음 단계 
- [Azure Friday에서 예약된 이벤트](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)를 보고 데모를 확인합니다. 
- [Azure 인스턴스 메타데이터 예약된 이벤트 GitHub 리포지토리](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)에서 예약된 이벤트 코드 샘플을 검토합니다.
- [인스턴스 메타데이터 서비스](instance-metadata-service.md)에서 사용 가능한 API에 대해 자세히 알아봅니다.
- [Azure에서 Linux 가상 머신에 대한 계획된 유지 관리](planned-maintenance.md)에 대해 알아봅니다.
