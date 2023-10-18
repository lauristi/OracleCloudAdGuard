
#### Oracle Free Tier  (rev. 2023)

Primeiro crie uma conta no serviço da oracle no modo gratuito (Free Tier)

[Oracle Free Tier](https://www.oracle.com/br/cloud/free/)


####Iniciando a montagem da VM:

Assim que estiver no ambiente do Oracle Cloud

1- Selecione Compute Instances no menu lateral para listar todas as VM´s
2- Clique em "Create Instance"


####Configurando a instancia

*** !Atenção  Atente sempre para o selo:  "Always Free-eligible" Ele é a garantia que o item escolhido não será cobrado.

Selecione a imagem do "SO" e "Shape" respectivamnte
- Amd64 (AMD micro)
- Ubuntu (mais atual)

Em Networking:
-  Selecione "Create new virtual network" -> "Create new public subnet"
-  Neste ponto certifique-se que "Assign a public IPv4 address" esteja marcada. isso vai garantir um IP fixo público para sua VM


SSH KEY
Crie as chaves: Publica e Private e salve-as localmente.
!!NÃO ESQUEÇA ESSA PARTE OU VOCÊ NÃO VAI CONSEGUIR ACESSAR SUA VM DEPOIS


VOLUME SIZE

Na opção do volume (seu HD virtual) marque "Specify a custom boot volume size" 
O default é mais que o suficiente para um server AdGuard
** Lembre-se que até a presente data o limite global é de 200 GB para todas as VMs criadas dentro do Always Free.

Finalizada as configuracões Clique em "Create" para criar a VM

#### Configurações de acesso

Na pagina principal com os dados da VM Criada:
- Cliquem nem "subnet da VM" 
- Na tela de configuração Clique no link:  "Default Security List"  Isso o lecará para a lista de regras de firewall.


#### Adicionando regras ao Firewall


Clique em "Add Ingress Rule para adicionar novas regras.

Adicione:
- 53/udp
- 80/tcp
- 443/tcp
- 3000/tcp
- 853/tcp


*** Pra toda regra o "Source CIDR" será "0.0.0.0/0" isso permitirá aceitar conmexões de qualquer lugar
   - Não preencham o campo "Source Port Range" e em "Destination Port Range" Coloque o numero da porta em questão


#### Conectando à VM

[Cliente SSH](https://www.bitvise.com)


No terminal da VM rode os seguintes comandos:

```
sudo apt update && sudo apt upgrade -y
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v
sudo /opt/AdGuardHome/AdGuardHome -s start
```
Você estará basicamente:

- Atualizando o Ubuntu
- Instalando o AdGuard
- Iniciando o AdGuard


Para prevenir problemas com Firewall do Ubuntu você pode rodar adcionalmente:

```
sudo su
iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
iptables -I INPUT 6 -m state --state NEW -p tcp --dport 3000 -j ACCEPT
iptables -I INPUT 1 -s 192.168.0.0/16 -p tcp -m tcp --dport 80 -j ACCEPT
iptables -I INPUT 1 -s 127.0.0.0/8 -p tcp -m tcp --dport 53 -j ACCEPT
iptables -I INPUT 1 -s 127.0.0.0/8 -p udp -m udp --dport 53 -j ACCEPT
iptables -I INPUT 1 -s 192.168.0.0/16 -p tcp -m tcp --dport 53 -j ACCEPT
iptables -I INPUT 1 -s 192.168.0.0/16 -p udp -m udp --dport 53 -j ACCEPT
iptables -I INPUT 1 -p udp --dport 67:68 --sport 67:68 -j ACCEPT
iptables -I INPUT 1 -p tcp -m tcp --dport 4711 -i lo -j ACCEPT
iptables -I INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
ip6tables -I INPUT -p udp -m udp --sport 546:547 --dport 546:547 -j ACCEPT
ip6tables -I INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
netfilter-persistent save
exit
```

#### CONFIGURANDO O ADGUARD PARA O PRIMEIRO ACESSO


Em um navegador como Chrome, por exemplo, digite:

```
[endereço_ip_da_vm]:3000
```

Neste ponto apenas siga os passos da interface que realizará uma configuração básica
Após esse procedimento a porta 3000 pode ser fechada na VM pois não será mais usada.

####Iniciando o AdGuard
[endereço_ip_da_vm]




