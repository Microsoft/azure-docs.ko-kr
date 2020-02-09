---
title: Azure Active Directory 액세스 구성-Azure Blockchain 서비스
description: Azure Active Directory 액세스를 사용 하 여 Azure Blockchain 서비스를 구성 하는 방법
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455856"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Azure Blockchain 서비스에 대 한 Azure Active Directory 액세스를 구성 하는 방법

이 문서에서는 Azure Active Directory (Azure AD) 사용자, 그룹 또는 응용 프로그램 Id를 사용 하 여 액세스 권한을 부여 하 고 Azure Blockchain 서비스 노드에 연결 하는 방법에 대해 알아봅니다.

Azure AD는 클라우드 기반 id 관리 기능을 제공 하며 전체 엔터프라이즈에서 단일 id를 사용 하 고 Azure에서 응용 프로그램에 액세스할 수 있습니다. Azure Blockchain 서비스는 Azure AD와 통합 되며 ID 페더레이션, Single Sign-On 및 multi-factor authentication과 같은 혜택을 제공 합니다.

## <a name="prerequisites"></a>선행 조건

* [Azure Portal를 사용 하 여 블록 체인 구성원 만들기](create-member.md)

## <a name="grant-access"></a>액세스 권한 부여

멤버 수준과 노드 수준 모두에서 액세스 권한을 부여할 수 있습니다. 멤버 수준에서 액세스 권한을 부여 하면 해당 멤버 아래의 모든 노드에 대 한 액세스 권한을 부여 합니다.

### <a name="grant-member-level-access"></a>멤버 수준 액세스 권한 부여

멤버 수준에서 액세스 권한을 부여 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **액세스 제어 (IAM) > 추가 하 > 역할 할당**추가로 이동 합니다.
1. **Blockchain Member Node access (Preview)** 역할을 선택 하 고 액세스 권한을 부여 하려는 AZURE AD ID 개체를 추가 합니다. Azure AD ID 개체는 다음과 같을 수 있습니다.

    | Azure AD 개체 | 예 |
    |-----------------|---------|
    | Azure AD 사용자   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 그룹  | `sales@contoso.onmicrosoft.com` |
    | 애플리케이션 UI  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![역할 할당 추가](./media/configure-aad/add-role-assignment.png)

1. **저장**을 선택합니다.

### <a name="grant-node-level-access"></a>노드 수준 액세스 권한 부여

노드 보안으로 이동 하 여 액세스 권한을 부여 하려는 노드 이름을 클릭 하 여 노드 수준에서 액세스 권한을 부여할 수 있습니다.

Blockchain Member Node Access (Preview) 역할을 선택 하 고 액세스 권한을 부여 하려는 Azure AD ID 개체를 추가 합니다.

자세한 내용은 [Azure Blockchain 서비스 트랜잭션 노드 구성](configure-transaction-nodes.md#azure-active-directory-access-control)을 참조 하세요.

## <a name="connect-using-azure-blockchain-connector"></a>Azure Blockchain 커넥터를 사용 하 여 연결

[GitHub에서 Azure Blockchain 커넥터](https://github.com/Microsoft/azure-blockchain-connector/)를 다운로드 하거나 복제 합니다.

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

**추가 정보** 의 빠른 시작 섹션에 따라 소스 코드에서 커넥터를 빌드합니다.

### <a name="connect-using-an-azure-ad-user-account"></a>Azure AD 사용자 계정을 사용 하 여 연결

1. 다음 명령을 실행 하 여 Azure AD 사용자 계정을 사용 하 여 인증 합니다. \<myAADDirectory\>를 Azure AD 도메인으로 바꿉니다. 예: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD에서 자격 증명을 묻는 메시지를 표시 합니다.
1. 사용자 이름 및 암호를 사용 하 여 로그인 합니다.
1. 인증에 성공 하면 로컬 프록시가 blockchain 노드에 연결 됩니다. 이제 지역 엔드포인트을 사용 하 여 Geth 클라이언트를 연결할 수 있습니다.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>응용 프로그램 ID를 사용 하 여 연결

많은 응용 프로그램은 Azure ad 사용자 계정 대신 응용 프로그램 ID를 사용 하 여 Azure AD로 인증 합니다.

응용 프로그램 ID를 사용 하 여 노드에 연결 하려면 **aadauthcode** 을 **aadclient**로 바꿉니다.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 매개 변수 | 설명 |
|-----------|-------------|
| 테넌트-id | 예를 들어 Azure AD 도메인 `yourdomain.onmicrosoft.com`
| 클라이언트 id | Azure AD에서 등록 된 응용 프로그램의 클라이언트 ID
| 클라이언트-비밀 | Azure AD에서 등록 된 응용 프로그램의 클라이언트 암호

Azure AD에서 응용 프로그램을 등록 하는 방법에 대 한 자세한 내용은 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 를 참조 하세요.

### <a name="connect-a-mobile-device-or-text-browser"></a>모바일 장치 또는 텍스트 브라우저 연결

Azure AD 인증 팝업 디스플레이가 가능 하지 않은 모바일 장치 또는 텍스트 기반 브라우저의 경우 Azure AD는 일회성 암호를 생성 합니다. 암호를 복사 하 고 다른 환경에서 Azure AD 인증을 진행할 수 있습니다.

암호를 생성 하려면 **aadauthcode** 를 **aaddevice**로 바꿉니다. \<myAADDirectory\>를 Azure AD 도메인으로 바꿉니다. 예: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>다음 단계

Azure Blockchain 서비스의 데이터 보안에 대 한 자세한 내용은 [Azure Blockchain 서비스 보안](data-security.md)을 참조 하세요.