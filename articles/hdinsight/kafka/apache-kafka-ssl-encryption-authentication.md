---
title: Azure HDInsight에서 Apache Kafka에 대한 SSL 암호화 및 인증 설정
description: Kafka 클라이언트와 Kafka 브로커 간의 통신 및 Kafka 브로커 간의 통신에 대해 SSL 암호화를 설정합니다. 클라이언트의 SSL 인증을 설정합니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 01/15/2019
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 9d8d5e57d0dd7d7022e65a061360c8450848fb4b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114960"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Kafka에 대한 SSL(Secure Sockets Layer) 암호화 및 인증 설정

이 문서에서는 Apache Kafka 클라이언트와 Apache Kafka 브로커 간에 SSL 암호화를 설정하는 방법을 설명합니다. 또한 클라이언트 인증(양방향 SSL이라고도 함)을 설정하는 데 필요한 단계를 제공합니다.

## <a name="server-setup"></a>서버 설정

첫 단계에서는 각 Kafka 브로커에 키 저장소와 신뢰 저장소를 만듭니다. 이러한 저장소를 만든 후에는 CA(인증 기관) 및 브로커 인증서를 저장소로 가져옵니다.

> [!Note] 
> 이 가이드에서는 자체 서명된 인증서를 사용하지만, 가장 안전한 솔루션은 신뢰할 수 있는 CA에서 발급한 인증서를 사용하는 것입니다.

다음 작업을 수행하여 서버 설정을 완료합니다.

1. ssl이라는 폴더를 만들고 서버 암호를 환경 변수로 내보냅니다. 이 가이드의 나머지 부분에서 `<server_password>`를 서버의 실제 관리자 암호로 바꿉니다.
1. Java 키 저장소(kafka.server.keystore.jks) 및 CA 인증서를 만듭니다.
1. 그런 다음, 서명 요청을 만들어 CA에서 서명한, 이전 단계에서 만든 인증서를 가져옵니다.
1. 이제 서명 요청을 CA로 전송하고 서명된 이 인증서를 가져옵니다. 자체 서명된 인증서를 사용 중이므로 `openssl` 명령을 사용하여 CA를 통해 인증서에 서명합니다.
1. 신뢰 저장소를 만들고 CA의 인증서를 가져옵니다.
1. 공용 CA 인증서를 키 저장소로 가져옵니다.
1. 서명된 인증서를 키 저장소로 가져옵니다.

아래 코드 조각에는 이러한 단계를 완료하는 명령이 나와 있습니다.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

서명된 인증서를 키 저장소로 가져오는 작업은 Kafka 브로커에 대한 신뢰 저장소와 키 저장소를 구성하는 데 필요한 최종 단계입니다.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>SSL을 사용하도록 Kafka 구성 업데이트 및 브로커 다시 시작

키 저장소 및 신뢰 저장소를 사용하여 각 Kafka 브로커를 설정하고 올바른 인증서를 가져왔습니다.  다음으로, Ambari를 사용하여 관련된 Kafka 구성 속성을 수정하고 Kafka 브로커를 다시 시작합니다. 

구성 수정을 완료하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 Azure HDInsight Apache Kafka 클러스터를 선택합니다.
1. **클러스터 대시보드**에서 **Ambari 홈**을 클릭하여 Ambari UI로 이동합니다.
1. **Kafka 브로커**에서 **수신기** 속성을 `PLAINTEXT://localhost:9092,SSL://localhost:9093`로 설정합니다.
1. **고급 kafka-broker**에서 **security.inter.broker.protocol** 속성을 `SSL`로 설정합니다.

    ![Ambari에서 Kafka SSL 구성 속성 편집](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. **사용자 지정 kafka-broker**에서 **ssl.client.auth** 속성을 `required`로 설정합니다. 이 단계는 인증 및 암호화를 설정하는 경우에만 필요합니다.

    ![Ambari에서 kafka SSL 구성 속성 편집](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. FQDN(정규화된 도메인 이름) 대신 IP 주소를 보급하기 위해 Kafka `server.properties` 파일에 구성 속성을 추가합니다.

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 이전 변경 내용이 올바르게 적용되었는지 확인하기 위해 필요한 경우 Kafka `server.properties` 파일에 다음 줄이 있는지 확인할 수 있습니다.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. 모든 Kafka 브로커를 다시 시작합니다.

## <a name="client-setup-with-authentication"></a>클라이언트 설정(인증 포함)

> [!Note]
> 다음 단계는 SSL 암호화 **및** 인증을 둘 다 설정하는 경우에만 필요합니다. 암호화만 설정하는 경우 [클라이언트 설정(인증 제외)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)에서 계속 진행하세요.

다음 단계를 수행하여 클라이언트 설정을 완료합니다.

1. 클라이언트 컴퓨터(hn1)에 로그인합니다.
1. 클라이언트 암호를 내보냅니다. `<client_password>`를 Kafka 클라이언트 머신의 실제 관리자 암호로 바꿉니다.
1. Java 키 저장소를 만들고 서명된 브로커용 인증서를 가져옵니다. 그런 다음, CA가 실행 중인 VM에 인증서를 복사합니다.
1. CA 머신(wn0)으로 전환하여 클라이언트 인증서에 서명합니다.
1. 클라이언트 머신(hn1)으로 이동한 다음, `~/ssl` 폴더로 이동합니다. 서명된 인증서를 클라이언트 머신에 복사합니다.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

마지막으로 `cat client-ssl-auth.properties` 명령을 사용하여 `client-ssl-auth.properties` 파일을 확인합니다. 파일에 다음 줄이 있어야 합니다.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>클라이언트 설정(인증 제외)

인증이 필요하지 않은 경우 SSL 암호화만 설정하는 단계는 다음과 같습니다.

1. 클라이언트 컴퓨터(hn1)에 로그인한 다음 `~/ssl` 폴더로 이동합니다.
1. 클라이언트 암호를 내보냅니다. `<client_password>`를 Kafka 클라이언트 머신의 실제 관리자 암호로 바꿉니다.
1. CA 컴퓨터(wn0)에서 클라이언트 컴퓨터로 서명된 인증서를 복사합니다.
1. 신뢰 저장소로 CA 인증서를 가져옵니다.
1. 키 저장소로 CA 인증서를 가져옵니다.

다음 코드 조각에 이러한 단계가 나와 있습니다.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

마지막으로 `cat client-ssl-auth.properties` 명령을 사용하여 `client-ssl-auth.properties` 파일을 확인합니다. 파일에 다음 줄이 있어야 합니다.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Kafka란?](apache-kafka-introduction.md)