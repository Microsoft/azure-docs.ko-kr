---
title: 지역 간 Azure 리소스 이동에 대 한 지원
description: Azure 지역 간에 이동할 수 있는 Azure 리소스 종류를 나열 합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760711"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>지역 간 Azure 리소스 이동에 대 한 지원

이 문서에서는 Azure 리소스 종류가 다른 Azure 지역으로 이동 하는 데 지원 되는지 여부를 확인 합니다. 

리소스 공급자 네임 스페이스로 이동 합니다.
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
> - [Microsoft AzureData](#microsoftazuredata)
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
> - [Microsoft CustomProviders](#microsoftcustomproviders)
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
> - [DataShare](#microsoftdatashare)
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
> - [Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
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
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | domainservices | 아닙니다. | 
> | domainservices/replicasets | 아닙니다. | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | tenants | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | actionrules | 아닙니다. | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서버 | 아닙니다. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서비스 |  예 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | configurationstores | 아닙니다. | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | apiapps | 아닙니다. | 
> | appidentities | 아닙니다. | 
> | gateways | 아닙니다. | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | policyassignments | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | automationaccounts | 아닙니다. | 
> | automationaccounts/구성 | 아닙니다. | 
> | automationaccounts/runbook | 아닙니다. | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | b2cdirectories | 아닙니다. | 

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlserverregistrations | 아닙니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registrations | 아닙니다. | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | batchaccounts | 아닙니다. |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 아닙니다. | 
> | fileservers | 아닙니다. | 
> | jobs | 아닙니다. | 
> | workspaces | 아닙니다. | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mapapis | 아닙니다. | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | BizTalk | 아닙니다. | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blockchainmembers | 아닙니다. |
> | 감시자 | 아닙니다. | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blueprintassignments | 아닙니다. | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | botservices | 아닙니다. | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | redis | 아닙니다. | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 아닙니다. |
> | 프로필 | 아닙니다. | 
> | 프로필/엔드포인트 | 아닙니다. | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificateorders | 아닙니다. | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domainnames | 아닙니다. |  
> | virtualmachines | 아닙니다. | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | networksecuritygroups | 아닙니다. |
> | reservedips | 아닙니다. | 
> | virtualnetworks | 아닙니다. | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예 |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availabilitysets | 아닙니다. | 
> | disk; 집합 | 아닙니다. | 
> | disks | 아닙니다. | 
> | galleries | 아닙니다. | 
> | 갤러리/이미지 | 아닙니다. | 
> | 갤러리/이미지/버전 | 아닙니다. | 
> | hostgroups | 아닙니다. | 
> | 호스트 그룹/호스트 | 아닙니다. | 
> | images | 아닙니다. | 
> | proximityplacementgroups | 아닙니다. | 
> | restorepointcollections | 아닙니다. | 
> | sharedvmimages | 아닙니다. | 
> | sharedvmimages/버전 | 아닙니다. | 
> | 스냅샷 | 아닙니다. | 
> | virtualmachines | 예 | 
> | virtualmachines/확장 | 아닙니다. | 
> | virtualmachinescalesets | 아닙니다. | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 아닙니다. | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 아닙니다. | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registries | 아닙니다. |  
> | 레지스트리/buildtasks | 아닙니다. |  
> | 레지스트리/복제 | 아닙니다. | 
> | 레지스트리/작업 | 아닙니다. |  
> | 레지스트리/웹 후크 | 아닙니다. | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containerservices | 아닙니다. | 
> | managedclusters | 아닙니다. | 
> | openshiftmanagedclusters | 아닙니다. | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 아닙니다. | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 커넥터 | 아닙니다. |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hubs | 아닙니다. |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | resourceproviders | 아닙니다. | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs | 아닙니다. | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databoxedgedevices | 아닙니다. | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 아닙니다. | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | catalogs | 아닙니다. | 
> | datacatalogs | 아닙니다. | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | connectionmanagers | 아닙니다. | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 패키지 | 아닙니다. | 
> | 플랜 | 아닙니다. | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datafactories | 아닙니다. | 
> | factories | 아닙니다. |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datalakeaccounts | 아닙니다. | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 아닙니다. | 
> | 서비스/프로젝트 | 아닙니다. | 
> | slots | 아닙니다. | 

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서버 | 아닙니다. |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서버 | 아닙니다. |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servergroups | 아닙니다. | 
> | 서버 | 아닙니다. |  
> | serversv2 | 아닙니다. | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | artifactsources | 아닙니다. | 
> | rollouts | 아닙니다. |  
> | servicetopologies | 아닙니다. | 
> | servicetopologies/서비스 | 아닙니다. |  
> | servicetopologies/서비스/serviceunits | 아닙니다. | 
> | 단계 | 아닙니다. | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | elasticpools | 아닙니다. | 
> | elasticpools / iothubtenants | 아닙니다. | 
> | iothubs | 예 | 
> | provisioningservices | 아닙니다. | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | controllers | 아닙니다. | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labcenters | 아닙니다. | 
> | labs | 아닙니다. | 
> | 랩/환경 | 아닙니다. |  
> | labs/servicerunners | 아닙니다. | 
> | labs/virtualmachines | 아닙니다. |  
> | schedules | 아닙니다. |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databaseaccounts | 아닙니다. | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 아닙니다. | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 아닙니다. |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 아닙니다. |  
> | topics | 아닙니다. | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 아닙니다. |  
> | 네임스페이스 | 아닙니다. | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hanainstances | 아닙니다. | 
> | sapmonitors | 아닙니다. |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 아닙니다. | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 아닙니다. |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 머신의 | 아닙니다. | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datamanagers |  아닙니다. | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs |  아닙니다. | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 
> | actiongroups |  아닙니다. | 
> | activitylogalerts | 아닙니다. | 
> | alertrules |  아닙니다. | 
> | autoscalesettings |  아닙니다. | 
> | components |  아닙니다. |  
> | guestdiagnosticsettings | 아닙니다. | 
> | metricalerts | 아닙니다. | 
> | notificationgroups | 아닙니다. | 
> | notificationrules | 아닙니다. | 
> | scheduledqueryrules |  아닙니다. | 
> | webtests |  아닙니다. | 
> | workbooks |  아닙니다. |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iotapps |  아닙니다. |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | checknameavailability |  아닙니다. |  
> | graph |  아닙니다. | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hsmpools | 아닙니다. | 
> | vaults |  아닙니다. | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters |  아닙니다. |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labaccounts | 아닙니다. | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingenvironments | 아닙니다. | 
> | integrationaccounts |  아닙니다. |  
> | integrationserviceenvironments | 아닙니다. | 
> | isolatedenvironments | 아닙니다. | 
> | workflows |  아닙니다. |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | commitmentplans |  아닙니다. | 
> | webservices |  아닙니다. | 
> | workspaces |  아닙니다. | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | operationalizationclusters |  아닙니다. | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 
> | 계정/작업 영역 | 아닙니다. | 
> | 계정/작업 영역/프로젝트 | 아닙니다. | 
> | teamaccounts | 아닙니다. | 
> | teamaccounts/작업 영역 | 아닙니다. | 
> | teamaccounts/workspace/projects | 아닙니다. | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingaccounts | 아닙니다. | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 아닙니다. | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | userassignedidentities | 아닙니다. | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 |  아닙니다. |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | classicdevservices | 아닙니다. | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mediaservices |  아닙니다. | 
> | windowsazure.mediaservices/liveevents |  아닙니다. | 
> | windowsazure.mediaservices/streamingendpoints |  아닙니다. | 

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appclusters | 아닙니다. | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | assessmentprojects | 아닙니다. | 
> | migrateprojects | 아닙니다. | 
> | projects | 아닙니다. | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | netappaccounts | 아닙니다. | 
> | netappaccounts/capacitypools | 아닙니다. | 
> | netappaccounts/capacitypools/볼륨 | 아닙니다. | 
> | netappaccounts/capacitypools/볼륨/mounttargets | 아닙니다. | 
> | netappaccounts/capacitypools/볼륨/스냅숏 | 아닙니다. | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgateways | 아닙니다. | 
> | applicationgatewaywebapplicationfirewallpolicies | 아닙니다. | 
> | applicationsecuritygroups |  아닙니다. |  
> | azurefirewalls |  아닙니다. |  
> | bastionhosts | 아닙니다. | 
> | connections |  아닙니다. | 
> | ddoscustompolicies |  아닙니다. | 
> | ddosprotectionplans | 아닙니다. | 
> | dnszones |  아닙니다. | 
> | expressroutecircuits | 아닙니다. | 
> | expressroutecrossconnections | 아닙니다. | 
> | expressroutegateways | 아닙니다. | 
> | expressrouteports | 아닙니다. | 
> | frontdoors | 아닙니다. | 
> | frontdoorwebapplicationfirewallpolicies | 아닙니다. | 
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br> -예 표준 SKU |
> | localnetworkgateways |  아닙니다. | 
> | natgateways |  아닙니다. | 
> | networkintentpolicies |  아닙니다. | 
> | networkinterfaces | 예 | 
> | networkprofiles | 아닙니다. | 
> | networksecuritygroups | 예 | 
> | networkwatchers |  아닙니다. |  
> | networkwatchers/connectionmonitors |  아닙니다. | 
> | networkwatchers/lenses |  아닙니다. | 
> | networkwatchers/ |  아닙니다. | 
> | p2svpngateways | 아닙니다. | 
> | privatednszones |  아닙니다. |  
> | privatednszones / virtualnetworklinks |  아닙니다. |  
> | privateendpoints | 아닙니다. | 
> | privatelinkservices | 아닙니다. | 
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | 아닙니다. | 
> | routefilters | 아닙니다. | 
> | routetables |  아닙니다. | 
> | serviceendpointpolicies |  아닙니다. | 
> | trafficmanagerprofiles |  아닙니다. | 
> | virtualhubs | 아닙니다. | 
> | virtualnetworkgateways |  아닙니다. |  
> | virtualnetworks |  아닙니다. | 
> | virtualnetworktaps | 아닙니다. | 
> | virtualwans | 아닙니다. | 
> | vpngateways (가상 WAN) | 아닙니다. | 
> | vpnsites (가상 WAN) | 아닙니다. | 
> | webapplicationfirewallpolicies |  아닙니다. | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아닙니다. | 
> | 네임 스페이스/notificationhubs |  아닙니다. |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces |  아닙니다. | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managementconfigurations |  아닙니다. | 
> | 뷰 |  아닙니다. | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 피어 링 | 아닙니다. | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dashboards | 아닙니다. | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | rootresources | 아닙니다. | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspacecollections |  아닙니다. | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | capacities |  아닙니다. | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아닙니다. | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | vaults | 아닙니다. [자격 증명 모음을 사용 하지 않도록 설정 하 고 Site Recovery 다시 만들기](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions)  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아닙니다. | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 쿼리 |  아닙니다. |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  아닙니다. | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | flows |  아닙니다. |  
> | jobcollections |  아닙니다. | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | searchservices |  아닙니다. | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  아닙니다. | 
> | playbookconfigurations | 아닙니다. | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | gateways | 아닙니다. | 
> | 노드 | 아닙니다. | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아닙니다. | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 아닙니다. | 
> | clusters |  아닙니다. | 
> | 클러스터/응용 프로그램 | 아닙니다. | 
> | containergroups | 아닙니다. | 
> | containergroupsets | 아닙니다. | 
> | edgeclusters | 아닙니다. | 
> | networks | 아닙니다. | 
> | secretstores | 아닙니다. | 
> | volumes | 아닙니다. | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  아닙니다. | 
> | containergroups | 아닙니다. | 
> | gateways |  아닙니다. | 
> | networks |  아닙니다. | 
> | secrets |  아닙니다. | 
> | volumes |  아닙니다. |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | signalr |  아닙니다. |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appliancedefinitions | 아닙니다. | 
> | appliances | 아닙니다. | 
> | applicationdefinitions | 아닙니다. | 
> | 애플리케이션 | 아닙니다. | 
> | jitrequests | 아닙니다. | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | instancepools | 아닙니다. | 
> | managedinstances | 예 | 
> | managedinstances/데이터베이스 | 예 | 
> | 서버 | 예 | 
> | 서버/데이터베이스 | 예 | 
> | servers/elasticpools | 예 | 
> | virtualclusters | 예 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  아닙니다. |  
> | sqlvirtualmachines |  아닙니다. |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dwvm | 아닙니다. | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예 | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 캐시 | 아닙니다. | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices |  아닙니다. | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 아닙니다. | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 아닙니다. | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managers | 아닙니다. | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | streamingjobs |  아닙니다. |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments | 아닙니다. | 
> | 환경/s s o 원본 | 아닙니다. | 
> | 인스턴스 | 아닙니다. | 
> | 인스턴스/환경 | 아닙니다. | 
> | 인스턴스/환경/s s o 원본 | 아닙니다. | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | providerregistrations | 아닙니다. | 
> | 리소스 | 아닙니다. | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments |  아닙니다. | 
> | 환경/s s o 원본 |  아닙니다. |  
> | environment/referencedatasets |  아닙니다. | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 정보가 | 아닙니다. | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | imagetemplates | 아닙니다. | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | account |  아닙니다. | 
> | 계정/확장 |  아닙니다. | 
> | 계정/프로젝트 |  아닙니다. | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 아닙니다. | 
> | dedicatedcloudservices | 아닙니다. | 
> | virtualmachines | 아닙니다. | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificates | 아닙니다. | 
> | connectiongateways |  아닙니다. |  
> | connections |  아닙니다. |  
> | customapis |  아닙니다. | 
> | hostingenvironments | 아닙니다. | 
> | serverfarms |  아닙니다. |  
> | sites |  아닙니다. | 
> | 사이트/premieraddons |  아닙니다. |  
> | 사이트/슬롯 |  아닙니다. |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | deviceservices | 아닙니다. | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgroups | 아닙니다. | 
> | hostpools | 아닙니다. | 
> | workspaces | 아닙니다. | 

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.
