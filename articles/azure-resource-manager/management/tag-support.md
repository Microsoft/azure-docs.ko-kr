---
title: 리소스에 대 한 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 09dd71ef8c3ac4803a988dffbdca47116c967a0e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207930"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](tag-resources.md)를 지원하는지 여부를 설명합니다. **태그 지원** 이라는 열은 리소스 형식에 태그에 대 한 속성이 있는지 여부를 나타냅니다. **비용 보고서에서 태그** 레이블이 지정 된 열은 리소스 종류가 태그를 비용 보고서에 전달 하는지 여부를 나타냅니다. [Cost Management 비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) 및 [Azure 청구 송장 및 일간 사용 현황 데이터](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)에서 태그를 기준으로 비용을 볼 수 있습니다.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) 파일을 다운로드합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [Addons](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft Advisor](#microsoftadvisor)
> - [AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices.sharepoint.integration.dll](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft AppConfiguration](#microsoftappconfiguration)
> - [Microsoft AppPlatform](#microsoftappplatform)
> - [Microsoft 증명](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Azconfig](#microsoftazconfig)
> - [Geneva](#microsoftazuregeneva)
> - [AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft AzureStack](#microsoftazurestack)
> - [Microsoft Batch](#microsoftbatch)
> - [Microsoft 청구](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft Blockchain](#microsoftblockchain)
> - [Microsoft 청사진](#microsoftblueprint)
> - [BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft 용량](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.classiccompute](#microsoftclassiccompute)
> - [ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [ClassicStorage](#microsoftclassicstorage)
> - [Cognitiveservices account](#microsoftcognitiveservices)
> - [Microsoft 상거래](#microsoftcommerce)
> - [Microsoft Compute](#microsoftcompute)
> - [Microsoft 소비량](#microsoftconsumption)
> - [ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [CortanaAnalytics](#microsoftcortanaanalytics)
> - [CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft CustomProviders](#microsoftcustomproviders)
> - [DataBox](#microsoftdatabox)
> - [DataBoxEdge](#microsoftdataboxedge)
> - [Databricks](#microsoftdatabricks)
> - [Microsoft.datacatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.datamigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft DBforMySQL](#microsoftdbformysql)
> - [DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft DevOps](#microsoftdevops)
> - [Microsoft DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. 기능](#microsoftfeatures)
> - [Microsoft 갤러리](#microsoftgallery)
> - [Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [HanaOnAzure](#microsofthanaonazure)
> - [HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [HybridData](#microsofthybriddata)
> - [Microsoft 히드라](#microsofthydra)
> - [Microsoft ImportExport](#microsoftimportexport)
> - [Microsoft Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. 서비스](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [MachineLearning](#microsoftmachinelearning)
> - [MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.managedidentity](#microsoftmanagedidentity)
> - [Microsoft ManagedServices](#microsoftmanagedservices)
> - [Microsoft 관리](#microsoftmanagement)
> - [Microsoft Maps](#microsoftmaps)
> - [Microsoft Marketplace](#microsoftmarketplace)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft 마이그레이션](#microsoftmigrate)
> - [MixedReality](#microsoftmixedreality)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft. 전자 필기장](#microsoftnotebooks)
> - [Microsoft 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 피어 링](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft 포털](#microsoftportal)
> - [Microsoft PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft Relay](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
> - [Microsoft ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 서비스](#microsoftservices)
> - [SignalRService](#microsoftsignalrservice)
> - [SiteRecovery](#microsoftsiterecovery)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft 솔루션](#microsoftsolutions)
> - [SpoolService](#microsoftspoolservice)
> - [Microsoft .SQL](#microsoftsql)
> - [SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Microsoft.storagesync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft StorSimple](#microsoftstorsimple)
> - [Microsoft StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft 구독](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DomainServices | yes | yes |
> | DomainServices/oucontainer | 예 | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 예 | 예 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 예 | 예 |
> | addsservices | 예 | 예 |
> | agents | 예 | 예 |
> | anonymousapiusers | 예 | 예 |
> | 구성 | 예 | 예 |
> | 로그 | 예 | 예 |
> | reports | 예 | 예 |
> | servicehealthmetrics | 예 | 예 |
> | services | 예 | 예 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 | 예 | 예 |
> | generateRecommendations | 예 | 예 |
> | metadata | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | suppressions | 예 | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | yes | yes |
> | 경고 | 예 | 예 |
> | alertsList | 예 | 예 |
> | alertsMetaData | 예 | 예 |
> | alertsSummary | 예 | 예 |
> | alertsSummaryList | 예 | 예 |
> | 사용자 의견 | 예 | 예 |
> | smartDetectorAlertRules | yes | yes |
> | smartDetectorRuntimeEnvironments | 예 | 예 |
> | smartGroups | 예 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | yes | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 예 | 예 |
> | 서비스 | yes | yes |
> | validateServiceName | 예 | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | yes | yes |
> | configurationStores/eventGridFilters | 예 | 예 |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Spring | yes | yes |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 예 | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 예 | 예 |
> | dataAliases | 예 | 예 |
> | denyAssignments | 예 | 예 |
> | elevateAccess | 예 | 예 |
> | findOrphanRoleAssignments | 예 | 예 |
> | locks | 예 | 예 |
> | 권한 | 예 | 예 |
> | policyAssignments | 예 | 예 |
> | policyDefinitions | 예 | 예 |
> | policySetDefinitions | 예 | 예 |
> | providerOperations | 예 | 예 |
> | roleAssignments | 예 | 예 |
> | roleAssignmentsUsageMetrics | 예 | 예 |
> | roleDefinitions | 예 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | yes | yes |
> | automationAccounts/구성 | yes | yes |
> | automationAccounts/job | 예 | 예 |
> | automationAccounts/runbook | yes | yes |
> | automationAccounts/softwareUpdateConfigurations | 예 | 예 |
> | automationAccounts/웹 후크 | 예 | 예 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | yes | yes |
> | configurationStores/eventGridFilters | 예 | 예 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 예 |
> | 환경/계정 | 예 | 예 |
> | 환경/계정/네임 스페이스 | 예 | 예 |
> | 환경/계정/네임 스페이스/구성 | 예 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | yes | 예 |
> | b2ctenants | 예 | 예 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | yes | yes |
> | postgresInstances | yes | yes |
> | sqlBigDataClusters | yes | yes |
> | sqlInstances | yes | yes |
> | sqlServerRegistrations | yes | yes |
> | sqlServerRegistrations/sqlServers | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registrations | yes | yes |
> | 등록/customerSubscriptions | 예 | 예 |
> | 등록/제품 | 예 | 예 |
> | verificationKeys | 예 | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | yes | yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 예 | 예 |
> | billingAccounts/규약 | 예 | 예 |
> | billingAccounts / billingPermissions | 예 | 예 |
> | billingAccounts / billingProfiles | 예 | 예 |
> | billingAccounts / billingProfiles / billingPermissions | 예 | 예 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 예 | 예 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / billingProfiles / billingSubscriptions | 예 | 예 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 예 | 예 |
> | billingAccounts/billingProfiles/고객 | 예 | 예 |
> | billingAccounts/billingProfiles/지침 | 예 | 예 |
> | billingAccounts/billingProfiles/송장 | 예 | 예 |
> | billingAccounts/billingProfiles/청구서/가격표 | 예 | 예 |
> | billingAccounts/billingProfiles/송장/트랜잭션 | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 예 | 예 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 예 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products | 예 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 예 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 예 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 예 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 예 | 예 |
> | billingAccounts / BillingProfiles / patchOperations | 예 | 예 |
> | billingAccounts / billingProfiles / paymentMethods | 예 | 예 |
> | billingAccounts/billingProfiles/정책 | 예 | 예 |
> | billingAccounts/billingProfiles/가격표 | 예 | 예 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 예 | 예 |
> | billingAccounts/billingProfiles/제품 | 예 | 예 |
> | billingAccounts/billingProfiles/트랜잭션 | 예 | 예 |
> | billingAccounts / billingRoleAssignments | 예 | 예 |
> | billingAccounts / billingRoleDefinitions | 예 | 예 |
> | billingAccounts / billingSubscriptions | 예 | 예 |
> | billingAccounts/billingSubscriptions/송장 | 예 | 예 |
> | billingAccounts / createBillingRoleAssignment | 예 | 예 |
> | billingAccounts / createInvoiceSectionOperations | 예 | 예 |
> | billingAccounts/고객 | 예 | 예 |
> | billingAccounts/customers/billingPermissions | 예 | 예 |
> | billingAccounts/customers/billingSubscriptions | 예 | 예 |
> | billingAccounts/customers/initiateTransfer | 예 | 예 |
> | billingAccounts/고객/정책 | 예 | 예 |
> | billingAccounts/고객/제품 | 예 | 예 |
> | billingAccounts/고객/트랜잭션 | 예 | 예 |
> | billingAccounts/고객/전송 | 예 | 예 |
> | billingAccounts/부서 | 예 | 예 |
> | billingAccounts / enrollmentAccounts | 예 | 예 |
> | billingAccounts/송장 | 예 | 예 |
> | billingAccounts / invoiceSections | 예 | 예 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 예 | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions | 예 | 예 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 예 | 예 |
> | billingAccounts/invoiceSections/상승 | 예 | 예 |
> | billingAccounts / invoiceSections / initiateTransfer | 예 | 예 |
> | billingAccounts / invoiceSections / patchOperations | 예 | 예 |
> | billingAccounts/invoiceSections/제품 Moveoperations | 예 | 예 |
> | billingAccounts/invoiceSections/제품 | 예 | 예 |
> | billingAccounts/invoiceSections/제품/이전 | 예 | 예 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 예 | 예 |
> | billingAccounts/invoiceSections/트랜잭션 | 예 | 예 |
> | billingAccounts/invoiceSections/전송 | 예 | 예 |
> | billingAccounts/lineOfCredit | 예 | 예 |
> | billingAccounts / patchOperations | 예 | 예 |
> | billingAccounts / paymentMethods | 예 | 예 |
> | billingAccounts/제품 | 예 | 예 |
> | billingAccounts/트랜잭션 | 예 | 예 |
> | billingPeriods | 예 | 예 |
> | billingPermissions | 예 | 예 |
> | billingProperty | 예 | 예 |
> | billingRoleAssignments | 예 | 예 |
> | billingRoleDefinitions | 예 | 예 |
> | createBillingRoleAssignment | 예 | 예 |
> | departments | 예 | 예 |
> | enrollmentAccounts | 예 | 예 |
> | invoices | 예 | 예 |
> | transfers | 예 | 예 |
> | 전송/acceptTransfer | 예 | 예 |
> | 전송/declineTransfer | 예 | 예 |
> | 전송/operationStatus | 예 | 예 |
> | 전송/validateTransfer | 예 | 예 |
> | validateAddress | 예 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mapApis | yes | yes |
> | updateCommunicationPreference | 예 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | yes | yes |
> | cordaMembers | yes | yes |
> | 감시자 | yes | yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 예 | 예 |
> | blueprintAssignments / assignmentOperations | 예 | 예 |
> | blueprintAssignments/작업 | 예 | 예 |
> | blueprints | 예 | 예 |
> | 청사진/아티팩트 | 예 | 예 |
> | 청사진/버전 | 예 | 예 |
> | 청사진/버전/아티팩트 | 예 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | yes | yes |
> | botServices/채널 | 예 | 예 |
> | botServices/연결 | 예 | 예 |
> | 언어 | 예 | 예 |
> | 템플릿 | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | yes | yes |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 예 | 예 |
> | autoQuotaIncrease | 예 | 예 |
> | calculateExchange | 예 | 예 |
> | calculatePrice | 예 | 예 |
> | calculatePurchasePrice | 예 | 예 |
> | catalogs | 예 | 예 |
> | commercialReservationOrders | 예 | 예 |
> | 교환 | 예 | 예 |
> | placePurchaseOrder | 예 | 예 |
> | reservationOrders | 예 | 예 |
> | reservationOrders / calculateRefund | 예 | 예 |
> | reservationOrders/병합 | 예 | 예 |
> | reservationOrders/예약 | 예 | 예 |
> | reservationOrders/예약/수정 버전 | 예 | 예 |
> | reservationOrders/반환 | 예 | 예 |
> | reservationOrders/분할 | 예 | 예 |
> | reservationOrders/교환 | 예 | 예 |
> | reservations | 예 | 예 |
> | resourceProviders | 예 | 예 |
> | 리소스 | 예 | 예 |
> | validateReservationOrder | 예 | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 예 | 예 |
> | CdnWebApplicationFirewallPolicies | yes | yes |
> | edgenodes | 예 | 예 |
> | 프로필 | yes | yes |
> | 프로필/끝점 | yes | yes |
> | 프로필/끝점/customdomains | 예 | 예 |
> | 프로필/끝점/원본 | 예 | 예 |
> | validateProbe | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | yes | yes |
> | certificateOrders/certificate | 예 | 예 |
> | validateCertificateRegistrationInformation | 예 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | domainNames | 예 | 예 |
> | domainNames/기능 | 예 | 예 |
> | domainNames/internalLoadBalancers 장치 | 예 | 예 |
> | domainNames/serviceCertificates | 예 | 예 |
> | domainNames/슬롯 | 예 | 예 |
> | domainNames/슬롯/역할 | 예 | 예 |
> | domainNames/슬롯/역할/metricDefinitions | 예 | 예 |
> | domainNames/슬롯/역할/메트릭 | 예 | 예 |
> | moveSubscriptionResources | 예 | 예 |
> | operatingSystemFamilies | 예 | 예 |
> | operatingSystems | 예 | 예 |
> | quotas | 예 | 예 |
> | resourceTypes | 예 | 예 |
> | validateSubscriptionMoveAvailability | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | virtualMachines/diagnosticSettings | 예 | 예 |
> | virtualMachines/metricDefinitions | 예 | 예 |
> | virtualMachines/메트릭 | 예 | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 예 | 예 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | expressRouteCrossConnections/피어 링 | 예 | 예 |
> | gatewaySupportedDevices | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | quotas | 예 | 예 |
> | reservedIps | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 예 | 예 |
> | virtualNetworks/virtualNetworkPeerings | 예 | 예 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | disks | 예 | 예 |
> | images | 예 | 예 |
> | osImages | 예 | 예 |
> | osPlatformImages | 예 | 예 |
> | publicImages | 예 | 예 |
> | quotas | 예 | 예 |
> | storageAccounts | 예 | 예 |
> | storageAccounts/blobServices | 예 | 예 |
> | storageAccounts/fileServices | 예 | 예 |
> | storageAccounts/metricDefinitions | 예 | 예 |
> | storageAccounts/메트릭 | 예 | 예 |
> | storageAccounts/queueServices | 예 | 예 |
> | storageAccounts/서비스 | 예 | 예 |
> | storageAccounts/services/diagnosticSettings | 예 | 예 |
> | storageAccounts/services/metricDefinitions | 예 | 예 |
> | storageAccounts/서비스/메트릭 | 예 | 예 |
> | storageAccounts/tableServices | 예 | 예 |
> | storageAccounts/vmImages | 예 | 예 |
> | vmImages | 예 | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 예 | 예 |
> | UsageAggregates | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | yes | yes |
> | Disk; 집합 | yes | yes |
> | disks | yes | yes |
> | galleries | yes | yes |
> | 갤러리/응용 프로그램 | 예 | 예 |
> | 갤러리/응용 프로그램/버전 | 예 | 예 |
> | 갤러리/이미지 | 예 | 예 |
> | 갤러리/이미지/버전 | 예 | 예 |
> | 호스트 그룹 | yes | yes |
> | 호스트 그룹/호스트 | yes | yes |
> | images | yes | yes |
> | proximityPlacementGroups | yes | yes |
> | restorePointCollections | yes | yes |
> | restorePointCollections / restorePoints | 예 | 예 |
> | sharedVMImages | yes | yes |
> | sharedVMImages/버전 | 예 | 예 |
> | 스냅샷 | yes | yes |
> | virtualMachines | yes | yes |
> | virtualMachines/확장 | yes | yes |
> | virtualMachines/metricDefinitions | 예 | 예 |
> | virtualMachineScaleSets | yes | yes |
> | virtualMachineScaleSets/확장 | 예 | 예 |
> | virtualMachineScaleSets/networkInterfaces | 예 | 예 |
> | virtualMachineScaleSets/publicIPAddresses | 예 | 예 |
> | virtualMachineScaleSets/virtualMachines | 예 | 예 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 예 | 예 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 예 | 예 |
> | 잔액 | 예 | 예 |
> | 예산 | 예 | 예 |
> | Charges | 예 | 예 |
> | CostTags | 예 | 예 |
> | credits | 예 | 예 |
> | events | 예 | 예 |
> | 예측 | 예 | 예 |
> | lots | 예 | 예 |
> | Marketplace | 예 | 예 |
> | Pricesheets | 예 | 예 |
> | products | 예 | 예 |
> | ReservationDetails | 예 | 예 |
> | ReservationRecommendations | 예 | 예 |
> | ReservationSummaries | 예 | 예 |
> | ReservationTransactions | 예 | 예 |
> | 태그들 | 예 | 예 |
> | tenants | 예 | 예 |
> | 용어 | 예 | 예 |
> | UsageDetails | 예 | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | yes | yes |
> | serviceAssociationLinks | 예 | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registries | yes | yes |
> | 레지스트리/빌드 | 예 | 예 |
> | 레지스트리/빌드/취소 | 예 | 예 |
> | 레지스트리/빌드/getLogLink | 예 | 예 |
> | 레지스트리/buildTasks | yes | yes |
> | 레지스트리/buildTasks/단계 | 예 | 예 |
> | 레지스트리/eventGridFilters | 예 | 예 |
> | 레지스트리/generateCredentials | 예 | 예 |
> | 레지스트리/getBuildSourceUploadUrl | 예 | 예 |
> | 레지스트리/GetCredentials | 예 | 예 |
> | 레지스트리/importImage | 예 | 예 |
> | 레지스트리/privateEndpointConnectionProxies | 예 | 예 |
> | 레지스트리/privateEndpointConnectionProxies/유효성 검사 | 예 | 예 |
> | 레지스트리/privateLinkResources | 예 | 예 |
> | 레지스트리/queueBuild | 예 | 예 |
> | 레지스트리/regenerateCredential | 예 | 예 |
> | 레지스트리/regenerateCredentials | 예 | 예 |
> | 레지스트리/복제 | yes | yes |
> | 레지스트리/실행 | 예 | 예 |
> | 레지스트리/실행/취소 | 예 | 예 |
> | 레지스트리/scheduleRun | 예 | 예 |
> | 레지스트리/scopeMaps | 예 | 예 |
> | 레지스트리/taskRuns | yes | yes |
> | 레지스트리/작업 | yes | yes |
> | 레지스트리/토큰 | 예 | 예 |
> | 레지스트리/updatePolicies | 예 | 예 |
> | 레지스트리/웹 후크 | yes | yes |
> | registry/웹 후크/getcallbackconfig | 예 | 예 |
> | registry/웹 후크/ping | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | yes | yes |
> | managedClusters | yes | yes |
> | openShiftManagedClusters | yes | yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 경고 | 예 | 예 |
> | BillingAccounts | 예 | 예 |
> | 예산 | 예 | 예 |
> | CloudConnectors | 예 | 예 |
> | 커넥터 | yes | yes |
> | Departments | 예 | 예 |
> | 차원 | 예 | 예 |
> | EnrollmentAccounts | 예 | 예 |
> | 내보내도록 | 예 | 예 |
> | ExternalBillingAccounts | 예 | 예 |
> | ExternalBillingAccounts/경고 | 예 | 예 |
> | ExternalBillingAccounts/차원 | 예 | 예 |
> | ExternalBillingAccounts/예측 | 예 | 예 |
> | ExternalBillingAccounts/쿼리 | 예 | 예 |
> | ExternalSubscriptions | 예 | 예 |
> | ExternalSubscriptions/Alerts | 예 | 예 |
> | ExternalSubscriptions/차원 | 예 | 예 |
> | ExternalSubscriptions/예측 | 예 | 예 |
> | ExternalSubscriptions/Query | 예 | 예 |
> | 예측 | 예 | 예 |
> | 쿼리 | 예 | 예 |
> | register | 예 | 예 |
> | Reportconfigs | 예 | 예 |
> | 보고서 | 예 | 예 |
> | 설정 | 예 | 예 |
> | showbackRules | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | requests | 예 | 예 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 연결 | 예 | 예 |
> | resourceProviders | yes | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | yes | yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | yes | yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | yes | 예 |
> | workspace/dbWorkspaces | 예 | 예 |
> | 작업 영역/storageEncryption | 예 | 예 |
> | 작업 영역/virtualNetworkPeerings | 예 | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | yes | yes |
> | datacatalogs | yes | yes |
> | datacatalogs/데이터 원본 | 예 | 예 |
> | datacatalogs/데이터 원본/검색 | 예 | 예 |
> | datacatalogs/데이터 원본/검색/데이터 집합 | 예 | 예 |
> | datacatalogs/데이터 원본/검색/트리거 | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | yes | 예 |
> | dataFactories/diagnosticSettings | 예 | 예 |
> | dataFactories/metricDefinitions | 예 | 예 |
> | dataFactorySchema | 예 | 예 |
> | factories | yes | 예 |
> | 팩터리/integrationRuntimes | 예 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |
> | 계정/dataLakeStoreAccounts | 예 | 예 |
> | 계정/storageAccounts | 예 | 예 |
> | 계정/storageAccounts/컨테이너 | 예 | 예 |
> | 계정/transferAnalyticsUnits | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |
> | 계정/eventGridFilters | 예 | 예 |
> | 계정/firewallRules | 예 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |
> | 서비스/프로젝트 | 예 | 예 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |
> | 계정/공유 | 예 | 예 |
> | 계정/공유/데이터 집합 | 예 | 예 |
> | 계정/공유/초대 | 예 | 예 |
> | 계정/공유/providersharesubscriptions | 예 | 예 |
> | 계정/공유/synchronizationSettings | 예 | 예 |
> | 계정/sharesubscriptions | 예 | 예 |
> | 계정/sharesubscriptions/consumerSourceDataSets | 예 | 예 |
> | 계정/sharesubscriptions/datasetmappings | 예 | 예 |
> | 계정/sharesubscriptions/트리거 | 예 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | yes | yes |
> | 서버/관리자 | 예 | 예 |
> | 서버/키 | 예 | 예 |
> | servers/privateEndpointConnectionProxies | 예 | 예 |
> | servers/privateEndpointConnections | 예 | 예 |
> | servers/privateLinkResources | 예 | 예 |
> | 서버/queryTexts | 예 | 예 |
> | servers/recoverableServers | 예 | 예 |
> | servers/topQueryStatistics | 예 | 예 |
> | servers/virtualNetworkRules | 예 | 예 |
> | 서버/waitStatistics | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | yes | yes |
> | 서버/관리자 | 예 | 예 |
> | 서버/키 | 예 | 예 |
> | servers/privateEndpointConnectionProxies | 예 | 예 |
> | servers/privateEndpointConnections | 예 | 예 |
> | servers/privateLinkResources | 예 | 예 |
> | 서버/queryTexts | 예 | 예 |
> | servers/recoverableServers | 예 | 예 |
> | servers/topQueryStatistics | 예 | 예 |
> | servers/virtualNetworkRules | 예 | 예 |
> | 서버/waitStatistics | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | serverGroups | yes | yes |
> | servers | yes | yes |
> | 서버/관리자 | 예 | 예 |
> | 서버/키 | 예 | 예 |
> | servers/privateEndpointConnectionProxies | 예 | 예 |
> | servers/privateEndpointConnections | 예 | 예 |
> | servers/privateLinkResources | 예 | 예 |
> | 서버/queryTexts | 예 | 예 |
> | servers/recoverableServers | 예 | 예 |
> | servers/topQueryStatistics | 예 | 예 |
> | servers/virtualNetworkRules | 예 | 예 |
> | 서버/waitStatistics | 예 | 예 |
> | serversv2 | yes | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | artifactSources | yes | yes |
> | rollouts | yes | yes |
> | serviceTopologies | yes | yes |
> | serviceTopologies/서비스 | yes | yes |
> | serviceTopologies/서비스/serviceUnits | yes | yes |
> | 단계 | yes | yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | yes | yes |
> | applicationgroups/응용 프로그램 | 예 | 예 |
> | applicationgroups/데스크톱 | 예 | 예 |
> | applicationgroups/startmenuitems | 예 | 예 |
> | hostpools | yes | yes |
> | hostpools/sessionhosts | 예 | 예 |
> | hostpools/sessionhosts/usersessions | 예 | 예 |
> | hostpools/usersessions | 예 | 예 |
> | workspaces | yes | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | yes | yes |
> | ElasticPools / IotHubTenants | yes | yes |
> | ElasticPools/IotHubTenants/securitySettings | 예 | 예 |
> | IotHubs | yes | yes |
> | IotHubs/eventGridFilters | 예 | 예 |
> | IotHubs/securitySettings | 예 | 예 |
> | ProvisioningServices | yes | yes |
> | usages | 예 | 예 |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 파이프라인 | yes | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | yes | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | yes | yes |
> | labs | yes | yes |
> | 랩/환경 | yes | yes |
> | labs/serviceRunners | yes | yes |
> | labs/virtualMachines | yes | yes |
> | schedules | yes | yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 예 | 예 |
> | databaseAccounts | yes | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | yes | yes |
> | 도메인/도메인 소유자의 식별자 | 예 | 예 |
> | generateSsoRequest | 예 | 예 |
> | topLevelDomains | 예 | 예 |
> | validateDomainRegistrationInformation | 예 | 예 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 예 | 예 |
> | lcsprojects/clouddeployments | 예 | 예 |
> | lcsprojects/커넥터 | 예 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | yes | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | yes | yes |
> | 도메인/항목 | 예 | 예 |
> | eventSubscriptions | 예 | 예 |
> | extensionTopics | 예 | 예 |
> | 네임 스페이스 | yes | yes |
> | 네임 스페이스/a s p 채널 | 예 | 예 |
> | 파트너 등록 | yes | yes |
> | 항목 항목 | yes | yes |
> | systemTopics | yes | yes |
> | systemTopics/eventSubscriptions | 예 | 예 |
> | topics | yes | yes |
> | topicTypes | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | yes | yes |
> | 네임스페이스 | yes | yes |
> | 네임 스페이스/authorizationrules | 예 | 예 |
> | 네임 스페이스/disasterrecoveryconfigs | 예 | 예 |
> | 네임 스페이스/eventhubs | 예 | 예 |
> | 네임 스페이스/eventhubs/authorizationrules | 예 | 예 |
> | 네임 스페이스/eventhubs/consumergroups | 예 | 예 |
> | 네임 스페이스/네트워크 규칙 집합 | 예 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 기능 | 예 | 예 |
> | providers | 예 | 예 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | enroll | 예 | 예 |
> | galleryitems | 예 | 예 |
> | generateartifactaccessuri | 예 | 예 |
> | myareas | 예 | 예 |
> | myareas/areas | 예 | 예 |
> | myareas/areas/areas | 예 | 예 |
> | myareas/areas/areas/galleryitems | 예 | 예 |
> | myareas/areas/galleryitems | 예 | 예 |
> | myareas/galleryitems | 예 | 예 |
> | register | 예 | 예 |
> | 리소스 | 예 | 예 |
> | retrieveresourcesbyid | 예 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | yes | yes |
> | autoManagedVmConfigurationProfiles | yes | yes |
> | configurationprofil | 예 | 예 |
> | guestConfigurationAssignments | 예 | 예 |
> | software | 예 | 예 |
> | softwareUpdateProfile | 예 | 예 |
> | 업데이트 | 예 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | yes | yes |
> | sapMonitors | yes | yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | yes | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | yes | yes |
> | 클러스터/응용 프로그램 | 예 | 예 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | yes | yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 머신의 | yes | yes |
> | 컴퓨터/확장 | yes | yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataManagers | yes | yes |

## <a name="microsofthydra"></a>Microsoft 히드라

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | yes | yes |
> | networkScopes | yes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | yes | yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 예 | 예 |
> | IoTApps | yes | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | yes | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 예 | 예 |
> | hsmPools | yes | yes |
> | vaults | yes | yes |
> | 자격 증명 모음/Accesspolicy | 예 | 예 |
> | 자격 증명 모음/eventGridFilters | 예 | 예 |
> | 자격 증명 모음/비밀 | 예 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | yes | yes |
> | 클러스터/attacheddatabaseconfigurations | 예 | 예 |
> | 클러스터/데이터베이스 | 예 | 예 |
> | 클러스터/데이터베이스/dataconnections | 예 | 예 |
> | 클러스터/데이터베이스/eventhubconnections | 예 | 예 |
> | 클러스터/데이터베이스/principalassignments | 예 | 예 |
> | 클러스터/principalassignments | 예 | 예 |
> | 클러스터/sharedidentities | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | yes | yes |
> | users | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | yes | yes |
> | integrationAccounts | yes | yes |
> | integrationServiceEnvironments | yes | yes |
> | integrationServiceEnvironments/managedApis | yes | yes |
> | isolatedEnvironments | yes | yes |
> | workflows | yes | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | yes | yes |
> | webServices | yes | yes |
> | 작업 영역 | yes | yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | yes | yes |
> | 작업 영역/계산 | 예 | 예 |
> | workspace/eventGridFilters | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Identities | 예 | 예 |
> | userAssignedIdentities | yes | yes |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 예 | 예 |
> | registrationAssignments | 예 | 예 |
> | registrationDefinitions | 예 | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 예 | 예 |
> | managementGroups | 예 | 예 |
> | 리소스 | 예 | 예 |
> | startTenantBackfill | 예 | 예 |
> | tenantBackfillStatus | 예 | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |
> | 계정/eventGridFilters | 예 | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | offers | 예 | 예 |
> | offerTypes | 예 | 예 |
> | offerTypes/게시자 | 예 | 예 |
> | offerTypes/게시자/제안 | 예 | 예 |
> | offerTypes/게시자/제품/계획 | 예 | 예 |
> | offerTypes/게시자/제품/계획/규약 | 예 | 예 |
> | offerTypes/게시자/제품/계획/configs | 예 | 예 |
> | offerTypes/publishers/제품/계획/configs/importImage | 예 | 예 |
> | privategalleryitems | 예 | 예 |
> | privateStoreClient | 예 | 예 |
> | products | 예 | 예 |
> | 게시자 | 예 | 예 |
> | 게시자/제안 | 예 | 예 |
> | 게시자/제품/개정 | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | yes | yes |
> | updateCommunicationPreference | 예 | 예 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | agreements | 예 | 예 |
> | offertypes | 예 | 예 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mediaservices | yes | yes |
> | windowsazure.mediaservices/accountFilters | 예 | 예 |
> | windowsazure.mediaservices/자산 | 예 | 예 |
> | windowsazure.mediaservices/asset/assetFilters | 예 | 예 |
> | windowsazure.mediaservices/Contentkeypolicy | 예 | 예 |
> | windowsazure.mediaservices/eventGridFilters | 예 | 예 |
> | windowsazure.mediaservices/liveEventOperations | 예 | 예 |
> | windowsazure.mediaservices/liveEvents | yes | yes |
> | windowsazure.mediaservices/liveEvents/Liveoutput | 예 | 예 |
> | windowsazure.mediaservices/liveOutputOperations | 예 | 예 |
> | windowsazure.mediaservices/mediaGraphs | 예 | 예 |
> | windowsazure.mediaservices/streamingEndpointOperations | 예 | 예 |
> | windowsazure.mediaservices/streamingEndpoints | yes | yes |
> | windowsazure.mediaservices/streamingLocators | 예 | 예 |
> | windowsazure.mediaservices/streamingPolicies | 예 | 예 |
> | windowsazure.mediaservices/변환 | 예 | 예 |
> | windowsazure.mediaservices/변환/작업 | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appClusters | yes | yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | yes | yes |
> | migrateprojects | yes | yes |
> | moveCollections | yes | yes |
> | projects | yes | yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | yes | yes |
> | objectUnderstandingAccounts | yes | yes |
> | remoteRenderingAccounts | yes | yes |
> | spatialAnchorsAccounts | yes | yes |
> | surfaceReconstructionAccounts | yes | yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | yes | 예 |
> | netAppAccounts/capacityPools | yes | 예 |
> | netAppAccounts/capacityPools/볼륨 | yes | 예 |
> | netAppAccounts/capacityPools/볼륨/mountTargets | yes | 예 |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | yes | 예 |

## <a name="microsoftnotebooks"></a>Microsoft. 전자 필기장

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | 예 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | yes | yes |
> | applicationGatewayWebApplicationFirewallPolicies | yes | yes |
> | applicationSecurityGroups | yes | yes |
> | azureFirewallFqdnTags | 예 | 예 |
> | azureFirewalls | yes | 예 |
> | bastionHosts | yes | yes |
> | bgpServiceCommunities | 예 | 예 |
> | connections | yes | yes |
> | ddosCustomPolicies | yes | yes |
> | ddosProtectionPlans | yes | yes |
> | dnsOperationStatuses | 예 | 예 |
> | dnszones | yes | yes |
> | dnszones/A | 예 | 예 |
> | dnszones/AAAA | 예 | 예 |
> | dnszones/모두 | 예 | 예 |
> | dnszones/CAA | 예 | 예 |
> | dnszones/CNAME | 예 | 예 |
> | dnszones/MX | 예 | 예 |
> | dnszones/NS | 예 | 예 |
> | dnszones/PTR | 예 | 예 |
> | dnszones/레코드 집합 | 예 | 예 |
> | dnszones/SOA | 예 | 예 |
> | dnszones/SRV | 예 | 예 |
> | dnszones/TXT | 예 | 예 |
> | expressRouteCircuits | yes | yes |
> | expressRouteCrossConnections | yes | yes |
> | expressRouteGateways | yes | yes |
> | expressRoutePorts | yes | yes |
> | expressRouteServiceProviders | 예 | 예 |
> | firewallPolicies | yes | yes |
> | frontdoors | 예, 제한 ( [아래 참고](#frontdoor)참조) | yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | frontdoorWebApplicationFirewallPolicies | 예, 제한 ( [아래 참고](#frontdoor)참조) | yes |
> | getDnsResourceReference | 예 | 예 |
> | internalNotify | 예 | 예 |
> | loadBalancers | yes | 예 |
> | localNetworkGateways | yes | yes |
> | natGateways | yes | yes |
> | networkIntentPolicies | yes | yes |
> | networkInterfaces | yes | yes |
> | networkProfiles | yes | yes |
> | networkSecurityGroups | yes | yes |
> | networkWatchers | yes | 예 |
> | networkWatchers/connectionMonitors | yes | 예 |
> | networkWatchers/lenses | yes | 예 |
> | networkWatchers/ | yes | 예 |
> | p2sVpnGateways | yes | yes |
> | privateDnsOperationStatuses | 예 | 예 |
> | privateDnsZones | yes | yes |
> | privateDnsZones/A | 예 | 예 |
> | privateDnsZones/AAAA | 예 | 예 |
> | privateDnsZones/모두 | 예 | 예 |
> | privateDnsZones/CNAME | 예 | 예 |
> | privateDnsZones/MX | 예 | 예 |
> | privateDnsZones/PTR | 예 | 예 |
> | privateDnsZones/SOA | 예 | 예 |
> | privateDnsZones/SRV | 예 | 예 |
> | privateDnsZones/TXT | 예 | 예 |
> | privateDnsZones / virtualNetworkLinks | yes | yes |
> | privateEndpoints | yes | yes |
> | privateLinkServices | yes | yes |
> | publicIPAddresses | yes | yes |
> | publicIPPrefixes | yes | yes |
> | routeFilters | yes | yes |
> | routeTables | yes | yes |
> | serviceEndpointPolicies | yes | yes |
> | trafficManagerGeographicHierarchies | 예 | 예 |
> | trafficmanagerprofiles | yes | yes |
> | trafficmanagerprofiles/heatMaps | 예 | 예 |
> | trafficManagerUserMetricsKeys | 예 | 예 |
> | virtualHubs | yes | yes |
> | virtualNetworkGateways | yes | yes |
> | virtualNetworks | yes | yes |
> | virtualNetworkTaps | yes | yes |
> | virtualWans | yes | yes |
> | vpnGateways | yes | 예 |
> | vpnSites | yes | yes |
> | webApplicationFirewallPolicies | yes | yes |

<a id="frontdoor" />

> [!NOTE]
> Azure Front 도어 서비스의 경우 리소스를 만들 때 태그를 적용할 수 있지만, 태그를 업데이트 하거나 추가 하는 기능은 현재 지원 되지 않습니다.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | yes | 예 |
> | 네임 스페이스/notificationHubs | yes | 예 |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | osNamespaces 스페이스 | yes | yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | yes | yes |
> | ImportSites | yes | yes |
> | ServerSites | yes | yes |
> | VMwareSites | yes | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | yes | yes |
> | linkTargets | 예 | 예 |
> | storageInsightConfigs | 예 | 예 |
> | workspaces | yes | yes |
> | 작업 영역/데이터 내보내기 | 예 | 예 |
> | 작업 영역/데이터 원본 | 예 | 예 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 예 | 예 |
> | 작업 영역/privateEndpointConnectionProxies | 예 | 예 |
> | 작업 영역/privateEndpointConnections | 예 | 예 |
> | 작업 영역/privateLinkResources | 예 | 예 |
> | 작업 영역/쿼리 | 예 | 예 |
> | 작업 영역/scopedPrivateLinkProxies | 예 | 예 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 예 | 예 |
> | managementconfigurations | yes | yes |
> | solutions | yes | yes |
> | 뷰 | yes | yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 예 | 예 |
> | peerAsns | 예 | 예 |
> | 피어 링 | yes | yes |
> | peeringServiceProviders | 예 | 예 |
> | peeringServices | yes | yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 예 | 예 |
> | policyMetadata | 예 | 예 |
> | policyStates | 예 | 예 |
> | policyTrackedResources | 예 | 예 |
> | remediations | 예 | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 예 | 예 |
> | dashboards | yes | yes |
> | userSettings | 예 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | yes | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capacities | yes | yes |

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | yes | yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 예 | 예 |
> | vaults | yes | yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | yes | yes |
> | 네임 스페이스/authorizationrules | 예 | 예 |
> | 네임 스페이스/hybridconnections | 예 | 예 |
> | 네임 스페이스/hybridconnections/authorizationrules | 예 | 예 |
> | 네임 스페이스/wcfrelays | 예 | 예 |
> | 네임 스페이스/wcfrelays/authorizationrules | 예 | 예 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 컬렉션 | yes | yes |
> | 컬렉션/응용 프로그램 | 예 | 예 |
> | collections/securityprincipals | 예 | 예 |
> | 템플릿 이미지 | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | yes | yes |
> | resourceChangeDetails | 예 | 예 |
> | resourceChanges | 예 | 예 |
> | 리소스 | 예 | 예 |
> | 자원 기록 | 예 | 예 |
> | subscriptionsStatus | 예 | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 예 | 예 |
> | childAvailabilityStatuses | 예 | 예 |
> | childResources | 예 | 예 |
> | emergingissues | 예 | 예 |
> | events | 예 | 예 |
> | impactedResources | 예 | 예 |
> | metadata | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 배포 | yes | 예 |
> | 배포/작업 | 예 | 예 |
> | deploymentScripts | yes | yes |
> | deploymentScripts/logs | 예 | 예 |
> | 링크 | 예 | 예 |
> | notifyResourceJobs | 예 | 예 |
> | providers | 예 | 예 |
> | resourceGroups | yes | 예 |
> | 구독 | yes | 예 |
> | tenants | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | yes | yes |
> | saasresources | 예 | 예 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobcollections | yes | yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 예 | 예 |
> | searchServices | yes | yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 예 | 예 |
> | advancedThreatProtectionSettings | 예 | 예 |
> | 경고 | 예 | 예 |
> | allowedConnections | 예 | 예 |
> | applicationWhitelistings | 예 | 예 |
> | assessmentMetadata | 예 | 예 |
> | 인사 | 예 | 예 |
> | autoDismissAlertsRules | 예 | 예 |
> | 자동화 | yes | yes |
> | AutoProvisioningSettings | 예 | 예 |
> | Compliances | 예 | 예 |
> | dataCollectionAgents | 예 | 예 |
> | deviceSecurityGroups | 예 | 예 |
> | discoveredSecuritySolutions | 예 | 예 |
> | externalSecuritySolutions | 예 | 예 |
> | InformationProtectionPolicies | 예 | 예 |
> | iotSecuritySolutions | yes | yes |
> | IanalyticsModels Securitysolutions/ | 예 | 예 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 예 | 예 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 예 | 예 |
> | jitNetworkAccessPolicies | 예 | 예 |
> | Networkdata.xml | 예 | 예 |
> | 정책 | 예 | 예 |
> | pricings | 예 | 예 |
> | regulatoryComplianceStandards | 예 | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 예 | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 예 | 예 |
> | securityContacts | 예 | 예 |
> | securitySolutions | 예 | 예 |
> | securitySolutionsReferenceData | 예 | 예 |
> | securityStatuses | 예 | 예 |
> | securityStatusesSummaries | 예 | 예 |
> | serverVulnerabilityAssessments | 예 | 예 |
> | 설정 | 예 | 예 |
> | 하위 평가 | 예 | 예 |
> | tasks | 예 | 예 |
> | topologies | 예 | 예 |
> | workspaceSettings | 예 | 예 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aggregations | 예 | 예 |
> | alertRules | 예 | 예 |
> | alertRuleTemplates | 예 | 예 |
> | 책갈피 | 예 | 예 |
> | cases | 예 | 예 |
> | dataConnectors | 예 | 예 |
> | dataConnectorsCheckRequirements | 예 | 예 |
> | 엔터티 | 예 | 예 |
> | entityQueries | 예 | 예 |
> | 사고 | 예 | 예 |
> | officeConsents | 예 | 예 |
> | 설정 | 예 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | yes | 예 |
> | 네임 스페이스/authorizationrules | 예 | 예 |
> | 네임 스페이스/disasterrecoveryconfigs | 예 | 예 |
> | 네임 스페이스/eventgridfilters | 예 | 예 |
> | 네임 스페이스/네트워크 규칙 집합 | 예 | 예 |
> | 네임 스페이스/큐 | 예 | 예 |
> | 네임 스페이스/큐/authorizationrules | 예 | 예 |
> | 네임 스페이스/항목 | 예 | 예 |
> | 네임 스페이스/토픽/authorizationrules | 예 | 예 |
> | 네임 스페이스/토픽/구독 | 예 | 예 |
> | 네임 스페이스/항목/구독/규칙 | 예 | 예 |
> | premiumMessagingRegions | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | yes | yes |
> | clusters | yes | yes |
> | 클러스터/응용 프로그램 | 예 | 예 |
> | containerGroups | yes | yes |
> | containerGroupSets | yes | yes |
> | edgeclusters | yes | yes |
> | edgeclusters/응용 프로그램 | 예 | 예 |
> | managedclusters | yes | yes |
> | managedclusters/nodetypes | 예 | 예 |
> | networks | yes | yes |
> | secretstores | yes | yes |
> | secretstores/인증서 | 예 | 예 |
> | secretstores/비밀 | 예 | 예 |
> | volumes | yes | yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | yes | yes |
> | containerGroups | yes | yes |
> | gateways | yes | yes |
> | networks | yes | yes |
> | secrets | yes | yes |
> | volumes | yes | yes |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 예 | 예 |
> | providerRegistrations resourceTypeRegistrations | 예 | 예 |
> | rollouts | yes | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | yes | yes |
> | SignalR/eventGridFilters | 예 | 예 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | yes | yes |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 예 | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | yes | yes |
> | 애플리케이션 | yes | yes |
> | jitRequests | yes | yes |

## <a name="microsoftspoolservice"></a>SpoolService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registeredSubscriptions | 예 | 예 |
> | 스풀 | yes | yes |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | yes | yes |
> | managedInstances/데이터베이스 | 예 ( [아래 참고](#sqlnote)참조) | yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | 예 | 예 |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | 예 | 예 |
> | managedInstances/databases/vulnerabilityAssessments | 예 | 예 |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | 예 | 예 |
> | managedInstances/가 나 보호기 | 예 | 예 |
> | managedInstances/키 | 예 | 예 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 예 | 예 |
> | managedInstances/vulnerabilityAssessments | 예 | 예 |
> | servers | yes | yes |
> | 서버/관리자 | 예 | 예 |
> | servers/communicationLinks | 예 | 예 |
> | 서버/데이터베이스 | 예 ( [아래 참고](#sqlnote)참조) | yes |
> | 서버/서버 보호기 | 예 | 예 |
> | servers/firewallRules | 예 | 예 |
> | 서버/키 | 예 | 예 |
> | servers/restorableDroppedDatabases | 예 | 예 |
> | servers/serviceobjectives | 예 | 예 |
> | servers/tdeCertificates | 예 | 예 |
> | virtualClusters | 예 | 예 |

<a id="sqlnote" />

> [!NOTE]
> master 데이터베이스는 태그를 지원하지 않지만, Azure SQL Data Warehouse 데이터베이스를 포함한 다른 데이터베이스는 태그를 지원합니다. Azure SQL Data Warehouse 데이터베이스가 활성(일시 중지 아님) 상태여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | yes | yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 예 | 예 |
> | SqlVirtualMachines | yes | yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | yes | yes |
> | storageAccounts/blobServices | 예 | 예 |
> | storageAccounts/fileServices | 예 | 예 |
> | storageAccounts/queueServices | 예 | 예 |
> | storageAccounts/서비스 | 예 | 예 |
> | storageAccounts/services/metricDefinitions | 예 | 예 |
> | storageAccounts/tableServices | 예 | 예 |
> | usages | 예 | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 캐시 | yes | yes |
> | 캐시/storageTargets 가져오기 | 예 | 예 |
> | usageModels | 예 | 예 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 예 | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | yes | yes |
> | storageSyncServices/registeredServers | 예 | 예 |
> | storageSyncServices/syncGroups | 예 | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 | 예 |
> | storageSyncServices/워크플로 | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | yes | yes |
> | storageSyncServices/registeredServers | 예 | 예 |
> | storageSyncServices/syncGroups | 예 | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 | 예 |
> | storageSyncServices/워크플로 | 예 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | yes | yes |
> | storageSyncServices/registeredServers | 예 | 예 |
> | storageSyncServices/syncGroups | 예 | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 | 예 |
> | storageSyncServices/워크플로 | 예 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managers | yes | yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 예(아래 참고를 참조) | yes |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | cancel | 예 | 예 |
> | CreateSubscription | 예 | 예 |
> | enable | 예 | 예 |
> | 이름 바꾸기 | 예 | 예 |
> | SubscriptionDefinitions | 예 | 예 |
> | SubscriptionOperations | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | yes | 예 |
> | 환경/Accesspolicy | 예 | 예 |
> | 환경/s s o 원본 | yes | 예 |
> | environment/referenceDataSets | yes | 예 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | yes | yes |
> | dedicatedCloudServices | yes | yes |
> | virtualMachines | yes | yes |

## <a name="microsoftvnfmanager"></a>Microsoft VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | devices | yes | yes |
> | 판매 | 예 | 예 |
> | 공급 업체/sku | 예 | 예 |
> | 공급 업체/vnfs | 예 | 예 |
> | vnfs | yes | yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 예 | 예 |
> | Apimanagementaccount/apiAcls | 예 | 예 |
> | Apimanagementaccount/api | 예 | 예 |
> | Apimanagementaccount/api/apiAcls | 예 | 예 |
> | Apimanagementaccount/api/connectionAcls | 예 | 예 |
> | Apimanagementaccount/api/연결 | 예 | 예 |
> | Apimanagementaccount/api/connections/connectionAcls | 예 | 예 |
> | Apimanagementaccount/api/localizedDefinitions | 예 | 예 |
> | Apimanagementaccount/connectionAcls | 예 | 예 |
> | Apimanagementaccount/연결 | 예 | 예 |
> | billingMeters | 예 | 예 |
> | certificates | yes | yes |
> | connectionGateways | yes | yes |
> | connections | yes | yes |
> | customApis | yes | yes |
> | deletedSites | 예 | 예 |
> | hostingEnvironments | yes | yes |
> | hostingEnvironments/eventGridFilters | 예 | 예 |
> | hostingEnvironments/multiRolePools | 예 | 예 |
> | hostingEnvironments/이상 풀 | 예 | 예 |
> | publishingUsers | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | resourceHealthMetadata | 예 | 예 |
> | runtimes | 예 | 예 |
> | serverFarms | yes | yes |
> | serverFarms/eventGridFilters | 예 | 예 |
> | sites | yes | yes |
> | 사이트/구성  | 예 | 예 |
> | sites/eventGridFilters | 예 | 예 |
> | 사이트/hostNameBindings | 예 | 예 |
> | 사이트/네트워크 구성 | 예 | 예 |
> | 사이트/premieraddons | yes | yes |
> | 사이트/슬롯 | yes | yes |
> | 사이트/슬롯/eventGridFilters 필터 | 예 | 예 |
> | 사이트/슬롯/hostNameBindings | 예 | 예 |
> | 사이트/슬롯/네트워크 구성 | 예 | 예 |
> | sourceControls | 예 | 예 |
> | staticSites | yes | yes |
> | validate | 예 | 예 |
> | verifyHostingEnvironmentVnet | 예 | 예 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 예 | 예 |
> | diagnosticSettingsCategories | 예 | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | yes | yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | componentsSummary | 예 | 예 |
> | monitorInstances | 예 | 예 |
> | monitorInstancesSummary | 예 | 예 |
> | monitors | 예 | 예 |
> | notificationSettings | 예 | 예 |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
