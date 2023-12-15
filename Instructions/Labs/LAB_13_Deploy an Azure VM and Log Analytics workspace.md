---
lab:
  title: 13 - Implantar uma VM do Azure e um espaço de trabalho do Log Analytics
  module: Module 04 - Manage security operations
---

# Laboratório 13: Implantar uma VM do Azure e um espaço de trabalho do Log Analytics

# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar um espaço de trabalho de machione virtual e Log Analytics do Azure.

- Implantar uma máquina virtual do Azure
- Criar um espaço de trabalho do Log Analytics.

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício – criar uma máquina virtual do Azure
  
## Instruções

### Implantar uma máquina virtual do Azure

### Tempo do exercício: 10 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Implantar uma máquina virtual do Azure 

#### Implantar uma máquina virtual do Azure

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

3. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um grupo de recursos que será usado neste laboratório:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Nota**: Este grupo de recursos será usado para os laboratórios 13, 14 e 15. 

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma nova máquina virtual do Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  Quando solicitado a fornecer credenciais, use estas:

    |Configuração|Valor|
    |---|---|
    |Usuário |localadmin|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >Aguarde até que a implantação seja concluída. 

7. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para confirmar se a máquina virtual chamada **myVM** foi criada e seu **ProvisioningState** foi **bem-sucedido**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Feche o painel do Cloud Shell. 

#### Tarefa 1: Crie um workspace do Log Analytics.

Nesta tarefa, você criará um espaço de trabalho do Log Analytics. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Espaços de trabalho do** Log Analytics e pressione a **tecla Enter**.

2. Na folha Espaços de trabalho do **** Log Analytics, clique em ** + Criar**.

3. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Nome|qualquer nome globalmente exclusivo|
    |Region|**Leste dos EUA**|

4. Selecione **Examinar + criar**.

5. Na guia **Examinar + criar** da folha **Criar Aplicativo de Funções**, selecione **Criar**.

> Resultados: você implantou uma máquina virtual do Azure e criou um espaço de trabalho do Log Analytics.

>**Observação**: não remova os recursos deste laboratório, pois eles são necessários para o laboratório do Microsoft Defender for Cloud e o laboratório do Microsoft Sentinel.
 
