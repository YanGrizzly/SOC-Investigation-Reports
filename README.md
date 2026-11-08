
# SOC Investigation Reports

> **Portfolio project — simulated SOC investigations**
>
> Este repositório reúne exercícios práticos de análise e documentação de alertas de segurança. Os casos são simulados para fins de estudo e demonstram raciocínio investigativo, avaliação de evidências e tomada de decisão em um fluxo de SOC.
>
> **Importante:** estes documentos não representam incidentes reais, experiência profissional de atendimento a incidentes ou dados coletados de uma organização. Os nomes, equipamentos e eventos são fictícios. O endereço `192.0.2.80` pertence a uma faixa reservada para documentação (TEST-NET-1).

## Objetivo

Demonstrar, por meio de cenários controlados, como um analista SOC pode:

- interpretar um alerta e estabelecer seu contexto;
- organizar eventos em uma linha do tempo;
- diferenciar fatos observados de hipóteses;
- identificar lacunas de evidência;
- justificar uma classificação e uma decisão de escalonamento;
- registrar recomendações para continuidade da investigação.

## Casos

| ID | Cenário | Resultado do exercício |
|---|---|---|
| `SOC-001` | Tentativas de autenticação malsucedidas seguidas de login bem-sucedido | Investigar a relação entre as falhas e o login posterior |
| `SOC-003` | Possível acesso não autorizado a uma conta do setor financeiro | Escalonamento simulado para SOC N2 |

---

## SOC-001 — Tentativas de login suspeitas

### Resumo do alerta

Foi observado um conjunto de tentativas de autenticação malsucedidas para a conta `carlos`, seguido por um login bem-sucedido. O endereço IP associado às tentativas foi `192.0.2.80`.

### Evidências disponíveis

- Conta envolvida: `carlos`.
- Origem observada: `192.0.2.80`.
- Cinco tentativas de autenticação malsucedidas.
- Uma autenticação bem-sucedida após as falhas.

### Análise

A sequência de falhas seguida por uma autenticação bem-sucedida merece investigação. Uma possibilidade é que alguém tenha conseguido autenticar-se após várias tentativas; outra é que o comportamento tenha uma explicação legítima, como erro de digitação ou uso de credenciais antigas.

A informação disponível não permite concluir, por si só, que a conta foi comprometida. Um ponto importante da investigação é confirmar se o login bem-sucedido ocorreu a partir do mesmo endereço IP das tentativas malsucedidas e comparar o evento com o padrão habitual da conta.

### Avaliação

**Classificação do exercício:** atividade suspeita — requer validação adicional.

**Conclusão:** há um padrão que justifica investigação, mas as evidências registradas neste exercício são insuficientes para confirmar comprometimento.

### Próximas ações recomendadas

1. Confirmar o endereço IP e o dispositivo associados ao login bem-sucedido.
2. Verificar horário, localização aproximada e método de autenticação, quando esses dados estiverem disponíveis.
3. Comparar o evento com o histórico normal de autenticação da conta.
4. Procurar outros eventos relacionados no mesmo período.
5. Se houver indicadores adicionais de comprometimento, seguir o procedimento de resposta a incidentes da organização.

### Limitações

O registro disponível não informa o horário exato dos eventos, o dispositivo utilizado nem se o login bem-sucedido partiu efetivamente do mesmo IP. Esses pontos permanecem como itens de validação, não como fatos confirmados.

---

## SOC-003 — Possível acesso não autorizado

### Resumo do alerta

Foi identificada uma sequência de falhas de autenticação associada à conta `m.souza`, pertencente ao setor financeiro. As falhas ocorreram em `PC-RECEP-02`, seguidas por um login bem-sucedido em `PC-FIN-04`. A pessoa associada à conta informou não reconhecer a atividade.

### Evidências disponíveis

- Conta: `m.souza`.
- Setor: Financeiro.
- Nove falhas de autenticação em aproximadamente 51 segundos.
- Origem das falhas: `PC-RECEP-02`.
- Login legítimo previamente observado em `PC-FIN-04`, às 13:42.
- Falhas observadas em `PC-RECEP-02` entre 14:03 e 14:04.
- Login bem-sucedido em `PC-FIN-04` às 14:05.
- A pessoa associada à conta negou ter realizado a atividade.
- Não foram encontrados eventos semelhantes nos 30 dias anteriores, conforme os dados do exercício.

### Linha do tempo

| Horário | Evento |
|---|---|
| 13:42 | Login observado em `PC-FIN-04` |
| 14:03–14:04 | Nove falhas de autenticação associadas à conta em `PC-RECEP-02` |
| 14:05 | Login bem-sucedido em `PC-FIN-04` |

### Análise

A sequência é relevante por combinar várias falhas em um intervalo curto, uso de um equipamento diferente (`PC-RECEP-02`) e um login posterior bem-sucedido. A negativa da pessoa associada à conta aumenta a necessidade de validação.

O fato de o login bem-sucedido ter ocorrido em `PC-FIN-04` não demonstra, isoladamente, que as falhas em `PC-RECEP-02` causaram ou possibilitaram esse acesso. É necessário correlacionar os registros de autenticação, identificar a origem efetiva da sessão e verificar se houve uso da conta em outros sistemas.

A ausência de eventos semelhantes nos 30 dias anteriores torna o comportamento menos comum dentro do histórico fornecido, mas não prova atividade maliciosa.

### Avaliação

**Classificação do exercício:** suspeita de acesso não autorizado.

**Decisão simulada:** escalonamento para SOC N2.

**Justificativa:** a combinação de falhas concentradas, divergência entre os equipamentos envolvidos e negação da atividade pela pessoa associada à conta exige investigação mais aprofundada. O escalonamento evita concluir prematuramente que houve comprometimento, mas preserva a necessidade de apurar o possível impacto.

### Ações recomendadas ao SOC N2

1. Correlacionar os logs de autenticação dos dois equipamentos e do serviço de identidade.
2. Confirmar a origem, o horário e o contexto da sessão bem-sucedida das 14:05.
3. Verificar quais sistemas, arquivos e recursos foram acessados pela conta após o evento.
4. Procurar alterações de privilégios, criação de sessões adicionais ou outras atividades incomuns.
5. Verificar se `PC-RECEP-02` apresenta outros eventos relevantes no mesmo intervalo.
6. Preservar os registros pertinentes conforme os procedimentos da organização.
7. Avaliar medidas de contenção de acordo com a política interna e com os resultados da investigação.

### Limitações

Os dados do exercício não identificam a causa das falhas, não comprovam quem operava os equipamentos e não detalham os recursos acessados após o login. Portanto, não é possível afirmar que houve exfiltração, alteração de dados ou comprometimento confirmado.

---

## Metodologia de documentação

Em cada exercício, as informações devem ser separadas em três grupos:

- **Fatos observados:** dados explicitamente presentes no cenário, como horários, contas, equipamentos e quantidade de eventos.
- **Hipóteses:** explicações possíveis que ainda precisam ser testadas.
- **Lacunas:** informações que seriam necessárias para confirmar ou descartar as hipóteses.

Uma conclusão deve ser proporcional às evidências. Um alerta suspeito não é automaticamente um incidente confirmado.

## Escopo e uso responsável

Os cenários deste repositório são exclusivamente educacionais. Não contêm credenciais, dados pessoais reais ou instruções para realizar acesso não autorizado. Os nomes e equipamentos são fictícios e devem ser tratados como elementos de simulação.

## Evolução do repositório

Novos exercícios podem ser adicionados mantendo a mesma estrutura:

1. resumo do alerta;
2. evidências disponíveis;
3. linha do tempo, quando aplicável;
4. análise;
5. avaliação e justificativa;
6. ações recomendadas;
7. limitações e lacunas de evidência.

---

*Documentação inicial criada para fins de portfólio e estudo. Atualize este README conforme novos exercícios forem realizados e revisados.*
