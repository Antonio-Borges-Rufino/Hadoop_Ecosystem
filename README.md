# Projeto
1. Esse repositório é uma extensão do repositório [cluster_base](https://github.com/Antonio-Borges-Rufino/cluster_base)
2. A intenção é configurar um ecossistema baseado em hadoop para projetos de big data
3. Cada passo vai ser feito de forma linear para facilitar a reprodção

# Suporte atual do cluster
![](https://github.com/Antonio-Borges-Rufino/Hadoop_Ecosystem/blob/main/heco_atu_3.jpg)

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
3. Crie uma pasta para os projetos
```
virtualenv -p python3 projetos
```
4. Para acessar o ambiente virtual 
```
source projetos/bin/activate
```
5. Instalar a biblioteca pandas
```
pip3 install pandas
```
6. Instalar o jupyter
```
pip install jupyter
```
```
ipython kernel install --name projetos --user
```
7. Para executar o jupyter e acessar ele pela máquina mãe
```
jupyter notebook 
```
8. Instalar a biblioteca para acessar o redis
```
pip install redis
```
9. Instalar a biblioteca pyspark
```
pip install pyspark
```
10. Instalar o conector kafka-python
```
pip install kafka-python
```
11. Instalar o Flask api
```
pip install flask-restful
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
10. O servidor não permite ssh em localhost sem senha, portanto faça:
```
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
```
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
```
chmod 0600 ~/.ssh/authorized_keys
```
11. Formatar o sistema de arquivos:
```
hadoop_ecosystem/hadoop/bin/hdfs namenode -format
```
12. Iniciar namenode:
```
hadoop_ecosystem/hadoop/sbin/start-dfs.sh
```
13. Crie pasta e usuario no hdfs:
```
hadoop_ecosystem/hadoop/bin/hdfs dfs -mkdir /user
```
```
hadoop_ecosystem/hadoop/bin/hdfs dfs -mkdir /user/HADOOP
```
14. Adicione em .baschrc
```
export PATH="$PATH:$HOME/bin:/bin:/usr/bin:/sbin:/usr/sbin:/usr/local/bin" 
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-11.el8.x86_64/jre/
export HADOOP_HOME=/home/hadoop/hadoop_ecosystem/hadoop/
export PATH=$PATH:$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
```
source .baschrc
```
15. Edite hadoop_ecosystem/hadoop/etc/hadoop/mapred-site.xml com vim e adicione:
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>
</configuration>
```
16. Edite hadoop_ecosystem/hadoop/etc/hadoop/yarn-site.xml com vim e adicione:
```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
```
17. Para startar o hadoop e o yarn, respectivamente:
```
start-dfs.sh
```
```
start-yarn.sh
```
18. Para parar basta colocar stop no lugar do start

# Passo 5 -> Instalar o zookeeper
1. Baixe o zookeeper
```
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz
```
2. Extraia para dentro da pasta hadoop_ecosystem
```
tar xvzf apache-zookeeper-3.7.1-bin.tar.gz -C hadoop_ecosystem/
```
3. Mude o nome apenas para zookeeper
```
mv hadoop_ecosystem/apache-zookeeper-3.7.1-bin/ hadoop_ecosystem/zookeeper
```
4. Crie um diretorio vazio para acesso de memória do zookeeper
```
mkdir /home/hadoop/hadoop_ecosystem/zookeeper/dir_bd
```
6. crie um arquivo com vim em /home/hadoop/hadoop_ecosystem/zookeeper/conf/ com nome de zoo.cfg
```
vim /home/hadoop/hadoop_ecosystem/zookeeper/conf/zoo.cfg
```
7. Edite /home/hadoop/hadoop_ecosystem/zookeeper/conf/zoo.cfg com vim e adicione:
```
tickTime=2000
dataDir=/home/hadoop/hadoop_ecosystem/zookeeper/dir_bd/
clientPort=2181
```
8. Edite .baschrc para adicionar a variavel de ambiente do zookeeper
```
export ZOOKEPER_HOME=/home/hadoop/hadoop_ecosystem/zookeeper/
export PATH=$PATH:$ZOOKEPER_HOME
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEPER_HOME/bin
```
9. Para iniciar o zookeeper:
```
zkServer.sh start
```
10 Para pausar basta trocar o start pelo stop

# Passo 6 -> Instalando o kafka
1. Baixar o kafka
```
wget https://dlcdn.apache.org/kafka/3.3.1/kafka_2.13-3.3.1.tgz
```
2. Extraia para dentro da pasta hadoop_ecosystem
```
tar xvzf kafka_2.13-3.3.1.tgz -C hadoop_ecosystem/
```
3. Mude o nome apenas para kafka
```
mv hadoop_ecosystem/kafka_2.13-3.3.1/ hadoop_ecosystem/kafka
```
4. Edite .baschrc para adicionar a variavel de ambiente do kafka
```
export KAFKA_HOME=/home/hadoop/hadoop_ecosystem/kafka/
export PATH=$PATH:$KAFKA_HOME
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEPER_HOME/bin:$KAFKA_HOME/bin:KAFKA_HOME/config
```
5. Atualize o .baschrc
```
source .baschrc
```
6. Inicialize o zookeeper-server
```
zkServer.sh start
```
7. Em outro terminal start o kafka
```
kafka-server-start.sh /home/hadoop/hadoop_ecosystem/kafka/config/server.properties
```
8. Para parar os processos, abre-se um novo terminal e digita os mesmos comandos mas em vez de start, coloque stop e sem os caminhos de config
```
zkServer.sh stop
kafka-server-stop.sh
```
9. Por ser um comando grande, vou criar um alias para a inicialização do servidor kafka, editando .baschrc
```
alias kafka_start="kafka-server-start.sh /home/hadoop/hadoop_ecosystem/kafka/config/server.properties"
```
```
source .baschrc
```

# Passo 7 -> Instalar spark
1. Baixar o scala e instalar
```
curl -fL https://github.com/coursier/launchers/raw/master/cs-x86_64-pc-linux.gz | gzip -d > cs && chmod +x cs && ./cs setup
```
2. Baixar o spark
```
wget https://dlcdn.apache.org/spark/spark-3.3.1/spark-3.3.1-bin-hadoop3.tgz
```
3. Extrair para dentro da pasta hadoop_ecosystem
```
tar xvzf spark-3.3.1-bin-hadoop3.tgz -C hadoop_ecosystem/
```
4. Mudar o nome para apenas spark
```
mv hadoop_ecosystem/spark-3.3.1-bin-hadoop3/ hadoop_ecosystem/spark
```
5. Edite as variaveis de ambiente e adicione em .baschrc:
```
export SPARK_HOME=/home/hadoop/hadoop_ecosystem/spark/
export PATH=$PATH=$SPARK_HOME
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEPER_HOME/bin:$KAFKA_HOME/bin:$SPARK_HOME/bin:$SPARK_HOME/sbin
```
```
source .baschrc
```
# Passo 8 -> Instalar o redis
1. Baixar o redis
```
wget https://download.redis.io/redis-stable.tar.gz
```
2. Extrair o redis para a pasta hadoop_ecosystem
```
tar xvzf redis-stable.tar.gz -C hadoop_ecosystem/
```
3. Mudar de nome apenas para redis
```
mv hadoop_ecosystem/redis-stable/ hadoop_ecosystem/redis
```
4. Entrar na pasta do redis
```
cd hadoop_ecosystem/redis
```
5. Compilar o redis
```
make
```
6. Instalar o redis (ainda na mesma pasta)
```
make install
```
7. Para executar o servidor (não é preciso configurar como variavel de ambiente)
```
redis-server
```
8. Para entrar na linha de comando
```
redis-cli
```
9. Para sair do redis-server apert cntrl+c, e para sair do redis-cli use exit

# Passo 9 -> Instalar o mysql
1. Atualize o fluxo do centOS
```
sudo dnf upgrade --refresh -y
```
2. Instale o mysql
```
sudo dnf install mysql mysql-server -y
```
3. Ative o mysql
```
sudo systemctl enable mysqld --now
```
4. Desabilite a ativação do mysql ao ligar o sistema
```
sudo systemctl disable mysqld
```
5. Restart o mysql
```
sudo systemctl restart mysqld
```
6. Para ligar o mysql
```
sudo systemctl start mysqld
```
7. Para desligar o mysql
```
sudo systemctl disable mysqld
```
8. Com o mysql ligado, use para acessar
```
sudo mysql
```
# Passo 10 -> Instalar o Hive
1. Baixar a versão estavel do Hive
```
wget https://dlcdn.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
```
2. Extrair o hive 
```
tar -xzvf apache-hive-3.1.3-bin.tar.gz
```
3. Mover ele para a pasta do hadoop_ecosystem
```
mv apache-hive-3.1.3-bin/ hadoop_ecosystem/
```
4. Renomear apenas para hive
```
mv hadoop_ecosystem/apache-hive-3.1.3-bin/ hadoop_ecosystem/hive/
```
5. Edite o arquivo .baschrc e adicione
```
export HIVE_HOME=/home/hadoop/hadoop_ecosystem/hive/
export PATH=$PATH:$HIVE_HOME
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEPER_HOME/bin:$KAFKA_HOME/bin:$SPARK_HOME/bin:$SPARK_HOME/sbin:$HIVE_HOME/bin
```
6. Atualize o arquivo
```
source .baschrc
```
7. Start o hadoop caso ele esteja desligado
```
start-dfs.sh
```
8. Start o yarn caso ele esteja desligado
```
start-yarn.sh
```
9. O hive precisa de algumas pastas dentro do HDFS para funcionar, vamos criar primeiro a pasta /tmp
```
hdfs dfs -mkdir /tmp
```
10. Configure as permissões da pasta /tmp
```
hdfs dfs -chmod g+w /tmp
```
11. Crie a pasta do usuario warehouse hive
```
hdfs dfs -mkdir /user/hive
hdfs dfs -mkdir /user/hive/warehouse
```
12. Configure as permissões da pasta
```
hdfs dfs -chmod g+w /user/hive/warehouse
```
13. Crie o arquivo /home/hadoop/hadoop_ecosystem/hive/conf/hive-site.xml
```
vim /home/hadoop/hadoop_ecosystem/hive/conf/hive-site.xml
```
14. Adicione a hive-site.xml
```
  <property>
    <name>hive.server2.enable.doAs</name>
    <value>false</value>
    <description>
      Setting this property to true will have HiveServer2 execute
      Hive operations as the user making the calls to it.
    </description>
  </property>
```
15. Inicie o Hive utilizando um bd base, no meu caso utilizei o derby
```
schematool -dbType derby -initSchema
```
16. Ligue o HiveServer2
```
hiveserver2
```
17. Se conecte com o beeline
```
beeline -u jdbc:hive2://localhost:10000
```

# Passo 11 -> Instalar o druid
1. Baixar a versão mais estável do druid
```
wget https://dlcdn.apache.org/druid/24.0.0/apache-druid-24.0.0-bin.tar.gz
```
2. Descompactar o druid na pasta hadoop_ecosystem
```
tar -xzf apache-druid-24.0.0-bin.tar.gz -C hadoop_ecosystem/
```
3. Mudar o nome apenas para druid
```
mv /home/hadoop/hadoop_ecosystem/apache-druid-24.0.0/ /home/hadoop/hadoop_ecosystem/druid
```
4. Inserir no arquivo .bashrc
```
export DRUID_HOME=/home/hadoop/hadoop_ecosystem/druid/
export PATH=$PATH:$DRUID_HOME
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEPER_HOME/bin:$KAFKA_HOME/bin:$SPARK_HOME/bin:$SPARK_HOME/sbin:$HIVE_HOME/bin:$DRUID_HOME/bin
```
5. Atualizar o arquivo
```
source .bashrc
```
6. Para que o druid funcione com o zookeper já instalado, vá em /home/hadoop/hadoop_ecosystem/druid/conf/supervise/single-server/micro-quickstart.conf com vim e comente
```
#!p10 zk bin/run-zk conf
```
7. Agora e só ligar o servidor Druid e colocar a porta 8888 na conexão com ssh, algo como, -L 8002:localhost:8888
```
start-micro-quickstart
```
# Passo 12 -> Instalando o NiFi
1. Baixe a versão estável do NiFi
```
wget https://downloads.apache.org/nifi/1.18.0/nifi-1.18.0-bin.zip
```


# ORDEM DE START
```
start-dfs.sh
```
```
start-yarn.sh
```
```
zkServer.sh start
```
```
kafka_start > /dev/null & 
```
```
redis-server > /dev/null & 
```
```
sudo systemctl start mysqld
```
-> Para se conectar ao Hive
```
hiveserver2
beeline -u jdbc:hive2://localhost:10000
```
-> Caso queira startar o druid (Por compatibilidade, o druid e o jupyter não podem startar ao mesmo tempo, caso seja necessário, mude a porta de funcionamento do jupyter)
```
start-micro-quickstart
```
