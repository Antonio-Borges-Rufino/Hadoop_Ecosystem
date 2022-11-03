# Projeto
1. Esse repositório é uma extensão do repositório [cluster_base](https://github.com/Antonio-Borges-Rufino/cluster_base)
2. A intenção é configurar um ecossistema baseado em hadoop para projetos de big data
3. Cada passo vai ser feito de forma linear para facilitar a reprodção

# Passo 1 -> Criação do servidor
1. O servidor base desse projeto vai ser um centOS-8 stream, que pode ser baixado [aqui](http://isoredirect.centos.org/centos/8-stream/isos/x86_64/)
2. O ambiente de virtualização utilizado foi o virtual-box
3. As configurações do servidor foram:  
      -> Nome: Hadoop_Ecosystem  
      -> Tipo: Linux  
      -> Versão: Red Hat (64 bits)  
      -> Quantidade de memória: 3072 mb  
      -> Tipo de disco: VDI dinamicamente alocado  
      -> Tamanho do disco: 100 gb  
      -> Quantidade de núcleos de processador: 3
      -> Memoria de vídeo: 128 mb
      -> Audio: desabilitado
      -> Rede: placa em modo bridge  
 4. A instalação teve os seguintes parâmetros:  
      -> Lingua utilizada: Português (Brasil)  
      -> Kdump: Desativado  
      -> Destino instalação: VirtualBox Hard_Disk com configurações de armazenamento personalizada + criar automaticamente   
      -> Seleção de programas: Instalação minima + Padrão + Ferramenta de desenvolvimento + Ferramentas de desenvolvimento RPM  
      -> Usuario root: Desabilitado  
      -> Criar usuario: Hadoop com permissão de administrador  
5. Com a instalação feita procure o ip da máquina para poder fazer um gerenciamento com ssh  
```
ip addr show
```
6. Use o gerenciador da sua escolha para fazer o acesso com SSh

# Passo 2 -> Instalando e configurando o python
1. Para instalar o python 3 + pip faça:  
```
sudo yum install python3
```
2. Para instalar o virtualenv:  
```
sudo pip3 install virtualenv
```

# Passo 3 -> Instalar e configurar o Java
1. O java é base de funcionamento do ecossistema hadoop, por isso, vou instalar a versão mais estável dele que é a 8
2. Para atualizar o gerenciador de pacote
```
sudo yum update
```
3. Para instalar o java 8
```
sudo yum install java-1.8.0-openjdk
```
3. Para ver onde estão os executaveis java
```
update-alternatives --config java
```
4. OBS: Guarde apenas até a versão java, nesse caso, está em /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-11.el8.x86_64/jre

# Passo 4 -> Instalar o Hadoop
1. Baixar o hadoop
```
sudo wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz
```
2. Crie uma pasta onde os arquivos vão ficar
``` 
mkdir hadoop_ecosystem
```
3. Descompacte o arquivo
```
tar xvzf hadoop-3.3.4.tar.gz -C hadoop_ecosystem/
```
4. Mude o nome da pasta
```
 mv hadoop-3.3.4/ hadoop/
```
5. Configure o arquivo hadoop_ecosysten/hadoop/etc/hadoop/hadoop-env.sh
```
vim hadoop_ecosystem/hadoop/etc/hadoop/hadoop-env.sh
```
6. Com o arquivo hadoop_ecosysten/hadoop/etc/hadoop/hadoop-env.sh aberto no vim, escreva e salve:
      -> export JAVA_HOME =/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-11.el8.x86_64/jre
7. Teste o hadoop
```
hadoop_ecosystem/hadoop/bin/hadoop
```
8. Edite hadoop_ecosystem/hadoop/etc/hadoop/core-site.xml com vim e adicione:
```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
9 Edite hadoop_ecosystem/hadoop/etc/hadoop/hdfs-site.xml com vim e adicione:
```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```



      
      
