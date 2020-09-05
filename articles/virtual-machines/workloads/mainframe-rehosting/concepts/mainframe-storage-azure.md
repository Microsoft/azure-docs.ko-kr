---
title: 메인프레임 저장소를 Azure Storage 이동
description: 대규모 확장 가능한 Azure storage 리소스는 메인프레임 기반 조직에서 IBM z14 응용 프로그램을 마이그레이션하고 현대화 하는 데 도움이 될 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 639764c9cae5e27e90a55da00b246807e49d2a20
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88652851"
---
# <a name="move-mainframe-storage-to-azure"></a>메인프레임 저장소를 Azure로 이동

Microsoft Azure에서 메인프레임 워크 로드를 실행 하려면 메인프레임 기능이 Azure와 어떻게 비교 되는지 알아야 합니다. 대규모 확장 가능한 저장소 리소스를 사용 하면 조직에서 사용 하는 응용 프로그램을 포기 하지 않고도 현대화를 시작할 수 있습니다.

Azure는 IBM z14 기반 시스템 (이 문서를 작성 하는 최신 모델)과 비교할 수 있는 메인프레임 기능 및 저장소 용량을 제공 합니다. 이 문서에서는 Azure에서 비슷한 결과를 얻는 방법을 설명 합니다.

## <a name="mainframe-storage-at-a-glance"></a>한 눈에 메인프레임 저장소

IBM 메인프레임은 두 가지 방법으로 저장소를 특징으로 합니다. 첫 번째는 EASD (직접 액세스 저장 장치)입니다. 두 번째는 순차적 저장소입니다. 저장소 관리를 위해 메인프레임은 데이터 기능 저장소 관리 하위 시스템 (DFSMS)을 제공 합니다. 다양 한 저장 장치에 대 한 데이터 액세스를 관리 합니다.

[Easd](https://en.wikipedia.org/wiki/Direct-access_storage_device) 는 데이터에 대 한 직접 액세스에 고유한 주소를 사용할 수 있는 보조 (메모리 내 아님) 저장소에 대 한 별도의 장치를 나타냅니다. 원래는 회전 디스크, 자기 드럼 또는 데이터 셀에 적용 되는 것과 같은 용어입니다. 그러나 이제 용어는 Ssd (반도체 저장 장치), San (저장 영역 네트워크), NAS (네트워크 연결 저장소) 및 광학 드라이브에도 적용 될 수 있습니다. 이 문서의 목적상, EASD는 디스크, San 및 Ssd의 회전을 의미 합니다.

E.ASD 저장소와 달리 메인프레임 저장소는 시작 지점에서 데이터에 액세스 한 다음 줄에서 읽거나 쓰는 테이프 드라이브와 같은 장치를 참조 합니다.

저장소 장치는 일반적으로 파이버 연결 (FICON)을 사용 하 여 연결 되거나 하이퍼바이저를 사용 하는 서버의 파티션 간 고속 통신을 위한 IBM 기술인 [Hipersockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)를 사용 하 여 메인프레임 IO 버스에서 직접 액세스 됩니다.

대부분의 메인프레임 시스템은 다음 두 가지 유형으로 저장소를 구분 합니다.

- 매일의 작업에는 *온라인 저장소* (핫 저장소 라고도 함)가 필요 합니다. 이 목적을 위해 일반적으로는 E.ASD 저장소를 사용 합니다. 그러나이 목적을 위해 일별 테이프 백업 (논리적 또는 물리적)과 같은 순차적 저장소를 사용할 수도 있습니다.

- *보관 저장소* (콜드 저장소 라고도 함)는 지정 된 시간에 탑재 되지 않을 수 있습니다. 대신, 필요에 따라 탑재 되 고 액세스 됩니다. 보관 저장소는 저장소에 대 한 순차적 테이프 백업 (논리적 또는 물리적)을 사용 하 여 구현 되는 경우가 많습니다.

## <a name="mainframe-versus-io-latency-and-iops"></a>메인프레임 및 IO 대기 시간 및 IOPS

메인프레임은 고성능 IO 및 낮은 IO 대기 시간이 필요한 응용 프로그램에 자주 사용 됩니다. IO 장치 및 대상에 대 한 FICON 연결을 사용 하 여이 작업을 수행할 수 있습니다. 응용 프로그램 및 장치를 메인프레임의 IO 채널에 직접 연결 하는 데 사용 되는 경우 마이크로초의 대기 시간을 달성할 수 있습니다.

## <a name="azure-storage-at-a-glance"></a>Azure storage를 한눈에 보기

저장소에 대 한 Azure[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)(infrastructure as a service) 옵션은 비슷한 메인프레임 용량을 제공 합니다.

Microsoft는 Azure에서 호스트 되는 응용 프로그램을 위해 페타바이트의 저장소를 제공 하 고, 몇 가지 저장소 옵션을 제공 합니다. 이러한 범위는 고성능을 위해 SSD 저장소에서 대용량 스토리지 및 아카이브를 위한 저렴 한 blob 저장소 까지입니다. 또한 Azure는 저장소에 대 한 데이터 중복성 옵션을 제공 하며,이는 메인프레임 환경에서 설정 하는 데 더 많은 노력이 필요 합니다.

Azure storage는 다음 표에 요약 된 것 처럼 azure [디스크](../../../managed-disks-overview.md), [Azure Files](../../../../storage/files/storage-files-introduction.md)및 [azure blob](../../../../storage/blobs/storage-blobs-overview.md) 으로 사용할 수 있습니다. [각를 사용 하는 경우](../../../../storage/common/storage-introduction.md)에 대해 자세히 알아보세요.

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>유형</th><th>설명</th><th>다음을 수행 하려는 경우를 사용 합니다.</th></tr>
</thead>
<tbody>
<tr><td>Azure 파일
</td>
<td>
어디에서 나 저장 된 파일에 액세스할 수 있는 SMB 인터페이스, 클라이언트 라이브러리 및 <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> 인터페이스를 제공 합니다.
</td>
<td><ul>
<li>응용 프로그램에서 네이티브 파일 시스템 Api를 사용 하 여 Azure에서 실행 되는 응용 프로그램과 다른 응용 프로그램 간에 데이터를 공유 하는 경우 응용 프로그램을 클라우드로 리프트 앤 시프트 합니다.</li>
<li>여러 Vm에서 액세스 해야 하는 개발 및 디버깅 도구를 저장 합니다.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>는 구조화 되지 않은 데이터를 블록 blob에서 대규모로 저장 하 고 액세스할 수 있는 클라이언트 라이브러리 및 <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> 인터페이스를 제공 합니다. 엔터프라이즈 빅 데이터 분석 솔루션을 위한 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>도 지원합니다.
</td>
<td><ul>
<li>응용 프로그램에서 스트리밍 및 임의 액세스 시나리오를 지원 합니다.</li>
<li>어디에서 나 응용 프로그램 데이터에 액세스할 수 있습니다.</li>
<li>Azure에서 엔터프라이즈 data lake를 구축 하 고 빅 데이터 분석을 수행 합니다.</li>
</ul></td>
</tr>
<tr><td>Azure 디스크
</td>
<td>연결 된 가상 하드 디스크에서 데이터를 영구적으로 저장 하 고 액세스할 수 있는 클라이언트 라이브러리 및 <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> 인터페이스를 제공 합니다.
</td>
<td><ul>
<li>네이티브 파일 시스템 Api를 사용 하는 응용 프로그램을 이동 하 여 영구적 디스크에서 데이터를 읽고 씁니다.</li>
<li>디스크가 연결 된 VM 외부에서 액세스할 필요가 없는 데이터를 저장 합니다.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 핫 (온라인) 및 콜드 (보관) 저장소

지정 된 시스템의 저장소 유형은 저장소 크기, 처리량 및 IOPS를 비롯 한 시스템의 요구 사항에 따라 달라 집니다. Azure의 응용 프로그램은 메인프레임의 EASD 유형 저장소에 대해 일반적으로 Azure disk drive storage를 대신 사용 합니다. 메인프레임 보관 저장소의 경우 blob 저장소는 Azure에서 사용 됩니다.

Ssd는 Azure에서 가장 높은 저장소 성능을 제공 합니다. 이 문서를 작성할 때 사용할 수 있는 옵션은 다음과 같습니다.

| 유형         | 크기           | IOPS                  |
|--------------|----------------|-----------------------|
| 울트라 SSD    | 4gb ~ 64 TB  | 1200 ~ 16만 IOPS |
| 프리미엄 SSD  | 32 g b-32 TB | 12 ~ 15000 IOPS     |
| 표준 SSD | 32 g b-32 TB | 12 ~ 2000 IOPS      |

Blob 저장소는 Azure에서 가장 큰 저장소 용량을 제공 합니다. 저장소 크기 외에도 Azure는 관리 되는 저장소 및 관리 되지 않는 저장소를 제공 합니다. Azure는 관리 되는 저장소를 사용 하 여 기본 저장소 계정 관리를 담당 합니다. 관리 되지 않는 저장소를 사용 하는 경우 사용자는 저장소 요구 사항에 맞게 적절 한 크기의 Azure storage 계정을 설정 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure Virtual Machines의 메인프레임 재호스팅](../overview.md)
- [메인프레임 계산을 Azure로 이동](mainframe-compute-Azure.md)
- [Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정](../../../../storage/common/storage-introduction.md)
- [Azure VM 워크 로드에 대 한 표준 SSD Managed Disks](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 결합 기능: 기본 사항 이외](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 기능 설치에 필요한 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-인스턴스 만들기 명령](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 클러스터형 데이터베이스 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 응용 프로그램용 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 및 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>추가 마이그레이션 리소스

- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
