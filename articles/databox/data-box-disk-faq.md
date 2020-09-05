---
title: Microsoft Azure Data Box Disk FAQ | Microsoft Docs
description: 많은 양의 데이터를 Azure로 전송할 수 있는 클라우드 솔루션인 Azure Data Box Disk에 대해 자주 묻는 질문과 대답이 포함되어 있습니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: f2231b74034ba6cea672a7bbf68f506fce423d45
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826143"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: 질문과 대답

Microsoft Azure Data Box Disk 클라우드 솔루션을 사용하면 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 Azure로 보낼 수 있습니다. 이 FAQ에는 Azure Portal에서 Data Box Disk를 사용할 때 제기될 수 있는 질문과 대답이 포함되어 있습니다. 

질문과 대답은 다음과 같은 범주로 정렬됩니다.

- 서비스 정보
- 구성 및 연결 
- 상태 추적
- 데이터 마이그레이션 
- 데이터 확인 및 업로드 


## <a name="about-the-service"></a>서비스 정보

### <a name="q-what-is-azure-data-box-service"></a>17. Azure Data Box 서비스란? 
A.  Azure Data Box 서비스는 오프라인 데이터 수집을 위해 설계되었습니다. 이 서비스는 다양한 스토리지 용량의 데이터 전송에 맞게 조정된 일련의 제품을 관리합니다. 

### <a name="q-what-are-azure-data-box-disks"></a>17. Azure Data Box Disk란?
A. Azure Data Box Disk를 사용하면 Azure에서 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 전송할 수 있습니다. Microsoft에서 1-5개의 디스크를 제공하며 최대 스토리지 용량은 35TB입니다. Azure Portal에서 Data Box 서비스를 통해 이러한 디스크를 쉽게 구성, 연결 및 잠금 해제할 수 있습니다.  

디스크는 Microsoft BitLocker 드라이브 암호화를 통해 암호화되고, 암호화 키는 SSL을 통해 Azure Portal에서 관리됩니다. 그런 다음, 고객의 서버에서 데이터를 복사합니다. 데이터 센터에서 Microsoft는 빠른 프라이빗망 업로드 링크를 사용하여 데이터를 드라이브에서 클라우드로 마이그레이션하고 Azure에 업로드합니다.

### <a name="q-when-should-i-use-data-box-disks"></a>17. Data Box Disk는 언제 사용해야 하나요?
A. Azure로 전송하려는 데이터가 40TB 이하이면 Data Box Disk를 사용하는 것이 좋습니다.

### <a name="q-what-is-the-price-of-data-box-disks"></a>17. Data Box Disk 가격은 어떻게 되나요?
A. Data Box Disk 가격 정보를 보려면 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/databox/disk/)를 방문하세요.

### <a name="q-how-do-i-get-data-box-disks"></a>17. Data Box Disk를 얻으려면 어떻게 할까요? 
A.  Azure Data Box Disk를 얻으려면 Azure Portal에 로그인하고 디스크에 대한 Data Box 주문을 작성합니다. 연락처 정보 및 알림 세부 정보를 제공합니다. 가용성을 기준으로 주문이 확정되면 디스크가 10일 이내에 배송됩니다.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>17. 한 인스턴스에서 Data Box Disk를 통해 전송할 수 있는 최대 데이터 양은 어떻게 되나요?
A. 각각 8TB(사용 가능한 용량: 7TB) 크기의 5개 디스크에 대해 사용 가능한 최대 용량은 35TB입니다. 따라서 한 인스턴스에서 35TB의 데이터를 전송할 수 있습니다. 더 많은 데이터를 전송하려면 디스크를 더 많이 주문해야 합니다.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>17. 내 지역에서 Data Box Disk를 사용할 수 있는지 확인하려면 어떻게 해야 할까요? 
A.  Data Box Disk가 현재 사용 가능한 곳을 보려면 [지역 가용성](data-box-disk-overview.md#region-availability)으로 이동합니다.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>17. Data Box Disk에 데이터를 저장할 수 있는 지역은 어떻게 되나요?
A. Data Box Disk는 미국, 캐나다, 오스트레일리아, 서유럽, 북유럽, 한국 및 일본 내의 모든 지역에서 지원됩니다. Azure 퍼블릭 클라우드 지역만 지원됩니다. Azure Government 또는 다른 소버린 클라우드는 지원되지 않습니다.

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>17. Data Box Disk에 데이터를 저장할 수 있는 지역은 어떻게 되나요?
A. Data Box Disk는 미국, 캐나다, 오스트레일리아, 서유럽, 북유럽, 한국 및 일본 내의 모든 지역에서 지원됩니다. Azure 퍼블릭 클라우드 지역만 지원됩니다. Azure Government 또는 다른 소버린 클라우드는 지원되지 않습니다.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>17. 한 국가/지역에 있는 원본 데이터를 다른 국가의 Azure 지역으로 가져오려면 어떻게 하나요?
A. Data Box Disk는 해당 대상과 동일한 국가/지역 내 에서만 데이터 수집을 지원 하며 국가별 테두리를 교차 하지 않습니다. 유일한 예외는 EU (유럽 연합)의 주문에 대 한 것입니다. 여기에서 Data Box 디스크는 EU 국가/지역에 제공 될 수 있습니다.

예를 들어 캐나다의 위치에 있는 데이터를 Azure WestUS 저장소 계정으로 이동 하려는 경우 다음과 같은 방법으로 달성할 수 있습니다.

### <a name="option-1"></a>옵션 1: 

Microsoft azure [Import/Export 서비스](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) 를 사용 하 여 데이터를 포함 하는 [지원 되는 디스크](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements?toc=/azure/storage/blobs/toc.json#supported-disks) 를 캐나다의 원본 위치에서 azure WestUS datacenter로 배송 합니다.

### <a name="option-2"></a>옵션 2:

1. Cananda Central 이라고 하는 저장소 계정을 선택 하 여 캐나다의 Data Box Disk 주문 합니다. SSD 디스크는 주문 생성 중에 제공 된 판매 주소 (캐나다)로 캐나다 중부의 Azure 데이터 센터에서 제공 됩니다.

2. 온-프레미스 서버의 데이터를 디스크에 복사한 후 Microsoft에서 제공 하는 반송 레이블을 사용 하 여 캐나다의 Azure 데이터 센터에 반환 합니다. 그러면 Data Box Disk에 있는 데이터가 주문 생성 중에 선택한 캐나다 Azure 지역의 대상 저장소 계정에 업로드 됩니다.

3. 그런 다음 AzCopy와 같은 도구를 사용 하 여 WestUS의 저장소 계정에 데이터를 복사할 수 있습니다. 이 단계에서는 Data Box Disk 요금 청구에 포함 되지 않은 [표준 저장소](https://azure.microsoft.com/pricing/details/storage/) 및 [대역폭 요금이](https://azure.microsoft.com/pricing/details/bandwidth/) 발생 합니다.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>17. Data Box Disk에 문제가 발생하면 누구에게 연락해야 하나요?
A. Data Box Disk에 문제가 발생하면 [Microsoft 지원에 문의](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support)하세요.

## <a name="configure-and-connect"></a>구성 및 연결
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>17. 주문에서 Data Box Disk의 수를 지정할 수 있나요?
A.  아니요. 데이터 크기 및 디스크 가용성에 따라 8TB 디스크(최대 5개 디스크)를 얻을 수 있습니다.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>17. Data Box Disk의 잠금을 해제하려면 어떻게 할까요? 
A.  Azure Portal에서 해당 Data Box Disk 주문, **디바이스 세부 정보**로 차례로 이동합니다. 지원 암호를 복사합니다. 운영 체제에 대한 Azure Portal에서 Data Box Disk 잠금 해제 도구를 다운로드하고 추출합니다. 디스크에 복사할 데이터가 있는 컴퓨터에서 도구를 실행합니다. 지원 암호를 제공하여 디스크의 잠금을 해제합니다. 동일한 지원 암호를 사용하여 모든 디스크의 잠금을 해제할 수 있습니다. 

단계별 지침을 보려면 [Windows 클라이언트에서 디스크 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) 또는 [Linux 클라이언트에서 디스크 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)로 이동하세요.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>17. Linux 호스트 컴퓨터를 사용하여 데이터를 Data Box Disk에 연결하고 복사할 수 있나요?
A.  예. Linux 및 Windows 클라이언트를 모두 사용하여 Data Box Disk에 연결하고 데이터를 복사할 수 있습니다. 자세한 내용은 호스트 컴퓨터에 대한 [지원되는 운영 체제](data-box-disk-system-requirements.md) 목록을 참조하세요.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>17. 내 디스크가 발송되었지만 이제 이 주문을 취소하려고 합니다. 취소 단추를 사용할 수 없는 이유는 무엇인가요?
A.  디스크의 주문 이후 및 배송 이전에만 주문을 취소할 수 있습니다. 디스크가 발송되면 더 이상 주문을 취소할 수 없습니다. 그러나 청구 시 디스크를 반환할 수 있습니다. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>17. 여러 개의 Data Box Disk를 호스트 컴퓨터에 연결하여 데이터를 전송할 수 있나요?
A. 예. 여러 개의 Data Box Disk를 동일한 호스트 컴퓨터에 연결하여 데이터를 전송할 수 있으며, 여러 복사 작업을 병렬로 실행할 수 있습니다.

## <a name="track-status"></a>상태 추적

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>17. 주문을 확정했을 때부터 디스크를 반송할 때까지 디스크를 추적하려면 어떻게 할까요? 
A.  Azure Portal에서 Data Box Disk 주문의 상태를 추적할 수 있습니다. 주문을 만들 때 알림 이메일을 제공하라는 메시지도 표시됩니다. 이메일을 제공한 경우 주문의 모든 상태 변경 내용에 대한 알림을 이 이메일을 통해 받게 됩니다. 자세한 내용은 [알림 이메일을 구성하는 방법](data-box-portal-ui-admin.md#edit-notification-details)을 참조하세요.

### <a name="q-how-do-i-return-the-disks"></a>17. 디스크를 반환하려면 어떻게 할까요? 
A.  Microsoft에서는 배송 패키지에 Data Box Disk와 함께 배송 레이블을 제공합니다. 레이블을 배송 상자에 부착하고 봉인된 패키지를 배송업체 지점에 위탁합니다. 레이블이 손상되거나 분실되었으면 **개요 > 배송 레이블 다운로드**로 차례로 이동하여 새 반송 레이블을 다운로드합니다.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>주문한 Data Box Disk를 직접 픽업할 수 있나요? 원하는 운송업체를 통해 디스크를 반환할 수 있나요?
A. 예. Microsoft는 US Gov 지역에만 자체 관리형 배송을 제공합니다. Data Box Disk를 주문할 때 자체 관리형 배송 옵션을 선택할 수 있습니다. 주문한 Data Box Disk를 직접 픽업하려면 다음 단계를 수행합니다.
    
1. 발주 후 주문이 처리되고 디스크가 준비됩니다. 주문한 제품의 픽업 준비가 완료되었다는 이메일 알림이 발송됩니다. 
2. 제품 픽업 준비가 완료되면 Azure Portal에서 주문한 제품으로 이동하여 **개요** 블레이드로 이동합니다. 
3. Azure Portal에 코드가 포함된 알림이 표시됩니다. 이메일을 통해 [Azure Data Box 운영 팀](mailto:adbops@microsoft.com)에 코드를 보냅니다. 그러면 운영 팀에서 위치를 알려주고 픽업 날짜 및 시간을 예약합니다. 이메일 알림을 받은 후 5영업일 내에 팀에게 알려야 합니다.

데이터 복사 및 유효성 검사가 완료되면 다음 단계에 따라 디스크를 반환합니다.

1. 데이터 유효성 검사가 완료되면 디스크를 분리합니다. 연결 케이블을 제거합니다.
2. 완충재를 사용하여 모든 디스크 및 연결 케이블을 래핑하고 배송 상자에 배치합니다. 액세서리가 없는 경우 요금이 부과될 수 있습니다.

    - 최초 배송의 포장재를 다시 사용합니다. 안전하게 보호하는 공기 쿠션 랩을 사용하여 디스크를 포장하는 것이 좋습니다.
    - 상자 내의 움직임을 줄이기 위해 빈틈 없이 채워졌는지 확인합니다.
3. Azure Portal에서 주문한 제품의 **개요 블레이드**로 이동합니다. 코드가 포함된 알림이 표시됩니다.
4. 이 코드를 사용하여 [Azure Data Box 운영 팀](mailto:adbops@microsoft.com)에게 이메일을 통해 코드를 보냅니다. 그러면 운영 팀에서는 디스크가 배송되는 위치와 시간 정보를 알려줄 것입니다.


## <a name="migrate-data"></a>데이터 마이그레이션

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>17. Data Box Disk에 사용할 수 있는 최대 데이터 크기는 어떻게 되나요?  
A.  Data Box Disk 솔루션은 최대 5개의 디스크를 사용할 수 있으며, 사용 가능한 최대 용량은 35TB입니다. 디스크 자체는 8TB(7TB 사용 가능)입니다.

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>17. Data Box Disk에서 지원하는 블록 Blob 및 페이지 Blob에 대한 최대 크기는 어떻게 되나요? 
A.  최대 크기는 Azure Storage 제한으로 관리됩니다. 최대 블록 Blob 크기는 대략 4.768TiB이고, 최대 페이지 Blob 크기는 8TiB입니다. 자세한 내용은 [Azure 스토리지의 확장성 및 성능 목표](../storage/blobs/scalability-targets.md)를 참조하세요.

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>17. Data Box Disk의 데이터 전송 속도는 어떻게 되나요?
A. USB 3.0을 통해 연결된 디스크를 테스트했을 때 디스크 성능은 최대 430MB/초였습니다. 실제 속도는 사용된 파일 크기에 따라 달라집니다. 작은 파일의 경우 성능이 저하될 수 있습니다.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>17. 전송 중에 내 데이터가 안전하다는 것을 확인하려면 어떻게 할까요? 
A.  Data Box Disk는 BitLocker AES 128비트 암호화를 사용하여 암호화되며, 지원 암호는 Azure Portal에서만 사용할 수 있습니다. 계정 정보를 사용하여 Azure Portal에 로그인한 다음, 지원 암호를 얻습니다. Data Box Disk 잠금 해제 도구를 실행할 때 이 지원 암호를 제공합니다.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>17. 데이터를 Data Box Disk에 복사하려면 어떻게 할까요? 
A.  Robocopy, Diskboss 또는 Windows 파일 탐색기 끌어서 놓기와 같은 SMB 복사 도구를 사용하여 데이터를 디스크에 복사합니다.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>17. 데이터 복사 속도를 높일 수 있는 팁이 있나요?
A.  복사 프로세스의 속도를 높이려면 다음을 수행합니다.

- 다중 데이터 복사 스트림을 사용합니다. 예를 들어 Robocopy에서는 다중 스레드 옵션을 사용합니다. 사용되는 정확한 명령에 대한 자세한 내용은 [자습서: Azure Data Box Disk에 데이터 복사 및 확인](data-box-disk-deploy-copy-data.md#copy-data-to-disks)을 참조하세요.
- 다중 세션을 사용합니다.
- 네트워크 공유를 통해 복사하는(네트워크 속도로 인해 제한될 수 있음) 대신, 디스크가 연결된 컴퓨터에 데이터가 로컬로 있는지 확인합니다.
- 복사 프로세스 전체에서 USB 3.0 이상을 사용하고 있는지 확인합니다. [USBView 도구](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview)를 다운로드하고 사용하여 컴퓨터에 연결된 USB 컨트롤러 및 USB 디바이스를 식별합니다.
- 데이터를 복사하는 데 사용되는 컴퓨터의 성능을 벤치마크합니다. [Bluestop FIO 도구](https://ci.appveyor.com/project/axboe/fio)를 다운로드하고 사용하여 서버 하드웨어의 성능을 벤치마크합니다. 최신 x86 또는 x64 빌드를 선택하고 **아티팩트** 탭을 선택한 후 MSI를 다운로드합니다.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>17. 원본 데이터에 작은 파일(KB 또는 수 MB)이 있는 경우 데이터의 속도를 높이려면 어떻게 해야 할까요?
A.  복사 프로세스의 속도를 높이려면 다음을 수행합니다.

- 고속 스토리지에 로컬 VHDx를 만들거나, HDD/SSD에 빈 VHD를 만듭니다(속도가 느림).
- VM에 탑재합니다.
- 파일을 VM의 디스크에 복사합니다.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>17. Data Box Disk에 여러 개의 스토리지 계정을 사용할 수 있나요?
A.  아니요. Data Box Disk에는 현재 하나의 스토리지 계정(범용 또는 클래식)만 지원됩니다. 핫 및 쿨 Blob은 모두 지원됩니다. 현재 Azure 퍼블릭 클라우드에서 미국, 서유럽 및 북유럽의 스토리지 계정만 지원됩니다.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>17. Data Box Disk에서 데이터에 사용할 수 있는 도구 세트는 무엇입니까?
A. Data Box Disk에서 사용할 수 있는 도구 세트로는 세 가지 도구가 있습니다.
 - **Data Box Disk 잠금 해제 도구**: 이 도구를 사용하여 Microsoft에서 배송하는 암호화된 디스크를 잠금 해제합니다. 이 도구를 사용하여 디스크를 잠금 해제할 때 Azure Portal의 Data Box Disk 주문에 제공되는 암호를 입력해야 합니다. 
 - **Data Box Disk 유효성 검사 도구**: 이 도구를 사용하여 Azure 명명 규칙에 따라 크기, 형식 및 Blob 이름의 유효성을 검사합니다. 또한 이 도구는 복사된 데이터에 대한 체크섬을 생성하며, 생성된 체크섬은 Azure에 업로드된 데이터를 확인하는 데 사용됩니다.
 - **Data Box Disk 분할 복사 도구**: 여러 디스크를 사용 중이고 데이터를 분할하여 모든 디스크에 복사해야 하는 대용량 데이터 세트가 있는 경우 이 도구를 사용합니다. 이 도구는 현재 Windows에서 사용할 수 있습니다. 이 도구는 관리 디스크에는 지원되지 않습니다. 또한 이 도구는 데이터 복사 시 유효성을 검사하므로, 이 도구를 사용할 때는 유효성 검사 단계를 건너뛸 수 있습니다.

이 도구 세트는 Windows 및 Linux 모두에 사용할 수 있습니다. 이 도구 세트는 여기서 다운로드할 수 있습니다.
- [Windows용 Data Box Disk 도구 집합 다운로드](https://aka.ms/databoxdisktoolswin) 
- [Linux용 Data Box Disk 도구 집합 다운로드](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>17. Data Box Disk를 사용하여 Azure Files로 데이터를 전송한 다음, Azure 파일 동기화에서 데이터를 사용할 수 있나요? 
A. Azure Files는 Data Box Disk에 지원되지만, Azure 파일 동기화에는 작동되지 않습니다. Azure 파일 동기화에서 파일 데이터를 나중에 사용하는 경우에도 메타데이터가 유지되지 않습니다.


## <a name="verify-and-upload"></a>확인 및 업로드

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>17. 디스크가 반송되면 Azure에서 내 데이터에 얼마나 빨리 액세스할 수 있나요? 
A.  데이터 복사에 대한 주문 상태가 완료됨으로 표시되면 데이터에 바로 액세스할 수 있습니다.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>17. 업로드되면 Azure에서 내 데이터가 어디에 있나요?
A.  *BlockBlob* 및 *PageBlob* 폴더 아래에 있는 데이터를 디스크에 복사하면, *BlockBlob* 및 *PageBlob* 폴더 아래의 각 하위 폴더에 대한 컨테이너가 Azure Storage 계정에 만들어집니다. 파일을 *BlockBlob* 및 *PageBlob* 폴더 아래에 직접 복사한 경우 이러한 파일은 Azure Storage 계정 아래의 *$root* 기본 컨테이너에 있습니다. 데이터를 *AzureFile* 폴더 아래의 폴더에 복사하면 파일 공유가 생성됩니다.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>17. 방금 내 컨테이너에 대해 Azure 명명 요구 사항을 따르지 않았음을 알게 되었습니다. 이 경우 내 데이터가 Azure에 업로드되지 않나요?
A. 컨테이너 이름에 대문자가 있으면 자동으로 소문자로 변환됩니다. 이름이 다른 방식(특수 문자, 다른 언어 등)으로 준수되지 않으면 업로드가 실패합니다. 자세한 내용을 보려면 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)으로 이동하세요.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>17. 여러 Data Box Disk에 복사한 데이터를 확인하려면 어떻게 할까요?
A.  데이터 복사가 완료되면 *DataBoxDiskImport* 폴더에 제공된 `DataBoxDiskValidation.cmd`를 실행하여 유효성 검사를 위한 체크섬을 생성할 수 있습니다. 여러 개의 디스크가 있는 경우 디스크마다 명령 창을 열어 이 명령을 실행해야 합니다. 이 작업은 데이터 크기에 따라 시간이 오래 걸릴 수 있음에 유의하세요.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>17. 디스크가 반환되면 내 데이터는 어떻게 되나요?
A.  Azure로의 데이터 복사가 완료되면 디스크의 데이터는 NIST SP 800-88 수정 1 지침에 따라 안전하게 지워집니다.  

### <a name="q-how-is-my-data-protected-during-transit"></a>17. 전송 중인 내 데이터는 어떻게 보호되나요? 
A.  Data Box Disk는 AES-128 Microsoft BitLocker 암호화로 암호화됩니다. 모든 디스크의 잠금을 해제하고 데이터에 액세스하려면 단일 지원 암호가 필요합니다.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>17. Data Box Disk에 더 많은 데이터를 추가하면 체크섬 유효성 검사를 다시 실행해야 하나요?
A. 예. 데이터의 유효성을 검사하기로 결정한 경우(권장)! 디스크에 데이터를 추가한 경우 유효성 검사를 다시 실행해야 합니다.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>17. 모든 내 디스크를 사용하여 데이터를 전송했으며 더 많은 디스크를 주문해야 합니다. 주문을 빠르게 확정할 수 있는 방법이 있나요?
A. 이전 주문을 복제할 수 있습니다. 복제하는 경우 이전과 동일한 주문을 만들고, 주소, 연락처 및 알림 세부 정보를 입력할 필요 없이 주문 세부 정보를 편집할 수 있습니다.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>17. 데이터를 ManagedDisk 폴더에 복사했는데, 관리 디스크에 대해 지정된 리소스 그룹이 있는 관리 디스크가 보이지 않습니다. 내 데이터가 Azure에 업로드되었다면 어떻게 찾을 수 있나요?
A. 예. 데이터가 Azure로 업로드되었지만 지정된 리소스 그룹의 관리 디스크가 보이지 않을 경우에는 데이터가 유효하지 않았을 가능성이 높습니다. 페이지 Blob, 블록 Blob, Azure Files 및 관리 디스크가 유효하지 않은 경우 다음 폴더로 이동했을 것입니다.
 - 페이지 Blob은 *databoxdisk-invalid-pb-* 로 시작하는 블록 Blob 컨테이너로 이동합니다.
 - Azure Files는 *databoxdisk-invalid-af-* 로 시작하는 블록 Blob 컨테이너로 이동합니다.
 - 관리 디스크는 *databoxdisk-invalid-md-* 로 시작하는 블록 Blob 컨테이너로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Data Box Disk 시스템 요구 사항](data-box-disk-system-requirements.md)을 검토합니다.
- [Data Box Disk 제한](data-box-disk-limits.md)을 알아봅니다.
- Azure Portal에서 [Azure Data Box Disk](data-box-disk-quickstart-portal.md)를 빠르게 배포합니다.
