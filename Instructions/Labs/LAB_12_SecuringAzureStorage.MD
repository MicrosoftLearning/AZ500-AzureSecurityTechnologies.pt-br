---
lab:
  title: ' Laboratório: Terminais de serviço e armazenamento seguro'
  module: Module 03 - Secure data and applications
---

# Laboratório &#58; Pontos de extremidade de serviço e armazenamento seguro
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito para demonstrar a proteção de compartilhamentos de arquivos do Azure. Especificamente, você precisa:
    
- Crie um ponto de extremidade de armazenamento para que o tráfego destinado ao Armazenamento do Azure permaneça sempre na rede de backbone do Azure.
- Configure o ponto de extremidade de armazenamento para que apenas recursos de uma sub-rede específica possam acessar o armazenamento.
- Confirme se os recursos fora da sub-rede específica não podem acessar o armazenamento. 

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Laboratório: Terminais de serviço e armazenamento seguro

## Laboratório: Terminais de serviço e armazenamento seguro

![imagem](https://user-images.githubusercontent.com/91347931/157534883-29664a05-85d1-4c70-99a7-f16d2360755d.png)

## Instruções

### Laboratório: Terminais de serviço e armazenamento seguro

### Tempo estimado: 45 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma rede virtual
- Tarefa 2: Adicionar uma sub-rede à rede virtual e configurar um ponto de extremidade de armazenamento
- Tarefa 3: Configurar um grupo de segurança de rede para restringir o acesso à sub-rede
- Tarefa 4: Configurar um grupo de segurança de rede para permitir rdp na sub-rede pública
- Criar uma conta de armazenamento com compartilhamento de arquivo
- Tarefa 6: Implantar máquinas virtuais nas sub-redes designadas
- Tarefa 7: Testar a conexão de armazenamento da sub-rede privada para confirmar se o acesso é permitido
- Tarefa 8: Testar a conexão de armazenamento da sub-rede pública para confirmar se o acesso foi negado 

#### Tarefa 1: criar uma rede virtual

Nesta seção, você criará uma rede virtual.

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Redes** virtuais e pressione a **tecla Enter**.

3. Na folha **Rede Virtual**, selecione  **Criar**.

4. Na guia Noções básicas da folha Criar máquina virtual, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB12**|
    |Nome|**myVirtualNetwork**|
    |Região|**(EUA) Leste dos EUA**|

5. **Na guia Endereços** IP da folha Criar rede virtual, defina o **espaço** de endereço IPv4 como **10.0.0.0/16**, na coluna Nome** da ****sub-rede, clique em padrão** e, na **folha Editar sub-rede****, especifique as seguintes configurações e clique **em **Salvar**:

    |Configuração|Valor|
    |---|---|
    |Nome da sub-rede|**Público**|
    |Intervalo de endereços da sub-rede|**10.0.0.0/24**|

6. De volta à **guia Endereços** IP da **folha Criar rede** virtual, clique em **Revisar + criar**.

7. Na guia **Examinar + Criar** da folha **Criar máquina virtual**, selecione **Criar**.

#### Tarefa 2: Adicionar uma sub-rede à rede virtual e configurar um ponto de extremidade de armazenamento

Nesta tarefa, você criará outra sub-rede e habilitará um ponto de extremidade de serviço nessa sub-rede. Pontos de extremidade de serviço são habilitados por serviço, por sub-rede. 

1. No portal do Azure, navegue até Redes virtuais.

2. **Na folha Redes** virtuais, clique na **entrada myVirtualNetwork**.

3. Na página **myVirtualNetwork**, na seção **Configurações**, selecione **Sub-redes**.

4. Na folha **myVnet – Sub-redes\|, clique em **+Sub-rede. 

5. Na folha Novo Contêiner, especifique as seguintes configurações (deixe as demais com seus valores padrão) e selecione OK:

    |Configuração|Valor|
    |---|---|
    |Nome da sub-rede|**Privado**|
    |Intervalo de endereços da sub-rede|**10.0.1.0/24**|
    |Pontos de extremidade de serviço|**Microsoft.Storage**|

6. **Na folha Adicionar sub-rede, clique em **Salvar** para adicionar a nova sub-rede**.

    >**Nota**: A rede virtual agora tem duas sub-redes: Pública e Privada. 
    
#### Tarefa 3: Configurar um grupo de segurança de rede para restringir o acesso à sub-rede

Nesta tarefa, você criará um grupo de segurança de rede com duas regras de segurança de saída (Armazenamento e Internet) e uma regra de segurança de entrada (RDP). Associar uma sub-rede ao Grupo de Segurança de Rede Isso restringirá o tráfego de saída das VMs do Azure conectadas a essa sub-rede.

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de segurança de rede e pressione a **tecla Enter**.

2. Na folha **Grupos de segurança de rede**, clique em **Adicionar**.

3. Na guia **Noções básicas**, do **grupo Criar segurança de rede**, execute as seguintes ações: 

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome|myNsgPrivate|
    |Region|**Leste dos EUA**|

4. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: nas próximas etapas, você criará uma regra de segurança de saída que permite a comunicação com o serviço de Armazenamento do Azure. 

5. No portal do Azure, navegue de volta para a **folha Grupos** de segurança de rede e clique na **entrada myNsgPrivate** .

6. **Na folha myNsgPrivate**, na **seção Configurações**, clique em **Regras** de segurança de saída.

7. Na folha de **regras** de segurança myNsgPrivate \| Outbound, clique em **+ Adicionar**.

8. **Na folha Adicionar regra** de segurança de saída, especifique as seguintes configurações para permitir explicitamente o tráfego de saída para o Armazenamento do Azure (deixe todos os outros valores com suas configurações padrão): 

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
    |Nome|Allow-Storage-All|

9. Na folha Adicionar regra de segurança de saída, clique em **Adicionar** para criar a nova regra** de **saída. 

10. **Na folha myNsgPrivate**, na **seção Configurações**, clique em Regras** de segurança de saída e em ****+ Adicionar**.

11. **Na folha Adicionar regra** de segurança de saída, especifique as seguintes configurações para negar explicitamente o tráfego de saída para a Internet (deixe todos os outros valores com suas configurações padrão): 

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
    |Nome|Deny-Internet-All|

    >Essa regra substitui uma regra padrão em todos os grupos de segurança de rede que permite a comunicação de saída à Internet. 

    >Crie uma regra de segurança de entrada que permita o tráfego do protocolo RDP à sub-rede de qualquer lugar. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. Conexões de área de trabalho remota são permitidas para a sub-rede para que a conectividade possa ser testada em uma etapa posterior.

12. **Na folha myNsgPrivate**, na **seção Configurações**, clique em Regras** de segurança de entrada e clique em ****+ Adicionar**.

13. Na folha **Configurações adicionais**, especifique a seguinte configuração, deixando todas as outras configurações com os valores padrão: 

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
    |Nome|Allow-RDP-All|

14. Na folha Adicionar regra de segurança de entrada, clique em **Adicionar** para criar a nova regra** de **entrada. 

    >**Nota**: Agora você associará o grupo de segurança de rede à sub-rede Privada.

15. **Na folha Sub-redes, selecione **+ Associar e especifique as **seguintes configurações na seção Associar** sub-rede**** e clique em **OK:**

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Privado**|

#### Tarefa 4: Configurar um grupo de segurança de rede para permitir rdp na sub-rede pública

Nesta tarefa, você criará um grupo de segurança de rede com uma regra de segurança de entrada (RDP). Associar uma sub-rede ao Grupo de Segurança de Rede Isso permitirá o acesso RDP à VM Pública.

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de segurança de rede e pressione a **tecla Enter**.

2. Na folha **Grupos de segurança de rede**, clique em **Adicionar**.

3. Na guia **Noções básicas**, do **grupo Criar segurança de rede**, execute as seguintes ações: 

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome|**myNsgPublic**|
    |Region|**Leste dos EUA**|

4. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: nas próximas etapas, você criará uma regra de segurança de saída que permite a comunicação com o serviço de Armazenamento do Azure. 

5. No portal do Azure, navegue de volta para a **folha Grupos** de segurança de rede e clique na **entrada myNsgPublic** .

6. **Na folha myNsgPublic**, na **seção Configurações**, clique em Regras** de segurança de entrada e clique em ****+ Adicionar**.

7. Na folha **Configurações adicionais**, especifique a seguinte configuração, deixando todas as outras configurações com os valores padrão: 

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
    |Nome|Allow-RDP-All|

8. Na folha Adicionar regra de segurança de entrada, clique em **Adicionar** para criar a nova regra** de **entrada. 

    >**Nota**: Agora você associará o grupo de segurança de rede à sub-rede Pública.

9. **Na folha Sub-redes, selecione **+ Associar e especifique as **seguintes configurações na seção Associar** sub-rede**** e clique em **OK:**

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Público**|
    
#### Criar uma conta de armazenamento com compartilhamento de arquivo

Nesta tarefa, você criará uma conta de armazenamento com um compartilhamento de arquivos e obterá a chave da conta de armazenamento.  

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Contas** de armazenamento e pressione a **tecla Enter**.

2. Na folha **Contas de armazenamento**, selecione  **+ Criar**.

3. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome da conta de armazenamento|Insira qualquer nome global exclusivo de 3 a 24 caracteres composto por letras e dígitos e começando com uma letra.|
    |Location|**(EUA) Leste dos EUA**|
    |Desempenho|Contas para uso geral v2 Standard|
    |Redundância|**LRS (armazenamento com redundância local)**|

4. Na guia **Básico** da folha **Criar conta de armazenamento**, selecione **Examinar + Criar**, aguarde a conclusão do processo de validação e selecione **Criar**.

    >Aguarde até a conta de armazenamento ser criado. Isso deverá levar cerca de dois minutos.

5. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

6. **Na folha Grupos de recursos, na lista de grupos de recursos**, clique na **entrada AZ500LAB12**.

7. Na folha AZ500LAB12 **** grupo de recursos, na lista de recursos, clique na entrada que representa a conta de armazenamento recém-criada.

8. Na folha Visão geral da conta **de armazenamento, clique em Compartilhamentos de **Arquivos** na guia Armazenamento de dados e clique em ****+ Compartilhamento de** Arquivos**.**

9. **Na folha Novo compartilhamento de** arquivos, desmarque a **opção Habilitar backup** na guia backup. 

10. **Na folha Novo compartilhamento de** arquivos, especifique as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Nome|my-file-share|

11. Na folha **Novo**, clique em **Criar**.

    >**Observação**: agora, recupere e registre o script do PowerShell que cria um mapeamento de unidade para o compartilhamento de arquivos do Azure. 

12. Na folha Conta de armazenamento, na lista de compartilhamentos de arquivos, clique em **meu-compartilhamento de** arquivos.

13. **Na folha my-file-share**, clique em **Conectar**.

14. **Na folha Conectar**, na **guia Windows**, copie o script do PowerShell que cria um mapeamento de unidade Z para o compartilhamento de arquivos. 

    >**Nota**: Grave este script. Você precisará disso posteriormente neste laboratório para mapear o compartilhamento de arquivos da máquina virtual do Azure na **sub-rede Privada** .

15. Navegue de volta para a folha da conta de armazenamento e, na **seção Segurança + rede** , clique em **Rede**.

16. Na **folha Firewalls e redes virtuais, selecione a opção Habilitado a **partir de redes** virtuais e endereços** IP selecionados e clique no **link + Adicionar rede** virtual existente. 

17. **Na folha Adicionar redes**, especifique as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Redes virtuais|**myVirtualNetwork**|
    |Sub-redes|**Privado**|

18. **Na folha Adicionar redes**, clique em **Adicionar**. 

19. De volta à folha da conta de armazenamento, clique em **Salvar**.

    >**Nota**: Neste ponto do laboratório, você configurou uma rede virtual, um grupo de segurança de rede e uma conta de armazenamento com um compartilhamento de arquivos. 

#### Tarefa 6: Implantar máquinas virtuais nas sub-redes designadas

Os scripts a seguir criam duas máquinas virtuais, uma na sub-rede Pública e outra na Privada. 

>**Nota**: A primeira máquina virtual será conectada à sub-rede Privada.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos**, insira **máquinas virtuais** e pressione **Enter**.

2. Na folha Máquinas **** virtuais, clique em + Criar** e, na lista suspensa, clique em ****+ Máquina virtual do Azure**

3. Na guia **Noções básicas** da folha **Criar máquina virtual**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|O nome da assinatura do Azure que você usará neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome da máquina virtual|**myVmPrivate**|
    |Region|**(EUA) Leste dos EUA**|
    |Imagem|Windows Server 2022 Datacenter: Azure Edition - x64 Gen 2|
    |Nome de Usuário|localadmin|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Porta de entrada públicas|**Nenhuma**|
    |Já tem uma Licença do Windows Server? *|**Não** selecionado|

    >**Nota**: Para portas de entrada públicas, dependeremos do NSG pré-criado. 

4. Clique em **Avançar: >** Discos e, na **guia Discos da folha Criar uma máquina** virtual, defina o tipo** de** **disco do **sistema operacional como **HDD** padrão e clique em **Avançar: >** de rede.

5. Na guia Discos da folha Criar máquina virtual, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Privado (10.0.1.0/24)**|
    |IP público|(novo) myVmPrivate-ip|
    |Grupo de segurança de rede da NIC|**Nenhuma**|

6. Clique em Avançar: Gerenciamento >**, na **guia Gerenciamento** da **folha Criar uma máquina** virtual, aceite as configurações padrão e clique em **** Revisar + criar**.

7. Na folha Revisar + criar **, verifique se a **validação foi bem-sucedida e clique em **Criar**.

    >**Nota**: A segunda máquina virtual será conectada à sub-rede Pública.

8. Na folha Máquinas **** virtuais, clique em + Adicionar** e, na lista suspensa, clique em ****+ Máquina** virtual do Azure.

9. Na guia **Noções básicas** da folha **Criar máquina virtual**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|O nome da assinatura do Azure que você usará neste laboratório|
    |Grupo de recursos|**AZ500LAB12**|
    |Nome da máquina virtual|**myVmPublic**|
    |Region|**(EUA) Leste dos EUA**|
    |Imagem|Windows Server 2022 Datacenter: Azure Edition - x64 Gen 2|
    |Nome de Usuário|localadmin|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Porta de entrada públicas|**Nenhuma**|
    |Já tem uma Licença do Windows Server? *|**Não** selecionado|

    >**Nota**: Para portas de entrada públicas, dependeremos do NSG pré-criado. 

10. Clique em **Avançar: >** Discos e, na **guia Discos da folha Criar uma máquina** virtual, defina o tipo** de** **disco do **sistema operacional como **HDD** padrão e clique em **Avançar: >** de rede.

11. Na guia Discos da folha Criar máquina virtual, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**Público (10.0.0.0/24)**|
    |IP público|(novo) myVmPublic-ip|
    |Grupo de segurança de rede da NIC|**Nenhuma**|

12. Clique em Avançar: Gerenciamento >**, na **guia Gerenciamento** da **folha Criar uma máquina** virtual, aceite as configurações padrão e clique em **** Revisar + criar**.

13. Na folha Revisar + criar **, verifique se a **validação foi bem-sucedida e clique em **Criar**.

    >**Observação**: você pode continuar para a próxima tarefa depois que a implantação da VM do **Azure myVMPrivate** for concluída.

#### Tarefa 7: Testar a conexão de armazenamento da sub-rede privada para confirmar se o acesso é permitido

Nesta tarefa, você se conectará à máquina virtual myVMPrivate via Área de Trabalho Remota e mapeará uma unidade para o compartilhamento de arquivos. 

1. Navegue até a **folha Máquina virtual**. 

2. **Na folha Máquinas virtuais**, clique na **entrada myVMPrivate**.

3. **Na folha myVMPrivate**, clique em Conectar** e, no menu suspenso, clique em ****RDP**. 

4. Clique em **Baixar Arquivo** RDP e use-o **para se conectar à VM do Azure myVMPrivate** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça os seguintes credntials:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|localadmin|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Nota**: Aguarde até que a sessão da Área de Trabalho Remota seja aberta e o Gerenciador do Servidor seja carregado.

    >**Observação**: agora você mapeará a unidade Z para um compartilhamento de arquivos do Azure na sessão da Área de Trabalho Remota para um computador com Windows Server 2022

5. Na sessão de Área de Trabalho Remota para **myVMPrivate**, clique em Iniciar** e em **ISE** do **Windows PowerShell.

6. Na janela ISE** do **Windows PowerShell, abra o painel Script** e, em seguida, cole e execute o **script do PowerShell que você registrou anteriormente neste laboratório.  tem o seguinte formato:

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
    >**Nota**: O `<storage_account_name>` espaço reservado representa o nome da conta de armazenamento que hospeda o compartilhamento de arquivos e `<storage_account_key>` uma de suas chaves primárias

7. Inicie o Explorador de Arquivos e verifique se o mapeamento da unidade Z: foi criado com êxito.

8. Em seguida, no painel de console do console ISE **** do Windows PowerShell, execute o seguinte para verificar se a máquina virtual não tem conectividade de saída com a Internet:

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >Você não recebe nenhuma resposta, pois o grupo de segurança de rede associado à sub-rede Privada não permite acesso de saída para a Internet.

9. Feche a sessão da área de trabalho remota para a VM **myVmPrivate**.

    >**Nota**: Neste ponto, você confirmou que a máquina virtual na sub-rede privada pode acessar a conta de armazenamento. 

####  Tarefa 8: Testar a conexão de armazenamento da sub-rede pública para confirmar se o acesso foi negado 

1. Navegue até a **folha Máquina virtual**. 

2. **Na folha Máquinas virtuais**, clique na **entrada myVMPublic**.

3. **Na folha myVMPublic**, clique em Conectar** e, no menu suspenso, clique em ****RDP**. 

4. Clique em **Baixar Arquivo** RDP e use-o **para se conectar à VM do Azure myVMPublic** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça os seguintes credntials:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|localadmin|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Nota**: Aguarde até que a sessão da Área de Trabalho Remota seja aberta e o Gerenciador do Servidor seja carregado.

    >**Observação**: agora você mapeará a unidade Z para um compartilhamento de arquivos do Azure na sessão da Área de Trabalho Remota para um computador com Windows Server 2022

5. Na sessão de Ambiente de Trabalho Remoto para **myVMPublic**, clique em Iniciar** e, em seguida, clique em **** ISE** do Windows PowerShell.

6. Na janela ISE** do Windows PowerShell, abra o painel Script** e, em seguida, cole e execute o **mesmo script do **PowerShell que você executou na sessão da Área de Trabalho Remota na **VM do Azure myVMPrivate**.

    >**Nota**: Desta vez, você receberá o **erro New-PSDrive: Acesso negado** . 

    >O acesso é negado porque a VM myVmPublic é implantada na sub-rede Pública. A sub-rede Pública não tem um ponto de extremidade de serviço habilitado para Armazenamento do Azure. A conta de armazenamento só permite o acesso à rede a partir da sub-rede Privada, não da sub-rede Pública.

7. Em seguida, no painel de console do console ISE **** do Windows PowerShell, execute o seguinte para verificar se a máquina virtual tem conectividade de saída com a Internet: 

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Nota**: O teste será bem-sucedido porque não há nenhuma regra de segurança de saída para negar a Internet na sub-rede Pública.

8. Feche a sessão da área de trabalho remota para a VM **myVmPublic**.

    >**Nota**: Neste ponto, você confirmou que a máquina virtual na sub-rede Pública não pode acessar a conta de armazenamento, mas tem acesso à Internet.


**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados. 

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 

