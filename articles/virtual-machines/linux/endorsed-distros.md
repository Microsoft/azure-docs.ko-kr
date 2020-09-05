---
title: Azure 보증 Linux 배포판
description: Ubuntu, CentOS, Oracle 및 SUSE 관련 지침을 포함하여 Azure에서 Linux의 인증 배포를 수행하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 08/02/2020
ms.author: guybo
ms.openlocfilehash: 0b3a598213545bbad833e9b8036f65b999df1c76
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607009"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure에서 Linux 배포판 보증

파트너는 Azure Marketplace에서 Linux 이미지를 제공 합니다. Microsoft는 다양 한 Linux 커뮤니티와 협력 하 여 보증 메일 그룹에 더 많은 추가 기능을 추가 합니다. Marketplace에서 사용할 수 없는 배포판의 경우 [linux 운영 체제를 포함 하는 가상 하드 디스크 만들기 및 업로드](./create-upload-generic.md)의 지침에 따라 항상 고유한 linux를 가져올 수 있습니다.

## <a name="supported-distributions-and-versions"></a>지원되는 배포판 및 버전

다음 표에서 Linux 배포판 및 Azure에서 지원 되는 버전을 나열합니다. 자세한 내용은 [Microsoft Azure의 Linux 이미지 지원](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)을 참조 하세요.

Hyper-V 및 Azure용 LIS(Linux 통합 서비스) 드라이버는 Microsoft가 업스트림 Linux 커널로 직접 제공되는 커널 모듈입니다. 일부 LIS 드라이버는 기본적으로 배포 커널에 빌드됩니다. RHEL(Red Hat Enterprise)/CentOS를 기반으로 둔 이전 배포는 [Hyper-V 및 Azure용 Linux Integration Services 버전 4.2](https://www.microsoft.com/download/details.aspx?id=55106)에서 별도의 다운로드로 제공됩니다. 자세한 내용은 [Linux 커널 요구 사항](create-upload-generic.md#linux-kernel-requirements)을 참조 하세요.

Azure Linux 에이전트는 이미 Azure Marketplace 이미지에 미리 설치 되어 있으며 일반적으로 배포의 패키지 리포지토리에서 사용할 수 있습니다. 소스 코드는 [GitHub](https://github.com/azure/walinuxagent)에서 찾을 수 있습니다.

| 배포 | 버전 | 드라이버 | 에이전트 |
| --- | --- | --- | --- |
| Rogue Wave 소프트웨어로 CentOS |CentOS .x, 4.x, 4.x |CentOS 6.3: [LIS 다운로드](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: 커널에 있음 |패키지: "WALinuxAgent"의 [리포지토리](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS는 이제 2020 년 5 월 26 일에 [수명이 종료](https://coreos.com/os/eol/) 됩니다. |더 이상 사용할 수 없음 | | |
| Debian by Credativ |8.x, 9.x |커널에 있음 |패키지: "waagent"에서 리포지토리의 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Kinvolk에의 한 Flatcar Container Linux| Stable, Edge| | |
| Oracle에서 Oracle Linux |6.x, 7.x, 8.x |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat에서 Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/overview) |6.x, 7.x, 8.x |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise by SUSE |SLES/SLES for SAP 11.x, 4.x, 4.x <br/> [SUSE 공용 클라우드 이미지 수명 주기](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |커널에 있음 |패키지:<p> 11의 경우 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음<br>12의 경우 "python-azure-agent" 아래의 "퍼블릭 클라우드" 모듈에 포함됨<br/>소스 코드: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| SUSE 별 openSUSE |openSUSE Leap 15.x |커널에 있음 |패키지: "python-azure-agent"의 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| 정식 Ubuntu |Ubuntu 서버 및 Pro. 16.x, 18.x, 20.x<p>Ubuntu 12.04 및 14.04에 대 한 확장 지원에 대 한 정보는 [Ubuntu 확장 보안 유지 관리](https://www.ubuntu.com/esm)에서 찾을 수 있습니다. |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>이미지 업데이트 흐름

Azure를 사용 하려면 보증 Linux 배포판의 게시자가 분기별 또는 더 빠른 흐름에서 최신 패치와 보안 픽스를 사용 하 여 Azure Marketplace의 이미지를 정기적으로 업데이트 해야 합니다. Marketplace의 업데이트 된 이미지는 새 버전의 이미지 SKU로 고객에 게 자동으로 제공 됩니다. Linux 이미지를 찾는 방법에 대 한 자세한 정보: [Azure Marketplace에서 LINUX VM 이미지를 찾습니다](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Azure 조정 커널

Azure는 다양 한 보증 Linux 배포판과 긴밀 하 게 협력 하 여 Azure Marketplace에 게시 된 이미지를 최적화 합니다. 이 공동 작업의 한 가지 측면은 Azure 플랫폼에 최적화 되 고 Linux 배포판의 완전히 지원 되는 구성 요소로 제공 되는 "조정 된" Linux 커널을 개발 하는 것입니다. Azure에 의해 조정 되는 커널에는 배포에서 사용할 수 있는 기본 또는 일반 커널을 비교 하 여 새로운 기능과 성능이 개선 되 고 더 빠르게 (일반적으로 분기별) 주기가 통합 됩니다.

대부분의 경우 이러한 커널은 Azure Marketplace의 기본 이미지에 미리 설치 되어 있으므로 고객이 이러한 최적화 된 커널의 이점을 즉시 얻을 수 있습니다. 이러한 Azure 조정 커널에 대 한 자세한 내용은 다음 링크에서 찾을 수 있습니다.

- [CentOS Azure 튜닝 된 커널-CentOS 가상화 SIG를 통해 사용 가능](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian 클라우드 커널-Azure의 Debian 10 및 Debian 9 "backports" 이미지에서 사용할 수 있음](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure 튜닝 커널](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure 조정 커널](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)

## <a name="partners"></a>파트너

### <a name="coreos"></a>CoreOS

CoreOS는 2020 년 5 월 26 일까지 [수명이 종료](https://coreos.com/os/eol/) 되도록 예약 됩니다.
Microsoft에는 CoreOS 사용자를 위한 2 개의 마이그레이션 채널이 있습니다.

- Flatcar by Kinvolk ("Flatcar Container Linux by Kinvolk" 항목 참조)
- [Fedora CORE OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (고객은 자신의 이미지를 업로드 해야 합니다. [마이그레이션 설명서](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)는 다음과 같습니다.

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ는 독립적인 컨설팅 및 서비스 회사로, 주요 사업 분야는 무료 소프트웨어를 사용하여 전문 솔루션을 개발하고 구현하는 것입니다. 시장을 선도하는 오픈 소스 전문 회사인 Credative는 지원을 받는 많은 IT 부서들로부터 국제적인 명성을 얻고 있습니다. 현재 Credativ는 Microsoft와 협력 관계를 맺고 Debian 8(Jessie) 및 Debian 7(Wheezy) 이전 버전에 해당하는 Debian 이미지를 준비 중입니다. 두 이미지 모두 Azure에서 실행하도록 특별히 설계되었으며 플랫폼을 통해 쉽게 관리할 수 있습니다. Credativ는 또한 자체적인 오픈 소스 지원 센터를 통해 장기적인 유지 및 Azure의 Debian 이미지 업데이트를 지원합니다.

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk는 Flatcar Container Linux를 기반으로 하는 회사 이며, 컨테이너 화 된 응용 프로그램에 대 한 변경 불가능 한 최소 및 자동 업데이트 기반에 대해 원래 CoreOS 비전을 계속 합니다. 최소 배포판 Flatcar에는 컨테이너를 배포 하는 데 필요한 패키지만 포함 되어 있습니다. 변경 불가능 한 파일 시스템은 일관성과 보안을 보장 하는 반면 자동 업데이트 기능을 사용 하면 최신 보안 픽스를 사용 하 여 항상 최신 상태를 유지할 수 있습니다. 

Flatcar Container Linux는 연중 무휴 응답, 보안 및 기술 경고, 장기 지원 채널을 포함 하는 독점적인 Azure에 최적화 된 이미지를 포함 하는 선택적 상업적 지원 구독을 제공 하는 Kinvolk의 글로벌 Linux 및 컨테이너 기술 전문가에 의해 백업 됩니다.


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle의 전략은 공용 및 사설 클라우드에 대 한 광범위 한 솔루션 포트폴리오를 제공 하는 것입니다. 이 전략을 통해 고객은 Oracle 클라우드 및 다른 클라우드에 Oracle 소프트웨어를 배포하는 방법을 유연하게 선택할 수 있습니다. Oracle의 Microsoft와의 파트너 관계를 통해 고객은 Oracle의 인증과 지원으로 Microsoft 공용 및 사설 클라우드에서 Oracle 소프트웨어를 배포할 수 있습니다.  Oracle 공용 및 사설 클라우드 솔루션에 대 한 oracle의 약정 및 투자는 변경 되지 않습니다.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

세계 최고의 오픈 소스 솔루션 공급자 인 Red Hat는 90% 이상의 Fortune 500 회사에서 비즈니스 문제를 해결 하 고, IT 및 비즈니스 전략을 맞추고, 기술의 미래를 준비 하는 데 도움이 됩니다. Red Hat은 오픈 비즈니스 모델 및 저렴 하 고 예측 가능한 구독 모델을 통해 안전한 솔루션을 제공 하 여이를 달성 합니다.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure는 클라우드 컴퓨팅에 대해 우수한 안정성과 보안을 제공하는 검증된 플랫폼입니다. SUSE의 다양한 Linux 플랫폼은 Azure 클라우드 서비스와 자연스럽게 통합되어 쉽게 관리할 수 있는 클라우드 환경을 제공합니다. 1,800곳이 넘는 SUSE Linux Enterprise Server의 독립 소프트웨어 공급업체에서 9,200개 이상의 인증된 애플리케이션을 통해 SUSE는 데이터 센터에서 지원되는 실행 중인 작업이 확실히 Azure에 배포될 수 있도록 합니다.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 엔지니어링과 개방형 커뮤니티 거버넌스로 인해 소비자용 개인 클라우드 서비스를 비롯한 클라이언트, 서버 및 클라우드 컴퓨팅에서 Ubuntu가 성공할 수 있었습니다. Canonical은 전화에서 클라우드까지 적용되는 Ubuntu의 무료 통합 플랫폼에 대한 비전이 실현되도록 전화, 태블릿, TV 및 데스크톱에 대해 일관된 인터페이스 제품군을 제공합니다. 이러한 비전으로 인해 퍼블릭 클라우드 공급자에서 가전제품 제조업체에 이르는 다양한 기관에서 Ubuntu를 우선적으로 선택하고 있으며 개별 기술자 사이에서도 Ubuntu를 선호하게 되었습니다.

Canonical은 전 세계의 개발자 및 엔지니어링 센터와 함께 PC, 서버 및 핸드헬드 디바이스 업계에 Ubuntu 솔루션을 제공하기 위해 하드웨어 제조업체, 콘텐츠 공급자 및 소프트웨어 개발자와 유일하게 파트너 입장에 있습니다.
