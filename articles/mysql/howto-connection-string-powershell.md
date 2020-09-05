---
title: PowerShell을 사용하여 연결 문자열 생성 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에 연결하는 연결 문자열을 생성하는 Azure PowerShell 예제를 제공합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/5/2020
ms.openlocfilehash: 37a257e2ca7f354659c032833480dd207cc1bed9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839951"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>PowerShell을 사용하여 Azure Database for MySQL 연결 문자열을 생성하는 방법

이 문서에서는 Azure Database for MySQL 서버에 대한 연결 문자열을 생성하는 방법을 보여줍니다. 연결 문자열을 사용하여 여러 애플리케이션에서 Azure Database for MySQL에 연결할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 문서에서는 다음 가이드에서 만든 리소스를 시작점으로 사용합니다.

* [빠른 시작: PowerShell을 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>연결 문자열 가져오기

`Get-AzMySqlConnectionString` cmdlet은 Azure Database for MySQL에 애플리케이션을 연결하기 위한 연결 문자열을 생성하는 데 사용됩니다. 다음 예제에서는 **mydemoserver**의 PHP 클라이언트에 대한 연결 문자열을 반환합니다.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

`Client` 매개 변수에 사용할 수 있는 유효한 값은 다음과 같습니다.

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Database for MySQL 서버 매개 변수 사용자 지정](howto-configure-server-parameters-using-powershell.md)
