# 📘 Comparativo: Interação com Step Functions via Console AWS vs Grafana

---

## 🎯 Objetivo

Esta página tem como objetivo comparar os recursos disponíveis para interação, análise e monitoramento das execuções da AWS Step Functions entre dois cenários:

1. **Com acesso ao Console da AWS**
2. **Sem acesso ao Console, utilizando o Grafana como alternativa para observabilidade**

Este comparativo busca elencar **funcionalidades equivalentes**, **limitações** e **lacunas** que podem impactar a operação e o troubleshooting de workflows serverless.

---

## 🧭 Cenários Avaliados

| Cenário                                            | Descrição                                                                                                                                                           |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Acesso via Console AWS**                         | Acesso completo à interface gráfica do AWS Step Functions, permitindo navegação interativa nas execuções, visualização de payloads, estados e logs correlacionados. |
| **Acesso via Grafana (CloudWatch Logs / Metrics)** | Observabilidade indireta através de painéis e dashboards personalizados que consomem logs e métricas exportadas pelo serviço Step Functions via CloudWatch.         |

---

## 🔁 Funcionalidades Comparadas

| Funcionalidade                                     | Console AWS           | Grafana                | Observações                                                                               |
| -------------------------------------------------- | --------------------- | ---------------------- | ----------------------------------------------------------------------------------------- |
| Visualização de Execuções (diagrama visual)        | ✅                     | ❌                      | Não é possível visualizar o gráfico de estados e transições no Grafana                    |
| Filtro por `executionArn` / `businessKey`          | ✅                     | ✅ (via query nos logs) | Requer parse customizado no Log Insights ou painel específico no Grafana                  |
| Visualizar Payload de Input/Output                 | ✅                     | ⚠️ (limitado a logs)   | É necessário que o log contenha os payloads explicitamente                                |
| Start manual de execução                           | ✅                     | ❌                      | Execução só pode ser iniciada via API/SDK                                                 |
| Stop manual de execução                            | ✅                     | ❌                      | Não é possível encerrar uma execução via Grafana                                          |
| Retry manual de execução                           | ✅                     | ❌                      | Só possível via SDK/API ou console                                                        |
| Visualização de falhas com detalhes (error, cause) | ✅                     | ✅ (via logs parseados) | Grafana requer customização no parsing do log                                             |
| Métricas agregadas (sucesso, erro, duração)        | ✅                     | ✅                      | Métricas disponíveis via CloudWatch Metrics                                               |
| Drill-down por estado                              | ✅                     | ⚠️ (limitado)          | Em Grafana, é necessário extrair manualmente o estado dos logs para esse nível de análise |
| Exportar logs                                      | ⚠️ (parcial)          | ✅                      | Grafana pode facilitar exportação dos logs em formatos estruturados                       |
| Atribuição de tags e busca por tags                | ✅                     | ❌                      | Não suportado via Grafana                                                                 |
| Criação de alertas                                 | ❌ (direto na console) | ✅                      | Grafana permite alertas com base em métricas e logs                                       |

---

## 🔎 O que conseguimos fazer com o Grafana

* Consultar métricas agregadas (execuções por status, tempo médio, etc.)
* Visualizar logs de falhas com parsing estruturado (ex: erro por `businessKey`)
* Construir dashboards com visualização temporal
* Criar alertas (ex: aumento de falhas, execuções longas)
* Usar queries no Log Insights (CloudWatch) embutidas no Grafana para análise

---

## ❌ O que perdemos sem o Console

* Visualização **gráfica e interativa** do fluxo de estados
* Facilidade para **startar, parar ou reexecutar** execuções manualmente
* Visualização direta do **input/output por estado**
* Acompanhamento em tempo real e detalhamento por **breadcrumbs**
* Acesso simplificado aos metadados da execução (tempo de início, fim, status de cada estado)
* **Depuração rápida** de execuções complexas com múltiplos subprocessos

---

## 🧩 Considerações Técnicas

* É **obrigatório que os estados do Step Functions estejam configurados para logar dados no CloudWatch Logs**, com níveis de detalhamento suficientes (ex: `ALL`, `ERROR`).
* A integração com Grafana depende do acesso ao CloudWatch e permissões específicas (IAM) para leitura de métricas e logs.
* Para minimizar perdas, recomenda-se:

  * Adicionar `executionArn`, `stateName`, `error`, `cause`, `input`, `output` nos logs de cada execução
  * Criar painéis específicos para observação de execuções por chave de negócio (`businessKey`)
  * Configurar **dashboards temáticos por máquina de estado**

---

## 📌 Conclusão

O Grafana pode **suprir parcialmente** a ausência do Console da AWS para propósitos de **observabilidade**, especialmente com foco em **métricas, logs e alertas**. No entanto, **operações manuais e depuração interativa** ainda são significativamente prejudicadas sem o console.

Para ambientes restritos, é recomendável:

* Criar uma camada de automação (ex: via Lambda ou SDK) para permitir operações como stop/retry
* Documentar fluxos com artefatos visuais (mermaid, draw\.io) para facilitar troubleshooting

---

Se desejar, posso converter esse conteúdo em formato Markdown (.md) ou gerar diretamente para o Confluence com macros (`{expand}`, `{info}`, etc.). Deseja essa versão também?
