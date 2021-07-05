# Contagem da Ocorrência de Cada Porta
## Autores
Arthur A. Vianna e Nilson L. Damasceno

03/07/2021

## Objetivo
Este documento tem como objetivo apresentar o passo a passo para gerar um arquivo que contenha apenas a porta de destino e a quantidade occorrências da mesma, tendo como entrada um arquivo "input.txt", que foi gerado a partir de uma coleta usando o TCPDUMP.

## Gerando Arquivo

### Passo 1

Com este comando será gerado um arquivo que contém apenas a linha que possui a informação da porta de cada pacote.

`grep "^    " input.txt > output.txt`

### Passo 2
Com este comando será gerado um arquivo que contém apenas a parte da linha que possui as informações de ip de origem e destino, e porta de origem e destino.

`cut -d ":" -f 1 output.txt > saida2.txt`

### Passo 3
Com este comando será gerado um arquivo que contém apenas a parte da linha que possui a informação do ip de destino e da porta de destino.

`cut -d ">" -f 2 saida2.txt > saida3.txt`

### Passo 4
Com este comando será gerado um arquivo que contém apenas a porta de destino.

`cut -d "." -f 5 saida3.txt > saida4.txt`

### Passo 5
Com este comando será gerado um arquivo ordenado que contém apenas a porta de destino.

`sort saida4.txt > saida5.txt`

### Passo 6
Com este comando será contabilizado quantas vezes cada linha se repetiu, ou seja, a quantidade de ocorrências de cada porta, o arquivo gerado terá como prefixo de cada porta, separado por espaço em branco, a quantidade de vezes ela ocorreu.

`uniq -c saida5.txt > saida6.txt`

### Passo 7(Opcional)
Este passo está marcado como opcional pois ele simplesmente ordena o arquivo em ordem decrescente de ocorrência da porta.

`sort -nr saida6.txt > saida7.txt`

## Podemos combinar todos os passos usando pipe(|)

`grep "^    " input.txt | cut -d ":" -f 1 | cut -d ">" -f 2 | cut -d "." -f 5 | sort | uniq -c | sort -nr > output.txt`
