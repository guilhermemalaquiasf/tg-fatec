# TENSORFLOW

## CONCEITOS GERAIS

Como pode ser visto no trabalho de \citeonline[p. 1, tradução nossa]{abaditensorflow2015} – membro da equipe que desenvolveu a ferramenta, da empresa Google –, TensorFlow é uma interface para expressar algoritmos de aprendizado de máquina e também uma implementação para executar tais algoritmos.

Uma operação de computação com o TensorFlow pode ser executada em uma ampla variedade de sistemas heterogêneos, desde dispositivos móveis, como smartphones e _tablets_, até sistemas distribuídos em grande escala de centenas de máquinas e milhares de dispositivos computacionais, como cartões GPU.

O TensorFlow é flexível e pode ser usado para expressar uma grande variedade de algoritmos, incluindo algoritmos de treinamento e inferência para modelos de redes neurais profundas, e tem sido utilizado em pesquisas e em sistemas de aprendizagem de máquinas em várias áreas, incluindo reconhecimento de fala, visão por computador, robótica recuperação de informações, processamento de linguagem natural, extração de informações geográficas e descoberta computacional de medicamentos.

A biblioteca de aprendizagem de máquina surgiu do Distbelief, um sistema proprietário também utilizado para aprendizado de máquina lançado em 2011. O TensorFlow foi originalmente desenvolvido por pesquisadores e engenheiros da Google Brain Team dentro da organização de pesquisa de inteligência de máquina da empresa para fins de pesquisa em máquinas e pesquisa de redes neurais profundas, mas o sistema é genérico o suficiente para ser aplicado em uma ampla variedade de outros domínios. \cite{google2017tensor}

O sistema foi lançada como um projeto _open source_ sob a licença Apache 2.0 em novembro de 2015, disponibilizado na plataforma Github [^GitHub] \citeonline{github2017tensorflow} e está atualmente na versão 1.3, lançada em 16 de agosto de 2017. Atualmente o sistema é utilizado por empresas como Airbnb, Ebay, Dropbox, Google, Intel, IBM, Snapchat, Twitter e Uber.

[^GitHub]: Plataforma _web_ de hospedagem de projetos de _software_ que utiliza o programa de controle de versionamento _git_.

Por conseguinte, apesar do termo TensorFlow geralmente se referir à API usada para desenvolvimento e treinamento de modelos de aprendizagem de máquina, pode-se dizer que o TensorFlow é um pacote de software composto por:

1. TensorFlow: API de aprendizado de máquina propriamente dita, onde os modelos são desenvolvidos nas linguagens Python e C++ \cite[p. 1, tradução nossa]{abaditensorflow2015};
2. TensorBoard: Ferramenta de complementar ao TensorFlow para visualização de estruturas gráficas e estatísticas. Facilita no entendimento da estrutura dos grafos de computação e do comportamento geral dos modelos de aprendizado de máquinas. Toda a visualização é interativa, onde os usuários podem ampliar os nós do grafo agrupados para visualizar os detalhes. \cite[p. 12, tradução nossa]{abaditensorflow2015};
3. TensorFlow Serving: _Software_ que facilita a instalação de modelos TensorFlow pré-treinados. Usando funções TensorFlow embutidas, o usuário pode exportar seu modelo para um arquivo que pode então ser lido nativamente pelo TensorFlow Serving. Assim, é possível  iniciar um servidor simples de alto desempenho que pode receber dados de entrada, passá-los para o modelo treinado e retornar a saída do modelo \cite{ciencia2016tensorflow}.

## INSTALAÇÃO E CONFIGURAÇÃO DO TENSORFLOW

A documentação do site oficial, \citeonline{google2017tensor} descreve detalhadamente a instalação do sistema. É possível instalar o software nos sistemas operacionais Linux, Mac OS X e Windows, com arquitetura de processamento CPU e GPU. O sistema foi instalado para a realização deste trabalho em uma máquina com arquitetura CPU e sistema operacional GNU/Linux Debian 9.0.1.

Vale citar que na documentação do site oficial o tutorial para instalação para o GNU/Linux Ubuntu também funciona para o GNU/Linux Debian.

Inicialmente, a documentação oficial cita que é possível instalar o sistema das seguinte formas:

1. Utilizando o gerenciador de pacotes _pip_ dentro de um ambiente virtual isolado _virtualenv_;
2. Usando o gerenciador de pacothes _pip_ sem um ambiente virtual isolado _virtualenv_;
3. Por meio de um contêiner _Docker_;
4. Utilizando o gerenciador de pacotes _Anaconda_;
5. Construindo com os binários fonte.

Assim, para o trabalho foi realizada uma instalação do TensorFlow utilizando um contêiner Docker, por ser mais simples e ter sempre a versão mais atualizada.

Por conseguinte, para a instalalação do Docker foram utilizado os seguintes comandos (em ordem):

$ sudo apt-get update && sudo apt-get upgrade && sudo apt-get install -y apt-transport-https ca-certificates curl gnupg2 software-properties-common

$ curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -

$ sudo apt-key fingerprint 0EBFCD88

$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"

$ sudo apt-get update && sudo apt-get install -y docker-ce

Por conseguinte, para não ser necessário utilizar o programa Docker como super usuário, foi necessário executar:

sudo usermod -aG docker $USER

Assim, para verificar se o sistema foi corretamente instalado, foi utilizado o seguinte comando, que roda uma imagem de testes "hello-world":

$ docker run hello-world

Dessa forma, como a mensagem "Hello from Docker!" apareceu no terminal, o sistema Docker foi instalado corretamente.

Em seguida, para baixar a imagem Docker tensorflow:nightly-py3 [^nota] e iniciar um novo contêiner foi utilizado o seguinte comando:

[^nota]: A versão estável "tensorflow" mais recente recomendada na documentação oficial (que corresponde a versão 1.3 do TensorFlow) não possui algumas bibliotecas necessárias para a execução do código do projeto até o momento da escrita desse trabalho, dessa forma, foi utilizada a imagem tensorflow:nightly-py3 (que é uma imagem do tipo "nightly", ou seja, é uma versão "não fechada" do TensorFlow, onde os desenvolvedores ainda estão trabalhando no código), que possui as bibliotecas necessárias.

```
# docker run -it tensorflow/tensorflow:nightly-py3 bash
```

Na primeira execução do comando, a imagem comecará a ser baixada, o que pode demorar um pouco. Assim, após o download da imagem, será iniciada a máquina do contêiner, em um bash com o super usuário. Em outros momentos, para iniciar novamente o contêiner, pode ser utilizado o mesmo comando.

Dessa forma, após a instalação, como a imagem é bem básica, foi necessário instalar alguns programas para a execução do projeto, com o seguinte comando:

```
# apt-get update && apt-get upgrade && apt-get install -y vim git openjdk-8-jdk curl build-essential libcurl3-dev libfreetype6-dev libpng-dev libzmq3-dev pkg-config python-dev python-numpy python-pip software-properties-common swig zip zlib1g-dev
```

Por conseguinte, outro programa necessário é foi tzdata, que configura o horário do contêiner. Ele pode ser instalado com o comando:

```
# apt-get install tzdata
```

Com isso, o programa pergunta a região geográfica, sendo necessário colocar a opção "2. America" e, em seguida, "132. Sao_Paulo".

Por conseguinte, também foi necessário instalar o programa Bazel; para instalá-lo, foram utilizados os seguintes comandos (um por vez):

```
# echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list
# curl https://bazel.build/bazel-release.pub.gpg | apt-key add -
# apt-get update && apt-get install bazel
```

Posteriormente, foi necessário setar uma variável de ambiente, inserindo no arquivo _~/.bashrc_ a seguinte linha de código:

```
export PATH="$PATH:$HOME/bin"
```

Por fim, para testar se o TensorFlow funciona corretamente, foi criado um script com o nome _helloWorld.py_, com o seguinte código:

```
import tensorflow as tf
hello = tf.constant('Hello, world!')
sess = tf.Session()
print(sess.run(hello))
```

Em seguida, o script foi executado com o python3:

```
# python3 helloWorld.py
```

Assim, com a mensagem "Hello, world!" sem nenhum aviso de erro, isso significa que o TensorFlow está funcionando corretamente. É normal que apareçam alguns avisos na tela dizendo que as operações de computação poderiam ser mais rápidas com certas bibliotecas. Esses avisos aparecem quando a instalação não foi feita utilizando os binários fonte.

Por conseguinte, foi necessário clonar o repositório do GitHub do TensorFlow:

```
# cd ~
# git clone https://github.com/tensorflow/tensorflow.git
```

O comando baixa a pasta tensorflow, com os scripts que foram utilizados no Projeto.

Por conseguinte, após instalar todos os programas necessários, é necessário persistir as mudanças realizadas, ou então elas serão perdidas quando as máquina for reiniciada. Para isso, foi criada uma nova imagem com o nome tensorflow-atualizado, baseada no estado do contêiner atual, com o seguinte comando (fora do contêiner, sem sair deste), onde "7bbea35054e8" é o id do contêiner:

$ docker commit 7bbea35054e8 tensorflow-atualizado

Posteriormente, para iniciar um contêiner baseado na nova imagem Docker gerada após o computador ter sido reiniciado, foi utilizado o seguinte comando:

$ docker -it tensorflow-atualizado bash

Por fim, dentro de ~/tensorflow, é necessário executar executar:
```
# ./configure
```

1) Please specify the location of python. [Default is /usr/bin/python]: /usr/bin/python3
2) Please input the desired Python library path to use.  Default is [/usr/local/lib/python3.5/dist-packages] (só apertar enter)
3) Do you wish to build TensorFlow with jemalloc as malloc support? [Y/n]: n
4) Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: n
5) Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: n
6) Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: n
7) Do you wish to build TensorFlow with XLA JIT support? [y/N]: N
8) Do you wish to build TensorFlow with GDR support? [y/N]: N
9) Do you wish to build TensorFlow with VERBS support? [y/N]: N
10) Do you wish to build TensorFlow with OpenCL support? [y/N]: N
11) Do you wish to build TensorFlow with CUDA support? [y/N]: N
12) Do you wish to build TensorFlow with MPI support? [y/N]: N
13) Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native] (só enter):





## INSTALAÇÃO DO TENSORFLOW SERVING

Para a instalação propriamente do TensorFlow Serving, existem duas possibilidades de instalação. Caso o processador do computador suporte instruções AVX (_Advanced Vector Extensions_), que é comum em computadores mais novos, é possível instalar o sistema com os comandos:

```
$ echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server" | tee /etc/apt/sources.list.d/tensorflow-serving.list
$ curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg |  apt-key add -
# apt-get update && apt-get install tensorflow-model-server
```

## FUNCIONAMENTO GERAL

Inicialmente, verifica-se que todas as operações de computação utilizando a biblioteca são realizada por meio dos chamados tensores, que são vetores multidimensionais (ou seja, matrizes), através dos nós de um grafo.

O TensorFlow fornece um conjunto extenso de funções e classes que permitem aos usuários definir modelos a partir do zero matematicamente. Ou seja, a príncípio, é possível desenvolver qualquer modelo de Redes Neurais Artificiais. \cite{ciencia2016tensorflow}

### Tipos de dados e variáveis

A classe tf.DType representa os tipos de dados do TensorFlow. Existem diversos tipos, por exemplo:

- tf.float64: Ponto flutuante de dupla precisão de 64 bits.
- tf.float16: Ponto de flutuação de meia precisão de 16 bit.
- tf.complex128: Complexo de dupla precisão de 128 bits.
- tf.int8: Inteiro com sinal de 8 bits.
- tf.int32: Inteiro com sinal de 32 bits.
- tf.uint8: Inteiro sem sinal de 8 bits.
- tf.bool: Boleano.
- tf.string: String de caracteres.

Por conseguinte, as variáveis ​​são manipuladas através da classe tf.Variable. A tf.Variable representa um tensor cujo valor pode ser alterado executando operações sobre ele. Ao contrário dos objetos tf.Tensor, um tf.Variable existe fora do contexto de uma única chamada session.run.

Internamente, tf.Variable armazena um tensor persistente. Operações específicas permitem ler e modificar os valores desse tensor.

A melhor maneira de criar uma variável é chamar a função tf.get_variable. Esta função requer que se especifique o nome da variável. Para criar uma variável com tf.get_variable, basta fornecer o nome e a forma:

my_variable = tf.get_variable("my_variable", [1, 2, 3])

Isso cria uma variável chamada "my_variable", que é um tensor tridimensional com forma [1, 2, 3]. Esta variável, por padrão, terá o dtype tf.float32 e seu valor inicial será randomizado via tf.glorot_uniform_initializer.

Para usar o valor de um grafo tf.Variable, basta utilizá-lo como um tf.Tensor normal:

v = tf.get_variable("v", shape=(), initializer=tf.zeros_initializer())
w = v + 1  # w é um tf.Tensor que é calculado baseado no valor de v.
           #  Sempre que uma variável é usada em uma expressão, ela é automaticamente
           # convertida em um tf.Tensor que representa seu valor.

Para atribuir um valor a uma variável, podem ser utilizados os métodos assign, assign_adde da classe tf.Variable. Por exemplo, é possível chamar esses métodos:

v = tf.get_variable("v", shape=(), initializer=tf.zeros_initializer())
assignment = v.assign_add(1)

### Tensores

De acordo com a documentação oficial,  TensorFlow, como o nome indica, é uma estrutura para definir e executar cálculos envolvendo tensores. Um tensor é uma generalização de vetores e matrizes para dimensões potencialmente maiores. Internamente, TensorFlow representa tensores como matrizes n-dimensionais de tipos de dados base.

Ao escrever um programa TensorFlow, o principal objeto manipulado é o tf.Tensor. Um objeto tf.Tensor representa uma computação parcialmente definida que eventualmente produzirá um valor. Os programas TensorFlow funcionam primeiro criando um grafo dos objetos tf.Tensor, detalhando como cada tensor é calculado com base nos outros tensores disponíveis e, em seguida, executando partes desse grafo para alcançar os resultados desejados.

Um tf.Tensor as seguintes propriedades: um tipo de dados ( float32, int32, ou string, por exemplo) e uma forma

Cada elemento no Tensor tem o mesmo tipo de dados, e o tipo de dados é sempre conhecido. A forma (ou seja, o número de dimensões que tem e o tamanho de cada dimensão) pode ser apenas parcialmente conhecida. A maioria das operações produz tensores de formas totalmente conhecidas se as formas de suas entradas também são totalmente conhecidas, mas em alguns casos, somente é possível encontrar a forma de um tensor no tempo de execução do grafo.

Os principais tipos de tensores são: tf.Variable, tf.Constant, tf.Placeholder, tf.SparseTensor.

Com exceção de tf.Variable, o valor de um tensor é imutável, o que significa que, no contexto de um único tensor de execução, tem apenas um valor.

A classificação de um tf.Tensorobjeto é o seu número de dimensões. Assim, um objeto tf.Tensor de classificação 2 consiste em pelo menos uma linha e pelo menos uma coluna. A seguir estão duas possíveis formas de criar um tensor de dimensão 2:

mymat = tf.Variable([[7],[11]], tf.int16)
myxor = tf.Variable([[False, True],[True, False]], tf.bool)

A forma de um tensor (shape) é o número de elementos em cada dimensão. Assim, as formas podem ser representadas através de listas Python, tuplas de inteiros, ou com o tf.TensorShape. Assim, um tensor t1 com os dados [1, 2, 3, 4, 5, 6, 7, 8, 9] tem a forma [9], e um t2 com os dados [[1, 2, 3], [4, 5, 6], [7, 8, 9]] tem a forma [3, 3].

### Grafos e sessões

O TensorFlow usa um grafo de fluxo de dados para representar sua computação em termos das dependências entre operações individuais. Isso leva a um modelo de programação de baixo nível no qual primeiro se define o grafo de fluxo de dados e, em seguida, se cria uma sessão TensorFlow para executar partes do grafo.

O fluxo de dados é um modelo de programação comum para computação paralela. Em um grafo de fluxo de dados, os nós representam unidades de computação, e as bordas representam os dados consumidos ou produzidos por uma computação.

O fluxo de dados tem várias vantagens que o TensorFlow aproveita ao executar seus programas, tais como:

- Paralelismo: Ao usar bordas explícitas para representar dependências entre as operações, é fácil para o sistema identificar as operações que podem ser executadas em paralelo.
- Execução distribuída: Ao usar bordas explícitas para representar os valores que fluem entre as operações, é possível que o TensorFlow divida o programa em vários dispositivos conectados a diferentes máquinas. O TensorFlow insere a comunicação e a coordenação necessárias entre os dispositivos.
- Compilação: O compilador XLA do TensorFlow pode usar as informações em seu gráfico de fluxo de dados para gerar código mais rápido, por exemplo, ao fundir as operações adjacentes.
- Portabilidade: O gráfico de fluxo de dados é uma representação independente do idioma do código em seu modelo. Você pode criar um gráfico de fluxo de dados em Python, armazená-lo em um SavedModel e restaurá-lo em um programa C ++ para inferência de baixa latência.

Por exemplo, em um gráfico TensorFlow, a operação tf.add corresponderia a um único nó com duas bordas de entrada (os valores a serem somados) e uma borda de saída (o resultado da adição). Esse exemplo pode ser visto na Figura X.

![Figura x: Representação de um grafo com a operação tf.add](/graph.jpeg)

Fonte: Medium Débora Mesquisa (2017)

Como verificado em Mesquita (2017), o grafo é composto por um conjunto de objetos tf.Operation, que representam as unidades com as operações e um conjunto de tf.Tensor, que representam as unidades com os dados. Assim, um código que cria grafo é:

import tensorflow as tf
my_graph = tf.Graph()
with my_graph.as_default():
    x = tf.constant([1,3,6])
    y = tf.constant([1,1,1])
    op = tf.add(x,y)

O ciclo de trabalho no TensowFlow é esse, primeiro define-se o grafo e só depois realiza-se as computações ("rodar" a operação de cada nó do grafo). Para isso, é necessário criar uma tf.Session.

O TensorFlow usa a classe tf.Session para representar uma conexão entre o programa cliente e o tempo de execução C++. Um objeto tf.Session fornece acesso a dispositivos na máquina local e dispositivos remotos usando o tempo de execução TensorFlow distribuído. Ele também armazena em cache informações sobre o tf.Graph que serja possível executar com eficiência a mesma computação várias vezes.

Segundo Mesquita (2017), uma tf.Session encapsula o ambiente onde as operações do grafo são executadas e os tensors são avaliados. Para isso a gente precisa definir qual o grafo que vai ser usado na sessão.

import tensorflow as tf
my_graph = tf.Graph()
with tf.Session(graph=my_graph) as sess:
    x = tf.constant([1,3,6])
    y = tf.constant([1,1,1])
    op = tf.add(x,y)

Para executar as operações você vai usar o método tf.Session.run(). O método tf.Session.run é o mecanismo principal para executar grafo ou avaliar um tf.Tensor. É possível passar um ou mais tf.Operation ou objetos tf.Tensor para tf.Session.run e o TensorFlow irá executar as operações que são necessárias para calcular o resultado.

O tf.Session.run requer que se especifique uma lista de buscas, que determinam os valores de retorno e podem ser a tf.Operation, a tf.Tensor, ou um tipo tensor como, por exemplo tf.Variable,. Essas buscas determinam o que o subgrupo do geral tf.Graph deve ser executado para produzir o resultado: este é o subgrafo que contém todas as operações mencionadas na lista de busca, mais todas as operações cujas saídas são usadas para calcular o valor das buscas. Por exemplo, o seguinte fragmento de código mostra a simples operação de soma apresentada anteriormente:

Para executar as operações você vai usar o método tf.Session.run(). Esse método executa um ‘passo’ da computação do grafo. Definimos o que queremos rodar através do argumento fetches. Ele pode ser um elemento do grafo, uma lista arbitrária, um dicionário, etc. No nosso caso vamos rodar um passo da nossa adição:

import tensorflow as tf
my_graph = tf.Graph()
with tf.Session(graph=my_graph) as sess:
    x = tf.constant([1,3,6])
    y = tf.constant([1,1,1])
    op = tf.add(x,y)
    result = sess.run(fetches=op)
    print(result)
>>> [2 4 7]

### Estimadores

Os Estimadores são uma API de alto nível que simplifica muito a programação de aprendizagem em máquina. Os estimadores encapsulam as ações de treinamento, avaliação, predição, exportação para servir. Todos os Estimadores são classes baseadas na classe tf.estimator.

Os estimadores fornecem os seguintes benefícios:

- Você pode executar modelos baseados em Estimators em um host local ou em um ambiente multi-servidor distribuído sem alterar seu modelo. Além disso, você pode executar modelos baseados em Estimators em CPUs, GPUs ou TPUs sem recodificar seu modelo.
- Os estimadores simplificam o compartilhamento de implementações entre desenvolvedores de modelos.
- Você pode desenvolver um modelo de última geração com código intuitivo de alto nível. Em suma, geralmente é muito mais fácil criar modelos com Estimadores do que com as API TensorFlow de baixo nível.
- Os estimadores são criados em tf.layers, o que simplifica a personalização.
- Os estimadores criam o gráfico para você. Em outras palavras, você não precisa construir o gráfico.
- Os estimadores fornecem um ciclo de treinamento distribuído seguro que controla como e quando:
  - construir o gráfico
  - inicializar variáveis
  - iniciar filas
  - lidar com exceções
  - criar arquivos de ponto de verificação e recuperar de falhas
  - salve sumários para o TensorBoard

Os Estimadores prontos permitem que se trabalhe em um nível conceitual muito maior do que as API TensorFlow básicas. Não é necessário se preocupar com a criação do grafo computacional ou das sessões, uma vez que os Estimadores lidam com tudo. Ou seja, os Estimadores prontos criam, gerenciam objetos Graph e Session automaticamente.

Além disso, os Estimadores prontos permitem se utilize diferentes arquiteturas de modelo, fazendo apenas alterações de código mínimas. DNNClassifier, por exemplo, é uma classe estimada pré-feita que treina modelos de classificação através de redes neurais densas e feed-forward.


A API de aprendizado de máquinas de alto nível da TensorFlow (tf.estimator) facilita configurar, treinar e avaliar uma variedade de modelos de aprendizagem de máquinas.

Uma API de alto nível, como tf.estimator, ajuda você a gerenciar conjuntos de dados, estimadores, treinamento e inferência.

tf.estimator é uma biblioteca TensorFlow de alto nível que simplifica a mecânica da aprendizagem em máquina, incluindo o seguinte:

executando loops de treinamento
executando loops de avaliação
gerenciando conjuntos de dados

O tf.estimator define muitos modelos comuns.

Estimador: ferramentas de alto nível para trabalhar com modelos prontos.

class DNNClassifier: Um classificador para modelos TensorFlow DNN.
class DNNLinearCombinedClassifier: Um estimador para modelos de classificação TensorFlow Linear e DNN joined.
class DNNLinearCombinedRegressor: Um estimador para TensorFlow Linear e DNN juntou modelos para regressão.
class DNNRegressor: Um regressor para modelos TensorFlow DNN.
class Estimator: Classe Estimator para treinar e avaliar modelos TensorFlow.
class LinearClassifier: Modelo classificador linear.
class LinearRegressor: Um estimador para TensorFlow Problemas de regressão linear.
