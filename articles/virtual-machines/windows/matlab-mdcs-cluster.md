---
title: 가상 머신의 MATLAB 클러스터 | Microsoft Docs
description: Microsoft Azure 가상 머신을 사용하여 계산 집약적인 병렬 MATLAB 작업을 실행하기 위한 MATLAB 분산 컴퓨팅 서버 클러스터를 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 49824741facc8822a9417306794f1028fc180e16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555143"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Azure VM에 MATLAB 분산 컴퓨팅 서버 클러스터 만들기
Microsoft Azure 가상 머신을 사용하여 계산 집약적인 병렬 MATLAB 작업을 실행하기 위한 MATLAB 분산 컴퓨팅 서버 클러스터를 하나 이상 만듭니다. MATLAB 분산 컴퓨팅 서버 소프트웨어를 VM에 설치하여 기본 이미지로 사용하고, Azure 빠른 시작 템플릿 또는 Azure PowerShell 스크립트( [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)에 제공됨)를 사용하여 클러스터를 배포하고 관리합니다. 배포 후에 클러스터에 연결하여 작업을 실행합니다.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>MATLAB 및 MATLAB 분산 컴퓨팅 서버 정보
[MATLAB](https://www.mathworks.com/products/matlab/) 플랫폼은 공학 및 과학 문제 해결에 최적화되어 있습니다. 대규모 시뮬레이션 및 데이터 처리 작업을 수행하는 MATLAB 사용자는 MathWorks 병렬 컴퓨팅 제품을 사용하면 계산 클러스터 및 그리드 서비스의 이점을 활용하여 계산 집약적인 워크로드의 속도를 높일 수 있습니다. [병렬 컴퓨팅 도구 상자](https://www.mathworks.com/products/parallel-computing/)는 MATLAB 사용자가 애플리케이션을 병렬화하여 멀티 코어 프로세서, GPU 및 계산 클러스터의 이점을 활용할 수 있도록 합니다. [MATLAB 분산 컴퓨팅 서버](https://www.mathworks.com/products/distriben/) 는 MATLAB 사용자가 하나의 계산 클러스터 내에서 여러 컴퓨터를 활용할 수 있도록 합니다.

Azure 가상 머신을 사용하면, 대화형 작업, 배치 작업, 독립 작업, 통신 작업 같은 병렬 작업을 온-프레미스 클러스터로 전송하는 데 사용하는 메커니즘과 같은 모든 메커니즘을 포함하는 MATLAB 분산 컴퓨팅 서버 클러스터를 만들 수 있습니다. Azure를 MATLAB 플랫폼과 결합하여 사용하면 기존의 온-프레미스 하드웨어를 프로비전하고 사용하는 것에 비해 많은 장점이 있습니다. 다양한 가상 머신 크기, 사용하는 계산 리소스에 대해서만 지불하는 주문형 클러스터 생성, 대규모로 모델을 테스트할 수 있는 기능 등이 장점입니다.  

## <a name="prerequisites"></a>필수 조건
* **클라이언트 컴퓨터** - 배포 후 Azure 및 MATLAB 분산 컴퓨팅 서버 클러스터와 통신하기 위한 Windows 기반 클라이언트 컴퓨터가 필요합니다.
* **Azure PowerShell** - 클라이언트 컴퓨터에 설치하려면 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하세요.
* **Azure 구독** - 구독이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/free/) 을 만들 수 있습니다. 대규모 클러스터의 경우, 종량제 구독이나 다른 구매 옵션을 고려하세요.
* **vCPU 할당량** - 대형 클러스터 또는 MATLAB 분산 컴퓨팅 서버 클러스터를 하나 이상 배포하려면 vCPU 할당량을 늘리는 것이 필요할 수 있습니다. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 합니다.
* **MATLAB, 병렬 컴퓨팅 도구 상자 및 MATLAB 분산 컴퓨팅 서버 정보 라이선스** - 스크립트는 모든 라이선스에 대해 [MathWorks 호스티드 라이선스 관리자](https://www.mathworks.com/help/install/license-management.html) 가 사용되었다고 가정합니다.  
* **MATLAB 분산 컴퓨팅 서버 소프트웨어** - 클러스터 VM의 기본 VM 이미지로 사용될 VM에 설치됩니다.

## <a name="high-level-steps"></a>대략적인 단계
MATLAB 분산 컴퓨팅 서버 클러스터에 대해 Azure 가상 머신을 사용하려면, 다음과 같은 대략적인 단계가 필요합니다. 자세한 지침은 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)의 스크립트 및 빠른 시작 템플릿을 포함하는 설명서에 있습니다.

1. **기본 VM 이미지 만들기**  

   * MATLAB 분산 컴퓨팅 서버 소프트웨어를 VM에 다운로드하여 설치합니다.

     > [!NOTE]
     > 이 프로세스에 한두 시간이 소요될 수 있지만, 사용하는 MATLAB 각 버전에 대해 한 번만 수행하면 됩니다.   
     >
     >
2. **하나 이상의 클러스터 만들기**  

   * 제공된 PowerShell 스크립트를 사용하거나 빠른 시작 템플릿을 사용하여 기본 VM 이미지에서 클러스터를 만듭니다.   
   * 제공된 PowerShell 스크립트를 사용하여 클러스터를 관리합니다. 클러스터를 나열하고, 일시 중지하고, 다시 시작하고, 삭제하는 것이 가능합니다.

## <a name="cluster-configurations"></a>클러스터 구성
현재, 클러스터 생성 스크립트 및 템플릿을 사용하면 단일 MATLAB 분산 컴퓨팅 서버 토폴로지를 만들 수 있습니다. 원한다면, 하나 이상의 클러스터를 추가적으로 만들고, 각 클러스터마다 작업자 VM의 수를 다르게 지정하고, 다른 VM 크기를 사용하는 등의 작업이 가능합니다.

### <a name="matlab-client-and-cluster-in-azure"></a>Azure의 MATLAB 클라이언트 및 클러스터
MATLAB 클라이언트 노드, MATLAB 작업 Scheduler 노드 및 MATLAB 분산 컴퓨팅 서버 “작업자” 노드는 다음 그림에서와 같이, 모두 가상 네트워크의 Azure VM으로 구성됩니다.


* 클러스터를 사용하려면 원격 데스크톱을 통해 클라이언트 노드에 연결합니다. 클라이언트 노드는 MATLAB 클라이언트를 실행합니다.
* 클라이언트 노드에는 모든 작업자가 액세스할 수 있는 파일 공유가 있습니다.
* MathWorks 호스티드 라이선스 관리자가 모든 MATLAB 소프트웨어의 라이선스를 확인하기 위해 사용됩니다.
* 기본적으로, vCPU당 하나의 MATLAB 분산 컴퓨팅 서버 작업자가 작업자 VM에 생성되지만, 어떤 숫자든 지정할 수 있습니다.

## <a name="use-an-azure-based-cluster"></a>Azure 기반 클러스터 사용
다른 유형의 MATLAB 분산 컴퓨터 서버 클러스터에서처럼, MATLAB 작업 Scheduler 프로필을 만들려면 MATLAB 클라이언트(또는 클라이언트 VM)의 클라이언트 프로필 관리자를 사용해야 합니다.

![클러스터 프로필 관리자](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>다음 단계
* Azure에서 MATLAB 분산 컴퓨팅 서버 클러스터를 배포하고 관리하는 자세한 내용은, 템플릿과 스크립트를 포함하는 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) 리포지토리를 참조하세요.
* MATLAB 및 MATLAB 분산 컴퓨팅 서버에 대한 자세한 설명서는 [MathWorks 사이트](https://www.mathworks.com/) 를 참조하세요.
