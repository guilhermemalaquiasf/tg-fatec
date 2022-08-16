# INTRODUÇÃO

Desde 1940 um ramo da ciência que vem ganhado grande evidência é o da Inteligência Artificial (IA). Como discutido no trabalho de \citeonline[p. 8, tradução nossa]{minsky1961steps}, não existe nenhuma definição universalmente aceita de inteligência, logo, delimitar o que é Inteligência Artificial é um assunto complexo e polêmico.

No entanto, um conceito que pode ser visto na pesquisa de \citeonline{bellman1978introduction} é que IA é a automatização de atividades associadas ao pensamento humano, como a tomada de decisões, a resolução de problemas e o aprendizado. Ou ainda, de acordo com \citeonline{kurzweil1990age}, IA é a arte de criar máquinas que executam funções que exigem inteligência quando executadas por pessoas.

Por conseguinte, em um mundo cada vez mais conectado, é consenso que na história da humanidade nunca se produziu tantos dados quanto atualmente. Estima-se que até 2020 estejam sendo gerados 44 zetabytes (44 trilhões de gigabytes) de dados por ano. \cite{idc2014digital}.

Nesse contexto, a IA surge como uma importante ferramenta para lidar e interpretar esse colossal volume de dados.

Assim, atualmente a IA abrange uma enorme variedade de subcampos, do geral, de aprendizagem e percepção, até tarefas específicas, como jogos de xadrez, demonstração de teoremas matemáticos, criação de poesia, carros autônomos e diagnóstico de doenças. A IA é relevante para qualquer tarefa intelectual; é verdadeiramente um campo universal. \cite{russel2013inteligencia}

Existem diversos subcampos de pesquisa dentro da IA, dentre eles: Sistemas _Fuzzy_, Árvores de Decisão, Algoritmos Genéticos, Mineração de Dados e Redes Neurais Artificiais. Desses subcampos, um dos que mais tem suscitado estudos e aplicações no século XXI é o de RNAs, que faz parte também do campo de pesquisa de Aprendizado de Máquina.

Dessa forma, como pode ser visto na obra de \citeonline[p. 2]{braga2000redes}, a capacidade de aprender através de exemplos e de generalizar a informação aprendida é o principal atrativo da solução de problemas através de RNAs.

Por conseguinte, percebe-se que nos últimos anos surgiram vários tipos de sistemas, APIs [^APIs], bibliotecas de _software_ e frameworks [^frameworks] para o desenvolvimento de modelos computacionais baseado RNAs e aprendizado de máquina[^Aprendizado de máquina] (AM). Esses sistemas possibilitam simplicidade, maior produtividade e recursos para análise e desenvolvimento dos modelos de IA. Alguns exemplos destes sistemas, por linguagem, são listados a seguir.

[^Frameworks]: Abstração de programação com um conjunto de objetos para simplificação do desenvolvimento de _software_ de determinada linguagem. 
[^APIs]: _Application Programming Interface_, conjunto de padrões de programação para acesso a um sistema de _software web_.
[^Aprendizado de máquina]: Aprendizado de Máquina (_Machine Learning_) é a area que busca dar capacidade de aprendizado a sistemas computacionais.

- Java: Deeplearning4j, do \citeonline{deeplearning4j2017}.
- Python: Theano, de \citeonline{rami2016theano}, Keras, de \citeonline{chollet2015keras}, Microsoft Cognitive Toolkit, do grupo \citeonline{microsoft2017cognitive}, Caffe, de \citeonline{jia2014caffe}, Chainer, de \citeonline{tokui2015chainer} e TensorFlow, de \citeonline{abadi2015tensorflow}.
- C++: Kaldi, de \citeonline{kaldi2011}.

Assim, o objetivo desse trabalho é explicar o processo de desenvolvimento de um modelo preditivo baseado em uma RNA utilizando um _framework_ de desenvolvimento de sistemas de AM. Dessa forma, será demonstrada a criação de um modelo de Rede Neural Convolucional (RNC) para o reconhecimento de comandos de voz utilizando o _framework TensorFlow_.

Sendo assim, para o treinamento do algoritmo de RNC foram utilizadas gravações de voz de diferentes locutores pronunciando em inglês os números de zero a nove. Após o treinamento, o modelo foi capaz de reconhecer – com **95%** **(coloco o resultado final ou sou só coloco na Conclusão?)** de precisão a verbalização dos dez números, independente de locutor.

As razões que motivaram a escolha do TensorFlow como o sistema de IA foram:

- A linguagem da API é Python, que atualmente é extensivamente utilizada para a análise de dados.
- É um sistema que desde de 2015 vem sido muito utilizado pela comunidade de IA para várias aplicações comerciais e científicas.
- As críticas da comunidade de IA ao _software_ têm sido bastante positivas.

Por conseguinte, o que motivou a escolha da área de reconhecimento de fala como o domínio para a construção do modelo preditivo foi a importância da fala na vida humana. Verifica-se que a fala está intimamente ligada à história e ao desenvolvimento de nossa espécie, logo, nada mais natural do que o desenvolvimento de tecnologias relacionadas à fala.

Atualmente a utilização de tecnologias de reconhecimento de fala tornou-se bastante comum, principalmente com a popularização dos dispositivos móveis. Tais aplicações facilitam a vida das pessoas com a automatização de várias tarefas.

Ademais, percebe-se que os sistemas de reconhecimento de fala estão em alta no momento, principalmente por conta da ascensão da IoT (_Internet of Things_ - Internet das Coisas), uma área que utiliza bastante os algoritmos do tipo.

Para uma melhor percepção do quanto a IoT é promissora (e por consequência a pesquisa em reconhecimento de fala), na pesquisa de \cite[p. 1645, tradução nossa]{gubbi2013internet}, calculou-se que em meados de 2013 existiam cerca de 9 bilhões de dispositivos interligados, e espera-se que até 2020 existam cerca de 24 bilhões.

Sendo assim, para o treinamento da RNC foi utilizado um computador com as seguintes características:

- _Hardware_:
 + Memória: 6 GB, DDR3, 1333 MHz
 + Armazenamento: SSD, 120 GB, R/W 540 MB/s
 + SWAP: 16 GB (no SSD)
 + Processador: i3-3110M, amd64, 4 núcleos, 2.4 GHz
- _Software_:
 + GNU/Linux Debian 9.0.1
 + Docker 17.06.2
 + Imagem Docker: tensorflow/tensorflow:nightly-py3

Por fim, a respeito da estrutura do trabalho, no segundo capítulo foi realizada uma caracterização da natureza do som e da voz humana, além da apresentação de um breve histórico dos sistemas de reconhecimento de fala.

No terceiro capítulo foi desenvolvida uma apresentação dos conceitos gerais sobre as Redes Neurais Artificiais e especificamente das Redes Neurais Convolucionais.

Já no quarto capítulo abordou-se o sistema TensorFlow, com seus principais conceitos, instruções de instalação e funcionamento geral.

O quinto capítulo possui a descrição do desenvolvimento do modelo de RNC para reconhecimento de comandos de voz utilizando o _framework_ TensorFlow, além da demonstração de cada etapa do treinamento da RNC.

No sexto capítulo foram discutidas as conclusões e resultados do trabalho.