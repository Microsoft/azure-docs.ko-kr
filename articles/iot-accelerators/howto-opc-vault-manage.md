---
title: OPC 자격 증명 모음 인증서 서비스를 관리 하는 방법-Azure | Microsoft Docs
description: OPC 자격 증명 모음 루트 CA 인증서 및 사용자 권한을 관리 합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203660"
---
# <a name="manage-the-opc-vault-certificate-service"></a>OPC 자격 증명 모음 인증서 서비스 관리

이 문서에서는 Azure의 OPC 자격 증명 모음 인증서 관리 서비스에 대 한 관리 작업을 설명 합니다. 발급자 CA 인증서를 갱신 하는 방법, CRL (인증서 해지 목록)을 갱신 하는 방법 및 사용자 액세스 권한을 부여 하 고 취소 하는 방법에 대 한 정보가 포함 되어 있습니다.

## <a name="create-or-renew-the-root-ca-certificate"></a>루트 CA 인증서를 만들거나 갱신 합니다.

OPC 자격 증명 모음을 배포한 후 루트 CA 인증서를 만들어야 합니다. 유효한 발급자 CA 인증서가 없으면 응용 프로그램 인증서에 서명 하거나 발급할 수 없습니다. [인증서를 사용](howto-opc-vault-secure-ca.md#certificates) 하 여 적절 하 고 안전한 수명으로 인증서를 관리 하세요. 수명의 절반 후 발급자 CA 인증서를 갱신 합니다. 갱신 시 새로 서명 된 응용 프로그램 인증서의 구성 된 수명이 발급자 CA 인증서의 수명을 초과 해서는 안 됩니다.
> [!IMPORTANT]
> 관리자 역할은 발급자 CA 인증서를 만들거나 갱신 하는 데 필요 합니다.

1. 에서 인증서 서비스 `https://myResourceGroup-app.azurewebsites.net`를 열고 로그인 합니다.
2. **인증서 그룹**으로 이동 합니다.
3. 나열 된 기본 인증서 그룹이 하나 있습니다. **편집**을 선택합니다.
4. **인증서 그룹 세부 정보 편집**에서 CA 및 응용 프로그램 인증서의 주체 이름 및 수명을 수정할 수 있습니다. 첫 번째 CA 인증서가 발급 되기 전에는 주체와 수명을 한 번만 설정 해야 합니다. 작업 중의 수명 변경으로 인해 발급 된 인증서와 Crl의 수명이 일치 하지 않을 수 있습니다.
5. 올바른 제목을 입력 합니다 (예: `CN=My CA Root, O=MyCompany, OU=MyDepartment`).<br>
   > [!IMPORTANT]
   > 주체를 변경 하는 경우 발급자 인증서를 갱신 해야 합니다. 그렇지 않으면 서비스에서 응용 프로그램 인증서에 서명 하지 못합니다. 활성 발급자 인증서의 주체에 대해 구성의 주체를 확인 합니다. 주체가 일치 하지 않으면 인증서 서명이 거부 됩니다.
6. **저장**을 선택합니다.
7. 이 시점에서 "사용할 수 없음" 오류가 발생 하는 경우 사용자 자격 증명에 새 루트 인증서를 수정 하거나 만들 수 있는 관리자 권한이 없습니다. 기본적으로 서비스를 배포한 사용자에 게는 서비스를 사용 하 여 관리자 및 서명 역할이 있습니다. 다른 사용자는 Azure Active Directory (Azure AD) 응용 프로그램 등록에 적절 한 승인자, 기록기 또는 관리자 역할에 추가 해야 합니다.
8. **세부 정보**를 선택 합니다. 업데이트 된 정보가 표시 됩니다.
9. **Ca 인증서 갱신** 을 선택 하 여 첫 번째 발급자 ca 인증서를 발급 하거나 발급자 인증서를 갱신 합니다. 그런 다음 **확인**을 선택합니다.
10. 몇 초 후에 **인증서 세부 정보**를 볼 수 있습니다. OPC UA 응용 프로그램에 배포할 최신 CA 인증서 및 CRL을 다운로드 하려면 **발급자** 또는 **crl**을 선택 합니다.

이제 opc UA 인증서 관리 서비스가 OPC UA 응용 프로그램에 대 한 인증서를 발급할 준비가 되었습니다.

## <a name="renew-the-crl"></a>CRL 갱신

CRL 갱신은 업데이트 이며, 정기적으로 응용 프로그램에 배포 해야 합니다. CRL 배포 지점 X509 확장을 지 원하는 OPC UA 장치는 마이크로 서비스 엔드포인트에서 CRL을 직접 업데이트할 수 있습니다. 다른 OPC UA 장치에는 수동 업데이트가 필요 하거나, GDS 서버 푸시 확장 (*)을 사용 하 여 인증서 및 Crl로 신뢰 목록을 업데이트 하 여 업데이트할 수 있습니다.

다음 워크플로에서 삭제 된 상태의 모든 인증서 요청은 발급 된 발급자 CA 인증서에 해당 하는 Crl에서 해지 됩니다. CRL의 버전 번호는 1 씩 증가 합니다. <br>
> [!NOTE]
> 발급 된 모든 Crl은 발급자 CA 인증서가 만료 될 때까지 유효 합니다. OPC UA 사양에는 CRL에 대 한 필수 결정적 배포 모델이 필요 하지 않기 때문입니다.

> [!IMPORTANT]
> 관리자 역할은 발급자 CRL을 갱신 하는 데 필요 합니다.

1. 에서 인증서 서비스 `https://myResourceGroup.azurewebsites.net`를 열고 로그인 합니다.
2. **인증서 그룹** 페이지로 이동 합니다.
3. **세부 정보**를 선택 합니다. 현재 인증서 및 CRL 정보를 표시 해야 합니다.
4. OPC 자격 증명 모음 저장소의 모든 활성 발급자 인증서에 대해 업데이트 된 CRL을 발급 하려면 crl **(Crl 해지 목록) 업데이트** 를 선택 합니다.
5. 몇 초 후에 **인증서 세부 정보**를 볼 수 있습니다. OPC UA 응용 프로그램에 배포할 최신 CA 인증서 및 CRL을 다운로드 하려면 **발급자** 또는 **crl**을 선택 합니다.

## <a name="manage-user-roles"></a>사용자 역할 관리

Azure AD Enterprise 응용 프로그램에서 OPC 자격 증명 모음 마이크로 서비스에 대 한 사용자 역할을 관리 합니다. 역할 정의에 대 한 자세한 설명은 [역할](howto-opc-vault-secure-ca.md#roles)을 참조 하세요.

기본적으로 테넌트의 인증 된 사용자는 서비스를 판독기로 로그인 할 수 있습니다. 권한이 높은 역할은 Azure Portal 또는 PowerShell을 사용 하 여 수동으로 관리 해야 합니다.

### <a name="add-user"></a>사용자 추가

1. Azure Portal을 엽니다.
2. **Azure Active Directory** > **엔터프라이즈 응용 프로그램**으로 이동 합니다.
3. OPC 자격 증명 모음 마이크로 서비스 (기본적 `resourceGroupName-service`으로) 등록을 선택 합니다.
4. **사용자 및 그룹**으로 이동 합니다.
5. **사용자 추가**를 선택합니다.
6. 특정 역할에 대 한 할당을 위해 사용자를 선택 하거나 초대 합니다.
7. 사용자에 대 한 역할을 선택 합니다.
8. **할당**을 선택합니다.
9. 관리자 또는 승인자 역할의 사용자에 대해 Azure Key Vault 액세스 정책 추가를 계속 합니다.

### <a name="remove-user"></a>사용자 삭제

1. Azure Portal을 엽니다.
2. **Azure Active Directory** > **엔터프라이즈 응용 프로그램**으로 이동 합니다.
3. OPC 자격 증명 모음 마이크로 서비스 (기본적 `resourceGroupName-service`으로) 등록을 선택 합니다.
4. **사용자 및 그룹**으로 이동 합니다.
5. 제거할 역할이 있는 사용자를 선택 하 고 **제거**를 선택 합니다.
6. 관리자 또는 승인자 역할에서 제거 된 사용자의 경우 Azure Key Vault 정책 에서도 제거 합니다.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Azure Key Vault에 사용자 액세스 정책 추가

추가 액세스 정책은 승인자 및 관리자에 게 필요 합니다.

기본적으로 서비스 id에는 Key Vault 액세스에 대 한 제한 된 권한만 있으므로 사용자 가장 없이 상승 된 작업 또는 변경 작업을 수행할 수 있습니다. 기본 서비스 권한은 비밀 및 인증서에 대 한 Get 및 List입니다. 비밀의 경우 한 가지 예외가 있습니다. 서비스는 사용자가 승인한 후에 비밀 저장소에서 개인 키를 삭제할 수 있습니다. 다른 모든 작업에는 사용자가 가장 한 권한이 필요 합니다.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>승인자 역할의 경우 다음 사용 권한을에 추가 해야 합니다 Key Vault

1. Azure Portal을 엽니다.
2. 배포 중에 사용 되 `resourceGroupName`는 OPC 자격 증명 모음으로 이동 합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동 합니다.
4. **액세스 정책**으로 이동 합니다.
5. **새로 추가**를 선택합니다.
6. 템플릿을 건너뜁니다. 요구 사항과 일치 하는 템플릿이 없습니다.
7. **보안 주체 선택**을 선택 하 고 추가할 사용자를 선택 하거나 새 사용자를 테넌트에 초대 합니다.
8. 다음 **키 사용 권한을**선택 합니다. **가져오기**, **나열**및 **서명**
9. 다음 **비밀 권한을**선택 합니다. **Get**, **List**, **Set**및 **Delete**.
10. 다음 **인증서 사용 권한을**선택 합니다. **Get** 및 **List**.
11. **확인**을 선택 하 고 **저장**을 선택 합니다.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>관리자 역할의 경우 다음 사용 권한을에 추가 해야 합니다 Key Vault

1. Azure Portal을 엽니다.
2. 배포 중에 사용 되 `resourceGroupName`는 OPC 자격 증명 모음으로 이동 합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동 합니다.
4. **액세스 정책**으로 이동 합니다.
5. **새로 추가**를 선택합니다.
6. 템플릿을 건너뜁니다. 요구 사항과 일치 하는 템플릿이 없습니다.
7. **보안 주체 선택**을 선택 하 고 추가할 사용자를 선택 하거나 새 사용자를 테넌트에 초대 합니다.
8. 다음 **키 사용 권한을**선택 합니다. **가져오기**, **나열**및 **서명**
9. 다음 **비밀 권한을**선택 합니다. **Get**, **List**, **Set**및 **Delete**.
10. 다음 **인증서 사용 권한을**선택 합니다. **가져오기**, **나열**, **업데이트**, **만들기**및 **가져오기**.
11. **확인**을 선택 하 고 **저장**을 선택 합니다.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Azure Key Vault에서 사용자 액세스 정책 제거

1. Azure Portal을 엽니다.
2. 배포 중에 사용 되 `resourceGroupName`는 OPC 자격 증명 모음으로 이동 합니다.
3. Key Vault `resourceGroupName-xxxxx`로 이동 합니다.
4. **액세스 정책**으로 이동 합니다.
5. 제거할 사용자를 찾고 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

OPC 자격 증명 모음 인증서와 사용자를 관리 하는 방법을 배웠으므로 이제 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 장치의 보안 통신](howto-opc-vault-secure.md)
