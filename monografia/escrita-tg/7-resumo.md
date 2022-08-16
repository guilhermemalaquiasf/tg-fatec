Em um mundo cada vez mais conectado, é consenso que na história da humanidade nunca se produziu tantos dados quanto atualmente. Estima-se que até 2020 estejam sendo gerados 44 zetabytes (44 trilhões de gigabytes) de dados por ano. (IDC, 2014). Nesse contexto, a IA surge como uma importante ferramenta para lidar e interpretar esse colossal volume de dados.

A capacidade de aprender através de exemplos e de generalizar a informação aprendida é o principal atrativo da solução de problemas através de RNAs.

Nos últimos anos surgiram vários tipos de sistemas, APIs , bibliotecas de software e frameworks  para o desenvolvimento de modelos computacionais baseados em AM e RNAs. Esses sistemas possibilitam simplicidade no desenvolvimento, maior produtividade e poderosos recursos para análise dos modelos de AM

O que motivou a escolha da área de reconhecimento de fala como o domínio para a construção do modelo preditivo foi a importância da fala na vida humana e a relevância das tecnologias de fala na atualidade, principalmente com a ascensão da IoT (Internet of Things – Internet das Coisas), uma área que utiliza bastante algoritmos do tipo.

Para uma melhor percepção do quanto a IoT (e por consequência a pesquisa em reconhecimento de fala) é promissora, na pesquisa de Gubbi et al. (2013, p. 1645, tradução nossa), calculou-se que em meados de 2013 existiam cerca de 9 bilhões de dispositivos interligados, e espera-se que até 2020 existam cerca de 24 bilhões.








O campo de pesquisa das Redes Neurais Artificiais (RNAs) possui grande relevância no mundo contemporâneo, sendo utilizado com sucesso na resolução dos mais diversos tipos de problema, como no tratamento do câncer e no reconhecimento de imagens e de fala. Especificamente no reconhecimento de fala, as RNAs têm sido extensivamente utilizadas, pois estas atingem um alto nível de precisão e adaptabilidade aos ruídos do ambiente. Assim, existem diversos frameworks e bibliotecas de programação que simplificam a construção de modelo de RNAs. Dessa forma, o objetivo deste trabalho é explicar o desenvolvimento de uma RNA utilizando o framework TensorFlow, demonstrando assim a capacidade e os recursos da ferramenta na construção de modelos preditivos. Para isso, foi utilizado como exemplo uma RNA construída para a classificação das palavras em inglês dos números de zero a nove. Além dos números, a rede distingue momentos de silêncio e palavras desconhecidas, ou seja, classifica um som como algum dos números – “one”, “two”, etc., como "silence" em casos de silêncio ou como "unknown" para palavras desconhecidas. Para a explicação do modelo preditivo, foram abordados os principais conceitos do fenômeno físico da fala humana, das RNAs e do framework TensorFlow, que possui diversos recursos para a construção de RNAs. Por fim, após a contextualização da teoria e dos algoritmos da rede utilizada, foram abordados o treinamento e as simulações realizadas para a verificação da precisão do modelo, feitas com um áudio de dez minutos (com sons contínuos) e com vinte sons isolados. Após a execução do algoritmo, a rede apresentou classificações com altos níveis de acurácia e baixos níveis de erros e falsos positivos, tanto no treinamento, quanto nas validações, nos testes e nas simulações posteriores. Dessa forma, utilizando o TensorFlow, em (razoavelmente) poucas linhas, usando vários métodos prontos e um alto nível de abstração, desenvolveu-se uma robusta RNA, o que permitiu concluir que, com o framework, é possível construir poderosos modelos preditivos de forma simplificada.

Palavras-chave: Reconhecimento de fala. Redes Neurais Artificiais. Framework TensorFlow. Redes Neurais Convolucionais. Aprendizado de Máquina. Rede cnn-trad-fpoll3.

Acurácia de 89% em seu treinamento, de 91.1% em sua validação e de 90.9% em seus testes. Para os testes realizados com um áudio contínuo de dez minutos com várias palavras (conhecidas e desconhecidas), que simulava os sons do mundo real (com momentos de silêncio e ruído), a rede obteve 96.5% de acertos, 3.5% de erros e 0% de falsos positivos em suas classificações. 

Já para os testes realizados com vinte palavras isoladas (sem ruído e silêncio), a rede classificou corretamente 90% das amostras. Dessa forma, foi possível concluir que o TensorFlow possibilita o desenvolvimento de RNAs robustas de forma simplificada, como corroborado pela rede utilizada, que possui várias camadas e parâmetros e obteve altos níveis de precisão.




The research field of Artificial Neural Networks (RNAs) has great relevance in the contemporary world, being successfully used in the resolution of the most diverse types of problem, as in the treatment of cancer and in the recognition of images and speech. Specifically in speech recognition, ANNs have been extensively used because they achieve a high level of accuracy and adaptability to environmental noise. Thus, there are several frameworks and programming libraries that simplify the RNA model construction. Thus, the objective of this paper is to explain the development of an RNA using the TensorFlow framework.

For this, an RNA constructed for the classification of English words from numbers zero to nine was used as an example. Besides the numbers, the network distinguishes moments of silence and unknown words, that is, it classifies a sound as one of the numbers - "one", "two", etc., as "silence" in cases of silence or as "unknown" for unknown words.

For the explanation of the predictive model, the main concepts of the physical phenomenon of human speech, of the RNAs and of the TensorFlow framework, which has several resources for the construction of RNAs, were discussed. Finally, after the contextualization of the theory and algorithms of the network used, the training and the simulations performed to verify the accuracy of the model, made with a ten minutes audio (with continuous sounds) and with twenty isolated sounds, were approached.

After the execution of the algorithm, the network presented classifications with high levels of accuracy and low levels of errors and false positives, both in training, validations, tests and subsequent simulations.

Thus, using the TensorFlow, in (reasonably) few lines, using several ready methods and a high level of abstraction, a robust RNA was developed, which allowed to conclude that, with the framework, it is possible to construct powerful predictive models of form simplified.

Keywords: Speech recognition. Artificial neural networks. Framework TensorFlow. Convolutional Neural Networks. Machine Learning.