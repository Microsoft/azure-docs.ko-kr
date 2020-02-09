---
title: 오프 라인 백업 개요
description: 오프 라인 백업에 대한 Azure Data Box 기반 오프 라인 백업 및 Azure Import/Export를 사용 하 여 오프 라인 백업의 다양 한 구성 요소에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027018"
---
# <a name="overview-of-offline-backup"></a>오프 라인 백업 개요

이 문서는 오프 라인 백업에 대한 개요를 제공 합니다.

Azure에 대한 초기 전체 백업은 일반적으로 많은 양의 데이터를 온라인으로 전송 하며 증분 변경 내용만 전송 하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요 합니다. 특정 지역의 원격 사무실 또는 데이터 센터에는 항상 네트워크 대역폭이 충분 하지 않습니다. 따라서 이러한 초기 백업은 며칠 정도 걸리며,이 시간 동안에는 온-프레미스 데이터 센터에서 실행 되는 응용 프로그램에 대해 프로 비전 된 동일한 네트워크를 계속 사용 합니다.

Azure Backup는 네트워크 대역폭을 사용 하지 않고 오프 라인으로 초기 백업 데이터를 전송할 수 있도록 하는 "오프 라인 백업"을 지원 합니다. 또한 백업 데이터를 실제 저장소 장치에 복사 하는 메커니즘을 제공 합니다 .이 장치는 가까운 Azure 데이터 센터에 배송 되어 Recovery Services 자격 증명 모음에 업로드 됩니다. 이 프로세스를 통해 네트워크 대역폭을 사용 하지 않고 백업 데이터를 강력 하 게 전송할 있습니다.

## <a name="offline-backup-options"></a>오프 라인 백업 옵션

오프 라인-백업은 저장 장치의 소유권을 기반으로 하는 두 가지 모드로 제공 됩니다.

1. Azure Data Box 기반 오프 라인 백업 (미리 보기)
2. 오프 라인 백업 기반 Azure 가져오기/내보내기

## <a name="azure-data-box-based-offline-backup-preview"></a>Azure Data Box 기반 오프 라인 백업 (미리 보기)

이 모드는 현재 MARS 에이전트 (미리 보기)에서 지원 됩니다. 이 옵션은 [Azure Data Box 서비스](https://azure.microsoft.com/services/databox/) 를 활용 하 여 USB 커넥터를 통해 Microsoft 독점, 안전 하 고 변조 방지 전송 어플라이언스를 데이터 센터 또는 원격 사무실에 제공 하 고 백업 데이터를 이러한 장치에 직접 기록 합니다. **이 옵션은 Azure 호환 디스크 및 커넥터를 프로 비전 하거나 스테이징 위치로 임시 저장소를 프로 비전 하는 데 필요한 노력을 절감 합니다.** 또한 Microsoft는 Azure Portal에서 추적할 수 있는 종단 간 전송 물류를 처리 합니다. 이 옵션을 사용 하 여 백업 데이터를 이동 하는 것을 설명 하는 아키텍처는 다음과 같습니다.

![Azure Backup Data Box 아키텍처](./media/offline-backup-overview/azure-backup-databox-architecture.png)

아키텍처에 대한 요약은 다음과 같습니다.

1. Azure Backup은 이러한 미리 구성 된 장치에 백업 데이터를 직접 복사 합니다.
2. 그런 다음 이러한 장치를 Azure 데이터 센터에 다시 제공할 수 있습니다.
3. Azure Data Box 서비스는 고객 소유의 저장소 계정에 데이터를 복사 합니다.
4. Azure Backup는 저장소 계정에서 지정 된 Recovery Services 자격 증명 모음으로 백업 데이터를 자동으로 복사 하 고 증분 온라인 백업이 예약 됩니다.

Azure Data Box 기반 오프 라인 백업을 사용 하려면 [이 문서](offline-backup-azure-data-box.md)를 참조 하세요.

## <a name="azure-importexport-based-offline-backup"></a>오프 라인 백업 기반 Azure 가져오기/내보내기

이 옵션은 MABS (Azure Backup Server)/DPM-A/MARS 에이전트에서 지원 됩니다. Azure [가져오기/내보내기 서비스](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) 를 활용 하 여 azure와 호환 되는 azure와 호환 되는 디스크 및 커넥터를 사용 하 여 초기 백업 데이터를 azure에 전송할 수 있습니다. 이 접근 방식을 사용 하려면 **준비 위치** 라는 임시 저장소를 프로 비전 하 고, 미리 빌드된 유틸리티를 사용 하 여 백업 데이터를 포맷 하 고 고객 소유 디스크로 복사 해야 합니다. 이 옵션을 사용 하 여 백업 데이터를 이동 하는 것을 설명 하는 아키텍처는 다음과 같습니다.

![Azure Backup 가져오기/내보내기 아키텍처](./media/offline-backup-overview/azure-backup-import-export.png)

아키텍처에 대한 요약은 다음과 같습니다.

1. Azure Backup는 네트워크를 통해 백업 데이터를 전송 하는 대신 백업 데이터를 준비 위치에 기록 합니다.
2. 준비 위치의 데이터는 사용자 지정 유틸리티를 사용 하 여 하나 이상의 SATA 디스크에 기록 됩니다.
3. 이 유틸리티는 준비 작업의 일부로 Azure 가져오기 작업을 만듭니다. SATA 드라이브는 가장 가까운 Azure 데이터 센터에 배송 되며 작업을 연결 하는 가져오기 작업을 참조 합니다.
4. Azure 데이터 센터에서 디스크의 데이터는 Azure Storage 계정에 복사됩니다.
5. Azure Backup은 스토리지 계정의 백업 데이터를 Recovery Services 자격 증명 모음으로 복사하고 증분 백업이 예약됩니다.

MARS 에이전트를 사용 하 여 Azure Import/Export 기반 오프 라인 백업을 사용 하려면 [이 문서](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)를 참조 하세요.

MABS/DPM-A와 함께 동일한를 사용 하려면 [이 문서](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)를 참조 하세요.

## <a name="offline-backup-support-summary"></a>오프 라인 백업 지원 요약

아래 표에서는 사용 가능한 두 옵션을 비교 하 여 시나리오에 따라 적절 한 선택 항목을 만들 수 있습니다.

| **고려 사항**                                            | **Azure Data Box 기반 오프 라인 백업**                     | **오프 라인 백업 기반 Azure 가져오기/내보내기**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup 배포 모델                              | MARS 에이전트 (미리 보기)                                              | MARS 에이전트, Azure Backup Server (MABS), DPM-A                                           |
| 서버당 최대 백업 데이터 (MARS) 또는 보호 그룹당 (MABS, DPM-A) | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7.2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (각각 8tb의 최대 10 개 디스크)                          |
| 보안 (데이터, 장치 & 서비스)                           | [데이터](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256 비트 암호화 됨 <br> 데이터를 복사 하는 [장치](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) 견고한 사례, 소유 자격 증명 기반 인터페이스 <br> Azure 보안 기능으로 [서비스](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) 보호 | 데이터-BitLocker 암호화                                 |
| 임시 스테이징 위치 프로 비전                     | 필요 없음                                                | 예상 백업 데이터 크기 보다 크거나 같음        |
| 지원 되는 지역                                           | [Azure Data Box Disk 지역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box 지역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure Import/Export 지역](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 국가 간 배송 *                                     | **지원 되지 않음**  <br>    *원본 주소 & 대상 Azure 데이터 센터는 동일한 국가에 있어야 합니다.* | 지원됨                                                    |
| 전송 물류 (배달, 전송, 선택)           | 완전 한 Microsoft 관리                                     | 고객이 관리                                            |
| 가격 책정                                                      | [Azure Data Box 가격 책정](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box Disk 가격 책정](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure Import/Export 가격 책정](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *사용자의 국가에 Azure 데이터 센터가 없으면 다른 국가의 Azure 데이터 센터에 디스크를 배송 해야 합니다.*

## <a name="next-steps"></a>다음 단계

* [MARS 에이전트에 대한 Azure Data Box 기반 오프 라인 백업](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [MARS 에이전트에 대한 Azure Import/Export 기반 오프 라인 백업](backup-azure-backup-import-export.md)  
* [MABS/DPM에 대한 Azure Import/Export 기반 오프 라인 백업-A](backup-azure-backup-server-import-export-.md)
