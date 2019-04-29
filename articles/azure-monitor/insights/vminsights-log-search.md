---
title: VM용 Azure Monitor에서 로그를 쿼리하는 방법(미리 보기) | Microsoft Docs
description: Vm 솔루션에 대 한 azure Monitor 메트릭을 수집 하 고 로그 데이터와이 문서에서는 레코드를 설명 하 고 샘플 쿼리를 포함 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 8b6745a2b9afe8d3101585e3f7a13f2fc978c84a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122594"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor에서 로그를 쿼리하는 방법(미리 보기)
Vm에 대 한 azure Monitor는 성능 및 연결 메트릭, 컴퓨터 및 프로세스 인벤토리 데이터 및 상태 정보를 수집 하 고 Azure Monitor에서 Log Analytics 작업 영역으로 전달 합니다.  이 데이터를 사용할 수 [쿼리](../../azure-monitor/log-query/log-query-overview.md) Azure Monitor에서. 마이그레이션 계획, 용량 분석, 검색 및 주문형 성능 문제 해결을 포함하는 시나리오에 이 데이터를 적용할 수 있습니다.

## <a name="map-records"></a>레코드 매핑
프로세스 또는 컴퓨터가 시작되거나 VM용 Azure Monitor 맵 기능에 등록된 경우 생성되는 레코드 외에도 고유한 각 컴퓨터 및 프로세스에 대해 시간당 하나의 레코드가 생성됩니다. 이러한 레코드는 다음 표의 속성을 가집니다. ServiceMapComputer_CL 이벤트의 필드 및 값은 ServiceMap Azure Resource Manager API의 컴퓨터 리소스 필드에 매핑됩니다. ServiceMapProcess_CL 이벤트의 필드 및 값은 ServiceMap Azure Resource Manager API의 프로세스 리소스 필드에 매핑됩니다. ResourceName_s 필드는 해당하는 Resource Manager 리소스의 이름 필드와 일치합니다. 

고유한 프로세스 및 컴퓨터를 식별하는 데 사용할 수 있는 내부적으로 생성된 속성이 있습니다.

- 컴퓨터: *ResourceId* 또는 *ResourceName_s*를 사용하여 Log Analytics 작업 영역 내에서 컴퓨터를 고유하게 식별합니다.
- 프로세스: *ResourceId*를 사용하여 Log Analytics 작업 영역 내에서 프로세스를 고유하게 식별합니다. *ResourceName_s*는 프로세스가 실행 중인 머신(MachineResourceName_s)의 컨텍스트 내에서 고유합니다. 

지정된 시간 범위 내에서 지정된 프로세스 및 컴퓨터에 대해 여러 레코드가 존재할 수 있으므로 쿼리는 동일한 컴퓨터 또는 프로세스에 대해 둘 이상의 레코드를 반환할 수 있습니다. 가장 최근 레코드만 포함하려면 쿼리에 "| dedup ResourceId"를 추가합니다.

### <a name="connections-and-ports"></a>연결 및 포트
연결 메트릭 기능은 Azure Monitor 로그 VMConnection VMBoundPort에서에서 두 개의 새 테이블을 소개합니다. 이러한 표에서 포트 열기/활성 하는 서버 뿐만 아니라 (인바운드 및 아웃 바운드) 컴퓨터에 대 한 연결에 대 한 정보를 제공 합니다. ConnectionMetrics 기간 동안 특정 메트릭을 가져올 수 있는 방법을 제공 하는 Api를 통해 노출 됩니다. TCP 연결에서 발생 *수락* 수신 대기 소켓 아닌에서 생성 하는 동안 바인딩된 *연결* 지정 IP 및 포트에 아웃 바운드 됩니다. 연결의 방향은 **인바운드** 또는 **아웃바운드** 중 하나로 설정할 수 있는 Direction 속성으로 표현됩니다. 

이러한 테이블의 레코드는 종속성 에이전트에서 보고 데이터에서 생성 됩니다. 모든 레코드는 1 분 시간 간격 동안 관찰을 나타냅니다. TimeGenerated 속성은 시간 간격의 시작을 나타냅니다. 각 레코드에는 각 엔터티를 식별하는 정보가 있습니다. 즉, 해당 엔터티에 연결된 메트릭과 함께 연결 또는 포트를 포함합니다. 현재 IPv4를 통해 TCP를 사용하여 발생하는 네트워크 활동만 보고됩니다. 

#### <a name="common-fields-and-conventions"></a>공용 필드 및 규칙 
다음 필드 및 규칙에 VMConnection 및 VMBoundPort 둘 다에 적용 됩니다. 

- 컴퓨터: 컴퓨터를 보고 합니다. 정규화 된 도메인 이름 
- AgentID: Log Analytics 에이전트를 사용 하 여 컴퓨터에 대 한 고유 식별자  
- 컴퓨터: ServiceMap에서 노출 하는 컴퓨터에 대 한 Azure Resource Manager 리소스의 이름입니다. 폼의 것 *m-{GUID}*, 여기서 *GUID* 노드의 에이전트 Id guid와 동일  
- 프로세스: ServiceMap에서 노출 하는 프로세스에 대 한 Azure Resource Manager 리소스의 이름입니다. 폼의 것 *p-{16 진수 문자열}* 합니다. 프로세스는 컴퓨터 범위 내에서 고유 하 고 컴퓨터에서 고유 프로세스 ID를 생성 하려면 컴퓨터와 프로세스 필드를 결합 합니다. 
- ProcessName: 보고 프로세스의 실행 파일 이름입니다.
- 모든 IP 주소는 문자열 형식으로 정식 IPv4 예를 들어 *13.107.3.160* 

비용 및 복잡성을 관리하기 위해 연결 레코드는 개별 물리적 네트워크 연결을 나타내지 않습니다. 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다. 그런 다음, 각 테이블에 반영됩니다.  즉, *VMConnection* 테이블의 레코드는 관찰되는 개별 물리적 연결이 아닌 논리적 그룹화를 나타냅니다. 지정된 1분 간격 동안 다음 특성에 대해 동일한 값을 공유하는 물리적 네트워크 연결이 *VMConnection*의 단일 논리적 레코드에 집계됩니다. 

| 자산 | 설명 |
|:--|:--|
|방향 |연결 방향으로 값은 *인바운드* 또는 *아웃바운드*입니다. |
|컴퓨터 |컴퓨터 FQDN |
|Process |연결을 시작/수락하는 프로세스 또는 프로세스 그룹의 ID입니다. |
|SourceIp |원본의 IP 주소 |
|DestinationIp |대상의 IP 주소 |
|DestinationPort |대상의 포트 번호 |
|프로토콜 |연결에 사용되는 프로토콜입니다.  값은 *tcp*입니다. |

그룹화의 영향을 고려하기 위해 그룹화된 물리적 연결 수에 대한 정보가 다음과 같은 레코드 속성에서 제공됩니다.

| 자산 | 설명 |
|:--|:--|
|LinksEstablished |보고 기간 동안 설정된 물리적 네트워크 연결의 수 |
|LinksTerminated |보고 기간 동안 종료된 물리적 네트워크 연결의 수 |
|LinksFailed |보고 기간 동안 실패한 물리적 네트워크 연결의 수. 이 정보는 현재 아웃바운드 연결에 대해서만 사용할 수 있습니다. |
|LinksLive |보고 기간의 마지막에 시작된 물리적 네트워크 연결의 수|

#### <a name="metrics"></a>메트릭

연결 수 메트릭 외에도 지정된 논리적 연결 또는 네트워크 포트에 전송 및 수신된 데이터의 볼륨에 대한 정보도 다음과 같은 레코드 속성에 포함됩니다.

| 자산 | 설명 |
|:--|:--|
|BytesSent |보고 기간 동안 전송된 총 바이트 수 |
|BytesReceived |보고 기간 동안 수신된 총 바이트 수 |
|응답 |보고 기간 동안 관찰된 응답의 수 
|ResponseTimeMax |보고 기간 동안 관찰된 최대 응답 시간(밀리초). 값이 없는 경우 속성은 비어 있습니다.|
|ResponseTimeMin |보고 기간 동안 관찰된 최소 응답 시간(밀리초). 값이 없는 경우 속성은 비어 있습니다.|
|ResponseTimeSum |보고 기간 동안 관찰된 모든 응답 시간의 합계(밀리초). 값이 없는 경우 속성은 비어 있습니다.|

보고할 데이터의 세 번째 형식은 호출자가 원격 엔드포인트에서 처리 및 응답할 연결을 통해 전송된 요청을 대기하는 데 걸리는 시간인 응답 시간입니다. 보고된 응답 시간은 기본 애플리케이션 프로토콜의 true 응답 시간에 대한 추정치입니다. 물리적 네트워크 연결의 원본 및 대상 끝 사이의 데이터 흐름을 관찰하여 그에 따른 추론 방식을 사용하여 계산됩니다. 개념적으로는 발신자가 요청의 마지막 바이트를 보낸 시간과 응답의 마지막 바이트가 다시 도착할 때의 시간 사이에는 차이가 있습니다. 이러한 두 타임스탬프는 지정된 물리적 연결에서 요청 및 응답 이벤트를 설명하는 데 사용됩니다. 그 차이는 단일 요청의 응답 시간을 나타냅니다. 

이 기능의 이 첫 번째 릴리스에서 해당 알고리즘은 지정된 네트워크 연결에 사용된 실제 애플리케이션 프로토콜에 따라 각기 다른 성공도로 작동할 수도 있는 근사값입니다. 예를 들어, 현재 접근 방식은 HTTP(S)와 같은 프로토콜 기반 요청-응답에는 적합하지만, 단방향 또는 메시지 큐 기반 프로토콜에는 작동하지 않습니다.

고려할 몇 가지 중요한 사항은 다음과 같습니다.

1. 프로세스가 동일한 IP 주소에 있지만 여러 네트워크 인터페이스를 통한 연결을 허용하는 경우 각 인터페이스에 대한 별도 레코드가 보고됩니다. 
2. 와일드카드 IP를 사용한 레코드에는 활동이 포함되지 않습니다. 해당 레코드는 머신에 있는 포트가 인바운드 트래픽에 대해 열려있음을 나타내기 위해 포함됩니다.
3. 자세한 정도 및 데이터 볼륨을 줄이기 위해 와일드카드 IP를 사용한 레코드는 특정 IP 주소와 일치하는 레코드(동일한 프로세스, 포트 및 프로토콜에 대해)가 있는 경우 생략됩니다. 와일드카드 IP 레코드가 생략되면 포트가 보고한 머신의 모든 인터페이스에서 노출됨을 나타내기 위해 특정 IP 주소를 가진 IsWildcardBind 레코드 속성이 “True”로 설정됩니다.
4. 특정 인터페이스에만 바인딩되는 포트에로 IsWildcardBind *False*합니다.

#### <a name="naming-and-classification"></a>이름 지정 및 분류
편의를 위해 연결의 원격 끝 IP 주소는 RemoteIp 속성에 포함됩니다. 인바운드 연결의 경우 RemoteIp는 SourceIp와 동일하고, 아웃바운드 연결의 경우 DestinationIp와 동일합니다. RemoteDnsCanonicalNames 속성은 RemoteIp에 대한 머신에서 보고된 DNS 정식 이름을 나타냅니다. 나중에 사용하기 위해 RemoteDnsQuestions 및 RemoteClassification 속성이 예약되어 있습니다. 

#### <a name="geolocation"></a>지리적 위치
또한 *VMConnection*은 다음과 같은 레코드 속성에서 각 연결 레코드의 원격 끝에 대한 지리적 위치 정보를 포함합니다. 

| 자산 | 설명 |
|:--|:--|
|RemoteCountry |RemoteIp를 호스팅하는 국가 이름입니다.  예: *United States* |
|RemoteLatitude |지리적 위치 위도입니다. 예: *47.68* |
|RemoteLongitude |지리적 위치 경도입니다. 예: *-122.12* |

#### <a name="malicious-ip"></a>악성 IP
*VMConnection* 테이블의 모든 RemoteIp 속성을 알려진 악의적인 활동의 IP 집합에 대해 검사합니다. RemoteIp가 악성으로 식별되면 다음과 같은 속성이 다음과 같은 레코드 속성에서 채워집니다(IP가 악성으로 간주되지 않으면 비어 있음).

| 자산 | 설명 |
|:--|:--|
|MaliciousIp |RemoteIp 주소 |
|IndicatorThreadType |검색된 위협 표시기가 *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist* 값 중 하나입니다.   |
|설명 |관찰된 위협에 대한 설명입니다. |
|TLPLevel |TLP(Traffic Light Protocol) 수준은 정의된 *White*, *Green*, *Amber*, *Red* 값 중 하나입니다. |
|신뢰도 |값은 *0 - 100*입니다. |
|심각도 |값은 *0 - 5*입니다. 여기서 *5*는 가장 심각하고 *0*은 심각하지 않습니다. 기본값은 *3*입니다.  |
|FirstReportedDateTime |공급자가 표시기를 처음 보고한 시간입니다. |
|LastReportedDateTime |표시기가 Interflow에 의해 마지막으로 확인된 시간입니다. |
|IsActive |표시기가 *True* 또는 *False* 값으로 비활성화되었음을 나타냅니다. |
|ReportReferenceLink |지정된 관찰 가능 개체와 관련된 보고서의 링크입니다. |
|AdditionalInformation |관찰된 위협에 대한 추가 정보(해당되는 경우)를 제공합니다. |

### <a name="ports"></a>포트 
적극적으로 들어오는 트래픽을 허용 하거나 트래픽을 잠재적으로 수락할 수 있지만 보고 기간 동안 유휴 상태가 되는 컴퓨터에서 포트 VMBoundPort 테이블에 기록 됩니다.  

>[!NOTE]
>수집 하 고 다음 지역에서 Log Analytics 작업 영역에서 포트 데이터 기록에 Vm에 대 한 azure Monitor 지원 하지 않습니다.  
>- 미국 동부  
>- 서유럽
>
> 다른 사용은이 데이터를 수집 [지원 되는 지역](vminsights-onboard.md#log-analytics) Vm에 대 한 Azure Monitor에 대 한 합니다. 

VMBoundPort의 모든 레코드는 다음 필드에 의해 식별 됩니다. 

| 자산 | 설명 |
|:--|:--|
|Process | 포트와 연결 된 프로세스 (또는 프로세스의 그룹)의 id입니다.|
|IP | Ip 포트 (와일드 카드 IP 수 *0.0.0.0*) |
|포트 |포트 번호 |
|프로토콜 | 프로토콜입니다.  예에서 *tcp* 하거나 *udp* (만 *tcp* 는 현재 지원).|
 
Id는 포트는 위의 다섯 개의 필드에서 파생 되며 PortId 속성에 저장 됩니다. 이 속성은 특정 포트에 대 한 전체 시간 레코드를 신속 하 게 찾는 데 사용할 수 있습니다. 

#### <a name="metrics"></a>메트릭 
포트 레코드에는 연결 된 연결을 나타내는 메트릭이 포함 됩니다. 현재 다음 메트릭을 보고 됩니다 (각 메트릭에 대 한 세부 정보는 이전 섹션에서 설명 됨): 

- BytesSent 및 BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

고려할 몇 가지 중요한 사항은 다음과 같습니다.

- 프로세스가 동일한 IP 주소에 있지만 여러 네트워크 인터페이스를 통한 연결을 허용하는 경우 각 인터페이스에 대한 별도 레코드가 보고됩니다.  
- 와일드카드 IP를 사용한 레코드에는 활동이 포함되지 않습니다. 해당 레코드는 머신에 있는 포트가 인바운드 트래픽에 대해 열려있음을 나타내기 위해 포함됩니다. 
- 자세한 정도 및 데이터 볼륨을 줄이기 위해 와일드카드 IP를 사용한 레코드는 특정 IP 주소와 일치하는 레코드(동일한 프로세스, 포트 및 프로토콜에 대해)가 있는 경우 생략됩니다. 와일드 카드 IP 레코드를 생략 하면 합니다 *IsWildcardBind* 특정 IP 주소를 가진 레코드에 대 한 속성에 설정할 *True*합니다.  이 포트는 보고 시스템의 모든 인터페이스를 통해 노출 된다는 것을 나타냅니다. 
- 특정 인터페이스에만 바인딩되는 포트에로 IsWildcardBind *False*합니다. 

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 레코드
*ServiceMapComputer_CL* 형식의 레코드는 종속성 에이전트가 있는 서버에 대한 인벤토리 데이터를 포함합니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 작업 영역 내 컴퓨터에 대한 고유 식별자 |
| ResourceName_s | 작업 영역 내 컴퓨터에 대한 고유 식별자 |
| ComputerName_s | 컴퓨터 FQDN |
| Ipv4Addresses_s | 서버의 IPv4 주소 목록 |
| Ipv6Addresses_s | 서버의 IPv6 주소 목록 |
| DnsNames_s | DNS 이름 배열 |
| OperatingSystemFamily_s | Windows 또는 Linux |
| OperatingSystemFullName_s | 운영 체제의 전체 이름  |
| Bitness_s | 컴퓨터의 비트(32비트 또는 64비트)  |
| PhysicalMemory_d | 실제 메모리(MB) |
| Cpus_d | CPU 수 |
| CPUSpeed_d | CPU 속도(MHz)|
| VirtualizationState_s | *unknown*, *physical*, *virtual*, *hypervisor* |
| VirtualMachineType_s | *hyperv*, *vmware* 등 |
| VirtualMachineNativeMachineId_g | 해당 하이퍼바이저에 의해 할당된 VM ID |
| VirtualMachineName_s | VM 이름 |
| BootTime_t | 부팅 시간 |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 형식 레코드
*ServiceMapProcess_CL* 형식의 레코드는 종속성 에이전트가 있는 서버에서 TCP 연결 프로세스에 대한 인벤토리 데이터를 포함합니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 작업 영역 내 프로세스에 대한 고유 식별자 |
| ResourceName_s | 프로세스를 실행 중인 컴퓨터 내의 프로세스에 대한 고유 식별자|
| MachineResourceName_s | 컴퓨터의 리소스 이름 |
| ExecutableName_s | 프로세스 실행 파일의 이름 |
| StartTime_t | 프로세스 풀 시작 시간 |
| FirstPid_d | 프로세스 풀의 첫 번째 PID |
| Description_s | 프로세스 설명 |
| CompanyName_s | 회사의 이름 |
| InternalName_s | 내부 이름 |
| ProductName_s | 제품 이름 |
| ProductVersion_s | 제품 버전 |
| FileVersion_s | 파일 버전 |
| CommandLine_s | 명령줄 |
| ExecutablePath _s | 실행 파일 경로 |
| WorkingDirectory_s | 작업 디렉터리 |
| 사용자 이름 | 프로세스를 실행 중인 계정 |
| UserDomain | 프로세스를 실행 중인 도메인 |

## <a name="sample-log-searches"></a>샘플 로그 검색

### <a name="list-all-known-machines"></a>알려진 모든 컴퓨터 나열
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>VM이 마지막으로 재부팅된 시기
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>이미지, 위치 및 SKU별 Azure VM 요약
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>모든 관리되는 컴퓨터의 실제 메모리 용량을 나열합니다.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>컴퓨터 이름, DNS, IP 및 OS를 나열합니다.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>명령줄에서 "sql"로 모든 프로세스 찾기
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>리소스 이름으로 컴퓨터(가장 최근 레코드) 찾기
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP 주소로 컴퓨터(가장 최근 레코드) 찾기
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>특정 컴퓨터의 알려진 프로세스 모두 나열
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>SQL Server를 실행하는 모든 컴퓨터 나열
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>내 데이터 센터에서 curl의 고유한 제품 버전 모두 나열
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS를 실행하는 모든 컴퓨터의 컴퓨터 그룹 만들기
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>보내고 받은 바이트 수 추세
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>가장 많은 바이트를 전송하는 Azure VM
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>연결 상태 추세
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>연결 오류 추세
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>바인딩 포트
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>컴퓨터에서 열려 있는 포트 수
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>열려 있는 포트 번호로 작업 영역에서 프로세스 점수
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>각 포트에 대 한 집계 동작
이 쿼리에 사용할 수 있습니다 포트 점수를 매길 작업별: 예: 인바운드/아웃 바운드 트래픽이 가장 많은 포트, 대부분의 연결을 사용 하 여 포트
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>머신 그룹에서 아웃바운드 연결 요약
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>다음 단계
* Azure Monitor에서 로그 쿼리를 작성 하는 데 처음 이라면 검토 [Log Analytics를 사용 하는 방법을](../../azure-monitor/log-query/get-started-portal.md) 로그 쿼리를 작성 하려면 Azure portal에서.
* [검색 쿼리 작성](../../azure-monitor/log-query/search-queries.md)에 대해 알아봅니다.
