---
title: Avere vFXT for Azure 배포
description: Azure Marketplace에서 제공 하는 배포 마법사를 사용 하 여 Azure 용 Avere vFXT를 사용 하 여 클러스터를 배포 하는 방법을 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 31d12466186bb7f66197218fbb9675888a35fef3
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272776"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT 클러스터 배포

이 절차에서는 Azure Marketplace에서 제공되는 배포 마법사를 사용하는 과정을 안내합니다. 이 마법사는 Azure Resource Manager 템플릿을 사용하여 클러스터를 자동으로 배포합니다. 양식에 매개 변수를 입력 하 고 **만들기**를 클릭 하면 Azure에서 다음 작업을 자동으로 완료 합니다.

* 클러스터를 배포 하 고 관리 하는 데 필요한 소프트웨어를 포함 하는 기본 VM 인 클러스터 컨트롤러를 만듭니다.
* 새 요소 만들기를 포함 하 여 리소스 그룹 및 가상 네트워크 인프라를 설정 합니다.
* 클러스터 노드 Vm을 만들고 Avere 클러스터로 구성 합니다.
* 요청 된 경우에서 새 Azure Blob 컨테이너를 만들고 클러스터 코어 필터 구성 합니다.

이 문서의 지침을 수행 하면 다음 다이어그램에 표시 된 것 처럼 가상 네트워크, 서브넷, 클러스터 컨트롤러 및 vFXT 클러스터가 만들어집니다. 이 다이어그램은 새 Blob 저장소 컨테이너 (표시 되지 않음) 및 서브넷 내 Microsoft 저장소에 대 한 서비스 끝점을 포함 하는 선택적 Azure Blob core 필터를 보여 줍니다.

![Avere 클러스터 구성 요소가 포함 된 세 개의 동심 사각형을 보여 주는 다이어그램입니다. 외부 사각형에는 ' 리소스 그룹 ' 레이블이 지정 되 고 ' Blob storage (선택 사항) ' 레이블이 지정 된 육각형이 포함 됩니다. 의 다음 사각형은 ' Virtual network: 10.0.0.0/16 ' 레이블이 지정 되 고 고유 구성 요소는 포함 되지 않습니다. 가장 안쪽의 사각형에는 ' Subnet: 10.0.0.0/24 ' 레이블이 지정 되 고 ' Cluster controller ' 레이블이 지정 된 VM, ' vFXT nodes (vFXT Cluster) ' 레이블이 지정 된 3 개의 Vm 스택, ' Service endpoint ' 레이블이 지정 된 육각형이 포함 됩니다. 서비스 끝점 (서브넷 내부) 및 blob 저장소 (리소스 그룹의 서브넷 및 vnet 외부)를 연결 하는 화살표가 있습니다. 화살표는 서브넷 및 가상 네트워크 경계를 통과 합니다.](media/avere-vfxt-deployment.png)

만들기 템플릿을 사용하기 전에 다음 필수 구성 요소가 충족되었는지 확인하세요.  

* [새 구독](avere-vfxt-prereqs.md#create-a-new-subscription)
* [구독 소유자 권한](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Storage 서비스 끝점 (필요한 경우)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -기존 가상 네트워크를 사용 하 고 blob 저장소를 만드는 배포에 필요 합니다.

클러스터 배포 단계 및 계획에 대한 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md) 및 [배포 개요](avere-vfxt-deploy-overview.md)를 참조하세요.

## <a name="create-the-avere-vfxt-for-azure"></a>Avere vFXT for Azure 만들기

Avere를 검색 하 고 "Avere vFXT for Azure ARM Template"을 선택 하 여 Azure Portal에서 생성 템플릿에 액세스 합니다.

![브라우저 창에 이동 경로 "새로 만들기 > Marketplace > 모두"와 함께 Azure Portal이 표시됩니다. 모든 페이지에서 검색 필드는 "avere" 라는 용어를 포함 하 고 두 번째 결과는 "Avere vFXT for Azure ARM Template"을 빨간색으로 표시 하 여 강조 표시 합니다.](media/avere-vfxt-template-choose.png)

Avere vFXT for Azure ARM 템플릿 페이지에서 세부 정보를 읽은 후 **만들기** 단추를 클릭 하 여 시작 합니다.

![배포 템플릿의 첫 페이지가 표시된 Azure Marketplace](media/avere-vfxt-deploy-first.png)

템플릿은 4단계로 구성되어 있습니다. 그중 두 단계는 정보 수집 페이지이고 나머지 두 단계는 각각 유효성 검사 및 확인 단계입니다.

* 페이지 1은 클러스터 컨트롤러 VM에 대 한 설정을 수집 합니다.
* 2 페이지는 클러스터 및 서브넷 및 저장소와 같은 추가 리소스를 만들기 위한 매개 변수를 수집 합니다.
* 3 페이지는 선택 항목을 요약 하 고 구성의 유효성을 검사 합니다.
* 소프트웨어 사용 약관 설명이 표시되는 4페이지에서 클러스터 만들기 프로세스를 시작할 수 있습니다.

## <a name="page-one-parameters---cluster-controller-information"></a>1페이지 매개 변수 - 클러스터 컨트롤러 정보

배포 템플릿의 첫 번째 페이지는 클러스터 컨트롤러를 중심으로 합니다.

![배포 템플릿 1페이지](media/avere-vfxt-deploy-1.png)

다음 정보를 입력합니다.

* **클러스터 컨트롤러 이름** - 클러스터 컨트롤러 VM의 이름을 설정합니다.

* **컨트롤러 사용자 이름** -클러스터 컨트롤러 VM에 대 한 루트 사용자 이름을 설정 합니다.

* **인증 유형** - 컨트롤러에 연결하는 데 사용할 인증 유형(암호 또는 SSH 공개 키 인증)을 선택합니다. SSH 공개 키 방법을 사용하는 것이 좋습니다. 도움이 필요한 경우 [SSH 키를 만들고 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

* **암호** 또는 **SSH 공개 키** - 선택한 인증 유형에 따라 다음 필드에 RSA 공개 키 또는 암호를 입력해야 합니다. 앞에서 입력한 사용자 이름과 함께 이 자격 증명을 사용합니다.

* **구독** - Avere vFXT의 구독을 선택합니다.

* **리소스 그룹** - 기존의 빈 Avere vFXT 클러스터용 리소스 그룹을 선택하거나 "새로 만들기"를 클릭하고 새 리소스 그룹 이름을 입력합니다.

* **위치** - 클러스터와 리소스의 Azure 위치를 선택합니다.

완료 되 면 **확인을** 클릭 합니다.

> [!NOTE]
> 클러스터 컨트롤러에 공용 IP 주소를 사용하려는 경우 기존 네트워크를 선택하는 대신 클러스터용으로 새 가상 네트워크를 만듭니다. 이 설정은 2페이지에 있습니다.

## <a name="page-two-parameters---vfxt-cluster-information"></a>2페이지 매개 변수 - vFXT 클러스터 정보

배포 템플릿의 2페이지에서는 클러스터 크기, 노드 유형, 캐시 크기 및 스토리지 매개 변수와 같은 여러 설정을 지정할 수 있습니다.

![배포 템플릿 2페이지](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 클러스터 노드 수** -클러스터의 노드 수를 선택 합니다. 최소값은 노드 3개, 최대값은 12개입니다.

* **클러스터 관리 암호** - 클러스터 관리용 암호를 만듭니다. 이 암호는 클러스터를 모니터링 하 ```admin``` 고 클러스터 설정을 구성할 수 있는 클러스터 제어판에 로그인 하기 위해 사용자 이름과 함께 사용 됩니다.

* **Avere vFXT 클러스터 이름** - 클러스터에 고유한 이름을 지정합니다.

* **크기** -이 섹션에서는 클러스터 노드에 사용 되는 VM 유형을 보여 줍니다. 권장 옵션은 하나 뿐 이지만 **크기 변경** 링크를 사용 하면이 인스턴스 유형에 대 한 세부 정보 및 가격 계산기에 대 한 링크가 있는 테이블이 열립니다.

* **노드당 캐시 크기** -클러스터 캐시가 클러스터 노드에 분산 되므로 Avere vFXT 클러스터의 총 캐시 크기는이 크기에 노드 수를 곱한 값이 됩니다.

  권장 구성: Standard_E32s_v3 노드에 대해 노드당 4tb를 사용 합니다.

* **가상 네트워크** -클러스터를 저장할 새 가상 네트워크를 정의 하거나 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)에 설명 된 전제 조건을 충족 하는 기존 네트워크를 선택 합니다.

  > [!NOTE]
  > 새 가상 네트워크를 만들 경우 새 개인 네트워크에 액세스할 수 있도록 클러스터 컨트롤러에 공용 IP 주소가 포함 됩니다. 기존 가상 네트워크를 선택 하는 경우 클러스터 컨트롤러는 공용 IP 주소 없이 구성 됩니다.
  >
  > 클러스터 컨트롤러에 공개된 IP 주소가 있으면 vFXT 클러스터에 더 쉽게 액세스할 수 있지만 보안상으로는 다소 위험합니다.
  >* 클러스터 컨트롤러의 공용 IP 주소를 사용하면 이 주소를 점프 호스트로 사용하여 프라이빗 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다.
  >* 컨트롤러에 공용 IP 주소가 없는 경우 클러스터에 액세스 하려면 다른 점프 호스트, VPN 연결 또는 Express 경로를 사용 해야 합니다. 예를 들어 VPN 연결이 이미 구성 된 기존 가상 네트워크를 사용 합니다.
  >* 공용 IP 주소가 있는 컨트롤러를 만드는 경우 컨트롤러 VM을 네트워크 보안 그룹으로 보호해야 합니다. 기본적으로 Avere vFXT for Azure 배포는 공용 IP 주소를 사용 하는 컨트롤러의 경우 포트 22로만 인바운드 액세스를 제한 하는 네트워크 보안 그룹을 만듭니다. 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 컴퓨터의 연결만 허용)하면 시스템을 추가로 보호할 수 있습니다.

  또한 새 가상 네트워크는 Azure Blob 저장소에 대 한 저장소 서비스 끝점을 사용 하 여 구성 되 고, 네트워크 액세스 제어는 클러스터 서브넷의 Ip만 허용 하도록 구성 됩니다.

* **서브넷** -서브넷을 선택 하거나 새 서브넷을 만듭니다.

* **Blob 저장소 만들기 및 사용** -새 Azure blob 컨테이너를 만들고 새 Avere vFXT 클러스터에 대 한 백 엔드 저장소로 구성 하려면 **true** 를 선택 합니다. 이 옵션은 또한 클러스터의 리소스 그룹에 새 저장소 계정을 만들고 클러스터 서브넷 내에 Microsoft storage 서비스 끝점을 만듭니다.
  
  기존 가상 네트워크를 제공 하는 경우 클러스터를 만들기 전에 저장소 서비스 끝점이 있어야 합니다. 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md)을 참조 하세요.

  새 컨테이너를 만들지 않으려면 이 필드를 **false**로 설정합니다. 이 경우 클러스터를 만든 후 저장소를 연결 하 고 구성 해야 합니다. 지침은 [스토리지 구성](avere-vfxt-add-storage.md)을 읽으세요.

* **(새) 저장소 계정** -새 Azure Blob 컨테이너를 만드는 경우 새 저장소 계정의 이름을 입력 합니다.

## <a name="validation-and-purchase"></a>유효성 검사 및 구매

3 페이지는 구성을 요약 하 고 매개 변수의 유효성을 검사 합니다. 유효성 검사에 성공한 후 요약을 확인 하 고 **확인** 단추를 클릭 합니다.

> [!TIP]
> **확인** 단추 옆의 **템플릿 및 매개 변수 다운로드** 링크를 클릭 하 여이 클러스터의 만들기 설정을 저장할 수 있습니다. 이 정보는 예를 들어 재해 복구 시나리오에서 대체 클러스터를 만들 때와 같이 유사한 클러스터를 만들어야 하는 경우에 유용 합니다. 자세한 내용은 [재해 복구 지침](disaster-recovery.md) 을 참조 하세요.

![배포 템플릿 3페이지 - 유효성 검사](media/avere-vfxt-deploy-3.png)

4 페이지에서는 사용 약관 및 개인 정보 및 가격 책정 정보에 대 한 링크를 제공 합니다.

누락 된 연락처 정보를 입력 한 다음 **만들기** 단추를 클릭 하 여 약관에 동의 하 고 Azure 클러스터에 대 한 Avere vFXT를 만듭니다.

![배포 템플릿 4페이지 - 약관, 만들기 단추](media/avere-vfxt-deploy-4.png)

클러스터 배포는 15~20분 정도 걸립니다.

## <a name="gather-template-output"></a>템플릿 출력 수집

Avere vFXT 템플릿이 클러스터 만들기를 완료 하면 새 클러스터에 대 한 중요 한 정보를 출력 합니다.

> [!TIP]
> 템플릿 출력에서 **관리 IP 주소** 를 복사 해야 합니다. 클러스터를 관리하려면 이 주소가 필요합니다.

정보를 찾으려면 다음을 수행 합니다.

1. 클러스터 컨트롤러에 대한 리소스 그룹으로 이동합니다.

1. 왼쪽에서 **배포**를 클릭한 다음 **microsoft-avere.vfxt-template**을 클릭합니다.

   ![왼쪽에 배포가 선택되어 있고 배포 이름 아래의 표에 microsoft-avere.vfxt-template이 표시되어 있는 리소스 그룹 포털 페이지](media/avere-vfxt-outputs-deployments.png)

1. 왼쪽에서 **출력**을 클릭합니다. 각 필드의 값을 복사합니다.

   ![레이블 오른쪽의 필드에 SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs, MGMT_IP 값이 표시된 출력 페이지](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>다음 단계

클러스터가 실행 중이 고 관리 IP 주소를 알고 있으므로 [클러스터 구성 도구에 연결](avere-vfxt-cluster-gui.md)합니다.

구성 인터페이스를 사용 하 여 다음 설치 작업을 포함 하 여 클러스터를 사용자 지정 합니다.

* [지원 사용](avere-vfxt-enable-support.md)
* [저장소 추가](avere-vfxt-add-storage.md) (필요한 경우)
