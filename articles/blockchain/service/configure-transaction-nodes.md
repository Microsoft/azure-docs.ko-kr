---
title: Azure Blockchain 서비스 트랜잭션 노드 구성
description: Azure Blockchain 서비스 트랜잭션 노드를 구성 하는 방법
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326315"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure Blockchain 서비스 트랜잭션 노드 구성

트랜잭션 노드는 공용 엔드포인트을 통해 Azure Blockchain 서비스에 blockchain 트랜잭션을 보내는 데 사용 됩니다. 기본 트랜잭션 노드에는 블록 체인에 등록 된 Ethereum 계정의 개인 키가 포함 되어 있으므로 삭제할 수 없습니다.

기본 트랜잭션 노드 세부 정보를 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain Service 멤버로 이동합니다. **트랜잭션 노드**를 선택 합니다.

    ![기본 트랜잭션 노드 선택](./media/configure-transaction-nodes/nodes.png)

    개요 정보에는 공용 엔드포인트 주소와 공개 키가 포함 됩니다.

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

총 10 개의 트랜잭션 노드를 위해 blockchain 구성원에 최대 9 개의 추가 트랜잭션 노드를 추가할 수 있습니다. 트랜잭션 노드를 추가 하 여 확장성을 늘리거나 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 응용 프로그램에 대 한 트랜잭션 노드 엔드포인트이 있을 수 있습니다.

트랜잭션 노드를 추가 하려면:

1. Azure Portal에서 Azure Blockchain 서비스 구성원으로 이동 하 고 **트랜잭션 노드 > 추가**를 선택 합니다.
1. 새 트랜잭션 노드에 대 한 설정을 완료 합니다.

    ![트랜잭션 노드 추가](./media/configure-transaction-nodes/add-node.png)

    | 설정 | 설명 |
    |---------|-------------|
    | 이름 | 트랜잭션 노드 이름입니다. 트랜잭션 노드 엔드포인트에 대한 DNS 주소를 만드는 데 사용됩니다. 예: `newnode-myblockchainmember.blockchain.azure.com`. 노드 이름을 만든 후에는 변경할 수 없습니다. |
    | 암호 | 강력한 암호를 설정 합니다. 기본 인증을 사용 하 여 트랜잭션 노드 엔드포인트에 액세스 하려면 암호를 사용 합니다.

1. **만들기**를 선택합니다.

    새 트랜잭션 노드를 프로비저닝하는 데 약 10분이 걸립니다. 추가 트랜잭션 노드는 비용을 발생 시킵니다. 비용에 대 한 자세한 내용은 [Azure 가격 책정](https://aka.ms/ABSPricing)을 참조 하세요.

## <a name="endpoints"></a>엔드포인트

트랜잭션 노드에는 고유한 DNS 이름 및 공용 엔드포인트이 있습니다.

트랜잭션 노드의 엔드포인트 세부 정보를 보려면 다음을 수행 합니다.

1. Azure Portal에서 Azure Blockchain 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 **개요**를 선택 합니다.

    ![엔드포인트](./media/configure-transaction-nodes/endpoints.png)

트랜잭션 노드 엔드포인트은 안전 하며 인증을 요구 합니다. Azure AD 인증, HTTPS 기본 인증을 사용 하 고 HTTPS 또는 SSL을 통해 Websocket을 통해 액세스 키를 사용 하 여 트랜잭션 엔드포인트에 연결할 수 있습니다.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 액세스 제어

Azure Blockchain 서비스 트랜잭션 노드 엔드포인트은 Azure AD (Azure Active Directory) 인증을 지원 합니다. Azure AD 사용자, 그룹 및 서비스 사용자에 게 엔드포인트에 대 한 액세스 권한을 부여할 수 있습니다.

엔드포인트에 Azure AD 액세스 제어를 부여 하려면:

1. Azure Portal에서 Azure Blockchain 서비스 구성원으로 이동한 후 **트랜잭션 노드 > 액세스 제어 (IAM)를 선택 하 > 역할 할당 추가 > 추가**합니다.
1. 사용자, 그룹 또는 서비스 사용자 (응용 프로그램 역할)에 대 한 새 역할 할당을 만듭니다.

    ![IAM 역할 추가](./media/configure-transaction-nodes/add-role.png)

    | 설정 | 작업 |
    |---------|-------------|
    | 역할 | **소유자**, **참가자**또는 **읽기 권한자**를 선택 합니다.
    | 다음에 대한 액세스 할당 | **AZURE AD 사용자, 그룹 또는 서비스 주체**를 선택 합니다.
    | 선택 | 추가 하려는 사용자, 그룹 또는 서비스 주체를 검색 합니다.

1. **저장** 을 선택 하 여 역할 할당을 추가 합니다.

Azure AD access control에 대 한 자세한 내용은 [RBAC를 사용 하 여 azure 리소스에 대 한 액세스 관리 및 Azure Portal](../../role-based-access-control/role-assignments-portal.md) 을 참조 하세요.

Azure AD 인증을 사용 하 여 연결 하는 방법에 대 한 자세한 내용은 [AAD 인증을 사용 하 여 노드에 연결](configure-aad.md)을 참조 하세요.

### <a name="basic-authentication"></a>기본 인증

HTTPS 기본 인증의 경우 사용자 이름 및 암호 자격 증명은 요청의 HTTPS 헤더에서 엔드포인트으로 전달 됩니다.

Azure Portal에서 트랜잭션 노드의 기본 인증 엔드포인트 세부 정보를 볼 수 있습니다. Azure Blockchain 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 설정에서 **기본 인증** 을 선택 합니다.

![기본 인증](./media/configure-transaction-nodes/basic.png)

사용자 이름은 노드의 이름이 며 변경할 수 없습니다.

URL을 사용 하려면 \<password\>를 노드가 프로 비전 될 때 설정 된 암호로 바꿉니다. **암호 재설정**을 선택 하 여 암호를 업데이트할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키 인증의 경우 액세스 키가 엔드포인트 URL에 포함 됩니다. 트랜잭션 노드가 프로 비전 되 면 두 개의 액세스 키가 생성 됩니다. 액세스 키를 인증에 사용할 수 있습니다. 두 키를 사용 하 여 키를 변경 하 고 회전할 수 있습니다.

트랜잭션 노드의 액세스 키 정보를 확인 하 고 액세스 키를 포함 하는 엔드포인트 주소를 복사할 수 있습니다. Azure Blockchain 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 설정에서 **액세스 키** 를 선택 합니다.

### <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙을 사용 하면 트랜잭션 노드에 인증을 시도할 수 있는 IP 주소를 제한할 수 있습니다.  트랜잭션 노드에 대해 구성 된 방화벽 규칙이 없는 경우 모든 파티에서 액세스할 수 없습니다.  

트랜잭션 노드의 방화벽 규칙을 보려면 Azure Blockchain 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 설정에서 **방화벽 규칙** 을 선택 합니다.

**방화벽** 규칙 표에서 규칙 이름, 시작 ip 주소 및 끝 ip 주소를 입력 하 여 방화벽 규칙을 추가할 수 있습니다.

![방화벽 규칙](./media/configure-transaction-nodes/firewall-rules.png)

사용 하도록 설정 하려면

* **단일 IP 주소:** 시작 IP 주소와 끝 IP 주소에 대해 동일한 IP 주소를 구성 합니다.
* **IP 주소 범위:** 시작 IP 주소와 끝 IP 주소 범위를 구성 합니다. 예를 들어 10.221.34.0에서 시작 하 여 10.221.34.255에서 끝나는 범위는 전체 10.221.34.xxx 서브넷을 사용 하도록 설정 합니다.
* **모든 IP 주소 허용:** 시작 IP 주소를 0.0.0.0으로 구성 하 고 끝 IP 주소를 255.255.255.255로 구성 합니다.

## <a name="connection-strings"></a>연결 문자열

기본 인증 또는 액세스 키 사용을 위해 트랜잭션 노드에 대 한 연결 문자열 구문이 제공 됩니다. HTTPS 및 Websocket을 통한 액세스 키를 비롯 한 연결 문자열이 제공 됩니다.

트랜잭션 노드의 연결 문자열을 보고 엔드포인트 주소를 복사할 수 있습니다. Azure Blockchain 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 설정에서 **연결 문자열** 을 선택 합니다.

![연결 문자열](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>샘플 코드

Web3, Nethereum, Web3js 및 Truffle를 통해 트랜잭션 노드에 대 한 연결을 신속 하 게 설정 하기 위해 샘플 코드가 제공 됩니다.

트랜잭션 노드의 샘플 연결 코드를 보고 인기 있는 개발자 도구에서 사용할 수 있도록 복사할 수 있습니다. Azure Blockchain 서비스 구성원 트랜잭션 노드 중 하나로 이동 하 고 설정에서 **샘플 코드** 를 선택 합니다.

Web3, Nethereum, Truffle 또는 Web3j 탭을 선택 하 여 사용 하려는 코드 샘플을 확인 합니다.

![샘플 코드](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용 하 여 트랜잭션 노드 구성](manage-cli.md)
