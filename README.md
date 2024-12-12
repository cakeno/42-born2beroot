# 💾 Born2beRoot - Guia de Avaliação
Guia passo a passo para a avaliação do projeto Born2beRoot.

<details>
  <summary>Table of Contents</summary>
 - [Checklist de Avaliação](#-checklist)
</details>

## 📋 Checklist de Avaliação
- [ ] a root deve conter apenas o signature.txt
- [ ] verificar se a signature.txt é identica ao .vdi
- entre no diretorio que foi criada sua VM, lá deve conter o arquivo .vdi da sua VM original
- gere uma assinatura hash da maquina original, com o comando abaixo, ela deve ser identica a signature.txt
```
sashum {name}.vdi
```
> [!WARNING]
> NÃO DÊ START NA VM ainda
>
> CLONE PRIMEIRO a VM para não alterar a assinatura da VM original e impossibilitar as próximas avaliações
>
> Realize a avaliação no CLONE.


<details>
  <summary>😶‍🌫️ Passo extra: Acesso pelo ssh para copiar/colar comandos no terminal</summary>
  <p>
   - com a VM desligada, mudar para modo "bridge" em configurações > network ou rede
   - startar o CLONE da VM
   - no terminal da VM para saber o IP que sera usado no comando de ssh. OBS > utilize o -I (maiúsculo) e não o minúsculo, os resultados sao IPs diferentes.
```
hostname -I
```

- no terminal local
```
ssh {user}@{IPhostname} -p 4242
```
  </p>
</details>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 🖥️ Como funciona a VM?
 o VirtualBox é um hypervisor ou monitor de máquina virtual (VMM), ou seja, um software que cria e executa máquinas virtuais. A virtualização, habilitada pelos hypervisores, transforma a maneira como os recursos de TI são utilizados. São mais baratos que um novo hardware, economiza espaço fisico, simplifica o backup, uma VM nao impacta a outra, o particionamento e isolamento de serviços entre os servidores é seguro contra alguns tipos de virus como malware.


### 💿 A diferenca entre os sistemas operacionais Debian e Rocky
 Como eu sou iniciante escolhi o Debian, por ser mais popular e user-friendly, possui mais documentação e possui controles de acesso mais faceis de se configurar, o gerenciamento de pacotes é feito pelo apt que por ter uma comunidade maior possui extensa documentacao e foruns para suporte.
 
 Já o RockyOS replica as funcionalidades do RHEL(Red Hat Enterprise Linux), como politicas de segurança e gerenciamento de pacotes(yum e dnf) a longo termo, se o objetivo for migrar para RHEL utilizar o RockyOS faz mais sentido, ou também se voce quiser se familiarizar com o ambiente empresarial Red Hat para se destacar em empresas que usam ele, pois os comandos, gerenciamento e configuração são os mesmos fora que é um OS open-source e gratuito.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 📦 Diferenca entre aptitude e apt
 apt:
 - usa o formato de pacote DEB e resolve dependências automaticamente.
 - combina as funcionalidades de várias ferramentas em um único comando mais simples e fácil de usar.
 - interface simplificada e sintaxe intuitiva.

 aptitude:
 - mais complexo
 - interface de linha de comando quanto uma interface de usuário baseada em texto (TUI)
 - sistema de resolução de dependências mais sofisticado
 - log detalhado


### 🔐 O que é APPArmor
 É um modulo de segurança para controlar e restringir o que aplicativos podem fazer em um sistema Linux.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Avaliação da VM

- [ ] script deve mostrar informações a cada 10 minutos;

- [ ] a maquina nao deve exibir um ambiente grafico
```
ls /usr/bin/*session
```

- [ ] deve pedir uma senha
- [ ] nao pode ser o usuario root

- [ ] UFW service deve estar on
```
tail -f /var/log/cron.log
```
ou
```
sudo ufw status
```
ou
```
sudo service ufw status
```
- [ ] ssh service deve estar on
```
sudo systemctl status ssh
```

- [ ] verifique o sistema operacional de acordo com o que foi explicado no inicio
```
uname -v o uname --kernel-version
```

- [ ] verificar se o userpadrao42 esta cadastrado como "sudo" e "user42"
```
getent group sudo
getent group user42
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 🔐 Quais as vantagens das politicas de senha, as vantagens e desvantagens da sua implementacao

- verificar as regras de senha
```
chage -l {username}
```

```
sudo chage -l root
```

```
sudo vim /etc/pam.d/common-password
```


Regras:
- minlen=10 ➤ O número mínimo de caracteres que a senha deve conter.
- ucredit=-1 ➤ Deve conter pelo menos uma letra maiúscula. Colocamos o - como deve conter pelo menos um caracter, se colocarmos + queremos dizer no máximo esses caracteres.
- dcredit=-1 ➤ Deve conter pelo menos um dígito.
- lcredit=-1 ➤ Deve conter pelo menos uma letra minúscula.
- maxrepeat=3 ➤ Não se pode ter o mesmo carácter mais de 3 vezes seguidas.
- reject_username ➤ Não pode conter o nome do utilizador.
- difok=7 ➤ Deve ter pelo menos 7 caracteres que não façam parte da senha antiga.
- enforce_for_root ➤ Iremos implementar esta política para o utilizador de raiz.


- [ ] criar um usuario novo, a senha seguindo as regras e um grupo evaluating, coloque o newuser nesse grupo
```
sudo adduser {newuser}
sudo addgroup evaluating
sudo adduser {newuser} evaluating
```

- [ ]  verificar se o newuser esta no grupo
```
getent group evaluating
```

- [ ]  verificar se o hostname esta user42
```
hostname
```

- [ ]  modifique o hostname e restarte, o hostname deve atualizar
```
sudo nano /etc/hostname
```
```
sudo nano /etc/hosts
```
```
sudo reboot
```

- [ ]  voltar o hostname antigo, siga os passos acima

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 🗄️ LVM
Logical Volume Manager é uma ferramenta para gerenciar armazenamento em sistemas Linux.
  - PVs - sda: disco fisico que é a base do armazenamento;
  - VGs - cnoba42-vg: volume group, contem varios LVs;
  - LVs - cnoba42--vg-root: volume logico utilizado para criar sistemas de arquivos;

- [ ] verificar as partições com do subject
```
lsblk
```

- [ ] verificar instalação do sudo
```
sudo --version
```
ou
```
dpkg -s sudo
```
ou
```
which sudo
```

- [ ] colocar newuser no grupo sudo
```
usermod -aG sudo {newuser}
```
ou
```
sudo adduser {newuser} sudo
```

- [ ] verificar se newuser esta no grupo sudo
```
getent group sudo
```

- [ ] explicar os valores e operação do sudo e mostrar como implementou as regras de sudo do subject
```
sudo nano /etc/sudoers
```

```
Defaults        env_reset //seta min de variaveis
Defaults        mail_badpass //envia email de erro para admin MTA
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:>
Defaults        badpass_message="Password is wrong, please try again!"
Defaults        passwd_tries=3
Defaults        logfile="/var/log/sudo/sudo.log"
Defaults        log_input, log_output //input e output serão salvos
Defaults        requiretty // ativa TTY
```

- [ ] verificar se var/log/sudo existe e tem um arquivo dentro com os logs
```
cat var/log/sudo/sudo.log
```

- [ ] execute um comando sudo para validar o log
```
sudo nano helloworld
cat var/log/sudo/sudo.log
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 🔥 UFW
o Uncomplicated Firewall permite a configuração do tráfego baseado em porta, protocolo ou endereço IP com comandos simples.

- [ ] UFW instalado e funcionando
```
dpkg -s ufw
sudo service ufw status
```

- [ ] mostrar as regras de UFW, incluindo a da 4242
```
sudo ufw status numbered
```

- [ ] criar uma nova regra e verificar
```
sudo ufw allow 8080
sudo ufw status numbered
```

- [ ] deletar e verificar
```
sudo ufw delete {num_rule}
sudo ufw status numbered
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 🥨 SSH
Protocolo de rede que permite a execução de comandos remotamente e transferência de arquivos com criptografia.

- [ ] SSH instalado e funcionando
- [ ] verificar se usa somente a porta 4242
```
which ssh
sudo service ssh status
```

- [ ] usar o usuario novo para logar usando ssh
```
hostname -I
```

```
ssh {newuser}@{localhost} -p 4242
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### 📆 cron
Um serviço do sistema Unix/Linux para agendar e automatizar a execução de tarefas e scripts em horários ou intervalos específicos.

- [ ] explicar o script, cron, alterar para 1min, testar os valores dinamicos
```
sudo crontab -u root -e
```

- [ ] verificar se  cron esta ativo
```
sudo systemctl status cron
```

- [ ] fazer o script parar de rodar na inicialização da maquina e restartar
```
sudo systemctl disable cron
sudo reboot
```

- [ ] verificar o script, rights e se nao houve alterações
```
sudo crontab -u root -e
```

- [ ] inicializar o cron novamente
```
sudo /etc/init.d/cron start
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

> [!NOTE]
> ⭐️ Se este projeto te ajudou, não esqueça de dar uma estrela!
