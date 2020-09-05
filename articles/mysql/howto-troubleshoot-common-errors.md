---
title: 일반적인 오류 문제 해결 - Azure Database for MySQL
description: Azure Database for MySQL 서비스를 처음 사용하는 사용자가 발생하는 일반적인 마이그레이션 오류 문제를 해결하는 방법을 알아봅니다.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719786"
---
# <a name="common-errors"></a>일반 오류

Azure Database for MySQL은 MySQL 커뮤니티 버전을 기반으로 하는 완전 관리형 서비스입니다. 관리되는 서비스 환경의 MySQL 환경은 사용자 환경에서 MySQL을 실행하는 것과 다를 수 있습니다. 이 문서에서는 처음으로 Azure Database for MySQL 서비스로 마이그레이션하거나 개발하는 동안 발생할 수 있는 일반적인 오류 중 일부를 확인할 수 있습니다.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>SUPER 권한 및 DBA 역할 부족으로 인한 오류

SUPER 권한 및 DBA 역할은 서비스에서 지원되지 않습니다. 따라서 아래 나열된 몇 가지 일반적인 오류가 발생할 수 있습니다.

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>오류 1419: SUPER 권한이 없고 이진 로깅을 사용하도록 설정되어 있습니다(덜 안전한 log_bin_trust_function_creators 변수를 *사용할 수 있음*).

함수를 만들거나 아래와 같이 트리거하거나 스키마를 가져오는 동안 위의 오류가 발생할 수 있습니다. CREATE FUNCTION 또는 CREATE TRIGGER와 같은 DDL 문은 이진 로그에 기록되므로 보조 복제본에서 실행할 수 있습니다. 복제본 SQL 스레드에는 권한을 상승시키는 데 활용할 수 있는 모든 권한이 있습니다. 이진 로깅을 사용하도록 설정된 서버에 대해 이러한 위험을 방지하려면 MySQL 엔진에는 저장된 함수 작성자에게 필요한 일반적인 CREATE ROUTINE 권한 외에 SUPER 권한이 있어야 합니다. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**해결 방법**:  이 오류를 해결하려면 포털의 [서버 매개 변수](howto-server-parameters.md) 블레이드에서 log_bin_trust_function_creators를 1로 설정하여 DDL 문을 실행하거나 스키마를 가져와서 원하는 개체를 만들고 log_bin_trust_function_creators 매개 변수를 만든 후에 이전 값으로 되돌립니다.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>줄 101의 오류 1227(42000): 액세스가 거부되었습니다. 이 작업에 대한 SUPER 권한(하나 이상)이 필요합니다. 종료 코드 1로 인해 작업이 실패했습니다.

위의 오류는 덤프 파일을 가져오거나 [정의자](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html)가 포함된 프로시저를 만드는 동안 발생할 수 있습니다. 

**해결 방법**:  이 오류를 해결하기 위해 관리 사용자는 다음 예제와 같이 GRANT 명령을 실행하여 프로시저를 만들거나 실행할 수 있는 권한을 부여할 수 있습니다.

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
또는 아래와 같이 정의자를 가져오기 프로세스를 실행하는 관리 사용자의 이름으로 바꿀 수 있습니다.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>다음 단계
원하는 답을 찾지 못한 경우 다음을 고려하세요.
- [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)에 질문을 게시합니다.
- Azure Database for MySQL 팀 [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)에 이메일을 보냅니다. 이 이메일 주소는 기술 지원 별칭이 아닙니다.
- Azure 지원에 문의하여 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql)를 통해 항목을 만드세요.
