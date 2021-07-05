# Coleta TCPDUMP
## Autores
Arthur A. Vianna e Nilson L. Damasceno

03/07/2021

## Objetivo
Este documento tem como objetivo expor a estratégia que será utilizada para coleta de tráfego.

## Gerando arquivo
Para minimizar a perda de pacotes, devido ao tráfego intenso, decidiu-se gerar um arquivo binário(.pcap) utilizando o parâmetro "-w" e, em seguida, usando o parâmetro "-r" gerar um arquivo texto(.txt) a partir do arquivo binário gerado na etapa anterior. Nas subseções a seguir temos exemplos destas etapas.

É importante ressaltar que, no momento que geramos o arquivo texto, utilizando o parâmetro "-r", podemos também aplicar filtros no arquivo binário para gerar um txt que contenha apenas o tráfego de interesse.

### Gerando arquivo binário

`sudo timeout 30 tcpdump -n -i bond1 -tttt -vvv not net 10.0.0.0/8 and "(tcp[tcpflags] == tcp-syn and ether src cc:4e:24:42:55:0d) or udp port 53" -w syn_dns_ether_30s.pcap`

Com este comando foi feita uma coleta na interface bond1, aplicando os seguintes filtros:
* Ignorar tráfego da máscara 10.0.0.0/8;
* Do tráfego TCP capturar apenas pacotes SYN e que tenham como origem a interface de rede cc:4e:24:42:55:0d;
* Do tráfego UDP capturar apenas os que usam a porta 53(origem e/ou destino).

### Gerando arquivo texto

`sudo tcpdump -tttt -vvv -n -r syn_dns_ether_30s.pcap > syn_dns_ether_30s.txt`

Com este comando foi gerado um arquivo texto a partir do arquivo binário.

Mas como mencionado anteriormente, podemos aplicar filtros nesta etapa também, abaixo temos uma versão modifcada do exemplo acima.

`sudo tcpdump -tttt -vvv -n udp src port 53 -r syn_dns_ether_30s.pcap > syn_dns_ether_30s.txt`

Com este comando estamos gerando um arquivo txt que contém apenas pacotes UDP com porta de origem 53, ou seja, respostas DNS.
