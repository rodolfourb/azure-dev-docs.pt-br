---
title: Implantar um aplicativo Web do Spring Boot no Serviço de Aplicativo do Azure no Linux
description: Este tutorial orienta você pelas etapas de implantar um aplicativo Spring Boot como um aplicativo Web do Linux no Microsoft Azure.
services: azure app service
documentationcenter: java
ms.date: 12/31/2019
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 97f6ef6e1d53b8923a8d29aa7747442ee6ac7efc
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90830062"
---
# <a name="deploy-a-spring-boot-application-to-linux-on-azure-app-service"></a>Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure no Linux

Este tutorial descreve como usar o [Docker] para colocar seu aplicativo [Spring Boot] em um contêiner e implantar sua própria imagem do Docker em um host Linux no [Serviço de Aplicativo do Azure](/azure/app-service/containers/app-service-linux-intro).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisa ter os seguintes pré-requisitos:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* A[CLI (interface de linha de comando) do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* A ferramenta de compilação [Maven] do Apache (Versão 3).
* Um cliente [Git].
* Um cliente do [Docker].

> [!NOTE]
>
> Devido aos requisitos de virtualização deste tutorial, você não pode seguir as etapas neste artigo em uma máquina virtual. Você deve usar um computador físico com recursos de virtualização habilitados.

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar o aplicativo Web de Introdução ao Spring Boot no Docker

As etapas a seguir descrevem as etapas necessárias para criar um aplicativo Web Spring Boot simples e testá-lo localmente.

1. Abra um prompt de comando, crie um diretório local para conter o aplicativo e altere para o diretório. Por exemplo:

   ```bash
   mkdir SpringBoot
   cd SpringBoot
   ```

1. Clone o exemplo de projeto [Introdução ao Spring Boot no Docker] para o diretório criado. Por exemplo:

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto concluído. Por exemplo:

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Crie o arquivo JAR usando o Maven. Por exemplo:

   ```bash
   mvn package
   ```

1. Quando o aplicativo Web tiver sido criado, altere o diretório para o diretório `target` em que o arquivo JAR está localizado e inicie o aplicativo Web. Por exemplo:

   ```bash
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar --server.port=80
   ```

1. Teste o aplicativo Web navegando até ele localmente usando um navegador da Web. Por exemplo, se você tiver ondulação disponível e tiver configurado o servidor Tomcat para ser executado na porta 80:

   ```bash
   curl http://localhost
   ```

1. Você verá a seguinte mensagem exibida: **Olá, mundo do Docker**

   ![Procurar aplicativo de exemplo localmente][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Criar um Registro de Contêiner do Azure para usar como um Registro do Docker Privado

As etapas a seguir descrevem como usar o portal do Azure para criar um Registro de Contêiner do Azure.

> [!NOTE]
>
> Se você quiser usar a CLI do Azure, em vez do portal do Azure, siga as etapas em [Criar um registro de contêiner do Docker privado usando a CLI do Azure 2.0](/azure/container-registry/container-registry-get-started-azure-cli).

1. Navegue até o [Azure portal] e conecte-se.

   Depois de entrar em sua conta no portal do Azure, siga as etapas no artigo [Criar um registro de contêiner privado do Docker usando o portal do Azure], que são explicadas nas etapas a seguir para fins de conveniência.

1. Clique no ícone de menu para **+ Novo**, clique em **Contêineres** e, em seguida, clique em **Registro de Contêiner do Azure**.

   ![Criar um novo Registro de Contêiner do Azure][AR01]

1. Quando a página **Criar registro de contêiner** for exibida, insira o **Nome do registro**, a **Assinatura**, o **Grupo de recursos** e a **Localização**. Em seguida, clique em **Criar**.

   ![Definir configurações do registro de contêiner do Azure][AR03]

## <a name="configure-maven-to-build-image-to-your-azure-container-registry"></a>Configurar o Maven a fim de criar uma imagem para o Registro de Contêiner do Azure

1. Navegue até o diretório de projeto concluído para o seu aplicativo Spring Boot (por exemplo: "*C:\SpringBoot\gs-spring-boot-docker\complete*" ou " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") e abra o arquivo *pom.xml* com um editor de texto.

1. Atualize a coleção `<properties>` no arquivo *pom.xml* com a versão mais recente do [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin), o valor do servidor de logon e as configurações de acesso para o Registro de Contêiner do Azure da seção anterior deste tutorial. Por exemplo: 

   ```xml
   <properties>
      <jib-maven-plugin.version>2.5.2</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Adicione [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) à coleção `<plugins>` no arquivo *pom.xml*.  Esse exemplo usa a versão 2.2.0.

   Especifique a imagem base em `<from>/<image>`, aqui `mcr.microsoft.com/java/jre:8-zulu-alpine`. Especifique o nome da imagem final a ser criada da base em `<to>/<image>`.  

   A autenticação `{docker.image.prefix}` é o **Servidor de logon** na página de registro mostrada anteriormente. O `{project.artifactId}` é o nome e o número de versão do arquivo JAR do primeiro build do Maven do projeto.

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jre:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navegue até o diretório de projeto completo para o seu aplicativo Spring Boot e execute o seguinte comando para recompilar o aplicativo e fazer o push do contêiner ao Registro de Contêiner do Azure:

   ```bash
   az acr login -n wingtiptoysregistry && mvn compile jib:build
   ```

> [!NOTE]
> 1. O comando `az acr login ...` tentará fazer logon no Registro de Contêiner do Azure. Caso contrário, você precisará fornecer `<username>` e `<password>` para jib-maven-plugin. Confira [Métodos de autenticação](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#authentication-methods) no Jib.
> 2. Quando estiver usando o Jib para enviar sua imagem por push ao Registro de Contêiner do Azure, a imagem não usará o *Dockerfile*. Consulte [este](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html) documento para obter detalhes.
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Criar um aplicativo Web no Linux no Serviço de Aplicativo do Azure usando a imagem de contêiner

1. Navegue até o [Azure portal] e conecte-se.

2. Clique no ícone de menu para **+ Criar um recurso**, clique em **Computação** e, em seguida, clique em **Aplicativo Web para Contêineres**.
   
   ![Criar um aplicativo Web no portal do Azure][LX01]

3. Quando a página **Aplicativo Web no Linux** for exibida, insira as seguintes informações:

   * Escolha uma **Assinatura** na lista suspensa.

   * Escolha um **Grupo de Recursos** existente ou especifique um nome para criar um novo grupo de recursos.

   * Insira um nome exclusivo para o **Nome do aplicativo**, por exemplo: "*wingtiptoyslinux*"

   * Especifique `Docker Container` para **Publicar**.

   * Escolha o *Linux* como o **Sistema Operacional**.

   * Selecione a **Região**.

   * Aceite o **Plano do Linux** e escolha um **Plano do Serviço de Aplicativo** existente ou clique em **Criar novo** para criar um novo plano do serviço de aplicativo.

   * Clique em **Avançar: Docker**.

   ![Definir configurações do aplicativo Web][LX02]

      Na página **Aplicativo Web**, selecione **Docker** e insira as seguintes informações:

   * Selecione **Contêiner Único**.

   * **Registro**: Escolha seu contêiner, por exemplo: "*wingtiptoysregistry*"

   * **Imagem**: Selecione a imagem criada anteriormente, por exemplo: "*gs-spring-boot-docker*"

   * **Tag**: escolha a marca para a imagem, por exemplo: "*mais recente*"

   * **Comando de inicialização**: deixe em branco, pois a imagem já tem o comando de inicialização

   Depois de inserir todas as informações acima, clique em **Revisar + criar**.

   ![Definir configurações do aplicativo Web][LX02-A]

   * Clique em **Revisar + Criar**.

Revise as informações e clique em **Criar**.

Quando a implantação estiver concluída, selecione **Ir para o recurso**.  A página de implantação exibirá a URL para acessar o aplicativo.

   ![Obtenha a URL de implantação][LX02-B]

> [!NOTE]
>
> O Azure mapeará automaticamente as solicitações de Internet para o servidor Tomcat inserido que está em execução na porta -80. No entanto, se você tiver configurado seu servidor Tomcat inserido para ser executado na porta -8080 ou na porta personalizada, precisará adicionar uma variável de ambiente ao seu aplicativo Web que defina a porta do servidor Tomcat inserido. Para fazer isso, execute as seguintes etapas:
>
> 1. Navegue até o [Azure portal] e conecte-se.
>
> 2. Clique no ícone dos **Aplicativos Web** e selecione seu aplicativo na página **Serviços de Aplicativos**.
>
> 3. Clique em **Configuração** no painel de navegação à esquerda.
>
> 4. Na seção **Configurações de aplicativo**, adicione uma nova configuração chamada **WEBSITES_PORT** e insira o número da porta personalizada para o valor.
>
> 5. Clique em **OK**. Em seguida, clique em **Salvar**.
>
> ![Como salvar um número da porta personalizado no portal do Azure][LX03]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](./index.yml)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Implantar um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

Para obter mais detalhes sobre o Spring Boot no projeto de exemplo do Docker, consulte [Introdução ao Spring Boot no Docker].

Para obter ajuda na introdução a seus próprios aplicativos Spring Boot, confira **Spring Initializr** em https://start.spring.io/.

Para obter mais informações de introdução sobre como criar um aplicativo Spring Boot simples, consulte o Spring Initializr em https://start.spring.io/.

Para obter mais exemplos sobre como usar imagens personalizadas do Docker com o Azure, veja [Usando uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux].

<!-- URL List -->

[CLI (interface de linha de comando) do Azure]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure para desenvolvedores Java]: ../index.yml
[Azure portal]: https://portal.azure.com/
[Criar um registro de contêiner privado do Docker usando o portal do Azure]: /azure/container-registry/container-registry-get-started-portal
[Usando uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux]: /azure/app-service/tutorial-custom-container
[Docker]: https://www.docker.com/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/java/
[Maven]: http://maven.apache.org/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Introdução ao Spring Boot no Docker]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: ../fundamentals/java-jdk-long-term-support.md
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: media/deploy-spring-boot-java-app-on-linux/SB02.png
[AR01]: media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR03]: media/deploy-spring-boot-java-app-on-linux/AR03.png
[LX01]: media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX02-A]: media/deploy-spring-boot-java-app-on-linux/LX02-A.png
[LX02-B]: media/deploy-spring-boot-java-app-on-linux/LX02-B.png
[LX03]: media/deploy-spring-boot-java-app-on-linux/LX03.png