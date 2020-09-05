---
title: 지역 간 Azure 리소스 이동에 대 한 지원
description: Azure 지역 간에 이동할 수 있는 Azure 리소스 종류를 나열 합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 8c18a4d2fa6e5bdb211b77d4d7bb28af7e5b1c1a
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948116"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>지역 간 Azure 리소스 이동에 대 한 지원

이 문서에서는 Azure 리소스 종류가 다른 Azure 지역으로 이동 하는 데 지원 되는지 여부를 확인 합니다. 

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | domainservices | 예 | 
> | domainservices/replicasets | 예 | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | tenants | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | actionrules | 예 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 아니요 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서비스 |  예 (템플릿 사용) <br/><br/> [지역 간에 API Management 이동](../../api-management/api-management-howto-migrate.md)합니다. | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | configurationstores | 아니요 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | apiapps | 예 (템플릿 사용)<br/><br/> [App Service 앱을 다른 지역으로 이동](../../app-service/manage-move-across-regions.md) | 
> | appidentities | 예 | 
> | gateways | 예 | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | policyassignments | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | automationaccounts | 예 (템플릿 사용) <br/><br/> [지역에서 복제 사용](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts / configurations | 예 | 
> | automationaccounts / runbooks | 예 | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | b2cdirectories | 예 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlserverregistrations | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registrations | 예 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 계정은 한 지역에서 다른 지역으로 직접 이동할 수 없지만 템플릿을 사용 하 여 템플릿을 내보내고 수정한 다음 템플릿을 새 지역에 배포할 수 있습니다. <br/><br/> [지역 간 Batch 계정 이동](../../batch/best-practices.md#moving-batch-accounts-across-regions) 에 대 한 자세한 정보 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 <br/><br/> Azure Batch AI 서비스가 사용 [중지](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)되었습니다.
> | fileservers | 예 | 
> | jobs | 예 | 
> | workspaces | 예 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mapapis | 예 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | biztalk | 예 | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blockchainmembers | 예 <br/><br/> Blockchain 네트워크는 다른 지역에 노드를 포함할 수 없습니다. 
> | watchers | 예 | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blueprintassignments | 예 | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | botservices | 예 | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | redis | 예 | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 예 |
> | 프로필 | 아니요 | 
> | profiles / endpoints | 예 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificateorders | 예 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domainnames | 클래식 서비스에 대 한 작업은 예정 되어 있지 않습니다.
> | virtualmachines | 예 | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | networksecuritygroups | 클래식 서비스에 대 한 작업은 예정 되어 있지 않습니다.
> | reservedips | 예 | 
> | virtualnetworks | 예 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예 |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 
> | Cognitive Search | 수동 단계에서 지원 됩니다.<br/><br/> [Azure Cognitive Search 서비스를 다른 지역으로 이동 하](../../search/search-howto-move-across-regions.md) 는 방법을 알아봅니다.

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availabilitysets | 예 | 
> | diskencryptionsets | 예 | 
> | disks | 예 | 
> | galleries | 예 | 
> | galleries / images | 예 | 
> | galleries / images / versions | 예 | 
> | hostgroups | 예 | 
> | hostgroups / hosts | 예 | 
> | images | 예 | 
> | proximityplacementgroups | 아니요 | 
> | restorepointcollections | 예 | 
> | sharedvmimages | 예 | 
> | sharedvmimages / versions | 예 | 
> | 스냅샷 | 예 | 
> | virtualmachines | 예 | 
> | virtualmachines / extensions | 예 | 
> | virtualmachinescalesets | 예 | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 예 | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 예 | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registries | 예 |  
> | registries / buildtasks | 예 |  
> | registries / replications | 예 | 
> | registries / tasks | 예 |  
> | registries / webhooks | 예 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containerservices | 아니요.<br/><br/> 서비스가 사용 [중지](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)되었습니다.
> | managedclusters | 예 | 
> | openshiftmanagedclusters | 예 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 예 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 커넥터 | 예 |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hubs | 예 |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | resourceproviders | 아니요 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs | 예 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databoxedgedevices | 예 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 예 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | catalogs | 예 | 
> | datacatalogs | 예 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | connectionmanagers | 예 | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 패키지 | 예 | 
> | 플랜 | 예 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datafactories | 예 | 
> | factories | 예 |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datalakeaccounts | 예 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 | 
> | services / projects | 예 | 
> | slots | 예 | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 지역 간 읽기 복제본을 사용 하 여 기존 서버를 이동할 수 있습니다. [자세히 알아보기](../../postgresql/howto-move-regions-portal.md).<br/><br/> 지역 중복 백업 저장소를 사용 하 여 서비스를 프로 비전 하는 경우 지역 복원을 사용 하 여 다른 지역에서 복원할 수 있습니다. [자세히 알아보기](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 지역 간 읽기 복제본을 사용 하 여 기존 서버를 이동할 수 있습니다. [자세히 알아보기](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servergroups | 예 | 
> | servers | 지역 간 읽기 복제본을 사용 하 여 기존 서버를 이동할 수 있습니다. [자세한 정보](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | 아니요 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | artifactsources | 예 | 
> | rollouts | 예 |  
> | servicetopologies | 아니요 | 
> | servicetopologies / services | 아니요 |  
> | servicetopologies / services / serviceunits | 아니요 | 
> | 단계 | 아니요 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | elasticpools | 아니요. 리소스가 노출 되지 않습니다.
> | elasticpools / iothubtenants | 아니요. 리소스가 노출 되지 않습니다.
> | iothubs | 예. [자세한 정보](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | 아니요 | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | controllers | 아니요 | 
> | AKS 클러스터 | 아니요<br/><br/> 다른 지역으로 이동 하는 방법에 [대해 자세히 알아보세요](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) .

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labcenters | 예 | 
> | labs | 예 | 
> | labs / environments | 아니요 |  
> | labs / servicerunners | 아니요 | 
> | labs / virtualmachines | 예 |  
> | schedules | 예 |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databaseaccounts | 예 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 예 | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 예 |  
> | topics | 예 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 |  
> | 네임스페이스 | 예 (템플릿 사용)<br/><br/> [이벤트 허브 네임 스페이스를 다른 지역으로 이동](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hanainstances | 예 | 
> | sapmonitors | 예 |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | machines | 예 | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datamanagers |  예 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs |  예 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 
> | actiongroups |  아니요 | 
> | activitylogalerts | 예 | 
> | alertrules |  예 | 
> | autoscalesettings |  예 | 
> | components |  예 |  
> | guestdiagnosticsettings | 예 | 
> | metricalerts | 예 | 
> | notificationgroups | 예 | 
> | notificationrules | 예 | 
> | scheduledqueryrules |  예 | 
> | webtests |  아니요 | 
> | workbooks |  아니요 |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | checknameavailability |  아니요.<br/><br/> IoT Central는 지역이 아닌 지역에서 작동 합니다.
> | graph | 아니요

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 영역 이동 | 
> | ------------- | ----------- |
> |  iothub |  예 (허브 복제) <br/><br/> [IoT hub를 다른 지역에 복제](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | checknameavailability |  예 |  
> | graph |  예 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hsmpools | 예 | 
> | vaults |  예 | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters |  예 |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labaccounts | 예 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요, 글로벌 서비스입니다.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingenvironments | 예 | 
> | integrationaccounts |  아니요 |  
> | integrationserviceenvironments | 예 | 
> | isolatedenvironments | 예 | 
> | workflows |  아니요 |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | commitmentplans |  예 | 
> | webservices |  예 | 
> | workspaces |  예 | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | operationalizationclusters |  예 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 
> | accounts / workspaces | 예 | 
> | accounts / workspaces / projects | 예 | 
> | teamaccounts | 예 | 
> | teamaccounts / workspaces | 예 | 
> | teamaccounts / workspaces / projects | 예 | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingaccounts | 예 | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 예 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | userassignedidentities | 예 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 |  아니요, Azure Maps는 지리 공간적 서비스입니다. 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | classicdevservices | 클래식 서비스에 대해 계획 된 작업이 없습니다. 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mediaservices |  예 | 
> | mediaservices / liveevents |  예 | 
> | mediaservices / streamingendpoints |  예 | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appclusters | 예 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | assessmentprojects | 예 | 
> | migrateprojects | 예 | 
> | projects | 예 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | netappaccounts | 예 | 
> | netappaccounts / capacitypools | 예 | 
> | netappaccounts / capacitypools / volumes | 예 | 
> | netappaccounts / capacitypools / volumes / mounttargets | 예 | 
> | netappaccounts / capacitypools / volumes / snapshots | 예 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgateways | 예 | 
> | applicationgatewaywebapplicationfirewallpolicies | 예 | 
> | applicationsecuritygroups |  예 |  
> | azurefirewalls |  예 |  
> | bastionhosts | 예 | 
> | connections |  아니요 | 
> | ddoscustompolicies |  아니요 | 
> | ddosprotectionplans | 예 | 
> | dnszones |  예 | 
> | expressroutecircuits | 예 | 
> | expressroutecrossconnections | 예 | 
> | expressroutegateways | 예 | 
> | expressrouteports | 예 | 
> | frontdoors | 예 | 
> | frontdoorwebapplicationfirewallpolicies | 예 | 
> | loadbalancers | 예 <br/><br/> 기존 구성을 템플릿으로 내보내고 새 지역에 템플릿을 배포할 수 있습니다. [외부](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) 또는 [내부](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) 부하 분산 장치를 이동 하는 방법을 알아봅니다. |
> | localnetworkgateways |  예 | 
> | natgateways |  예 | 
> | networkintentpolicies |  아니요 | 
> | networkinterfaces | 예 | 
> | networkprofiles | 예 | 
> | networksecuritygroups | 예 | 
> | networkwatchers |  예 |  
> | networkwatchers / connectionmonitors |  아니요 | 
> | networkwatchers/lenses |  예 | 
> | networkwatchers / pingmeshes |  예 | 
> | p2svpngateways | 예 | 
> | privatednszones |  예 |  
> | privatednszones / virtualnetworklinks |  예 |  
> | privateendpoints | 아니요 | 
> | privatelinkservices | 예 | 
> | publicipaddresses | 예<br/><br/> 기존 공용 IP 주소 구성을 템플릿으로 내보내고 새 지역에 템플릿을 배포할 수 있습니다. 공용 IP 주소 이동에 [대해 자세히 알아보세요](../../virtual-network/move-across-regions-publicip-portal.md) . |
> | publicipprefixes | 아니요 | 
> | routefilters | 예 | 
> | routetables |  아니요 | 
> | serviceendpointpolicies |  예 | 
> | trafficmanagerprofiles |  예 | 
> | virtualhubs | 예 | 
> | virtualnetworkgateways |  예 |  
> | virtualnetworks |  예 | 
> | virtualnetworktaps | 예 | 
> | virtualwans | 예 | 
> | vpngateways(Virtual WAN) | 예 | 
> | vpnsites(Virtual WAN) | 예 | 
> | webapplicationfirewallpolicies |  아니요 | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아니요 | 
> | namespaces / notificationhubs |  아니요 |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces |  예 | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managementconfigurations |  예 | 
> | 뷰 |  예 | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | peerings | 예 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dashboards | 예 | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | rootresources | 예 | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspacecollections |  아니요 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | capacities |  예 | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | vaults | 아니요.<br/><br/> Azure 지역에서 Azure Backup에 대 한 Recovery Services 자격 증명 모음 이동은 지원 되지 않습니다.<br/><br/> Azure Site Recovery에 대 한 Recovery Services 자격 증명 모음에서 대상 지역에 [자격 증명 모음을 사용 하지 않도록 설정 하 고 다시 만들](../../site-recovery/move-vaults-across-regions.md) 수 있습니다. | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  예 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 쿼리 |  예 |  

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 |
> | ------------- | ----------- |
> | deploymentScripts |  예<br/><br/>[Microsoft .Resources 리소스를 새 지역으로 이동](microsoft-resources-move-regions.md) |
> | templateSpecs |  예<br/><br/>[Microsoft .Resources 리소스를 새 지역으로 이동](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  예 | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 영역 이동 | 
> | ------------- | ----------- |
> | flows |  아니요 |  
> | jobcollections |  예 | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | searchservices |  아니요 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  아니요 | 
> | playbookconfigurations | 예 | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | gateways | 예 | 
> | nodes | 예 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  예 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 예 | 
> | clusters |  예 | 
> | clusters / applications | 예 | 
> | containergroups | 예 | 
> | containergroupsets | 예 | 
> | edgeclusters | 예 | 
> | networks | 예 | 
> | secretstores | 예 | 
> | volumes | 예 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  예 | 
> | containergroups | 예 | 
> | gateways |  예 | 
> | networks |  예 | 
> | secrets |  예 | 
> | volumes |  예 |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | signalr |  예 |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appliancedefinitions | 아니요 | 
> | appliances | 아니요 | 
> | applicationdefinitions | 예 | 
> | 애플리케이션 | 예 | 
> | jitrequests | 예 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | instancepools | 예 | 
> | managedinstances | 예 | 
> | managedinstances / databases | 예 | 
> | servers | 예 | 
> | servers / databases | 예 | 
> | servers / elasticpools | 예 | 
> | virtualclusters | 예 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  예 |  
> | sqlvirtualmachines |  아니요 |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dwvm | 예 | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예<br/><br/> [Azure Storage 계정을 다른 지역으로 이동](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | caches | 예 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices |  예 | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 예 | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 예 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managers | 예 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | streamingjobs |  아니요 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments | 예 | 
> | environments / eventsources | 예 | 
> | 인스턴스 | 예 | 
> | instances / environments | 예 | 
> | instances / environments / eventsources | 예 | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | providerregistrations | 예 | 
> | 리소스 | 예 | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments |  예 | 
> | environments / eventsources |  예 |  
> | environments / referencedatasets |  예 | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | stores | 예 | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | imagetemplates | 예 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | account |  예 | 
> | account / extension |  예 | 
> | account / project |  예 | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 예 | 
> | dedicatedcloudservices | 예 | 
> | virtualmachines | 예 | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificates | 예 | 
> | connectiongateways |  예 |  
> | connections |  아니요 |  
> | customapis |  예 | 
> | hostingenvironments | 예 | 
> | serverfarms |  예 |  
> | sites |  예 | 
> | sites / premieraddons |  아니요 |  
> | sites / slots |  예 |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | deviceservices | 예 | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgroups | 예 | 
> | hostpools | 예 | 
> | workspaces | 예 | 

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.
