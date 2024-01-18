---
lab:
  title: 02 - Política do Azure
  module: Module 01 - Manage Identity and Access
---

# Laboratório &#58; Políticas do Azure
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito mostrando como a política do Azure pode ser usada. Especificamente, você precisa:

- Crie uma política de Locais Permitidos que garanta que os recursos sejam criados apenas em uma região específica.
- Teste para garantir que os recursos sejam criados apenas no local Permitido

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório, você realizará as seguintes tarefas:

- Exercício 1: Implementar a Política do Azure. 

## Diagrama de Política do Azure

![imagem](https://user-images.githubusercontent.com/91347931/157511920-19c1f06c-86bd-440d-80ac-d96aa27aefff.png)

## Instruções

### Exercício 1: Implementar a Política do Azure

#### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- 1. Criar um grupo de recursos do Azure 
- Tarefa 2: Criar uma atribuição de política de Locais Permitidos.
- Tarefa 3: Verifique se a atribuição de política Locais Permitidos está funcionando. 

#### Tarefa 1: criar um grupo de recursos 

Nesta tarefa, você criará um novo grupo de recursos. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

1. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

1. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um grupo de recursos (verifique com seu instrutor o valor do parâmetro location):

    ```powershell
    New-AzResourceGroup -Name AZ500LAB02 -Location 'East US'
    
    Confirm
    Provided resource group already exists. Are you sure you want to update it?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): Y
    ```
1. Na sessão do PowerShell no painel Cloud Shell, digite **Y** e pressione a tecla Enter.

1. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para listar grupos de recursos para verificar se o novo grupo de recursos foi criado:

    ```powershell
    Get-AzResourceGroup | format-table
    ```

1. Fechar o Cloud Shell

#### Tarefa 2: Criar uma atribuição de política de Locais Permitidos.

Nesta tarefa, você criará uma atribuição de política de Locais Permitidos e especificará quais regiões do Azure a política pode usar. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Política** e pressione a **tecla Enter**.

1. **Na folha Política**, na **seção Criação**, selecione **Definições**.

1. Reserve um minuto para navegar pelas definições internas. Use a lista suspensa Categoria** para filtrar a **lista de políticas.

1. **Na caixa de texto Pesquisar**, digite **Locais permitidos**. 

   >**Observação**: a política Locais** permitidos permite restringir a **localização de recursos, não de grupos de recursos. Para restringir locais de grupos de recursos, você pode usar a **política Locais permitidos para grupos de recursos** .

1. Clique na definição de **política Locais** permitidos para exibir seus detalhes.. 

   >**Nota**: Esta definição de política usa uma matriz de locais como parâmetros. Uma regra é como uma instrução IF-THEN. A cláusula 'if' verifica se o local do recurso está incluído na lista de parâmetros e, caso contrário, a cláusula 'then' nega a criação do recurso ou, para recursos existentes, os marca como não compatíveis.

1. **Na folha Locais permitidos**, clique em **Atribuir**.

1. **Na guia Noções básicas** da folha Locais permitidos **, clique no botão Reticências (...) ao lado da ****caixa de texto Escopo** e, na **folha Escopo**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Subscription|O nome da sua assinatura do Azure.|
   |Grupo de recursos|**AZ500LAB02**|

1. Clique em **Selecionar**.

1. **Na folha Locais permitidos, na **guia Noções** básicas**, especifique as seguintes configurações (deixe outras pessoas com seus valores defenaut):

   |Configuração|Valor|
   |---|---|
   |Nome da atribuição|**Permitir que o Reino Unido do Sul AZ500LAB02**|
   |Descrição|**Permitir que recursos sejam criados no Sul do Reino Unido apenas para AZ500LAB02**|
   |Imposição de política|**Enabled**|

1. Clique em **Avançar**.

1. **Na guia Parâmetros** da **folha Locais permitidos, na **lista suspensa Locais permitidos****, selecione **Sul** do Reino Unido como o único local permitido. 

   >**Nota**: Você pode selecionar mais de um local. Se a política exigisse um conjunto diferente de parâmetros, essa guia forneceria essas seleções. 

1. Clique em **Revisar + criar, seguido de **Criar** para criar** a atribuição de política. 

   >**Observação**: você verá uma notificação de que a atribuição foi bem-sucedida e que a atribuição pode levar cerca de 30 minutos para ser concluída.

   >**Observação**: o motivo pelo qual a atribuição de política do Azure pode levar até 30 minutos para entrar em vigor é que é necessário replicar globalmente. Este processo normalmente leva alguns minutos.  Se a próxima tarefa falhar, basta aguardar alguns minutos e tentar suas etapas novamente.

#### Tarefa 3: Testar a atribuição de política Locais Permitidos

Nesta tarefa, você testará a atribuição de política Locais Permitidos. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Redes** virtuais e pressione a **tecla Enter**.

1. Na folha **Rede Virtual**, selecione  **Criar**.

   >**Nota**: Primeiro, você tentará criar uma rede virtual no leste dos EUA. Como este não é um local permitido, a solicitação deve ser bloqueada. 

1. Na guia **Noções básicas** da folha **Criar máquina virtual** , defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Resource group|**AZ500LAB02**|
    |Nome|myVnet|
    |Region|**Leste dos EUA**|

1. Clique em **Revisar + criar**. 

1. **Na guia Revisar + criar** da **folha Criar rede **virtual, observe a **mensagem Falha** na validação. 

    > **Nota**: Se o aviso Falha** na **validação não aparecer, clique em **Anterior** e aguarde mais alguns minutos.

1. **Na guia Noções básicas, clique no link da mensagem de erro para abrir a **folha Atribuição de** diretiva**. Você verá a atribuição de política que restringe o local.

1. Feche a **folha Atribuição de Política**, na folha Criar rede** virtual, clique na guia Noções básicas** e, na ******lista suspensa Região**, selecione **Sul** do Reino Unido.

1. Clique em Revisar + criar **, verifique se a validação foi aprovada, clique em**** Criar** e verifique se a rede virtual foi criada com êxito. 

> Resultados do exercício: neste exercício, você aprendeu a aplicar uma política do Azure selecionando uma definição de política interna e atribuindo-a a um grupo de recursos.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, clique em **Conectar**.

1. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB02" -Force -AsJob
    ```
1.  Feche o painel do **Cloud Shell**. 
  
1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Política** e pressione a **tecla Enter**.

1. Na seção Criação, selecione Atribuições.

1. Na lista de atribuições, selecione o nome da política de **Locais Permitidos** que você criou neste laboratório.

1. Na atribuição de política, selecione Excluir atribuição e, em seguida, selecione ****Sim**.**
