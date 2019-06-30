---
title: Azure 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 6/6/2019
ms.author: tomfitz
ms.openlocfilehash: 9ab8fbd8fa0453ca6c89f3e7ad91bea95b0b9096
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331994"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 이동 작업을 지원하는 리소스 종류라도 해당 리소스를 이동할 수 없는 조건이 있을 수 있습니다. 이동 작업에 영향을 주는 조건에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.

## <a name="microsoftaad"></a>Microsoft.AAD
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| domainservices | 아닙니다. | 아닙니다. |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| tenants | 아닙니다. | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| actionrules | 예 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 서비스 | 예 | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| configurationstores | 예 | 예 |

## <a name="microsoftappservice"></a>Microsoft.AppService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| apiapps | 아닙니다. | 아닙니다. |
| appidentities | 아닙니다. | 아닙니다. |
| gateways | 아닙니다. | 아닙니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| policyassignments | 아닙니다. | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| automationaccounts | 예 | 예 |
| automationaccounts/configurations | 예 | 예 |
| automationaccounts/runbooks | 예 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| b2cdirectories | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| registrations | 예 | 예 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| backupvault | 아닙니다. | 아닙니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| batchaccounts | 예 | 예 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| clusters | 아닙니다. | 아닙니다. |
| fileservers | 아닙니다. | 아닙니다. |
| jobs | 아닙니다. | 아닙니다. |
| workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| mapapis | 아닙니다. | 아닙니다. |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| biztalk | 예 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| blockchainmembers | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| blueprintassignments | 아닙니다. | 아닙니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| botservices | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| redis | 예 | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 프로필 | 예 | 예 |
| profiles/endpoints | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| certificateorders | 예 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| domainnames | 예 | 아닙니다. |
| virtualmachines | 예 | 아닙니다. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| networksecuritygroups | 아닙니다. | 아닙니다. |
| reservedips | 아닙니다. | 아닙니다. |
| virtualnetworks | 아닙니다. | 아닙니다. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| storageaccounts | 예 | 아닙니다. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| availabilitysets | 예 | 예 |
| disks | 예 | 예 |
| galleries | 아닙니다. | 아닙니다. |
| galleries/images | 아닙니다. | 아닙니다. |
| galleries/images/versions | 아닙니다. | 아닙니다. |
| hostgroups | 아닙니다. | 아닙니다. |
| hostgroups/hosts | 아닙니다. | 아닙니다. |
| images | 예 | 예 |
| proximityplacementgroups | 아닙니다. | 아닙니다. |
| restorepointcollections | 아닙니다. | 아닙니다. |
| sharedvmimages | 아닙니다. | 아닙니다. |
| sharedvmimages/versions | 아닙니다. | 아닙니다. |
| 스냅샷 | 예 | 예 |
| virtualmachines | 예 | 예 |
| virtualmachines/extensions | 예 | 예 |
| virtualmachinescalesets | 예 | 예 |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| containergroups | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| containergroups | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| registries | 예 | 예 |
| registries/buildtasks | 예 | 예 |
| registries/replications | 예 | 예 |
| registries/tasks | 예 | 예 |
| registries/webhooks | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| containerservices | 아닙니다. | 아닙니다. |
| managedclusters | 아닙니다. | 아닙니다. |
| openshiftmanagedclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 애플리케이션 | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 커넥터 | 예 | 예 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| hubs | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| jobs | 아닙니다. | 아닙니다. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| databoxedgedevices | 아닙니다. | 아닙니다. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| catalogs | 예 | 예 |
| datacatalogs | 아닙니다. | 아닙니다. |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| connectionmanagers | 아닙니다. | 아닙니다. |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 패키지 | 아닙니다. | 아닙니다. |
| 플랜 | 아닙니다. | 아닙니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| datafactories | 예 | 예 |
| factories | 예 | 예 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| datalakeaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| services | 아닙니다. | 아닙니다. |
| services/projects | 아닙니다. | 아닙니다. |
| slots | 아닙니다. | 아닙니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| servers | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| servers | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| servergroups | 아닙니다. | 아닙니다. |
| servers | 예 | 예 |
| serversv2 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| artifactsources | 예 | 예 |
| rollouts | 예 | 예 |
| servicetopologies | 예 | 예 |
| servicetopologies/services | 예 | 예 |
| servicetopologies/services/serviceunits | 예 | 예 |
| 단계 | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| elasticpools | 아닙니다. | 아닙니다. |
| elasticpools/iothubtenants | 아닙니다. | 아닙니다. |
| iothubs | 예 | 예 |
| provisioningservices | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| controllers | 아닙니다. | 아닙니다. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| labcenters | 아닙니다. | 아닙니다. |
| labs | 예 | 아닙니다. |
| 랩/환경 | 예 | 예 |
| labs/servicerunners | 예 | 예 |
| labs/virtualmachines | 예 | 아닙니다. |
| schedules | 예 | 예 |

## <a name="microsoftdns"></a>microsoft.dns
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| dnszones | 아닙니다. | 아닙니다. |
| dnszones/a | 아닙니다. | 아닙니다. |
| dnszones/aaaa | 아닙니다. | 아닙니다. |
| dnszones/cname | 아닙니다. | 아닙니다. |
| dnszones/mx | 아닙니다. | 아닙니다. |
| dnszones/ptr | 아닙니다. | 아닙니다. |
| dnszones/srv | 아닙니다. | 아닙니다. |
| dnszones/txt | 아닙니다. | 아닙니다. |
| trafficmanagerprofiles | 아닙니다. | 아닙니다. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| databaseaccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| domains | 예 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| domains | 예 | 예 |
| topics | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| clusters | 예 | 예 |
| namespaces | 예 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 아닙니다. | 아닙니다. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| hanainstances | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| clusters | 예 | 예 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 컴퓨터 | 아닙니다. | 아닙니다. |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| datamanagers | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| jobs | 예 | 예 |

## <a name="microsoftinsights"></a>microsoft.insights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 아닙니다. | 아닙니다. |
| actiongroups | 예 | 예 |
| activitylogalerts | 아닙니다. | 아닙니다. |
| alertrules | 예 | 예 |
| autoscalesettings | 예 | 예 |
| components | 예 | 예 |
| guestdiagnosticsettings | 아닙니다. | 아닙니다. |
| metricalerts | 아닙니다. | 아닙니다. |
| notificationgroups | 아닙니다. | 아닙니다. |
| notificationrules | 아닙니다. | 아닙니다. |
| scheduledqueryrules | 예 | 예 |
| webtests | 예 | 예 |
| workbooks | 예 | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| iotapps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| checknameavailability | 예 | 예 |
| graph | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| hsmpools | 아닙니다. | 아닙니다. |
| vaults | 예 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| clusters | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| labaccounts | 예 | 예 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| hostingenvironments | 아닙니다. | 아닙니다. |
| integrationaccounts | 예 | 예 |
| integrationserviceenvironments | 아닙니다. | 아닙니다. |
| isolatedenvironments | 아닙니다. | 아닙니다. |
| workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| commitmentplans | 예 | 예 |
| webservices | 예 | 아닙니다. |
| workspaces | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| operationalizationclusters | 예 | 예 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 아닙니다. | 아닙니다. |
| accounts/workspaces | 아닙니다. | 아닙니다. |
| accounts/workspaces/projects | 아닙니다. | 아닙니다. |
| teamaccounts | 아닙니다. | 아닙니다. |
| teamaccounts/workspaces | 아닙니다. | 아닙니다. |
| teamaccounts/workspaces/projects | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| hostingaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| userassignedidentities | 아닙니다. | 아닙니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| classicdevservices | 아닙니다. | 아닙니다. |

## <a name="microsoftmedia"></a>Microsoft.Media
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| mediaservices | 예 | 예 |
| mediaservices/liveevents | 예 | 예 |
| mediaservices/streamingendpoints | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| assessmentprojects | 아닙니다. | 아닙니다. |
| migrateprojects | 아닙니다. | 아닙니다. |
| projects | 아닙니다. | 아닙니다. |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| netappaccounts | 아닙니다. | 아닙니다. |
| netappaccounts/capacitypools | 아닙니다. | 아닙니다. |
| netappaccounts/capacitypools/volumes | 아닙니다. | 아닙니다. |
| netappaccounts/capacitypools/volumes/mounttargets | 아닙니다. | 아닙니다. |
| netappaccounts/capacitypools/volumes/snapshots | 아닙니다. | 아닙니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| applicationgateways | 아닙니다. | 아닙니다. |
| applicationgatewaywebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
| applicationsecuritygroups | 예 | 예 |
| azurefirewalls | 예 | 예 |
| bastionhosts | 아닙니다. | 아닙니다. |
| connections | 예 | 예 |
| ddoscustompolicies | 예 | 예 |
| ddosprotectionplans | 아닙니다. | 아닙니다. |
| dnszones | 예 | 예 |
| expressroutecircuits | 아닙니다. | 아닙니다. |
| expressroutecrossconnections | 아닙니다. | 아닙니다. |
| expressroutegateways | 아닙니다. | 아닙니다. |
| expressrouteports | 아닙니다. | 아닙니다. |
| frontdoors | 아닙니다. | 아닙니다. |
| frontdoorwebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
| loadbalancers | 예 | 예 |
| localnetworkgateways | 예 | 예 |
| natgateways | 예 | 예 |
| networkintentpolicies | 예 | 예 |
| networkinterfaces | 예 | 예 |
| networkprofiles | 아닙니다. | 아닙니다. |
| networksecuritygroups | 예 | 예 |
| networkwatchers | 예 | 예 |
| networkwatchers/connectionmonitors | 예 | 예 |
| networkwatchers/lenses | 예 | 예 |
| networkwatchers/pingmeshes | 예 | 예 |
| p2svpngateways | 아닙니다. | 아닙니다. |
| privatednszones | 예 | 예 |
| privatednszones/virtualnetworklinks | 예 | 예 |
| privateendpoints | 아닙니다. | 아닙니다. |
| privatelinkservices | 아닙니다. | 아닙니다. |
| publicipaddresses | 예 | 예 |
| publicipprefixes | 예 | 예 |
| routefilters | 아닙니다. | 아닙니다. |
| routetables | 예 | 예 |
| securegateways | 예 | 예 |
| serviceendpointpolicies | 예 | 예 |
| trafficmanagerprofiles | 예 | 예 |
| virtualhubs | 아닙니다. | 아닙니다. |
| virtualnetworkgateways | 예 | 예 |
| virtualnetworks | 예 | 예 |
| virtualnetworktaps | 아닙니다. | 아닙니다. |
| virtualwans | 아닙니다. | 아닙니다. |
| vpngateways | 아닙니다. | 아닙니다. |
| vpnsites | 아닙니다. | 아닙니다. |
| webapplicationfirewallpolicies | 예 | 예 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| namespaces | 예 | 예 |
| namespaces/notificationhubs | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| workspaces | 예 | 예 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| managementconfigurations | 예 | 예 |
| solutions | 예 | 예 |
| 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 피어 링 | 아닙니다. | 아닙니다. |

## <a name="microsoftportal"></a>Microsoft.Portal
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| dashboards | 예 | 예 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| rootresources | 아닙니다. | 아닙니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| capacities | 예 | 예 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| vaults | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| namespaces | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 애플리케이션 | 예 | 아닙니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| flows | 예 | 예 |
| jobcollections | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| searchservices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | 예 | 예 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| gateways | 아닙니다. | 아닙니다. |
| nodes | 아닙니다. | 아닙니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| namespaces | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 애플리케이션 | 아닙니다. | 아닙니다. |
| clusters | 예 | 예 |
| containergroups | 아닙니다. | 아닙니다. |
| containergroupsets | 아닙니다. | 아닙니다. |
| edgeclusters | 아닙니다. | 아닙니다. |
| networks | 아닙니다. | 아닙니다. |
| secretstores | 아닙니다. | 아닙니다. |
| volumes | 아닙니다. | 아닙니다. |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 애플리케이션 | 예 | 예 |
| containergroups | 아닙니다. | 아닙니다. |
| gateways | 예 | 예 |
| networks | 예 | 예 |
| secrets | 예 | 예 |
| volumes | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| signalr | 예 | 예 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| siterecoveryvault | 아닙니다. | 아닙니다. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| appliancedefinitions | 아닙니다. | 아닙니다. |
| appliances | 아닙니다. | 아닙니다. |
| applicationdefinitions | 아닙니다. | 아닙니다. |
| 애플리케이션 | 아닙니다. | 아닙니다. |
| jitrequests | 아닙니다. | 아닙니다. |

## <a name="microsoftsql"></a>Microsoft.Sql
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| instancepools | 예 | 예 |
| managedinstances | 예 | 예 |
| managedinstances/databases | 예 | 예 |
| servers | 예 | 예 |
| servers/databases | 예 | 예 |
| servers/elasticpools | 예 | 예 |
| virtualclusters | 예 | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | 예 | 예 |
| sqlvirtualmachines | 예 | 예 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| dwvm | 아닙니다. | 아닙니다. |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| storageaccounts | 예 | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 캐시 | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 아닙니다. | 아닙니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| managers | 아닙니다. | 아닙니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| streamingjobs | 예 | 예 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| environments | 아닙니다. | 아닙니다. |
| environments/eventsources | 아닙니다. | 아닙니다. |
| 인스턴스 | 아닙니다. | 아닙니다. |
| instances/environments | 아닙니다. | 아닙니다. |
| instances/environments/eventsources | 아닙니다. | 아닙니다. |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| providerregistrations | 아닙니다. | 아닙니다. |
| 리소스 | 아닙니다. | 아닙니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| environments | 예 | 예 |
| environments/eventsources | 예 | 예 |
| environments/referencedatasets | 예 | 예 |

## <a name="microsofttoken"></a>Microsoft.Token
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 저장소 | 아닙니다. | 아닙니다. |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| imagetemplates | 아닙니다. | 아닙니다. |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| 계정 | 예 | 예 |
| account/extension | 예 | 예 |
| account/project | 예 | 예 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | 예 | 예 |
| dedicatedcloudservices | 예 | 예 |
| virtualmachines | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| certificates | 아닙니다. | 예 |
| connectiongateways | 예 | 예 |
| connections | 예 | 예 |
| customapis | 예 | 예 |
| hostingenvironments | 아닙니다. | 아닙니다. |
| serverfarms | 예 | 예 |
| sites | 예 | 예 |
| sites/premieraddons | 예 | 예 |
| sites/slots | 예 | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| deviceservices | 아닙니다. | 아닙니다. |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | ----------- | ---------- |
| applicationgroups | 아닙니다. | 아닙니다. |
| hostpools | 아닙니다. | 아닙니다. |
| workspaces | 아닙니다. | 아닙니다. |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
