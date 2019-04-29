---
title: 안전한 Azure Service Fabric 클러스터 연결 구성 | Microsoft Docs
description: Visual Studio를 사용하여 Azure 서비스 패브릭 클러스터에서 지원하는 보안 연결을 구성하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628299"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Visual Studio에서 서비스 패브릭 클러스터에 대한 보안 연결 구성
액세스 제어 정책이 구성되어 있는 Azure 서비스 패브릭 클러스터에 안전하게 액세스하기 위해 Visual Studio를 사용하는 방법에 대해 알아봅니다.

## <a name="cluster-connection-types"></a>클러스터 연결 유형
Azure Service Fabric 클러스터에서 지원되는 2가지 연결 형식: **비보안** 연결과 **x509 인증서 기반** 보안 연결입니다. (온-프레미스에 호스트된 Service Fabric 클러스터의 경우 **Windows** 및 **dSTS** 인증도 지원됩니다.) 클러스터를 만들 때 클러스터 연결 형식을 구성해야 합니다. 만든 후에는 연결 형식을 변경할 수 없습니다.

Visual Studio 서비스 패브릭 도구는 게시할 클러스터에 연결하기 위한 모든 인증 형식을 지원합니다. 보안 서비스 패브릭 클러스터를 설정하는 방법에 대한 지침은 [Azure Portal에서 서비스 패브릭 클러스터 설정](service-fabric-cluster-creation-via-portal.md)을 참조하세요.

## <a name="configure-cluster-connections-in-publish-profiles"></a>게시 프로필에서 클러스터 연결 구성
Visual Studio에서 Service Fabric 프로젝트를 게시하는 경우 **Service Fabric 애플리케이션 게시** 대화 상자에서 Azure Service Fabric 클러스터를 선택할 수 있습니다. **연결 엔드포인트** 아래의 해당 구독에서 기존 클러스터를 선택합니다.

![**Service Fabric 애플리케이션 게시** 대화 상자를 사용하여 Service Fabric 연결을 구성합니다.][publishdialog]

**Service Fabric 애플리케이션 게시** 대화 상자에서 자동으로 클러스터 연결의 유효성을 검사합니다. 메시지가 표시되면 Azure 계정에 로그인합니다. 유효성 검사에 통과하면 시스템에 클러스터를 안전하게 연결하기 위한 올바른 인증서가 설치되어 있거나 클러스터가 비보안이라는 것을 의미합니다. 네트워크 문제가 있거나 시스템이 보안 클러스터에 연결하도록 올바르게 구성되지 않으면 유효성 검사가 실패할 수 있습니다.

![**Service Fabric 애플리케이션 게시** 대화 상자가 기존의 정확히 구성된 Service Fabric 클러스터 연결의 유효성을 검사합니다.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>보안 클러스터에 연결하려면
1. 대상 클러스터에서 신뢰하는 클라이언트 인증서 중 하나에 액세스할 수 있는지 확인합니다. 인증서는 일반적으로 개인 정보 교환(.pfx) 파일로 공유됩니다. 클라이언트에 액세스를 허용하도록 서버를 구성하는 방법은 [Azure Portal에서 서비스 패브릭 클러스터 설정](service-fabric-cluster-creation-via-portal.md)을 참조하세요.
2. 신뢰할 수 있는 인증서를 설치합니다. 이를 위해 .pfx 파일을 두 번 클릭하거나 PowerShell 스크립트 Import-PfxCertificate를 사용하여 인증서를 가져옵니다. 인증서를 **Cert:\LocalMachine\My**에 설치합니다. 인증서를 가져오는 동안 모든 기본 설정을 수락할 수 있습니다.
3. 프로젝트의 바로 가기 메뉴에서 **게시...** 명령을 선택하여 **Azure 애플리케이션 게시** 대화 상자를 연 다음 대상 클러스터를 선택합니다. 도구가 자동으로 연결을 확인한 다음 게시 프로필에 보안 연결 매개 변수를 저장합니다.
4. 선택 사항: 보안 클러스터 연결을 지정 하려면 게시 프로필을 편집할 수 있습니다.
   
   게시 프로필 XML 파일을 수동으로 편집하여 인증서 정보를 지정하므로 인증서 저장소 이름, 저장소 위치 및 인증서 지문을 적어 두어야 합니다. 인증서 저장소 이름 및 저장소 위치에 이러한 값을 제공해야 합니다. [방법: 인증서의 지문 검색](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) 자세한 내용은 합니다.
   
   *ClusterConnectionParameters* 매개 변수를 사용하여 서비스 패브릭 클러스터에 연결할 때 사용할 PowerShell 매개 변수를 지정할 수 있습니다. 유효한 매개 변수는 Connect-ServiceFabricCluster cmdlet에서 허용하는 모든 매개 변수입니다. 사용 가능한 매개 변수 목록은 [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) 를 참조하세요.
   
   원격 클러스터에 게시하는 경우 해당 특정 클러스터에 적절한 매개 변수를 지정해야 합니다. 다음은 비보안 클러스터에 연결하기 위한 예제입니다.
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   다음은 x509 인증서 기반 보안 클러스터에 연결하기 위한 예제입니다.
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. 업그레이드 매개 변수 및 애플리케이션 매개 변수 파일 위치와 같이 필요한 다른 설정을 편집한 다음 Visual Studio의 **Service Fabric 애플리케이션 게시** 대화 상자에서 애플리케이션을 게시합니다.

## <a name="next-steps"></a>다음 단계
서비스 패브릭 클러스터에 액세스하는 방법에 대한 자세한 내용은 [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 참조하세요.

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
