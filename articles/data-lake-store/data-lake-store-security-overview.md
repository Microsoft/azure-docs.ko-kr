---
title: Azure Data Lake Storage Gen1의 보안 개요 | Microsoft Docs
description: 인증, 권한 부여, 네트워크 격리, 데이터 보호 및 감사를 비롯 한 Azure Data Lake Storage Gen1의 보안 기능에 대해 알아봅니다.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: ec0e62297e6eee995fc571589d450176c81f8aac
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192820"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1의 보안

대부분의 기업에서는 현명한 의사 결정을 내리는 데 도움을 주는 비즈니스 insights에 대한 빅 데이터 분석을 활용합니다. 조직은 다양한 사용자 수가 증가하는 복잡하고 규제된 환경을 포함할 수 있습니다. 중요한 비즈니스 데이터가 개별 사용자에게 부여된 올바른 액세스 수준으로 더욱 안전하게 저장되도록 하는 것이 기업에게 필수적입니다. Azure Data Lake Storage Gen1은 이러한 보안 요구 사항에 부응하도록 설계되었습니다. 이 문서에서는 다음을 포함하는 Data Lake Storage Gen1의 보안 기능에 대해 알아봅니다.

* 인증
* 권한 부여
* 네트워크 격리
* 데이터 보호
* 감사

## <a name="authentication-and-identity-management"></a>인증 및 ID 관리

인증은 사용자가 Data Lake Storage Gen1 또는 Data Lake Storage Gen1에 연결하는 서비스와 상호 작용할 때 사용자의 ID를 확인하는 프로세스입니다. ID 관리 및 인증을 위해 Data Lake Storage Gen1은 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)와 사용자 및 그룹의 관리를 간소화하는 포괄적인 ID 및 액세스 관리 클라우드 솔루션을 사용합니다.

각 Azure 구독은 Azure Active Directory의 인스턴스와 연결될 수 있습니다. 사용자 및 Azure Active Directory 서비스에서 정의된 서비스 ID는 Azure Portal, 명령줄 도구를 사용하거나 Data Lake Storage Gen1 SDK를 사용하여 조직이 빌드하는 클라이언트 애플리케이션을 통해 Data Lake Storage Gen1 계정에 액세스할 수 있습니다. 중앙 집중식 액세스 제어 메커니즘으로 Azure Active Directory를 사용하는 주요 이점은 다음과 같습니다.

* 단순화된 ID 수명 주기 관리. 디렉터리에서 계정을 삭제하거나 비활성화하여 사용자 또는 서비스(서비스 주체 ID)를 신속하게 만들고 해지할 수 있습니다.
* 다단계 인증. [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) 은 사용자 로그인 및 트랜잭션에 대해 추가 보안 계층을 제공합니다.
* OAuth 또는 OpenID와 같은 표준 개방형 프로토콜을 통해 모든 클라이언트에서 인증.
* 엔터프라이즈 디렉터리 서비스 및 클라우드 ID 공급자와 페더레이션.

## <a name="authorization-and-access-control"></a>권한 부여 및 액세스 제어

사용자가 Data Lake Storage Gen1에 액세스할 수 있도록 Azure Active Directory가 사용자를 인증한 후 권한 부여로 Data Lake Storage Gen1에 대한 액세스 권한을 제어합니다. Data Lake Storage Gen1은 다음과 같은 방식으로 계정 관련 작업과 데이터 관련 작업에 대한 권한 부여를 구분합니다.

* 계정 관리를 위한 [AZURE RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md)
* 저장소에서 데이터에 액세스하기 위한 POSIX ACL

### <a name="rbac-for-account-management"></a>계정 관리를 위한 RBAC

기본적으로 네 가지 기본 역할이 Data Lake Storage Gen1에 대해 정의됩니다. 역할은 Azure Portal, PowerShell cmdlet 및 REST API를 통해 Data Lake Storage Gen1 계정에서 다양한 작업을 허용합니다. 소유자 및 참여자 역할은 해당 계정에 다양한 관리 기능을 수행할 수 있습니다. 계정 관리 데이터를 보기만 하는 사용자에게 읽기 권한자 역할을 할당할 수 있습니다.

![Azure 역할](./media/data-lake-store-security-overview/rbac-roles.png "Azure 역할")

계정 관리에 대한 역할이 할당되어 있지만 일부 역할은 데이터 액세스에 영향을 줍니다. 파일 시스템에 대해 사용자가 수행할 수 있는 작업에 대한 액세스를 제어하기 위해 ACL을 사용해야 합니다. 다음 표는 관리 권한 및 기본 역할에 대한 데이터 액세스 권한의 요약을 보여 줍니다.

| 역할 | 관리 권한 | 데이터 액세스 권한 | 설명 |
| --- | --- | --- | --- |
| 할당된 역할 없음 |None |ACL에 의해 제어 |사용자는 Azure Portal 또는 Azure PowerShell cmdlet을 사용하여 Data Lake Storage Gen1을 찾아볼 수 없습니다. 사용자는 명령줄 도구만 사용할 수 있습니다. |
| 소유자 |모두 |모두 |소유자 역할은 superuser입니다. 이 역할은 모든 것을 관리할 수 있으며 데이터에 대한 완전한 액세스를 가집니다. |
| Reader |읽기 전용 |ACL에 의해 제어 |리더 역할은 계정 관리와 관련된 모든 항목(예: 어떤 사용자가 어떤 역할에 할당되는지)을 볼 수 있습니다. 리더 역할은 항목을 변경할 수 없습니다. |
| 참가자 |역할 추가 및 제거를 제외한 모든 항목 |ACL에 의해 제어 |참여자 역할은 배포 및 경고 만들기 및 관리와 같은 계정의 일부 측면을 관리할 수 있습니다. 참여자 역할은 역할을 추가 또는 제거할 수 없습니다. |
| 사용자 액세스 관리자 |역할 추가 및 제거 |ACL에 의해 제어 |사용자 액세스 관리자 역할은 계정에 대한 사용자 액세스를 관리할 수 있습니다. |

지침은 [사용자 또는 보안 그룹을 Data Lake Storage Gen1 계정에 할당](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts)을 참조하세요.

### <a name="using-acls-for-operations-on-file-systems"></a>파일 시스템 작업에 ACL 사용

Data Lake Storage Gen1은 HDFS(Hadoop 분산 파일 시스템)와 같은 계층적 파일 시스템이며 [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)을 지원합니다. 소유자 역할, 소유자 그룹 및 다른 사용자 및 그룹의 리소스에 대한 읽기(r), 쓰기(w) 및 실행(x) 권한을 제어합니다. Data Lake Storage Gen1에서 ACL은 루트 폴더, 하위 폴더 및 개별 파일에서도 사용할 수 있습니다. Data Lake Storage Gen1의 컨텍스트에서 ACL 작동 방식에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어](data-lake-store-access-control.md)를 참조하세요.

[보안 그룹](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)을 사용하여 여러 사용자에 대한 ACL을 정의하는 것이 좋습니다. 사용자를 보안 그룹에 추가한 다음 파일 또는 폴더에 대한 ACL을 해당 보안 그룹에 할당합니다. 할당된 권한에 대해 최대 28개 항목으로 제한하기 때문에 할당된 사용 권한을 제공하려는 경우에 유용합니다. Azure Active Directory 보안 그룹을 사용하여 Data Lake Storage Gen1에 저장된 데이터의 보안을 강화하는 방법에 대한 자세한 내용은 [ACL인 사용자 또는 보안 그룹을 Azure Data Lake Storage Gen1 파일 시스템에 할당](data-lake-store-secure-data.md#filepermissions)을 참조하세요.

![액세스 권한 나열](./media/data-lake-store-security-overview/adl.acl.2.png "액세스 권한 나열")

## <a name="network-isolation"></a>네트워크 격리

네트워크 수준에서 데이터 저장소에 대한 액세스를 제어하려면 Data Lake Storage Gen1을 사용합니다. 방화벽을 설정하고 신뢰할 수 있는 클라이언트에 대한 IP 주소 범위를 정의할 수 있습니다. IP 주소 범위와 함께 정의된 범위 내에서 IP 주소를 가지고 있는 클라이언트만 Data Lake Storage Gen1에 연결할 수 있습니다.

![방화벽 설정 및 IP 액세스](./media/data-lake-store-security-overview/firewall-ip-access.png "방화벽 설정 및 IP 주소")

Azure VNet (가상 네트워크)은 Data Lake Gen 1에 대 한 서비스 태그를 지원 합니다. 서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다. 자세한 내용은 [Azure 서비스 태그 개요](../virtual-network/service-tags-overview.md)를 참조 하세요.

## <a name="data-protection"></a>데이터 보호

Data Lake Storage Gen1은 수명 주기 전체에 걸쳐 데이터를 보호합니다. 전송 중인 데이터의 경우 Data Lake Storage Gen1은 업계 표준 TLS(전송 계층 보안) 1.2 프로토콜을 사용하여 네트워크에서 데이터를 보호합니다.

![Data Lake Storage Gen1 암호화](./media/data-lake-store-security-overview/adls-encryption.png "Data Lake Storage Gen1 암호화")

또한 Data Lake Storage Gen1은 계정에 저장된 데이터에 대한 암호화를 제공합니다. 암호화된 데이터 또는 암호화 없음을 선택할 수 있습니다. 암호화를 선택하면 Data Lake Storage Gen1에 저장된 데이터가 영구 미디어에 저장되기 전에 암호화됩니다. 이 경우 Data Lake Storage Gen1은 자동으로 데이터를 영구 저장하기 전에 데이터를 암호화하고, 검색하기 전에 데이터를 해독하므로 데이터에 액세스하는 클라이언트는 완전히 투명합니다. 데이터를 암호화/해독하기 위해 클라이언트 쪽에서 코드를 변경할 필요가 없습니다.

키 관리의 경우 Data Lake Storage Gen1은 Data Lake Storage Gen1에 저장된 모든 데이터의 암호를 해독하는 데 필요한 MEK(마스터 암호화 키)를 관리하는 두 가지 모드를 제공합니다. Data Lake Storage Gen1에서 MEK를 관리하도록 하거나 Azure Key Vault 계정을 사용하여 MEK의 소유권을 유지하도록 선택할 수 있습니다. Data Lake Storage Gen1 계정을 만들면서 키 관리 모드를 지정합니다. 암호화 관련 구성을 제공하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="activity-and-diagnostic-logs"></a>활동 및 진단 로그

계정 관리 관련 활동 또는 데이터 관련 활동에 대한 로그를 찾는지에 따라 활동 로그 또는 진단 로그를 사용할 수 있습니다.

* 계정 관리 관련 작업에서는 Azure Resource Manager API를 사용하며 활동 로그를 통해 Azure Portal에 표시됩니다.
* 데이터 관련 활동에서는 WebHDFS REST API를 사용하고 진단 로그를 통해 Azure Portal에 표시됩니다.

### <a name="activity-log"></a>활동 로그

규정을 준수하기 위해 특정 인시던트를 자세히 살펴보아야 하는 경우 조직에는 계정 관리 활동의 적절한 감사 내역이 필요할 수 있습니다. Data Lake Storage Gen1에는 기본 제공 모니터링이 포함되며 모든 계정 관리 활동을 기록합니다.

계정 관리 감사 내역의 경우 기록하려는 열을 보고 선택합니다. Azure Storage에 활동 로그를 내보낼 수도 있습니다.

![활동 로그](./media/data-lake-store-security-overview/activity-logs.png "활동 로그")

활동 로그로 작업하는 방법에 대한 자세한 내용은 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.

### <a name="diagnostics-logs"></a>진단 로그

Azure Portal에서 데이터 액세스 감사 및 진단 로깅을 사용 하도록 설정 하 고 Azure Blob storage 계정, 이벤트 허브 또는 Azure Monitor 로그에 로그를 보낼 수 있습니다.

![진단 로그](./media/data-lake-store-security-overview/diagnostic-logs.png "진단 로그")

Data Lake Storage Gen1과 함께 진단 로그를 사용하는 방법에 대한 자세한 내용은 [Data Lake Storage Gen1에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)를 참조하세요.

## <a name="summary"></a>요약

엔터프라이즈 고객은 안전하고 사용하기 간편한 데이터 분석 클라우드 플랫폼을 요구합니다. Data Lake Storage Gen1은 Azure Active Directory 통합, ACL 기반 권한 부여, 네트워크 격리, 전송 중 및 미사용 데이터 암호화 및 감사를 통해 ID 관리 및 인증에서 이러한 요구 사항을 해결하도록 설계되었습니다.

Data Lake Storage Gen1의 새로운 기능을 참조하려는 경우 [Data Lake Storage Gen1 UserVoice 포럼](https://feedback.azure.com/forums/327234-data-lake)에 사용자 의견을 보내주세요.

## <a name="see-also"></a>참조

* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
* [Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
