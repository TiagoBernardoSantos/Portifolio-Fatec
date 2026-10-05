# 🚀 API 4º Semestre - 2025/2

🎓 **Parceiro Acadêmico:** FATEC São José dos Campos - Prof. Jessen Vidal (projeto com cliente real: Prefeitura de São José dos Campos)

🔗 **Repositórios:** [API-4SEM](https://github.com/DenariusData/API-4SEM) (principal) · [Frontend](https://github.com/DenariusData/API-4SEM-FRONTEND) · [Backend](https://github.com/DenariusData/API-4SEM-BACKEND)

---

## 📌 Resumo do Projeto

> **Radarius** — Sistema Inteligente de Monitoramento e Alerta de Tráfego para a cidade de São José dos Campos — solução que centraliza o controle do trânsito a partir de dados de radares, emitindo alertas automáticos e facilitando a alocação de agentes de mobilidade urbana.

---

## ⚠️ Problema

> A Prefeitura precisava de uma forma centralizada de monitorar o trânsito da cidade a partir dos dados de radares, identificar rapidamente situações de congestionamento por via/zona e acionar os agentes de mobilidade urbana responsáveis de forma automática, ao invés de um processo manual e disperso.

---

## 💡 Solução

> Desenvolvimento de uma aplicação web (Java + Spring Boot no backend, Vue.js no frontend, Oracle como banco de dados) com dashboard interativo de indicadores, mapa com zonas de congestionamento, cadastro de indicadores com severidade customizável, disparo automático de alertas via WhatsApp, designação de agentes por zona e múltiplos perfis de acesso (público, agente, gestor e admin), com logs de auditoria dos alertas gerados.

**Funcionalidades:**
- Dashboard interativo com gráficos e tabelas de indicadores de tráfego
- Mapa interativo com divisões de zonas da cidade e status de congestionamento por via
- Cadastro e gerenciamento de indicadores com níveis de severidade customizáveis
- Disparo automático de alertas via WhatsApp para agentes e gestores
- Designação de agentes por zona e protocolos de atendimento de alertas
- Múltiplos perfis de acesso: público, agente, gestor e admin
- Logs de auditoria dos alertas gerados

---

## 🛠 Tecnologias Adotadas

**Java + Spring Boot | Vue.js | Oracle | Docker | Swagger | Figma | Jira | Slack**

- **Spring Boot**: backend e regras de negócio da aplicação.
- **Vue.js 3 + TypeScript + Vuetify**: construção das páginas, componentes e navegação do frontend.
- **Chart.js**: gráficos dos dashboards de tráfego.
- **Oracle**: banco de dados relacional do sistema.
- **Docker**: containerização do ambiente de desenvolvimento.
- **Swagger**: documentação e teste dos endpoints da API REST.
- **Figma**: prototipação de telas antes da implementação.
- **Jira / Slack**: gestão de sprints e comunicação do time.

---

## 👨‍💻 Contribuições Individuais

Atuei como **Dev Team**, com foco principal no desenvolvimento frontend (Vue.js) e na integração com o backend, sendo responsável por boa parte da construção do dashboard de indicadores de tráfego. Abaixo estão as entregas, cada uma com o código que escrevi e o link do commit correspondente.

🎥 **Sistema em funcionamento (vídeos do projeto):** [usuário público](https://drive.google.com/file/d/1G6b-caz4GOOALUhfYMFFiktgj53RN-BM/view?usp=sharing) · [agente](https://drive.google.com/file/d/12RU3fXxbnhlbY8p932MR_5re0Mrc_VQK/view?usp=sharing) · [gestor](https://drive.google.com/file/d/1vHfJ08QC7UmmriwL5C7PuEZcnNhNgzLA/view?usp=sharing) · [admin](https://drive.google.com/file/d/1J-53EJ_zDeAMko_-KFkLkkIGlr-jld9O/view?usp=sharing)

### 🧭 1. Rotas e navegação do menu

Configurei o roteamento das opções de menu (alertas, dashboards e indicadores) com carregamento sob demanda das telas (*lazy loading*), criando a estrutura inicial de cada módulo.

🔗 Commit: [`9ae4383` — setup routing for menu options and interface](https://github.com/DenariusData/API-4SEM-FRONTEND/commit/9ae4383a66b9a728f3ec6e62a2f7cd9b6e28954b)

<details>
<summary><b>Código em TypeScript/Vue — rotas e navegação (router/index.ts e App.vue)</b></summary>

```typescript
// src/router/index.ts
{
  path: '/alerts',
  name: 'alerts',
  component: () => import('@/modules/alerts/AlertsView.vue'),
},
{
  path: '/dashboards',
  name: 'dashboards',
  component: () => import('@/modules/dashboards/DashboardsView.vue')
},
{
  path: '/indicators',
  name: 'indicators',
  component: () => import('@/modules/indicators/IndicatorsView.vue')
},
```

```vue
<!-- src/App.vue -->
<script setup lang="ts">
const goTo = (routeName: string) => {
  router.push({ name: routeName })
}
</script>

<v-list>
  <v-list-item title="Home" @click="goTo('home')" />
  <v-list-item title="Critérios" @click="goTo('criterias')" />
  <v-list-item title="Alertas" @click="goTo('alerts')" />
  <v-list-item title="Dashboards" @click="goTo('dashboards')" />
  <v-list-item title="Indicadores" @click="goTo('indicators')" />
</v-list>
```

</details>

### 🖥️ 2. Dashboard de indicadores de tráfego

Criei a página de dashboard com os indicadores de tráfego e, em seguida, refatorei a tela separando tipos e responsabilidades para facilitar a manutenção. Depois evoluí o dashboard para um modal aberto a partir do mapa, com carrossel de zonas, gráfico de barras dos principais corredores e gráfico de linha por hora.

🔗 Commits: [`69a675f` — create dashboard page with indicators](https://github.com/DenariusData/API-4SEM-FRONTEND/commit/69a675f027dcfe5d18c29fa460c8c80e6f9bc4b0) · [`b2d0df0` — componentize dashboards](https://github.com/DenariusData/API-4SEM-FRONTEND/commit/b2d0df09af5bcc382cfcc3d7ab17c750efe1b62c) · [`e2a5e9b` — dashboard modal and map filters dropdown](https://github.com/DenariusData/API-4SEM-FRONTEND/commit/e2a5e9bdcee2e02b36e0e290bf0f1a4782b884a3)

<details>
<summary><b>Código em TypeScript — carrossel de zonas e gráfico dos corredores (DashboardsPopup.vue)</b></summary>

```typescript
// Navega para a próxima zona disponível, pulando as que foram
// removidas pelo filtro do mapa
const navigateZone = (direction: number) => {
  let idx = (currentZoneIndex.value + direction + zonesData.value.length) % zonesData.value.length
  while (!isZoneAvailable(zonesData.value[idx].zone) && idx !== currentZoneIndex.value) {
    idx = (idx + direction + zonesData.value.length) % zonesData.value.length
  }
  if (isZoneAvailable(zonesData.value[idx].zone)) {
    currentZoneIndex.value = idx
    updateCurrentZoneData()
  }
}

const updateCurrentZoneData = () => {
  corridorData.value = zonesData.value[currentZoneIndex.value].corridors
  createBarChart()
}

const createBarChart = () => {
  if (!barChartRef.value) return
  if (barChartInstance) barChartInstance.destroy()

  const data = corridorData.value.map(c => c.vehicles)
  const labels = corridorData.value.map(c => c.name.replace(/\s/g, '\n'))
  const maxValue = Math.max(...data, 1500)
  const chartMax = Math.ceil(maxValue * 1.2 / 500) * 500

  barChartInstance = new Chart(barChartRef.value, {
    type: 'bar',
    data: { labels, datasets: [{ label: 'Veículos/dia', data, backgroundColor: '#00c853', borderColor: '#00963e', borderWidth: 1, borderRadius: 4 }] },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: { legend: { display: false }, title: { display: false } },
      scales: {
        y: { beginAtZero: true, max: chartMax, ticks: { stepSize: 500 } },
        x: { ticks: { maxRotation: 0 }, grid: { display: false } }
      }
    }
  })
}
```

</details>

### 📊 3. Comparação entre zonas

Adicionei o botão de comparação, que abre um modal onde o gestor seleciona as zonas da cidade e vê, no mesmo gráfico, o volume de veículos dos principais corredores de cada uma.

🔗 Commit: [`33eb4d8` — add comparison button, zones carousel and improve line chart UI](https://github.com/DenariusData/API-4SEM-FRONTEND/commit/33eb4d8fa53b1f2629bd0a73c735e88fc8ccfee1)

<details>
<summary><b>Código em TypeScript — gráfico comparativo entre zonas (ComparisonZones.vue)</b></summary>

```typescript
const props = defineProps<{
  zonesData: ZoneData[]
  filteredZones: string[]
}>()

const selectedZonesForComparison = ref<string[]>([])

function toggleZoneSelection(zone: string) {
  if (!isZoneAvailable(zone)) return

  const index = selectedZonesForComparison.value.indexOf(zone)
  if (index > -1) {
    selectedZonesForComparison.value.splice(index, 1)
  } else {
    selectedZonesForComparison.value.push(zone)
  }
}

const createComparisonChart = () => {
  if (comparisonChartRef.value && selectedZonesForComparison.value.length > 0) {
    if (comparisonChartInstance) comparisonChartInstance.destroy()

    const selectedZones = props.zonesData.filter(z =>
      selectedZonesForComparison.value.includes(z.zone)
    )

    // um conjunto de barras (dataset) por zona selecionada
    const datasets = selectedZones.map((zone, index) => {
      const colors = ['#00c853', '#2196f3', '#ff9800', '#e91e63', '#9c27b0', '#00bcd4']
      return {
        label: `Zona ${zone.zone}`,
        data: zone.corridors.map(c => c.vehicles),
        backgroundColor: colors[index % colors.length],
        borderColor: colors[index % colors.length],
        borderWidth: 1,
        borderRadius: 4
      }
    })

    const maxCorridors = Math.max(...selectedZones.map(z => z.corridors.length))
    const labels = Array.from({ length: maxCorridors }, (_, i) => `Corredor ${i + 1}`)

    comparisonChartInstance = new Chart(comparisonChartRef.value, {
      type: 'bar',
      data: { labels, datasets },
      // ...
    })
  }
}
```

</details>

### 🔗 4. Integração Frontend/Backend

Substituí os dados fixos do dashboard pelos dados reais da API: criei a camada de serviço com os endpoints de métricas, tipei os retornos (DTOs) e conectei os filtros de período e de zona do mapa aos gráficos. As seis zonas são consultadas em paralelo e, se uma delas falhar, as demais continuam sendo exibidas.

🔗 Commit: [`b18b9fe` — front-end and back-end integration for filters and dashboards](https://github.com/DenariusData/API-4SEM-FRONTEND/commit/b18b9fef249e38e9a88b6bdd5102febcea6de4bb)

<details>
<summary><b>Código em TypeScript — camada de serviço dos dashboards (dashboardService.ts)</b></summary>

```typescript
import api from '@/utils/servicesUtils'
import type {
  HourlyVehiclesDTO,
  RoadDailyAggregateDTO,
  VehiclesPerHourParams,
  RoadsDailyParams
} from '../types/dashboardsTypes'

export async function getVehiclesPerHourForRoad(params: VehiclesPerHourParams) {
  const { regionId, roadId, start, end } = params
  return await api.get<HourlyVehiclesDTO[]>(
    `/v1/metrics/region/${regionId}/road/${roadId}/hourly`,
    { params: { start, end } }
  )
}

export async function getRoadsDailyAggregate(params: RoadsDailyParams) {
  const { regionId, date, start, end } = params

  // a API aceita uma data única OU um intervalo
  const queryParams: Record<string, string> = {}

  if (date) {
    queryParams.date = date
  } else if (start) {
    queryParams.start = start
    if (end) {
      queryParams.end = end
    }
  }

  return await api.get<RoadDailyAggregateDTO[]>(
    `/v1/metrics/region/${regionId}/roads/daily`,
    { params: queryParams }
  )
}

// busca os dados horários de várias vias ao mesmo tempo
export async function getMultipleRoadsHourlyData(
  regionId: number,
  roadIds: number[],
  start: string,
  end: string
) {
  const promises = roadIds.map(roadId =>
    getVehiclesPerHourForRoad({ regionId, roadId, start, end })
  )

  return await Promise.all(promises)
}
```

</details>

<details>
<summary><b>Código em TypeScript — carga dos dados das zonas a partir da API (DashboardsPopup.vue)</b></summary>

```typescript
async function loadZonesData() {
  errorMessage.value = ''
  try {
    const { start, end } = getDateRange()
    const promises = zonesData.value.map(async (zone) => {
      try {
        const response = start === end
          ? await getAllRoadsDataForDate(zone.regionId, start)
          : await getAllRoadsDataForRange(zone.regionId, start, end)

        let dataArray: any[] = []
        if (Array.isArray(response.data)) dataArray = response.data
        else if (response.data?.data) dataArray = response.data.data
        else if (response.data?.roads) dataArray = response.data.roads

        if (!dataArray.length) return { ...zone, corridors: [] }

        // os 3 corredores com mais veículos de cada zona
        const corridors: Corridor[] = dataArray.map(r => ({
          id: r.roadId,
          name: r.roadName || `Via ${r.roadId}`,
          vehicles: r.totalCount || 0,
          speed: r.hours?.length ? r.hours.reduce((s, h) => s + (h.avgSpeedKmh || 0), 0) / r.hours.length : 0
        })).sort((a, b) => b.vehicles - a.vehicles).slice(0, 3)

        return { ...zone, corridors }
      } catch { return { ...zone, corridors: [] } }   // uma zona com erro não derruba as outras
    })

    zonesData.value = await Promise.all(promises)
    if (!zonesData.value.some(z => z.corridors.length)) {
      errorMessage.value = `Nenhum dado encontrado para o período de ${start} a ${end}. Usando dados de exemplo.`
      loadMockData()
    } else updateCurrentZoneData()
  } catch {
    errorMessage.value = 'Erro ao carregar dados. Usando dados de exemplo.'
    loadMockData()
  }
}
```

</details>

<!-- TODO (Tiago): adicionar aqui prints ou GIFs do dashboard e do modal de comparação.
     Salve as imagens em projetos/evidencias/4-semestre/ e use, por exemplo:

<details>
<summary><b>Tela — Dashboard de indicadores</b></summary>
<br>

![Dashboard de indicadores](evidencias/4-semestre/dashboard.png)

</details>
-->

---

## 📚 Aprendizados Efetivos

Este foi meu projeto com maior complexidade de frontend até então, com múltiplos perfis de acesso e integração intensa com a API. Trabalhar em um projeto com cliente real (Prefeitura de SJC) trouxe uma dimensão extra de responsabilidade com prazos e qualidade de entrega.

### 🧠 Hard Skills

> **Escala (Taxonomia de Bloom adaptada):** ★☆☆☆ Ouvi falar (lembrar) → ★★☆☆ Entendi → ★★★☆ Sei fazer com ajuda (aplicar) → ★★★★ Sei fazer com autonomia (aplicar)

| Tecnologia / Método | Nível | Classificação | Onde apliquei |
|---------------------|:-----:|---------------|---------------|
| Vue.js 3 (Composition API) | ★★★★ | Sei fazer com autonomia | Páginas, componentes, props/emits e modais do dashboard |
| TypeScript | ★★★★ | Sei fazer com autonomia | Tipagem dos componentes e dos DTOs retornados pela API |
| Chart.js | ★★★★ | Sei fazer com autonomia | Gráficos de barras, linha e comparação entre zonas |
| Vue Router | ★★★★ | Sei fazer com autonomia | Rotas do menu com *lazy loading* |
| Consumo de API REST (Axios) | ★★★☆ | Sei fazer com ajuda | Camada de serviço e integração dos filtros com o backend |
| Vuetify | ★★★☆ | Sei fazer com ajuda | Componentes visuais do menu e dos filtros |
| Git/GitHub | ★★★★ | Sei fazer com autonomia | Branches por tarefa, commits semânticos e resolução de conflitos de merge |
| Figma | ★★☆☆ | Entendi | Consulta ao protótipo para implementar as telas |
| Docker | ★★☆☆ | Entendi | Subir o ambiente do projeto para desenvolver e testar |
| Spring Boot / Oracle | ★★☆☆ | Entendi | Leitura dos endpoints e do modelo para integrar o frontend (não desenvolvi o backend neste semestre) |

### 🤝 Soft Skills

| Competência | Situação no projeto | O que fiz |
|-------------|---------------------|-----------|
| Visão Sistêmica | O produto tinha quatro perfis (público, agente, gestor e admin) e o dashboard precisava conversar com o mapa e com os filtros | Estruturei rotas e componentes pensando no fluxo completo, ligando os filtros do mapa aos gráficos |
| Responsabilidade | Projeto com cliente real (Prefeitura de SJC) e entregas apresentadas a cada sprint | Entreguei primeiro a interface do dashboard com dados de exemplo, para validação, e em seguida a integração com os dados reais |
| Resolução de Problemas | A integração gerou conflitos de merge e diferenças entre o formato esperado e o retornado pela API | Resolvi os conflitos e tratei os diferentes formatos de resposta e os casos sem dados, exibindo aviso ao usuário |
| Trabalho em Equipe | O dashboard dependia dos endpoints de métricas desenvolvidos por outros membros | Alinhei com o backend os parâmetros e retornos de cada endpoint e os registrei como tipos no frontend |
| Adaptabilidade | O escopo mudou durante o projeto, com histórias novas e outras removidas | Reorganizei o dashboard (de página própria para modal aberto a partir do mapa) para acompanhar a mudança |

---

## 🔎 Navegação entre Projetos

- [1º Semestre: Calculadora Científica](API-1-semestre.md)
- [2º Semestre: PACER+ — Avaliador de Competências](API-2-semestre.md)
- [3º Semestre: Altime — Controle de Ponto e Gestão de Funcionários](API-3-semestre.md)
- **4º Semestre:** Radarius — Monitoramento e Alerta de Tráfego
- [5º Semestre: Nexus — Plataforma Analítica de Dados de Projetos](API-5-semestre.md)

[⬅️ Voltar ao Portfólio](../README.md)
