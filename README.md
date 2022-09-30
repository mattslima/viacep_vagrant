# viacep_vagrant
Utilizando o Vagrant para subir uma aplicação do ViaCep com Ngnix


Primeiramente vamos instalar o Vagrant nna sua máquina e dependendo da sua distribuição a forma é diferente. Segue o link da documentação oficial:
https://www.vagrantup.com/downloads

Próximo passo, após o Vagrant instalado, abra o terminal da sua distro (no caso eu utilizo o Fedora) e digite o comando "vagrant init ubuntu/focal64"

![image](https://user-images.githubusercontent.com/55968562/193170332-2d5d4285-aa62-4632-a30d-290b49dbc3bc.png)

Vai aparecer essa tela:

![image](https://user-images.githubusercontent.com/55968562/193170510-235de234-c86c-4428-a3a1-bfff8d757879.png)

Vai criar o arquivo Vagrantfile, é com ele que vamos trabalhar como Infraestrutura como Código - IaaC, eu abri meu arquivo com o VsCode

![image](https://user-images.githubusercontent.com/55968562/193170647-99907dbd-0727-4d34-8a65-52de5a48a839.png)

Vou deixar o nosso código já pronto e explicar passo a passo. Por isso, vamos limpar esse arquivo:

"Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y apache2
  SHELL
end

![image](https://user-images.githubusercontent.com/55968562/193171105-1a81826e-25d7-4cf3-b1e6-6011c0d678f7.png)

Como podemos observar, o Vagrantfile vem como padrão o apache2 para usar de BD, mas vamos trocar pelo Nginx. Com isso você deleta o apache2 e insere Nginx

![image](https://user-images.githubusercontent.com/55968562/193173465-e7473163-525b-4c15-b914-b8f74d3c218a.png)

Explicando o código: 
Vagrant.configure("2") do |config| --> O arquivo de configuração inicial do Vagrantfile necessário para rodar o script
config.vm.box = "ubuntu/focal64" --> Esse comando informa qual imagem vamos utilizar, vamos utilizar o Ubuntu na sua ultima versão a focal de 64bits
config.vm.provision "shell", inline: <<-SHELL --> Aqui é um dos pontos mais importantes, vamos começar o provisionamento da nossa maquina. Os comando que vão ser executados depois que subir a maquina virtual, e os comandos vão ser em shell script 

apt update --> Update do sistema 
apt install -y ngnix --> instalação do serviço do nginx
  SHELL
  
Perceberam que tem dois SHELL, um indica a aberta e o outro o fechamento do provision.

end --> indica o fim do arquivo.

* REMOVE O -GET, deixa só apt install -y nginx


Muito cuidado na hora de digitar os comandos, pois o arquivo é case sensitive.

Depois de salvar o arquivo, vamos executar com o comando vagrant up

![image](https://user-images.githubusercontent.com/55968562/193172173-378e1161-b5e3-4b56-b19f-8d6e84a9deed.png)


Quando finalizar, vocês teram algo parecido com isso aqui

![image](https://user-images.githubusercontent.com/55968562/193172375-4350dce5-cbe0-48c8-9239-935813580429.png)

Para saber se está tudo ok, damos o comando vagrant status

![image](https://user-images.githubusercontent.com/55968562/193172545-5c84509c-c4ae-49f1-90a0-7c3a54f43ad4.png)

O resultado tem que ser igual ao da imagem. Pronto, subimos nossa máquina. Agora ver se o serviço está funcionando normal, vamos dar o comando vagrant ssh, assim acessaremos a máquina via SSH.

![image](https://user-images.githubusercontent.com/55968562/193173852-f4d32cf7-19be-4f76-a6b8-df994ae181fb.png)

Vai aparecer algo como essa tela. Agora damos o comando systemctl status nginx

![image](https://user-images.githubusercontent.com/55968562/193173980-285adb15-d120-4170-8365-063eb37b9cf3.png)

A tela será igual a esta.
Se sua tela for igual a esta, tudo até agora deu certo, até agora kkk
Pode dar um exit, para sair da maquina ubuntu que estavamos em acesso via ssh.
Agora vamos apimentar um pouco as coisas com novos comandos
Primeiramente vamos no site 
https://viacep.com.br/exemplo/javascript/

![image](https://user-images.githubusercontent.com/55968562/193174491-f2793eab-ad71-4b60-8bc0-665bd14fd192.png)

Copiamos todo o codigo html da pagina
Voltamos nosso editor de texto, e dentro da pasta que estamos com o Vagrantfile, criamos mais uma pasta com o nome html, e dentro dela uma arquivo html com o nome de index.html e colocamos o codigo, não vou entrar em detalhes nessa parte, pois vamos apenas aproveitar a API.

![image](https://user-images.githubusercontent.com/55968562/193174754-c5a27bee-320d-40d7-8d52-1888e3790b59.png)

Salvamos o arquivo e voltamos para o nosso Vagrantfile.
Agora vamos por mais paramentros do proprio Vagrant
Vou por o codigo e explicar cada um novamente

![image](https://user-images.githubusercontent.com/55968562/193175785-98333510-b196-4588-ba25-e5dbd0c78727.png)


adicionaremos os seguintes codigos: 
  config.vm.network "forwarded_port", guest: 80, host: 8080 --> Estamos fazendo um direcionamento de porta, a porta 8080 do  host(nossa maquina fisica) vai passar a ser a porta 80 da nossa maquina virtual(guest)

  config.vm.network "public_network", ip: "192.168.1.13" --> public_network estamos indicando o modo bridge do nosso adaptador de rede, e ip estamos setando um ip fixo para nossa maquina, muito util quando vamos subir muitas maquinas, para não ficar dando ssh em todas para descobrir o ip de cada uma, uma boa pratica pensando em DevOps
  
  config.vm.synced_folder "./html", "/var/www/html" --> Estamos sincronizando as pastas, no caso estamos jogando a pasta html e seu conteudo(index.html) para dentro da nossa maquina virtual dentro do html para subir a aplicação da via cep
  
Terminado tudo, vamos salvar e dar o seguinte comando
vagrant reload, este comando vai recarregar o arquivo do Vagrantfile

Caso pare nessa parte, digite 1, isso ocorre quando tem mais de uma placa de rede no computador do host 

![image](https://user-images.githubusercontent.com/55968562/193175964-7bb91169-b946-4cb1-9123-ae35f158092c.png)

Observamos o forwarding ports em ação

![image](https://user-images.githubusercontent.com/55968562/193176068-65b39986-345c-4c8b-a0a8-58f38b32f682.png)

Aqui o synced folders

![image](https://user-images.githubusercontent.com/55968562/193176132-9a099989-0634-4a38-9e1a-c056230fbe9a.png)

No final vamos no nosso navegador e digitamos o ip do guest no nosso caso é 192.168.1.13

![image](https://user-images.githubusercontent.com/55968562/193176309-3cfde7ac-839d-4875-b4cb-6bfc8fd310f3.png)

ao colocar um cep valido ele carrega com as informações assim mostrando que tudo está ok

![image](https://user-images.githubusercontent.com/55968562/193176417-476d5c97-534a-4c11-a92d-de6f2af149e3.png)


Agradeço você e acompanhou esse pequeno tutorial.
Bons estudos
