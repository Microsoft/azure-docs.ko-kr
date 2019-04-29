---
title: 'Always Encrypted: Azure SQL Database - Windows 인증서 저장소 | Microsoft Docs'
description: 이 문서에서는 SSMS(SQL Server Management Studio)의 상시 암호화 마법사를 사용하여 데이터베이스 암호화로 SQL Database의 중요한 데이터를 보호하는 방법을 보여 줍니다. 그뿐 아니라 Windows 인증서 저장소에 암호화 키를 저장하는 방법을 보여 줍니다.
keywords: 데이터 암호화, sql 암호화, 데이터베이스 암호화, 중요한 데이터 암호화, 상시 암호화
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
manager: craigg
ms.date: 03/08/2019
ms.openlocfilehash: 5226ec05af95cf305008968cf945070532274ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420077"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: 중요한 데이터 보호 및 Windows 인증서 저장소에 암호화 키 저장

이 문서에서는 [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/hh213248.aspx)의 [상시 암호화 마법사](https://msdn.microsoft.com/library/mt459280.aspx)를 사용하여 데이터베이스 암호화로 SQL Database의 중요한 데이터를 보호하는 방법을 보여 줍니다. 그뿐 아니라 Windows 인증서 저장소에 암호화 키를 저장하는 방법을 보여 줍니다.

Always Encrypted는 클라이언트와 서버 사이의 이동 중에, 그리고 데이터를 사용 중일 때 서버에서 중요한 미사용 데이터를 보호하는 Azure SQL Database 및 SQL Server 내의 새로운 데이터 암호 기술로서, 중요한 데이터가 데이터베이스 시스템에서 일반 텍스트로 나타나지 않도록 보장합니다. 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 앱 서버는 일반 텍스트 데이터에 액세스할 수 있습니다. 자세한 내용은 [상시 암호화(데이터베이스 엔진)](https://msdn.microsoft.com/library/mt163865.aspx)를 참조하세요.

상시 암호화를 사용하는 데이터베이스를 구성한 후에 Visual Studio로 C#에서 클라이언트 애플리케이션을 만들어 암호화된 데이터로 작업합니다.

이 문서의 단계를 수행하고 Azure SQL 데이터베이스에 대해 상시 암호화를 설정하는 방법을 알아봅니다. 이 문서에서는 다음 작업을 수행하는 방법을 배웁니다.

* SSMS에서 상시 암호화 마법사를 사용하여 [상시 암호화 키](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)를 만듭니다.
  * [CMK(열 마스터 키)](https://msdn.microsoft.com/library/mt146393.aspx)를 만듭니다.
  * [CEK(열 암호화 키)](https://msdn.microsoft.com/library/mt146372.aspx)를 만듭니다.
* 데이터베이스 테이블을 만들고 열을 암호화합니다.
* 암호화된 열에서 데이터를 삽입하고 선택하며 표시한 애플리케이션을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에는 다음이 필요합니다.

* Azure 계정 및 구독 없는 경우 지금 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록하세요.
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 버전 13.0.700.242 이상.
* [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) 이상(클라이언트 컴퓨터에서).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>빈 SQL 데이터베이스 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** > **데이터 + 저장소** > **SQL Database**로 이동합니다.
3. 새 서버 또는 기존 서버에 **클리닉**이라는 **빈** 데이터베이스를 만듭니다. Azure Portal에서 데이터베이스를 만드는 자세한 지침은 [첫 번째 Azure SQL 데이터베이스](sql-database-single-database-get-started.md)를 참조하세요.

    ![빈 데이터베이스 만들기](./media/sql-database-always-encrypted/create-database.png)

자습서의 뒷부분에서 연결 문자열이 필요합니다. 데이터베이스를 만든 후에 새 Clinic 데이터베이스로 이동하고 연결 문자열을 복사합니다. 언제든지 연결 문자열을 가져올 수 있지만 Azure 포털에 있을 때 복사하는 것이 쉽습니다.

1. **SQL Databases** > **Clinic** > **데이터베이스 연결 문자열 표시**를 클릭합니다.
2. **ADO.NET**에 대한 연결 문자열을 복사합니다.

    ![연결 문자열 복사](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>SSMS로 데이터베이스에 연결

SSMS를 열고 클리닉 데이터베이스가 있는 서버에 연결합니다.

1. SSMS를 엽니다. (열리지 않은 경우 **연결** > **데이터베이스 엔진**을 클릭하여 **서버에 연결** 창을 엽니다.)
2. 서버 이름 및 자격 증명을 입력합니다. 앞에서 복사한 SQL 데이터베이스 블레이드 및 연결 문자열에 서버 이름을 찾아볼 수 있습니다. *database.windows.net*을 포함하여 전체 서버 이름을 입력합니다.

    ![연결 문자열 복사](./media/sql-database-always-encrypted/ssms-connect.png)

**새 방화벽 규칙** 창이 열리면 Azure에 로그인하고 SSMS가 새 방화벽 규칙을 만들도록 합니다.

## <a name="create-a-table"></a>테이블 만들기

이 섹션에서는 환자 데이터를 저장할 테이블을 만듭니다. 처음에는 일반 테이블이지만 다음 섹션에서 암호화를 구성합니다.

1. **데이터베이스**를 확장합니다.
2. **Clinic** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.
3. 새 쿼리 창에 다음 Transact-SQL(T-SQL)을 붙여넣고 **실행** 합니다.

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO

## <a name="encrypt-columns-configure-always-encrypted"></a>열 암호화(상시 암호화 구성)

SSMS는 CMK, CEK 및 암호화된 열을 설정하여 상시 암호화를 쉽게 구성하는 마법사를 제공합니다.

1.  **데이터베이스** > **빈** > **테이블**를 사용하여 데이터베이스 암호화로 SQL 데이터베이스의 중요한 데이터를 보호하는 방법을 보여 줍니다.
2. **Patients** 테이블을 마우스 오른쪽 단추로 클릭하고 **열 암호화**를 선택하여 상시 암호화 마법사를 엽니다.

    ![열 암호화](./media/sql-database-always-encrypted/encrypt-columns.png)

Always Encrypted 마법사에는 다음 섹션이 포함되어 있습니다. **열 선택**, **마스터 키 구성**(CMK), **유효성 검사** 및 **요약**.

### <a name="column-selection"></a>열 선택

**소개** 페이지에서 **다음**을 클릭하여 **열 선택** 페이지를 엽니다. 이 페이지에서 암호화하려는 열, [암호화 형식 및 사용할 CEK(열 암호화 키)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) 를 선택합니다.

각 환자에 대해 **SSN** 및 **BirthDate** 정보를 암호화합니다. **SSN** 열은 같음 조회, 조인 및 그룹화를 지원하는 결정적 암호화를 사용합니다. **BirthDate** 열은 작업을 지원하지 않는 임의의 암호화를 사용합니다.

**SSN** 열에 대한 **암호화 형식**을 **결정적**으로 설정하고 **BirthDate** 열을 **무작위**로 설정합니다. **다음**을 클릭합니다.

![열 암호화](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>마스터 키 구성

**마스터 키 구성** 페이지는 CMK를 설치하고 CMK가 저장될 키 저장소 공급자를 선택합니다. 현재 Windows 인증서 저장소, Azure Key Vault 또는 하드웨어 보안 모듈(HSM)에 CMK를 저장할 수 있습니다. 이 자습서에는 Windows 인증서 저장소에 키를 저장하는 방법을 보여줍니다.

**Windows 인증서 저장소**가 선택되었는지 확인하고 **다음**을 클릭합니다.

![마스터 키 구성](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>유효성 검사

이제 열을 암호화하거나 나중에 실행할 PowerShell 스크립트를 저장할 수 있습니다. 이 자습서의 경우 **지금 전체 과정 진행**을 선택하고 **다음**을 클릭합니다.

### <a name="summary"></a>요약

설정이 모두 정확한 것을 확인하고 **마침** 을 클릭하여 상시 암호화에 대한 설정을 완료합니다.

![요약](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>마법사의 작업 확인

마법사가 완료된 후에 데이터베이스는 상시 암호화에 대해 설정됩니다. 마법사는 다음 작업을 수행했습니다.

* CMK를 만들었습니다.
* CEK를 만들었습니다.
* 암호화에 선택한 열을 구성합니다. **Patients** 테이블에는 현재 데이터가 없지만 이제 선택된 열의 기존 데이터가 암호화됩니다.

**Clinic** > **보안** > **상시 암호화 키**로 이동하여 SSMS에서 키 만들기를 확인할 수 있습니다. 이제 마법사에서 생성한 새 키를 볼 수 있습니다.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>암호화된 데이터로 작동하는 클라이언트 애플리케이션 만들기

상시 암호화가 설정되었으므로 암호화된 열에서 *삽입* 및 *선택*을 수행하는 애플리케이션을 빌드할 수 있습니다. 샘플 애플리케이션을 성공적으로 실행하려면 상시 암호화 마법사를 실행한 동일한 컴퓨터에서 실행해야 합니다. 다른 컴퓨터에서 이 애플리케이션을 실행하려면 클라이언트 앱을 실행하는 컴퓨터에 상시 암호화 인증서를 배포해야 합니다.  

> [!IMPORTANT]
> 상시 암호화 열이 있는 서버에 일반 텍스트 데이터를 전달하는 경우 애플리케이션은 [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) 개체를 사용해야 합니다. SqlParameter 개체를 사용하지 않고 리터럴 값을 전달하면 예외가 발생합니다.

1. Visual Studio를 열고 새 C# 콘솔 애플리케이션을 만듭니다. 프로젝트가 **.NET Framework 4.6** 이상으로 설정되도록 합니다.
2. 프로젝트 이름을 **AlwaysEncryptedConsoleApp**으로 지정하고 **확인**을 클릭합니다.

![새 콘솔 애플리케이션](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>연결 문자열을 수정하여 상시 암호화 사용

이 섹션에는 데이터베이스 연결 문자열에서 상시 암호화를 사용하는 방법을 설명합니다. 다음 섹션 "상시 암호화 샘플 콘솔 애플리케이션"에서 실제로 방금 만든 콘솔 앱을 수정합니다.

상시 암호화를 사용하려면 **열 암호화 설정** 키워드를 연결 문자열에 추가하고 **사용함**으로 설정해야 합니다.

이 연결 문자열에서 직접 설정하거나 [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)를 사용하여 설정할 수 있습니다. 다음 섹션에서 애플리케이션 예제는 **SqlConnectionStringBuilder**를 사용하는 방법을 보여줍니다.

> [!NOTE]
> 상시 암호화에 특정된 클라이언트 애플리케이션에서 필요한 유일한 변경 내용입니다. 외부(즉, 구성 파일)에서 연결 문자열을 저장하는 기존 애플리케이션이 있는 경우 코드를 변경하지 않고 상시 암호화를 사용할 수 있습니다.

### <a name="enable-always-encrypted-in-the-connection-string"></a>연결 문자열에서 상시 암호화 사용

연결 문자열에 다음 키워드를 추가합니다.

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>SqlConnectionStringBuilder로 상시 암호화 사용

다음 코드는 [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx)을 [사용함](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)으로 설정하여 상시 암호화를 사용하는 방법을 보여 줍니다.

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>상시 암호화 샘플 콘솔 애플리케이션

이 샘플에서는 다음 방법을 설명합니다.

* 연결 문자열을 수정하여 상시 암호화 사용.
* 암호화된 열에 데이터 삽입.
* 암호화된 열에서 특정 값에 필터링하여 레코드 선택.

**Program.cs** 내용을 다음 코드로 바꿉니다. Main 메서드 바로 위의 줄에서 전역 connectionString 변수에 대한 연결 문자열을 Azure 포털에서 유효한 연결 문자열로 바꿉니다. 이 코드에 대한 유일한 변경 내용입니다.

작업에서 상시 암호화를 확인하려면 앱을 실행합니다.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>데이터가 암호화되는지 확인합니다.

SSMS로 **Patients** 데이터를 쿼리하여 서버의 실제 데이터가 암호화되었는지 신속하게 확인할 수 있습니다. (열 암호화 설정이 아직 사용되도록 설정되지 않은 경우 현재 연결을 사용합니다.)

Clinic 데이터베이스에 대해 다음 쿼리를 실행합니다.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

암호화된 열에 일반 텍스트 데이터가 포함되지 않은 것을 볼 수 있습니다.

   ![새 콘솔 애플리케이션](./media/sql-database-always-encrypted/ssms-encrypted.png)

SSMS를 사용하여 일반 텍스트 데이터에 액세스하려면 **열 암호화 설정=활성화** 매개 변수를 연결에 추가할 수 있습니다.

1. SSMS에서 **개체 탐색기**에 있는 서버를 마우스 오른쪽 단추로 클릭하고 **연결 끊기**를 클릭합니다.
2. **연결** > **데이터베이스 엔진**을 클릭하여 **서버에 연결** 창을 열고 **옵션**을 클릭합니다.
3. **추가 연결 매개 변수**를 클릭하고 **열 암호화 설정=활성화**를 입력합니다.

    ![새 콘솔 애플리케이션](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. **Clinic** 데이터베이스에 대해 다음 쿼리를 실행합니다.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     이제 암호화된 열에서 일반 텍스트 데이터를 볼 수 있습니다.

    ![새 콘솔 애플리케이션](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> 다른 컴퓨터에서 SSMS(또는 클라이언트)와 연결한 경우 암호화 키에 대한 액세스 권한이 없으므로 데이터를 해독할 수 없습니다.

## <a name="next-steps"></a>다음 단계

상시 암호화를 사용하는 데이터베이스를 만든 후에 다음을 수행할 수 있습니다.

* 다른 컴퓨터에서 이 샘플을 실행합니다. 암호화 키에 대한 액세스 권한이 없으므로 일반 텍스트 데이터에 액세스할 수 없고 성공적으로 실행되지 않습니다.
* [키 회전 및 정리](https://msdn.microsoft.com/library/mt607048.aspx).
* [상시 암호화로 이미 암호화된 데이터 마이그레이션](https://msdn.microsoft.com/library/mt621539.aspx)
* [다른 클라이언트 머신에 상시 암호화 인증서 배포](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) ("애플리케이션 및 사용자가 인증서를 사용할 수 있도록 지정" 섹션 참조).

## <a name="related-information"></a>관련 정보

* [상시 암호화(클라이언트 개발)](https://msdn.microsoft.com/library/mt147923.aspx)
* [투명한 데이터 암호화](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server 암호화](https://msdn.microsoft.com/library/bb510663.aspx)
* [상시 암호화 마법사](https://msdn.microsoft.com/library/mt459280.aspx)
* [상시 암호화 블로그](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)