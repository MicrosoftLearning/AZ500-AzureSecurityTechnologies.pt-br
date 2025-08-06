---
lab:
  title: 07 – Key Vault (implementação de dados seguros com a configuração do Always Encrypted)
  module: Module 01 - Implement and manage enforcement of cloud governance policies
---

# Laboratório 07: Key Vault (implementação de dados seguros com a configuração do Always Encrypted)

# Manual de laboratório do aluno

## Cenário do laboratório

Foi solicitado que você crie um aplicativo de prova de conceito que usa o suporte do Banco de Dados SQL do Azure para a funcionalidade Always Encrypted. Todos os segredos e chaves usados nesse cenário devem ser armazenados no Key Vault. O aplicativo deve ser registrado no Microsoft Entra ID para melhorar sua postura de segurança. Para atingir esses objetivos, a prova de conceito deve incluir:

- Como criar um Azure Key Vault e armazenar chaves e segredos no cofre.
- Crie um Banco de Dados SQL e criptografe o conteúdo de colunas em tabelas de banco de dados usando o Always Encrypted.

>**Observação**: para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região para ser usada na aula. 

Para manter o foco nos aspectos de segurança do Azure relacionados à criação dessa prova de conceito, você começará a partir de uma implantação de modelo automatizada do ARM, configurando uma máquina virtual com o Visual Studio 2019 e o SQL Server Management Studio 19.

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: implantar a infraestrutura base a partir de um modelo do ARM
- Exercício 2: configurar o recurso Key Vault com uma chave e um segredo
- Exercício 3: configurar um banco de dados SQL do Azure e um aplicativo controlado por dados
- Exercício 4: demonstrar o uso do Azure Key Vault na criptografia do banco de dados SQL do Azure

## Diagrama do Key Vault

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/38c4ba6d-2fc7-45e5-b9a2-d5dbb4fbbcbc)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **\\Allfiles\\Labs\\10\\program.cs**

### Estimativa de tempo total do laboratório: 60 minutos

### Exercício 1: implantar a infraestrutura base a partir de um modelo do ARM

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: implantar uma VM do Azure e um banco de dados SQL do Azure

#### Tarefa 1: implantar uma VM do Azure e um banco de dados SQL do Azure

Nesta tarefa, você implantará uma VM do Azure, que instalará automaticamente o Visual Studio 2019 e o SQL Server Management Studio 19 como parte da implantação. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. Na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado** e pressione a tecla **Enter**.

3. Na folha **Implantação personalizada**, selecione a opção **Criar seu próprio modelo no editor**.

4. Na folha **Editar modelo**, selecione **Carregar arquivo**, localize o arquivo **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** e escolha **Abrir**.

5. Na folha **Editar modelo**, selecione **Salvar**.

6. Na folha **Implantação personalizada**, em **Escopo de implantação**, verifique se as seguintes configurações estão definidas (deixe as demais com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Assinatura|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|selecione **Criar novo** e digite o nome **AZ500LAB10**|
   |Localização|**Leste dos EUA**|
   |Nome de Usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 2 > Tarefa 1 > Etapa 3.**|
   
    >**Observação**: embora seja possível alterar as credenciais administrativas usadas para fazer logon na máquina virtual, não é necessário.

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

8. Selecione o botão **Revisar e Criar** e confirme a implantação clicando no botão **Criar**. 

    >**Observação**: isso inicia a implantação da VM do Azure e do Banco de Dados SQL do Azure necessários para este laboratório. 

    >**Observação**: não espere a conclusão da implantação do modelo do ARM e passe para o próximo exercício. A implantação pode levar entre **20 – 25 minutos**. 

#### Instalar o modelo personalizado do az500-10-DB.json

1. Na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado** e pressione a tecla **Enter**.

2. No painel **Implantação personalizada**, clique na opção **Criar seu próprio modelo no editor**.

3. Na folha **Editar modelo**, clique em **Carregar arquivo**, localize o arquivo **\Allfiles\Labs\10\az-500-10_DB.json** e clique em **Abrir**.

4. Verifique se está selecionado o Grupo de Recursos correto.

5. Defina a **Senha de administrador** igual à senha usada para a etapa anterior.

### Exercício 2: configurar o recurso Key Vault com uma chave e um segredo

>**Observação**: para todos os recursos neste laboratório, estamos usando a região **Leste (EUA)**. Confirme com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar e configurar um Key Vault
- Tarefa 2: adicionar uma chave ao Key Vault
- Tarefa 3: adicionar um segredo ao Key Vault

#### Tarefa 1: criar e configurar um Key Vault

Nesta tarefa, você criará um recurso do Azure Key Vault. Você também configurará as permissões do Azure Key Vault.

1. Abra o Cloud Shell selecionando o primeiro ícone (ao lado da barra de pesquisa) no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um Azure Key Vault no grupo de recursos **AZ500LAB10**. (Se você escolheu outro nome para o grupo de recursos deste laboratório na Tarefa 1, use esse nome para essa tarefa também). O nome do Key Vault precisa ser exclusivo. Guarde o nome que você escolheu. Você vai precisar dele durante este laboratório.  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location -DisableRbacAuthorization
    ```

    >**Observação**: a saída do último comando exibirá o nome e o URI do cofre. O URI do cofre está no formato `https://<vault_name>.vault.azure.net/`

4. Feche o painel do Cloud Shell. 

5. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

6. Na folha **Grupos de recursos**, na lista de grupos de recursos, selecione a entrada **AZ500LAB10** (ou o nome escolhido anteriormente para o grupo de recursos).

7. Na folha Grupo de Recursos, selecione a entrada que representa o Key Vault recém-criado. 

8. Na folha Key Vault, na seção **Visão Geral**, selecione **Políticas de Acesso ** e escolha **+ Criar**.

9. Na folha **Criar uma política de acesso**, especifique as seguintes configurações (deixe as demais com seus valores padrão): 

    |Configuração|Valor|
    |----|----|
    |Configurar do modelo (opcional)|**Gerenciamento de Chave, Segredo e Certificado**|
    |Permissões de chave|escolha **Selecionar tudo**, resultando em um total de **9 permissões** selecionadas|
    |Permissões de chave/Operações criptográficas|clique em **Assinar**, resultando em um total de **1 permissão** selecionada|
    |Permissões de segredo|clique em **Selecionar tudo**, resultando em um total de **7 permissões** selecionadas|
    |Permissões de certificado|clique em **Selecionar tudo**, resultando em um total de **15 permissões** selecionadas|
    |Selecionar entidade de segurança|clique em **Nenhum selecionado**, na folha **Entidade de segurança**, selecione sua conta de usuário e clique em **Avançar**|
    |Aplicativo (opcional)|Clique em **Avançar**|
    |Examinar + criar|Clique em **Criar**|
    
    >**Observação**: a operação Revisar + criar anterior retorna à página Políticas de acesso que lista aplicativo, email e permissões de chave, de segredo e de certificado.
      
#### Tarefa 2: adicionar uma chave ao Key Vault

Nesta tarefa, você adicionará uma chave ao Key Vault e exibirá informações sobre a chave. 

1. No portal do Azure, abra a sessão PowerShell no painel do Cloud Shell.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no lado superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell, no painel Cloud Shell, execute o seguinte para adicionar uma chave protegida por software ao Key Vault: 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**Observação**: o nome da chave é **MyLabKey**

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para verificar se a chave foi criada:

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para exibir o identificador de chave:

    ```powershell
    $key.key.kid
    ```

6. Minimize o painel do Cloud Shell. 

7. De volta ao portal do Azure, na folha Key Vault, na seção **Objetos**, selecione **Chaves**.

8. Na lista de chaves, selecione a entrada **MyLabKey** e, em seguida, na folha **MyLabKey**, selecione a entrada que representa a versão atual da chave.

    >**Observação**: examine as informações sobre a chave que você criou.

    >**Observação**: você pode fazer referência a qualquer chave usando o identificador de chave. Para obter a versão mais atual, faça referência a `https://<key_vault_name>.vault.azure.net/keys/MyLabKey` ou obtenha a versão específica com: `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### Tarefa 3: adicionar um segredo ao Key Vault

1. Volte para o painel do Cloud Shell.

2. Na sessão do PowerShell, no painel do Cloud Shell, execute o seguinte para criar uma variável com um valor de cadeia de caracteres seguro:

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

3.  Na sessão do PowerShell, no painel do Cloud Shell, execute o seguinte para adicionar o segredo ao cofre:

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**Observação**: o nome do segredo é SQLPassword. 

4.  Na sessão do PowerShell, no painel do Cloud Shell, execute o seguinte para verificar se o segredo foi criado.

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

5. Minimize o painel do Cloud Shell. 

6. No portal do Azure, navegue de volta para a folha Key Vault, na seção **Objetos**, selecione **Segredos**.

7. Na lista de segredos, selecione a entrada **SQLPassword** e, em seguida, na folha **SQLPassword**, selecione a entrada que representa a versão atual do segredo.

    >**Observação**: examine as informações sobre o segredo que você criou.

    >**Nota**: para obter a versão mais atual de um segredo, faça referência a `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>` ou obtenha uma versão específica, faça referência a `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>`


### Exercício 3: configurar um banco de dados SQL do Azure e um aplicativo controlado por dados

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: habilitar um aplicativo cliente para acessar o serviço do Banco de Dados SQL do Azure.
- Tarefa 2: criar uma política que permita que o aplicativo acesse o Key Vault.
- Tarefa 3: recuperar a cadeia de conexão ADO.NET do banco de dados SQL do Azure 
- Tarefa 4: fazer logon na VM do Azure executando o Visual Studio 2019 e o SQL Management Studio 19
- Tarefa 5: criar uma tabela no Banco de Dados SQL e selecionar colunas de dados para criptografia


#### Tarefa 1: habilitar um aplicativo cliente para acessar o serviço do Banco de Dados SQL do Azure. 

Nesta tarefa, você habilitará um aplicativo cliente para acessar o serviço do Banco de Dados SQL do Azure. Isso será feito configurando a autenticação necessária e adquirindo a ID e o segredo do aplicativo que você precisará para autenticar seu aplicativo.

1. Na caixa de texto **Pesquisar recursos, serviços e documentos**, na parte superior da página do portal do Azure, digite **Registros de aplicativo** e pressione a tecla **Enter**.

2. Na folha **Registros de Aplicativo**, selecione **+ Novo registro**. 

3. Na folha **Registrar um aplicativo**, especifique as seguintes configurações (deixe as demais com seus valores padrão):

    |Configuração|Valor|
    |----|----|
    |Nome|**sqlApp**|
    |Redirecionar URI (opcional)|**Web** e **https://sqlapp**|

4. Na folha **Registrar um aplicativo**, selecione **Registrar**. 

    >**Observação**: assim que o registro for concluído, o navegador redirecionará você automaticamente para a folha **sqlApp**. 

5. Na folha **sqlApp**, identifique o valor da **ID do Aplicativo (cliente)**. 

    >**Observação**: guarde esse valor. Isso será necessário na próxima tarefa.

6. Na folha **sqlApp**, na seção **Gerenciar**, selecione **Certificados e segredos**.

7. Na folha **sqlApp | Certificados e segredos** / seção **Segredos do Cliente**, selecione **+ Novo segredo do cliente**

8. No painel **Adicionar um segredo do cliente**, especifique as seguintes configurações:

    |Configuração|Valor|
    |----|----|
    |Descrição|**Key1**|
    |Expira|**12 meses**|
    
9. Clique em **Adicionar** para atualizar as credenciais do aplicativo.

10. Na folha **sqlApp | Certificados e segredos**, identifique o valor de **Key1**.

    >**Observação**: guarde esse valor. Isso será necessário na próxima tarefa. 

    >**Observação**: certifique-se de copiar o valor *antes* de sair da folha. Depois de fazer isso, não é mais possível recuperar seu valor de texto não criptografado.


#### Tarefa 2: criar uma política que permita que o aplicativo acesse o Key Vault.

Nesta tarefa, você concederá ao aplicativo recém-registrado permissões para acessar segredos armazenados no Cofre de chaves.

1. No portal do Azure, abra a sessão PowerShell no painel do Cloud Shell.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no lado superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell, no painel do Cloud Shell, execute o seguinte para criar uma variável que armazene a **ID do Aplicativo (do cliente)** que você registrou na tarefa anterior (substitua o espaço reservado `<Azure_AD_Application_ID>` com o valor da **ID do Aplicativo (do cliente)**):
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
4. Na sessão do PowerShell, no painel Cloud Shell, execute o seguinte para criar uma variável que armazene o nome do Key Vault.
    ```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

5. Na sessão do PowerShell, no painel Cloud Shell, execute o seguinte para conceder permissões no Key Vault ao aplicativo registrado na tarefa anterior:

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

6. Feche o painel do Cloud Shell. 


#### Tarefa 3: recuperar a cadeia de conexão ADO.NET do banco de dados SQL do Azure 

A implantação do modelo do ARM no Exercício 1 provisionou uma instância do SQL Server do Azure e um banco de dados SQL do Azure chamado **medical** . Você atualizará o recurso de banco de dados vazio com uma nova estrutura de tabela e selecionará colunas de dados para criptografia

1. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **bancos de dados SQL** e pressione a tecla **Enter**.

2. Na lista de bancos de dados SQL, selecione a entrada **medical(<randomsqlservername>)**.

    >**Observação**: se o banco de dados não puder ser encontrado, isso provavelmente significa que a implantação iniciada no Exercício 1 ainda não foi concluída. Para fazer essa validação, acesse o Grupo de Recursos do Azure "AZ500LAB10" (ou o nome de sua escolha) e selecione **Implantações** no painel Configurações.  

3. Na folha banco de dados SQL, na seção **Configurações**, selecione **Cadeias de conexão**. 

    >**Observação**: a interface inclui cadeias de conexão para ADO.NET, JDBC, ODBC, PHP e Go. 
   
4. Registre a cadeia de conexão **ADO.NET (autenticação SQL)**. Você precisará dela mais tarde.

    >**Observação**: ao usar a cadeia de conexão, substitua o espaço reservado `{your_password}` com a senha foi configurada na implantação no Exercício 1.

#### Tarefa 4: fazer logon na VM do Azure executando o Visual Studio 2019 e o SQL Management Studio 19

Nesta tarefa, você faz logon na VM do Azure, cuja implantação você iniciou no Exercício 1. Essa VM do Azure hospeda o Visual Studio 2019 e o SQL Server Management Studio 19.

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **máquinas virtuais** e pressione tecla **Enter**.

2. Na lista de Máquinas Virtuais exibida, selecione a entrada **az500-10-vm1**. Na folha **az500-10-vm1**, no painel **Essentials**, anote o **endereço IP público**. Você irá usá-lo mais tarde. 

#### Tarefa 5: criar uma tabela no Banco de Dados SQL e selecionar colunas de dados para criptografia

Nesta tarefa, você se conectará ao Banco de dados SQL com o SQL Server Management Studio e criará uma tabela. Em seguida, você criptografará duas colunas de dados usando uma chave gerada automaticamente no Azure Key Vault. 

1. No portal do Azure, navegue até a folha do banco de dados SQL **medical**, na seção **Essentials**, identifique o **Nome do servidor** (copie para a área de transferência) e, na barra de ferramentas, selecione **Definir firewall do servidor**.  

    >**Observação**: guarde o nome do servidor. Você precisará dele mais adiante nesta tarefa.

2. Na folha **Configurações do firewall**, role para baixo até Nome da regra, selecione **+ Adicionar uma regra de firewall** e especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Nome da Regra|**Permitir VM de Gerenciamento**|
    |IP Inicial|o endereço IP público de az500-10-vm1|
    |IP Final|o endereço IP público de az500-10-vm1|

3. Selecione **Salvar** para salvar a alteração e fechar o painel de confirmação. 

    >**Observação**: isso modifica as configurações de firewall do servidor, permitindo conexões com o banco de dados medical a partir do endereço IP público da VM do Azure implantado neste laboratório.

4. Volte até a folha **az500-10-vm1**, clique em **Visão geral**, depois em **Conectar** e, no menu suspenso, clique e, **Conectar**. 

5. Baixe o arquivo RDP e use-o para se conectar à VM do Azure **az500-10-vm1** por meio da Área de Trabalho Remota. Quando solicitado a autenticar, forneça as seguintes credenciais:

    |Configuração|Valor|
    |---|---|
    |Nome de Usuário|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    
    >**Observação**: aguarde até que a sessão da Área de Trabalho Remota e o **Gerenciador do Servidor** sejam carregados. Feche o Gerenciador de Servidores. 

    >**Observação**: as etapas restantes neste laboratório são executadas na sessão da Área de Trabalho Remota para a VM do Azure **az500-10-vm1**.

6. Instale o [SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) em **az500-10-vm1.** VM do Azure.
 
7. Abra o **SQL Server Management Studio**.

8. Na caixa de diálogo **Conectar ao servidor**, especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Tipo de Servidor|**Mecanismo de Banco de Dados**|
    |Nome do Servidor|o nome do servidor que você identificou anteriormente nesta tarefa|
    |Autenticação|**Autenticação do SQL Server**|
    |Nome de Usuário|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 2 > Tarefa 1 > Etapa 3.**|

9. Na caixa de diálogo **Conectar ao Servidor**, selecione **Conectar**.

10. No console do **SQL Server Management Studio**, no painel **Pesquisador de Objetos**, expanda a pasta **Bancos de Dados**.

11. No painel **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados **medical** e selecione  **Nova consulta**.

12. Cole o código a seguir na janela de consulta e selecione **Executar**. Isso criará uma tabela de **Pacientes**.

     ```sql
     CREATE TABLE [dbo].[Patients](
        [PatientId] [int] IDENTITY(1,1),
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NULL,
        [LastName] [nvarchar](50) NULL,
        [MiddleName] [nvarchar](50) NULL,
        [StreetAddress] [nvarchar](50) NULL,
        [City] [nvarchar](50) NULL,
        [ZipCode] [char](5) NULL,
        [State] [char](2) NULL,
        [BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
13. Depois que a tabela for criada, no painel **Pesquisador de Objetos**, expanda o nó do banco de dados **medical**, o nó das **tabelas**, clique com o botão direito do mouse no nó **dbo.Patients** e clique em **Criptografar colunas**. 

    >**Observação**: isso iniciará o assistente **Always Encrypted**.

14. Na página **Introdução** clique em **Avançar**.

15. Na página **Seleção de coluna**, selecione as colunas **SSN** e **Data de nascimento**, defina o **Tipo de criptografia** da coluna **SSN** como **Determinístico** e da coluna **Data de nascimento** como **Randomizado** e clique em **Avançar**.

    >**Observação**: ao executar a criptografia, se algum erro gerado como **Uma exceção foi gerada pelo destino de uma invocação** relacionada a **Rotary(Microsoft.SQLServer.Management.ServiceManagement)**, verifique se os valores da **Permissão de chave** de **Operações de Política de Rotação** estão **desmarcados**. Se não estiver no portal do Azure, navegue até as **Key Vault** >> **Políticas de acesso** >> **Permissões de chave** >> Desmarque todos os valores em **Operações de política de rotação** >> Em **Operações de chave privilegiada** >> Desmarque **Liberar**.

16. Na página **Configuração de chave mestra**, selecione **Azure Key Vault**, clique em **Entrar** quando solicitado, autentique-se usando a mesma conta de usuário usada para provisionar a instância do Azure Key Vault anteriormente neste laboratório, verifique se esse Key Vault aparece na lista suspensa **Selecionar um Azure Key Vault** e clique em **Avançar**.

17. Na página **Configurações de execução**, clique em **Avançar**.
    
18. Na página **Resumo**, clique em **Concluir** para prosseguir com a criptografia. Quando solicitado, entre novamente usando a mesma conta de usuário usada para provisionar a instância do Azure Key Vault anteriormente neste laboratório.

19. Quando o processo de criptografia estiver concluído, na página **Resultados**, clique em **Fechar**.

20. No console do **SQL Server Management Studio**, no painel **Pesquisador de Objetos**, no nó **médico**, expanda os subnós **Segurança** e **Chaves do Always Encrypted**. 

    >**Observação**: o subnó **Chaves do Always Encrypted** contém as subpastas **Chaves Mestras de Coluna** e **Chaves de Criptografia de Coluna**.

### Exercício 4: demonstrar o uso do Azure Key Vault na criptografia do banco de dados SQL do Azure

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Instalar o Visual Studio 2022
- Tarefa 2: Executar um aplicativo controlado por dados para demonstrar o uso do Azure Key Vault na criptografia do banco de dados SQL do Azure

#### Tarefa 1: Instalar o Visual Studio 2022

1. Alterne para a máquina virtual do Servidor se você já não estiver nela.

2. Abra o Gerenciador de Servidor.

3. Selecione Servidores Locais.

4. Defina **Configuração de Segurança Aprimorada do IE** como **Desativado**.

5. Abra o Navegador e ignore o aviso sobre o ESC do IE estar desativado.

6. Ir para https://visualstudio.microsoft.com/downloads.

7. Na caixa **Visual Studio 2022**, em **Comunidade**, selecione **Download gratuito**.

8. Quando o download estiver concluído, selecione **Abrir Arquivo**.

9. Selecione continuar para iniciar a instalação.
  - A instalação leva cerca de 10 minutos

#### Tarefa 2: Executar um aplicativo controlado por dados para demonstrar o uso do Azure Key Vault na criptografia do banco de dados SQL do Azure

Você criará um aplicativo de console usando o Visual Studio para carregar dados nas colunas criptografadas e acessar esses dados com segurança usando uma cadeia de conexão que acessa a chave no Key Vault.

1. Da sessão RDP para a **az500-10-vm1**, inicie o **Visual Studio 2019** no **menu Iniciar**.

2. Alterne para a janela que exibe a mensagem de boas-vindas do Visual Studio 2019, clique no botão **Entrar** e, quando solicitado, forneça as credenciais usadas para autenticar a assinatura do Azure que você está usando neste laboratório.

3. Na página **Introdução**, clique em **Criar um novo projeto**. 

4. Na lista de modelos de projeto, procure **Aplicativo de console (.NET Framework),** na lista de resultados, clique em **Aplicativo de console (.NET Framework)** para **C#** e clique em **Avançar**.

5. Na página **Configurar seu novo projeto**, especifique as seguintes configurações (deixe outras configurações com seus valores padrão) e clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome do projeto|**OpsEncrypt**|
    |Solução nome|**OpsEncrypt**|
    |Estrutura|**.NET Framework 4.7.2**|

6. No console do Visual Studio, clique no menu **Ferramentas**, no menu suspenso, clique em **Gerenciador de Pacotes NuGet** e, no menu em cascata, clique em **Console do Gerenciador de Pacotes**.

7. No painel do **Console do Gerenciador de Pacotes**, execute os seguintes comandos para instalar o primeiro pacote **NuGet** exigido:

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. No painel do **Console do Gerenciador de pacotes**, execute os seguintes comandos para instalar o segundo pacote **NuGet** exigido:

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
9. Minimize a sessão RDP na sua máquina virtual do Azure, navegue até **\\Allfiles\\Labs\\10\\program.cs**, abra-o no Bloco de notas e copie o conteúdo para a Área de transferência.

10. Retorne à sessão RDP e, no console do Visual Studio, na janela **Gerenciador de Soluções**, clique em **Program.cs** e substitua o conteúdo pelo código copiado na Área de transferência.

11. Na janela do Visual Studio, no painel **Program.cs**, na linha 15, substitua o espaço reservado `<connection string noted earlier>` pela cadeia de conexão **ADO.NET** do banco de dados SQL do Azure que você registrou anteriormente no laboratório. Na cadeia de conexão, substitua o espaço reservado `{your_password}` pela senha especificada na implantação no Exercício 1. Se você salvou a cadeia de conexão no computador do laboratório, talvez seja necessário sair da sessão RDP para copiar a cadeia de caracteres ADO e, em seguida, retornar à máquina virtual do Azure para colá-la.

12. Na janela do Visual Studio, no painel **Program.cs**, na linha 16, substitua o espaço reservado `<client id noted earlier>` pelo valor de **ID do Aplicativo (cliente)** do aplicativo registrado que você registrou anteriormente no laboratório. 

13. Na janela do Visual Studio, no painel **Program.cs**, na linha 17, substitua o espaço reservado `<key value noted earlier>` pelo valor de **Key1** do aplicativo registrado que você registrou anteriormente no laboratório. 

14. No console do Visual Studio, clique no botão **Iniciar** para iniciar a compilação do aplicativo de console e iniciá-lo.

15. O aplicativo vai iniciar uma janela do Prompt de Comando. Quando for solicitada a senha, digite a senha especificada na implantação no Exercício 1 para se conectar ao Banco de Dados SQL do Azure. 

16. Deixe o aplicativo de console em execução e alterne para o console do **SQL Management Studio**. 

17. No painel **Pesquisador de Objetos**, clique com o botão direito do mouse no **banco de dados médico** e, no menu do botão direito do mouse, clique em **Nova consulta**.

18. Na janela de consulta, execute a consulta a seguir para verificar se os dados carregados no banco de dados a partir do aplicativo de console estão criptografados.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. Volte para o aplicativo de console, onde será solicitado que você insira um SSN válido. Isso consultará a coluna criptografada para os dados. No prompt de comando, digite o seguinte e pressione a tecla Enter:

    ```cmd
    999-99-0003
    ```

    >**Observação**: verifique se os dados retornados pela consulta não estão criptografados.

20. Pressione a tecla Enter para encerrar o aplicativo de console

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados.

1. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. 

2. No menu suspenso superior esquerdo do painel Cloud Shell, se necessário, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover os grupos de recursos criados neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
