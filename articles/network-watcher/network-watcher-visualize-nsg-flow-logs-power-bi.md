---
title: Power BI를 사용하여 Azure 네트워크 보안 그룹 흐름 로그 시각화 | Microsoft Docs
description: 이 페이지에서는 Power BI를 사용하여 NSG 흐름 로그를 시각화하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 6df49f9cd308f4bb9b1fef6e5860872526ce8bb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860884"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Power BI를 사용하여 네트워크 보안 그룹 흐름 로그 시각화

네트워크 보안 그룹 흐름 로그를 통해 네트워크 보안 그룹의 송/수신 IP 트래픽에 대한 정보를 볼 수 있습니다. 이러한 흐름 로그는 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여줍니다.

로그 파일을 수동으로 검색하는 방법으로는 흐름 로깅에 대한 정보를 얻기 어려울 수 있습니다. 이 문서에서는 가장 최근의 흐름 로그를 시각화하고 네트워크 트래픽에 대해 알아보는 솔루션을 제공합니다.

> [!Warning]  
> 다음 단계는 흐름 로그 버전 1을 사용하여 작동합니다. 자세한 내용은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md)를 참조하세요. 다음 지침은 수정 없이 로그 파일의 버전 2를 사용하여 작동하지 않습니다.

## <a name="scenario"></a>시나리오

다음 시나리오에서는 NSG 흐름 로깅 데이터에 대한 싱크로 구성한 저장소 계정에 Power BI 데스크톱을 연결합니다. 스토리지 계정에 연결되면 Power BI에서는 로그를 다운로드하고 구분 분석하여 네트워크 보안 그룹에서 기록한 트래픽의 시각적 표현을 제공합니다.

템플릿에 제공된 시각화 기능을 사용하여 다음 항목을 검사할 수 있습니다.

* 상위 토커
* 방향 및 규칙 결정에 따른 시계열 흐름 데이터
* 네트워크 인터페이스 MAC 주소에 따른 흐름
* NSG 및 규칙에 따른 흐름
* 대상 포트에 따른 흐름

제공된 템플릿은 편집 가능하므로 사용자는 템플릿을 수정하여 새 데이터와 시각 효과를 추가하거나 본인의 요구 사항에 맞게 쿼리를 편집할 수 있습니다.

## <a name="setup"></a>설정

시작하기 전에 계정에 있는 하나 이상의 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로깅을 사용하도록 설정해야 합니다. 네트워크 보안 흐름 로그를 사용하도록 설정하는 방법에 대한 지침은 다음 문서를 참조하세요. [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md) 문서를 참조하세요.

또한 컴퓨터에 Power BI Desktop 클라이언트가 설치되어 있고, 저장소 계정에 있는 로그 데이터를 다운로드 및 로드하는 데 충분한 여유 공간이 있어야 합니다.

![Visio 다이어그램][1]

### <a name="steps"></a>단계

1. Power BI Desktop 애플리케이션 [Network Watcher PowerBI 흐름 로그 템플릿](https://aka.ms/networkwatcherpowerbiflowlogstemplate)에서 다음 Power BI 템플릿을 다운로드하여 열기
1. 필요한 쿼리 매개 변수 입력
   1. **StorageAccountName** - 로드하여 시각화하려는 NSG 흐름 로그가 포함된 스토리지 계정의 이름을 지정합니다.
   1. **NumberOfLogFiles** – Power BI에서 다운로드하여 시각화하려는 로그 파일의 수를 지정합니다. 예를 들어 50을 지정하면 가장 최근의 로그 파일 50개가 지정됩니다. NSG 2개가 활성화되어 이 계정에 NSG 흐름 로그를 보내도록 구성되어 있는 경우 지난 25시간 동안의 로그를 볼 수 있습니다.

      ![power BI 메인][2]

1. 저장소 계정의 액세스 키를 입력합니다. Azure Portal에서 저장소 계정으로 이동한 후 설정 메뉴에서 **액세스 키**를 선택하면 유효한 액세스 키를 찾을 수 있습니다. 그런 다음 **연결**을 클릭하여 변경 내용을 적용합니다.

    ![액세스 키][3]

    ![액세스 키 2][4]

4. 이제 로그가 다운로드되고 구문 분석되므로 사용자는 미리 만든 시각적 개체를 사용할 수 있습니다.

## <a name="understanding-the-visuals"></a>시각 효과의 이해

템플릿에는 NSG 흐름 로그 데이터를 이해하는 데 도움이 되는 일련의 시각 효과가 제공됩니다. 다음 이미지는 대시보드에 데이터가 채워지면 어떤 모습인지 살펴볼 수 있는 샘플을 제공합니다. 아래에서는 각 시각 효과를 좀 더 자세히 살펴보겠습니다. 

![powerbi][5]
 
상위 토커 시각 효과는 지정된 시간 동안 가장 많은 연결을 시작한 IP를 보여줍니다. 상자 크기는 연결 수에 비례합니다. 

![toptalkers][6]

다음 시계열 그래프는 시간에 따른 흐름의 수를 보여줍니다. 위쪽 그래프는 흐름 방향에 따라, 아래쪽 그래프는 내려진 결정(허용 또는 거부)에 따라 분리됩니다. 이 시각 효과를 사용하면 시간에 따른 트래픽 추세를 검사하고, 트래픽 또는 트래픽 세그먼트의 비정상적인 급증 현상 또는 급감 현상을 찾아낼 수 있습니다.

![flowsoverperiod][7]

다음 그래프는 네트워크 인터페이스별 흐름을 보여주며, 위쪽은 흐름 방향, 아래쪽은 내려진 결정에 따르 세그먼트가 분류됩니다. 이 정보를 사용하면 다른 VM에 비해 가장 많이 통신한 VM을 파악하고 특정 VM에 대한 트래픽이 허용되는지 또는 거부되는지 파악할 수 있습니다.

![flowspernic][8]

다음 도넛 휠 차트는 대상 포트에 따른 흐름의 분류를 보여줍니다. 이 정보를 사용하면 지정된 시간 동안 가장 많이 사용된 대상 포트를 확인할 수 있습니다.

![donut][9]

다음 가로 막대형 차트는 NSG 및 규칙에 따른 흐름을 보여줍니다. 이 정보를 사용하면 가장 많은 트래픽을 담당하는 NSG를 살펴보고, NSG의 트래픽을 규칙에 따라 분류할 수 있습니다.

![barchart][10]
 
다음 정보 차트는 로그에 있는 NSG에 대한 정보, 시간에 따라 캡처된 흐름 수, 가장 빠른 로그가 캡처된 날짜를 표시합니다. 이 정보는 어떤 NSG가 기록되었는지 그리고 흐름의 날짜 범위가 어떻게 되는지 알려줍니다.

![infochart1][11]

![infochart2][12]

이 템플릿에는 가장 관심이 있는 데이터만 볼 수 있도록 다음 슬라이서가 포함되어 있습니다. 사용자는 리소스 그룹, NSG 및 규칙을 필터링할 수 있습니다. 또한 5튜플 정보, 의사 결정 및 로그가 작성된 시간을 필터링할 수 있습니다.

![slicers][13]

## <a name="conclusion"></a>결론

이 시나리오에서는 Network Watcher 및 Power BI에서 제공하는 네트워크 보안 그룹 흐름 로그를 사용하여 트래픽을 시각화하고 이해할 수 있다는 것을 보여드렸습니다. Power BI에서는 제공된 템플릿을 사용하여 스토리지에서 직접 로그를 다운로드하고 로컬로 처리합니다. 템플릿을 로드하는 데 걸리는 시간은 요청된 파일 수와 다운로드 파일의 전체 크기에 따라 달라집니다.

본인의 요구 사항에 맞게 얼마든지 이 템플릿을 사용자 지정해도 됩니다. Power BI를 네트워크 보안 그룹 흐름 로그와 함께 사용할 수 있는 매우 다양한 방법이 있습니다. 

## <a name="notes"></a>메모

* 로그는 기본적으로 `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`에 저장됩니다.

    * 다른 디렉터리에 다른 데이터가 있으면 데이터를 가져와서 처리하는 쿼리를 수정해야 합니다.

* 로그 크기가 1GB를 초과하는 경우에는 제공된 템플릿을 사용하지 않는 것이 좋습니다.

* 로그 용량이 큰 경우 Data Lake 또는 SQL Server 같은 다른 데이터 저장소를 사용하는 솔루션을 알아보는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[오픈 소스 도구를 사용하여 Azure Network Watcher NSG 흐름 로그 시각화](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)를 참조하여 탄력적 스택을 통해 NSG 흐름 로그를 시각화하는 방법을 알아봅니다.

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
