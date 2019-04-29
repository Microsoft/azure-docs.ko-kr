---
title: Azure Site Recovery를 사용하여 다중 계층 IIS 기반 웹 애플리케이션에 대한 재해 복구 설정 | Microsoft Docs
description: Azure Site Recovery를 사용하여 IIS 웹 팜 가상 머신을 복제하는 방법을 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: aa42371692cd5d0dc96835db5b66fe0877b90665
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280420"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>다중 계층 IIS 기반 웹 애플리케이션에 대한 재해 복구 설정

애플리케이션 소프트웨어는 조직에서 비즈니스 생산성의 엔진입니다. 다양한 웹 애플리케이션은 조직 내 여러 용도로 사용될 수 있습니다. 급여 처리, 재무 및 고객 지향 웹 사이트에 사용되는 애플리케이션과 같은 일부 애플리케이션은 조직에 중요할 수 있습니다. 생산성 저하를 방지하려면 조직에서 이러한 애플리케이션을 지속적으로 작동하고 실행해야 합니다. 더 중요한 것은 이러한 애플리케이션을 일관되게 사용할 수 있게 되면 조직의 브랜드 또는 이미지가 손상되지 않도록 하는 데 도움이 될 수 있습니다.

중요한 웹 애플리케이션은 일반적으로 웹, 데이터베이스 및 애플리케이션이 서로 다른 계층에 있는 다중 계층 애플리케이션으로 설정됩니다. 여러 계층으로 분산되는 것 외에도, 애플리케이션은 각 계층에 여러 서버를 사용하여 트래픽 부하를 분산할 수도 있습니다. 또한 다양한 계층 간 및 웹 서버 상의 매핑은 고정 IP 주소를 기반으로 할 수 있습니다. 장애 조치 시, 특히 웹 서버에 여러 웹 사이트가 구성된 경우 이러한 매핑 중 일부를 업데이트해야 합니다. 웹 애플리케이션에서 SSL을 사용하는 경우 인증서 바인딩을 업데이트해야 합니다.

복제를 기반으로 하지 않는 기존의 복구 방법에는 다양한 구성 파일, 레지스트리 설정, 바인딩, 사용자 지정 구성 요소(COM 또는 .NET), 콘텐츠 및 인증서를 백업하는 작업이 포함됩니다. 파일은 일단의 수동 단계를 통해 복구됩니다. 파일을 백업하고 수동으로 복구하는 기존의 복구 방법은 번거롭고, 오류가 발생하기 쉽고, 크기를 조정할 수 없습니다. 예를 들어 인증서 백업을 쉽게 잊을 수 있습니다. 장애 조치 후에는 서버에 대한 새 인증서를 구매할 수밖에 없습니다.

뛰어난 재해 복구 솔루션은 복잡한 애플리케이션 아키텍처에 대한 복구 계획을 모델링할 수 있도록 지원합니다. 또한 계층 간의 애플리케이션 매핑을 처리하기 위해 복구 계획에 사용자 지정 단계를 추가할 수 있어야 합니다. 재해가 있는 경우 애플리케이션 매핑은 한 번의 클릭으로 확실한 솔루션을 제공하여 RTO를 낮추는 데 도움이 됩니다.

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 IIS(인터넷 정보 서비스)를 기반으로 하는 웹 애플리케이션을 보호하는 방법에 대해 설명합니다. 그리고 3계층 IIS 기반 웹 애플리케이션을 Azure로 복제하는 방법, 재해 복구 연습을 수행하는 방법 및 애플리케이션을 Azure로 장애 조치하는 방법에 대한 모범 사례를 설명합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 작업을 수행하는 방법을 알고 있어야 합니다.

* [Azure에 가상 머신 복제](vmware-azure-tutorial.md)
* [복구 네트워크 디자인](site-recovery-network-design.md)
* [Azure로 테스트 장애 조치(failover) 수행](site-recovery-test-failover-to-azure.md)
* [Azure로 장애 조치(failover) 수행](site-recovery-failover.md)
* [도메인 컨트롤러 복제](site-recovery-active-directory.md)
* [SQL Server 복제](site-recovery-sql.md)

## <a name="deployment-patterns"></a>배포 패턴
IIS 기반 웹 애플리케이션은 일반적으로 다음 배포 패턴 중 하나를 따릅니다.

**배포 패턴 1**

ARR(애플리케이션 요청 라우팅), IIS 서버 및 SQL Server가 있는 IIS 기반 웹 팜

![3계층으로 구성된 IIS 기반 웹 팜의 다이어그램](./media/site-recovery-iis/deployment-pattern1.png)

**배포 패턴 2**

ARR, IIS 서버, 애플리케이션 서버 및 SQL Server가 있는 IIS 기반 웹 팜

![4계층으로 구성된 IIS 기반 웹 팜의 다이어그램](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery 지원

이 문서의 예제에서는 Windows Server 2012 R2 Enterprise에서 IIS 7.5가 있는 VMware 가상 머신을 사용합니다. Site Recovery 복제는 애플리케이션마다 다르므로 이 문서의 권장 사항은 다음 표에 나열된 시나리오와 다른 버전의 IIS에 적용되어야 합니다.

### <a name="source-and-target"></a>원본 및 대상

시나리오 | 보조 사이트 대상 | Azure 대상
--- | --- | ---
Hyper-V | 예 | 예
VMware | 예 | 예
물리적 서버 | 아닙니다. | 예
Azure|해당 없음|예

## <a name="replicate-virtual-machines"></a>가상 머신 복제

모든 IIS 웹 팜 가상 머신을 Azure로 복제하려면 [Site Recovery에서 Azure로 장애 조치 테스트](site-recovery-test-failover-to-azure.md)의 지침을 따릅니다.

고정 IP 주소를 사용하는 경우 가상 머신에서 사용할 IP 주소를 지정할 수 있습니다. IP 주소를 설정하려면 **계산 및 네트워크 설정** > **대상 IP**로 차례로 이동합니다.

![Site Recovery 계산 및 네트워크 창에서 대상 IP를 설정하는 방법을 보여주는 스크린샷](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>복구 계획 만들기
복구 계획은 장애 조치(failover) 시에 다중 계층 애플리케이션에서 여러 계층의 시퀀싱을 지원합니다. 시퀀싱은 애플리케이션의 일관성을 유지하는 데 도움이 됩니다. 다중 계층 웹 애플리케이션에 대한 복구 계획을 생성할 때는 [Site Recovery를 사용하여 복구 계획 만들기](site-recovery-create-recovery-plans.md)에서 설명하는 단계를 수행합니다.

### <a name="add-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 머신 추가
일반적인 다중 계층 IIS 웹 애플리케이션을 구성하는 요소는 다음과 같습니다.
* SQL 가상 머신이 있는 데이터베이스 계층
* IIS 서버와 애플리케이션 계층으로 구성된 웹 계층 

다음과 같이 계층에 따라 서로 다른 그룹에 가상 머신을 추가합니다.

1. 복구 계획을 만듭니다. 그룹 1에 데이터베이스 계층 가상 머신을 추가합니다. 이렇게 하면 데이터베이스 계층 가상 머신이 맨 먼저 작동되고 마지막으로 종료됩니다.
1. 그룹 2에 애플리케이션 계층 가상 머신을 추가합니다. 이렇게 하면 데이터베이스 계층이 작동된 후에 애플리케이션 계층 가상 머신이 작동됩니다.
1. 그룹 3에 웹 계층 가상 머신을 추가합니다. 이렇게 하면 애플리케이션 계층이 작동된 후에 웹 계층 가상 머신이 작동됩니다.
1. 그룹 4에 부하 분산 가상 머신을 추가합니다. 이렇게 하면 웹 계층이 작동된 후에 부하 분산 가상 머신이 작동됩니다.

자세한 내용은 [복구 계획 사용자 지정](site-recovery-runbook-automation.md#customize-the-recovery-plan)을 참조하세요.


### <a name="add-a-script-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
IIS 웹 팜 기능이 제대로 작동하려면 장애 조치 후 또는 테스트 장애 조치 중에 Azure 가상 머신에서 일부 작업을 수행해야 할 수도 있습니다. 일부 장애 조치 후 작업은 자동화할 수 있습니다. 예를 들어 복구 계획에 해당 스크립트를 추가하여 DNS 항목을 업데이트하거나, 사이트 바인딩을 변경하거나, 연결 문자열을 변경할 수 있습니다. [복구 계획에 VMM 스크립트 추가](site-recovery-how-to-add-vmmscript.md)에서는 스크립트를 사용하여 자동화된 작업을 설정하는 방법에 대해 설명합니다.

#### <a name="dns-update"></a>DNS 업데이트
DNS가 동적 DNS 업데이트로 구성된 경우 가상 머신은 일반적으로 시작할 때 DNS를 새 IP 주소로 업데이트합니다. DNS를 가상 머신의 새 IP 주소로 업데이트하는 명시적 단계를 추가하려면 복구 계획 그룹에 장애 조치 후 작업으로 [DNS의 IP를 업데이트하는 스크립트](https://aka.ms/asr-dns-update)를 추가합니다.  

#### <a name="connection-string-in-an-applications-webconfig"></a>애플리케이션의 web.config에 있는 연결 문자열
연결 문자열은 웹 사이트에서 통신하는 데이터베이스를 지정합니다. 연결 문자열에 데이터베이스 가상 머신의 이름이 포함되어 있으면 장애 조치 후 추가 단계가 필요하지 않습니다. 애플리케이션은 데이터베이스와 자동으로 통신할 수 있습니다. 또한 데이터베이스 가상 머신의 IP 주소가 유지되면 연결 문자열을 업데이트할 필요가 없습니다. 

연결 문자열에서 IP 주소를 사용하여 데이터베이스 가상 머신을 참조하는 경우 장애 조치 후에 업데이트해야 합니다. 예를 들어 다음 연결 문자열은 IP 주소가 127.0.1.2인 데이터베이스를 가리킵니다.

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

웹 계층에서 연결 문자열을 업데이트하려면 복구 계획에서 그룹 3 뒤에 [IIS 연결 업데이트 스크립트](https://aka.ms/asr-update-webtier-script-classic)를 추가합니다.

#### <a name="site-bindings-for-the-application"></a>애플리케이션에 대한 사이트 바인딩
모든 사이트는 바인딩 정보로 구성됩니다. 바인딩 정보에는 바인딩 유형, IIS 서버에서 사이트에 대한 요청을 수신 대기하는 IP 주소, 포트 번호 및 사이트에 대한 호스트 이름이 포함됩니다. 장애 조치 중에 연결된 IP 주소가 변경되면 이러한 바인딩을 업데이트해야 할 수 있습니다.

> [!NOTE]
>
> 사이트 바인딩을 **지정되지 않은 모든 항목**으로 설정하는 경우 이 바인딩은 장애 조치 후에 업데이트할 필요가 없습니다. 또한 사이트와 연결된 IP 주소가 장애 조치 후에 변경되지 않는 경우에도 사이트 바인딩을 업데이트할 필요가 없습니다. (IP 주소의 보존은 기본 및 복구 사이트에 할당된 네트워크 아키텍처 및 서브넷에 따라 다릅니다. 조직에서 이러한 업데이트를 수행하지 못할 수도 있습니다.)

![SSL 바인딩 설정을 보여 주는 스크린샷](./media/site-recovery-iis/sslbinding.png)

IP 주소를 사이트와 연결한 경우 모든 사이트 바인딩을 새 IP 주소로 업데이트합니다. 사이트 바인딩을 변경하려면 복구 계획에서 그룹 3 뒤에 [IIS 웹 계층 업데이트 스크립트](https://aka.ms/asr-web-tier-update-runbook-classic)를 추가합니다.

#### <a name="update-the-load-balancer-ip-address"></a>부하 분산 장치 IP 주소 업데이트
ARR 가상 머신이 있는 경우 IP 주소를 업데이트하려면 그룹 4 뒤에 [IIS ARR 장애 조치 스크립트](https://aka.ms/asr-iis-arrtier-failover-script-classic)를 추가합니다.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>HTTPS 연결에 대한 SSL 인증서 바인딩
웹 사이트에는 웹 서버와 사용자 브라우저 간의 보안 통신을 보장하는 데 도움이 되는 연결된 SSL 인증서가 있을 수 있습니다. 웹 사이트에 HTTPS 연결이 있고 SSL 인증서 바인딩을 사용하여 IIS 서버의 IP 주소에 연결되는 HTTPS 사이트 바인딩이 있는 경우, 장애 조치 후에 IIS 가상 머신의 IP 주소를 사용하여 인증서에 대한 새 사이트 바인딩을 추가해야 합니다.

SSL 인증서가 발급될 수 있는 구성 요소는 다음과 같습니다.

* 웹 사이트의 정규화된 도메인 이름
* 서버 이름
* 도메인 이름에 대한 와일드카드 인증서  
* IP 주소. SSL 인증서가 IIS 서버의 IP 주소에 대해 발급되면, Azure 사이트에 있는 IIS 서버의 IP 주소에 대해 다른 SSL 인증서를 발급해야 합니다. 이 인증서에 대한 추가 SSL 바인딩을 만들어야 합니다. 따라서 IP 주소에 대해 발급된 SSL 인증서를 사용하지 않는 것이 좋습니다. 이 옵션은 널리 사용되고 있지 않으며, 새로운 인증 기관/브라우저 포럼 변경 내용에 따라 곧 사용되지 않을 예정입니다.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>웹 계층과 애플리케이션 계층 간의 종속성 업데이트
가상 머신의 IP 주소를 기반으로 하는 애플리케이션별 종속성이 있는 경우 이 종속성은 장애 조치 후에 업데이트해야 합니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1. Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
2. IIS 웹 팜에 대해 만든 복구 계획을 선택합니다.
3. **테스트 장애 조치**를 선택합니다.
4. 테스트 장애 조치(failover) 프로세스를 시작하려면 복구 지점과 Azure 가상 네트워크를 선택합니다.
5. 보조 환경이 가동 중인 경우 유효성 검사를 수행할 수 있습니다.
6. 유효성 검사가 완료되면 테스트 장애 조치 환경을 정리하기 위해 **유효성 검사 완료**를 선택합니다.

자세한 내용은 [Site Recovery에서 Azure로 테스트 장애 조치(failover)](site-recovery-test-failover-to-azure.md)를 참조하세요.

## <a name="run-a-failover"></a>장애 조치(Failover) 실행

1. Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
1. IIS 웹 팜에 대해 만든 복구 계획을 선택합니다.
1. **장애 조치**를 선택합니다.
1. 복구 지점을 선택하여 장애 조치(failover) 프로세스를 시작합니다.

자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Site Recovery를 사용하여 [다른 애플리케이션을 복제](site-recovery-workload.md)하는 방법에 대해 자세히 알아봅니다.
