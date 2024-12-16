---
lab:
  title: 09 - Microsoft Defender para Nuvem
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# Laboratório 09: Microsoft Defender para Nuvem
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito do Microsoft Defender para o ambiente baseado em nuvem. Você precisa especificamente:

- Configure os recursos de segurança aprimorados para servidores do Microsoft Defender para Nuvem para monitorar uma máquina virtual.
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

#### Tarefa 1: Configurar recursos de segurança aprimorados para servidores do Microsoft Defender para Nuvem

Nesta tarefa, você vai integrar e configurar o recursos de segurança aprimorados para servidores do Microsoft Defender para Nuvem.

1. Inicie uma sessão no navegador e entre na [assinatura do Azure](https://azure.microsoft.com/en-us/free/?azure-portal=true). na qual você tem acesso administrativo.

2. Na caixa de texto Pesquisar recursos, serviços e documentos na parte superior da página do portal do Azure, digite Microsoft Defender para Nuvem e pressione a tecla Enter.

3. Na folha Gerenciamento do Microsoft Defender para Nuvem, acesse Configurações de ambiente. Expanda as pastas de configurações do ambiente até que a seção assinatura seja exibida e clique na assinatura para exibir os detalhes.

4. Na folha Configurações, em Planos do Defender, expanda CWP (Proteção de Carga de Trabalho na Nuvem).
  
5. Na lista Plano de CWP (Proteção de Carga de Trabalho na Nuvem), selecione Servidores. No lado direito da página, altere o Status de Desativado para Ativado e clique em Salvar.
  
6. Para examinar os detalhes do Plano 2 do Microsoft Defender para Servidores, escolha Alterar plano >.

>**Observação**: habilitar o plano de Servidores de CWP (Proteção de Carga de Trabalho na Nuvem) de Desativado para Ativado habilita o Plano 2 do Microsoft Defender para servidores.

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
