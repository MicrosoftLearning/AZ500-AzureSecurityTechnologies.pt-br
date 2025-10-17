---
lab:
  title: 09 – Configurar recursos de segurança aprimorada para servidores do Microsoft Defender para Nuvem
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Laboratório 09: Configurar recursos de segurança aprimorada para servidores do Microsoft Defender para Nuvem

# Manual de laboratório do aluno

## Cenário do laboratório

Como engenheiro de segurança do Azure para uma empresa global de comércio eletrônico, você é responsável por proteger a infraestrutura de nuvem da empresa. A organização depende muito de VMs (máquinas virtuais) do Azure e servidores locais para executar aplicativos críticos, gerenciar dados do cliente e processar transações. O CISO (diretor de segurança de informação) identificou a necessidade de medidas de segurança aprimoradas para proteger esses recursos contra ameaças cibernéticas, vulnerabilidades e configurações incorretas. Você ficou responsável por habilitar o Microsoft Defender para Servidores no Microsoft Defender para Nuvem para fornecer proteção avançada contra ameaças e monitoramento de segurança para VMs do Azure e servidores híbridos.

## Objetivos do laboratório

- Configurar recursos de segurança aprimorada do Microsoft Defender para Nuvem para servidores
  
- Examine os recursos de segurança aprimorados do Plano 2 do Microsoft Defender para Servidores

## Instruções para o exercício

### Configurar recursos de segurança aprimorada do Microsoft Defender para Nuvem para servidores

1. Na caixa de texto Pesquisar recursos, serviços e documentos na parte superior da página do portal do Azure, digite **Microsoft Defender para Nuvem** e pressione a tecla **Enter**.

2. Na folha **Gerenciamento** do **Microsoft Defender para Nuvem**, acesse **Configurações de ambiente**. Expanda as pastas de configurações do ambiente até que a seção **assinatura** seja exibida e clique na **assinatura** para exibir os detalhes.

   ![Captura de tela das Configurações de Ambiente do Microsoft Defender para Nuvem](../media/defender-for-cloud-environment-settings.png)
   
3. Na folha **Configurações**, em **Planos do Defender**, expanda **CWP (Proteção de Carga de Trabalho na Nuvem)**.

4. Na lista **Plano de CWP (Proteção de Carga de Trabalho na Nuvem)**, selecione **Servidores**. No lado direito da página, altere o **Status** de **Desativado** para **Ativado** e clique em **Salvar**.

5. Para examinar os detalhes do **Plano 2 do Microsoft Defender para Servidores**, escolha **Alterar plano >**.

   Observação: habilitar o plano de Servidores de CWP (Proteção de Carga de Trabalho na Nuvem) de Desativado para Ativado habilita o Plano 2 do Microsoft Defender para servidores.
 
   ![Captura de tela da página de seleção do plano Microsoft Defender para Nuvem.](../media/defender-for-cloud-plan-selection.png)
   
> **Resultados**: você habilitou o Plano 2 do Microsoft Defender para servidores na sua assinatura.
