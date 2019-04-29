---
title: Azure Key Vault 개발자 가이드
description: 개발자는 Microsoft Azure 환경 내에서 Azure Key Vault를 사용하여 암호화 키를 관리할 수 있습니다.
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 06fd66c3fb9e90e0198d40253f3c554fe8596c3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640806"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 개발자 가이드

Key Vault를 사용하면 애플리케이션 내에서 중요한 정보를 안전하게 액세스할 수 있습니다.

- 키와 암호는 코드 작성 없이 보호되며 애플리케이션에서 쉽게 사용할 수 있습니다.
- 고객들에게 고유한 키를 부여하여 관리하게 하므로 핵심 소프트웨어 기능을 제공하는 데 집중할 수 있습니다 이러한 이유로 애플리케이션에는 고객의 테넌트 키와 암호에 대한 책임 또는 잠재적인 법적 책임이 없습니다.
- 애플리케이션은 서명 및 암호화를 위해 키를 사용할 수 있지만, 애플리케이션 외부에서 키 관리를 유지할 수 있으므로 지리적으로 분산된 앱에 적합한 솔루션을 구현할 수 있습니다.
- Key Vault의 2016년 9월 릴리스를 통해 애플리케이션에서 Key Vault 인증서를 관리할 수 있게 되었습니다. 자세한 내용은 [키, 암호 및 인증서 정보](/rest/api/keyvault/about-keys--secrets-and-certificates)를 참조하세요.

Azure Key Vault에 대한 일반적인 내용은 [키 자격 증명 모음이란?](key-vault-whatis.md)을 참조하세요.

## <a name="public-previews"></a>공개 미리 보기

새로운 Key Vault 기능의 공개 미리 보기가 정기적으로 릴리스됩니다. 이러한 공개 미리 보기를 사용해보고, 피드백 메일 주소인 azurekeyvault@microsoft.com을 통해 의견을 알려주세요.

### <a name="storage-account-keys---july-10-2017"></a>Storage 계정 키 - 2017년 7월 10일

>[!NOTE]
>이 Azure Key Vault 업데이트에서는 **Storage 계정 키** 기능만 미리 보기 상태입니다.

이 미리 보기에는 [.NET/C#](/dotnet/api/microsoft.azure.keyvault/), [REST](/rest/api/keyvault/) 및 [PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) 인터페이스를 통해 사용할 수 있는 새로운 Storage 계정 키가 포함되어 있습니다. 

새 Storage 계정 키 기능에 대한 자세한 내용은 [Azure Key Vault Storage 계정 키 개요](key-vault-ovw-storage-keys.md)를 참조하세요.

## <a name="videos"></a>동영상

이 비디오에서는 사용자 고유의 키 자격 증명 모음을 만드는 방법과 'Hello Key Vault' 샘플 애플리케이션에서 이를 사용하는 방법을 보여 줍니다.

- [Key Vault 개발자 - 빠른 시작 가이드](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

위 비디오에 언급된 리소스:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault 샘플 코드](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>주요 자격 증명 모음 만들기 및 관리

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. Azure 리소스에 대한 관리 ID를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공하여 이 문제를 더 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

Azure 리소스의 관리 ID에 대한 자세한 내용은 [관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요. AAD 사용에 대한 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/active-directory-integrating-applications.md)을 참조하세요.

Key Vault에서 키, 암호 또는 인증서를 사용하기 전에 다음 문서에 설명된 대로 CLI, PowerShell, Resource Manager 템플릿 또는 REST를 통해 Key Vault를 만들고 관리합니다.

- [CLI를 사용하여 Key Vault 만들기 및 관리](key-vault-manage-with-cli2.md)
- [PowerShell을 사용하여 Key Vault 만들기 및 관리](key-vault-overview.md)
- [Azure Resource Manager 템플릿을 통한 Key Vault 만들기 및 암호 추가](../azure-resource-manager/resource-manager-template-keyvault.md)
- [REST를 사용하여 Key Vault 만들기 및 관리](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>주요 자격 증명 모음을 사용한 코딩

프로그래머를 위한 Key Vault 관리 시스템은 인터페이스로 구성됩니다. 이 섹션에는 모든 언어에 대한 링크와 일부 코드 예제가 포함되어 있습니다. 

### <a name="supported-programming-and-scripting-languages"></a>지원되는 프로그래밍 및 스크립팅 언어

#### <a name="rest"></a>REST (영문)

REST 인터페이스를 통해 자격 증명 모음, 키, 암호 등을 비롯한 모든 Key Vault 리소스에 액세스할 수 있습니다. 

[Key Vault REST API 참조](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Key Vault에 대한 .NET API 참조](/dotnet/api/microsoft.azure.keyvault).

.NET SDK의 2.x 버전에 대한 자세한 내용은 [릴리스 정보](key-vault-dotnet2api-release-notes.md)를 참조하세요.

#### <a name="java"></a>자바

[Key Vault용 Java SDK](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Node.js에서 Key Vault 관리 API와 Key Vault 개체 API는 별개입니다. 다음 개요 문서에서는 둘 다에 액세스할 수 있습니다. 

[Node.js용 Azure Key Vault 모듈](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Python용 Azure Key Vault 라이브러리](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Key Vault용 Azure CLI](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Key Vault용 Azure PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>빠른 시작 가이드

- [주요 자격 증명 모음 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Node.js에서 Key Vault 시작](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>코드 예제

애플리케이션에서 Key Vault를 사용하는 전체 예제는 다음을 참조하세요.

- [Azure Key Vault 코드 샘플](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault에 대한 코드 샘플입니다. 
- [웹 애플리케이션에서 Azure Key Vault 사용](quick-create-net.md) - Azure의 웹 애플리케이션에서 Azure Key Vault를 사용하는 방법을 제공하는 자습서입니다. 

## <a name="how-tos"></a>방법

다음 문서 및 시나리오에서는 Azure Key Vault 사용에 대한 작업별 지침을 제공합니다.

- [구독 이동 후 Key Vault 테넌트 변경](key-vault-subscription-move-fix.md) - Azure 구독을 테넌트 A에서 테넌트 B로 이동할 때 기존 주요 자격 증명 모음에는 테넌트 B의 주체(사용자 및 애플리케이션)가 액세스할 수 없게 됩니다. 이 가이드를 통해 이 문제를 해결합니다.
- [방화벽 뒤 Key Vault 액세스](key-vault-access-behind-firewall.md) - 주요 자격 증명 모음에 액세스하려면 주요 자격 증명 모음 클라이언트 애플리케이션은 다양한 기능에 대한 여러 엔드포인트에 액세스할 수 있어야 합니다.
- [Azure Key Vault용으로 HSM 보호 키를 생성하여 전송하는 방법](key-vault-hsm-protected-keys.md) - 이 문서를 통해 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다.
- [배포하는 동안 보안 값(예: 암호)을 전달하는 방법](../azure-resource-manager/resource-manager-keyvault-parameter.md) - 배포하는 동안 보안 값(예: 암호)을 매개 변수로 전달해야 하는 경우 이 값을 Azure Key Vault에 암호로 저장하고 다른 리소스 관리자 템플릿에서 이 값을 참조할 수 있습니다.
- [확장 가능 키 관리를 위해 SQL Server에서 키 자격 증명을 사용하는 방법](https://msdn.microsoft.com/library/dn198405.aspx) - Azure Key Vault용 SQL Server 커넥터를 사용하면 SQL Server 및 SQL-in-a-VM에서 Azure Key Vault를 EKM(확장 가능 키 관리) 공급자로 활용하여 애플리케이션 링크에 대한 암호화 키를 보호할 수 있습니다(투명한 데이터 암호화, 백업 암호화 및 열 수준 암호화).
- [주요 자격 증명 모음에서 VM에 인증서를 배포하는 방법](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Azure의 VM에서 실행 중인 클라우드 애플리케이션에는 인증서가 필요합니다. 지금 이 VM으로 인증서를 가져오려면 어떻게 하나요?
- [종단 간 키 회전 및 감사를 사용하여 Key Vault를 설정하는 방법](key-vault-key-rotation-log-monitoring.md) - Azure Key Vault를 사용하는 키 회전 및 감사를 설정하는 방법을 안내합니다.
- [Key Vault를 통한 Azure Web App Certificate 배포]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)는 [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) 제품의 일부로 Key Vault에 저장된 인증서를 배포하기 위한 단계별 지침을 제공합니다.
- [여러 애플리케이션에 Key Vault 액세스 권한 부여](key-vault-group-permissions-for-apps.md) Key Vault 액세스 제어 정책은 최대 1,024개의 항목을 지원합니다. 그러나 Azure Active Directory 보안 그룹을 만들 수 있습니다. 관련된 모든 서비스 주체를 이 보안 그룹에 추가한 다음 Key Vault에 이 보안 그룹에 대한 액세스를 허용합니다.
- Azure에서 Key Vault를 통합 및 사용하는 방법에 대한 작업별 지침은 [Key Vault에 대한 Ryan Jones Azure Resource Manager 템플릿 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.
- [CLI로 Key Vault 일시 삭제를 사용하는 방법](key-vault-soft-delete-cli.md)에서는 활성화된 일시 삭제를 사용하는 Key Vault 및 다양한 Key Vault 개체의 사용 및 수명 주기에 대해 설명합니다.
- [PowerShell로 Key Vault 일시 삭제를 사용하는 방법](key-vault-soft-delete-powershell.md)에서는 활성화된 일시 삭제를 사용하는 Key Vault 및 다양한 Key Vault 개체의 사용 및 수명 주기에 대해 설명합니다.

## <a name="integrated-with-key-vault"></a>주요 자격 증명 모음과 통합됨

다음 문서에서는 사용하거나 Key Vault와 통합하는 다른 시나리오 및 서비스에 대한 정보를 다룹니다.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md)은 업계 표준인 Windows의 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 함께 통합되어 주요 자격 증명 모음 구독에서 디스크 암호화 키 및 암호를 제어하고 관리할 수 있도록 하며 가상 머신 디스크의 모든 휴지 상태 데이터가 Azure 저장소에서 암호화되도록 보장합니다.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)는 계정에 저장된 데이터의 암호화에 대한 옵션을 제공합니다. 키 관리의 경우 Data Lake Store는 Data Lake Store에 저장된 모든 데이터의 암호를 해독하는 데 필요한 MEK(마스터 암호화 키)를 관리하는 두 가지 모드를 제공합니다. Data Lake Store에서 MEK를 관리하도록 하거나 Azure Key Vault 계정을 사용하여 MEK의 소유권을 유지하도록 선택할 수 있습니다. Data Lake Store 계정을 만들면서 키 관리 모드를 지정합니다.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key)을 통해 테넌트 키를 직접 관리할 수 있습니다. 예를 들어, Microsoft가 테넌트 키를 관리하는 대신(기본값) 테넌트 키를 직접 관리하여 해당 조직에 적용되는 특정 규정을 준수할 수 있습니다. 테넌트 키 직접 관리는 BYOK(Bring Your Own Key)라고 하기도 합니다.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 개요 및 개념

- [Key Vault 일시 삭제 동작](key-vault-ovw-soft-delete.md)에서는 삭제가 실수인지, 의도적인 것인지에 관계없이 삭제된 개체를 복구할 수 있는 기능에 대해 설명합니다.
- [Key Vault 클라이언트 제한](key-vault-ovw-throttling.md)에서는 제한의 기본 개념을 소개하고 앱에 맞는 접근 방식을 제공합니다.
- [Key Vault 스토리지 계정 키 개요](key-vault-ovw-storage-keys.md)에서는 Key Vault 통합 Azure Storage 계정 키에 대해 설명합니다.
- [Key Vault 보안 권역](key-vault-ovw-security-worlds.md)에서는 지역과 보안 영역 간의 관계를 설명합니다.

## <a name="social"></a>사회적

- [키 자격 증명 모음 블로그](https://aka.ms/kvblog)
- [키 자격 증명 모음 포럼](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>라이브러리 지원

- [Microsoft Azure Key Vault 핵심 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)는 식별자의 키를 찾고 키로 작업을 수행하기 위한 **IKey** 및 **IKeyResolver** 인터페이스를 제공합니다.
- [Microsoft Azure Key Vault 확장](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions)은 Azure Key Vault에 확장된 기능을 제공합니다.
