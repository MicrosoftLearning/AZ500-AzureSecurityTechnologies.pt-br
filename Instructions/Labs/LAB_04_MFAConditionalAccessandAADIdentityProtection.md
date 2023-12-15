---
lab:
  title: 04 - MFA e Acesso Condicional
  module: Module 01 - Manage Identity and Access
---

# Configure o Acesso Condicional e a MFA.
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito de recursos que aprimoram a autenticação do Azure Active Directory (Azure AD). Especificamente, você deseja avaliar:

- Autenticação multifator do Azure AD
- Acesso condicional do Azure AD
- Políticas de Acesso Condicional Baseadas em Risco.

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do Laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício: implantar um Banco de Dados SQL do Azure usando um modelo do Azure Resource Manager
- Exercício 2: Implementar o Azure MFA
- Exercício 3: Implementar políticas de acesso condicional do Azure AD 
- Implementar o Azure AD Identity Protection

## Laboratório: MFA, Acesso Condicional e Proteção de Identidade do AAD

![imagem](https://user-images.githubusercontent.com/91347931/157518628-8b4a9efe-0086-4ec0-825e-3d062748fa63.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**
- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** 

### Exercício: implantar um Banco de Dados SQL do Azure usando um modelo do Azure Resource Manager

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Implantar VMs do Azure usando modelos do Azure Resource Manager

#### Implantar VMs do Azure usando modelos do Azure Resource Manager

Nesta tarefa, você criará uma máquina virtual usando um modelo ARM. Esta máquina virtual será usada no último exercício para este laboratório. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório e a função Administrador Global no locatário do Azure AD associado a essa assinatura.

2. No portal do Azure, use a caixa de texto **Pesquisar recursos, serviços e documentos** no início da página do portal do Azure para pesquisar **DevOps Starter**.

    >**Observação**: você também pode selecionar **Implantação de modelo (implantar usando modelos personalizados)** na lista do **Marketplace** .

3. Na **página Implantação personalizada**, clique em **Criar seu modelo no editor**.

4. **Na folha Editar modelo**, clique em Carregar arquivo, localize o\\** arquivo** Allfiles\\Labs\\04\\az-500-04_azuredeploy.json** e clique em ****Abrir**.

    >**Observação**: revise o conteúdo do modelo e observe que ele implanta uma VM do Azure que hospeda o Windows Server 2019 Datacenter.

5. Na folha Editar **modelo** , clique em **Salvar**.

6. De volta à **folha Implantação** personalizada, clique em **Editar parâmetros**.

7. **Na folha Editar parâmetros, clique em Carregar arquivo, localize o **\\arquivo** Allfiles\\Labs**\\ 04\\az-500-04_azuredeploy.parameters.json** e clique em **** Abrir**.

    >**Nota**: Revise o conteúdo do arquivo de parâmetros observando os valores adminUsername e adminPassword.

8. Na folha Editar **parâmetros**, clique em Salvar.****

9. **Na folha Implantação** personalizada, verifique se as seguintes configurações estão definidas (deixe outras com seus valores padrão):

   >**Nota**: Você precisará criar uma senha exclusiva que será usada para criar VMs (máquinas virtuais) para o restante do curso. A senha deve ter entre 8 e 123 caracteres e ter pelo menos 3 dos seguintes: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Requisitos de senha Anote a Senha.

   |Configuração|Valor|
   |---|---|
   |Subscription|O nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB04**|
   |Localização|**Leste dos EUA**|
   |Tamanho da VM|Standard_D2s_v3|
   |Nome da VM|az104-04-vm1|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Por favor, crie sua própria senha e registre-a para referência futura. Essa senha será solicitada para o acesso de laboratório necessário.**|
   |Nome da Rede Virtual|**az500-04-VNET1**|

   >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

10. Clique em **Revisar + criar** e em **Criar**.

    >**Observação:** prossiga para a próxima etapa sem aguardar a conclusão da implantação. Você usará a máquina virtual incluída nesta implantação no último exercício deste laboratório.

> Resultado: você iniciou uma implantação de modelo de uma VM **do Azure az500-04-vm1** que será usada no último exercício deste laboratório.


### Exercício 2: Implementar o Azure MFA

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Criar um novo locatário no Microsoft Azure AD.
- Tarefa 2: Ativar a avaliação do Azure AD Premium P2.
- Criar grupos e usuários do AAD
- Tarefa 4: Atribuir licenças do Azure AD Premium P2 aos usuários do Azure AD.
- Definir configurações do Azure MFA
- Tarefa 6: Validar a configuração do MFA

#### Criar um novo locatário no Microsoft Azure AD.

Nesta tarefa, você criará um novo locatário do Azure AD. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Azure Active Directory** e pressione a **tecla Enter**.

2. Na folha que exibe **Visão geral** do seu locatário atual do Azure AD, clique em Gerenciar locatários** e, na próxima tela, clique **em **+ Criar**.

3. Na guia **Básico** do painel **Criar um locatário**, verifique se a opção **Azure Active Directory** está selecionada, e selecione **Próximo: Configuração >**.

4. Na guia **Configuração** do painel **Criar um locatário**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da organização|**AdatumLab500-04**|
   |Nome de domínio inicial|um nome exclusivo que consiste em uma combinação de letras e dígitos|
   |País ou Região|**Estados Unidos**|

    >**Nota**: Registre o nome de domínio inicial. Você precisará disso em uma etapa posterior.

5. Clique em **Examinar + Criar** e em **Criar**.
6. Adicione o código Captcha em Ajude-nos a provar que você não é uma lâmina de robô** e, em **seguida, clique no **botão Enviar**. 

    >**Observação**: aguarde até que o novo locatário seja criado. Selecione o ícone **Notificações** para exibir o status da implantação. 


#### Tarefa 2: Ativar a avaliação do Azure AD Premium P2

Nesta tarefa, você se inscreverá na avaliação gratuita do Azure AD Premium P2. 

1. No portal do Azure, na barra de ferramentas, clique no **ícone Diretório + assinatura** , localizado à direita do ícone do Cloud Shell. 

2. **Na folha Diretório + assinatura**, clique no locatário **recém-criado AdatumLab500-04** e clique no **botão Alternar** para defini-lo como o diretório atual.

    >**Nota**: Talvez seja necessário atualizar a janela do navegador se a **entrada AdatumLab500-04** não aparecer na **lista Diretório + filtro de assinatura** .

3. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Azure Active Directory** e pressione a **tecla Enter**. **Na folha AdatumLab500-04**, na **seção Gerenciar**, clique em **Licenças**.

4. Na folha Visão Geral de **Licenças, na **seção Gerenciar\|**, clique em Todos os produtos** e em ****+ Experimentar/Comprar**.**

5. **Na folha Ativar, na seção Azure AD Premium P2, clique em Avaliação Gratuita** e em ****Ativar****.


#### Criar grupos e usuários do AAD

Nesta tarefa, você criará três usuários: aaduser1 (administrador global), aaduser2 (usuário) e aaduser3 (usuário). Você precisará do nome principal e da senha de cada usuário para tarefas posteriores. 

1. Navegue de volta para a **folha AdatumLab500-04** Azure Active Directory e, na **seção Gerenciar** , clique em **Usuários**.

2. Na folha Usuários \| Todos os **usuários**, clique em **+ Novo Usuário** e em **Criar novo usuário**. 

3. No painel Novo usuário, verifique se a opção Criar usuário está selecionada e especifique as seguintes configurações, deixando as demais com seus valores padrão:

   |Configuração|Valor|
   |---|---|
   |Nome UPN|**aaduser1**|
   |Nome|**aaduser1**|
   |Senha|verifique se a opção **Gerar senha** automaticamente está selecionada|
   
      >**Nota**: Registre o nome principal completo do usuário. Você pode copiar seu valor clicando no botão Copiar para a **área** de transferência no lado direito da lista suspensa que exibe o nome do domínio. Você precisará delas mais tarde neste tutorial.
   
      >**Nota**: Registre a senha do usuário. Você pode copiar seu valor clicando no botão Copiar para a **área** de transferência no lado direito da caixa de texto. Você precisará delas mais tarde neste tutorial. 
   
4. **Na guia Propriedades**, role até a parte inferior e especifique o Local de uso: Estados Unidos** (deixe todos os outros com seus valores padrão) e clique em **Avançar: **Atribuições >**.

5. **Na guia Atribuições**, clique em **+ Adicionar função** e procure e selecione **Administrador Global**. Selecione em Revisar e criar e, em seguida, clique em Criar.

6. De volta à folha Usuários Todos os **usuários**, clique em **+ Novo Usuário**.\| 

7. No painel Novo usuário, verifique se a opção Criar usuário está selecionada e especifique as seguintes configurações, deixando as demais com seus valores padrão:

   |Configuração|Valor|
   |---|---|
   |Nome UPN|**aaduser2**|
   |Nome|**aaduser2**|
   |Senha|verifique se a opção **Gerar senha** automaticamente está selecionada| 

    >**Nota**: Registre o nome principal completo do usuário e a senha.

8. **Na guia Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com seus valores padrão) e clique em Revisar + criar** e, em **seguida, **Criar**.

9. De volta à folha Usuários Todos os **usuários**, clique em **+ Novo Usuário**.\| 

10. No painel Novo usuário, verifique se a opção Criar usuário está selecionada e especifique as seguintes configurações, deixando as demais com seus valores padrão:

    |Configuração|Valor|
    |---|---|
    |Nome UPN|**aaduser3**|
    |Nome|**aaduser3**|
    |Senha|verifique se a opção **Gerar senha** automaticamente está selecionada|

    >**Nota**: Registre o nome principal completo do usuário e a senha.

11. **Na guia Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com seus valores padrão) e clique em Revisar + criar** e, em **seguida, **Criar**.

    >**Nota**: Neste ponto, você deve ter três novos usuários listados na **página Usuários** . 
    
#### Tarefa 4: Atribuir licenças do Azure AD Premium P2 a usuários do Azure AD

Nesta tarefa, você atribuirá cada usuário à licença do Azure Active Directory Premium P2.

1. Na folha Usuários \| Todos os **usuários**, clique na entrada que representa sua conta de usuário. 

2. Na folha que exibe as propriedades de suas contas de usuário, clique em **Editar propriedades**.  Confirme se o Local de Uso está definido como **Estados Unidos**. Caso contrário, defina o local de uso e clique em **Salvar**.

3. Navegue de volta para a **folha AdatumLab500-04** Azure Active Directory e, na **seção Gerenciar** , clique em **Licenças**.

4. Na folha Visão Geral de Licenças\|, clique em Todos os produtos **, marque a caixa de ****seleção Azure Active Directory Premium P2** e clique em ****+ Atribuir**.**

5. Na folha **Adicionar Atribuição**, clique em **Usuários e grupos**.

6. **Na folha Usuários**, selecione **aaduser1**, aaduser2 **, ****aaduser3** e sua conta de usuário e clique em **Selecionar**.

7. De volta à **folha Atribuir licenças****, clique em Opções de atribuição, verifique se todas as opções estão habilitadas, clique em Revisar + atribuir** e clique em ******Atribuir.**

8. Saia do portal do Azure e entre novamente usando a mesma conta. (Esta etapa é necessária para que a atribuição da licença entre em vigor.)

    >**Observação**: neste ponto, você atribuiu licenças do Azure Active Directory Premium P2 a todas as contas de usuário que serão usadas neste laboratório. Certifique-se de sair e, em seguida, entrar novamente. 

#### Definir configurações do Azure MFA

Nesta tarefa, você configurará a MFA e habilitará a MFA para aaduser1. 

1. No portal do Azure, navegue de volta para a folha de locatário do Active Directory do **Azure AdatumLab500-04** .

    >**Observação**: verifique se você está usando o locatário do AdatumLab500-04 Azure AD.

2. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Segurança**.

3. Na folha Introdução à Segurança, na **seção Gerenciar****, clique em **Autenticação multifator**.\| **

4. Na folha Introdução** à **autenticação multifator, clique no **link Configurações adicionais de autenticação \|** multifator baseada em nuvem. 

    >**Nota**: Isso abrirá uma nova guia do navegador, exibindo **a página de autenticação** multifator.

5. Na página Autenticação** multifator, clique na guia Configurações** de serviço. Revise **as **** opções** de verificação. Observe que **a mensagem de texto para o telefone, **a notificação por meio de aplicativo móvel e **o** código de verificação do aplicativo** móvel ou token** de hardware estão habilitados. Clique em **Salvar**e em **Fechar**.

6. Alterne para a **guia usuários**, clique em entrada aaduser1 **, clique no **link Habilitar** e, quando solicitado, clique em habilitar autenticação** multifator e, em seguida, clique em ****** fechar**.

7. Observe que a coluna de status de **autenticação multifator para **aaduser1** agora **está habilitada**.**

8. Clique em **aaduser1** e observe que, neste ponto, você também tem a **opção Enforce** . 

    >**Observação**: alterar o status do usuário de Habilitado para Imposto afeta apenas os aplicativos integrados herdados do Azure AD que não oferecem suporte ao Azure MFA e, quando o status for alterado para Imposto, exigirá o uso de senhas de aplicativo.

9. Com a **entrada aaduser1** selecionada, clique em **Gerenciar configurações** do usuário e revise as opções disponíveis: 

   - Exigir que os usuários selecionados forneçam métodos de contato novamente

   - Excluir todas as senhas de aplicativos existentes geradas pelos usuários selecionados

   - Restaurar a autenticação multifator em todos os dispositivos lembrados

10. Clique em **cancelar** e volte para a guia do navegador que exibe a **folha Introdução** à autenticação \| multifator no portal do Azure.

11. **Na seção Configurações**, clique em **Alerta de** fraude.

12. Na folha de alerta** de fraude de **autenticação \| multifator, defina as seguintes configurações:

    |Configuração|Valor|
    |---|---|
    |Defina Permitir que os usuários enviem alertas de fraude como Ativado.|**Em**|
    |Bloquear automaticamente os usuários que relatarem fraudes.|**Em**|
    |Código para relatar fraudes durante a saudação inicial.|**0**|

13. Clique em **Salvar**

    >**Nota**: Neste ponto, você ativou o MFA para aaduser1 e configurou as configurações de alerta de fraude. 

14. Navegue de volta para a folha de locatário do **Active Directory do Azure AdatumLab500-04**, na seção Gerenciar **, clique em Propriedades **, em seguida, clique no **link Gerenciar padrões de segurança na parte inferior da folha, na **** folha Habilitar padrões**** de segurança, clique **em **Desabilitado**. Selecione **Minha Organização está usando o Acesso** Condicional como Motivo da desabilitação *, clique em Salvar **, leia o *aviso e clique em ****Desabilitar**.

    >**Observação**: verifique se você está conectado ao locatário do **AdatumLab500-04** Azure AD. Você pode usar o **filtro Diretório + assinatura** para alternar entre locatários do Azure AD. Verifique se você está conectado como um usuário com a função de Administrador Global no locatário do Azure AD.

#### Tarefa 6: Validar a configuração do MFA

Nesta tarefa, você validará a configuração de MFA testando o login da conta de usuário aaduser1. 

1. Abra uma janela do navegador InPrivate.

2. Navegue até o portal **`https://portal.azure.com/`** do Azure e entre usando a **conta de usuário aaduser1.** 

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da **conta de usuário aaduser1** , incluindo o nome de domínio DNS do locatário do Azure AD, que você registrou anteriormente neste laboratório. Esse nome de usuário está no formato aaduser1@.onmicrosoft.com, em que é o espaço reservado que `<your_tenant_name>` representa seu nome de`<your_tenant_name>` locatário exclusivo do Azure AD. 

3. Na caixa de diálogo **Mais informações necessárias**, selecione **Avançar**.

    >**Nota**: A sessão do navegador será redirecionada para a **página Verificação** de segurança adicional.

4. **Na página Manter sua conta segura**, selecione o **link Desejo configurar um método diferente, na **lista suspensa Qual método** você gostaria de usar?**, selecione Telefone** e selecione ****Confirmar**.

5. Na página Manter **sua conta segura** , selecione seu país ou região, digite seu número de **celular na área Inserir número** de telefone, verifique se a **opção Enviar um código** para mim está selecionada e clique em **Avançar**.
 
6. Na página Manter **sua conta segura** , digite o código recebido na mensagem de texto em seu celular e clique em **Avançar**.

7. **Na página Mantenha sua conta segura**, verifique se a verificação foi bem-sucedida e clique em **Avançar**.

8. Se for solicitado um método de autenticação adicional, clique em Desejo usar um método diferente, selecione **Email** na lista suspensa, clique em Confirmar, forneça o endereço de email que você pretende usar e clique em ******Avançar****.** Depois de receber o e-mail correspondente, identifique o código no corpo do e-mail, forneça-o e clique em **Concluído**.

9. Quando solicitado, insira a senha. Lembre-se de registrá-la.

10. Verifique se você entrou com êxito no portal do Azure.

11. Saia como **aaduser1** e feche a janela do navegador InPrivate.

> Resultado: você criou um novo locatário do AD, configurou usuários do AD, configurou a MFA e testou a experiência de MFA para um usuário. 


### Exercício 3: Implementar políticas de acesso condicional do Azure AD 

### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Configure a política de Acesso Condicional.
- Testar a política de acesso condicional

#### Configure a política de Acesso Condicional. 

Nesta tarefa, revisaremos as configurações da política de acesso condicional e criaremos uma política que exige a MFA ao entrar no Portal. 

1. No portal do Azure, navegue de volta para a folha de locatário do Active Directory do **Azure AdatumLab500-04** .

2. **Na folha AdatumLab500-04**, na **seção Gerenciar**, clique em **Segurança**.

3. Na página **Segurança\|, na seção **Proteger **, clique em **Acesso Condicional. No painel de navegação esquerdo, clique em **Procurar**.

4. Na folha Políticas** de **Acesso \| Condicional, clique em **+ Nova política**. 

5. Na guia **Discos**, configure os seguintes valores:

   - Na caixa de **texto Nome** , digite **AZ500Policy1**
    
   - Em **Usuários**, selecione **0 usuários e grupos selecionados**. No lado direito, em Incluir, Habilitar Selecionar usuários e grupos >> marcar a caixa de seleção Usuários e Grupos, na folha Selecionar **** usuários e grupos****, marque a **caixa de **seleção aaduser2** e clique em **Selecionar**.**
    
   - Em **Recursos de** destino, clique em Nenhum recurso de destino selecionado,** clique em Selecionar aplicativos **, em Selecionar, clique em ****** Nenhum.** Na página **Selecionar**, selecione **Microsoft Azure** e clique em **Selecionar**. 

     >Revise o aviso de que essa política afeta o acesso ao Portal.
    
   - Em **Condições**, clique em 0 condições selecionadas **, em **Risco** de entrada, clique **em **Não configurado**. **Na folha Risco de entrada, revise os níveis de risco, mas não faça alterações e feche a **folha Risco**** de entrada.
    
   - Em **Plataformas de dispositivo, clique em Não configurado **, examine as plataformas de dispositivo que podem ser incluídas, mas não faça alterações, e clique em ****Concluído**.**
    
   - Em **Locais**, clique em **Não configurado** e revise as opções de local sem fazer alterações.
    
   - Em **Controles de acesso**e **Conceder**, selecione **0 controles selecionados**. **Na folha Conceder**, marque a **caixa de seleção Exigir autenticação** multifator e clique em **Selecionar**
    
   - Defina **Habilitar política** como **Ativo**.

6. Na folha **Novo**, clique em **Criar**. 

    >**Observação**: neste ponto, você tem uma política de acesso condicional que requer MFA para entrar no portal do Azure. 

#### Testar a política de acesso condicional

Nesta tarefa, você entrará no portal do Azure como **aaduser2** e verificará se a MFA é necessária. Você também excluirá a política antes de continuar para o próximo exercício. 

1. Abra uma janela InPrivate do Microsoft Edge.

2. Em uma nova janela do navegador, vá para o **`https://portal.azure.com/`** portal do Azure e entre com sua conta do Azure.

3. Na caixa de diálogo **Mais informações necessárias**, selecione **Avançar**.

    >**Observação**: a sessão do navegador será redirecionada para a **página Mantenha sua conta segura** .
    
4. **Na página Manter sua conta segura**, selecione o **link Desejo configurar um método diferente, na **lista suspensa Qual método** você gostaria de usar?**, selecione Telefone** e selecione ****Confirmar**.

5. Na página Manter **sua conta segura** , selecione seu país ou região, digite seu número de **celular na área Inserir número** de telefone, verifique se a **opção Enviar um código** para mim está selecionada e clique em **Avançar**.

6. Na página Manter **sua conta segura** , digite o código recebido na mensagem de texto em seu celular e clique em **Avançar**.

7. **Na página Mantenha sua conta segura**, verifique se a verificação foi bem-sucedida e clique em **Avançar**.

8. Na página **Manter sua conta segura**, selecione **Concluído**.

9. Quando solicitado, insira a senha. Lembre-se de registrá-la.

10. Verifique se você entrou com êxito no portal do Azure.

11. Saia como **aaduser2** e feche a janela do navegador InPrivate.

    >**Observação**: agora você verificou se a política de acesso condicional recém-criada impõe MFA quando aaduser2 entra no portal do Azure.

12. De volta à janela do navegador que exibe o portal do Azure, navegue de volta para a folha de locatário do Active Directory do **Azure AdatumLab500-04** .

13. **Na folha AdatumLab500-04**, na **seção Gerenciar**, clique em **Segurança**.

14. Na página **Segurança\|, na seção **Proteger **, clique em **Acesso Condicional. No painel de navegação esquerdo, clique em **Procurar**.

15. Na folha Políticas de Acesso Condicional, clique nas reticências ao lado de **\| **AZ500Policy1**, clique em Excluir** e, quando solicitado a confirmar, clique em ****Sim**.**

    >**Observação**: Resultado: neste exercício, você implementa uma política de acesso condicional para exigir MFA quando um usuário entra no portal do Azure. 

>Resultado: você configurou e testou o acesso condicional do Azure AD.

### Exercício 4: Implantar políticas baseadas em risco no Acesso Condicional

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Exibir opções do Azure AD Identity Protection no portal do Azure
- Configurar política de risco do usuário >
- Configurar a política de risco de entrada
- Tarefa 4: Simular eventos de risco em relação às políticas de Proteção de Identidade do Azure AD 
- Você examina os relatórios do Azure Active Directory Identity Protection.

#### Habilitar o Azure AD Identity Protection

Nesta tarefa, você exibirá as opções de Proteção de Identidade do Azure AD no portal do Azure. 

1. Se necessário, entre no Portal do Azure.

    >**Observação**: verifique se você está conectado ao locatário do **AdatumLab500-04** Azure AD. Você pode usar o **filtro Diretório + assinatura** para alternar entre locatários do Azure AD. Verifique se você está conectado como um usuário com a função de Administrador Global no locatário do Azure AD.

#### Configurar política de risco do usuário >

Nesta tarefa, você criará uma nova função de usuário. 

1. Navegue até **AdatumLab500-04 locatário do Azure AD > **Políticas de Acesso**** > Condicional de Segurança.**** > ****

2. Clique em **Nova política**.

3. Digite o seguinte nome de política na caixa de **texto Nome** , digite **AZ500Policy2**.

4. Em **Atribuições** > **Usuários**, selecione **0 usuários e grupos selecionados**.

5. Em **Incluir, clique em Selecionar usuários e grupos, clique em Usuários e grupos****, selecione **aaduser2** e **aaduser3** e clique em ******Selecionar.****

6. Em **Excluir**, clique em Usuários e grupos **, selecione **aaduser1** e clique em **** Selecionar**. 

7. Em **Recursos de destino, clique em Nenhum recurso de destino selecionado,** confirme se **os aplicativos** de nuvem estão selecionados na lista suspensa e, em **** Incluir**, selecione **Todos os aplicativos** de** nuvem.

8. Em **Condições**, clique em 0 condições selecionadas **, em Risco do** usuário, clique em ******Não configurado**. Na folha Risco do **usuário, defina **Configurar** como **Sim**.**

9. Em **Configurar os níveis de risco do usuário necessários para que a política seja imposta**, selecione **Alta**.

10. Clique em **Concluído**.

11. Em **Controles de acesso**e **Conceder**, selecione **0 controles selecionados**. Na folha Conceder **, confirme se **a **opção Conceder acesso** está selecionada.

12. Selecione Conceder acesso, Exigir autenticação multifator e Exigir alteração de senha.

13. Clique em **Selecionar**.

14. Em **Sessão**, clique em **0 controles selecionados**. Selecione **Frequência** de início de sessão e, em seguida, selecione **Sempre**.

15. Clique em **Selecionar**.

16. Confirme suas configurações e defina **Habilitar política** com **Somente relatório**.

17. Clique em **Ativar** para habilitar a sua política.

#### Configurar a política de risco de entrada

1. Navegue até **AdatumLab500-04 locatário do Azure AD > **Políticas de Acesso****> Condicional de Segurança.**** > ****

2. Selecione **+ Nova política**.

3. Digite o seguinte nome de política na caixa de **texto Nome** , digite **AZ500Policy3**.

4. Em **Atribuições** > **Usuários**, selecione **0 usuários e grupos selecionados**.

5. Em **Incluir, clique em Selecionar usuários e grupos, clique em Usuários e grupos****, selecione **aaduser2** e aaduser3** e **clique em ****** Selecionar**.**

6. Em **Excluir**, clique em Usuários e grupos **, selecione **aaduser1** e clique em ****Selecionar**. 

7. Em **Recursos de destino, clique em Nenhum recurso de destino selecionado,** confirme se **os aplicativos** de nuvem estão selecionados na lista suspensa e, em **** Incluir**, selecione **Todos os aplicativos** de** nuvem.

8. Em **Condições**, clique em 0 condições selecionadas **, em **Risco** de entrada, clique **em **Não configurado**. **Na folha Risco** de entrada, defina **Configurar** como **Sim**.

9. Abaixo de Selecionar o nível de risco de entrada ao qual essa política será aplicada.

10. Clique em **Concluído**.

11. Em **Controles de acesso**e **Conceder**, selecione **0 controles selecionados**. Na folha Conceder **, confirme se **a **opção Conceder acesso** está selecionada.   

12. Selecione exigir autenticação multifator

13. Clique em **Selecionar**.

14. Em **Sessão**, clique em **0 controles selecionados**. Selecione **Frequência** de início de sessão e, em seguida, selecione **Sempre**.

15. Clique em **Selecionar**.

16. Confirme suas configurações e defina **Habilitar política** como **Ativado**.

17. Clique em **Ativar** para habilitar a sua política.

#### Tarefa 4: Simular eventos de risco em relação às políticas de Proteção de Identidade do Azure AD 

> Antes de iniciar essa tarefa, verifique se a implantação do modelo iniciada no Exercício 1 foi concluída. A implantação inclui uma VM do Azure chamada **az500-04-vm1**. 

1. No portal do Azure, defina o filtro Diretório + assinatura para o **locatário do Azure AD associado à assinatura** do Azure na qual você implantou a **VM do Azure az500-04-vm1** .

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos**, insira **máquinas virtuais** e pressione **Enter**.

3. **Na folha Máquinas** virtuais, clique na **entrada az500-04-vm1**. 

4. **Na folha az500-04-vm1**, clique em **Conectar**. Confirme se você está na **guia RDP** .

5. Clique em **Baixar Arquivo** RDP e use-o para se conectar à **VM do Azure az500-04-vm1** via Área de Trabalho Remota. Quando a autenticação for solicitada, forneça suas credenciais do Azure.

    |Configuração|Valor|
    |---|---|
    |Nome de usuário|**Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Nota**: Aguarde até que a sessão de Área de Trabalho Remota e **o Gerenciador do Servidor** sejam carregados.  

    >**Observação**: as etapas a seguir são executadas na sessão da Área de Trabalho Remota para a **VM do Azure az500-04-vm1** . 

6. No **Gerenciador do Servidor, clique em Servidor** Local e em **Configuração de Segurança Reforçada** do**** IE.

7. Na caixa de diálogo Configuração** de Segurança Reforçada do **Internet Explorer, defina as duas opções como **Desativado** e clique em **OK.**

8. Inicie **o Internet Explorer**, clique no ícone de roda dentada na barra de ferramentas, no menu suspenso, clique em Segurança e, em seguida, clique **em **Navegação**** InPrivate.

9. Na janela InPrivate Internet Explorer, navegue até o Projeto de navegador ToR em **https://www.torproject.org/projects/torbrowser.html.en**.

10. Baixe e instale a versão do Windows do navegador ToR com as configurações padrão. 

11. Quando a instalação for concluída, inicie o navegador ToR, use a **opção Conectar** na página inicial e navegue até o Painel de Acesso ao Aplicativo em **https://myapps.microsoft.com**.

12. Quando solicitado, tente entrar com a **conta aaduser3** . 

    >**Nota**: Ser-lhe-á apresentada a mensagem **O seu início de sessão foi bloqueado**. Isso é esperado, já que essa conta não está configurada com autenticação multifator, que é necessária devido ao maior risco de entrada associado ao uso do navegador ToR.

13. No navegador ToR, selecione a seta para trás para entrar como **conta aaduser1** que você criou e configurou para autenticação multifator anteriormente neste laboratório.

    >**Nota**: Desta vez, ser-lhe-á apresentada a **mensagem Atividade suspeita detectada** . Novamente, isso é esperado, já que essa conta é configurada com autenticação multifator. Considerando o aumento do risco de entrada associado ao uso do Navegador ToR, você terá que usar a autenticação multifator.

14. Use a **opção Verificar** e especifique se deseja verificar sua identidade por texto ou chamada.

15. Conclua a verificação e certifique-se de que você entrou com êxito no Painel de Acesso ao Aplicativo.

16. Feche sua sessão RDP. 

    >**Nota**: Neste ponto, você tentou duas entradas diferentes. Em seguida, você analisará os relatórios do Azure Identity Protection.

#### Você examina os relatórios do Azure Active Directory Identity Protection.

Nesta tarefa, você analisará os relatórios do Azure AD Identity Protection gerados a partir dos logons do navegador ToR.

1. De volta ao portal do Azure, use o filtro Diretório + assinatura** para alternar para o **locatário do **Active Directory do Azure AdatumLab500-04**.

2. **Na folha AdatumLab500-04**, na **seção Gerenciar**, clique em **Segurança**.

3. Na folha Introdução à **Segurança**\|, na **seção Relatório**, clique em **Usuários arriscados**. 

4. Revise o relatório e identifique todas as entradas que fazem referência à conta de **usuário aaduser3** .

5. Na folha Introdução à **Segurança**\|, na **seção Relatórios**, clique em **Entradas** arriscadas. 

6. Revise o relatório e identifique todas as entradas correspondentes ao login com a **conta de usuário aaduser3** .

7. Em **Relatórios, clique em **Detecções** de risco**.

8. Revise o relatório e identifique todas as entradas que representam a entrada do endereço IP anônimo gerado pelo navegador ToR. 

    >**Nota**: Pode levar de 10 a 15 minutos para que os riscos apareçam nos relatórios.

> **Resultado**: você habilitou o Azure AD Identity Protection, configurou a política de risco de usuário e a política de risco de entrada, bem como validou a configuração do Azure AD Identity Protection simulando eventos de risco.

**Limpar recursos**

> Precisamos remover os recursos de proteção de identidade que você não usa mais. 

Use as etapas a seguir para desabilitar as políticas de proteção de identidade no locatário do **Azure AD do AdatumLab500-04** .

1. No portal do Azure, navegue de volta para a folha de locatário do Active Directory do **Azure AdatumLab500-04** .

2. **Na folha AdatumLab500-04**, na **seção Gerenciar**, clique em **Segurança**.

3. Na folha Introdução à Segurança, na **seção Proteger**, clique em **Proteção de Identidade**.\| ****

4. Na página **Azure AD Identity Protection\|, clique em **Política de risco do usuário.

5. Na folha Política de risco do Usuário do Identity Protection\|, defina **Imposição** de **Política como **Desabilitar e clique em **Salvar****.**

6. Na folha Política de risco de usuário do **Identity Protection\|, clique em **Política de risco**** de entrada.

7. Na folha Política **de risco de entrada da Proteção** de \| Identidade, defina **Imposição** de Política como **Desabilitar e clique em **Salvar****.

Use as etapas a seguir para interromper a VM do Azure provisionada anteriormente no laboratório.

1. No portal do Azure, defina o filtro Diretório + assinatura para o **locatário do Azure AD associado à assinatura** do Azure na qual você implantou a **VM do Azure az500-04-vm1** .

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos**, insira **máquinas virtuais** e pressione **Enter**.

3. **Na folha Máquinas virtuais**, clique na **entrada az500-04-vm1**. 
 
4. Na folha az500-04-vm1 **, clique em Parar** e, quando solicitado a confirmar, clique em **** OK **** 

>  Não remova nenhum recurso provisionado neste laboratório, pois o laboratório PIM tem uma dependência deles.
