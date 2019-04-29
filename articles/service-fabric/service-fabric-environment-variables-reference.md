---
title: Azure Service Fabric 환경 변수 | Microsoft Docs
description: Service Fabric 환경 변수에 대한 참조 설명서
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946725"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric 환경 변수

Service Fabric에는 각 서비스 인스턴스에 대해 설정된 기본 제공 환경 변수가 있습니다. 환경 변수의 전체 목록은 다음과 같습니다.

| 환경 변수                         | 설명                                                            | 예                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | 애플리케이션의 패브릭 URI 이름                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | 프로세스가 속하는 코드 패키지의 이름              | 코드                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | 엔드포인트 IP 주소 또는 FQDN                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | 엔드포인트에 대한 포트 번호                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | 로그 폴더                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | 임시 폴더                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | 작업 폴더                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | 애플리케이션 홈 폴더                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | 프로세스가 컨테이너인지를 지정하는 부울                   | false                                                                |
| Fabric_NodeId                                | 프로세스를 실행 중인 노드의 노드 ID                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | 클러스터 매니페스트 파일에 지정된 노드의 IP 또는 FQDN | localhost 또는 10.0.0.1                                                |
| Fabric_NodeName                              | 프로세스를 실행 중인 노드의 노드 이름                          | _Node_0                                                              |
| Fabric_ServiceName                           | 서비스의 패브릭 URI가 ExclusiveProcess 모드에서 호스트되는 경우 서비스의 이름. 이 변수 값은 ServicePackageActivationMode ExclusiveProcess를 사용하여 서비스를 생성한 경우에만 제공됩니다.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | GUID                                                               |
| Fabric_ServicePackageName                    | 프로세스가 속하는 서비스 패키지의 이름                     | Web1Pkg                                                              |

Service Fabric 런타임에서 사용되는 내부 환경 변수:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
