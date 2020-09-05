---
title: 리소스 명명 제한
description: Azure 리소스의 이름을 지정할 때 적용되는 규칙 및 제한 사항을 보여 줍니다.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 56c50354a6ecbd6ba1d9a806f986cea1c4f3215f
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235981"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 리소스에 대한 명명 규칙 및 제한 사항

이 문서에서는 Azure 리소스의 명명 규칙 및 제한 사항을 요약해서 설명합니다. 리소스의 이름을 지정하는 방법에 대한 권장 사항은 [권장 명명 및 태그 지정 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)을 참조하세요.

이 문서에서는 리소스 공급자 네임 스페이스 별로 리소스를 나열 합니다. 리소스 공급자가 Azure 서비스와 일치 하는 방법에 대 한 목록은 [azure 서비스에 대 한 리소스 공급자](azure-services-resource-providers.md)를 참조 하세요.

리소스 이름은 유효 문자 열에 명시된 경우를 제외하고 대/소문자를 구분하지 않습니다.

다음 표에서 용어 영숫자는 다음을 나타냅니다.

* **a**~**z**(소문자)
* **A**~**Z**(대문자)
* **0**~**9**(숫자)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 리소스 그룹 | 3-63 | 소문자와 숫자만<br><br>소문자로 시작합니다. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서비스 | 글로벌 | 1-50 | 영숫자 및 하이픈<br><br>문자로 시작하고 영숫자로 끝납니다. |
> | service / apis | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / issues | api | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / issues / attachments | 이슈 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / issues / comments | 이슈 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / operations | api | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / operations / tags | operation(작업) | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / releases | api | 1-80 | 영숫자, 밑줄 및 하이픈<br><br>영숫자 또는 밑줄로 시작하고 끝납니다. |
> | service / apis / schemas | api | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / tagDescriptions | api | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / apis / tags | api | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / api-version-sets | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / authorizationServers | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / backends | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / certificates | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / diagnostics | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / groups | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / groups / users | group | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / identityProviders | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / loggers | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / notifications | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / notifications / recipientEmails | 알림 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / openidConnectProviders | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / policies | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / products | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / products / apis | product | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / products / groups | product | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / products / tags | product | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / properties | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / subscriptions | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / tags | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / templates | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |
> | service / users | 서비스 | 1-256 | 다음을 사용할 수 없습니다.<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | configurationStores | 리소스 그룹 | 5-50 | 영숫자, 밑줄 및 하이픈 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | locks | 할당 범위 | 1-90 | 영숫자, 마침표, 밑줄, 하이픈, 괄호<br><br>마침표로 끝날 수 없습니다. |
> | policyAssignments | 할당 범위 | 1-128 표시 이름<br><br>1-64 리소스 이름 | 표시 이름에는 모든 문자를 사용할 수 있습니다.<br><br>리소스 이름은 `%`를 포함할 수 없고 마침표 또는 공백으로 끝날 수 없습니다. |
> | policyDefinitions | 정의의 범위 | 1-128 표시 이름<br><br>1-64 리소스 이름 | 표시 이름에는 모든 문자를 사용할 수 있습니다.<br><br>리소스 이름은 `%`를 포함할 수 없고 마침표 또는 공백으로 끝날 수 없습니다. |
> | policySetDefinitions | 정의의 범위 | 1-128 표시 이름<br><br>1-64 리소스 이름<br><br>1-24 관리 그룹 범위에서 리소스 이름 | 표시 이름에는 모든 문자를 사용할 수 있습니다.<br><br>리소스 이름은 `%`를 포함할 수 없고 마침표 또는 공백으로 끝날 수 없습니다.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | automationAccounts | 리소스 그룹 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작하고 영숫자로 끝납니다. |
> | automationAccounts / certificates | Automation 계정 | 1-128 | 다음을 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다.  |
> | automationAccounts / connections | Automation 계정 | 1-128 | 다음을 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts / credentials | Automation 계정 | 1-128 | 다음을 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts / runbooks | Automation 계정 | 1-63 | 영숫자, 밑줄 및 하이픈<br><br>문자로 시작합니다.  |
> | automationAccounts / schedules | Automation 계정 | 1-128 | 다음을 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts / variables | Automation 계정 | 1-128 | 다음을 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | automationAccounts / watchers | Automation 계정 | 1-63 |  영숫자, 밑줄 및 하이픈<br><br>문자로 시작합니다. |
> | automationAccounts / webhooks | Automation 계정 | 1-128 | 다음을 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | batchAccounts | 지역 | 3-24 | 소문자와 숫자만 |
> | batchAccounts / applications | Batch 계정 | 1-64 | 영숫자, 밑줄 및 하이픈 |
> | batchAccounts / certificates | Batch 계정 | 5-45 | 영숫자, 밑줄 및 하이픈 |
> | batchAccounts / pools | Batch 계정 | 1-64 | 영숫자, 밑줄 및 하이픈 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | blockchainMembers | 글로벌 | 2-20 | 소문자와 숫자만<br><br>소문자로 시작합니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | botServices | 글로벌 | 2-64 |  영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. |
> | botServices / channels | BOT Service | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. |
> | botServices / Connections | BOT Service | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. |
> | enterpriseChannels | 리소스 그룹 | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | Redis | 글로벌 | 1-63 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. 연속 하이픈은 허용되지 않습니다. |
> | Redis / firewallRules | Redis | 1-256 | 영숫자 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 프로필 | 리소스 그룹 | 1-260 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |
> | profiles / endpoints | 글로벌 | 1-50 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | certificateOrders | 리소스 그룹 | 3-30 | 영숫자 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 리소스 그룹 | 2-64 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | availabilitySets | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | diskEncryptionSets | 리소스 그룹 | 1-80 | 영숫자 및 밑줄 |
> | disks | 리소스 그룹 | 1-80 | 영숫자 및 밑줄 |
> | galleries | 리소스 그룹 | 1-80 | 영숫자 및 마침표<br><br>영숫자로 시작하고 끝납니다. |
> | galleries / applications | gallery | 1-80 | 영숫자, 하이픈 및 마침표<br><br>영숫자로 시작하고 끝납니다. |
> | galleries / applications/versions | 애플리케이션 | 32비트 정수 | 숫자 및 마침표 |
> | galleries / images | gallery | 1-80 | 영숫자, 하이픈 및 마침표<br><br>영숫자로 시작하고 끝납니다. |
> | galleries / images / versions | 이미지 | 32비트 정수 | 숫자 및 마침표 |
> | images | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | 스냅샷 | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | virtualMachines | 리소스 그룹 | 1-15(Windows)<br>1-64(Linux)<br><br>아래 참고 사항을 참조하세요. | 다음을 사용할 수 없습니다.<br> `\/"'[]:|<>+=;,?*@&`<br><br>밑줄로 시작할 수 없습니다. 마침표 또는 하이픈으로 끝날 수 없습니다. |
> | virtualMachineScaleSets | 리소스 그룹 | 1-15(Windows)<br>1-64(Linux)<br><br>아래 참고 사항을 참조하세요. | 다음을 사용할 수 없습니다.<br> `\/"'[]:|<>+=;,?*@&`<br><br>밑줄로 시작할 수 없습니다. 마침표 또는 하이픈으로 끝날 수 없습니다. |

> [!NOTE]
> Azure Virtual Machines에는 리소스 이름과 호스트 이름이라는 두 가지 고유한 이름이 있습니다. 포털에서 가상 머신을 만들 때 두 이름에 동일한 값이 사용됩니다. 위의 표에 나와 있는 제한은 호스트 이름에 대한 것입니다. 실제 리소스 이름은 최대 64자까지 가능합니다.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | containerGroups | 리소스 그룹 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작하거나 끝날 수 없습니다. 연속 하이픈은 허용되지 않습니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | registries | 글로벌 | 5-50 | 영숫자 |
> | registries / buildTasks | 사용된 | 5-50 | 영숫자 |
> | registries / buildTasks/steps | 빌드 작업 | 5-50 | 영숫자 |
> | registries / replications | 사용된 | 5-50 | 영숫자 |
> | registries / scopeMaps | 사용된 | 5-50 | 영숫자, 하이픈 및 밑줄 |
> | registries / tasks | 사용된 | 5-50 | 영숫자, 하이픈 및 밑줄 |
> | registries / tokens | 사용된 | 5-50 | 영숫자, 하이픈 및 밑줄 |
> | registries / webhooks | 사용된 | 5-50 | 영숫자 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managedClusters | 리소스 그룹 | 1-63 | 영숫자, 밑줄 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |
> | openShiftManagedClusters | 리소스 그룹 | 1-30 | 영숫자 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | hubs | 리소스 그룹 | 1-64 | 영숫자<br><br>문자로 시작합니다.  |
> | hubs / authorizationPolicies | hub | 1-50 | 영숫자, 밑줄 및 마침표<br><br>영숫자로 시작하고 끝납니다. |
> | hubs / connectors | hub | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / connectors/mappings | 커넥터 | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / interactions | hub | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / kpi | hub | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / links | hub | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / predictions | hub | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / profiles | hub | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / relationshipLinks | hub | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / relationships | hub | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / roleAssignments | hub | 1-128 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |
> | hubs / views | hub | 1-512 | 영숫자 및 밑줄<br><br>문자로 시작합니다. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | associations | 리소스 그룹 | 1-180 | 다음을 사용할 수 없습니다.<br>`%&\\?/`<br><br>마침표 또는 공백으로 끝날 수 없습니다. |
> | resourceProviders | 리소스 그룹 | 3-64 | 다음을 사용할 수 없습니다.<br>`%&\\?/`<br><br>마침표 또는 공백으로 끝날 수 없습니다. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | jobs | 리소스 그룹 | 3-24 | 영숫자, 하이픈, 밑줄 및 마침표 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaces | 리소스 그룹 | 3-30 | 영숫자, 밑줄 및 하이픈 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | factories | 글로벌 | 3-63 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |
> | factories / dataflows | 공장 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작합니다. |
> | factories / datasets | 공장 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작합니다. |
> | factories / integrationRuntimes | 공장 | 3-63 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |
> | factories / linkedservices | 공장 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작합니다. |
> | factories / pipelines | 공장 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작합니다. |
> | factories / triggers | 공장 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작합니다. |
> | factories / triggers / rerunTriggers | 트리거 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>영숫자로 시작합니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 글로벌 | 3-24 | 소문자와 숫자만 |
> | accounts / computePolicies | account | 3-60 | 영숫자, 하이픈 및 밑줄 |
> | accounts / dataLakeStoreAccounts | account | 3-24 | 소문자와 숫자만 |
> | accounts / firewallRules | account | 3-50 | 영숫자, 하이픈 및 밑줄 |
> | accounts / storageAccounts | account | 3-60 | 영숫자, 하이픈 및 밑줄 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 글로벌 | 3-24 | 소문자와 숫자만 |
> | accounts / firewallRules | account | 3-50 | 영숫자, 하이픈 및 밑줄 |
> | accounts / virtualNetworkRules | account | 3-50 | 영숫자, 하이픈 및 밑줄 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | services | 리소스 그룹 | 2-62 | 영숫자, 하이픈, 마침표 및 밑줄<br><br>영숫자로 시작합니다. |
> | services / projects | 서비스 | 2-57 | 영숫자, 하이픈, 마침표 및 밑줄<br><br>영숫자로 시작합니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 글로벌 | 3-63 | 소문자, 하이픈 및 숫자<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | servers / databases | servers | 1-63 | 영숫자 및 하이픈 |
> | servers / firewallRules | servers | 1-128 | 영숫자, 하이픈 및 밑줄 |
> | servers / virtualNetworkRules | servers | 1-128 | 영숫자 및 하이픈 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 글로벌 | 3-63 | 소문자, 하이픈 및 숫자<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | servers / databases | servers | 1-63 | 영숫자 및 하이픈 |
> | servers / firewallRules | servers | 1-128 | 영숫자, 하이픈 및 밑줄 |
> | servers / virtualNetworkRules | servers | 1-128 | 영숫자 및 하이픈 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 글로벌 | 3-63 | 소문자, 하이픈 및 숫자<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | servers / databases | servers | 1-63 | 영숫자 및 하이픈 |
> | servers / firewallRules | servers | 1-128 | 영숫자, 하이픈 및 밑줄 |
> | servers / virtualNetworkRules | servers | 1-128 | 영숫자 및 하이픈 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | IotHubs | 글로벌 | 3-50 | 영숫자 및 하이픈<br><br>하이픈으로 끝날 수 없습니다. |
> | IotHubs / certificates | IoT hub | 1-64 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | provisioningServices | 리소스 그룹 | 3-64 | 영숫자 및 하이픈<br><br>영숫자로 끝납니다. |
> | provisioningServices / certificates | provisioningServices | 1-64 | 영숫자, 하이픈, 마침표 및 밑줄 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | labs | 리소스 그룹 | 1-50 | 영숫자, 밑줄 및 하이픈 |
> | labs / customimages | lab | 1-80 | 영숫자, 밑줄, 하이픈 및 괄호 |
> | labs / formulas | lab | 1-80 | 영숫자, 밑줄, 하이픈 및 괄호 |
> | labs / virtualmachines | lab | 1-15(Windows)<br>1-64(Linux) | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. 모두 숫자일 수는 없습니다. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | databaseAccounts | 글로벌 | 3-44 | 소문자, 숫자 및 하이픈<br><br>소문자 또는 숫자로 시작합니다. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | domains | 리소스 그룹 | 3-50 | 영숫자 및 하이픈 |
> | domains / topics | 도메인 | 3-50 | 영숫자 및 하이픈 |
> | eventSubscriptions | 리소스 그룹 | 3-64 | 영숫자 및 하이픈 |
> | topics | 리소스 그룹 | 3-50 | 영숫자 및 하이픈 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 리소스 그룹 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. |
> | 네임스페이스 | 글로벌 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. |
> | namespaces / AuthorizationRules | 네임스페이스 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작하고 끝나야 합니다. |
> | namespaces / disasterRecoveryConfigs | 네임스페이스 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작하고 끝나야 합니다. |
> | namespaces / eventhubs | 네임스페이스 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작하고 끝나야 합니다. |
> | namespaces / eventhubs / authorizationRules | event hub | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작하고 끝나야 합니다. |
> | namespaces / eventhubs / consumergroups | event hub | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작하고 끝나야 합니다. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 글로벌 | 3-59 | 영숫자 및 하이픈<br><br>문자 또는 숫자로 시작하고 끝나야 합니다. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | jobs | 리소스 그룹 | 2-64 | 영숫자 및 하이픈<br><br>문자로 시작합니다. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | actionGroups | 리소스 그룹 | 1-260 | 다음을 사용할 수 없습니다.<br>`/` <br><br>공백 또는 마침표로 끝날 수 없습니다.  |
> | components | 리소스 그룹 | 1-260 | 다음을 사용할 수 없습니다.<br>`%&\?/` <br><br>공백 또는 마침표로 끝날 수 없습니다.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | IoTApps | 글로벌 | 2-63 | 소문자, 숫자 및 하이픈<br><br>소문자 또는 숫자로 시작합니다. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | vaults | 글로벌 | 3-24 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. 연속 하이픈을 포함할 수 없습니다. |
> | vaults / secrets | Vault | 1-127 | 영숫자 및 하이픈 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 글로벌 | 4-22 | 소문자와 숫자만<br><br>문자로 시작합니다. |
> | /clusters / databases | cluster | 1-260 | 영숫자, 하이픈, 공백 및 마침표 |
> | /clusters / databases / dataConnections | 데이터베이스 | 1-40 | 영숫자, 하이픈, 공백 및 마침표 |
> | /clusters / databases / eventhubconnections | 데이터베이스 | 1-40 | 영숫자, 하이픈, 공백 및 마침표 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | integrationAccounts | 리소스 그룹 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / assemblies | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / batchConfigurations | 통합 계정 | 1-20 | 영숫자 |
> | integrationAccounts / certificates | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / maps | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / partners | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / rosettanetprocessconfigurations | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / schemas | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationAccounts / sessions | 통합 계정 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |
> | integrationServiceEnvironments | 리소스 그룹 | 1-80 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | integrationServiceEnvironments / managedApis | 통합 서비스 환경 | 1-80 | 영숫자, 하이픈, 마침표 및 밑줄 |
> | workflows | 리소스 그룹 | 1-80 | 영숫자, 하이픈, 밑줄, 마침표 및 괄호 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | commitmentPlans | 리소스 그룹 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*%&:?+/\\`<br><br>공백으로 끝날 수 없습니다. |
> | webServices | 리소스 그룹 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*%&:?+/\\`<br><br>공백으로 끝날 수 없습니다. |
> | workspaces | 리소스 그룹 | 1-260 | 다음을 사용할 수 없습니다.<br>`<>*%&:?+/\\`<br><br>공백으로 끝날 수 없습니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaces | 리소스 그룹 | 3-33 | 영숫자 및 하이픈 |
> | workspaces / computes | 작업 영역 | 2-16 | 영숫자 및 하이픈 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 리소스 그룹 | 3-128 | 영숫자, 하이픈 및 밑줄<br><br>문자 또는 숫자로 시작합니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 리소스 그룹 | 1-98(리소스 그룹 이름 및 계정 이름) | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | mediaservices | 리소스 그룹 | 3-24 | 소문자와 숫자만 |
> | mediaservices / liveEvents | 미디어 서비스 | 1-32 | 영숫자 및 하이픈<br><br>영숫자로 시작합니다. |
> | mediaservices / liveEvents / liveOutputs | 라이브 이벤트 | 1-256 | 영숫자 및 하이픈<br><br>영숫자로 시작합니다. |
> | mediaservices / streamingEndpoints | 미디어 서비스 | 1-24 | 영숫자 및 하이픈<br><br>영숫자로 시작합니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | applicationGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | applicationSecurityGroups | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | azureFirewalls | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | bastionHosts | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | connections | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | dnsZones | 리소스 그룹 | 1-63자<br><br>2-34개 레이블<br><br>각 레이블은 마침표로 구분된 문자 세트입니다. 예를 들어 **contoso.com**에는 두 개의 레이블이 있습니다. | 각 레이블은 영숫자, 밑줄 및 하이픈을 포함할 수 있습니다.<br><br>각 레이블은 마침표로 구분됩니다. |
> | expressRouteCircuits | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | firewallPolicies | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | firewallPolicies / ruleGroups | 방화벽 정책 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | frontDoors | 글로벌 | 5-64 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |
> | loadBalancers | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | loadBalancers / inboundNatRules | 부하 분산 장치 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | localNetworkGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | networkInterfaces | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | networkSecurityGroups | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | networkSecurityGroups / securityRules | 네트워크 보안 그룹 | 1-80 |  영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | networkWatchers | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | privateDnsZones | 리소스 그룹 | 1-63자<br><br>2-34개 레이블<br><br>각 레이블은 마침표로 구분된 문자 세트입니다. 예를 들어 **contoso.com**에는 두 개의 레이블이 있습니다. | 각 레이블은 영숫자, 밑줄 및 하이픈을 포함할 수 있습니다.<br><br>각 레이블은 마침표로 구분됩니다. |
> | privateDnsZones / virtualNetworkLinks | 프라이빗 DNS 영역 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | publicIPAddresses | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | publicIPPrefixes | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | routeFilters | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | routeFilters / routeFilterRules | 경로 필터 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | routeTables | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | routeTables / routes | 경로 테이블 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | serviceEndpointPolicies | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | trafficmanagerprofiles | 글로벌 | 1-63 | 영숫자, 하이픈 및 마침표<br><br>영숫자로 시작하고 끝납니다. |
> | virtualNetworkGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | virtualNetworks | 리소스 그룹 | 2-64 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | virtualnetworks / subnets | 가상 네트워크 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | virtualNetworks / virtualNetworkPeerings | 가상 네트워크 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | virtualWans | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | vpnGateways | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | vpnGateways / vpnConnections | VPN 게이트웨이 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |
> | vpnSites | 리소스 그룹 | 1-80 | 영숫자, 밑줄, 마침표 및 하이픈<br><br>영숫자로 시작합니다. 영숫자 또는 밑줄로 끝납니다. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 글로벌 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 영숫자로 끝납니다. |
> | namespaces / AuthorizationRules | 네임스페이스 | 1-256 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작합니다. |
> | namespaces / notificationHubs | 네임스페이스 | 1-260 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작합니다. |
> | namespaces / notificationHubs / AuthorizationRules | 알림 허브 | 1-256 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작합니다. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 리소스 그룹 | 4-63 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |
> | workspaces | 리소스 그룹 | 4-63 | 영숫자 및 하이픈<br><br>영숫자로 시작하고 끝납니다. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | solutions | 작업 영역 | 해당 없음 | Microsoft에서 작성한 솔루션의 경우 이름은 다음 패턴이어야 합니다.<br>`SolutionType(WorkspaceName)`<br><br>타사에서 작성한 솔루션의 경우 이름은 다음 패턴이어야 합니다.<br>`SolutionType[WorkspaceName]`<br><br>예를 들어 올바른 이름은 다음과 같습니다.<br>`AntiMalware(contoso-IT)`<br><br>솔루션 유형은 대/소문자를 구분합니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | dashboards | 리소스 그룹 | 3-160 | 영숫자 및 하이픈<br><br>제한된 문자를 사용하려면 사용하려는 대시보드 이름에 **hidden-title** 태그를 추가합니다. 대시보드를 표시할 때 포털에 해당 이름이 표시됩니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 영숫자 및 하이픈<br><br>하이픈으로 시작할 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 소문자 또는 숫자<br><br>소문자로 시작합니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | vaults | 리소스 그룹 | 2-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. |
> | vaults / backupPolicies | 자격 증명 모음 | 3-150 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 하이픈으로 끝날 수 없습니다. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 글로벌 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. |
> | namespaces / AuthorizationRules | 네임스페이스 | 1-50 |  영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / HybridConnections | 네임스페이스 | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / HybridConnections/authorizationRules | 하이브리드 연결 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / WcfRelays | 네임스페이스 | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / WcfRelays / authorizationRules | WCF 릴레이 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 배포 | 리소스 그룹 | 1-64 | 영숫자, 밑줄, 괄호, 하이픈 및 마침표 |
> | resourcegroups | subscription | 1-90 | [regex 설명서](/rest/api/resources/resourcegroups/createorupdate)와 일치하는 영숫자, 밑줄, 괄호, 하이픈, 마침표 및 유니코드 문자<br><br>마침표로 끝날 수 없습니다. |
> | tagNames | resource | 1-512 | 다음을 사용할 수 없습니다.<br>`<>%&\?/` |
> | tagNames / tagValues | 태그 이름 | 1-256 | 모두 문자입니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 글로벌 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다.<br><br>자세한 내용은 [네임스페이스 만들기](/rest/api/servicebus/create-namespace)를 참조하세요. |
> | namespaces / AuthorizationRules | 네임스페이스 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / disasterRecoveryConfigs | 글로벌 | 6-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 영숫자로 끝납니다. |
> | namespaces / migrationConfigurations | 네임스페이스 |  | 항상 **$default**여야 합니다. |
> | namespaces / queues | 네임스페이스 | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / queues / authorizationRules | queue | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / topics | 네임스페이스 | 1-260 | 영숫자, 마침표, 하이픈, 밑줄 및 슬래시<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / topics / authorizationRules | 토픽 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / topics / subscriptions | 토픽 | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |
> | namespaces / topics / subscriptions / rules | subscription | 1-50 | 영숫자, 마침표, 하이픈 및 밑줄<br><br>영숫자로 시작하고 끝납니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 소문자, 숫자 및 하이픈<br><br>소문자로 시작합니다. 소문자 또는 숫자로 끝납니다. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | signalR | 글로벌 | 3-63 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managedInstances | 글로벌 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | servers | 글로벌 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | servers / administrators | 서버 |  | `ActiveDirectory`이어야 합니다. |
> | servers / databases | 서버 | 1-128 | 다음을 사용할 수 없습니다.<br>`<>*%&:\/?`<br><br>마침표 또는 공백으로 끝날 수 없습니다. |
> | servers / databases / syncGroups | 데이터베이스 | 1-150 | 영숫자, 하이픈 및 밑줄 |
> | servers / elasticPools | 서버 | 1-128 | 다음을 사용할 수 없습니다.<br>`<>*%&:\/?`<br><br>마침표 또는 공백으로 끝날 수 없습니다. |
> | servers / failoverGroups | 글로벌 | 1-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | servers / firewallRules | 서버 | 1-128 | 다음을 사용할 수 없습니다.<br>`<>*%&:;\/?`<br><br>마침표로 끝날 수 없습니다. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | storageAccounts | 글로벌 | 3-24 | 소문자와 숫자만 |
> | storageAccounts / blobServices | 스토리지 계정 만들기 |  | `default`이어야 합니다. |
> | storageAccounts / blobServices / containers | 스토리지 계정 만들기 | 3-63 | 소문자, 숫자 및 하이픈<br><br>소문자 또는 숫자로 시작합니다. 연속 하이픈을 사용할 수 없습니다. |
> | storageAccounts / fileServices | 스토리지 계정 만들기 |  | `default`이어야 합니다. |
> | storageAccounts / fileServices / shares | 스토리지 계정 만들기 | 3-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작하거나 끝날 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |
> | storageAccounts / managementPolicies | 스토리지 계정 만들기 |  | `default`이어야 합니다. |
> | blob | container | 1-1024 | 모든 URL 문자, 대/소문자 구분 |
> | queue | 스토리지 계정 만들기 | 3-63 | 소문자, 숫자 및 하이픈<br><br>하이픈으로 시작하거나 끝날 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |
> | 테이블 | 스토리지 계정 만들기 | 3-63 | 영숫자<br><br>문자로 시작합니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | storageSyncServices | 리소스 그룹 | 1-260 | 영숫자, 공백, 마침표, 하이픈 및 밑줄<br><br>마침표 또는 공백으로 끝날 수 없습니다. |
> | storageSyncServices / syncGroups | 스토리지 동기화 서비스 | 1-260 | 영숫자, 공백, 마침표, 하이픈 및 밑줄<br><br>마침표 또는 공백으로 끝날 수 없습니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managers | 리소스 그룹 | 2-50 | 영숫자 및 하이픈<br><br>문자로 시작합니다. 영숫자로 끝납니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | streamingjobs | 리소스 그룹 | 3-63 | 영숫자, 하이픈 및 밑줄 |
> | streamingjobs / functions | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄 |
> | streamingjobs / inputs | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄 |
> | streamingjobs / outputs | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄 |
> | streamingjobs / transformations | 스트리밍 작업 | 3-63 | 영숫자, 하이픈 및 밑줄 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | environments | 리소스 그룹 | 1-90 | 다음을 사용할 수 없습니다.<br>`'<>%&:\?/#` |
> | environments / accessPolicies | 환경 | 1-90 | 다음을 사용할 수 없습니다.<br> `'<>%&:\?/#` |
> | environments / eventSources | 환경 | 1-90 | 다음을 사용할 수 없습니다.<br>`'<>%&:\?/#` |
> | environments / referenceDataSets | 환경 | 3-63 | 영숫자 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 엔터티 | 범위 | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | certificates | 리소스 그룹 | 1-260 | 다음을 사용할 수 없습니다.<br>`/` <br><br>공백 또는 마침표로 끝날 수 없습니다.  | 
> | serverfarms | 리소스 그룹 | 1-40 | 영숫자 및 하이픈 |
> | sites | 글로벌 | 2-60 | 영숫자 및 하이픈을 포함합니다.<br><br>하이픈으로 시작하거나 끝날 수 없습니다. |
> | sites / slots | site | 2-59 | 영숫자 및 하이픈 |

> [!NOTE]
> Azure Functions에는 Microsoft.Web/sites와 동일한 명명 규칙 및 제한 사항이 있습니다.

## <a name="next-steps"></a>다음 단계

리소스 이름을 표시하는 방법에 대한 권장 사항은 [준비: 권장 명명 및 태그 지정 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)을 참조하세요.
