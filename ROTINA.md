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
  Categorias em uso: Receita Federal, CGIBS, Documentos fiscais, Simples Nacional, Legislação, TCU, Congresso. Crie outra só se nenhuma servir.
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

Use WebFetch nas páginas e WebSearch (ex.: "reforma tributária CBS IBS" + nome do órgão + mês atual) para achar o que não aparece na página inicial.
Se um site não abrir, registre isso no `obs` do histórico e siga com os demais.

## O que conta como novidade

- Publicação sobre a reforma tributária do consumo (IBS, CBS, Imposto Seletivo, CGIBS, documentos fiscais, Simples Nacional na reforma, transição) com data **posterior à última verificação**, ou anterior mas que ainda não esteja no painel.
- Antes de incluir, compare com `atualizacoes` por `url`, `titulo` e assunto. Nada de duplicata.
- Só fontes oficiais (gov.br, leg.br, tcu.gov.br, in.gov.br, cgibs.gov.br, planalto.gov.br, Agência Gov). Fonte não oficial só quando trouxer ato oficial ainda não localizado; nesse caso `fonteOficial: false` e o campo `ato` diz o que conferir.

## Como escrever

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

- `ultimaVerificacao`: agora, ex. `2026-11-11T07:05:00-03:00`.
- `novidadesHoje`: quantidade de itens novos incluídos nesta verificação.
- `resumo`: curto, em português, para cliente de escritório contábil. Primeiro parágrafo: o que entrou hoje (ou "Sem novidades nos canais oficiais na verificação de dd/mm/aaaa."). Depois, em um parágrafo, o principal ponto de atenção em aberto (ex.: prazo próximo). Sem interpretação.
- `historico`: acrescente `{"data": "AAAA-MM-DD", "novidades": N, "obs": "..."}`.

## Gravar e publicar

1. Grave o JSON de volta no bloco, com `ensure_ascii=False`. Garanta que nenhum texto contenha `</` (troque por `<\/`).
2. Valide: extraia o bloco de novo e rode `json.loads`; confira que o arquivo ainda tem `<!--DADOS-FIM-->` e o `<script>` final.
3. `git add index.html && git commit -m "Verificação dd/mm/aaaa: N novidade(s)" && git push origin HEAD:main`.
4. Se não houver novidade, ainda assim atualize `ultimaVerificacao`, `novidadesHoje: 0`, `resumo` e `historico`, e faça o commit (o cliente vê que a verificação rodou).
5. Termine com um resumo de 2 a 4 linhas do que foi incluído.
