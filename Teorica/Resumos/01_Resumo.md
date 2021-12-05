	# Redes e IP

## Index

- [Classes de endereços](#classes-de-endereços)
- [Endereços IP reservados](#endereços-ip-reservados)
- [Notação em decimal e gamas](#notação-em-decimal-e-gamas)
- [Máscaras](#máscaras)
- [Atribuição de endereços IP](#atribuição-de-endereços-ip)
- [Encapsulamento de IP Datagrams](#encapsulamento-de-ip-datagrams)
- ...

## Classes de endereços

| Classes \ bits | 0 | 7 | 15 | 23 | 31 |
| --- | :--- | :--- | :--- | :--- | :--- |
| **A** | 0  netid | >hostid |||>
| **B** | 10 netid || > hostid ||>
| **C** | 110 netid |||> hostid | >
| **D** | 1110 endereço multicast |||| >
| **E** | 1111 reservado para utilização futura |||| >

- Endereços IPv4 são de 4 bytes
- Classes **A**, **B** e **C** podem ser atribuidas a terminais, são **unicast**
- **netid**: identifica a subnet de IP
- **hostid**: identifica o host nessa subnet de IP
- **Unicast**: apenas 1 host
- **Multicast**: multiplos hosts

### O número máximo de redes é é dado pela gama total do **netid** desse IP

| Classes | Nº bits netid | Gama total |
| --- | --- | --- |
| **A** | 7 | 128 |
| **B** | 14 | 16.384 |
| **C** | 21 | 2.097.152 |

### O número máximo de hosts é é dado pela gama total do **hostid** desse IP

| Classes | Nº bits hostid | Gama total |
| --- | --- | --- |
| **A** | 24 | 16.777.216 |
| **B** | 16 | 65.536 |
| **C** | 8 | 256 |
---

## Endereços IP reservados

| Classes | 0 | 7 | 15 | 31
| --- | --- | --- | --- | --- |
| **Este Host**[^1] | 0 |||>
| **host NESTA REDE**[^1] | 0 ||>host|>
| **BROADCAST LOCAL**[^2] | 1 |||>
| **BROADCAST DIRIGIDO para net**[^2] | net ||> 1|>
| **LOOPBACK**[^3] | 127 |> qualquer (em geral 1)||>
| **ESTA net**[^4] | net ||> 0 |>

[^1]: Permitido apenas na inicialização; nunca é endereço destion válido
[^2]: Nunca é endereço origem válido
[^3]: Nunca deve aparecer na rede
[^4]: Reservado para designar a rede

## Notação em decimal e gamas

| Classes | limite inferior | limite superior |
| --- | --- | --- |
| **A** | 1.0.0.0 | 126.0.0.0 |
| **B** | 127.0.0.0 | 191.255.0.0 |
| **C** | 192.0.0.0 | 223.255.255.0 |
| **D** | 224.0.0.0 | 239.255.255.255 |
| **E** | 240.0.0.0 | 255.255.255.254 |
---

## Máscaras

- Inicialmente os tamanhos do netid e do hostid tinham tamanho fixo correspondente a classe a que pertenciam
- Chegousse á conclusão que o numero de endereços da subnet da classe A era demasiado grande e da classe C demasiado pequeno
- A **netmask** é composta por **4 bytes** e é sempre constituida por uma sequência de 1s e depois uma sequência de 0s
- Os 1s definem o **netid** e os 0s o **hostid**
- Para alem de dar possibilidade de escolher o tamanho do netid e do hostid, cada host usa a máscara para determinar o IP da subnet onde está ligado, fazendo um **bitwise AND entre o IP e a máscara**.

<br>

## Atribuição de endereços IP

### Regras

- Todas as interfaces ligadas na **mesma rede** têm de ter **netid igual** e **hostid diferente**
- Interfaces em redes **diferentes** têm de ter **netid diferente**

### Tipos de hosts

- **Single-homed host**: host com uma única interface;
- **Multi-homed host**: host com mais que uma inteface, mas estas não são capazes de trocar informação entre si;
- **Router**: São como os multi-homed hosts, mas estas conseguem trocar informação entre interfaces.

<br>

## Encapsulamento de IP Datagrams

- O protocolo IP envia informação em forma de datagrams.
- Cada bloco de bytes, o protocolo IP concatena um header, é esta concatenação que forma o **IP Datagram**.
- Cada IP Datagram é entre á camada **MAC** de baixo para ser enviado para a rede.
- Em cada rede a **MAC layer frame** é composta por um campo **header** e por um campo **data**.
- No campo **data** do MAC é encapsulado o **IP Datagram**.

### Formato

- **IP Header**
  - Tem um tamanho total de **20 bytes**
  - Os últimos dois campos obrigatório conteem o **IP origem** e o **IP destino**
  - Campos:
    - **Version (4 bits)**: versão do protocolo de IP
    - **Header length (4 bits)**: tamanho do header em múltiplos de 4 bytes
    - **Service type (1 byte)**: tipo de serviço do datagram de IP (default é 0x00)
    - **Total length (2 bytes)**: tamanho do datagram de IP (header + data), o tamanho tem sempre de ser menor que o **MTU** do MAC layer frame onde vai ser encapsulado
  - **Identificação (2 bytes)**: o valor atribuido ao host original do IP datagram
  - **Flags (3 bits)**:
    - 1 bit é reservado para uso futuro
    - **2 bit** é o **do not fragment bit**, e está a **1** se a **origem não permite** que o datagram de IP seja **fragmentado**
    - **3 bit** é o **last fragment bit**, irá estar a **0** se o IP datagram onde se encontra for o **último fragmento** de um IP datagram
  - **Fragment Offset (13 bits)**: posição (em multiplos de 8 bytes) deste fragmento no fragmento original do IP datagram. Este campo indica quantos bytes estão na totalidade dos fragmentos anteriores
  - **Time to Live (1 byte)**: Representa o número de **hops maximos** que este pacote (IP datagram) pode dar entre routers **antes de ser descartado**. **Cada router** por onde passa **subtrai 1** a este valor
  - **Protocol (1 byte)**: especifica a camada de protocolo mais alta a que o data field pertence
  - **Header Checksum (2 bytes)**: resultado da soma (em words de 16 bits) dos outros campos do header, permite detetar erros á chegada 
- **MTU** - *Maximum Transmission Unit* - é o tamanho máximo do data field to MAC layers frame

### Fragmentação e reassemblagem em IP

Acontece que o IP datagram for **maior que o MTU** da rede, o pacote terá de ser fragmentado, isto poderá ser feito pelo **host ou pelo router**

1. O campo do **data** é segmentado de forma a que os seus blocos **mais o header** não seja maior que o **MTU**. Cada bloco mais um header vai formar um IP datagram
2. O campo de **identificação** dso blocos será igual entre todos
3. O campo de **offset** será *byte* a partir do inicio do primeiro pacotes
4. A **flag** do ultimo pacote será **1** para identificar que é o **ultimo**, todos os outros a flag será **0**

<br>

## ARP - Address Resolution Protocol

- Tecnologias diferentes têm diferentes adereços, o standartizado pela IEEE é cada endereço tem **6 bytes**
- Fabricantes codificam esses endereços unicos nos **NIC's** (Network Interface Card)
- Para um host enviar um pacote a outro, o origem tem de codificar no header o **MAC address** próprio e do destino, para isso, o **origem envia** um pacote **ARP Request** a todos os hosts a pedir o MAC address do host com o endereço IP conhecido. Se estes host estiver ativo, irá **responder ao ARP Request**.
- O mapeamento entre **MAC e IP** descobertos pelo protocolo ARP são **temporariamente guardados**.

### ARP Request

- Pacotes ARP são **encapsulados** em MAC layer frames.
- O pacote **ARP Request** especifica:
  - **MAC address origem** e **IP origem**
  - **IP destino**
  - Campo para **MAC destino** vazio

### ARP Response
- Especifica:
  - O seu **MAC address** e o seu **IP**
  - O **MAC address destino** e o seu **IP**

<br>

## Switching

- Ligações de estações sem colisões
- Largura de banda agregada não é limitada pela taxa de transmissão das portas

### Tabela de Encaminhamento

Cada switch contem uma tabela que identifica quais terminais estão na sua rede quais os seus dados, para depois poder enviar pacotes para esses terminais

**MAC address | interface | TTL**

Quando um switch **recebe** um pacote com endereço de destino **desconhecido**, o switch irá fazer **flood**, enviando o pacote a todos os sistemas na rede, e assim que o destinatário o receber e responder, o switch ai aprender os seus dados e adicioná-lo á **arp table**

### Filtragem/forwarding

Sequência de eventos quando uma trama é recebida por um switch

1. Regista na **tabela de encaminhamento** a interface do **emissor**
2. Procura na tabela de encominhamento uma **entrada** com o **endereço MAC** de destino
3. if entrada encontrada {
		if **emissor** = **destino** {
			descarta a trama
		}else{
			**Forwarding**
		}
	}else{
		**Flooding**
	}

<br>

## Do host origem ao primeiro router

Quando um host IP tem um pacote IP para um destinatario IP, o host compara os **netid** do destinatário como o seu, se forem **diferentes**, o host sabe que não estão localizados na mesma rede e **envia** o pacote para a **Default Gateway**.

Pata ter conectividade global, um host IP tem de ter configurado o **IP da sua Default Gateway**. Este adereço tem de estar atribuido a uma interface network de um router connectado rede do host.

## IP Routing

Os routers são responsáveis por **encaminhar os pacotes** (IP datagrams) pelas redes até ao host de destino.

Para conseguir isso, cada router cria uma **tabela** que corresponde **cada porta** sua de saida para **um endereço de destino**.

A tabela que o router cria tem o seguinte formato

| Destino | Máscara | next hop | interface |
| --- | --- | --- | --- |
| 30.0.0.0 | 255.0.0.0 | 40.0.0.7 | 1 |
| 192.4.10.0 | 255.255.255.0 | 128.1.0.9 | 2 |
| ... |

- A rede de destino é definida pelo **IP destino e pela netmask**
- A *interface* especifica o porto de saída associado a esse IP
- No *next hop* é especificado o endereço IP do proxímo router
- Se a rede de destino está diretamente ligada ao router, o IP do next hop é omitido

### Passos para a transmissão de pacotes entre host origem e destino
1. Se necessário, o host **origem** descobre, atraves de **ARP** o endereço **MAC** da sua **Default Gateway**
2. O pacote IP é encapsulado na **MAC layer frame** e é enviado para a rede
3. O que cada router até ao destino deve fazer:
    - **Desencapsula** o pacote IP do **MAC layer frame** recebido
    - Procura na sua **tabela de routing** a **porta** e o **next hop IP** de saida associados ao **IP destino**
    - Se necessário, envia **ARP Request** para descobrir o **MAC address** do próximo router
    - O pacote IP é novamente encapsulado num MAC layer frame, mas agora com o MAC address do próximo router
4. O último router:
    - Desencapsula o pacote IP do MAC layer frame recebido
    - Procura na **tabela de routing**  a porta associada ao host destinatário ligado a sua rede
    - Se necessário envia ARP Request
    - Encapsula novamente o pacote IP num MAC layer address com o MAC address do host destino 

<br>

## ICMP

- Permite a troca de mensagens de controlo e diagonóstico
- Os pacotes ICMP são **encapsulados** em **pacotes IP**
  - Os primeiros 3 campos são comuns a todos os pacotes ICMP
    - **type** (1 byte)
    - **code** (1 byte)
    - **chekcsum** (2 bytes)  
- O campo *Checksum* é determinado com base em toda a mensagem

### Tipos de mensagens ICMP

| Campo type | Significado |
|:---:|:---:|
|0|Echo reply|
|3|Destination unreachable|
|4|Source quench|
|5|Redirect|
|8|Echo request|
|11|Time exceeded|
|12|Parameter problem
|13|Timestamp request
|14|Timestramp reply|

### ICMP Redirect

Quando um router deteta que uma estação esta a usar uma rota que não é a melhor, envia uma mensagem **ICMP Redirect* para que ele mude de rota

O router inicial, para além do **ICMP Redirect**, envia também o datagrama original para o destino

Não possibilita mudanças de **rotas entre routers**; apenas entre um host e um router na mesma rede

O campo **code** é usado para passar mais informação
- **0** - Datagrama de redirect para a **rede**
- **1** - Datagrama de redirect para o **host**
- **2** - Datagrama de redirect para **Type of Service** e para **rede**
- **3** - Datagrama de redirect para **Type of Service** e para **host**

### ICMP Destination Unreachable

Existem **6** valores possiveis para o campo **code**

- **0** - **Net Unreachable** - enviado pelo router se este não sober a rota para a rede pedida
- **1** - **Host Unreachable** - enviado pelo router quando sabe a rota mas não o host
- **2** - **Protocol Unreachable** - enviado pelo host destino, se o protocolo destino não funcionar
- **3** - **Port Unreachable** - enviado pelo host destino, se a porta estiver ""vazia"
- **4** - **Cannot Fragment** - enviado pelo router, se for necessário fragmentar, mas o bit "do not fragment" estiver a 1
- **5** - **Source Route Failed** - o IP Source routing é um dos IP Header Options

### ICMP Time Exceeded message

É enviado por um **router** para a **origem** quando o **TTL** do pacote chega a **0**

#### Comando Tracert

Permite descobrir o percurso utilizado no encaminhamento dos pacotes

Recorre ao campo **TTL** e a mensagens **ICMP Time Exceeded**

Envia ICMP Echo requests, 3 de cada vez, com **TTL** inicial de **1**, e incrementa gradualmente **TTL+=1**, sempre que a resposta for **Time exceeded** é descoberto um novo **router**, isto é repetido **ate deixar de receber** a mensagem de **Time exceeded**

<br>

## Subredes

Uma **subereds** é um **subconjunto de uma rede** de classe A, B ou C

A utilização de **máscaras, permite que uma rede seja dividida** em subredes estendendo a parte da rede à parte de **host do endereço IP**; esta técnica aumenta o número de resdes e reduz o número de hosts