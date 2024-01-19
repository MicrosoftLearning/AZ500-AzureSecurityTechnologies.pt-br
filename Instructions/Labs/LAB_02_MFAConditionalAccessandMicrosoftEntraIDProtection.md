---
lab:
  title: 02 – MFA e acesso condicional
  module: Module 01 - Manage Identity and Access
---

# Laboratório 02: MFA e acesso condicional
# Manual de laboratório do aluno

## Cenário do laboratório

Foi solicitado a você a criação de uma prova de conceito de recursos que aprimoram a autenticação do Microsoft Entra ID. Especificamente, você deseja avaliar:

- Autenticação multifator do Microsoft Entra ID
- Acesso condicional da ID do Microsoft Entra
- Políticas de acesso condicional baseadas em risco do Microsoft Entra ID

> Para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região para ser usada na aula. 

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: implantar uma VM do Azure usando um modelo do Azure Resource Manager
- Exercício 2: implementar o Azure MFA
- Exercício 3: implementar políticas de acesso condicional do Microsoft Entra ID 
- Exercício 4: implementar a proteção de identidade do Microsoft Entra ID

## MFA – Acesso condicional – Diagrama de proteção de identidade

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/246a3798-6f50-4a41-99c2-71e9ab6a4c8f)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**
- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** 

### Exercício 1: implantar uma VM do Azure usando um modelo do Azure Resource Manager

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: implantar uma VM do Azure usando modelo do Azure Resource Manager.

#### Tarefa 1: implantar uma VM do Azure usando modelo do Azure Resource Manager

Nesta tarefa, você criará uma máquina virtual usando um modelo do ARM. Esta máquina virtual será usada no último exercício deste laboratório. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: faça logon no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório e a função Administrador global no locatário do Microsoft Entra ID associado a essa assinatura.

2. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado**.

    >**Observação**: você também pode selecionar **Implantação de modelo (implantar usando modelos personalizados)** na lista do **Marketplace**.

3. Na folha **Implantação personalizada**, selecione a opção **Criar seu próprio modelo no editor**.

4. Na folha **Editar modelo**, selecione **Carregar arquivo**, localize o arquivo **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json** e escolha **Abrir**.

    >**Observação**: analise o conteúdo do modelo e observe que ele implanta uma VM do Azure que hospeda o Windows Server 2019 Datacenter.

5. Na folha **Editar modelo**, selecione **Salvar**.

6. De volta à folha **Implantação personalizada**, selecione **Editar parâmetros**.

7. Na folha **Editar parâmetros**, selecione **Carregar arquivo**, localize o arquivo **\\Allfiles\\Labs\\ 04\\az-500-04_azuredeploy.parameters.json** e escolha **Abrir**.

    >**Observação**: analise o conteúdo do arquivo de parâmetros observando os valores adminUsername e adminPassword.

8. Na folha **Editar parâmetros**, selecionar **Salvar**.

9. Na folha **Implantação personalizada**, verifique se as seguintes configurações estão definidas (deixe as outras com seus valores padrão):

   >**Observação**: você precisará criar uma senha exclusiva que será usada para criar VMs (máquinas virtuais) para o resto do curso. A senha deve ter pelo menos 12 caracteres e atender aos requisitos de complexidade definidos (a senha deve ter três dos seguintes: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial). [Requisitos de senha de VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm-). Guarde a senha.

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|selecione **Criar novo** e digite o nome **AZ500LAB04**|
   |Localização|**Leste dos EUA**|
   |Tamanho da VM|**Standard_D2s_v3**|
   |Nome da VM|**az500-04-vm1**|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Crie sua própria senha e registre-a para referência futura. Essa senha lhe será solicitada para o acesso do laboratório necessário.**|
   |Nome da Rede Virtual|**az500-04-vnet1**|

   >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

10. Selecione **Revisar + criar** e, em seguida, **Criar**.

    >**Observação:** prossiga para o próximo exercício sem aguardar a conclusão da implantação. Você usará a máquina virtual incluída nesta implantação no último exercício deste laboratório.

> Resultado: você iniciou uma implantação de modelo de uma VM **az500-04-vm1** do Azure que será usada no último exercício deste laboratório.


### Exercício 2: implementar o Azure MFA

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um novo locatário do Microsoft Entra ID.
- Tarefa 2: ativar a versão de avaliação P2 do Microsoft Entra ID.
- Tarefa 3: criar usuários e grupos do Microsoft Entra ID
- Tarefa 4: atribuir licenças P2 do Microsoft Entra ID aos seus usuários.
- Tarefa 5: definir configurações do Azure MFA
- Tarefa 6: validar a configuração do MFA

#### Tarefa 1: criar um novo locatário do Microsoft Entra ID

Nessa tarefa, você criará um novo locatário do Microsoft Entra ID. 

1. No portal do Azure, a caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

2. Na folha com a **Visão geral** do locatário atual do Microsoft Entra ID, selecione **Gerenciar locatários** e, na próxima tela, escolha **+ Criar**.

3. Na guia **Noções básicas** da folha **Criar um locatário**, verifique se a opção **Microsoft Entra ID** está selecionada e clique **Avançar: Configuração >**.

4. Na guia **Configuração** do painel **Criar um locatário**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da organização|**AdatumLab500-04**|
   |Nome de domínio inicial|um nome exclusivo que consiste de uma combinação de letras e dígitos|
   |País ou Região|**Estados Unidos**|

    >**Observação**: registre o nome de domínio inicial. Você precisará disso nesse laboratório.

5. Clique em **Examinar + Criar** e em **Criar**.
6. Adicione o código Captcha na lâmina **Ajude-nos a provar que você não é um robô** e, em seguida, selecione o botão **Enviar**. 

    >**Observação**: aguarde até que o novo locatário seja criado. Use o ícone de **Notificação** para monitorar o status da implantação. 


#### Tarefa 2: ativar a versão de avaliação P2 do Microsoft Entra ID

Nesta tarefa, você se inscreverá para a avaliação gratuita P2 do Microsoft Entra ID. 

1. No portal do Azure, na barra de ferramentas, clique no ícone **Diretório + assinatura**, localizado à direita do ícone do Cloud Shell. 

2. Na folha **Diretório + assinatura**, escolha o locatário recém-criado, **AdatumLab500-04**, e selecione no botão **Alternar** para defini-lo como o diretório atual.

    >**Observação**: talvez seja necessário atualizar a janela do navegador se a entrada **AdatumLab500-04** não aparecer na lista de filtros **Diretório + assinatura** .

3. No portal do Azure, a caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**. Na seção **Gerenciar**, da folha **AdatumLab500-04**, selecione **Licenças**.

4. Na folha **Licenças\|Visão geral**, em **Tarefas rápidas**, selecione **Obter uma avaliação gratuita**.

5. Expanda P2 do MICROSFT ENTRA ID e selecione **Ativar.**


#### Tarefa 3: criar usuários e grupos do Microsoft Entra ID

Nesta tarefa, você criará três usuários: aaduser1 (administrador global), aaduser2 (usuário) e aaduser3 (usuário). Você precisará do nome UPN e da senha de cada usuário para tarefas posteriores. 

1. Navegue de volta até a folha **AdatumLab500-04** do Microsoft Entra ID e, na seção **Gerenciar**, selecione **Usuários**.

2. Na folha **Usuários \| Todos os usuários**, selecione **+ Novo Usuário** e, em seguida, **Criar novo usuário**. 

3. Na folha **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações na guia Básico (deixando as demais com seus valores padrão) e selecione **Próximo: Propriedades**:

   |Configuração|Valor|
   |---|---|
   |Nome UPN|**aaduser1**|
   |Nome|**aaduser1**|
   |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada|
   
      >**Observação**: registre o nome UPN completo. Você pode copiar seu valor selecionando o botão **Copiar para a área de transferência** no lado direito da lista suspensa com o nome do domínio. Você precisará disso mais tarde neste laboratório.
   
      >**Observação**: registre a senha do usuário. Você pode copiar seu valor selecionando o botão **Copiar para a área de transferência** no lado direito da caixa de texto. Você precisará disso mais tarde neste laboratório. 
   
4. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com seus valores padrão) e clique em **Avançar: Atribuições >**.

5. Na guia **Atribuições**, escolha **+ Adicionar função** e procure e selecione **Administrador Global**. Escolha **Selecionar**, **Revisar + criar** e, em seguida, clique em **Criar**.

6. De volta à folha **Usuários \| Todos os usuários**, selecione **+ Novo Usuário**. 

7. Na folha **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações (deixando as demais com seus valores padrão) e escolha **Avançar: Propriedades >**:

   |Configuração|Valor|
   |---|---|
   |Nome UPN|**aaduser2**|
   |Nome|**aaduser2**|
   |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada| 

    >**Observação**: registre o nome UPN completo e a senha.

8. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com seus valores padrão) e selecione **Revisar + criar** e, em seguida, **Criar**.

9. De volta à folha **Usuários \| Todos os usuários**, selecione **+ Novo Usuário**. 

10. Na folha **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações (deixando as demais com seus valores padrão) e escolha **Avançar: Propriedades >**:

    |Configuração|Valor|
    |---|---|
    |Nome UPN|**aaduser3**|
    |Nome|**aaduser3**|
    |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada|

    >**Observação**: registre o nome UPN completo e a senha.

11. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com seus valores padrão) e selecione **Revisar + criar** e, em seguida, **Criar**.

    >**Observação**: neste ponto, você deve ter três novos usuários listados na página **Usuários**. 
    
#### Tarefa 4: atribuir licenças P2 do Microsoft Entra ID Premium a seus usuários

Nesta tarefa, você atribuirá cada usuário à licença P2 do Microsoft Entra ID Premium.

1. Na folha **Usuários \| Todos os usuários**, selecione a entrada que representa sua conta de usuário. 

2. Na folha com as propriedades de suas contas de usuário, selecione **Editar propriedades**.  Confirme se o Local de uso está definido como **Estados Unidos**. Caso contrário, defina o local e selecione **Salvar**.

3. Navegue de volta para a folha **AdatumLab500-04** do Microsoft Entra ID e, na seção **Gerenciar**, selecione **Licenças**.

4. Na folha **Licenças \| Visão Geral**, selecione **Todos os produtos**, marque a caixa de seleção **P2 do Microsoft Entra ID Premium** e clique em **+ Atribuir**.

5. Na folha **Atribuir licença**, selecione **+ Adicionar usuários e grupos**.

6. Na folha **Usuários**, selecione **aaduser1**, **aaduser2**, **aaduser3** e sua conta de usuário e clique em **Selecionar**.

7. De volta à folha **Atribuir licenças**, selecione **Opções de atribuição**, verifique se todas as opções estão habilitadas, selecione **Revisar + atribuir** e escolha **Atribuir**.

8. Saia do portal do Azure e faça logon novamente usando a mesma conta. (Esta etapa é necessária para que a atribuição da licença entre em vigor).

    >**Nota**: neste ponto, você atribuiu licenças P2 do Microsoft Entra ID Premium a todas as contas de usuário que você usará neste laboratório. Não esqueça de sair e fazer logon novamente. 

#### Tarefa 5: definir configurações do Azure MFA

Nesta tarefa, você configurará a MFA e a habilitará para aaduser1. 

1. No portal do Azure, navegue de volta para a folha de locatário **AdatumLab500-04** do Microsoft Entra ID.

    >**Observação**: verifique se você está usando o locatário AdatumLab500-04 do Microsoft Entra ID.

2. Na folha de locatário **AdatumLab500-04** do Microsoft Entra ID, na seção **Gerenciar**, selecione **Segurança**.

3. Na folha **Segurança \| Introdução**, na seção **Gerenciar**, selecione **Autenticação multifator**.

4. Na folha **Autenticação Multifator \| Introdução**, selecione o link **Configurações adicionais da autenticação multifator baseada em nuvem**. 

    >**Observação**: isso abrirá uma nova guia do navegador com a página **Autenticação multifator**.

5. Na página **Autenticação multifator**, selecione a guia **configurações de serviço** e revise as **opções de verificação**. Observe que **Mensagem de texto para o telefone**, **Notificação por meio de aplicativo móvel** e **Código de verificação do aplicativo móvel ou token de hardware** estão habilitados. Selecione **Salvar** e **fechar**.

6. Alterne para a guia **usuários**, selecione a entrada **aaduser1**, clique no link **Habilitar** e, quando solicitado, escolha **habilitar autenticação multifator** e, em seguida, selecione **fechar**.

7. Observe que a coluna **status da Autenticação Multifator** para **aaduser1** agora está **Habilitada**.

8. Selecione **aaduser1** e observe que, neste ponto, você também tem a opção **Impor**. 

    >**Observação**: a alteração do status do usuário de Habilitado para Imposto afeta apenas os aplicativos integrados herdados do Microsoft Entra ID que não oferecem suporte ao Azure MFA e, uma vez que o status muda para Imposto, eles precisam do uso de senhas de aplicativo.

9. Com a entrada **aaduser1** selecionada, escolha **Gerenciar configurações do usuário** e analise as opções disponíveis: 

   - Exigir que os usuários selecionados forneçam os métodos de contato novamente.

   - Excluir todas as senhas de aplicativos já existentes geradas pelos usuários selecionados.

   - Restaurar a autenticação multifator em todos os dispositivos lembrados.

10. Selecione **cancelar** e volte para a guia do navegador com a folha **Autenticação Multifator \| Introdução** no portal do Azure.

11. Na seção **Configurações**, selecione **Alerta de fraude**.

12. Na folha **Autenticação Multifator \| Alerta de fraude**, defina as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Permitir que os usuários enviem alertas de fraude|**Em**|
    |Bloquear automaticamente os usuários que relatarem fraudes|**Em**|
    |Código para relatar fraudes durante a saudação inicial|**0**|

13. Clique em **Salvar**

    >**Observação**: neste ponto, você ativou a MFA para aaduser1 e definiu as configurações do alerta de fraude. 

14. Navegue de volta para a folha do locatário **AdatumLab500-04** do Microsoft Entra ID, na seção **Gerenciar**, selecione **Propriedades**, em seguida, clique no link **Gerenciar Padrões de segurança** na parte inferior da folha. Na folha **Habilitar Padrões de Segurança**, selecione **Desabilitado**. Selecione **Minha Organização está usando o Acesso Condicional** como o *Motivo da desabilitação*, clique em **Salvar**, leia o aviso e escolha **Desabilitar**.

    >**Observe**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra ID . Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra ID. Verifique se você está conectado como um usuário com a função Administrador global no locatário do Microsoft Entra ID.

#### Tarefa 6: validar a configuração do MFA

Nesta tarefa, você validará a configuração da MFA testando o logon da conta de usuário aaduser1. 

1. Abra uma janela do navegador InPrivate.

2. Navegue até o portal do Azure, **`https://portal.azure.com/`**, e faça logon usando a conta de usuário **aaduser1**. 

    >**Observação**: para entrar, você precisa fornecer um nome totalmente qualificado da conta de usuário **aaduser1**, incluindo o nome de domínio DNS do locatário do Microsoft Entra ID, que você registrou anteriormente neste laboratório. Esse nome de usuário está no formato aaduser1@`<your_tenant_name>`.onmicrosoft.com, em que `<your_tenant_name>` é o espaço reservado que representa o nome exclusivo do locatário do Microsoft Entra ID. 

3. Quando solicitado, selecione **Avançar** na caixa de diálogo **Mais informações necessárias**.

    >**Observação**: a sessão do navegador será redirecionada para a página **Verificação de segurança adicional**.

4. Na página **Proteja a sua conta**, selecione o link **Desejo configurar um método diferente**, na lista suspensa **Qual método você gostaria de usar?**, selecione **Telefone** e **Confirmar**.

5. Na página **Proteja a sua conta** , selecione seu país ou região, digite seu número de celular na área **Insira o número de telefone**, verifique se a opção **Envie-me um código** está selecionada e escolha **Avançar**.
 
6. Na página **Proteja sua conta**, digite o código recebido na mensagem de texto em seu celular e selecione **Avançar**.

7. Na página **Proteja sua conta**, confirme que a verificação foi bem-sucedida e selecione **Avançar**.

8. Se for solicitado um método de autenticação adicional, selecione em **Desejo usar um método diferente**, escolha **Email** na lista suspensa, e selecione **Confirmar**, forneça o endereço de email pretendido e selecione **Avançar**. Depois de receber o email correspondente, identifique o código, forneça-o e selecione **Concluído**.

9. Quando solicitado, insira a senha. Lembre-se de guardá-la.

10. Verifique se você fez o logon com êxito no portal do Azure.

11. Saia como **aaduser1** e feche a janela InPrivate do navegador.

> Resultado: você criou um novo locatário do AD, configurou usuários do AD, configurou a MFA e testou a experiência da MFA para um usuário. 


### Exercício 3: implementar políticas de acesso condicional do Microsoft Entra ID 

### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Tarefa 1: configurar uma política de acesso condicional.
- Tarefa 2: testar a política de acesso condicional

#### Tarefa 1: configurar uma política de acesso condicional. 

Nesta tarefa, revisaremos as configurações da política de acesso condicional e criaremos uma política que exige uma MFA ao fazer logon no portal do Azure. 

1. No portal do Azure, navegue de volta para a folha de locatário **AdatumLab500-04** do Microsoft Entra ID.

2. Na folha **AdatumLab500-04**, na seção **Gerenciar**, selecione **Segurança**.

3. Na folha **Segurança\| Introdução**, na seção **Proteger**, selecione **Acesso Condicional**. No painel de navegação esquerdo, selecione **Políticas**.

4. Na folha **Acesso Condicional \| Políticas**, selecione **+ Nova política**. 

5. Na folha **Novo**, defina as seguintes configurações:

   - Na caixa de texto **Nome**, digite **AZ500Policy1**
    
   - Em **Usuários**, escolha **0 usuários e grupos selecionados**. No lado direito, em Incluir, habilite **Selecionar usuários e grupos** >> e marque a caixa de seleção **Usuários e Grupos**. Na folha **Selecionar usuários e grupos**, marque a caixa de seleção **aaduser2** e escolha **Selecionar**.
    
   - Em **Recursos de destino**, escolha **Nenhum recurso de destino selecionado**, clique em **Selecionar aplicativos**, e em Selecionar, escolha **Nenhum**. Na folha **Selecionar**, marque a caixa de seleção **Gerenciamento do Microsoft Azure** e escolha **Selecionar**. 

     >**Observação**: analise o aviso de que essa política afeta o acesso ao portal do Azure.
    
   - Em **Condições**, escolha **0 condições selecionadas**, e em **Risco de entrada**, selecione **Não configurado**. Na folha **Risco de entrada**, revise os níveis de risco, mas não faça alterações e feche a folha **Risco de entrada**.
    
   - Em **Plataformas de dispositivo**, selecione **Não configurado**, examine as plataformas de dispositivo que podem ser incluídas, mas não faça alterações, e selecione **Concluído**.
    
   - Em **Locais**, selecione **Não configurado** e revise as opções de local sem fazer alterações.
    
   - Na seção **Controles de acesso**, em **Concessão**, escolha **0 controles selecionados**. Na folha **Concessão**, marque a caixa de seleção **Exigir autenticação multifator** e escolha **Selecionar**
    
   - Defina **Habilitar política** como **Ativo**.

6. Na folha **Novo**, clique em **Criar**. 

    >**Observação**: neste ponto, você tem uma política de acesso condicional que exige uma MFA para entrar no portal do Azure. 

#### Tarefa 2: testar a política de acesso condicional.

Nesta tarefa, você fará logon no portal do Azure como **aaduser2** e verificará se a MFA é exigida. Você também excluirá a política antes de continuar para o próximo exercício. 

1. Abra uma janela InPrivate do Microsoft Edge.

2. Em uma nova janela do navegador, vá para o portal do Azure, **`https://portal.azure.com/`**, e faça logon com a conta de usuário **aaduser2**.

3. Quando solicitado, selecione **Avançar** na caixa de diálogo **Mais informações necessárias**.

    >**Observação**: a sessão do navegador será redirecionada para a página **Proteja sua conta**.
    
4. Na página **Proteja a sua conta**, selecione o link **Desejo configurar um método diferente**, na lista suspensa **Qual método você gostaria de usar?**, selecione **Telefone** e **Confirmar**.

5. Na página **Proteja a sua conta** , selecione seu país ou região, digite seu número de celular na área **Insira o número de telefone**, verifique se a opção **Envie-me um código** está selecionada e escolha **Avançar**.

6. Na página **Proteja sua conta**, digite o código recebido na mensagem de texto em seu celular e selecione **Avançar**.

7. Na página **Proteja sua conta**, confirme que a verificação foi bem-sucedida e selecione **Avançar**.

8. Na página **Proteja sua conta**, selecione **Concluído**.

9. Quando solicitado, insira a senha. Lembre-se de guardá-la.

10. Verifique se você fez o logon com êxito no portal do Azure.

11. Saia como **aaduser2** e feche a janela InPrivate do navegador.

    >**Observação**: agora você verificou se a política de acesso condicional recém-criada impõe a MFA quando aaduser2 entra no portal do Azure.

12. De volta à janela do navegador com o portal do Azure, navegue de volta até a folha de locatário **AdatumLab500-04** do Microsoft Entra ID.

13. Na folha **AdatumLab500-04**, na seção **Gerenciar**, selecione **Segurança**.

14. Na folha **Segurança\| Introdução**, na seção **Proteger**, selecione **Acesso Condicional**. No painel de navegação esquerdo, selecione **Políticas**.

15. Na folha **Acesso Condicional \| Políticas**, selecione as reticências ao lado de **AZ500Policy1**, escolha **Excluir** e, quando solicitado a confirmar, selecione **Sim**.

    >**Observação**: resultado: neste exercício, você implementa uma política de acesso condicional para exigir uma MFA quando um usuário faz logon no portal do Azure. 

>Resultado: você configurou e testou o acesso condicional do Microsoft Entra ID.

### Exercício 4: implantar políticas baseadas em risco no acesso condicional

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: visualizar as opções de proteção de identidade do Microsoft Entra ID no portal do Azure
- Tarefa 2: configurar uma política de risco do usuário
- Tarefa 3: configurar uma política de risco de entrada
- Tarefa 4: simular eventos de risco contra as políticas de proteção de identidade do Microsoft Entra ID 
- Tarefa 5: analisar os relatórios de proteção de identidade do Microsoft Entra ID

#### Tarefa 1: habilitar a proteção de identidade do Microsoft Entra ID

Nesta tarefa, você verá as opções de proteção de identidade do Microsoft Entra ID no portal do Azure. 

1. Se necessário, faça logon no Portal do Azure (**`https://portal.azure.com/`**).

    >**Observe**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra ID . Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra ID. Verifique se você está conectado como um usuário com a função Administrador global no locatário do Microsoft Entra ID.

#### Tarefa 2: configurar uma política de risco do usuário

Nesta tarefa, você criará uma política de risco do usuário. 

1. Navegue até o locatário **AdatumLab500-04** do Microsoft Entra ID > **Segurança** > **Acesso Condicional** > **Políticas**.

2. Selecione **+ Nova política**.

3. Na caixa de texto **Nome**, digite o nome de política **AZ500Policy2**.

4. Em **Atribuições** > **Usuários**, escolha **0 usuários e grupos selecionados**.

5. Em **Incluir**, escolha **Selecionar Usuários e grupos**, clique em **Usuários e grupos**, selecione **aaduser2** e **aaduser3** e escolha **Selecionar**.

6. Em **Excluir**, escolha **Usuários e grupos**, selecione **aaduser1** e clique em **Selecionar**. 

7. Em **Recursos de destino**, escolha **Nenhum recurso de destino selecionado**, verifique se **Aplicativos de nuvem** está selecionado na lista suspensa e, em **Incluir**, selecione **Todos os aplicativos de nuvem**.

8. Em **Condições**, escolha **0 condições selecionadas** e, em **Risco do usuário**, selecione **Não configurado**. Na folha **Risco do usuário**, defina **Configurar** como **Sim**.

9. Em **Configurar os níveis de risco do usuário necessários para que a política seja imposta**, selecione **Alta**.

10. Clique em **Concluído**.

11. Na seção **Controles de acesso**, em **Concessão**, escolha **0 controles selecionados**. Na folha **Concessão**, confirme se a opção **Conceder acesso** está selecionada.

12. Selecione **Exigir autenticação multifator** e **Exigir alteração de senha**.

13. Clique em **Selecionar**.

14. Em **Sessão**, escolha **0 controles selecionados**. Selecione **Frequência de entrada** e, em seguida, **Sempre**.

15. Clique em **Selecionar**.

16. Confirme se **Habilitar política** está definido como **Somente relatório**.

17. Selecione **Criar** para habilitar sua política.

#### Tarefa 3: configurar uma política de risco de entrada

1. Navegue até o locatário **AdatumLab500-04** do Microsoft Entra ID > **Segurança** > **Acesso Condicional**> **Políticas**.

2. Selecione **+ Nova política**.

3. Na caixa de texto **Nome**, digite o nome de política **AZ500Policy3**.

4. Em **Atribuições** > **Usuários**, escolha **0 usuários e grupos selecionados**.

5. Em **Incluir**, escolha **Selecionar Usuários e grupos**, clique em **Usuários e grupos**, selecione **aaduser2** e **aaduser3** e clique em **Selecionar**.

6. Em **Excluir**, escolha **Usuários e grupos**, selecione **aaduser1** e clique em **Selecionar**. 

7. Em **Recursos de destino**, escolha **Nenhum recurso de destino selecionado**, verifique se **Aplicativos de nuvem** está selecionado na lista suspensa e, em **Incluir**, selecione **Todos os aplicativos de nuvem**.

8. Em **Condições**, escolha **0 condições selecionadas**, e em **Risco de entrada**, selecione **Não configurado**. Na folha **Risco de entrada**, defina **Configurar** como **Sim**.

9. Em **Selecione o nível de risco de entrada ao qual esta política será aplicada**, selecione **Alto** e **Médio**.

10. Clique em **Concluído**.

11. Na seção **Controles de acesso**, em **Concessão**, escolha **0 controles selecionados**. Na folha **Concessão**, confirme se a opção **Conceder acesso** está selecionada.   

12. Selecione **Exigir autenticação multifator**.

13. Clique em **Selecionar**.

14. Em **Sessão**, escolha **0 controles selecionados**. Selecione **Frequência de entrada** e, em seguida, **Sempre**.

15. Clique em **Selecionar**.

16. Confirme suas configurações e defina **Habilitar política** como **Ativado**.

17. Selecione **Criar** para habilitar sua política.

#### Tarefa 4: simular eventos de risco contra as políticas de proteção de identidade do Microsoft Entra ID 

> Antes de iniciar essa tarefa, verifique se a implantação de modelo iniciada no Exercício 1 foi concluída. A implantação inclui uma VM do Azure chamada **az500-04-vm1**. 

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra ID associado à assinatura do Azure na qual você implantou a VM do Azure **az500-04-vm1**.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior do portal da Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

3. Na folha **Máquinas Virtuais**, clique na entrada **az500-04-vm1**. 

4. Na folha **az500-04-vm1**, selecione **Conectar**. Confirme se você está na guia **RDP**.

5. Selecione **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **az500-04-vm1** via Área de Trabalho Remota. Quando a autenticação for solicitada, forneça as seguintes credenciais:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: aguarde até que a sessão da Área de Trabalho Remota e o **Gerenciador do Servidor** sejam carregados.  

    >**Observação**: as etapas a seguir são executadas na sessão da Área de Trabalho Remota para a VM do Azure **az500-04-vm1**. 

6. Em **Gerenciador de Servidores**, selecione **Servidor local** e **Configuração de Segurança Aprimorada do IE**.

7. Na caixa de diálogo de **Configuração de Segurança Aprimorada do Internet Explorer**, defina as duas opções como **Desativado** e selecione **OK**.

8. Inicie o **Internet Explorer** e selecione o ícone de engrenagem na barra de ferramentas. No menu suspenso, escolha **Segurança** e, em seguida, **Navegação InPrivate**.

9. Na janela InPrivate do Internet Explorer, navegue até o Projeto de navegador ToR em **https://www.torproject.org/projects/torbrowser.html.en**.

10. Baixe e instale a versão do Windows do navegador ToR com as configurações padrão. 

11. Quando a instalação for concluída, inicie o navegador ToR, use a opção **Conectar** na página inicial e navegue até o Painel de acesso ao aplicativo em **https://myapps.microsoft.com**.

12. Quando solicitado, tente fazer logon com a conta **aaduser3**. 

    >**Observação**: a mensagem **A sua entrada foi bloqueada** será exibida. Isso é esperado, já que essa conta não está configurada com autenticação multifator, que é necessária devido ao maior risco de entrada associado ao uso do navegador ToR.

13. No navegador ToR, selecione a seta para voltar para entrar com a conta **aaduser1** que você criou e configurou para autenticação multifator anteriormente neste laboratório.

    >**Observação**: desta vez, a mensagem **Atividade suspeita detectada** será exibida. Novamente, isso é esperado, já que essa conta está configurada com a autenticação multifator. Considerando o aumento do risco de entrada associado ao uso do Navegador ToR, você terá que usar a autenticação multifator.

14. Use a opção **Verificar** e especifique se deseja verificar sua identidade por texto ou chamada.

15. Conclua a verificação e certifique-se de que você entrou com êxito no Painel de acesso ao aplicativo.

16. Feche sua sessão RDP. 

    >**Observação**: neste ponto, você tentou duas entradas diferentes. Em seguida, você analisará os relatórios do Azure Identity Protection.

#### Tarefa 5: analisar os relatórios de proteção de identidade do Microsoft Entra ID

Nesta tarefa, você analisará os relatórios de proteção de identidade do Microsoft Entra ID gerados a partir dos logons no navegador ToR.

1. De volta ao portal do Azure, use o filtro **Diretório + assinatura** para alternar para o locatário **AdatumLab500-04** do Microsoft Entra ID.

2. Na folha **AdatumLab500-04**, na seção **Gerenciar**, selecione **Segurança**.

3. Na folha **Segurança\|Introdução**, na seção **Relatório**, selecione **Usuários suspeitos**. 

4. Analise o relatório e identifique todas as entradas que fazem referência à conta de usuário **aaduser3**.

5. Na folha **Segurança\|Introdução**, na seção **Relatórios**, selecione **Entradas suspeitas**. 

6. Analise o relatório e identifique todas as entradas correspondentes ao logon com a conta de usuário **aaduser3**.

7. Em **Relatórios**, selecione **Detecções de risco**.

8. Analise o relatório e identifique todas as entradas que representem logons de endereços IP anônimos gerados pelo navegador ToR. 

    >**Observação**: pode levar de 10 a 15 minutos para que os riscos apareçam nos relatórios.

> **Resultado**: você habilitou a proteção de identidade do Microsoft Entra ID, configurou a política de risco do usuário e a política de risco de entrada, bem como validou a configuração da proteção de identidade do Microsoft Entra ID, simulando eventos de risco.

**Limpar recursos**

> Remova os recursos de proteção de identidade que você não precisa mais. 

Use as etapas a seguir para desabilitar as políticas de proteção de identidade no locatário **AdatumLab500-04** do Microsoft Entra ID.

1. No portal do Azure, navegue de volta para a folha de locatário **AdatumLab500-04** do Microsoft Entra ID.

2. Na folha **AdatumLab500-04**, na seção **Gerenciar**, selecione **Segurança**.

3. Na folha **Segurança \| Introdução**, na seção **Proteger**, clique em **Proteção de Identidade**.

4. Na folha **Proteção de Identidade \| Visão Geral**, clique em **Política de risco do usuário**.

5. Na folha **Proteção de Identidade \| Política de risco do usuário**, defina **Imposição de Política** como **Desabilitar** e clique em **Salvar**.

6. Na folha **Proteção de Identidade \| Política de risco de usuário**, clique em **Política de risco de entrada**.

7. Na folha **Proteção de Identidade\| Política de risco de entrada**, defina **Imposição de Política** como **Desabilitar** e clique em **Salvar**.

Use as etapas a seguir para interromper a VM do Azure provisionada anteriormente no laboratório.

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra ID associado à assinatura do Azure na qual você implantou a VM do Azure **az500-04-vm1**.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior do portal da Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

3. Na folha **Máquinas Virtuais**, clique na entrada **az500-04-vm1**. 
 
4. Na folha **az500-04-vm1**, clique em **Parar** e, quando a confirmação for solicitada, clique em **OK** 

>  Não remova nenhum recurso provisionado neste laboratório, pois o laboratório PIM tem uma dependência deles.
