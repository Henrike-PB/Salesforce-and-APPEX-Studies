---
title: Conceitos Salesforce
parent: Salesforce & APPEX Studies
nav_order: 2
---


# Conceitos Principais do Salesforce

Este documento apresenta os principais conceitos utilizados na plataforma Salesforce, com foco em desenvolvimento, automação e melhores práticas.

---

## ⚙️ Plataforma Salesforce

Salesforce é uma plataforma de CRM (Customer Relationship Management) baseada em nuvem que permite a criação de aplicações customizadas com pouco ou nenhum código.

- **Cloud-Based:** Funciona 100% na nuvem.
- **Multitenant:** Vários clientes compartilham a mesma infraestrutura.
- **Metadata-Driven:** Toda a customização é baseada em metadados.
- **Atualizações Trimestrais:** Três grandes atualizações por ano.

---

## 🧩 Estrutura de Objetos

- **Objeto Padrão:** Fornecidos pela Salesforce (ex: Account, Contact).
- **Objeto Personalizado:** Criados pelo usuário/admin (ex: Project__c).
- **Campo:** Unidade de dado em um objeto (ex: Nome, Status__c).
- **Relacionamentos:** Lookup ou Master-Detail entre objetos.

---

## 🧠 Apex (Linguagem)

Apex é uma linguagem de programação orientada a objetos, fortemente tipada, semelhante ao Java, usada para executar lógica personalizada no Salesforce.

### Principais Elementos:

- `@AuraEnabled`: Exponibiliza métodos para LWC/Aura.
- `with sharing`: Respeita as permissões de compartilhamento.
- `Database.query()`: Executa consultas dinâmicas.
- `SOQL`: Linguagem de consulta de objetos.

---

## ⚡ Lightning Web Components (LWC)

Framework moderno baseado em Web Standards, usado para criar interfaces dinâmicas no Salesforce.

### Estrutura:

- `.html`: Estrutura visual.
- `.js`: Lógica e comunicação com Apex.
- `.xml`: Metadata e visibilidade.

### Recursos:

- `@track`: Reatividade de variáveis.
- `connectedCallback()`: Disparado ao carregar o componente.
- `get` e `set`: Acessores personalizados para propriedades.

---

## 🛡️ Segurança

- **CRUD (Create, Read, Update, Delete):** Permissões por objeto.
- **FLS (Field-Level Security):** Permissões por campo.
- **Sharing Rules:** Regras de compartilhamento entre usuários.
- **with sharing**: Define se o Apex respeita as regras de acesso.

---

## 🧪 Testes e Deploy

- **Classe de Teste:** Necessária para deploy em produção.
- **Cobertura mínima:** 75% das linhas de código Apex.
- **SFDX (Salesforce DX):** Ferramenta CLI para automação, versionamento e deploy.

---

## 🔁 Automação Declarativa

- **Flows:** Lógica visual com ramificações e condições.
- **Validation Rules:** Impede dados inválidos.
- **Process Builder (legado):** Automatiza ações com base em critérios.
- **Approval Processes:** Fluxo de aprovação hierárquica.

---

## 📦 Componentização e Reuso

- **Componentes Reutilizáveis:** LWC com responsabilidade única.
- **Modularização em Apex:** Classes pequenas, coesas.
- **Cacheable Apex:** Melhora a performance em chamadas de leitura.

---

## 🧭 Glossário Rápido

| Termo                  | Definição |
|------------------------|----------|
| Apex                  | Linguagem backend no Salesforce |
| LWC                   | Lightning Web Components |
| SOQL                  | Salesforce Object Query Language |
| FLS                   | Field-Level Security |
| SFDX                  | Salesforce Developer Experience |
| Metadata              | Configuração descritiva dos objetos e componentes |
| with sharing          | Respeita o modelo de segurança |
| AuraEnabled           | Torna método acessível ao frontend |

---

Este guia é um resumo inicial. Recomenda-se consultar a [Documentação Oficial Salesforce](https://developer.salesforce.com/docs) para aprofundamento.
