docker run -it tensorflow-atualizado3 bash

cd /tensorflow/tensorflow/examples/speech_commands/

python3 label_wav.py --graph=/tmp/my_frozen_graph.pb --labels=/tmp/speech_commands_train/conv_labels.txt --wav=/root/sons-gravados/1-eu/wav/5.wav

