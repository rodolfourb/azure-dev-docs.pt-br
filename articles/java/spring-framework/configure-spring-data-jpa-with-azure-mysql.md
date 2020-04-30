---
title: Como usar o Spring Data JPA com o Banco de Dados do Azure para MySQL
description: Saiba como configurar e usar o Spring Data JPA com um Banco de Dados do Azure para MySQL.
documentationcenter: java
ms.date: 11/27/2019
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: conceptual
ms.openlocfilehash: 8620c7d5306e221479baad4cc2a73cc9cb04947d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81668822"
---
# <a name="how-to-use-spring-data-jpa-with-azure-database-for-mysql"></a>Como usar o Spring Data JPA com o Banco de Dados do Azure para MySQL

Este artigo demonstra a criação de um aplicativo de exemplo que usa o [Spring Data] para armazenar e recuperar informações em um [Banco de Dados do Azure para MySQL](/azure/mysql/) usando [JPA (Java Persistence API)](https://docs.oracle.com/javaee/7/tutorial/persistence-intro.htm).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.
* [Curl](https://curl.haxx.se/) ou utilitário HTTP semelhante para testar a funcionalidade.
* O utilitário de linha de comando [mysql](https://dev.mysql.com/downloads/).
* Um cliente [Git](https://git-scm.com/downloads).

## <a name="create-a-azure-database-for-mysql-server"></a>Criar um servidor de Banco de Dados do Azure para MySQL

### <a name="create-a-server-using-the-azure-portal"></a>Criar um servidor usando o portal do Azure

> [!NOTE]
> 
> Veja informações mais detalhadas sobre a criação de bancos de dados MySQL em [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

1. Navegue até o [portal do Azure](https://portal.azure.com) e conecte-se.

1. Selecione **+Criar um recurso**, **Bancos de dados** e selecione **Banco de Dados do Azure para MySQL**.

   ![Criar um banco de dados MySQL][MYSQL01]

1. Insira as seguintes informações:

   - **Assinatura**: especifique a assinatura do Azure para usar.
   - **Grupo de recursos**: especifique se deseja criar um novo grupo de recursos ou escolher um grupo de recursos existente.
   - **Nome do servidor**: Escolha um nome exclusivo para o servidor MySQL. Ele será usado para criar um nome de domínio totalmente qualificado, como *wingtiptoysmysql.mysql.database.azure.com*.
   - **Selecionar fonte**: No caso deste tutorial, escolha `Blank` para criar um novo banco de dados.
   - **Logon de administrador do servidor**: especifique o nome do administrador do banco de dados.
   - **Senha** e **Confirmar senha**: especifique a senha para o administrador do banco de dados.
   - **Localização**: especifique a região geográfica mais próxima do banco de dados.
   - **Versão**: especifique a versão mais atualizada do banco de dados.

   ![Criar as propriedades do banco de dados MySQL][MYSQL02]

1. Após inserir todas as informações acima, clique em **Revisar + Criar**.

### <a name="configure-a-firewall-rule-for-your-server-using-the-azure-portal"></a>Configurar uma regra de firewall para o servidor usando o portal do Azure

1. Navegue até o portal do Azure em <https://portal.azure.com/> e entre.

1. Clique em **Todos os Recursos** e, em seguida, clique no banco de dados MySQL que acabou de criar.

1. Clique em **Segurança de conexão** e, nas **Regras de firewall** , crie uma nova regra especificando um nome exclusivo para a regra e, em seguida, insira o intervalo de endereços IP que precisarão acessar seu banco de dados e clique em **Salvar** . (Para este exercício, o endereço IP é o do seu computador de desenvolvimento, que é o cliente.  Você pode usá-lo para o **Endereço IP inicial** e o **Endereço IP final**. Consulte também a observação sob o título *Criar um banco de dados usando o utilitário de linha de comando mysql*.)

   ![Configurar a segurança da conexão][MYSQL04]

### <a name="retrieve-the-connection-string-for-your-server-using-the-azure-portal"></a>Recuperar a cadeia de conexão para o servidor usando o portal do Azure

1. Navegue até o portal do Azure em <https://portal.azure.com/> e entre.

1. Clique em **Todos os Recursos** e, em seguida, clique no recurso do Banco de Dados do Azure para MySQL que você acabou de criar.

1. Clique em **Cadeias de conexão** e copie o valor no campo de texto **JDBC**.

   ![Recuperar sua cadeia de conexão JDBC][MYSQL05]

### <a name="create-a-database-using-the-mysql-command-line-utility"></a>Criar um banco de dados usando o utilitário de linha de comando `mysql`

1. Abra um shell de comando e conecte-se ao servidor do Banco de Dados do Azure para MySQL digitando um comando `mysql`, conforme demonstrado no exemplo a seguir:

   ```shell
   mysql --host wingtiptoysmysql.mysql.database.azure.com --user wingtiptoysuser@wingtiptoysmysql -p
   ```
   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `host` | Especifica o nome totalmente qualificado do servidor MySQL neste artigo. |
   | `user` | Especifica o administrador do MySQL e o nome abreviado do servidor neste artigo. |
   | `p` | Especifica uma espera até que uma senha seja solicitada. |


   O servidor MySQL deve responder com uma exibição como o exemplo a seguir:

   ```shell
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64552
   Server version: 5.6.39.0 MySQL Community Server (GPL)
   
   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql>
   ```
   > Observação: Se você receber um erro informando que o servidor não reconhece esse endereço IP, o endereço IP que o cliente está usando será mostrado no erro.  Volte e atribua-o conforme descrito anteriormente: *Configurar uma regra de firewall para o servidor usando o portal do Azure*.

1. Crie um banco de dados denominado *mysqldb* inserindo um comando `mysql`, como o exemplo a seguir:

   ```SQL
   CREATE DATABASE mysqldb;
   ```

   O servidor MySQL deve responder com uma exibição como o exemplo a seguir:

   ```shell
   Query OK, 1 row affected (0.30 sec)
   ```

1. OPCIONAL: verifique se seu banco de dados foi criado inserindo um comando `mysql` como o exemplo a seguir:

   ```SQL
   SHOW DATABASES;
   ```

   O servidor MySQL deve responder com uma exibição como o exemplo a seguir:

   ```shell
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | mysqldb            |
   | performance_schema |
   | sys                |
   +--------------------+
   ```

1. Insira `\q` para sair do utilitário `mysql`.

## <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Abra um shell de comando e clone o projeto de exemplo usando um comando git como no exemplo a seguir:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jpa-on-azure.git
   ```

1. Localize o arquivo *application.properties* no diretório *recursos* do seu projeto de exemplo ou crie o arquivo se ele ainda não existe.

1. Abra o arquivo *application.properties* em um editor de texto e adicione ou configure as seguintes linhas ao arquivo e substitua os valores de exemplo pelos valores adequados do início do artigo:

   ```yaml
   spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
   spring.datasource.url=jdbc:mysql://wingtiptoysmysql.mysql.database.azure.com:3306/mysqldb?useSSL=true&requireSSL=false
   spring.datasource.username=wingtiptoysuser@wingtiptoysmysql
   spring.datasource.password=********
    ```
   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `spring.jpa.database-platform` | Especifica a plataforma de banco de dados JPA. |
   | `spring.datasource.url` | Especifica sua cadeia de JDBC do MySQL neste artigo. |
   | `spring.datasource.username` | Especifica o nome do administrador do MySQL neste artigo com o nome abreviado do servidor anexado a ele. |
   | `spring.datasource.password` | Especifica sua senha de administrador do MySQL neste artigo. |

1. Salve e feche o arquivo *application.properties*.

> [!NOTE]
> A primeira propriedade do arquivo *application.properties* é `spring.jpa.hibernate.ddl-auto=create`, que é uma propriedade Hibernate que removerá e recriará automaticamente o esquema de banco de dados na inicialização do aplicativo.
> Essa configuração é útil durante os procedimentos de desenvolvimento/teste, mas você não deve usá-la na produção.

## <a name="package-and-test-the-sample-application"></a>Empacotar e testar o aplicativo de exemplo

1. Crie seu aplicativo de exemplo com o Maven. Por exemplo:

   ```shell
   mvn clean package -P mysql
   ```

1. Inicie o aplicativo de exemplo. Por exemplo:

   ```shell
   java -jar target/spring-data-jpa-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Crie novos registros usando `curl` em um prompt de comando como nos exemplos a seguir:

   ```shell
   curl -s -d '{"name":"dog","species":"canine"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d '{"name":"cat","species":"feline"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Seu aplicativo deve retornar valores como os seguintes:

   ```shell
   Added Pet(id=1, name=dog, species=canine).

   Added Pet(id=2, name=cat, species=feline).
   ```

1. Recupere todos os registros existentes usando `curl` em um prompt de comando como nos exemplos a seguir:

   ```shell
   curl -s http://localhost:8080/pets
   ```
    
   Seu aplicativo deve retornar valores como os seguintes:

   ```json
   [{"id":1,"name":"dog","species":"canine"},{"id":2,"name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Resumo

Neste tutorial, você criou um aplicativo Java de exemplo que usa o Spring Data para armazenar e recuperar informações em um Banco de Dados do Azure para MySQL usando o JPA.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

<!-- URL List -->

[Azure para desenvolvedores Java]: /azure/developer/java/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[MYSQL01]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png
[MYSQL02]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png
[MYSQL04]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-04.png
[MYSQL05]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-05.png