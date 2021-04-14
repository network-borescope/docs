# Objetivo
Estudar as respostas de consultas DNS.

## Estratégia
Gerar um arquivo txt a partir da saída do TcpDump e em seguida analisá-lo, para a geração deste arquivo foi executado o script abaixo, com ele deviríamos capturar 10000 pacotes que seriam de tráfego DNS(request e response) e WEB(apenas request).
```Shell
#!/bin/sh
FILE=`date +dns_web_%Y%m%d_%H%M%S.txt`
current_directory=`pwd`
INTERFACE="enp6s0f1"
N=10000
DNS="udp port 53"
WEB="tcp dst port 80 or tcp dst port 443"

sudo tcpdump -i "$INTERFACE" -l -U -vvv -n -tttt -c "$N" "$DNS" or "$WEB" > "$current_directory/data/$FILE"
```

## Análise
Com o arquivo de coleta em mãos observou-se que apesar de termos requisições DNS para diversos servidores DNS, nenhum dos servidores DNS públicos mais utilizados(8.8.8.8, 8.8.8.4 e 1.1.1.1) estavam enviando respostas. Com base nisso levantamos uma hipótese.

## Hipótese
**Estamos capturando apenas respostas de servidores DNS internos.**

### Prova
A prova da hipótese levantada se divide em 4 passos.
#### 1) Utilizando o comando ```nslookup``` descobrir os servidores cujas respostas foram capturadas
**Resultado**(no formato ip hostname):
* 200.192.233.10 c.dns.br
* 200.192.232.14 e.sec.dns.br
* 200.130.3.138 dns2.mec.gov.br
* 200.130.19.38 ns2.capes.gov.br
* 200.130.18.193 ns3.capes.gov.br
* 200.130.77.69 lua.na-df.rnp.br
* 172.17.61.159 Not Found
* 177.8.81.232 Not Found
* 164.41.101.6 dns2.unb.br
* 177.8.81.233 dns2.eb.mil.br
* 200.192.232.11 b.sec.dns.br
* 200.130.11.91 ns3.fiocruz.br
#### 2) Tentar capturar tráfego que tenha como origem um DNS externo.
```sudo tcpdump -i enp6s0f1 -n -vvv -tttt -c 20 src 8.8.8.8```

Com este comando deviríamos capturar 20 pacotes que tivessem como origem o servidor dns 8.8.8.8 do google.

**Resultado: nenhum pacote foi capturado**
#### 3) Tentar capturar tráfego que tenha como origem determinada rede.
```sudo tcpdump -i enp6s0f1 -n -vvv -tttt -c 20 src net 8.8.0.0/16```

Com este comando deviríamos capturar 20 pacotes de que tivessem como origem qualquer servidor dns do google(8.8.8.8 ou 8.8.4.4)

**Resultado: nenhum pacote foi capturado**
#### 4) Flexibilizar as regras da ```iptables``` e repetir os passos 2 e 3
Para flexibilizar as regras executamos os comandos abaixo.

```sudo iptables -P INPUT ACCEPT```

```sudo iptables -P OUTPUT ACCEPT```

```sudo iptables -P FORWARD ACCEPT```

**Resultado: nenhum pacote foi capturado**
## Conclusão
Por fim concluímos que a hipótese é verdadeira, pois no primeiro passo verificamos que todos os servidores DNS que capturamos resposta eram internos e em nenhum dos passos seguintes conseguimos coletar respostas de servidores DNS externos. Como trabalhos futuros temos que levantar novas hipóteses, desta vez sobre o porque não conseguimos realizar a captura destes pacotes, e testá-las.

*OBS: Todos os procedimentos aqui descritos foram realizados na máquina PoP DF no dia 12/04/2021*