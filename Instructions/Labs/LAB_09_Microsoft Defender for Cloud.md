---
lab:
  title: 09 - Microsoft Defender para Nuvem
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# Laboratório 09: Microsoft Defender para Nuvem
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito do Microsoft Defender para o ambiente baseado em nuvem. Você precisa especificamente:

- Configure o Microsoft Defender para Nuvem para monitorar uma máquina virtual.
- Revise as recomendações do Microsoft Defender para Nuvem para uma máquina virtual.
- Implemente as recomendações para configuração de convidado e acesso Just-in-time à VM. 
- Analise como a Classificação de Segurança pode ser usado para determinar o progresso na criação de uma infraestrutura mais segura.

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório você realizará os seguintes exercícios:

- Exercício 1: implementar o Microsoft Defender para Nuvem

## Diagrama do Microsoft Defender para Nuvem

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## Instruções

### Exercício 1: implementar o Microsoft Defender para Nuvem

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: configurar o Microsoft Defender para Nuvem
- Tarefa 2: revisar as recomendações do Microsoft Defender para Nuvem
- Tarefa 3: implementar a recomendação do Microsoft Defender para Nuvem para habilitar o Acesso just-in-time à VM

#### Tarefa 1: configurar o Microsoft Defender para Nuvem

Nesta tarefa, você vai integrar e configurar o Microsoft Defender para Nuvem.

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Defender para Nuvem** e pressione a tecla **Enter**.

3. No menu de navegação esquerdo, clique em **Introdução**. No painel **Microsoft Defender para Nuvem \| Introdução**, clique em **Fazer upgrade**.
     
4. No painel **Microsoft Defender para Nuvem \| Introdução**, na guia Instalar agentes, role para baixo e clique em **Instalar agentes**. 

5. No painel **Microsoft Defender para Nuvem \| Introdução**, na guia **Fazer upgrade** >> role para baixo até a seção **Selecionar espaços de trabalho com recursos de segurança aprimorados** ser exibida >> ative o **plano do Microsoft Defender** selecionando seu Workspace do Log Analytics e, em seguida, clique no botão grande azul de Upgrade.  

    >**Observação**: revise todos os recursos disponíveis como parte dos planos do Microsoft Defender. 

6. Navegue até **Microsoft Defender para Nuvem** e, no painel de navegação esquerdo, na seção Gerenciamento, clique em **Configurações do ambiente**.

7. No painel **Microsoft Defender para Nuvem \| Configurações de ambiente**, role para baixo, expanda até a assinatura ser exibida e clique na assinatura relevante. 

8. No painel **Configurações \| Planos do Defender**, selecione **Habilitar todos os planos** e, se necessário, clique em **Salvar**.

9. Navegue de volta para a folha de **configurações de ambiente do Microsoft Defender para Nuvem \|**, expanda até que sua assinatura seja exibida e clique na entrada que representa o Workspace do Log Analytics criado no laboratório anterior.

10. Na folha **Configurações \| Planos do Defender**, verifique se todas as opções estão "Ativadas". Se necessário, clique em **Habilitar todos os planos** e, em seguida, clique em **Salvar**.

11. Selecione **Coleta de dados** no painel **Configurações \| Planos do Defender**. Clique em **Todos os Eventos** e **Salvar**.

#### Tarefa 2: revise as recomendações do Microsoft Defender para Nuvem

Nesta tarefa, você revisará as recomendações do Microsoft Defender para Nuvem. 

1. No portal do Azure, navegue de volta para o painel **Visão geral do Microsoft Defender para Nuvem\|**. 

2. No painel **Visão geral do Microsoft Defender para Nuvem\|**, examine o bloco **Classificação de segurança**.

    >**Observação**: registre a pontuação atual, se ela estiver disponível.

3. Navegue de volta para o painel **Visão geral do Microsoft Defender para Nuvem\|**, clique em **Recursos avaliados**.

4. No painel **Inventário**, clique na entrada **myVM**.

    >**Observação**: talvez seja necessário aguardar alguns minutos e atualizar a página do navegador para que a entrada apareça.
    
5. No painel **Resource health**, na guia **Recomendações**, revise a lista de recomendações para **myVM**.

#### Tarefa 3: implementar a recomendação do Microsoft Defender para Nuvem para habilitar o Acesso just-in-time à VM

Nesta tarefa, você implementará a recomendação do Microsoft Defender para Nuvem para habilitar o acesso Just-in-time à VM na máquina virtual. 

1. No portal do Azure, navegue de volta para a folha **Visão geral do Microsoft Defender para Nuvem \|** e clique em **Proteções de carga de trabalho** em **Segurança da nuvem** no painel de navegação esquerdo.

2. No painel **Proteções de carga de trabalho do Microsoft Defender para Nuvem\|**, role para baixo até a seção **Proteção avançada** e clique no bloco **Acesso à VM just-in-time**.

3. No painel **Acesso à VM just-in-time**, na seção **Máquinas virtuais**, selecione **Não configurado** e selecione a caixa de seleção da entrada **myVM**.

    >**Observação**: talvez seja necessário aguardar alguns minutos, atualizar a página do navegador e selecionar **Não configurado** novamente para que a entrada apareça.

4. Clique na opção **Habilitar JIT em 1 VM** na extremidade direita da seção **Máquinas virtuais**.

5. No painel de **Configuração de acesso JIT VM**, na extremidade direita da linha que faz referência à porta **22**, clique no botão de reticências e, em seguida, clique em **Excluir**.

6. No painel **Configuração de acesso à VM JIT**, clique em **Salvar**.

    >**Observação**: monitore o progresso da configuração clicando no ícone **Notificações** na barra de ferramentas e visualizando a folha **Notificações**. 

    >**Observação**: pode levar algum tempo para que a implementação de recomendações neste laboratório seja refletida pela Classificação de Segurança. Verifique periodicamente a Classificação de Segurança para determinar o impacto da implementação desses recursos. 

> Resultados: você integrou o Microsoft Defender para Nuvem e implementou recomendações de máquina virtual. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
