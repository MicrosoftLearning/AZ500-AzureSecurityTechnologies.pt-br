---
lab:
  title: Controle de Acesso baseado em função–3.1
  module: Module 01 - Manage Identity and Access
---

# Laboratório&#58; Controle de acesso baseado em função
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito mostrando como os usuários e grupos do Azure são criados. Além disso, como o controle de acesso baseado em função é usado para atribuir funções a grupos. Especificamente, você precisa:

- Crie um grupo de Administradores Seniores contendo a conta de usuário de Joseph Price como membro.
- Crie um grupo de Administradores Juniores contendo a conta de usuário de Isabel Garcia como membro.
- Crie um grupo de Service Desk contendo a conta de usuário de Dylan Williams como seu membro.
- Atribua a função Colaborador de Máquina Virtual ao grupo Service Desk. 

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Criar o grupo Administradores Seniores com a conta de usuário Joseph Price como membro (o portal do Azure). 
- Exercício 2: Criar o grupo Administradores Juniores com a conta de usuário Isabel Garcia como membro (PowerShell).
- Exercício 3: Criar o grupo Service Desk com o usuário Dylan Williams como membro (CLI do Azure). 
- Exercício 4: Atribua a função de Colaborador de Máquina Virtual ao grupo Service Desk.

## Diagrama de arquitetura de controle de acesso baseado em função

![imagem](https://user-images.githubusercontent.com/91347931/157751243-5aa6e521-9bc1-40af-839b-4fd9927479d7.png)

## Instruções

### Exercício 1: Criar o grupo Administradores Seniores com a conta de usuário Joseph Price como membro. 

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Usar o portal do Azure para criar uma conta de usuário para Joseph Price.
- Tarefa 2: Use o portal do Azure para criar um grupo de Administradores Seniores e adicionar a conta de usuário de Joseph Price ao grupo.

#### Tarefa 1: Usar o portal do Azure para criar uma conta de usuário para Joseph Price 

Nesta tarefa, você criará uma conta de usuário para Joseph Price. 

1. Abra um navegador e entre no portal do Azure.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório e a função Administrador Global no locatário do Azure AD associado a essa assinatura.

2. **Na caixa de texto Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra ID** e pressione a **tecla Enter**.

3. **Na folha Visão geral** do locatário do Microsoft Entra ID, na **seção Gerenciar**, selecione **Usuários** e selecione **+ Novo usuário**.

4. No painel **Novo usuário,** verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações, deixando as demais com seus valores padrão:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Joseph**|
   |Nome|**José Preço**|

5. Clique no ícone de cópia ao lado do Nome** de **usuário para copiar o usuário completo.

6. Verifique se a opção Gerar** senha automaticamente está marcada, marque a caixa de seleção Mostrar senha para identificar a ****senha** gerada automaticamente. Você precisaria fornecer essa senha, juntamente com o nome de usuário para Joseph. 

7. Clique em **Criar**.

8. Atualize a **folha Usuários \| Todos os usuários** para verificar se o novo usuário foi criado em seu locatário do Azure AD.

#### Tarefa2: Use o portal do Azure para criar um grupo de Administradores Seniores e adicionar a conta de usuário de Joseph Price ao grupo.

Nesta tarefa, você criará o grupo Administradores* Seniores, adicionará a conta de usuário de Joseph Price ao grupo e o *configurará como proprietário do grupo.

1. No portal do Azure, navegue de volta para a folha que exibe seu locatário do Microsoft Entra ID. 

2. Na **seção Gerenciar** , clique em **Grupos** e selecione **+ Novo grupo**.
 
3. Na folha Novo Contêiner, especifique as seguintes configurações (deixe as demais com seus valores padrão) e selecione OK:

   |Configuração|Valor|
   |---|---|
   |Tipo de grupo|**Segurança**|
   |Nome do grupo|Administradores juniores|
   |Tipo de associação|**Atribuído**|
    
4. Clique no **link Sem proprietários selecionados** , na **folha Adicionar proprietários** , selecione **Joseph Price** e clique em **Selecionar**.

5. Clique no **link Nenhum membro selecionado** , na **folha Adicionar membros** , selecione **Joseph Price** e clique em **Selecionar**.

6. Na folha **Novo**, clique em **Criar**.

> Resultado: você usou o Portal do Azure para criar um usuário e um grupo e atribuiu o usuário ao grupo. 

### Exercício 2: Criar um grupo de Administradores Juniores contendo a conta de usuário de Isabel Garcia como membro.

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Usar o PowerShell para criar uma conta de usuário para Isabel Garcia.
- Tarefa 2: Use o PowerShell para criar o grupo Administradores Juniores e adicionar a conta de usuário de Isabel Garcia ao grupo. 

#### Tarefa 1: Usar o PowerShell para criar uma conta de usuário para Isabel Garcia.

Nesta tarefa, você criará uma conta de usuário para Isabel Garcia usando o PowerShell.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

   >**Nota**: Para colar o texto copiado no Cloud Shell, clique com o botão direito do mouse na janela do painel e selecione **Colar**. Como alternativa, você pode usar a combinação de **teclas Shift+Insert** .

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um objeto de perfil de senha:

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para definir o valor da senha dentro do objeto de perfil:
    ```powershell
    $passwordProfile.Password = "Pa55w.rd1234"
    ```

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para se conectar ao Microsoft Entra ID:

    ```powershell
    Connect-AzureAD
    ```
      
6. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para identificar o nome do locatário do Azure AD: 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

7. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma conta de usuário para Isabel Garcia: 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

8. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para listar os usuários do Azure AD (as contas de Joseph e Isabel devem aparecer na listada): 

    ```powershell
    Get-AzureADUser 
    ```

#### Tarefa2: Use o PowerShell para criar o grupo Administradores Juniores e adicionar a conta de usuário de Isabel Garcia ao grupo.

Nesta tarefa, você criará o grupo Administradores Juniores e adicionará a conta de usuário de Isabel Garcia ao grupo usando o PowerShell.

1. Na mesma sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um novo grupo de segurança chamado Administradores Juniores:
    
    ```powershell
    New-AzureADGroup -DisplayName 'Junior Admins' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
    ```

2. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para listar os grupos em seu locatário do Azure AD (a lista deve incluir os grupos Administradores Sênior e Administradores Juniores):

    ```powershell
    Get-AzureADGroup
    ```

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para obter uma referência à conta de usuário de Isabel Garcia:

    ```powershell
    $user = Get-AzureADUser -Filter "MailNickName eq 'Isabel'"
    ```

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para adicionar a conta de usuário de Isabel ao grupo Administradores Juniores:
    
    ```powershell
    Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para verificar se o grupo Administradores Juniores contém a conta de usuário de Isabel:

    ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins"
    ```

> Resultado: você usou o PowerShell para criar uma conta de usuário e uma conta de grupo e adicionou a conta de usuário à conta de grupo. 


### Exercício 3: Criar um grupo de Service Desk contendo a conta de usuário de Dylan Williams como membro.

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Usar a CLI do Azure para criar uma conta de usuário para Dylan Williams.
- Tarefa 2: Use a CLI do Azure para criar o grupo Service Desk e adicionar a conta de usuário do Dylan ao grupo. 

#### Tarefa 1: Usar a CLI do Azure para criar uma conta de usuário para Dylan Williams.

Nesta tarefa, você criará uma conta de usuário para Dylan Williams.

1. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **Bash** e, quando solicitado, clique em **Confirmar**. 

2. Na sessão Bash no painel Cloud Shell, execute o seguinte para identificar o nome do locatário do Azure AD:

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. Na sessão Bash no painel Cloud Shell, execute o seguinte para criar um usuário, Dylan Williams. Use *seudomínio*.
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. Na sessão Bash no painel Cloud Shell, execute o seguinte para listar contas de usuário do Azure AD (a lista deve incluir contas de usuário de Joseph, Isabel e Dylan)
    
    ```cli
    az ad user list --output table
    ```

#### Tarefa 2: Use a CLI do Azure para criar o grupo Service Desk e adicionar a conta de usuário do Dylan ao grupo. 

Nesta tarefa, você criará o grupo Service Desk e atribuirá Dylan ao grupo. 

1. Na mesma sessão Bash no painel Cloud Shell, execute o seguinte para criar um novo grupo de segurança chamado Service Desk.

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
2. Na sessão Bash no painel Cloud Shell, execute o seguinte para listar os grupos do Azure AD (a lista deve incluir grupos Service Desk, Administradores Seniores e Administradores Juniores):

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

5. Na sessão Bash no painel Cloud Shell, execute o seguinte para adicionar a conta de usuário do Dylan ao grupo Service Desk: 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

6. Na sessão Bash no painel Cloud Shell, execute o seguinte para listar os membros do grupo Service Desk e verificar se ele inclui a conta de usuário do Dylan:

    ```cli
    az ad group member list --group "Service Desk"
    ```

7. Feche o painel do Cloud Shell.

> Resultado: usando a CLI do Azure, você criou uma conta de usuário e um grupo e adicionou a conta de usuário ao grupo. 


### Exercício 4: Atribua a função de Colaborador de Máquina Virtual ao grupo Service Desk.

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um grupo de recursos 
- Tarefa 2: Atribua as permissões de Colaborador de Máquina Virtual do Service Desk ao grupo de recursos.  

#### Tarefa 1: criar um grupo de recursos

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

2. **Na folha Grupos de recursos**, clique em **+ Criar** e especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da assinatura|O nome da sua assinatura do Azure.|
   |Nome do grupo de recursos|**AZ500Lab01**|
   |Localização|**Leste dos EUA**|

3. Clique em **Examinar + criar** e depois **Criar**.

   >Aguarde até o recurso ser implantado. Use o ícone Notificação** (canto superior direito) para acompanhar o **progresso do status da implantação.

4. De volta à **folha Grupos de recursos, atualize a página e verifique se o novo grupo de recursos aparece na lista de grupos de recursos** .


#### Tarefa 2: Atribuir as permissões de Colaborador de Máquina Virtual do Service Desk. 

1. **Na folha Grupos de recursos**, clique na entrada AZ500LAB01 **** grupo de recursos.

2. **Na folha AZ500Lab01**, clique em **Controle de acesso (IAM)** no painel central.

3. Na folha **Controle de acesso (IAM),\| selecione **+ Adicionar** e, no menu suspenso, selecione **Adicionar atribuição de função.

4. **Na folha Adicionar atribuição** de função, especifique as seguintes configurações e clique em **Avançar** após cada etapa:

   |Configuração|Valor|
   |---|---|
   |Função na guia de pesquisa|**Colaborador de Máquina Virtual**|
   |Atribuir acesso a (em Painel de Membros)|**Usuário, grupo ou entidade de serviço**|
   |Selecione Selecionar membros.|Central de Serviços|

5. Selecione **Examinar + atribuir** para criar a atribuição de função.

6. Na página Controle de acesso (IAM), selecione AdicionarAdicionar atribuição de função.

7. Na folha Controle de **acesso (IAM)** do AZ500Lab01\|, na **guia Verificar acesso**, na **caixa de texto Pesquisar por nome ou endereço** de e-mail, digite **Dylan Williams**.

8. Na lista de resultados da pesquisa, selecione a conta de usuário de Dylan Williams e, na **folha Atribuições de Dylan Williams - AZ500Lab01** , visualize a atribuição recém-criada.

9. Feche as **atribuições de Dylan Williams - lâmina AZ500Lab01** .

10. Repita as mesmas duas últimas etapas para verificar o acesso de **Joseph Price**. 

> Resultado: Você atribuiu e verificou permissões RBAC. 

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. 

2. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**. 

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
