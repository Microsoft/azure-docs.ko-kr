---
title: Azure HPC 캐시 탑재
description: Azure HPC 캐시 서비스에 클라이언트를 연결 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582222"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC 캐시 탑재

캐시를 만든 후에는 NFS 클라이언트에서 간단한 mount 명령을 사용 하 여 해당 캐시에 액세스할 수 있습니다.

Mount 명령은 다음 두 요소로 구성 됩니다.

* 캐시의 탑재 주소 중 하나 (캐시 개요 페이지에 나열 됨)
* 저장소 대상을 만들 때 설정 하는 가상 네임 스페이스 경로

![오른쪽 아래에 있는 탑재 주소 목록 주위에 강조 표시 상자가 있는 Azure HPC 캐시 인스턴스의 개요 페이지 스크린샷](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> 캐시 탑재 주소는 캐시의 서브넷 내에 있는 네트워크 인터페이스에 해당 합니다. 리소스 그룹에서 이러한 Nic는 `-cluster-nic-`으로 끝나는 이름 및 숫자로 나열 됩니다. 이러한 인터페이스를 변경 하거나 삭제 하지 마세요. 캐시를 사용할 수 없게 됩니다.

가상 네임 스페이스 경로는 **저장소 대상** 페이지에 표시 됩니다. 개별 저장소 대상 이름을 클릭 하 여 관련 된 집계 네임 스페이스 경로를 포함 하 여 세부 정보를 확인 합니다.

![테이블의 경로 열에 있는 항목 주위에 강조 표시 상자가 있는 캐시의 저장소 대상 패널 스크린샷](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Mount 명령 구문

다음과 같은 탑재 명령을 사용 합니다.

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

예:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

이 명령이 성공한 후에는 클라이언트의 ``hpccache`` 디렉터리에 저장소 내보내기의 내용이 표시 되어야 합니다.

> [!NOTE] 
> 클라이언트는 캐시를 보관 하는 가상 네트워크 및 서브넷에 액세스할 수 있어야 합니다. 예를 들어 동일한 가상 네트워크 내에서 클라이언트 Vm을 만들거나 외부에서 액세스 하기 위해 가상 네트워크에서 엔드포인트, 게이트웨이 또는 다른 솔루션을 사용 합니다. 캐시의 서브넷 내에 호스트 될 수 있는 다른 항목이 없다는 것을 명심 하십시오.

### <a name="mount-command-options"></a>탑재 명령 옵션

강력한 클라이언트 탑재의 경우 탑재 명령에 다음 설정 및 인수를 전달 합니다. 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 권장 탑재 명령 설정 | |
--- | --- 
``hard`` | Azure HPC 캐시에 소프트 탑재는 응용 프로그램 오류 및 가능한 데이터 손실과 관련이 있습니다. 
``proto=netid`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=netid`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=n`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

## <a name="next-steps"></a>다음 단계

* 캐시의 저장소 대상으로 데이터를 이동 하려면 [새 Azure Blob Storage 채우기](hpc-cache-ingest.md)를 참조 하세요.
