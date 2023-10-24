# Regras de Negócio

Regras de negócio que deverão ser aplicadas.

## 1 - Download das Informações <br>

* Origem das Informações: [Site ANP](https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/serie-historica-de-precos-de-combustiveis)
* Período: 2022 e 2023
    * [1º Semetre de 2022](https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/precos-semestrais-ca.zip)
    * [2º Semetre de 2022](https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/ca-2022-02.zip)
    * [3º Semetre de 2023](https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/ca-2023-01.zip)
* Link para realizar o download.
    * https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/precos-semestrais-ca.zip
    * https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/ca-2022-02.zip
    * https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/ca-2023-01.zip


## 2 - Tratamento dos Dados <br><br>

* 2.1 - Definir quais colunas serão importadas;
* 2.2 - Ajustar a nomenclatura das colunas;
* 2.3 - Definir o DataType das colunas;
* 2.4 - Tratar colunas de data;
* 2.5 - Tratar colunas nulas;
    * 2.5.1 - Tratar colunas nulas - Colunas do Tipo String, informar 'NI';
    * 2.5.2 - Tratar colunas nulas - Colunas do Tipo Numérico, informar 0 (zero);
* 2.6 - A partir da coluna Data Coleta, criar outras três colunas:
    * 2.6.1 - AnoMes, formato AAAAMM;
    * 2.6.2 - Ano, formato AAAA;
    * 2.6.3 - Mes, formato MM;
* 2.7 - Criar coluna "Endereço Completo" com o formato:
    * 2.7.1 - Coluna("Nome da Rua") + ", NR: " + Coluna("Nome da Rua") + ', COMPLEMENTO: ' + Coluna("Complemento") + ' - BAIRRO: ' + Coluna("Bairro")
* 2.8 - Tratar o DataType da Coluna "Valor de Venda" para numérico;
* 2.9 - Eliminar a Coluna "Valor de Compra";

## 3 - Filtrar Dados Utilizados <br><br>

* 3.1 - Dados dos Estados "RJ", "SP", "CE" e "MA", onde:

    * 3.1.1 - Estado "CE", Municipio "JUAZEIRO DO NORTE";
    * 3.1.2 - Estado "MA", Municipio "IMPERATRIZ";
    * 3.1.3 - Estado "RJ", Municipio "SAO GONCALO";
    * 3.1.4 - Estado "SP", Municipio "BARUERI";

    | Estado | Município |
    | ------ | --------- |
    | Ceará - CE | Juazeiro do Norte |
    | Maranhão - MA | Imperatriz |
    | Rio de Janeiro - RJ | São Gonçalo |
    | São Paulo - SP | Barueri |

## 4 - Calcular a média de venda de cada combustivel <br><br>

* 4.1 - Agrupar os dados pelas colunas:

    * Regiao
    * UF
    * NomMunicipio
    * NomBairro
    * NomRevenda
    * NomEnderecoCompleto
    * DscProduto
    * AnoMes
    * Ano
    * Mes
    
* 4.2 - Calcular a média sobre a coluna "Valor de Venda":

## 5 - Pivot das Colunas (Transformar linhas em colunas) <br><br>

* 5.1 - Colunas que não deverão mudar:
    * Regiao
    * UF
    * NomMunicipio
    * NomBairro
    * NomRevenda
    * NomEnderecoCompleto
    * DscProduto
* 5.2 - Linha que vai virar coluna:
    * AnoMes
    * VlrMedio
* 5.3 - Colunas com valores nulos deverão ser preenchidas com zero:

## 6 - Envio Informações <br><br>

* 6.1 - O envio do e-mail deverá obedecer a seguinte regra:

    * 3.1.1 - Estado "CE", xpto_ce@empresatransporte.com.br;
    * 3.1.2 - Estado "MA", xpto_ma@empresatransporte.com.br;
    * 3.1.3 - Estado "RJ", xpto_rj@empresatransporte.com.br;
    * 3.1.4 - Estado "SP", xpto_sp@empresatransporte.com.br;

    | Estado | Município | E-mail |
    | ------ | --------- | ------ |
    | Ceará - CE | Juazeiro do Norte | xpto_ce@empresatransporte.com.br |
    | Maranhão - MA | Imperatriz | xpto_ma@empresatransporte.com.br |
    | Rio de Janeiro - RJ | São Gonçalo | xpto_rj@empresatransporte.com.br |
    | São Paulo - SP | Barueri | xpto_sp@empresatransporte.com.br |
