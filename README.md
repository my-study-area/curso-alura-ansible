# curso-alura-ansible

## Anotações
### Confiugração de rede
Para o estudo foram criadas duas máquinas virtuais Ubuntu Server 20-04.1 utilizando VirtualBox. Cada máquina possue 2  placas de redes (NAT e Bridge utilizando a placa de rede Microsoft Loopback Adapter para estabelecer comunicação com o host).

As placas de rede Bridge foram configuradas com IP fixo através do arquivo `/etc/netplan/00-installer-config.yaml`. O servidor está utilizando o ip `10.0.0.10` e o cliente com o ip `10.0.0.11`. Veja o exemplo do arquivo de configuração do server:

```yaml
network:
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses:
        - 10.0.0.10/24

  version: 2
```
Após a configuração execute o comando `sudo netplan apply` para aplicar a configuração no sistema.
Veja lista de comando essenciais para consultar a consfigurações de rede:
- ` cat /proc/net/dev`: verifica as placas de redes disponíveis
- `ip link`:exibe a configuração do dispositivo de rede
- ` ip address show dev enp0s3`: verifica o ip da interface enp0s3
- `hostname -I`: exibe os endereços de rede das interfaces

### Configuração SSH
`ssh-keygen` - gera a chave SSH
`ssh-copy-id adriano@10.0.0.11`: (onde adriano é o nome do usuário e 10.0.0.11 é o ip do cliente que receberá o acesso via ssh). Copia a chave pública para o cliente.
`ssh adriano@10.0.0.11`: acessa cliente utilizando chave pública.

### Comandos do Ansible
- `/etc/ansible/hosts`: caminho do arquivo de inventário onde colocaremos todas as informações necessárias para o Ansible saber como acessar as máquinas. Exemplo:
```txt
[wordpress]
10.0.0.11
```
- ` ansible all -m ping`: executa o comando ping nos hosts. Exemplo da saída do comando executada com sucesso:
```bash
$ ansible all -m ping
10.0.0.11 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
- `ansible all --private-key ~/.ssh/  -i hosts -m shell -a 'echo hello, World'`: executa o comando `echo hello, World` nos hosts apontando para a chave privada. Exemplo da saída do comando executada com sucesso:
```bash
$ ansible all --private-key ~/.ssh/  -i hosts -m shell -a 'echo hello, World'
10.0.0.11 | CHANGED | rc=0 >>
hello, World
```
