---
title: 개념-저장소
description: Azure VMware 솔루션 사설 클라우드의 주요 저장소 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: 83a4a52f8414e656b5bc688796db6e93a53d4d76
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801624"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 솔루션 저장소 개념

Azure VMware 솔루션 사설 클라우드는 VMware vSAN을 사용 하 여 클러스터 차원의 기본 저장소를 제공 합니다. 클러스터의 각 호스트에서 모든 로컬 저장소는 vSAN 데이터 저장소에서 사용 되며, 저장소 데이터 암호화를 사용 하 고 기본적으로 사용 하도록 설정 합니다. Azure Storage 리소스를 사용 하 여 사설 클라우드의 저장소 기능을 확장할 수 있습니다.

## <a name="vsan-clusters"></a>vSAN 클러스터

각 클러스터 호스트의 로컬 저장소는 vSAN 데이터 저장소의 일부로 사용 됩니다. 모든 diskgroups는 1.6 TB의 NVMe 캐시 계층을 사용 하며, 호스트 당 raw, SSD 기반 용량 15.4 TB를 사용 합니다. 클러스터의 원시 용량 계층 크기는 호스트 수 당 호스트 수의 수를 곱한 값입니다. 예를 들어 4 개의 호스트 클러스터가 vSAN 용량 계층에서 61.6-TB 원시 용량을 제공 합니다.

클러스터 호스트의 로컬 저장소는 클러스터 차원의 vSAN 데이터 저장소에 사용 됩니다. 모든 데이터 저장소는 사설 클라우드 배포의 일부로 생성 되며 즉시 사용할 수 있습니다. Cloudadmin 그룹의 cloudadmin 사용자 및 모든 사용자는 다음과 같은 vsan 권한으로 데이터 저장소를 관리할 수 있습니다.

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- DeleteFile
- FileManagement
- UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>휴지 상태의 데이터 암호화

vSAN 데이터 저장소는 기본적으로 저장소 데이터 암호화를 사용 합니다. 암호화 솔루션은 KMS 기반 이며 키 관리를 위한 vCenter 작업을 지원 합니다. 키는 암호화 되어 Azure Key Vault 마스터 키로 래핑됩니다. 클러스터에서 호스트를 제거 하면 Ssd의 데이터가 즉시 무효화 됩니다.

## <a name="scaling"></a>크기 조정

클러스터에 호스트를 추가 하 여 기본 클러스터 저장소 용량 크기를 조정 합니다. AVS36 호스트를 사용 하는 클러스터의 경우 추가 된 각 호스트와 함께 원시 클러스터 전체 용량이 15.4 TB 씩 증가 합니다. 호스트는 클러스터에 추가 하는 데 10 분 정도 걸립니다.  클러스터 크기 조정에 대 한 지침은 [사설 클라우드 크기 조정 자습서][tutorial-scale-private-cloud]를 참조 하세요.

## <a name="azure-storage-integration"></a>Azure storage 통합

사설 클라우드에서 실행 되는 워크 로드에서 Azure storage 서비스를 사용할 수 있습니다. Azure storage 서비스에는 저장소 계정, Table Storage 및 Blob Storage 포함 됩니다. Azure storage 서비스에 대 한 워크 로드 연결은 인터넷을 트래버스 하지 않습니다. 이 연결을 통해 보안을 강화 하 고 사설 클라우드 워크 로드에서 SLA 기반 Azure storage 서비스를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware 솔루션 저장소 개념에 대해 설명 했으므로 다음에 대해 알아볼 수 있습니다.

- [사설 클라우드 id 개념](concepts-identity.md).
- [Azure VMware 솔루션에 대 한 Vsphere 역할 기반 액세스 제어](concepts-identity.md).
- [Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법](enable-azure-vmware-solution.md)
- [Azure VMware 솔루션으로 Azure NetApp Files](netapp-files-with-azure-vmware-solution.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
