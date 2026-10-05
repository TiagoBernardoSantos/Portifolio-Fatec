# 🚀 API 3º Semestre - 2025/1

🎓 **Parceiro Acadêmico:** FATEC São José dos Campos - Prof. Jessen Vidal

🔗 **Repositórios:** [API-3SEM](https://github.com/DenariusData/API-3SEM) (principal) · [Backend](https://github.com/DenariusData/DenariusData-Back) · [Frontend](https://github.com/DenariusData/DenariusData-Front) · [Docs](https://github.com/DenariusData/DenariusData-docs)

---

## 📌 Resumo do Projeto

> **Altime** — sistema para controle de ponto e gestão de funcionários, com múltiplos perfis de acesso, construído em arquitetura em camadas (Java + Spring Boot no backend e Vue.js no frontend), consumindo uma API REST.

---

## ⚠️ Problema

> Empresas precisam registrar e consultar os pontos de seus funcionários, cadastrar e gerenciar informações de empresas e acompanhar métricas de ponto de forma centralizada, o que exige uma solução web integrada entre banco de dados, backend e frontend.

---

## 💡 Solução

> Construção de uma aplicação web em arquitetura em camadas, com API REST em Spring Boot, banco de dados relacional MySQL/PostgreSQL e frontend em Vue.js + TypeScript, contemplando cadastro e gestão de empresas, registro e consulta de pontos, e um painel visual com métricas.

**Funcionalidades:**
- Cadastro e gestão de empresas
- Registro e consulta de pontos
- Painel visual com métricas

---

## 🛠 Tecnologias Adotadas

**Java + Spring Boot | Vue.js | MySQL | API REST | Arquitetura em camadas**

- **Spring Boot**: construção da API REST, com Controllers, Services e DTOs.
- **Vue.js + TypeScript**: desenvolvimento das telas dinâmicas integradas à API.
- **MySQL / PostgreSQL**: modelagem, consultas, relacionamentos e normalização do banco.
- **Arquitetura em camadas**: organização escalável entre banco, backend e frontend.

---

## 👨‍💻 Contribuições Individuais

Tive participação ativa tanto na parte técnica quanto na organização da estrutura do repositório e documentação do projeto, atuando em backend, frontend, documentação e validação do sistema. Abaixo estão as entregas, cada uma com o código que escrevi e o link do commit correspondente.

### ⚙️ 1. Endpoint de funcionários por empresa (Backend)

Implementei, nas três camadas do Spring Boot (Controller, Service e Repository), o endpoint `GET /api/funcionarios/por-empresa`, que retorna a quantidade de funcionários agrupada por empresa. A contagem é feita no banco com uma consulta JPQL usando `GROUP BY`, e o resultado é convertido em um mapa `empresa → quantidade` para ser consumido pelo painel de métricas.

🔗 Commit: [`40d00ce` — implementation of employee data collection function (ALT-34)](https://github.com/DenariusData/DenariusData-Back/commit/40d00ce7f391fbbf6cc6770a9fc23b9e01505858)

<details>
<summary><b>Código em Java — Repository, Service e Controller</b></summary>

```java
// FuncionarioRepository.java — consulta agregada no banco
public interface FuncionarioRepository extends JpaRepository<Funcionario, Long> {
    @Query("SELECT f.empresa, COUNT(f) FROM Funcionario f GROUP BY f.empresa")
    List<Object[]> countFuncionariosPorEmpresa();
}
```

```java
// FuncionarioService.java — converte o resultado em um mapa empresa → quantidade
public Map<String, Long> getFuncionariosPorEmpresa() {
    List<Object[]> resultados = funcionarioRepository.countFuncionariosPorEmpresa();
    Map<String, Long> mapa = new HashMap<>();

    for (Object[] resultado : resultados) {
        String empresa = (String) resultado[0];
        Long quantidade = (Long) resultado[1];
        mapa.put(empresa, quantidade);
    }

    return mapa;
}
```

```java
// FuncionarioController.java — expõe o endpoint REST
@GetMapping("/por-empresa")
public ResponseEntity<Map<String, Long>> getFuncionariosPorEmpresa() {
    Map<String, Long> dados = funcionarioService.getFuncionariosPorEmpresa();
    return ResponseEntity.ok(dados);
}
```

</details>

### 🖥️ 2. Seleção de cargo no cadastro de funcionário (Frontend)

No formulário de cadastro de funcionário, o cargo era um campo de texto livre, o que permitia gravar o mesmo cargo escrito de formas diferentes. Substituí o campo por uma lista (`USelect`) carregada da API de cargos, mantendo no banco apenas valores que realmente existem.

🔗 Commit: [`e6244fb` — replaced company and position inputs with USelect components (ALT-37)](https://github.com/DenariusData/DenariusData-Front/commit/e6244fbb94bc8d64e945b710ba7098e1b35105f1)

<details>
<summary><b>Código em Vue/TypeScript — busca dos cargos e campo de seleção (funcionario.vue)</b></summary>

```vue
<script setup lang="ts">
const empresas = ref<any[]>([]);
const cargos = ref<any[]>([]);

const fetchCargos = async () => {
  try {
    const response = await axios.get('http://localhost:8080/api/cargos');
    cargos.value = response.data;
  } catch (error) {
    console.error('Erro ao carregar cargos', error);
  }
};

onMounted(() => {
  fetchFuncionarios();
  fetchEmpresas();
  fetchCargos();
});
</script>

<template>
  <UFormGroup label="Cargo">
    <USelect
      v-model="funcionario.cargo"
      :options="cargos.map(c => ({ label: c.nome, value: c.nome }))"
      placeholder="Selecione um cargo"
    />
  </UFormGroup>
</template>
```

</details>

### 📄 3. Padronização dos READMEs

Criei e padronizei os arquivos README do repositório **principal**, do **Backend** e do **Frontend**: descrição do projeto, passo a passo de execução, tabela com todas as rotas da API (método, rota e descrição), tecnologias e links entre os módulos. O objetivo foi facilitar o onboarding de novos membros e atender ao requisito de guia de instalação e documentação da API.

🔗 Commits: [README principal](https://github.com/DenariusData/API-3SEM/commit/dbf7ff4125a0a99b4fe23cc88ae2dec010544393) · [README do Backend](https://github.com/DenariusData/DenariusData-Back/commit/9027101a4720aaaf05dbb4a12201163f3ce6d14d) · [README do Frontend](https://github.com/DenariusData/DenariusData-Front/commit/dcdbde62761e221ab73df554a42c4a8ede5d102a)

📎 Resultado: [README do Backend](https://github.com/DenariusData/DenariusData-Back#readme) · [README do Frontend](https://github.com/DenariusData/DenariusData-Front#readme) · [README principal](https://github.com/DenariusData/API-3SEM#readme)

<details>
<summary><b>Trecho do README do Backend — tabela de rotas da API</b></summary>

```markdown
## :railway_track: Rotas disponíveis

> A documentação completa da API está disponível via Swagger após iniciar o servidor:
> http://localhost:8080/swagger-ui/index.html

| Método | Rota                              | Descrição                              |
|--------|-----------------------------------|----------------------------------------|
| GET    | /api/funcionarios                 | Listar todos os funcionários           |
| GET    | /api/funcionarios/{id}            | Obter funcionário por ID               |
| POST   | /api/funcionarios                 | Criar novo funcionário                 |
| PUT    | /api/funcionarios/{id}            | Atualizar funcionário por ID           |
| DELETE | /api/funcionarios/{id}            | Remover funcionário por ID             |
| GET    | /api/funcionarios/por-empresa     | Listar funcionários por empresa        |
| GET    | /api/empresa                      | Listar todas as empresas               |
| GET    | /api/dashboard/horas-trabalhadas  | Visualizar total de horas trabalhadas  |
| ...    | ...                               | ...                                    |
```

</details>

### 🔍 4. Testes e Validação

- Participei da validação final do sistema, comparando dados cadastrados x retornos da API, fluxos esperados x comportamento real, e interface x persistência no banco.
- Ajudei a identificar inconsistências entre camadas e sugeri correções alinhadas às boas práticas.

### 🤝 5. Decisões Técnicas e Alinhamentos

- Colaborei em discussões sobre a organização do repositório, padronização de commits e branches, e estrutura do backend e frontend.
- Auxiliei a garantir que o projeto permanecesse coeso, documentado e escalável ao longo do semestre.

<!-- TODO (Tiago): se tiver prints da tela de cadastro de funcionário ou do painel,
     salve em projetos/evidencias/3-semestre/ e adicione aqui, por exemplo:

<details>
<summary><b>Tela — Cadastro de funcionário</b></summary>
<br>

![Cadastro de funcionário](evidencias/3-semestre/cadastro-funcionario.png)

</details>
-->

---

## 📚 Aprendizados Efetivos

Neste projeto, aprofundei minha experiência em Spring Boot e boas práticas REST, além de reforçar a importância da documentação técnica (READMEs) para a manutenção e o onboarding de um projeto em equipe.

### 🧠 Hard Skills

> **Escala (Taxonomia de Bloom adaptada):** ★☆☆☆ Ouvi falar (lembrar) → ★★☆☆ Entendi → ★★★☆ Sei fazer com ajuda (aplicar) → ★★★★ Sei fazer com autonomia (aplicar)

| Tecnologia / Método | Nível | Classificação | Onde apliquei |
|---------------------|:-----:|---------------|---------------|
| Java + Spring Boot (API REST) | ★★★☆ | Sei fazer com ajuda | Endpoint de funcionários por empresa (Controller, Service e Repository) |
| Spring Data JPA / JPQL | ★★★☆ | Sei fazer com ajuda | Consulta agregada com `COUNT` e `GROUP BY` |
| SQL (MySQL) | ★★★☆ | Sei fazer com ajuda | Validação dos dados persistidos x retornos da API |
| Nuxt / Vue.js + TypeScript | ★★★☆ | Sei fazer com ajuda | Formulário de cadastro de funcionário integrado à API |
| Consumo de API REST (Axios) | ★★★☆ | Sei fazer com ajuda | Busca dos cargos na API para o campo de seleção |
| Documentação técnica (Markdown) | ★★★★ | Sei fazer com autonomia | READMEs do repositório principal, Backend e Frontend |
| Git/GitHub | ★★★★ | Sei fazer com autonomia | Commits semânticos vinculados às tarefas do Jira (`ALT-33`, `ALT-34`, `ALT-37`) |
| Swagger | ★★☆☆ | Entendi | Consulta e teste dos endpoints durante a validação |
| Arquitetura em camadas | ★★☆☆ | Entendi | Separação entre Controller, Service, Repository e frontend |

### 🤝 Soft Skills

| Competência | Situação no projeto | O que fiz |
|-------------|---------------------|-----------|
| Comunicação | O projeto estava dividido em repositórios separados (principal, backend, frontend e docs) e cada um era documentado de um jeito | Padronizei os READMEs com a mesma estrutura e links entre os módulos, para qualquer pessoa conseguir rodar o projeto |
| Atenção ao Detalhe | Na validação final, era preciso garantir que tela, API e banco mostravam a mesma informação | Comparei os dados cadastrados com os retornos da API e apontei as inconsistências para correção |
| Resolução de Problemas | O cargo do funcionário era digitado livremente, gerando valores diferentes para o mesmo cargo | Troquei o campo por uma lista carregada da API de cargos |
| Trabalho em Equipe | Atuei em mais de uma frente (backend, frontend e documentação) conforme a necessidade de cada sprint | Assumi tarefas de frentes diferentes e segui o padrão de commits e branches combinado com o time |
| Organização | Cada tarefa precisava ser rastreável entre o Jira e o código | Identifiquei os commits com o código da tarefa correspondente |

---

## 🔎 Navegação entre Projetos

- [1º Semestre: Calculadora Científica](API-1-semestre.md)
- [2º Semestre: PACER+ — Avaliador de Competências](API-2-semestre.md)
- **3º Semestre:** Altime — Controle de Ponto e Gestão de Funcionários
- [4º Semestre: Radarius — Monitoramento e Alerta de Tráfego](API-4-semestre.md)
- [5º Semestre: Nexus — Plataforma Analítica de Dados de Projetos](API-5-semestre.md)

[⬅️ Voltar ao Portfólio](../README.md)
