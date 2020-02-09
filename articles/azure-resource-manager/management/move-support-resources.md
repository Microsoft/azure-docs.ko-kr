---
title: 리소스 유형별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 01/22/2020
ms.openlocfilehash: 3fe404b8b501056f75ac45ccebc5bdbed1acbe43
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705482"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해 야 할 특수 조건에 대 한 정보를 제공 합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft 청구](#microsoftbilling)
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
> - [Microsoft 소비량](#microsoftconsumption)
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
> - [Microsoft DataProtection](#microsoftdataprotection)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
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
> - [Kubernetes](#microsoftkubernetes)
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
> - [Microsoft ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
> - [Microsoft ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 서비스](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft 구독](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | 아닙니다. | 아닙니다. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | 아닙니다. | 아닙니다. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 아닙니다. | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. | 아닙니다. |
> | suppressions | 아닙니다. | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | 예 | 예 |
> | 경고 | 아닙니다. | 아닙니다. |
> | alertssummary | 아닙니다. | 아닙니다. |
> | smartdetectoralertrules | 예 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 서버 | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 서비스 | 예 | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | 예 | 예 |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 스프링 | 예 | 예 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 아닙니다. | 아닙니다. |
> | appidentities | 아닙니다. | 아닙니다. |
> | gateways | 아닙니다. | 아닙니다. |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | 아닙니다. | 아닙니다. |
> | denyassignments | 아닙니다. | 아닙니다. |
> | findorphanroleassignments | 아닙니다. | 아닙니다. |
> | locks | 아닙니다. | 아닙니다. |
> | 권한 | 아닙니다. | 아닙니다. |
> | policyassignments | 아닙니다. | 아닙니다. |
> | policydefinitions | 아닙니다. | 아닙니다. |
> | policysetdefinitions | 아닙니다. | 아닙니다. |
> | roleassignments | 아닙니다. | 아닙니다. |
> | roleassignmentsusagemetrics | 아닙니다. | 아닙니다. |
> | roledefinitions | 아닙니다. | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 예 | 예 |
> | automationaccounts/구성 | 예 | 예 |
> | automationaccounts/runbook | 예 | 예 |

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 예 | 예 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | 아닙니다. | 아닙니다. |
> | postgresinstances | 아닙니다. | 아닙니다. |
> | sqlbigdataclusters | 아닙니다. | 아닙니다. |
> | sqlinstances | 아닙니다. | 아닙니다. |
> | sqlserverregistrations | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | 예 | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 예 | 예 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아닙니다. | 아닙니다. |
> | fileservers | 아닙니다. | 아닙니다. |
> | jobs | 아닙니다. | 아닙니다. |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | billingperiods | 아닙니다. | 아닙니다. |
> | billingpermissions | 아닙니다. | 아닙니다. |
> | billingroleassignments | 아닙니다. | 아닙니다. |
> | billingroledefinitions | 아닙니다. | 아닙니다. |
> | createbillingroleassignment | 아닙니다. | 아닙니다. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | 아닙니다. | 아닙니다. |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | BizTalk | 아닙니다. | 아닙니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 아닙니다. | 아닙니다. |
> | 감시자 | 아닙니다. | 아닙니다. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 아닙니다. | 아닙니다. |
> | blueprints | 아닙니다. | 아닙니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | 예 | 예 |

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용 하 여 구성 된 경우 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한 사항](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | 예 | 예 |
> | 프로필 | 예 | 예 |
> | 프로필/엔드포인트 | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | 예 | 예 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | 예 | 아닙니다. |
> | virtualmachines | 예 | 아닙니다. |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 아닙니다. | 아닙니다. |
> | reservedips | 아닙니다. | 아닙니다. |
> | virtualnetworks | 아닙니다. | 아닙니다. |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 아닙니다. |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 예 | 예 |
> | disk; 집합 | 아닙니다. | 아닙니다. |
> | disks | 예 | 예 |
> | galleries | 아닙니다. | 아닙니다. |
> | 갤러리/이미지 | 아닙니다. | 아닙니다. |
> | 갤러리/이미지/버전 | 아닙니다. | 아닙니다. |
> | hostgroups | 아닙니다. | 아닙니다. |
> | 호스트 그룹/호스트 | 아닙니다. | 아닙니다. |
> | images | 예 | 예 |
> | proximityplacementgroups | 아닙니다. | 아닙니다. |
> | restorepointcollections | 아닙니다. | 아닙니다. |
> | sharedvmimages | 아닙니다. | 아닙니다. |
> | sharedvmimages/버전 | 아닙니다. | 아닙니다. |
> | 스냅샷 | 예 | 예 |
> | virtualmachines | 예 | 예 |
> | virtualmachines/확장 | 예 | 예 |
> | virtualmachinescalesets | 예 | 예 |

> [!IMPORTANT]
> [Virtual Machines 이동 가이드](./move-limitations/virtual-machines-move-limitations.md)를 참조 하세요.

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 아닙니다. | 아닙니다. |
> | 잔고 | 아닙니다. | 아닙니다. |
> | 예산의 | 아닙니다. | 아닙니다. |
> | 요금 | 아닙니다. | 아닙니다. |
> | costtags | 아닙니다. | 아닙니다. |
> | credits | 아닙니다. | 아닙니다. |
> | events | 아닙니다. | 아닙니다. |
> | 예측 | 아닙니다. | 아닙니다. |
> | lots | 아닙니다. | 아닙니다. |
> | 마켓플레이스 | 아닙니다. | 아닙니다. |
> | operationresults | 아닙니다. | 아닙니다. |
> | operationstatus | 아닙니다. | 아닙니다. |
> | pricesheets | 아닙니다. | 아닙니다. |
> | products | 아닙니다. | 아닙니다. |
> | reservationdetails | 아닙니다. | 아닙니다. |
> | reservationrecommendations | 아닙니다. | 아닙니다. |
> | reservationsummaries | 아닙니다. | 아닙니다. |
> | reservationtransactions | 아닙니다. | 아닙니다. |
> | tags | 아닙니다. | 아닙니다. |
> | tenants | 아닙니다. | 아닙니다. |
> | terms | 아닙니다. | 아닙니다. |
> | 사용량 세부 정보 | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아닙니다. | 아닙니다. |
> | serviceassociationlinks | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | 예 | 예 |
> | 레지스트리/buildtasks | 예 | 예 |
> | 레지스트리/복제 | 예 | 예 |
> | 레지스트리/taskruns | 예 | 예 |
> | 레지스트리/작업 | 예 | 예 |
> | 레지스트리/웹 후크 | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 아닙니다. | 아닙니다. |
> | managedclusters | 아닙니다. | 아닙니다. |
> | openshiftmanagedclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아닙니다. | 아닙니다. |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 아닙니다. | 아닙니다. |
> | 예산의 | 아닙니다. | 아닙니다. |
> | 커넥터 | 예 | 예 |
> | 차원 | 아닙니다. | 아닙니다. |
> | 내보내기 | 아닙니다. | 아닙니다. |
> | externalsubscriptions | 아닙니다. | 아닙니다. |
> | forecast | 아닙니다. | 아닙니다. |
> | Query | 아닙니다. | 아닙니다. |
> | reportconfigs | 아닙니다. | 아닙니다. |
> | reports | 아닙니다. | 아닙니다. |
> | showbackrules | 아닙니다. | 아닙니다. |
> | 뷰 | 아닙니다. | 아닙니다. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 아닙니다. | 아닙니다. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 연결 | 아닙니다. | 아닙니다. |
> | resourceproviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 아닙니다. | 아닙니다. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 아닙니다. | 아닙니다. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | 예 | 예 |
> | datacatalogs | 아닙니다. | 아닙니다. |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 아닙니다. | 아닙니다. |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 아닙니다. | 아닙니다. |
> | 플랜 | 아닙니다. | 아닙니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | 예 | 예 |
> | factories | 예 | 예 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 아닙니다. | 아닙니다. |
> | 서비스/프로젝트 | 아닙니다. | 아닙니다. |
> | slots | 아닙니다. | 아닙니다. |

## <a name="microsoftdataprotection"></a>Microsoft DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | 아닙니다. | 아닙니다. |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 서버 | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 서버 | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | 아닙니다. | 아닙니다. |
> | 서버 | 예 | 예 |
> | serversv2 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | 예 | 예 |
> | rollouts | 예 | 예 |
> | servicetopologies | 예 | 예 |
> | servicetopologies/서비스 | 예 | 예 |
> | servicetopologies/서비스/serviceunits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | 아닙니다. | 아닙니다. |
> | elasticpools / iothubtenants | 아닙니다. | 아닙니다. |
> | iothubs | 예 | 예 |
> | provisioningservices | 예 | 예 |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 파이프라인 | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 아닙니다. | 아닙니다. |
> | labs | 예 | 아닙니다. |
> | 랩/환경 | 예 | 예 |
> | labs/servicerunners | 예 | 예 |
> | labs/virtualmachines | 예 | 아닙니다. |
> | schedules | 예 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |
> | eventSubscriptions | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. |
> | eventsubscriptions | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. |
> | extensiontopics | 아닙니다. | 아닙니다. |
> | topics | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | 아닙니다. | 아닙니다. |
> | software | 아닙니다. | 아닙니다. |
> | softwareupdateprofile | 아닙니다. | 아닙니다. |
> | 업데이트 | 아닙니다. | 아닙니다. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 아닙니다. | 아닙니다. |
> | sapmonitors | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 머신의 | 예 | 예 |
> | 컴퓨터/확장 | 아닙니다. | 아닙니다. |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | 예 | 예 |
> | activitylogalerts | 아닙니다. | 아닙니다. |
> | alertrules | 예 | 예 |
> | automatedexportsettings | 아닙니다. | 아닙니다. |
> | autoscalesettings | 예 | 예 |
> | baseline | 아닙니다. | 아닙니다. |
> | calculatebaseline | 아닙니다. | 아닙니다. |
> | components | 예 | 예 |
> | diagnosticsettings | 아닙니다. | 아닙니다. |
> | diagnosticsettingscategories | 아닙니다. | 아닙니다. |
> | eventtypes | 아닙니다. | 아닙니다. |
> | extendeddiagnosticsettings | 아닙니다. | 아닙니다. |
> | logdefinitions | 아닙니다. | 아닙니다. |
> | 로그 | 아닙니다. | 아닙니다. |
> | metricalerts | 아닙니다. | 아닙니다. |
> | metricbaselines | 아닙니다. | 아닙니다. |
> | metricdefinitions | 아닙니다. | 아닙니다. |
> | metricnamespaces | 아닙니다. | 아닙니다. |
> | 메트릭 | 아닙니다. | 아닙니다. |
> | myworkbooks 문서 | 아닙니다. | 아닙니다. |
> | scheduledqueryrules | 예 | 예 |
> | 토폴로지 | 아닙니다. | 아닙니다. |
> | 트랜잭션 | 아닙니다. | 아닙니다. |
> | vminsightsonboardingstatuses | 아닙니다. | 아닙니다. |
> | webtests | 예 | 예 |
> | workbooks | 예 | 예 |
> | 통합 문서의 템플릿 | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | graph | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> 디스크 암호화에 사용 되는 키 자격 증명 모음은 동일한 구독의 리소스 그룹 또는 구독 간에 이동할 수 없습니다.

## <a name="microsoftkubernetes"></a>Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 아닙니다. | 아닙니다. |
> | integrationaccounts | 예 | 예 |
> | integrationserviceenvironments | 예 | 아닙니다. |
> | integrationserviceenvironments/연결은 | 예 | 아닙니다. |
> | isolatedenvironments | 아닙니다. | 아닙니다. |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 예 | 예 |
> | webservices | 예 | 아닙니다. |
> | workspaces | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |
> | 계정/작업 영역 | 아닙니다. | 아닙니다. |
> | 계정/작업 영역/프로젝트 | 아닙니다. | 아닙니다. |
> | teamaccounts | 아닙니다. | 아닙니다. |
> | teamaccounts/작업 영역 | 아닙니다. | 아닙니다. |
> | teamaccounts/workspace/projects | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 아닙니다. | 아닙니다. |
> | userassignedidentities | 아닙니다. | 아닙니다. |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | registrationassignments | 아닙니다. | 아닙니다. |
> | registrationdefinitions | 아닙니다. | 아닙니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아닙니다. | 아닙니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | 예 | 예 |
> | windowsazure.mediaservices/liveevents | 예 | 예 |
> | windowsazure.mediaservices/streamingendpoints | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | projects | 아닙니다. | 아닙니다. |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 아닙니다. | 아닙니다. |
> | netappaccounts/backuppolicies | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools/볼륨 | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools/볼륨/mounttargets | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools/볼륨/스냅숏 | 아닙니다. | 아닙니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 아닙니다. | 아닙니다. |
> | applicationgatewaywebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
> | applicationsecuritygroups | 예 | 예 |
> | azurefirewalls | 예 | 예 |
> | bastionhosts | 아닙니다. | 아닙니다. |
> | connections | 예 | 예 |
> | ddoscustompolicies | 예 | 예 |
> | ddosprotectionplans | 아닙니다. | 아닙니다. |
> | dnszones | 예 | 예 |
> | expressroutecircuits | 아닙니다. | 아닙니다. |
> | expressroutegateways | 아닙니다. | 아닙니다. |
> | frontdoors | 아닙니다. | 아닙니다. |
> | frontdoorwebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | localnetworkgateways | 예 | 예 |
> | networkexperimentprofiles | 예 | 예 |
> | networkintentpolicies | 예 | 예 |
> | networkinterfaces | 예 | 예 |
> | networkprofiles | 아닙니다. | 아닙니다. |
> | networksecuritygroups | 예 | 예 |
> | networkwatchers | 예 | 예 |
> | networkwatchers/connectionmonitors | 예 | 예 |
> | networkwatchers/flowlogs | 예 | 예 |
> | networkwatchers/lenses | 예 | 예 |
> | networkwatchers/ | 예 | 예 |
> | p2svpngateways | 아닙니다. | 아닙니다. |
> | privatednszones | 예 | 예 |
> | privatednszones / virtualnetworklinks | 예 | 예 |
> | privateendpointredirectmaps | 아닙니다. | 아닙니다. |
> | privateendpoints | 아닙니다. | 아닙니다. |
> | privatelinkservices | 아닙니다. | 아닙니다. |
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | 예 | 예 |
> | routefilters | 아닙니다. | 아닙니다. |
> | routetables | 예 | 예 |
> | serviceendpointpolicies | 예 | 예 |
> | trafficmanagerprofiles | 예 | 예 |
> | virtualhubs | 아닙니다. | 아닙니다. |
> | virtualnetworkgateways | 예 | 예 |
> | virtualnetworks | 예 | 예 |
> | virtualnetworktaps | 아닙니다. | 아닙니다. |
> | virtualrouters | 예 | 예 |
> | virtualwans | 아닙니다. | 아닙니다. |
> | vpngateways (가상 WAN) | 아닙니다. | 아닙니다. |
> | vpnserverconfigurations | 아닙니다. | 아닙니다. |
> | vpnsites (가상 WAN) | 아닙니다. | 아닙니다. |
> | webapplicationfirewallpolicies | 예 | 예 |

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |
> | 네임 스페이스/notificationhubs | 예 | 예 |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces 스페이스 | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | 아닙니다. | 아닙니다. |
> | workspaces | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 아닙니다. | 아닙니다. |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 피어 링 | 예 | 예 |
> | peeringservices | 아닙니다. | 아닙니다. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | 아닙니다. | 아닙니다. |
> | policystates | 아닙니다. | 아닙니다. |
> | policytrackedresources | 아닙니다. | 아닙니다. |
> | remediations | 아닙니다. | 아닙니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | 예 | 예 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | 아닙니다. | 아닙니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | 예 | 예 |

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftproviderhub"></a>Microsoft ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 아닙니다. | 아닙니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 아닙니다. | 아닙니다. |
> | replicationeligibilityresults | 아닙니다. | 아닙니다. |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> [Recovery Services 이동 가이드](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)를 참조 하세요.

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 쿼리 | 예 | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | 아닙니다. | 아닙니다. |
> | childavailabilitystatuses | 아닙니다. | 아닙니다. |
> | childresources | 아닙니다. | 아닙니다. |
> | events | 아닙니다. | 아닙니다. |
> | 알림 | 아닙니다. | 아닙니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | 아닙니다. | 아닙니다. |
> | 링크 | 아닙니다. | 아닙니다. |
> | tags | 아닙니다. | 아닙니다. |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아닙니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | jobcollections | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | 예 | 예 |

> [!IMPORTANT]
> 한 번의 작업으로 다른 지역에 있는 여러 검색 리소스를 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 아닙니다. | 아닙니다. |
> | advancedthreatprotectionsettings | 아닙니다. | 아닙니다. |
> | assessmentmetadata | 아닙니다. | 아닙니다. |
> | 평가 | 아닙니다. | 아닙니다. |
> | 자동화 | 예 | 예 |
> | complianceresults | 아닙니다. | 아닙니다. |
> | 규격 | 아닙니다. | 아닙니다. |
> | datacollectionagents | 아닙니다. | 아닙니다. |
> | datacollectionresults | 아닙니다. | 아닙니다. |
> | devicesecuritygroups | 아닙니다. | 아닙니다. |
> | informationprotectionpolicies | 아닙니다. | 아닙니다. |
> | iotsecuritysolutions | 예 | 예 |
> | servervulnerabilityassessments | 아닙니다. | 아닙니다. |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 아닙니다. | 아닙니다. |
> | alertrules | 아닙니다. | 아닙니다. |
> | alertruletemplates | 아닙니다. | 아닙니다. |
> | 책갈피 | 아닙니다. | 아닙니다. |
> | cases | 아닙니다. | 아닙니다. |
> | dataconnectors | 아닙니다. | 아닙니다. |
> | 엔터티 | 아닙니다. | 아닙니다. |
> | entityqueries | 아닙니다. | 아닙니다. |
> | officeconsents | 아닙니다. | 아닙니다. |
> | 설정 | 아닙니다. | 아닙니다. |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 아닙니다. | 아닙니다. |
> | 노드 | 아닙니다. | 아닙니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아닙니다. | 아닙니다. |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아닙니다. | 아닙니다. |
> | containergroups | 아닙니다. | 아닙니다. |
> | containergroupsets | 아닙니다. | 아닙니다. |
> | edgeclusters | 아닙니다. | 아닙니다. |
> | networks | 아닙니다. | 아닙니다. |
> | secretstores | 아닙니다. | 아닙니다. |
> | volumes | 아닙니다. | 아닙니다. |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 아닙니다. | 아닙니다. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 아닙니다. | 아닙니다. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 아닙니다. | 아닙니다. |
> | 애플리케이션 | 아닙니다. | 아닙니다. |
> | jitrequests | 아닙니다. | 아닙니다. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | 아닙니다. | 아닙니다. |
> | managedinstances | 아닙니다. | 아닙니다. |
> | managedinstances/데이터베이스 | 아닙니다. | 아닙니다. |
> | 서버 | 예 | 예 |
> | 서버/데이터베이스 | 예 | 예 |
> | servers/elasticpools | 예 | 예 |
> | virtualclusters | 예 | 예 |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 예 | 예 |
> | sqlvirtualmachines | 예 | 예 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | 아닙니다. | 아닙니다. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아닙니다. | 아닙니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 아닙니다. | 아닙니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | 예 | 예 |

> [!IMPORTANT]
> Stream Analytics 작업은 실행 중 상태일 때 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 아닙니다. | 아닙니다. |
> | 환경/s s o 원본 | 아닙니다. | 아닙니다. |
> | 인스턴스 | 아닙니다. | 아닙니다. |
> | 인스턴스/환경 | 아닙니다. | 아닙니다. |
> | 인스턴스/환경/s s o 원본 | 아닙니다. | 아닙니다. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | createsubscription | 아닙니다. | 아닙니다. |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | createsupportticket | 아닙니다. | 아닙니다. |
> | supporttickets | 아닙니다. | 아닙니다. |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 아닙니다. | 아닙니다. |
> | 리소스 | 아닙니다. | 아닙니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | 환경/s s o 원본 | 예 | 예 |
> | environment/referencedatasets | 예 | 예 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | 예 | 예 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 아닙니다. | 아닙니다. |
> | 계정/확장 | 아닙니다. | 아닙니다. |
> | 계정/프로젝트 | 아닙니다. | 아닙니다. |

> [!IMPORTANT]
> Azure DevOps에 대 한 구독을 변경 하려면 [청구에 사용 되는 azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 아닙니다. | 아닙니다. |
> | dedicatedcloudservices | 아닙니다. | 아닙니다. |
> | virtualmachines | 아닙니다. | 아닙니다. |

## <a name="microsoftvsonline"></a>Microsoft VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | 플랜 | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | 아닙니다. | 예 |
> | connectiongateways | 예 | 예 |
> | connections | 예 | 예 |
> | customapis | 예 | 예 |
> | hostingenvironments | 아닙니다. | 아닙니다. |
> | serverfarms | 예 | 예 |
> | sites | 예 | 예 |
> | 사이트/premieraddons | 예 | 예 |
> | 사이트/슬롯 | 예 | 예 |
> | staticsites | 아닙니다. | 아닙니다. |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 아닙니다. | 아닙니다. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 리소스 그룹 | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아닙니다. | 아닙니다. |
> | monitorinstances | 아닙니다. | 아닙니다. |
> | monitors | 아닙니다. | 아닙니다. |
> | notificationsettings | 아닙니다. | 아닙니다. |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
