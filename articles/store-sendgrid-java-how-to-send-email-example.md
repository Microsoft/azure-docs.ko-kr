---
title: store-sendgrid-java-how-to-send-email-example
description: Azure 배포에서 Java의 SendGrid를 사용하여 메일을 보내는 방법
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 79cb9bb82862f5720d5ec2262ba30dbbcf3e3f66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930177"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Azure 배포에서 Java의 SendGrid를 사용하여 메일을 보내는 방법
다음 예제는 Azure에 호스트된 웹 페이지에서 SendGrid를 사용하여 전자 메일을 보내는 방법을 보여 줍니다. 다음 스크린샷에 표시 된 것 처럼 응용 프로그램에서 전자 메일 값에 대 한 사용자를 묻습니다.

![전자 메일 양식][emailform]

결과 이메일은 다음 스크린샷과 유사 합니다.

![전자 메일 메시지][emailsent]

이 항목에서 코드를 사용하려면 다음을 수행해야 합니다.

1. 예를 들어 <https://www.oracle.com/technetwork/java/javamail/index.html>에서 javax.mail JAR을 가져옵니다.
2. Java 빌드 경로에 JAR을 추가합니다.
3. Eclipse를 사용하여 이 Java 애플리케이션을 만드는 경우, Eclipse의 배포 어셈블리 기능을 사용하여 애플리케이션 배포 파일(WAR)에 SendGrid 라이브러리를 포함시킵니다. Eclipse를 사용하지 않고 이 Java 애플리케이션을 만드는 경우, 같은 Azure 역할 내에 이 라이브러리가 Java 애플리케이션으로 포함되어 있으며 애플리케이션의 클래스 경로에 추가되어 있는지 확인합니다.

또한 고유한 SendGrid 사용자 이름 및 암호가 있어야 전자 메일을 보낼 수 있습니다. SendGrid를 시작하려면 [Java의 SendGrid를 사용하여 전자 메일을 보내는 방법](store-sendgrid-java-how-to-send-email.md)을 참조하십시오.

또한 [Eclipse에서 Azure용 Hello World 애플리케이션 만들기](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)(영문)에 나온 정보나 Eclipse를 사용하지 않는 경우 Azure에서 Java 애플리케이션을 호스트하는 다른 기술을 익히는 것이 좋습니다.

## <a name="create-a-web-form-for-sending-email"></a>전자 메일을 보내기 위한 웹 양식 만들기
다음 코드는 전자 메일을 보내기 위해 웹 양식을 만들고 사용자 데이터를 검색하는 방법을 보여 줍니다. 이 내용에서 JSP 파일의 이름은 **emailform.jsp**입니다.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>전자 메일을 보내는 코드 만들기
다음 코드는 emailform.jsp에서 양식을 완료하면 호출되어 전자 메일 메시지를 만들고 보냅니다. 이 내용에서 JSP 파일의 이름은 **sendemail.jsp**입니다.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

전자 메일을 전송 하는 것 외에도 emailform.jsp 사용자에 대 한 결과 제공 예로 다음 스크린샷과 같습니다.

![메일 보내기 결과][emailresult]

## <a name="next-steps"></a>다음 단계
컴퓨팅 에뮬레이터에 애플리케이션을 배포하고 브라우저 내에서 emailform.jsp를 실행한 후, 양식에 값을 입력하고 **이 이메일 보내기**를 클릭한 다음, sendemail.jsp의 결과를 표시합니다.

이 코드는 Azure의 Java에서 SendGrid를 사용하는 방법을 보여 줍니다. Azure를 프로덕션에 배포하기 전에 더 많은 오류 처리 또는 기타 기능을 추가할 수 있습니다. 예를 들면 다음과 같습니다. 

* 웹 양식을 사용하는 대신 Azure 저장소 Blob 또는 SQL Database를 사용하여 전자 메일 주소 및 전자 메일 메시지를 저장할 수 있습니다. Java에서 Azure Storage Blob 사용에 대한 내용은 [Java에서 Blob Storage 서비스를 사용하는 방법](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/)(영문)을 참조하십시오. Java에서 SQL Database 사용에 대한 내용은 [Java에서 SQL Database 사용](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java)(영문)을 참조하십시오.
* Java의 SendGrid 사용에 대한 자세한 내용은 [Java의 SendGrid를 사용하여 전자 메일을 보내는 방법](store-sendgrid-java-how-to-send-email.md)을 참조하십시오.

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
