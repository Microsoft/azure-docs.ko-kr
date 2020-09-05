---
title: Avere vFXT 탑재 - Azure
description: Azure 용 Avere vFXT에서 vFXT 클러스터에 클라이언트를 연결 하는 방법 및 클러스터 노드 간에 클라이언트 트래픽 부하를 분산 하는 방법에 대해 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 44a4e1293bc4c5a54e1e345d5cf95ba307a7b120
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272574"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Avere vFXT 클러스터 탑재

클라이언트 머신을 vFXT 클러스터에 연결하려면 다음 단계를 수행합니다.

1. 클러스터 노드 간에 클라이언트 트래픽의 부하를 분산하는 방법을 결정합니다. 자세한 내용은 아래의 [클라이언트 부하 분산](#balance-client-load)을 참조하세요.
1. 탑재할 IP 주소와 접합 경로를 식별합니다.
1. 적절한 인수가 포함된 [mount 명령](#mount-command-arguments)을 실행합니다.

## <a name="balance-client-load"></a>클라이언트 부하 분산

클러스터의 모든 노드 간에 클라이언트 요청의 부하를 분산하려면 클라이언트를 모든 범위의 클라이언트 측 IP 주소 범위에 탑재해야 합니다. 이 작업을 자동화하는 몇 가지 간단한 방법이 있습니다.

> [!TIP]
> 다른 부하 분산 방법은 크거나 복잡한 시스템에 적합할 수 있습니다. [지원 티켓을 열어](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) 도움을 받습니다.
>
> 자동 서버 쪽 부하 분산에 DNS 서버를 사용하려면 Azure 내에서 사용자 고유의 DNS 서버를 설정하고 관리해야 합니다. 이 경우 [Avere 클러스터 DNS 구성](avere-vfxt-configure-dns.md) 문서에 따라 vFXT 클러스터에 대한 라운드 로빈 DNS를 구성할 수 있습니다.

### <a name="sample-balanced-client-mounting-script"></a>부하 분산된 클라이언트 탑재 샘플 스크립트

이 코드 예제는 클라이언트 IP 주소를 무작위 요소로 사용하여 클라이언트를 모든 vFXT 클러스터의 사용 가능한 IP 주소로 배포합니다.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

위의 함수는 [Avere vFXT 예제](https://github.com/Azure/Avere#tutorials) 사이트에서 사용할 수 있는 Batch 예제의 일부입니다.

## <a name="create-the-mount-command"></a>mount 명령 만들기

> [!NOTE]
> Avere vFXT 클러스터를 만들 때 새 Blob 컨테이너를 만들지 않은 경우 클라이언트를 탑재 하기 전에 [저장소 구성](avere-vfxt-add-storage.md) 에 설명 된 대로 저장소 시스템을 추가 합니다.

클라이언트에서 ``mount`` 명령은 vFXT 클러스터의 가상 서버(vserver)를 로컬 파일 시스템의 경로에 매핑합니다. 형식은 ``mount <vFXT path> <local path> {options}``입니다.

Mount 명령에는 세 가지 요소가 있습니다.

* vFXT path-아래에 설명 된 클러스터에 있는 IP 주소와 네임 스페이스의 병합 경로 조합입니다.
* 로컬 경로 - 클라이언트의 경로
* 탑재 명령 옵션- [탑재 명령 인수](#mount-command-arguments) 에 나열 됩니다.

### <a name="junction-and-ip"></a>접합 및 IP

vserver 경로는 *IP 주소*와 *네임스페이스 접합*에 대한 경로의 조합입니다. 네임스페이스 접합은 스토리지 시스템을 추가할 때 정의된 가상 경로입니다.

클러스터가 Blob storage를 사용 하 여 만들어진 경우에는 해당 컨테이너에 대 한 네임 스페이스 경로가 `/msazure`

예: ``mount 10.0.0.12:/msazure /mnt/vfxt``

클러스터를 만든 후 저장소를 추가한 경우에는 네임 스페이스를 만들 때 네임 스페이스 **경로** 에 설정 하는 값이 네임 스페이스 병합 경로입니다. 예를 들어 ``/avere/files``를 네임스페이스 경로로 사용한 경우 클라이언트에서 *IP_address*:/avere/files를 해당 로컬 탑재 지점에 탑재합니다.

![네임스페이스 경로 필드에 /avere/files가 있는 "새 접합 추가" 대화 상자](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

IP 주소는 vserver에 대해 정의된 클라이언트 측 IP 주소 중 하나입니다. Avere 제어판의 두 곳에서 클라이언트 측 IP 범위를 찾을 수 있습니다.

* **VServers** 테이블(대시보드 탭)

  ![그래프 아래의 데이터 테이블에서 VServer 탭이 선택되고 IP 주소 섹션이 원으로 표시된 Avere 제어판의 대시보드 탭](media/avere-vfxt-ip-addresses-dashboard.png)

* **클라이언트 측 네트워크** 설정 페이지

  ![특정 vserver에 대한 테이블의 주소 범위 섹션 주위가 원으로 표시된 설정 > VServer > 클라이언트 측 네트워크 구성 페이지](media/avere-vfxt-ip-addresses-settings.png)

각 클라이언트를 탑재할 때 경로 외에도 아래에서 설명하는 [mount 명령 인수](#mount-command-arguments)를 포함합니다.

### <a name="mount-command-arguments"></a>mount 명령 인수

클라이언트 탑재를 원활하게 수행하려면 mount 명령에 다음 설정 및 인수를 전달합니다.

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 필수 설정 | Description |
--- | ---
``hard`` | vFXT 클러스터에 대한 소프트 탑재는 애플리케이션 오류 및 가능한 데이터 손실과 관련이 있습니다.
``proto=netid`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=netid`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=n`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

## <a name="next-steps"></a>다음 단계

클라이언트를 탑재 한 후에는이를 사용 하 여 클러스터의 새 Blob storage 컨테이너에 데이터를 복사할 수 있습니다. 새 저장소를 채우지 않아도 되는 경우 다른 설치 작업에 대 한 자세한 내용은 다음 링크를 참조 하세요.

* [클러스터 코어로 데이터 이동 필터](avere-vfxt-data-ingest.md) -여러 클라이언트 및 스레드를 사용 하 여 새 핵심 필터로 데이터를 효율적으로 업로드 하는 방법
* [클러스터 튜닝 사용자 지정](avere-vfxt-tuning.md) - 워크로드에 맞게 클러스터 설정을 조정합니다.
* [클러스터 관리](avere-vfxt-manage-cluster.md) - 클러스터를 시작 또는 중지하고 노드를 관리하는 방법입니다.
