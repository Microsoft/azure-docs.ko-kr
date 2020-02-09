---
title: Azure VMware 솔루션 (AVS)-vRealize 자동화를 위해 AVS 사설 클라우드에서 vCenter 설정
description: VMware 사설 클라우드에서 vmware vCenter server를 VMware vRealize Automation의 엔드포인트으로 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 41106498594ac05b944323e5f5e63de739aedf37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024843"
---
# <a name="set-up-vcenter-on-your-avs-private-cloud-for-vmware-vrealize-automation"></a>VMware vRealize 자동화를 위해 AVS 사설 클라우드에서 vCenter 설정

Vmware 사설 클라우드에서 vmware vCenter server를 VMware vRealize 자동화에 대 한 엔드포인트으로 설정할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

VCenter server를 구성 하기 전에 다음 작업을 완료 합니다.

* 온-프레미스 환경과 AVS 사설 클라우드 간에 [사이트 간 VPN 연결](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) 을 구성 합니다.
* [온-프레미스 dns 요청](on-premises-dns-setup.md) 을 AVS 사설 클라우드의 dns 서버로 전달 하는 Dns를 구성 합니다.
* [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 을 제출 하 여 다음 표에 나열 된 사용 권한 집합과 함께 Vrealize Automation IaaS 관리 사용자를 만듭니다.

| 특성 값 | 사용 권한 |
------------ | ------------- |  
| 데이터 저장소 |  공간 할당 <br> 데이터 저장소 찾아보기 |
| 데이터 저장소 클러스터 | 데이터 저장소 클러스터 구성 |
| 폴더 | 폴더 만들기 <br>폴더 삭제 |
| 글로벌 |  사용자 지정 특성 관리<br>사용자 지정 특성 설정 |
| 네트워크 | 네트워크 할당 |
| 권한 | 권한 수정 |
| 리소스 | 리소스 풀에 VM 할당<br>가상 컴퓨터의 전원 끄기 마이그레이션<br>가상 컴퓨터의 마이그레이션 전원 |
| 가상 컴퓨터 인벤토리 |  기존에서 만들기<br>새로 만들기<br>이동<br>제거 | 
| 가상 컴퓨터 상호 작용 |  CD 미디어 구성<br>콘솔 상호 작용<br>디바이스 연결<br>전원 끄기<br>전원 켜기<br>다시 설정<br>일시 중지됨<br>도구 설치 | 
| 가상 컴퓨터 구성 |  기존 디스크 추가<br>새 디스크 추가<br>추가 또는 제거<br>디스크 제거<br>고급<br>CPU 수 변경<br>리소스 변경<br>가상 디스크 확장<br>디스크 변경 내용 추적<br>메모리<br>장치 설정 수정<br>이름 바꾸기<br>Set Annotation (버전 5.0 이상)<br>설정<br>스왑 스왑 배치 |
| 프로비저닝 |  사용자 지정<br>템플릿 복제<br>가상 컴퓨터 복제<br>템플릿 배포<br>사용자 지정 사양 읽기 |
| 가상 컴퓨터 상태 | 스냅숏 만들기<br>스냅숏 제거<br>스냅숏으로 되돌리기 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>온-프레미스 환경에서 vRealize 자동화 설치

1. VRealize Automation IaaS 서버 어플라이언스에 AVS를 지 원하는 IaaS 관리자로 로그인 합니다.
2. Vsphere 자동화 엔드포인트에 대 한 vSphere 에이전트를 배포 합니다.
    1. Https://*vra-url*: 5480/installer로 이동 합니다. 여기서 *vra-Url* 은 VRA Automation 관리 UI에 액세스 하는 데 사용 하는 url입니다.
    2. **IaaS 설치 관리자** 를 클릭 하 여 설치 관리자를 다운로드 합니다.<br>
    설치 관리자 파일의 명명 규칙은*vra-url*@5480.exesetup_ 됩니다.
    3. 설치 관리자를 실행합니다. 시작 화면에서 **다음**을 클릭 합니다.
    4. EULA에 동의 하 고 **다음**을 클릭 합니다.
    5. 로그인 정보를 입력 하 고 **인증서 수락**을 클릭 한 후 **다음**을 클릭 합니다.
    vRA 자격 증명을 ![](media/configure-vra-endpoint-login.png)
    6. **사용자 지정 설치** 및 **프록시 에이전트** 를 선택 하 고 **다음**을 클릭 합니다.
    ![vRA 설치 유형](media/configure-vra-endpoint-install-type.png)
    7. IaaS 서버 로그인 정보를 입력 하 고 **다음**을 클릭 합니다. Active Directory를 사용 하는 경우 **domain\user** 형식으로 사용자 이름을 입력 합니다. 그렇지 않으면 **user@domain** 형식을 사용 합니다.
    vRA 로그인 정보를 ![](media/configure-vra-endpoint-account.png)
    8. 프록시 설정에 대해 **에이전트 유형에**대 한 **vsphere** 입력 합니다. 에이전트의 이름을 입력합니다.
    9. **관리자 서비스 호스트** 의 IAAS 서버 FQDN과 **모델 관리자 웹 서비스 호스트** 필드를 입력 합니다. **테스트** 를 클릭 하 여 각 FQDN 값에 대 한 연결을 테스트 합니다. 테스트가 실패할 경우 IaaS 서버 호스트 이름이 확인 되도록 DNS 설정을 수정 합니다.
    10. AVS 사설 클라우드의 vCenter 서버 엔드포인트 이름을 입력 합니다. 나중에 구성 프로세스에서 사용할 이름을 기록 합니다.

        ![vRA 설치 프록시](media/configure-vra-endpoint-proxy.png)

    11. **다음**을 클릭합니다.
    12. **Install**을 클릭합니다.

## <a name="configure-the-vsphere-agent"></a>VSphere 에이전트 구성

1. Https://*vra-url*/vcac로 이동 하 여 **configurationadmin**으로 로그인 합니다.
2. **엔드포인트 > 엔드포인트** > **인프라** **를 선택**합니다.
3. **새** > **가상** > **vsphere**를 선택 합니다.
4. 이전 절차에서 지정한 vSphere 엔드포인트 이름을 입력 합니다.
5. **주소**에 대해 https://*vcenter-fqdn*/Sdk 형식으로 AVS 사설 클라우드 vCenter Server URL을 입력 합니다. 여기서 *vcenter-fqdn* 은 vcenter 서버의 이름입니다.
6. AVS에서 만든 vRealize Automation IaaS 관리 사용자에 대 한 자격 증명을 입력 합니다.
7. **연결 테스트** 를 클릭 하 여 사용자 자격 증명의 유효성을 검사 합니다. 테스트가 실패 하면 URL, 계정 정보 및 [엔드포인트 이름을](#verify-the-endpoint-name) 확인 하 고 다시 테스트 합니다.
8. 성공적으로 테스트 한 후 **확인** 을 클릭 하 여 vsphere 엔드포인트을 만듭니다.
    vRA 엔드포인트 구성 액세스 ![](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>엔드포인트 이름 확인

올바른 vCenter server 엔드포인트 이름을 식별 하려면 다음을 수행 합니다.

1. IaaS 어플라이언스에서 명령 프롬프트를 엽니다.
2. 디렉터리를 C:\Program Files (x86) \VMware\vCAC\Agents\agent-name로 변경 합니다. 여기서 *에이전트-name* 은 vCenter 서버 엔드포인트에 할당 한 이름입니다.
3. 다음 명령을 실행합니다.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

출력은 다음과 유사 합니다. `managementEndpointName` 필드의 값은 엔드포인트 이름입니다.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
