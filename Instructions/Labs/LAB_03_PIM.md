---
lab:
  title: 03 – Microsoft Entra Privileged Identity Management
  module: Module 01 - Manage Identity and Access
---

# Laboratório 03: Microsoft Entra Privileged Identity Management
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito que usa o Microsoft Entra PIM (Privileged Identity Management) para habilitar a administração just-in-time e controlar o número de usuários que podem executar operações privilegiadas. Os requisitos específicos são:

- Crie uma atribuição permanente do usuário do Microsoft Entra ID aaduser2 para a função Administrador de Segurança. 
- Configure o usuário do Microsoft Entra ID aaduser2 para ser qualificado para as funções Administrador de cobrança e Leitor global.
- Configure a ativação da função Leitor global para exigir a aprovação do usuário do Microsoft Entra ID aaduser3
- Configure uma revisão de acesso da função Leitor global e revise os recursos de auditoria.

> Para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região para ser usada na aula. 

> Antes de continuar, verifique se você concluiu o Laboratório 02: MFA, Acesso condicional e Proteção de identidade do Microsoft Entra. Você precisará do locatário AdatumLab500-04 do Microsoft Entra, e das contas de usuário aaduser1, aaduser2 e aaduser3.

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: configurar usuários e funções do PIM.
- Exercício 2: ativar funções do PIM com e sem aprovação.
- Exercício 3: criar revisão de acesso e revisar recursos de auditoria do PIM.

## Diagrama do Microsoft Entra Privileged Identity Management

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/aef34a22-8ebd-4015-a04a-7ac3c357b862)

## Instruções

### Exercício 1 – configurar usuários e funções do PIM

#### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: qualificar um usuário para uma função.
- Tarefa 2: configurar uma função para exigir aprovação para ativar e adicionar um membro qualificado.
- Tarefa 3: conceder a um usuário uma atribuição permanente a uma função. 

#### Tarefa 1: qualificar um usuário para uma função

Nesta tarefa, você tornará um usuário qualificado para uma função no Microsoft Entra ID.

1. Faça logon no portal do Azure em **`https://portal.azure.com/`**.

    >**Observação**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra. Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra. Verifique se você está conectado como um usuário com a função Administrador global.
    
    >**Observação**: se você ainda não vir a entrada AdatumLab500-04, clique no link Alternar Diretório, selecione a linha AdatumLab500-04 e, em seguida, o botão Alternar.

2. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra Privileged Identity Management** e pressione a tecla **Enter**.

3. Na folha **Privileged Identity Management**, na seção **Gerenciar**, selecione **Funções do Microsoft Entra ID**.

4. Na folha **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, selecione **Funções**.

5. Na folha **AdatumLab500-04 \| Funções**, selecione **+ Adicionar atribuições**.

6. Na folha **Adicionar atribuições**, na lista suspensa **Selecionar função**, selecione **Administrador de cobrança**.

7. Clique no link **Nenhum membro selecionado**, na folha **Selecionar membro**, escolha **aaduser2** e, em seguida, clique em **Selecionar**.

8. De volta à folha **Adicionar atribuições**, selecione **Avançar**. 

9. Verifique se o **Tipo de atribuição** está definido como **Qualificado** e selecione **Atribuir**.
 
10. De volta à folha **AdatumLab500-04 \| Funções** na seção **Gerenciar**, Selecione **Atribuições**.

11. De volta à folha **AdatumLab500-04 \| Atribuições**, observe as guias para **Atribuições qualificadas**, **Atribuições ativas** e **Atribuições expiradas**.

12. Verifique na guia **Atribuições qualificadas** que o **aaduser2** é mostrado como um **Administrador de cobrança**. 

    >**Observação**: durante o logon, o aaduser2 estará qualificado para usar a função de Administrador de cobrança. 

#### Tarefa 2: configurar uma função para exigir aprovação para ativar e adicionar um membro qualificado

1. No Portal do Azure, navegue de volta para a folha **Privileged Identity Management** e clique em **Funções do Microsoft Entra ID**.

2. Na folha **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, selecione **Funções**.

3. Na folha **AdatumLab500-04 \| Funções**, clique na entrada da função **Leitor global**. 

4. Na folha **Leitor Global \| Atribuições**, clique no ícone **Configurações de função** na barra de ferramentas da folha e revise as definições de configuração da função, incluindo os requisitos de autenticação multifator do Azure.

5. Clique em **Editar**.

6. Na guia **Ativação**, habilite a caixa de seleção **Exigir aprovação para ativar**.

7. Clique em **Selecionar aprovador(es)** na folha **Selecionar um membro**, clique em **aaduser3** e, em seguida, clique em **Selecionar**.

8. Clique em **Avançar:Atribuição**.

9. Desmarque a caixa de seleção **Permitir atribuição qualificada permanente**, deixando todas as outras configurações com seus valores padrão.

10. Clique em **Avançar:Notificação**.

11. Revise as configurações de **Notificação**, deixe tudo definido por padrão e clique em **Atualizar**.

    >**Observação**: qualquer pessoa que tentar usar a função Leitor Global agora precisará da aprovação do aaduser3. 

12. Na folha **Leitor Global \| Atribuições**, clique em **+ Adicionar atribuições**.

13. Na folha **Adicionar atribuições**, clique em **Nenhum membro selecionado**, na folha **Selecionar um membro**, clique em **aaduser2** e, em seguida, clique em **Selecionar**.

14. Clique em **Avançar**. 

15. Verifique se o **Tipo de atribuição** é **Qualificado** e revise as configurações de duração qualificada.

16. Clique em **Atribuir**.

    >**Observação**: o usuário aaduser2 é qualificado para a função de Leitor Global. 
 
#### Tarefa 3: conceder a um usuário uma atribuição permanente a uma função.

1. No Portal do Azure, navegue de volta para a folha **Privileged Identity Management** e clique em **Funções do Microsoft Entra ID**.

2. Na folha **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, selecione **Funções**.

3. Na folha **AdatumLab500-04 \| Funções**, selecione **+ Adicionar atribuições**.

4. Na folha **Adicionar atribuições**, na lista suspensa **Selecionar função**, selecione **Administrador de segurança**.

5. Na folha **Adicionar atribuições**, clique na opção **Nenhum membro selecionado**; na folha **Selecionar um membro**, clique em **aaduser2** e, em seguida, clique em **Selecionar**.

6. Clique em **Avançar**. 

7. Revise as configurações de **Tipo de atribuição** e clique em **Atribuir**.

8. No painel de navegação esquerdo, clique em **Atribuições**. Na guia **Atribuições Qualificadas**, em **Administrador de segurança**, selecione **Atualizar** para a atribuição **aaduser2**. Selecione **Permanentemente Qualificado** e **Salvar**.

    >**Observação**: o usuário aaduser2 agora está permanentemente qualificado para a função de Administrador de segurança.
    
### Exercício 2 - Ativar funções PIM com e sem aprovação

#### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: ativar uma função que não requer aprovação. 
- Tarefa 2: ativar uma função que requer aprovação. 

#### Tarefa 1: ativar uma função que não requer aprovação.

Nesta tarefa, você ativará uma função que não requer aprovação.

1. Abra uma janela do navegador InPrivate.

2. Na janela InPrivate do navegador, navegue até o portal do Azure em **`https://portal.azure.com/`** e entre usando a conta de usuário **aaduser2**.

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da conta de usuário **aaduser2**, incluindo o nome de domínio DNS do locatário do Microsoft Entra, que você registrou anteriormente neste laboratório. Esse nome de usuário está no formato aaduser2@`<your_tenant_name>`.onmicrosoft.com, onde `<your_tenant_name>` é o espaço reservado que representa seu nome de locatário exclusivo do Microsoft Entra. 

3. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra Privileged Identity Management** e pressione a tecla **Enter**.

4. Na folha **Privileged Identity Management**, na seção **Tarefas**, clique em **Minhas funções**.

5. Você deve ver três **Funções qualificadas** para **aaduser2**: **Leitor Global**, **Administrador de segurança** e **Administrador de cobrança**. 

6. Na linha que exibe a entrada da função **Administrador de cobrança**, clique em **Ativar**.

7. Se necessário, clique no aviso **Verificação adicional necessária. Clique para continuar** e siga as instruções para verificar sua identidade.

8. Na folha **Ativar - Administrador de cobrança**, na caixa de texto **Motivo**, digite um texto que forneça uma justificativa para a ativação e clique em **Ativar**.

    >**Observação**: quando você ativa uma função no PIM, pode levar até 10 minutos para que a ativação entre em vigor. 
    
    >**Observação**: quando sua atribuição de função estiver ativa, seu navegador será atualizado (se algo der errado, basta sair e entrar novamente no portal do Azure usando a conta de usuário **aaduser2**).

9. Navegue de volta para a folha **Privileged Identity Management** e, na seção **Tarefas**, selecione **Minhas funções**.

10. Na folha **Minhas funções \| Funções do Microsoft Entra ID**, alterne para a guia **Atribuições ativas**. Observe que a função **Administrador de cobrança** está **Ativada**.

    >**Observação**: após uma função ter sido ativada, ela será desativada automaticamente quando seu limite de tempo em **Hora de término** (duração qualificada) for atingido.

    >**Observação**: se você concluir as tarefas de administrador com antecedência, poderá desativar uma função manualmente.

11.  Na lista de **Atribuições Ativas**, na linha que representa a função Administrador de Cobrança, selecione o link **Desativar**.

12.  Na folha **Desativar – Administrador de Cobrança**, selecione **Desativar** novamente para confirmar.


#### Tarefa 2: ativar uma função que requer aprovação. 

Nesta tarefa, você ativará uma função que requer aprovação.

1. Na janela do navegador InPrivate, no portal do Azure, enquanto estiver conectado como o usuário **aaduser2**, navegue de volta para a folha **Privileged Identity Management \| Início Rápido**. 

2. Na folha **Privileged Identity Management \| Início Rápido**, na seção **Tarefas**, selecione **Minhas funções**.

3. Na folha **Minhas funções \| funções do Microsoft Entra ID**, na lista de **Atribuições qualificadas**, na linha que exibe a função **Leitor Global**, selecione **Ativar**. 

4. Na folha **Ativar – Leitor Global**, na caixa de texto **Motivo**, digite um texto que forneça uma justificativa para a ativação e selecione **Ativar**.

5. Clique no ícone **Notificações** na barra de ferramentas do portal do Azure e veja a notificação informando que sua solicitação está pendente de aprovação.

    >**Observação**: como administrador de funções com privilégios, você pode revisar e cancelar solicitações a qualquer momento. 

6. Na folha **Minhas funções \| Funções do Microsoft Entra ID**, localize a função **Administrador de Segurança** e clique em **Ativar**. 

7. Se necessário, clique no aviso **Verificação adicional necessária. Clique para continuar** e siga as instruções para verificar sua identidade.

    >**Observação**: você só precisa se autenticar uma vez por sessão. 

8. Quando você voltar à interface do Portal do Azure, na folha **Ativar – Administrador de Segurança**, na caixa de texto **Motivo**, digite um texto que forneça uma justificativa para a ativação e clique em **Ativar**.

    >**Observação**: o processo de aprovação automática deve ser concluído.

9. De volta à folha **Minhas funções \| Funções do Microsoft Entra ID**, clique na guia **Atribuições ativas** e observe que a listagem de **atribuições ativas** inclui o **Administrador de Segurança**, mas não a função **Leitor Global**.

    >**Observação**: agora você aprovará a função Leitor Global.

10. Saia do portal do Azure como **aaduser2**.

11. No navegador InPrivate, entre no portal do Azure em **`https://portal.azure.com/`** como **aaduser3**.

    >**Observação**: se você tiver problemas com a autenticação usando uma das contas de usuário, você pode entrar no locatário do Microsoft Entra usando sua conta de usuário para redefinir suas senhas ou reconfigurar suas opções de entrada.

12. Navegue até o **Microsoft Entra Privileged Identity Management** (na caixa de texto Pesquisar recursos, serviços e documentos na parte superior da página do portal do Azure, digite Microsoft Entra Privileged Identity Management e pressione a tecla Enter).

13. Na folha **Privileged Identity Management \| Início Rápido**, na seção **Tarefas**, clique em **Aprovar solicitações**.

14. Na folha **Aprovar solicitações \| Funções Microsoft Entra ID**, na seção **Solicitações para ativações de função**, marque a caixa de seleção da entrada que representa a solicitação de ativação da função **Leitor Global** por **aaduser2**.

15. Clique em **Aprovar**. Na folha **Aprovar Solicitação**, na caixa de texto **Justificativa**, digite um motivo para a ativação, observe os horários de início e término e clique em **Confirmar**. 

    >**Observação**: você também tem a opção de negar solicitações.

16. Saia do portal do Azure como **aaduser3**.

17. No navegador InPrivate, entre no portal do Azure em **`https://portal.azure.com/`** como **aaduser2**

18. Navegue até o **Microsoft Entra Privileged Identity Management** (na caixa de texto Pesquisar recursos, serviços e documentos na parte superior da página do portal do Azure, digite Microsoft Entra Privileged Identity Management e pressione a tecla Enter).

19. Na folha **Privileged Identity Management \| Início Rápido**, na seção **Tarefas**, selecione **Minhas funções**.

20. Na folha **Minhas funções \| Funções do Microsoft Entra ID**, clique na guia **Atribuições Ativas** e verifique que a função Leitor Global agora está ativa.

    >**Observação**: talvez seja necessário atualizar a página para exibir a lista atualizada de atribuições ativas.

21. Saia e feche a janela InPrivate do navegador.

> Resultado: você praticou a ativação de funções do PIM com e sem aprovação. 

### Exercício 3 – Criar uma revisão de acesso e analisar recursos de auditoria do PIM

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: configurar alertas de segurança para funções do Microsoft Entra ID no PIM
- Tarefa 2: revisar alertas do PIM, informações resumidas e informações detalhadas de auditoria

#### Tarefa 1: configurar alertas de segurança para funções do Microsoft Entra ID no PIM

Nesta tarefa, você reduzirá o risco associado a atribuições de funções "obsoletas". Você fará isso criando uma revisão de acesso do PIM para garantir que as funções atribuídas ainda sejam válidas. Especificamente, você analisará a função Leitor Global. 

1. Faça logon no portal do Azure **`https://portal.azure.com/`** usando sua conta.

    >**Observação**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra. Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra. Verifique se você está conectado como um usuário com a função Administrador global.
    
    >**Observação**: se você ainda não vir a entrada AdatumLab500-04, clique no link Alternar Diretório, selecione a linha AdatumLab500-04 e, em seguida, o botão Alternar.

2. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra Privileged Identity Management** e pressione a tecla **Enter**.

3. Navegue para a folha do **Privileged Identity Management**. 

4. Na folha **Privileged Identity Management \| Início rápido**, na seção **Gerenciar**, selecione **Funções do Microsoft Entra**.

5. Na folha **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, Selecione **Revisões de acesso**.

6. Na folha **AdatumLab500-04 \| Revisões de acesso**, selecione **Novo**:

7. Na folha **Criar revisão de acesso**, especifique as seguintes configurações (deixe as demais com seus valores padrão): 

   |Configuração|Valor|
   |---|---|
   |Nome da revisão|**Revisão do Leitor global**|
   |Data de Início|data de hoje| 
   |Frequência|**Uma vez**|
   |Data de término|final do mês atual|
   |Função, Selecionar Função(ões) com privilégios|**Leitor global**|
   |Revisores|**Usuários selecionados**|
   |Selecionar os revisores|sua conta|

8. Na folha **Criar revisão de acesso**, selecione **Iniciar**:
 
    >**Observação**: levará cerca de um minuto para que a revisão seja implantada e apareça na folha **AdatumLab500-04 \| Revisões de acesso**. Talvez seja necessário atualizar a página da Web. O status da revisão estará **Ativo**. 

9. Na folha **AdatumLab500-04 \| Revisões de acesso**, no cabeçalho **Revisão do Leitor global**, selecione a entrada **Leitor global**. 

10. Na folha **Revisão do Leitor global**, examine a página **Visão geral** e observe que os gráficos de **Progresso** mostram um único usuário na categoria **Não revisado**. 

11. Na folha **Revisão do Leitor global** na seção **Gerenciar**, selecione **Resultados**. Observe que aaduser2 está listado como tendo acesso a essa função.

12. Selecione **exibir** na linha **aaduser2** para exibir um log de auditoria detalhado com entradas que representam as atividades do PIM que envolvem esse usuário.

13. Navegue de volta para a folha **AdatumLab500-04 \| Revisões de acesso** 

14. Na folha **AdatumLab500-04 \| Revisões de acesso**, na seção **Tarefas**, selecione **Revisão de acesso** e, em seguida, escolha a entrada **Revisão do Leitor global**. 

15. Na folha **Revisão do Leitor global**, selecione a entrada**aaduser2**. 

16. Na caixa de texto **Motivo**, digite uma justificativa para aprovação e clique em **Aprovar** para manter a associação da função atual ou em **Negar** para revogá-la. 

17. Navegue de volta para a folha **Privileged Identity Management** e, na seção **Gerenciar**, selecione **Funções do Microsoft Entra ID**.

18. Na folha **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, Selecione **Revisões de acesso**.

19. Selecione a entrada que representa a revisão do **Leitor global**. Observe que o gráfico de **Progresso** foi atualizado para mostrar a sua revisão. 

#### Tarefa 2: analisar alertas do PIM, informações resumidas e informações detalhadas de auditoria. 

Nesta tarefa, você analisará os alertas do PIM, as informações resumidas e as informações detalhadas de auditoria. 

1. Navegue de volta para a folha **Privileged Identity Management** e, na seção **Gerenciar**, selecione **Funções do Microsoft Entra ID**.

2. Na folha **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, selecione **Alertas** e, em seguida, **Configuração**.

3. Na folha **Configurações de alerta**, analise os alertas pré-configurados e os níveis de risco. Clique em qualquer um deles para obter informações mais detalhadas. 

4. Retorne à folha **AdatumLab500-04 \| Início rápido** e selecione **Visão geral**. 

5. Na folha **AdatumLab500-04 \| Visão geral**, analise as informações resumidas sobre ativações de função, atividades do PIM, alertas e atribuições de função.

6. Na folha **AdatumLab500-04 \| Visão geral**, na seção **Atividade**, selecione **Auditoria de recursos**. 

    >**Observação**: o histórico de auditoria está disponível para todas as atribuições e ativações de funções com privilégios nos últimos 30 dias.

7. Observe que você pode recuperar informações detalhadas, incluindo **Hora**, **Solicitante**, **Ação**, **Nome do recurso**, **Escopo**, **Destino principal** e **Assunto**. 

> Resultado: você configurou uma revisão de acesso e analisou as informações de auditoria. 

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não incorra em custos inesperados. 

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra associado à assinatura do Azure na qual você implantou a VM do Azure **az500-04-vm1**.

    >**Observação**: se você não vir sua entrada de locatário principal do Microsoft Entra, clique no link Alternar Diretório, selecione a linha do seu locatário principal e clique no botão Alternar.

2. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

3. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado no laboratório anterior:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

5. Feche o painel do **Cloud Shell**. 

6. De volta ao portal do Azure, use o filtro **Diretório + assinatura** para alternar para o locatário **AdatumLab500-04** do Microsoft Entra.

7. Navegue até a folha **AdatumLab500-04 do Microsoft Entra** e, na seção **Gerenciar**, selecione **Licenças**.

8. Na folha **Licenças** | Visão Geral, selecione **Todos os produtos**, marque a caixa de seleção **P2 do Microsoft Entra ID** e clique nela para abrir.

    >**Observação**: no Laboratório 2 – Exercício 2 – Tarefa 4 **Atribuir licenças P2 do Microsoft Entra ID aos usuários do Microsoft Entra**, a tarefa era atribuir as Licenças Premium aos usuários **aaduser1, aaduser2, and aaduser3**, certifique-se de remover essas licenças dos usuários atribuídos

9. Na folha **P2 do Microsoft Entra ID – Usuários licenciados**, marque as caixas de seleção das contas de usuário às quais você atribuiu licenças **P2 do Microsoft Entra ID**. Selecione **Remover licença** no painel superior e, quando solicitado a confirmar, escolha **Sim**.

10. No portal do Azure, navegue até a folha **Usuários – Todos os usuários**, selecione a entrada que representa a conta de usuário **aaduser1**. Na folha **aaduser1 – Perfil** clique em **Excluir** e, quando solicitado a confirmar, escolha **Sim**.

11. Repita a mesma sequência de etapas para excluir as demais contas de usuário restantes que você criou.

12. Navegue até a folha **AdatumLab500-04 – Visão geral** do locatário do Microsoft Entra, selecione **Gerenciar locatários** e, na próxima tela, marque a caixa de seleção ao lado de**AdatumLab500-04** e escolha **Excluir**. Na folha **Excluir locatário "AdatumLab500-04"**, selecione o link **Obter permissão para excluir recursos do Azure**, na folha **Propriedades** do Microsoft Entra ID, defina **Gerenciamento de acesso para recursos do Azure** como **Sim** e selecione **Salvar**.

13. Saia do portal do Azure e entre novamente. 

14. Navegue de volta para a folha **Excluir diretório "AdatumLab500-04"** e selecione **Excluir**.

    >**Observação**: se a mensagem **Exclua todas as assinaturas e licenças** for exibida impedindo a exclusão do locatário, pode ser devido a alguma assinatura que ainda está vinculada ao locatário. Aqui a **licença Free P2** pode estar gerando o erro de validação. Excluir a assinatura de avaliação da Licença P2 usando a ID de Administrador global em Administrador do M365>> **Seus Produtos** e no portal da **Loja Comercial** resolveria esse problema. Além disso, observe que a exclusão do locatário leva mais tempo. Verifique a data de término da assinatura, após o término do período de avaliação, revisite o Microsoft Entra ID e tente excluir o locatário.    

> Para obter informações adicionais sobre essa tarefa, consulte [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
