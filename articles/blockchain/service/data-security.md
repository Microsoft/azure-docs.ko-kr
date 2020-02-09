---
title: Azure Blockchain 서비스 보안
description: Azure Blockchain 서비스 데이터 액세스 및 보안 개념
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982236"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain 서비스 보안

Azure Blockchain Service는 여러 가지 Azure 기능을 사용하여 데이터를 안전하고 사용 가능하도록 유지합니다. 데이터는 격리, 암호화 및 인증을 사용하여 보안이 유지됩니다.

## <a name="isolation"></a>격리

Azure Blockchain 서비스 리소스는 개인 가상 네트워크에서 격리 됩니다. 각 트랜잭션 및 유효성 검사 노드는 VM (가상 머신)입니다. 한 가상 네트워크의 Vm은 다른 가상 네트워크의 Vm과 직접 통신할 수 없습니다. 격리는 가상 네트워크 내에서 통신이 개인 상태로 유지 되도록 합니다. Azure 가상 네트워크 격리에 대 한 자세한 내용은 [Azure 공용 클라우드에서 격리](../../security/fundamentals/isolation-choices.md#networking-isolation)를 참조 하세요.

![VNET 다이어그램](./media/data-security/vnet.png)

## <a name="encryption"></a>암호화

사용자 데이터는 Azure storage에 저장 됩니다. 사용자 데이터는 보안 및 기밀성을 위해 동작 및 미사용에서 암호화 됩니다. 자세한 내용은 [Azure Storage 보안 가이드](../../storage/blobs/security-recommendations.md)를 참조 하세요.

## <a name="authentication"></a>인증

트랜잭션은 RPC 엔드포인트을 통해 blockchain 노드로 전송 될 수 있습니다. 클라이언트는 사용자 인증을 처리 하 고 SSL을 통해 데이터를 암호화 하는 역방향 프록시 서버를 사용 하 여 트랜잭션 노드와 통신 합니다.

![인증 다이어그램](./media/data-security/authentication.png)

RPC 액세스에는 세 가지 인증 모드가 있습니다.

### <a name="basic-authentication"></a>기본 인증

기본 인증에서는 사용자 이름 및 암호를 포함 하는 HTTP 인증 헤더를 사용 합니다. 사용자 이름은 blockchain 노드의 이름입니다. 멤버가 나 노드를 프로 비전 하는 동안 암호를 설정 합니다. Azure Portal 또는 CLI를 사용 하 여 암호를 변경할 수 있습니다.

### <a name="access-keys"></a>액세스 키

액세스 키는 엔드포인트 URL에 포함 된 임의로 생성 된 문자열을 사용 합니다. 키 회전을 사용 하는 데 도움이 되는 두 가지 선택키가 있습니다. Azure Portal 및 CLI에서 키를 다시 생성할 수 있습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure ad (Azure Active Directory)는 azure ad 사용자 자격 증명을 사용 하 여 Azure AD에서 사용자를 인증 하는 클레임 기반 인증 메커니즘을 사용 합니다. Azure AD는 클라우드 기반 id 관리를 제공 하며, 고객이 전체 기업에서 단일 id를 사용 하 고 클라우드의 응용 프로그램에 액세스할 수 있도록 합니다. Azure Blockchain 서비스는 ID 페더레이션, Single Sign-On 및 multi-factor authentication을 사용 하도록 설정 하는 Azure AD와 통합 됩니다. 사용자, 그룹 및 응용 프로그램 역할을 조직에서 blockchain 구성원 및 노드 액세스에 할당할 수 있습니다.

Azure AD 클라이언트 프록시는 [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)에서 사용할 수 있습니다. 클라이언트 프록시는 사용자를 Azure AD 로그인 페이지로 안내 하 고 인증에 성공 하면 전달자 토큰을 가져옵니다. 그런 다음 사용자는 Geth 또는 Truffle와 같은 Ethereum 클라이언트 응용 프로그램을 클라이언트 프록시의 엔드포인트에 연결 합니다. 마지막으로 트랜잭션이 제출 되 면 클라이언트 프록시는 http 헤더에 전달자 토큰을 삽입 하 고 역방향 프록시는 OAuth 프로토콜을 사용 하 여 토큰의 유효성을 검사 합니다.

## <a name="keys-and-ethereum-accounts"></a>키 및 Ethereum 계정

Azure Blockchain 서비스 멤버를 프로 비전 할 때 Ethereum 계정 및 공개 키와 개인 키 쌍이 생성 됩니다. 개인 키를 사용 하 여 블록 체인으로 트랜잭션을 보냅니다. Ethereum 계정은 공개 키 해시의 마지막 20 바이트입니다. Ethereum 계정을 작은 이름이 라고도 합니다.

개인 및 공개 키 쌍은 JSON 형식의 keyfile로 저장 됩니다. 개인 키는 blockchain 원장 서비스를 만들 때 입력 한 암호를 사용 하 여 암호화 됩니다.

개인 키는 트랜잭션에 디지털 서명 하는 데 사용 됩니다. 개인 블록 체인에서 개인 키로 서명 된 스마트 계약은 서명자의 id를 나타냅니다. 서명의 유효성을 확인 하기 위해 수신기는 서명자의 공개 키를 서명에서 계산 된 주소와 비교할 수 있습니다.

Constellation 키는 쿼럼 노드를 고유 하 게 식별 하는 데 사용 됩니다. Constellation 키는 노드 프로 비전 시에 생성 되 고 쿼럼에서 전용 트랜잭션의 privateFor 매개 변수에 지정 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Blockchain 서비스에 대 한 Azure Active Directory 액세스를 구성 하는 방법을](configure-aad.md)참조 하세요.
