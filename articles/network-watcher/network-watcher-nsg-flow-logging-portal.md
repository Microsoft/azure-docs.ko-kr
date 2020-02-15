---
title: VM 간에 네트워크 트래픽 흐름 기록 - 자습서 - Azure Portal | Microsoft Docs
description: Network Watcher의 NSG 흐름 기록 기능을 사용하여 VM 간에 네트워크 트래픽 흐름을 기록하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: f3448765eecf4a586e13155903f1c093607781dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896441"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 가상 머신 간에 네트워크 트래픽 기록

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure 리소스 관리자](network-watcher-nsg-flow-logging-azure-resource-manager.md)

NSG(네트워크 보안 그룹)를 사용하면 VM(가상 머신)에 대한 인바운드 트래픽 및 VM(가상 머신)으로부터의 아웃바운드 트래픽을 필터링할 수 있습니다. Network Watcher의 NSG 흐름 기록 기능을 사용하여 NSG를 통해 흐르는 네트워크 트래픽을 기록할 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 네트워크 보안 그룹을 사용하여 VM 만들기
> * Network Watcher 설정 및 Microsoft.Insights 공급자 등록
> * Network Watcher의 NSG 흐름 기록 기능을 사용하여 NSG에 대한 트래픽 흐름 기록 설정
> * 기록된 데이터 다운로드
> * 기록된 데이터 보기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-vm"></a>VM 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기**를 선택합니다.
2. **컴퓨팅**을 선택한 다음, **Windows Server 2016 Datacenter** 또는 **Ubuntu Server** 버전을 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |속성|myVm|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |Subscription| 구독을 선택합니다.|
    |Resource group| **새로 만들기**를 선택하고 **myResourceGroup**을 입력합니다.|
    |위치| **미국 동부**를 선택합니다.|

4. VM에 대한 크기를 선택한 다음, **선택**을 선택합니다.
5. **설정** 아래에서 모든 기본값을 그대로 적용하고, **확인**을 선택합니다.
6. **요약**의 **만들기** 아래에서 **만들기**를 선택하여 VM 배포를 시작합니다. VM을 배포하는 데 몇 분이 걸립니다. 나머지 단계를 계속하기 전에 VM이 배포를 완료하도록 기다립니다.

VM을 만드는 데 몇 분이 걸립니다. VM 만들기를 완료할 때까지 나머지 단계를 계속하지 않습니다. 포털에서 VM을 만드는 동안 **myVm nsg**라는 이름의 네트워크 보안 그룹도 만들고 VM의 네트워크 인터페이스에 연결합니다.

## <a name="enable-network-watcher"></a>Network Watcher 사용

미국 동부 지역에서 이미 Network Watcher를 사용하도록 설정한 경우 [정보 공급자 등록](#register-insights-provider)으로 건너뜁니다.

1. 포털에서 **모든 서비스**를 선택합니다. **필터 상자**에 *Network Watcher*를 입력합니다. 결과에 **Network Watcher**가 표시되면 이를 선택합니다.
2. **지역**을 선택하여 확장하고, 다음 그림처럼 **미국 동부** 오른쪽에서 **...** 를 선택합니다.

    ![Network Watcher 사용](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. **Network Watcher 사용**을 선택합니다.

## <a name="register-insights-provider"></a>Insights 공급자 등록

NSG 흐름을 기록하려면 **Microsoft.Insights** 공급자가 필요합니다. 공급자를 등록하려면 다음 단계를 완료합니다.

1. 포털의 맨 왼쪽 위에서 **모든 서비스**를 선택합니다. 필터 상자에 *구독*을 입력합니다. 검색 결과에 **구독**이 표시되면 이를 선택합니다.
2. 구독 목록에서 공급자를 사용하도록 설정하려는 구독을 선택합니다.
3. **설정**에서 **리소스 공급자**를 선택합니다.
4. 다음 그림에 표시된 대로 **microsoft.insights** 공급자의 **상태**가 **등록됨**인지 확인합니다. 상태가 **미등록됨**인 경우 공급자의 권한에 대한 **등록**을 선택합니다.

    ![공급자 등록](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>NSG 흐름 로그 설정

1. NSG 흐름 로그 데이터가 Azure Storage 계정에 기록됩니다. Azure Storage 계정을 만들려면 포털의 왼쪽 맨 위에서 **+ 리소스 만들기**를 선택합니다.
2. **스토리지**를 선택한 다음, **스토리지 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    | 설정        | 값                                                        |
    | ---            | ---   |
    | 속성           | 길이가 3~24자이고 소문자 및 숫자만 포함할 수 있고 모든 Azure Storage 계정에서 고유해야 합니다.                                                               |
    | 위치       | **미국 동부**를 선택합니다.                                           |
    | Resource group | **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다. |

    스토리지 계정을 만들 때 몇 분이 걸릴 수 있습니다. 스토리지 계정을 만들 때까지 나머지 단계를 계속하지 않습니다. 모든 경우에 스토리지 계정은 NSG와 동일한 영역에 있어야 합니다.
4. 포털의 맨 왼쪽 위에서 **모든 서비스**를 선택합니다. **필터 상자**에 *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
5. **로그**에서 다음 그림에 표시된 대로 **NSG 흐름 로그**를 선택합니다.

    ![NSG](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. NSG 목록에서 **myVm nsg**라는 NSG를 선택합니다.
7. **흐름 로그 설정**에서 **켜기**를 선택합니다.
8. 흐름 로깅을 버전을 선택합니다. 버전 2에는 흐름 세션 통계(바이트 및 패킷)가 포함됩니다.

   ![흐름 로그 버전 선택](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. 3단계에 만든 스토리지 계정을 선택합니다.
   > [!NOTE]
   > NSG Flow 로그는 [계층 구조 네임스페이스](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)를 사용하도록 설정된 스토리지 계정에서 작동하지 않습니다.
1. 포털의 맨 왼쪽 위에서 **모든 서비스**를 선택합니다. **필터 상자**에 *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
10. **보존(일)** 을 5로 선택한 다음, **저장**을 선택합니다.

## <a name="download-flow-log"></a>흐름 로그 다운로드

1. Network Watcher의 포털에서 **로그** 아래에 있는 **NSG 흐름 로그**를 선택합니다.
2. 다음 그림에서 표시된 대로 **구성된 스토리지 계정에서 흐름 로그를 다운로드할 수 있습니다**를 선택합니다.

   ![흐름 로그 다운로드](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. [NSG 흐름 로그 사용](#enable-nsg-flow-log)의 2단계에서 구성된 스토리지 계정을 선택합니다.
4. **Blob service**에서 **Blob**을 선택한 다음, **insights-logs-networksecuritygroupflowevent** 컨테이너를 선택합니다.
5. 컨테이너에서 다음 그림에 표시된 대로 PT1H.json 파일에 도달할 때까지 폴더 계층으로 탐색합니다. 다음 명명 규칙에 따라 폴더 계층에 로그 파일을 작성합니다. https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

   ![흐름 로그](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. PT1H.json 파일의 오른쪽에서 **...** 을 선택하고 **다운로드**를 선택합니다.

## <a name="view-flow-log"></a>흐름 로그 보기

다음 json은 다음에 대한 데이터를 기록한 각 흐름의 PT1H.json 파일에 표시되는 내용의 예제입니다.

### <a name="version-1-flow-log-event"></a>버전 1 흐름 로그 이벤트
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>버전 2 흐름 로그 이벤트
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

이전 출력에서 **mac**의 값은 VM을 만들 때 만든 네트워크 인터페이스의 MAC 주소입니다. **flowTuples**의 쉼표로 구분된 정보는 다음과 같습니다.

| 예제 데이터 | 데이터가 나타내는 정보   | 설명                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | 타임스탬프             | 흐름이 발생하는 경우 UNIX EPOCH 형식의 타임스탬프입니다. 이전 예제에서 날짜는 2018년 5월 1일 오후 2:59:05 GMT으로 변환됩니다.                                                                                    |
| 10.0.0.4  | 원본 IP 주소      | 흐름이 시작된 원본 IP 주소입니다. 10.0.0.4는 [VM 만들기](#create-a-vm)에서 만든 VM의 개인 IP 주소입니다.
| 13.67.143.118     | 대상 IP 주소 | 흐름을 보내는 대상 IP 주소입니다.                                                                                  |
| 44931        | 원본 포트            | 흐름이 시작된 원본 포트입니다.                                           |
| 443         | 대상 포트       | 흐름을 보낸 대상 포트입니다. 포트 443으로 트래픽을 보냈으므로 **UserRule_default-allow-rdp**라는 규칙이 로그 파일에서 흐름을 처리했습니다.                                                |
| T            | 프로토콜               | 흐름의 프로토콜이 TCP(T) 또는 UDP(U)인지입니다.                                  |
| O            | Direction              | 트래픽이 인바운드(I) 또는 아웃바운드(O)인지입니다.                                     |
| A            | 작업                 | 트래픽을 허용했는지(A) 또는 거부했는지(D)입니다.  
| C            | 흐름 상태 **버전 2만 해당** | 흐름의 상태를 캡처합니다. 가능한 상태는 다음과 같습니다. **B**: 흐름이 만들어질 때 시작합니다. 통계가 제공되지 않습니다. **C**: 지속적인 흐름에 대해 계속됩니다. 통계가 5분 간격으로 제공됩니다. **E**: 흐름이 종료되면 끝납니다. 통계가 제공됩니다. |
| 30 | 보낸 패킷 - 원본에서 대상 **버전 2만 해당** | 마지막 업데이트 이후 원본에서 대상으로 전송된 TCP 또는 UDP 패킷의 총 수입니다. |
| 16978 | 보낸 바이트 - 원본에서 대상 **버전 2만 해당** | 마지막 업데이트 이후 원본에서 대상으로 전송된 TCP 또는 UDP 패킷 바이트의 총 수입니다. 패킷 바이트에는 패킷 헤더 및 페이로드가 포함됩니다. |
| 24 | 보낸 패킷 - 대상에서 원본으로 **버전 2만 해당** | 마지막 업데이트 이후 대상에서 원본으로 전송된 TCP 또는 UDP 패킷의 총 수입니다. |
| 14008| 보낸 바이트 - 대상에서 원본으로 **버전 2만 해당** | 마지막 업데이트 이후 대상에서 원본으로 전송된 TCP 및 UDP 패킷 바이트의 총 수입니다. 패킷 바이트에는 패킷 헤더 및 페이로드가 포함됩니다.|

## <a name="next-steps"></a>다음 단계

이 자습서에서는 NSG에 NSG 흐름 로깅을 사용하도록 설정하는 방법을 알아보았습니다. 파일에 기록된 데이터를 다운로드하고 보는 방법도 알아보았습니다. json 파일의 원시 데이터를 해석하기 어려울 수 있습니다. Flow Logs 데이터를 시각화하기 위해 [Azure Traffic Analytics](traffic-analytics.md), [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) 및 기타 도구를 사용할 수 있습니다. [PowerShell](network-watcher-nsg-flow-logging-powershell.md), [Azure CLI](network-watcher-nsg-flow-logging-cli.md), [REST API](network-watcher-nsg-flow-logging-rest.md) 및 [ARM 템플릿](network-watcher-nsg-flow-logging-azure-resource-manager.md)과 같은 NSG Flow Logs를 사용하도록 설정하는 다른 방법을 시도할 수 있습니다.
