# 5 EXPERIMENTO

Como citado anteriormente, é uma boa prática separar o conjunto de dados em três categorias. No experimento, o maior (neste caso cerca de 80% dos dados) é usado para treinar a rede, um conjunto menor (10% aqui, conhecido como "validação") é reservado para avaliação da precisão durante o treinamento e outro conjunto (o último 10%, "teste") é usado para avaliar a precisão uma vez após a conclusão do treinamento.

O script de treinamento separa automaticamente o conjunto de dados nessas três categorias.

## EXECUÇÃO DO PROJETO

1. Executar o programa train.py (python3 train.py):
  - Monta os conjunto de: a) treinamento, com os áudios necessários, inclusive os "unknown" e os "silence" (que são escolhidos áudios arbitrários e depois sua matriz de características são zeradas) seus rótulos corretos, o som é nivelado, as amostras são fatiadas, é adicionado ruído, etc, b) validação e c) testes.
  - Pega o conjunto de áudio e transforma em espectograma
  - Pega os espectrogramas e faz MFCC, que retorna uma matriz de características
  - Pega as matriz de características e usa como entrada para treinar o modelo de RNC cnn-trad-fpoll3
2. Gerar modelo "congelado" com freezy.py
```
python3 freeze.py \
--start_checkpoint=/tmp/speech_commands_train/conv.ckpt-18000 \
--output_file=/tmp/my_frozen_graph.pb
```
3. Gerar arquivão de som pra ser utilizado com o modelo congelado pelo script generate_streaming_test_wav.py:
```
bazel run generate_streaming_test_wav
```
4. Testar arquivão para ter estatística de acertos, erros e falsos positivos como test_streaming_accuracy.cc.
```
bazel run test_streaming_accuracy -- \
--graph=/root/my_frozen_graph.pb \
--labels=/tmp/speech_commands_train/conv_labels.txt \
--wav=/tmp/speech_commands_train/streaming_test.wav \
--ground_truth=/tmp/speech_commands_train/streaming_test_labels.txt \
--verbose
```
5. Testar arquivos específicos com modelo congelado em algum áudio com label_wav.py
```
python3 label_wav.py \
--graph=/tmp/my_frozen_graph.pb \
--labels=/tmp/speech_commands_train/conv_labels.txt \
--wav=/tmp/speech_dataset/left/a5d485dc_nohash_0.wav
```

## Espectrograma

O espectrograma é uma forma comum de se representar as informações de áudio como uma série de fatias de informações de freqüência, uma fatia para cada janela (intervalo) de tempo do áudio. Assim, unindo essas fatias em uma seqüência, forma-se representação única do som ao longo do tempo, chamada de "impressão digital".

Este método do algoritmo espera receber dados de áudio como uma entrada, armazenados como números reais entre o intervalo de -1 a 1, além da largura da janela das amostras (que por padrão é 480 ms) e o valor do passo especificando o quão longe mover a janela entre as fatias (que por padrão é 160 ms).

A partir disso, o programa gera uma saída tridimensional:
- Amplitude de cada freqüência durante a fatia de tempo (é a menor dimensão),
- Tempo, com as sucessivas fatias de freqüência e,
- Canais do áudio de entrada, assim, uma entrada de áudio estéreo por exemplo, teria dois canais.

Isso significa que o layout quando convertido e salvo como uma imagem é girado 90 graus no sentido horário de um espectrograma típico.

Por causa da ordem da memória de TensorFlow, o tempo nesta imagem está aumentando de cima para baixo, com freqüências indo da esquerda para a direita, ao contrário da convenção usual para espectrogramas onde o tempo é de esquerda para a direita. Você deve poder ver algumas partes distintas, com a primeira sílaba "Ha" distinta de "ppy".

O tempo é representado descendo pelo eixo Y e a frequência diminui da esquerda para a direita. Cada valor no resultado representa a raiz quadrada da soma das partes real e imaginária de uma FFT [^FFT] na janela atual das amostras. Desta forma, a menor dimensão representa a potência de cada freqüência na janela atual e as janelas adjacentes são concatenadas na próxima dimensão.

[^FFT]: Fast Fourier Transform, em português Transformada Rápida de Fourier, é um algoritmo para se calcular a Transformada Discreta de Fourier, que é o método para se calcular o Espectrograma do algoritmo.

Para obter um visual mais intuitivo e visual sobre o que esta operação faz, você pode executar o script para ler um arquivo de áudio e salvar o espectrograma resultante como uma imagem .png.

### MFCC

MFCC (Mel Frequency Cepstral Coefficients) é uma maneira de representar dados de áudio de entrada para o aprendizado da máquina.

Eles são criados tomando o espectro de um espectrograma (um 'cepstrum') e descartam algumas das freqüências mais altas que são menos significativas para o ouvido humano.

Transforma um espectrograma em uma forma útil para o reconhecimento de fala

# ALGORITMO

## 1 train.py

### 1.1 Parametrização inicial

Inicialmente, o script importa as bibliotecas e módulos necessários. Em seguida, na verificação de execução if __name__ == '__main__': são parametrizados os argumentos de chamada do script, são eles:
--data_url: É um argumento que recebe uma url para download da base dados e voz. Por padrão o parâmetro está configurado para baixar a base já citada, de Warden (2017).
--data_dir: É um argumento que define o diretório onde será baixada a base de dados de voz. Por padrão, o caminho é "/tmp/speech_dataset/'"
--background_volume: Define o quão alto o ruído de fundo deve ser, entre 0 e 1. Por padrão, o valor é 0.1.
--background_frequency: Define quantas amostras de treinamento deverão ter ruído de fundo misturado. O valor padrão é 0.8, ou seja, 80% das amostras terão ruído misturado ao som.
--silence_percentage: Argumento que define quantas amostras devem ser de silêncio, tendo por padrão a quantidade de 10%.
--unknown_percentage: Define quantas amostras devem ser de palavras desconhecidas, tendo por padrão a quantidade de 10%.
--time_shift_ms: Parâmetro que envolve um deslocamento aleatório no tempo dos dados da amostra de treinamento, de modo que uma pequena parte do começo ou do final dos áudios é cortada (em milissegundos) e a seção oposta é preenchida com zeros. Isso imita as variações naturais no tempo de início nos dados de treinamento. O argumento tem como padrão o valor de 100ms.
--testing_percentage: Porcentagem de arquivos para utilizar como conjunto de testes. Tem como padrão 10%.
--validation_percentage: Porcentagem de arquivos para utilizar como conjunto de testes. Tem como padrão o valor de 10%.
--sample_rate: Taxa de amostragem (em Hz) esperada dos arquivos wavs; tem como valor padrão 16000.
--clip_duration_ms: Duração esperada (em milissegundos) dos arquivos. Tem como valor padrão 1000.
--window_size_ms: Quantidade de tempo (em milissegundos) de cada intervalo de tempo de espectrograma. O parâmetro controla a amplitude da área usada para calcular as frequências para cada amostra, e tem como valor padrão 30.0.
--window_stride_ms: controla o quão distante (em milissegundos) cada amostra de análise de freqüência é do anterior. Tem como padrão o valor 10.0.
--dct_coefficient_count: Quantos bins usar para a MFCC fingerprint, ou seja, controla quantos compartimentos são usados para a contagem de freqüência. Este parâmetro tem como padrão o valor 40.
--how_many_training_steps: Quantos loops de treinamento deverão ser realizados, em duas partes. O parâmetro tem como padrão o valor "15000,3000", ou seja, primeiro será realizado 15000 repetições e depois 3000 de treinamento.
--eval_step_interval: Com que frequência serão realizadas avaliações dos resultados do treinamento (validações), onde o padrão do algoritmo é 400. Ou seja, a cada 400 repetições de treinamento sreá realizada uma avaliação dos resultados do treinamento.
--learning_rate: Tamanho da taxa de aprendizado, que controla a velocidade das atualizações de peso da rede para usar no treinamento Tem como valor padrão "0.001,0.0001", ou seja, nos primeiros loops do treinamento, no caso 15000, a taxa de aprendizado será de 0.001 e nos 3000 restantes a taxa será de 0.0001.
--batch_size: Quantos itens serão treinados por vez, onde o valor padrão é 100.
--summaries_dir: Onde salvar registros de resumo para o TensorBoard, onde o valor padrão é o caminho "/tmp/retrain_logs".
--wanted_words: Rótulos das palavras que serão usadas. O valor padrão foi alterado para "zero,one,two,three,four,five,six,seven,eight,nine". O script adicionará por padrão o rótulo _silence_ para amostras de silêncio e o _unknown_ para amostras que não coincidem com as palavras configuradas
--train_dir: Diretório para escrever os logs de eventos e os ponto de verificação. O caminho padrão é "/tmp/speech_commands_train".
--save_step_interval: Frequência de salvamento do ponto de verificação do modelo de treinamento, onde o valor padrão é 100.
--model_architecture: Argumento que define a o modelo de rede que será utilizado. No caso, o parâmetro utiliza por padrão o tipo "conv", que no código corresponde a rede cnn-trad-fpoll3, de Sainath (2015), abordada anteriormente.
--check_nans: Verificação ou não de números inválidos durante o processamento. Tem false como valor padrão.

## 1.2 Método principal main()

### 1.2.1 Operações anteriores ao treinamento

Inicialmente, com tf.logging.set_verbosity(tf.logging.INFO) são ativadas as mensagens de log e com sess = tf.InteractiveSession() é iniciada uma nova sessão do TensorFlow, com tf.InteractiveSession().

Em seguida, com model_settings = models.prepare_model_settings(...) é chamada a função prepare_model_settings() do módulo models.py, que retorna o seguinte dicionário com parâmetros necessários para o modelo:
{
  'desired_samples': 16000,
  'window_size_samples': 480,
  'window_stride_samples': 160,
  'spectrogram_length': 97,
  'dct_coefficient_count': 40,
  'fingerprint_size': 3880,
  'label_count': 10,
  'sample_rate': 16000
}

Em seguida, com audio_processor = input_data.AudioProcessor(...) é importada a classe AudioProcessor do módulo input_data.py,  que manipula o carregamento, o particionamento e a preparação de dados de treinamento de áudio, que possui os seguintes métodos:

maybe_download_and_extract_datase: verifica se a base de dados já foi baixada para o diretório parametrizado, caso negativo, realiza o download (e depois descompacta o arquivo), e, do contrário, o programa utilizará a base já baixada.

prepare_data_index(): analisa as pastas dentro do "data_dir", calcula as etiquetas corretas para cada arquivo de áudio (incluindo as de silêncio - "silence" e desconhecidas - "unknown") com base no nome do subdiretório a que pertence e para atribui este a uma partição do conjunto de dados, além de .

prepare_background_data(): procura uma pasta (com o nome _background_noise_) para os áudios de ruído de fundo e carrega-a na memória.

prepare_processing_graph(): constrói um gráfico TensorFlow para aplicar as distorções de entrada: cria um grafo que carrega um arquivo WAVE, o decodifica, escala seu volume, faz o deslocamento no tempo, acrescenta o ruído de fundo, calcula um espectrograma e, em seguida, cria uma impressão digital MFCC a partir disso. (FAAAAAAAAZ A PORRA TODA)

set_size: Calcule o número de amostras na partição do conjunto de dados.

get_data: Reúne amostras do conjunto de dados, aplicando transformações conforme necessário (como a criação de amostras de silêncio, ou seja, uma amostra só de zeros), retornando uma lista de dados de amostra para as amostras transformadas e uma lista de rótulos.

get_unprocessed_data(): Recupera os dados de amostra para a partição fornecida, sem transformações, retorna uma lista de dados de amostra para as amostras e lista de rótulos.

Em seguida, na linha 125, com fingerprint_input = tf.placeholder(...) é gerado um tensor com o nome fingerprint_input, do tipo float32, na forma de um array [None, fingerprint_size], com fingerprint_size = 3880, ou seja, é um tensor que representa uma matriz com uma quantidade de linhas ainda não definida e 3800 colunas.

Por conseguinte, na linha 128, nas variáveis logits, dropout_prob são salvos os resultados do método create_model() do módulo models.py, que constrói a estrutura da arquitetura conv (cnn-trad-fpoll3) com os parâmetros adequados de rede convolucional: camadas de convolução, parametrização dos pesos e bias, ReLU, maxpool, etc.

Em seguida, na linha 135, com ground_truth_input = tf.placeholder(...) é gerado um tensor com o nome groundtruth_input, de tipo indefinido, na forma de um array [None, label_count], com label_count = 10, ou seja, é um tensor que representa uma matriz com uma quantidade de linhas ainda não definida e 10 colunas. Este tensor será utilizado para definir perda (custo) e o otimizador.

Entre as linhas 141 e 161 é criada a estrutura para o backpropagation e a avaliação de treinamento no grafo.

Na linha 166 as variáveis globais são salvas, para caso o script seja interrompido, em uma próxima execução as variáveis possam ser recaregadas com os mesmos valores.

Entre as linhas 169 e 172 são juntados todos os resumos do treinamento e da validação e escritos em "/tmp/retrain_logs/".

Na linha 176 a variável start_step é inicializada com o número 1, indicando que o treinamento irá inicializar da época 1.

Na linha 178, caso o programa tenha sido chamado com o parâmetro --start_checkpoint, será recarregado o modelo pré-treinado anteriormente, com todas as variáveis e parâmetros, inclusive a variável start_step, indicando em qual época o treinamento irá inicializar.

Na linha 182 com tf.logging.info(...) é impressa na tela a informação de qual passo (ou época) o treinamento está sendo iniciado.

Na linha 185 o grafo do treinamento com todos os parâmetros do momento é salvo no arquivo conv.pbtxt em /tmp/speech_commands_train e na linha 189 é salvo um arquivo com o nome conv_labels.txt no mesmo diretório com os rótulos definidos (mais o _silence_ e o _unknown_).

### 1.2.2 TREINAMENTO

Da linha 194 até a 196 259 é realizado o treinamento.

Da 196 a 200 é definido o valor da taxa de aprendizado para o loop, para os primeiros 15000 a taxa é de 0.001 e para os 3000 restantes a taxa é de 0.0001.

Na linha 202 são inseridas as amostras de áudio que serão utilizadas para treinar.

Da linha 206 até a 216 é executado o grafo com este lote de dados de treinamento inserido.

Na linha 220, com train_writer.add_summary(...), são salvas as informações do modelo e do treinamento do momento.

Na linha 221 são impressas em tela as seguintes informações:
- step: é o número passo (loop, época)
- rate: o valor do learning rate, que é 0.001 para os 15000 primeiros loops e 0.0001 para os 3000 últimos
- accuracy: é a precisão, a quantidade de classes previstas corretamente em uma looping específico do treinamento. O modelo emite uma matriz de números, um para cada rótulo, e cada número é a probabilidade prevista de a entrada ser essa classe. O rótulo previsto é selecionado escolhendo a entrada com a pontuação mais alta. A pontuação está sempre entre zero e um, onde valores mais altos representam maior confiança no resultado.
- cross entropy: entropia cruzada, é o resultado da função de custo utilizada para orientar o processo de treinamento. A pontuação é obtida comparando o vetor de pontuações da execução do looping de treinamento atual com os rótulos corretos, e isso deve diminuir para baixo durante o treinamento.

Da linha 225 à linha 251, a cada 400 passos (e no último passo), o código realiza a validação e, posteriormente, imprime na tela a matriz de confusão para as as predições do lote de amostras do passo, além de imprimir em seguida a precisão do passo.

Posteriormente, na linha 254, a cada 100 passos o código salva o modelo completo em um arquivo no diretório /tmp/speech_commands_train com o nome conv.ckpt-passo, onde "passo" é o passo no momento.

Assim, o código é repetido para o próximo passo, até completarem todas as 18000 iterações.

### 1.3 Testes

Por fim, após todas as iterações do treinamento terem sido realizadas, é executado o teste. Assim, é impressa na tela uma matriz de confusão relacionada aos resultados do teste, e, por fim, é impressa a porcentagem de precisão do treinamento.


Espera-se que, para a execução dos comandos esteja-se no diretório ~/tensorflow/tensorflow/examples/speech_commands.

# 2 freeze.py

Posteriormente, após treinar o modelo, é possível gerar um arquivo binário com o modelo congelado (treinado0, que pode ser utilizado para reconhecer os comandos para os quais foi treinado. O script freeze.py, que gera o modelo congelado permite a utiliização de diversos parâmetros, a maioria semelhante (e com os mesmos valores padrão) aos parâmetros utilizados no método __main__ do script train.py.

Assim, para gerar um arquivo binário com o modelo "congelado" com o nome "my_frozen_graph.pb" no caminho /tmp utilizando a última época (passo) do treinamento realizado, que no caso é o 18000 (o arquivo do modelo treinado no passo 18000 ficou salvo em /tmp/speech_commands_train/conv.ckpt-18000)  é possível utilizar o seguinte comando:
```
python3 freeze.py \
--start_checkpoint=/tmp/speech_commands_train/conv.ckpt-18000 \
--output_file=/tmp/my_frozen_graph.pb
```

# 3 generate_streaming_test_wav.py

Após gerar o modelo congelado da rede treinada, é importante realizar testes para verificar a precisão do modelo. Assim, a melhor maneira de estimar o real desempenho de um modelo de reconhecimento de áudio é executá-lo com um fluxo contínuo de dados (ao invés de amostras individuais), da maneira que ele seria usado em um aplicativo, por exemplo.

Assim, o script generate_streaming_test_wav.py gera um arquivo de áudio longo para posteriormente ser testado com o modelo treinado. O script utiliza as amostras do conjunto de teste (ou seja, amostras que não foram utilizadas para o treinamento da rede), as mistura com ruído de fundo em posições aleatórias no tempo e salva o resultado.

Assim, o script gera um arquivo de áudio .wav de dez minutos com o nome streaming_test.wav e um arquivo de texto com o nome streaming_test_labels.txt no diretório /tmp/speech_commands_train. O arquivo de texto informa o momento em que cada palavra foi pronunciada, e o arquivo .wav possui dez minutos, com palavras aproximadamente a cada três segundos. Para executar o script, foi utilizado o seguinte comando:
```
bazel run generate_streaming_test_wav
```

# 4 test_streaming_accuracy.cc

562740ms: five: 0.741772 (Wrong)
36.7% matched, 2.1% correctly, 34.6% wrongly, 0.0% false positives

37.0% matched, 2.0% correctly, 35.0% wrongly, 0.0% false positives

Em seguida, é necessário realizar o teste de precisão do modelo utilizando o arquivo do modelo congelado com o arquivo de som gerado. Para isso, é utilizado o script test_streaming_accuracy.py, que gera estatísticas de precisão.

O script permite criar estatísticas de precisão executando um modelo de reconhecimento de áudio em um fluxo contínuo de amostras. Isso é projetado para ser um ambiente para executar experimentos em novos modelos e configurações para entender os efeitos que terão em uma aplicação real.

A ferramenta processará o fluxo de áudio, aplicará o modelo e acompanhará quantos erros e sucessos o modelo alcançado.

A porcentagem compatívle (matched) representa o número de sons que foram corretamente classificados no arquivo de som, em relação ao número total de sons listados no arquivo. Uma classificação correta é quando o rótulo correto é escolhido dentro de um determinado período de tempo, onde a tolerância de tempo é controlada pelo parâmetro 'time_tolerance_ms', que tem o valor de 750 ms por padrão.

A porcentagem de erro (wrongly) representa quantos sons desencadearam uma detecção (o modelo detectou que não era silêncio ou ruído de fundo), mas a classe detectada estava errada. Esta é também uma porcentagem em relação ao número total de sons listados no arquivo de som.

A porcentagem de falso positivo (false positives) representa quantas vezes o modelo disparou quando não havia nenhuma palavra real falada (silêncio ou ruído de fundo). Esta porcentagem também é expressa em relação ao número total de sons do arquivo de som. Sobre os parâmetros ao se chamar o script, alguns possíveis são:
-"-verbose", o log de cada teste será impresso na tela e, em seguida, o resultado final e, sem o parâmetro, será impresso somente o resultado final.
--graph: o caminho do modelo congelado.
--labels: o arquivo de texto com os rótulos para os quais a rede foi treinada.
--wav: o caminho do arquivo de som.
--ground_truth: o caminho do arquivo de texto com o momento da pronúncia de cada palavra.

Assim, o script pode ser executado com o seguinte comando:

```
bazel run test_streaming_accuracy -- \
--graph=/root/my_frozen_graph.pb \
--labels=/tmp/speech_commands_train/conv_labels.txt \
--wav=/tmp/speech_commands_train/streaming_test.wav \
--ground_truth=/tmp/speech_commands_train/streaming_test_labels.txt \
--verbose
```

# 5 label_wav.py

Por fim, para testar o modelo treinado com arquivos de som específicos, é possível utilizar o script label_wav.py. Ele exdecuta o grafo treinado utilizando um arquivo .wav e imprime na tela os resultados. O modelo, os rótulos e o arquivo .wav especificados nos argumentos serão carregados, e as previsões de execução do modelo contra os dados de áudio serão impressas no console. Este é um roteiro útil para verificação de sanidade de modelos treinados e como exemplo de como usar um modelo de áudio da Python.

```
python3 label_wav.py \
--graph=/tmp/my_frozen_graph.pb \
--labels=/tmp/speech_commands_train/conv_labels.txt \
--wav=/tmp/speech_dataset/left/a5d485dc_nohash_0.wav
```




## Explicar de forma geral o experimento e como foram obtidos os áudios

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras eget venenatis leo, ut accumsan augue. Aliquam scelerisque lorem id arcu efficitur, non lacinia lectus pellentesque. Sed porttitor est quam, sed consequat erat malesuada et. Suspendisse commodo eleifend condimentum.

## Explicar o algoritmo de extração de características dos áudios e o método de normalização dos dados

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras eget venenatis leo, ut accumsan augue. Aliquam scelerisque lorem id arcu efficitur, non lacinia lectus pellentesque. Sed porttitor est quam, sed consequat erat malesuada et. Suspendisse commodo eleifend condimentum.

## Explicar a construção do algoritmo de aprendizado com o TensorFlow

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras eget venenatis leo, ut accumsan augue. Aliquam scelerisque lorem id arcu efficitur, non lacinia lectus pellentesque. Sed porttitor est quam, sed consequat erat malesuada et. Suspendisse commodo eleifend condimentum.

## Apresentação e explicação dos resultados - Resultados e discussão

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras eget venenatis leo, ut accumsan augue. Aliquam scelerisque lorem id arcu efficitur, non lacinia lectus pellentesque. Sed porttitor est quam, sed consequat erat malesuada et. Suspendisse commodo eleifend condimentum.


python3 label_wav.py \
--graph=/tmp/my_frozen_graph.pb \
--labels=/tmp/speech_commands_train/conv_labels.txt \
--wav=/tmp/speech_dataset/left/a5d485dc_nohash_0.wav
1> ~/saída-resultados.txt





python3 label_wav.py \
--graph=/tmp/my_frozen_graph.pb \
--labels=/tmp/speech_commands_train/conv_labels.txt \
--wav=/tmp/speech_dataset/left/a5d485dc_nohash_0.wav
1> ~/saída-resultados.txt


-ZERO 	1: 	64f1c742_nohash_0,wav
zero 	0,96766
_unknown_ 	0,01481
nine 	0,01392
eight 	0,00152
three 	0,00100
six 	0,00053
one 	0,00045
seven 	0,00005
two 	0,00003
five 	0,00002

-ZERO 	2: 	cc71bada_nohash_0,wav
zero 	0,99686
_unknown_ 	0,00151
two 	0,00142
nine 	0,00015
four 	0,00006
three 	0,00000
six 	0,00000
one 	0,00000
seven 	0,00000
eight 	0,00000


-ONE 	1: 	5e3dde6b_nohash_1,wav
one 	0,98134
_unknown_ 	0,01649
four 	0,00149
nine 	0,00062
five 	0,00006
zero 	0,00001
eight 	0,00000
three 	0,00000
seven 	0,00000
six 	0,00000



-ONE 	2: 	f6581345_nohash_1,wav
one 	0,96219
_unknown_ 	0,03058
nine 	0,00579
four 	0,00103
five 	0,00038
eight 	0,00001
seven 	0,00000
six 	0,00000
three 	0,00000
zero 	0,00000


-TWO 	1: 	e652590d_nohash_1,wav
two 	0,86031
seven 	0,07103
_unknown_ 	0,02699
zero 	0,01962
four 	0,01700
six 	0,00216
eight 	0,00108
five 	0,00077
three 	0,00041
one 	0,00026



-TWO 	2: 	fb01a182_nohash_0,wav
two 	0,96194
_unknown_ 	0,02007
eight 	0,01789
three 	0,00006
six 	0,00002
zero 	0,00002
seven 	0,00000
four 	0,00000
nine 	0,00000
five 	0,00000


-THREE 	1: 	c692524d_nohash_1,wav
three 	0,99087
_unknown_ 	0,00781
eight 	0,00080
six 	0,00047
nine 	0,00004
zero 	0,00001
five 	0,00001
two 	0,00000
seven 	0,00000
four 	0,00000


-THREE 	2: 	1995cf9e_nohash_0,wav
three 	0,97442
_unknown_ 	0,01507
five 	0,00875
nine 	0,00156
eight 	0,00012
four 	0,00008
six 	0,00001
one 	0,00000
zero 	0,00000
seven 	0,00000

-FOUR 	1: 	86b89661_nohash_1,wav
four 	0,93963
_unknown_ 	0,02780
two 	0,00854
one 	0,00570
zero 	0,00513
six 	0,00481
seven 	0,00329
five 	0,00211
three 	0,00150
eight   0,00060


-FOUR 	2: 	ffb86d3c_nohash_0,wav
four 	0,99958
one 	0,00018
_unknown_ 	0,00013
zero 	0,00008
two 	0,00002
three 	0,00001
eight 	0,00000
six 	0,00000
five 	0,00000
seven 	0,00000



-FIVE 	1: 	53eb0a88_nohash_0,wav
five 	0,78815
_unknown_ 	0,12183
seven 	0,04899
nine 	0,02644
six 	0,01139
eight 	0,00106
four 	0,00073
one 	0,00052
zero 	0,00047
two 	0,00020



-FIVE 	2: 	e14a99a5_nohash_0,wav
five 	0,83622
_unknown_ 	0,06204
nine 	0,06199
one 	0,02828
four 	0,00582
six 	0,00167
seven 	0,00161
three 	0,00120
zero 	0,00048
eight 	0,00046



-SIX 	1: 	fffcabd1_nohash_0,wav
six 	0,96115
seven 	0,01376
_unknown_ 	0,01133
eight 	0,00536
five 	0,00328
three 	0,00145
two 	0,00113
four 	0,00084
nine 	0,00077
zero 	0,00060


-SIX 	2: 	0a7c2a8d_nohash_0,wav
six 	0,99850
_unknown_ 	0,00099
eight 	0,00025
seven 	0,00012
five 	0,00004
two 	0,00004
four 	0,00003
three 	0,00002
zero 	0,00001
nine 	0,00000



-SEVEN 	1: 	1b88bf70_nohash_0,wav
seven 	0,96763
six 	0,01553
_unknown_ 	0,00988
five 	0,00222
eight 	0,00164
two 	0,00162
one 	0,00054
nine 	0,00040
zero 	0,00023
four 	0,00016



-SEVEN 	2: 	f8f60f59_nohash_2,wav
seven 	0,97410
_unknown_ 	0,01829
two 	0,00198
five 	0,00145
nine 	0,00115
four 	0,00099
six 	0,00067
one 	0,00059
zero 	0,00027
eight 	0,00018



-EIGH 	1: 	6f342826_nohash_0,wav
eight 	0,89536
six 	0,08653
three 	0,00921
_unknown_ 	0,00862
two 	0,00014
five 	0,00008
zero 	0,00004
nine 	0,00002
four 	0,00000
one 	0,00000



-EIGHT 	2: 	b5d1e505_nohash_3,wav
eight 	0,97557
six 	0,01203
three 	0,01052
two 	0,00105
_unknown_ 	0,00062
seven 	0,00008
five 	0,00006
zero 	0,00003
nine 	0,00002
four 	0,00001



-NINE 	1: 	0a9f9af7_nohash_0,wav
nine 	0,98840
five 	0,00641
_unknown_ 	0,00289
one 	0,00206
three 	0,00016
zero 	0,00003
six 	0,00001
seven 	0,00001
eight 	0,00000
four 	0,00000



-NINE 	2: 	3a33d3a4_nohash_0,wav
nine 	0,53633
one 	0,45327
_unknown_ 	0,01004
five 	0,00030
four 	0,00004
three 	0,00001
eight 	0,00001
seven 	0,00000
zero 	0,00000
six 	0,00000



-BED 	1: 	19f9c115_nohash_1,wav 	(ERRRRRRRRRROUUUUUUUUUUUUU!
seven 	0,71749
_unknown_ 	0,17619
five 	0,03174
nine 	0,02239
one 	0,01154
four 	0,01089
zero 	0,00996
six 	0,00832
two 	0,00547
three 	0,00371




-BIRD 	2: 	1a9afd33_nohash_0,wav
_unknown_ 	0,75936
three 	0,09522
zero 	0,08787
six 	0,01937
nine 	0,01705
five 	0,00953
four 	0,00667
seven 	0,00398
one 	0,00043
eight 	0,00031


-CAT 	3: 	4f2be90f_nohash_0,wav
_unknown_ 	0,65972
zero 	0,13430
six 	0,12026
seven 	0,02227
two 	0,02085
eight 	0,01557
nine 	0,01373
four 	0,00776
five 	0,00332
one 	0,00137




-DOG 	4: 	0f250098_nohash_1,wav
_unknown_ 	0,98122
nine 	0,01491
zero 	0,00129
five 	0,00120
four 	0,00101
one 	0,00015
six 	0,00007
two 	0,00005
seven 	0,00005
eight 	0,00002


-DOWN 	5: 	1cb788bc_nohash_0,wav
_unknown_ 	0,63743
nine 	0,13553
five 	0,05041
two 	0,03851
four 	0,03751
zero 	0,03562
seven 	0,02698
eight 	0,01266
one 	0,00978
_silence_ 	0,00666



-GO 	6: 	0eb48e10_nohash_2,wav
_unknown_ 	0,83299
five 	0,06142
nine 	0,04705
zero 	0,02126
four 	0,00994
two 	0,00928
seven 	0,00732
six 	0,00563
eight 	0,00266
one 	0,00185


-HAPPY 	7: 	1c6e5447_nohash_0,wav
_unknown_ 	0,55845
seven 	0,18181
six 	0,12493
eight 	0,09585
five 	0,01265
three 	0,00845
two 	0,00606
nine 	0,00472
zero 	0,00413
four 	0,00228



-HOUSE 	8: 	2d3c8dcb_nohash_0,wav
_unknown_ 	0,89283
five 	0,04440
four 	0,01547
six 	0,01225
nine 	0,00926
seven 	0,00870
eight 	0,00566
two 	0,00561
zero 	0,00376
one 	0,00113




-LEFT 	9: 	8c7f81df_nohash_0,wav
_unknown_ 	0,92419
nine 	0,04801
one 	0,02464
three 	0,00191
five 	0,00070
four 	0,00025
zero 	0,00018
eight 	0,00005
six 	0,00004
seven 	0,00003






-MARVIN 	10: 	20d3f11f_nohash_0,wav 	(EROOOOOOOOOOOOOOOUUUUUU
one 	0,56454
_unknown_ 	0,23309
four 	0,16287
five 	0,01565
nine 	0,01486
zero 	0,00374
three 	0,00275
six 	0,00096
eight 	0,00094
two 	0,00034




-NO 	11: 	6d2d3b0d_nohash_2,wav
_unknown_ 	0,83862
nine 	0,12740
zero 	0,01475
four 	0,00865
five 	0,00406
one 	0,00209
two 	0,00176
seven 	0,00160
six 	0,00062
eight 	0,00020

-OFF 	12: 	fffcabd1_nohash_0,wav
_unknown_ 	0,92394
five 	0,04838
six 	0,01569
four 	0,00657
eight 	0,00193
zero 	0,00144
two 	0,00060
nine 	0,00057
seven 	0,00041
one 	0,00038

-ON 	13: 	0a7c2a8d_nohash_0,wav 	(ERROUUUUUUUUUUUU!
five 	0,52667
_unknown_ 	0,25870
nine 	0,14622
one 	0,06503
three 	0,00243
seven 	0,00058
four 	0,00029
eight 	0,00005
zero 	0,00001
six 	0,00001

-RIGHT 	14: 	0ff728b5_nohash_2,wav
_unknown_ 	0,80125
nine 	0,10313
three 	0,04749
five 	0,02772
one 	0,01305
eight 	0,00596
six 	0,00056
seven 	0,00035
four 	0,00033
zero 	0,00017

-SHEILA 	15: 	05cf43ef_nohash_1,wav
zero 	0,67247
two 	0,28095
_unknown_ 	0,04424
six 	0,00096
seven 	0,00052
four 	0,00051
three 	0,00023
eight 	0,00004
nine 	0,00004
one 	0,00002




-STOP 	16: 	bed06fac_nohash_0,wav
_unknown_ 	0,89600
five 	0,03969
zero 	0,01765
nine 	0,01132
four 	0,01124
two 	0,00602
seven 	0,00581
six 	0,00555
one 	0,00335
_silence_ 	0,00260


-TREE 	17: 	f6af2457_nohash_0,wav 	(ERROU!
three 	0,63619
_unknown_ 	0,33636
nine 	0,01932
zero 	0,00769
two 	0,00011
eight 	0,00009
five 	0,00009
one 	0,00006
six 	0,00004
seven 	0,00002

-UP 	18: 	9f4098cb_nohash_1,wav
_unknown_ 	0,41148
six 	0,35673
eight 	0,12714
five 	0,08600
nine 	0,00684
seven 	0,00534
three 	0,00253
one 	0,00144
four 	0,00132
zero 	0,00058


-WOW 	19: 	09ddc105_nohash_2,wav
_unknown_ 	0,70182
nine 	0,27509
five 	0,01391
one 	0,00652
four 	0,00122
zero 	0,00099
seven 	0,00020
eight 	0,00010
six 	0,00008
two 	0,00005


-YES 	20: 	2a0b413e_nohash_0,wav
_unknown_ 	0,68311
seven 	0,09817
nine 	0,06451
two 	0,05722
zero 	0,04864
four 	0,01567
one 	0,01489
five 	0,00736
six 	0,00467
_silence_ 	0,00279
