# Projeto Aurora - Versão 1 (Power BI)

Base de dados da Rede Aurora Odontologia (Bootcamp Educados). Esta versão 1 reúne a **ingestão e a limpeza inicial** das tabelas no Power BI Desktop. A modelagem (relacionamentos) e os painéis ainda não foram feitos.

Os dados são fictícios. A data máxima das vendas é 30/09/2026.

## O que tem neste repositório

| Pasta / arquivo | O que é |
|---|---|
| `Projeto_Aurora/` | As 25 tabelas originais em .xlsx e o dicionário de dados |
| `Projeto_Aurora/00_dicionario_de_dados.xlsx` | Leia primeiro. Explica cada tabela, coluna, chave e relacionamento |
| `ProjetoAurora_V1.pbix` | Arquivo do Power BI com as tabelas carregadas e tratadas |
| `docs/` | Documentação do que foi feito |

## Como abrir

1. Abra o `ProjetoAurora_V1.pbix` no Power BI Desktop.
2. Se aparecer erro de fonte de dados, vá em **Transformar dados > Configurações da fonte de dados** e aponte para a sua cópia da pasta `Projeto_Aurora`. O caminho original é da máquina de quem criou.

## O que foi feito na V1

### 1. Ingestão
As 25 tabelas foram carregadas uma a uma no Power Query, cada uma com o mesmo nome do arquivo.

### 2. Datas
O Power BI leu várias datas como número inteiro (por exemplo, 45293). Esse número é o formato serial do Excel, contado em dias desde 30/12/1899. Corrigimos mudando o tipo da coluna para **Data**.

- Colunas de data não devem ter hora. Onde apareceu `00:00:00`, o tipo foi trocado de Data/Hora para **Data**.
- Exemplo: `recebiveis_cartao` (`data_venda`, `data_prevista_recebimento`, `data_recebimento`).

### 3. Valores em dinheiro
Colunas de dinheiro foram convertidas para **Número Decimal Fixo**, que evita pequenas diferenças de arredondamento nas somas. Isso vale para as colunas `valor_*` (por exemplo `valor_bruto`, `valor_liquido`, `valor_total`), a coluna `valor` de `financeiro` e `estornos`, e `meta_valor` de `metas_valor`.

O símbolo R$ é só aparência e é colocado no Power BI (Ferramentas de coluna > Formato > Moeda), não no Power Query.

### 4. Chaves (colunas `codigo_*`)
Colunas de código precisam ser **Número Inteiro** para os relacionamentos funcionarem. Em `metas_valor` e `metas_quantidade`, `codigo_vendedor` e `codigo_profissional` tinham tipo "Qualquer" (ABC123) por causa dos muitos vazios, e foram convertidas para inteiro.

### 5. Tabela calendario
- Removida a coluna `mes_ano`, que repetia exatamente o conteúdo de `ano_mes`.
- `ano_mes` ficou como Data (primeiro dia do mês) e é exibida no formato `yyyy-mm`.
- A tabela foi marcada como **tabela de datas**, usando a coluna `data`.
- Para os nomes de mês e de dia da semana saírem na ordem certa, use **Classificar por coluna**: `mes_nome` e `mes_abreviado` por `mes_numero`, e `dia_semana_nome` por `dia_semana_numero`.

## Vazios que NÃO devem ser tratados

Estes vazios fazem parte do dado. Não preencha com zero nem remova linhas.

| Tabela | Coluna | Por que está vazia |
|---|---|---|
| `metas_valor` e `metas_quantidade` | `codigo_grupo_servico`, `codigo_servico`, `codigo_vendedor`, `codigo_profissional` | Cada linha de meta usa só a coluna do seu nível (Produto, Serviço, Vendedor ou Profissional) |
| `orcamentos` | `codigo_venda` | Só orçamento Aprovado virou venda. Perdido e Pendente ficam vazios |
| `recebiveis_cartao` | `data_recebimento` | Parcela ainda "A receber" |
| `ortodontia` | `data_proxima_consulta` | Sem próxima consulta agendada |

## Cuidados importantes para quem for usar os dados

1. **Metas: sempre filtre o `nivel`.** Os quatro níveis são recortes independentes do mesmo total. Somar tudo sem filtrar conta a meta quatro vezes. A meta de valor da filial no mês é a soma do nível **Produto**.
2. **`taxa_percentual` (recebiveis_cartao):** o valor 3,5 significa 3,5%, não 0,035. Não converta a coluna para o tipo Percentual, senão vira 350%. Se precisar no cálculo, divida por 100.
3. **Perfil de coluna:** o Power Query mostra, por padrão, estatísticas das primeiras 1000 linhas. Para ver a base inteira, troque no rodapé para "conjunto de dados inteiro".
4. **Serviços sem meta:** devem aparecer como "sem meta" nos painéis, não como 0%.

## O que ainda falta (próximos passos)

- [ ] Montar os relacionamentos conforme a aba **Relacionamentos** do dicionário, só para as tabelas da fase em que estivermos. A detecção automática do Power BI criou ligações em excesso e deve ser desligada ou limpa.
- [ ] Resolver caminhos duplos (por exemplo `vendas` e `profissionais` com `filiais`), deixando alguns relacionamentos inativos e usando `USERELATIONSHIP` quando precisar.
- [ ] Em `financeiro`, que tem várias colunas de data, definir qual fica ativa com o calendário.
- [ ] Criar as medidas (vendas, ticket médio, atingimento de meta, taxa de conversão de orçamentos, taxa de abandono).
- [ ] Montar os painéis.

## Combinados do grupo

- Apenas **uma pessoa por vez** edita o arquivo `.pbix`. Ele é binário e o Git não consegue juntar duas versões.
- Cada um trabalha na sua parte e avisa antes de subir mudanças.
- Antes de editar, faça **Fetch origin** e **Pull** no GitHub Desktop.
- Mensagens de commit curtas e claras, dizendo o que mudou.

## Dúvidas

Consulte primeiro o dicionário de dados. Ele é a fonte oficial de tipos, chaves e relacionamentos.
