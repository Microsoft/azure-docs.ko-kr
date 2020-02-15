---
title: 전체 모드 삭제
description: 리소스 종류가 Azure Resource Manager 템플릿에서 전체 모드 삭제를 처리하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 80d2ee356e3bc15a178862c453bf7f1ab8d66c77
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207811"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>완료 모드 배포를 위한 Azure 리소스의 삭제

이 문서에서는 완료 모드로 배포된 템플릿에 없는 경우 리소스 종류가 삭제를 처리하는 방법을 설명합니다.

**예** 로 표시 된 리소스 유형은 형식이 전체 모드로 배포 된 템플릿에 없는 경우 삭제 됩니다.

로 표시 된 리소스 종류는 템플릿에 없을 때 자동으로 삭제 **되지 않습니다.** 그러나 부모 리소스를 삭제 하는 경우 삭제 됩니다. 동작에 대한 전체 설명은 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.

[템플릿에서 둘 이상의 리소스 그룹](cross-resource-group-deployment.md)에 배포 하는 경우 배포 작업에 지정 된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제 되지 않습니다.

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
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DomainServices | yes |
> | DomainServices/oucontainer | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | supportProviders | 예 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aadsupportcases | 예 |
> | addsservices | 예 |
> | agents | 예 |
> | anonymousapiusers | 예 |
> | 구성 | 예 |
> | 로그 | 예 |
> | reports | 예 |
> | servicehealthmetrics | 예 |
> | services | 예 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 구성 | 예 |
> | generateRecommendations | 예 |
> | metadata | 예 |
> | 동영상 추천 기능 | 예 |
> | suppressions | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | actionRules | yes |
> | 경고 | 예 |
> | alertsList | 예 |
> | alertsMetaData | 예 |
> | alertsSummary | 예 |
> | alertsSummaryList | 예 |
> | 사용자 의견 | 예 |
> | smartDetectorAlertRules | yes |
> | smartDetectorRuntimeEnvironments | 예 |
> | smartGroups | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | reportFeedback | 예 |
> | 서비스 | yes |
> | validateServiceName | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | yes |
> | configurationStores/eventGridFilters | 예 |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Spring | yes |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestationProviders | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicAdministrators | 예 |
> | dataAliases | 예 |
> | denyAssignments | 예 |
> | elevateAccess | 예 |
> | findOrphanRoleAssignments | 예 |
> | locks | 예 |
> | 권한 | 예 |
> | policyAssignments | 예 |
> | policyDefinitions | 예 |
> | policySetDefinitions | 예 |
> | providerOperations | 예 |
> | roleAssignments | 예 |
> | roleAssignmentsUsageMetrics | 예 |
> | roleDefinitions | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | automationAccounts | yes |
> | automationAccounts/구성 | yes |
> | automationAccounts/job | 예 |
> | automationAccounts/runbook | yes |
> | automationAccounts/softwareUpdateConfigurations | 예 |
> | automationAccounts/웹 후크 | 예 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | yes |
> | configurationStores/eventGridFilters | 예 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | 환경/계정 | 예 |
> | 환경/계정/네임 스페이스 | 예 |
> | 환경/계정/네임 스페이스/구성 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | b2cDirectories | yes |
> | b2ctenants | 예 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridDataManagers | yes |
> | postgresInstances | yes |
> | sqlBigDataClusters | yes |
> | sqlInstances | yes |
> | sqlServerRegistrations | yes |
> | sqlServerRegistrations/sqlServers | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registrations | yes |
> | 등록/customerSubscriptions | 예 |
> | 등록/제품 | 예 |
> | verificationKeys | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | batchAccounts | yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | billingAccounts | 예 |
> | billingAccounts/규약 | 예 |
> | billingAccounts / billingPermissions | 예 |
> | billingAccounts / billingProfiles | 예 |
> | billingAccounts / billingProfiles / billingPermissions | 예 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 예 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 예 |
> | billingAccounts / billingProfiles / billingSubscriptions | 예 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 예 |
> | billingAccounts/billingProfiles/고객 | 예 |
> | billingAccounts/billingProfiles/지침 | 예 |
> | billingAccounts/billingProfiles/송장 | 예 |
> | billingAccounts/billingProfiles/청구서/가격표 | 예 |
> | billingAccounts/billingProfiles/송장/트랜잭션 | 예 |
> | billingAccounts / billingProfiles / invoiceSections | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 예 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 예 |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 예 |
> | billingAccounts / BillingProfiles / patchOperations | 예 |
> | billingAccounts / billingProfiles / paymentMethods | 예 |
> | billingAccounts/billingProfiles/정책 | 예 |
> | billingAccounts/billingProfiles/가격표 | 예 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 예 |
> | billingAccounts/billingProfiles/제품 | 예 |
> | billingAccounts/billingProfiles/트랜잭션 | 예 |
> | billingAccounts / billingRoleAssignments | 예 |
> | billingAccounts / billingRoleDefinitions | 예 |
> | billingAccounts / billingSubscriptions | 예 |
> | billingAccounts/billingSubscriptions/송장 | 예 |
> | billingAccounts / createBillingRoleAssignment | 예 |
> | billingAccounts / createInvoiceSectionOperations | 예 |
> | billingAccounts/고객 | 예 |
> | billingAccounts/customers/billingPermissions | 예 |
> | billingAccounts/customers/billingSubscriptions | 예 |
> | billingAccounts/customers/initiateTransfer | 예 |
> | billingAccounts/고객/정책 | 예 |
> | billingAccounts/고객/제품 | 예 |
> | billingAccounts/고객/트랜잭션 | 예 |
> | billingAccounts/고객/전송 | 예 |
> | billingAccounts/부서 | 예 |
> | billingAccounts / enrollmentAccounts | 예 |
> | billingAccounts/송장 | 예 |
> | billingAccounts / invoiceSections | 예 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions | 예 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 예 |
> | billingAccounts/invoiceSections/상승 | 예 |
> | billingAccounts / invoiceSections / initiateTransfer | 예 |
> | billingAccounts / invoiceSections / patchOperations | 예 |
> | billingAccounts/invoiceSections/제품 Moveoperations | 예 |
> | billingAccounts/invoiceSections/제품 | 예 |
> | billingAccounts/invoiceSections/제품/이전 | 예 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 예 |
> | billingAccounts/invoiceSections/트랜잭션 | 예 |
> | billingAccounts/invoiceSections/전송 | 예 |
> | billingAccounts/lineOfCredit | 예 |
> | billingAccounts / patchOperations | 예 |
> | billingAccounts / paymentMethods | 예 |
> | billingAccounts/제품 | 예 |
> | billingAccounts/트랜잭션 | 예 |
> | billingPeriods | 예 |
> | billingPermissions | 예 |
> | billingProperty | 예 |
> | billingRoleAssignments | 예 |
> | billingRoleDefinitions | 예 |
> | createBillingRoleAssignment | 예 |
> | departments | 예 |
> | enrollmentAccounts | 예 |
> | invoices | 예 |
> | transfers | 예 |
> | 전송/acceptTransfer | 예 |
> | 전송/declineTransfer | 예 |
> | 전송/operationStatus | 예 |
> | 전송/validateTransfer | 예 |
> | validateAddress | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mapApis | yes |
> | updateCommunicationPreference | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blockchainMembers | yes |
> | cordaMembers | yes |
> | 감시자 | yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blueprintAssignments | 예 |
> | blueprintAssignments / assignmentOperations | 예 |
> | blueprintAssignments/작업 | 예 |
> | blueprints | 예 |
> | 청사진/아티팩트 | 예 |
> | 청사진/버전 | 예 |
> | 청사진/버전/아티팩트 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | botServices | yes |
> | botServices/채널 | 예 |
> | botServices/연결 | 예 |
> | 언어 | 예 |
> | 템플릿 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Redis | yes |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliedReservations | 예 |
> | autoQuotaIncrease | 예 |
> | calculateExchange | 예 |
> | calculatePrice | 예 |
> | calculatePurchasePrice | 예 |
> | catalogs | 예 |
> | commercialReservationOrders | 예 |
> | 교환 | 예 |
> | placePurchaseOrder | 예 |
> | reservationOrders | 예 |
> | reservationOrders / calculateRefund | 예 |
> | reservationOrders/병합 | 예 |
> | reservationOrders/예약 | 예 |
> | reservationOrders/예약/수정 버전 | 예 |
> | reservationOrders/반환 | 예 |
> | reservationOrders/분할 | 예 |
> | reservationOrders/교환 | 예 |
> | reservations | 예 |
> | resourceProviders | 예 |
> | 리소스 | 예 |
> | validateReservationOrder | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 예 |
> | CdnWebApplicationFirewallPolicies | yes |
> | edgenodes | 예 |
> | 프로필 | yes |
> | 프로필/끝점 | yes |
> | 프로필/끝점/customdomains | 예 |
> | 프로필/끝점/원본 | 예 |
> | validateProbe | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | certificateOrders | yes |
> | certificateOrders/certificate | 예 |
> | validateCertificateRegistrationInformation | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | domainNames | yes |
> | domainNames/기능 | 예 |
> | domainNames/internalLoadBalancers 장치 | 예 |
> | domainNames/serviceCertificates | 예 |
> | domainNames/슬롯 | 예 |
> | domainNames/슬롯/역할 | 예 |
> | domainNames/슬롯/역할/metricDefinitions | 예 |
> | domainNames/슬롯/역할/메트릭 | 예 |
> | moveSubscriptionResources | 예 |
> | operatingSystemFamilies | 예 |
> | operatingSystems | 예 |
> | quotas | 예 |
> | resourceTypes | 예 |
> | validateSubscriptionMoveAvailability | 예 |
> | virtualMachines | yes |
> | virtualMachines/diagnosticSettings | 예 |
> | virtualMachines/metricDefinitions | 예 |
> | virtualMachines/메트릭 | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 예 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | expressRouteCrossConnections | 예 |
> | expressRouteCrossConnections/피어 링 | 예 |
> | gatewaySupportedDevices | 예 |
> | networkSecurityGroups | yes |
> | quotas | 예 |
> | reservedIps | yes |
> | virtualNetworks | yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 예 |
> | virtualNetworks/virtualNetworkPeerings | 예 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | disks | 예 |
> | images | 예 |
> | osImages | 예 |
> | osPlatformImages | 예 |
> | publicImages | 예 |
> | quotas | 예 |
> | storageAccounts | yes |
> | storageAccounts/blobServices | 예 |
> | storageAccounts/fileServices | 예 |
> | storageAccounts/metricDefinitions | 예 |
> | storageAccounts/메트릭 | 예 |
> | storageAccounts/queueServices | 예 |
> | storageAccounts/서비스 | 예 |
> | storageAccounts/services/diagnosticSettings | 예 |
> | storageAccounts/services/metricDefinitions | 예 |
> | storageAccounts/서비스/메트릭 | 예 |
> | storageAccounts/tableServices | 예 |
> | storageAccounts/vmImages | 예 |
> | vmImages | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | RateCard | 예 |
> | UsageAggregates | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilitySets | yes |
> | Disk; 집합 | yes |
> | disks | yes |
> | galleries | yes |
> | 갤러리/응용 프로그램 | 예 |
> | 갤러리/응용 프로그램/버전 | 예 |
> | 갤러리/이미지 | 예 |
> | 갤러리/이미지/버전 | 예 |
> | 호스트 그룹 | yes |
> | 호스트 그룹/호스트 | yes |
> | images | yes |
> | proximityPlacementGroups | yes |
> | restorePointCollections | yes |
> | restorePointCollections / restorePoints | 예 |
> | sharedVMImages | yes |
> | sharedVMImages/버전 | 예 |
> | 스냅샷 | yes |
> | virtualMachines | yes |
> | virtualMachines/확장 | yes |
> | virtualMachines/metricDefinitions | 예 |
> | virtualMachineScaleSets | yes |
> | virtualMachineScaleSets/확장 | 예 |
> | virtualMachineScaleSets/networkInterfaces | 예 |
> | virtualMachineScaleSets/publicIPAddresses | 예 |
> | virtualMachineScaleSets/virtualMachines | 예 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 예 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AggregatedCost | 예 |
> | 잔액 | 예 |
> | 예산 | 예 |
> | Charges | 예 |
> | CostTags | 예 |
> | credits | 예 |
> | events | 예 |
> | 예측 | 예 |
> | lots | 예 |
> | Marketplace | 예 |
> | Pricesheets | 예 |
> | products | 예 |
> | ReservationDetails | 예 |
> | ReservationRecommendations | 예 |
> | ReservationSummaries | 예 |
> | ReservationTransactions | 예 |
> | 태그들 | 예 |
> | tenants | 예 |
> | 용어 | 예 |
> | UsageDetails | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerGroups | yes |
> | serviceAssociationLinks | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registries | yes |
> | 레지스트리/빌드 | 예 |
> | 레지스트리/빌드/취소 | 예 |
> | 레지스트리/빌드/getLogLink | 예 |
> | 레지스트리/buildTasks | yes |
> | 레지스트리/buildTasks/단계 | 예 |
> | 레지스트리/eventGridFilters | 예 |
> | 레지스트리/generateCredentials | 예 |
> | 레지스트리/getBuildSourceUploadUrl | 예 |
> | 레지스트리/GetCredentials | 예 |
> | 레지스트리/importImage | 예 |
> | 레지스트리/privateEndpointConnectionProxies | 예 |
> | 레지스트리/privateEndpointConnectionProxies/유효성 검사 | 예 |
> | 레지스트리/privateLinkResources | 예 |
> | 레지스트리/queueBuild | 예 |
> | 레지스트리/regenerateCredential | 예 |
> | 레지스트리/regenerateCredentials | 예 |
> | 레지스트리/복제 | yes |
> | 레지스트리/실행 | 예 |
> | 레지스트리/실행/취소 | 예 |
> | 레지스트리/scheduleRun | 예 |
> | 레지스트리/scopeMaps | 예 |
> | 레지스트리/taskRuns | yes |
> | 레지스트리/작업 | yes |
> | 레지스트리/토큰 | 예 |
> | 레지스트리/updatePolicies | 예 |
> | 레지스트리/웹 후크 | yes |
> | registry/웹 후크/getcallbackconfig | 예 |
> | registry/웹 후크/ping | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerServices | yes |
> | managedClusters | yes |
> | openShiftManagedClusters | yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 경고 | 예 |
> | BillingAccounts | 예 |
> | 예산 | 예 |
> | CloudConnectors | 예 |
> | 커넥터 | yes |
> | Departments | 예 |
> | 차원 | 예 |
> | EnrollmentAccounts | 예 |
> | 내보내도록 | 예 |
> | ExternalBillingAccounts | 예 |
> | ExternalBillingAccounts/경고 | 예 |
> | ExternalBillingAccounts/차원 | 예 |
> | ExternalBillingAccounts/예측 | 예 |
> | ExternalBillingAccounts/쿼리 | 예 |
> | ExternalSubscriptions | 예 |
> | ExternalSubscriptions/Alerts | 예 |
> | ExternalSubscriptions/차원 | 예 |
> | ExternalSubscriptions/예측 | 예 |
> | ExternalSubscriptions/Query | 예 |
> | 예측 | 예 |
> | 쿼리 | 예 |
> | register | 예 |
> | Reportconfigs | 예 |
> | 보고서 | 예 |
> | 설정 | 예 |
> | showbackRules | 예 |
> | 뷰 | 예 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | requests | 예 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 연결 | 예 |
> | resourceProviders | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | yes |
> | workspace/dbWorkspaces | 예 |
> | 작업 영역/storageEncryption | 예 |
> | 작업 영역/virtualNetworkPeerings | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | catalogs | yes |
> | datacatalogs | yes |
> | datacatalogs/데이터 원본 | 예 |
> | datacatalogs/데이터 원본/검색 | 예 |
> | datacatalogs/데이터 원본/검색/데이터 집합 | 예 |
> | datacatalogs/데이터 원본/검색/트리거 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataFactories | yes |
> | dataFactories/diagnosticSettings | 예 |
> | dataFactories/metricDefinitions | 예 |
> | dataFactorySchema | 예 |
> | factories | yes |
> | 팩터리/integrationRuntimes | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | 계정/dataLakeStoreAccounts | 예 |
> | 계정/storageAccounts | 예 |
> | 계정/storageAccounts/컨테이너 | 예 |
> | 계정/transferAnalyticsUnits | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | 계정/eventGridFilters | 예 |
> | 계정/firewallRules | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | yes |
> | 서비스/프로젝트 | yes |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | 계정/공유 | 예 |
> | 계정/공유/데이터 집합 | 예 |
> | 계정/공유/초대 | 예 |
> | 계정/공유/providersharesubscriptions | 예 |
> | 계정/공유/synchronizationSettings | 예 |
> | 계정/sharesubscriptions | 예 |
> | 계정/sharesubscriptions/consumerSourceDataSets | 예 |
> | 계정/sharesubscriptions/datasetmappings | 예 |
> | 계정/sharesubscriptions/트리거 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | yes |
> | 서버/관리자 | 예 |
> | 서버/키 | 예 |
> | servers/privateEndpointConnectionProxies | 예 |
> | servers/privateEndpointConnections | 예 |
> | servers/privateLinkResources | 예 |
> | 서버/queryTexts | 예 |
> | servers/recoverableServers | 예 |
> | servers/topQueryStatistics | 예 |
> | servers/virtualNetworkRules | 예 |
> | 서버/waitStatistics | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | yes |
> | 서버/관리자 | 예 |
> | 서버/키 | 예 |
> | servers/privateEndpointConnectionProxies | 예 |
> | servers/privateEndpointConnections | 예 |
> | servers/privateLinkResources | 예 |
> | 서버/queryTexts | 예 |
> | servers/recoverableServers | 예 |
> | servers/topQueryStatistics | 예 |
> | servers/virtualNetworkRules | 예 |
> | 서버/waitStatistics | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | serverGroups | yes |
> | servers | yes |
> | 서버/관리자 | 예 |
> | 서버/키 | 예 |
> | servers/privateEndpointConnectionProxies | 예 |
> | servers/privateEndpointConnections | 예 |
> | servers/privateLinkResources | 예 |
> | 서버/queryTexts | 예 |
> | servers/recoverableServers | 예 |
> | servers/topQueryStatistics | 예 |
> | servers/virtualNetworkRules | 예 |
> | 서버/waitStatistics | 예 |
> | serversv2 | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | artifactSources | yes |
> | rollouts | yes |
> | serviceTopologies | yes |
> | serviceTopologies/서비스 | yes |
> | serviceTopologies/서비스/serviceUnits | yes |
> | 단계 | yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationgroups | yes |
> | applicationgroups/응용 프로그램 | 예 |
> | applicationgroups/데스크톱 | 예 |
> | applicationgroups/startmenuitems | 예 |
> | hostpools | yes |
> | hostpools/sessionhosts | 예 |
> | hostpools/sessionhosts/usersessions | 예 |
> | hostpools/usersessions | 예 |
> | workspaces | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ElasticPools | yes |
> | ElasticPools / IotHubTenants | yes |
> | ElasticPools/IotHubTenants/securitySettings | 예 |
> | IotHubs | yes |
> | IotHubs/eventGridFilters | 예 |
> | IotHubs/securitySettings | 예 |
> | ProvisioningServices | yes |
> | usages | 예 |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 파이프라인 | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | controllers | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labcenters | yes |
> | labs | yes |
> | 랩/환경 | yes |
> | labs/serviceRunners | yes |
> | labs/virtualMachines | yes |
> | schedules | yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | databaseAccountNames | 예 |
> | databaseAccounts | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | yes |
> | 도메인/도메인 소유자의 식별자 | 예 |
> | generateSsoRequest | 예 |
> | topLevelDomains | 예 |
> | validateDomainRegistrationInformation | 예 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | lcsprojects | 예 |
> | lcsprojects/clouddeployments | 예 |
> | lcsprojects/커넥터 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | yes |
> | 도메인/항목 | 예 |
> | eventSubscriptions | 예 |
> | extensionTopics | 예 |
> | 네임 스페이스 | yes |
> | 네임 스페이스/a s p 채널 | 예 |
> | 파트너 등록 | yes |
> | 항목 항목 | yes |
> | systemTopics | yes |
> | systemTopics/eventSubscriptions | 예 |
> | topics | yes |
> | topicTypes | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | 네임스페이스 | yes |
> | 네임 스페이스/authorizationrules | 예 |
> | 네임 스페이스/disasterrecoveryconfigs | 예 |
> | 네임 스페이스/eventhubs | 예 |
> | 네임 스페이스/eventhubs/authorizationrules | 예 |
> | 네임 스페이스/eventhubs/consumergroups | 예 |
> | 네임 스페이스/네트워크 규칙 집합 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 기능 | 예 |
> | providers | 예 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | enroll | 예 |
> | galleryitems | 예 |
> | generateartifactaccessuri | 예 |
> | myareas | 예 |
> | myareas/areas | 예 |
> | myareas/areas/areas | 예 |
> | myareas/areas/areas/galleryitems | 예 |
> | myareas/areas/galleryitems | 예 |
> | myareas/galleryitems | 예 |
> | register | 예 |
> | 리소스 | 예 |
> | retrieveresourcesbyid | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | autoManagedAccounts | yes |
> | autoManagedVmConfigurationProfiles | yes |
> | configurationprofil | 예 |
> | guestConfigurationAssignments | 예 |
> | software | 예 |
> | softwareUpdateProfile | 예 |
> | 업데이트 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hanaInstances | yes |
> | sapMonitors | yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedHSMs | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | 클러스터/응용 프로그램 | 예 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 머신의 | yes |
> | 컴퓨터/확장 | yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataManagers | yes |

## <a name="microsofthydra"></a>Microsoft 히드라

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | yes |
> | networkScopes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appTemplates | 예 |
> | IoTApps | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 그래프 | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedVaults | 예 |
> | hsmPools | yes |
> | vaults | yes |
> | 자격 증명 모음/Accesspolicy | 예 |
> | 자격 증명 모음/eventGridFilters | 예 |
> | 자격 증명 모음/비밀 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | 클러스터/attacheddatabaseconfigurations | 예 |
> | 클러스터/데이터베이스 | 예 |
> | 클러스터/데이터베이스/dataconnections | 예 |
> | 클러스터/데이터베이스/eventhubconnections | 예 |
> | 클러스터/데이터베이스/principalassignments | 예 |
> | 클러스터/principalassignments | 예 |
> | 클러스터/sharedidentities | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labaccounts | yes |
> | users | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hostingEnvironments | yes |
> | integrationAccounts | yes |
> | integrationServiceEnvironments | yes |
> | integrationServiceEnvironments/managedApis | yes |
> | isolatedEnvironments | yes |
> | workflows | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | commitmentPlans | yes |
> | webServices | yes |
> | 작업 영역 | yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | yes |
> | 작업 영역/계산 | 예 |
> | workspace/eventGridFilters | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Identities | 예 |
> | userAssignedIdentities | yes |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 예 |
> | registrationAssignments | 예 |
> | registrationDefinitions | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | getEntities | 예 |
> | managementGroups | 예 |
> | 리소스 | 예 |
> | startTenantBackfill | 예 |
> | tenantBackfillStatus | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | 계정/eventGridFilters | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | offers | 예 |
> | offerTypes | 예 |
> | offerTypes/게시자 | 예 |
> | offerTypes/게시자/제안 | 예 |
> | offerTypes/게시자/제품/계획 | 예 |
> | offerTypes/게시자/제품/계획/규약 | 예 |
> | offerTypes/게시자/제품/계획/configs | 예 |
> | offerTypes/publishers/제품/계획/configs/importImage | 예 |
> | privategalleryitems | 예 |
> | privateStoreClient | 예 |
> | products | 예 |
> | 게시자 | 예 |
> | 게시자/제안 | 예 |
> | 게시자/제품/개정 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicDevServices | yes |
> | updateCommunicationPreference | 예 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | agreements | 예 |
> | offertypes | 예 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mediaservices | yes |
> | windowsazure.mediaservices/accountFilters | 예 |
> | windowsazure.mediaservices/자산 | 예 |
> | windowsazure.mediaservices/asset/assetFilters | 예 |
> | windowsazure.mediaservices/Contentkeypolicy | 예 |
> | windowsazure.mediaservices/eventGridFilters | 예 |
> | windowsazure.mediaservices/liveEventOperations | 예 |
> | windowsazure.mediaservices/liveEvents | yes |
> | windowsazure.mediaservices/liveEvents/Liveoutput | 예 |
> | windowsazure.mediaservices/liveOutputOperations | 예 |
> | windowsazure.mediaservices/mediaGraphs | 예 |
> | windowsazure.mediaservices/streamingEndpointOperations | 예 |
> | windowsazure.mediaservices/streamingEndpoints | yes |
> | windowsazure.mediaservices/streamingLocators | 예 |
> | windowsazure.mediaservices/streamingPolicies | 예 |
> | windowsazure.mediaservices/변환 | 예 |
> | windowsazure.mediaservices/변환/작업 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appClusters | yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | assessmentProjects | yes |
> | migrateprojects | yes |
> | moveCollections | yes |
> | projects | yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | yes |
> | objectUnderstandingAccounts | yes |
> | remoteRenderingAccounts | yes |
> | spatialAnchorsAccounts | yes |
> | surfaceReconstructionAccounts | yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | netAppAccounts | yes |
> | netAppAccounts/capacityPools | yes |
> | netAppAccounts/capacityPools/볼륨 | yes |
> | netAppAccounts/capacityPools/볼륨/mountTargets | yes |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | yes |

## <a name="microsoftnotebooks"></a>Microsoft. 전자 필기장

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | NotebookProxies | 예 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationGateways | yes |
> | applicationGatewayWebApplicationFirewallPolicies | yes |
> | applicationSecurityGroups | yes |
> | azureFirewallFqdnTags | 예 |
> | azureFirewalls | yes |
> | bastionHosts | yes |
> | bgpServiceCommunities | 예 |
> | connections | yes |
> | ddosCustomPolicies | yes |
> | ddosProtectionPlans | yes |
> | dnsOperationStatuses | 예 |
> | dnszones | yes |
> | dnszones/A | 예 |
> | dnszones/AAAA | 예 |
> | dnszones/모두 | 예 |
> | dnszones/CAA | 예 |
> | dnszones/CNAME | 예 |
> | dnszones/MX | 예 |
> | dnszones/NS | 예 |
> | dnszones/PTR | 예 |
> | dnszones/레코드 집합 | 예 |
> | dnszones/SOA | 예 |
> | dnszones/SRV | 예 |
> | dnszones/TXT | 예 |
> | expressRouteCircuits | yes |
> | expressRouteCrossConnections | yes |
> | expressRouteGateways | yes |
> | expressRoutePorts | yes |
> | expressRouteServiceProviders | 예 |
> | firewallPolicies | yes |
> | frontdoors | yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예 |
> | frontdoorWebApplicationFirewallPolicies | yes |
> | getDnsResourceReference | 예 |
> | internalNotify | 예 |
> | loadBalancers | yes |
> | localNetworkGateways | yes |
> | natGateways | yes |
> | networkIntentPolicies | yes |
> | networkInterfaces | yes |
> | networkProfiles | yes |
> | networkSecurityGroups | yes |
> | networkWatchers | yes |
> | networkWatchers/connectionMonitors | yes |
> | networkWatchers/lenses | yes |
> | networkWatchers/ | yes |
> | p2sVpnGateways | yes |
> | privateDnsOperationStatuses | 예 |
> | privateDnsZones | yes |
> | privateDnsZones/A | 예 |
> | privateDnsZones/AAAA | 예 |
> | privateDnsZones/모두 | 예 |
> | privateDnsZones/CNAME | 예 |
> | privateDnsZones/MX | 예 |
> | privateDnsZones/PTR | 예 |
> | privateDnsZones/SOA | 예 |
> | privateDnsZones/SRV | 예 |
> | privateDnsZones/TXT | 예 |
> | privateDnsZones / virtualNetworkLinks | yes |
> | privateEndpoints | yes |
> | privateLinkServices | yes |
> | publicIPAddresses | yes |
> | publicIPPrefixes | yes |
> | routeFilters | yes |
> | routeTables | yes |
> | serviceEndpointPolicies | yes |
> | trafficManagerGeographicHierarchies | 예 |
> | trafficmanagerprofiles | yes |
> | trafficmanagerprofiles/열 지도 | 예 |
> | trafficManagerUserMetricsKeys | 예 |
> | virtualHubs | yes |
> | virtualNetworkGateways | yes |
> | virtualNetworks | yes |
> | virtualNetworkTaps | yes |
> | virtualWans | yes |
> | vpnGateways | yes |
> | vpnSites | yes |
> | webApplicationFirewallPolicies | yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | yes |
> | 네임 스페이스/notificationHubs | yes |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | osNamespaces 스페이스 | yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | HyperVSites | yes |
> | ImportSites | yes |
> | ServerSites | yes |
> | VMwareSites | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | linkTargets | 예 |
> | storageInsightConfigs | 예 |
> | workspaces | yes |
> | 작업 영역/데이터 내보내기 | 예 |
> | 작업 영역/데이터 원본 | 예 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 예 |
> | 작업 영역/privateEndpointConnectionProxies | 예 |
> | 작업 영역/privateEndpointConnections | 예 |
> | 작업 영역/privateLinkResources | 예 |
> | 작업 영역/쿼리 | 예 |
> | 작업 영역/scopedPrivateLinkProxies | 예 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managementassociations | 예 |
> | managementconfigurations | yes |
> | solutions | yes |
> | 뷰 | yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | legacyPeerings | 예 |
> | peerAsns | 예 |
> | 피어 링 | yes |
> | peeringServiceProviders | 예 |
> | peeringServices | yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | policyEvents | 예 |
> | policyMetadata | 예 |
> | policyStates | 예 |
> | policyTrackedResources | 예 |
> | remediations | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoles | 예 |
> | dashboards | yes |
> | userSettings | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaceCollections | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capacities | yes |

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | backupProtectedItems | 예 |
> | vaults | yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | yes |
> | 네임 스페이스/authorizationrules | 예 |
> | 네임 스페이스/hybridconnections | 예 |
> | 네임 스페이스/hybridconnections/authorizationrules | 예 |
> | 네임 스페이스/wcfrelays | 예 |
> | 네임 스페이스/wcfrelays/authorizationrules | 예 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 컬렉션 | yes |
> | 컬렉션/응용 프로그램 | 예 |
> | collections/securityprincipals | 예 |
> | 템플릿 이미지 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 쿼리 | yes |
> | resourceChangeDetails | 예 |
> | resourceChanges | 예 |
> | 리소스 | 예 |
> | 자원 기록 | 예 |
> | subscriptionsStatus | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilityStatuses | 예 |
> | childAvailabilityStatuses | 예 |
> | childResources | 예 |
> | emergingissues | 예 |
> | events | 예 |
> | impactedResources | 예 |
> | metadata | 예 |
> | 알림 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 배포 | 예 |
> | 배포/작업 | 예 |
> | deploymentScripts | yes |
> | deploymentScripts/logs | 예 |
> | 링크 | 예 |
> | notifyResourceJobs | 예 |
> | providers | 예 |
> | resourceGroups | 예 |
> | 구독 | 예 |
> | tenants | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | yes |
> | saasresources | 예 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobcollections | yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 예 |
> | searchServices | yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 예 |
> | advancedThreatProtectionSettings | 예 |
> | 경고 | 예 |
> | allowedConnections | 예 |
> | applicationWhitelistings | 예 |
> | assessmentMetadata | 예 |
> | 인사 | 예 |
> | autoDismissAlertsRules | 예 |
> | 자동화 | yes |
> | AutoProvisioningSettings | 예 |
> | Compliances | 예 |
> | dataCollectionAgents | 예 |
> | deviceSecurityGroups | 예 |
> | discoveredSecuritySolutions | 예 |
> | externalSecuritySolutions | 예 |
> | InformationProtectionPolicies | 예 |
> | iotSecuritySolutions | yes |
> | IanalyticsModels Securitysolutions/ | 예 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 예 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 예 |
> | jitNetworkAccessPolicies | 예 |
> | Networkdata.xml | 예 |
> | 정책 | 예 |
> | pricings | 예 |
> | regulatoryComplianceStandards | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 예 |
> | securityContacts | 예 |
> | securitySolutions | 예 |
> | securitySolutionsReferenceData | 예 |
> | securityStatuses | 예 |
> | securityStatusesSummaries | 예 |
> | serverVulnerabilityAssessments | 예 |
> | 설정 | 예 |
> | 하위 평가 | 예 |
> | tasks | 예 |
> | topologies | 예 |
> | workspaceSettings | 예 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aggregations | 예 |
> | alertRules | 예 |
> | alertRuleTemplates | 예 |
> | 책갈피 | 예 |
> | cases | 예 |
> | dataConnectors | 예 |
> | dataConnectorsCheckRequirements | 예 |
> | 엔터티 | 예 |
> | entityQueries | 예 |
> | 사고 | 예 |
> | officeConsents | 예 |
> | 설정 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | yes |
> | 네임 스페이스/authorizationrules | 예 |
> | 네임 스페이스/disasterrecoveryconfigs | 예 |
> | 네임 스페이스/eventgridfilters | 예 |
> | 네임 스페이스/네트워크 규칙 집합 | 예 |
> | 네임 스페이스/큐 | 예 |
> | 네임 스페이스/큐/authorizationrules | 예 |
> | 네임 스페이스/항목 | 예 |
> | 네임 스페이스/토픽/authorizationrules | 예 |
> | 네임 스페이스/토픽/구독 | 예 |
> | 네임 스페이스/항목/구독/규칙 | 예 |
> | premiumMessagingRegions | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | yes |
> | clusters | yes |
> | 클러스터/응용 프로그램 | 예 |
> | containerGroups | yes |
> | containerGroupSets | yes |
> | edgeclusters | yes |
> | edgeclusters/응용 프로그램 | 예 |
> | managedclusters | yes |
> | managedclusters/nodetypes | 예 |
> | networks | yes |
> | secretstores | yes |
> | secretstores/인증서 | 예 |
> | secretstores/비밀 | 예 |
> | volumes | yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | yes |
> | containerGroups | yes |
> | gateways | yes |
> | networks | yes |
> | secrets | yes |
> | volumes | yes |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | 예 |
> | providerRegistrations resourceTypeRegistrations | 예 |
> | rollouts | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SignalR | yes |
> | SignalR/eventGridFilters | 예 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SiteRecoveryVault | yes |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridUseBenefits | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationDefinitions | yes |
> | 애플리케이션 | yes |
> | jitRequests | yes |

## <a name="microsoftspoolservice"></a>SpoolService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registeredSubscriptions | 예 |
> | 스풀 | yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | yes |
> | managedInstances/데이터베이스 | yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | 예 |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | 예 |
> | managedInstances/databases/vulnerabilityAssessments | 예 |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | 예 |
> | managedInstances/가 나 보호기 | 예 |
> | managedInstances/키 | 예 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 예 |
> | managedInstances/vulnerabilityAssessments | 예 |
> | servers | yes |
> | 서버/관리자 | 예 |
> | servers/communicationLinks | 예 |
> | 서버/데이터베이스 | yes |
> | 서버/서버 보호기 | 예 |
> | servers/firewallRules | 예 |
> | 서버/키 | 예 |
> | servers/restorableDroppedDatabases | 예 |
> | servers/serviceobjectives | 예 |
> | servers/tdeCertificates | 예 |
> | virtualClusters | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 예 |
> | SqlVirtualMachines | yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageAccounts | yes |
> | storageAccounts/blobServices | 예 |
> | storageAccounts/fileServices | 예 |
> | storageAccounts/queueServices | 예 |
> | storageAccounts/서비스 | 예 |
> | storageAccounts/services/metricDefinitions | 예 |
> | storageAccounts/tableServices | 예 |
> | usages | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 캐시 | yes |
> | 캐시/storageTargets 가져오기 | 예 |
> | usageModels | 예 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | replicationGroups | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices/registeredServers | 예 |
> | storageSyncServices/syncGroups | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices/registeredServers | 예 |
> | storageSyncServices/syncGroups | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices/registeredServers | 예 |
> | storageSyncServices/syncGroups | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managers | yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | streamingjobs | yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cancel | 예 |
> | CreateSubscription | 예 |
> | enable | 예 |
> | 이름 바꾸기 | 예 |
> | SubscriptionDefinitions | 예 |
> | SubscriptionOperations | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | yes |
> | 환경/Accesspolicy | 예 |
> | 환경/s s o 원본 | yes |
> | environment/referenceDataSets | yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | yes |
> | dedicatedCloudServices | yes |
> | virtualMachines | yes |

## <a name="microsoftvnfmanager"></a>Microsoft VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | devices | yes |
> | 판매 | 예 |
> | 공급 업체/sku | 예 |
> | 공급 업체/vnfs | 예 |
> | vnfs | yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | apiManagementAccounts | 예 |
> | Apimanagementaccount/apiAcls | 예 |
> | Apimanagementaccount/api | 예 |
> | Apimanagementaccount/api/apiAcls | 예 |
> | Apimanagementaccount/api/connectionAcls | 예 |
> | Apimanagementaccount/api/연결 | 예 |
> | Apimanagementaccount/api/connections/connectionAcls | 예 |
> | Apimanagementaccount/api/localizedDefinitions | 예 |
> | Apimanagementaccount/connectionAcls | 예 |
> | Apimanagementaccount/연결 | 예 |
> | billingMeters | 예 |
> | certificates | yes |
> | connectionGateways | yes |
> | connections | yes |
> | customApis | yes |
> | deletedSites | 예 |
> | hostingEnvironments | yes |
> | hostingEnvironments/eventGridFilters | 예 |
> | hostingEnvironments/multiRolePools | 예 |
> | hostingEnvironments/이상 풀 | 예 |
> | publishingUsers | 예 |
> | 동영상 추천 기능 | 예 |
> | resourceHealthMetadata | 예 |
> | runtimes | 예 |
> | serverFarms | yes |
> | serverFarms/eventGridFilters | 예 |
> | sites | yes |
> | 사이트/구성  | 예 |
> | sites/eventGridFilters | 예 |
> | 사이트/hostNameBindings | 예 |
> | 사이트/네트워크 구성 | 예 |
> | 사이트/premieraddons | yes |
> | 사이트/슬롯 | yes |
> | 사이트/슬롯/eventGridFilters 필터 | 예 |
> | 사이트/슬롯/hostNameBindings | 예 |
> | 사이트/슬롯/네트워크 구성 | 예 |
> | sourceControls | 예 |
> | staticSites | yes |
> | validate | 예 |
> | verifyHostingEnvironmentVnet | 예 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DeviceServices | yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | componentsSummary | 예 |
> | monitorInstances | 예 |
> | monitorInstancesSummary | 예 |
> | monitors | 예 |
> | notificationSettings | 예 |

## <a name="next-steps"></a>다음 단계

쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)를 다운로드합니다.
