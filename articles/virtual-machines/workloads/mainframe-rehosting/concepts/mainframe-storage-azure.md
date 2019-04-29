---
title: 메인프레임 저장소를 Azure Storage로 이동
description: 대규모로 확장 가능한 Azure storage 리소스 메인프레임 기반 조직 마이그레이션하고 IBM z14 응용 프로그램을 현대화 할 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485540"
---
# <a name="move-mainframe-storage-to-azure"></a>메인프레임 저장소를 Azure로 이동

메인프레임 워크 로드를 Microsoft Azure에서 실행 하려면 Azure에 메인프레임의 기능을 비교 하는 방법 알 필요가 있습니다. 대규모로 확장 가능한 저장소 리소스에 의존 하므로 응용 프로그램을 중단 하지 않고도 현대화 하기 시작 하는 조직은 도움이 됩니다.

Azure에는 메인프레임 같은 기능 및 IBM z14 기반 시스템 (이 문서의 작성일 기준 최신 모델) 비교할 수 있는 저장소 용량의 제공 합니다. 이 문서에서는 Azure에서 유사한 결과 가져오는 방법을 알려줍니다.

## <a name="mainframe-storage-at-a-glance"></a>한 눈에 메인프레임 저장소

IBM 메인프레임 두 가지 방법으로 저장소를 지정 합니다. 첫 번째는 직접 액세스 저장 장치 (DASD). 두 번째는 순차 저장소입니다. 메인프레임에는 저장소를 관리 하는 것에 대 한 데이터 기능 저장소 관리 하위 시스템 (DFSMS)를 제공 합니다. 다양 한 저장소 장치에 대 한 데이터 액세스를 관리합니다.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) 고유한 주소 데이터의 직접 액세스 하기 위해 사용할 수 있는 보조 (메모리) 저장소에 대 한 별도 장치를 가리킵니다. 원래 용어 DASD 회전 디스크, 자기 요소 또는 데이터 셀에 적용 합니다. 그러나 용어 반도체 저장소 장치 (Ssd)에 적용할 수도, 이제 저장 영역 네트워크 (San), 네트워크 저장소 (NAS) 및 광 드라이브를 연결 합니다. 이 문서의 목적상, DASD 회전 디스크, San 및 Ssd를 의미 합니다.

DASD 저장소와 달리 메인프레임에서 순차 저장소 데이터 시작 지점에서 액세스한 다음 읽기 또는 줄에 작성 된 테이프 드라이브와 같은 장치를 가리킵니다.

저장소 장치 파이버 연결 (FICON)를 사용 하 여 일반적으로 연결 또는 사용 하 여 메인프레임의 IO bus에서 직접 액세스 하는 [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)를 사용 하 여 서버에서 파티션 간에 고속 통신 하는 IBM 기술을 하이퍼바이저입니다.

두 가지 유형으로 저장소를 분리 하는 대부분의 메인프레임 시스템:

- *온라인 저장소* (핫 저장소 라고도 함) 일상적인 작업에 필요 합니다. DASD 저장소는이 목적을 위해 일반적으로 사용 됩니다. 그러나 일별 테이프 백업의 (논리적 또는 물리적)와 같은 순차적 저장소에이 목적을 위해 수 있습니다.

- *보관 저장소* (콜드 저장소 라고도 함)를 지정된 된 시간에 탑재할 보장 되지 않습니다. 대신는 탑재 하 고 필요에 따라 액세스 합니다. 보관 저장소는 종종 순차 테이프 백업을 (논리적 또는 물리적)를 사용 하 여 저장소에 대 한 구현 됩니다.

## <a name="mainframe-versus-io-latency-and-iops"></a>IO 대기 시간 및 IOPS 및 메인프레임

메인프레임 IO 고성능과 짧은 IO 대기 시간을 필요로 하는 응용 프로그램에 대 한 자주 사용 됩니다. IO 장치와 HiperSockets FICON 연결을 사용 하 여 수행할 수 있습니다. HiperSockets 메인프레임의 IO 채널에 직접 응용 프로그램 및 장치 연결을 사용 하는 경우에 대기 시간을 마이크로초에서를 구현할 수 있습니다.

## <a name="azure-storage-at-a-glance"></a>한 눈에 azure storage

Azure 인프라-as a service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) 저장소에 대 한 옵션 비교 가능한 메인프레임 용량을 제공 합니다.

여러 저장소 옵션을 사용할 수 및 Microsoft Azure에서 호스팅되는 응용 프로그램에 대 한 저장소 페타바이트 가치를 제공 합니다. 대용량 저장소 및 보관에 대 한 저렴 한 비용 blob storage에 높은 성능을 위해 SSD 저장소에서 이러한 다양 합니다. Azure storage에 대 한 데이터 중복 옵션을 제공 하는 또한-더 많은 노력이 메인프레임 환경에서 설정 하는 것입니다.

Azure storage는 사용할 수 있습니다 [Azure Disks](/azure/virtual-machines/windows/managed-disks-overview)를 [Azure Files](/azure/storage/files/storage-files-introduction), 및 [Azure Blob](/azure/storage/blobs/storage-blobs-overview) 으로 다음 표에 요약 되어 있습니다. 에 대해 자세히 알아보세요 [각각 사용 하는 경우](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)합니다.

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>설명</th><th>다음과 같은 경우에 사용합니다.</th></tr>
</thead>
<tbody>
<tr><td>Azure 파일
</td>
<td>
SMB 인터페이스, 클라이언트 라이브러리를 제공 및 <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> 인터페이스 어디에서 나 액세스할 수 있도록 저장 된 파일에 있습니다.
</td>
<td><ul>
<li>리프트 및 응용 프로그램 기본 파일 시스템 Api를 사용 하 여 Azure에서 실행 중인 다른 응용 프로그램 사이 데이터를 공유 하는 클라우드 응용 프로그램을 이동 합니다.</li>
<li>개발 및 디버깅 여러 Vm에서 액세스 해야 하는 도구를 저장 합니다.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>클라이언트 라이브러리를 제공 및 <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> 구조화 되지 않은 데이터를 저장 하 고 대규모로 블록 blob에 액세스할 수 있는 인터페이스입니다. 엔터프라이즈 빅 데이터 분석 솔루션을 위한 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>도 지원합니다.
</td>
<td><ul>
<li>응용 프로그램에서 스트리밍 및 임의 액세스 시나리오를 지원 합니다.</li>
<li>어디에서 나 응용 프로그램 데이터에 액세스할 수 있으며</li>
<li>Azure에서 엔터프라이즈 데이터 레이크를 작성 하 고 빅 데이터 분석을 수행 합니다.</li>
</ul></td>
</tr>
<tr><td>Azure 디스크
</td>
<td>클라이언트 라이브러리를 제공 및 <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> 데이터를 영구적으로 저장 하 고 연결된 된 가상 하드 디스크에서 액세스할 수 있는 인터페이스입니다.
</td>
<td><ul>
<li>리프트 앤 시프트를 영구 디스크에 데이터를 읽고 기본 파일 시스템 Api를 사용 하는 응용 프로그램입니다.</li>
<li>디스크가 연결 된 VM 외부에서 액세스할 필요가 없는 데이터를 저장 합니다.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure (온라인) 핫 및 콜드 (보관) 저장소

지정된 된 시스템에 대 한 저장소 형식의 저장소 크기, 처리량 및 IOPS를 비롯 한 시스템 요구 사항에 따라 달라 집니다. 메인프레임에서 DASD 형식 저장소를 Azure에서 응용 프로그램에서 일반적으로 Azure 디스크 드라이브 저장소를 대신 하 게 사용합니다. 메인프레임 보관 저장소에 대 한 blob 저장소는 Azure에서 사용 됩니다.

Ssd는 Azure에서 고성능 저장소를 제공합니다. 다음 옵션은이 문서 작성) (현재 사용할 수 있습니다.

| Type         | 크기           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 64TB 4GB  | 1,200 160, 000 IOPS |
| 프리미엄 SSD  | 32TB을 32GB | 12 ~ 15,000 IOPS     |
| 표준 SSD | 32TB을 32GB | 12-2,000 IOPS      |

Blob 저장소는 Azure에서 가장 큰 볼륨의 저장소를 제공합니다. 저장소 크기 외에도 Azure 관리 및 관리 되지 않는 저장소를 제공합니다. 관리 되는 저장소를 사용 하 여 Azure의 기본 저장소 계정 관리를 담당 합니다. 관리 되지 않는 저장소를 사용 하 여 사용자는 저장소 요구 사항에 맞게 적절 한 크기의 Azure 저장소 계정을 설정 하는 작업을 담당 합니다.

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [메인프레임 재호스팅 Azure Virtual Machines에서](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [메인프레임 계산을 Azure로 이동](mainframe-compute-Azure.md)
- [Azure Blob, Azure Files 또는 Azure Disks를 사용 하는 경우 결정](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Azure VM 워크 로드에 대 한 표준 SSD Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z에 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 결합 기능: 향상 된 기능](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 기능 설치에 대한 필수 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 클러스터형 데이터베이스 솔루션](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 응용 프로그램에 대 한 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 및 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>추가 마이그레이션 리소스

- [Platform Modernization Alliance: Azure에서 IBM Db2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
