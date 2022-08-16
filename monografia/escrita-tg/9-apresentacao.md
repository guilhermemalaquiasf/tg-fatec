1 Inteligência Artificial
2 Objetivo e motivação
3 Som e voz humana

4 Redes Neurais Artificiais
4.1 Cérebro humano
4.2 Neurônios artificiais
4.3 Arquiteturas de rede
4.3.1 Redes alimentadas adiante com camada única
4.4.2 Redes alimentadas adiante com múltiplas camadas
4.3.3 Redes recorrentes
4.4 Aprendizado e treinamento
4.4.1 Conjunto de dados
4.4.2 Matriz de confusão
4.5 Perceptron de camada única
4.6 Perceptron de múltiplas camadas

5 TensorFlow
5.1 Estimadores

6 Modelo de reconhecimento de fala
6.1 Espectrograma e Médodo MFCC
6.2 Corpus oral utilizado
6.3 Geração do modelo preditivo

7 Conclusão



# APRESENTAÇÃO

## 0 Capa - Introdução 0

- Cronograma da apresentação.
- Boa noite a todas e todos, meu nome é Guilherme e vou apresentar meu trabalho de graduação, entitulado "Estudo de um modelo de reconhecimento de fala construído com o Framework Tensorflow".

## 1 INTELIGÊNCIA ARTIFICIAL

- Inteligência Artificial: desde 1940.
- Automatização de atividades associadas ao pensamento humano, como a tomada de decisões, a resolução de problemas e o aprendizado (BELLMAN, 1978).
- É a arte de criar máquinas que executam funções que exigem inteligência quando executadas por pessoas KURZWEIL (1990).
- Grande volume de dados: até 2020 44 zetabytes (44 trilhões de gigabytes) por ano (IDC, 2014). Tratamento com IA.
- Vários subcampos: Sistemas Fuzzy, Árvores de Decisão, Algoritmos Genéticos, Mineração de Dados e Redes Neurais Artificiais (RNAs).
(Figuras de IA)

## 2 OBJETIVO E MOTIVAÇÃO (1)

- RNAs possuem grande relevância na atualidade: tratamento do câncer, reconhecimento de imagens e de fala.
- Frameworks e bibliotecas de programação que simplificam a construção de modelo de RNAs.
- Objetivo: explicar o desenvolvimento de uma RNA utilizando o framework de IA TensorFlow, demonstrando a capacidade e os recursos da ferramenta na construção de modelos preditivos.
- Para isso, foi utilizado como exemplo a RNA cnn-trad-fpool3 de Sainath e Parada (2015), que foi treinada no trabalho para classificar os sons dos números em inglês de zero a nove, momentos de silêncio e palavras desconhecidas: {'zero', 'one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', '_unknown_', '_silence_'}
- Importância da fala e das tecnologias relacionadas na (atual) vida humana, principalmente com a IoT (Internet of Things – Internet das Coisas).
(Figuras ?)

## 2 OBJETIVO E MOTIVAÇÃO (2)


- IoT: Em 2013 nove bilhões de dispositivos interligados, em até 2020 vinte e quatro bilhões (GUBBI et al., 2013).
- Hardware:
  - Memória: 6 GB, DDR3, 1333 MHz.
  - Armazenamento: SSD, 120 GB, R/W 540 MB/s.
  - SWAP: 8 GB (no SSD).
  - Processador: i3-3110M, 64 bits, 4 núcleos, 2,4 GHz.
- Software:
  - GNU/Linux Debian 9.0.1.
  - Docker 17.06.2.
  - Imagem Docker: tensorflow/tensorflow:nightly-py3.
(Figuras de tecnologias de fala e IoT?)

# 3 Som e voz humana

O som é uma onda longitudinal (deslocamentos paralelos à direção da propagação da onda) se propagando em um meio, geralmente, o ar (YOUNG et al., 2003).
- O ouvido humano possui grande sensibilidade, detectando ondas sonoras com frequências entre 20 Hz e 20000 Hz.
- Praticamente todos os sons da fala são gerados pela ação do aparelho fonador:
  - Pulmões, brônquios e traqueia.
  - Laringe.
  - Cavidades supralaríngeas (faringe, boca e fossas nasais).

## 4 REDES NEURAIS ARTIFICIAIS

- Campo que pesquisa modelos matemáticos baseados no funcionamento do cérebro humano (BRAGA; CARVALHO; LUDERMIR, 2000).
- Pouco conhecimento sobre o cérebro humano, mas conhece-se sua estrutura fisiológica básica, fundamento das RNAs.

### 4.1 CÉREBRO HUMANO

- Extremamente complexo, processa informações de forma não linear e paralela muito mais rapidamente que o mais poderoso computador ou datacenter atuais (HAYKIN, 2007).
- Eventos em circuitos: 10^−9 s; nos neurônios: 10^−3 s.
- Compensação da velocidade de operação: 100 bilhões de neurônios, interligados a outros 6000, perfazendo um total de 600 trilhões de sinapses.
- Eficiência energética do cérebro: 10^−16 J/op./s; dos melhores computadores atuais: 10^−6 J/op/s.
(Figuras do cérebro e dos neurônios biológicos)

### 4.2 NEURÔNIOS ARTIFICIAIS

- Baseados nos neurônios biológicos.
- O modelo do neurônio de McCulloch e Pitts (1943), MCP, é o mais utilizado nas arquiteturas de RNAs:
  1. Terminais de entrada.
  2. Pesos.
  3. Combinador linear.
  4. Limiar de ativação (bias, threshold).
  5. Potencial de ativação.
  6. Função de ativação.
  7. Sinal de saída.
  (Figuras do MCP e do Mark I - Perceptron)

### 4.3 ARQUITETURAS DE REDE (1)

- Implementadas por programação, ou diretamente em circuitos.
- Define os tipos de problema que a rede pode resolver.
- Um problema linearmente separável é dado quando as classes deste, representadas em um hiperplano, podem ser separadas por uma reta.
- Redes MCP, que possuem uma única camada de neurônios, por exemplo, só conseguem resolver problemas linearmente separáveis.
(Figura problemas linearmente separáveis)

### 4.3 ARQUITETURAS DE REDE (2)

- Toda rede possui os seguintes parâmetros: número de camadas de rede, número de neurônios por camada, tipo de conexão entre os neurônios e a topologia da rede
- Uma RNA pode ser dividida em três partes (camadas), que são:
  - Camada de entrada: Responsável pelo recebimento de dados do meio externo.
  - Camadas ocultas: São compostas de neurônios que extraem as características associadas ao sistema a ser inferido. Quase todo o processamento interno da rede é realizado nessas camadas.
  - Camada de saída:  São produzidos os resultados finais da rede.
- Existem basicamente três tipos de arquitetura de redes.

### 4.3 ARQUITETURAS DE REDE (3)

#### 4.3.1 Redes alimentadas adiante com camada única

- Feedforward ou acíclicas: O fluxo dos dados se dá para frente, ou seja, não existe realimentação.
- Principais: Perceptron e Adaline.
(Figura rede feedforward de camada única)

### 4.3 ARQUITETURAS DE REDE (4)

#### 4.4.2 Redes alimentadas adiante com múltiplas camadas

- Distinguem-se pela presença de uma ou mais camadas ocultas. Os nós de entrada fornecem os dados de entrada aos neurônios da segunda camada e assim por diante, até a camada de saída, que produz a resposta global da rede.
- Principais: Perceptron Multicamadas (Multilayer Perceptron - MLP) e as Redes de Base Radial (Radial Basis Function).
(Figura Rede feedforward com múltiplas camadas.)

### 4.3 ARQUITETURAS DE REDE (5)

#### 4.3.3 Redes recorrentes

- São redes feedforward de múltiplas camadas que possuem realimentação (recorrência).
- Principais: Hopfield e a Perceptron multicamadas com realimentação.
(Figura Redes recorrentes)

### 4.4 APRENDIZADO E TREINAMENTO (1)

- Um conjunto de passos ordenados com o intuito de ajustar os pesos e os bias dos neurônios da rede para que as suas respostas estejam próximas dos valores desejados, é chamado de algoritmo de aprendizagem.
- A aplicação do algoritmo de aprendizagem sobre um conjunto de dados (conjunto de treinamento) do domínio do problema a ser resolvido é chamado de treinamento.
- O conceito de aprendizado em RNAs foi introduzido por Rosenblatt (1958), com o Perceptron.

### 4.4 APRENDIZADO E TREINAMENTO (2)

- Existem diversos tipos de algoritmos de aprendizagem, cada um com suas vantagens e desvantagens. O que os diferencia é a forma pela qual o ajuste dos pesos é feito (BRAGA; CARVALHO; LUDERMIR, 2000).
- O procedimento de aprendizagem é iterativo: modifica ligeiramente os pesos e os bias usando o conjunto de treinamento para cada ciclo de aprendizagem (época).
- Em cada época, os pesos devem ser modificados para minimizar uma função de custo (ou erro) específica para o problema em questão.
- As funções de custo mais comumente utilizadas são a do erro quadrático e a cross-entropy.

### 4.4 APRENDIZADO E TREINAMENTO (3)

- É possível agrupar os algoritmos de aprendizado em aprendizado supervisionado e não supervisionado.
- No aprendizado supervisionado há um supervisor (professor) que fornece as entradas e a saídas desejadas para a rede, indicando quais são os comportamentos “bons” e “ruins” para direcionar o treinamento da rede.
 - A cada entrada compara-se a resposta desejada com a resposta real calculada, ajustando-se os pesos das conexões para minimizar o erro, que é definido como a diferença entre a resposta desejada e a resposta dada pela rede.
 - A RNA utilizada para o trabalho possui um algoritmo de aprendizagem supervisionado.
- No aprendizado não supervisionado, não há um professor para supervisionar o processo de aprendizagem, logo, não existe o fornecimento de entradas e saídas desejadas para a rede.
- A  própria rede se organiza para identificar subconjuntos que contenham similaridades.

### 4.4 APRENDIZADO E TREINAMENTO (4)

#### 4.4.1 CONJUNTO DE DADOS

- É importante analisar o conjunto de dados para o aprendizado e a convergência da rede.
- O conjunto de dados é dividido aleatoriamente em três subconjuntos: um de treinamento, um de validação e um de teste.
- O de treinamento será utilizado exclusivamente para o treinamento da rede.
- Os subconjuntos de validação e teste são utilizados para verificar a precisão da rede para entradas não utilizadas no treinamento, ou seja, para analisar o grau de generalização do modelo.
- No trabalho: 80% treinamento, 10% validação e 10% teste.

### 4.4 APRENDIZADO E TREINAMENTO (5)

#### 4.4.2 MATRIZ DE CONFUSÃO

- É uma forma de medir o desempenho de um algoritmo de aprendizado, verificando como o modelo está dividindo as classes (TING, 2011), (RICHERT; COELHO, 2013).
- Representação bidimensional do cruzamento entre as informações das amostras reais e as classificações realizadas por um sistema de classificação, onde cada linha da matriz representa as amostras reais do conjunto, enquanto cada coluna representa as previsões realizadas pelo sistema de classificação.
- Os dados da matriz são extraídos das classificações realizadas com os subconjuntos de validação e/ou de teste.
(Figura matriz de confusão)
- Todas as classificações da diagonal principal são acertos, enquanto todas as outras são erros. Um modelo perfeito teria uma diagonal principal totalmente preenchida, com os outros valores zerados.
- O nome da ferramenta decorre do fato de que é possível verificar se o modelo treinado está confundindo as classes.

### 4.5 PERCEPTRON DE CAMADA ÚNICA

- Possuem apenas uma camada neural com apenas um neurônio MCP, com treinamento supervisionado com o algoritmo backpropagation.
- Resolve somente problemas linearmente separáveis.
(Figura Perceptron, Perceptron original, Treinamento do Perceptron de camada única e Algoritmo aprendizagem?)

### 4.6 PERCEPTRON DE MÚLTIPLAS CAMADAS

- São redes completamente conectadas, caracterizadas por uma camada de entrada e uma ou mais camadas ocultas de neurônios.
- A utilização de uma camada oculta é suficiente para aproximar qualquer função contínua, e duas camadas intermediárias são suficientes para aproximar qualquer função matemática (BRAGA; CARVALHO; LUDERMIR, 2000).
(Figuras MLP, Treinamento)

#### 5 TensorFlow (1)

- Desenvolvido por pesquisadores da Google Brain Team, o TensorFlow é uma interface opensorce lançada em 2015 para desenvolvimento e execução de algoritmos de Aprendizado de Máquina (ABADI et. al, 2015).
- Pode ser executado em várias arquiteturas: dispositivos móveis, sistemas distribuídos com milhares de GPUs e CPUs, etc.
- Utilizada: Google, Airbnb, Ebay, Dropbox, Intel, IBM, Snapchat, Twitter e Uber.
- Composto por:
  - TensorFlow: Biblioteca de programação (para Python e C++.) para o aprendizado de máquina.
  - TensorBoard: Ferramenta que permite a visualização de estruturas gráficas e estatísticas.
  - TensorFlow Serving: Ferramenta que facilita a instalação de modelos TensorFlow pré-treinados.
- Instalação: Realizada utilizando uma imagem Docker.

### 5 TENSORFLOW (2)

- É uma estrutura para definir e executar cálculos envolvendo os chamados tensores.
- Um tensor é uma generalização de vetores e matrizes.
- Com a biblioteca, o principal objeto manipulado é o tf.Tensor, que possui as seguintes propriedades: um tipo de dado (como um float32), e uma forma, que representa o número de dimensões do tensor.
(Figura da operação de soma com o tensorflow e a Figura do código)

#### 5.1 ESTIMADORES

- API que simplifica a configuração, o treinamento e a avaliação de modelos de aprendizagem de máquina.
- São baseados na classe tf.estimator e encapsulam as ações de treinamento, avaliação, predição e exportação de modelos.
- É possível construir estimadores manualmente ou utilizar os estimadores prontos.
- Os estimadores prontos permitem o uso de diferentes arquiteturas com poucas alterações de código:
  - DNNClassifier: Classificador para RNAs profundas (várias camadas) feedforward.
  - DNNRegressor: Método de regressão para modelos RNP.
  - LinearClassifier: Modelo de classificador linear.
  - LinearRegressor: Estimador para problemas de regressão linear.

### 6 MODELO DE RECONHECIMENTO DE FALA

- Rede utilizada: cnn-trad-fpool3, de Sainath e Parada (2015), construída com o TensorFlow.
- Possui três componentes principais: (a) um módulo de extração de características, (b) a rede neural profunda propriamente dita e (c) um módulo de tratamento posterior.
(Figura rede doida)
- No fim de todas as etapas, a rede atualiza 244,2 mil parâmetros e realizado 9,7 milhões de operações de multiplicação.

#### 6.1 ESPECTROGRAMA E MÉTODO MFCC (1)

- São transformações pelas quais as amostras de som passam antes de servirem como entrada para a rede.
- Espectrograma é uma forma de se representar as informações de áudio como uma série de fatias de informações de frequência, uma para cada intervalo de tempo do áudio.
- A geração do espectrograma utiliza um algoritmo chamado Transformada Rápida de Fourier.
- Gera saída bidimensional (tridimensional para sons em estéreo), com as dimensões:
  - Amplitude de cada frequência durante a fatia de tempo.
  - Tempo, com as sucessivas fatias de frequência.

#### 6.1 ESPECTROGRAMA E MÉTODO MFCC (2)

- No TensorFlow, na representação em uma imagem, o espectrograma teria o tempo representado pelo eixo Y e a frequência representada pelo eixo X, ao contrário da convenção usual para espectrogramas.
(Figura Espectrograma TensorFlow)
- O método MFCC descarta algumas das frequências mais altas do som que são menos significativas para o ouvido humano, transformando os dados em uma forma útil para o reconhecimento de fala.
- Unindo as fatias do espectrograma em uma sequência, forma-se uma representação única do som ao longo do tempo, chamada de impressão digital (fingerprint) MFCC.

#### 6.2 CORPUS ORAL UTILIZADO

- O corpus oral utilizado, de Warden (2017), é distribuído sob a licença “Creative Commons Attribution 4.0 International Public License”.
- Composto por 64.727 arquivos de áudio (compactados em um arquivo de 2.3 GB) de 1 s, cada um contendo uma palavra falada em por uma pessoa diferente (organizados por palavras em pastas).
- Formato WAVE 16-bit little-endian PCM-encoded e uma taxa de amostragem de 16000 Hz.
- Base coletada utilizando crowdsourcing, por meio da plataforma web do grupo AIY Projects (2017).
- As gravações são de pessoas dizendo: "Yes", "No", "Up", "Down", "Left", "Right", "On", "Off", "Stop", "Go", "Zero", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Bed", "Bird", "Cat", "Dog", "Happy", "House", "Marvin", "Sheila", “Tree” e “Wow”.

### 6.3 GERAÇÃO DO MODELO PREDITIVO (1)

- Para gerar o modelo de reconhecimento dos dez comandos de voz, foram executadas as seguintes ferramentas de software (onde a test_streaming_accuracy.cc é em C++ e as outras em Python):
  1. train.py: Realiza todos os processos para o download da base de voz (ou utilização de uma já existente), tratamento dos áudios de entrada (espectrograma, MFCC, inserção de ruídos e silêncio, etc.), o treinamento, as validações, os teste e gera arquivos com os parâmetros da rede treinada.
  2. freeze.py: Gera um modelo "congelado" utilizável.
  3. generate_streaming_test_wav.py: Gera um arquivo de áudio de 10 min com palavras do conjunto de teste, inseridas a cada dois segundos, com ruídos de fundo e trechos de silêncio (em posições aleatórias) e um arquivo de texto com as informações do momento em que cada palavra foi pronunciada.
  4. test_streaming_accuracy.cc: Utiliza o modelo congelado com o áudio de 10 min e imprime as estatísticas de precisão.
  5. label_wav.py: Testa o modelo congelado com um determinado arquivo de som e imprime as estatísticas de acerto.

### 6.3 GERAÇÃO DO MODELO PREDITIVO (2)

- A execução do script train.py consumiu um processamento de 99% dos quatro núcleos da CPU com:
  - Duração total do programa: 31 horas, 21 minutos e 12 segundos.
  - Duração do treinamento e validações: 31 horas, 17 minutos e 10 segundos.
  - Duração dos testes: 1 min e 13 segundos.
(Figura Informações do treinamento e validação da rede e Figura Matriz de confusão do processo de testes da rede.)
- Acurácia: 89% no fim do treinamento; 91.1% na última validação e 90.9% nos testes.
- Classificações dos testes realizados com um áudio contínuo de dez minutos:
  - 96.5% de acertos.
  - 3.5% de erros.
  - 0% de falsos positivos.
- Testes realizados com vinte palavras isoladas: 90% de acertos.

### 7 Conclusão (1)

- A rede apresentou classificações com altos níveis de acurácia e baixos níveis de erros e falsos positivos, tanto no treinamento, quanto nas validações, nos testes e nas simulações posteriores.
- Com o TensorFlow, em poucas linhas, utilizando métodos prontos e uma alta abstração, foi possível desenvolver uma robusta RNA, o que permitiu concluir que, com o framework, é possível construir poderosos modelos preditivos de forma simplificada.
- O modelo gerado estaria pronto para ser utilizado em um aplicativo de reconhecimento de voz para smartphone, por exemplo.
- Realizações pessoais:
  - Conhecimento obtido, tanto com os campos de IA e das RNAs, quanto com as tecnologias utilizadas para o desenvolvimento do trabalho: GitHub, Bitbucket, Limarka, LaTEX, Bibtex, Docker, git, VirtualBox e Markdown.

### 7 Conclusão (2)

  - Desenvolver a pesquisa utilizando trabalhos da empresa Google me permitiu perceber a grandiosidade da empresa, tanto pela complexidade e capacidade de seus sistemas (onde vários são opensource) quanto pela qualidade de suas pesquisas acadêmicas (que também são abertas, disponíveis para leitura).
- Dificuldades:
  - Tempo para compreensão da carga conceitual das RNAs.
  - A baixa capacidade de processamento da CPU utilizada.
  - Encontrar corpus orais em português gratuitos e disponíveis para uso.
  - O TensorFlow ainda possui muitos bugs e bibliotecas em desenvolvimento (pPor ser muito recente).




Refer
