---
title: Instalação do Kit de Ferramentas do Azure para o Eclipse
description: Saiba como instalar o Kit de Ferramentas do Azure para o plug-in Eclipse para criar e implantar aplicativos de nuvem no Azure.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: b269be52157516b97905b0a917fec856127a761f
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81674332"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalação do Kit de Ferramentas do Azure para o Eclipse

Há duas maneiras de instalar o Azure Toolkit for Eclipse:

  - [Marketplace do Eclipse](#eclipse-marketplace)
  - [Instalar novo software](#install-new-software)

> [!NOTE] 
> 
> O Kit de Ferramentas do Azure para Eclipse é um projeto de código-fonte aberto, cujo código-fonte está disponível de acordo com a Licença do MIT no site do projeto no GitHub na seguinte URL: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="eclipse-marketplace"></a>Marketplace do Eclipse

1. Arraste o botão a seguir para seu workspace do Eclipse em execução.

    [![Arraste-o para o workspace do Eclipse* em execução. *Exige o Cliente do Eclipse Marketplace](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Arraste-o para o workspace do Eclipse* em execução. *Exige o Cliente do Eclipse Marketplace")

2. Caso contrário, também será possível pesquisar e instalar o **plug-in Azure Toolkit for Eclipse** em **Ajuda/Marketplace do Eclipse**.

    ![Marketplace](media/installation/marketplace.png)

## <a name="install-new-software"></a>Instalar novo software

1. Inicie o Eclipse.

1. Clique no menu **Ajuda** e, em seguida, clique em **Instalar Novo Software**, conforme mostrado na ilustração a seguir.

   ![Instalação do Kit de Ferramentas do Azure para o Eclipse][01]

1. Na caixa de diálogo **Software Disponível** na caixa de texto **Trabalhar com**, digite `http://dl.microsoft.com/eclipse/` seguido pela tecla **Enter**.

1. No painel **Nome**, marque **Kit de Ferramentas do Azure para Java** e desmarque **Entrar em contato com todos os sites de atualização durante a instalação para encontrar o software necessário**. Sua tela será semelhante à seguinte:

   ![Instalação do Kit de Ferramentas do Azure para o Eclipse][02]

1. Se você expandir o **Kit de Ferramentas do Azure para Eclipse**, verá uma lista de componentes que serão instalados; por exemplo:

   | Recurso | Descrição | 
   |---|---| 
   | **Plug-in do Application Insights para Java** | Permite que você use os serviços de registro em log e análise de telemetria do Azure para seus aplicativos e instâncias do servidor. | 
   | **Plug-in Comum do Azure** | fornece a funcionalidade comum necessária para outros componentes do kit de ferramentas. | 
   | **Ferramentas de Contêiner do Azure para Eclipse** | Permite que você crie e implante um .WAR como um contêiner do Docker em uma máquina do docker. | 
   | **Contêineres do Azure para Eclipse** | Permite que você implante um artefato .WAR ou .JAR como um contêiner do Docker em uma máquina virtual do Azure. | 
   | **Gerenciador do Azure para Eclipse** | Fornece uma interface do gerenciador para gerenciar os recursos do Azure. | 
   | **Microsoft JDBC Driver 6.1 para SQL Server** | Fornece a API JDBC para o SQL Server e o Banco de Dados SQL do Microsoft Azure para o Java Platform Enterprise Edition 8. | 
   | **Pacote para as Bibliotecas do Microsoft Azure para Java** | Fornece APIs para acessar os serviços do Microsoft Azure, como armazenamento, barramento de serviço, execução do serviço etc. | 

1. Clique em **Próximo**. (Se você experimentar atrasos incomuns ao instalar o kit de ferramentas, certifique-se de que a opção **Contatar todos os sites de atualização durante a instalação para encontrar o software necessário** está desmarcada.)

1. No diálogo **Instalar Detalhes**, clique em **Avançar**.

   ![Revisão dos detalhes de instalação][03]

1. No diálogo **Examinar Licenças** , leia os termos dos contratos de licença. Se você aceitar os termos dos contratos de licença, clique em **Aceito os termos dos contratos de licença** e clique em **Concluir**. (As etapas restantes supõem que você aceite os termos dos contratos de licença. Se você não aceitar os termos dos contratos de licença, saia do processo de instalação.)

   ![Examinar Licenças][04]

   O Eclipse baixará e instalará os pacotes necessários.

   ![Progresso da Instalação][05]

1. Se for solicitado a reiniciar o Eclipse para concluir a instalação, clique em **Sim**.

   ![Reinicie o prompt][06]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[01]: media/installation/eclipse-installation-01.png
[02]: media/installation/eclipse-installation-02.png
[03]: media/installation/eclipse-installation-03.png
[04]: media/installation/eclipse-installation-04.png
[05]: media/installation/eclipse-installation-05.png
[06]: media/installation/eclipse-installation-06.png