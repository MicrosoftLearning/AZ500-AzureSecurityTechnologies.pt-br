---
lab:
  title: 04 – MFA e Acesso Condicional
  module: Module 01 - Manage Identity and Access
---

# Laboratório 04: MFA e Acesso Condicional
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito de recursos que aprimoram a autenticação do Microsoft Entra ID. Especificamente, você deseja avaliar:

- Autenticação multifator do Microsoft Entra ID
- Acesso condicional da ID do Microsoft Entra
- Políticas baseadas em risco de acesso condicional ao Microsoft Entra ID

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do Laboratório

Neste laboratório, você realizará os seguintes exercícios:

- Exercício 1: implantar uma VM do Azure usando um modelo do Azure Resource Manager
- Exercício 2: implementar o Azure MFA
- Exercício 3: implementar as políticas de acesso condicional do Microsoft Entra ID 
- Exercício 4: implementar o Microsoft Entra ID Identity Protection

## MFA – Acesso Condicional – Diagrama do Identity Protection

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/246a3798-6f50-4a41-99c2-71e9ab6a4c8f)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**
- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** 

### Exercício 1: implantar uma VM do Azure usando um modelo do Azure Resource Manager

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: implantar uma VM do Azure usando um modelo do Azure Resource Manager.

#### Tarefa 1: implantar VMs do Azure usando um modelo do Azure Resource Manager

Nesta tarefa, você criará uma máquina virtual usando um modelo do ARM. Esta máquina virtual será usada no último exercício para este laboratório. 

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório e a função Administrador Global no locatário do Microsoft Entra ID associado a essa assinatura.

2. No portal do Azure, use a caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado**.

    >**Observação**: você também pode selecionar **Implantação de modelo (implantar usando modelos personalizados)** na lista do **Marketplace** .

3. Na folha **Implantação personalizada**, clique na opção **Criar seu modelo no editor**.

4. No painel **Editar modelo**, clique em **Carregar arquivo**, localize o arquivo **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json** e clique em **Abrir**.

    >**Observação**: revise o conteúdo do modelo e observe que ele implanta uma VM do Azure que hospeda o Windows Server 2019 Datacenter.

5. Na folha **Editar modelo**, clique em **Salvar**.

6. De volta ao painel **Implantação personalizada**, clique em **Editar parâmetros**.

7. No painel **Editar parâmetros**, clique em **Carregar arquivo**, encontre o arquivo **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** e clique em **Abrir**.

    >**Observação**: revise o conteúdo do arquivo de parâmetros observando os valores adminUsername e adminPassword.

8. No painel **Editar parâmetros**, clique em **Salvar**.

9. Na folha **Implantação personalizada**, verifique se as seguintes configurações estão definidas (deixe outras com os valores padrão):

   >**Observação**: você precisará criar uma senha exclusiva que será usada para criar VMs (máquinas virtuais) para o restante do curso. A senha deve ter pelo menos 12 caracteres e atender aos requisitos de complexidade definidos (a senha deve ter três destes itens: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial). [Requisitos de senha da VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm-). Anote a senha.

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB04**|
   |Localização|**Leste dos EUA**|
   |Tamanho da VM|**Standard_D2s_v3**|
   |Nome da VM|**az500-04-vm1**|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Crie sua própria senha e registre-a para referência futura. Essa senha será solicitada para o acesso de laboratório necessário.**|
   |Nome da Rede Virtual|**az500-04-vnet1**|

   >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

10. Clique em **Revisar + criar** e em **Criar**.

    >**Observação:** não aguarde a conclusão da implantação, passe para o próximo exercício. Você usará a máquina virtual incluída nesta implantação no último exercício deste laboratório.

> Resultado: você iniciou uma implantação de modelo de uma VM do Azure **az500-04-vm1** que será usada no último exercício deste laboratório.


### Exercício 2: implementar o Azure MFA

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um novo locatário no Microsoft Entra ID.
- Tarefa 2: ativar a versão de avaliação do Microsoft Entra ID P2.
- Tarefa 3: criar usuários e grupos do Microsoft Entra ID.
- Tarefa 4: atribuir licenças do Microsoft Entra ID P2 aos usuários do Microsoft Entra ID.
- Tarefa 5: definir configurações do Azure MFA.
- Tarefa 6: validar a configuração de MFA

#### Tarefa 1: criar um locatário no Microsoft Entra ID

Na tarefa, você criará um novo locatário no Microsoft Entra ID. 

1. No portal do Azure, use a caixa de texto **Pesquisar recursos, serviços e documentos** no início da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

2. No painel que exibe **Visão geral** do locatário atual do Microsoft Entra ID, clique em **Gerenciar locatários** e, na próxima tela, clique em **+ Criar**.

3. Na guia **Noções básicas** do painel **Criar um locatário**, verifique se a opção ** Microsoft Entra ID** está selecionada e clique em **Avançar: configuração >**.

4. Na guia **Configuração** do painel **Criar um locatário**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da organização|**AdatumLab500-04**|
   |Nome de domínio inicial|um nome exclusivo que consiste em uma combinação de letras e dígitos|
   |País ou Região|**Estados Unidos**|

    >**Observação**: registre o nome de domínio inicial. Você precisará dele posteriormente nesse laboratório.

5. Clique em **Examinar + Criar** e em **Criar**.
6. Adicione o código Captcha ao painel **Ajude-nos a provar que você não é um robô** e clique no botão **Enviar**. 

    >**Observação**: aguarde até que o novo locatário seja criado. Use o ícone **Notificação** para monitorar o status da implantação. 


#### Tarefa 2: ativar a versão de avaliação do Microsoft Entra ID P2

Nesta tarefa, você se inscreverá para a avaliação gratuita do Microsoft Entra ID P2. 

1. No portal do Azure, na barra de ferramentas, clique no ícone **Diretório + assinatura**, localizado à direita do ícone do Cloud Shell. 

2. No painel **Diretório + assinatura**, clique no locatário recém-criado **AdatumLab500-04** e clique no botão **Alternar** para defini-lo como o diretório atual.

    >**Observação**: talvez seja necessário atualizar a janela do navegador se a entrada **AdatumLab500-04** não aparecer na lista de filtros **Diretório + assinatura**.

3. No portal do Azure, use a caixa de texto **Pesquisar recursos, serviços e documentos** no início da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**. No painel **AdatumLab500-04**, na seção **Gerenciar**, clique em **Licenças**.

4. No painel **Licenças \| Visão geral**, em **Tarefas rápidas**, clique em **Obter uma avaliação gratuita**.

5. Expanda o MICROSFT ENTRA ID P2 e clique em **Ativar.**


#### Tarefa 3: criar usuários e grupos do Microsoft Entra ID.

Nesta tarefa, você criará três usuários: aaduser1 (administrador global), aaduser2 (usuário) e aaduser3 (usuário). Você precisará do nome UPN e da senha de cada usuário para tarefas posteriores. 

1. Navegue de volta até a folha do Microsoft Entra ID **AdatumLab500-04** e, na seção **Gerenciar**, clique em **Usuários**.

2. No painel **Usuários \| Todos os usuários**, clique em **+ Novo usuário** e em **Criar novo usuário**. 

3. No painel **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações na guia Noções básicas (deixe as demais com os valores padrão) e clique em **Avançar: Propriedades >**:

   |Configuração|Valor|
   |---|---|
   |Nome UPN|**aaduser1**|
   |Nome|**aaduser1**|
   |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada|
   
      >**Observação**: registre o nome UPN completo. Você pode copiar seu valor clicando no botão **Copiar para a área de transferência** no lado direito da lista suspensa que exibe o nome do domínio. Você precisará dele mais tarde neste laboratório.
   
      >**Observação**: registre a senha do usuário. Você pode copiar seu valor clicando no botão **Copiar para a área de transferência** no lado direito da caixa de texto. Você precisará dele mais tarde neste laboratório. 
   
4. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com os valores padrão) e clique em **Avançar: atribuições >**.

5. Na guia **Atribuições**, clique em **+ Adicionar função** e procure e selecione **Administrador Global**. Clique em **Selecionar** e em **Examinar + criar** e, em seguida, em **Criar**.

6. De volta ao painel **Usuários \| Todos os usuários**, clique em **+ Novo usuário**. 

7. No painel **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações (deixe as demais com os valores padrão) e clique em **Avançar: propriedades >**:

   |Configuração|Valor|
   |---|---|
   |Nome UPN|**aaduser2**|
   |Nome|**aaduser2**|
   |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada| 

    >**Observação**: registre o nome UPN completo do usuário e a senha.

8. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todo o resto com os valores padrão) e clique em **Examinar + criar** e em **Criar**.

9. De volta ao painel **Usuários \| Todos os usuários**, clique em **+ Novo usuário**. 

10. No painel **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações (deixe as demais com os valores padrão) e clique em **Avançar: propriedades >**:

    |Configuração|Valor|
    |---|---|
    |Nome UPN|**aaduser3**|
    |Nome|**aaduser3**|
    |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada|

    >**Observação**: registre o nome UPN completo do usuário e a senha.

11. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todo o resto com os valores padrão) e clique em **Examinar + criar** e em **Criar**.

    >**Observação**: neste ponto, você deve ter três novos usuários listados na página **Usuários**. 
    
#### Tarefa 4: atribuir licenças do Microsoft Entra ID Premium P2 aos usuários do Microsoft Entra ID

Nesta tarefa, você atribuirá cada usuário à licença do Microsoft Entra ID Premium P2.

1. No painel **Usuários \| Todos os usuários**, clique na entrada que representa sua conta de usuário. 

2. No painel que exibe as propriedades de suas contas de usuário, clique em **Editar propriedades**.  Confirme se o Local de Uso está definido como **Estados Unidos**. Caso contrário, defina o local de uso e clique em **Salvar**.

3. Navegue de volta para o painel do Microsoft Entra ID **AdatumLab500-04** e, na seção **Gerenciar**, clique em **Licenças**.

4. No painel **Licenças \| Visão geral**, clique em **Todos os produtos**, marque a caixa de seleção do **Microsoft Entra ID Premium P2** e clique em **+ Atribuir**.

5. No painel **Atribuir licença**, clique em **+ Adicionar usuários e grupos**.

6. No painel **Usuários**, selecione **aaduser1**, **aaduser2**, **aaduser3** e sua conta de usuário, e clique em **Selecionar**.

7. De volta ao painel **Atribuir licenças**, clique em **Opções de atribuição**, verifique se todas as opções estão habilitadas, clique em **Revisar + atribuir** e clique em **Atribuir**.

8. Saia do portal do Azure e entre novamente usando a mesma conta. (Esta etapa é necessária para que a atribuição da licença entre em vigor.)

    >**Observação**: neste ponto, você atribuiu licenças do Microsoft Entra ID Premium P2 a todas as contas de usuário que você usará neste laboratório. Certifique-se de sair e, em seguida, entrar novamente. 

#### Tarefa 5: definir configurações do Azure MFA.

Nesta tarefa, você configurará a MFA e habilitará a MFA para aaduser1. 

1. No portal do Azure, navegue de volta para o painel de locatário do Microsoft Entra ID **AdatumLab500-04**.

    >**Observação**: verifique se você está usando o locatário do Microsoft Entra ID AdatumLab500-04.

2. No painel de locatário do Microsoft Entra ID **AdatumLab500-04**, na seção **Gerenciar**, clique em **Segurança**.

3. No painel **Segurança \| Introdução**, na seção **Gerenciar**, clique em **Autenticação multifator**.

4. No painel **Autenticação Multifator \| Introdução**, clique no link **Configurações adicionais de autenticação multifator baseada na nuvem**. 

    >**Observação**: isso abrirá uma nova guia do navegador, exibindo a página de **Autenticação multifator**.

5. Na página **Autenticação multifator**, clique na guia **configurações de serviço**. Revise as **opções de verificação**. Observe que a **Mensagem de texto para o telefone**, a **Notificação por meio do aplicativo móvel** e o **Código de verificação do aplicativo móvel ou token de hardware** estão habilitados. Clique em **Salvar** e em **Fechar**.

6. Alterne para a guia **usuários**, clique na entrada **aaduser1**, clique no link **Habilitar** e, quando solicitado, clique em **habilitar autenticação multifator** e, em seguida, clique em **fechar**.

7. Observe que a coluna de **status de autenticação multifator** para **aaduser1** agora está **Habilitada**.

8. Clique em **aaduser1** e observe que, neste ponto, você também tem a opção **Aplicar**. 

    >**Observação**: a alteração do status do usuário de Habilitado para Aplicado afeta apenas os aplicativos integrados herdados do Microsoft Entra ID que não oferecem suporte à MFA do Azure e, uma vez que o status muda para Aplicado, exigem o uso de senhas do aplicativo.

9. Com a entrada **aaduser1** selecionada, clique em **Gerenciar configurações do usuário** e revise as opções disponíveis: 

   - Exigir que os usuários selecionados forneçam métodos de contato novamente.

   - Excluir todas as senhas de aplicativos existentes geradas pelos usuários selecionados.

   - Restaurar a autenticação multifator em todos os dispositivos lembrados.

10. Clique em **cancelar** e volte para a guia do navegador que exibe o painel **Autenticação Multifator \| Introdução** no portal do Azure.

11. Na seção **Configurações**, clique em **Alerta de fraude**.

12. No painel **Autenticação Multifator \| Alerta de fraude**, defina as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Permitir que os usuários enviem alertas de fraude|**Em**|
    |Bloquear automaticamente os usuários que relatarem fraudes|**Em**|
    |Código para relatar fraudes durante a saudação inicial|**0**|

13. Clique em **Salvar**

    >**Observação**: neste ponto, você habilitou a MFA para aaduser1 e definiu as configurações de alerta de fraude. 

14. Navegue de volta para o painel do locatário do Microsoft Entra ID **AdatumLab500-04**, na seção **Gerenciar**, clique em **Propriedades** e, em seguida, clique no link **Gerenciar padrões de segurança** na parte inferior do painel, no painel **Habilitar padrões de segurança**, clique em **Desabilitado**. Selecione **Minha Organização está usando o Acesso Condicional** como *Motivo para desabilitar*, clique em **Salvar**, leia o aviso e clique em **Desabilitar**.

    >**Observação**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra ID. Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra ID. Verifique se você está conectado como um usuário com a função Administrador Global no locatário do Microsoft Entra ID.

#### Tarefa 6: validar a configuração de MFA

Nesta tarefa, você validará a configuração de MFA testando o login da conta de usuário aaduser1. 

1. Abra uma janela do navegador InPrivate.

2. Navegue até o portal do Azure, **`https://portal.azure.com/`**, e entre usando a conta de usuário **aaduser1**. 

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da conta de usuário **aaduser1**, incluindo o nome de domínio DNS do locatário do Microsoft Entra ID, que você registrou anteriormente neste laboratório. Esse nome de usuário está no formato aaduser1@`<your_tenant_name>`.onmicrosoft.com, em que `<your_tenant_name>` é o espaço reservado que representa o nome exclusivo do locatário do Microsoft Entra ID. 

3. Quando solicitado, na caixa de diálogo **Mais informações necessárias**, clique em **Avançar**.

    >**Observação**: a sessão do navegador será redirecionada para a página **Verificação de segurança adicional**.

4. Na página **Proteja a sua conta**, selecione o link **Quero configurar um método diferente**, na lista suspensa **Qual método você gostaria de usar?**, selecione **Telefone** e, em seguida, **Confirmar**.

5. Na página **Proteja a sua conta**, selecione seu país ou região, digite o número do telefone celular na área **Inserir número do telefone**, verifique se a opção **Enviar código por SMS** está selecionada e clique em **Avançar**.
 
6. Na página **Proteja a sua conta**, digite o código recebido na mensagem de texto em seu celular e clique em **Avançar**.

7. Na página **Proteja a sua conta**, confira se a verificação foi bem-sucedida e clique em **Avançar**.

8. Caso um método de autenticação adicional seja solicitado, clique em **Quero usar outro método**, selecione **Email** na lista suspensa, clique em **Confirmar**, forneça o endereço de email que você pretende usar e clique em **Avançar**. Depois de receber o email correspondente, identifique o código no corpo do email, forneça-o e clique em **Concluído**.

9. Quando solicitado, insira a senha. Lembre-se de registrar a nova senha.

10. Verifique se você entrou com êxito no portal do Azure.

11. Saia de **aaduser1** e feche a janela do navegador InPrivate.

> Resultado: você criou um locatário do AD, configurou usuários do AD, configurou a MFA e testou a experiência de MFA para um usuário. 


### Exercício 3: implementar as políticas de acesso condicional do Microsoft Entra ID 

### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Tarefa 1: configurar a política de acesso condicional.
- Tarefa 2: testar a política de acesso condicional.

#### Tarefa 1 – Configurar a política de acesso condicional. 

Nesta tarefa, revisaremos as configurações da política de acesso condicional e criaremos uma política que exige a MFA ao entrar no portal do Azure. 

1. No portal do Azure, navegue de volta para o painel de locatário do Microsoft Entra ID **AdatumLab500-04**.

2. No painel **AdatumLab500-04**, na seção **Gerenciar**, clique em **Segurança**.

3. No painel **Segurança \| Introdução**, na seção **Proteger**, clique em **Acesso Condicional**. No painel de navegação esquerdo, clique em **Políticas**.

4. No painel **Acesso Condicional \| Políticas**, clique em **+ Nova política**. 

5. No painel **Novo**, defina as seguintes configurações:

   - Na caixa de texto **Nome**, digite **AZ500Policy1**
    
   - Em **Usuários**, clique em **0 usuários e grupos selecionados**. No lado direito, em Incluir, habilite **Selecionar usuários e grupos** >> marque a caixa de seleção **Usuários e Grupos**, no painel **Selecionar usuários e grupos**, marque a caixa de seleção **aaduser2** e clique em **Selecionar**.
    
   - Em **Recursos de destino**, clique em **Nenhum recurso de destino selecionado**, clique em **Selecionar aplicativos**, em Selecionar, clique em **Nenhum**. No painel **Selecionar**, marque a caixa de seleção de **Gerenciamento do Microsoft Azure** e clique em **Selecionar**. 

     >**Observação**: analise o aviso de que essa política afeta o acesso ao Portal do Azure.
    
   - Em **Condições**, clique em **0 condições selecionadas**, em **Risco de entrada**, clique em **Não configurado**. No painel **Risco de entrada**, analise os níveis de risco, mas não faça alterações e feche o painel **Risco de entrada**.
    
   - Em **Plataformas de dispositivo**, clique em **Não configurado**, analise as plataformas de dispositivo que podem ser incluídas, mas não faça alterações e clique em **Concluído**.
    
   - Em **Locais**, clique em **Não configurado** e analise as opções de local sem fazer alterações.
    
   - Na seção **Controles de acesso**, em **Conceder**, clique em **0 controles selecionados**. No painel **Conceder**, marque a caixa de seleção **Exigir autenticação multifator** e clique em **Selecionar**
    
   - Defina **Habilitar política** como **Ativo**.

6. Na folha **Novo**, clique em **Criar**. 

    >**Observação**: neste momento, você tem uma política de acesso condicional que exige uma MFA para entrar no portal do Azure. 

#### Tarefa 2: testar a política de acesso condicional.

Nesta tarefa, você entrará no portal do Azure como **aaduser2** e verificará se a MFA é necessária. Você também excluirá a política antes de ir para o próximo exercício. 

1. Abra uma janela InPrivate do Microsoft Edge.

2. Em uma nova janela do navegador, vá para o Azure Portal, **`https://portal.azure.com/`**, e entre com sua conta de usuário **aaduser2**.

3. Quando solicitado, na caixa de diálogo **Mais informações necessárias**, clique em **Avançar**.

    >**Observação**: a sessão do navegador será redirecionada para a página **Proteja sua conta**.
    
4. Na página **Proteja a sua conta**, selecione o link **Quero configurar um método diferente**, na lista suspensa **Qual método você gostaria de usar?**, selecione **Telefone** e, em seguida, **Confirmar**.

5. Na página **Proteja a sua conta**, selecione seu país ou região, digite o número do telefone celular na área **Inserir número do telefone**, verifique se a opção **Enviar código por SMS** está selecionada e clique em **Avançar**.

6. Na página **Proteja a sua conta**, digite o código recebido na mensagem de texto em seu celular e clique em **Avançar**.

7. Na página **Proteja a sua conta**, confira se a verificação foi bem-sucedida e clique em **Avançar**.

8. Na página **Proteja a sua conta**, clique em **Concluído**.

9. Quando solicitado, insira a senha. Lembre-se de registrar a nova senha.

10. Verifique se você entrou com êxito no portal do Azure.

11. Saia como **aaduser2** e feche a janela InPrivate do navegador.

    >**Observação**: agora você verificou se a política de acesso condicional recém-criada exige a MFA quando aaduser2 entra no portal do Azure.

12. De volta à janela do navegador que exibe o portal do Azure, navegue de volta até a folha de locatário **AdatumLab500-04** do Microsoft Entra ID.

13. No painel **AdatumLab500-04**, na seção **Gerenciar**, clique em **Segurança**.

14. No painel **Segurança \| Introdução**, na seção **Proteger**, clique em **Acesso Condicional**. No painel de navegação esquerdo, clique em **Políticas**.

15. No painel **Acesso Condicional \| Políticas**, selecione as reticências ao lado de **AZ500Policy1**, clique em **Excluir** e, quando solicitado a confirmar, clique em **Sim**.

    >**Observação**: resultado: neste exercício, você implementa uma política de acesso condicional para exigir uma MFA quando um usuário faz logon no portal do Azure. 

>Resultado: você configurou e testou o acesso condicional ao Microsoft Entra ID.

### Exercício 4: implantar políticas baseadas em risco no Acesso Condicional

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: exibir as opções do Microsoft Entra ID Identity Protection no portal do Azure
- Tarefa 2: configurar uma política de risco do usuário
- Tarefa 3: configurar uma política de risco de entrada
- Tarefa 4: simular eventos de risco em relação às políticas do Microsoft Entra ID Identity Protection 
- Tarefa 5: analisar os relatórios do Microsoft Entra ID Identity Protection

#### Tarefa 1: Habilitar o Microsoft Entra ID Identity Protection

Nesta tarefa, você verá as opções do Microsoft Entra ID Identity Protection no portal do Azure. 

1. Se necessário, entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra ID. Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra ID. Verifique se você está conectado como um usuário com a função Administrador Global no locatário do Microsoft Entra ID.

#### Tarefa 2: configurar uma política de risco do usuário

Nesta tarefa, você criará uma política de risco do usuário. 

1. Navegue até o locatário **AdatumLab500-04** do Microsoft Entra ID > **Segurança** > **Acesso Condicional** > **Políticas**.

2. Clique em **+ Nova política**.

3. Na caixa de texto **Nome**, digite o nome de política **AZ500Policy2**.

4. Em **Atribuições** > **Usuários**, clique em **0 usuários e grupos selecionados**.

5. Em **Incluir**, clique em **Selecionar usuários e grupos**, clique em **Usuários e grupos**, selecione **aaduser2** e **aaduser3** e clique em **Selecionar**.

6. Em **Excluir**, clique em **Usuários e grupos**, selecione **aaduser1** e clique em **Selecionar**. 

7. Em **Recursos de destino**, clique em **Nenhum recurso de destino selecionado**, confirme se os **Aplicativos de nuvem** estão selecionados na lista suspensa e, em **Incluir**, selecione **Todos os aplicativos de nuvem**.

8. Em **Condições**, clique em **0 condições selecionadas** e, em **Risco do usuário**, clique em **Não configurado**. No painel **Risco do usuário**, defina **Configurar** como **Sim**.

9. Em **Configurar os níveis de risco do usuário necessários para que a política seja imposta**, selecione **Alta**.

10. Clique em **Concluído**.

11. Na seção **Controles de acesso**, em **Conceder**, clique em **0 controles selecionados**. No painel **Conceder**, confirme se a opção **Conceder acesso** está selecionada.

12. Selecione **Exigir autenticação multifator** e **Exigir alteração de senha**.

13. Clique em **Selecionar**.

14. Em **Sessão**, clique em **0 controles selecionados**. Selecione **Frequência de entrada** e, em seguida, **Sempre**.

15. Clique em **Selecionar**.

16. Confirme se a configuração **Habilitar política** está definida como **Somente relatório**.

17. Clique em **Criar** para habilitar sua política.

#### Tarefa 3: configurar uma política de risco de entrada

1. Navegue até o locatário **AdatumLab500-04** do Microsoft Entra ID > **Segurança** > **Acesso Condicional**> **Políticas**.

2. Selecione **+ Nova política**.

3. Na caixa de texto **Nome**, digite o nome de política **AZ500Policy3**.

4. Em **Atribuições** > **Usuários**, clique em **0 usuários e grupos selecionados**.

5. Em **Incluir**, clique em **Selecionar usuários e grupos**, clique em **Usuários e grupos**, selecione **aaduser2** e **aaduser3** e clique em **Selecionar**.

6. Em **Excluir**, clique em **Usuários e grupos**, selecione **aaduser1** e clique em **Selecionar**. 

7. Em **Recursos de destino**, clique em **Nenhum recurso de destino selecionado**, confirme se os **Aplicativos de nuvem** estão selecionados na lista suspensa e, em **Incluir**, selecione **Todos os aplicativos de nuvem**.

8. Em **Condições**, clique em **0 condições selecionadas**, em **Risco de entrada**, clique em **Não configurado**. No painel **Risco de entrada**, defina **Configurar** como **Sim**.

9. Em **Selecionar o nível de risco de entrada ao qual essa política será aplicada**, selecione **Alto** e **Médio**.

10. Clique em **Concluído**.

11. Na seção **Controles de acesso**, em **Conceder**, clique em **0 controles selecionados**. No painel **Conceder**, confirme se a opção **Conceder acesso** está selecionada.   

12. Selecione **Exigir autenticação multifator**.

13. Clique em **Selecionar**.

14. Em **Sessão**, clique em **0 controles selecionados**. Selecione **Frequência de entrada** e, em seguida, **Sempre**.

15. Clique em **Selecionar**.

16. Confirme suas configurações e defina **Habilitar política** como **Ativado**.

17. Clique em **Criar** para habilitar sua política.

#### Tarefa 4: simular eventos de risco em relação às políticas do Microsoft Entra ID Identity Protection 

> Antes de iniciar essa tarefa, verifique se a implantação de modelo iniciada no Exercício 1 foi concluída. A implantação inclui uma VM do Azure chamada **az500-04-vm1**. 

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra ID associado à assinatura do Azure na qual você implantou a VM do Azure **az500-04-vm1**.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

3. No portal **Máquinas virtuais**, clique na entrada **az500-04-vm1**. 

4. No portal **az500-04-vm1**, clique em **Conectar**. Confirme se você está na guia **RDP**.

5. Clique em **Baixar Arquivo RDP** e use-o para se conectar à VM **az500-04-vm1** do Azure via Área de Trabalho Remota. Quando a autenticação for solicitada, forneça esta credenciais do Azure:

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: aguarde até a sessão da Área de Trabalho Remota e o **Gerenciador do Servidor** serem carregados.  

    >**Observação**: as etapas a seguir são executadas na sessão da Área de Trabalho Remota para a VM **az500-04-vm1** do Azure. 

6. No **Gerenciador do Servidor**, clique em **Servidor Local** e em **Configuração de Segurança Aprimorada do IE**.

7. Na caixa de diálogo **Configuração de Segurança Aprimorada do IE**, defina as duas opções como **Desativado** e clique em **OK.**

8. Inicie o **Internet Explorer**, clique no ícone de engrenagem na barra de ferramentas, no menu suspenso, clique em **Segurança** e, em seguida, em **Navegação InPrivate**.

9. Na janela InPrivate do Internet Explorer, navegue até o ToR Browser Project em **https://www.torproject.org/projects/torbrowser.html.en**.

10. Baixe e instale a versão do Windows do navegador ToR com as configurações padrão. 

11. Quando a instalação for concluída, inicie o navegador ToR, use a opção **Conectar** na página inicial e navegue até o Painel de Acesso ao Aplicativo em **https://myapps.microsoft.com**.

12. Quando solicitado, tente fazer logon com a conta **aaduser3**. 

    >**Observação**: será exibida a mensagem **Sua entrada foi bloqueada**. Isso é esperado, já que essa conta não está configurada com autenticação multifator, que é necessária devido ao maior risco de entrada associado ao uso do navegador ToR.

13. No navegador ToR, selecione a seta Voltar para entrar com a conta **aaduser1** que você criou e configurou para autenticação multifator anteriormente neste laboratório.

    >**Observação**: desta vez, a mensagem **Atividade suspeita detectada** será exibida. Novamente, isso é esperado, já que essa conta está configurada com autenticação multifator. Considerando o aumento do risco de entrada associado ao uso do Navegador ToR, você terá que usar a autenticação multifator.

14. Use a opção **Verificar** e especifique se deseja verificar sua identidade por texto ou chamada.

15. Conclua a verificação e certifique-se de que você entrou com êxito no Painel de Acesso ao Aplicativo.

16. Encerre sua sessão RDP. 

    >**Observação**: neste ponto, você tentou duas entradas diferentes. Em seguida, você analisará os relatórios do Azure Identity Protection.

#### Tarefa 5: analisar os relatórios do Microsoft Entra ID Identity Protection

Nesta tarefa, você analisará os relatórios do Microsoft Entra ID Identity Protection gerados a partir dos logons do navegador ToR.

1. De volta ao portal do Azure, use o filtro **Diretório + assinatura** para alternar para o locatário **AdatumLab500-04** do Microsoft Entra ID.

2. No painel **AdatumLab500-04**, na seção **Gerenciar**, clique em **Segurança**.

3. No painel **Segurança \| Introdução**, na seção **Relatório**, clique em **Usuários suspeitos**. 

4. Analise o relatório e identifique todas as entradas que fazem referência à conta de usuário **aaduser3**.

5. Na folha **Segurança \| Introdução**, na seção **Relatórios**, clique em **Entradas suspeitas**. 

6. Analise o relatório e identifique todas as entradas correspondentes ao logon com a conta de usuário **aaduser3**.

7. Em **Relatórios**, clique em **Detecções suspeitas**.

8. Analise o relatório e identifique todas as entradas que representam o logon do endereço IP anônimo gerado pelo navegador ToR. 

    >**Observação**: pode levar de 10 a 15 minutos para que os riscos apareçam nos relatórios.

> **Resultado**: você habilitou o Microsoft Entra ID Identity Protection, configurou a política de risco do usuário e a política de risco de entrada, bem como validou a configuração do Microsoft Entra ID Identity Protection, simulando eventos de risco.

**Limpar recursos**

> Remova os recursos de proteção de identidade que você não usa mais. 

Use as etapas a seguir para desabilitar as políticas de proteção de identidade no locatário **AdatumLab500-04** do Microsoft Entra ID.

1. No portal do Azure, navegue de volta para o painel de locatário do Microsoft Entra ID **AdatumLab500-04**.

2. No painel **AdatumLab500-04**, na seção **Gerenciar**, clique em **Segurança**.

3. No painel **Segurança \| Introdução**, na seção **Proteger**, clique em **Identity Protection**.

4. No painel **Identity Protection \| Visão Geral**, clique em **Política de risco do usuário**.

5. No painel **Identity Protection \| Política de risco do usuário**, defina **Imposição de Política** como **Desabilitado** e clique em **Salvar**.

6. No painel **Identity Protection \| Política de risco do usuário**, clique em **Política de risco de entrada**.

7. No painel **Identity Protection \| Política de risco de entrada**, defina **Imposição de Política** como **Desabilitado** e clique em **Salvar**.

Use as etapas a seguir para interromper a VM do Azure provisionada anteriormente no laboratório.

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra ID associado à assinatura do Azure na qual você implantou a VM do Azure **az500-04-vm1**.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

3. No portal **Máquinas virtuais**, clique na entrada **az500-04-vm1**. 
 
4. No painel **az500-04-vm1**, clique em **Parar** e, quando solicitado a confirmar, clique em **OK** 

>  Não remova nenhum recurso fornecido neste laboratório, pois o laboratório PIM precisa deles.
