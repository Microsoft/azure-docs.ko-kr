---
title: 'Service Fabric 클러스터 보안: 클라이언트 역할 | Microsoft Docs'
description: 이 문서에서는 두 개의 클라이언트 역할 및 역할에 제공된 사용 권한을 설명합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: ''
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ed000dc4be1ae45382d688d4a596ec745c69d0bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711161"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>서비스 패브릭 클라이언트용 역할 기반 액세스 제어
Azure 서비스 패브릭은 서비스 패브릭 클러스터에 연결된 클라이언트 즉, 관리자 및 사용자에 대한 액세스 제어 형식을 지원합니다. 액세스 제어를 사용하면 클러스터 관리자가 사용자 그룹마다 특정 클러스터 작업에 대한 액세스를 제한하여 클러스터의 보안을 강화할 수 있습니다.  

**관리자** 는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 기본적으로 **사용자**는 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 애플리케이션과 서비스를 확인하는 기능만 갖습니다.

클러스터 생성 시 각각에 대해 개별 인증서를 제공하여 두 개의 클라이언트 역할(관리자 및 클라이언트)을 지정합니다. 안전한 서비스 패브릭 클러스터를 설정하는 방법에 대한 자세한 내용은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md) 을 참조하세요.

## <a name="default-access-control-settings"></a>기본 액세스 제어 설정
관리자 액세스 제어 형식에는 모든 FabricClient API에 대한 전체 권한이 있습니다. 다음 작업을 포함하여 서비스 패브릭 클러스터에 대해 읽기 및 쓰기를 수행할 수 있습니다.

### <a name="application-and-service-operations"></a>애플리케이션 및 서비스 작업
* **CreateService**: 서비스 만들기                             
* **CreateServiceFromTemplate**: 템플릿에서 서비스 만들기                             
* **UpdateService**: 서비스 업데이트                             
* **DeleteService**: 서비스 삭제                             
* **ProvisionApplicationType**: 애플리케이션 형식 프로비전                             
* **CreateApplication**: 애플리케이션 만들기                               
* **DeleteApplication**: 애플리케이션 삭제                             
* **UpgradeApplication**: 애플리케이션 업그레이드 시작 또는 중단                             
* **UnprovisionApplicationType**: 애플리케이션 형식 프로비전 해제                             
* **MoveNextUpgradeDomain**: 명시적 업데이트 도메인으로 애플리케이션 업그레이드 다시 시작                             
* **ReportUpgradeHealth**: 현재 업그레이드 진행으로 애플리케이션 업그레이드 다시 시작                             
* **ReportHealth**: 상태 보고                             
* **PredeployPackageToNode**: 사전 배포 API                            
* **CodePackageControl**: 코드 패키지 다시 시작                             
* **RecoverPartition**: 파티션 복구                             
* **RecoverPartitions**: 파티션 복구                             
* **RecoverServicePartitions**: 서비스 파티션 복구                             
* **RecoverSystemPartitions**: 시스템 서비스 파티션 복구                             

### <a name="cluster-operations"></a>클러스터 작업
* **ProvisionFabric**: MSI 및/또는 클러스터 매니페스트 프로 비전                             
* **UpgradeFabric**: 클러스터 업그레이드 시작                             
* **UnprovisionFabric**: MSI 및/또는 클러스터 매니페스트 프로 비전 해제                         
* **MoveNextFabricUpgradeDomain**: 명시적 업데이트 도메인으로 클러스터 업그레이드 다시 시작                             
* **ReportFabricUpgradeHealth**: 현재 업그레이드 프로세스로 클러스터 업그레이드 다시 시작                             
* **StartInfrastructureTask**: 인프라 작업 시작                             
* **FinishInfrastructureTask**: 인프라 작업 완료                             
* **InvokeInfrastructureCommand**: 인프라 작업 관리 명령                              
* **ActivateNode**: 노드 활성화                             
* **DeactivateNode**: 노드 비활성화                             
* **DeactivateNodesBatch**: 다중 노드 비활성화                             
* **RemoveNodeDeactivations**: 다중 노드에서 비활성화 되돌리기                             
* **GetNodeDeactivationStatus**: 비활성화 상태 확인                             
* **NodeStateRemoved**: 노드 상태 제거됨 보고                             
* **ReportFault**: 오류 보고                             
* **FileContent**: 이미지 저장소 클라이언트 파일 전송(클러스터 외부)                             
* **FileDownload**: 이미지 저장소 클라이언트 파일 다운로드 시작(클러스터 외부)                             
* **InternalList**: 이미지 저장소 클라이언트 파일 목록 작업(내부)                             
* **Delete**: 이미지 저장소 클라이언트 삭제 작업                              
* **Upload**: 이미지 저장소 클라이언트 업로드 작업                             
* **NodeControl**: 노드 시작, 중지 및 다시 시작                             
* **MoveReplicaControl**: 노드 간 복제본 이동                             

### <a name="miscellaneous-operations"></a>기타 작업
* **Ping**: 클라이언트 Ping                             
* **Query**: 허용된 모든 쿼리
* **NameExists**: 이름 지정 URI 존재 확인                             

사용자 액세스 제어 형식은 기본적으로 다음 작업으로 제한됩니다. 

* **EnumerateSubnames**: 이름 지정 URI 열거                             
* **EnumerateProperties**: 이름 지정 속성 열거                             
* **PropertyReadBatch**: 이름 지정 속성 읽기 작업                             
* **GetServiceDescription**: 긴 폴링 서비스 알림 및 서비스 설명 읽기                             
* **ResolveService**: 불만 기반 서비스 확인                             
* **ResolveNameOwner**: 이름 지정 URI 소유자 확인                             
* **ResolvePartition**: 시스템 서비스 확인                             
* **ServiceNotifications**: 이벤트 기반 서비스 알림                             
* **GetUpgradeStatus**: 애플리케이션 업그레이드 상태 폴링                             
* **GetFabricUpgradeStatus**: 클러스터 업그레이드 상태 폴링                             
* **InvokeInfrastructureQuery**: 인프라 작업 쿼리                             
* **List**: 이미지 저장소 클라이언트 파일 목록 작업                             
* **ResetPartitionLoad**: 장애 조치(failover) 단위에 대한 부하 초기화                             
* **ToggleVerboseServicePlacementHealthReporting**: 자세한 정보 표시 서비스 배치 상태 보고 설정/해제                             

또한 관리자 액세스 제어는 앞의 작업에 액세스할 수 있습니다.

## <a name="changing-default-settings-for-client-roles"></a>클라이언트 역할의 기본 설정 변경
클러스터 매니페스트 파일에서는 필요한 경우 클라이언트에 대한 관리 기능을 제공합니다. [클러스터 생성](service-fabric-cluster-creation-via-portal.md) 중 **패브릭 설정** 옵션으로 이동하고 **이름**, **관리**, **사용자** 및 **값** 필드에 앞의 설정을 제공하여 기본값을 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)

[서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)

