---
title: 리소스 명명 제한
description: Azure 리소스의 이름을 지정 하는 규칙 및 제한 사항을 보여 줍니다.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 7b6a9e16ba28a2a0e5e4e181dc5650d2110eab88
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153165"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 리소스에 대한 명명 규칙 및 제한 사항

이 문서에서는 Azure 리소스에 대 한 명명 규칙 및 제한 사항을 요약 합니다. 리소스의 이름을 지정 하는 방법에 대 한 권장 사항은 [준비: 권장 되는 명명 및 태그 지정 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)을 참조 하세요.

리소스 이름은 유효한 문자 열에 명시 된 경우를 제외 하 고는 대/소문자를 구분 하지 않습니다.

다음 표에서는 영숫자 라는 용어를 참조 합니다.

* **a-z** (소문자)
* **A-z** (대문자)
* **0-9** ( **숫자** )

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서버 | 리소스 그룹 | 3-63 | 소문자 및 숫자<br><br>소문자로 시작 합니다. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서비스 | 전역 | 1-50 | 영숫자와.<br><br>문자로 시작 합니다. |
> | 서비스/a p i | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/문제 | api | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/문제/첨부 파일 | 문제 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/문제/설명 | 문제 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/작업 | api | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/작업/태그 | operation(작업) | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/릴리스 | api | 1-80 | 영숫자, 밑줄 및 하이픈입니다.<br><br>영숫자 또는 밑줄로 시작 하 고 종료 합니다. |
> | 서비스/a p i/스키마 | api | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/tagDescriptions | api | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i/태그 | api | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/a p i-버전 집합 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | service/authorizationServers | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/백 엔드 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/인증서 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/진단 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/그룹 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/그룹/사용자 | 그룹 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/identityProviders | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/로 거 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/알림 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/알림/recipientEmails | 알림 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | service/openidConnectProviders | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/정책 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/제품 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/제품/a p i | 제품 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/제품/그룹 | 제품 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/제품/태그 | 제품 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/속성 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/구독 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/태그 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/템플릿 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |
> | 서비스/사용자 | 서비스 | 1-256 | 사용할 수 없음:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | configurationStores | 리소스 그룹 | 5-50 | 영숫자, 밑줄 및 하이픈입니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 잠금 | 할당 범위 | 1-90 | 영숫자, 마침표, 밑줄, 하이픈, 괄호 등이 있습니다.<br><br>기간을 종료할 수 없습니다. |
> | policyassignments | 할당 범위 | 1-128 표시 이름<br><br>1-260 리소스 이름 | 표시 이름은 모든 문자를 포함할 수 있습니다.<br><br>리소스 이름은 `%` 포함할 수 없으며 마침표 또는 공백으로 끝날 수 없습니다. |
> | policydefinitions | 정의의 범위 | 1-128 표시 이름<br><br>1-260 리소스 이름 | 표시 이름은 모든 문자를 포함할 수 있습니다.<br><br>리소스 이름은 `%` 포함할 수 없으며 마침표 또는 공백으로 끝날 수 없습니다. |
> | policySetDefinitions | 정의의 범위 | 1-128 표시 이름<br><br>1-260 리소스 이름 | 표시 이름은 모든 문자를 포함할 수 있습니다.<br><br>리소스 이름은 `%` 포함할 수 없으며 마침표 또는 공백으로 끝날 수 없습니다.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | automationAccounts | 리소스 그룹 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작 하 고 영숫자로 끝납니다. |
> | automationAccounts/certificate | automation 계정 | 1-128 | 사용할 수 없음:<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다.  |
> | automationAccounts/connections | automation 계정 | 1-128 | 사용할 수 없음:<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts/credentials | automation 계정 | 1-128 | 사용할 수 없음:<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts/runbook | automation 계정 | 1-63 | 영숫자, 밑줄 및 하이픈입니다.<br><br>문자로 시작 합니다.  |
> | automationAccounts/일정 | automation 계정 | 1-128 | 사용할 수 없음:<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts/variables | automation 계정 | 1-128 | 사용할 수 없음:<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts/감시자 | automation 계정 | 1-63 |  영숫자, 밑줄 및 하이픈입니다.<br><br>문자로 시작 합니다. |
> | automationAccounts/웹 후크 | automation 계정 | 1-128 | 사용할 수 없음:<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | batchAccounts | 지역 | 3-24 | 소문자 및 숫자 |
> | batchAccounts/응용 프로그램 | batch 계정 | 1-64 | 영숫자, 밑줄 및 하이픈입니다. |
> | batchAccounts/certificate | batch 계정 | 5-45 | 영숫자, 밑줄 및 하이픈입니다. |
> | batchAccounts/풀 | batch 계정 | 1-64 | 영숫자, 밑줄 및 하이픈입니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | blockchainMembers | 전역 | 2-20 | 소문자 및 숫자<br><br>소문자로 시작 합니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | botServices | 전역 | 2-64 |  영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. |
> | botServices/채널 | bot 서비스 | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. |
> | botServices/연결 | bot 서비스 | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. |
> | enterpriseChannels | 리소스 그룹 | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | Redis | 전역 | 1-63 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. 연속 하이픈이 허용 되지 않습니다. |
> | Redis/firewallRules | Redis | 1-256 | 영숫자와 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 프로필 | 리소스 그룹 | 1-260 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 프로필/끝점 | 전역 | 1-50 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | certificateOrders | 리소스 그룹 | 3-30 | 영숫자와. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 리소스 그룹 | 2-64 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | availabilitySets | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 영숫자 또는 밑줄로 끝납니다. |
> | Disk; 집합 | 리소스 그룹 | 1-80 | 영숫자 및 밑줄 |
> | 디스크 | 리소스 그룹 | 1-80 | 영숫자 및 밑줄 |
> | galleries | 리소스 그룹 | 1-80 | 영숫자 및 마침표.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 갤러리/응용 프로그램 | 갤러리 | 1-80 | 영숫자, 하이픈 및 마침표입니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 갤러리/응용 프로그램/버전 | 응용 프로그램 | 32비트 정수 | 숫자 및 마침표. |
> | 갤러리/이미지 | 갤러리 | 1-80 | 영숫자, 하이픈 및 마침표입니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 갤러리/이미지/버전 | image | 32비트 정수 | 숫자 및 마침표. |
> | 이미지 | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 영숫자 또는 밑줄로 끝납니다. |
> | 스냅샷 | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 영숫자 또는 밑줄로 끝납니다. |
> | virtualMachines | 리소스 그룹 | 1-15 (Windows)<br>1-64 (Linux)<br><br>아래 참고를 참조 하세요. | 사용할 수 없음:<br> `\/""[]:|<>+=;,?*@&`<br><br>밑줄로 시작할 수 없습니다. 마침표 또는 하이픈으로 끝날 수 없습니다. |
> | virtualMachineScaleSets | 리소스 그룹 | 1-15 (Windows)<br>1-64 (Linux)<br><br>아래 참고를 참조 하세요. | 사용할 수 없음:<br> `\/""[]:|<>+=;,?*@&`<br><br>밑줄로 시작할 수 없습니다. 마침표 또는 하이픈으로 끝날 수 없습니다. |

> [!NOTE]
> Azure 가상 컴퓨터에는 리소스 이름과 호스트 이름 이라는 두 가지 고유한 이름이 있습니다. 포털에서 가상 컴퓨터를 만들 때 두 이름에 동일한 값이 사용 됩니다. 위의 표에 나와 있는 제한은 호스트 이름에 대 한 것입니다. 실제 리소스 이름은 최대 64자까지 가능합니다.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | containerGroups | 리소스 그룹 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. 연속 된 하이픈은 허용 되지 않습니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | registries | 전역 | 5-50 | 영숫자와. |
> | 레지스트리/buildTasks | 레지스트리 | 5-50 | 영숫자와. |
> | 레지스트리/buildTasks/단계 | 빌드 작업 | 5-50 | 영숫자와. |
> | 레지스트리/복제 | 레지스트리 | 5-50 | 영숫자와. |
> | 레지스트리/scopeMaps | 레지스트리 | 5-50 | 영숫자, 하이픈 및 밑줄입니다. |
> | 레지스트리/작업 | 레지스트리 | 5-50 | 영숫자, 하이픈 및 밑줄입니다. |
> | 레지스트리/토큰 | 레지스트리 | 5-50 | 영숫자, 하이픈 및 밑줄입니다. |
> | 레지스트리/웹 후크 | 레지스트리 | 5-50 | 영숫자와. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managedClusters | 리소스 그룹 | 1-63 | 영숫자, 밑줄 및 하이픈입니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | openShiftManagedClusters | 리소스 그룹 | 1-30 | 영숫자와. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | hubs | 리소스 그룹 | 1-64 | 영숫자와.<br><br>문자로 시작 합니다.  |
> | 허브/a p i 정책 | hub-and-spoke | 1-50 | 영숫자, 밑줄 및 마침표입니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 허브/커넥터 | hub-and-spoke | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/커넥터/매핑 | 커넥터 | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/상호 작용 | hub-and-spoke | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/kpi | hub-and-spoke | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/링크 | hub-and-spoke | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/예측 | hub-and-spoke | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/프로필 | hub-and-spoke | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/relationshipLinks | hub-and-spoke | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/관계 | hub-and-spoke | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/역할 할당 | hub-and-spoke | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |
> | 허브/뷰 | hub-and-spoke | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작 합니다. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 연결 | 리소스 그룹 | 1-180 | 사용할 수 없음:<br>`%&\\?/`<br><br>는 마침표 또는 공백으로 끝날 수 없습니다. |
> | resourceProviders | 리소스 그룹 | 3-64 | 사용할 수 없음:<br>`%&\\?/`<br><br>는 마침표 또는 공백으로 끝날 수 없습니다. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 작업 | 리소스 그룹 | 3-24 | 영숫자, 하이픈, 밑줄 및 마침표입니다. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 작업 영역 | 리소스 그룹 | 3-30 | 영숫자, 밑줄 및 하이픈 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 팩터리 | 전역 | 3-63 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 팩터리/데이터 흐름 | 공장 | 1-260 | 사용할 수 없음:<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작 합니다. |
> | 팩터리/데이터 집합 | 공장 | 1-260 | 사용할 수 없음:<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작 합니다. |
> | 팩터리/integrationRuntimes | 공장 | 3-63 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 팩터리/linkedservices.json 및 datasets.json | 공장 | 1-260 | 사용할 수 없음:<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작 합니다. |
> | 팩터리/파이프라인 | 공장 | 1-260 | 사용할 수 없음:<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작 합니다. |
> | 팩터리/트리거 | 공장 | 1-260 | 사용할 수 없음:<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작 합니다. |
> | 팩터리/트리거/처리 되지 않은 트리거 | 트리거 | 1-260 | 사용할 수 없음:<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작 합니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 전역 | 3-24 | 소문자 및 숫자 |
> | 계정/computePolicies | 아닙니다. | 3-60 | 영숫자, 하이픈 및 밑줄입니다. |
> | 계정/dataLakeStoreAccounts | 아닙니다. | 3-24 | 소문자 및 숫자 |
> | 계정/firewallRules | 아닙니다. | 3-50 | 영숫자, 하이픈 및 밑줄입니다. |
> | 계정/storageAccounts | 아닙니다. | 3-60 | 영숫자, 하이픈 및 밑줄입니다. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 전역 | 3-24 | 소문자 및 숫자 |
> | 계정/firewallRules | 아닙니다. | 3-50 | 영숫자, 하이픈 및 밑줄입니다. |
> | 계정/virtualNetworkRules | 아닙니다. | 3-50 | 영숫자, 하이픈 및 밑줄입니다. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서비스 | 리소스 그룹 | 2-62 | 영숫자, 하이픈, 마침표 및 밑줄<br><br>영숫자로 시작 합니다. |
> | 서비스/프로젝트 | 서비스 | 2-57 | 영숫자, 하이픈, 마침표 및 밑줄<br><br>영숫자로 시작 합니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서버 | 전역 | 3-63 | 소문자, 하이픈 및 숫자입니다.<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | 서버/데이터베이스 | 서버 | 1-63 | 영숫자 및 하이픈 |
> | servers/firewallRules | 서버 | 1-128 | 영숫자, 하이픈 및 밑줄입니다. |
> | servers/virtualNetworkRules | 서버 | 1-128 | 영숫자 및 하이픈 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서버 | 전역 | 3-63 | 소문자, 하이픈 및 숫자입니다.<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | 서버/데이터베이스 | 서버 | 1-63 | 영숫자 및 하이픈 |
> | servers/firewallRules | 서버 | 1-128 | 영숫자, 하이픈 및 밑줄입니다. |
> | servers/virtualNetworkRules | 서버 | 1-128 | 영숫자 및 하이픈 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서버 | 전역 | 3-63 | 소문자, 하이픈 및 숫자입니다.<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | 서버/데이터베이스 | 서버 | 1-63 | 영숫자 및 하이픈 |
> | servers/firewallRules | 서버 | 1-128 | 영숫자, 하이픈 및 밑줄입니다. |
> | servers/virtualNetworkRules | 서버 | 1-128 | 영숫자 및 하이픈 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | IotHubs | 전역 | 3-50 | 영숫자 및 하이픈<br><br>하이픈으로 끝날 수 없습니다. |
> | IotHubs/인증서 | IoT hub | 1-64 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | provisioningServices | 리소스 그룹 | 3-64 | 영숫자 및 하이픈<br><br>영숫자로 끝납니다. |
> | provisioningServices/인증서 | provisioningServices | 1-64 | 영숫자, 하이픈, 마침표 및 밑줄 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | labs | 리소스 그룹 | 1-50 | 영숫자, 밑줄 및 하이픈입니다. |
> | labs/customimages | 환경의 | 1-80 | 영숫자, 밑줄, 하이픈, 괄호 등이 있습니다. |
> | 랩/수식 | 환경의 | 1-80 | 영숫자, 밑줄, 하이픈, 괄호 등이 있습니다. |
> | labs/virtualmachines | 환경의 | 1-15 (Windows)<br>1-64 (Linux) | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. 모든 숫자일 수 없습니다. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | databaseAccounts | 전역 | 3-31 | 소문자, 숫자 및 하이픈<br><br>소문자 또는 숫자로 시작 합니다. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 도메인 | 리소스 그룹 | 3-50 | 영숫자 및 하이픈 |
> | 도메인/항목 | 도메인 | 3-50 | 영숫자 및 하이픈 |
> | eventSubscriptions | 리소스 그룹 | 3-64 | 영숫자 및 하이픈 |
> | topics | 리소스 그룹 | 3-50 | 영숫자 및 하이픈 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 클러스터 | 리소스 그룹 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 문자 또는 숫자로 끝납니다. |
> | 네임스페이스 | 전역 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 문자 또는 숫자로 끝납니다. |
> | 네임 스페이스/AuthorizationRules | namespace | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작 하 고 종료 합니다. |
> | 네임 스페이스/disasterRecoveryConfigs | namespace | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작 하 고 종료 합니다. |
> | 네임 스페이스/eventhubs | namespace | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작 하 고 종료 합니다. |
> | 네임 스페이스/eventhubs/authorizationRules | 이벤트 허브 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작 하 고 종료 합니다. |
> | 네임 스페이스/eventhubs/consumergroups | 이벤트 허브 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작 하 고 종료 합니다. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 클러스터 | 전역 | 3-59 | 영숫자 및 하이픈<br><br>문자 또는 숫자로 시작 하 고 종료 합니다. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 작업 | 리소스 그룹 | 2-64 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | IoTApps | 전역 | 2-63 | 소문자, 숫자 및 하이픈<br><br>소문자 또는 숫자로 시작 합니다. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | vaults | 전역 | 3-24 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 문자 또는 숫자로 끝납니다. 연속 하이픈을 포함할 수 없습니다. |
> | 자격 증명 모음/비밀 | Vault | 1-127 | 영숫자 및 하이픈 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 클러스터 | 전역 | 4-22 | 소문자 및 숫자<br><br>문자로 시작 합니다. |
> | /클러스터/데이터베이스 | cluster | 1-260 | 영숫자, 하이픈, 공백 및 마침표입니다. |
> | /클러스터/데이터베이스/dataConnections | 데이터베이스 | 1-40 | 영숫자, 하이픈, 공백 및 마침표입니다. |
> | /클러스터/데이터베이스/eventhubconnections | 데이터베이스 | 1-40 | 영숫자, 하이픈, 공백 및 마침표입니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | integrationAccounts | 리소스 그룹 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/어셈블리 | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/batchConfigurations | 통합 계정 | 1-20 | 영숫자와. |
> | integrationAccounts/인증서 | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/지도 | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/파트너 | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/rosettanetprocessconfigurations | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/스키마 | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationAccounts/세션 | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |
> | integrationServiceEnvironments | 리소스 그룹 | 1-80 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | integrationServiceEnvironments/managedApis | 통합 서비스 환경 | 1-80 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | 워크플로 | 리소스 그룹 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호입니다. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | commitmentPlans | 리소스 그룹 | 1-260 | 사용할 수 없음:<br>`<>*%&:?+/\\`<br><br>공백으로 끝날 수 없습니다. |
> | webServices | 리소스 그룹 | 1-260 | 사용할 수 없음:<br>`<>*%&:?+/\\`<br><br>공백으로 끝날 수 없습니다. |
> | 작업 영역 | 리소스 그룹 | 1-260 | 사용할 수 없음:<br>`<>*%&:?+/\\`<br><br>공백으로 끝날 수 없습니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 작업 영역 | 리소스 그룹 | 3-33 | 영숫자 및 하이픈 |
> | 작업 영역/계산 | 작업 영역 | 2-16 | 영숫자 및 하이픈 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 리소스 그룹 | 3-128 | 영숫자, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작 합니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 리소스 그룹 | 1-98 (리소스 그룹 이름 및 계정 이름) | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | mediaservices | 리소스 그룹 | 3-24 | 소문자 및 숫자 |
> | windowsazure.mediaservices/liveEvents | 미디어 서비스 | 1-32 | 영숫자 및 하이픈<br><br>영숫자로 시작 합니다. |
> | windowsazure.mediaservices/liveEvents/Liveoutput | 라이브 이벤트 | 1-256 | 영숫자 및 하이픈<br><br>영숫자로 시작 합니다. |
> | windowsazure.mediaservices/streamingEndpoints | 미디어 서비스 | 1-24 | 영숫자 및 하이픈<br><br>영숫자로 시작 합니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | applicationGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | applicationSecurityGroups | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | azureFirewalls | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 영숫자 또는 밑줄로 끝납니다. |
> | bastionHosts | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | connections | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | dnsZones | 리소스 그룹 | 1-63 문자<br><br>2 ~ 34 레이블<br><br>각 레이블은 마침표로 구분 되는 문자 집합입니다. 예를 들어 **contoso.com** 에는 두 개의 레이블이 있습니다. | 각 레이블은 영숫자, 밑줄 및 하이픈을 포함할 수 있습니다.<br><br>각 레이블은 마침표로 구분 됩니다. |
> | expressRouteCircuits | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | firewallPolicies | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | firewallPolicies/ruleGroups | 방화벽 정책 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | frontDoors | 전역 | 5-64 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | loadBalancers | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | loadBalancers 조정기/Loadbalancer.inboundnatrules | 부하 분산 장치 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | localNetworkGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | networkInterfaces | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | networkSecurityGroups | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | networkSecurityGroups/securityRules | 네트워크 보안 그룹 | 1-80 |  영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | networkWatchers | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | privateDnsZones | 리소스 그룹 | 1-63 문자<br><br>2 ~ 34 레이블<br><br>각 레이블은 마침표로 구분 되는 문자 집합입니다. 예를 들어 **contoso.com** 에는 두 개의 레이블이 있습니다. | 각 레이블은 영숫자, 밑줄 및 하이픈을 포함할 수 있습니다.<br><br>각 레이블은 마침표로 구분 됩니다. |
> | privateDnsZones / virtualNetworkLinks | 개인 DNS 영역 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | publicIPAddresses | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | publicIPPrefixes | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | routeFilters | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | routeFilters / routeFilterRules | 경로 필터 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | routeTables | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | routeTables/경로 | 경로 테이블 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | serviceEndpointPolicies | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | trafficmanagerprofiles | 전역 | 1-63 | 영숫자, 하이픈 및 마침표입니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | virtualNetworkGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | virtualNetworks | 리소스 그룹 | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | virtualnetworks/서브넷 | 가상 네트워크 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | virtualNetworks/virtualNetworkPeerings | 가상 네트워크 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | virtualWans | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | vpnGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | vpnGateways / vpnConnections | VPN 게이트웨이 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |
> | vpnSites | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈입니다.<br><br>영숫자로 시작 합니다. 끝 영숫자 또는 밑줄입니다. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 전역 | 6-50 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/AuthorizationRules | namespace | 1-256 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>시작 영숫자입니다. |
> | 네임 스페이스/notificationHubs | namespace | 1-260 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>시작 영숫자입니다. |
> | 네임 스페이스/notificationHubs/AuthorizationRules | 알림 허브 | 1-256 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>시작 영숫자입니다. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 클러스터 | 리소스 그룹 | 4-63 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 작업 영역 | 리소스 그룹 | 4-63 | 영숫자 및 하이픈<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 대시보드 | 리소스 그룹 | 3-160 | 영숫자 및 하이픈<br><br>제한 문자를 사용 하려면 사용 하려는 대시보드 이름이 **숨겨진** 태그 라는 태그를 추가 합니다. 대시보드를 표시할 때 포털에 해당 이름이 표시 됩니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 영숫자 및 하이픈<br><br>하이픈으로 시작할 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 용량 | region | 3-63 | 소문자 또는 숫자<br><br>소문자로 시작 합니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | vaults | 리소스 그룹 | 2-50 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. |
> | 자격 증명 모음/backupPolicies | 자격 증명 모음 | 3-150 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 하이픈으로 끝날 수 없습니다. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 전역 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. |
> | 네임 스페이스/AuthorizationRules | namespace | 1-50 |  영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/HybridConnections | namespace | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시가 있습니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/HybridConnections/authorizationRules | 하이브리드 연결 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/WcfRelays | namespace | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시가 있습니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/WcfRelays/authorizationRules | Wcf 릴레이 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 배포 | 리소스 그룹 | 1-64 | 영숫자, 밑줄, 괄호, 하이픈 및 마침표입니다. |
> | resourcegroups | 구독 | 1-90 | [Regex 설명서](/rest/api/resources/resourcegroups/createorupdate)와 일치 하는 영숫자, 밑줄, 괄호, 하이픈, 마침표 및 유니코드 문자입니다.<br><br>마침표로 끝날 수 없습니다. |
> | tagNames | resource | 1-512 | 사용할 수 없음:<br>`<>%&\?/` |
> | tagNames / tagValues | 태그 이름 | 1-256 | 모든 문자입니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 전역 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다.<br><br>자세한 내용은 [네임 스페이스 만들기](/rest/api/servicebus/create-namespace)를 참조 하세요. |
> | 네임 스페이스/AuthorizationRules | namespace | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>Alphnumeric로 시작 하 고 종료 합니다. |
> | 네임 스페이스/disasterRecoveryConfigs | 전역 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 영숫자로 끝납니다. |
> | 네임 스페이스/migrationConfigurations | namespace |  | 항상 **$default**해야 합니다. |
> | 네임 스페이스/큐 | namespace | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시가 있습니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/큐/authorizationRules | queue | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>Alphnumeric로 시작 하 고 종료 합니다. |
> | 네임 스페이스/항목 | namespace | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시가 있습니다.<br><br>영숫자를 사용 하 여 시작 하 고 종료 합니다. |
> | 네임 스페이스/토픽/authorizationRules | 토픽 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>Alphnumeric로 시작 하 고 종료 합니다. |
> | 네임 스페이스/토픽/구독 | 토픽 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>Alphnumeric로 시작 하 고 종료 합니다. |
> | 네임 스페이스/항목/구독/규칙 | 구독 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>Alphnumeric로 시작 하 고 종료 합니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 클러스터 | region | 4-23 | 소문자, 숫자 및 하이픈<br><br>소문자로 시작 합니다. 소문자 또는 숫자로 끝납니다. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | signalR | 전역 | 3-63 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 문자 또는 숫자로 끝납니다.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managedInstances | 전역 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | 서버 | 전역 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | 서버/데이터베이스 | 서버 | 1-128 | 사용할 수 없음:<br>`<>*%&:\/?`<br><br>는 마침표 또는 공백으로 끝날 수 없습니다. |
> | servers/databases/syncGroups | 데이터베이스 | 1-150 | 영숫자, 하이픈 및 밑줄입니다. |
> | servers/elasticPools | 서버 | 1-128 | 사용할 수 없음:<br>`<>*%&:\/?`<br><br>는 마침표 또는 공백으로 끝날 수 없습니다. |
> | servers/failoverGroups | 전역 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | servers/firewallRules | 서버 | 1-128 | 사용할 수 없음:<br>`<>*%&:;\/?`<br><br>마침표로 끝날 수 없습니다. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | storageAccounts | 전역 | 3-24 | 소문자 및 숫자 |
> | storageAccounts/blobServices | 저장소 계정 |  | `default`이어야 합니다. |
> | storageAccounts/blobServices/컨테이너 | 저장소 계정 | 3-63 | 소문자, 숫자 및 하이픈<br><br>소문자 또는 숫자로 시작 합니다. 연속 하이픈을 사용할 수 없습니다. |
> | storageAccounts/fileServices | 저장소 계정 |  | `default`이어야 합니다. |
> | storageAccounts/fileServices/공유 | 저장소 계정 | 3-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |
> | storageAccounts/managementPolicies | 저장소 계정 |  | `default`이어야 합니다. |
> | blob | container | 1-1024 | 모든 URL 문자, 대/소문자 구분 |
> | queue | 저장소 계정 | 3-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |
> | 테이블 | 저장소 계정 | 3-63 | 영숫자와.<br><br>문자로 시작 합니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | storageSyncServices | 리소스 그룹 | 1-260 | 영숫자, 공백, 마침표, 하이픈 및 밑줄을 포함 합니다.<br><br>는 마침표 또는 공백으로 끝날 수 없습니다. |
> | storageSyncServices/syncGroups | 저장소 동기화 서비스 | 1-260 | 영숫자, 공백, 마침표, 하이픈 및 밑줄을 포함 합니다.<br><br>는 마침표 또는 공백으로 끝날 수 없습니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managers | 리소스 그룹 | 2-50 | 영숫자 및 하이픈<br><br>문자로 시작 합니다. 영숫자로 끝납니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | streamingjobs | 리소스 그룹 | 3-63 | 영숫자, 하이픈 및 밑줄입니다. |
> | streamingjobs 대해/함수 | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄입니다. |
> | streamingjobs 대해/입력 | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄입니다. |
> | streamingjobs 대해/출력 | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄입니다. |
> | streamingjobs 대해/변환 | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄입니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 환경 | 리소스 그룹 | 1-90 | 사용할 수 없음:<br>`'<>%&:\?/#` |
> | 환경/Accesspolicy | 환경 | 1-90 | 사용할 수 없음:<br> `'<>%&:\?/#` |
> | 환경/s s o 원본 | 환경 | 1-90 | 사용할 수 없음:<br>`'<>%&:\?/#` |
> | environment/referenceDataSets | 환경 | 3-63 | 영숫자와 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | serverfarms | 리소스 그룹 | 1-40 | 영숫자 및 하이픈 |
> | sites | 전역 | 2-60 | 영숫자 및 하이픈을 포함 합니다.<br><br>하이픈으로 시작 하거나 끝날 수 없습니다. |
> | 사이트/슬롯 | site | 2-59 | 영숫자 및 하이픈 |

## <a name="next-steps"></a>다음 단계

리소스의 이름을 지정 하는 방법에 대 한 권장 사항은 [준비: 권장 되는 명명 및 태그 지정 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)을 참조 하세요.
