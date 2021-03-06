---
title: Guia de Início Rápido – Configurar o Terraform usando o Azure Cloud Shell
description: Nesse início rápido, você aprende a como instalar e configurar o Terraform para criar recursos do Azure.
keywords: azure devops terraform install configure cloud shell init plan apply execution portal login rbac service principal automated script
ms.topic: quickstart
ms.date: 08/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: d8cec2954357269b5605a7b35c96030b8e8b5fa0
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241168"
---
# <a name="quickstart-configure-terraform-using-azure-cloud-shell"></a>Início Rápido: Configurar o Terraform usando o Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Este artigo descreve como começar a usar o [Terraform no Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Autenticar-se no Azure usando `az login`
> * Criar uma entidade de serviço do Azure usando a CLI do Azure
> * Autenticar-se no Azure usando uma entidade de serviço
> * Definir a assinatura do Azure atual – a ser usado se você tiver várias assinaturas
> * Escrever um script Terraform para criar um grupo de recursos do Azure
> * Criar e aplicar um plano de execução Terraform
> * Usar o sinalizador `terraform plan -destroy` para reverter um plano de execução

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Configurar seu ambiente

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Se você ainda não entrou, o portal do Azure exibe uma lista de contas Microsoft disponíveis. Selecione uma conta Microsoft associada a uma ou mais assinaturas ativas do Azure e insira suas credenciais para continuar.

1. Abra o Azure Cloud Shell.

    ![Accessar o Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Se você ainda não usou o Cloud Shell, defina as configurações de ambiente e armazenamento. Este artigo usa o ambiente de Bash.

**Observações**:
- O Cloud Shell tem automaticamente a versão mais recente do Terraform instalada. Além disso, o Terraform usa automaticamente as informações da assinatura atual do Azure. Como resultado, não há nenhuma instalação ou configuração necessária.

## <a name="authenticate-to-azure"></a>Autenticar-se no Azure

O Cloud Shell é automaticamente autenticado sob a conta Microsoft que você usou para entrar no portal do Azure. Se sua conta tiver várias assinaturas do Azure, você poderá [alternar para uma de suas outras assinaturas](#set-the-current-azure-subscription).

O Terraform dá suporte a várias opções de autenticação no Azure. As seguintes técnicas são abordadas neste artigo:

- Ao usar o Terraform interativamente, é recomendável [autenticar via conta Microsoft](#authenticate-via-microsoft-account).
- Ao usar o Terraform do código, [autenticar via entidade de serviço do Azure](#authenticate-via-azure-service-principal) é um modo recomendado.

### <a name="authenticate-via-microsoft-account"></a>Autenticar via conta Microsoft

A chamada a `az login` sem nenhum parâmetro exibe uma URL e um código. Navegue até a URL, insira o código e siga as instruções para entrar no Azure usando sua conta Microsoft. Quando você estiver conectado, volte ao portal.

```azurecli
az login
```

**Observações**:

- Após o logon bem-sucedido, `az login` exibe uma lista das assinaturas do Azure associadas à conta Microsoft conectada.
- Uma lista de propriedades é exibida para cada assinatura do Azure disponível. A propriedade `isDefault` identifica qual assinatura do Azure você está usando. Para saber como alternar para outra assinatura do Azure, consulte a seção [Definir a assinatura do Azure atual](#set-the-current-azure-subscription).

### <a name="authenticate-via-azure-service-principal"></a>Autenticar via entidade de serviço do Azure

**Crie uma entidade de serviço do Azure**: para fazer logon em uma assinatura do Azure usando uma entidade de serviço, primeiro você precisará ter acesso a uma entidade de serviço. Se você já tiver uma entidade de serviço, ignore esta parte da seção.

Ferramentas automatizadas que implantam ou usam os serviços do Azure, como o Terraform, sempre têm permissões restritas. Em vez de os aplicativos entrarem como um usuário com privilégio total, o Azure oferece as entidades de serviço. Mas e se você não tiver uma entidade de serviço com a qual entrar? Nesse cenário, você pode entrar usando suas credenciais de usuário e, em seguida, criar uma entidade de serviço. Depois que a entidade de serviço for criada, você poderá usar suas informações para futuras tentativas de logon.

Há muitas opções ao [criar uma entidade de serviço com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?). Para este artigo, usaremos [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) para criar uma entidade de serviço com uma função de **Colaborador**. A função de **Colaborador** (a padrão) tem permissões completas de leitura e gravação em uma conta do Azure. Para obter mais informações sobre o Controle de Acesso Baseado em Função (RBAC) e funções, confira [RBAC: funções internas](/azure/active-directory/role-based-access-built-in-roles).

Insira o comando a seguir, substituindo `<subscription_id>` pela ID da conta de assinatura que deseja usar.

```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

**Observações**:

- Após a conclusão bem-sucedida, `az ad sp create-for-rbac` exibe vários valores. Os valores `name`, `password` e `tenant` são usados na próxima etapa.
- Se for perdida, a senha não poderá ser recuperada. Por isso, você deve armazenar sua senha em um local seguro. Se você esquecer a senha, você precisará [redefinir as credenciais da entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).

**Entre usando uma entidade de serviço Azure**: Na chamada a seguir a `az login`, substitua os espaços reservados por informações da entidade de serviço.

```azurecli
az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
```

## <a name="set-the-current-azure-subscription"></a>Definir a assinatura atual do Azure

Uma conta Microsoft pode ser associada a várias assinaturas do Azure. As seguintes etapas descrevem como você pode alternar entre suas assinaturas:

1. Para ver a assinatura atual do Azure, use [az account show](/cli/azure/account#az-account-show).

    ```azurecli
    az account show
    ```

1. Se você tiver acesso a várias assinaturas do Azure disponíveis, use [az account list](/cli/azure/account#az-account-list) para exibir uma lista de valores de ID de nome de assinatura:

    ```azurecli
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Para usar uma assinatura específica do Azure para a sessão atual do Cloud Shell, use [az account set](/cli/azure/account#az-account-set). Substitua o espaço reservado `<subscription_id>` com a ID (ou nome) da assinatura que deseja usar:

    ```azurecli
    az account set --subscription="<subscription_id>"
    ```

    **Observações**:

    - A chamada a `az account set` não exibe os resultados da alternância para a assinatura especificada do Azure. No entanto, você pode usar `az account show` para confirmar se a assinatura atual do Azure foi alterada.

## <a name="create-a-terraform-configuration-file"></a>Criar um arquivo de configuração Terraform

Nesta seção, você aprenderá a criar um arquivo de configuração do Terraform que cria um grupo de recursos do Azure.

1. Altere os diretórios para o compartilhamento de arquivos montado em que seu trabalho no Cloud Shell é mantido. Para obter mais informações sobre como o Cloud Shell mantém seus arquivos, consulte [Conectar seu armazenamento de Arquivos do Microsoft Azure](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)

    ```bash
    cd clouddrive
    ```

1. Crie um diretório para manter os arquivos Terraform para esta demonstração.

    ```bash
    mkdir QuickstartTerraformTest
    ```

1. Altere os diretórios para o diretório de demonstração.

    ```bash
    cd QuickstartTerraformTest
    ```

1. Usando seu editor favorito, crie um arquivo de configuração do Terraform. Este artigo usa o [editor interno do Cloud Shell](/azure/cloud-shell/using-cloud-shell-editor).

    ```bash
    code QuickstartTerraformTest.tf
    ```
 
1. Cole o seguinte HCL no novo arquivo.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }
    resource "azurerm_resource_group" "rg" {
            name = "QuickstartTerraformTest-rg"
            location = "eastus"
    }
    ```

    **Observações**:

    - O bloco `provider` especifica que o [provedor do Azure (`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html) é usado.
    - No bloco do provedor `azurerm`, os atributos `version` e `features` são definidos. Como o comentário diz, seu uso é específico da versão. Para obter mais informações sobre como definir esses atributos para seu ambiente, consulte [v 2.0 do provedor AzureRM](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - A única [declaração de recurso](https://www.terraform.io/docs/configuration/resources.html) é para um tipo de recurso de [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Os dois argumentos necessários para `azure_resource_group` são `name` e `location`.

1. Salve o arquivo ( **&lt;Ctrl>S**).

1. Saia do editor ( **&lt;CTRL> Q**).

## <a name="create-and-apply-a-terraform-execution-plan"></a>Criar e aplicar um plano de execução Terraform

Nesta seção, você cria um *plano de execução* e o aplica à sua infraestrutura de nuvem.

1. Inicialize a implantação do Terraform com [terraform init](https://www.terraform.io/docs/commands/init.html). Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

    ```bash
    terraform init
    ```

1. Execute [plano do Terraform](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução do seu arquivo de configuração do Terraform.

    ```bash
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

    **Observações:**
    - O comando `terraform plan` cria um plano de execução, mas não o executa. Em vez disso, ele determina quais ações são necessárias para criar a configuração especificada em seus arquivos de configuração. Esse padrão permite que você verifique se o plano de execução corresponde às suas expectativas antes de fazer qualquer alteração nos recursos reais.
    - O parâmetro opcional `-out` permite que você especifique um arquivo de saída para o plano. Usar o parâmetro `-out` garante que o plano que você examinou seja exatamente o que é aplicado.
    - Para ler mais sobre a persistência de planos de execução e segurança, confira a [seção de aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Execute a [aplicação do Terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o plano de execução.

    ```bash
    terraform apply QuickstartTerraformTest.tfplan
    ```

1. Depois que o plano de execução for aplicado, você poderá testar se o grupo de recursos foi criado com êxito usando [az group show](/cli/azure/group?#az-group-show).

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Observações**:

    - Se ele for bem-sucedido, `az group show` exibirá várias propriedades do grupo de recursos recém-criado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo.

1. Execute o [plano do Terraform](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução para destruir os recursos indicados no arquivo de configuração do Terraform.

    ```bash
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **Observações**:
    - O comando `terraform plan` cria um plano de execução, mas não o executa. Em vez disso, ele determina quais ações são necessárias para criar a configuração especificada em seus arquivos de configuração. Esse padrão permite que você verifique se o plano de execução corresponde às suas expectativas antes de fazer qualquer alteração nos recursos reais.
    - O parâmetro `-destroy` gera um plano para destruir os recursos.
    - O parâmetro opcional `-out` permite que você especifique um arquivo de saída para o plano. Usar o parâmetro `-out` garante que o plano que você examinou seja exatamente o que é aplicado.
    - Para ler mais sobre a persistência de planos de execução e segurança, confira a [seção de aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Execute a [aplicação do Terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o plano de execução.

    ```bash
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. Verifique se o grupo de recursos foi excluído usando [az group show](/cli/azure/group?#az-group-show).

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Observações**:
    - Se ele for bem-sucedido, `az group show` exibirá uma mensagem informando que o grupo de recursos não existe.

1. Altere os diretórios para o diretório pai e remova o diretório de demonstração. O parâmetro `-r` remove o conteúdo do diretório antes de remover o diretório. O conteúdo do diretório inclui o arquivo de configuração que você criou anteriormente e os arquivos de estado do Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](create-linux-virtual-machine-with-infrastructure.md)