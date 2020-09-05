---
title: Azure Data Box Heavy 설정 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure Data Box Heavy를 케이블링하고, Azure Data Box Heavy를 연결하고, Azure Data Box Heavy를 켜는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c38ff7e642017afe5f220ae26d3a04c2c0b706ee
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920945"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>자습서: Azure Data Box Heavy에 케이블 연결

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Azure Data Box Heavy 시작

::: zone-end

::: zone target = "docs"

이 자습서에서는 Azure Data Box Heavy에 케이블을 장착하고, 연결을 설정하고, 전원을 켜는 방법을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Data Box Heavy에 케이블 장착
> * Data Box Heavy에 연결

::: zone-end

::: zone target = "chromeless"

이 가이드에서는 필수 구성 요소를 검토하고, 디바이스를 케이블에 연결하고, 데이터를 복사하고, Azure에 업로드한 다음, 업로드된 데이터를 확인하는 방법에 대한 지침을 제공합니다.

::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box Heavy 주문](data-box-heavy-deploy-ordered.md)을 완료했습니다.
2. Data Box Heavy를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. [Data Box Heavy 안전 지침](data-box-safety.md)을 검토했습니다.
4. 이 공간을 차지하는 디바이스를 수용할 수 있는 사용 가능한 네트워크 연결에 근접한 데이터 센터의 플랫 사이트에 대한 액세스 권한이 있어야 합니다. 이 디바이스는 랙에 탑재할 수 없습니다.
5. 스토리지 디바이스에 사용할 접지된 전원 코드 4개를 받았습니다.
6. 호스트 컴퓨터를 데이터 센터 네트워크에 연결해야 합니다. Data Box Heavy는 이 컴퓨터에서 데이터를 복사합니다. 호스트 컴퓨터에서 [지원되는 운영 체제](data-box-heavy-system-requirements.md)를 실행해야 합니다.
7. 데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 
8. 로컬 UI에 연결하고 디바이스를 구성하기 위해 RJ-45 케이블이 있는 랩톱이 있어야 합니다. 랩톱을 사용하여 디바이스의 각 노드를 한 번 구성합니다.
9. 디바이스 노드당 40Gbps 케이블 또는 10Gbps 케이블이 하나씩 필요합니다.
    - [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 네트워크 인터페이스와 호환되는 케이블을 선택합니다.
    - 40Gbps 케이블의 경우 케이블의 디바이스 끝이 QSFP+여야 합니다.
    - 10Gbps 케이블의 경우, 한 쪽에는 10Gbps 스위치에 꽂을 SFP+ 케이블이 필요하고, 디바이스에 꽂는 쪽에는 QSFP+ ~ SFP+ 어댑터(또는 QSA 어댑터)가 필요합니다.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>디바이스에 전원 케이블 연결

다음 단계를 수행하여 디바이스에 케이블을 연결합니다.

1. 디바이스에 변조의 증거 또는 기타 명백한 손상이 있는지 검사합니다. 디바이스가 변조되거나 심각하게 손상된 경우에는 진행하지 마십시오. 디바이스의 작동 상태가 양호한지 교체품을 배송해야 하는지 여부를 평가하는 데 도움을 얻으려면 [Microsoft 지원에 즉시 문의](data-box-disk-contact-microsoft-support.md)하세요.
2. 디바이스를 설치 사이트로 이동합니다.

    ![Data Box Heavy 디바이스 설치 사이트](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. 아래 그림과 같이 디바이스 뒤쪽의 다리 바퀴를 잠급니다.

    ![Data Box Heavy 디바이스 다리 바퀴가 잠겨 있음](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. 디바이스 앞쪽과 뒤쪽 문의 잠금을 풀 수 있는 손잡이를 찾습니다. 앞쪽 문의 잠금을 풀고 디바이스의 옆면과 평평해질 때까지 움직입니다. 뒤쪽 문에도 이 단계를 반복합니다.
    디바이스를 작동할 때는 디바이스의 앞쪽에서 뒤쪽으로 공기가 흐를 수 있도록 양쪽 문이 모두 열려 있어야 합니다.

    ![Data Box Heavy 문이 열려 있음](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. 디바이스 뒤쪽의 트레이에 전원 케이블 4개가 있어야 합니다. 트레이에서 케이블을 모두 제거하여 따로 보관합니다.

    ![Data Box Heavy 트레이의 전원 코드](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. 다음 단계에서는 디바이스의 뒤쪽에 있는 다양한 포트를 식별합니다. **NODE1**과 **NODE2**라는 2가지 디바이스 노드가 있습니다. 각 노드에는 **MGMT**, **DATA1**, **DATA2**, **DATA3**이라는 4가지 네트워크 인터페이스가 있습니다. **MGMT**는 디바이스를 초기 구성하는 동안 관리를 구성하는 데 사용됩니다. **DATA1**-**DATA3**은 데이터 포트입니다. **MGMT**와 **DATA3** 포트는 1Gbps이며, **DATA1**, **DATA2**는 40Gbps 포트나 10Gbps 포트로 작동할 수 있습니다. 두 디바이스 노드의 하단에는 두 디바이스 노드에서 공유되는 PSU(전원 공급 장치)가 4개 있습니다. 이 디바이스를 정면에서 볼 때 **PSU**는 왼쪽에서 오른쪽으로 **PSU1**, **PSU2**, **PSU3**, **PSU4**입니다.

    ![Data Box Heavy 포트](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. 4개 전원 케이블 모두를 디바이스 전원 공급 장치에 연결합니다. 녹색 LED가 켜지고 깜빡입니다.
8. 전면 패널의 전원 단추를 사용하여 디바이스 노드를 켭니다. 파란색 등이 켜질 때까지 전원 단추를 몇 초 동안 누르고 있습니다. 이제 디바이스 뒤쪽에 있는 전원 공급 장치의 녹색 LED가 모두 켜져 있어야 합니다. 디바이스 전면의 작동 패널에는 오류 LED도 있습니다. PSU 또는 팬에 오류가 있거나 디스크 드라이브에 문제가 있으면 오류 LED가 켜집니다.  

    ![Data Box Heavy 전면 작동 패널](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>첫 번째 노드에 네트워크 케이블 연결

다음 단계에 따라 디바이스의 노드 중 하나에 네트워크 케이블을 연결합니다.

1. CAT 6 RJ-45 네트워크 케이블(그림의 오른쪽 상단 케이블, MGMT라고 레이블이 지정된 플러그에 연결됨)을 사용하여 호스트 컴퓨터를 1Gbps 관리 포트에 연결합니다.
2. QSFP+ 케이블(광섬유 또는 구리)을 사용하여 데이터용 40Gbps(1Gbps 이상 추천) 네트워크 인터페이스를 하나 이상 연결합니다. 10Gbps 스위치를 사용하는 경우 QSFP+ ~ SFP+ 어댑터(QSA 어댑터)가 있는 케이블을 사용하여 데이터용 40Gbps 네트워크 인터페이스를 연결합니다.

    ![케이블이 연결된 Data Box Heavy 포트](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1과 DATA 2가 전환되어 로컬 웹 UI에 표시된 내용과 일치하지 않습니다.
    > 아래 그림과 같이 삽입하면 40Gbps 케이블 어댑터가 연결됩니다.

    ![Data Box Heavy 40Gbps 케이블 어댑터](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>첫 번째 노드 구성

다음 단계에 따라 로컬 구성과 Azure Portal을 사용하여 디바이스를 설정합니다.

1. 포털에서 디바이스 자격 증명을 다운로드합니다. **일반 &gt; 디바이스 세부 정보**로 이동합니다. **디바이스 암호**를 복사합니다. 이 암호는 포털의 특정 순서에 연결됩니다. Data Box Heavy의 두 노드에 해당하는 디바이스 일련 번호 두 개가 표시됩니다. 두 노드의 디바이스 관리자 암호는 동일합니다.

    ![Data Box Heavy 디바이스 자격 증명](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. CAT6 RJ-45 네트워크 케이블을 통해 클라이언트 워크스테이션을 디바이스에 연결합니다.
3. 고정 IP 주소가 `192.168.100.5`고 서브넷이 `255.255.255.0`인 디바이스에 연결하는 데 사용 중인 컴퓨터의 이더넷 어댑터를 구성합니다.

    ![Data Box Heavy를 로컬 웹 UI에 연결](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. URL `http://192.168.100.10`에서 디바이스의 로컬 웹 UI에 연결합니다. **고급**을 클릭한 다음, **192.168.100.10으로 이동(안전하지 않음)** 을 클릭합니다.
5. 로컬 웹 UI에 대한 **로그인** 페이지가 보입니다.
    
    - 이 페이지의 노드 일련 번호 중 하나는 포털 UI와 로컬 웹 UI 모두에서 일치합니다. 일련 번호 매핑을 위해 노드 번호를 기록해 둡니다. 포털에 노드 2개와 디바이스 일련 번호 2개가 있습니다. 이 매핑을 통해 어떤 노드가 어떤 일련 번호에 해당하는지 파악할 수 있습니다.
    - 이 때는 디바이스가 잠겨있습니다.
    - 이전 단계에서 얻은 디바이스 관리자 암호를 입력하여 디바이스에 로그인합니다. **로그인**을 클릭합니다.

    ![Data Box Heavy 로컬 웹 UI에 로그인](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. 대시보드에서 네트워크 인터페이스가 구성되어 있는지 확인합니다. 디바이스 노드에는 네트워크 인터페이스가 4개 있고 그 중 2개는 1Gbps이고 2개는 40Gbps입니다. 1Gbps 인터페이스 중 하나는 관리 인터페이스이기 때문에 사용자가 구성할 수 없습니다. 나머지 네트워크 인터페이스 3개는 데이터 전용이며 사용자가 구성할 수 있습니다.

- 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다.
- DHCP를 사용하도록 설정하지 않으면 필요한 경우 집합 네트워크 인터페이스로 이동하여 고정 IP를 할당합니다.

    ![Data Box Heavy 대시보드 노드 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>두 번째 노드 구성

[첫 번째 노드 구성](#configure-first-node)에 설명된 단계를 디바이스의 두 번째 노드에 수행합니다.

![Data Box Heavy 대시보드 노드 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

디바이스 설정이 완료되면 디바이스 공유에 연결하여 컴퓨터의 데이터를 디바이스로 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Heavy 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box Heavy에 케이블 장착
> * Data Box Heavy에 연결

다음 자습서에서는 Data Box Disk Heavy에서 데이터를 복사하는 방법을 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Data Box에 데이터 복사](./data-box-heavy-deploy-copy-data.md)

::: zone-end
