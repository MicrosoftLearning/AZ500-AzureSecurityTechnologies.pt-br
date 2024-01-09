---
lab:
  title: ' 13: pontos de extremidade de serviço e armazenamento seguro'
  module: Module 03 - Secure data and applications
---

# Laboratório 13: pontos de extremidade de serviço e armazenamento seguro
# Manual do laboratório para o aluno

## Cenário do laboratório

Foi solicitado que você crie uma prova de conceito para demonstrar a proteção de compartilhamentos de arquivos do Azure. Especificamente, você precisa:
    
- Crie um ponto de extremidade de armazenamento para que o tráfego destinado ao Armazenamento do Azure permaneça sempre na rede de backbone do Azure.
- Configure o terminal de armazenamento para que apenas recursos de uma sub-rede específica possam acessar o armazenamento.
- Confirme se os recursos fora da sub-rede específica não podem acessar o armazenamento. 

> Para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório, você vai realizar os seguintes exercícios:

- Exercício 1: Pontos de extremidade de serviço e armazenamento seguro

## Diagrama de Pontos de extremidade de Serviço e Armazenamento Seguro

![imagem](https://user-images.githubusercontent.com/91347931/157534883-29664a05-85d1-4c70-99a7-f16d2360755d.png)

## Instruções

### Exercício 1: Pontos de extremidade de serviço e armazenamento seguro

### Tempo estimado: 45 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma rede virtual
- Tarefa 2: adicionar uma sub-rede à rede virtual e configurar um ponto de extremidade de armazenamento
- Tarefa 3: configurar um grupo de segurança de rede para restringir o acesso à sub-rede
- Tarefa 4: configurar um grupo de segurança de rede para permitir rdp na sub-rede pública
- Tarefa 5: criar uma conta de armazenamento com compartilhamento de arquivo
- Tarefa 6: implantar máquinas virtuais nas sub-redes designadas
- Tarefa 7: testar a conexão de armazenamento da sub-rede privada para confirmar que o acesso é permitido
- Tarefa 8: testar a conexão de armazenamento da sub-rede pública para confirmar que o acesso foi negado 

#### Tarefa 1: criar uma rede virtual

Nesta tarefa, você vai criar uma rede virtual.

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Redes virtuais** e pressione a tecla **Enter**.

3. Na folha **Redes virtuais**, clique em **+ Criar**.

4. Na guia **Noções básicas** da folha **Criar rede virtual**, especifique as seguintes configurações (deixe as outras com seus valores padrão) e clique em **Avançar: Endereços IP**:

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB12**|
    |Nome|**myVirtualNetwork**|
    |Região|**(EUA) Leste dos EUA**|

5. Na guia **Endereços IP** da folha **Criar rede virtual**, defina o **espaço de endereço IPv4** como **10.0.0.0/16**, na coluna **Nome da sub-rede**, clique em **padrão** e, na folha **Editar sub-rede**, especifique as seguintes configurações e clique em **Salvar**:

    |Configuração|Valor|
    |---|---|
    |Nome da sub-rede|**Público**|
    |Intervalo de endereços da sub-rede|**10.0.0.0/24**|

6. De volta à guia **Endereços IP** da folha **Criar rede virtual**, clique em **Examinar + criar**.

7. Na guia **Examinar + Criar** da folha **Criar rede virtual**, clique em **Criar**.

#### Tarefa 2: adicionar uma sub-rede à rede virtual e configurar um ponto de extremidade de armazenamento

Nesta tarefa, você criará outra sub-rede e habilitará um ponto de extremidade de serviço nessa sub-rede. Pontos de extremidade de serviço são habilitados por serviço, por sub-rede. 

1. No portal do Azure, navegue até à folha **Redes virtuais**.

2. Na folha **Redes virtuais**, clique na entrada **myVirtualNetwork**.

3. Na folha **myVirtualNetwork**, na seção **Configurações**, clique em **Sub-redes**.

4. Na folha **myVirtualNetwork \| Sub-redes**, clique em **+ Sub-rede**. 

5. Na folha **Adicionar sub-rede**, especifique as seguintes configurações (deixe as demais com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Nome da sub-rede|**Privado**|
    |Intervalo de endereços da sub-rede|**10.0.1.0/24**|
    |Pontos de extremidade de serviço|**Microsoft.Storage**|

6. Na folha **Adicionar sub-rede**, clique em **Salvar** para adicionar a nova sub-rede.

    >**Observação**: a rede virtual agora tem duas sub-redes: Pública e Privada. 
    
#### Tarefa 3: configurar um grupo de segurança de rede para restringir o acesso à sub-rede

Nesta tarefa, você criará um grupo de segurança de rede com duas regras de segurança de saída (Armazenamento e Internet) e uma regra de segurança de entrada (RDP). Você também irá associar o grupo de segurança de rede à sub-rede privada. Isso restringirá o tráfego de saída das VMs do Azure conectadas a essa sub-rede.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de segurança de rede** e pressione a tecla **Enter**.

2. Na folha **Grupos de segurança de rede**, clique em **+ Criar**.

3. Na guia **Básico**, da folha **Criar grupo de segurança de rede**, execute as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome|**myNsgPrivate**|
    |Region|**Leste dos EUA**|

4. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: nas próximas etapas, você criará uma regra de segurança de saída que permite a comunicação com o serviço de Armazenamento do Azure. 

5. No portal do Azure, navegue de volta para a folha **Grupos de segurança de rede** e clique na entrada **myNsgPrivate**.

6. Na folha **myNsgPrivate**, na seção **Configurações**, clique em **Regras de segurança de saída**.

7. Na folha **myNsgPrivate \| Regras de segurança de saída**, clique em **+ Adicionar**.

8. Na folha ** Adicionar regra de segurança de saída**, especifique as seguintes configurações para permitir explicitamente o tráfego de saída para o Armazenamento do Azure (deixe todos os outros valores com suas configurações padrão): 

    |Configuração|Valor|
    |---|---|
    |Fonte|**Marca de serviço**|
    |Marca de serviço de origem|**VirtualNetwork**|
    |Intervalos de portas de origem|**\***|
    |Destino|**Marca de serviço**|
    |Marca de serviço de destino|**Storage**|
    |Intervalos de portas de destino|**\***|
    |Protocolo|**Qualquer**|
    |Ação|**Permitir**|
    |Prioridade|**1000**|
    |Nome|**Allow-Storage-All**|

9. Na folha **Adicionar regra de segurança de saída**, clique em **Adicionar** para criar a nova regra de saída. 

10. Na folha **myNsgPrivate**, na seção **Configurações**, clique em **Regras de segurança de saída** e em **+ Adicionar**.

11. Na folha **Adicionar regra de segurança de saída**, especifique as seguintes configurações para negar explicitamente o tráfego de saída para a Internet (deixe todos os outros valores com suas configurações padrão): 

    |Configuração|Valor|
    |---|---|
    |Fonte|**Marca de serviço**|
    |Marca de serviço de origem|**VirtualNetwork**|
    |Intervalos de portas de origem|**\***|
    |Destino|**Marca de serviço**|
    |Marca de serviço de destino|**Internet**|
    |Intervalos de portas de destino|**\***|
    |Protocolo|**Qualquer**|
    |Ação|**Deny**|
    |Prioridade|**1100**|
    |Nome|**Deny-Internet-All**|

    >**Observação**: essa regra substitui uma regra padrão em todos os grupos de segurança de rede que permite a comunicação de saída à Internet. 

    >**Observação**: nas próximas etapas você criará uma regra de segurança de entrada que permite o tráfego de RDP (Remote Desktop Protocol) para a sub-rede. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. Conexões de Área de Trabalho Remota são permitidas para a sub-rede para que a conectividade possa ser testada em uma etapa posterior.

12. Na folha **myNsgPrivate**, na seção **Configurações**, clique em **Regras de segurança de entrada** e clique em **+ Adicionar**.

13. Na folha **Adicionar regra de segurança de entrada**, especifique as seguintes configurações (deixando todas as outras configurações com os valores padrão): 

    |Configuração|Valor|
    |---|---|
    |Fonte|**Qualquer**|
    |Intervalos de portas de origem|**\***|
    |Destino|**Marca de serviço**|
    |Marca de serviço de destino|**VirtualNetwork**|
    |Intervalos de portas de destino|**3389**|
    |Protocolo|**TCP**|
    |Ação|**Permitir**|
    |Prioridade|**1200**|                                                    
    |Nome|**Allow-RDP-All**|

14. Na folha **Adicionar regra de segurança de entrada**, clique em **Adicionar** para criar a nova regra de entrada. 

    >**Observação**: agora você associará o grupo de segurança de rede à sub-rede Privada.

15. Na folha **Sub-redes**, selecione **+ Associar** e especifique as seguintes configurações na seção **Associar sub-rede** e clique em **OK**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Privado**|

#### Tarefa 4: configurar um grupo de segurança de rede para permitir rdp na sub-rede pública

Nesta tarefa, você criará um grupo de segurança de rede com uma regra de segurança de entrada (RDP). Você também associará o grupo de segurança de rede à sub-rede Pública. Isso permitirá o acesso RDP à VM Pública.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de segurança de rede** e pressione a tecla **Enter**.

2. Na folha **Grupos de segurança de rede**, clique em **+ Criar**.

3. Na guia **Básico**, da folha **Criar grupo de segurança de rede**, execute as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome|**myNsgPublic**|
    |Region|**Leste dos EUA**|

4. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: nas próximas etapas, você criará uma regra de segurança de saída que permite a comunicação com o serviço de Armazenamento do Azure. 

5. No portal do Azure, navegue de volta para a folha **Grupos de segurança de rede** e clique na entrada **myNsgPublic**.

6. Na folha **myNsgPublic**, na seção **Configurações**, clique em **Regras de segurança de entrada** e clique em **+ Adicionar**.

7. Na folha **Adicionar regra de segurança de entrada**, especifique as seguintes configurações (deixando todas as outras configurações com os valores padrão): 

    |Configuração|Valor|
    |---|---|
    |Fonte|**Qualquer**|
    |Intervalos de portas de origem|**\***|
    |Destino|**Marca de serviço**|
    |Marca de serviço de destino|**VirtualNetwork**|
    |Intervalos de portas de destino|**3389**|
    |Protocolo|**TCP**|
    |Ação|**Permitir**|
    |Prioridade|**1200**|                                                    
    |Nome|**Allow-RDP-All**|

8. Na folha **Adicionar regra de segurança de entrada**, clique em **Adicionar** para criar a nova regra de entrada. 

    >**Observação**: agora você associará o grupo de segurança de rede à sub-rede Pública.

9. Na folha **Sub-redes**, selecione **+ Associar** e especifique as seguintes configurações na seção **Associar sub-rede** e clique em **OK**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Público**|
    
#### Tarefa 5: criar uma conta de armazenamento com compartilhamento de arquivo

Nesta tarefa, você criará uma conta de armazenamento com um compartilhamento de arquivos e obterá a chave da conta de armazenamento.  

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Contas de armazenamento** e pressione a tecla **Enter**.

2. Na folha **Contas de armazenamento**, clique em  **+ Criar**.

3. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, defina as seguintes configurações (deixe as outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome da conta de armazenamento|qualquer nome global exclusivo de 3 a 24 caracteres composto por letras e dígitos|
    |Location|**(EUA) EastUS**|
    |Desempenho|**Padrão (conta para uso geral v2)**|
    |Redundância|**LRS (armazenamento com redundância local)**|

4. Na guia **Básico** da folha **Criar conta de armazenamento**, clique em **Examinar + Criar**, aguarde a conclusão do processo de validação e clique em **Criar**.

    >**Observação**: aguarde até a conta de armazenamento ser criada. Isso deverá levar cerca de dois minutos.

5. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

6. Na folha **Grupos de recursos**, na lista de grupos de recursos, clique na entrada **AZ500LAB12**.

7. Na folha de grupo de recursos **AZ500LAB12**, na lista de recursos, clique na entrada que representa a conta de armazenamento recém-criada.

8. Na folha **Visão geral** da conta de armazenamento, clique em **Compartilhamentos de Arquivos** na guia **Armazenamento de dados** e clique em **+ Compartilhamento de Arquivos**.

9. Na folha **Novo compartilhamento de arquivos**, desmarque a opção **Habilitar backup** na guia backup. 

10. Na folha **Novo compartilhamento de arquivos**, especifique as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Nome|**my-file-share**|

11. Na folha **Novo compartilhamento de arquivo**, clique em **Criar**.

    >**Observação**: agora, recupere e registre o script do PowerShell que cria um mapeamento de unidade para o compartilhamento de arquivos do Azure. 

12. Na folha conta de armazenamento, na lista de compartilhamentos de arquivos, clique em **my-file-share**.

13. Na folha **my-file-share**, clique em **Conectar**.

14. Na folha **Conectar**, na guia **Windows**, copie o script do PowerShell que cria um mapeamento de unidade Z para o compartilhamento de arquivos. 

    >**Observação**: grave este script. Você precisará disso posteriormente neste laboratório para mapear o compartilhamento de arquivos da máquina virtual do Azure na sub-rede **Privada**.

15. Navegue de volta para a folha da conta de armazenamento e, na seção **Segurança + rede** , clique em **Rede**.

16. Na folha **Firewalls e redes virtuais**, selecione a opção **Habilitado a partir de redes virtuais e endereços IP selecionados** e clique no link **+ Adicionar rede virtual existente**. 

17. Na folha **Adicionar redes**, especifique as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Redes virtuais|**myVirtualNetwork**|
    |Sub-redes|**Privado**|

18. Na folha **Adicionar redes**, clique em **Adicionar**. 

19. De volta à folha da conta de armazenamento, clique em **Salvar**.

    >**Observação**: neste ponto do laboratório, você configurou uma rede virtual, um grupo de segurança de rede e uma conta de armazenamento com um compartilhamento de arquivos. 

#### Tarefa 6: implantar máquinas virtuais nas sub-redes designadas

Nesta tarefa, você criará duas máquinas virtuais, uma na sub-rede privada e outra na sub-rede Pública. 

>**Observação**: a primeira máquina virtual será conectada à sub-rede Privada.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior do portal da Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

2. Na folha **Máquinas virtuais**, clique em **+ Criar** e, na lista suspensa, clique em **+ Máquina virtual do Azure**

3. Na guia **Básico** da folha **Criar uma máquina virtual**, defina as seguintes configurações (deixe as outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome da máquina virtual|**myVmPrivate**|
    |Region|**(US)East US**|
    |Imagem|**Windows Server 2022 Datacenter: Azure Edition - Gen 2**|
    |Nome de Usuário|**localadmin**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Porta de entrada públicas|**Nenhuma**|
    |Já tem uma licença do Windows Server?|**Não** selecionado|

    >**Observação**: para portas de entrada públicas, dependeremos do NSG pré-criado. 

4. Clique em **Avançar: Discos >** e, na guia **Discos** da folha **Criar uma máquina virtual**, defina o tipo de**disco do sistema operacional** como **HDD padrão** e clique em **Avançar: Rede >**.

5. Clique em **Avançar: Rede >**, na guia **Rede** da folha **Criar máquina virtual**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Privado (10.0.1.0/24)**|
    |IP público|**(new)myVmPrivate-ip**|
    |Grupo de segurança de rede da NIC|**Nenhuma**|

6. Clique em **Avançar: Gerenciamento >**, na guia **Gerenciamento** da folha **Criar uma máquina virtual**, aceite as configurações padrão e clique em **Revisar + criar**.

7. Na folha **Revisar + criar**, verifique se a validação foi bem-sucedida e clique em **Criar**.

    >**Observação**: a segunda máquina virtual será conectada à sub-rede Pública.

8. Na folha **Máquinas virtuais**, clique em **+ Adicionar** e, na lista suspensa, clique em **+ Máquina virtual do Azure**.

9. Na guia **Básico** da folha **Criar uma máquina virtual**, defina as seguintes configurações (deixe as outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome da máquina virtual|**myVmPublic**|
    |Region|**(US)East US**|
    |Imagem|**Windows Server 2022 Datacenter: Azure Edition - Gen 2**|
    |Nome de Usuário|**localadmin**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Porta de entrada públicas|**Nenhuma**|
    |Já tem uma licença do Windows Server?|**Não** selecionado|

    >**Observação**: para portas de entrada públicas, dependeremos do NSG pré-criado. 

10. Clique em **Avançar: Discos >** e, na guia **Discos** da folha **Criar uma máquina virtual**, defina o tipo de**disco do sistema operacional** como **HDD padrão** e clique em **Avançar: Rede >**.

11. Clique em **Avançar: Rede >**, na guia **Rede** da folha **Criar máquina virtual**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Público (10.0.0.0/24)**|
    |IP público|**(new)myVmPublic-ip**|
    |Grupo de segurança de rede da NIC|**Nenhuma**|

12. Clique em **Avançar: Gerenciamento >**, na guia **Gerenciamento** da folha **Criar uma máquina virtual**, aceite as configurações padrão e clique em **Revisar + criar**.

13. Na folha **Revisar + criar**, verifique se a validação foi bem-sucedida e clique em **Criar**.

    >**Observação**: você pode continuar para a próxima tarefa depois que a implantação da VM do Azure **myVMPrivate** for concluída.

#### Tarefa 7: testar a conexão de armazenamento da sub-rede privada para confirmar que o acesso é permitido

Nesta tarefa, você vai se conectar à máquina virtual myVMPrivate via Área de Trabalho Remota e mapear uma unidade para o compartilhamento de arquivos. 

1. Navegue de volta até a folha **Máquinas virtuais**. 

2. Na folha **Máquinas virtuais**, clique na entrada **myVMPrivate**.

3. Na folha **myVMPrivate**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

4. Clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **myVMPrivate** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça as seguintes credenciais:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|**localadmin**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: aguarde até que a sessão da Área de Trabalho Remota seja aberta e o Gerenciador do Servidor seja carregado.

    >**Observação**: agora você mapeará a unidade Z para um compartilhamento de arquivos do Azure na sessão da Área de Trabalho Remota para um computador com Windows Server 2022

5. Na sessão de Área de Trabalho Remota para **myVMPrivate**, clique em ** Iniciar** e em** ISE do Windows PowerShell**.

6. Na janela **ISE do Windows PowerShell**, abra o painel **Script ** e, em seguida, cole e execute o script do PowerShell que você registrou anteriormente neste laboratório. O script tem o seguinte formato:

    ```powershell
    $connectTestResult = Test-NetConnection -ComputerName <storage_account_name>.file.core.windows.net -Port 445
    if ($connectTestResult.TcpTestSucceeded) {
       # Save the password so the drive will persist on reboot
       cmd.exe /C "cmdkey /add:`"<storage_account_name>.file.core.windows.net`" /user:`"localhost\<storage_account_name>`"  /pass:`"<storage_account_key>`""
       # Mount the drive
       New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage_account_name>.file.core.windows.net\my-file-share" -Persist
    } else {
       Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
    }
    ```
    >**Observação**: o espaço reservado `<storage_account_name>` representa o nome da conta de armazenamento que hospeda o compartilhamento de arquivos e uma `<storage_account_key>` de suas chaves primárias

7. Inicie o Explorador de Arquivos e verifique se o mapeamento da unidade Z: foi criado com êxito.

8. Em seguida, no painel do console **ISE do Windows PowerShell**, execute o seguinte para verificar se a máquina virtual não tem conectividade de saída com a Internet:

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Observação**: o teste vai falhar, pois o grupo de segurança de rede associado à sub-rede Privada não permite acesso de saída para a Internet.

9. Feche a sessão da Área de Trabalho Remota para a VM do Azure **myVmPrivate**.

    >**Observação**: neste ponto, você confirmou que a máquina virtual na sub-rede privada pode acessar a conta de armazenamento. 

####  Tarefa 8: testar a conexão de armazenamento da sub-rede pública para confirmar que o acesso foi negado 

1. Navegue de volta até a folha **Máquinas virtuais**. 

2. Na folha **Máquinas virtuais**, clique na entrada **myVMPublic**.

3. Na folha **myVMPublic**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

4. Clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **myVMPublic** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça as seguintes credenciais:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|**localadmin**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: aguarde até que a sessão da Área de Trabalho Remota seja aberta e o Gerenciador do Servidor seja carregado.

    >**Observação**: agora você mapeará a unidade Z para um compartilhamento de arquivos do Azure na sessão da Área de Trabalho Remota para um computador com Windows Server 2022

5. Na sessão da Área de Trabalho Remota para **myVMPublic**, clique em **Iniciar** e, em seguida, clique em **ISE do Windows PowerShell**.

6. Na janela **ISE do Windows PowerShell**, abra o painel **Script** e, em seguida, cole e execute o mesmo script do PowerShell que você executou na sessão da Área de Trabalho Remota na VM do Azure **myVMPrivate**.

    >**Observação**: desta vez, você receberá o erro **New-PSDrive: Acesso negado**. 

    >**Observação**: o acesso é negado porque a máquina virtual *myVmPublic* é implantada na sub-rede Pública. A sub-rede Pública não tem um ponto de extremidade de serviço habilitado para Armazenamento do Azure. A conta de armazenamento só permite o acesso à rede a partir da sub-rede Privada.

7. Em seguida, no painel do console **ISE do Windows PowerShell**, execute o seguinte para verificar se a máquina virtual tem conectividade de saída com a Internet: 

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Observação**: o teste será bem-sucedido porque não há nenhuma regra de segurança de saída para negar a Internet na sub-rede Pública.

8. Feche a sessão da Área de Trabalho Remota para a VM do Azure **myVmPublic**.

    >**Observação**: neste ponto, você confirmou que a máquina virtual na sub-rede Pública não pode acessar a conta de armazenamento, mas tem acesso à Internet.


**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que não sejam gerados custos inesperados. 

1. Abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Certifique-se que o **PowerShell** esteja selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 

