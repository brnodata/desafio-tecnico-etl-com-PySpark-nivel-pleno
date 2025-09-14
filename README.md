# Pipeline ETL com PySpark no Google Colab

objetivo
processar clientes (csv) e vendas (posicional fixo, sem separador) para gerar:
- resumo por cliente: total_vendas, quantidade_vendas, ticket_medio
- balanço por produto: total_vendas_produto, quantidade_vendas_produto, ticket_medio_produto

entradas
- clientes.csv (com header): colunas cliente_id,nome,data_nascimento
- vendas (posicional, 31 caracteres por linha), formatação esperada:
  1–5 cliente_id | 6–10 venda_id | 11–15 produto_id | 16–23 valor (inteiro; 00012345 → 123,45) | 24–31 data_venda (YYYYMMDD)

caminhos padrão no colab
- clientes: /content/dados/clientes.txt
- vendas:   /content/dados/vendas.csv  (conteúdo posicional, sem vírgulas)
- saídas:   /content/dados/Saida/resumo_clientes e /content/dados/Saida/balanco_produtos

execução rápida
```python
# 1) instalar e iniciar spark
!pip install pyspark -q
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()

# 2) ajustar caminhos se necessário e executar o notebook/script do pipeline
#    (o script já lê, transforma, faz o join e grava as saídas)
```

observações mínimas
- venda_id é chave primária e não deve repetir
- cliente_id não pode se repetir no dataset de Clientes, porem pode se repetir no join (múltiplas vendas por cliente). Padronização via `lpad` garante o match com o arquivo posicional (5 dígitos).
- ticket_medio arredondado para 2 casas decimais
- o arquivo de vendas é lido como texto “cru” (uma coluna) e quebrado via substring

