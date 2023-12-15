---
lab:
  title: 'Laboratório: Key Vault (implementação de dados seguros com a configuração de Always Encrypted)'
  module: Module 03 - Secure Data and Applications
---

# Laboratório &#58; Key Vault (implementação de dados seguros com a configuração de Always Encrypted)
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar um aplicativo de prova de conceito que usa o suporte do Banco de Dados SQL do Azure para a funcionalidade Always Encrypted. Todos os segredos e chaves usados nesse cenário devem ser armazenados no Cofre de Chaves. O aplicativo deve ser registrado no Azure Active Directory (Azure AD) para aprimorar sua postura de segurança. Para atingir esses objetivos, a prova de conceito deve incluir:

- Criar um Cofre de Chaves do Azure e armazenar chaves e segredos no cofre.
- Crie um Banco de Dados SQL e criptografe o conteúdo de colunas em tabelas de banco de dados usando o Always Encrypted.

>**Nota**: Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

Para manter o foco nos aspectos de segurança do Azure, relacionados à criação dessa prova de conceito, você começará a partir de uma implantação automatizada de modelo ARM, configurando uma Máquina Virtual com o Visual Studio 2019 e o SQL Server Management Studio 19.

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Implantar a infraestrutura base a partir de um modelo ARM
- Exercício 2: Configurar o recurso Cofre de Chaves com uma chave e um segredo
- Exercício 3: Configurar um banco de dados SQL do Azure e um aplicativo controlado por dados
- Exercício 4: Demonstrar o uso do Cofre de Chaves do Azure na criptografia do banco de dados SQL do Azure

## Diagrama do Cofre de Chaves

![imagem](https://user-images.githubusercontent.com/91347931/157532938-c724cc40-f64f-4d69-9e91-d75344c5e0a2.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\10 az-500-10_azuredeploy.json\\**

- **\\Programa Allfiles\\Labs\\10\\.cs**

### Estimativa de tempo total de laboratório: 60 minutos

### Exercício 1: Implantar a infraestrutura base a partir de um modelo ARM

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Implantar uma VM do Azure e um banco de dados SQL do Azure

#### Tarefa 1: Implantar uma VM do Azure e um banco de dados SQL do Azure

Nesta tarefa, você implantará uma VM do Azure, que instalará automaticamente o Visual Studio 2019 e o SQL Server Management Studio 19 como parte da implantação. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Implantar um modelo** personalizado e pressione a **tecla Enter**.

3. Na **página Implantação personalizada**, clique em **Criar seu modelo no editor**.

4. **Na folha Editar modelo**, clique em Carregar arquivo, localize o\\** arquivo** Allfiles\\Labs\\10 az-500-10_azuredeploy.json\\** e clique em ****Abrir.**

5. Na folha Editar **modelo** , clique em **Salvar**.

6. **Na folha Implantação** personalizada, em **Escopo de implantação**, verifique se as seguintes configurações estão definidas (deixe outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|O nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB10**|
   |Localização|**Leste dos EUA**|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   
    >**Nota**: Embora seja possível alterar as credenciais administrativas usadas para fazer logon na Máquina Virtual, não é necessário.

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

7. Clique no botão Revisar e Criar e confirme a implantação clicando no **botão Criar****.** 

    >**Observação**: isso inicia a implantação da VM do Azure e do Banco de Dados SQL do Azure necessários para este laboratório. 

    >**Nota**: Não espere a conclusão da implantação do modelo ARM, mas continue para o próximo exercício. A implantação pode levar até 5 minutos. 

### Exercício 2: Configurar o recurso Cofre de Chaves com uma chave e um segredo

>**Nota**: Para todos os recursos neste laboratório, estamos usando a **região Leste (EUA).** Verifique com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Criar e configurar um cofre de chaves
- Depois, adicione uma chave no cofre de chaves.
- Adicionar um segredo ao Key Vault.

#### Criar e configurar um cofre de chaves

Nesta tarefa, você criará um recurso do Cofre de Chaves do Azure. Você também configurará as permissões do Cofre de Chaves do Azure.

1. Abra o Cloud Shell clicando no primeiro ícone (ao lado da barra de pesquisa) no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um Cofre de Chaves do Azure no grupo **de recursos AZ500LAB10**. (Se você escolheu outro nome para o Grupo de Recursos deste laboratório fora da Tarefa 1, use esse nome para essa tarefa também). O nome do cofre precisa ser exclusivo. Lembre-se do nome que você escolheu. Você vai precisar dele em todo este laboratório.  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >**Nota**: A saída do último comando exibirá o nome do vault e o URI do vault. O URI do vault está no formato `https://<vault_name>.vault.azure.net/`

4. Feche o painel do Cloud Shell. 

5. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

6. **Na folha Grupos de recursos**, na lista de grupos de recursos, clique na **entrada AZ500LAB10** (ou outro nome escolhido anteriormente para o grupo de recursos).

7. Na folha Grupo de Recursos, clique na entrada que representa o Cofre de Chaves recém-criado. 

8. Na folha Cofre de Chaves, na **seção Visão Geral**, clique em Políticas** de Acesso e em ****+ Criar**.

9. Na folha Novo Contêiner, especifique as seguintes configurações (deixe as demais com seus valores padrão) e selecione OK: 

    |Configuração|Valor|
    |----|----|
    |Configurar do modelo (opcional)|Gerenciamento de Chave, Segredo e Certificado|
    |Permissões de chave|clique em **Selecionar tudo**, resultando em um total de **9 permissões selecionadas**|
    |Permissões para operações criptográficas|clique em **Assinar**, resultando em um total de **1 permissões selecionadas**|
    |Permissões de segredo|clique em **Selecionar tudo**, resultando em um total de **7 permissões selecionadas**|
    |Permissões de certificado|clique em **Selecionar tudo**, resultando em um total de **15 permissões selecionadas**|
    |Selecionar entidade de segurança|clique em Nenhum selecionado **, na **folha Principal**, selecione sua conta de usuário e clique em Avançar ******|
    |Aplicativo (opcional)|Clique em **Avançar**|
    |Examinar + criar|Clique em **Criar**|
    
    >**Nota**: A operação Revisar + criar anterior retorna à página Políticas de acesso que lista Permissões de Aplicativo, Email, Permissões de Chave, Permissões Secretas e Permissões de Certificado.
      
#### Adicionar uma chave ao Key Vault

Nesta tarefa, você adicionará uma chave ao Cofre da Chave e exibirá informações sobre a chave. 

1. No portal do Azure, abra a sessão PowerShell no painel Cloud Shell.

2. Verifique se **o PowerShell** está selecionado no menu suspenso superior esquerdo do painel Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para adicionar uma chave protegida por software ao Cofre de Chaves: 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**Nota**: O nome da chave é **MyLabKey**

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para verificar se a chave foi criada:

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para exibir o identificador de chave:

    ```powershell
    $key.key.kid
    ```

6. Minimize o terminal do Cloud Shell. 

7. De volta ao portal do Azure, na folha Cofre de Chaves, na **seção Objetos, clique em **Chaves****.

8. Na lista de chaves, clique na entrada MyLabKey e, em seguida, na **folha MyLabKey****, clique na **entrada que representa a versão atual da chave.

    >**Nota**: Examine as informações sobre a chave que você criou.

    >**Nota**: Você pode fazer referência a qualquer chave usando o identificador de chave. Para obter a versão mais atual, faça referência `https://<key_vault_name>.vault.azure.net/keys/MyLabKey` ou obtenha a versão específica com: `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### Adicionar um segredo ao Key Vault

1. Volte para o Cloud Shell.

2. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma variável com um valor de cadeia de caracteres seguro:

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

3.  Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para adicionar o segredo ao cofre:

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**Nota**: O nome do segredo é SQLPassword. 

4.  Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para verificar se o segredo foi criado.

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

5. Minimize o terminal do Cloud Shell. 

6. No portal do Azure, navegue de volta para a folha Cofre da Chave, na seção Objetos **, clique em ****Segredos**.

7. Na lista de segredos, clique na entrada SQLPassword e, em seguida, na folha SQLPassword****, clique na **** entrada que representa a versão atual do segredo.

    >**Nota**: Examine as informações sobre o segredo que você criou.

    >**Nota**: Para obter a versão mais atual de um segredo, faça referência ou obtenha uma versão específica, faça referência `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>``https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>`


### Exercício 3: Configurar um banco de dados SQL do Azure e um aplicativo controlado por dados

Neste exercício, você realizará as seguintes tarefas:

- Habilitar seu aplicativo cliente para acessar o serviço do Banco de Dados SQL
- Tarefa 2: Criar uma política que permita o acesso do aplicativo ao Cofre de Chaves.
- Tarefa 3: Recuperar o banco de dados do SQL Azure ADO.NET a cadeia de conexão 
- Tarefa 4: Faça logon na VM do Azure executando o Visual Studio 2019 e o SQL Management Studio 19
- Tarefa 5: Criar uma tabela no Banco de dados SQL e selecionar colunas de dados para criptografia


#### Habilitar seu aplicativo cliente para acessar o serviço do Banco de Dados SQL 

Nesta tarefa, você habilitará um aplicativo cliente para acessar o serviço Banco de Dados SQL do Azure. Isso será feito configurando a autenticação necessária e adquirindo a ID e o Segredo do Aplicativo que você precisará para autenticar seu aplicativo.

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Registros de** Aplicativo e pressione a **tecla Enter**.

2. No painel **Registros de aplicativo**, selecione **+ Novo registro**. 

3. Na folha Novo Contêiner, especifique as seguintes configurações (deixe as demais com seus valores padrão) e selecione OK:

    |Configuração|Valor|
    |----|----|
    |Nome|**sqlApp**|
    |Redirecionar URI (opcional)|**Web** e **https://sqlapp**|

4. **Na folha Registrar um aplicativo**, clique em **Registrar**. 

    >**Nota**: Assim que o registro for concluído, o navegador redirecionará você automaticamente para a **folha sqlApp** . 

5. Na folha sqlApp **, identifique o **valor da ID** do **aplicativo (cliente). 

    >Grave esse valor. Isso será necessário na próxima etapa.

6. **Na folha sqlApp**, na **seção Gerenciar**, clique em **Certificados e segredos**.

7. **No sqlApp | Certificados & folha secrets** / **seção Client Secrets**, clique em **+ Novo segredo do cliente**

8. No painel Adicionar um segredo** do **cliente, especifique as seguintes configurações:

    |Configuração|Valor|
    |----|----|
    |Descrição|**Key1**|
    |Expira|12 meses|
    
9. Aguarde até que as credenciais do aplicativo sejam atualizadas.

10. **No sqlApp | Certificados e secrets** blade, identifique o valor de **Key1**.

    >Grave esse valor. Isso será necessário na próxima etapa. 

    >**Nota**: Certifique-se de copiar o valor *antes* de sair da folha. Depois de fazer isso, não é mais possível recuperar seu valor de texto não criptografado.


#### Tarefa 2: Criar uma política que permita o acesso do aplicativo ao Cofre de Chaves.

Nesta tarefa, você concederá ao aplicativo recém-registrado permissões para acessar segredos armazenados no Cofre de Chaves.

1. No portal do Azure, abra a sessão PowerShell no painel Cloud Shell.

2. Verifique se **o PowerShell** está selecionado no menu suspenso superior esquerdo do painel Cloud Shell.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para criar uma variável que armazene a ID do **Aplicativo (cliente) que você registrou na tarefa anterior (substitua o `<Azure_AD_Application_ID>` espaço reservado pelo valor da ID**** do **Aplicativo (cliente):
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma variável que armazene o nome do Cofre da Chave.
    ```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para conceder permissões no Cofre de Chaves ao aplicativo registrado na tarefa anterior:

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

6. Feche o painel do Cloud Shell. 


#### Tarefa 3: Recuperar o banco de dados do SQL Azure ADO.NET a cadeia de conexão 

A implantação do modelo ARM no Exercício 1 provisionou uma instância do SQL Server do Azure e um banco de dados SQL do Azure chamado **medical** . Você atualizará o recurso de banco de dados vazio com uma nova estrutura de tabela e selecionará colunas de dados para criptografia

1. No portal do Azure, na caixa de texto Procurar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **bancos de dados** SQL e pressione a **tecla Enter**.

2. Na lista de bancos de dados SQL, clique na **entrada medical(<randomsqlservername>).**

    >**Nota**: Se o banco de dados não puder ser encontrado, isso provavelmente significa que a implantação iniciada no Exercício 1 ainda não foi concluída. Você pode validar isso navegando até o Grupo de Recursos do Azure "AZ500LAB10" (ou o nome escolhido) e selecionando **Implantações** no painel Configurações.  

3. Na folha Banco de dados SQL, na **seção Configurações** , clique em **Cadeias de conexão**. 

    >**Nota**: A interface inclui cadeias de conexão para ADO.NET, JDBC, ODBC, PHP e Go. 
   
4. Copie a cadeia de conexão ADO.NET da autenticação SQL. Você precisará dela mais tarde.

    >**Nota**: Ao usar a cadeia de conexão, substitua o `{your_password}` espaço reservado pela senha configurada com a implantação no Exercício 1.

#### Tarefa 4: Faça logon na VM do Azure executando o Visual Studio 2019 e o SQL Management Studio 19

Nesta tarefa, você faz logon na VM do Azure, cuja implantação você iniciou no Exercício 1. Essa VM do Azure hospeda o Visual Studio 2019 e o SQL Server Management Studio 19.

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos**, insira **máquinas virtuais** e pressione **Enter**.

2. Na lista de Máquinas Virtuais mostrada, selecione a **entrada az500-10-vm1** . **Na folha az500-10-vm1**, no **painel Essentials**, anote o **endereço** IP público. Você usará este IP mais tarde. 

#### Tarefa 5: Criar uma tabela no Banco de dados SQL e selecionar colunas de dados para criptografia

Nesta tarefa, você se conectará ao Banco de dados SQL com o SQL Server Management Studio e criará uma tabela. Em seguida, você criptografará duas colunas de dados usando uma chave gerada automaticamente do Cofre de Chaves do Azure. 

1. No portal do Azure, navegue até a folha do banco de dados SQL médico, na seção Essentials **, identifique o Nome do servidor (copiar para a área de transferência) e, na **barra de ferramentas, clique em **Definir firewall do ****servidor****.**  

    >**Nota**: Registre o nome do servidor. Você precisará do nome do servidor mais adiante nesta tarefa.

2. Na folha Configurações do firewall, role para baixo até Nome da **regra, clique em **+ Adicionar uma regra** de firewall** e especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Nome da Regra|**Permitir VM de Gerenciamento**|
    |IP Inicial|Obtenha o endereço IP público da VM.|
    |IP Final|Obtenha o endereço IP público da VM.|

3. Clique em **Salvar** para salvar a alteração e fechar o painel de confirmação. 

    >**Observação**: isso modifica as configurações de firewall do servidor, permitindo conexões com o banco de dados médico a partir do endereço IP público da VM do Azure implantado neste laboratório.

4. Navegue de volta para a **folha az500-10-vm1**, clique em Visão geral **, clique **em Conectar** e, no menu suspenso, clique **em **RDP**. 

5. Clique em **Baixar Arquivo** RDP e use-o para se conectar à **VM do Azure az500-10-vm1** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça os seguintes credntials:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Nota**: Aguarde até que a sessão de Área de Trabalho Remota e **o Gerenciador do Servidor** sejam carregados. Feche o Gerenciador de Servidores. 

    >**Observação**: as etapas restantes neste laboratório são executadas na sessão da Área de Trabalho Remota para a **VM do Azure az500-10-vm1** . 

6. Clique em **Iniciar, no menu Iniciar****, expanda a **pasta Microsoft SQL Server Tools 19** e clique no ****item de menu Micosoft SQL Server Management Studio**.

7. Na caixa de diálogo **Conectar ao Servidor** , aplique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Tipo de Servidor|**Mecanismo de Banco de Dados**|
    |Nome do Servidor|O nome do servidor que você identificou anteriormente nesta tarefa|
    |Autenticação|**Autenticação do SQL Server**|
    |Logon|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

8. Na caixa de diálogo **Conectar ao Servidor**, selecione **Conectar**.

9. No console do **SQL Server Management Studio** , no **painel Pesquisador** de Objetos, expanda a **pasta Bancos de Dados** .

10. No Pesquisador de Objetos, clique com o botão direito do mouse no banco de dados e selecione Nova Consulta.

11. Cole o seguinte snippet de código T-SQL na janela de consulta e clique em **Executar**: Isso criará uma **tabela de pacientes** .

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
12. Depois que a tabela for criada com êxito, no **painel Pesquisador** de Objetos, expanda o nó do banco de dados médico **, o nó tabelas **, clique com o **** botão direito do mouse no **dbo. Nó Pacientes** e clique em **Criptografar Colunas**. 

    >**Nota**: Isso iniciará o **assistente Sempre Criptografado** .

13. Na página **Introdução** clique em **Avançar**.

14. Na página Seleção de **Coluna, selecione as **colunas SSN e Data de nascimento, defina o **Tipo** de criptografia da coluna SSN**** como Determinístico** e da **coluna Data**** de **nascimento como **** Randomizado** e **clique em **Avançar**.**

    >**Observação**: ao executar a criptografia, se algum erro gerado como **Exceção tiver sido lançado pelo destino de uma chamada** relacionada ao **Rotary (Microsoft.SQLServer.Management.ServiceManagement),** verifique se os **valores da Permissão de Chave** de Operações** de Política de Rotação estão **desmarcados**, se não estiver no portal do Azure, navegue até as **Permissões de Chave de Políticas** >> **de Acesso do Cofre**** >> de Chaves** ** >> Desmarque todos os valores em Operações de **política de rotação >> Em **operações** de chave privilegiada >> Desmarque **Liberar**.**

15. Na página Configuração de Chave Mestra, selecione **Cofre de Chaves do Azure, clique em Entrar **, quando solicitado, autentique-se usando a mesma conta de usuário usada para provisionar a instância do Cofre de Chaves do Azure anteriormente neste laboratório, verifique se esse Cofre de ****Chaves aparece na lista suspensa Selecionar um Cofre de Chaves**** do Azure e clique **em **Avançar**.**

16. Clique em **próximo** na página **Configurações**.
    
17. **Na página Resumo**, clique em **Concluir** para prosseguir com a criptografia. Quando solicitado, entre novamente usando a mesma conta de usuário usada para provisionar a instância do Cofre de Chaves do Azure anteriormente neste laboratório.

18. Quando o processo de criptografia estiver concluído, na **página Resultados** , clique em **Fechar**.

19. No console do **SQL Server Management Studio**, no painel Pesquisador** de Objetos, no **** nó médico**, expanda os **subnós Segurança** e **Chaves** Sempre Criptografadas. 

    >**Nota**: O **subnó Chaves Sempre Criptografadas contém as **subpastas Chaves Mestras de Coluna e **Chaves**** de Criptografia de Coluna**.


### Exercício 4: Demonstrar o uso do Cofre de Chaves do Azure na criptografia do banco de dados SQL do Azure

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Executar um aplicativo controlado por dados para demonstrar o uso do Cofre de Chaves do Azure na criptografia do banco de dados SQL do Azure

#### Tarefa 1: Executar um aplicativo controlado por dados para demonstrar o uso do Cofre de Chaves do Azure na criptografia do banco de dados SQL do Azure

Você criará um aplicativo de console usando o Visual Studio para carregar dados nas colunas criptografadas e, em seguida, acessar esses dados com segurança usando uma cadeia de conexão que acessa a chave no Cofre de Chaves.

1. Da sessão RDP para o az500-10-vm1 **, inicie **o **Visual Studio 2019** no **menu** Iniciar.

2. Alterne para a janela que exibe a mensagem de boas-vindas do Visual Studio 2019, clique no** botão Entrar e, quando solicitado, forneça os credenciais usados para autenticar a assinatura do **Azure que você está usando neste laboratório.

3. Na página Introdução, escolha Criar um projeto. 

4. Na lista de modelos de projeto, procure **Aplicativo de Console (.NET Framework),** na lista de resultados, clique em Aplicativo de Console (.NET Framework)** para **C#** e clique **em **Avançar**.

5. **Na página Configurar seu novo projeto**, especifique as seguintes configurações (deixe outras configurações com seus valores padrão) e clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome do projeto|**OpsEncrypt**|
    |Solução nome|**OpsEncrypt**|
    |Estrutura|**.NET Framework 4.7.2**|

6. No console do Visual Studio, clique no menu Ferramentas **, no menu suspenso, clique em Gerenciador de Pacotes NuGet e, no **menu em cascata, clique em ****Console do Gerenciador de** Pacotes.**

7. No Console do Gerenciador de pacotes, execute os seguintes comandos para instalar os pacotes do NuGet.

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. No Console do Gerenciador de pacotes, execute os seguintes comandos para instalar os pacotes do NuGet.

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
9. Minimize a sessão RDP para sua máquina virtual do Azure, navegue até **\\o programa Allfiles\\Labs 10\\.cs,** abra-o no Bloco de Notas e copie seu conteúdo para a Área de\\Transferência.

10. Retorne à sessão RDP e, no console do Visual Studio, na janela Gerenciador de Soluções, clique em **Programa.cs** e substitua seu conteúdo pelo código copiado na **Área de** Transferência.

11. Na janela do Visual Studio, no painel Program.cs, na linha 15, substitua o `<connection string noted earlier>` espaço reservado pelo banco de dados **SQL do Azure ADO.NET** cadeia de** conexão que você registrou anteriormente no **laboratório. Na cadeia de conexão, substitua o `{your_password}` placehodler pela senha especificada na implantação no Exercício 1. Se você salvou a cadeia de caracteres no computador do laboratório, talvez seja necessário sair da sessão RDP para copiar a cadeia de caracteres ADO e, em seguida, retornar à máquina virtual do Azure para colá-la.

12. Na janela do Visual Studio, no **painel Program.cs**, na linha 16, substitua o `<client id noted earlier>` espaço reservado pelo valor de ID** do **Aplicativo (cliente) do aplicativo registrado que você registrou anteriormente no laboratório. 

13. Na janela do Visual Studio, no painel Program.cs **, na linha 17, substitua o `<key value noted earlier>` espaço reservado pelo valor de **Key1** do aplicativo registrado que você registrou anteriormente no **laboratório. 

14. No console do Visual Studio, clique no **botão Iniciar** para iniciar a compilação do aplicativo de console e iniciá-lo.

15. Inicie o aplicativo de destino na janela do Prompt de Comando. Quando for solicitada a senha, digite a senha especificada na implantação no Exercício 1 para se conectar ao Banco de Dados SQL do Azure. 

16. Deixe o aplicativo de console em execução e alterne para o console do **SQL Management Studio** . 

17. No painel Pesquisador **** de Objetos, clique com o botão direito do mouse no banco de dados** médico e, no menu do botão direito do **mouse, clique em **Nova Consulta**.

18. Na janela de consulta, execute a consulta a seguir para verificar se os dados carregados no banco de dados do aplicativo de console estão criptografados.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. Volte para o aplicativo de console onde você será solicitado a inserir um SSN válido. Isso consultará a coluna criptografada para os dados. No prompt de comando, digite o seguinte e pressione ENTER:

    ```cmd
    999-99-0003
    ```

    >**Nota**: Verifique se os dados retornados pela consulta não estão criptografados.

20. Pressione qualquer tecla para encerrar o aplicativo de console do cliente.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. 

2. No menu suspenso superior esquerdo do painel Cloud Shell, se necessário, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover os grupos de recursos criados neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
