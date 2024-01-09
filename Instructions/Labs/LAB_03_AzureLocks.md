---
lab:
  title: Laboratório &#58; Bloqueios do gerenciador de recursos
  module: Module 01 - Manage Identity and Access
---

# Laboratório &#58; Bloqueios do gerenciador de recursos
# Manual do aluno

## Cenário do laboratório 

Você foi solicitado a criar uma prova de conceito mostrando como os bloqueios de recursos podem ser usados para evitar exclusões ou alterações acidentais. Especificamente, você precisa:

- criar um bloqueio ReadOnly

- criar um bloqueio Excluir

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 
 
## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Bloqueios do Gerenciador de Recursos

## Diagrama de bloqueios do Gerenciador de Recursos

![imagem](https://user-images.githubusercontent.com/91347931/157514986-1bf6a9ea-4c7f-4487-bcd7-542648f8dc95.png)

## Instruções

### Exercício 1: Bloqueios do Gerenciador de Recursos

#### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Crie um grupo de recursos e uma conta de armazenamento.
- Tarefa 2: Adicionar um bloqueio ReadOnly na conta de armazenamento. 
- Tarefa 3: Testar o bloqueio ReadOnly. 
- Tarefa 4: Remova o bloqueio ReadOnly e crie um bloqueio Delete.
- Tarefa 5: Testar o bloqueio Excluir.

#### Crie um grupo de recursos e uma conta de armazenamento.

Nesta tarefa, você criará um grupo de recursos e uma conta de armazenamento para o laboratório. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

1. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

1. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um grupo de recursos (verifique com seu instrutor o valor do parâmetro location):

    ```powershell
    New-AzResourceGroup -Name AZ500LAB03 -Location 'EastUS'
    
    Confirm
    Provided resource group already exists. Are you sure you want to update it?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): Y
    ```
1. Na sessão do PowerShell no painel Cloud Shell, digite **Y** e pressione a tecla Enter.

1. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma conta de armazenamento no grupo de recursos recém-criado:
    
    ```powershell
    New-AzStorageAccount -ResourceGroupName AZ500LAB03 -Name (Get-Random -Maximum 999999999999999) -Location  EastUS -SkuName Standard_LRS -Kind StorageV2 
    ```

   >**Nota**: Aguarde até que a conta de armazenamento seja criada. Isso poderá levar alguns minutos. 

1. Feche o painel do Cloud Shell.

#### Tarefa 2: Adicionar um bloqueio ReadOnly na conta de armazenamento. 

Nesta tarefa, você aplica um bloqueio de recurso somente leitura à conta de armazenamento. Proteja os recursos do Azure de exclusão ou modificação acidental. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

1. **Na folha Grupos de recursos**, selecione a entrada AZ500LAB03 **** grupo de recursos.

1. Na página do grupo de recursos, selecione a conta de armazenamento  na tabela Recursos. 

1. Na seção de Configurações, clique em **Configuração**.

1. Clique em **+ Adicionar** e especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome do bloqueio|**Bloqueio somente leitura**|
   |Tipo de bloqueio|**Somente leitura**|

1. Clique em **OK.** 

   >**Nota**: A conta de armazenamento está agora protegida contra eliminação e modificação acidentais.

#### Tarefa 3: Testar o bloqueio ReadOnly 

1. **Na seção Configurações** da folha da conta de armazenamento, clique em **Configuração**.

1. Defina a opção Transferência segura necessária como **Desabilitada **e clique em **Salvar****.**

1. Você deve ser capaz de detectar uma notificação informando **Falha ao atualizar a conta** de armazenamento.

1. Clique no **ícone Notificações** na barra de ferramentas na parte superior do portal do Azure e revise a notificação, que será semelhante ao seguinte texto: 

    > **"Falha ao atualizar a conta de armazenamento 'xxxxxxxx'. Erro: O escopo 'xxxxxxxx' não pode executar a operação de gravação porque o(s) seguinte(s) escopo(s) está(ão) bloqueado(s): '/subscriptions/xxxxx-xxx-xxxx-xxxx-xxxxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'. Remova o bloqueio e tente novamente"**

1. Retorne a **folha Configuração** da conta de armazenamento e clique em **Descartar**. 

1. Na folha Conta de armazenamento, selecione **Visão geral e, na **folha Visão geral****, clique em **Excluir**.

1. **Na folha Excluir conta de armazenamento, digite o nome da conta** de armazenamento para confirmar que você pretende continuar e clique em **Excluir**.

1. Revise a notificação recém-gerada, que será semelhante ao seguinte texto: 

    > **"Falha ao excluir a conta de armazenamento 'xxxxxxx'. Erro: O escopo 'xxxxxxx' não pode executar a operação de exclusão porque o(s) seguinte(s) escopo(s) está(ão) bloqueado(s): '/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'. Remova o bloqueio e tente novamente."**

   >**Nota**: Agora você verificou que um bloqueio ReadOnly interromperá a exclusão acidental e a modificação de um recurso.

#### Tarefa 4: Remova o bloqueio ReadOnly e crie um bloqueio Delete.

Nesta tarefa, você remove o bloqueio ReadOnly da conta de armazenamento e cria um bloqueio Delete. 

1. No portal do Azure, navegue de volta para a folha que exibe as propriedades da conta de armazenamento recém-criada.

1. Na seção **Configurações**, selecione **Bloqueios**.  

1. **Na folha Bloqueios**, clique no **ícone Excluir** na extremidade direita da **entrada Bloqueio Somente Leitura**.

1. Clique em **+ Adicionar** e especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome do bloqueio|**Excluir bloqueio**|
   |Tipo de bloqueio|**Delete (excluir)**|

1. Clique em **OK.** 

#### Tarefa 5: Testar o bloqueio Excluir.

Nesta tarefa, você testará o bloqueio Excluir. Você deve ser capaz de modificar a conta de armazenamento, mas não excluí-la. 

1. **Na seção Configurações** da folha da conta de armazenamento, clique em **Configuração**.

1. Defina a opção Transferência segura necessária como **Desabilitada **e clique em **Salvar****.**

   >**Nota**: Desta vez, a alteração deve ser bem-sucedida.

1. Na folha Conta de armazenamento, selecione **Visão geral e, na **folha Visão geral****, clique em **Excluir**.

1. Revise a notificação semelhante ao seguinte texto: 

    > 'tempstorage1263' não pode ser excluída porque esse recurso ou o pai dele tem um bloqueio de exclusão. Os bloqueios devem ser removidos para que

   >**Nota**: Agora você verificou que um **bloqueio Excluir** permitirá alterações de configuração, mas interromperá a exclusão acidental.

   >**Nota**: Usando Bloqueios de Recursos, você pode implementar uma linha de defesa extra contra alterações acidentais ou maliciosas e/ou exclusão dos recursos mais importantes. Os bloqueios de recursos podem ser removidos por qualquer usuário com a **função Proprietário** , mas isso requer um esforço consciente. Os bloqueios complementam o Controle de Acesso Baseado em Função. 

> Resultados: Neste exercício, você aprendeu a usar bloqueios do Gerenciador de Recursos para proteger os recursos contra modificação e exclusão acidental.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, clique em **Conectar**.

1. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o bloqueio de exclusão:

    ```powershell
    $storageaccountname = (Get-AzStorageAccount -ResourceGroupName AZ500LAB03).StorageAccountName

    $lockName = (Get-AzResourceLock -ResourceGroupName AZ500LAB03 -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).Name

    Remove-AzResourceLock -LockName $lockName -ResourceName $storageAccountName  -ResourceGroupName AZ500LAB03 -ResourceType Microsoft.Storage/storageAccounts -Force
    ```

1.  Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos:

    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB03" -Force -AsJob
    ```

1.  Feche o painel do **Cloud Shell**. 
