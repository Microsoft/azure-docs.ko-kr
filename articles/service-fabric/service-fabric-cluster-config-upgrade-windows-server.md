---
title: 독립 실행형 클러스터의 구성 업그레이드
description: 독립 실행형 Service Fabric 클러스터를 실행하는 구성을 업그레이드하는 방법을 알아봅니다.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610128"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>독립 실행형 클러스터의 구성 업그레이드 

최신 시스템에서 업그레이드 기능은 제품의 장기적인 성공을 위한 핵심적 요소입니다. Azure 서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다. 이 문서에서는 독립 실행형 Service Fabric 클러스터의 구성 설정을 업그레이드하는 방법을 설명합니다.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>ClusterConfig.json 파일에서 클러스터 설정 사용자 지정
독립 실행형 클러스터는 *ClusterConfig.json* 파일을 통해 구성됩니다. 다른 설정에 대해 자세히 알아보려면 [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)을 참조하세요.

*ClusterConfig.json*의 [클러스터 속성](./service-fabric-cluster-manifest.md#cluster-properties) 섹션 아래에서 `fabricSettings` 섹션의 설정을 추가, 업데이트 또는 제거할 수 있습니다. 

예를 들어 다음 JSON은 새 설정 *MaxDiskQuotaInMB*를 `fabricSettings` 아래의 *진단* 섹션에 추가합니다.

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

ClusterConfig.json 파일의 설정을 수정한 후 [클러스터 구성을 테스트](#test-the-cluster-configuration)한 다음, [클러스터 구성을 업그레이드](#upgrade-the-cluster-configuration)하여 클러스터에 설정을 적용합니다. 

## <a name="test-the-cluster-configuration"></a>클러스터 구성 테스트
구성 업그레이드를 시작하기 전에 독립 실행형 패키지에서 다음 PowerShell 스크립트를 실행하여 새 클러스터 구성 JSON을 테스트할 수 있습니다.

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

또는 다음 스크립트를 사용합니다.

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

일부 구성 (예: 엔드포인트, 클러스터 이름, 노드 IP 등)을 업그레이드할 수 없습니다. 새 클러스터 구성 JSON은 이전 구성에 대해 테스트 되 고 문제가 있는 경우 PowerShell 창에서 오류를 throw 합니다.

## <a name="upgrade-the-cluster-configuration"></a>클러스터 구성 업그레이드
클러스터 구성을 업그레이드하려면 [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)를 실행합니다. 업그레이드 도메인으로 구성 업그레이드가 처리됩니다.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>클러스터 인증서 구성 업그레이드
클러스터 인증서는 클러스터 노드 간의 인증에 사용됩니다. 인증서 롤오버는 클러스터 노드 간 통신을 차단하는 오류 때문에 특별히 주의해서 수행해야 합니다.

네 가지 옵션이 지원됩니다.  

* 단일 인증서 업그레이드: 업그레이드 경로는 인증서 A(기본) -> 인증서 B(기본) -> 인증서 C(기본) ->....입니다.

* 이중 인증서 업그레이드: 업그레이드 경로는 인증서 A(기본) -> 인증서 A(기본) 및 B(보조) -> 인증서 B(기본) -> 인증서 B(기본) 및 C(보조) -> 인증서 C(기본) ->....입니다.

* 인증서 형식 업그레이드: 지문 기반 인증서 구성 <-> CommonName 기반 인증서 구성입니다. 예를 들어, 인증서 지문 A(기본) 및 지문 B(보조) -> 인증서 CommonName C입니다.

* 인증서 발급자 지문 업그레이드: 업그레이드 경로는 Certificate CN=A,IssuerThumbprint=IT1(기본) -> Certificate CN=A,IssuerThumbprint=IT1,IT2(기본) -> Certificate CN=A,IssuerThumbprint=IT2(기본)입니다.


## <a name="next-steps"></a>다음 단계
* [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md) 중 일부를 사용자 지정하는 방법 알아보기
* [클러스터를 확장 및 축소](service-fabric-cluster-scale-up-down.md)하는 방법 알아보기
* [애플리케이션 업그레이드](service-fabric-application-upgrade.md)에 대해 알아보기

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
