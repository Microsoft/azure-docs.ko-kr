---
title: 관리 ID를 지원하는 Azure 서비스 - Azure AD
description: Azure 리소스 및 Azure AD 인증에 대해 관리 ID를 지원하는 서비스 목록
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 07/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: e62fc34ad02303f61214f754f6506c39af76fc94
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267448"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 서비스

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드에 자격 증명이 없어도 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 현재 Azure 전반에 걸쳐 Azure 리소스와 Azure AD 인증에 대한 관리 ID를 통합하는 과정이 진행되고 있습니다. 업데이트를 자주 확인해 주세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스

다음 Azure 서비스는 Azure 리소스에 대한 관리 ID를 지원합니다.


### <a name="azure-api-management"></a>Azure API Management

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |

사용 가능한 지역에서 Azure API Management용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](../../api-management/api-management-howto-use-managed-service-identity.md)


### <a name="azure-app-service"></a>Azure App Service

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check]  | ![사용 가능][check]  | ![사용 가능][check] |

사용 가능한 지역에서 Azure App Service용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 템플릿](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc를 지원하는 Kubernetes

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

Azure Arc enabled Kubernetes는 현재 [시스템 할당 id를 지원](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes)합니다. 관리 서비스 id 인증서는 Azure와의 통신을 위해 모든 Azure Arc 사용 Kubernetes 에이전트에 사용 됩니다.

### <a name="azure-blueprints"></a>Azure Blueprints

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |

[Azure Blueprints](../../governance/blueprints/overview.md)에서 관리 ID를 사용하려면 다음 목록을 참조하세요.

- [Azure Portal - 청사진 할당](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - 청사진 할당](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |


### <a name="azure-container-instances"></a>Azure Container Instances

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Container Instances용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 템플릿](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry 작업

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Container Registry 태스크용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Data Factory V2용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST (영문)](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)



### <a name="azure-event-grid"></a>Azure Event Grid 

관리 ID 유형 |모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음  | 사용할 수 없음  | 사용할 수 없음 |









### <a name="azure-functions"></a>Azure 기능

관리 ID 유형 |모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check]  | ![사용 가능][check]  | ![사용 가능][check]  |

사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 템플릿](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Data Factory V2용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | Azure 가져오기 내보내기 서비스를 사용할 수 있는 지역에서 사용할 수 있습니다. | 미리 보기 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] | 
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |


자세한 내용은 [Azure Kubernetes Service에서 관리 ID 사용](../../aks/use-managed-identity.md)을 참조하세요.


### <a name="azure-logic-apps"></a>Azure Logic Apps

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |


사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager 템플릿](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)


### <a name="azure-policy"></a>Azure Policy

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

Azure Policy에 대 한 관리 되는 id를 구성 하려면 다음 목록을 참조 하세요 (사용 가능한 지역).

- [Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- [Azure 리소스 관리자 템플릿](/azure/templates/microsoft.authorization/policyassignments)
- [REST (영문)](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Service Fabric 응용 프로그램에 대 한 관리 id](../../service-fabric/concepts-managed-identity.md) 는 모든 지역에서 사용할 수 있습니다.

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |사용할 수 없음 |

모든 지역에서 Azure Service Fabric 애플리케이션에 대한 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |


자세한 내용은 [Azure 스프링 클라우드 응용 프로그램에 대 한 시스템 할당 관리 id를 사용 하도록 설정 하는 방법](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)을 참조 하세요.


### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 미리 보기 | 미리 보기 | 미리 보기 |
| 사용자 할당 | ![사용 가능][check] | 미리 보기 | 미리 보기 | 미리 보기 |

사용 가능한 지역에서 Azure Virtual Machines Scale Sets용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 미리 보기 | 미리 보기 | 
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 미리 보기 | 미리 보기 |

사용 가능한 지역에서 Azure Virtual Machines용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM 이미지 작성기

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 
| 사용자 할당 | [지원 되는 지역에서 사용 가능](../../virtual-machines/windows/image-builder-overview.md#regions) | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

Azure VM 이미지 작성기 (사용 가능한 지역)에 대해 관리 id를 구성 하는 방법을 알아보려면 [이미지 작성기 개요](../../virtual-machines/windows/image-builder-overview.md#permissions)를 참조 하세요.
### <a name="azure-signalr-service"></a>Azure SignalR Service

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |

Azure SignalR Service에 대 한 관리 되는 id를 구성 하려면 다음 목록을 참조 하세요 (사용 가능한 지역).

- [Azure Resource Manager 템플릿](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, Azure 리소스에 대해 관리 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager에 대한 액세스를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal을 통해 액세스 할당](howto-assign-access-portal.md)
- [PowerShell을 통해 액세스 할당](howto-assign-access-powershell.md)
- [Azure CLI를 통해 액세스 할당](howto-assign-access-CLI.md)
- [Azure Resource Manager 템플릿을 통해 액세스 할당](../../role-based-access-control/role-assignments-template.md)

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://management.azure.com/`| ![사용 가능][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![사용 가능][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![사용 가능][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![사용 가능][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://vault.azure.net`| ![사용 가능][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![사용 가능][check] |

### <a name="azure-data-lake"></a>Azure 데이터 레이크

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://datalake.azure.net/` | ![사용 가능][check] |
| Azure Government |  | 사용할 수 없음 |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |

### <a name="azure-sql"></a>Azure SQL

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://database.windows.net/` | ![사용 가능][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![사용 가능][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![사용 가능][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![사용 가능][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://eventhubs.azure.net` | ![사용 가능][check] |
| Azure Government |  | 사용할 수 없음 |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |

### <a name="azure-service-bus"></a>Azure Service Bus

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://servicebus.azure.net`  | ![사용 가능][check] |
| Azure Government |  | ![사용 가능][check] |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage Blob 및 큐

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![사용 가능][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![사용 가능][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://*.asazure.windows.net` | ![사용 가능][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![사용 가능][check] |

> [!Note]
> Microsoft Power BI에서도 [관리 ID](../../stream-analytics/powerbi-output-managed-identity.md)를 지원합니다.


[check]: media/services-support-managed-identities/check.png "사용 가능"