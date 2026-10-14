# Análise de Dados de Veículos com Python e AWS

Projeto acadêmico de Ciência de Dados desenvolvido por **Caio Cabral Pinto** e **Guilherme Melo Freitas**, com foco em preparação de dados, análise exploratória, visualização e integração com o Amazon S3.

O estudo utiliza informações de veículos para apoiar a análise de oportunidades no fornecimento de peças automotivas, considerando tipos de transmissão, preços recomendados e potência dos motores.

## Tecnologias utilizadas

| Tecnologia | Aplicação |
| --- | --- |
| Python | Desenvolvimento das análises |
| Pandas | Leitura, limpeza, filtros e agrupamentos dos dados |
| Matplotlib | Configuração e exportação dos gráficos |
| Seaborn | Gráficos de contagem, barras e dispersão |
| Boto3 | Comunicação com o Amazon S3 |
| s3fs | Escrita de CSV no S3 por meio do Pandas |
| Jupyter Notebook / Google Colab | Execução interativa do notebook |
| Amazon S3 | Armazenamento dos dados tratados e gráficos |

## Base de dados

A entrada do projeto é o arquivo **`data.csv`**, disponibilizado na atividade acadêmica. Ele deve estar no diretório de execução do notebook.

A função `carregar_dados()` lê o CSV e padroniza os nomes das principais colunas para português, sem modificar o arquivo original. Entre os atributos utilizados estão:

- Fabricante, modelo e ano;
- Potência do motor e quantidade de cilindros;
- Tipo de transmissão e rodas motrizes;
- Número de portas e tamanho do veículo;
- Consumo na cidade e na rodovia, em MPG;
- Preço recomendado de venda.

Colunas adicionais presentes no CSV são mantidas durante a leitura.

## Cenário analisado

O notebook utiliza o cenário da **dupla 05**:

| Parâmetro | Valor |
| --- | --- |
| Ano mínimo | 2014 |
| Fabricante A | Toyota |
| Fabricante B | Nissan |

```python
ANO_MIN = 2014
FAB_A = 'Toyota'
FAB_B = 'Nissan'
```

## Etapas do projeto

### 1. Preparação dos dados

- Visualização das cinco primeiras linhas;
- Identificação e exclusão de linhas com valores nulos;
- Inspeção dos tipos de dados;
- Contagem de valores distintos por coluna;
- Remoção da coluna `Modelo`, direcionando a análise para fabricantes e características dos veículos.

### 2. Análise dos tipos de transmissão

Aplicação do filtro de ano e fabricantes, seguida da criação de um gráfico com a quantidade de registros por tipo de transmissão.

A análise serve como ponto de partida para discutir oportunidades de produção de peças para diferentes transmissões.

### 3. Comparação de preços médios

Agrupamento por fabricante, cálculo da média do preço recomendado e ordenação decrescente, com apresentação em gráfico de barras.

**Escopo atual:** embora o gráfico tenha o título “Top 10”, o código reutiliza o filtro de Toyota e Nissan. Portanto, compara somente essas duas fabricantes, em vez de produzir um ranking de todas as marcas da base.

### 4. Relação entre potência e preço

- Cálculo da correlação entre potência do motor e preço recomendado, no recorte geral e por fabricante;
- Gráfico de dispersão com cores diferentes para Toyota e Nissan;
- Exportação da visualização em PNG.

A correlação indica associação entre variáveis e não comprova causalidade.

### 5. Integração com o Amazon S3

O notebook contém código para autenticação com credenciais informadas via `getpass`, criação do cliente S3, listagem de objetos e envio dos resultados para um bucket existente.

| Destino no bucket | Conteúdo |
| --- | --- |
| `prova-dados/data.csv` | Dados tratados, exportados sem o índice do DataFrame |
| `graficos/grafico1.png` | Contagem por tipo de transmissão |
| `graficos/grafico2.png` | Comparação de preços médios |
| `graficos/grafico3.png` | Dispersão de potência e preço |

O arquivo `q4_potencia_preco.png` também é salvo localmente, mas não está incluído na lista de upload.

## Como executar

### Google Colab

1. Abra o Google Colab e importe o arquivo `.ipynb` do projeto.
2. Envie o arquivo `data.csv` para o ambiente de execução.
3. Caso necessário, instale as dependências em uma célula:

   ```python
   %pip install pandas matplotlib seaborn boto3 s3fs
   ```

4. Execute as células na ordem em que aparecem, pois as análises compartilham variáveis e filtros.
5. Para executar a etapa de nuvem, configure o bucket e as credenciais conforme as orientações abaixo.

### Ambiente local

Instale as dependências e abra o Jupyter Notebook:

```bash
python -m pip install notebook pandas matplotlib seaborn boto3 s3fs
python -m notebook
```

Abra o notebook, mantenha `data.csv` no diretório de execução e execute as células em sequência.

### Configuração da AWS

Para executar a Questão 05:

1. Crie ou utilize um bucket privado do Amazon S3.
2. Altere `nome_bucket` para o nome do seu bucket.
3. Ajuste `AWS_DEFAULT_REGION` para a região correspondente. O notebook utiliza `us-east-1`.
4. Informe `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` e, ao utilizar credenciais temporárias, `AWS_SESSION_TOKEN` nos campos solicitados.
5. Use credenciais com permissão para listar os objetos do bucket e gravar arquivos nele.
6. Execute o envio após gerar os gráficos e confira os objetos no console do S3.

Não inclua credenciais no código ou nos arquivos publicados no repositório. A etapa de AWS pode ser omitida para executar apenas as análises locais.

**Observação de execução:** a célula de listagem acessa `resposta["Contents"]` diretamente. Em um bucket vazio, essa chave pode não existir. Nesse caso, substitua por `resposta.get("Contents", [])` para continuar a execução.

## Arquivos gerados

| Arquivo | Descrição |
| --- | --- |
| `grafico1.png` | Distribuição de registros por transmissão |
| `grafico2.png` | Preço médio por fabricante no recorte utilizado |
| `grafico3.png` | Relação entre potência e preço, em 300 DPI |
| `q4_potencia_preco.png` | Versão adicional do gráfico de dispersão, em 150 DPI |

O CSV tratado é gravado diretamente no S3 em `prova-dados/data.csv`.

## Ajustes pendentes

A versão atual do notebook ainda apresenta pontos a concluir:

- Separar as fabricantes no gráfico de transmissões usando `hue='Fabricante'`;
- Revisar o filtro da Questão 03 caso o objetivo seja obter as dez marcas mais caras de toda a base;
- Listar os valores das colunas categóricas: `nunique()` conta valores distintos, mas não mostra quais são eles;
- Preencher as interpretações e justificativas indicadas nas células de texto;
- Uniformizar as referências a 2013 e 2014 nos enunciados, considerando que o código do cenário 05 utiliza 2014;
- Adequar o destino dos gráficos para `resultados/` caso seja exigido seguir literalmente o enunciado da Questão 05, pois o código atual utiliza `graficos/`.

As contagens representam registros do conjunto de dados, não volumes comprovados de produção ou vendas. A reprodução dos resultados depende do `data.csv` original, que não acompanha o notebook enviado para esta documentação.

## Autores

- **Caio Cabral Pinto** — [GitHub](https://github.com/CaioDev-one)
- **Guilherme Melo Freitas**

Projeto desenvolvido para a avaliação prática da disciplina de **Ciência de Dados**, sob orientação do professor **Miguel Bozer da Silva**.
