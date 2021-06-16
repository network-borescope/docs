# Filtro de múltiplos serviços.

O código fonte se encontra em `/home/borescope/tools/filter_select_file/fonte`.

Os arquivos gerados são armazenados em `/home/borescope/tools/filter_select_file/fonte/data`.

Os arquivos de saída são gerados a cada 5 minutos até o fim da coleta.

## Comando para coleta:
```
cd /home/borescope/tools/filter_select_file/fonte
sudo tcpdump -l -U -vvv -n -tttt -c 10000000 -i bond1 "$@"  | ./filter_c
```
10000000 gera apenas uma coleta de aproximadamente 5 min.

A interface bond1 é a junção das interfaces de entrada e saída.

## Saída
```
// key1 = yyyy-mm-dd hh:nn:00; id_cliente; src_ip; val_ttl; proto; serv; port_dst; dst_ip; port_src;count
```
1. `yyyy-mm-dd hh:nn:00` - Data hora do mensagem.
2. `id_cliente` - ID do cliente da RNP, 1 se o cliente é externo a rede. 
3. `src_ip` - IP do remetente da mensagem.
4. `val_ttl` - Time to Live da mensagem.
5. `proto` - Protocolo de rede. 6 = tcp e 17 = udp.
6. `serv` - ID para representar pares protocolo e porta do mesmo serviço. 1 = DNS, 2 = Web, 3 = smtp, 4 = pop3, 5 = imap, 6 = ftp e 7 = ssh.
7. `port_dst` - Porta do destinatário. Referente ao serviço.
8. `dst_ip` - IP do destinatário.
9. `port_src` - porta do remetente.
10. `count` - número de repetições de mensagens com as mesmas informações.
