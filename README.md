![image](https://github.com/AlbertoFAraujo/Teste_Shapiro_t_f/assets/105552990/57876723-7b94-4570-a87e-8b0a7d9c5046)

### Tecnologias utilizadas: 
| [<img align="center" alt="R_studio" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/02dff6df-07be-43dc-8b35-21d06eabf9e1">](https://posit.co/download/rstudio-desktop/) |
|:---:|
| R Studio |

- **RStudio:** Ambiente integrado para desenvolvimento em R, oferecendo ferramentas para escrita, execução e depuração de código.
<hr>

### Sobre o dataset "Sleep"

Esse dataset é o resultado de um trabalho de pesquisa com pacientes que possuem dificuldades para dormir. Os pacientes foram separados em 2 grupos e cada grupo recebeu um medicamento diferente para tratar os distúrbios no sono e ajudar a aumentar o tempo dormindo.

Variáveis:

-   *extra*: Variável numérica que indica quantas horas a mais ou a menos o paciente dormiu após receber o medicamento. Será a nossa variável dependente;

-   *group*: Variável do tipo fator (categórica) que indica que o medicamento usado pelo paciente (1 ou 2). Está será a nossa variável independente;

-   *ID*: Identificação do paciente.

------------------------------------------------------------------------

#### Objetivo:

Verificar se há diferença significativa entre os dois medicamentos que ajudam no distúrbio do sono

Como há duas amostras (dois grupos), podemos aplicar o Test t de Student para responder à pergunta. Mas para aplicar o Teste t, precisamos validar suas suposições e para isso precisamos do Teste de Shapiro-Wilk e do Teste F de Snedecor.

Definimos assim as hipóteses para nosso teste:

-   H0 (Hipótese Nula): Não há diferença significativa entre as médias dos 2 grupos;

-   H1 (Hipótese alternativa): Há diferença significativa entre as médias dos 2 grupos.

------------------------------------------------------------------------

Para validar o Teste t, iremos validar 5 suposições do Teste:

1.  Os dados são aleatórios e e representativos da população;
2.  A variável dependente é contínua;
3.  Ambos os grupos são independentes (ou seja, grupos exaustivos e excludentes);
4.  Os resíduos do modelo são normalmente distribuídos;
5.  A variância residual é homogênia (princípio da homocedastidade)

*Consideraremos como verdadeiras as suposições de 1 a 3 e validaremos as suposições 4 e 5. Para a suposição 4 usaremos o teste de Shapiro-Wilk (verificar se a amostra segue ou não uma distribuição normal) e para a suposição 5 o teste F (se há diferença significativa nas variâncias das médias)*

------------------------------------------------------------------------

### Análise dos dados

```R
# Importação das bibliotecas
libs <- c('car','tidyverse','qqplotr')

for (lib in libs) {
  if(!require(lib)) install.packages(lib)
}
```

```R
# Visualizando o dataset 
head(sleep)
```
| extra | group | ID |
|-------|-------|----|
|  0.7  |   1   |  1 |
| -1.6  |   1   |  2 |
| -0.2  |   1   |  3 |
| -1.2  |   1   |  4 |
| -0.1  |   1   |  5 |
|  3.4  |   1   |  6 |


```R
# Extraindo dados de um dos grupos
grupo_um <- sleep$group == 1
```

```R
# Visualização gráfica do grupo UM
qqPlot(sleep$extra[grupo_um])
```

![image](https://github.com/AlbertoFAraujo/Teste_Shapiro_t_f/assets/105552990/014a1d2e-90ec-44a7-a5ac-e09a8a726a61)


```R
# Visualização gráfica do grupo DOIS
qqPlot(sleep$extra[!grupo_um])
```

![image](https://github.com/AlbertoFAraujo/Teste_Shapiro_t_f/assets/105552990/f081cfc0-cf6f-404b-b2c5-c521bccca9f4)

Os pontos da variável "extra" estão localizados dentro da área de confiança, indicando que os dados seguem uma distribuição normal.

------------------------------------------------------------------------

### Validando a suposição 4 (Teste de Shapiro-Wilk)

"Os resíduos do modelo são normalmente distribuídos"

*Suposição: Falsa"*

Para dizer que uma distribuição é normal, o valor-p precisa ser maior que 0.05

*Hipóteses:*

-   H0 = Os dados seguem uma distribuição normal

-   H1 = Os dados não seguem uma distribuição normal

```R
shapiro.test(sleep$extra[grupo_um])
shapiro.test(sleep$extra[!grupo_um])
```

| Test Data                           | Result           |
|-------------------------------------|------------------|
| Shapiro-Wilk normality test        |                  |
| Variable:                            | sleep$extra[grupo_um]|
| W                                   | 0.93             |
| p-value                             | 0.4              |

| Test Data                           | Result           |
|-------------------------------------|------------------|
| Shapiro-Wilk normality test        |                  |
| Variable:                            | sleep$extra[!grupo_um]|
| W                                   | 0.92             |
| p-value                             | 0.4              |

Como p-value = 0.4 \> 0.05 para ambos os grupos, então falhamos em rejeitar a H0. Portanto, podemos assumir que os dados seguem uma distribuição normal.

Não há significância estatística para rejeitar H0. Não pode-se dizer que aceitamos H0, não tem como afirmar categoricamente que H0 é verdadeira. Pra isso precisamos realizar outros testes estatísticas. Neste caso, vamos considerar que H0 pode ser validada, ou seja, que H0 segue uma distribuição normal.

Nossa hipótese H0 afirmava que os dados são distribuídos normalmente, porém inicialmente aplicamos a suposição em ser FALSA. Ou seja, pra isso o nosso teste estatístico aplicado deveria apresentar evidências significativas para comprovar isso com valor-p \< 0.05. Como o resultado apresentou ser maior, portanto, concluímos que que não podemos rejeitar o H0, logo segue uma distribuição normal.

------------------------------------------------------------------------

### Validando a suposição 5 (Teste F de Snedecor)

"A variância residual é homogênia (princípio da homocedastidade)"

*Suposição: Falsa"*

*Teste F: Verifica se a média das amostras tem a mesma variância (ou que não há diferença significativa nas variâncias das médias)*

*Hipóteses:*

-   H0: As médias de dados extraídos de uma população normalmente distribuídas tem a mesma variância;

-   H1: As médias de dados extraídos de uma população normalmente distribuídas NÃO tem a mesma variância.

Na validação da suposição 4 já identificamos que os dados estão normalmente distribuídos.

```R
# Verificando se há valores ausentes
colSums(is.na(sleep))
```
| extra | group | ID |
|-------|-------|----|
|   0   |   0   |  0 |

```R
# Analisando as estatísticas
dt <- data.table::data.table(sleep)
dt[,.(Total = .N, Media = mean(extra), Sd = sd(extra)), by = group]
```

| group | Total | Media | Sd   |
|-------|-------|-------|------|
| 1     | 10    | 0.75  | 1.789|
| 2     | 10    | 2.33  | 2.002|


```R
# Aplicando o teste F
teste_f <- var.test(extra ~ group, data = sleep)
teste_f
```
|   Test Data      |    Result         |
|-------------------|------------------|
| **F test to compare two variances** |                  |
| Data              | extra by group   |
| F                 | 0.8              |
| num df            | 9                |
| denom df          | 9                |
| p-value           | 0.7              |
| Alternative hypothesis | true ratio of variances is not equal to 1 |
| 95 percent confidence interval | 0.1983 to 3.2141 |
| Sample estimates  | ratio of variances = 0.7983 |


valor-p \> 0.05. Portanto, falhamos em reiejtar H0, não há diferença significativa entre as médias dos dois grupos. Portanto, as médias possuem a mesma variância.

------------------------------------------------------------------------

Todas as suposições foram validadas com sucesso! Portanto, podemos aplicar o Teste T- Student

*Teste t: Usado para comparar a média de dois grupos*

*Hipóteses*

-   H0 (Hipótese nula): Não há diferença significativa entre as médias dos 2 grupos.

```R
# Aplicando o Teste T
teste_t <- t.test(extra ~ group, data = sleep, 
                  var.equal = TRUE) 
teste_t
# VAR.QUAL = TRUE, pois realizamos a validação com o teste F já, anteriormente.
```
|         Test Data         |        Result          |
|------------------------------|------------------|
| **Two Sample t-test**        |                  |
| Data                         | extra by group   |
| t                            | -1.9             |
| df                           | 18               |
| p-value                      | 0.08             |
| Alternative hypothesis       | true difference in means between group 1 and group 2 is not equal to 0 |
| 95 percent confidence interval | -3.3639 to 0.2039 |
| Sample estimates             | mean in group 1: 0.75, mean in group 2: 2.33 |


valor-p = 0.08 \> 0.05. Portanto, falhamos em rejeitar a H0! Ou, podemos concluir que os 2 grupos não tem diferença significativa entre os medicamentos aplicados para tratar distúrbios de sono.
