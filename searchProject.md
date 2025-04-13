# Documentação do Projeto Projeto Search

## Visão Geral
Este projeto implementa uma solução no Salesforce que permite aos usuários pesquisarem projetos personalizados (`Project__c`) com filtros avançados, visualizar rapidamente os ciclos vinculados e acessar rapidamente os projetos que visualizaram recentemente. Ele utiliza Lightning Web Components (LWC), Apex Classes e um objeto customizado (`Recent_Viewed_Project__c`) para controlar o histórico.

---

## Componentes

### 1. Apex Class: ProjectController.cls
```apex
public with sharing class ProjectController {
    @AuraEnabled(cacheable=true)
    public static List<Project__c> getProjects(String projectName, String responsible, String status, String startDate) {
        String query = 'SELECT Id, Name, Data_de_inicio__c, Data_de_termino__c, Responsavel__c, ' +
                       '(SELECT Id, Status__c FROM Cicles__r) FROM Project__c';

        List<String> conditions = new List<String>();

        if (String.isNotBlank(projectName)) {
            conditions.add('Name LIKE '%' + String.escapeSingleQuotes(projectName) + '%'');
        }
        if (String.isNotBlank(responsible)) {
            conditions.add('Responsavel__c LIKE '%' + String.escapeSingleQuotes(responsible) + '%'');
        }
        if (String.isNotBlank(startDate)) {
            conditions.add('Data_de_inicio__c >= ' + startDate);
        }

        if (!conditions.isEmpty()) {
            query += ' WHERE ' + String.join(conditions, ' AND ');
        }

        List<Project__c> projects = Database.query(query);

        if (String.isNotBlank(status)) {
            projects = filterByCycleStatus(projects, status);
        }

        return projects;
    }

    private static List<Project__c> filterByCycleStatus(List<Project__c> projects, String status) {
        List<Project__c> filteredProjects = new List<Project__c>();
        for (Project__c project : projects) {
            for (Cicle__c ciclo : project.Cicles__r) {
                if (ciclo.Status__c == status) {
                    filteredProjects.add(project);
                    break;
                }
            }
        }
        return filteredProjects;
    }
}
```

**Explicação:**
- **getProjects:** Responsável por montar e executar a query dinamicamente com base nos filtros.
- **filterByCycleStatus:** Realiza o filtro em memória dos projetos baseado no status dos ciclos.

### 2. Apex Class: RecentProjectController.cls
```apex
public with sharing class RecentProjectController {

    @AuraEnabled
    public static void saveRecentProject(Id projectId) {
        Recent_Viewed_Project__c view = new Recent_Viewed_Project__c(
            Project__c = projectId,
            User__c = UserInfo.getUserId(),
            Viewed_Date__c = System.now()
        );
        insert view;
    }

    @AuraEnabled(cacheable=true)
    public static List<Project__c> getRecentProjects() {
        List<Recent_Viewed_Project__c> recents = [
            SELECT Project__c, Project__r.Name, Project__r.Responsavel__c, Project__r.Data_de_inicio__c
            FROM Recent_Viewed_Project__c
            WHERE User__c = :UserInfo.getUserId()
            ORDER BY Viewed_Date__c DESC
            LIMIT 5
        ];

        List<Project__c> projects = new List<Project__c>();
        for (Recent_Viewed_Project__c r : recents) {
            projects.add(r.Project__r);
        }
        return projects;
    }
}
```

**Explicação:**
- `saveRecentProject`: Salva um registro de visualização associando o projeto ao usuário logado.
- `getRecentProjects`: Recupera os 5 últimos projetos visualizados pelo usuário.

### 3. LWC: projetoSearch
**Arquivos Associados:** HTML, JS, Meta XML

#### `projetoSearch.html`
```html
<template>
  <lightning-card title="Busca de Projetos">
    <div class="slds-p-around_medium">
      <lightning-input label="Nome do Projeto" value={projectName} onchange={handleInputChange} data-id="name"></lightning-input>
      <lightning-input label="Responsável" value={responsible} onchange={handleInputChange} data-id="responsible"></lightning-input>
      <lightning-input label="Status do Ciclo" value={status} onchange={handleInputChange} data-id="status"></lightning-input>
      <lightning-input type="date" label="Data de Início" value={startDate} onchange={handleInputChange} data-id="startDate"></lightning-input>
      <lightning-button label="Buscar" onclick={handleSearch} class="slds-m-top_medium"></lightning-button>
    </div>

    <template if:true={projects}>
      <div class="slds-box slds-m-around_medium">
        <template for:each={projects} for:item="proj">
          <p key={proj.Id} class="slds-p-vertical_x-small">
            <a href="#" onclick={navigateToProject} data-id={proj.Id}>{proj.Name}</a>
          </p>
        </template>
      </div>
    </template>
  </lightning-card>

  <lightning-card title="Projetos Recentemente Visualizados">
    <template if:true={recentProjects}>
      <div class="slds-box slds-m-around_medium">
        <template for:each={recentProjects} for:item="recent">
          <p key={recent.Id} class="slds-p-vertical_x-small">
            <a href="#" onclick={navigateToProject} data-id={recent.Id}>{recent.Name}</a>
          </p>
        </template>
      </div>
    </template>
  </lightning-card>
</template>
```

#### `projetoSearch.js`
```javascript
import { LightningElement, track } from 'lwc';
import getProjects from '@salesforce/apex/ProjectController.getProjects';
import saveRecentProject from '@salesforce/apex/RecentProjectController.saveRecentProject';
import getRecentProjects from '@salesforce/apex/RecentProjectController.getRecentProjects';

export default class ProjetoSearch extends LightningElement {
  @track projectName = '';
  @track responsible = '';
  @track status = '';
  @track startDate = '';
  @track projects = [];
  @track recentProjects = [];

  connectedCallback() {
    this.loadRecentProjects();
  }

  handleInputChange(event) {
    const field = event.target.dataset.id;
    this[field] = event.target.value;
  }

  handleSearch() {
    getProjects({ projectName: this.projectName, responsible: this.responsible, status: this.status, startDate: this.startDate })
      .then(result => {
        this.projects = result;
      });
  }

  navigateToProject(event) {
    const projectId = event.target.dataset.id;
    saveRecentProject({ projectId })
      .then(() => {
        window.open(`/lightning/r/Project__c/${projectId}/view`, '_blank');
        this.loadRecentProjects();
      });
  }

  loadRecentProjects() {
    getRecentProjects()
      .then(result => {
        this.recentProjects = result;
      });
  }
}
```

#### `projetoSearch.js-meta.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>63.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightning__AppPage</target>
        <target>lightning__RecordPage</target>
        <target>lightning__HomePage</target>
    </targets>
</LightningComponentBundle>
```

---

## Glossário de Termos Técnicos
- **@AuraEnabled**: Permite que métodos Apex sejam acessados via LWC.
- **track**: Usado para reatividade no LWC.
- **LWC**: Lightning Web Components.
- **SOQL**: Salesforce Object Query Language.
- **Metadata XML**: Arquivo de configuração que define onde e como o LWC será exposto.
- **UserInfo**: Classe Apex usada para acessar dados do usuário atual.

## Fluxo de Trabalho
```ascii
[Usuário] => [LWC projetoSearch] => [Apex getProjects()] => [Project__c]
                                          ↓
                                  [click projeto]
                                          ↓
                              [Apex saveRecentProject()]
                                          ↓
                        [Recent_Viewed_Project__c - salva visualização]
                                          ↓
                        [getRecentProjects()] => [LWC exibe últimos 5 projetos]
```

## Conceitos Aplicados
- **Segurança (with sharing)**: Garante respeito às permissões de acesso do usuário.
- **Bulkificação**: Consultas eficientes e uso de filtros dinâmicos.
- **Reutilização**: Componentes LWC com separação clara de responsabilidade.
- **Cacheable Apex**: Para melhorar performance em chamadas que não modificam dados.

---

