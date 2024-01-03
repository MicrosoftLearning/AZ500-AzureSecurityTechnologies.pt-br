---
lab:
  title: 05 – Privileged Identity Management do Microsoft Entra
  module: Module 01 - Manage Identity and Access
---

# Laboratório 05: Privileged Identity Management do Microsoft Entra
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito que usa o PIM (Privileged Identity Management) do Microsoft Entra para habilitar a administração just-in-time e controlar o número de usuários que podem executar operações privilegiadas. Os requisitos específicos são:

- Crie uma atribuição permanente do usuário aaduser2 do Microsoft Entra ID para a função de Administrador de Segurança. 
- Configure o usuário aaduser2 do Microsoft Entra ID para se qualificar para as funções de Administrador de Cobrança e Leitor Global.
- Configurar a ativação de função de Leitor Global para exigir uma aprovação do usuário aaduser3 do Microsoft Entra ID
- Configure uma revisão de acesso da função de Leitor Global e revise os recursos de auditoria.

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

> Antes de continuar, verifique se você concluiu o Laboratório 04: MFA, Acesso Condicional e Microsoft Entra Identity Protection. Você precisará do locatário do Microsoft Entra, AdatumLab500-04, e das contas de usuário aaduser1, aaduser2 e aaduser3.

## Objetivos do laboratório

Neste laboratório, você realizará os seguintes exercícios:

- Exercício 1: configurar usuários e funções do PIM.
- Exercício 2: ativar as funções do PIM com e sem aprovação.
- Exercício 3: criar uma revisão de acesso e revisar os recursos de auditoria do PIM.

## Diagrama de Privileged Identity Management do Microsoft Entra

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/aef34a22-8ebd-4015-a04a-7ac3c357b862)

## Instruções

### Exercício 1 – Configurar usuários e funções do PIM

#### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: qualificar um usuário para uma função.
- Tarefa 2: configurar uma função para exigir aprovação para ativar e adicionar um membro qualificado.
- Tarefa 3: conceder a um usuário uma atribuição permanente para uma função. 

#### Tarefa 1: qualificar um usuário para uma função

Nesta tarefa, você tornará um usuário qualificado para uma função do Microsoft Entra ID.

1. Entre no portal do Azure em **`https://portal.azure.com/`**.

    >**Observação**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra. Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra. Verifique se você está conectado como um usuário com a função de Administrador Global.
    
    >**Observação**: se você ainda não vir a entrada AdatumLab500-04, clique no link Alternar diretório, selecione a linha AdatumLab500-04 e clique no botão Alternar.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Privileged Identity Management do Microsoft Entra** e pressione a tecla **Enter**.

3. No painel **Privileged Identity Management**, na seção **Gerenciar**, clique em **Funções do Microsoft Entra ID**.

4. No painel **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, clique em **Funções**.

5. No painel **AdatumLab500-04 \| Funções**, clique em **+ Adicionar atribuições**.

6. No painel **Adicionar atribuições**, no menu suspenso **Selecionar função**, selecione **Administrador de cobrança**.

7. Clique no link **Nenhum membro selecionado**, no painel **Selecionar um membro**, clique em **aaduser2** e, em seguida, clique em **Selecionar**.

8. De volta ao painel **Adicionar atribuições**, clique em **Avançar**. 

9. Verifique se o **Tipo de atribuição** está definido como **Qualificado** e clique em **Atribuir**.
 
10. De volta ao painel **AdatumLab500-04 \| Funções**, na seção **Gerenciar**, clique em **Atribuições**.

11. De volta ao painel **AdatumLab500-04 \| Atribuições**, observe as guias para **Habilitar atribuições**, **Ativar atribuições** e **Atribuições expiradas**.

12. Verifique na guia **Atribuições qualificadas** se **aaduser2** é mostrado como um **Administrador de cobrança**. 

    >**Observação**: durante o login, aaduser2 estará qualificado para usar a função de Administrador de cobrança. 

#### Tarefa 2: configurar uma função para exigir aprovação para ativar e adicionar um membro qualificado

1. No Portal do Azure, navegue de volta até a folha **Privileged Identity Management** e clique em **Funções do Microsoft Entra ID**.

2. No painel **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, clique em **Funções**.

3. No painel **AdatumLab500-04 \| Funções**, clique na entrada da função de **Leitor global**. 

4. No painel **Leitor Global \| Atribuições**, clique no ícone **Configurações de função** na barra de ferramentas do painel e revise as definições de configuração da função, incluindo os requisitos de Autenticação Multifator do Azure.

5. Clique em **Editar**.

6. Na guia **Ativação**, habilite a caixa de seleção **Exigir aprovação para ativar**.

7. Clique em **Selecionar aprovadores**, no painel **Selecionar um membro**, clique em **aaduser3** e, em seguida, clique em **Selecionar**.

8. Clique em **Avançar: Atribuição**.

9. Desmarque a caixa de seleção **Permitir atribuição qualificada permanente**, deixando todas as outras configurações com os valores padrão.

10. Clique em **Avançar: Notificação**.

11. Revise as configurações de **Notificação**, deixe tudo definido por padrão e clique em **Atualizar**.

    >**Observação**: qualquer pessoa que tentar usar a função de Leitor Global agora precisará da aprovação do aaduser3. 

12. No painel **Leitor Global \| Atribuições**, clique em **+ Adicionar atribuições**.

13. No painel **Adicionar atribuições**, clique em **Nenhum membro selecionado**, na folha **Selecionar um membro**, clique em **aaduser2** e em **Selecionar**.

14. Clique em **Avançar**. 

15. Verifique se o **Tipo de atribuição** é **Qualificado** e revise as configurações de duração qualificada.

16. Clique em **Atribuir**.

    >**Observação**: o usuário aaduser2 se qualifica para a função de Leitor Global. 
 
#### Tarefa 3: conceder a um usuário uma atribuição permanente para uma função.

1. No Portal do Azure, navegue de volta até a folha **Privileged Identity Management** e clique em **Funções do Microsoft Entra ID**.

2. No painel **AdatumLab500-04 \| Início rápido**, na seção **Gerenciar**, clique em **Funções**.

3. No painel **AdatumLab500-04 \| Funções**, clique em **+ Adicionar atribuições**.

4. Na folha **Adicionar atribuições**, na lista suspensa **Selecionar função**, selecione **Administrador de Segurança**.

5. No painel **Adicionar atribuições**, clique em **Nenhum membro selecionado**, na folha **Selecionar um membro**, clique em **aaduser2** e, em seguida, clique em **Selecionar**.

6. Clique em **Avançar**. 

7. Revise as configurações de **Tipo de atribuição** e clique em **Atribuir**.

8. No painel de navegação esquerdo, clique em **Atribuições**. Na guia **Atribuições qualificadas**, em **Administrador de segurança**, selecione **Atualizar** para a atribuição **aaduser2**. Selecione **Permanentemente Qualificado** e **Salvar**.

    >**Observação**: o usuário aaduser2 agora está permanentemente qualificado para a função de Administrador de Segurança.
    
### Exercício 2 – Ativar funções de PIM com e sem aprovação

#### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: ativar uma função que não requer aprovação. 
- Tarefa 2: ativar uma função que requer aprovação. 

#### Tarefa 1: ativar uma função que não requer aprovação.

Nesta tarefa, você ativará uma função que não requer aprovação.

1. Abra uma janela do navegador InPrivate.

2. Na janela do navegador InPrivate, navegue até o portal do Azure em **`https://portal.azure.com/`** e entre usando a conta de usuário **aaduser2**.

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da conta de usuário **aaduser2**, incluindo o nome de domínio DNS do locatário do Microsoft Entra, que você registrou anteriormente neste laboratório. Esse nome de usuário está no formato aaduser2@`<your_tenant_name>`.onmicrosoft.com, em que `<your_tenant_name>` é o espaço reservado que representa seu nome de locatário exclusivo do Microsoft Entra. 

3. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Privileged Identity Management do Microsoft Entra** e pressione a tecla **Enter**.

4. No painel **Privileged Identity Management**, na seção **Tarefas**, clique em **Minhas funções**.

5. Você verá três **Funções qualificadas** para **aaduser2**: **Leitor global**, **Administrador de segurança** e **Administrador de cobrança**. 

6. Na linha que exibe a entrada da função **Administrador de cobrança**, clique em **Ativar**.

7. Se necessário, clique no aviso **Verificação adicional necessária. Clique para continuar** e siga as instruções para verificar sua identidade.

8. Na folha **Ativar – Administrador de cobrança**, na caixa de texto **Motivo**, digite um texto que forneça uma justificativa para a ativação e clique em **Ativar**.

    >**Observação**: quando você ativa uma função no PIM, ela pode levar até 10 minutos para entrar em vigor. 
    
    >**Observação**: quando sua atribuição de função estiver ativa, seu navegador será atualizado (se algo der errado, basta sair e entrar novamente no portal do Azure usando a conta de usuário **aaduser2**).

9. Navegue de volta para o painel **Privileged Identity Management** e, na seção **Tarefas**, clique em **Minhas funções**.

10. No painel **Minhas funções \| Funções do Microsoft Entra ID**, alterne para a guia **Atribuições ativas**. Observe que a função de **Administrador de cobrança** está **Ativada**.

    >**Observação**: após uma função ter sido ativada, ela será desativada automaticamente quando a **Hora de término**(duração qualificada) for atingida.

    >**Observação**: se você concluir as tarefas de administrador com antecedência, poderá desativar uma função manualmente.

11.  Na lista de **Atribuições Ativas **, na linha que representa a função Administrador de Cobrança, clique no link **Desativar**.

12.  No painel **Desativar – Administrador de Cobrança**, clique em **Desativar** novamente para confirmar.


#### Tarefa 2: ativar uma função que requer aprovação. 

Nesta tarefa, você ativará uma função que requer aprovação.

1. Na janela do navegador InPrivate, no portal do Azure, enquanto estiver conectado como o usuário **aaduser2**, navegue de volta para a folha de **Início rápido** do Privileged Identity Management\|. 

2. No painel Início rápido do **Privileged Identity Management \|, na seção **Tarefas**, clique em **Minhas funções****.

3. **No painel \| Minhas funções ** das funções do Microsoft Entra ID, na lista de **Atribuições qualificadas**, na linha que exibe a função **Leitor Global**, clique em **Ativar**. 

4. No painel **Ativar – Leitor Global**, na caixa de texto **Motivo**, digite um texto justificando a ativação e clique em **Ativar**.

5. Clique no ícone **Notificações** na barra de ferramentas do portal do Azure e exiba a notificação informando que sua solicitação está pendente de aprovação.

    >**Observação**: como administrador de funções com privilégios, você pode revisar e cancelar solicitações a qualquer momento. 

6. **No painel \|Minhas funções ** das funções do Microsoft Entra ID, localize a função **Administrador de segurança** e clique em **Ativar**. 

7. Se necessário, clique no aviso **Verificação adicional necessária. Clique para continuar** e siga as instruções para verificar sua identidade.

    >**Observação**: você só precisa se autenticar uma vez por sessão. 

8. Quando você voltar à interface do portal do Azure, no painel **Ativar – Administrador de segurança**, na caixa de texto **Motivo**, digite um texto justificando a ativação e clique em **Ativar**.

    >**Observação**: o processo de aprovação automática deve ser concluído.

9. De volta ao painel **Minhas funções \| funções do Microsoft Entra ID**, clique na guia **Atribuições ativas** e observe que a listagem de **atribuições ativas** inclui **Administrador de segurança** mas não a função **Leitor global**.

    >**Observação**: agora você aprovará a função Leitor Global.

10. Saia do portal do Azure como **aaduser2**.

11. No navegador InPrivate, entre no portal do Azure em **`https://portal.azure.com/`** como **aaduser3**.

    >**Observação**: se você tiver problemas com a autenticação usando qualquer uma das contas de usuário, poderá entrar no locatário do Microsoft Entra usando sua conta de usuário para redefinir as senhas ou reconfigurar as opções de entrada.

12. No portal do Azure, navegue até **Privileged Identity Management do Microsoft Entra** (Na caixa de texto Pesquisar recursos, serviços e documentos na parte superior da página do portal do Azure, digite Microsoft Entra Privileged Identity Management e pressione a tecla Enter).

13. No painel **Privileged Identity Management \| Início rápido** na seção, **Tarefas**, clique em **Aprovar solicitações**.

14. No painel **Aprovar solicitações \| de funções do Microsoft Entra ID**, na seção **Solicitações para ativações de função**, marque a caixa de seleção da entrada que representa a solicitação de ativação de função para a função **Leitor Global** por **aaduser2**.

15. Clique em **Aprovar**. No painel **Aprovar Solicitação**, na caixa de texto **Justificativa**, digite um motivo para a ativação, anote as horas de início e término e clique em **Confirmar**. 

    >**Observação**: você também tem a opção de negar solicitações.

16. Saia do portal do Azure como **aaduser3**.

17. No navegador InPrivate, entre no portal do Azure em **`https://portal.azure.com/`** como **aaduser2**

18. No portal do Azure, navegue até **Privileged Identity Management do Microsoft Entra** (Na caixa de texto Pesquisar recursos, serviços e documentos na parte superior da página do portal do Azure, digite Microsoft Entra Privileged Identity Management e pressione a tecla Enter).

19. No painel Início rápido do **Privileged Identity Management \|, na seção **Tarefas**, clique em **Minhas funções****.

20. Na folha **Minhas funções \| Funções do Microsoft Entra ID**, clique na guia **Atribuições Ativas** e verifique se a função Leitor Global está ativa.

    >**Observação**: talvez seja necessário atualizar a página para exibir a lista atualizada de atribuições ativas.

21. Saia e feche a janela do navegador InPrivate.

> Resultado: você praticou a ativação de funções PIM com e sem aprovação. 

### Exercício 3  – Criar uma Revisão de Acesso e revisar recursos de auditoria do PIM

#### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: configurar alertas de segurança para funções do Microsoft Entra ID no PIM
- Tarefa 2: revisar alertas PIM, informações resumidas e informações detalhadas de auditoria

#### Tarefa 1: configurar alertas de segurança para funções do Microsoft Entra ID no PIM

Nesta tarefa, você reduzirá o risco associado a atribuições de função "obsoletas". Você fará isso criando uma revisão de acesso PIM para garantir que as funções atribuídas ainda sejam válidas. Especificamente, você analisará a função Leitor Global. 

1. Entre no portal do Azure **`https://portal.azure.com/`** usando sua conta.

    >**Observação**: verifique se você está conectado ao locatário **AdatumLab500-04** do Microsoft Entra. Você pode usar o filtro **Diretório + assinatura** para alternar entre locatários do Microsoft Entra. Verifique se você está conectado como um usuário com a função de Administrador Global.
    
    >**Observação**: se você ainda não vir a entrada AdatumLab500-04, clique no link Alternar diretório, selecione a linha AdatumLab500-04 e clique no botão Alternar.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Privileged Identity Management do Microsoft Entra** e pressione a tecla **Enter**.

3. Navegue para o painel do **Privileged Identity Management**. 

4. No painel **Início rápido do Privileged Identity Management \|**, na seção **Gerenciar**, clique em **Funções do Microsoft Entra**.

5. No painel **Início rápido do AdatumLab500-04 \| **, na seção **Gerenciar**, clique em **Acessar revisões**.

6. No painel **Revisões de acesso do AdatumLab500-04 \|**, clique em **Novo**:

7. No painel **Criar uma revisão de acesso**, especifique as seguintes configurações (deixe as demais com seus valores padrão): 

   |Configuração|Valor|
   |---|---|
   |Nome da revisão|**Revisão do Leitor Global**|
   |Data de Início|data de hoje| 
   |Frequência|**Uma vez**|
   |Data de Término|final do mês atual|
   |Função, Selecionar Funções com Privilégios|**Leitor global**|
   |Revisores|**Usuários selecionados**|
   |Selecionar os revisores|sua conta|

8. No painel **Criar uma revisão de acesso**, clique em **Iniciar**:
 
    >**Observação**: levará cerca de um minuto para que a revisão seja implantada e apareça no painel de **Revisões de acesso do AdatumLab500-04 \|**. Talvez seja necessário atualizar a página da Web. O status da revisão estará **Ativo**. 

9. No painel **Revisões de acesso do AdatumLab500-04 \|**, no cabeçalho **Revisão do Leitor Global **, clique na entrada **Leitor Global**. 

10. No painel **Revisão do Leitor Global**, examine a página **Visão geral** e observe que os gráficos de **Progresso** mostram um único usuário na categoria **Não revisado**. 

11. No painel **Revisão do Leitor Global**, na seção **Gerenciar**, clique em **Resultados**. Observe que aaduser2 está listado como tendo acesso a essa função.

12. Clique em **exibir** na linha **aaduser2** para exibir um log de auditoria detalhado com entradas que representam as atividades de PIM que envolvem esse usuário.

13. Navegue de volta para a folha de **Revisões de acesso do AdatumLab500-04 \|**.

14. No painel **Revisões de acesso do AdatumLab500-04\|**, na seção **Tarefas**, clique em **Acesso de revisão** e, em seguida, clique na entrada **Revisão do Leitor Global**. 

15. No painel **Revisão do Leitor Global**, clique na entrada **aaduser2**. 

16. Na caixa de texto **Motivo**, digite uma justificativa para aprovação e clique em **Aprovar** para manter a associação da função atual ou em **Negar** para revogá-la. 

17. Navegue de volta para o painel **Privileged Identity Management** e, na seção **Gerenciar**, clique em **funções do Microsoft Entra ID**.

18. No painel **Início rápido do AdatumLab500-04 \| **, na seção **Gerenciar**, clique em **Acessar revisões**.

19. Selecione a entrada que representa a Revisão do **Leitor Global**. Observe que o gráfico do **Progresso** foi atualizado para mostrar a sua revisão. 

#### Tarefa 2: revisar os alertas PIM, informações resumidas e informações detalhadas de auditoria. 

Nesta tarefa, você analisará os alertas PIM, as informações resumidas e as informações detalhadas de auditoria. 

1. Navegue de volta para o painel **Privileged Identity Management** e, na seção **Gerenciar**, clique em **funções do Microsoft Entra ID**.

2. No painel **Início rápido do AdatumLab500-04\|**, na seção **Gerenciar**, clique em **Alertas** e em **Configuração**.

3. Na folha **Configurações de alerta**, revise os alertas pré-configurados e os níveis de risco. Clique em qualquer um deles para obter informações mais detalhadas. 

4. Retorne ao painel de **Início rápido do AdatumLab500-04 \|** e clique em **Visão geral**. 

5. No painel **Visão geral do AdatumLab500-04 \|**, revise as informações resumidas sobre ativações de função, atividades do PIM, alertas e atribuições de função.

6. No painel **Visão geral do AdatumLab500-04 \|**, na seção **Atividade**, clique em **Auditoria de recursos**. 

    >**Observação**: o histórico de auditoria está disponível para todas as atribuições e ativações de funções com privilégios nos últimos 30 dias.

7. Observe que você pode recuperar informações detalhadas, incluindo **Hora**, **Solicitante**, **Ação**, **Nome do recurso**, **Escopo**, **Destino principal** e **Assunto**. 

> Resultado: você configurou uma revisão de acesso e revisou as informações de auditoria. 

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados. 

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra associado à assinatura do Azure na qual você implantou a VM do Azure **az500-04-vm1**.

    >**Observação**: se você não vir sua entrada de locatário principal do Microsoft Entra, clique no link Alternar diretório, selecione sua linha de locatário principal e clique no botão Alternar.

2. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

3. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado no laboratório anterior:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

5. Feche o painel do **Cloud Shell**. 

6. De volta ao portal do Azure, use o filtro **Diretório + assinatura** para alternar para o locatário do AMicrosoft Entra **AdatumLab500-04**.

7. Navegue até o painel do **Microsoft Entra AdatumLab500-04** e, na seção **Gerenciar**, clique em **Licenças**.

8. No painel **Licenças** | Visão geral, clique em **Todos os produtos**, marque a caixa de seleção **Microsoft Entra ID P2** e clique nela para abrir.

    >**Observação**: no Laboratório 4 – Exercício 2 – Tarefa 4 **Atribuir licenças do Microsoft Entra ID P2 aos usuários do Microsoft Entra** foi para atribuir as Licenças Premium aos usuários **aaduser1, aaduser2 e aaduser3**, remova essas licenças dos usuários atribuídos

9. No painel **Microsoft Entra ID P2 – Usuários licenciados**, marque as caixas de seleção das contas de usuário às quais você atribuiu licenças do **Microsoft Entra ID P2**. Clique em **Remover licença** no painel superior e, quando solicitado a confirmar, selecione **Sim**.

10. No portal do Azure, navegue até a folha **Usuários – Todos os usuários**, clique na entrada que representa a conta de usuário **aaduser1**, no painel **aaduser1 – Perfil** clique em **Excluir** e, quando solicitado a confirmar, selecione **Sim**.

11. Repita a mesma sequência de etapas para excluir as demais contas de usuário que você criou.

12. Navegue até o painel **AdatumLab500-04 – Visão geral** do locatário do Microsoft Entra, selecione **Gerenciar locatários** e, na próxima tela, marque a caixa de seleção ao lado de **AdatumLab500-04** e selecione **Excluir**. No painel **Excluir locatário "AdatumLab500-04"**, selecione o link **Obter permissão para excluir recursos do Azure**, no painel **Propriedades** do AMicrosoft Entra ID, defina **Gerenciamento de acesso para recursos do Azure** como **Sim** e selecione **Salvar**.

13. Saia do portal do Azure e entre novamente. 

14. Navegue de volta para o painel **Excluir diretório "AdatumLab500-04"** e clique em **Excluir**.

    >**Observação**: ainda não é possível excluir o locatário e gerar um erro **Excluir todas as assinaturas baseadas em licença**, possivelmente devido a assinaturas que foram vinculadas ao locatário. Aqui a **licença Free P2** pode estar gerando o erro de validação. Excluir a assinatura de avaliação da Licença P2 usando o ID de administrador global do administrador do M365>> **Seus Produtos** e do portal do **Loja Comercial** resolveria esse problema. Observe também que a exclusão do locatário leva mais tempo. Verifique a Data de término da assinatura, após o término do período de avaliação, revisite o Microsoft Entra ID e tente excluir o locatário.    

> Para obter informações adicionais sobre essa tarefa, confira [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
