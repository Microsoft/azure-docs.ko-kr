---
title: Azure의 SAP HANA (대규모 인스턴스)에서 HANA 쪽의 모니터링 및 문제 해결 | Microsoft Docs
description: Azure의 SAP HANA (대규모 인스턴스)에서 HANA 쪽의 모니터링 및 문제 해결.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15b3fb4ae483e5b0e4f930d0dc08de6d198d0e5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61128972"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 쪽에서 모니터링 및 문제 해결

Azure(큰 인스턴스)의 SAP HANA와 관련된 문제를 효과적으로 분석하기 위해 문제의 근본 원인을 찾는 것이 유용합니다. SAP는 도움이 될 많은 양의 문서를 게시했습니다.

SAP HANA 성능과 관련된 적용 가능한 FAQ는 다음 SAP 참고 사항에서 찾을 수 있습니다.

- [SAP 참고 사항 #2222200 – FAQ: SAP HANA 네트워크](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP 참고 사항 #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP 참고 사항 #199997 – FAQ: SAP HANA 메모리](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP 참고 사항 #200000 – FAQ: SAP HANA 성능 최적화](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP 참고 사항 #199930 – FAQ: SAP HANA I/O Analysis](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP 참고 사항 #2177064 – FAQ: SAP HANA 서비스 다시 시작 하 고 충돌](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA 경고

첫 번째 단계로 현재 SAP HANA 경고 로그를 확인합니다. SAP HANA Studio에서으로 **관리 콘솔: 경고: 표시: 모든 경고**합니다. 이 탭에서는 최소 및 최대 임계값 설정 범위에 속하지 않는 특정 값(사용 가능한 실제 메모리, CPU 사용률 등)에 대한 모든 SAP HANA 경고를 표시합니다. 기본적으로 검사는 15분마다 자동으로 새로 고쳐집니다.

![SAP HANA Studio의 관리 콘솔로 이동 합니다. 경고: 모든 경고를 표시 합니다.](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

부적절한 임계값 설정으로 인해 트리거되는 경고의 경우 해결책은 기본값 또는 보다 적절한 크기의 임계값으로 다시 설정하는 것입니다.

![기본값 또는 보다 적절한 크기의 임계값으로 다시 설정](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

다음과 같은 경고는 CPU 리소스 문제를 의미할 수 있습니다.

- 호스트 CPU 사용량(경고 5)
- 가장 최근 저장 지점 작업(경고 28)
- 저장 지점 기간(경고 54)

다음 중 하나에서 SAP HANA 데이터베이스에 대한 높은 CPU 사용량을 알려줍니다.

- 경고 5(호스트 CPU 사용량)는 현재 또는 과거 CPU 사용량에 발생합니다.
- 개요 화면에 표시된 CPU 사용량

![개요 화면에 표시된 CPU 사용량](./media/troubleshooting-monitoring/image3-cpu-usage.png)

로드 그래프는 높은 CPU 사용량 또는 과거의 높은 사용량을 표시할 수 있습니다.

![로드 그래프는 높은 CPU 사용량 또는 과거의 높은 사용량을 표시할 수 있습니다.](./media/troubleshooting-monitoring/image4-load-graph.png)

CPU 사용률로 인해 트리거되는 경고 등을 망라 하는 몇 가지 원인으로 인해 일어날 수: 특정 트랜잭션, 데이터 로드에 응답 하지 않는 장기 실행 중인 SQL 문 및 잘못 된 쿼리 성능 (예를 들어, 작업 실행 HANA 큐브의 BW).

참조 된 [SAP HANA 문제 해결: CPU 관련 원인 및 솔루션](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) 자세한 문제 해결 단계에 대 한 사이트입니다.

## <a name="operating-system"></a>운영 체제

Linux의 SAP HANA에 대한 가장 중요한 검사 중 하나는 Transparent Huge Pages를 사용하지 않도록 설정하는 것입니다. [SAP 참고 사항 #2131662 - SAP HANA 서버의 THP(Transparent Huge Pages)](https://launchpad.support.sap.com/#/notes/2131662)를 참조하세요.

- 다음 Linux 명령을 통해 Transparent Huge Pages를 사용할 수 있는지 확인할 수 있습니다. **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- _always_가 아래와 같이 대괄호로 묶이면 Transparent Huge Pages를 사용할 수 있다는 의미입니다. [always] madvise never _never_가 아래와 같이 대괄호로 묶이면 Transparent Huge Pages를 사용할 수 없다는 의미입니다. always madvise [never]

다음 Linux 명령은 아무것도 반환하지 않습니다. **rpm -qa | grep ulimit.** _ulimit_가 표시되는 경우 즉시 설치를 제거합니다.

## <a name="memory"></a>메모리

SAP HANA 데이터베이스에 의해 할당된 메모리 양이 예상보다 높다는 것을 확인할 수 있습니다. 다음 경고는 높은 메모리 사용량에 문제가 있는 경우 표시됩니다.

- 호스트 실제 메모리 사용량(경고 1)
- 이름 서버의 메모리 사용량(경고 12)
- 열 저장소 테이블의 총 메모리 사용량(경고 40)
- 서비스의 메모리 사용량(경고 43)
- 열 저장소 테이블 중 기본 저장소의 메모리 사용량(경고 45)
- 런타임 덤프 파일(경고 46)

참조 된 [SAP HANA 문제 해결: 메모리 문제](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) 자세한 문제 해결 단계에 대 한 사이트입니다.

## <a name="network"></a>네트워크

[SAP 참고 사항 #2081065 - SAP HANA 네트워크 문제 해결](https://launchpad.support.sap.com/#/notes/2081065)을 참조하고 이 SAP 참고 사항에 있는 네트워크 문제 해결 단계를 수행합니다.

1. 서버와 클라이언트 간의 왕복 시간을 분석합니다.
  a. SQL 스크립트 [_HANA\_네트워크\_클라이언트_](https://launchpad.support.sap.com/#/notes/1969700)_를 실행합니다._
  
2. 노드 간 통신을 분석합니다.
  a. SQL 스크립트 [_HANA\_네트워크\_서비스_](https://launchpad.support.sap.com/#/notes/1969700)_를 실행합니다._

3. Linux 명령 **ifconfig**를 실행합니다(패킷 손실이 발생하는 경우 출력에서 표시).
4. Linux 명령 **tcpdump**를 실행합니다.

또한 오픈 소스 [IPERF](https://iperf.fr/) 도구(또는 유사한 기능)를 사용하여 실제 애플리케이션 네트워크 성능을 측정합니다.

참조 된 [SAP HANA 문제 해결: 네트워킹 성능 및 연결 문제](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) 자세한 문제 해결 단계에 대 한 사이트입니다.

## <a name="storage"></a>Storage

최종 사용자 관점에서 응용 프로그램 (또는 시스템 전체의) 느리게 실행, 응답성이 뛰어난 아니거나 I/O 성능 문제가 있는 경우 응답을 중지 하도록 보일 수 있습니다. SAP HANA Studio의 **볼륨** 탭에서 연결된 볼륨 및 각 서비스에서 사용하는 볼륨을 확인할 수 있습니다.

![SAP HANA Studio의 [볼륨] 탭에서 연결된 볼륨 및 각 서비스에서 사용하는 볼륨을 확인할 수 있습니다.](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

화면 아래쪽의 [연결된 볼륨]에서 파일 및 I/O 통계와 같은 볼륨의 세부 정보를 볼 수 있습니다.

![화면 아래쪽의 [연결된 볼륨]에서 파일 및 I/O 통계와 같은 볼륨의 세부 정보를 볼 수 있습니다.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

참조 된 [SAP HANA 문제 해결: I/O 관련 근본 원인 및 솔루션](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) 고 [SAP HANA 문제 해결: 디스크 관련 근본 원인 및 솔루션](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) 자세한 문제 해결 단계에 대 한 사이트입니다.

## <a name="diagnostic-tools"></a>진단 도구

HANA\_Configuration\_Minichecks를 통해 SAP HANA 상태 검사를 수행합니다. 이 도구는 SAP HANA Studio에서 이미 경고로 발생했어야 하는 잠재적으로 중요한 기술 문제를 반환합니다.

[SAP 참고 사항 #1969700 - SAP HANA에 대한 SQL 문 컬렉션](https://launchpad.support.sap.com/#/notes/1969700)을 참조하고 참고 사항에 연결된 SQL Statements.zip 파일을 다운로드합니다. 로컬 하드 드라이브에서 이.zip 파일을 저장합니다.

SAP HANA Studio의 **시스템 정보** 탭에서 **이름** 열을 마우스 오른쪽 단추로 클릭하고 **가져오기 SQL 문**을 선택합니다.

![SAP HANA Studio의 [시스템 정보] 탭에서 [이름] 열을 마우스 오른쪽 단추로 클릭하고 [가져오기 SQL 문]을 선택합니다.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

로컬에 저장된 SQL Statements.zip 파일을 선택하고 해당 SQL 문을 포함한 폴더를 가져옵니다. 이 시점에서 이러한 SQL 문으로 다른 여러 진단 검사를 실행할 수 있습니다.

예를 들어 SAP HANA 시스템 복제 대역폭 요구를 테스트 하려면 마우스 오른쪽 단추로 클릭 합니다 **대역폭** 아래에 문을 **복제 합니다. 대역폭** 선택한 **오픈** SQL 콘솔에서.

입력 매개 변수(수정 섹션)을 변경한 다음 실행할 수 있도록 전체 SQL 문이 열립니다.

![입력 매개 변수(수정 섹션)을 변경한 다음 실행할 수 있도록 전체 SQL 문이 열립니다.](./media/troubleshooting-monitoring/image8-import-statements-b.png)

또 다른 예는 아래에서 문을 마우스 오른쪽 단추로 클릭 **복제 합니다. 개요**합니다. 상황에 맞는 메뉴에서 **실행**을 선택합니다.

![또 다른 예는 복제 아래에서 문을 마우스 오른쪽 단추로 클릭 합니다. 개요입니다. 상황에 맞는 메뉴에서 [실행]을 선택합니다.](./media/troubleshooting-monitoring/image9-import-statements-c.png)

그러면 문제 해결에 도움이 되는 정보가 표시됩니다.

![그러면 문제 해결에 도움이 되는 정보가 표시됩니다.](./media/troubleshooting-monitoring/image10-import-statements-d.png)

HANA\_Configuration\_Minichecks에 대해 동일한 작업을 수행하고 _C_(위험) 열에서 _X_ 표시를 확인합니다.

샘플 출력:

일반 SAP HANA 검사는 **HANA\_Configuration\_MiniChecks\_Rev102.01+1**.

![일반 SAP HANA 검사는 [HANA\_Configuration\_MiniChecks\_Rev102.01+1].](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

현재 실행 중인 SAP HANA 서비스의 개요는 **HANA\_Services\_Overview**.

![현재 실행 중인 SAP HANA 서비스의 개요는 [HANA\_Services\_Overview].](./media/troubleshooting-monitoring/image12-services-overview.png)

SAP HANA 서비스 정보(CPU, 메모리 등)는 **HANA\_Services\_Statistics**.

![SAP HANA 서비스 정보는 [HANA\_Services\_Statistics].](./media/troubleshooting-monitoring/image13-services-statistics.png)

SAP HANA 인스턴스에 대한 일반적인 정보는 **HANA\_Configuration\_Overview\_Rev110+**.

![SAP HANA 인스턴스에 대한 일반적인 정보는 [HANA\_Configuration\_Overview\_Rev110+].](./media/troubleshooting-monitoring/image14-configuration-overview.png)

SAP HANA 매개 변수를 확인하려면 **HANA\_Configuration\_Parameters\_Rev70+**.

![SAP HANA 매개 변수를 확인하려면 [HANA\_Configuration\_Parameters\_Rev70+].](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**다음 단계**

- [STONITH를 사용하여 SUSE에서 고가용성 설정](ha-setup-with-stonith.md)을 참조하세요.