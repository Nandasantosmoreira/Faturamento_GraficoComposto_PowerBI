# 📊 Power BI - Gráfico Composto de Faturamento  

Este projeto apresenta uma solução avançada de visualização no Power BI, combinando **barras e linhas** para exibir o faturamento de maneira dinâmica e intuitiva.  

## 🚀 Visão Geral  

O relatório permite ao usuário selecionar um **mês/ano** e visualizar:  
✅ **Faturamento dos últimos 6 meses** (barras)  
✅ **Faturamento diário do mês selecionado** (linha)  
✅ **Total do faturamento do mês** como uma sétima barra  

Para tornar essa solução possível, foi criada uma **tabela de calendário customizada** e utilizadas técnicas avançadas em DAX, como a função `TREATAS`, que possibilita a criação de relacionamentos virtuais entre tabelas.  

---

## 🛠 Tecnologias Utilizadas  

- **Power BI**  
- **DAX (Data Analysis Expressions)**  
- **Modelagem de Dados**  
- **Figma** *(para o design do template do plano de fundo do relatório)*  

---

## 🏗 Estrutura do Projeto  

### 📌 **1. Criação da Tabela dCalendario Customizada**  

A tabela `dCalendarioCustomizada` foi criada para estruturar os diferentes níveis de agregação do tempo no gráfico.  

```DAX
dCalendarioCustomizada = 
VAR _Meses = 
    SELECTCOLUMNS(
        ALL(dCalendario[MesCount], dCalendario[MesAno]),
        "Ordem", dCalendario[MesCount],
        "Eixo", dCalendario[MesAno]
    )
VAR _Datas = 
    SELECTCOLUMNS(
        VALUES(dCalendario[Data]),
        "Ordem", dCalendario[Data],
        "Eixo", dCalendario[Data]
    )
VAR _TotalMes = ROW("Ordem", 999999, "Eixo", "Total Mês")
RETURN
DISTINCT(
    UNION(
        _Meses,
        _Datas,
        _TotalMes
    )
)

### 📌 **2.Medidas DAX Criadas** 


-> Faturamento dos Últimos 6 Meses
Fat 6Meses = 
VAR _MesCountAtual = SELECTEDVALUE(dCalendario[MesCount]) 
VAR _Ultimos6Meses = 
    FILTER(
        VALUES(dCalendarioCustomizada[Ordem]), 
        dCalendarioCustomizada[Ordem] < _MesCountAtual && 
        dCalendarioCustomizada[Ordem] >= _MesCountAtual - 6
    ) 
RETURN
CALCULATE(
    [Faturamento],
    ALL(dCalendario),
    TREATAS(
        _Ultimos6Meses,
        dCalendario[MesCount]
    )
)

-> Faturamento Diário do Mês Selecionado
Fat Datas = 
VAR _MesAtual = SELECTEDVALUE(dCalendario[MesNum]) 
VAR _AnoAtual = SELECTEDVALUE(dCalendario[Ano]) 
VAR _DatasMesAtual = 
    FILTER(
        VALUES(dCalendarioCustomizada[Ordem]), 
        YEAR(dCalendarioCustomizada[Ordem]) = _AnoAtual && 
        MONTH(dCalendarioCustomizada[Ordem]) = _MesAtual
    ) 
RETURN
CALCULATE(
    [Faturamento],
    TREATAS(
        _DatasMesAtual, 
        dCalendario[Data]
    )
)

-> Total do Faturamento do Mês
Fat Total Mês = 
VAR _MesAtual = SELECTEDVALUE(dCalendario[MesNum]) 
VAR _AnoAtual = SELECTEDVALUE(dCalendario[Ano]) 
VAR _DatasMesAtual = 
    FILTER(
        ALL(dCalendarioCustomizada[Ordem]), 
        YEAR(dCalendarioCustomizada[Ordem]) = _AnoAtual && 
        MONTH(dCalendarioCustomizada[Ordem]) = _MesAtual
    ) 
VAR _FatDatas =
    CALCULATE(
        [Faturamento], 
        TREATAS(
            _DatasMesAtual, 
            dCalendario[Data]
        )
    )
RETURN
IF(
    SELECTEDVALUE(dCalendarioCustomizada[Eixo]) = "Total Mês", 
    _FatDatas
)

🎯 **Destaque para a função TREATAS**

A função TREATAS foi essencial para criar relacionamentos virtuais no modelo, garantindo que os cálculos respeitassem os períodos filtrados.

Ela permite que uma tabela filtrada seja mapeada como um filtro em outra tabela, sem necessidade de criar relacionamentos físicos. Isso melhora a flexibilidade e otimiza o desempenho.

Exemplo de uso no projeto:

TREATAS(
    _DatasMesAtual, 
    dCalendario[Data]
)

📝 Conclusão
Esse projeto traz uma abordagem eficiente para visualizar faturamento ao longo do tempo, combinando agregações mensais e diárias de maneira dinâmica no Power BI.

Além disso, utilizamos o Figma para desenvolver um plano de fundo customizado, trazendo mais identidade visual e profissionalismo ao relatório.

Se esse método foi útil para você, ⭐ marque este repositório e contribua com sugestões nos comentários!

💬 Conecte-se Comigo
🔗 LinkedIn: fernanda-santos-moreira-analista-python-r-sql
📧 E-mail: nandasmiranda2014@gmail.com

🚀 Vamos trocar ideias sobre modelagem de dados no Power BI!
