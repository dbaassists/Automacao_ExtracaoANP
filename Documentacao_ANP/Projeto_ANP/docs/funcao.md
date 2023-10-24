# Códigos

## 0 - Bibliotecas

```
import warnings
import pandas as pd
import os
import seaborn as sns
import matplotlib.pyplot as plt

```

## 1 - Notificação de Erro 

```
def envia_notificacao_erro(mensagem, detalhe_msg, link, fromaddr, toaddr):

    msg = MIMEMultipart() 
    msg['From'] = fromaddr 
    msg['To'] = toaddr 

    msg['Subject'] = mensagem
    body = detalhe_msg

    msg.attach(MIMEText(body, 'plain')) 

    s = smtplib.SMTP('smtp.gmail.com', 587) 
    s.starttls() 
    s.login(fromaddr, "") 
    text = msg.as_string() 
    s.sendmail(fromaddr, toaddr, text) 
    s.quit()   
```

## 2 - Download dos Arquivos

```
def download_arquivo(links, arquivo, diretorio, fromaddr, toaddr):

    try:

        for i in range(len(links)):

            nome_arquivo_local = diretorio+'\\'+arquivo[i]

            print(nome_arquivo_local)

            if requests.get(links[i]).status_code == 200:

                if not os.path.exists(diretorio):
                    os.makedirs(diretorio)

                response = requests.get(links[i])

                with open(nome_arquivo_local, 'wb') as arquivo_local:
                    arquivo_local.write(response.content)
                    
                    print(f'Download do arquivo realizado e salvo no diretório {nome_arquivo_local}')

            else:

                print("O servidor está indisponível.")
                mensagem = "[Ingestão ANP] Link inválido"
                detalhe_msg = "Olá tudo bem? \nLink {0} inválido.".format(links[i])
                envia_notificacao_erro(mensagem, detalhe_msg, links[i], fromaddr, toaddr)

    except Exception as e:

        print("Servidor Inexistente. Erro:", e)
        mensagem = "[Ingestão ANP] Erro Processamento - Erro Função download_arquivo()."
        detalhe_msg = "Olá tudo bem? \nOcorreu um erro ao executar a função download_arquivo().\n\nErro: {0}".format(e)
        envia_notificacao_erro(mensagem, detalhe_msg, links[i], fromaddr, toaddr) 
```

## 3 - Descompactar Arquivos

```
def descompacta_arquivo(diretorio_zip,diretorio_unzip):

    for arquivo_zip in os.listdir(diretorio_zip):

        print(arquivo_zip)

        if (os.path.splitext(arquivo_zip)[1]=='.zip') and ( os.path.isfile(fr'{diretorio_zip}\{arquivo_zip}')):

            # Abra o arquivo zip para leitura
            with zipfile.ZipFile(f'{diretorio_zip}\{arquivo_zip}', 'r') as zip_externo:

                zip_externo.extractall(path=diretorio_unzip) 
```

## 4 - Ingestão Arquivos

```
def ingestao_arquivos(diretorioarquivo
                      , separador
                      , cabecalho
                      , colunas
                      , nomecolunas
                      , tipodado
                      , colunatipodata):

    dfFinal = pd.DataFrame()

    for arquivo in os.listdir(diretorioarquivo):
        
        path = diretorioarquivo + '\\' + arquivo

        df = pd.read_csv(path
                        , sep=separador
                        , header=cabecalho
                        , usecols=colunas
                        , names=nomecolunas
                        , dtype=tipodado
                        , parse_dates=colunatipodata)
        
        dfFinal = pd.concat([dfFinal, df],ignore_index=True)

    return dfFinal 
```

## 5 - Envio de Arquivos para as Filiais

```
def envia_arquivo_unidades(df, diretorioArquivo ,fromaddr ,toaddr ,listaUF):

    for lista_uf in listaUF:
        
        df[df['UF']==lista_uf].to_csv('{0}\\lista_{1}.csv'.format(diretorioArquivo, lista_uf),sep=';',index=False)

        print('Arquivo Estado do ' + lista_uf + ' gerado com sucesso!')

        msg = MIMEMultipart() 
        msg['From'] = fromaddr 
        msg['To'] = toaddr 
        msg['Subject'] = "Arquivo de Variação dos Preços dos Combustíveis do Estado {0}".format(lista_uf)
        body = "Olá tudo bem? \nSegue em anexo o arquivo de Variação dos Preços dos Combustíveis.".format(lista_uf)
        msg.attach(MIMEText(body, 'plain')) 
        filename = "lista_{0}.csv".format(lista_uf)
        attachment = open("{0}\lista_{1}.csv".format(diretorioArquivo, lista_uf), "rb")
        p = MIMEBase('application', 'octet-stream') 
        p.set_payload((attachment).read()) 
        encoders.encode_base64(p) 

        p.add_header('Content-Disposition', "attachment; filename= %s" % filename) 
        msg.attach(p) 
        s = smtplib.SMTP('smtp.gmail.com', 587) 
        s.starttls() 
        s.login(fromaddr, "") 
        text = msg.as_string() 
        s.sendmail(fromaddr, toaddr, text) 
        s.quit()    
```