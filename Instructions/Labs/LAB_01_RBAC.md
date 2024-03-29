---
lab:
  title: 01 – Controle de Acesso baseado em função
  module: Module 01 - Manage Identity and Access
---

# Laboratório 01: Controle de acesso baseado em função
# Manual de laboratório do aluno

## Cenário do laboratório

Foi solicitado a você criar uma prova de conceito mostrando como os usuários e grupos do Azure são criados. Além disso, como o controle de acesso baseado em função é usado para atribuir funções aos grupos. Especificamente, você precisa:

- Crie um grupo de Administradores Seniores contendo a conta de usuário de Joseph Price como membro.
- Crie um grupo de Administradores Juniores contendo a conta de usuário de Isabel Garcia como membro.
- Crie um grupo de Central de serviços contendo a conta de usuário de Dylan Williams como seu membro.
- Atribua a função Colaborador da Máquina Virtual ao grupo Central de Serviços. 

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório, você realizará os seguintes exercícios:

- Exercício 1: criar o grupo de Administradores Seniores com a conta de usuário Joseph Price como membro (o portal do Azure). 
- Exercício 2: criar o grupo de Administradores Juniores com a conta de usuário Isabel Garcia como membro (PowerShell).
- Exercício 3: criar o grupo Central de Serviços com o usuário Dylan Williams como membro (CLI do Azure). 
- Exercício 4: atribua a função de Colaborador de Máquina Virtual ao grupo Central de Serviços.

## Diagrama de arquitetura de controle de acesso baseado em função

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/506cde9c-5242-4438-a793-f88a5434a2b2)

## Instruções

### Exercício 1: criar o grupo de Administradores Seniores com a conta de usuário Joseph Price como membro. 

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: usar o portal do Azure para criar uma conta de usuário para Joseph Price.
- Tarefa 2: usar o portal do Azure para criar um grupo de Administradores Seniores e adicionar a conta de usuário de Joseph Price ao grupo.

#### Tarefa 1: usar o portal do Azure para criar uma conta de usuário para Joseph Price 

Nesta tarefa, você criará uma conta de usuário para Joseph Price. 

1. Inicie uma sessão no navegador e entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função de Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório e a função Administrador global no locatário do Microsoft Entra associado a essa assinatura.

2. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

3. No painel **Visão geral** do locatário do Microsoft Entra ID, na seção **Gerenciar**, selecione **Usuários**, e então selecione **+ Novo usuário**.

4. No painel **Novo usuário**, verifique se a opção **Criar usuário** está selecionada, e especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Joseph**|
   |Nome|**Joseph Price**|

5. Clique no ícone de copiar ao lado do **Nome de usuário** para copiar o usuário completo.

6. Verifique se a opção **Gerar** a senha automaticamente está marcada, selecione a caixa de seleção **Mostrar senha** para identificar a senha gerada automaticamente. Você precisaria fornecer essa senha, juntamente com o nome de usuário para Joseph. 

7. Clique em **Criar**.

8. Atualize a folha **Usuários \| Todos os usuários** para verificar se o novo usuário foi criado em seu locatário do Microsoft Entra.

#### Tarefa2: use o portal do Azure para criar um grupo de Administradores Seniores e adicionar a conta de usuário de Joseph Price ao grupo.

Nesta tarefa, você criará o grupo de *Administradores Seniores*, adicionará a conta de usuário de Joseph Price ao grupo e o configurará como proprietário do grupo.

1. No portal do Azure, navegue de volta para a folha que exibe seu locatário do Microsoft Entra ID. 

2. Na seção **Gerenciar**, clique em **Grupos**, e então selecione **+ Novo grupo**.
 
3. No painel **Novo Grupo**, especifique as seguintes configurações (deixe as demais com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Tipo de grupo|**Segurança**|
   |Nome do grupo|**Administradores Seniores**|
   |Tipo de afiliação|**Atribuído**|
    
4. Selecione o link **Nenhum proprietário selecionado**, na folha **Adicionar proprietários**, selecione **Joseph Price** e clique em **Selecionar**.

5. Selecione o link **Nenhum membro selecionado**, no painel **Adicionar membros**, selecione **Joseph Price** e clique em **Selecionar**.

6. No painel **Novo Grupo**, selecione **Criar**.

> Resultado: você usou o Portal do Azure para criar um usuário, um grupo e atribuir o usuário ao grupo. 

### Exercício 2: criar um grupo de Administradores Juniores incluindo a conta de usuário de Isabel Garcia como membro.

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: usar o PowerShell para criar uma conta de usuário para Isabel Garcia.
- Tarefa 2: usar o PowerShell para criar o grupo Administradores Juniores e adicione a conta de usuário de Isabel Garcia ao grupo. 

#### Tarefa 1: usar o PowerShell para criar uma conta de usuário para Isabel Garcia.

Nesta tarefa, você criará uma conta de usuário para Isabel Garcia usando o PowerShell.

1. No portal do Azure, abra o Cloud Shell clicando no ícone no canto superior direito. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

   >**Observação**: para colar o texto copiado no Cloud Shell, selecione com o botão direito na janela do painel e selecione **Colar**. Como alternativa, você pode usar a combinação de teclas **Shift+Insert**.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um objeto de perfil de senha:

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para definir o valor da senha dentro do objeto de perfil:
    ```powershell
    $PasswordProfile = @{
      Password = 'Helo123!'
      ForceChangePasswordNextSignIn = $true
      ForceChangePasswordNextSignInWithMfa = $true
    }
    ```

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para se conectar ao Microsoft Entra ID:

    ```powershell
    Connect-MgGraph -Scopes "User.ReadWrite.All", "AuditLog.Read.All", "RoleManagement.Read.Directory"
    ```
      
6. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para identificar o nome do locatário do Microsoft Entra: 

    ```powershell
    $domainName = ((Get-MgOrganization).VerifiedDomains)[0].Name
    ```

7. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma conta de usuário para Isabel Garcia: 

    ```powershell
    New-MgUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -MailNickName 'Isabel' -AccountEnabled
    ```

8. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para listar os usuários do Microsoft Entra ID (as contas de Joseph e Isabel devem aparecer na lista): 

    ```powershell
    Get-MgUser 
    ```

#### Tarefa2: use o PowerShell para criar o grupo Administradores Juniores e adicionar a conta de usuário de Isabel Garcia ao grupo.

Nesta tarefa, você criará o grupo Administradores Juniores e adicionará a conta de usuário de Isabel Garcia ao grupo usando o PowerShell.

1. Na mesma sessão do PowerShell no painel do Cloud Shell, execute o seguinte para **criar um grupo de segurança** chamado Administradores Juniores:
   
   ```powershell
   Get-MgGroup -"DisplayName 'Junior Admins'" -MailEnabled:$false -SecurityEnabled:$true -MailNickName JuniorAdmins
   ```
   
2. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para **listar os grupos** no seu locatário do Microsoft Entra (a lista deve incluir os grupos Administradores Seniores e Administradores Juniores)
   
   ```powershell
   Get-MgGroup
   ```

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para **obter uma referência** à conta de usuário de Mila Moraes:

   ```powershell
   $user =Get-MgUser -Filter "MailNickName eq 'Isabel'"
   ```

4. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para **adicionar a conta de usuário de Mila** ao grupo Administradores Juniores:
   
   ```powershell
    New-MgGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```
   
5. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para **verificar** se o grupo Administradores Juniores contém a conta de usuário de Mila:
   
    ```powershell
    Get-MgGroupMember -GroupDisplayName "Junior Admins"
    ```
 
> Resultado: você usou o PowerShell para criar uma conta de usuário, uma conta de grupo e adicionar a conta de usuário à conta de grupo. 

### Exercício 3: criar um grupo de Central de Serviços incluindo a conta de usuário de Dylan Williams como membro.

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: usar a CLI do Azure para criar uma conta de usuário para Dylan Williams.
- Tarefa 2: usar a CLI do Azure para criar o grupo Central de Serviços e adicionar a conta de usuário de Dylan ao grupo. 

#### Tarefa 1: usar a CLI do Azure para criar uma conta de usuário para Dylan Williams.

Nesta tarefa, você criará uma conta de usuário para Dylan Williams.

1. No menu suspenso no canto superior esquerdo do painel Cloud Shell, selecione **Bash** e, quando solicitado, clique em **Confirmar**. 

2. Na sessão Bash no painel Cloud Shell, execute o seguinte para identificar o nome do locatário do Microsoft Entra:

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. Na sessão Bash no painel Cloud Shell, execute o seguinte para criar o usuário Dylan Williams. Use *yourdomain*.
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. Na sessão Bash no painel Cloud Shell, execute o seguinte para listar contas de usuário do Microsoft Entra ID (a lista deve incluir as contas de usuário de Joseph, Isabel e Dylan):
    
    ```cli
    az ad user list --output table
    ```

#### Tarefa 2: usar a CLI do Azure para criar o grupo Central de Serviços e adicionar a conta de usuário de Dylan ao grupo. 

Nesta tarefa, você criará o grupo Central de Serviços e atribuirá Dylan ao grupo. 

1. Na mesma sessão Bash no painel do Cloud Shell, execute o seguinte para criar um novo grupo de segurança chamado Central de Serviços.

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
2. Na sessão Bash no painel Cloud Shell, execute o seguinte para listar os grupos do Microsoft Entra ID (a lista deve incluir os grupos Central de Serviços, Administradores Seniores e Administradores Juniores):

    ```cli
    az ad group list -o table
    ```

3. Na sessão Bash no painel Cloud Shell, execute o seguinte para obter uma referência à conta de usuário de Dylan Williams: 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

4. Na sessão Bash no painel Cloud Shell, execute o seguinte para obter a propriedade objectId da conta de usuário de Dylan Williams: 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].id' | tr -d '"')
    ```

5. Na sessão Bash no painel Cloud Shell, execute o seguinte para adicionar a conta de usuário do Dylan ao grupo Central de Serviços: 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

6. Na sessão Bash no painel Cloud Shell, execute o seguinte para listar os membros do grupo Central de Serviços e verificar se ele inclui a conta de usuário do Dylan:

    ```cli
    az ad group member list --group "Service Desk"
    ```

7. Feche o painel do Cloud Shell.

> Resultado: usando a CLI do Azure, você criou uma conta de usuário e um grupo e adicionou a conta de usuário a ele. 


### Exercício 4: atribua a função de Colaborador de Máquina Virtual ao grupo Central de Serviços.

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um grupo de recursos. 
- Tarefa 2: atribua as permissões de Colaborador da Máquina Virtual da Central de Serviços ao grupo de recursos.  

#### Tarefa 1: criar um grupo de recursos

1. No portal do Azure, na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

2. No painel **Grupos de recursos**, clique em **+ Criar** e especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da assinatura|o nome da sua assinatura do Azure|
   |Nome do grupo de recursos|**AZ500Lab01**|
   |Localização|**Leste dos EUA**|

3. Clique em **Examinar + criar** e depois **Criar**.

   >**Observação**: aguarde até o grupo de recursos ser implantado. Use o ícone **Notificação** (canto superior direito) para acompanhar o progresso do status da implantação.

4. De volta ao painel **Grupos de recursos**, atualize a página e verifique se o novo grupo de recursos aparece na lista de grupos de recursos.


#### Tarefa 2: atribuir as permissões de Colaborador de Máquina Virtual da Central de Serviços. 

1. No painel **Grupos de recursos**, clique na entrada do grupo de recursos **AZ500LAB01**.

2. No painel **AZ500Lab01**, clique em **Controle de acesso (IAM)** no painel central.

3. No painel **AZ500Lab01 \| Controle de acesso (IAM)** selecione **+ Adicionar** e, no menu suspenso, selecione **Adicionar atribuição de função**.

4. No painel **Adicionar atribuição de função**, especifique as seguintes configurações e clique em **Avançar** após cada etapa:

   |Configuração|Valor|
   |---|---|
   |Função na guia de pesquisa|**Colaborador de Máquina Virtual**|
   |Atribuir acesso a (no Painel de Membros)|**Usuário, grupo ou entidade de serviço**|
   |Selecione (+ Selecionar Membros)|**Central de Serviços**|

5. Clique em **Examinar + atribuir** duas vezes para criar a atribuição de função.

6. No painel **Controle de acesso (IAM)**, selecione **Atribuições de função**.

7. No painel **AZ500Lab01 \| Controle de acesso (IAM)**, na guia **Verificar acesso**, na caixa de texto **Pesquisar por nome ou endereço de e-mail**, digite **Dylan Williams**.

8. Na lista de resultados da pesquisa, selecione a conta de usuário de Dylan Williams e, na folha **Atribuições de Dylan Williams – AZ500Lab01**, visualize a atribuição recém-criada.

9. Feche o painel **atribuições de Dylan Williams – AZ500Lab01**.

10. Repita as mesmas duas últimas etapas para verificar o acesso de **Joseph Price**. 

> Resultado: você atribuiu e verificou permissões RBAC. 

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados.

1. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. 

2. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**. 

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
