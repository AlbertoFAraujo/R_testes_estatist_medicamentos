![image](https://github.com/AlbertoFAraujo/R_MongoDB_R_airbnb/assets/105552990/f88f67b4-373f-41a1-ac46-b21ec3137c97)

### Tecnologias utilizadas: 
| [<img align="center" alt="R_studio" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/02dff6df-07be-43dc-8b35-21d06eabf9e1">](https://posit.co/download/rstudio-desktop/) | [<img align="center" alt="ggplot" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/db55b001-0d4c-42eb-beb2-5131151c7114">](https://plotly.com/r/) | [<img align="center" alt="plotly" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/5f681062-c399-44af-a658-23e94b8b656f">](https://plotly.com/r/) | [<img align="center" alt="mongodb" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_MongoDB_R_airbnb/assets/105552990/e0104b01-1696-45a3-9ae6-4769dd898722">](https://www.mongodb.com/docs/) | [<img align="center" alt="devtools" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_MongoDB_R_airbnb/assets/105552990/d8377fe5-aade-4bbd-8331-812901e2d8c1">](https://www.rdocumentation.org/packages/devtools/versions/2.4.5) |
|:---:|:---:|:---:|:---:|:---:|
| R Studio | Ggplot2 | Plotly | MongoDB | Devtools |

- **RStudio:** Ambiente integrado para desenvolvimento em R, oferecendo ferramentas para escrita, execução e depuração de código.
- **Ggplot2:** Pacote para criação de visualizações de dados elegantes e flexíveis em R.
- **Plotly:** Biblioteca interativa para criação de gráficos e visualizações em diversas linguagens.
- **MongoDB:** Um banco de dados NoSQL de alta performance, orientado a documentos, projetado para escalabilidade e flexibilidade.
- **Devtools:** Conjunto de ferramentas e utilitários para desenvolvedores, incluindo bibliotecas, frameworks, ambientes de desenvolvimento integrado (IDEs) e outros recursos para facilitar o desenvolvimento de software.
<hr>

### Objetivo: 

Integrar os dados de uma base contida no banco de dados MongoDB e realizar um Map Reduce somente com as funções nativas do mongo para identicar as seguintes questões:

1.  Total do número de avaliações dos quartos? E por faixa?
2.  Quantas propriedades possuem acomodações acima de 2?
3.  Quantas propriedades não aceitam nenhuma pessoa extra?

Base de dados: <https://insideairbnb.com/get-the-data/>
<hr>

### Script R: 
```r
# Ajustar as casas decimais
options(scipen = 999, digits = 4)

# Definir um espelho de CRAN
options(repos = "http://cran.rstudio.com/")
```
```r
# Instalando os pacores necessários
utils::install.packages("devtools")
install.packages("mongolite")
install.packages("plotly")
```

```r
# Carregando as bibliotecas
library(devtools)
library(mongolite)
library(ggplot2)
library(dplyr)
library(plotly)
```
```r
# Criando a conexão com banco de dados

con <- mongolite::mongo(
  collection = 'airbnb',
  db = 'dbairbnb',
  url = 'mongodb://localhost:27017',
  verbose = FALSE,
  options = ssl_options()
)
```
```r
# Visualizar a conexão
print(con)
```
```r
# Visualizar os dados
dados <- con$find()
head(dados,1)
```
```r
# Verifica o número de registros
con$count('{}')
```
[1] 5555

```r
# Verificando o nome das variáveis (colunas)
names(dados)
```
| Número | Variável                | Número | Variável                | Número | Variável                | Número | Variável                |
|--------|-------------------------|--------|-------------------------|--------|-------------------------|--------|-------------------------|
| [1]    | listing_url             | [11]   | house_rules             | [21]   | last_review             | [31]   | extra_people            |
| [2]    | name                    | [12]   | property_type           | [22]   | accommodates            | [32]   | guests_included         |
| [3]    | summary                 | [13]   | room_type               | [23]   | bedrooms                | [33]   | images                  |
| [4]    | space                   | [14]   | bed_type                | [24]   | beds                    | [34]   | host                    |
| [5]    | description             | [15]   | minimum_nights          | [25]   | number_of_reviews       | [35]   | address                 |
| [6]    | neighborhood_overview   | [16]   | maximum_nights          | [26]   | bathrooms               | [36]   | availability            |
| [7]    | notes                   | [17]   | cancellation_policy     | [27]   | amenities               | [37]   | review_scores           |
| [8]    | transit                 | [18]   | last_scraped            | [28]   | price                   | [38]   | reviews                 |
| [9]    | access                  | [19]   | calendar_last_scraped   | [29]   | security_deposit        | [39]   | weekly_price            |
| [10]   | interaction             | [20]   | first_review            | [30]   | cleaning_fee            | [40]   | monthly_price           |
|        |                         |        |                         |        |                         | [41]   | reviews_per_month       |
```r
# Filtrando por query e fields no mongo
filtro <- con$find(
  query = '{"property_type":"House"}',
  fields = '{"name": true,"maximum_nights": true, "price": true, "_id": false}',
  sort = '{"price": -1}' # ordenar desc
)
head(filtro)
```
| Número | name                                              | Número | maximum_nights | Número | price |
|--------|---------------------------------------------------|--------|----------------|--------|-------|
| [1]    | Stunning Waterfront Marina bay house in Sai Kung | [1]    | 1125           | [1]    | 7002  |
| [2]    | Barra da Tijuca beach house                      | [2]    | 1125           | [2]    | 5595  |
| [3]    | Casa MARAVILHOSA, 5 suites OLIMPIADAS RIO 2016   | [3]    | 60             | [3]    | 5595  |
| [4]    | LUXURY HOUSE IN BARRA DA TIJUCA                  | [4]    | 180            | [4]    | 5502  |
| [5]    | 鮀城小家-感受不一样的异地之旅                        | [5]    | 2              | [5]    | 4828  |
| [6]    | IPANEMA, Rio de Janeiro, Brasil                  | [6]    | 1125           | [6]    | 3730  |

```r
# Contagem do número de visualizações dos quartos
resultado2 <- con$mapreduce(
  map = "function(){
          emit(Math.floor(this.number_of_reviews), 1)
        }",
  reduce = "function(id, counts){
          return Array.sum(counts)
          }"
)

names(resultado2) <- c('numero_reviews','contagem')
```
```r
# Gerando o gráfico do número de visualizações por propriedades
fig <- plot_ly(resultado2, x = ~numero_reviews, y = ~contagem, type = 'bar',
        marker = list(color = 'rgb(158,202,225)',
                      line = list(color = 'rgb(8,48,107)',
                                  width = 1.5)))
fig <- fig %>% layout(title = "Número de avaliações por propriedadades",
         xaxis = list(title = "Total de Visualizações"),
         yaxis = list(title = "Contagem total"))
fig
```
![1](https://github.com/AlbertoFAraujo/R_MongoDB_R_airbnb/assets/105552990/a5f69278-853b-42a1-98be-afa69bfcf856)

- 1388 propriedades não obtiveram nenhuma visualização;
- 511 propriedades obtiveram pelo menos 1 visualização;
- 329 propriedades obtiveram pelo menos 2 visualizações.

```r
# Número de visualizações por faixa
resultado3 <- con$mapreduce(
  map = "function(){
            emit(Math.floor(this.number_of_reviews/100) * 100, 1)
            }",
  reduce = "function(id, counts){
    return Array.sum(counts)
  }"
)

names(resultado3) <- c('numero_reviews','contagem')
```
```r
# Gerando o gráfico do número de visualizações por propriedades por faixa

fig <- plot_ly(resultado3, x = ~numero_reviews, y = ~contagem, type = 'bar',
        marker = list(color = 'rgb(158,202,225)',
                      line = list(color = 'rgb(8,48,107)',
                                  width = 1.5)))
fig <- fig %>% layout(title = "Número de visualizazções por faixa",
         xaxis = list(title = "Faixa de visualizações"),
         yaxis = list(title = "Contagem Total"))

fig
```
![2](https://github.com/AlbertoFAraujo/R_MongoDB_R_airbnb/assets/105552990/494e59a6-03f2-4826-81ea-b955e95c2ade)

**Resumo da análise:**

-   5105 propriedades obtiveram entre 0 e 100 visualizações;
-   351 propriedades obtiveram entre 100 e 200 visualizações;
-   80 propriedades obtiveram entre 200 e 300 visualizações;
-   13 propriedades obtiveram entre 300 e 400 visualizações;
-   5 propriedades obtiveram entre 400 e 500 visualizações;
-   1 propriedade obteve 500 ou mais visualizações.

```r
# Quantas propriedades possuem o maior número de quartos? E a segunda maior?

resultado4 <- con$mapreduce(
  map = "function(){
          if (this.accommodates <= 100){
            emit(Math.floor(this.accommodates), 1)
          }
        }",
  reduce = "function(id, counts){
          return Array.sum(counts);
          }"
)

names(resultado4) <- c('numero_acomodações','contagem')
resultado4 <- resultado4[order(resultado4$numero_acomodações),]
resultado4
```
| Numero_acomodações | Contagem |
|--------------------|----------|
| 8                  | 1        |
| 15                 | 2        |
| 14                 | 3        |
| 7                  | 4        |
| 5                  | 5        |
| 1                  | 6        |
| 3                  | 7        |
| 9                  | 8        |
| 2                  | 9        |
| 11                 | 10       |
| 6                  | 11       |
| 12                 | 12       |
| 16                 | 13       |
| 10                 | 14       |
| 13                 | 15       |
| 4                  | 16       |

```r
# Plotagem das propriedades com maiores números de quartos
plot1 <- resultado4 %>% 
  plot_ly(
    x = ~numero_acomodações,
    y = ~contagem,
    type = 'bar',
    text = ~contagem,
    textposition = 'auto',
    marker = list(color = 'rgb(158, 202, 225)',
                  line = list(color = 'rgb(8, 48, 107)',
                              width = 1.5)
                  )
  ) %>% 
  layout(title = "Propriedades menos de 5 quartos",
         xaxis = list(title = "Número de quartos"),
         yaxis = list(title = "Total por número de quartos")
         )

plot1
```
![3](https://github.com/AlbertoFAraujo/R_MongoDB_R_airbnb/assets/105552990/19e51dc1-b1ca-4beb-a25a-faa6facf6f9d)

2052 propriedades possuem 2 quartos e 1154 propriedades possuem 4 quartos, sendo a predominância do número de quartos

```r
# Quantas propriedades não aceita nenhuma pessoa extra?

resultado5 <- con$mapreduce(
  map = "function(){
            if (this.extra_people != 0){
              emit(this.extra_people, 1)
            }
        }",
  reduce = "function(id, counts){
          return Array.sum(counts);
          }"
)

names(resultado5) <- c('pessoas_extras','contagem')
resultado5 <- resultado5[order(resultado5$pessoas_extras),]
head(resultado5)
```
| Pessoas_extras | Contagem |
|----------------|----------|
| 32             | 0        |
| 115            | 4        |
| 43             | 5        |
| 37             | 6        |
| 39             | 7        |
| 41             | 8        |

3135 das propriedades listadas não aceitam pessoas extras.
