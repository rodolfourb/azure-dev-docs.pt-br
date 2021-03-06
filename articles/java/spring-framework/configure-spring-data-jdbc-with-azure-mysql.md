---
title: Usar o Spring Data JDBC com o Banco de Dados do Azure para MySQL
description: Saiba como usar o Spring Data JDBC com um Banco de Dados do Azure para MySQL.
documentationcenter: java
ms.date: 04/07/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 3bbed7be27854514d76b5cd3e5b905ba5741fa72
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831822"
---
# <a name="use-spring-data-jdbc-with-azure-database-for-mysql"></a>Usar o Spring Data JDBC com o Banco de Dados do Azure para MySQL

Este tópico demonstra a criação de um aplicativo de exemplo que usa o [Spring Data JDBC](https://spring.io/projects/spring-data-jdbc) para armazenar e recuperar informações no [Banco de Dados do Azure para MySQL](/azure/mysql/).

O [JDBC](https://jcp.org/en/jsr/detail?id=221) é a API Java padrão para se conectar a bancos de dados relacionais tradicionais.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Aplicativo de exemplo

Neste artigo, escreveremos o código de um aplicativo de exemplo. Caso você queira adiantar o processo, esse aplicativo já está codificado e disponível em [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-mysql](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-mysql).

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando ao digitar:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,mysql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Configurar o Spring Boot para usar o Banco de Dados do Azure para MySQL

Abra o arquivo *src/main/resources/application.properties* e adicione:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_MYSQL_PASSWORD

spring.datasource.initialization-mode=always
```

- Substitua as duas variáveis `$AZ_DATABASE_NAME` pelo valor que você configurou no início deste artigo.
- Substitua a variável `$AZ_MYSQL_PASSWORD` pelo valor que você configurou no início deste artigo.

> [!WARNING]
> A propriedade de configuração `spring.datasource.initialization-mode=always` significa que o Spring Boot gerará um esquema de banco de dados automaticamente, usando o arquivo `schema.sql` que criaremos mais tarde, sempre que o servidor for iniciado. Isso é ótimo para testes, mas excluirá os dados a cada reinicialização. Portanto, não deve ser usado na produção.

> [!NOTE]
> Acrescentamos `?serverTimezone=UTC` à propriedade de configuração `spring.datasource.url` para instruir o driver JDBC a usar o formato de data UTC (Tempo Universal Coordenado) ao conectar-se ao banco de dados. Caso contrário, nosso servidor Java não usaria o mesmo formato de data que o banco de dados, o que resultaria em um erro.

Agora, você deveria conseguir iniciar o aplicativo usando o wrapper do Maven fornecido:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Criar o esquema de banco de dados

O Spring Boot executará *src/main/resources/`schema.sql`* automaticamente para criar um esquema de banco de dados. Crie esse arquivo com o seguinte conteúdo:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Pare o aplicativo em execução e inicie-o novamente. O aplicativo agora usará o banco de dados `demo` que você criou anteriormente e criará uma tabela `todo` dentro dele.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o JDBC para armazenar e recuperar dados do servidor MySQL.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot que usa o JDBC para armazenar e recuperar dados do Banco de Dados do Azure para MySQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data JDBC, confira a [documentação de referência](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](../index.yml) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).