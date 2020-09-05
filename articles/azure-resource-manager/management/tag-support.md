---
title: 리소스에 대 한 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 1f74ecff7c5db7a851ad2795fdfdb8e552a309c0
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231523"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](tag-resources.md)를 지원하는지 여부를 설명합니다. **태그 지원** 이라는 열은 리소스 형식에 태그에 대 한 속성이 있는지 여부를 나타냅니다. **비용 보고서에서 태그** 레이블이 지정 된 열은 리소스 종류가 태그를 비용 보고서에 전달 하는지 여부를 나타냅니다. [Cost Management 비용 분석](../../cost-management-billing/costs/group-filter.md) 및 [Azure 청구 송장 및 일간 사용 현황 데이터](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)에서 태그를 기준으로 비용을 볼 수 있습니다.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) 파일을 다운로드합니다.

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. 전자 필기장](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft .SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 예 | 예 |
> | DomainServices/oucontainer | 아니요 | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 아니요 | 예 |

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
> | advisorScore | 아니요 | 아니요 |
> | 구성 | 예 | 아니요 |
> | generateRecommendations | 아니요 | 예 |
> | metadata | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | suppressions | 예 | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | 예 | 예 |
> | 경고 | 예 | 아니요 |
> | alertsList | 아니요 | 아니요 |
> | alertsMetaData | 아니요 | 아니요 |
> | alertsSummary | 아니요 | 아니요 |
> | alertsSummaryList | 아니요 | 아니요 |
> | smartDetectorAlertRules | 예 | 예 |
> | smartGroups | 아니요 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 아니요 | 예 |
> | 서비스 | 예 | 예 |
> | validateServiceName | 아니요 | 아니요 |

> [!NOTE]
> Azure API Management는 각 서비스에 대해 최대 15 개의 태그 이름/값 쌍을 만드는 것만 지원 합니다.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 | 예 |
> | configurationStores/eventGridFilters | 아니요 | 아니요 |
> | configurationStores/keyValues | 아니요 | 예 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Spring | 예 | 예 |
> | 스프링/앱 | 아니요 | 아니요 |
> | 스프링/앱/배포 | 아니요 | 아니요 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 예 | 예 |
> | defaultProviders | 아니요 | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | 아니요 | 아니요 |
> | accessReviewScheduleSettings | 아니요 | 아니요 |
> | classicAdministrators | 아니요 | 아니요 |
> | dataAliases | 아니요 | 아니요 |
> | denyAssignments | 아니요 | 아니요 |
> | elevateAccess | 아니요 | 아니요 |
> | findOrphanRoleAssignments | 아니요 | 예 |
> | locks | 예 | 예 |
> | 권한 | 예 | 아니요 |
> | policyAssignments | 아니요 | 아니요 |
> | policyDefinitions | 아니요 | 아니요 |
> | policyExemptions | 아니요 | 아니요 |
> | policySetDefinitions | 아니요 | 아니요 |
> | privateLinkAssociations | 아니요 | 아니요 |
> | providerOperations | 아니요 | 아니요 |
> | resourceManagementPrivateLinks | 예 | 예 |
> | roleAssignments | 아니요 | 아니요 |
> | roleAssignmentsUsageMetrics | 아니요 | 아니요 |
> | roleDefinitions | 아니요 | 아니요 |

## <a name="microsoftautomanage"></a>Microsoft Automanage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | configurationprofil | 아니요 | 아니요 |
> | configurationProfilePreferences 설정 | 예 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 예 | 예 |
> | automationAccounts/구성 | 예 | 예 |
> | automationAccounts/job | 아니요 | 아니요 |
> | automationAccounts/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | automationAccounts/privateEndpointConnections | 아니요 | 아니요 |
> | automationAccounts/privateLinkResources | 아니요 | 아니요 |
> | automationAccounts / runbooks | 예 | 예 |
> | automationAccounts/softwareUpdateConfigurations | 아니요 | 아니요 |
> | automationAccounts / webhooks | 아니요 | 아니요 |

> [!NOTE]
> Azure Automation는 각 자동화 리소스에 대해 최대 15 개의 태그 이름/값 쌍을 만드는 것만 지원 합니다.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | privateClouds | 예 | 예 |
> | privateClouds/권한 부여 | 아니요 | 아니요 |
> | privateClouds/클러스터 | 아니요 | 아니요 |
> | privateClouds / globalReachConnections | 아니요 | 아니요 |
> | privateClouds / hcxEnterpriseSites | 아니요 | 아니요 |
> | privateClouds / workloadNetworks | 아니요 | 아니요 |
> | privateClouds/workloadNetworks/dhcpConfigurations | 아니요 | 아니요 |
> | privateClouds/workloadNetworks/게이트웨이 | 아니요 | 아니요 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 아니요 | 아니요 |
> | privateClouds/workloadNetworks/세그먼트 | 아니요 | 아니요 |
> | privateClouds/workloadNetworks/virtualMachines | 아니요 | 아니요 |
> | privateClouds/workloadNetworks/vmGroups | 아니요 | 아니요 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 아니요 |
> | 환경/계정 | 아니요 | 아니요 |
> | 환경/계정/네임 스페이스 | 아니요 | 아니요 |
> | 환경/계정/네임 스페이스/구성 | 아니요 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 예 | 아니요 |
> | b2ctenants | 예 | 아니요 |
> | guestUsages | 예 | 예 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataControllers | 예 | 예 |
> | postgresInstances | 예 | 예 |
> | sqlManagedInstances | 예 | 예 |
> | sqlServerInstances | 예 | 예 |
> | sqlServerRegistrations | 예 | 예 |
> | sqlServerRegistrations/sqlServers | 아니요 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | 아니요 | 아니요 |
> | edgeSubscriptions | 예 | 예 |
> | registrations | 예 | 예 |
> | 등록/customerSubscriptions | 아니요 | 예 |
> | registrations / products | 예 | 예 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 아니요 | 아니요 |
> | billingAccounts/규약 | 아니요 | 아니요 |
> | billingAccounts / billingPermissions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / billingPermissions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / billingSubscriptions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/고객 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/지침 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/송장 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/청구서/가격표 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/송장/트랜잭션 | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/invoiceSections/products | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 아니요 | 아니요 |
> | billingAccounts / BillingProfiles / patchOperations | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / paymentMethods | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/정책 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/가격표 | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/제품 | 아니요 | 아니요 |
> | billingAccounts/billingProfiles/트랜잭션 | 아니요 | 아니요 |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | 아니요 | 아니요 |
> | billingAccounts / billingRoleAssignments | 아니요 | 아니요 |
> | billingAccounts / billingRoleDefinitions | 아니요 | 아니요 |
> | billingAccounts / billingSubscriptions | 아니요 | 아니요 |
> | billingAccounts/billingSubscriptions/송장 | 아니요 | 아니요 |
> | billingAccounts / createBillingRoleAssignment | 아니요 | 아니요 |
> | billingAccounts / createInvoiceSectionOperations | 아니요 | 아니요 |
> | billingAccounts/고객 | 아니요 | 아니요 |
> | billingAccounts/customers/billingPermissions | 아니요 | 아니요 |
> | billingAccounts/customers/billingSubscriptions | 아니요 | 아니요 |
> | billingAccounts/customers/initiateTransfer | 아니요 | 아니요 |
> | billingAccounts/고객/정책 | 아니요 | 아니요 |
> | billingAccounts/고객/제품 | 아니요 | 아니요 |
> | billingAccounts/고객/트랜잭션 | 아니요 | 아니요 |
> | billingAccounts/고객/전송 | 아니요 | 아니요 |
> | billingAccounts/부서 | 아니요 | 아니요 |
> | billingAccounts/부서/billingPermissions | 아니요 | 아니요 |
> | billingAccounts/부서/billingRoleAssignments | 아니요 | 아니요 |
> | billingAccounts/부서/billingRoleDefinitions | 아니요 | 아니요 |
> | billingAccounts / enrollmentAccounts | 아니요 | 아니요 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 아니요 | 아니요 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 아니요 | 아니요 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 아니요 | 아니요 |
> | billingAccounts/송장 | 아니요 | 아니요 |
> | billingAccounts/송장/트랜잭션 | 아니요 | 아니요 |
> | billingAccounts / invoiceSections | 아니요 | 아니요 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 아니요 | 아니요 |
> | billingAccounts / invoiceSections / billingSubscriptions | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/상승 | 아니요 | 아니요 |
> | billingAccounts / invoiceSections / initiateTransfer | 아니요 | 아니요 |
> | billingAccounts / invoiceSections / patchOperations | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/제품 Moveoperations | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/제품 | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/제품/이전 | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/트랜잭션 | 아니요 | 아니요 |
> | billingAccounts/invoiceSections/전송 | 아니요 | 아니요 |
> | billingAccounts/lineOfCredit | 아니요 | 아니요 |
> | billingAccounts / patchOperations | 아니요 | 아니요 |
> | billingAccounts / paymentMethods | 아니요 | 아니요 |
> | billingAccounts/제품 | 아니요 | 아니요 |
> | billingAccounts/트랜잭션 | 아니요 | 아니요 |
> | billingPeriods | 아니요 | 아니요 |
> | billingPermissions | 아니요 | 아니요 |
> | billingProperty | 아니요 | 아니요 |
> | billingRoleAssignments | 아니요 | 아니요 |
> | billingRoleDefinitions | 아니요 | 아니요 |
> | createBillingRoleAssignment | 아니요 | 예 |
> | departments | 예 | 아니요 |
> | enrollmentAccounts | 아니요 | 예 |
> | invoices | 예 | 예 |
> | transfers | 예 | 아니요 |
> | 전송/acceptTransfer | 아니요 | 아니요 |
> | 전송/declineTransfer | 아니요 | 아니요 |
> | 전송/operationStatus | 아니요 | 아니요 |
> | 전송/validateTransfer | 아니요 | 아니요 |
> | validateAddress | 아니요 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mapApis | 예 | 예 |
> | updateCommunicationPreference | 아니요 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 예 | 예 |
> | cordaMembers | 예 | 예 |
> | watchers | 예 | 예 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | TokenServices | 예 | 예 |
> | TokenServices/BlockchainNetworks | 아니요 | 아니요 |
> | TokenServices/그룹 | 아니요 | 아니요 |
> | TokenServices/그룹/계정 | 아니요 | 아니요 |
> | TokenServices/TokenTemplates | 아니요 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 아니요 | 아니요 |
> | blueprintAssignments / assignmentOperations | 아니요 | 아니요 |
> | blueprintAssignments/작업 | 아니요 | 예 |
> | blueprints | 예 | 예 |
> | blueprints / artifacts | 예 | 예 |
> | blueprints / versions | 예 | 예 |
> | blueprints / versions / artifacts | 예 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | 예 | 예 |
> | botServices / channels | 아니요 | 아니요 |
> | botServices/연결 | 아니요 | 아니요 |
> | 언어 | 아니요 | 아니요 |
> | 템플릿 | 아니요 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | 예 | 예 |
> | Redis/EventGridFilters | 아니요 | 아니요 |
> | Redis/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | Redis/privateEndpointConnectionProxies/validate | 아니요 | 아니요 |
> | Redis/privateEndpointConnections | 아니요 | 아니요 |
> | Redis/privateLinkResources | 아니요 | 아니요 |
> | redisEnterprise | 예 | 예 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 아니요 | 아니요 |
> | autoQuotaIncrease | 아니요 | 아니요 |
> | calculateExchange | 아니요 | 아니요 |
> | calculatePrice | 아니요 | 아니요 |
> | calculatePurchasePrice | 아니요 | 예 |
> | catalogs | 예 | 아니요 |
> | commercialReservationOrders | 아니요 | 예 |
> | 교환 | 예 | 아니요 |
> | placePurchaseOrder | 아니요 | 아니요 |
> | reservationOrders | 아니요 | 아니요 |
> | reservationOrders / calculateRefund | 아니요 | 아니요 |
> | reservationOrders/병합 | 아니요 | 아니요 |
> | reservationOrders/예약 | 아니요 | 아니요 |
> | reservationOrders/예약/수정 버전 | 아니요 | 아니요 |
> | reservationOrders/반환 | 아니요 | 아니요 |
> | reservationOrders/분할 | 아니요 | 아니요 |
> | reservationOrders/교환 | 아니요 | 예 |
> | reservations | 예 | 아니요 |
> | resourceProviders | 아니요 | 예 |
> | 리소스 | 예 | 아니요 |
> | validateReservationOrder | 아니요 | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 아니요 | 아니요 |
> | CdnWebApplicationFirewallPolicies | 예 | 예 |
> | edgenodes | 예 | 예 |
> | 프로필 | 예 | 예 |
> | profiles / endpoints | 예 | 예 |
> | profiles / endpoints / customdomains | 예 | 예 |
> | profiles / endpoints / origingroups | 예 | 예 |
> | profiles / endpoints / origins | 예 | 아니요 |
> | validateProbe | 아니요 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 예 | 예 |
> | certificateOrders/certificate | 아니요 | 아니요 |
> | validateCertificateRegistrationInformation | 아니요 | 아니요 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | profile | 아니요 | 아니요 |
> | resourceChanges | 아니요 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 아니요 |
> | domainNames | 아니요 | 아니요 |
> | domainNames/기능 | 아니요 | 아니요 |
> | domainNames/internalLoadBalancers 장치 | 아니요 | 아니요 |
> | domainNames/serviceCertificates | 아니요 | 아니요 |
> | domainNames/슬롯 | 아니요 | 아니요 |
> | domainNames/슬롯/역할 | 아니요 | 아니요 |
> | domainNames/슬롯/역할/metricDefinitions | 아니요 | 아니요 |
> | domainNames/슬롯/역할/메트릭 | 아니요 | 아니요 |
> | moveSubscriptionResources | 아니요 | 아니요 |
> | operatingSystemFamilies | 아니요 | 아니요 |
> | operatingSystems | 아니요 | 예 |
> | quotas | 예 | 아니요 |
> | resourceTypes | 아니요 | 아니요 |
> | validateSubscriptionMoveAvailability | 아니요 | 아니요 |
> | virtualMachines | 아니요 | 아니요 |
> | virtualMachines/diagnosticSettings | 아니요 | 아니요 |
> | virtualMachines/metricDefinitions | 아니요 | 아니요 |
> | virtualMachines/메트릭 | 아니요 | 아니요 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 아니요 | 아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 아니요 |
> | expressRouteCrossConnections | 아니요 | 아니요 |
> | expressRouteCrossConnections/피어 링 | 아니요 | 아니요 |
> | gatewaySupportedDevices | 아니요 | 아니요 |
> | networkSecurityGroups | 아니요 | 예 |
> | quotas | 예 | 아니요 |
> | reservedIps | 아니요 | 아니요 |
> | virtualNetworks | 아니요 | 아니요 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 아니요 | 아니요 |
> | virtualNetworks / virtualNetworkPeerings | 아니요 | 아니요 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 예 | 예 |
> | disks | 예 | 예 |
> | images | 예 | 아니요 |
> | osImages | 아니요 | 아니요 |
> | osPlatformImages | 아니요 | 아니요 |
> | publicImages | 아니요 | 예 |
> | quotas | 예 | 아니요 |
> | storageAccounts | 아니요 | 아니요 |
> | storageAccounts / blobServices | 아니요 | 아니요 |
> | storageAccounts / fileServices | 아니요 | 아니요 |
> | storageAccounts/metricDefinitions | 아니요 | 아니요 |
> | storageAccounts/메트릭 | 아니요 | 아니요 |
> | storageAccounts/queueServices | 아니요 | 아니요 |
> | storageAccounts/서비스 | 아니요 | 아니요 |
> | storageAccounts/services/diagnosticSettings | 아니요 | 아니요 |
> | storageAccounts/services/metricDefinitions | 아니요 | 아니요 |
> | storageAccounts/서비스/메트릭 | 아니요 | 아니요 |
> | storageAccounts/tableServices | 아니요 | 아니요 |
> | storageAccounts/vmImages | 아니요 | 아니요 |
> | vmImages | 아니요 | 아니요 |

## <a name="microsoftcodespaces"></a>Microsoft Codespaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 플랜 | 예 | 예 |
> | registeredSubscriptions | 아니요 | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | 계정/privateEndpointConnections | 아니요 | 아니요 |
> | 계정/privateLinkResources | 아니요 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 아니요 | 아니요 |
> | UsageAggregates | 아니요 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 예 | 예 |
> | cloudServices | 예 | 예 |
> | diskAccesses | 예 | 예 |
> | diskEncryptionSets | 예 | 예 |
> | disks | 예 | 예 |
> | galleries | 예 | 예 |
> | galleries / applications | 아니요 | 아니요 |
> | 갤러리/응용 프로그램/버전 | 아니요 | 예 |
> | galleries / images | 예 | 예 |
> | galleries / images / versions | 예 | 아니요 |
> | 호스트 그룹 | 예 | 예 |
> | 호스트 그룹/호스트 | 예 | 예 |
> | images | 예 | 예 |
> | proximityPlacementGroups | 예 | 예 |
> | restorePointCollections | 예 | 예 |
> | restorePointCollections / restorePoints | 아니요 | 아니요 |
> | sharedVMExtensions | 예 | 예 |
> | sharedVMExtensions/버전 | 아니요 | 아니요 |
> | sharedVMImages | 예 | 예 |
> | sharedVMImages/버전 | 아니요 | 예 |
> | 스냅샷 | 예 | 예 |
> | sshPublicKeys | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | virtualMachines/확장 | 예 | 예 |
> | virtualMachines/metricDefinitions | 아니요 | 아니요 |
> | virtualMachines/runCommands | 예 | 예 |
> | virtualMachineScaleSets | 예 | 예 |
> | virtualMachineScaleSets/확장 | 아니요 | 아니요 |
> | virtualMachineScaleSets/networkInterfaces | 아니요 | 아니요 |
> | virtualMachineScaleSets/publicIPAddresses | 아니요 | 아니요 |
> | virtualMachineScaleSets/virtualMachines | 아니요 | 아니요 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 아니요 | 아니요 |

> [!NOTE]
> 일반화 된 것으로 표시 된 가상 컴퓨터에는 태그를 추가할 수 없습니다. [New-azvm-일반화](/powershell/module/Az.Compute/Set-AzVM) 또는 [az vm generalize](/cli/azure/vm#az-vm-generalize)를 사용 하 여 가상 머신을 일반화 된 것으로 표시 합니다.

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CacheNodes | 예 | 예 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 아니요 | 아니요 |
> | 잔액 | 아니요 | 아니요 |
> | 예산 | 아니요 | 아니요 |
> | Charges | 아니요 | 아니요 |
> | CostTags | 아니요 | 예 |
> | credits | 예 | 예 |
> | events | 예 | 아니요 |
> | 예측 | 아니요 | 예 |
> | lots | 예 | 아니요 |
> | Marketplace | 아니요 | 아니요 |
> | Pricesheets | 아니요 | 예 |
> | products | 예 | 아니요 |
> | ReservationDetails | 아니요 | 아니요 |
> | ReservationRecommendationDetails | 아니요 | 아니요 |
> | ReservationRecommendations | 아니요 | 아니요 |
> | ReservationSummaries | 아니요 | 아니요 |
> | ReservationTransactions | 아니요 | 아니요 |
> | 태그 | 아니요 | 예 |
> | tenants | 예 | 아니요 |
> | 용어 | 아니요 | 아니요 |
> | UsageDetails | 아니요 | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 예 | 예 |
> | serviceAssociationLinks | 아니요 | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registries | 예 | 예 |
> | 레지스트리/agentPools | 예 | 예 |
> | registries / builds | 예 | 예 |
> | registries / builds / cancel | 예 | 아니요 |
> | 레지스트리/빌드/getLogLink | 아니요 | 아니요 |
> | registries / buildTasks | 예 | 예 |
> | 레지스트리/buildTasks/단계 | 아니요 | 아니요 |
> | 레지스트리/eventGridFilters | 아니요 | 아니요 |
> | 레지스트리/exportPipelines | 아니요 | 아니요 |
> | 레지스트리/generateCredentials | 아니요 | 아니요 |
> | 레지스트리/getBuildSourceUploadUrl | 아니요 | 아니요 |
> | 레지스트리/GetCredentials | 아니요 | 아니요 |
> | 레지스트리/importImage | 아니요 | 아니요 |
> | 레지스트리/importPipelines | 아니요 | 아니요 |
> | 레지스트리/pipelineRuns | 아니요 | 아니요 |
> | 레지스트리/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | 레지스트리/privateEndpointConnectionProxies/유효성 검사 | 아니요 | 아니요 |
> | 레지스트리/privateEndpointConnections | 아니요 | 아니요 |
> | 레지스트리/privateLinkResources | 아니요 | 아니요 |
> | 레지스트리/queueBuild | 아니요 | 아니요 |
> | 레지스트리/regenerateCredential | 아니요 | 아니요 |
> | 레지스트리/regenerateCredentials | 아니요 | 예 |
> | registries / replications | 예 | 예 |
> | registries / runs | 예 | 예 |
> | registries / runs / cancel | 예 | 아니요 |
> | 레지스트리/scheduleRun | 아니요 | 아니요 |
> | registries / scopeMaps | 아니요 | 아니요 |
> | 레지스트리/taskRuns | 아니요 | 예 |
> | registries / tasks | 예 | 예 |
> | registries / tokens | 예 | 아니요 |
> | 레지스트리/updatePolicies | 아니요 | 예 |
> | registries / webhooks | 예 | 예 |
> | registry/웹 후크/getcallbackconfig | 아니요 | 예 |
> | registries / webhooks / ping | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | 예 | 예 |
> | managedClusters | 예 | 예 |
> | openShiftManagedClusters | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 경고 | 아니요 | 아니요 |
> | BillingAccounts | 아니요 | 아니요 |
> | 예산 | 아니요 | 아니요 |
> | CloudConnectors | 아니요 | 아니요 |
> | 커넥터 | 예 | 예 |
> | costAllocationRules | 아니요 | 아니요 |
> | Departments | 아니요 | 아니요 |
> | 차원 | 아니요 | 아니요 |
> | EnrollmentAccounts | 아니요 | 아니요 |
> | 내보내기 | 아니요 | 아니요 |
> | ExternalBillingAccounts | 아니요 | 아니요 |
> | ExternalBillingAccounts/경고 | 아니요 | 아니요 |
> | ExternalBillingAccounts/차원 | 아니요 | 아니요 |
> | ExternalBillingAccounts/예측 | 아니요 | 아니요 |
> | ExternalBillingAccounts/쿼리 | 아니요 | 아니요 |
> | ExternalSubscriptions | 아니요 | 아니요 |
> | ExternalSubscriptions/Alerts | 아니요 | 아니요 |
> | ExternalSubscriptions/차원 | 아니요 | 아니요 |
> | ExternalSubscriptions/예측 | 아니요 | 아니요 |
> | ExternalSubscriptions/Query | 아니요 | 아니요 |
> | 예측 | 아니요 | 아니요 |
> | 자세한 정보 | 아니요 | 아니요 |
> | 쿼리 | 아니요 | 예 |
> | register | 예 | 아니요 |
> | Reportconfigs | 아니요 | 아니요 |
> | 보고서 | 아니요 | 아니요 |
> | 설정 | 아니요 | 아니요 |
> | showbackRules | 아니요 | 아니요 |
> | 뷰 | 아니요 | 예 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | requests | 예 | 예 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | associations | 예 | 아니요 |
> | resourceProviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | workspace/dbWorkspaces | 아니요 | 아니요 |
> | 작업 영역/storageEncryption | 아니요 | 아니요 |
> | 작업 영역/virtualNetworkPeerings | 아니요 | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 예 | 예 |
> | dataFactories/diagnosticSettings | 아니요 | 아니요 |
> | dataFactories/metricDefinitions | 아니요 | 아니요 |
> | dataFactorySchema | 아니요 | 예 |
> | factories | 예 | 예 |
> | factories / integrationRuntimes | 아니요 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / dataLakeStoreAccounts | 아니요 | 아니요 |
> | accounts / storageAccounts | 아니요 | 아니요 |
> | 계정/storageAccounts/컨테이너 | 아니요 | 아니요 |
> | 계정/transferAnalyticsUnits | 아니요 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/eventGridFilters | 아니요 | 아니요 |
> | accounts / firewallRules | 아니요 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |
> | services / projects | 예 | 예 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | BackupVaults | 예 | 예 |
> | ResourceOperationGateKeepers 키퍼 | 예 | 예 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | accounts / shares | 예 | 예 |
> | accounts / shares / datasets | 예 | 예 |
> | accounts / shares / invitations | 예 | 예 |
> | accounts / shares / providersharesubscriptions | 예 | 아니요 |
> | 계정/공유/synchronizationSettings | 아니요 | 예 |
> | accounts / sharesubscriptions | 예 | 아니요 |
> | 계정/sharesubscriptions/consumerSourceDataSets | 아니요 | 예 |
> | accounts / sharesubscriptions / datasetmappings | 예 | 예 |
> | accounts / sharesubscriptions / triggers | 예 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 아니요 |
> | servers/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | servers/privateEndpointConnections | 아니요 | 아니요 |
> | servers/privateLinkResources | 아니요 | 아니요 |
> | 서버/queryTexts | 아니요 | 아니요 |
> | servers/recoverableServers | 아니요 | 아니요 |
> | servers/topQueryStatistics | 아니요 | 아니요 |
> | servers / virtualNetworkRules | 아니요 | 아니요 |
> | 서버/waitStatistics | 아니요 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 아니요 |
> | servers/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | servers/privateEndpointConnections | 아니요 | 아니요 |
> | servers/privateLinkResources | 아니요 | 아니요 |
> | 서버/queryTexts | 아니요 | 아니요 |
> | servers/recoverableServers | 아니요 | 아니요 |
> | servers/topQueryStatistics | 아니요 | 아니요 |
> | servers / virtualNetworkRules | 아니요 | 아니요 |
> | 서버/waitStatistics | 아니요 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | flexibleServers | 예 | 예 |
> | serverGroups | 예 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 아니요 |
> | servers/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | servers/privateEndpointConnections | 아니요 | 아니요 |
> | servers/privateLinkResources | 아니요 | 아니요 |
> | 서버/queryTexts | 아니요 | 아니요 |
> | servers/recoverableServers | 아니요 | 아니요 |
> | servers/topQueryStatistics | 아니요 | 아니요 |
> | servers / virtualNetworkRules | 아니요 | 아니요 |
> | 서버/waitStatistics | 아니요 | 예 |
> | serversv2 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 예 | 예 |
> | rollouts | 예 | 예 |
> | serviceTopologies | 예 | 예 |
> | serviceTopologies/서비스 | 예 | 예 |
> | serviceTopologies/서비스/serviceUnits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 예 | 예 |
> | applicationgroups / applications | 예 | 예 |
> | applicationgroups / desktops | 예 | 예 |
> | applicationgroups / startmenuitems | 예 | 예 |
> | hostpools | 예 | 예 |
> | hostpools / sessionhosts | 예 | 예 |
> | hostpools / sessionhosts / usersessions | 예 | 예 |
> | hostpools / usersessions | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 예 | 예 |
> | ElasticPools / IotHubTenants | 예 | 예 |
> | ElasticPools/IotHubTenants/securitySettings | 아니요 | 아니요 |
> | IotHubs | 예 | 예 |
> | IotHubs/eventGridFilters | 아니요 | 아니요 |
> | IotHubs/securitySettings | 아니요 | 아니요 |
> | ProvisioningServices | 예 | 예 |
> | usages | 예 | 아니요 |

## <a name="microsoftdeviceupdate"></a>Microsoft DeviceUpdate

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/인스턴스 | 예 | 예 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | pipelines | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | 예 | 예 |
> | labs | 예 | 예 |
> | labs / environments | 예 | 예 |
> | labs/serviceRunners | 예 | 예 |
> | labs/virtualMachines | 예 | 예 |
> | schedules | 예 | 예 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | 예 | 예 |
> | digitalTwinsInstances/끝점 | 아니요 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 아니요 | 아니요 |
> | databaseAccounts | 예 | 예 |
> | restorableDatabaseAccounts | 아니요 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인/도메인 소유자의 식별자 | 아니요 | 아니요 |
> | generateSsoRequest | 아니요 | 아니요 |
> | topLevelDomains | 아니요 | 아니요 |
> | validateDomainRegistrationInformation | 아니요 | 아니요 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 아니요 | 아니요 |
> | lcsprojects/clouddeployments | 아니요 | 아니요 |
> | lcsprojects/커넥터 | 아니요 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | domains / topics | 예 | 아니요 |
> | eventSubscriptions | 아니요 | 아니요 |
> | extensionTopics | 아니요 | 아니요 |
> | 네임 스페이스 | 예 | 예 |
> | 네임 스페이스/a s p 채널 | 아니요 | 아니요 |
> | 파트너 등록 | 예 | 예 |
> | 항목 항목 | 예 | 예 |
> | 항목 토픽/eventSubscriptions | 아니요 | 아니요 |
> | systemTopics | 예 | 예 |
> | systemTopics/eventSubscriptions | 아니요 | 예 |
> | topics | 예 | 예 |
> | topicTypes | 아니요 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / disasterrecoveryconfigs | 예 | 예 |
> | namespaces / eventhubs | 예 | 예 |
> | namespaces / eventhubs / authorizationrules | 예 | 예 |
> | namespaces / eventhubs / consumergroups | 예 | 예 |
> | namespaces / networkrulesets | 예 | 예 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | 예 | 예 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | featureProviders | 아니요 | 예 |
> | 기능 | 예 | 예 |
> | providers | 예 | 아니요 |
> | subscriptionFeatureRegistrations | 아니요 | 아니요 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | enroll | 아니요 | 아니요 |
> | galleryitems | 아니요 | 아니요 |
> | generateartifactaccessuri | 아니요 | 아니요 |
> | myareas | 아니요 | 아니요 |
> | myareas/areas | 아니요 | 아니요 |
> | myareas/areas/areas | 아니요 | 아니요 |
> | myareas/areas/areas/galleryitems | 아니요 | 아니요 |
> | myareas/areas/galleryitems | 아니요 | 아니요 |
> | myareas/galleryitems | 아니요 | 예 |
> | register | 예 | 예 |
> | 리소스 | 예 | 아니요 |
> | retrieveresourcesbyid | 아니요 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | 예 | 예 |
> | autoManagedVmConfigurationProfiles | 예 | 예 |
> | configurationprofil | 아니요 | 아니요 |
> | guestConfigurationAssignments | 아니요 | 예 |
> | software | 예 | 아니요 |
> | softwareUpdateProfile | 아니요 | 아니요 |
> | 업데이트 | 아니요 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 예 | 예 |
> | sapMonitors | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | clusters / applications | 예 | 아니요 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |
> | 서비스/iomtconnectors | 아니요 | 아니요 |
> | 서비스/iomtconnectors/연결 | 아니요 | 아니요 |
> | services/iomtconnectors/매핑 | 아니요 | 아니요 |
> | 서비스/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | 서비스/privateEndpointConnections | 예 | 예 |
> | 서비스/privateLinkResources | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | machines | 예 | 예 |
> | machines / extensions | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 예 | 예 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 디바이스 | 예 | 예 |
> | registeredSubscriptions | 아니요 | 아니요 |
> | 판매 | 아니요 | 아니요 |
> | 공급 업체/vendorskus | 아니요 | 아니요 |
> | 공급 업체/vendorskus/previewSubscriptions | 아니요 | 아니요 |
> | virtualnetworkfunctions | 예 | 예 |
> | virtualnetworkfunctionvendors | 아니요 | 예 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | networkScopes | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionGroups | 예 | 예 |
> | activityLogAlerts | 예 | 예 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | components | 예 | 예 |
> | 구성 요소/linkedStorageAccounts | 아니요 | 아니요 |
> | 구성 요소/ProactiveDetectionConfigs | 아니요 | 아니요 |
> | diagnosticSettings | 아니요 | 아니요 |
> | 종류 | 예 | 예 |
> | 찾은 guestdiagnosticsettingsassociation | 예 | 예 |
> | logprofiles | 예 | 예 |
> | metricAlerts | 예 | 예 |
> | privateLinkScopes | 예 | 예 |
> | privateLinkScopes / privateEndpointConnections | 아니요 | 아니요 |
> | privateLinkScopes / scopedResources | 아니요 | 아니요 |
> | queryPacks | 예 | 예 |
> | queryPacks/쿼리 | 아니요 | 아니요 |
> | scheduledQueryRules | 예 | 예 |
> | webtests | 예 | 예 |
> | workbooks | 예 | 예 |
> | workbooktemplates | 예 | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니요 | 아니요 |
> | diagnosticSettingsCategories | 아니요 | 아니요 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 아니요 | 아니요 |
> | IoTApps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 아니요 | 아니요 |
> | hsmPools | 예 | 예 |
> | managedHSMs | 예 | 예 |
> | vaults | 예 | 예 |
> | 자격 증명 모음/Accesspolicy | 아니요 | 아니요 |
> | 자격 증명 모음/eventGridFilters | 아니요 | 예 |
> | vaults / secrets | 예 | 아니요 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | connectedClusters | 예 | 예 |
> | registeredSubscriptions | 아니요 | 예 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 확장 | 아니요 | 아니요 |
> | sourceControlConfigurations | 아니요 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | clusters / attacheddatabaseconfigurations | 예 | 예 |
> | clusters / databases | 예 | 예 |
> | clusters / databases / dataconnections | 예 | 예 |
> | clusters / databases / eventhubconnections | 예 | 예 |
> | clusters / databases / principalassignments | 예 | 아니요 |
> | 클러스터/dataconnections | 아니요 | 예 |
> | clusters / principalassignments | 예 | 아니요 |
> | 클러스터/sharedidentities | 아니요 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 예 | 예 |
> | users | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 예 | 예 |
> | integrationAccounts | 예 | 예 |
> | integrationServiceEnvironments | 예 | 예 |
> | integrationServiceEnvironments / managedApis | 아니요 | 아니요 |
> | isolatedEnvironments | 예 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 예 | 예 |
> | webServices | 예 | 예 |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | workspaces / computes | 예 | 아니요 |
> | workspace/eventGridFilters | 아니요 | 아니요 |
> | 작업 영역/inferenceEndpoints | 예 | 예 |
> | 작업 영역/inferenceEndpoints/배포 | 예 | 예 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 아니요 | 예 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applyUpdates | 아니요 | 아니요 |
> | configurationAssignments | 아니요 | 아니요 |
> | maintenanceConfigurations | 예 | 예 |
> | publicMaintenanceConfigurations | 아니요 | 아니요 |
> | updates | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Identities | 아니요 | 아니요 |
> | userAssignedIdentities | 예 | 예 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedNetworks | 예 | 예 |
> | managedNetworks/managedNetworkGroups | 예 | 예 |
> | managedNetworks/Managednetworkpeering정책만 | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 아니요 | 아니요 |
> | registrationAssignments | 아니요 | 아니요 |
> | registrationDefinitions | 아니요 | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 아니요 | 아니요 |
> | managementGroups | 아니요 | 아니요 |
> | managementGroups/settings | 아니요 | 예 |
> | 리소스 | 예 | 아니요 |
> | startTenantBackfill | 아니요 | 아니요 |
> | tenantBackfillStatus | 아니요 | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/eventGridFilters | 아니요 | 아니요 |
> | 계정/privateAtlases | 예 | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | macc | 아니요 | 예 |
> | offers | 예 | 아니요 |
> | offerTypes | 아니요 | 아니요 |
> | offerTypes/게시자 | 아니요 | 아니요 |
> | offerTypes/게시자/제안 | 아니요 | 아니요 |
> | offerTypes/게시자/제품/계획 | 아니요 | 아니요 |
> | offerTypes/게시자/제품/계획/규약 | 아니요 | 아니요 |
> | offerTypes/게시자/제품/계획/configs | 아니요 | 아니요 |
> | offerTypes/publishers/제품/계획/configs/importImage | 아니요 | 예 |
> | privategalleryitems | 예 | 아니요 |
> | privateStoreClient | 아니요 | 아니요 |
> | privateStores | 아니요 | 아니요 |
> | privateStores/제안 | 아니요 | 예 |
> | products | 예 | 예 |
> | publishers | 예 | 예 |
> | publishers / offers | 예 | 예 |
> | publishers / offers / amendments | 예 | 예 |
> | register | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 예 | 예 |
> | updateCommunicationPreference | 아니요 | 예 |

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
> | mediaservices | 예 | 예 |
> | windowsazure.mediaservices/accountFilters | 아니요 | 예 |
> | mediaservices / assets | 예 | 아니요 |
> | windowsazure.mediaservices/asset/assetFilters | 아니요 | 아니요 |
> | windowsazure.mediaservices/Contentkeypolicy | 아니요 | 아니요 |
> | windowsazure.mediaservices/eventGridFilters | 아니요 | 아니요 |
> | windowsazure.mediaservices/liveEventOperations | 아니요 | 아니요 |
> | mediaservices / liveEvents | 예 | 예 |
> | mediaservices / liveEvents / liveOutputs | 아니요 | 아니요 |
> | windowsazure.mediaservices/liveOutputOperations | 아니요 | 아니요 |
> | windowsazure.mediaservices/mediaGraphs | 아니요 | 아니요 |
> | windowsazure.mediaservices/privateEndpointConnectionOperations | 아니요 | 아니요 |
> | windowsazure.mediaservices/privateEndpointConnectionProxies | 아니요 | 아니요 |
> | windowsazure.mediaservices/privateEndpointConnections | 아니요 | 아니요 |
> | windowsazure.mediaservices/streamingEndpointOperations | 아니요 | 아니요 |
> | mediaservices / streamingEndpoints | 예 | 예 |
> | windowsazure.mediaservices/streamingLocators | 아니요 | 아니요 |
> | windowsazure.mediaservices/streamingPolicies | 아니요 | 예 |
> | mediaservices / transforms | 예 | 예 |
> | mediaservices / transforms / jobs | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appClusters | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | moveCollections | 예 | 예 |
> | projects | 예 | 예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 예 | 예 |
> | objectUnderstandingAccounts | 예 | 예 |
> | remoteRenderingAccounts | 예 | 예 |
> | spatialAnchorsAccounts | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 예 | 아니요 |
> | netAppAccounts/accountBackups | 아니요 | 아니요 |
> | netAppAccounts/capacityPools | 예 | 아니요 |
> | netAppAccounts/capacityPools/볼륨 | 예 | 아니요 |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | 아니요 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 예 | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 | 예 |
> | applicationSecurityGroups | 예 | 예 |
> | azureFirewallFqdnTags | 아니요 | 아니요 |
> | azureFirewalls | 예 | 아니요 |
> | bastionHosts | 예 | 아니요 |
> | bgpServiceCommunities | 아니요 | 예 |
> | connections | 예 | 예 |
> | ddosCustomPolicies | 예 | 예 |
> | ddosProtectionPlans | 예 | 예 |
> | dnsOperationStatuses | 아니요 | 예 |
> | dnszones | 예 | 예 |
> | dnszones/A | 아니요 | 아니요 |
> | dnszones/AAAA | 아니요 | 예 |
> | dnszones / all | 예 | 아니요 |
> | dnszones/CAA | 아니요 | 아니요 |
> | dnszones/CNAME | 아니요 | 아니요 |
> | dnszones/MX | 아니요 | 아니요 |
> | dnszones/NS | 아니요 | 아니요 |
> | dnszones/PTR | 아니요 | 예 |
> | dnszones / recordsets | 예 | 아니요 |
> | dnszones/SOA | 아니요 | 아니요 |
> | dnszones/SRV | 아니요 | 아니요 |
> | dnszones/TXT | 아니요 | 아니요 |
> | expressRouteCircuits | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | expressRouteGateways | 예 | 예 |
> | expressRoutePorts | 예 | 예 |
> | expressRouteServiceProviders | 아니요 | 아니요 |
> | firewallPolicies | 예 | 예 |
> | frontdoors | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예, 제한 ( [아래 참고](#frontdoor)참조) | 아니요 |
> | frontdoorWebApplicationFirewallPolicies | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | getDnsResourceReference | 아니요 | 아니요 |
> | internalNotify | 아니요 | 아니요 |
> | loadBalancers | 예 | 예 |
> | localNetworkGateways | 예 | 예 |
> | natGateways | 예 | 예 |
> | networkIntentPolicies | 예 | 예 |
> | networkInterfaces | 예 | 예 |
> | networkProfiles | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | networkWatchers | 예 | 예 |
> | networkWatchers/connectionMonitors | 예 | 아니요 |
> | networkWatchers/flowLogs | 아니요 | 아니요 |
> | networkWatchers/lenses | 예 | 아니요 |
> | networkWatchers/ | 예 | 아니요 |
> | p2sVpnGateways | 예 | 예 |
> | privateDnsOperationStatuses | 아니요 | 아니요 |
> | privateDnsZones | 예 | 예 |
> | privateDnsZones/A | 아니요 | 아니요 |
> | privateDnsZones/AAAA | 아니요 | 아니요 |
> | privateDnsZones/모두 | 아니요 | 아니요 |
> | privateDnsZones/CNAME | 아니요 | 아니요 |
> | privateDnsZones/MX | 아니요 | 아니요 |
> | privateDnsZones/PTR | 아니요 | 아니요 |
> | privateDnsZones/SOA | 아니요 | 아니요 |
> | privateDnsZones/SRV | 아니요 | 아니요 |
> | privateDnsZones/TXT | 아니요 | 아니요 |
> | privateDnsZones / virtualNetworkLinks | 예 | 예 |
> | privateEndpoints | 예 | 예 |
> | privateLinkServices | 예 | 예 |
> | publicIPAddresses | 예 | 예 |
> | publicIPPrefixes | 예 | 예 |
> | routeFilters | 예 | 예 |
> | routeTables | 예 | 예 |
> | serviceEndpointPolicies | 예 | 예 |
> | trafficManagerGeographicHierarchies | 아니요 | 예 |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles/heatMaps | 아니요 | 아니요 |
> | trafficManagerUserMetricsKeys | 아니요 | 아니요 |
> | virtualHubs | 예 | 예 |
> | virtualNetworkGateways | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworks/서브넷 | 아니요 | 아니요 |
> | virtualNetworkTaps | 예 | 예 |
> | virtualWans | 예 | 아니요 |
> | vpnGateways | 예 | 예 |
> | vpnSites | 예 | 예 |
> | webApplicationFirewallPolicies | 예 | 예 |

<a id="frontdoor"></a>

> [!NOTE]
> Azure Front 도어 서비스의 경우 리소스를 만들 때 태그를 적용할 수 있지만, 태그를 업데이트 하거나 추가 하는 기능은 현재 지원 되지 않습니다.


## <a name="microsoftnotebooks"></a>Microsoft. 전자 필기장

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | 아니요 | 아니요 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 아니요 |
> | namespaces / notificationHubs | 예 | 예 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | osNamespaces 스페이스 | 예 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 예 | 예 |
> | ImportSites | 예 | 예 |
> | MasterSites | 예 | 예 |
> | ServerSites | 예 | 예 |
> | VMwareSites | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | deletedWorkspaces | 아니요 | 아니요 |
> | linkTargets | 아니요 | 아니요 |
> | storageInsightConfigs | 아니요 | 예 |
> | workspaces | 예 | 예 |
> | 작업 영역/데이터 내보내기 | 아니요 | 아니요 |
> | 작업 영역/데이터 원본 | 아니요 | 아니요 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 아니요 | 아니요 |
> | 작업 영역/linkedStorageAccounts | 아니요 | 예 |
> | workspaces / metadata | 예 | 예 |
> | workspaces / query | 예 | 아니요 |
> | 작업 영역/scopedPrivateLinkProxies | 아니요 | 아니요 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 예 | 예 |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 아니요 | 아니요 |
> | peerAsns | 아니요 | 예 |
> | peerings | 예 | 예 |
> | peeringServiceCountries | 아니요 | 아니요 |
> | peeringServiceProviders | 아니요 | 아니요 |
> | peeringServices | 예 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 증명 | 아니요 | 아니요 |
> | policyEvents | 아니요 | 아니요 |
> | policyMetadata | 아니요 | 아니요 |
> | policyStates | 아니요 | 아니요 |
> | policyTrackedResources | 아니요 | 예 |
> | remediations | 예 | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 예 | 예 |
> | dashboards | 예 | 예 |
> | userSettings | 아니요 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | 예 | 예 |
> | tenants | 예 | 예 |
> | 테 넌 트/작업 영역 | 아니요 | 아니요 |
> | workspaceCollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capacities | 예 | 예 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 아니요 | 아니요 |
> | providerRegistrations/defaultRollouts | 아니요 | 아니요 |
> | providerRegistrations resourceTypeRegistrations | 아니요 | 예 |
> | rollouts | 예 | 예 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 아니요 | 예 |
> | vaults | 예 | 예 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / hybridconnections | 예 | 예 |
> | namespaces / hybridconnections / authorizationrules | 예 | 아니요 |
> | 네임 스페이스/privateEndpointConnections | 아니요 | 예 |
> | namespaces / wcfrelays | 예 | 예 |
> | namespaces / wcfrelays / authorizationrules | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | 예 | 예 |
> | resourceChangeDetails | 아니요 | 아니요 |
> | resourceChanges | 아니요 | 예 |
> | 리소스 | 예 | 아니요 |
> | 자원 기록 | 아니요 | 아니요 |
> | subscriptionsStatus | 아니요 | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 아니요 | 아니요 |
> | childAvailabilityStatuses | 아니요 | 아니요 |
> | childResources | 아니요 | 예 |
> | emergingissues | 예 | 예 |
> | events | 예 | 아니요 |
> | impactedResources | 아니요 | 예 |
> | metadata | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | 아니요 | 예 |
> | 배포 | 예 | 예 |
> | deployments / operations | 예 | 아니요 |
> | deploymentScripts | 예 | 예 |
> | deploymentScripts/logs | 아니요 | 예 |
> | 링크 | 예 | 아니요 |
> | notifyResourceJobs | 아니요 | 예 |
> | providers | 예 | 아니요 |
> | resourceGroups | 예 | 예 |
> | 구독 | 예 | 아니요 |
> | templateSpecs | 예 | 예 |
> | templateSpecs/버전 | 예 | 예 |
> | tenants | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | saasresources | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 아니요 | 아니요 |
> | searchServices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 아니요 | 아니요 |
> | advancedThreatProtectionSettings | 아니요 | 예 |
> | 경고 | 예 | 아니요 |
> | alertsSuppressionRules | 아니요 | 아니요 |
> | allowedConnections | 아니요 | 아니요 |
> | applicationWhitelistings | 아니요 | 아니요 |
> | assessmentMetadata | 아니요 | 예 |
> | assessments | 예 | 아니요 |
> | autoDismissAlertsRules | 아니요 | 예 |
> | automations | 예 | 예 |
> | AutoProvisioningSettings | 아니요 | 아니요 |
> | Compliances | 아니요 | 예 |
> | 커넥터 | 아니요 | 아니요 |
> | dataCollectionAgents | 아니요 | 아니요 |
> | deviceSecurityGroups | 아니요 | 아니요 |
> | discoveredSecuritySolutions | 아니요 | 아니요 |
> | externalSecuritySolutions | 아니요 | 아니요 |
> | InformationProtectionPolicies | 아니요 | 아니요 |
> | iotSecuritySolutions | 예 | 예 |
> | IanalyticsModels Securitysolutions/ | 아니요 | 아니요 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 아니요 | 아니요 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 아니요 | 아니요 |
> | iotSecuritySolutions/I이상 경고 | 아니요 | 아니요 |
> | iotSecuritySolutions/Iotsecuritysolutions | 아니요 | 아니요 |
> | iotSecuritySolutions/I이상 권장 사항 | 아니요 | 아니요 |
> | IiotRecommendationTypes Securitysolutions/ | 아니요 | 아니요 |
> | jitNetworkAccessPolicies | 아니요 | 아니요 |
> | jitPolicies | 아니요 | 예 |
> | 정책 | 예 | 예 |
> | pricings | 예 | 아니요 |
> | regulatoryComplianceStandards | 아니요 | 아니요 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 아니요 | 아니요 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 아니요 | 아니요 |
> | secureScoreControlDefinitions | 아니요 | 아니요 |
> | secureScoreControls | 아니요 | 아니요 |
> | secureScores | 아니요 | 아니요 |
> | secureScores / secureScoreControls | 아니요 | 아니요 |
> | securityContacts | 아니요 | 아니요 |
> | securitySolutions | 아니요 | 아니요 |
> | securitySolutionsReferenceData | 아니요 | 아니요 |
> | securityStatuses | 아니요 | 아니요 |
> | securityStatusesSummaries | 아니요 | 아니요 |
> | serverVulnerabilityAssessments | 아니요 | 예 |
> | 설정 | 예 | 아니요 |
> | 하위 평가 | 아니요 | 예 |
> | tasks | 예 | 예 |
> | topologies | 예 | 아니요 |
> | workspaceSettings | 아니요 | 아니요 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니요 | 아니요 |
> | diagnosticSettingsCategories | 아니요 | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aggregations | 예 | 아니요 |
> | alertRules | 아니요 | 아니요 |
> | alertRuleTemplates | 아니요 | 아니요 |
> | automationRules | 아니요 | 예 |
> | bookmarks | 예 | 예 |
> | cases | 예 | 아니요 |
> | dataConnectors | 아니요 | 아니요 |
> | dataConnectorsCheckRequirements | 아니요 | 예 |
> | 엔터티 | 예 | 아니요 |
> | entityQueries | 아니요 | 예 |
> | incidents | 예 | 아니요 |
> | officeConsents | 아니요 | 예 |
> | 설정 | 예 | 아니요 |
> | threatIntelligence | 아니요 | 아니요 |
> | watchlists | 아니요 | 예 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoleServices | 아니요 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / disasterrecoveryconfigs | 예 | 예 |
> | namespaces / eventgridfilters | 예 | 예 |
> | namespaces / networkrulesets | 예 | 예 |
> | namespaces / queues | 예 | 예 |
> | namespaces / queues / authorizationrules | 예 | 예 |
> | namespaces / topics | 예 | 예 |
> | namespaces / topics / authorizationrules | 예 | 예 |
> | namespaces / topics / subscriptions | 예 | 예 |
> | namespaces / topics / subscriptions / rules | 예 | 아니요 |
> | premiumMessagingRegions | 아니요 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | 예 | 예 |
> | clusters / applications | 예 | 아니요 |
> | containerGroups | 예 | 예 |
> | containerGroupSets | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | edgeclusters/응용 프로그램 | 아니요 | 예 |
> | managedclusters | 예 | 예 |
> | managedclusters/nodetypes | 아니요 | 예 |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | secretstores/인증서 | 아니요 | 아니요 |
> | secretstores/비밀 | 아니요 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | containerGroups | 예 | 예 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 아니요 | 아니요 |
> | providerRegistrations resourceTypeRegistrations | 아니요 | 예 |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | 예 | 예 |
> | SignalR/eventGridFilters | 아니요 | 예 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 아니요 | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitRequests | 예 | 예 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 예 | 예 |
> | managedInstances/데이터베이스 | 예 ( [아래 참고](#sqlnote)참조) | 예 |
> | managedInstances/databases/backupShortTermRetentionPolicies | 아니요 | 아니요 |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | 아니요 | 아니요 |
> | managedInstances/databases/vulnerabilityAssessments | 아니요 | 아니요 |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | 아니요 | 아니요 |
> | managedInstances/가 나 보호기 | 아니요 | 아니요 |
> | managedInstances/키 | 아니요 | 아니요 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 아니요 | 아니요 |
> | managedInstances/vulnerabilityAssessments | 아니요 | 예 |
> | servers | 예 | 예 |
> | servers / administrators | 예 | 아니요 |
> | servers/communicationLinks | 아니요 | 예 |
> | servers / databases | 예 ( [아래 참고](#sqlnote)참조) | 예 |
> | 서버/서버 보호기 | 아니요 | 아니요 |
> | servers / firewallRules | 아니요 | 예 |
> | servers / keys | 예 | 아니요 |
> | servers/restorableDroppedDatabases | 아니요 | 예 |
> | servers / serviceobjectives | 예 | 아니요 |
> | servers/tdeCertificates | 아니요 | 아니요 |
> | virtualClusters | 아니요 | 아니요 |

<a id="sqlnote"></a>

> [!NOTE]
> Master 데이터베이스는 태그를 지원 하지 않지만 Azure Synapse Analytics 데이터베이스를 비롯 한 다른 데이터베이스는 태그를 지원 합니다. Azure Synapse Analytics 데이터베이스는 활성 (일시 중지 되지 않음) 상태 여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 예 | 예 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 아니요 | 아니요 |
> | SqlVirtualMachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 예 | 예 |
> | storageAccounts / blobServices | 아니요 | 아니요 |
> | storageAccounts / fileServices | 아니요 | 아니요 |
> | storageAccounts/queueServices | 아니요 | 아니요 |
> | storageAccounts/서비스 | 아니요 | 아니요 |
> | storageAccounts/services/metricDefinitions | 아니요 | 아니요 |
> | storageAccounts/tableServices | 아니요 | 예 |
> | usages | 예 | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | caches | 예 | 예 |
> | 캐시/storageTargets 가져오기 | 아니요 | 아니요 |
> | usageModels | 아니요 | 아니요 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 아니요 | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아니요 | 아니요 |
> | storageSyncServices / syncGroups | 아니요 | 아니요 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니요 | 아니요 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니요 | 아니요 |
> | storageSyncServices/워크플로 | 아니요 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아니요 | 아니요 |
> | storageSyncServices / syncGroups | 아니요 | 아니요 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니요 | 아니요 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니요 | 아니요 |
> | storageSyncServices/워크플로 | 아니요 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아니요 | 아니요 |
> | storageSyncServices / syncGroups | 아니요 | 아니요 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니요 | 아니요 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니요 | 아니요 |
> | storageSyncServices/워크플로 | 아니요 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | streamingjobs | 예(아래 참고를 참조) | 예 |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | 아니요 | 아니요 |
> | 별칭 | 아니요 | 아니요 |
> | cancel | 예 | 아니요 |
> | Changeten앤틸리스 요청 | 아니요 | 아니요 |
> | Changeten앤틸리스 상태 | 아니요 | 아니요 |
> | CreateSubscription | 아니요 | 예 |
> | enable | 예 | 예 |
> | 이름 바꾸기 | 예 | 아니요 |
> | SubscriptionDefinitions | 아니요 | 아니요 |
> | SubscriptionOperations | 아니요 | 예 |
> | 구독 | 예 | 예 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | 예 | 예 |
> | workspaces | 예 | 예 |
> | 작업 영역/bigDataPools | 예 | 예 |
> | 작업 영역/작업 상태 | 아니요 | 아니요 |
> | 작업 영역/a s 데이터베이스 | 예 | 예 |
> | workspace/sqlPools | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 아니요 |
> | environments / accessPolicies | 아니요 | 예 |
> | environments / eventsources | 예 | 아니요 |
> | environments / referenceDataSets | 예 | 예 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | stores | 예 | 예 |
> | 매장/Accesspolicy | 아니요 | 예 |
> | stores / services | 예 | 예 |
> | stores / services / tokens | 예 | 예 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | imageTemplates | 예 | 예 |
> | imageTemplates/runOutputs | 아니요 | 아니요 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ArcZones | 예 | 예 |
> | ResourcePools | 예 | 예 |
> | Vcenter | 예 | 예 |
> | VirtualMachines | 예 | 예 |
> | VirtualMachineTemplates | 예 | 예 |
> | VirtualNetworks | 예 | 예 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 예 | 예 |
> | dedicatedCloudServices | 예 | 예 |
> | virtualMachines | 예 | 예 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 디바이스 | 예 | 예 |
> | registeredSubscriptions | 아니요 | 아니요 |
> | 판매 | 아니요 | 아니요 |
> | 공급 업체/sku | 아니요 | 아니요 |
> | 공급 업체/vnfs | 아니요 | 아니요 |
> | virtualNetworkFunctionSkus | 아니요 | 예 |
> | vnfs | 예 | 예 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 플랜 | 예 | 예 |
> | registeredSubscriptions | 아니요 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 아니요 | 아니요 |
> | Apimanagementaccount/apiAcls | 아니요 | 아니요 |
> | Apimanagementaccount/api | 아니요 | 아니요 |
> | Apimanagementaccount/api/apiAcls | 아니요 | 아니요 |
> | Apimanagementaccount/api/connectionAcls | 아니요 | 아니요 |
> | Apimanagementaccount/api/연결 | 아니요 | 아니요 |
> | Apimanagementaccount/api/connections/connectionAcls | 아니요 | 아니요 |
> | Apimanagementaccount/api/localizedDefinitions | 아니요 | 아니요 |
> | Apimanagementaccount/connectionAcls | 아니요 | 아니요 |
> | Apimanagementaccount/연결 | 아니요 | 아니요 |
> | billingMeters | 아니요 | 예 |
> | certificates | 예 | 예 |
> | connectionGateways | 예 | 예 |
> | connections | 예 | 예 |
> | customApis | 예 | 예 |
> | deletedSites | 아니요 | 아니요 |
> | hostingEnvironments | 예 | 예 |
> | hostingEnvironments/eventGridFilters | 아니요 | 아니요 |
> | hostingEnvironments/multiRolePools | 아니요 | 아니요 |
> | hostingEnvironments/이상 풀 | 아니요 | 아니요 |
> | kubeEnvironments | 예 | 예 |
> | publishingUsers | 아니요 | 예 |
> | 동영상 추천 기능 | 예 | 아니요 |
> | resourceHealthMetadata | 아니요 | 예 |
> | runtimes | 예 | 아니요 |
> | serverFarms | 예 | 예 |
> | serverFarms/eventGridFilters | 아니요 | 예 |
> | sites | 예 | 예 |
> | 사이트/구성  | 아니요 | 아니요 |
> | sites/eventGridFilters | 아니요 | 아니요 |
> | 사이트/hostNameBindings | 아니요 | 아니요 |
> | 사이트/네트워크 구성 | 아니요 | 예 |
> | sites / premieraddons | 예 | 예 |
> | sites / slots | 예 | 예 |
> | 사이트/슬롯/eventGridFilters 필터 | 아니요 | 아니요 |
> | 사이트/슬롯/hostNameBindings | 아니요 | 아니요 |
> | 사이트/슬롯/네트워크 구성 | 아니요 | 아니요 |
> | sourceControls | 아니요 | 아니요 |
> | staticSites | 예 | 예 |
> | validate | 예 | 아니요 |
> | verifyHostingEnvironmentVnet | 아니요 | 아니요 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니요 | 아니요 |
> | diagnosticSettingsCategories | 아니요 | 아니요 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | 예 | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 예 | 예 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 | 예 | 예 |
> | 워크 로드/인스턴스 | 아니요 | 아니요 |
> | 워크 로드/버전 | 아니요 | 아니요 |
> | 워크 로드/버전/아티팩트 | 아니요 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 아니요 |
> | componentsSummary | 아니요 | 아니요 |
> | monitorInstances | 아니요 | 아니요 |
> | monitorInstancesSummary | 아니요 | 예 |
> | monitors | 예 | 아니요 |
> | notificationSettings | 아니요 | 아니요 |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
