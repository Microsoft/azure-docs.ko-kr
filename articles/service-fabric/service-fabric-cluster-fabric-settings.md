---
title: Azure Service Fabric 클러스터 설정 변경
description: 이 문서에서는 사용자 지정할 수 있는 패브릭 설정 및 패브릭 업그레이드 정책에 대해 설명합니다.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: fbd6c9503e409473a87c58202eb88d77716441f9
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055123"
---
# <a name="customize-service-fabric-cluster-settings"></a>Service Fabric 클러스터 설정 사용자 지정
이 문서에서는 사용자 지정할 수 있는 Service Fabric 클러스터의 다양한 패브릭 설정을 설명합니다. Azure에서 호스팅된 클러스터의 경우 [Azure Portal](https://portal.azure.com)을 통해 또는 Azure Resource Manager 템플릿을 사용하여 설정을 사용자 지정할 수 있습니다. 자세한 내용은 [Azure 클러스터의 구성 업그레이드](service-fabric-cluster-config-upgrade-azure.md)를 참조하세요. 독립 실행형 클러스터의 경우 *ClusterConfig.json* 파일을 업데이트하고 클러스터에서 구성 업그레이드를 수행하여 설정을 사용자 지정합니다. 자세한 내용은 [독립 실행형 클러스터의 구성 업그레이드](service-fabric-cluster-config-upgrade-windows-server.md)를 참조하세요.

세 가지 다른 업그레이드 정책이 있습니다.

- **동적** – 동적 구성을 변경해도 Service Fabric 프로세스 또는 서비스 호스트 프로세스가 다시 시작되지는 않습니다. 
- **정적** – 정적 구성을 변경하면 변경 내용을 사용하기 위해 Service Fabric 노드가 다시 시작됩니다. 노드의 서비스는 다시 시작됩니다.
- **허용 안함** – 이 설정은 수정할 수 없습니다. 이 설정을 변경하려면 클러스터를 삭제하고 새 클러스터를 만들어야 합니다. 

다음은 사용자 지정하고 섹션별로 정리할 수 있는 패브릭 설정의 목록입니다.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|string, 기본값: "None"|정적| 서버 인증서의 유효성을 검사하지 않습니다. 요청이 성공합니다. 역방향 프록시에서 신뢰할 수 있는 원격 인증서의 쉼표로 구분된 지문 목록에 대한 ServiceCertificateThumbprints 구성을 참조합니다. 역방향 프록시에서 신뢰할 수 있는 원격 인증서의 주체 이름 및 발급자 지문에 대한 ServiceCommonNameAndIssuer 구성을 참조합니다. 자세한 정보는 [역방향 프록시 보안 연결](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)을 참조하세요. |
|BodyChunkSize |uint, 기본값: 16384 |동적| 본문을 읽는 데 사용되는 청크의 크기(바이트)를 지정합니다. |
|CrlCheckingFlag|uint, 기본값: 0x40000000 |동적| 애플리케이션/서비스 인증서 체인 유효성 검사에 대한 플래그입니다. 예: CRL 확인 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY 0으로 설정하면 CRL 확인이 해제됩니다. 지원되는 전체 값 목록은 CertGetCertificateChain의 dwFlags에서 문서화되어 있습니다(https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx ).  |
|DefaultHttpRequestTimeout |시간(초). 기본값은 120입니다. |동적|시간 간격은 초 단위로 지정합니다.  http 앱 게이트웨이에서 처리 중인 http 요청에 대한 기본 요청 시간 제한을 지정합니다. |
|ForwardClientCertificate|bool, 기본값: FALSE|동적|false로 설정된 경우 역방향 프록시는 클라이언트 인증서에 대해 요청하지 않습니다. true로 설정된 경우 역방향 프록시는 TLS 핸드셰이크 중 클라이언트 인증서에 대해 요청하며 X-Client-Certificate라는 헤더의 서비스에 base64로 인코딩된 PEM 형식의 문자열을 전달합니다. 서비스는 인증서 데이터를 검사한 후 적절한 상태 코드로 요청에 실패할 수 있습니다. true이고 클라이언트가 인증서를 제시하지 않으면 역방향 프록시는 빈 헤더를 전달하고 서비스에서 처리하도록 합니다. 역방향 프록시는 투명한 계층처럼 작동합니다. 자세한 정보는 [클라이언트 인증서 인증 설정](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy)을 참조하세요. |
|GatewayAuthCredentialType |string, 기본값: "None" |정적| HTTP 앱 게이트웨이 엔드포인트에서 사용할 보안 자격 증명의 유형을 나타냅니다. 유효한 값: None/X509 |
|GatewayX509CertificateFindType |string, 기본값: "FindByThumbprint" |동적| GatewayX509CertificateStoreName에 지정한 스토어에서 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | string, 기본값: "" |동적| http 앱 게이트웨이 인증서를 찾는 데 사용되는 검색 필터 값. 이 인증서는 https 엔드포인트에서 구성되며, 서비스에서 필요한 경우 앱 ID를 확인하는 데 사용할 수도 있습니다. FindValue를 먼저 조회하고, 이 FindValue가 없으면 FindValueSecondary를 조회합니다. |
|GatewayX509CertificateFindValueSecondary | string, 기본값: "" |동적|http 앱 게이트웨이 인증서를 찾는 데 사용되는 검색 필터 값. 이 인증서는 https 엔드포인트에서 구성되며, 서비스에서 필요한 경우 앱 ID를 확인하는 데 사용할 수도 있습니다. FindValue를 먼저 조회하고, 이 FindValue가 없으면 FindValueSecondary를 조회합니다.|
|GatewayX509CertificateStoreName |string, 기본값: "My" |동적| http 앱 게이트웨이에 대한 서버 인증서가 있는 X.509 인증서 저장소의 이름 |
|HttpRequestConnectTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(5)|동적|시간 간격은 초 단위로 지정합니다.  http 응용 프로그램 게이트웨이에서 보내는 http 요청에 대한 연결 시간 제한을 제공합니다.  |
|IgnoreCrlOfflineError|bool, 기본값: TRUE|동적|애플리케이션/서비스 인증서 확인에 대한 CRL 오프라인 오류를 무시할지 여부 |
|IsEnabled |bool, 기본값: false |정적| HttpApplicationGateway를 사용하거나 사용하지 않도록 설정합니다. HttpApplicationGateway는 기본적으로 사용하지 않도록 설정되며, 이 구성을 활성화하려면 사용하도록 설정해야 합니다. |
|NumberOfParallelOperations | Uint, 기본값: 5000 |정적|http 서버 큐에 게시할 읽기 수. HttpGateway에서 충족할 수 있는 동시 요청 수를 제어합니다. |
|RemoveServiceResponseHeaders|string, 기본값: "Date; Server"|정적|클라이언트에 전달하기 전에 서비스 응답에서 제거될 세미콜론/쉼표로 구분된 응답 헤더 목록입니다. 이 값을 빈 문자열로 설정하면 현재 서비스에서 반환된 모든 헤더를 있는 그대로 전달합니다. 예: Date와 Server를 덮어쓰지 않음 |
|ResolveServiceBackoffInterval |time(초), 기본값: 5 |동적|시간 간격은 초 단위로 지정합니다.  실패한 서비스 확인 작업을 다시 시도하기 전의 기본 백오프 간격을 지정합니다. |
|SecureOnlyMode|bool, 기본값: FALSE|동적| SecureOnlyMode: true: 역방향 프록시에서 보안 엔드포인트를 게시하는 서비스에만 전달합니다. false: 역방향 프록시에서 보안/비보안 엔드포인트에 요청을 전달할 수 있습니다. 자세한 정보는 [역방향 프록시 엔드포인트 선택 논리](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints)를 참조하세요.  |
|ServiceCertificateThumbprints|string, 기본값: ""|동적|역방향 프록시에서 신뢰할 수 있는 원격 인증서의 쉼표로 구분된 지문 목록입니다. 자세한 정보는 [역방향 프록시 보안 연결](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)을 참조하세요. |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, 기본값: None|동적| 역방향 프록시에서 신뢰할 수 있는 원격 인증서의 주체 이름 및 발급자 지문입니다. 자세한 정보는 [역방향 프록시 보안 연결](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)을 참조하세요. |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, 기본값: 0|정적|BackupRestoreService의 MinReplicaSetSize입니다. |
|PlacementConstraints|string, 기본값: ""|정적|    BackupRestore 서비스의 PlacementConstraints입니다. |
|SecretEncryptionCertThumbprint|string, 기본값: ""|동적|비밀 암호화 X509 인증서의 지문 |
|SecretEncryptionCertX509StoreName|문자열, 권장 값: "My"(기본값 없음) |    동적|    백업/복원 서비스에서 사용하는 저장소 자격 증명을 암호화 및 암호 해독하는 데 사용되는 X.509 인증서 저장소의 자격 증명 이름 암호화 및 암호 해독용 인증서를 나타냅니다. |
|TargetReplicaSetSize|int, 기본값:    0|정적| BackupRestoreService의 TargetReplicaSetSize입니다. |

## <a name="clustermanager"></a>ClusterManager

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | bool, 기본값: false |동적|사용자 지정 업그레이드 정렬 정책을 허용할지 여부입니다. 이 기능을 사용하도록 설정하는 2단계 업그레이드를 수행하는 데 사용됩니다. Service Fabric 6.5는 클러스터 또는 애플리케이션 업그레이드 중 업그레이드 도메인에 대한 정렬 정책을 지정하도록 지원을 추가합니다. 지원되는 정책은 Numeric, Lexicographical, ReverseNumeric 및 ReverseLexicographical입니다. 기본값은 Numeric입니다. 이 기능을 사용하려면 SF 6.5 코드 업그레이드가 완료된 후 두 번째 구성 업그레이드 단계로 클러스터 매니페스트 설정 ClusterManager/ AllowCustomUpgradeSortPolicies를 True로 설정해야 합니다. 이 작업은 두 단계로 수행해야 합니다. 그렇지 않으면 첫 번째 업그레이드 중 코드 업그레이드의 업그레이드 순서가 혼동될 수 있습니다.|
|EnableDefaultServicesUpgrade | bool, 기본값: false |동적|애플리케이션 업그레이드 중에 기본 서비스 업그레이드를 사용하도록 설정합니다. 업그레이드 후에 기본 서비스 설명을 덮어씁니다. |
|FabricUpgradeHealthCheckInterval |시간(초), 기본값: 60 |동적|모니터링되는 패브릭 업그레이드 동안 시스템 상태 확인 빈도 |
|FabricUpgradeStatusPollInterval |시간(초), 기본값: 60 |동적|패브릭 업그레이드 상태에 대한 풀링 간격. 이 값은 GetFabricUpgradeProgress 호출에 대한 업데이트 속도를 결정합니다. |
|ImageBuilderTimeoutBuffer |time(초), 기본값: 3 |동적|시간 간격은 초 단위로 지정합니다. 이미지 작성기 특정 시간 제한 오류를 클라이언트에 반환할 수 있도록 허용되는 시간입니다. 이 버퍼가 너무 작으면 클라이언트에서 서버보다 먼저 시간을 초과하고 일반적인 시간 제한 오류를 가져옵니다. |
|InfrastructureTaskHealthCheckRetryTimeout | 시간(초), 기본값: 60 |동적|시간 간격은 초 단위로 지정합니다. 인프라 태스크를 사후 처리하는 동안 실패한 상태 검사를 다시 시도하는 데 걸리는 시간입니다. 통과한 상태 검사를 관찰하면 이 타이머가 다시 설정됩니다. |
|InfrastructureTaskHealthCheckStableDuration | time(초), 기본값: 0|동적| 시간 간격은 초 단위로 지정합니다. 인프라 태스크의 사후 처리를 성공적으로 완료하기 전에 연속적으로 통과한 상태 검사를 관찰하는 데 걸리는 시간입니다. 실패한 상태 검사를 관찰하면 이 타이머가 다시 설정됩니다. |
|InfrastructureTaskHealthCheckWaitDuration |time(초), 기본값: 0|동적| 시간 간격은 초 단위로 지정합니다. 인프라 태스크를 사후 처리한 후 상태 검사를 시작하기 전까지 대기하는 시간입니다. |
|InfrastructureTaskProcessingInterval | 시간(초), 기본값: 10 |동적|시간 간격은 초 단위로 지정합니다. 인프라 태스크 처리 상태 시스템에서 사용하는 처리 간격입니다. |
|MaxCommunicationTimeout |time(초), 기본값: 600 |동적|시간 간격은 초 단위로 지정합니다. ClusterManager와 다른 시스템 서비스(예: 이름 지정 서비스, 장애 조치 관리자 등) 간의 내부 통신에 대한 최대 시간 제한입니다. 각 클라이언트 작업의 시스템 구성 요소 간에 여러 통신이 있을 수 있으므로 이 시간 제한은 전역 MaxOperationTimeout보다 작아야 합니다. |
|MaxDataMigrationTimeout |time(초), 기본값: 600 |동적|시간 간격은 초 단위로 지정합니다. 패브릭 업그레이드를 수행한 이후 데이터 마이그레이션 복구 작업에 대한 최대 시간 제한입니다. |
|MaxOperationRetryDelay |time(초), 기본값: 5|동적| 시간 간격은 초 단위로 지정합니다. 오류가 발생하는 경우 내부 다시 시도에 대한 최대 지연 시간입니다. |
|MaxOperationTimeout |time(초), 기본값: MaxValue |동적| 시간 간격은 초 단위로 지정합니다. ClusterManager에서 내부적으로 작업을 처리하기 위한 최대 전역 시간 제한입니다. |
|MaxTimeoutRetryBuffer | time(초), 기본값: 600 |동적|시간 간격은 초 단위로 지정합니다. 시간 제한으로 인해 내부적으로 다시 시도하는 경우 최대 작업 시간 제한은 `<Original Time out> + <MaxTimeoutRetryBuffer>`입니다. 추가 시간 제한은 MinOperationTimeout 증분 단위로 추가됩니다. |
|MinOperationTimeout | 시간(초), 기본값: 60 |동적|시간 간격은 초 단위로 지정합니다. ClusterManager에서 내부적으로 작업을 처리하기 위한 최소 전역 시간 제한입니다. |
|MinReplicaSetSize |int, 기본값: 3 |허용되지 않음|ClusterManager의 MinReplicaSetSize입니다. |
|PlacementConstraints | string, 기본값: "" |허용되지 않음|ClusterManager의 PlacementConstraints입니다. |
|QuorumLossWaitDuration |time(초), 기본값: MaxValue |허용되지 않음| 시간 간격은 초 단위로 지정합니다. ClusterManager의 QuorumLossWaitDuration입니다. |
|ReplicaRestartWaitDuration |시간(초), 기본값: (60.0 \* 30)|허용되지 않음|시간 간격은 초 단위로 지정합니다. ClusterManager의 ReplicaRestartWaitDuration입니다. |
|ReplicaSetCheckTimeoutRollbackOverride |time(초), 기본값: 1200 |동적| 시간 간격은 초 단위로 지정합니다. ReplicaSetCheckTimeout을 최대 DWORD 값으로 설정하면 롤백을 위해 이 구성의 값으로 재정의됩니다. 롤포워드에 사용되는 값은 절대로 재정의되지 않습니다. |
|SkipRollbackUpdateDefaultService | bool, 기본값: false |동적|CM에서 애플리케이션 업그레이드 롤백 중에 업데이트된 기본 서비스 되돌리기를 건너뜁니다. |
|StandByReplicaKeepDuration | 시간(초), 기본값: (3600.0 \* 2)|허용되지 않음|시간 간격은 초 단위로 지정합니다. ClusterManager의 StandByReplicaKeepDuration입니다. |
|TargetReplicaSetSize |int, 기본값: 7 |허용되지 않음|ClusterManager의 TargetReplicaSetSize입니다. |
|UpgradeHealthCheckInterval |시간(초), 기본값: 60 |동적|모니터링되는 애플리케이션 업그레이드 동안 시스템 상태 확인 빈도 |
|UpgradeStatusPollInterval |시간(초), 기본값: 60 |동적|애플리케이션 업그레이드 상태에 대한 풀링 간격. 이 값은 GetApplicationUpgradeProgress 호출에 대한 업데이트 속도를 결정합니다. |
|CompleteClientRequest | bool, 기본값: false |동적| CM이 수락하면 클라이언트 요청을 완료합니다. |

## <a name="common"></a>일반

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PerfMonitorInterval |time(초), 기본값: 1 |동적|시간 간격은 초 단위로 지정합니다. 성능 모니터링 간격입니다. 0 또는 음수 값으로 설정하면 모니터링을 사용하지 않도록 설정됩니다. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, 기본값: None|동적|노드를 비울 때 정책 조각 모음을 수행하도록 지정합니다. 메트릭에 대해 0을 지정하면 SF에서 UD와 FD 간에 노드를 균등하게 조각 모음해야 합니다. 1을 지정하면 노드를 조각 모음해야 합니다. |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, 기본값: None|동적|조각 모음에는 사용되지만 부하 분산에는 사용되지 않아야 하는 메트릭 집합을 결정합니다. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, 기본값: None|동적|[0.0 - 1.0] 범위의 백분율 또는 1.0보다 크거나 같은 빈 노드 수를 지정하여 클러스터 조각 모음을 고려하는 데 필요한 사용 가능한 노드 수를 결정합니다. |

## <a name="diagnostics"></a>진단

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |bool, 기본값: true | 동적 | 감사에서 클러스터 상태에 영향을 주지 않는 HTTP 요청을 제외합니다. 현재는 "GET" 형식의 요청만 제외되지만 앞으로 변경될 수 있습니다. |
|AppDiagnosticStoreAccessRequiresImpersonation |bool, 기본값: true | 동적 |애플리케이션 대신 진단 저장소에 액세스할 때 가장이 필요한지의 여부 |
|AppEtwTraceDeletionAgeInDays |int, 기본값: 3 | 동적 |애플리케이션 ETW 추적을 포함하고 있는 오래된 ETL 파일을 삭제한 이후 경과한 일 수 |
|ApplicationLogsFormatVersion |int, 기본값: 0 | 동적 |애플리케이션 로그 형식의 버전. 지원되는 값은 0과 1입니다. 버전 1은 버전 0보다 더 많은 ETW 이벤트 레코드 필드를 포함합니다. |
|AuditHttpRequests |bool, 기본값: false | 동적 | HTTP 감사를 켜거나 끕니다. 감사의 목적은 클러스터에 대해 수행된 작업(요청을 시작한 사람 포함)을 확인하는 것입니다. 최적의 시도 로깅이며 추적 손실이 발생할 수 있습니다. "User" 인증을 사용하는 HTTP 요청은 기록되지 않습니다. |
|CaptureHttpTelemetry|bool, 기본값: true | 동적 | HTTP 원격 분석을 켜거나 끕니다. 원격 분석의 목적은 Service Fabric이 향후 작업을 계획하고 문제 영역을 식별하는 데 유용한 원격 분석 데이터를 캡처할 수 있도록 하는 것입니다. 원격 분석은 개인 데이터나 요청 본문을 기록하지 않습니다. 원격 분석은 달리 구성하지 않는 한 모든 HTTP 요청을 캡처합니다. |
|ClusterId |String | 동적 |클러스터의 고유 ID입니다. 클러스터를 만들 때 생성됩니다. |
|ConsumerInstances |String | 동적 |DCA 소비자 인스턴스 목록 |
|DiskFullSafetySpaceInMB |int, 기본값: 1024 | 동적 |DCA에서 사용하지 못하도록 보호하기 위해 남아 있는 디스크 공간(MB) |
|EnableCircularTraceSession |bool, 기본값: false | 정적 |플래그에서 순환 추적 세션을 사용해야 하는지 여부를 나타냅니다. |
|EnablePlatformEventsFileSink |bool, 기본값: false | 정적 |디스크에 기록되는 플랫폼 이벤트 사용/사용 안 함 |
|EnableTelemetry |bool, 기본값: true | 동적 |원격 분석 사용 여부 |
|FailuresOnlyHttpTelemetry | bool, 기본값: false | 동적 | HTTP 원격 분석 캡처를 사용하는 경우, 실패한 요청만 캡처합니다. 원격 분석을 위해 생성되는 이벤트 수를 줄이는 데 유용합니다. |
|HttpTelemetryCapturePercentage | int, 기본값: 50 | 동적 | HTTP 원격 분석 캡처를 사용하는 경우, 임의 비율의 요청만 캡처합니다. 원격 분석을 위해 생성되는 이벤트 수를 줄이는 데 유용합니다. |
|MaxDiskQuotaInMB |int, 기본값: 65536 | 동적 |Windows Fabric 로그 파일의 디스크 할당량(MB) |
|ProducerInstances |String | 동적 |DCA 생산자 인스턴스 목록 |

## <a name="dnsservice"></a>DnsService
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, 기본값: FALSE|정적|분할된 서비스에 대한 DNS 쿼리 지원이 가능하도록 설정하는 플래그입니다. 이 기능은 기본적으로 꺼져 있습니다. 자세한 내용은 [Service Fabric DNS 서비스](service-fabric-dnsservice.md)를 참조하세요.|
|InstanceCount|int, 기본값: -1|정적|기본값은 -1이며, DnsService가 모든 노드에서 실행된다는 의미입니다. OneBox는 DnsService에서 잘 알려진 포트 53을 사용하므로 1로 설정되어야 합니다. 따라서 동일한 컴퓨터에 여러 인스턴스를 가질 수 없습니다.|
|IsEnabled|bool, 기본값: FALSE|정적|DnsService를 활성화하거나 비활성화합니다. DnsService는 기본적으로 비활성화되며, 이 구성을 활성화하도록 설정해야 합니다. |
|PartitionPrefix|string, 기본값은 "--"입니다.|정적|분할된 서비스에 대한 DNS 쿼리의 파티션 접두사 문자열 값을 제어합니다. 값: <ul><li>DNS 쿼리의 일부이므로 RFC를 준수해야 합니다.</li><li>점은 DNS 접미사 동작을 방해하므로 점('.')을 포함하지 않아야 합니다.</li><li>5자를 초과할 수 없습니다.</li><li>빈 문자열일 수 없습니다.</li><li>PartitionPrefix 설정이 재정의된 경우 PartitionSuffix를 재정의하고 그 반대로 해야 합니다.</li></ul>자세한 내용은 [Service Fabric DNS 서비스](service-fabric-dnsservice.md)를 참조하세요.|
|PartitionSuffix|string, 기본값: ""|정적|분할된 서비스에 대한 DNS 쿼리의 파티션 접미사 문자열 값을 제어합니다. 값: <ul><li>DNS 쿼리의 일부이므로 RFC를 준수해야 합니다.</li><li>점은 DNS 접미사 동작을 방해하므로 점('.')을 포함하지 않아야 합니다.</li><li>5자를 초과할 수 없습니다.</li><li>PartitionPrefix 설정이 재정의된 경우 PartitionSuffix를 재정의하고 그 반대로 해야 합니다.</li></ul>자세한 내용은 [Service Fabric DNS 서비스](service-fabric-dnsservice.md)를 참조하세요. |

## <a name="eventstoreservice"></a>EventStoreService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, 기본값: 0|정적|EventStore 서비스의 MinReplicaSetSize입니다. |
|PlacementConstraints|string, 기본값: ""|정적|    EventStore 서비스의 PlacementConstraints입니다. |
|TargetReplicaSetSize|int, 기본값:    0|정적| EventStore 서비스의 TargetReplicaSetSize입니다. |

## <a name="fabricclient"></a>FabricClient

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |time(초), 기본값: 2 |동적|시간 간격은 초 단위로 지정합니다. 클라이언트에서 게이트웨이에 대한 연결을 열려고 시도 할 때마다 적용되는 연결 시간 제한 간격입니다.|
|HealthOperationTimeout |time(초), 기본값: 120 |동적|시간 간격은 초 단위로 지정합니다. 상태 관리자에게 전송되는 보고서 메시지의 시간 제한입니다. |
|HealthReportRetrySendInterval |time(초), 기본값: 30 최솟값: 1 |동적|시간 간격은 초 단위로 지정합니다. 보고 구성 요소에서 누적된 상태 보고서를 상태 관리자로 다시 보내는 간격입니다. |
|HealthReportSendInterval |시간(초), 기본값: 30 |동적|시간 간격은 초 단위로 지정합니다. 보고 구성 요소에서 누적된 상태 보고서를 상태 관리자로 보내는 간격입니다. |
|KeepAliveIntervalInSeconds |int, 기본값: 20 |정적|FabricClient 전송에서 연결 유지 메시지를 게이트웨이로 보내는 간격. 0 값인 경우 keepAlive를 사용하지 않도록 설정됩니다. 양수 값이어야 합니다. |
|MaxFileSenderThreads |uint, 기본값: 10 |정적|동시에 전송되는 최대 파일 수 |
|NodeAddresses |string, 기본값: "" |정적|이름 지정 서비스와 통신하는 데 사용할 수 있는 여러 노드의 주소 컬렉션(연결 문자열). 초기에는 클라이언트에서 주소 중 하나를 임의로 선택하여 연결합니다. 연결 문자열이 둘 이상 제공되고 통신 오류 또는 시간 제한 오류로 인해 연결이 실패하는 경우 Client(클라이언트)에서 다음 주소를 순차적으로 사용하도록 전환합니다. 다시 시도 체계에 대한 자세한 내용은 이름 지정 서비스 주소 다시 시도 섹션을 참조하세요. |
|PartitionLocationCacheLimit |int, 기본값: 100000 |정적|서비스 확인을 위해 캐시되는 파티션 수(제한하지 않을 경우 0으로 설정) |
|RetryBackoffInterval |time(초), 기본값: 3 |동적|시간 간격은 초 단위로 지정합니다. 작업을 다시 시도하기 전의 백오프 간격입니다. |
|ServiceChangePollInterval |time(초), 기본값: 120 |동적|시간 간격은 초 단위로 지정합니다. 등록된 서비스 변경 알림 콜백을 위해 클라이언트에서 게이트웨이로의 서비스 변경에 대한 연속 폴링 간격입니다. |

## <a name="fabrichost"></a>FabricHost

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |int, 기본값: 10 |동적|시스템에서 실패한 활성화를 포기하기 전에 다시 시도할 최대 횟수 |
|ActivationMaxRetryInterval |시간(초), 기본값: 300 |동적|시간 간격은 초 단위로 지정합니다. Activation(활성화)를 위한 최대 다시 시도 간격입니다. 연속적인 활성화 시도에서 실패할 때마다 다시 시도 간격은 Min(ActivationMaxRetryInterval; 연속 실패 횟수 * ActivationRetryBackoffInterval)로 계산됩니다. |
|ActivationRetryBackoffInterval |time(초), 기본값: 5 |동적|시간 간격은 초 단위로 지정합니다. 모든 활성화 오류에 대한 백오프 간격입니다. 연속적인 활성화 시도에서 실패할 때마다 시스템에서 MaxActivationFailureCount에 도달할 때까지 활성화를 다시 시도합니다. 모든 시도에서 다시 시도 간격은 연속 활성화 실패와 활성화 백오프 간격을 곱한 값입니다. |
|EnableRestartManagement |bool, 기본값: false |동적|서버를 다시 시작하도록 설정합니다. |
|EnableServiceFabricAutomaticUpdates |bool, 기본값: false |동적|Windows 업데이트를 통해 패브릭을 자동으로 업데이트하도록 설정합니다. |
|EnableServiceFabricBaseUpgrade |bool, 기본값: false |동적|서버에 대한 기본 업데이트를 사용하도록 설정합니다. |
|FailureReportingExpeditedReportingIntervalEnabled | bool, 기본값: true | 정적 | FabricHost가 실패 보고 모드인 경우 DCA의 업로드 속도를 빠르게 할 수 있습니다. |
|FailureReportingTimeout | TimeSpan, 기본값: Common::TimeSpan::FromSeconds(60) | 정적 |시간 간격은 초 단위로 지정합니다. FabricHost에서 초기 단계 시작 실패가 발생하면 DCA 실패 보고 시간이 초과됩니다. | 
|RunDCAOnStartupFailure | bool, 기본값: true | 정적 |FabricHost에 시작 문제가 발생하는 경우 DCA를 시작하여 로그를 업로드할지 여부를 결정합니다. | 
|StartTimeout |시간(초), 기본값: 300 |동적|시간 간격은 초 단위로 지정합니다. fabricactivationmanager 시작에 대한 시간 제한입니다. |
|StopTimeout |시간(초), 기본값: 300 |동적|시간 간격은 초 단위로 지정합니다. 호스티드 서비스 활성화, 비활성화 및 업그레이드에 대한 시간 제한 |

## <a name="fabricnode"></a>FabricNode

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |string, 기본값: "FindByThumbprint" |동적|ClientAuthX509StoreName에 지정한 스토어에서 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |string, 기본값: "" | 동적|FabricClient 기본 관리자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
|ClientAuthX509FindValueSecondary |string, 기본값: "" |동적|FabricClient 기본 관리자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
|ClientAuthX509StoreName |string, 기본값: "My" |동적|FabricClient 기본 관리자 역할에 대한 인증서가 있는 X.509 인증서 저장소의 이름 |
|ClusterX509FindType |string, 기본값: "FindByThumbprint" |동적|ClusterX509StoreName에 지정한 스토어에서 클러스터 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: "FindByThumbprint"; "FindBySubjectName" With "FindBySubjectName". 여러 항목이 일치하는 경우; 만료가 가장 먼 항목이 사용됩니다. |
|ClusterX509FindValue |string, 기본값: "" |동적|클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
|ClusterX509FindValueSecondary |string, 기본값: "" |동적|클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
|ClusterX509StoreName |string, 기본값: "My" |동적|클러스터 내 통신을 보호하기 위한 클러스터 인증서가 있는 X.509 인증서 저장소의 이름 |
|EndApplicationPortRange |int, 기본값: 0 |정적|호스팅 하위 시스템에서 관리하는 애플리케이션 포트의 끝(inclusive 없음). Hosting(호스팅)에서 EndpointFilteringEnabled가 true인 경우 필수입니다. |
|ServerAuthX509FindType |string, 기본값: "FindByThumbprint" |동적|ServerAuthX509StoreName에 지정한 스토어에서 서버 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |string, 기본값: "" |동적|클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
|ServerAuthX509FindValueSecondary |string, 기본값: "" |동적|클러스터 인증서를 찾는 데 사용되는 검색 필터 값 |
|ServerAuthX509StoreName |string, 기본값: "My" |동적|주 서비스에 대한 서버 인증서가 있는 X.509 인증서 저장소의 이름 |
|StartApplicationPortRange |int, 기본값: 0 |정적|호스트 하위 시스템에서 관리하는 애플리케이션 포트의 시작. Hosting(호스팅)에서 EndpointFilteringEnabled가 true인 경우 필수입니다. |
|StateTraceInterval |시간(초), 기본값: 300 |정적|시간 간격은 초 단위로 지정합니다. 각 노드에서 노드 상태를 추적하고 FM/FMM에서 노드를 추적하는 간격입니다. |
|UserRoleClientX509FindType |string, 기본값: "FindByThumbprint" |동적|UserRoleClientX509StoreName에 지정한 스토어에서 인증서를 검색하는 방법을 나타냅니다. 지원되는 값: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |string, 기본값: "" |동적|FabricClient 기본 사용자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
|UserRoleClientX509FindValueSecondary |string, 기본값: "" |동적|FabricClient 기본 사용자 역할에 대한 인증서를 찾는 데 사용되는 검색 필터 값 |
|UserRoleClientX509StoreName |string, 기본값: "My" |동적|FabricClient 기본 사용자 역할에 대한 인증서가 있는 X.509 인증서 저장소의 이름 |

## <a name="failovermanager"></a>FailoverManager

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, 기본값: FALSE |동적|시드 노드의 노드 상태를 제거할 수 있는지 여부를 나타내는 플래그 |
|BuildReplicaTimeLimit|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(3600)|동적|시간 간격은 초 단위로 지정합니다. 상태 저장 복제본을 빌드하는 데 걸리는 시간 제한이며, 이 시간이 지나면 경고 상태 보고서가 시작됩니다. |
|ClusterPauseThreshold|int, 기본값: 1|동적|시스템의 노드 수가 이 값보다 작으면 배치, 부하 분산 및 장애 조치가 중지됩니다. |
|CreateInstanceTimeLimit|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(300)|동적|시간 간격은 초 단위로 지정합니다. 상태 비저장 인스턴스를 만드는 데 걸리는 시간 제한이며, 이 시간이 지나면 경고 상태 보고서가 시작됩니다. |
|ExpectedClusterSize|int, 기본값: 1|동적|클러스터가 처음 시작될 때 이름 지정 서비스와 같은 시스템 서비스를 포함하여 다른 서비스를 배치하기 전에 FM은 이 많은 노드가 보고될 때까지 기다립니다. 이 값을 늘리면 클러스터를 시작하는 데 더 많은 시간이 걸리지만, 초기 노드의 오버로드를 방지하고 더 많은 노드가 온라인 상태가 될 때 필요한 추가 이동도 방지합니다. 이 값은 일반적으로 초기 클러스터 크기의 일부로 설정해야 합니다. |
|ExpectedNodeDeactivationDuration|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(60.0 \* 30)|동적|시간 간격은 초 단위로 지정합니다. 노드에서 비활성화를 완료하는 데 예상되는 기간입니다. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(60.0 \* 30)|동적|시간 간격은 초 단위로 지정합니다. Windows Fabric 업그레이드 중에 노드를 업그레이드하는 데 예상되는 기간입니다. |
|ExpectedReplicaUpgradeDuration|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(60.0 \* 30)|동적|시간 간격은 초 단위로 지정합니다. 애플리케이션 업그레이드 중 노드에서 모든 복제본을 업그레이드하는 데 예상되는 기간입니다. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, 기본값: TRUE|동적|true로 설정하면 대상 복제본 세트 크기가 1인 복제본이 업그레이드 중에 이동할 수 있습니다. |
|MinReplicaSetSize|int, 기본값: 3|허용되지 않음|FM의 최소 복제본 세트 크기입니다. 활성 FM 복제본 수가 이 값 아래로 떨어지면 적어도 최소 복제본 수로 복구될 때까지 FM에서 클러스터에 대한 변경을 거부합니다 |
|PlacementConstraints|string, 기본값: ""|허용되지 않음|장애 조치 관리자 복제본에 대한 모든 배치 제약 조건 |
|PlacementTimeLimit|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(600)|동적|시간 간격은 초 단위로 지정합니다. 대상 복제본 수에 도달하는 데 걸리는 시간 제한이며, 이 시간이 지나면 경고 상태 보고서가 시작됩니다. |
|QuorumLossWaitDuration |time(초), 기본값: MaxValue |동적|시간 간격은 초 단위로 지정합니다. 파티션을 쿼럼 손실 상태로 있도록 하는 최대 기간입니다. 이 기간이 경과한 후에도 파티션이 여전히 쿼럼 손실 상태로 있으면 중단된 복제본을 쿼럼 손실로 간주하여 파티션이 쿼럼 손실로부터 복구됩니다. 이 경우 잠재적으로 데이터 손실이 발생할 수 있습니다. |
|ReconfigurationTimeLimit|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(300)|동적|시간 간격은 초 단위로 지정합니다. 재구성하는 데 걸리는 시간 제한이며, 이 시간이 지나면 경고 상태 보고서가 시작됩니다. |
|ReplicaRestartWaitDuration|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(60.0 \* 30)|허용되지 않음|시간 간격은 초 단위로 지정합니다. FMService의 ReplicaRestartWaitDuration입니다. |
| SeedNodeQuorumAdditionalBufferNodes | int, 기본값: 0 | 동적 | 작동하는 데 필요한 시드 노드의 버퍼(시드 노드의 쿼럼과 함께) FM은 최대 (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes))개의 시드 노드가 다운되도록 허용해야 합니다. |
|StandByReplicaKeepDuration|Timespan, 기본값: Common::TimeSpan::FromSeconds(3600.0 \* 24 \* 7)|허용되지 않음|시간 간격은 초 단위로 지정합니다. FMService의 StandByReplicaKeepDuration입니다. |
|TargetReplicaSetSize|int, 기본값: 7|허용되지 않음|Windows Fabric에서 유지 관리하는 FM 복제본의 대상 수입니다. 숫자가 높을수록 FM 데이터의 안정성이 높아집니다. 성능과 약간 절충하는 효과가 있습니다. |
|UserMaxStandByReplicaCount |int, 기본값: 1 |동적|시스템에서 사용자 서비스를 위해 보관하는 StandBy 복제본의 기본 최대 수 |
|UserReplicaRestartWaitDuration |시간(초), 기본값: 60.0 \* 30 |동적|시간 간격은 초 단위로 지정합니다. 보관된 복제본이 중단되면 상태의 복사본이 필요한 새로운 대체 복제본을 만들기 전에 복제본이 다시 시작될 때까지 Windows Fabric에서 대기합니다. |
|UserStandByReplicaKeepDuration |time(초), 기본값: 3600.0 \* 24 \* 7 |동적|시간 간격은 초 단위로 지정합니다. 보관된 복제본이 중단된 상태에서 돌아올 때 복제본이 이미 대체되었을 수 있습니다. 이 타이머는 FM에서 대기 복제본을 삭제하기 전에 보관할 기간을 결정합니다. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | int, 기본값: 604800 |정적| 대체로 터미널 상태에 있는 작업을 유지하는 기간입니다. 또한 정리 작업이 StoredActionCleanupIntervalInSeconds의 간격에서만 수행되기 때문에 이 간격에 따라 달라집니다. 604800은 7일입니다. |
|DataLossCheckPollIntervalInSeconds|int, 기본값: 5|정적|데이터 손실을 기다리는 동안 시스템에서 수행하는 검사 사이의 시간입니다. 내부 반복당 데이터 손실 수를 확인하는 횟수는 DataLossCheckWaitDurationInSeconds/this입니다. |
|DataLossCheckWaitDurationInSeconds|int, 기본값: 25|정적|데이터 손실이 발생할 때까지 시스템에서 기다리는 총 시간(초 단위)입니다. StartPartitionDataLossAsync() API를 호출할 때 내부적으로 사용됩니다. |
|MinReplicaSetSize |int, 기본값: 0 |정적|FaultAnalysisService의 MinReplicaSetSize입니다. |
|PlacementConstraints | string, 기본값: ""|정적| FaultAnalysisService의 PlacementConstraints입니다. |
|QuorumLossWaitDuration | time(초), 기본값: MaxValue |정적|시간 간격은 초 단위로 지정합니다. FaultAnalysisService의 QuorumLossWaitDuration입니다. |
|ReplicaDropWaitDurationInSeconds|int, 기본값: 600|정적|데이터 손실 API를 호출할 때 사용되는 매개 변수입니다. 복제본 제거를 내부적으로 호출한 후 복제본이 삭제될 때까지 시스템에서 기다리는 시간을 제어합니다. |
|ReplicaRestartWaitDuration |time(초), 기본값: 60분|정적|시간 간격은 초 단위로 지정합니다. FaultAnalysisService의 ReplicaRestartWaitDuration입니다. |
|StandByReplicaKeepDuration| time(초), 기본값: 60*24*7분 |정적|시간 간격은 초 단위로 지정합니다. FaultAnalysisService의 StandByReplicaKeepDuration입니다. |
|StoredActionCleanupIntervalInSeconds | int, 기본값: 3600 |정적|저장소를 정리하는 빈도. 터미널 상태에 있는 작업 및 적어도 CompletedActionKeepDurationInSeconds 전에 완료된 작업만 제거합니다. |
|StoredChaosEventCleanupIntervalInSeconds | int, 기본값: 3600 |정적|저장소를 정리하기 위해 감사하는 빈도. 이벤트 수가 30,000개를 초과하면 정리가 시작됩니다. |
|TargetReplicaSetSize |int, 기본값: 0 |정적|NOT_PLATFORM_UNIX_START. FaultAnalysisService의 TargetReplicaSetSize입니다. |

## <a name="federation"></a>페더레이션

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|LeaseDuration |시간(초), 기본값: 30 |동적|노드 및 해당 이웃 노드 간에 임대가 지속되는 기간 |
|LeaseDurationAcrossFaultDomain |시간(초), 기본값: 30 |동적|장애 도메인에서 노드 및 해당 이웃 노드 간에 임대가 지속되는 기간 |

## <a name="filestoreservice"></a>FileStoreService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, 기본값: TRUE|동적|파일 저장소 서비스에서 복사 애플리케이션 패키지 동안 청크 기반 파일 업로드를 허용하는지 여부를 결정하도록 구성합니다. |
|AnonymousAccessEnabled | bool, 기본값: true |정적|FileStoreService 공유에 대한 익명 액세스를 사용하거나 사용하지 않도록 설정합니다. |
|CommonName1Ntlmx509CommonName|string, 기본값: ""|정적| NTLM 인증을 사용할 때 CommonName1NtlmPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 일반 이름 |
|CommonName1Ntlmx509StoreLocation|string, 기본값: "LocalMachine"|정적|NTLM 인증을 사용할 때 CommonName1NtlmPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 위치 |
|CommonName1Ntlmx509StoreName|string, 기본값: "MY"| 정적|NTLM 인증을 사용할 때 CommonName1NtlmPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 이름 |
|CommonName2Ntlmx509CommonName|string, 기본값: ""|정적|NTLM 인증을 사용할 때 CommonName2NtlmPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 일반 이름 |
|CommonName2Ntlmx509StoreLocation|string, 기본값: "LocalMachine"| 정적|NTLM 인증을 사용할 때 CommonName2NtlmPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 위치 |
|CommonName2Ntlmx509StoreName|string, 기본값: "MY"|정적| NTLM 인증을 사용할 때 CommonName2NtlmPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 이름 |
|CommonNameNtlmPasswordSecret|SecureString, 기본값: Common::SecureString("")| 정적|NTLM 인증을 사용할 때 생성된 동일한 암호에 대한 시드로 사용되는 암호 비밀 |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, 기본값: Common::TimeSpan::FromMinutes(5)|동적|시간 간격은 초 단위로 지정합니다. 디스크 공간이 부족한 경우 상태 보고 이벤트를 위해 디스크 공간을 확인하는 시간 간격입니다. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, 기본값: Common::TimeSpan::FromMinutes(15)|동적|시간 간격은 초 단위로 지정합니다. 디스크 공간이 충분한 경우 상태 보고 이벤트를 위해 디스크 공간을 확인하는 시간 간격입니다. |
|EnableImageStoreHealthReporting |bool, 기본값: TRUE    |정적|파일 저장소 서비스가 상태를 보고해야 하는지 여부를 결정하는 구성입니다. |
|FreeDiskSpaceNotificationSizeInKB|int64, 기본값: 25\*1024 |동적|미만이 되면 상태 경고가 발생할 수 있는 사용 가능한 디스크 공간의 크기입니다. 이 구성의 최솟값 및 FreeDiskSpaceNotificationThresholdPercentage 구성이 상태 경고의 전송을 결정하는 데 사용됩니다. |
|FreeDiskSpaceNotificationThresholdPercentage|double, 기본값: 0.02 |동적|미만이 되면 상태 경고가 발생할 수 있는 사용 가능한 디스크 공간의 백분율입니다. 이 구성의 최솟값 및 FreeDiskSpaceNotificationInMB 구성이 상태 경고의 전송을 결정하는 데 사용됩니다. |
|GenerateV1CommonNameAccount| bool, 기본값: TRUE|정적|사용자 이름 V1 생성 알고리즘을 사용하여 계정을 생성할지 여부를 지정합니다. Service Fabric 버전 6.1부터 v2 생성으로 계정이 항상 생성됩니다. V1 계정은 V2 생성을 지원하지 않는 버전(6.1 이전)에서 업그레이드하거나 이 버전으로 업그레이드할 때 필요합니다.|
|MaxCopyOperationThreads | uint, 기본값: 0 |동적| 보조 저장소에서 기본 저장소로부터 복사할 수 있는 최대 병렬 파일 수. '0'은 코어 수와 동등합니다. |
|MaxFileOperationThreads | uint, 기본값: 100 |정적| 기본 저장소에서 FileOperations(복사/이동)를 수행할 수 있도록 허용되는 최대 병렬 스레드 수. '0'은 코어 수와 동등합니다. |
|MaxRequestProcessingThreads | uint, 기본값: 200 |정적|기본 저장소에서 요청을 처리할 수 있도록 허용되는 최대 병렬 스레드 수. '0'은 코어 수와 동등합니다. |
|MaxSecondaryFileCopyFailureThreshold | uint, 기본값: 25|동적|보조 저장소에서 파일 복사를 포기하기 전에 다시 시도할 최대 횟수 |
|MaxStoreOperations | uint, 기본값: 4096 |정적|주 복제본에 허용되는 최대 병렬 저장소 트랜잭션 작업 수입니다. '0'은 코어 수와 동등합니다. |
|NamingOperationTimeout |시간(초), 기본값: 60 |동적|시간 간격은 초 단위로 지정합니다. 이름 지정 작업을 수행하기 위한 시간 제한입니다. |
|PrimaryAccountNTLMPasswordSecret | SecureString, 기본값: 비어 있음 |정적| NTLM 인증을 사용할 때 동일한 암호를 생성하기 위해 시드로 사용한 암호 |
|PrimaryAccountNTLMX509StoreLocation | string, 기본값: "LocalMachine"|정적| NTLM 인증을 사용할 때 PrimaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 위치. |
|PrimaryAccountNTLMX509StoreName | string, 기본값: "MY"|정적| NTLM 인증을 사용할 때 PrimaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 이름. |
|PrimaryAccountNTLMX509Thumbprint | string, 기본값: ""|정적|NTLM 인증을 사용할 때 PrimaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 지문. |
|PrimaryAccountType | string, 기본값: "" |정적|FileStoreService에서 공유하는 ACL에 대한 주체의 기본 계정 유형 |
|PrimaryAccountUserName | string, 기본값: "" |정적|FileStoreService에서 공유하는 ACL에 대한 주체의 기본 계정 사용자 이름 |
|PrimaryAccountUserPassword | SecureString, 기본값: 비어 있음 |정적|FileStoreService에서 공유하는 ACL에 대한 주체의 기본 계정 암호 |
|QueryOperationTimeout | 시간(초), 기본값: 60 |동적|시간 간격은 초 단위로 지정합니다. 쿼리 작업을 수행하기 위한 시간 제한입니다. |
|SecondaryAccountNTLMPasswordSecret | SecureString, 기본값: 비어 있음 |정적| NTLM 인증을 사용할 때 동일한 암호를 생성하기 위해 시드로 사용한 암호 |
|SecondaryAccountNTLMX509StoreLocation | string, 기본값: "LocalMachine" |정적|NTLM 인증을 사용할 때 SecondaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 위치. |
|SecondaryAccountNTLMX509StoreName | string, 기본값: "MY" |정적|NTLM 인증을 사용할 때 SecondaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 저장소 이름. |
|SecondaryAccountNTLMX509Thumbprint | string, 기본값: ""| 정적|NTLM 인증을 사용할 때 SecondaryAccountNTLMPasswordSecret에서 HMAC를 생성하는 데 사용되는 X509 인증서의 지문. |
|SecondaryAccountType | string, 기본값: ""|정적| FileStoreService에서 공유하는 ACL에 대한 주체의 보조 계정 유형 |
|SecondaryAccountUserName | string, 기본값: ""| 정적|FileStoreService에서 공유하는 ACL에 대한 주체의 보조 계정 사용자 이름 |
|SecondaryAccountUserPassword | SecureString, 기본값: 비어 있음 |정적|FileStoreService에서 공유하는 ACL에 대한 주체의 보조 계정 암호 |
|SecondaryFileCopyRetryDelayMilliseconds|uint, 기본값: 500|동적|파일 복사 다시 시도 지연(밀리초)입니다.|
|UseChunkContentInTransportMessage|bool, 기본값: TRUE|동적|v6.4에 도입된 업로드 프로토콜의 새 버전 사용을 위한 플래그입니다. 이 프로토콜 버전은 서비스 패브릭 전송을 사용하여 이전 버전에서 사용되는 SMB 프로토콜보다 더 나은 성능을 제공하는 이미지 저장소로 파일을 업로드합니다. |

## <a name="healthmanager"></a>HealthManager

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |bool, 기본값: false |정적|클러스터 상태 평가 정책이며, 애플리케이션 유형별 상태 평가를 사용하도록 설정됩니다. |
|MaxSuggestedNumberOfEntityHealthReports|int, 기본값: 100 |동적|Watchdog의 상태 보고 논리에 대한 우려를 제기하기 전에 엔터티에 포함할 수 있는 최대 상태 보고서 수입니다. 각 상태 엔터티에는 비교적 적은 수의 상태 보고서를 포함해야 합니다. 보고서 수가 이 숫자를 초과하면 Watchdog의 구현에 문제가 있을 수 있습니다. 보고서가 너무 많은 엔터티에는 엔터티가 평가될 때 경고 상태 보고서를 통해 플래그가 지정됩니다. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |bool, 기본값: false |정적|클러스터 상태 평가 정책이며, 경고는 오류로 처리됩니다. |
|MaxPercentUnhealthyApplications | int, 기본값: 0 |정적|클러스터 상태 평가 정책이며, 클러스터가 정상적으로 작동하도록 허용되는 비정상 애플리케이션의 최대 백분율입니다. |
|MaxPercentUnhealthyNodes | int, 기본값: 0 |정적|클러스터 상태 평가 정책이며, 클러스터가 정상적으로 작동하도록 허용되는 비정상 노드의 최대 백분율입니다. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, 기본값: 10|정적|클러스터 업그레이드 상태 평가 정책이며, 클러스터가 정상적으로 작동하는 데 허용되는 델타 비정상 노드에 대한 최대 백분율입니다. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, 기본값: 15|정적|클러스터 업그레이드 상태 평가 정책이며, 클러스터가 정상적으로 작동하는 데 허용되는 업그레이드 도메인의 델타 비정상 노드에 대한 최대 백분율입니다. |

## <a name="hosting"></a>Hosting

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |정수, 기본값: 10 |동적|시스템이 포기하기 전에 실패한 활성화를 다시 시도하는 횟수 |
|ActivationMaxRetryInterval |시간(초), 기본값: 300 |동적|모든 연속 활성화 실패 시 시스템은 ActivationMaxFailureCount까지 활성화를 다시 시도합니다. ActivationMaxRetryInterval은 모든 활성화 실패 후 다시 시도하기 전에 대기 시간 간격을 지정합니다. |
|ActivationRetryBackoffInterval |시간(초), 기본값: 5 |동적|모든 활성화 오류의 백오프 간격. 모든 연속 활성화 실패 시 시스템은 MaxActivationFailureCount까지 활성화를 다시 시도합니다. 모든 시도에서 다시 시도 간격은 연속 활성화 실패와 활성화 백오프 간격을 곱한 값입니다. |
|ActivationTimeout| TimeSpan, 기본값: Common::TimeSpan::FromSeconds(180)|동적| 시간 간격은 초 단위로 지정합니다. 애플리케이션 활성화, 비활성화 및 업그레이드에 대한 시간 제한입니다. |
|ApplicationHostCloseTimeout| TimeSpan, 기본값: Common::TimeSpan::FromSeconds(120)|동적| 시간 간격은 초 단위로 지정합니다. 자체 활성화된 프로세스에서 Fabric 종료가 감지되면 FabricRuntime에서 사용자의 호스트(applicationhost) 프로세스에서 모든 복제본을 닫습니다. 닫기 작업에 대한 시간 제한입니다. |
| CnsNetworkPluginCnmUrlPort | wstring, 기본값: L"48080" | 정적 | Azure cnm API URL 포트 |
| CnsNetworkPluginCnsUrlPort | wstring, 기본값: L"10090" | 정적 | Azure cns URL 포트 |
|ContainerServiceArguments|string, 기본값: "-H localhost:2375 -H npipe://"|정적|SF(서비스 패브릭)는 docker 디먼을 관리합니다(Win10 같은 Windows 클라이언트 컴퓨터는 제외). 이 구성을 통해 사용자는 시작할 때 docker 디먼에 전달되어야 하는 사용자 지정 인수를 지정할 수 있습니다. 사용자 지정 인수가 지정되면 Service Fabric은 Docker 엔진에 '--pidfile' 인수를 제외한 다른 인수를 전달하지 않습니다. 따라서 사용자는 '-pidfile' 인수를 자신의 고객 인수의 일부로 지정할 수 없습니다. 또한 사용자 지정 인수는 Service Fabric이 디먼과 통신할 수 있도록 docker 디먼이 Windows의 기본 이름 파이프(또는 Linux의 UNIX 도메인 소켓)를 수신 대기하도록 해야 합니다.|
|ContainerServiceLogFileMaxSizeInKb|int, 기본값은 32768입니다.|정적|Docker 컨테이너에서 생성된 로그 파일의 최대 파일 크기입니다.  Windows만 해당됩니다.|
|ContainerImageDownloadTimeout|int, 초 수, 기본값: 1200(20분)|동적|이미지 다운로드가 시간을 초과까지 걸리는 시간(초)입니다.|
|ContainerImagesToSkip|string. 세로줄 문자로 구분된 이미지 이름입니다. 기본값은 ""입니다.|정적|삭제하면 안 되는 컨테이너 이미지 하나 이상의 이름입니다.  PruneContainerImages 매개 변수와 함께 사용합니다.|
|ContainerServiceLogFileNamePrefix|string, 기본값은 "sfcontainerlogs"입니다.|정적|Docker 컨테이너에서 생성된 로그 파일의 파일 이름 접두사입니다.  Windows만 해당됩니다.|
|ContainerServiceLogFileRetentionCount|int, 기본값: 10|정적|로그 파일을 덮어쓰기 전에 Docker 컨테이너에서 생성된 로그 파일의 수입니다.  Windows만 해당됩니다.|
|CreateFabricRuntimeTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(120)|동적| 시간 간격은 초 단위로 지정합니다. 동기화 FabricCreateRuntime 호출에 대한 시간 제한 값입니다. |
|DefaultContainerRepositoryAccountName|string, 기본값: ""|정적|ApplicationManifest.xml에 지정된 자격 증명 대신 사용하는 기본 자격 증명 |
|DefaultContainerRepositoryPassword|string, 기본값: ""|정적|ApplicationManifest.xml에 지정된 자격 증명 대신 사용하는 기본 암호 자격 증명|
|DefaultContainerRepositoryPasswordType|string, 기본값: ""|정적|비어 있지 않은 문자열인 경우 이 값은 "Encrypted" 또는 "SecretsStoreRef"일 수 있습니다.|
|DefaultDnsSearchSuffixEmpty|bool, 기본값: FALSE|정적|기본적으로 서비스 이름은 컨테이너 서비스의 SF DNS 이름에 추가됩니다. 이 기능은 해결 경로에서 기본적으로 SF DNS 이름에 아무것도 추가되지 않도록 이 동작을 중지합니다.|
|DeploymentMaxFailureCount|int, 기본값: 20| 동적|노드에서 애플리케이션의 배포가 실패하기 전에 DeploymentMaxFailureCount 시간 동안 해당 애플리케이션의 배포가 다시 시도됩니다.| 
|DeploymentMaxRetryInterval| TimeSpan, 기본값: Common::TimeSpan::FromSeconds(3600)|동적| 시간 간격은 초 단위로 지정합니다. 배포에 대한 최대 다시 시도 간격입니다. 모든 연속 실패에서 다시 시도 간격은 Min(DeploymentMaxRetryInterval, 연속 실패 횟수 * DeploymentRetryBackoffInterval)으로 계산됩니다. |
|DeploymentRetryBackoffInterval| TimeSpan, 기본값: Common::TimeSpan::FromSeconds(10)|동적|시간 간격은 초 단위로 지정합니다. 배포 실패에 대한 백오프 간격입니다. 모든 연속 배포 실패에서 시스템은 최대 MaxDeploymentFailureCount회까지 배포를 다시 시도합니다. 다시 시도 간격은 연속 배포 실패와 배포 백오프 간격의 곱입니다. |
|DisableContainers|bool, 기본값: FALSE|정적|컨테이너를 사용하지 않도록 설정하기 위한 구성 - 더 이상 사용되지 않는 DisableContainerServiceStartOnContainerActivatorOpen 구성 대신 사용됩니다. |
|DisableDockerRequestRetry|bool, 기본값: FALSE |동적| 기본적으로 SF는 전송되는 각 http 요청에 대해 시간 제한 'DockerRequestTimeout' 동안 DD(docker 디먼)와 통신합니다. 이 기간 내에 DD가 응답하지 않으면 SF는 최상위 작업 시간이 아직 남아 있는 경우 요청을 다시 전송합니다.  hyperv 컨테이너 사용 시에는 DD가 컨테이너를 불러오거나 비활성화하는 데 시간이 훨씬 더 많이 걸릴 수도 있습니다. 이러한 경우 SF 측면에서 DD 요청의 시간이 초과되며, SF는 작업을 다시 시도합니다. 이로 인해 DD의 부담이 가중될 수도 있습니다. 이 구성을 사용하면 작업을 다시 시도하지 않도록 설정하고 DD가 응답할 때까지 기다릴 수 있습니다. |
|DnsServerListTwoIps | Bool, 기본값: FALSE | 정적 | 이 플래그는 간헐적인 문제 해결에 유용하도록 로컬 dns 서버를 두 번 추가합니다. |
| DoNotInjectLocalDnsServer | bool, 기본값: FALSE | 정적 | 런타임에서 로컬 IP를 컨테이너의 DNS 서버로 삽입하지 못하게 합니다 |
|EnableActivateNoWindow| bool, 기본값: FALSE|동적| 활성화된 프로세스가 콘솔 없이 백그라운드에서 만들어집니다. |
|EnableContainerServiceDebugMode|bool, 기본값: TRUE|정적|Docker 컨테이너에 대한 로깅을 사용/사용하지 않도록 설정합니다.  Windows만 해당됩니다.|
|EnableDockerHealthCheckIntegration|bool, 기본값: TRUE|정적|Docker HealthCheck 이벤트를 Service Fabric 시스템 상태 보고서와 통합할 수 있도록 합니다. |
|EnableProcessDebugging|bool, 기본값: FALSE|동적| 디버거에서 애플리케이션 호스트를 시작할 수 있습니다. |
|EndpointProviderEnabled| bool, 기본값: FALSE|정적| 엔드포인트 리소스를 Fabric별로 관리할 수 있습니다. FabricNode에서 애플리케이션 포트의 시작 및 끝 범위를 지정해야 합니다. |
|FabricContainerAppsEnabled| bool, 기본값: FALSE|정적| |
|FirewallPolicyEnabled|bool, 기본값: FALSE|정적| ServiceManifest에 지정된 명시적 포트가 있는 엔드포인트 리소스에 대한 방화벽 포트를 열 수 있습니다. |
|GetCodePackageActivationContextTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(120)|동적|시간 간격은 초 단위로 지정합니다. CodePackageActivationContext 호출에 대한 시간 제한 값입니다. 임시 서비스에는 적용되지 않습니다. |
|GovernOnlyMainMemoryForProcesses|bool, 기본값: FALSE|정적|리소스 거버넌스의 기본 동작은 프로세스가 사용하는 총 메모리 양(RAM + 스왑)에 대해 MemoryInMB에 지정된 제한을 적용하는 것입니다. 이 제한을 초과하면 프로세스는 OutOfMemory 예외를 수신합니다. 이 매개 변수를 true로 설정하면 제한이 프로세스가 사용할 RAM 메모리의 양에만 적용됩니다. 이 제한을 초과하고 이 설정이 true인 경우 OS는 주 메모리를 디스크로 교체합니다. |
|IPProviderEnabled|bool, 기본값: FALSE|정적|IP 주소를 관리할 수 있습니다. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, 기본값: FALSE|정적|DefaultContainerRepositoryPassword가 암호화되는지 여부|
|LinuxExternalExecutablePath|string, 기본값: "/usr/bin/" |정적|노드의 외부 실행 명령에 대한 기본 디렉터리입니다.|
|NTLMAuthenticationEnabled|bool, 기본값: FALSE|정적| 다른 사용자로 실행되는 코드 패키지에서 NTLM을 사용하여 컴퓨터 간 프로세스에서 안전하게 통신할 수 있도록 지원할 수 있습니다. |
|NTLMAuthenticationPasswordSecret|SecureString, 기본값: Common::SecureString("")|정적|NTLM 사용자의 암호를 생성하는 데 사용되는 암호화된 비밀입니다. NTLMAuthenticationEnabled가 true이면 설정해야 합니다. 배포자에서 유효성을 검사합니다. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, 기본값: Common::TimeSpan::FromMinutes(3)|동적|시간 간격은 초 단위로 지정합니다. 환경 관련 설정이며, FileStoreService NTLM 구성에 사용할 새 인증서를 Hosting에서 검색하는 주기적인 간격입니다. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, 기본값: Common::TimeSpan::FromMinutes(4)|동적| 시간 간격은 초 단위로 지정합니다. 인증서 일반 이름을 사용하여 NTLM 사용자를 구성하는 데 걸리는 시간 제한입니다. NTLM 사용자는 FileStoreService 공유에 필요합니다. |
|PruneContainerImages|bool, 기본값: FALSE|동적| 노드에서 사용되지 않는 애플리케이션 컨테이너 이미지를 제거합니다. Service Fabric 컨테이너에서 ApplicationType 등록을 취소하면 이 애플리케이션에서 사용했던 컨테이너 이미지가 Service Fabric에 의해 다운로드되었던 노드에서 제거됩니다. 정리는 1시간마다 실행되므로 클러스터에서 이미지가 제거될 때까지 최대 1시간 + 이미지 정리에 소요되는 시간이 걸릴 수 있습니다.<br>Service Fabric은 애플리케이션과 관련이 없는 이미지를 다운로드하거나 제거하지 않습니다.  수동으로 또는 기타 방식을 통해 다운로드한 관련이 없는 이미지는 명시적으로 제거해야 합니다.<br>삭제하지 않아야 하는 이미지는 ContainerImagesToSkip 매개 변수에서 지정할 수 있습니다.| 
|RegisterCodePackageHostTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(120)|동적| 시간 간격은 초 단위로 지정합니다. FabricRegisterCodePackageHost 동기화 호출에 대한 시간 제한 값입니다. FWP와 같은 다중 코드 패키지 애플리케이션 호스트에만 적용됩니다. |
|RequestTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(30)|동적| 시간 간격은 초 단위로 지정합니다. 다양한 호스팅 관련 작업(예: 팩터리 등록, 런타임 등록)을 위한 사용자의 애플리케이션 호스트와 Fabric 프로세스 간 통신에 대한 시간 제한을 나타냅니다. |
|RunAsPolicyEnabled| bool, 기본값: FALSE|정적| 패브릭 프로세스가 실행되는 사용자 이외의 로컬 사용자로 코드 패키지를 실행할 수 있습니다. 이 정책을 사용하려면 Fabric이 SYSTEM 또는 SeAssignPrimaryTokenPrivilege가 있는 사용자로 실행되어야 합니다. |
|ServiceFactoryRegistrationTimeout| TimeSpan, 기본값: Common::TimeSpan::FromSeconds(120)|동적|시간 간격은 초 단위로 지정합니다. 동기화 Register(Stateless/Stateful)ServiceFactory 호출에 대한 시간 제한 값입니다. |
|ServiceTypeDisableFailureThreshold |정수, 기본값: 1 |동적|해당 노드에서 서비스 유형을 사용하지 않도록 설정하고 다른 노드에 배치를 시도하도록 FM(FailoverManager)에 알리기까지 허용되는 실패 횟수에 대한 임계값입니다. |
|ServiceTypeDisableGraceInterval|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(30)|동적|시간 간격은 초 단위로 지정합니다. 서비스 유형을 사용 해제할 수 있는 시간 간격입니다. |
|ServiceTypeRegistrationTimeout |시간(초), 기본값: 300 |동적|ServiceType을 패브릭에 등록하는 데 허용되는 최대 시간 |
|UseContainerServiceArguments|bool, 기본값: TRUE|정적|이 구성은 호스팅에 docker 디먼으로의 인수(ContainerServiceArguments 구성에 지정된) 전달을 건너뛰도록 지시합니다.|

## <a name="httpgateway"></a>HttpGateway

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ActiveListeners |uint, 기본값: 50 |정적| http 서버 큐에 게시할 읽기 수. HttpGateway에서 충족할 수 있는 동시 요청 수를 제어합니다. |
|HttpGatewayHealthReportSendInterval |시간(초), 기본값: 30 |정적|시간 간격은 초 단위로 지정합니다. Http 게이트웨이에서 상태 관리자로 누적 상태 보고서를 보내는 간격입니다. |
|HttpStrictTransportSecurityHeader|string, 기본값: ""|동적| HttpGateway에서 전송된 모든 응답에 포함할 HTTP Strict Transport Security 헤더 값을 지정합니다. 빈 문자열로 설정되면 이 헤더는 게이트웨이 응답에 포함되지 않습니다.|
|IsEnabled|bool, 기본값: false |정적| HttpGateway를 사용하거나 사용하지 않도록 설정합니다. HttpGateway는 기본적으로 사용하지 않도록 설정되어 있습니다. |
|MaxEntityBodySize |uint, 기본값: 4194304 |동적|http 요청에서 사용할 수 있는 본문의 최대 크기를 제공합니다. 기본값은 4MB입니다. 본문 크기가 이 값보다 크면 Httpgateway 요청이 실패합니다. 최소 읽기 청크 크기가 4,096바이트이므로 이 값은 4096 이상이어야 합니다. |

## <a name="imagestoreservice"></a>ImageStoreService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|사용 |bool, 기본값: false |정적|ImageStoreService의 Enabled 플래그입니다. 기본값: false |
|MinReplicaSetSize | int, 기본값: 3 |정적|ImageStoreService의 MinReplicaSetSize입니다. |
|PlacementConstraints | string, 기본값: "" |정적| ImageStoreService의 PlacementConstraints입니다. |
|QuorumLossWaitDuration | time(초), 기본값: MaxValue |정적| 시간 간격은 초 단위로 지정합니다. ImageStoreService의 QuorumLossWaitDuration입니다. |
|ReplicaRestartWaitDuration | 시간(초), 기본값: 60.0 \* 30 |정적|시간 간격은 초 단위로 지정합니다. ImageStoreService의 ReplicaRestartWaitDuration입니다. |
|StandByReplicaKeepDuration | 시간(초), 기본값: 3600.0 \* 2 |정적| 시간 간격은 초 단위로 지정합니다. ImageStoreService의 StandByReplicaKeepDuration입니다. |
|TargetReplicaSetSize | int, 기본값: 7 |정적|ImageStoreService의 TargetReplicaSetSize입니다. |

## <a name="ktllogger"></a>KtlLogger

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |int, 기본값: 1 |동적|메모리 설정을 자동 및 동적으로 구성해야 하는지 여부를 나타내는 플래그. 0인 경우 메모리 구성 설정을 직접 사용하고 시스템 조건에 따라 변경하지 않습니다. 1인 경우 메모리 설정을 자동으로 구성하며 시스템 조건에 따라 변경할 수 있습니다. |
|MaximumDestagingWriteOutstandingInKB | int, 기본값: 0 |동적|공유 로그가 전용 로그보다 먼저 진행될 수 있도록 허용하는 KB 수. 제한하지 않으려면 0을 사용합니다.
|SharedLogId |string, 기본값: "" |정적|공유 로그 컨테이너에 대한 고유 GUID. 패브릭 데이터 루트 아래의 기본 경로를 사용하는 경우 ""을 사용합니다. |
|SharedLogPath |string, 기본값: "" |정적|공유 로그 컨테이너를 배치할 경로 및 파일 이름. 패브릭 데이터 루트 아래의 기본 경로를 사용하려면 ""을 사용합니다. |
|SharedLogSizeInMB |int, 기본값: 8192 |정적|공유 로그 컨테이너에 할당할 MB 수 |
|SharedLogThrottleLimitInPercentUsed|int, 기본값: 0 | 정적 | 제한이 적용되도록 할 공유 로그의 사용량 백분율입니다. 값은 0에서 100 사이여야 합니다. 값을 0으로 지정하면 기본 백분율 값이 사용됩니다. 값을 100으로 지정하면 제한이 적용되지 않습니다. 1~99 사이의 값은 초과 시에 제한이 적용되는 로그 사용 백분율을 지정합니다. 예를 들어 공유 로그가 10GB일 때 값을 90으로 지정하는 경우 사용 용량이 9GB보다 많으면 제한이 적용됩니다. 기본값을 사용하는 것이 좋습니다.|
|WriteBufferMemoryPoolMaximumInKB | int, 기본값: 0 |동적|쓰기 버퍼 메모리 풀이 확장할 수 있는 KB 수. 제한하지 않으려면 0을 사용합니다. |
|WriteBufferMemoryPoolMinimumInKB |int, 기본값: 8388608 |동적|쓰기 버퍼 메모리 풀에 처음으로 할당할 KB 수. 제한하지 않으려면 0을 사용합니다. 기본값은 아래의 SharedLogSizeInMB와 일치해야 합니다. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|IsEnabled|bool, 기본값: FALSE|정적|클러스터에서 관리 ID 토큰 서비스의 존재 및 상태를 제어하는 플래그입니다. Service Fabric 애플리케이션의 관리 ID 기능을 사용하기 위한 필수 구성 요소입니다.|

## <a name="management"></a>관리

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, 기본값: Common::TimeSpan::FromMinutes(5)|동적|시간 간격은 초 단위로 지정합니다. 자동 애플리케이션 유형 정리 중 등록 취소 애플리케이션 유형에 허용되는 정리 간격입니다.|
|AzureStorageMaxConnections | int, 기본값: 5000 |동적|Azure Storage에 동시에 연결할 수 있는 최대 수 |
|AzureStorageMaxWorkerThreads | int, 기본값: 25 |동적|병렬 작업자 스레드의 최대 수 |
|AzureStorageOperationTimeout | time(초), 기본값: 6000 |동적|시간 간격은 초 단위로 지정합니다. xstore 작업을 완료하는 걸리는 시간 제한입니다. |
|CleanupApplicationPackageOnProvisionSuccess|bool, 기본값: FALSE |동적|프로비저닝 성공 시 애플리케이션 패키지의 자동 정리를 사용하거나 사용하지 않도록 설정합니다.

*모범 사례는를 사용 하는 것입니다 `true` .* | | CleanupUnusedApplicationTypes | Bool, 기본값은 FALSE |입니다. 동적 | 이 구성을 사용 하는 경우 사용 되지 않는 최신 버전을 건너뛴 사용 하지 않는 응용 프로그램 유형 버전의 등록을 자동으로 취소 하 여 이미지 저장소에서 사용 하는 디스크 공간을 자릅니다. 자동 정리는 해당하는 특정 앱 유형에 대한 프로비저닝이 성공하면 트리거되고 모든 애플리케이션 유형에 대해 하루에 한 번 주기적으로 실행됩니다. 건너뛸 사용하지 않은 버전 수는 "MaxUnusedAppTypeVersionsToKeep" 매개 변수를 사용하여 구성할 수 있습니다. 

*모범 사례는를 사용 하는 것입니다 `true` .*
| | DisableChecksumValidation | Bool, 기본값은 false |입니다. 정적 | 이 구성을 통해 응용 프로그램 프로 비전 중에 체크섬 유효성 검사를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. | | DisableServerSideCopy | Bool, 기본값은 false |입니다. 정적 | 이 구성은 응용 프로그램을 프로 비전 하는 동안 ImageStore에서 응용 프로그램 패키지의 서버 쪽 복사본을 사용 하거나 사용 하지 않도록 설정 합니다. | | ImageCachingEnabled | Bool, 기본값은 true |입니다. 정적 | 이 구성을 사용 하면 캐싱을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. | | ImageStoreConnectionString | SecureString | 정적 | ImageStore의 루트에 대 한 연결 문자열입니다. | | ImageStoreMinimumTransferBPS | Int, 기본값 1024 | 동적 | 클러스터와 ImageStore 간의 최소 전송 률입니다. 외부 ImageStore에 액세스할 때 시간 제한을 결정하는 데 사용됩니다. 클러스터와 ImageStore 간에 대기 시간이 높은 경우에만 이 값을 변경하여 클러스터가 외부 ImageStore에서 다운로드하는 데 더 많은 시간을 허용합니다. | | MaxUnusedAppTypeVersionsToKeep | Int, 기본값은 3 | 동적 | 이 구성에서는 정리를 위해 건너뛸 사용 하지 않는 응용 프로그램 형식 버전의 수를 정의 합니다. 이 매개 변수는 CleanupUnusedApplicationTypes 매개 변수를 사용하는 경우에만 적용할 수 있습니다.

*일반적인 모범 사례는 기본값 ()을 사용 하는 것입니다 `3` .*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, 기본값: None|동적|클러스터의 메트릭에 대한 MetricActivityThresholds 집합을 결정합니다. maxNodeLoad가 MetricActivityThresholds보다 크면 분산이 작동합니다. 조각 모음 메트릭의 경우 Service Fabric에서 빈 노드로 간주하게 되는 값보다 작거나 같은 부하의 양을 정의합니다 |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, 기본값: None|동적|클러스터의 메트릭에 대한 MetricBalancingThresholds 집합을 결정합니다. maxNodeLoad/minNodeLoad가 MetricBalancingThresholds보다 크면 분산이 작동합니다. 하나 이상의 FD 또는 UD에서 maxNodeLoad/minNodeLoad가 MetricBalancingThresholds보다 작으면 조각 모음이 작동합니다. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, 기본값: None|동적|교환 시 복제본에 고정되는 부하 부분을 결정하며, 0(부하가 복제본에 고정되지 않음)과 1(부하가 복제본에 고정됨 - 기본값) 사이의 값을 사용합니다. |

## <a name="namingservice"></a>NamingService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int, 기본값: 0 |정적|이름 지정 게이트웨이의 LRU 서비스 설명 캐시에 유지되는 최대 항목 수(제한하지 않을 경우 0으로 설정). |
|MaxClientConnections |int, 기본값: 1000 |동적|게이트웨이당 허용되는 최대 클라이언트 연결 수. |
|MaxFileOperationTimeout |시간(초), 기본값: 30 |동적|시간 간격은 초 단위로 지정합니다. 파일 저장소 서비스 작업에 허용되는 최대 시간 제한. 더 큰 시간 제한을 지정하는 요청은 거부됩니다. |
|MaxIndexedEmptyPartitions |int, 기본값: 1000 |동적|다시 연결하는 클라이언트를 동기화하기 위해 알림 캐시에 인덱싱되어 있는 빈 파티션의 최대 수. 이 수를 초과하는 빈 파티션은 모두 인덱스에서 조회 버전 오름차순으로 제거됩니다. 다시 연결하는 클라이언트를 다시 연결하면 빈 파티션 업데이트를 계속 동기화하고 받을 수 있지만 동기화 프로토콜에 더 많은 비용이 듭니다. |
|MaxMessageSize |int, 기본값: 4\*1024\*1024 |정적|이름 지정을 사용할 때 클라이언트 노드 통신에 대한 최대 메시지 크기. DOS 공격을 낮추려는 경우 기본값은 4MB입니다. |
|MaxNamingServiceHealthReports | int, 기본값: 10 |동적|한 번에 이름 지정 저장소 서비스에서 비정상 상태로 보고하는 느린 작업의 최대 수. 0인 경우 모든 느린 작업이 전송됩니다. |
|MaxOperationTimeout |time(초), 기본값: 600 |동적|시간 간격은 초 단위로 지정합니다. 클라이언트 작업에 허용되는 최대 시간 제한. 더 큰 시간 제한을 지정하는 요청은 거부됩니다. |
|MaxOutstandingNotificationsPerClient |int, 기본값: 1000 |동적|게이트웨이에서 클라이언트 등록을 강제로 닫기 전에 처리되지 않은 알림의 최대 수. |
|MinReplicaSetSize | int, 기본값: 3 |허용되지 않음| 업데이트를 완료하기 위해 작성하는 데 필요한 이름 지정 서비스 복제본의 최소 수. 시스템에 이 활성 값보다 적은 수의 복제본이 있는 경우 Reliability System(안정성 시스템)에서 복제본이 복원될 때까지 이름 지정 서비스 저장소에 대한 업데이트를 거부합니다. 이 값은 반드시 TargetReplicaSetSize 이하여야 합니다. |
|PartitionCount |int, 기본값: 3 |허용되지 않음|만들려는 이름 지정 서비스 저장소의 파티션 수. 각 파티션은 자체의 인덱스에 해당하는 단일 파티션 키를 소유하므로 파티션 키[0; PartitionCount]가 존재합니다. 이름 지정 서비스 파티션의 수를 늘리면 PartitionCount * ReplicaSetSize 서비스 복제본을 유지해야 하므로 리소스 사용이 늘어나더라도 모든 백업 복제본 세트에서 보유하는 평균 데이터 양을 줄여 이름 지정 서비스에서 수행할 수 있는 크기가 늘어납니다.|
|PlacementConstraints | string, 기본값: "" |허용되지 않음| 이름 지정 서비스에 대한 배치 제약 조건. |
|QuorumLossWaitDuration | time(초), 기본값: MaxValue |허용되지 않음| 시간 간격은 초 단위로 지정합니다. 이름 지정 서비스가 쿼럼 손실 상태가 될 때 이 타이머가 시작됩니다. 만료되면 FM에서 중단된 복제본을 손실된 것으로 간주하고 쿼럼을 복구하려고 시도합니다. 데이터가 손실될 수 있습니다. |
|RepairInterval | time(초), 기본값: 5 |정적| 시간 간격은 초 단위로 지정합니다. 기관 소유자와 이름 소유자 간의 이름 지정 불일치 복구가 시작되는 간격 |
|ReplicaRestartWaitDuration | time(초), 기본값: 60.0 * 30|허용되지 않음| 시간 간격은 초 단위로 지정합니다. 이름 지정 서비스 복제본이 중단될 때 이 타이머가 시작됩니다. 만료되면 FM은 다운되는 복제본을 대체하기 시작합니다(분실된 것으로 간주하지 않음). |
|ServiceDescriptionCacheLimit | int, 기본값: 0 |정적| 이름 지정 저장소 서비스의 LRU 서비스 설명 캐시에 유지되는 최대 항목 수(제한하지 않을 경우 0으로 설정). |
|ServiceNotificationTimeout |시간(초), 기본값: 30 |동적|시간 간격은 초 단위로 지정합니다. 클라이언트에 서비스 알림을 배달할 때 사용되는 시간 제한입니다. |
|StandByReplicaKeepDuration | time(초), 기본값: 3600.0 * 2 |허용되지 않음| 시간 간격은 초 단위로 지정합니다. 이름 지정 서비스 복제본이 중단된 상태에서 돌아올 때 이 복제본이 이미 대체되었을 수 있습니다. 이 타이머는 FM에서 대기 복제본을 삭제하기 전에 보관할 기간을 결정합니다. |
|TargetReplicaSetSize |int, 기본값: 7 |허용되지 않음|이름 지정 서비스 저장소의 각 파티션에 대한 복제본 집합 수. 복제본 집합의 수를 늘리면 Windows Fabric에 대한 로드가 증가하고 이름 지정 데이터 업데이트를 수행하는 데 시간을 소모하더라도 이름 지정 서비스 저장소에 있는 정보의 안정성 수준이 높아지고 노드 장애의 결과로 정보가 손실될 변화를 낮춥니다.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, 기본값: None|동적|메트릭 이름당 노드 용량 백분율이며, 장애 조치 시 노드에서 사용 가능한 약간의 공간을 유지하기 위해 버퍼로 사용됩니다. |

## <a name="nodecapacities"></a>NodeCapacities

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |정적|다양한 메트릭에 대한 노드 용량 컬렉션 |

## <a name="nodedomainids"></a>NodeDomainIds

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |정적|노드가 속한 장애 도메인을 설명합니다. 장애 도메인은 데이터 센터에서 노드의 위치를 설명하는 URI를 통해 정의됩니다.  장애 도메인 URI에는 fd:/fd/ 형식 뒤에 URI 경로 세그먼트가 나옵니다.|
|UpgradeDomainId |string, 기본값: "" |정적|노드가 속한 업그레이드 도메인을 설명합니다. |

## <a name="nodeproperties"></a>NodeProperties

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |정적|노드 속성에 대한 문자열 키-값 쌍 컬렉션 |

## <a name="paas"></a>Paas

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ClusterId |string, 기본값: "" |허용되지 않음|구성을 보호하기 위해 패브릭에서 사용하는 X509 인증서 저장소 |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|Counters |String | 동적 |수집할 성능 카운터의 쉼표로 구분된 목록 |
|IsEnabled |bool, 기본값: true | 동적 |플래그에서 로컬 노드의 성능 카운터 수집을 사용하는지 여부를 나타냅니다. |
|MaxCounterBinaryFileSizeInMB |int, 기본값: 1 | 동적 |각 성능 카운터 이진 파일의 최대 크기(MB) |
|NewCounterBinaryFileCreationIntervalInMinutes |int, 기본값: 10 | 동적 |새 성능 카운터 이진 파일이 만들어지는 최대 간격(초) |
|SamplingIntervalInSeconds |int, 기본값: 60 | 동적 |수집되는 성능 카운터의 샘플링 간격 |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int, 기본값: 0 | 동적|선호도 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|ApplicationCapacityConstraintPriority | int, 기본값: 0 | 동적|용량 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|AutoDetectAvailableResources|bool, 기본값: TRUE|정적|이 구성은 노드(CPU 및 메모리)에서 사용 가능한 리소스에 대한 자동 검색을 트리거합니다. 이 구성을 true로 설정하면 사용자가 잘못된 노드 용량을 지정하거나 전혀 정의하지 않은 경우 실제 용량을 읽고 수정합니다. false로 설정하면 사용자가 잘못된 노드 용량을 지정했다는 경고를 추적하지만 수정하지는 않습니다. 즉 사용자가 노드의 실제 용량보다 큰 용량을 지정하거나, 용량이 정의되지 않은 경우 무제한 용량을 가정하게 됩니다. |
|BalancingDelayAfterNewNode | time(초), 기본값: 120 |동적|시간 간격은 초 단위로 지정합니다. 새 노드를 추가한 이후 이 기간 내에 작업 분산을 시작하면 안됩니다. |
|BalancingDelayAfterNodeDown | time(초), 기본값: 120 |동적|시간 간격은 초 단위로 지정합니다. 노드 작동 중단 이벤트 이후 이 기간 내에 작업 분산을 시작하면 안됩니다. |
|CapacityConstraintPriority | int, 기본값: 0 | 동적|용량 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|ConsecutiveDroppedMovementsHealthReportLimit | int, 기본값: 20 | 동적|진단을 수행하고 상태 경고를 내보내기 전에 ResourceBalancer에서 발급한 Movements(이동)가 삭제되는 연속 횟수를 정의합니다. 음수: 이 조건에서는 경고를 내보내지 않습니다. |
|ConstraintFixPartialDelayAfterNewNode | time(초), 기본값: 120 |동적| 시간 간격은 초 단위로 지정합니다. 새 노드를 추가한 이후 이 기간 내에 FaultDomain 및 UpgradeDomain 제약 조건 위반을 수정하면 안됩니다. |
|ConstraintFixPartialDelayAfterNodeDown | time(초), 기본값: 120 |동적| 시간 간격은 초 단위로 지정합니다. 노드 작동 중단 이후 이 기간 내에 FaultDomain 및 UpgradeDomain 제한 조건 위반을 수정하면 안됩니다. |
|ConstraintViolationHealthReportLimit | int, 기본값: 50 |동적| 진단을 수행하고 상태 보고서를 내보내기 전에 제약 조건 위반 복제본이 영구적으로 고정되지 않아야 하는 횟수를 정의합니다. |
|DetailedConstraintViolationHealthReportLimit | int, 기본값: 200 |동적| 진단을 수행하고 자세한 상태 보고서를 내보내기 전에 제약 조건 위반 복제본이 영구적으로 고정되지 않아야 하는 횟수를 정의합니다. |
|DetailedDiagnosticsInfoListLimit | int, 기본값: 15 |동적| 진단에서 자르기 전에 포함할 제약 조건당 진단 항목 수(세부 정보 포함)를 정의합니다.|
|DetailedNodeListLimit | int, 기본값: 15 |동적| 배치되지 않은 복제본 보고서에서 자르기 전에 포함할 제약 조건당 노드 수를 정의합니다. |
|DetailedPartitionListLimit | int, 기본값: 15 |동적| 진단에서 자르기 전에 포함할 제약 조건의 진단 항목당 파티션 수를 정의합니다. |
|DetailedVerboseHealthReportLimit | int, 기본값: 200 | 동적|자세한 상태 보고서를 내보내기 전에 배치되지 않은 복제본이 영구적으로 배치되지 않아야 하는 횟수를 정의합니다. |
|EnforceUserServiceMetricCapacities|bool, 기본값: FALSE | 정적 |패브릭 서비스 보호를 사용하도록 설정합니다. 모든 사용자 서비스는 하나의 작업 개체/cgroup 아래에 있으며 지정된 양의 리소스로 제한됩니다. 사용자 작업 개체 생성/제거 및 제한 설정이 패브릭 호스트를 여는 동안 수행되기 때문에 정적이어야 합니다(FabricHost를 다시 시작해야 함). |
|FaultDomainConstraintPriority | int, 기본값: 0 |동적| 장애 도메인 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|GlobalMovementThrottleCountingInterval | time(초), 기본값: 600 |정적| 시간 간격은 초 단위로 지정합니다. GlobalMovementThrottleThreshold와 함께 사용되는 도메인 복제본 이동별로 추적할 이전 간격의 길이를 나타냅니다. 전역 제한을 모두 무시하려면 0으로 설정할 수 있습니다. |
|GlobalMovementThrottleThreshold | uint, 기본값: 1000 |동적| GlobalMovementThrottleCountingInterval에서 지시한 이전 간격에서 분산 단계에 허용되는 최대 이동 횟수 |
|GlobalMovementThrottleThresholdForBalancing | uint, 기본값: 0 | 동적|GlobalMovementThrottleCountingInterval에서 지시한 이전 간격에서 분산 단계에 허용되는 최대 이동 횟수. 0은 제한하지 않음을 나타냅니다. |
|GlobalMovementThrottleThresholdForPlacement | uint, 기본값: 0 |동적| GlobalMovementThrottleCountingInterval에서 지시한 이전 간격에서 배치 단계에 허용되는 최대 이동 횟수. 0은 제한하지 않음을 나타냅니다.|
|GlobalMovementThrottleThresholdPercentage|double, 기본값: 0|동적|GlobalMovementThrottleCountingInterval로 표시된 이전 간격의 분산 및 배치 단계에서 허용되는 최대 총 이동 수(클러스터의 총 복제본 수 대비 백분율로 표시)입니다. 0은 제한하지 않음을 나타냅니다. 이 매개 변수 및 GlobalMovementThrottleThreshold를 모두 지정하면 좀 더 보수적인 제한이 사용됩니다.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, 기본값: 0|동적|GlobalMovementThrottleCountingInterval로 표시된 이전 간격의 분산 단계에서 허용되는 최대 이동 수(PLB의 총 복제본 수 대비 백분율로 표시)입니다. 0은 제한하지 않음을 나타냅니다. 이 매개 변수 및 GlobalMovementThrottleThresholdForBalancing를 모두 지정하면 좀 더 보수적인 제한이 사용됩니다.|
|InBuildThrottlingAssociatedMetric | string, 기본값: "" |정적| 이 제한의 관련 메트릭 이름 |
|InBuildThrottlingEnabled | bool, 기본값: false |동적| 빌드 내(in-build) 제한을 사용할 수 있는지 결정합니다. |
|InBuildThrottlingGlobalMaxValue | int, 기본값: 0 |동적|전역으로 허용되는 빌드 내 복제본의 최대 수 |
|InterruptBalancingForAllFailoverUnitUpdates | bool, 기본값: false | 동적|모든 유형의 장애 조치(failover) 단위 업데이트에서 고속 또는 저속 분산 실행을 중단해야 하는지 여부를 결정합니다. "false"로 지정한 경우 FailoverUnit:가 만들어지거나 삭제되거나, 복제본이 누락되거나, 주 복제본 위치가 변경되거나, 복제본 수가 변경되면 분산 실행이 중단됩니다. 다른 경우, 즉 FailoverUnit:에 여분의 복제본이 있거나, 모든 복제본 플래그가 변경되거나, 파티션 버전만 변경되거나, 기타 모든 경우에는 분산 실행이 중단되지 않습니다. |
|MinConstraintCheckInterval | time(초), 기본값: 1 |동적| 시간 간격은 초 단위로 지정합니다. 두 번의 연속 제약 조건 확인 라운드가 통과해야 하는 최소 시간을 정의합니다. |
|MinLoadBalancingInterval | time(초), 기본값: 5 |동적| 시간 간격은 초 단위로 지정합니다. 두 번의 연속 분산 라운드를 통과해야 하는 최소 시간을 정의합니다. |
|MinPlacementInterval | time(초), 기본값: 1 |동적| 시간 간격은 초 단위로 지정합니다. 두 번의 연속 배치 라운드를 통과해야 하는 최소 시간을 정의합니다. |
|MoveExistingReplicaForPlacement | bool, 기본값: true |동적|배치 중에 기존 복제본을 이동할지 여부를 결정하는 설정 |
|MovementPerPartitionThrottleCountingInterval | time(초), 기본값: 600 |정적| 시간 간격은 초 단위로 지정합니다. MovementPerPartitionThrottleThreshold와 함께 사용되는 각 파티션에 대한 복제본 이동을 추적할 이전 간격의 길이를 나타냅니다. |
|MovementPerPartitionThrottleThreshold | uint, 기본값: 50 |동적| 파티션의 복제본과 관련된 이동의 분산 수가 MovementPerPartitionThrottleCountingInterval로 표시된 이전 간격의 MovementPerFailoverUnitThrottleThreshold에 도달하거나 초과하면 해당 파티션에 대한 분산 관련 작업이 수행되지 않습니다. |
|MoveParentToFixAffinityViolation | bool, 기본값: false |동적| 선호도 제약 조건을 수정하기 위해 부모 복제본을 이동할 수 있는지 여부를 결정하는 설정|
|PartiallyPlaceServices | bool, 기본값: true |동적| 제한된 적합한 노드가 지정되면 클러스터에 있는 모든 서비스 복제본을 "모두 배치하거나 전혀 배치하지 않을지"를 결정합니다.|
|PlaceChildWithoutParent | bool, 기본값: true | 동적|부모 복제본이 없는 경우 자식 서비스 복제본을 배치할 수 있는지 여부를 결정하는 설정 |
|PlacementConstraintPriority | int, 기본값: 0 | 동적|배치 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|PlacementConstraintValidationCacheSize | int, 기본값: 10000 |동적| 배치 제약 조건 식의 빠른 유효성 검사와 캐싱에 사용되는 테이블의 크기를 제한합니다. |
|PlacementSearchTimeout | time(초), 기본값: 0.5 |동적| 시간 간격은 초 단위로 지정합니다. 서비스를 배치할 때 결과를 반환하기 전까지 이 기간에서 최대한 오랫동안 검색합니다. |
|PLBRefreshGap | time(초), 기본값: 1 |동적| 시간 간격은 초 단위로 지정합니다. PLB에서 상태를 다시 새로 고치기 전에 경과해야 하는 최소 시간을 정의합니다. |
|PreferredLocationConstraintPriority | int, 기본값: 2| 동적|위치 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 2: 최적화; 음수: 무시 |
|PreferredPrimaryDomainsConstraintPriority| int, 기본값: 1 | 동적| 기본 도메인 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시 |
|PreferUpgradedUDs|bool, 기본값: FALSE|동적|기본적으로 이미 업그레이드된 UD로 전환하는 논리를 켜거나 끕니다. SF 7.0부터 이 매개 변수의 기본값이 TRUE에서 FALSE로 변경됩니다.|
|PreventTransientOvercommit | bool, 기본값: false | 동적|PLB에서 시작된 이동으로 인해 확보될 리소스를 즉시 계산해야 하는지 결정합니다. 기본적으로 PLB는 일시적인 초과 커밋을 만들 수 있는 동일한 노드에서 인출과 인입을 시작할 수 있습니다. 이 매개 변수를 true로 설정하면 이러한 종류의 초과 커밋을 방지하고 주문형 조각 모음, 즉 placementWithMove를 사용할 수 없게 됩니다. |
|ScaleoutCountConstraintPriority | int, 기본값: 0 |동적| 확장 수 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|SubclusteringEnabled|Bool, 기본값: FALSE | 동적 |분산을 위한 표준 편차를 계산할 때 서브 클러스터링 승인 |
|SubclusteringReportingPolicy| int, 기본값: 1 |동적|서브 클러스터링 상태 보고서를 전송하는 방법과 경우를 정의합니다. 0: 보고 안 함; 1: 경고; 2: 확인 |
|SwapPrimaryThrottlingAssociatedMetric | string, 기본값: ""|정적| 이 제한의 관련 메트릭 이름 |
|SwapPrimaryThrottlingEnabled | bool, 기본값: false|동적| 스왑-주(swap-primary) 제한을 사용할 수 있는지 결정합니다. |
|SwapPrimaryThrottlingGlobalMaxValue | int, 기본값: 0 |동적| 전역으로 허용되는 스왑-주 복제본의 최대 수 |
|TraceCRMReasons |bool, 기본값: true |동적|CRM에서 작업 이벤트 채널로의 이동을 발급한 이유를 추적할지 여부를 지정합니다. |
|UpgradeDomainConstraintPriority | int, 기본값: 1| 동적|업그레이드 도메인 제약 조건의 우선 순위를 결정합니다. 0: 하드; 1: 소프트; 음수: 무시. |
|UseMoveCostReports | bool, 기본값: false | 동적|점수 매기기 함수의 비용 요소를 무시하도록 LB에 지시합니다. 보다 효율적인 분산 배치를 위해 잠재적으로 많은 수의 이동이 발생합니다. |
|UseSeparateSecondaryLoad | bool, 기본값: true | 동적|보조 복제본에 대해 별도의 부하를 사용해야 하는지 여부를 결정하는 설정입니다. |
|UseSeparateSecondaryMoveCost | bool, 기본값: false | 동적|보조 복제본에 대해 별도의 이동 비용을 사용해야 하는지 여부를 결정하는 설정입니다. |
|ValidatePlacementConstraint | bool, 기본값: true |동적| 서비스의 ServiceDescription을 업데이트할 때 서비스에 대한 PlacementConstraint 식의 유효성을 검사할지 여부를 지정합니다. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, 기본값: TRUE |동적|서비스에 대한 PlacementConstraint 식을 장애 조치(failover) 시 주 기본 설정에 대해 평가할지 여부를 지정합니다. |
|VerboseHealthReportLimit | int, 기본값: 20 | 동적|상태 경고를 보고하기 전에 복제본이 배치되지 않아야 하는 횟수를 정의합니다(자세한 상태 보고를 사용하도록 설정한 경우). |
|NodeLoadsOperationalTracingEnabled | bool, 기본값: true |동적|이벤트 저장소에서 노드 로드 운영 구조 추적을 가능하게 설정하는 구성입니다. |
|NodeLoadsOperationalTracingInterval | TimeSpan, 기본값: Common::TimeSpan::FromSeconds(20) | 동적|시간 간격은 초 단위로 지정합니다. 각 서비스 도메인의 이벤트 저장소에 대한 노드 부하를 추적하는 간격입니다. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | time(초), 기본값: 900 |동적|시간 간격은 초 단위로 지정합니다. 애플리케이션 업그레이드 중에 닫힌 복제본이 있는 서비스 호스트를 종료하기 전에 시스템에서 기다리는 기간입니다.|
|FabricUpgradeMaxReplicaCloseDuration | time(초), 기본값: 900 |동적| 시간 간격은 초 단위로 지정합니다. 패브릭 업그레이드 중에 닫힌 복제본이 있는 서비스 호스트를 종료하기 전에 시스템에서 기다리는 기간입니다. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(120)|동적|시간 간격은 초 단위로 지정합니다. 긴밀하게 붙어 있는 복제본을 갖춘 서비스 호스트를 종료하기 전에 시스템에서 대기하는 기간입니다. 이 값을 0으로 설정하는 경우 복제본을 닫도록 지시되지 않습니다.|
|NodeDeactivationMaxReplicaCloseDuration | time(초), 기본값: 900 |동적|시간 간격은 초 단위로 지정합니다. 노드 비활성화 중에 닫힌 복제본이 있는 서비스 호스트를 종료하기 전에 시스템에서 기다리는 기간입니다. |
|PeriodicApiSlowTraceInterval | time(초), 기본값: 5분 |동적| 시간 간격은 초 단위로 지정합니다. PeriodicApiSlowTraceInterval은 API 모니터에서 느린 API 호출을 다시 추적할 간격을 정의합니다. |
|ReplicaChangeRoleFailureRestartThreshold|int, 기본값: 10|동적| 정수입니다. 기본 수준 승격 중에 자동 완화 작업(복제본 다시 시작)이 적용되는 API 실패 수를 지정합니다. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, 기본값: 2147483647|동적| 정수입니다. 기본 수준 승격 중에 경고 상태 보고서가 발생되는 API 실패 수를 지정합니다.|
|ServiceApiHealthDuration | time(초), 기본값: 30분 |동적| 시간 간격은 초 단위로 지정합니다. ServiceApiHealthDuration은 정상이 아닌 서비스 API로 보고하기 전에 서비스 API가 실행될 때까지 대기하는 시간을 정의합니다. |
|ServiceReconfigurationApiHealthDuration | 시간(초), 기본값: 30 |동적| 시간 간격은 초 단위로 지정합니다. 비정상으로 보고하기 전에 서비스 API가 실행될 때까지 기다리는 시간을 정의합니다. 가용성에 영향을 주는 API 호출에 적용됩니다.|

## <a name="replication"></a>복제
| **매개 변수** | **허용되는 값** | **업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, 기본값: Common::TimeSpan::FromMilliseconds(15)|정적|시간 간격은 초 단위로 지정합니다. 복제자에서 작업을 받은 후 승인을 다시 보내기 전에 대기하는 시간을 결정합니다. 이 기간 동안 받은 다른 작업은 승인을 단일 메시지로 다시 보내어 네트워크 트래픽을 줄이지만 잠재적으로 복제자의 처리량을 줄입니다.|
|MaxCopyQueueSize|uint, 기본값: 1024|정적|복제 작업을 유지하는 큐의 초기 크기를 정의하는 최대 값입니다. 2의 거듭제곱이어야 합니다. 런타임 동안 큐가 이 크기로 증가하면 주 복제자와 보조 복제자 간에 작업이 제한됩니다.|
|MaxPrimaryReplicationQueueMemorySize|uint, 기본값: 0|정적|주 복제 큐의 최대 값(바이트)|
|MaxPrimaryReplicationQueueSize|uint, 기본값: 1024|정적|주 복제 큐에 존재할 수 있는 작업의 최대 수. 2의 거듭제곱이어야 합니다.|
|MaxReplicationMessageSize|uint, 기본값: 52428800|정적|복제 작업의 최대 메시지 크기. 기본값은 50MB입니다.|
|MaxSecondaryReplicationQueueMemorySize|uint, 기본값: 0|정적|보조 복제 큐의 최대 값(바이트)|
|MaxSecondaryReplicationQueueSize|uint, 기본값: 2048|정적|보조 복제 큐에 존재할 수 있는 작업의 최대 수. 2의 거듭제곱이어야 합니다.|
|QueueHealthMonitoringInterval|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(30)|정적|시간 간격은 초 단위로 지정합니다. 이 값은 복제자에서 복제 작업 큐의 경고/오류 상태 이벤트를 모니터링하는 데 사용하는 기간을 결정합니다. '0' 값은 상태 모니터링을 사용하지 않도록 설정합니다. |
|QueueHealthWarningAtUsagePercent|uint, 기본값: 80|정적|이 값은 높은 큐 사용량에 대한 경고를 보고하는 복제 큐 사용량(백분율)을 결정합니다. 이는 QueueHealthMonitoringInterval의 유예 간격 이후에 수행됩니다. 큐 사용량이 유예 간격에서 이 백분율 미만일 경우|
|ReplicatorAddress|string, 기본값: "localhost:0"|정적|Windows Fabric 복제자에서 작업을 보내거나 받기 위해 다른 복제본과의 연결을 설정하는 데 사용되는 문자열 형식의 엔드포인트입니다(예: 'IP:Port').|
|ReplicatorListenAddress|string, 기본값: "localhost:0"|정적|Windows Fabric 복제자에서 다른 복제본으로부터 작업을 받는 데 사용하는 문자열 형식의 엔드포인트입니다(예: 'IP:Port').|
|ReplicatorPublishAddress|string, 기본값: "localhost:0"|정적|Windows Fabric 복제자에서 다른 복제본으로 작업을 보내는 데 사용하는 문자열 형식의 엔드포인트입니다(예: 'IP:Port').|
|RetryInterval|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(5)|정적|시간 간격은 초 단위로 지정합니다. 작업이 손실되거나 거부되면 이 타이머에서 복제자가 작업 전송을 다시 시도하는 빈도를 결정합니다.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **매개 변수** | **허용되는 값** | **업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|IsEnabled|bool, 기본값: FALSE |정적|클러스터에서 서비스가 사용되는지 여부를 제어합니다. |

## <a name="runas"></a>RunAs

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|RunAsAccountName |string, 기본값: "" |동적|RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |동적|RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem"|
|RunAsPassword|string, 기본값: "" |동적|RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

## <a name="runas_dca"></a>RunAs_DCA

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|RunAsAccountName |string, 기본값: "" |동적|RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |동적|RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" |
|RunAsPassword|string, 기본값: "" |동적|RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

## <a name="runas_fabric"></a>RunAs_Fabric

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|RunAsAccountName |string, 기본값: "" |동적|RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |동적|RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" |
|RunAsPassword|string, 기본값: "" |동적|RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|RunAsAccountName |string, 기본값: "" |동적|RunAs 계정 이름을 나타냅니다. "DomainUser" 또는 "ManagedServiceAccount" 계정 유형에만 필요합니다. 유효한 값: "domain\user" 또는 "user@domain" |
|RunAsAccountType|string, 기본값: "" |동적|RunAs 계정 유형을 나타냅니다. RunAs 섹션에 필요합니다. 유효한 값: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" |
|RunAsPassword|string, 기본값: "" |동적|RunAs 계정 암호를 나타냅니다. "DomainUser" 계정 유형에만 필요합니다. |

## <a name="security"></a>보안
| **매개 변수** | **허용되는 값** |**업그레이드 정책**| **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|string, 기본값: ""|정적|Azure Government "https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" 등 기본값이 아닌 환경용으로 지정되는 AAD 인증서 엔드포인트 형식입니다(기본값: Azure Commercial). |
|AADClientApplication|string, 기본값: ""|정적|Fabric 클라이언트를 나타내는 Native Client 애플리케이션 이름 또는 ID |
|AADClusterApplication|string, 기본값: ""|정적|클러스터를 나타내는 Web API 애플리케이션 이름 또는 ID |
|AADLoginEndpoint|string, 기본값: ""|정적|Azure Government "https:\//login.microsoftonline.us" 등 기본값이 아닌 환경용으로 지정되는 AAD 로그인 엔드포인트입니다(기본값: Azure Commercial). |
|AADTenantId|string, 기본값: ""|정적|테넌트 ID(GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, 기본값: FALSE|동적|지문을 통해 선언된 만료 클러스터 인증서를 수락할지 여부를 나타내는 플래그이며 클러스터 인증서에만 적용됩니다. 클러스터를 활성 상태로 유지하는 데 사용됩니다. |
|AdminClientCertThumbprints|string, 기본값: ""|동적|관리자 역할의 클라이언트에서 사용하는 인증서의 지문입니다. 쉼표로 구분된 이름 목록입니다. |
|AADTokenEndpointFormat|string, 기본값: ""|정적|Azure Government "https:\//login.microsoftonline.us/{0}" 등 기본값이 아닌 환경용으로 지정되는 AAD 토큰 엔드포인트입니다(기본값: Azure Commercial). |
|AdminClientClaims|string, 기본값: ""|동적|모든 가능한 클레임은 관리자 클라이언트에서 예상되는 모든 가능한 클레임이며, ClientClaims와 동일한 형식입니다. 이 목록은 ClientClaims에 내부적으로 추가되므로 ClientClaims에 동일한 항목을 추가할 필요가 없습니다. |
|AdminClientIdentities|string, 기본값: ""|동적|관리자 역할의 패브릭 클라이언트에 대한 Windows ID이며, 권한 있는 패브릭 작업에 대한 권한을 부여하는 데 사용됩니다. 쉼표로 구분된 목록이며, 각 항목은 도메인 계정 이름 또는 그룹 이름입니다. 편의상 fabric.exe를 실행하는 계정에 관리자 역할이 자동으로 할당됩니다. ServiceFabricAdministrators 그룹도 마찬가지입니다. |
|AppRunAsAccountGroupX509Folder|string, 기본값: /home/sfuser/sfusercerts |정적|AppRunAsAccountGroup X509 인증서와 프라이빗 키가 있는 폴더 |
|CertificateExpirySafetyMargin|TimeSpan, 기본값: Common::TimeSpan::FromMinutes(43200)|정적|시간 간격은 초 단위로 지정합니다. 인증서 만료에 대한 여유 제한입니다. 만료가 이 값보다 더 가까울 경우 인증서 상태 보고서 상태가 확인에서 경고로 변경됩니다. 기본값: 30일 |
|CertificateHealthReportingInterval|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(3600 * 8)|정적|시간 간격은 초 단위로 지정합니다. 인증서 상태 보고 간격을 지정하며, 기본값은 8시간입니다. 0으로 설정하면 인증서 상태 보고가 사용되지 않습니다. |
|ClientCertThumbprints|string, 기본값: ""|동적|클러스터에서 클라이언트와 통신하는 데 사용하는 서버 인증서의 지문이며, 클러스터에서 이 지문을 사용하여 들어오는 연결에 대한 권한을 부여합니다. 쉼표로 구분된 이름 목록입니다. |
|ClientClaimAuthEnabled|bool, 기본값: FALSE|정적|클라이언트에서 클레임 기반 인증이 사용되는지 여부를 나타냅니다. true로 설정하면 ClientRoleEnabled가 암시 적으로 설정됩니다. |
|ClientClaims|string, 기본값: ""|동적|게이트웨이에 연결하기 위해 클라이언트에서 예상되는 모든 가능한 클레임입니다. 'OR' 목록입니다. ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ... 각 ClaimsEntry는 "AND" 목록입니다. ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|ClientIdentities|string, 기본값: ""|동적|FabricClient의 Windows ID이며, 이름 지정 게이트웨이에서 이를 사용하여 들어오는 연결에 대한 권한을 부여합니다. 쉼표로 구분된 목록이며, 각 항목은 도메인 계정 이름 또는 그룹 이름입니다. 편의상 fabric.exe를 실행하는 계정이 자동으로 허용됩니다. ServiceFabricAllowedUsers 및 ServiceFabricAdministrators 그룹도 마찬가지입니다. |
|ClientRoleEnabled|bool, 기본값: FALSE|정적|클라이언트 역할이 사용되는지 여부를 나타냅니다. true로 설정하면 클라이언트에 해당 클라이언트 ID 기반의 역할이 할당됩니다. V2에 대해 이 매개 변수를 사용하면 AdminClientCommonNames/AdminClientIdentities에 없는 클라이언트에서 읽기 전용 작업만 실행할 수 있습니다. |
|ClusterCertThumbprints|string, 기본값: ""|동적|클러스터에 조인하도록 허용되는 인증서의 지문이며, 쉼표로 구분된 이름 목록입니다. |
|ClusterCredentialType|string, 기본값: "None"|허용되지 않음|클러스터를 보호하는 데 사용할 보안 자격 증명 유형을 나타냅니다. 유효한 값: "None / X509 / Windows" |
|ClusterIdentities|string, 기본값: ""|동적|클러스터 노드의 Windows ID이며, 클러스터 멤버 자격 권한 부여에 사용됩니다. 쉼표로 구분된 목록이며, 각 항목은 도메인 계정 이름 또는 그룹 이름입니다. |
|ClusterSpn|string, 기본값: ""|허용되지 않음|패브릭이 단일 도메인 사용자(gMSA/도메인 사용자 계정)로 실행되는 경우 클러스터의 서비스 사용자 이름입니다. fabric.exe의 임대 수신기 및 수신기, 즉 페더레이션 수신기, 내부 복제 수신기, 런타임 서비스 수신기 및 이름 지정 게이트웨이 수신기의 SPN입니다. . 패브릭이 머신 계정으로 실행될 때 이 값은 비워 두어야 합니다. 이 경우 수신기 전송 주소에서 측면 컴퓨팅 SPN을 연결합니다. |
|CrlCheckingFlag|uint, 기본값: 0x40000000|동적|기본 인증서 체인 유효성 검사에 대한 플래그이며, 구성 요소 특정 플래그로 재정의될 수 있습니다. 예: Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY 0으로 설정하면 CRL 확인이 해제됩니다. 지원되는 전체 값 목록은 CertGetCertificateChain의 dwFlags에서 문서화되어 있습니다(https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx ). |
|CrlDisablePeriod|TimeSpan, 기본값: Common::TimeSpan::FromMinutes(15)|동적|시간 간격은 초 단위로 지정합니다. CRL 오프라인 오류를 무시할 수 있는 경우 오프라인 오류가 발생한 후에 지정된 인증서에 대한 CRL 확인이 사용되지 않는 기간입니다. |
|CrlOfflineHealthReportTtl|TimeSpan, 기본값: Common::TimeSpan::FromMinutes(1440)|동적|시간 간격은 초 단위로 지정합니다. |
|DisableFirewallRuleForDomainProfile| bool, 기본값: TRUE |정적| 도메인 프로필에 대해 방화벽 규칙이 사용되지 않아야 하는지 여부를 나타냅니다. |
|DisableFirewallRuleForPrivateProfile| bool, 기본값: TRUE |정적| 프라이빗 프로필에 대해 방화벽 규칙이 사용되지 않아야 하는지 여부를 나타냅니다. | 
|DisableFirewallRuleForPublicProfile| bool, 기본값: TRUE | 정적|공용 프로필에 대해 방화벽 규칙이 사용되지 않아야 하는지 여부를 나타냅니다. |
| EnforceLinuxMinTlsVersion | bool, 기본값: FALSE | 정적 | true로 설정하면 TLS 버전 1.2 이상만 지원됩니다.  false이면 이전 TLS 버전을 지원합니다. Linux에만 적용됩니다. |
| EnforcePrevalidationOnSecurityChanges | bool, 기본값: FALSE| 동적 | 보안 설정 변경 감지 시 클러스터 업그레이드 동작을 제어하는 플래그입니다. 'true'로 설정하면 클러스터 업그레이드는 프레젠테이션 규칙과 일치하는 인증서 중 하나 이상이 해당 유효성 검사 규칙을 통과할 수 있도록 합니다. 새 설정이 임의의 노드에 적용되기 전에 사전 유효성 검사가 실행되지만 업그레이드를 시작할 때 Cluster Manager 서비스의 기본 복제본을 호스트하는 노드에서만 실행됩니다. 기본값은 현재 'false'로 설정되어 있습니다. 릴리스 7.1부터는 새 Azure Service Fabric 클러스터에 대해 설정이 'true'로 설정됩니다.|
|FabricHostSpn| string, 기본값: "" |정적| 패브릭이 단일 도메인 사용자(gMSA/도메인 사용자 계정)로 실행되고 FabricHost가 컴퓨터 계정에서 실행되는 경우 FabricHost의 서비스 사용자 이름입니다. FabricHost에 대한 IPC 수신기의 SPN입니다. FabricHost는 컴퓨터 계정에서 실행되므로 기본적으로 비워 두어야 합니다. |
|IgnoreCrlOfflineError|bool, 기본값: FALSE|동적|서버 쪽에서 들어오는 클라이언트 인증서를 확인할 때 CRL 오프라인 오류를 무시할지 여부 |
|IgnoreSvrCrlOfflineError|bool, 기본값: TRUE|동적|클라이언트 쪽에서 들어오는 서버 인증서를 확인할 때 CRL 오프라인 오류를 무시할지 여부이며, 기본값은 true입니다. 해지된 서버 인증서를 사용하여 공격하려면 DNS가 손상되어야 합니다. 이는 해지된 클라이언트 인증서를 사용하는 것보다 더 어렵습니다. |
|ServerAuthCredentialType|string, 기본값: "None"|정적|FabricClient와 클러스터 간의 통신을 보호하는 데 사용할 보안 자격 증명 유형을 나타냅니다. 유효한 값: "None / X509 / Windows" |
|ServerCertThumbprints|string, 기본값: ""|동적|클러스터에서 클라이언트와 통신하는 데 사용하는 서버 인증서의 지문이며, 클라이언트에서 이 지문을 사용하여 클러스터를 인증합니다. 쉼표로 구분된 이름 목록입니다. |
|SettingsX509StoreName| string, 기본값: "MY"| 동적|패브릭에서 구성을 보호하는 데 사용하는 X509 인증서 저장소 |
|UseClusterCertForIpcServerTlsSecurity|bool, 기본값: FALSE|정적|클러스터 인증서를 사용하여 IPC 서버 TLS 전송 단위의 보안을 유지하는지 여부 |
|X509Folder|string, 기본값: /var/lib/waagent|정적|X509 인증서와 프라이빗 키가 있는 폴더 |
|TLS1_2_CipherList| 문자열| 정적|비어 있지 않은 문자열로 설정하면 TLS1.2 이하에서 지원되는 암호 목록을 재정의합니다. 'openssl-ciphers' 설명서에서 지원되는 암호 목록 및 목록 형식을 검색하는 방법을 참조하세요. TLS1.2에 대한 강력한 암호 목록의 예: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Linux에만 적용됩니다. |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, 기본값: None|동적|"Name" 및 "Value" 쌍의 목록입니다. 각 "Name"은 주체 일반 이름 또는 관리자 클라이언트 작업에 대해 권한이 있는 X509 인증서의 DnsName입니다. 지정된 “Name”의 경우 “Value”는 발급자 고정을 위해 쉼표로 구분된 인증서 지문 목록입니다. 비어있지 않은 경우 관리자 클라이언트 인증서의 직접 발급자가 목록에 있어야 합니다. |

## <a name="securityclientaccess"></a>보안/ClientAccess

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ActivateNode |string, 기본값: "Admin" |동적| 노드 활성화에 대한 보안 구성 |
|CancelTestCommand |string, 기본값: "Admin" |동적| 실행 중인 특정 TestCommand를 취소합니다. |
|CodePackageControl |string, 기본값: "Admin" |동적| 코드 패키지 다시 시작에 대한 보안 구성 |
|CreateApplication |string, 기본값: "Admin" | 동적|애플리케이션 만들기에 대한 보안 구성 |
|CreateComposeDeployment|string, 기본값: "Admin"| 동적|작성 파일에 설명된 작성 배포를 만듭니다. |
|CreateGatewayResource|string, 기본값: "Admin"| 동적|게이트웨이 리소스 만들기 |
|CreateName |string, 기본값: "Admin" |동적|이름 지정 URI 만들기에 대한 보안 구성 |
|CreateNetwork|string, 기본값: "Admin" |동적|컨테이너 네트워크를 만듭니다. |
|CreateService |string, 기본값: "Admin" |동적| 서비스 만들기에 대한 보안 구성 |
|CreateServiceFromTemplate |string, 기본값: "Admin" |동적|템플릿에서 서비스 만들기에 대한 보안 구성 |
|CreateVolume|string, 기본값: "Admin"|동적|볼륨을 만듭니다. |
|DeactivateNode |string, 기본값: "Admin" |동적| 단일 노드 비활성화에 대한 보안 구성 |
|DeactivateNodesBatch |string, 기본값: "Admin" |동적| 여러 노드 비활성화에 대한 보안 구성 |
|DELETE |string, 기본값: "Admin" |동적| 이미지 저장소 클라이언트 삭제 작업에 대한 보안 구성 |
|DeleteApplication |string, 기본값: "Admin" |동적| 애플리케이션 삭제에 대한 보안 구성 |
|DeleteComposeDeployment|string, 기본값: "Admin"| 동적|작성 배포를 삭제합니다. |
|DeleteGatewayResource|string, 기본값: "Admin"| 동적|게이트웨이 리소스를 삭제합니다. |
|DeleteName |string, 기본값: "Admin" |동적|이름 지정 URI 삭제에 대한 보안 구성 |
|DeleteNetwork|string, 기본값: "Admin" |동적|컨테이너 네트워크를 삭제합니다. |
|DeleteService |string, 기본값: "Admin" |동적|서비스 삭제에 대한 보안 구성 |
|DeleteVolume|string, 기본값: "Admin"|동적|볼륨을 삭제합니다.| 
|EnumerateProperties |string, 기본값: "Admin\|\|User" | 동적|이름 지정 속성 열거형에 대한 보안 구성 |
|EnumerateSubnames |string, 기본값: "Admin\|\|User" |동적| 이름 지정 URI 열거형에 대한 보안 구성 |
|FileContent |string, 기본값: "Admin" |동적| 이미지 저장소 클라이언트 파일 전송(클러스터 외부)에 대한 보안 구성 |
|FileDownload |string, 기본값: "Admin" |동적| 이미지 저장소 클라이언트 파일 다운로드 시작(클러스터 외부)에 대한 보안 구성 |
|FinishInfrastructureTask |string, 기본값: "Admin" |동적| 인프라 작업 완료에 대한 보안 구성 |
|GetChaosReport | string, 기본값: "Admin\|\|User" |동적| 지정한 시간 범위 내에서 Chaos(격리 안 함) 상태를 가져옵니다. |
|GetClusterConfiguration | string, 기본값: "Admin\|\|User" | 동적|파티션에 GetClusterConfiguration을 유도합니다. |
|GetClusterConfigurationUpgradeStatus | string, 기본값: "Admin\|\|User" |동적| 파티션에 GetClusterConfigurationUpgradeStatus를 유도합니다. |
|GetFabricUpgradeStatus |string, 기본값: "Admin\|\|User" |동적| 클러스터 업그레이드 상태 폴링에 대한 보안 구성 |
|GetFolderSize |string, 기본값: "Admin" |동적|FileStoreService의 폴더 크기 가져오기에 대한 보안 구성 |
|GetNodeDeactivationStatus |string, 기본값: "Admin" |동적| 비활성화 상태 검사에 대한 보안 구성 |
|GetNodeTransitionProgress | string, 기본값: "Admin\|\|User" |동적| 노드 전환 명령의 진행률 가져오기에 대한 보안 구성 |
|GetPartitionDataLossProgress | string, 기본값: "Admin\|\|User" | 동적|데이터 손실 API 호출에 대한 진행률을 가져옵니다. |
|GetPartitionQuorumLossProgress | string, 기본값: "Admin\|\|User" |동적| 쿼럼 손실 API 호출에 대한 진행률을 가져옵니다. |
|GetPartitionRestartProgress | string, 기본값: "Admin\|\|User" |동적| 파티션 다시 시작 API 호출에 대한 진행률을 가져옵니다. |
|GetSecrets|string, 기본값: "Admin"|동적|비밀 값을 가져옵니다. |
|GetServiceDescription |string, 기본값: "Admin\|\|User" |동적| 장기 폴링 서비스 알림 및 읽기 서비스 설명에 대한 보안 구성 |
|GetStagingLocation |string, 기본값: "Admin" |동적| 이미지 저장소 클라이언트 스테이징 위치 검색에 대한 보안 구성 |
|GetStoreLocation |string, 기본값: "Admin" |동적| 이미지 저장소 클라이언트 저장소 위치 검색에 대한 보안 구성 |
|GetUpgradeOrchestrationServiceState|string, 기본값: "Admin"| 동적|파티션에서 GetUpgradeOrchestrationServiceState를 유도합니다. |
|GetUpgradesPendingApproval |string, 기본값: "Admin" |동적| 파티션에 GetUpgradesPendingApproval을 유도합니다. |
|GetUpgradeStatus |string, 기본값: "Admin\|\|User" |동적| 애플리케이션 업그레이드 상태 폴링에 대한 보안 구성 |
|InternalList |string, 기본값: "Admin" | 동적|이미지 저장소 클라이언트 파일 목록 작업(내부)에 대한 보안 구성 |
|InvokeContainerApi|string, 기본값: "Admin"|동적|컨테이너 API 호출 |
|InvokeInfrastructureCommand |string, 기본값: "Admin" |동적| 인프라 작업 관리 명령에 대한 보안 구성 |
|InvokeInfrastructureQuery |string, 기본값: "Admin\|\|User" | 동적|인프라 작업 쿼리에 대한 보안 구성 |
|목록 |string, 기본값: "Admin\|\|User" | 동적|이미지 저장소 클라이언트 파일 목록 작업에 대한 보안 구성 |
|MoveNextFabricUpgradeDomain |string, 기본값: "Admin" |동적| 명시적 업그레이드 도메인으로 클러스터 업그레이드 다시 시작에 대한 보안 구성 |
|MoveNextUpgradeDomain |string, 기본값: "Admin" |동적| 명시적 업그레이드 도메인으로 애플리케이션 업그레이드 다시 시작에 대한 보안 구성 |
|MoveReplicaControl |string, 기본값: "Admin" | 동적|복제본 이동 |
|NameExists |string, 기본값: "Admin\|\|User" | 동적|이름 지정 URI 존재 확인에 대한 보안 구성 |
|NodeControl |string, 기본값: "Admin" |동적| 노드 시작, 중지 및 다시 시작에 대한 보안 구성 |
|NodeStateRemoved |string, 기본값: "Admin" |동적| 제거된 노드 상태 보고에 대한 보안 구성 |
|Ping |string, 기본값: "Admin\|\|User" |동적| 클라이언트 ping에 대한 보안 구성 |
|PredeployPackageToNode |string, 기본값: "Admin" |동적| 배포 전 API |
|PrefixResolveService |string, 기본값: "Admin\|\|User" |동적| 불만 기반 서비스 접두사 확인에 대한 보안 구성 |
|PropertyReadBatch |string, 기본값: "Admin\|\|User" |동적| 이름 지정 속성 읽기 작업에 대한 보안 구성 |
|PropertyWriteBatch |string, 기본값: "Admin" |동적|이름 지정 속성 쓰기 작업에 대한 보안 구성 |
|ProvisionApplicationType |string, 기본값: "Admin" |동적| 애플리케이션 유형 프로비전에 대한 보안 구성 |
|ProvisionFabric |string, 기본값: "Admin" |동적| MSI 및/또는 클러스터 매니페스트 프로비전에 대한 보안 구성 |
|쿼리 |string, 기본값: "Admin\|\|User" |동적| 쿼리에 대한 보안 구성 |
|RecoverPartition |string, 기본값: "Admin" | 동적|단일 파티션 복구에 대한 보안 구성 |
|RecoverPartitions |string, 기본값: "Admin" | 동적|여러 파티션 복구에 대한 보안 구성 |
|RecoverServicePartitions |string, 기본값: "Admin" |동적| 서비스 파티션 복구에 대한 보안 구성 |
|RecoverSystemPartitions |string, 기본값: "Admin" |동적| 시스템 서비스 파티션 복구에 대한 보안 구성 |
|RemoveNodeDeactivations |string, 기본값: "Admin" |동적| 여러 노드에서 비활성화 되돌리기에 대한 보안 구성 |
|ReportFabricUpgradeHealth |string, 기본값: "Admin" |동적| 현재 업그레이드 진행으로 클러스터 업그레이드 다시 시작에 대한 보안 구성 |
|ReportFault |string, 기본값: "Admin" |동적| 오류 보고에 대한 보안 구성 |
|ReportHealth |string, 기본값: "Admin" |동적| 상태 보고에 대한 보안 구성 |
|ReportUpgradeHealth |string, 기본값: "Admin" |동적| 현재 업그레이드 진행으로 애플리케이션 업그레이드 다시 시작에 대한 보안 구성 |
|ResetPartitionLoad |string, 기본값: "Admin\|\|User" |동적| failoverUnit의 로드 다시 설정에 대한 보안 구성 |
|ResolveNameOwner |string, 기본값: "Admin\|\|User" | 동적|이름 지정 URI 소유자 확인에 대한 보안 구성 |
|ResolvePartition |string, 기본값: "Admin\|\|User" | 동적|시스템 서비스 확인에 대한 보안 구성 |
|ResolveService |string, 기본값: "Admin\|\|User" |동적| 불만 기반 서비스 확인에 대한 보안 구성 |
|ResolveSystemService|string, 기본값: "Admin\|\|User"|동적| 시스템 서비스 확인에 대한 보안 구성 |
|RollbackApplicationUpgrade |string, 기본값: "Admin" |동적| 애플리케이션 업그레이드 롤백에 대한 보안 구성 |
|RollbackFabricUpgrade |string, 기본값: "Admin" |동적| 클러스터 업그레이드 롤백에 대한 보안 구성 |
|ServiceNotifications |string, 기본값: "Admin\|\|User" |동적| 이벤트 기반 서비스 알림에 대한 보안 구성 |
|SetUpgradeOrchestrationServiceState|string, 기본값: "Admin"| 동적|파티션에서 SetUpgradeOrchestrationServiceState를 유도합니다. |
|StartApprovedUpgrades |string, 기본값: "Admin" |동적| 파티션에 StartApprovedUpgrades를 유도합니다. |
|StartChaos |string, 기본값: "Admin" |동적| 아직 시작되지 않은 Chaos(격리 안 함)를 시작합니다. |
|StartClusterConfigurationUpgrade |string, 기본값: "Admin" |동적| 파티션에 StartClusterConfigurationUpgrade를 유도합니다. |
|StartInfrastructureTask |string, 기본값: "Admin" | 동적|인프라 작업 시작에 대한 보안 구성 |
|StartNodeTransition |string, 기본값: "Admin" |동적| 노드 전환 시작에 대한 보안 구성 |
|StartPartitionDataLoss |string, 기본값: "Admin" |동적| 파티션에 데이터 손실을 유도합니다. |
|StartPartitionQuorumLoss |string, 기본값: "Admin" |동적| 파티션에 쿼럼 손실을 유도합니다. |
|StartPartitionRestart |string, 기본값: "Admin" |동적| 파티션의 일부 또는 모든 복제본을 동시에 다시 시작합니다. |
|StopChaos |string, 기본값: "Admin" |동적| 시작된 Chaos를 중지합니다. |
|ToggleVerboseServicePlacementHealthReporting | string, 기본값: "Admin\|\|User" |동적| Verbose ServicePlacement HealthReporting 전환에 대한 보안 구성 |
|UnprovisionApplicationType |string, 기본값: "Admin" |동적| 애플리케이션 유형 프로비전 해제에 대한 보안 구성 |
|UnprovisionFabric |string, 기본값: "Admin" |동적| MSI 및/또는 클러스터 매니페스트 프로비전 해제에 대한 보안 구성 |
|UnreliableTransportControl |string, 기본값: "Admin" |동적| 동작 추가 및 제거에 대한 신뢰할 수 없는 전송 |
|UpdateService |string, 기본값: "Admin" |동적|서비스 업데이트에 대한 보안 구성 |
|UpgradeApplication |string, 기본값: "Admin" |동적| 애플리케이션 업그레이드 시작 또는 중단에 대한 보안 구성 |
|UpgradeComposeDeployment|string, 기본값: "Admin"| 동적|작성 배포를 업그레이드합니다. |
|UpgradeFabric |string, 기본값: "Admin" |동적| 클러스터 업그레이드 시작에 대한 보안 구성 |
|업로드 |string, 기본값: "Admin" | 동적|이미지 저장소 클라이언트 업로드 작업에 대한 보안 구성 |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, 기본값: None |동적|클라이언트 인증서에 대한 X509 발급자 인증서 저장소; 이름 = clientIssuerCN; 값 =저장소의 쉼표로 구분 된 목록 |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, 기본값: None|동적|"Name" 및 "Value" 쌍의 목록입니다. 각 "Name"은 주체 일반 이름 또는 클라이언트 작업에 대해 권한이 있는 X509 인증서의 DnsName입니다. 지정된 “Name”의 경우 “Value”는 발급자 고정을 위해 쉼표로 구분된 인증서 지문 목록입니다. 비어있지 않은 경우 클라이언트 인증서의 직접 발급자가 목록에 있어야 합니다.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, 기본값: None |동적|클러스터 인증서에 대한 X509 발급자 인증서 저장소; 이름 = clusterIssuerCN; 값 =저장소의 쉼표로 구분 된 목록 |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, 기본값: None|동적|"Name" 및 "Value" 쌍의 목록입니다. 각 "Name"은 주체 일반 이름 또는 클러스터 작업에 대해 권한이 있는 X509 인증서의 DnsName입니다. 지정된 "Name"의 경우 "Value"는 발급자 고정을 위해 쉼표로 구분된 인증서 지문 목록입니다. 비어있지 않은 경우 클러스터 인증서의 직접 발급자가 목록에 있어야 합니다.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, 기본값: None |동적|서버 인증서에 대한 X509 발급자 인증서 저장소; 이름 = serverIssuerCN; 값 =저장소의 쉼표로 구분 된 목록 |

## <a name="securityserverx509names"></a>Security/ServerX509Names

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, 기본값: None|동적|"Name" 및 "Value" 쌍의 목록입니다. 각 "Name"은 주체 일반 이름 또는 서버 작업에 대해 권한이 있는 X509 인증서의 DnsName입니다. 지정된 "Name"의 경우 "Value"는 발급자 고정을 위해 쉼표로 구분된 인증서 지문 목록입니다. 비어있지 않은 경우 서버 인증서의 직접 발급자가 목록에 있어야 합니다.|

## <a name="setup"></a>설치 프로그램

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ContainerNetworkName|string, 기본값: ""| 정적 |컨테이너 네트워크를 설정할 때 사용할 네트워크 이름입니다.|
|ContainerNetworkSetup|Bool, 기본값: FALSE(Linux) 및 기본값 TRUE(Windows)| 정적 |컨테이너 네트워크를 설정할지 여부입니다.|
|FabricDataRoot |String | 허용되지 않음 |Service Fabric 데이터 루트 디렉터리. Azure에 대한 기본값: d:\svcfab |
|FabricLogRoot |String | 허용되지 않음 |Service Fabric 로그 루트 디렉터리. SF 로그 및 추적이 배치되는 위치입니다. |
|NodesToBeRemoved|string, 기본값: ""| 동적 |구성 업그레이드의 일부로 제거해야 하는 노드입니다. (독립 실행형 배포에만 해당)|
|ServiceRunAsAccountName |String | 허용되지 않음 |패브릭 호스트 서비스를 실행할 계정 이름 |
|SkipContainerNetworkResetOnReboot|bool, 기본값: FALSE|NotAllowed|다시 부팅 시 컨테이너 네트워크 재설정을 건너뛸지 여부입니다.|
|SkipFirewallConfiguration |bool, 기본값: false | 허용되지 않음 |시스템에서 방화벽 설정을 설정해야 하는지 여부를 지정합니다. Windows 방화벽을 사용하는 경우에만 적용됩니다. 타사 방화벽을 사용하는 경우 사용할 시스템 및 애플리케이션에 대한 포트를 열어야 합니다. |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|공급자 |string, 기본값: "DSTS" |정적|사용하도록 설정할 쉼표로 구분된 토큰 유효성 검사 공급자 목록(유효한 공급자: DSTS; AAD). 현재 단일 공급자만 언제든지 사용할 수 있습니다. |

## <a name="traceetw"></a>추적/ETW

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|Level |int, 기본값: 4 | 동적 |ETW 추적 수준은 1, 2, 3, 4 값을 가질 수 있습니다. 지원 받으려면 추적 수준을 4에서 유지해야 합니다. |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | time(초), 기본값: 0.015 | 정적 | 시간 간격은 초 단위로 지정합니다. 복제자에서 작업을 받은 후 승인을 다시 보내기 전에 대기하는 시간을 결정합니다. 이 기간 동안 받은 다른 작업은 승인을 단일 메시지로 다시 보내어 네트워크 트래픽을 줄이지만 잠재적으로 복제자의 처리량을 줄입니다. |
|MaxCopyQueueSize |uint, 기본값: 16384 | 정적 |복제 작업을 유지하는 큐의 초기 크기를 정의하는 최대 값입니다. 2의 거듭제곱이어야 합니다. 런타임 동안 큐가 이 크기로 증가하면 주 복제자와 보조 복제자 간에 작업이 제한됩니다. |
|MaxPrimaryReplicationQueueMemorySize |uint, 기본값: 0 | 정적 |주 복제 큐의 최대 값(바이트) |
|MaxPrimaryReplicationQueueSize |uint, 기본값: 8192 | 정적 |주 복제 큐에 존재할 수 있는 작업의 최대 수. 2의 거듭제곱이어야 합니다. |
|MaxReplicationMessageSize |uint, 기본값: 52428800 | 정적 | 복제 작업의 최대 메시지 크기. 기본값은 50MB입니다. |
|MaxSecondaryReplicationQueueMemorySize |uint, 기본값: 0 | 정적 |보조 복제 큐의 최대 값(바이트) |
|MaxSecondaryReplicationQueueSize |uint, 기본값: 16384 | 정적 |보조 복제 큐에 존재할 수 있는 작업의 최대 수. 2의 거듭제곱이어야 합니다. |
|ReplicatorAddress |string, 기본값: "localhost:0" | 정적 | Windows Fabric 복제자에서 작업을 보내거나 받기 위해 다른 복제본과의 연결을 설정하는 데 사용되는 문자열 형식의 엔드포인트입니다(예: 'IP:Port'). |

## <a name="transport"></a>전송
| **매개 변수** | **허용되는 값** |**업그레이드 정책** |**지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(60)|정적|시간 간격은 초 단위로 지정합니다. 들어오는 쪽과 수락하는 쪽 모두에 대한 연결 설정 시간 제한(보안 모드의 보안 협상 포함)입니다. |
|FrameHeaderErrorCheckingEnabled|bool, 기본값: TRUE|정적|안전하지 않은 모드에서 프레임 헤더에 대한 오류 검사를 위한 기본 설정입니다. 구성 요소 설정은 이를 재정의합니다. |
|MessageErrorCheckingEnabled|bool, 기본값: FALSE|정적|안전하지 않은 모드에서 메시지 헤더 및 본문에 대한 오류 검사를 위한 기본 설정입니다. 구성 요소 설정은 이를 재정의합니다. |
|ResolveOption|string, 기본값: "unspecified"|정적|FQDN을 확인하는 방법을 결정합니다.  유효한 값: "unspecified/ipv4/ipv6" |
|SendTimeout|TimeSpan, 기본값: Common::TimeSpan::FromSeconds(300)|동적|시간 간격은 초 단위로 지정합니다. 중단된 연결 검색에 대한 제한 시간을 보냅니다. TCP 오류 보고서는 일부 환경에는 안정적이지 않습니다. 사용 가능한 네트워크 대역폭 및 아웃바운드 데이터의 크기에 따라 조정해야 할 수 있습니다(\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | bool, 기본값: true |정적| 목표 상태(goal-state) 파일에 기반한 자동 폴링 및 업그레이드 작업입니다. |
|AutoupgradeInstallEnabled|Bool, 기본값: FALSE|정적|목표 상태(goal-state) 파일을 기준으로 하는 자동 폴링, 프로비저닝 및 코드 설치 업그레이드 작업입니다.|
|GoalStateExpirationReminderInDays|int, 기본값: 30|정적|목표 상태 미리 알림이 표시될 때까지 남은 일 수를 설정합니다.|
|MinReplicaSetSize |int, 기본값: 0 |정적 |UpgradeOrchestrationService 용 MinReplicaSetSize입니다.|
|PlacementConstraints | string, 기본값: "" |정적| UpgradeOrchestrationService의 PlacementConstraints입니다. |
|QuorumLossWaitDuration | time(초), 기본값: MaxValue |정적| 시간 간격은 초 단위로 지정합니다. UpgradeOrchestrationService의 QuorumLossWaitDuration입니다. |
|ReplicaRestartWaitDuration | time(초), 기본값: 60분|정적| 시간 간격은 초 단위로 지정합니다. UpgradeOrchestrationService의 ReplicaRestartWaitDuration입니다. |
|StandByReplicaKeepDuration | time(초), 기본값: 60*24*7분 |정적| 시간 간격은 초 단위로 지정합니다. UpgradeOrchestrationService의 StandByReplicaKeepDuration입니다. |
|TargetReplicaSetSize |int, 기본값: 0 |정적 |UpgradeOrchestrationService의 TargetReplicaSetSize입니다. |
|UpgradeApprovalRequired | bool, 기본값: false | 정적|코드를 업그레이드하도록 설정하려면 계속하기 전에 먼저 관리자의 승인이 필요합니다. |

## <a name="upgradeservice"></a>UpgradeService

| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|BaseUrl | string, 기본값: "" |정적|UpgradeService의 BaseUrl입니다. |
|ClusterId | string, 기본값: "" |정적|UpgradeService의 ClusterId입니다. |
|CoordinatorType | string, 기본값: "WUTest"|허용되지 않음|UpgradeService의 CoordinatorType입니다. |
|MinReplicaSetSize | int, 기본값: 2 |허용되지 않음| UpgradeService의 MinReplicaSetSize입니다. |
|OnlyBaseUpgrade | bool, 기본값: false |동적|UpgradeService의 OnlyBaseUpgrade입니다. |
|PlacementConstraints |string, 기본값: "" |허용되지 않음|UpgradeService의 PlacementConstraints입니다. |
|PollIntervalInSeconds|Timespan, 기본값: Common::TimeSpan::FromSeconds(60) |동적|시간 간격은 초 단위로 지정합니다. ARM 관리 작업의 UpgradeService 폴링 간격입니다. |
|TargetReplicaSetSize | int, 기본값: 3 |허용되지 않음| UpgradeService의 TargetReplicaSetSize입니다. |
|TestCabFolder | string, 기본값: "" |정적| UpgradeService의 TestCabFolder입니다. |
|X509FindType | string, 기본값: ""|동적| UpgradeService의 X509FindType입니다. |
|X509FindValue | string, 기본값: "" |동적| UpgradeService의 X509FindValue입니다. |
|X509SecondaryFindValue | string, 기본값: "" |동적| UpgradeService의 X509SecondaryFindValue입니다. |
|X509StoreLocation | string, 기본값: "" |동적| UpgradeService의 X509StoreLocation입니다. |
|X509StoreName | string, 기본값: "My"|동적|UpgradeService의 X509StoreName입니다. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **매개 변수** | **허용되는 값** | **업그레이드 정책** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, 기본값: None | 정적 | 사용자 서비스 리소스 거버넌스 제한의 컬렉션, AutoDetection 논리에 영향을 미치므로 정적이어야 함 |

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Azure 클러스터의 구성 업그레이드](service-fabric-cluster-config-upgrade-azure.md) 및 [독립 실행형 클러스터의 구성 업그레이드](service-fabric-cluster-config-upgrade-windows-server.md)를 참조하세요.