---
title: Azure HDInsight의 인증 문제
description: Azure HDInsight의 인증 문제
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: 6473b6b69ba45138c30e4f2c8a7f1ba1a7f2ae7e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816850"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight의 인증 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

Azure Data Lake (Gen1 또는 Gen2)에 의해 지원 되는 보안 클러스터에서 도메인 사용자가 HDI 게이트웨이를 통해 클러스터 서비스에 로그인 하는 경우 (예: Apache Ambari 포털에 로그인) HDI 게이트웨이는 먼저 Azure Active Directory (Azure AD)에서 OAuth 토큰을 가져오려고 시도한 다음 Azure AD DS에서 Kerberos 티켓을 가져옵니다. 이러한 단계 중 하나에서 인증이 실패할 수 있습니다. 이 문서에서는 이러한 문제 중 일부를 디버깅 하는 방법을 설명 합니다.

인증에 실패 하면 자격 증명을 입력 하 라는 메시지가 표시 됩니다. 이 대화 상자를 취소 하면 오류 메시지가 출력 됩니다. 다음은 몇 가지 일반적인 오류 메시지입니다.

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant 또는 unauthorized_client, 50126

### <a name="issue"></a>문제

페더레이션 사용자에 대 한 로그인이 실패 합니다 (오류 코드 50126) (클라우드 사용자에 대 한 로그인 성공). 오류 메시지는 다음과 유사 합니다.

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>원인

Azure AD 오류 코드 50126은 `AllowCloudPasswordValidation` 테 넌 트가 정책을 설정 하지 않았음을 의미 합니다.

### <a name="resolution"></a>해결 방법

Azure AD 테 넌 트의 회사 관리자는 Azure AD에서 ADFS 지원 사용자에 대 한 암호 해시를 사용 하도록 설정 해야 합니다.  `AllowCloudPasswordValidationPolicy` [HDInsight의 Enterprise Security Package 사용](../domain-joined/apache-domain-joined-architecture.md)문서에 표시 된 대로를 적용 합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant 또는 unauthorized_client, 50034

### <a name="issue"></a>문제

로그인이 실패 하 고 오류 코드 50034이 발생 합니다. 오류 메시지는 다음과 유사 합니다.

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>원인

사용자 이름이 잘못 되었습니다 (존재 하지 않음). 사용자가 Azure Portal에 사용 되는 것과 동일한 사용자 이름을 사용 하 고 있지 않습니다.

### <a name="resolution"></a>해결 방법

해당 포털에서 작동 하는 것과 동일한 사용자 이름을 사용 합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 또는 unauthorized_client, 50053

### <a name="issue"></a>문제

사용자 계정이 잠겨 있습니다. 오류 코드는 50053입니다. 오류 메시지는 다음과 유사 합니다.

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>원인

잘못 된 암호를 사용 하 여 로그인을 너무 많이 시도 했습니다.

### <a name="resolution"></a>해결 방법

30 분 정도 기다린 후 인증을 시도할 수 있는 응용 프로그램을 모두 중지 합니다.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant 또는 unauthorized_client, 50053 (#2)

### <a name="issue"></a>문제

암호가 만료 되었습니다. 오류 코드는 50053입니다. 오류 메시지는 다음과 유사 합니다.

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>원인

암호가 만료 되었습니다.

### <a name="resolution"></a>해결 방법

온-프레미스 시스템에서 Azure Portal의 암호를 변경한 다음 동기화가 완료 될 때까지 30 분 동안 기다립니다.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>문제

오류 메시지 `interaction_required` 를 수신 합니다.

### <a name="cause"></a>원인

조건부 액세스 정책 또는 MFA가 사용자에게 적용되고 있습니다. 대화형 인증은 아직 지원되지 않으므로 MFA/조건부 액세스에서 사용자 또는 클러스터를 제외해야 합니다. 클러스터 (IP 주소 기반 예외 정책)를 제외 하도록 선택한 경우 `ServiceEndpoints` 해당 vnet에 대해 AD를 사용할 수 있는지 확인 합니다.

### <a name="resolution"></a>해결 방법

[Azure Active Directory Domain Services를 사용 하 여 Enterprise Security Package를 사용 하 여 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)에 표시 된 대로 조건부 액세스 정책을 사용 하 고 MFA에서 HDInisght 클러스터를 제외 합니다.

---

## <a name="sign-in-denied"></a>로그인 거부 됨

### <a name="issue"></a>문제

로그인이 거부 되었습니다.

### <a name="cause"></a>원인

이 단계로 이동 하려면 OAuth 인증에 문제가 없지만 Kerberos 인증은입니다. 이 클러스터가 ADLS에서 지원 되는 경우 Kerberos 인증을 시도 하기 전에 OAuth 로그인이 성공 했습니다. WASB 클러스터에서 OAuth 로그인을 시도 하지 않습니다. 암호 해시가 동기화 되지 않은 경우, Azure AD DS에서 잠긴 사용자 계정 등의 여러 가지 Kerberos 오류가 발생할 수 있습니다. 암호 해시는 사용자가 암호를 변경 하는 경우에만 동기화 됩니다. Azure AD DS 인스턴스를 만들 때 생성 후 변경 된 암호의 동기화가 시작 됩니다. 처음에 설정 된 암호는 소급 하지 않습니다.

### <a name="resolution"></a>해결 방법

암호가 동기화 되지 않을 수 있다고 생각 되는 경우 암호를 변경 하 고 몇 분 후에 동기화 해 보세요.

에 SSH를 시도 합니다. 도메인에 가입 된 컴퓨터에서 동일한 사용자 자격 증명을 사용 하 여 인증 (kinit)을 시도 해야 합니다. 로컬 사용자를 사용 하 여 헤드/에 지 노드로 SSH를 실행 한 다음 kinit를 실행 합니다.

---

## <a name="kinit-fails"></a>kinit 실패

### <a name="issue"></a>문제

Kinit가 실패 합니다.

### <a name="cause"></a>원인

다양함

### <a name="resolution"></a>해결 방법

Kinit이 성공 하려면를 알아야 `sAMAccountName` 합니다 (영역이 없는 짧은 계정 이름). `sAMAccountName` 는 일반적으로의 bob과 같은 계정 접두사입니다 `bob@contoso.com` . 일부 사용자의 경우 다를 수 있습니다. 를 학습 하려면 디렉터리를 찾아보거나 검색 하는 기능이 필요 합니다 `sAMAccountName` .

검색 방법 `sAMAccountName` :

* 로컬 Ambari admin을 사용 하 여 Ambari에 로그인 할 수 있는 경우 사용자 목록을 확인 합니다.

* 도메인에 가입 된 [windows 컴퓨터](../../active-directory-domain-services/manage-domain.md)를 사용 하는 경우 표준 windows AD 도구를 사용 하 여 찾아볼 수 있습니다. 이렇게 하려면 도메인에서 작업 계정이 필요 합니다.

* 헤드 노드에서는 SAMBA 명령을 사용 하 여 검색할 수 있습니다. 이를 위해서는 유효한 Kerberos 세션 (성공한 kinit)이 필요 합니다. net ads 검색 "(userPrincipalName = bob *)"

    검색/찾아보기 결과에 특성이 표시 됩니다 `sAMAccountName` . 또한, 등의 다른 특성도 확인 `pwdLastSet` `badPasswordTime` `userPrincipalName` 하 여 이러한 속성이 기대한 내용과 일치 하는지 확인할 수 있습니다.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit이 사전 인증 실패로 실패 함

### <a name="issue"></a>문제

Kinit 실패와 함께 실패 `Preauthentication` 합니다.

### <a name="cause"></a>원인

사용자 이름 또는 암호가 잘못 되었습니다.

### <a name="resolution"></a>해결 방법

사용자 이름 및 암호를 확인 합니다. 또한 위에서 설명한 다른 속성도 확인 하세요. 자세한 디버깅을 사용 하려면 `export KRB5_TRACE=/tmp/krb.log` kinit를 시도 하기 전에 세션에서를 실행 합니다.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>TokenNotFoundException로 인해 작업/HDFS 명령이 실패 합니다.

### <a name="issue"></a>문제

로 인해 작업/HDFS 명령이 실패 `TokenNotFoundException` 합니다.

### <a name="cause"></a>원인

작업/명령이 성공 하기 위해 필요한 OAuth 액세스 토큰을 찾을 수 없습니다. ADLS/ABFS 드라이버는 저장소 요청을 만들기 전에 자격 증명 서비스에서 OAuth 액세스 토큰을 검색 합니다. 이 토큰은 동일한 사용자를 사용 하 여 Ambari 포털에 로그인 할 때 등록 됩니다.

### <a name="resolution"></a>해결 방법

작업을 실행 하는 데 해당 id가 사용 되는 사용자 이름을 통해 Ambari 포털에 한 번 로그인 했는지 확인 합니다.

---

## <a name="error-fetching-access-token"></a>액세스 토큰을 가져오는 동안 오류 발생

### <a name="issue"></a>문제

사용자가 오류 메시지 `Error fetching access token` 를 받습니다.

### <a name="cause"></a>원인

이 오류는 사용자가 Acl을 사용 하 여 ADLS Gen2에 액세스 하려고 할 때 간헐적으로 발생 하며, Kerberos 토큰이 만료 되었습니다.

### <a name="resolution"></a>해결 방법

* Azure Data Lake Storage Gen1의 경우 브라우저 캐시를 정리 하 고 다시 Ambari에 로그인 합니다.

* Azure Data Lake Storage Gen2의 경우 `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` 사용자가 로그인을 시도 하는 사용자에 대해를 실행 합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
