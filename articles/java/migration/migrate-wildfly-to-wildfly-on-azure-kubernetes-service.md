---
title: Migrar aplicativos WildFly para o WildFly no Serviço de Kubernetes do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WildFly existente para ser executado no WildFly em um contêiner do Serviço de Kubernetes do Azure.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 3/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 701e1f1a8db4148837b793227ee3e91ef8ef97d5
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738377"
---
# <a name="migrate-wildfly-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrar aplicativos WildFly para o WildFly no Serviço de Kubernetes do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WildFly existente para ser executado no WildFly em um contêiner do Serviço de Kubernetes do Azure.

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Inventariar todos os segredos

Verifique todas as propriedades e os arquivos de configuração nos servidores de produção em busca de segredos e senhas. Não se esqueça de verificar o *jboss-web.xml* em seus WARs. Arquivos de configuração que contenham senhas ou credenciais também podem ser encontrados dentro de seu aplicativo.

Considere armazenar esses segredos no Azure Key Vault. Para saber mais, consulte [Conceitos básicos do Azure Key Vault](/azure/key-vault/basic-concepts).

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

### <a name="inventory-jndi-resources"></a>Inventariar recursos de JNDI

Faça um inventário de todos os recursos de JNDI. Alguns, como agentes de mensagens JMS, podem exigir migração ou reconfiguração.

### <a name="determine-whether-session-replication-is-used"></a>Determinar se a replicação de sessão é usada

Se seu aplicativo depender da replicação de sessão, você precisará alterar seu aplicativo para remover essa dependência.

#### <a name="inside-your-application"></a>Dentro de seu aplicativo

Inspecione o arquivo *WEB-INF/jboss-web.xml* e/ou *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Documentar fontes de dados

Se seu aplicativo usar algum banco de dados, você precisará capturar as informações a seguir:

* Qual é o nome da fonte de dados?
* Qual é a configuração do pool de conexões?
* Onde posso encontrar o arquivo JAR do driver JDBC?

Para obter mais informações, consulte [Configuração de fonte de dados](http://docs.wildfly.org/19/Admin_Guide.html#DataSource) na documentação do WildFly.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Determinar se e como o sistema de arquivos é usado

Qualquer uso do sistema de arquivos no servidor de aplicativos exigirá reconfiguração ou, em casos raros, alterações de arquitetura. O sistema de arquivos pode ser usado por módulos do WildFly ou pelo código do aplicativo. Você pode identificar alguns ou todos os cenários descritos nas seções a seguir.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Determinar se conectores JCA estão em uso

Se seu aplicativo usar conectores JCA, você precisará validar se o conector JCA pode ser usado no WildFly. Se a implementação do JCA estiver vinculada ao WildFly, você precisará refatorar seu aplicativo para remover essa dependência. Se puder ser usado, você precisará adicionar os JARs ao classpath do servidor e colocar os arquivos de configuração necessários no local correto nos diretórios do servidor do WildFly para que ele esteja disponível.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Determinar se seu aplicativo está empacotado como um EAR

Se seu aplicativo estiver empacotado como um arquivo EAR, examine o arquivo *application.xml* e capture a configuração.

> [!NOTE]
> Se você deseja ser capaz de dimensionar cada um dos seus aplicativos Web independentemente para um melhor uso dos recursos do AKS, divida o EAR em aplicativos Web separados.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [perform-in-place-testing](includes/perform-in-place-testing.md)]

## <a name="migration"></a>Migração

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>Configurar um armazenamento persistente

Se seu aplicativo exigir armazenamento não volátil, configure um ou mais [Volumes Persistentes](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o Serviço de Kubernetes do Azure, você deve verificar se ele funciona conforme o esperado. Depois de fazer isso, temos algumas recomendações para você que podem tornar seu aplicativo mais nativo de nuvem.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
