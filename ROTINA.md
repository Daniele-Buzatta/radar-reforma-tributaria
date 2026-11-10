# Rotina diária do Radar da Reforma Tributária

Instruções para a verificação automática que roda todo dia às 7h (horário de Brasília).
O painel público é o `index.html` deste repositório, servido pelo GitHub Pages.

## Onde ficam os dados

Todo o conteúdo do painel está no bloco JSON entre `<!--DADOS-INICIO-->` e `<!--DADOS-FIM-->`
do `index.html` (`<script type="application/json" id="dados">`). **Altere apenas esse bloco.**
Não mexa no HTML, no CSS nem no JavaScript.

Estrutura:

- `painel`: `ultimaVerificacao` (ISO com `-03:00`), `novidadesHoje` (número), `resumo` (texto, `\n` para quebras), `historico` (lista de `{data, novidades, obs}`).
- `atualizacoes`: novidades. Campos: `id`, `data` (data da publicação, AAAA-MM-DD), `registradoEm` (data de hoje), `categoria`, `orgao`, `titulo`, `texto`, `ato` (opcional), `fonte`, `url`, `fonteOficial` (true/false).
  Itens da carga inicial têm `"inicial": true`; **itens novos não levam esse campo** (é o que faz aparecer a etiqueta "Novo").
  Categorias em uso: Receita Federal, CGIBS, Documentos fiscais, Simples Nacional, Legislação, TCU, Congresso, Transporte e combustíveis. Crie outra só se nenhuma servir.
- `temas`: situação por tema. Campos: `id`, `tema`, `status` (`definido`, `tramitacao`, `pendente`, `implantacao`, `andamento`), `situacao`, `fontes` (lista de `{nome, url}`), `ordem`, `atualizadoEm`.
- `cronograma`: `id`, `data`, `evento`, `curto` (até ~50 caracteres, aparece em "Próximos prazos"), `fonte`, `url`, `fonteOficial`.
- `normas`: `id`, `ato`, `data`, `ementa`, `situacao`, `url`, `obs` (opcional).

## Canais a consultar

1. Receita Federal · Reforma Tributária do Consumo – últimas notícias: https://www.gov.br/receitafederal/pt-br/acesso-a-informacao/acoes-e-programas/programas-e-atividades/reforma-tributaria-do-consumo/ultimas-noticias
2. Comitê Gestor do IBS: https://cgibs.gov.br (notícias e https://cgibs.gov.br/regulamentos)
3. Diário Oficial da União: https://www.in.gov.br (atos sobre IBS, CBS, Imposto Seletivo, CGIBS, LC 214/2025)
4. Ministério da Fazenda: https://www.gov.br/fazenda/pt-br
5. Agência Senado: https://www12.senado.leg.br/noticias
6. Agência Câmara: https://www.camara.leg.br/noticias
7. TCU · Notícias: https://portal.tcu.gov.br/imprensa/noticias
8. Portal Nacional da NF-e (notas técnicas): https://www.nfe.fazenda.gov.br/portal
9. Portal do Simples Nacional: https://www8.receita.fazenda.gov.br/SimplesNacional/
10. NFS-e Nacional: https://www.gov.br/nfse
11. Agência Gov: https://agenciagov.ebc.com.br

12. Portal do CT-e (notas técnicas de CT-e, MDF-e e BP-e): https://www.cte.fazenda.gov.br/portal/listaConteudo.aspx?tipoConteudo=Y0nErnoZpsg%3D
13. Receita Federal · Normas (portarias, instruções normativas e atos): https://normas.receita.fazenda.gov.br
14. Planalto · LC 214/2025 compilada: https://www.planalto.gov.br/ccivil_03/leis/lcp/lcp214compilado.htm

**Consulte canal por canal.** Para cada um dos canais acima, faça ao menos uma consulta: WebFetch na página e, se ela não abrir, WebSearch com `allowed_domains` do próprio site (ex.: "CBS IBS" + mês atual). Não encerre a verificação com uma ou duas buscas gerais.
Procure também atos dos últimos 60 dias que ainda não estejam no painel (portarias, portarias conjuntas, resoluções do CGIBS, atos conjuntos, decretos, notas técnicas).
Se um site não abrir, registre no `obs` do histórico quais canais ficaram sem verificação direta e siga com os demais.

## Tema fixo: transporte e combustíveis

Muitos clientes do escritório são transportadoras. Em toda verificação, procure especificamente:
- alíquotas da CBS e do IBS sobre combustíveis (cobrança ad rem, Portaria MF 3.074/2026 e atualizações), diesel, biodiesel, Imposto Seletivo;
- crédito de CBS/IBS sobre insumos do transporte (combustível, pedágio, pneus, manutenção), crédito presumido na contratação de transportador autônomo (LC 214, art. 169);
- CT-e, CT-eOS, MDF-e e BP-e (notas técnicas, prazos, rejeições);
- transporte coletivo de passageiros e regimes específicos de transporte;
- notícias da ANTT e do Ministério dos Transportes sobre a reforma.
Novidades desse assunto vão na categoria "Transporte e combustíveis", e o tema `transporte-combustiveis` deve ser atualizado quando houver mudança.

## O que conta como novidade

- Publicação sobre a reforma tributária do consumo (IBS, CBS, Imposto Seletivo, CGIBS, documentos fiscais, Simples Nacional na reforma, transição) com data **posterior à última verificação**, ou anterior mas que ainda não esteja no painel.
- Antes de incluir, compare com `atualizacoes` por `url`, `titulo` e assunto. Nada de duplicata.
- Só fontes oficiais (gov.br, leg.br, tcu.gov.br, in.gov.br, cgibs.gov.br, planalto.gov.br, Agência Gov). Fonte não oficial só quando trouxer ato oficial ainda não localizado; nesse caso `fonteOficial: false` e o campo `ato` diz o que conferir.

## Como escrever

- O painel é lido por clientes. **Nunca cite estimativas, cálculos ou qualquer informação interna do escritório** (por exemplo, a estimativa interna de 9,2% de CBS).
- Se confirmar no texto oficial um item que hoje está com `fonteOficial: false` (ex.: número do ato conjunto RFB/CGIBS do DOU de 31/07/2026), troque pela fonte oficial.
- `texto` com até ~600 caracteres; `resumo` com até ~700.
- Reproduza o que está escrito no ato ou comunicado, **sem interpretação, opinião ou previsão**. Trechos literais entre aspas.
- `titulo`: o título da publicação oficial.
- `texto`: 1 a 3 frases com os pontos objetivos (o que muda, para quem, a partir de quando, número do ato). Pode ficar vazio se a página não trouxer mais que o título.
- Datas no texto no formato dd/mm/aaaa. Valores e percentuais exatamente como na fonte.
- `id`: `AAAA-MM-DD-assunto-curto` (minúsculas, hífens, sem acento).

## Atualizações além das novidades

- Se a novidade mudar a situação de um tema, atualize `situacao`, `status`, `fontes` e `atualizadoEm` do tema. Tema novo só se o assunto for relevante e duradouro.
- Data nova prevista em ato ou comunicado oficial → adicione ao `cronograma`. Se uma data mudar, corrija o item e cite a nova fonte.
- Ato normativo novo e estruturante (lei complementar, decreto, resolução do CGIBS, resolução do Senado sobre alíquota) → adicione a `normas`.
- Não apague itens antigos.

## Painel

- `ultimaVerificacao`: a hora real de agora em Brasília. Obtenha com `TZ=America/Sao_Paulo date +%Y-%m-%dT%H:%M:%S-03:00` (nunca estime a hora).
- `novidadesHoje`: quantidade de itens com `registradoEm` igual à data de hoje e sem `"inicial": true` (soma as verificações do mesmo dia).
- Se já houve verificação hoje, o `resumo` deve continuar citando o que entrou mais cedo no mesmo dia, acrescentando o que entrou agora.
- `resumo`: curto, em português, para cliente de escritório contábil. Primeiro parágrafo: o que entrou hoje (ou "Sem novidades nos canais oficiais na verificação de dd/mm/aaaa."). Depois, em um parágrafo, o principal ponto de atenção em aberto (ex.: prazo próximo). Sem interpretação.
- `historico`: acrescente `{"data": "AAAA-MM-DD", "novidades": N, "obs": "..."}` e mantenha só os 60 últimos.

## Gravar e publicar

1. Grave o JSON de volta no bloco, com `ensure_ascii=False`. Garanta que nenhum texto contenha `</` (troque por `<\/`).
2. Valide: extraia o bloco de novo e rode `json.loads`; confira que o arquivo ainda tem `<!--DADOS-FIM-->` e o `<script>` final.
3. `git add index.html && git commit -m "Verificação dd/mm/aaaa: N novidade(s)" && git push origin HEAD:main`.
4. Se não houver novidade, ainda assim atualize `ultimaVerificacao`, `novidadesHoje: 0`, `resumo` e `historico`, e faça o commit (o cliente vê que a verificação rodou).
5. Termine com um resumo de 2 a 4 linhas do que foi incluído.
