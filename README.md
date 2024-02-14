# dio-lab-bike-rentals
Repositório para o projeto Trabalhando com Machine Learning na Prática no Azure ML


Para conseguir alcançar os objetivos do projeto, basta seguir os passos descritos no exercício proposto, [link](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/01-machine-learning.html)

## Aprendizado de máquina automatizado para treinar um modelo

1. No Azure Machine Learning Studio, veja a página Automated ML (em Authoring ).

2. Crie um novo trabalho de ML automatizado com as seguintes configurações, usando Next conforme necessário para avançar pela interface do usuário:

**Configurações básicas** :

 - **Nome do trabalho** : mslearn-bike-automl
 - **Novo nome do experimento** : mslearn-bike-rental
 - **Descrição** : Aprendizado de máquina automatizado para previsão de aluguel de bicicletas
 - **Marcadores** : nenhum

**Tipo de tarefa e dados** :

**Selecione o tipo de tarefa** : Regressão
**Selecionar conjunto de dados** : crie um novo conjunto de dados com as seguintes configurações:

**Tipo de dados** :
   - **Nome** : aluguel de bicicletas
   - **Descrição** : dados históricos de aluguel de bicicletas
   - **Tipo** : Tabular
     
**Fonte de dados** :
 - **Selecione Dos arquivos da web**
 - **URL da Web** :
    - **URL da Web** :https://aka.ms/bike-rentals
    - **Ignorar validação de dados** : não selecionar

**Configurações** :
 - **Formato de arquivo** : Delimitado
 - **Delimitador** : Vírgula
 - **Codificação** : UTF-8
 - **Cabeçalhos** de coluna : apenas o primeiro arquivo possui cabeçalhos
 - **Pular linhas** : Nenhum
 - **O conjunto de dados contém dados multilinhas** : não selecione

**Esquema** :
- Incluir todas as colunas exceto **Caminho/Path**
- Revise os tipos detectados automaticamente

Selecione **Criar**. Após a criação do conjunto de dados, selecione o conjunto de dados de aluguel de bicicletas para continuar a enviar o trabalho de ML automatizado.

**Configurações de tarefa** :

- **Tipo de tarefa** : Regressão
- **Conjunto de dados** : aluguel de bicicletas
- **Coluna de destino** : Aluguéis (inteiro)
- **Configurações adicionais** :
  - **Métrica primária** : raiz do erro quadrático médio normalizado
  - **Explique o melhor modelo** : Não selecionado
  - **Usar todos os modelos suportados** : Desmarcado . Você restringirá o trabalho para tentar apenas alguns algoritmos específicos.
  - **Modelos permitidos** : Selecione apenas RandomForest e LightGBM — normalmente você gostaria de tentar o máximo possível, mas cada modelo adicionado aumenta o tempo necessário para executar o trabalho.
- **Limites** : expanda esta seção
  - **Máximo de testes** : 3
  - **Máximo de testes simultâneos** : 3
  - **Máximo de nós** : 3
  - **Limite de pontuação da métrica** : 0,085 ( para que, se um modelo atingir uma pontuação da métrica de erro quadrático médio normalizado de 0,085 ou menos, o trabalho termina. )
  - **Tempo limite** : 15
  - **Tempo limite de iteração** : 15
  - **Habilitar rescisão antecipada** : selecionado
- **Validação e teste** :
  - **Tipo de validação** : divisão de validação de trem
  - **Porcentagem de dados de validação** : 10
  - **Conjunto de dados de teste** : Nenhum
- **Calcular** :

  - **Selecione o tipo de computação** : sem servidor
  - **Tipo de máquina virtual** : CPU
  - **Camada de máquina virtual** : Dedicada
  - **Tamanho da máquina virtual** : Standard_DS3_V2*
  - **Número de instâncias** : 1
    
3. Envie o trabalho de treinamento. Ele inicia automaticamente.

4. Espere o trabalho terminar. Pode demorar um pouco – agora pode ser um bom momento para uma pausa para o café! ☕


## Implantar e testar o modelo
1. Na guia Modelo do melhor modelo treinado pelo seu trabalho automatizado de machine learning, selecione **Implantar** e use a opção de **serviço Web** para implantar o modelo com as seguintes configurações:
 - **Nome** : predict-rentals
 - **Descrição** : Prever aluguel de bicicletas
 - **Tipo de computação** : Instância de Contêiner do Azure
 - **Habilitar autenticação** : selecionado
2. Aguarde o início da implantação – isso pode levar alguns segundos. O status de implantação do endpoint de previsão de aluguel será indicado na parte principal da página como **Running**.
3. Aguarde até que o status da implantação mude para Succeeded. Isso pode levar de 5 a 10 minutos.





# Testar o serviço implantado
 Agora você pode testar seu serviço implantado.

1. No estúdio Azure Machine Learning, no menu esquerdo, selecione **Endpoints** e abra o ponto final em tempo real de **predict-rentals** .

2. Na página do endpoint em tempo real de **predict-rentals**, visualize a guia **Teste** .

3. No painel **Dados de entrada para testar o endpoint** , substitua o modelo JSON pelos seguintes dados de entrada:

```
{
   "Inputs": { 
     "data": [
       {
         "day": 1,
         "mnth": 1,   
         "year": 2023,
         "season": 2,
         "holiday": 0,
         "weekday": 1,
         "workingday": 1,
         "weathersit": 2, 
         "temp": 0.3, 
         "atemp": 0.3,
         "hum": 0.3,
         "windspeed": 0.3 
       }
     ]    
   },   
   "GlobalParameters": 1.0
 }
 ```
Clique no botão **Testar** .

Revise os resultados do teste, que incluem um número previsto de aluguéis com base nos recursos de entrada - semelhante a este:

```
{
  "Results": [
    283.11983035075946
  ]
}
```

O painel de teste pegou os dados de entrada e usou o modelo treinado para retornar o número previsto de aluguéis.

### REVISANDO

Usamos um conjunto de dados históricos de aluguel de bicicletas para treinar um modelo. O modelo prevê o número de alugueres de bicicletas esperados num determinado dia, com base em características sazonais e meteorológicas.

# Excluir recursos
O serviço web que você criou está hospedado em uma instância de contêiner do Azure. Se não pretender experimentá-lo ainda mais, deverá eliminar o **endpoint** para evitar acumular utilização desnecessária do Azure.

1. No [estúdio Azure Machine Learning](https://ml.azure.com/?azure-portal=true), na guia **Endpoints** , selecione o ponto de extremidade de predict-rentals. Em seguida, selecione **Excluir** e confirme que deseja excluir o endpoint.

Excluir sua computação garante que sua assinatura não será cobrada por recursos de computação. No entanto, será cobrada uma pequena quantia pelo armazenamento de dados, desde que o espaço de trabalho do Azure Machine Learning exista na sua assinatura. Se tiver terminado de explorar o Azure Machine Learning, poderá eliminar o espaço de trabalho Azure Machine Learning e os recursos associados.

Para excluir seu espaço de trabalho:

1. No [portal Azure](https://portal.azure.com/?azure-portal=true) , na página **Grupos de recursos**, abra o grupo de recursos que especificou ao criar o seu espaço de trabalho Azure Machine Learning.
2. Clique em **Excluir grupo de recursos**, digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir** .