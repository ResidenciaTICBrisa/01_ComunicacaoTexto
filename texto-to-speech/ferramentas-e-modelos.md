# Ferramentas e modelos sobre Text to Speech e Speech to Text
## Text to Speech
Text-to-speech (TTS) é uma tecnologia que converte texto escrito em fala audível. É um processo de síntese de fala automatizada que permite que computadores e dispositivos eletrônicos transformem palavras e frases em áudio falado.

Existem alguns modelos pré-treinados que fazem a conversão de texto para áudio em português do Brasil, porém, a maioria deles utiliza o modelo de síntese de fala Tacotron 2, que não é software livre. Porém, a versão anterior ao Tacotron 2, o Tacotron, é software livre, e é usado no projeto da Mozilla TTS para português do Brasil, segue o link do repositório:
- Mozilla TTS: <https://github.com/Edresson/TTS>

Neste projeto, dois modelos de Vocoder são utilizados, o Griffin-Lim, e o WaveRNN. Um vocoder (codificador/decodificador de voz) desempenha um papel importante no processo de Text-to-Speech (TTS). Ele é responsável por sintetizar a voz humana a partir de dados de entrada, como texto escrito.

### Como funciona o treinamento de um modelo TTS baseado no Tacotron
- Carregue os dados: Leia os arquivos de áudio e as transcrições correspondentes do conjunto de dados em estruturas de dados adequadas. Você pode usar bibliotecas como librosa ou soundfile para ler os arquivos de áudio e ler os arquivos de texto.
- Pré-processamento de texto: Realize o pré-processamento nas transcrições de texto, se necessário. Isso pode incluir a remoção de pontuação, conversão para letras minúsculas, normalização ortográfica, entre outros. O pré-processamento depende das necessidades do seu modelo e dos requisitos do conjunto de dados.
- Extração de recursos de áudio: Extraia os recursos de áudio necessários para o treinamento do seu modelo. Isso geralmente envolve a conversão dos arquivos de áudio para espectrogramas ou outras representações de áudio adequadas para a síntese de fala. Você pode usar bibliotecas como librosa ou torchaudio para extrair recursos de áudio.
- Divisão do conjunto de dados: Divida o conjunto de dados em conjuntos de treinamento, validação e teste. É comum usar uma proporção de 80% para treinamento, 10% para validação e 10% para teste, mas isso pode variar dependendo do tamanho do conjunto de dados.
- Tokenização: Converta as transcrições de texto em sequências de tokens. Isso envolve a atribuição de um ID único a cada token e a criação de um mapeamento entre os tokens e seus IDs correspondentes. A tokenização é útil para alimentar as sequências de texto ao modelo durante o treinamento.
- Configuração do modelo: Defina a arquitetura e as configurações do seu modelo de síntese de fala. Isso inclui escolher o tipo de modelo (por exemplo, Tacotron, WaveNet), a configuração dos hiperparâmetros, como taxa de aprendizado, número de camadas, tamanho do lote, etc.
- Treinamento do modelo: Agora você pode iniciar o treinamento do seu modelo. Alimente os dados de treinamento ao modelo em lotes, calcule as perdas, faça a retropropagação e atualize os pesos do modelo. Ajuste os hiperparâmetros e continue o treinamento até que o modelo atinja o desempenho desejado.
- Avaliação do modelo: Após o treinamento, avalie o desempenho do seu modelo usando os dados de validação e teste. Calcule as métricas relevantes, como taxa de erro de síntese, métricas de qualidade do áudio gerado, etc.
- Inferência: Depois que seu modelo estiver treinado e avaliado, você pode usá-lo para gerar fala sintetizada para novos textos de entrada. Alimente o texto de entrada ao modelo e obtenha as predições de áudio correspondentes.

### Bibliotecas que geralmente são usadas

- Os: Manipular operações relacionadas ao sistema operacional
- Librosa: Funções e recursos úteis para extrair informações e recursos de áudio
- Numpy: Manipular e processar os dados de áudio
- ScikitLearn: Divisão do conjunto de dados entre treinamento e teste
- Spacy: Processamento de linguagem natural
- TTS: Técnicas de aprendizado de máquina para gerar fala realista a partir de texto
- Torch: Framework de aprendizado de máquina de código aberto que oferece uma ampla variedade de ferramentas e bibliotecas para construir e treinar redes neurais.

### Possíveis datasets
- Dataset UFBA: <https://gitlab.com/fb-audio-corpora/alcaim16k-DVD1de4>
- Dataset Mozilla: <https://commonvoice.mozilla.org/en/datasets>
- VoxForge: <https://www.voxforge.org/pt/downloads>
