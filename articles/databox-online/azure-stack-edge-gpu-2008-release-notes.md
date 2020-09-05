---
title: Azure Stack Edge Preview 릴리스 정보 | Microsoft Docs
description: 일반 공급 릴리스를 실행 하는 Azure Data Box Gateway에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: de0847beb92ebc95e1998d88cae93dbc19c3fb27
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180051"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>GPU 미리 보기 릴리스 정보를 사용 하 여 Azure Stack Edge

다음 릴리스 정보는 GPU를 사용 하는 Azure Stack Edge 장치의 2008 preview 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Azure Stack Edge 장치를 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 하세요.

이 **Azure Stack Edge 2008** 릴리스는 다음 소프트웨어 버전에 해당 합니다.

- **2.1.1328.1904**



## <a name="known-issues-in-ga-release"></a>GA 릴리스의 알려진 문제

다음 표에서는 Azure Stack Edge 장치에 대 한 알려진 문제를 요약 하 여 보여 줍니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge + Azure SQL | SQL database를 만들려면 관리자 액세스 권한이 필요 합니다.   |의 1-2 단계 대신 다음 단계를 수행 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) 합니다. <ul><li>장치의 로컬 UI에서 계산 인터페이스를 사용 하도록 설정 합니다. Compute **> 포트 # > 계산 > 적용을 선택 합니다.**</li><li>장치에 연결 된 PowerShell 창에서를 실행 `Add-HcsComputeNetwork` 합니다. </li><li>`sqlcmd`클라이언트 컴퓨터에서 다운로드https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>계산 인터페이스 IP 주소 (사용 하도록 설정 된 포트)에 연결 하 여 주소 끝에 ", 1401"을 추가 합니다.</li><li>최종 명령은 sqlcmd-S {Interface IP}, 1401-U SA-P "와 같습니다. Passw0rd ".</li>그러면 현재 설명서의 3-4 단계가 동일 해야 합니다. </li></ul> |
| **2.** |새로 고침| **새로 고침** 을 통해 복원 된 blob에 대 한 증분 변경은 지원 되지 않습니다. |Blob 끝점의 경우 새로 고침 후 blob의 부분 업데이트로 인해 업데이트가 클라우드에 업로드 되지 않을 수 있습니다. 예를 들어 다음과 같은 작업의 시퀀스입니다.<ul><li>클라우드에서 blob을 만듭니다. 또는 장치에서 이전에 업로드 된 blob을 삭제 합니다.</li><li>새로 고침 기능을 사용 하 여 클라우드에서 blob을 어플라이언스로 새로 고칩니다.</li><li>Azure SDK REST Api를 사용 하 여 blob의 일부만 업데이트 합니다.</li></ul>이러한 작업으로 인해 blob의 업데이트 된 섹션이 클라우드에서 업데이트 되지 않을 수 있습니다. <br>**해결 방법**: robocopy와 같은 도구를 사용 하거나 탐색기 또는 명령줄을 통해 일반 파일 복사를 사용 하 여 전체 blob을 바꿉니다.|
|**3.**|제한|조정 하는 동안 장치에 새 쓰기가 허용 되지 않는 경우 NFS 클라이언트에서 수행 하는 쓰기가 "권한 거부" 오류로 인해 실패 합니다.| 오류는 다음과 같이 표시 됩니다.<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: ' test ' 디렉터리를 만들 수 없습니다. 사용 권한이 거부 되었습니다.|
|**4.**|Blob Storage 수집|Blob storage 수집에 AzCopy 버전 10을 사용 하는 경우 다음 인수를 사용 하 여 AzCopy를 실행 합니다. `Azcopy <other arguments> --cap-mbps 2000`| AzCopy에 대해 이러한 제한을 제공 하지 않으면 잠재적으로 많은 수의 요청을 장치로 전송 하 여 서비스에 문제가 발생할 수 있습니다.|
|**5.**|계층화 된 저장소 계정|계층화 된 저장소 계정을 사용 하는 경우 다음이 적용 됩니다.<ul><li> 블록 blob만 지원 됩니다. 페이지 Blob은 지원되지 않습니다.</li><li>스냅숏 또는 복사 API는 지원 되지 않습니다.</li><li> 에서 `distcp` 복사 작업을 많이 사용 하므로 Hadoop 워크 로드 수집은 지원 되지 않습니다.</li></ul>||
|**6.**|NFS 공유 연결|여러 프로세스에서 동일한 공유로 복사 하 고 `nolock` 특성을 사용 하지 않는 경우 복사 하는 동안 오류가 표시 될 수 있습니다.|`nolock`NFS 공유에 파일을 복사 하려면 특성을 탑재 명령에 전달 해야 합니다. 예: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`|
|**7.**|Kubernetes 클러스터|Kubernetes 클러스터를 실행 하는 장치에 업데이트를 적용 하는 경우 kubernetes 가상 머신이 다시 시작 되 고 재부팅 됩니다. 이 인스턴스에서 지정 된 복제본과 함께 배포 되는 pod만 업데이트 후 자동으로 복원 됩니다.  |복제 세트를 지정 하지 않고 복제 컨트롤러 외부에서 개별 pod를 만든 경우 이러한 pod는 장치 업데이트 후 자동으로 복원 되지 않습니다. 이러한 pod를 복원 해야 합니다.<br>복제 세트는 노드 실패 또는 중단 노드 업그레이드와 같은 어떤 이유로 든 삭제 되었거나 종료 된 pod를 대체 합니다. 따라서 응용 프로그램에 단일 pod만 필요한 경우에도 복제본 집합을 사용 하는 것이 좋습니다.|
|**8.**|Kubernetes 클러스터|Azure Stack Edge의 Kubernetes는 투구 v3 이상 에서만 지원 됩니다. 자세한 내용은 질문과 [대답: Tiller 제거](https://v3.helm.sh/docs/faq/)를 참조 하세요.|
|**9.**|Azure Arc + Azure Stack Edge|웹 프록시가 Azure Stack Edge 장치에 구성 된 경우 Azure Arc 배포가 지원 되지 않습니다.||
|**5-10.**|Kubernetes |포트 31000은 Kubernetes 대시보드에 예약 되어 있습니다. 마찬가지로 기본 구성에서 IP 주소 10.96.0.1와 10.96.0.10는 각각 Kubernetes service 및 Core DNS 서비스용으로 예약 되어 있습니다.|예약 된 Ip를 사용 하지 마십시오.|
|**pt.**|Kubernetes |Kubernetes는 현재 다중 프로토콜 LoadBalancer 서비스를 허용 하지 않습니다. 예를 들어 TCP와 UDP 모두에서 수신 대기 해야 하는 DNS 서비스입니다. |MetalLB를 사용 하는 Kubernetes의이 제한 사항을 해결 하기 위해 동일한 pod 선택기에 두 가지 서비스 (TCP에 대해 하나, 하나는 UDP 용)를 만들 수 있습니다. 이러한 서비스는 동일한 공유 키와 loadBalancerIP를 사용 하 여 동일한 IP 주소를 공유 합니다. 사용 가능한 IP 주소 보다 많은 서비스를 사용 하는 경우 ip를 공유할 수도 있습니다. <br> 자세한 내용은 [IP 주소 공유](https://metallb.universe.tf/usage/#ip-address-sharing)를 참조 하세요.|
|**10.**|Kubernetes 클러스터|기존 Azure IoT Edge marketplace 모듈은 Azure Stack Edge 장치에서 IoT Edge에 대 한 호스팅 플랫폼으로 Kubernetes 클러스터에서 실행 되지 않습니다.|모듈은 Azure Stack Edge 장치에 배포 되기 전에 수정 해야 합니다. 자세한 내용은 marketplace에서 Azure IoT Edge 모듈을 수정 하 여 Azure Stack Edge 장치에서 실행을 참조 하세요.<!-- insert link-->|
|**/10.**|Kubernetes |파일 기반 바인드 탑재는 Azure Stack에 지 장치의 Kubernetes에서 Azure IoT Edge 지원 되지 않습니다.|IoT Edge는 번역 계층을 사용 하 여 `ContainerCreate` 옵션을 Kubernetes 구문으로 변환 합니다. `Binds`호스트 경로 디렉터리에 맵 만들기 또는 만들기 및 파일 기반 바인드 탑재는 IoT Edge 컨테이너의 경로에 바인딩할 수 없습니다.|
|**14.**|Kubernetes |IoT Edge에 대 한 자체 인증서를 가져와서 Azure Stack에 지 장치에 추가 하는 경우, 새 인증서는 투구 차트 업데이트의 일부로 선택 되지 않습니다.|이 문제를 해결 하려면 [장치의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md)합니다. `iotedged`Pod를 다시 시작 `edgehub` 합니다.|

## <a name="next-steps"></a>다음 단계

- [GPU를 사용 하 여 Azure Stack Edge 장치 배포 준비](azure-stack-edge-gpu-deploy-prep.md)

