---
title: Azure Data Box Heavy 주문 자습서 | Microsoft Docs
description: 이 자습서에서는 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션인 Azure Data Box Heavy와 Data Box Heavy를 정렬하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9b2b97f11c1493deca9b79907e894efbb7b9c456
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920996"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>자습서: Azure Data Box Heavy 주문


Azure Data Box Heavy는 빠르고 쉽게 신뢰할 수 있는 방식으로 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션입니다. Microsoft에서 제공한 770TB(대략적인 사용 가능한 용량) 스토리지 디바이스에 데이터를 전송한 다음, 디바이스를 다시 제공합니다. 그런 다음, 이 데이터는 Azure에 업로드됩니다.

이 자습서에서는 Azure Data Box Heavy를 주문할 수 있는 방법에 대해 설명합니다. 이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box Heavy 필수 구성 요소
> * Data Box Heavy 주문
> * 주문 추적
> * 주문 취소

## <a name="prerequisites"></a>사전 요구 사항

디바이스를 배포하기 전에 Data Box 서비스 및 디바이스에 대해 다음 필수 구성 요소를 완료합니다.

### <a name="for-installation-site"></a>설치 장소의 경우

시작하기 전에 다음 사항을 확인합니다.

- 디바이스가 표준 출입구 및 통로를 통과하기에 맞는 크기인지 여부 단, 디바이스가 모든 통로를 통과하기에 맞는 크기인지 확인합니다. 디바이스 크기: 너비: 26" 길이: 48" 높이: 28".
- 지층 이외의 층에 설치된 경우 엘리베이터 또는 경사로를 통해 디바이스에 접근해야 합니다. 디바이스는 최대 500lb(약 227kg)의 무게이며,
- 사용 가능한 네트워크 연결에 근접한 데이터 센터에 이 공간을 차지하는 디바이스를 수용할 수 있는 평평한 부분이 있어야 합니다.

### <a name="for-service"></a>서비스의 경우

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.
- 디바이스의 포장을 풀었는지 여부
- 호스트 컴퓨터를 데이터 센터 네트워크에 연결해야 합니다. Data Box Heavy는 이 컴퓨터에서 데이터를 복사합니다. 호스트 컴퓨터는 [Azure Data Box Heavy 시스템 요구 사항](data-box-system-requirements.md)에 설명된 대로 지원되는 운영 체제를 실행해야 합니다.
- 로컬 UI에 연결하고 디바이스를 구성하기 위해 RJ-45 케이블이 있는 랩톱이 있어야 합니다. 랩톱을 사용하여 디바이스의 각 노드를 한 번 구성합니다.
- 데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다.
- 디바이스 노드당 40Gbps 또는 10Gbps 케이블이 하나 필요합니다. [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 네트워크 인터페이스와 호환되는 케이블을 선택합니다.

    - 40Gbps 케이블의 경우 케이블의 디바이스 끝이 QSFP+이어야 합니다.
    - 10Gbps 케이블의 경우, 한 쪽에는 10G 스위치에 꽂을 SFP+ 케이블이 필요하고, 디바이스에 꽂는 쪽에는 QSFP+ ~ SFP+ 어댑터(또는 QSA 어댑터)가 필요합니다.
    - 전원 케이블은 디바이스에 포함되어 있습니다.

## <a name="order-data-box-heavy"></a>Data Box Heavy 주문

디바이스를 주문하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음 URL에서 로그인합니다. [https://portal.azure.com](https://portal.azure.com)
2. **+ 리소스 만들기**를 선택하고 *Azure Data Box*를 검색합니다. **Azure Data Box**를 선택합니다.
    
   [![Azure Data Box 1 검색](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. **만들기**를 선택합니다.

4. 해당 Azure 지역에서 Data Box 서비스를 사용할 수 있는지 확인합니다. 다음 정보를 입력하거나 선택하고 **적용**을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |Subscription     | Data Box 서비스에 대한 EA, CSP 또는 Azure 스폰서쉽 구독을 선택합니다. <br> 구독은 대금 청구 계정에 연결됩니다.       |
    |전송 형식     | **Azure로 가져오기**를 선택합니다.        |
    |원본 국가/지역     | 현재 데이터가 있는 국가/지역을 선택합니다.         |
    |대상 Azure 지역     | 데이터를 전송하려는 Azure 지역을 선택합니다.        |

    [![Data Box 제품군 가용성 선택](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. **Data Box Heavy**를 선택합니다. 단일 주문의 최대 사용 가능한 용량은 770TB입니다.

    [![Data Box Heavy 선택](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. **주문**에서 **주문 세부 정보**를 지정합니다. 다음 정보를 입력하거나 선택하고 **다음**을 클릭합니다.
    
    |설정  |값  |
    |---------|---------|
    |속성     | 주문을 추적하는 데 친숙한 이름을 입력합니다. <br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.      |
    |Resource group     | 기존 그룹을 사용하거나 새 그룹을 만듭니다. <br> 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다.         |
    |대상 Azure 지역     | 스토리지 계정에 대한 지역을 선택합니다. <br> 자세한 내용은 [지역 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=databox)을 참조하세요.        |
    |스토리지 대상     | 스토리지 계정이나 관리형 디스크 또는 둘 다를 선택합니다. <br> 지정된 Azure 지역에 따라 필터링된 기존 스토리지 계정 목록에서 하나 이상의 스토리지 계정을 선택합니다. <br>Data Box Heavy는 최대 10개의 스토리지 계정과 연결할 수 있습니다. <br> 새 **범용 v1**, **범용 v2** 또는 **Blob Storage 계정**도 만들 수 있습니다. <br>[디바이스에 지원되는 스토리지 계정](data-box-heavy-system-requirements.md#supported-storage-accounts)을 참조하세요. <br>가상 네트워크를 사용하는 스토리지 계정은 지원됩니다. Data Box 서비스에서 보안 스토리지 계정을 사용하려면 스토리지 계정 네트워크 방화벽 설정 내에서 신뢰할 수 있는 서비스를 사용하도록 설정합니다. 자세한 내용은 [Azure Data Box 서비스를 신뢰할 수 있는 서비스로 추가](../storage/common/storage-network-security.md#exceptions)하는 방법을 참조하세요.|

    스토리지 계정을 스토리지 대상으로 사용하는 경우 다음 스크린샷을 참조하세요.

    ![스토리지 계정에 대한 Data Box Heavy 주문](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    스토리지 대상인 스토리지 계정 외에 Data Box Heavy를 사용하여 온-프레미스 VHD의 관리 디스크도 만드는 경우 다음 정보를 제공해야 합니다.

    |설정  |값  |
    |---------|---------|
    |리소스 그룹     | 온-프레미스 VHD에서 관리형 디스크를 만들려는 경우 새 리소스 그룹을 만듭니다. 이전에 Data Box 서비스를 통해 관리 디스크의 Data Box Heavy 주문을 만들 때 리소스 그룹을 만든 경우에만 기존 리소스 그룹을 사용할 수 있습니다. <br> 세미콜론으로 구분해서 여러 리소스 그룹을 지정합니다. 최대 10개의 리소스 그룹이 지원됩니다.|

    ![관리 디스크에 대한 Data Box Heavy 주문](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    관리형 디스크에 대해 지정한 스토리지 계정은 스테이징 스토리지 계정으로 사용됩니다. Data Box 서비스는 VHD를 관리형 디스크로 변환한 후 리소스 그룹으로 이동하기 전에 페이지 Blob으로 스테이징 스토리지 계정에 업로드합니다. 자세한 내용은 [Azure에 대한 데이터 업로드 확인](data-box-deploy-picked-up.md#verify-data-upload-to-azure)을 참조하세요.

7. **배송 주소**에 사용자의 성과 이름, 회사의 이름과 우편 주소 및 유효한 전화 번호를 입력합니다. **주소 확인**을 선택합니다. 

    서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다. **다음**을 선택합니다.

8. **알림 세부 정보**에서 이메일 주소를 지정합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

9. 주문, 연락처, 알림 및 개인 정보 처리 방침과 관련된 정보 **요약**을 검토합니다. 개인 정보 처리 방침에 대한 계약에 해당하는 확인란을 선택합니다.

10. **주문**을 선택합니다. 주문을 만드는 데 몇 분 정도 걸립니다.


## <a name="track-the-order"></a>주문 추적

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box Heavy 주문, **개요**로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

디바이스를 사용할 수 없는 경우 알림을 받습니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 디바이스를 준비하는 동안 수행되는 작업은 다음과 같습니다.

- 디바이스와 연결된 각 스토리지 계정에 대해 SMB 공유가 생성됩니다.
- 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
- 디바이스의 잠금을 해제하는 데 도움이 되는 디바이스 암호도 생성됩니다.
- Data Box Heavy는 언제든지 디바이스에 대한 권한이 없는 액세스를 방지하기 위해 잠겨 있습니다.

디바이스 준비가 완료되면 포털에서는 해당 주문을 **처리됨** 상태로 표시합니다.

![Data Box Heavy 주문 처리됨](media/data-box-overview/data-box-order-status-processed.png)

그런 다음, Microsoft에서는 디바이스를 준비하고 지역 배송업체를 통해 발송합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![Data Box Heavy 주문 발송됨](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>주문 취소

이 주문을 취소하려면 Azure Portal에서 **개요**로 이동하고, 명령 모음에서 **취소**를 클릭합니다.

주문이 완료되면 주문 상태가 처리됨으로 표시되기 전까지는 취소할 수 있습니다.
 
취소된 주문을 삭제하려면 **개요**로 이동하고, 명령 모음에서 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Heavy 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * Data Box Heavy 주문
> * 주문 추적
> * 주문 취소

Data Box Heavy를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box Heavy 설정](./data-box-heavy-deploy-set-up.md)
