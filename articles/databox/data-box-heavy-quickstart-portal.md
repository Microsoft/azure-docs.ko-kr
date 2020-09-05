---
title: Microsoft Azure Data Box Heavy 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure에 업로드할 데이터를 연결, 구성 및 복사하는 방법을 비롯하여 Azure Portal을 사용하여 Azure Data Box Heavy를 배포하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 5a0a040d80911b086561213bd0884ed67545c618
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920792"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Data Box Heavy 배포

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Data Box Heavy를 배포하는 방법에 대해 설명합니다. 여기서는 Azure에 업로드하기 위해 Data Box Heavy를 케이블로 연결하여 구성하고 데이터를 복사하는 단계를 수행합니다. 디바이스의 로컬 웹 UI와 Azure Portal에서 이 빠른 시작을 수행합니다.

자세한 단계별 배포 및 추적 지침은 [자습서: Azure Data Box Heavy 주문](data-box-heavy-deploy-ordered.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

디바이스를 배포하기 전에 설치 장소, Data Box 서비스 및 디바이스에 대한 다음 필수 조건을 완료합니다.

### <a name="for-installation-site"></a>설치 장소의 경우

시작하기 전에 다음 사항을 확인합니다.

- 디바이스는 모든 진입로를 통과할 수 있습니다. 디바이스 크기: 너비: 26", 길이: 48", 높이: 28"
- 1층이 아닌 다른 층에 설치하려는 경우 엘리베이터 또는 경사로를 통해 디바이스에 접근할 수 있습니다.
- 두 사람이 디바이스를 다루어야 합니다. 디바이스는 최대 500lb(약 227kg)의 무게이며, 바퀴가 달려 있습니다.
- 사용 가능한 네트워크 연결에 인접한 데이터 센터에는 이 공간을 차지하는 디바이스를 수용할 수 있는 바닥이 고른 장소가 있어야 합니다.

### <a name="for-service"></a>서비스의 경우

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.

- [Data Box Heavy 안전 지침](data-box-safety.md)을 검토했습니다.
- 호스트 컴퓨터가 데이터 센터 네트워크에 연결되어 있습니다. Data Box Heavy는 이 컴퓨터에서 데이터를 복사합니다. 호스트 컴퓨터에서 [지원되는 운영 체제](data-box-heavy-system-requirements.md)를 실행해야 합니다.
- 로컬 UI에 연결하고 디바이스를 구성하려면 RJ-45 케이블이 있는 랩톱이 있어야 합니다. 랩톱을 사용하여 디바이스의 각 노드를 한 번 구성합니다.
- 데이터 센터에서 고속 네트워크를 사용하므로 하나 이상의 10GbE 연결이 있어야 합니다.
- 디바이스 노드당 40Gbps 케이블 또는 10Gbps 케이블이 하나씩 필요합니다. Mellanox MCX314A-BCCT 네트워크 인터페이스와 호환되는 케이블을 선택합니다.
    - 40Gbps 케이블의 경우 케이블의 디바이스 끝이 QSFP+여야 합니다.
    - 10Gbps 케이블의 경우 한쪽 끝에는 10G 스위치에 꽂을 SFP+ 케이블이 필요하고, 디바이스에 꽂는 쪽에는 QSFP+ ~ SFP+ 어댑터(또는 QSA 어댑터)가 필요합니다.
- 전원 케이블은 디바이스 뒷면의 트레이에 들어 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="order"></a>주문

이 단계에는 약 5분 정도가 걸립니다.

1. Azure Portal에서 새 Azure Data Box 리소스를 만듭니다.
2. 이 서비스에 대해 활성화된 기존 구독을 선택하고 전송 형식을 **가져오기**로 선택합니다. 데이터가 있는 **원본 국가** 및 데이터 전송에 대한 **Azure 대상 지역**을 제공합니다.
3. **Data Box Heavy**를 선택합니다. 사용 가능한 최대 용량은 770TB이며, 더 큰 데이터인 경우 여러 주문을 만들 수 있습니다.
4. 주문 세부 정보 및 배송 정보를 입력합니다. 해당 지역에서 서비스를 사용할 수 있는 경우 알림 이메일 주소를 제공하고, 요약을 검토한 다음, 주문을 만듭니다.

주문을 작성하고 나면 디바이스가 배송 가능하도록 준비됩니다.

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>디바이스에 케이블 연결

필수 구성 요소를 검토한 후 디바이스에 케이블을 연결합니다.

::: zone-end

## <a name="cable-for-power"></a>전원 케이블 연결

이 단계는 5분 정도 걸립니다.

Data Box Heavy를 받으면 다음 단계에 따라 디바이스의 전원 케이블을 연결하고 디바이스를 켭니다.

1. 디바이스가 변조되었거나 손상된 듯하면 이 단계를 진행하지 말고 Microsoft 지원에 교체용 디바이스 배송을 요청하세요.
2. 디바이스를 설치 장소로 옮기고 뒷바퀴를 잠급니다.
3. 4개의 전원 케이블을 모두 디바이스 뒤쪽의 전원 공급 장치에 연결합니다.
4. 전면 패널의 전원 단추를 사용하여 디바이스 노드를 켭니다.

## <a name="cable-first-node-for-network"></a>첫 번째 노드에 네트워크 케이블 연결

이 단계는 완료하는 데 10~15분 정도 걸립니다.

1. RJ-45 CAT 6 네트워크 케이블을 사용하여 호스트 컴퓨터를 디바이스의 관리 포트(MGMT)에 연결합니다.
2. Twinax QSFP+ 동 케이블을 사용하여 데이터용 40Gbps(1Gbps 이상 추천) 네트워크 인터페이스(DATA 1 또는 DATA 2)를 하나 이상 연결합니다. 10Gbps 스위치를 사용하는 경우 QSFP+ ~ SFP+ 어댑터(QSA 어댑터)가 있는 Twinax SFP+ 동 케이블을 사용하여 데이터용 40Gbps 네트워크 인터페이스를 연결합니다.
3. 아래 그림과 같이 디바이스에 케이블을 꽂습니다.  

    ![케이블이 연결된 Data Box Heavy](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>첫 번째 노드 구성

이 단계는 완료하는 데 약 5~7분이 걸립니다.

1. 디바이스 암호를 확인하려면 [Azure Portal](https://portal.azure.com)에서 **일반 &gt; 디바이스 정보**로 이동합니다. 디바이스의 두 노드에는 동일한 암호가 사용됩니다.
2. 192.168.100.5 고정 IP 주소 및 255.255.255.0 서브넷을 Data Box Heavy에 연결하는 데 사용하는 컴퓨터의 이더넷 어댑터에 할당합니다. `https://192.168.100.10`에서 디바이스의 로컬 웹 UI에 액세스합니다. 디바이스를 켠 후 연결이 될 때까지 최대 5분이 소요될 수 있습니다.
3. Azure Portal에서 암호를 사용하여 로그인합니다. 웹 사이트의 보안 인증서 문제를 나타내는 오류가 표시됩니다. 브라우저별 지침에 따라 웹 페이지로 이동합니다.
4. 기본적으로 인터페이스(MGMT 제외)에 대한 네트워크 설정은 DHCP로 구성됩니다. 필요한 경우 이러한 인터페이스를 정적으로 구성하고 IP 주소를 제공할 수 있습니다.

## <a name="cable-and-configure-the-second-node"></a>두 번째 노드 케이블 연결 및 구성

이 단계는 완료하는 데 15~20분 정도 걸립니다.

케이블을 첫 번째 노드에 연결하는 데 사용한 단계를 따르고 디바이스에서 두 번째 노드를 구성합니다.  


::: zone target = "docs"

## <a name="copy-data"></a>데이터 복사

이 작업을 완료하는 데 걸리는 시간은 데이터 크기와 데이터가 복사되는 네트워크의 속도에 따라 달라집니다.
 
1. 두 개의 40Gbps 데이터 인터페이스를 동시에 사용하여 데이터를 두 개의 디바이스 노드에 복사합니다.

    - Windows 호스트를 사용하는 경우 [Robocopy](https://technet.microsoft.com/library/ee851678.aspx)와 같은 SMB 호환 파일 복사 도구를 사용합니다.
    - NFS 호스트의 경우에는 `cp` 명령 또는 `rsync`를 사용하여 데이터를 복사합니다.
2. `\\<IP address of your device>\ShareName` 경로를 사용하여 디바이스의 공유에 연결합니다. 공유 액세스 자격 증명을 가져오려면 Data Box Heavy의 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다.
3. 공유 및 폴더 이름과 데이터가 [Azure Storage 및 Data Box Heavy 서비스 제한](data-box-heavy-limits.md)에서 설명하는 지침을 따르는지 확인합니다.

## <a name="prepare-to-ship"></a>배송 준비

이 작업을 완료하는 시간은 데이터 크기에 따라 달라집니다.

1. 오류 없이 데이터 복사가 완료되면 로컬 웹 UI에서 **배송 준비** 페이지로 이동하여 배송 준비를 시작합니다.
2. 두 노드 모두에서 **배송 준비**가 완료되면 로컬 웹 UI에서 디바이스를 끕니다.

## <a name="ship-to-azure"></a>Azure에 배송

이 작업은 완료하는 데 15~20분 정도 걸립니다.

1. 케이블을 제거하여 디바이스 뒤쪽의 트레이에 다시 넣습니다.
2. 해당 지역 운송업체에서 픽업하도록 예약합니다.
3. [Data Box 운영 부서](mailto:DataBoxOps@microsoft.com)에 연락하여 픽업 관련 사항을 알려주고 반송 레이블을 받습니다.
4. 반송 레이블은 디바이스에서 비어 있는 전면 패널에 표시되어 있어야 합니다.

## <a name="verify-data"></a>데이터 확인

이 작업을 완료하는 시간은 데이터 크기에 따라 달라집니다.

1. Data Box Heavy 디바이스가 Azure 데이터 센터 네트워크에 연결되면 데이터가 자동으로 Azure에 업로드됩니다.
2. Data Box 서비스에서 Azure Portal을 통해 데이터 복사가 완료되었음을 알려줍니다.

    1. 모든 오류에 대한 오류 로그를 확인하고 적절한 조치를 수행합니다.
    2. 원본에서 데이터를 삭제하기 전에 데이터 스토리지 계정에 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 단계를 완료하려면 2-3분이 걸립니다.

- 주문이 처리되기 전에는 Azure Portal에서 Data Box Heavy 주문을 취소할 수 있습니다. 주문이 처리되면 주문을 취소할 수 없습니다. 완료된 단계에 도달할 때까지 주문이 진행됩니다. 주문을 취소하려면 **개요**로 이동하고 명령 모음에서 **취소**를 클릭합니다.

- 상태가 Azure Portal에서 **완료됨** 또는 **취소됨**으로 표시되면 주문을 삭제할 수 있습니다. 주문을 삭제하려면 **개요**로 이동하고 명령 모음에서 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 데이터를 Azure에 가져오는 데 도움이 되는 Data Box Heavy를 배포했습니다. Azure Data Box Heavy 관리에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 Data Box Heavy 관리](data-box-portal-admin.md)

::: zone-end
