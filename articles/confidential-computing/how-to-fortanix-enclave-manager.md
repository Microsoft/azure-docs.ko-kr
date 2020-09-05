---
title: Fortanix Enclave Manager를 사용 하 여 응용 프로그램을 실행 하는 방법
description: Fortanix Enclave Manager를 사용 하 여 컨테이너 화 된 이미지를 변환 하는 방법을 알아봅니다.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: b5b0f9acc45dba81bb7653c844bb8c78a8bd29ba
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826296"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>방법: Fortanix Enclave Manager를 사용 하 여 응용 프로그램 실행 

[Fortanix](https://www.fortanix.com/)에서 [Fortanix Enclave Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) 및 [Fortanix Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) 를 사용 하 여 Azure 기밀 컴퓨팅에서 응용 프로그램 실행을 시작 합니다.


Fortanix는 Azure 인프라를 기반으로 구축 된 제품 및 서비스를 포함 하는 타사 소프트웨어 공급 업체입니다. Azure에서 유사한 기밀 컴퓨팅 서비스를 제공 하는 다른 타사 공급자가 있습니다.

> [!Note] 
 > 이 문서에서 참조 하는 제품은 MICROSOFT에서 제어 하 고 있지 않습니다. MICROSOFT는 편의를 위해서만이 정보를 제공 하 고, 이러한 타사 제품에 대 한 참조는 MICROSOFT의 보증을 암시 하지 않습니다.



이 자습서에서는 응용 프로그램 이미지를 기밀 계산 보호 이미지로 변환 하는 방법을 보여 줍니다. 이 환경에서는 Azure의 DCsv2 시리즈 Intel SGX 지원 가상 머신에서 제공 되는 [Fortanix](https://www.fortanix.com/) software를 사용 합니다. 이 솔루션은 id 확인 및 데이터 액세스 제어와 같은 중요 한 보안 정책을 오케스트레이션 합니다.

 Fortanix에 대 한 지원은 [Fortanix 여유 커뮤니티](https://fortanix.com/community/) 에 가입 하 고 채널 #enclavemanager를 사용 합니다.


## <a name="prerequisites"></a>사전 준비 사항

1. Fortanix Enclave Manager 계정이 없는 경우 시작 하기 전에 [등록](https://em.fortanix.com/auth/sign-up) 합니다.
1. 변환 된 응용 프로그램 이미지를 푸시하는 개인 [Docker](https://docs.docker.com/) 레지스트리
1. Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Fortanix Enclave Manager에 응용 프로그램 추가
1. [FORTANIX EM](https://fortanix.com) 에 로그인 합니다.
1. **계정 페이지로 이동** 하 고 **계정 추가** 를 선택 하 여 새 계정을 만듭니다. 
    
![계정 만들기](media/how-to-fortanix-enclave-manager/create-account.png)

1. 계정을 만든 후 **선택** 하 여 새로 만든 계정을 선택 합니다. 이제 계산 노드 등록 및 응용 프로그램 만들기를 시작할 수 있습니다. 
1. **+ 응용 프로그램** 단추를 선택 하 여 응용 프로그램을 추가 합니다. 이 예제에서는 Flask Server Enclave OS 응용 프로그램을 추가 합니다. 

1. Enclave OS 응용 프로그램에 대 한 **추가** 단추를 선택 합니다. 

    ![애플리케이션 추가](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > 이 자습서에서는 Enclave OS 응용 프로그램만 추가 하는 방법을 다룹니다. EDP Rust 응용 프로그램을 Fortanix Enclave Manager로 가져오는 방법에 대해 [자세히](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) 알아보세요. 

6. 이 자습서에서는 샘플 응용 프로그램에 대해 Fortanix의 docker 레지스트리를 사용 합니다. 다음 정보에서 세부 정보를 입력 합니다. 개인 docker 레지스트리를 사용 하 여 출력 이미지를 유지 합니다. 
 
    - **응용 프로그램 이름**: Python 응용 프로그램 서버
    - **설명**: Python Flask 서버
    - **입력 이미지 이름**: fortanix/python-flask
    - **출력 이미지 이름**: fortanx-private/python-flask-sgx
    - **ISVPRODID**: 1
    - **Isvsvm**: 1
    - **메모리 크기**: 1gb
    - **스레드 수**: 128

    *선택 사항*: 응용 프로그램을 실행 합니다.
    - **Docker 허브**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **앱**: fortanix/python-flask

        다음 명령을 실행합니다.
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. 인증서를 추가 합니다. 아래 세부 정보를 사용 하 여 정보를 입력 하 고 **다음**을 선택 합니다.
    - **도메인**: myapp. domain. dom
    - **유형**: Enclave Manager에서 발급 한 인증서 
    - **키 경로**:/appkey.pem
    - **키 유형**: RSA
    - **인증서 경로**:/appcert.pem
    - **RSA 키 크기**: 2048 비트
    

## <a name="create-an-image"></a>이미지 만들기
Fortanix EM 이미지는 소프트웨어 릴리스 또는 응용 프로그램의 버전입니다. 각 이미지는 하나의 MRENCLAVE (enclave hash)와 연결 되어 있습니다. 
1. **이미지 추가** 페이지에서 **출력 이미지 이름**에 대 한 **레지스트리 자격 증명** 을 입력 합니다. 이러한 자격 증명은 이미지가 푸시되는 개인 docker 레지스트리에 액세스 하는 데 사용 됩니다. 

    ![이미지 만들기](media/how-to-fortanix-enclave-manager/create-image.png)
1. 이미지 태그를 제공 하 고 **만들기**를 선택 합니다.

    ![태그 추가](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>도메인 및 이미지 허용 목록 
도메인이 허용 목록에 추가 되는 응용 프로그램은 Fortanix Enclave Manager에서 TLS 인증서를 가져옵니다. 마찬가지로, 변환 된 이미지에서 응용 프로그램을 실행 하는 경우 Fortanix Enclave Manager에 연결 하려고 합니다. 그러면 응용 프로그램은 TLS 인증서를 요청 합니다. 

왼쪽의 **작업** 탭으로 전환 하 고 보류 중인 요청을 승인 하 여 도메인 및 이미지를 허용 합니다. 

## <a name="enroll-compute-node-agent-in-azure"></a>Azure에서 계산 노드 에이전트 등록

### <a name="generate-and-copy-join-token"></a>조인 토큰 생성 및 복사
Fortanix Enclave Manager에서 토큰을 만듭니다. 이 토큰을 통해 Azure의 계산 노드에서 자신을 인증할 수 있습니다. Azure 가상 컴퓨터에이 토큰을 제공 해야 합니다.
1. 관리 콘솔에서 **+ 노드 등록** 단추를 선택 합니다. 
1. **토큰 생성** 을 선택 하 여 조인 토큰을 생성 합니다. 토큰을 복사 합니다.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Azure Marketplace에서 노드를 Fortanix 노드 에이전트로 등록

Fortanix 노드 에이전트를 만들면 가상 컴퓨터, 네트워크 인터페이스, 가상 네트워크, 네트워크 보안 그룹 및 공용 IP 주소가 Azure 리소스 그룹에 배포 됩니다. Azure 구독은 가상 컴퓨터에 대해 시간당 요금이 청구 됩니다. Fortanix 노드 에이전트를 만들기 전에 DCsv2 시리즈에 대 한 Azure [virtual machine 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 를 검토 하세요. 사용 하지 않는 경우 Azure 리소스를 삭제 합니다. 

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) 로 이동 하 여 Azure 자격 증명으로 로그인 합니다.
1. 검색 창에서 **Fortanix 기밀 컴퓨팅 노드 에이전트**를 입력 합니다. **Fortanix 기밀 컴퓨팅 노드 에이전트** 라는 검색 상자에 표시 되는 앱을 선택 하 여 제공의 홈 페이지로 이동 합니다. 
     ![marketplace 검색](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. **지금 가져오기**를 선택 하 고 필요한 경우 정보를 입력 하 고 **계속**을 선택 합니다. Azure Portal으로 리디렉션됩니다. 
1. **만들기** 를 선택 하 여 Fortanix 기밀 컴퓨팅 노드 에이전트 배포 페이지를 입력 합니다.
1. 이 페이지에서 가상 머신을 배포 하기 위한 정보를 입력 합니다. 특히이 VM은 Azure에서 Fortanix Node Agent 소프트웨어가 설치 된 DCsv2 시리즈 Intel SGX 지원 가상 머신입니다. 노드 에이전트는 변환 된 이미지가 Azure의 Intel SGX 노드에서 안전 하 게 실행 될 수 있도록 합니다.  가상 컴퓨터 및 관련 리소스를 배포 하려는 **구독** 및 **리소스 그룹** 을 선택 합니다. 
 
    > [!NOTE]
    > Azure에서 DCsv2 시리즈 가상 머신을 배포 하는 경우 제약 조건이 있습니다. 추가 코어에 대 한 할당량을 요청 해야 할 수도 있습니다. 자세한 내용은 [Azure vm의 기밀 컴퓨팅 솔루션](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution) 을 참조 하세요. 

1. 사용 가능한 지역을 선택합니다.
1. **노드 이름** 필드에 가상 컴퓨터의 이름을 입력 합니다. 
1. 가상 컴퓨터에 대 한 인증을 위해 및 사용자 이름 및 암호 (또는 SSH 키)를 입력 합니다.
1. 기본 OS 디스크 크기를 200으로 유지 하 고 VM 크기를 선택 합니다 (이 자습서에서는 충분 Standard_DC4s_v2).
1. 이전에 생성 된 토큰을 **조인 토큰** 필드에 붙여넣습니다.

     ![리소스 배포](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. **검토 + 만들기**를 선택합니다. 유효성 검사를 통과 했는지 확인 한 다음 **만들기**를 선택 합니다. 모든 리소스가 배포 되 면 이제 계산 노드가 Enclave Manager에 등록 됩니다. 

## <a name="run-the-application-image-on-the-compute-node"></a>계산 노드에서 응용 프로그램 이미지 실행
다음 명령을 실행 하 여 응용 프로그램을 실행 합니다. 노드 IP, 포트 및 변환 된 이미지 이름을 특정 응용 프로그램에 대 한 입력으로 변경 해야 합니다. 
 
이 자습서에서 실행할 명령은 다음과 같습니다.     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

각 항목이 나타내는 의미는 다음과 같습니다. 
- *52.152.206.164* 은 노드 에이전트 호스트 IP입니다.
- *9092* 는 노드 에이전트가 수신 대기 하는 포트입니다.
- *fortanix-* Fortanix Enclave Manage 웹 포털에 있는 **Images** 테이블의 **Image (이미지** ) 열 아래에 있는 이미지 탭에서 찾을 수 있는 변환 된 앱입니다. 
    
## <a name="verify-and-monitor-the-running-application"></a>실행 중인 응용 프로그램 확인 및 모니터링
1. [Fortanix Enclave Manager](https://em.fortanix.com/console) 로 돌아가기
1. 노드를 등록 한 **계정** 내에서 작업 중인지 확인 합니다.
1. 왼쪽 탐색 창에서 위쪽 아이콘을 선택 하 여 **관리 콘솔로** 이동 합니다. 
1. **응용 프로그램** 탭을 선택 합니다.
1. 연결 된 계산 노드를 사용 하는 실행 중인 응용 프로그램이 있는지 확인 합니다.


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 경우 리소스 그룹, 가상 머신 및 관련 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제 하면 변환 된 이미지와 연결 된 노드의 등록을 취소 합니다. 

가상머신에 대한 리소스 그룹을 선택한 다음 **삭제**를 선택합니다. 리소스 그룹의 이름을 확인하고 리소스 삭제를 마칩니다.

만든 Fortanix Enclave Manager 계정을 삭제 하려면 Enclave Manager의 [계정 페이지로](https://em.fortanix.com/accounts) 이동 합니다. 삭제 하려는 계정 위로 마우스를 가져갑니다. 오른쪽 위 모서리에서 세로 검은색 점을 선택 하 고 **계정 삭제**를 선택 합니다.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Fortanix 도구를 사용 하 여 응용 프로그램 이미지를 기밀 컴퓨팅 가상 머신을 기반으로 실행 되도록 변환 했습니다. Azure의 기밀 컴퓨팅 가상 머신에 대한 자세한 내용은 [Virtual Machines 솔루션](virtual-machine-solutions.md)을 참조하세요. 

Azure의 기밀 컴퓨팅 제품에 대해 자세히 알아보려면 [azure 기밀 컴퓨팅 개요](overview.md) 를 참조 하세요.

 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 및 [scone](https://sconedocs.github.io)와 같이 Azure에서 다른 타사 제품을 사용 하 여 유사한 작업을 완료 하는 방법을 알아봅니다.  