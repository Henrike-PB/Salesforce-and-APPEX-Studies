---
title: Configuração do VS Code para Desenvolvimento Salesforce
parent: Salesforce & APPEX Studies
nav_order: 3
---

# Configuração do VS Code para Desenvolvimento Salesforce

Aqui está um guia passo a passo detalhado para configurar o Visual Studio Code para desenvolvimento com Salesforce.

## Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/) instalado
- Conta de desenvolvedor Salesforce ([Developer Edition gratuita](https://developer.salesforce.com/signup))
- Node.js instalado (para algumas ferramentas)

## Passo 1: Instalar as Extensões Necessárias

1. Abra o VS Code
2. Acesse a aba de extensões (ícone de quadrados no menu lateral ou `Ctrl+Shift+X`)
3. Instale as seguintes extensões:

   - **Salesforce Extension Pack** (pacote completo que inclui):
     - Salesforce CLI Integration
     - Apex
     - Visualforce
     - Lightning Web Components
     - Apex Debugger
     - Apex Replay Debugger
     - Apex PMD
     - Prettier - Code formatter (opcional, mas recomendado)

## Passo 2: Instalar o Salesforce CLI

1. Baixe e instale o Salesforce CLI de acordo com seu sistema operacional:
   - [Windows/Mac/Linux](https://developer.salesforce.com/tools/sfdxcli)
2. Após instalação, verifique no terminal (Ctrl+`):
   ```bash
   sfdx --version
   ```
   Deve retornar a versão instalada.

## Passo 3: Configurar o Ambiente Salesforce

1. Autenticar na org Salesforce:
   ```bash
   sfdx auth:web:login -d -a MyOrg
   ```
   - `-d` define como org padrão
   - `-a MyOrg` é um alias para sua org (pode ser qualquer nome)

2. Isso abrirá seu navegador para login na Salesforce. Use suas credenciais de desenvolvedor.

## Passo 4: Criar um Projeto Salesforce

1. Crie uma pasta para seu projeto
2. No VS Code, abra a pasta (`File > Open Folder`)
3. Abra o terminal integrado (`Ctrl+` ``)
4. Execute:
   ```bash
   sfdx force:project:create -n meuProjetoSalesforce
   ```
5. Navegue até a pasta do projeto:
   ```bash
   cd meuProjetoSalesforce
   ```

## Passo 5: Configurar o VS Code para o Projeto

1. Crie um arquivo `.vscode/settings.json` na raiz do projeto com:
   ```json
   {
     "salesforcedx-vscode-core.push-or-deploy-on-save.enabled": true,
     "salesforcedx-vscode-core.show-cli-success-msg": true,
     "salesforcedx-vscode-core.enable-sobject-refresh-on-startup": true,
     "salesforcedx-vscode-apex.enable-sobject-refresh-on-startup": true,
     "salesforcedx-vscode-core.retrieve-test-code-coverage": true,
     "editor.tabSize": 2,
     "files.exclude": {
       "**/.sfdx": true,
       "**/.localdevserver": true
     }
   }
   ```

## Passo 6: Recuperar Metadados da Org

1. Para recuperar metadados existentes:
   ```bash
   sfdx force:source:retrieve -u MyOrg -m "ApexClass,CustomObject,LightningComponentBundle"
   ```
   (Substitua MyOrg pelo alias que você usou na autenticação)

## Passo 7: Configurar o Debugger

1. Instale a extensão "Apex Debugger for VS Code" (já incluída no Extension Pack)
2. Crie um arquivo `.vscode/launch.json` com:
   ```json
   {
     "version": "0.2.0",
     "configurations": [
       {
         "name": "Launch Apex Debugger",
         "type": "apex",
         "request": "launch",
         "sfdxProject": "${workspaceRoot}"
       }
     ]
   }
   ```

## Passo 8: Configurar Controle de Versão (Git)

1. Inicialize um repositório Git:
   ```bash
   git init
   ```
2. Crie um `.gitignore` com:
   ```
   # Salesforce
   .sfdx/
   .localdevserver/
   **/*.sfdx
   **/*.org

   # VS Code
   .vscode/
   *.code-workspace

   # Node
   node_modules/
   npm-debug.log

   # Outros
   *.swp
   *.swo
   .DS_Store
   ```

## Dicas Úteis

1. **Atalhos importantes**:
   - `Ctrl+Shift+P` - Abre a paleta de comandos
   - Digite `SFDX` para ver todos os comandos específicos

2. **Comandos úteis**:
   - Deploy para org: `SFDX: Deploy Source to Org`
   - Criar classe Apex: `SFDX: Create Apex Class`
   - Abrir org: `SFDX: Open Default Org`

3. **Configurações recomendadas**:
   ```json
   {
     "editor.formatOnSave": true,
     "apexPMD.rulesets": [
       "${workspaceRoot}/rulesets/apex_ruleset.xml",
       "${workspaceRoot}/rulesets/apex_security_ruleset.xml"
     ]
   }
   ```

## Solução de Problemas Comuns

1. **Erros de autenticação**:
   - Execute `sfdx auth:logout` e autentique novamente
   - Verifique se a org está ativa em `sfdx force:org:list`

2. **Problemas com extensões**:
   - Reinicie o VS Code após instalar extensões
   - Verifique atualizações (`Ctrl+Shift+P > Extensions: Show Outdated Extensions`)

3. **Erros de deploy**:
   - Verifique os logs no Output (`View > Output` e selecione "Salesforce CLI")
   - Execute `sfdx force:source:deploy -c` para verificar antes de deploy

Este guia cobre a configuração básica para começar a desenvolver para Salesforce no VS Code. Consulte a [documentação oficial](https://developer.salesforce.com/tools/vscode) para mais detalhes.
