---
lab:
  title: Laboratório&#58; Implementar a sincronização de diretório
  module: Module 01 - Manage Identity and Access
---

# Laboratório&#58; Implementar a sincronização de diretório
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito demonstrando como integrar o ambiente local dos Serviços de Domínio Active Directory (AD DS) com um locatário do Azure Active Directory (Azure AD). Especificamente, você precisa:

- Implementar uma floresta do AD DS de domínio único implantando uma VM do Azure que hospeda um controlador de domínio do AD DS
- Criar e configurar um locatário do Azure AD
- Sincronizar a floresta do AD DS com o locatário do Azure AD

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Implantar uma VM do Azure que hospeda um controlador de domínio do Active Directory
- Criar e configurar um locatário do Active Directory do Azure no Azure;
- Exercício 3: Sincronizar a floresta do Active Directory com um locatário do Active Directory do Azure

## Laboratório: Implementar a sincronização de diretório

![imagem](https://user-images.githubusercontent.com/91347931/157525374-8f740f14-c2db-47b3-98f8-7feb9bc122b5.png)

## Instruções

### Exercício 1: Implantar uma VM do Azure que hospeda um controlador de domínio do Active Directory

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Identificar um nome DNS disponível para uma implantação de VM do Azure
- Tarefa 2: Usar um modelo ARM para implantar uma VM do Azure que hospeda um controlador de domínio do Active Directory

#### Tarefa 1: Identificar um nome DNS disponível para uma implantação de VM do Azure

Nesta tarefa, você identificará um nome DNS para sua implantação de VM do Azure. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

3. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para identificar um nome DNS disponível que você pode usar para uma implantação de VM do Azure na próxima tarefa deste exercício:

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**Nota**: Substitua o espaço reservado `<custom-label>` por um nome DNS válido que provavelmente seja globalmente exclusivo. Substitua o espaço reservado pelo nome da região na qual você deseja implantar a VM do Azure que hospedará o `<location>` controlador de domínio do Active Directory que você usará neste laboratório.

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

5. Verifique se o comando retornou **True**. Caso contrário, execute novamente o mesmo comando com um valor diferente do `<custom-label>` até que o comando retorne **True**.

6. Registre o valor do `<custom-label>` que resultou no resultado bem-sucedido. Isso será necessário na próxima etapa.

7. Fechar o Cloud Shell

#### Tarefa 2: Usar um modelo ARM para implantar uma VM do Azure que hospeda um controlador de domínio do Active Directory

Nesta tarefa, você implantará uma VM do Azure que hospedará um controlador de domínio do Active Directory

1. Abra uma guia na mesma janela do navegador e vá para **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain**.

2. Na página Criar uma VM do Azure com uma nova Floresta do **AD, clique em **Implantar no Azure**.** Isso redirecionará automaticamente o navegador para a **folha Criar uma VM do Azure com uma nova folha Floresta** do AD no portal do Azure. 

3. Na folha Criar uma VM do Azure com uma nova Floresta do **AD, clique em **Editar parâmetros**.**

4. **Na folha Editar parâmetros**, clique em Carregar arquivo **, na caixa de diálogo Abrir **, navegue até a pasta **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json**, clique em ******Abrir** e em **Salvar.**

5. Na folha Criar uma VM do Azure com uma nova Floresta** do **AD, especifique as seguintes configurações (deixe outras pessoas com seus valores existentes):

   |Configuração|Valor|
   |---|---|
   |Subscription|O nome da sua assinatura do Azure.|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB06**|
   |Region|a região do Azure que você identificou na tarefa anterior|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Nome do Domínio|adatum.com|
   |Prefixo do DNS|o nome de host DNS que você identificou na tarefa anterior|
   |Tamanho da VM|Standard_D2s_v3|

6. Na folha Criar uma VM do Azure com uma nova Floresta do **AD, clique em Revisar + criar** e clique em ****Criar**.**

    >**Nota**: Não espere a conclusão da implantação, mas prossiga para o próximo exercício. A implantação pode levar até 5 minutos. Você usará a máquina virtual implantada nesta tarefa no terceiro exercício deste laboratório.

> Resultado: depois de concluir este exercício, você iniciou a implantação de uma VM do Azure que hospedará um controlador de domínio do Active Directory usando um modelo do Gerenciador de Recursos do Azure


### Criar e configurar um locatário do Active Directory do Azure no Azure; 

### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 3: criar um locatário do Azure AD (Azure Active Directory).
- Tarefa 2: Adicionar um nome DNS personalizado ao novo locatário do Azure AD
- Tarefa 3: Criar um usuário do Azure AD com a função Administrador Global

#### Tarefa 3: criar um locatário do Azure AD (Azure Active Directory).

Nesta tarefa, você criará um novo locatário do Azure AD para usar neste laboratório. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Azure Active Directory** e pressione a **tecla Enter**.

2. Na folha que exibe **Visão geral** do seu locatário atual do Azure AD, clique em Gerenciar locatários** e, na próxima tela, clique **em **+ Criar**.

3. Na guia **Básico** do painel **Criar um locatário**, verifique se a opção **Azure Active Directory** está selecionada, e selecione **Próximo: Configuração >**.

4. Na guia **Configuração** do painel **Criar um locatário**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da organização|**AdatumSync**|
   |Nome de domínio inicial|um nome exclusivo que consiste em uma combinação de letras e dígitos|
   |País ou Região|**Estados Unidos**|

    >**Nota**: Registre o nome de domínio inicial. Você precisará disso em uma etapa posterior.

    >**Nota**: A marca de seleção verde na **caixa de texto Nome de domínio inicial indicará se o nome de domínio** digitado é válido e exclusivo. (Registre seu nome de domínio inicial para uso posterior).

5. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: aguarde até que o novo locatário seja criado. Selecione o ícone **Notificações** para exibir o status da implantação. 

#### Tarefa 2: Adicionar um nome DNS personalizado ao novo locatário do Azure AD

Nesta tarefa, você adicionará seu nome DNS personalizado ao novo locatário do Azure AD. 

1. No portal do Azure, na barra de ferramentas, clique no **ícone Diretório + assinatura** , localizado à direita do ícone do Cloud Shell. 

2. **Na folha Diretório + assinatura**, selecione a linha AdatumSync** do locatário **recém-criada e clique no **botão Alternar**.

    >**Nota**: Talvez seja necessário atualizar a janela do navegador se a **entrada AdatumSync** não aparecer na **lista de filtros Diretório + assinatura** .

3. Na folha **Azure Active Directory\|, clique em **Personalizar nomes de domínio.

4. Na folha Nomes** de domínio personalizados do **AdatumSync\|, clique em **+ Adicionar domínio** personalizado.

5. **Na folha Nome de domínio personalizado, na **caixa de texto Nome de domínio personalizado, digite **adatum.com** e clique em **Adicionar domínio******.

6. Na folha adatum.com****, examine as informações necessárias para executar a verificação do nome de domínio do Azure AD e selecione **Excluir** duas vezes. 

    >**Nota**: Você não poderá concluir o processo de validação porque não possui o nome de domínio DNS adatum.com****. Isso não impedirá que você sincronize o domínio do adatum.com AD DS com o locatário do **** Azure AD. Você usará para essa finalidade o nome DNS inicial do locatário do Azure AD (o nome que termina com o sufixo **onmicrosoft.com** ), que você identificou na tarefa anterior. No entanto, lembre-se de que, como resultado, o nome de domínio DNS do domínio AD DS e o nome DNS do locatário do Azure AD serão diferentes. Isso significa que os usuários do Adatum precisarão usar nomes diferentes ao entrar no domínio do AD DS e ao entrar no locatário do Azure AD.

#### Tarefa 3: Criar um usuário do Azure AD com a função Administrador Global

Nesta tarefa, você adicionará um novo usuário do Azure AD e o atribuirá à função de Administrador Global. 

1. Na folha locatário do Azure AD do **AdatumSync**, na **seção Gerenciar**, clique em Usuários.****

2. Sobre os **Usuários | Folha Todos os usuários**, clique em + Novo Usuário** e, em seguida, em ****Criar novo usuário**.

3. No painel Novo usuário, verifique se a opção Criar usuário está selecionada e especifique as seguintes configurações, deixando as demais com seus valores padrão:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**syncadmin**|
   |Nome|**syncadmin**|
   |Senha|verifique se a opção **Gerar senha automaticamente está selecionada e clique em **Mostrar senha****|

    >**Nota**: Registre o nome de usuário completo. Você pode copiar seu valor clicando no botão Copiar para a **área** de transferência no lado direito da lista suspensa que exibe o nome de domínio e colando-o em um documento do bloco de notas. Você precisará delas mais tarde neste tutorial.

    >**Nota**: Registre a senha do usuário clicando **no botão Copiar para a área** de transferência no lado direito da caixa de texto Senha e colando-a em um documento do bloco de notas. Você precisará delas mais tarde neste tutorial.

4. **Na guia Propriedades**, role até a parte inferior e especifique o Local de uso: Estados Unidos** (deixe todos os outros com seus valores padrão) e clique em **Avançar: **Atribuições >**.

5. **Na guia Atribuições**, clique em + Adicionar função **, procure e selecione **Administrador** Global e clique em ****Selecionar**. Clique em **Revisar + criar** e em **Criar**.
   
    >**Observação**: um usuário do Azure AD com a função de Administrador Global é necessário para implementar o Azure AD Connect.

6. Abra uma janela do navegador InPrivate.

7. Navegue até o portal do Azure em **`https://portal.azure.com/`** e entre usando a conta de **usuário syncadmin** . Quando solicitado, altere a senha registrada anteriormente nesta tarefa para sua própria senha que atenda aos requisitos de complexidade e registre-a para referência futura. Será solicitado que você forneça a senha neste momento.

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da **conta de usuário syncadmin** , incluindo o nome de domínio DNS do locatário do Azure AD, que você registrou anteriormente nesta tarefa. Esse nome de usuário está no formato syncadmin@.onmicrosoft.com, em que é o espaço reservado que `<your_tenant_name>` representa seu nome de`<your_tenant_name>` locatário exclusivo do Azure AD. 

8. Saia como **syncadmin** e feche a janela do navegador InPrivate.

> **Resultado**: depois de concluir este exercício, você criou um locatário do Azure AD, viu como adicionar um nome DNS personalizado ao novo locatário do Azure AD e criou um usuário do Azure AD com a função de Administrador Global.


### Exercício 3: Sincronizar a floresta do Active Directory com um locatário do Active Directory do Azure

### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Preparar o AD DS local para sincronização de diretório.
- Parte 1: instalar o Azure AD Connect
- Tarefa 3: Verificar a sincronização de diretórios

#### Preparar o AD DS local para sincronização de diretório.

Nesta tarefa, você se conectará à VM do Azure que executa o controlador de domínio AD DS e criará uma conta de sincronização de diretório. 

   > Antes de iniciar esta tarefa, verifique se a implantação do modelo iniciada no primeiro exercício deste laboratório foi concluída.

1. No portal do Azure, defina o **filtro Diretório + assinatura para o locatário do Azure AD associado à assinatura** do Azure na qual você implantou a VM do Azure no primeiro exercício deste laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos**, insira **máquinas virtuais** e pressione **Enter**.

3. **Na folha Máquinas virtuais**, clique na **entrada adVM**. 

4. **Na folha adVM**, clique em Conectar** e, no menu suspenso, clique em ****RDP**. 

5. **Na lista suspensa Endereço IP, selecione **Endereço**** IP público do balanceador de carga e clique em **Baixar Arquivo** RDP e use-o para se conectar à **VM do Azure adVM** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça os seguintes credntials:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Nota**: Aguarde até que a sessão de Área de Trabalho Remota e **o Gerenciador do Servidor** sejam carregados.  

    >**Observação**: as etapas a seguir são executadas na sessão de Área de Trabalho Remota para a **VM do Azure adVM** .

    >**Observação**: se o endereço IP público do balanceador de carga não estiver disponível na lista suspensa Endereço IP da folha RDP, na **pesquisa do Portal do **Azure por **endereços** IP públicos, selecione **adPublicIP** e anote seu endereço** IP**. Clique no botão Iniciar, digite **MSTSC** e pressione **Enter** para iniciar o cliente de área de trabalho remota. Digite o endereço IP público do balanceador de carga na caixa de **texto Computador:** e clique em **Conectar**.

6. No Gerenciador do Servidor, clique em Ferramentas e em Centro Administrativo do Active Directory.

7. Na **Central Administrativa do Active Directory, clique em adatum (local), no painel Tarefas **, no **nome **de domínio adatum (local),**** clique em Novo** e, no menu em cascata, clique **em ****Unidade Organizacional**.**

8. **Na janela Criar Unidade** Organizacional, na caixa de **texto Nome**, digite **ToSync** e clique em **OK.**

9. Clique duas vezes na unidade organizacional ToSync** recém-criada**, de modo que seu conteúdo apareça no painel de detalhes do console do Centro Administrativo do Active Directory. 

10. **No painel Tarefas**, na **seção ToSync**, clique em Novo** e, no menu em cascata, clique em ****Usuário**.

11. **Na janela Criar usuário, crie uma nova conta de usuário** com as seguintes configurações (deixe outras pessoas com seus valores existentes) e clique em **OK:**
    
    |Configuração|Valor|
    |---|---|
    |Nome completo|**aduser1**|
    |Logon UPN do usuário:|**aduser1**|
    |Logon SamAccountName do usuário: test1|**aduser1**|
    |Senha e Confirmar Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Outras opções de senha|**A senha nunca expira**|


#### Parte 1: instalar o Azure AD Connect

Nesta tarefa, você instalará o conector do Proxy de Aplicativo do Azure AD nesta máquina virtual. 

1. Na sessão de Área de Trabalho Remota para adVM, use o Microsoft Edge para **navegar até o portal do Azure em ****https://portal.azure.come entre usando a **conta de usuário syncadmin** que você criou no exercício anterior.** Quando solicitado, especifique o nome principal do usuário completo e a senha que você registrou no exercício anterior.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Azure Active Directory** e pressione a **tecla Enter**.

3. No portal do Azure, na folha Visão Geral do **AdatumSync\|, no painel de navegação esquerdo, em Gerenciar **, clique em **** Azure AD Connect**.**

4. Na folha Introdução ao **AAD Connect\|, clique em Sincronizar Sincronização** no painel de navegação esquerdo e clique **no link Baixar o **Azure AD Connect****. Você será redirecionado para a página de download do **Azure AD Connect** .

5. Na página de download do **Azure AD Connect** , clique em **Baixar**.

6. Quando solicitado, clique em **Executar** para iniciar o assistente do **Microsoft Azure Active Directory Connect** .

7. **Na página Bem-vindo ao Azure AD Connect do assistente do **Microsoft Azure Active Directory Connect****, clique na caixa **de seleção Concordo com os termos de licença e o aviso** de privacidade e clique em **Continuar**.

8. Na página Configurações Expressas **do assistente do **Microsoft Azure Active Directory Connect**, clique na **opção Personalizar**.**

9. **Na página Instalar componentes** necessários, deixe todas as opções de configuração opcionais desmarcadas e clique em **Instalar**.

10. Na página Entrada** do **usuário, verifique se somente a Sincronização** de Hash de Senha está habilitada **e clique em **Avançar**.

11. **Na página Conectar ao Azure AD**, autentique-se usando as **credenciais da conta de usuário syncadmin** criada no exercício anterior e clique **em Avançar**. 

12. **Na página Conectar seus diretórios**, clique no **botão Adicionar diretório** à direita da entrada adatum.com **** floresta.

13. Na janela da conta de floresta do AD, verifique se a opção Criar **nova conta**** do **AD está selecionada, especifique as seguintes credenciais e clique em **OK:**

    |Configuração|Valor|
    |---|---|
    |Nome do Usuário|**Aluno ADATUM\\**|
    |Senha|**Por favor, use sua senha pessoal criada no Laboratório 06 > Exercício 1 > Tarefa 2**|

14. De volta à **página Conectar seus diretórios**, verifique se a entrada adatum.com** **aparece como um diretório configurado e clique em **Avançar**

15. Na página de configuração** de entrada do Azure AD, observe o aviso informando **que os usuários não poderão entrar no Azure AD com credenciais locais se o sufixo UPN não corresponder a um nome** de domínio verificado, habilite a caixa **de **seleção Continuar sem corresponder todos os sufixos UPN ao domínio** verificado e clique em **Avançar**.

    >**Observação**: conforme explicado anteriormente, isso é esperado, já que você não pôde verificar o domínio **DNS personalizado do Azure AD adatum.com**.

16. Na página Filtragem de domínio e UO, clique na opção **Sincronizar domínios e UOs** selecionados e desmarque a caixa de seleção ao lado do nome **de **** domínio adatum.com**. Clique para expandir **adatum.com**, marque somente a caixa de seleção ao lado da **UO ToSync** e, em seguida, clique em **Avançar** .

17. **Na página Identificando exclusivamente seus usuários**, aceite as configurações padrão e clique em **Avançar**.

18. **Na página Filtrar usuários e dispositivos**, aceite as configurações padrão e clique em **Avançar**.

19. Na página **Perfil**, aceite as configurações padrão e, em seguida, clique em **Avançar**.

20. Na página Pronto para configurar, verifique se a caixa de seleção Iniciar o processo de sincronização quando a configuração for concluída está marcada.

    >A instalação deve levar cerca de um minuto.

21. Revise **as informações na página Configuração concluída** e clique em **Sair** para fechar a **janela Microsoft Azure Active Directory Connect** .


#### Tarefa 3: Verificar a sincronização de diretórios

Nesta tarefa, você verificará se a sincronização de diretórios está funcionando. 

1. Na sessão da Área de Trabalho Remota para **adVM, na janela do Microsoft Edge que exibe o portal do Azure,** navegue até a **folha Usuários - Todos os usuários (Visualização)** do locatário do Adatum Lab Azure AD.

2. **Na folha Usuários \| Todos os usuários**, observe que a lista de objetos de usuário inclui a **conta aduser1**. 

   >**Observação**: talvez seja necessário aguardar alguns minutos e selecionar **Atualizar** para que a conta de **usuário aduser1** apareça.

3. Clique na **conta aduser1** e selecione a guia Propriedades **. Role para baixo até a ****seção Local**, observe que o **atributo Sincronização local habilitada** está definido como **Sim**.

4. **Na folha aduser1**, na seção Informações do **Trabalho**, observe que o **atributo Department** não está definido.

5. Na sessão da Área de Trabalho Remota para adVM, alterne para a Central Administrativa do Active Directory, selecione a **entrada aduser1 na lista de objetos na UO **ToSync** e, no **painel Tarefas**, na **seção aduser1****,** selecione **Propriedades**.******

6. **Na janela aduser1**, na seção Organização **, na ****caixa de texto Departamento**, digite **Vendas** e selecione **OK.**

7. Na sessão da área de trabalho remota da VM de laboratório, inicie o Windows PowerShell.

8. No console Administrador: Windows PowerShell **, execute o seguinte para iniciar a **sincronização delta do Azure AD Connect:

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. Alterne para a janela do Microsoft Edge que exibe a folha aduser1 **, atualize a página e observe que a **propriedade Department está definida como Sales.

    >**Observação**: talvez seja necessário aguardar até três minutos e atualizar a página novamente se o **atributo Department** permanecer não definido.

> **Resultado**: depois de concluir este exercício, você preparou o AD DS para sincronização de diretórios, instalou o Azure AD Connect e verificou a sincronização de diretórios.


**Limpar recursos**

>**Observação**: comece desabilitando a sincronização do Azure AD

1. Na sessão de Área de Trabalho Remota para **adVM,** inicie o Windows PowerShell como Administrador.

2. No console do Windows PowerShell, instale o módulo do PowerShell MsOnline executando o seguinte (quando solicitado, na caixa de diálogo provedor NuGet é necessário continuar, digite **Sim** e pressione Enter.):

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. No console do Windows PowerShell, conecte-se ao locatário do Azure AD do AdatumSync executando o seguinte (quando solicitado, entre com as **credenciais syncadmin** ):

    ```powershell
    Connect-MsolService
    ```

4. No console do Windows PowerShell, desabilite a sincronização do Azure AD Connect executando o seguinte:

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. Verifique se o script foi bem-sucedido executando o seguinte comando no ISE do Windows PowerShell:

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >O resultado deve ser . Se esse não for o caso, aguarde um minuto e execute novamente o comando.

    >**Observação**: em seguida, remova os recursos do Azure
6. Feche a sessão da área de trabalho remota.

7. No portal do Azure, defina o filtro Diretório + assinatura para o **locatário do Azure AD associado à assinatura** do Azure na qual você implantou a **VM do Azure adVM** .

8. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. 

9. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**. 

10. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. Feche o painel do **Cloud Shell**.

    >**Observação**: por fim, remova o locatário do Azure AD
    
    >**Nota 2**: Excluir um inquilino deve ser um processo muito difícil, por isso nunca pode ser feito acidentalmente ou maliciosamente.  Isso significa que remover o locatário como parte desse laboratório muitas vezes não funciona.  Embora tenhamos as etapas aqui para excluir o locatário, não é necessário considerar-se como concluindo este laboratório. Se você tiver a necessidade de remover um inquilino no mundo real, há artigos sobre DOCS.Microsoft.com para ajudá-lo.

12. De volta ao portal do Azure, use o filtro Diretório + assinatura** para alternar para o **locatário do Active Directory do **Azure AdatumSync**.

13. No portal do Azure, navegue até a folha Usuários - Todos os usuários, clique na entrada que representa a conta de usuário syncadmin, na folha syncadmin** - Perfil** clique em Excluir e, quando solicitado a **** confirmar, clique em ****** Sim.******

14. Repita a mesma sequência de etapas para excluir a conta de usuário aduser1** e a **Conta** do **Serviço de Sincronização de Diretórios Local.

15. Navegue até a folha AdatumSync - Visão geral do locatário do Azure AD, clique em Gerenciar locatários** e marque a **caixa de seleção do diretório AdatumSync **, clique em Excluir **, na folha Excluir locatário 'AdatumSync'**, clique no **link Obter permissão para excluir recursos do Azure, na **folha Propriedades** do Active Directory do Azure, defina **Gerenciamento de acesso para recursos**** do **Azure como **Sim** e clique **em ****** Salvar**.**

    >**Nota**: Ao excluir se você receber qualquer sinal de aviso como **Excluir todos os usuários, em seguida, prossiga para excluir os usuários** que você criou ou se o sinal de aviso diz **Excluir aplicativos** do LinkedIn clique na mensagem de aviso e confirme a exclusão do aplicativo LinkedIn, todo o aviso precisa ser endereçado para passar a exclusão do locatário.

16. Saia do portal do Azure e entre novamente. 

17. Navegue de volta para a **folha Excluir locatário 'AdatumSync'** e clique em **Excluir**.

> Para obter informações adicionais sobre essa tarefa, consulte [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
