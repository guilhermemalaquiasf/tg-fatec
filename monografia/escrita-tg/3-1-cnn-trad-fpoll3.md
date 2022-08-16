# cnn-trad-fpoll3

## Introdução

Etapas:

-Pega os áudios e transforma em espectograma, que é uma imagem
-Pega as imagens e faz MFCC, que retorna uma matriz de características
-Pega a matriz de características e treina com um modelo de RNC
    - Modelo RNC:
    - Gerar modelo "congelado"
-Gerar faixa longa de áudio e rodar com o modelo "congelado" para ter estatística de acertos, erros e falsos positivos.

## Modelo do algoritmo

Basicamente, o algoritmo consiste em três componentes principais: (i) um módulo de extração de características, (ii) uma rede neural profunda e (iii) um módulo de tratamento posterior. 

### (i)
O módulo de extração de características realiza a detecção de atividade de voz e gera um vetor de características de cada quadro de 10 ms dos arquivos de áudio. Esses vetores de características são "empilhados" para criar um vetor maior (uma matriz), que é utilizado como entrada para o e rede neural convonlucional. 

### (ii)
A rede neural profunda é uma rede com 2 camadas ocultas, uma camada de linear low rank[linear low rank], uma camada profunda feedforward completamente conectada e por fim uma camada com a função softmax, que produz uma estimativa da parte posterior de cada rótulo de saída - as palavras esperadas e mais um que representa os quadros que não pertencem a nenhuma das palavras-chave.

[^Linear low-rank]: Como verificado no trablho de \citeonline[p. 6655-6656, tradução nossa]{sainath2013lowrank}, a chamada operação Linear low-rank é um método para reduzir o tamanho do modelo da rede neural para acelerar o processo de treinamento reduzindo o tamanho da camada final (diminuindo a quantidade de parâmetros da matriz de pesos).

Os pesos e os bias da rede são estimados com o método de cross-entropy. A taxa de aprendizado é realizada com um método chamado asynchronous stochastic gradient descent with an exponential decay (descida gradiente estocástica assíncrona com uma descida exponencial).

Dessa forma, para a rede cnn-trad-fpool3, assumindo que a entrada log-mel é de dimensão t x f, t = 32 × f = 40, existem as seguintes etapas/elementos:

- Um sinal de entrada de dimensão t x f, t = 32 × f = 40
- Primeira camada oculta - Convolução: com filtro de dimensão m x r, m = 20, r = 8. O filtro de convolução da um passo de tamanho s = 1 (em relação ao tempo) e v = 3 (em relação a frequência)
- Com a primeira convolução, são gerados n = 64 matrizes de feature maps, cada uma com dimensões (t-m+1)/s x (f-r+1)/v =  (32-20+1)/1 x (40-8+1)/1 = 13 x 39
- Max-pooling de dimensão p x q, p em relação ao tempo e q em relação a frequência, p = 1, q = 3
- Depois do max-pooling as matrizes de feature maps terão dimensão (t−m+1)/sp x (f−r+1)/vq = (32−20+1)/1*1 x (40−8+1)/1*3 = 13 x 11 
- Segunda camada oculta - Convolução: com filtro de dimensão m x r, m=10, r=4. O filtro de convolução da um passo de tamanho s = 1 (em relação ao tempo) e v = 1 (em relação a frequência)
- Com a convolução, são gerados n=64 matrizes de feature maps, cada uma com dimensões (32-10+1)/1=23 x (40-4+1)/1=37
- Terceira camada - Linear low-rank, com n = 32 nós
- Quarta camada oculta - DNN: camada feed-forward totalmente totalmente conectada com n=128 nós e inserção de não linearidade ReLU
- Quinta camada oculta - função de ativação softmax, com n = 4 nós.

Este modelo de rede, apesar de possuir um alto grau de precisão para o reconhecimento de fala, demanda um alto grau de processamento. No fim de todas as etapas, a rede terá utilizado / atualizado 244.2 mil parâmetros, e realizado 9.7 milhões de operações de multiplicação (principalmente nas convoluções).

### (iii)

Finalmente, o módulo de manuseio posterior simples (iii) combina a saída da DNN (rótulos baseados em quadros) com uma pontuação de confiança das das palavras-chave em questão. Uma decisão será tomada (a escolha de algum rótulo) se a confiança exceder um certo limite pré-definido.