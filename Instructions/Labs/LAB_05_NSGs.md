---
lab:
  title: 05 – Grupos de segurança de rede e grupos de segurança de aplicativo
  module: Module 02 - Implement Platform Protection
---

# Laboratório 05: grupos de segurança de rede e grupos de segurança de aplicativo
# Manual de laboratório do aluno

## Cenário do laboratório

Foi solicitado que você implemente a infraestrutura de rede virtual da sua organização e teste para garantir que ela esteja funcionando corretamente. Especialmente:

- A organização tem dois grupos de servidores: servidores Web e servidores de gerenciamento.
- Cada grupo de servidores deve estar em seu próprio grupo de segurança do aplicativo. 
- Você conseguirá usar RDP nos servidores de gerenciamento, mas não nos servidores Web.
- Os servidores Web exibirão a página da Web do IIS quando acessados pela Internet. 
- As regras do grupo de segurança de rede devem ser usadas para controlar o acesso à rede. 

> Para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região para ser usada na aula. 

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: criar a infraestrutura de rede virtual
- Exercício 2: implantar máquinas virtuais e testar os filtros de rede

## Diagrama dos grupos de segurança de rede e de aplicativo

![imagem](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## Instruções

### Exercício 1: criar a infraestrutura de rede virtual

### Tempo estimado: 20 minutos

> Para todos os recursos deste laboratório, estamos usando a região **Leste (EUA)**. Confirme com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma rede virtual com uma sub-rede.
- Tarefa 2: criar dois grupos de segurança de aplicativo.
- Tarefa 3: criar um grupo de segurança de rede e associá-lo à sub-rede da rede virtual.
- Tarefa 4: criar regras de segurança NSG de entrada para todo o tráfego para servidores da Web e RDP para os servidores de gerenciamento.

#### Tarefa 1: criar uma rede virtual

Nesta tarefa, você criará uma rede virtual para usar com os grupos de segurança de rede e de aplicativo. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Redes virtuais** e pressione a tecla **Enter**.

3. Na folha **Redes Virtuais**, clique em **+ Criar**.

4. Na guia **Noções básicas** da folha **Criar rede virtual**, especifique as seguintes configurações (deixe outras com seus valores padrão) e clique em **Avançar: Endereços IP**:

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB07**|
    |Nome|**myVirtualNetwork**|
    |Region|**Leste dos EUA**|

5. Na guia **Endereços IP** da folha **Criar rede virtual**, defina o **espaço de endereço IPv4** como **10.0.0.0/16** e, se necessário, na coluna **Nome da sub-rede**, clique em **padrão**. Na folha **Editar sub-rede**, especifique as seguintes configurações e clique em **Salvar**:

    |Configuração|Valor|
    |---|---|
    |Nome da sub-rede|**default**|
    |Intervalo de endereços da sub-rede|**10.0.0.0/24**|

6. De volta à guia **Endereços IP** da folha **Criar rede virtual**, clique em **Revisar + criar**.

7. Na guia **Revisar + criar** da folha **Criar rede virtual**, clique em **Criar**.

#### Tarefa 2: criar grupos de segurança do aplicativo

Nesta tarefa, você criará um grupo de segurança do aplicativo.

1. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de segurança do aplicativo** e pressione a tecla **Enter**.

2. Na folha **Grupos de segurança do aplicativo**, selecione **+ Criar**.

3. Na guia **Básico** da folha **Criar grupo de segurança do aplicativo**, especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Nome|**myAsgWebServers**|
    |Region|**Leste dos EUA**|

    >**Observação**: este grupo será para os servidores Web.

4. Clique em **Revisar + criar** e em **Criar**.

5. Navegue de volta para a folha **Grupos de segurança do aplicativo** e clique em **+ Criar**.

6. Na guia **Básico** da folha **Criar grupo de segurança do aplicativo**, especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Nome|**myAsgMgmtServers**|
    |Region|**Leste dos EUA**|

    >**Observação**: este grupo será para os servidores de gerenciamento.

7. Clique em **Revisar + criar** e em **Criar**.

#### Tarefa 3: criar um grupo de segurança de rede e associar o NSG à sub-rede

Nesta tarefa, você criará um grupo de segurança de rede. 

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de segurança de rede** e pressione a tecla **Enter**.

2. Na folha **Grupos de segurança de rede**, clique em **+ Criar**.

3. Na guia **Noções básicas** da folha **Criar grupo de segurança de rede**, especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB07**|
    |Nome|**myNsg**|
    |Region|**Leste dos EUA**|

4. Clique em **Revisar + criar** e em **Criar**.

5. No portal do Azure, navegue de volta para a folha **Grupos de segurança de rede** e clique na entrada **myNsg**.

6. Na folha **myNsg**, na seção **Configurações**, clique em **Sub-redes** e em **+ Associar**. 

7. Na folha **Associar sub-rede**, especifique as seguintes configurações e clique em **OK**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**default**|

#### Tarefa 4: criar regras de segurança do NSG de entrada para todo o tráfego para servidores Web e RDP para os servidores. 

1. Na folha **myNsg**, na seção **Configurações**, clique em **Regras de segurança de entrada**.

2. Analise as regras de segurança de entrada padrão e clique em **+ Adicionar**.

3. Na folha **Adicionar regra de segurança de entrada**, especifique as seguintes configurações para permitir as portas TCP 80 e 443 para o grupo de segurança do aplicativo **myAsgWebServers** (deixe todos os outros valores com o padrão): 

    |Configuração|Valor|
    |---|---|
    |Destino|na lista suspensa, selecione **Grupo de segurança do aplicativo** e clique em **myAsgWebServers**|
    |Intervalos de portas de destino|**80.443**|
    |Protocolo|**TCP**|
    |Prioridade|**100**|                                                    
    |Nome|**Allow-Web-All**|

4. Na folha **Adicionar regra de segurança de entrada**, selecione **Adicionar** para criar a nova regra de entrada. 

5. Na folha **myNsg**, na seção **Configurações**, clique em **Regras de segurança de entrada** e em **+ Adicionar**.

6. Na folha **Adicionar regra de segurança de entrada**, especifique as seguintes configurações para liberar a porta RDP (TCP 3389) para o grupo de segurança do aplicativo **myAsgMgmtServers** (deixe todos os outros valores com o padrão): 

    |Configuração|Valor|
    |---|---|
    |Destino|na lista suspensa, selecione **Grupo de segurança do aplicativo** e clique em **myAsgMgmtServers**|
    |Intervalos de portas de destino|**3389**|
    |Protocolo|**TCP**|
    |Prioridade|**110**|                                                    
    |Nome|**Allow-RDP-All**|

7. Na folha **Adicionar regra de segurança de entrada**, selecione **Adicionar** para criar a nova regra de entrada. 

> Resultado: você implantou uma rede virtual, segurança de rede com regras de segurança de entrada e dois grupos de segurança de aplicativo. 

### Exercício 2: implantar máquinas virtuais e testar filtros de rede

### Tempo estimado: 25 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma máquina virtual para usar como um servidor Web.
- Tarefa 2: criar uma máquina virtual para usar como um servidor de gerenciamento. 
- Tarefa 3: associar cada adaptador de rede das máquinas virtuais aos respectivos grupos de segurança de aplicativo
- Tarefa 4: testar a filtragem de tráfego de rede.

#### Tarefa 1: criar uma máquina virtual para usar como um servidor Web.

Nesta tarefa, você criará uma máquina virtual para usar como um servidor Web.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior do portal da Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

2. Na folha **Máquinas virtuais**, clique em **+ Criar** e, na lista suspensa, clique em **+ Máquina virtual do Azure**.

3. Na guia **Básico** da folha **Criar uma máquina virtual**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|**AZ500LAB07**|
   |Nome da máquina virtual|**myVmWeb**|
   |Region|**(EUA) Leste dos EUA**|
   |Imagem|**Windows Server 2022 Datacenter: Azure Edition – x64 Gen2**|
   |Tamanho|**Standard D2s v3**|
   |Nome de Usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Confirmar senha|**Digite sua senha novamente**|
   |Porta de entrada públicas|**Nenhuma**|
   |Deseja usar uma licença existente do Windows Server |**Não**|

    >**Observação**: para portas de entrada públicas, usaremos o NSG pré-criado. 

4. Clique em **Avançar: Discos>** e, na guia **Discos** da folha **Criar uma máquina virtual**, defina o **tipo do disco do sistema operacional** como **HDD Standard** e clique em **Avançar: Rede >**.

5. Na guia **Rede** da folha **Criar uma máquina virtual**, selecione a rede **myVirtualNetwork** criada anteriormente.

6. Em **Grupo de segurança de rede da NIC**, selecione **Nenhum**.

7. Clique em **Avançar: Gerenciamento >** e clique em **Avançar: Monitoramento >**. Na guia **Monitoramento** da folha **Criar uma máquina virtual**, verifique a seguinte configuração:

   |Configuração|Valor|
   |---|---|
   |Diagnóstico de inicialização|**Habilitado com a conta de armazenamento gerenciada (recomendado)**|

8. Clique em **Revisar + criar**. Na folha **Revisar + criar**, verifique se a validação foi bem-sucedida e clique em **Criar**.

#### Tarefa 2: criar uma máquina virtual para usar como um servidor de gerenciamento. 

Nesta tarefa, você criará uma máquina virtual para usar como um servidor de gerenciamento.

1. No portal do Azure, navegue de volta para a folha **Máquinas virtuais**, clique em **+ Criar** e, na lista suspensa, clique em **+ Máquina virtual do Azure**.

2. Na guia **Básico** da folha **Criar uma máquina virtual**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|**AZ500LAB07**|
   |Nome da máquina virtual|**myVMMgmt**|
   |Region|(EUA) Leste dos EUA|
   |Imagem|**Windows Server 2022 Datacenter: Azure Edition – x64 Gen2**|
   |Tamanho|**Standard D2s v3**|
   |Nome de Usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Porta de entrada públicas|**Nenhuma**|
   |Já tem uma licença do Windows Server|**Não**|

    >**Observação**: para portas de entrada públicas, usaremos o NSG pré-criado. 

3. Clique em **Avançar: Discos>** e, na guia **Discos** da folha **Criar uma máquina virtual**, defina o **tipo do disco do sistema operacional** como **HDD Standard** e clique em **Avançar: Rede >**.

4. Na guia **Rede** da folha **Criar uma máquina virtual**, selecione a rede **myVirtualNetwork** criada anteriormente.

5. Em **Grupo de segurança de rede da NIC**, selecione **Nenhum**.

6. Clique em **Avançar: Gerenciamento >** e clique em **Avançar: Monitoramento >**. Na guia **Monitoramento** da folha **Criar uma máquina virtual**, verifique a seguinte configuração:

   |Configuração|Valor|
   |---|---|
   |Diagnóstico de inicialização|**Habilitado com a conta de armazenamento gerenciada (recomendado)**|

7. Clique em **Revisar + criar**. Na folha **Revisar + criar**, verifique se a validação foi bem-sucedida e clique em **Criar**.

    >**Observação**: aguarde até que ambas as máquinas virtuais sejam provisionadas antes de continuar. 

#### Tarefa 3: associar cada adaptador de rede das máquinas virtuais ao respectivo grupo de segurança de aplicativo.

Nesta tarefa, você associará cada adaptador de rede das máquinas virtuais ao grupo de segurança de aplicativo correspondente. O adaptador da máquina virtual myVMWeb será associado ao ASG myAsgWebServers. O adaptador da máquina virtual myVMMgmt será associado ao ASG myAsgMgmtServers. 

1. No portal do Azure, navegue de volta para a folha **Máquinas virtuais** e verifique se ambas as máquinas virtuais estão listadas com o status **Em execução**.

2. Na lista de máquinas virtuais, clique na entrada **myVMWeb**.

3. Na folha **myVMWeb**, na seção **Rede**, clique em **Configurações de rede** e, na folha **myVMWeb \| Configurações de rede**, clique na guia **Grupos de segurança de aplicativo**.

4. Clique em + **Adicionar grupos de segurança de aplicativo**. Na lista **Grupo de segurança de aplicativo**, selecione **myAsgWebServers** e clique em **Salvar**.

5. Navegue de volta para a folha **Máquinas virtuais** e, na lista de máquinas virtuais, clique na entrada **myVMMgmt**.

6. Na folha **myVMMgmt**, na seção **Rede**, clique em **Configurações de rede** e, na folha **myVMMgmt \| Configurações de rede**, clique na guia **Grupos de segurança de aplicativo**.

7. Clique em + **Adicionar grupos de segurança de aplicativo**. Na lista **Grupo de segurança de aplicativo**, selecione **myAsgMgmtServers** e clique em **Salvar**.

#### Tarefa 4: testar a filtragem de tráfego de rede

Nesta tarefa, você testará os filtros de tráfego de rede. Será possível usar o RDP na máquina virtual myVMMgmnt. Será possível se conectar a partir da Internet à máquina virtual myVMWeb e exibir a página da Web padrão do IIS.  

1. Navegue de volta até a folha da Máquina virtual **myVMMgmt**.

2. Na folha **myVMMgmt**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

3. Clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **myVMMgmt** através da Área de Trabalho Remota. Quando a autenticação for solicitada, forneça as seguintes credenciais:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: verifique se a conexão da Área de Trabalho Remota foi bem-sucedida. Neste ponto, você confirmou que pode se conectar via Área de Trabalho Remota à myVMMgmt.

4. No portal do Azure, navegue até a folha da máquina virtual **myVMWeb**.

5. Na folha **myVMWeb**, na seção **Conteúdo**, clique em **Executar comando** e, em seguida, clique em **RunPowerShellScript**.

6. No painel **Executar Script de Comando**, execute o seguinte para instalar a função de servidor Web em **myVmWeb**:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**Observação**: aguarde a conclusão da instalação. Isso poderá levar alguns minutos. Nesse ponto, você pode verificar se myVMWeb pode ser acessada via HTTP/HTTPS.

7. No portal do Azure, volte para a folha **myVMWeb**.

8. Na folha **myVMWeb**, identifique o **endereço IP Público** da VM myVmWeb do Azure.

9. Abra outra guia no navegador da Web e navegue até o endereço IP que você identificou na etapa anterior.

    >**Observação**: a página do navegador deve exibir a página de boas-vindas padrão do IIS porque a porta 80 tem permissão de entrada da Internet com base na configuração do grupo de segurança do aplicativo **myAsgWebServers**. O adaptador de rede da VM myVMWeb do Azure está associado a esse grupo de segurança do aplicativo. 

> Resultado: você validou que as configurações NSG e ASG estão funcionando e o tráfego está sendo gerenciado corretamente. 

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não incorra em custos inesperados. 

1. Abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
