---
title: Azure Portal에서 Service Fabric 클러스터 만들기 | Microsoft Docs
description: Azure Portal 및 Azure Key Vault를 사용하여 Azure에서 보안 Service Fabric 클러스터를 설정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: 02312a19c687908b0e1c0e6417dc6b0a9df23912
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125088"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure에서 서비스 패브릭 클러스터 만들기
> [!div class="op_single_selector"]
> * [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Azure Portal을 사용하여 Azure에 Service Fabric 클러스터(Linux 또는 Windows)를 설정하는 단계를 안내하는 단계별 가이드입니다. 이 가이드에서는 다음 단계를 안내합니다.

* Azure Portal을 통해 Azure에서 클러스터를 만듭니다.
* 인증서를 사용하여 관리자를 인증합니다.

> [!NOTE]
> Azure Active Directory를 사용한 사용자 인증 및 애플리케이션 보안에 대한 인증서 설정 등의 고급 보안 옵션을 사용하려면 [Azure Resource Manager를 사용하여 클러스터를 만듭니다][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>클러스터 보안 
인증서는 Service Fabric에서 클러스터 및 해당 애플리케이션의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다. Service Fabric에서 인증서가 사용되는 방식에 대한 자세한 내용은 [Service Fabric 클러스터 보안 시나리오][service-fabric-cluster-security]를 참조하세요.

서비스 패브릭 클러스터를 처음 만들거나 테스트 워크로드를 위해 클러스터를 처음 배포하는 경우, 다음 섹션(**Azure Portal에서 클러스터 만들기**)으로 건너뛰고 시스템이 테스트 워크로드를 실행하는 클러스터에 필요한 인증서를 생성하도록 할 수 있습니다. 프로덕션 워크로드를 위해 클러스터를 설정하는 경우에는 계속 읽어보시기 바랍니다.

#### <a name="cluster-and-server-certificate-required"></a>클러스터 및 서버 인증서(필수)
이 인증서는 클러스터를 보호하고 무단 액세스를 방지하기 위해 필요합니다. 다음 몇 가지 방법으로 클러스터 보안을 제공합니다.

* **클러스터 인증:** 클러스터 페더레이션에 대한 노드 간 통신을 인증합니다. 이 인증서로 자신의 신분을 증명할 수 있는 노드만 클러스터에 가입할 수 있습니다.
* **서버 인증:** 관리 클라이언트에 클러스터 관리 엔드포인트를 인증하여 관리 클라이언트가 실제 클러스터와 통신하는지 알 수 있도록 합니다. 이 인증서는 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer에 대해 SSL도 제공합니다.

이를 위해 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서의 **주체 이름은 Service Fabric 클러스터 액세스에 사용되는 도메인과 일치해야 합니다**. 클러스터의 HTTPS 관리 엔드포인트 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이러한 조건이 충족되어야 합니다. `.cloudapp.azure.com` 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 클러스터에 대한 사용자 지정 도메인 이름을 획득합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

#### <a name="client-authentication-certificates"></a>클라이언트 인증 인증서
추가 클라이언트 인증서가 클러스터 관리 작업을 위해 관리자를 인증합니다. 서비스 패브릭은 **관리자** 및 **읽기 전용 사용자**의 두 가지 액세스 수준을 제공합니다. 최소한 관리 액세스에 대해 단일 인증서를 사용해야 합니다. 추가 사용자 수준 액세스를 위해서는 별도 인증서를 제공해야 합니다. 액세스 역할에 대한 자세한 내용은 [Service Fabric 클라이언트의 역할 기반 액세스 제어][service-fabric-cluster-security-roles]를 참조하세요.

Service Fabric을 사용하기 위해 클라이언트 인증 인증서를 Key Vault에 업로드할 필요는 없습니다. 이러한 인증서는 클러스터 관리 권한이 있는 사용자에게 제공하기만 하면 됩니다. 

> [!NOTE]
> Azure Active Directory는 클러스터의 관리 작업을 위해 클라이언트를 인증하기 위한 권장 방법입니다. Azure Active Directory를 사용하려면 [Azure Resource Manager를 사용하여 클러스터를 만들어야][create-cluster-arm] 합니다.
> 
> 

#### <a name="application-certificates-optional"></a>애플리케이션 인증서(선택 사항)
애플리케이션 보안을 위해 클러스터에 제한 없는 수의 인증서를 추가로 설치할 수 있습니다. 클러스터를 만들기 전에, 다음과 같이 노드에 인증서를 설치하도록 요구하는 애플리케이션 보안 시나리오를 고려해 보세요.

* 애플리케이션 구성 값의 암호화 및 암호 해독
* 복제 중에 노드 간 데이터 암호화 

[Azure Portal을 통해 클러스터를 만드는](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md) 경우, 애플리케이션 인증서를 구성할 수 없습니다. 클러스터 설치 시에 애플리케이션 인증서를 구성하려면 [Azure Resource Manager를 사용하여 클러스터를 만들어야][create-cluster-arm] 합니다. 만든 클러스터에 애플리케이션 인증서를 추가할 수도 있습니다.

## <a name="create-cluster-in-the-azure-portal"></a>Azure 포털에서 클러스터 만들기

애플리케이션 요구를 충족하는 프로덕션 클러스터를 만들려면 몇 가지 계획이 필요합니다. 계획에 도움이 되도록 [Service Fabric 클러스터 계획 고려 사항][service-fabric-cluster-capacity] 문서를 읽고 이해하는 것이 좋습니다. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>서비스 패브릭 클러스터 리소스 검색

[Azure Portal][azure-portal]에 로그인합니다.
**리소스 만들기**를 클릭하여 새 리소스 템플릿을 추가합니다. **Marketplace**의 **모두**에서 Service Fabric 클러스터 템플릿을 검색합니다.
목록에서 **서비스 패브릭 클러스터** 를 선택합니다.

![Azure 포털에서 서비스 패브릭 클러스터 템플릿을 검색합니다.][SearchforServiceFabricClusterTemplate]

**Service Fabric 클러스터** 블레이드로 이동하고, **만들기**를 클릭합니다.

**Service Fabric 클러스터 만들기** 블레이드는 다음 4단계를 포함합니다.

### <a name="1-basics"></a>1. 기본 사항
![새 리소스 그룹을 만드는 스크린샷.][CreateRG]

기본 사항 블레이드에서는 클러스터에 대한 기본 세부 정보를 제공해야 합니다.

1. 클러스터 이름을 입력합니다.
2. VM용 원격 데스크톱의 **사용자 이름** 및 **암호**를 입력합니다.
3. 클러스터가 배포될 **구독**을 선택합니다. 특히 구독이 여러 개인 경우 반드시 선택해야 합니다.
4. 새 **리소스 그룹**을 만듭니다. 클러스터 이름과 같은 이름을 사용하면 나중에 쉽게 찾을 수 있으며 특히 배포를 변경하거나 클러스터를 삭제하려고 할 때 매우 유용합니다.
   
   > [!NOTE]
   > 기존 리소스 그룹을 사용해도 되지만 새 리소스 그룹을 만드는 것이 좋습니다. 이렇게 하면 클러스터 및 클러스터에서 사용하는 모든 리소스를 쉽게 삭제할 수 있습니다.
   > 
   > 
5. 클러스터를 만들려는 **위치**를 선택합니다. 이미 키 자격 증명 모음에 업로드한 기존 인증서를 사용하려는 경우 키 자격 증명 모음이 있는 지역과 동일한 지역을 사용해야 합니다. 

### <a name="2-cluster-configuration"></a>2. 클러스터 구성
![노드 형식 만들기][CreateNodeType]

클러스터 노드를 구성합니다. 노드 유형에서 VM 크기, VM 수 및 VM 속성을 정의합니다. 클러스터는 둘 이상의 노드 형식을 가질 수 있지만 주 노드 형식(포털에서 정의하는 첫 번째 노드)에는 최소한 5개의 VM이 있어야 하며, 서비스 패브릭 시스템 서비스가 배치된 노드 형식입니다. "NodeTypeName"의 기본 배치 속성은 자동으로 추가되므로 **배치 속성** 을 구성하지 마세요.

> [!NOTE]
> 여러 노드 형식 사용에 대한 일반적인 시나리오는 프런트 엔드 서비스 및 백 엔드 서비스를 포함하는 애플리케이션입니다. 인터넷에 열려 있는 포트를 포함하고 비교적 작은 VM(D2_V2 같은 VM 크기)에 프런트 엔드 서비스를 배치하고, 열려 있는 인터넷 연결 포트가 없고 비교적 큰 VM(D3_V2, D6_V2, D15_V2 등과 같은 VM 크기)에 백 엔드 서비스를 배치하려고 합니다.
> 

1. 노드 유형에 대한 이름(문자와 숫자만을 포함하는 1~12자)을 선택합니다.
2. 주 노드 형식에 대한 최소 VM **크기**는 클러스터에 대해 선택한 **내구성 계층**에 따라 결정됩니다. 내구성 계층에 대한 기본값은 브론즈입니다. 내구성에 대한 자세한 내용은 [Service Fabric 클러스터 내구성을 선택하는 방법][service-fabric-cluster-durability]을 참조하세요.
3. **가상 머신 크기**를 선택합니다. D 시리즈 VM에는 SSD 드라이브가 있으므로 특히 상태 저장 애플리케이션에 권장됩니다. 부분 코어가 있거나 사용 가능한 디스크 용량이 10GB 미만인 VM SKU를 사용하지 마세요. VM 크기 선택에 대한 도움말을 보려면 [Service Fabric 클러스터 계획 고려 사항 문서][service-fabric-cluster-capacity]를 참조하세요.
4.  **단일 노드 클러스터 및 3개 노드 클러스터**는 테스트용으로만 사용되며 실행 중인 프로덕션 워크로드에는 지원되지 않습니다.
5. 노드 형식에 대한 **초기 VM 확장 집합 용량**을 선택합니다. 나중에 노드 형식의 VM 수를 늘리거나 줄일 수 있지만 주 노드 형식에서 프로덕션 워크로드의 최소값은 5개입니다. 다른 노드 형식은 최소 VM이 1대만 있어도 됩니다. 주 노드 형식에 대한 최소 VM **수**는 클러스터의 **안정성**을 결정합니다.  
6. **사용자 지정 엔드포인트**를 구성합니다. 이 필드를 사용하면 Azure Load Balancer를 통해 애플리케이션에 대한 공용 인터넷에 노출하려고 하는 쉼표로 구분된 포트 목록을 입력할 수 있습니다. 예를 들어 클러스터에 웹 애플리케이션을 배포하려는 경우 여기에 "80"을 입력하여 포트 80의 트래픽이 클러스터로 이동되도록 허용합니다. 엔드포인트에 대한 자세한 내용은 [애플리케이션과 통신][service-fabric-connect-and-communicate-with-services]을 참조하세요.
7. **역방향 프록시 사용**.  [Service Fabric 역방향 프록시](service-fabric-reverseproxy.md)는 Service Fabric 클러스터 탐색에서 마이크로 서비스가 실행되도록 하고 http 엔드포인트가 있는 다른 서비스와 통신합니다.
8. **클러스터 구성** 블레이드로 돌아가, **+선택적 설정 표시** 아래에서 클러스터 **진단**을 구성합니다. 기본적으로 문제 해결을 돕기 위해 클러스터에서 진단이 활성화됩니다. 진단을 사용하지 않으려면 **상태** 토글을 **사용 안 함**로 변경합니다. 진단을 끄지 **않는** 것이 좋습니다. 이미 Application Insights 프로젝트가 생성되어 있는 경우 애플리케이션 추적이 프로젝트로 라우팅되도록 해당 키를 제공합니다.
9. **DNS 서비스 포함**  [DNS 서비스](service-fabric-dnsservice.md)는 DNS 프로토콜을 사용하여 다른 서비스를 찾을 수 있는 선택적 서비스입니다.
10. 클러스터를 설정할 **패브릭 업그레이드 모드**를 선택합니다. 시스템이 사용 가능한 최신 버전을 자동으로 선택하고 클러스터를 최신 버전으로 업그레이드하도록 하려면 **자동**을 선택합니다. 지원되는 버전을 선택하려는 경우 모드를 **수동**으로 설정합니다. 패브릭 업그레이드 모드에 대한 자세한 내용은 [Service Fabric 클러스터 업그레이드 문서][service-fabric-cluster-upgrade]를 참조하세요.

> [!NOTE]
> 지원되는 버전의 Service Fabric이 실행되는 클러스터만 지원합니다. **수동** 모드를 선택하면 사용자가 직접 클러스터를 지원되는 버전으로 업그레이드해야 합니다.
> 

### <a name="3-security"></a>3. 보안
![Azure portal에서 보안 구성의 스크린샷입니다.][BasicSecurityConfigs]

보안 테스트 클러스터를 설정하기 쉽도록 **기본** 옵션이 제공됩니다. 이미 인증서가 있고 [키 자격 증명 모음](/azure/key-vault/)에 업로드한 경우(키 자격 증명 모음을 배포에 사용하도록 설정함) **사용자 지정** 옵션을 사용합니다.

#### <a name="basic-option"></a>기본 옵션
화면 지침에 따라 기존 키 자격 증명 모음을 추가하거나 다시 사용하고 인증서를 추가합니다. 인증서 추가는 동기 프로세스이므로 인증서가 생성될 때까지 기다려야 합니다.

이전 프로세스가 완료될 때까지 화면을 벗어나지 마세요.

![CreateKeyVault]

이제 키 자격 증명 모음을 만들었으므로 키 자격 증명 모음에 대한 액세스 정책을 편집합니다. 

![CreateKeyVault2]

**액세스 정책 편집** 및 **고급 액세스 정책 표시**를 차례로 클릭한 다음, 배포를 위해 Azure Virtual Machines에 액세스할 수 있도록 합니다. 템플릿 배포도 사용하도록 설정하는 것이 좋습니다. 선택이 완료되면 **저장** 단추를 클릭하고 **액세스 정책** 창을 닫아야 합니다.

![CreateKeyVault3]

인증서 이름을 입력하고 **확인**을 클릭합니다.

![CreateKeyVault4]

#### <a name="custom-option"></a>사용자 지정 옵션
**기본** 옵션의 단계를 이미 수행한 경우에는 이 섹션을 건너뜁니다.

![SecurityCustomOption]

보안 페이지를 완료하려면 원본 키 자격 증명 모음, 인증서 URL 및 인증서 지문 정보가 필요합니다. 이 정보가 없는 경우 다른 브라우저 창을 열고 Azure Portal에서 다음을 수행합니다.

1. 키 자격 증명 모음 서비스로 이동합니다.
2. “속성” 탭을 선택하고 다른 브라우저 창의 “원본 키 자격 증명 모음”에 ‘리소스 ID’를 복사합니다. 

    ![CertInfo0]

3. 이제 “인증서” 탭을 선택합니다.
4. 인증서 지문을 클릭합니다. 버전 페이지로 이동됩니다.
5. 현재 버전 아래에 표시되는 GUID를 클릭합니다.

    ![CertInfo1]

6. 이제 아래와 같은 화면이 표시됩니다. 다른 브라우저 창의 “인증서 지문”에 16진 SHA-1 지문을 복사합니다.
7. 다른 브라우저 창의 “인증서 URL”에 ‘비밀 식별자’를 복사합니다.

    ![CertInfo2]

**고급 설정 구성** 확인란을 선택하여 **관리 클라이언트** 및 **읽기 전용 클라이언트**에 대한 클라이언트 인증서를 입력합니다. 이러한 필드에는 관리 클라이언트 인증서의 지문과 읽기 전용 사용자 클라이언트 인증서의 지문을 입력하면 됩니다(해당하는 경우). 관리자가 클러스터에 연결하려고 할 경우, 여기에 입력한 지문 값과 일치하는 지문을 포함하는 인증서가 있어야만 액세스 권한이 부여됩니다.  

### <a name="4-summary"></a>4. 요약

이제 클러스터를 배포할 준비가 되었습니다. 배포하기 전에 인증서를 다운로드하여 링크의 큰 파란색 정보 상자를 확인합니다. 인증서를 안전한 장소에 보관해야 합니다. 클러스터에 연결하려면 인증서가 필요합니다. 다운로드한 인증서에는 암호가 없으므로 암호를 추가하는 것이 좋습니다.

클러스터 만들기를 완료하려면 **만들기**를 클릭합니다. 필요에 따라 템플릿을 다운로드할 수 있습니다.

![요약]

알림에서 만들기 진행률을 볼 수 있습니다. (화면 오른쪽 위의 상태 표시줄 근처에 있는 "종" 모양 아이콘을 클릭합니다.) 클러스터를 만드는 동안 **시작 보드에 고정**을 클릭하면 **Service Fabric 클러스터 배포 중**이 **시작** 보드에 고정됩니다. 이 프로세스는 시간이 약간 걸립니다. 

Powershell 또는 CLI를 사용하여 클러스터에 대한 관리 작업을 수행하려면 클러스터에 연결하여 [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)에서 자세한 방법을 확인해야 합니다.

## <a name="view-your-cluster-status"></a>클러스터 상태 보기
![대시보드의 클러스터 세부 정보 스크린샷입니다.][ClusterDashboard]

클러스터를 만들면 포털에서 클러스터를 검사할 수 있습니다.

1. **찾아보기**로 이동하고 **Service Fabric 클러스터**를 클릭합니다.
2. 클러스터를 찾아 클릭합니다.
3. 이제 대시보드에서 클러스터의 공용 엔드포인트 및 Service Fabric Explorer에 대한 링크를 포함한 클러스터 세부 정보를 볼 수 있습니다.

클러스터 대시보드 블레이드의 **노드 모니터** 섹션에 정상 및 비정상 상태인 VM 수가 표시됩니다. 클러스터 상태에 대한 자세한 내용은 [Service Fabric 상태 모델 소개][service-fabric-health-introduction]에서 볼 수 있습니다.

> [!NOTE]
> 가용성을 유지하고 상태를 보존하기 위해 Service Fabric 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업][service-fabric-reliable-services-backup-restore]을 처음으로 수행하는 경우 외에는 일반적으로 클러스터의 모든 컴퓨터를 종료하는 것은 안전하지 않습니다.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Virtual Machine Scale Set 인스턴스 또는 클러스터 노드에 원격 연결
클러스터에서 지정한 각 NodeType에 따라 Virtual Machine Scale Set가 설정됩니다. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>다음 단계
이제 관리 인증을 위해 인증서를 사용하는 보안 클러스터가 구축되었습니다. 다음으로, [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)하고 [애플리케이션 비밀을 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.  또한 [Service Fabric 지원 옵션](service-fabric-support.md)을 알아봅니다.

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[요약]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
