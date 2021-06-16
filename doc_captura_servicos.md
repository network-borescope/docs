# Filtro de múltiplos serviços.

O código fonte se encontra em `/home/borescope/tools/filter_select_file/fonte`
Os arquivos gerados são armazenados em `/home/borescope/tools/filter_select_file/fonte/data`
Os arquivos de saída são gerados a cada 5 minutos até o fim da coleta.
##Comando para coleta:
```
cd /home/borescope/tools/filter_select_file/fonte
sudo tcpdump -l -U -vvv -n -tttt -c 10000000 -i bond1 "$@"  | ./filter_c
```
10000000 gera apenas uma coleta de aproximadamente 5 min.
A interface bond1 é a junção das interfaces de entrada e saída.
