# Fluxo

## 0 - Bibliotecas

```
import warnings
import pandas as pd
import os
```

## 1 - Importar Notebook com as Bibliotecas

```
%run bibliotecas.ipynb
```

## 2 - Download dos Arquivos

```
link = ['https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/precos-semestrais-ca.zip'
         ,'https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/ca-2022-02.zip'
         ,'https://www.gov.br/anp/pt-br/centrais-de-conteudo/dados-abertos/arquivos/shpc/dsas/ca/ca-2023-01.zip'
       ]

arquivo = ['arquivo_primeiro_semestre_2022.zip'
           ,'arquivo_segundo_semestre_2022.zip'
           ,'arquivo_primeiro_semestre_2023.zip']

diretorio = fr'C:\Temp\Python_YT\Download_File\Combustiveis'

fromaddr = "dbaassists@gmail.com"

toaddr = "dbaassists@gmail.com"

download_arquivo(link, arquivo, diretorio, fromaddr, toaddr)
```


## 3 - Descompactar Arquivos

```
diretorio_zip = fr'C:\Temp\Python_YT\Download_File\Combustiveis'
diretorio_unzip = fr'C:\Temp\Python_YT\Download_File\Combustiveis\output'

descompacta_arquivo(diretorio_zip,diretorio_unzip)
```

## 4 - Ingestão dos Arquivos

```
diretorioarquivo = fr'C:\Temp\Python_YT\Download_File\Combustiveis\output'
separador=';'
cabecalho=0
colunas=[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]
nomecolunas=['Regiao'
             ,'UF'
             ,'NomMunicipio'
             ,'NomRevenda'
             ,'NumCNPJRevenda'
             ,'NomLogradouro'
             ,'NrEndereco'
             ,'NomComplemento'
             ,'NomBairro'
             ,'NumCep'
             ,'DscProduto'
             ,'DtColeta'
             ,'VlrVenda'
             ,'VlrCompra'
             ,'UnMedida'
             ,'DscBandeira'
            ]
tipodado={'Regiao':'str'
,'UF':'str'
,'NomMunicipio':'str'
,'NomRevenda':'str'
,'NumCNPJRevenda':'str'
,'NrEndereco':'str'
,'NomLogradouro':'str'
,'NomComplemento':'str'
,'NomBairro':'str'
,'NumCep':'str'
,'DscProduto':'str'
,'DtColeta':'str'
,'VlrVenda':'str'
,'VlrCompra':'str'
,'UnMedida':'str'
,'DscBandeira':'str'
}
colunatipodata=['DtColeta']

dfCombustivel = ingestao_arquivos(diretorioarquivo
                       , separador
                       , cabecalho
                       , colunas
                       , nomecolunas
                       , tipodado
                       , colunatipodata)
```


## 5 - Tratamento dos Dados

```
#TRATAMENTO DE VALORES NULOS    
dfCombustivel['NomComplemento'].fillna('NI',inplace=True)
dfCombustivel['NomLogradouro'].fillna('NI',inplace=True)
dfCombustivel['NomBairro'].fillna('NI',inplace=True)
dfCombustivel['VlrCompra'].fillna('0',inplace=True)

#CRIAÇÃO DE COLUNAS NOVAS DE TEMPO - ANO, MES, ANOMES
dfCombustivel['Ano'] = dfCombustivel['DtColeta'].dt.strftime('%Y')
dfCombustivel['Mes'] = dfCombustivel['DtColeta'].dt.strftime('%m')
dfCombustivel['AnoMes']  = dfCombustivel['DtColeta'].dt.strftime('%Y%m')

#CRIAÇÃO DE COLUNA ENDERECO COMPLETO 
dfCombustivel['NomEnderecoCompleto'] = dfCombustivel['NomLogradouro'] \
                                        + ', NR: ' \
                                        + dfCombustivel['NrEndereco'] \
                                        + ', COMPLEMENTO: ' \
                                        + dfCombustivel['NomComplemento'] \
                                        + ' - BAIRRO: ' \
                                        + dfCombustivel['NomBairro']

dfCombustivel['NomEnderecoCompleto'].fillna('NI',inplace=True)

#CONVERSÃO DE TIPO DE DADOS
dfCombustivel['VlrCompra'] = dfCombustivel['VlrCompra'].str.replace(',', '.').astype(float)
dfCombustivel['VlrVenda'] = dfCombustivel['VlrVenda'].str.replace(',', '.').astype(float)
```

## 6 - Cálculo do Preço Médio de Combustiveis

```
resultEstadosAtuantes = dfCombustivel[((dfCombustivel['UF']=='CE') & (dfCombustivel['NomMunicipio']=='JUAZEIRO DO NORTE')) |
                                     ((dfCombustivel['UF']=='MA') & (dfCombustivel['NomMunicipio']=='IMPERATRIZ')) |
                                      ((dfCombustivel['UF']=='RJ') & (dfCombustivel['NomMunicipio']=='SAO GONCALO')) |
                                      ((dfCombustivel['UF']=='SP') & (dfCombustivel['NomMunicipio']=='BARUERI')) 
                                     ].groupby(['Regiao'
                                                ,'UF'
                                                ,'NomMunicipio'
                                                ,'NomBairro'
                                                ,'NomRevenda'
                                                ,'NomEnderecoCompleto'
                                                ,'DscProduto'
                                                ,'AnoMes'
                                                ,'Ano'
                                                ,'Mes'
                                                ],as_index=False).agg(VlrMedio=('VlrVenda','mean'))
```

## 7 - Pivot da Tabela de Dados

```
resultRelatorioFinal = resultEstadosAtuantes.pivot(index=['Regiao'
                                                ,'UF'
                                                ,'NomMunicipio'
                                                ,'NomBairro'
                                                ,'NomRevenda'
                                                ,'NomEnderecoCompleto'
                                                ,'DscProduto'
                                                ], columns="AnoMes", values="VlrMedio").fillna(0).reset_index()
```

## 8 - Envio das Informações

```
listaUF = resultRelatorioFinal['UF'].unique()

resultRelatorioFinal
diretorioArquivo = 'C:\Temp\Python_YT\Download_File\Combustiveis\enviado'
fromaddr = "dbaassists@gmail.com"
toaddr = "dbaassists@gmail.com"
listaUF

envia_arquivo_unidades(resultRelatorioFinal, diretorioArquivo ,fromaddr ,toaddr ,listaUF)
```