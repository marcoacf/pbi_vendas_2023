
# pbi_vendas_2023
exemplo de metricas para segmentação de indicadores, calendário, etc

[Aqui o relatório publicado](https://app.powerbi.com/groups/me/reports/f6780b1d-2fbd-47cf-8dad-8e1e5ad191e7/ReportSection)


## Arquivos:

 - [vendas-2023-04-29.json](https://github.com/marcoacf/pbi_vendas_2023/blob/main/vendas-2023-04-29.json) - base de dados, com 500 registros, criada no site [generatedata.com](https://generatedata.com/)

 - [vendas-2023-0-04-29.pbix](https://github.com/marcoacf/pbi_vendas_2023/blob/main/vendas-2023-0-04-29.pbix) - modelo Power BI com a modelagem DAX

 
 


## DAX

### - Tabelas auxiliares:
    
dCalendario - Tabela calendário, usando a data da fato vendas como parâmentro de inicio e fim    
```dax
    dCalendario = 
    var dataminima = DATE(YEAR(MIN(fVendas[data])),1,1)
    var datamaxima = DATE(YEAR(MAX(fVendas[data])),12,31)
    return 
    CALENDAR(dataminima, datamaxima)
```
dIndicadores - Tabela com a lista de indicadores que serão opção no visual segmentador
```dax    
    dIndicadores = {
        (1,'Qtde produtos'),
        (2,'Valor da Venda'),
        (3,'Desconto'),
        (4,'Custo'),
        (5,'Ticket Médio'),
        (6,'Custo Unitário')
        }
```

###  - Métricas:

```dax 
    Qtde de produtos = sum(fVendas[produtos])

    Valor Vendas = SUM(fVendas[venda])

    Desconto = SUM(fVendas[desconto])

    Custo = sum(fVendas[custo])

    Ticket Médio = DIVIDE([Valor Vendas], [Qtde de produtos],0)

    Custo Unitário = DIVIDE([Custo], [Qtde de produtos],0)

```
### - Métrica auxiliar (indicador selecionado):
Essa métrica irá identificar o valor selecionado na tabela auxiliar (dIndicadores), e retornará o indicador associado:
```dax 
Indicador Selecionado = 
 var indicador = SELECTEDVALUE ( dIndicadores[indicador], "Valor da Venda" )
 var valor = 
 SWITCH (
    True(),
    indicador = "Qtde produtos", [Qtde de produtos],
    indicador = "Valor da Venda" , [Valor Vendas],
    indicador = "Desconto"     , [Desconto],
    indicador = "Custo"        , [Custo],
    indicador = "Ticket Médio" , [Ticket Médio],
    indicador = "Custo Unitário", [Custo Unitário],
    "erro"
)
return valor
```
