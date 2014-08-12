# Machine Learning Guerrilha

---

# Quem?

* Empresa Individual
* 10 anos de indústria
* Desenvolvedor Software Livre
* Aluno Mestrado PUC-Rio
* github.com/felipecruz ou @felipejcruz
* http://loogica.com
* Código: https://github.com/loogica/in_the_cloud

---

![original](/Users/felipecruz/Downloads/pybr.jpg)

--- 

# ML Guerrilha?

* Início rápido
* Uso e criação de ferramentas
* Foco no fim e no meio
 * Boa forma de executar
 * Buscar melhor resultado

---

# Ambiente

* Linux
 * Gerenciador de pacotes + Pip
* OSX
 * Instalação padrão + brew + Pip
* Windows
 * Anaconda (Python + Diversos pacotes)

---

# ML & Python

* Numpy - importante aprender
 * Pacote mais fundamental
  * Implementação Array N-dimensional
  * Funções e operações sofisticadas
  * Integração com C/C++ e Fortran
  * Muitos outros pacote usam
    numpy por "baixo"

---

# ML & Python

* Numpy
 * Importação de CSVs
 * Selecionar colunas
 * Adicionar coluna
 * Particionar treino e teste/validação

---

# Importar CSV

```python
import numpy as np

data_train = np.loadtxt('training.csv',
                        delimiter=',',
                        skiprows=1)
```

* O primeiro parâmetro pode ser uma função geradora ou nomes de arquivos comprimidos (.gz ou .bz2)

---

# Selecionar colunas

* Selecionar as colunas 1, 2, e 5 de todo dataset

```python
import numpy as np

X_train = data_train[:,(1, 2, 5)]
```

---

# Particionar treino e teste/validação

* 80% treino e 20% teste/validação

```python
import numpy as np

X_train = data_train[:][r<0.8]
X_validation = data_train[:][r>=0.8]
```

---

# Adicionar coluna

```python
import numpy as np

z1 = np.zeros((NUM_ROWS,1))

X_train = np.append(X_train, z1, 1)

for i, example in enumerate(X_train):
    X_train[i] = transform_row(X_train[i])
```

---

# Scikit-Learn

* Utilitários para importação/tratamento de Datasets
* Diversos Algoritmos (supervisionados, não supervisionados)
* Diversas métricas implementadas
* Pipelines

---

# Importação/Ajuste de Datasets

* A partir de uma lista de dicionários cria um dataset

```python
from sklearn.feature_extraction import DictVectorizer

vec = DictVectorizer()

dataset = [{'a': 0, 'b': 0, 'r': 0},
           {'a': 0, 'b': 1, 'r': 1},
           {'a': 1, 'b': 0, 'r': 1},
           {'a': 1, 'b': 1, 'r': 1}]

dataset = vec.fit_transform(dataset).toarray()
```

---

# Importação/Ajuste de Datasets

* Separa dados e label usando numpy (considere o exemplo do slide anterior)

```python
# numpy
X_train = dataset[:,(0, 1)]
Y_train = dataset[:,2]
```
---

# Importação/Ajuste de Datasets

* Scalers, MinMaxScaler
 * `StandardScaler()`
* Normalization
 * `Normalizer()`
* Imputer 
 * `Imputer(strategy="most_frequent")`

* Mais em `sklearn.preprocessing`

---

# Pipelines

* Conectando "componentes"

```python
from sklearn.ensemble import GradientBoostingClassifier as GBC
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline

gbc = GBC(n_estimators=60, max_depth=8, min_samples_leaf=200,
          max_features=NUM_USED_VARIABLES, verbose=1)
scaler = StandardScaler()

pipe = Pipeline(steps=[('imputer', Imputer(strategy="most_frequent")),
                       ('scaler', scaler),
                       ('boosting', gbc)])
pipe.fit(X_train, Y_train)
pred = pipe.predict(X_test)
```

---

# Avaliação

* Pacote `sklearn.metrics`

```python
from sklearn.metrics

pred = model.predict(X_test)
r2_score(Y_test, pred)
```

* Interface igual para todas

---

# Outras métricas

* Classificação
 * accuracy - `sklearn.metrics.accuracy_score`
 * average precision `sklearn.metrics.average_precision_score`
 * f1 `sklearn.metrics.f1_score`
 * precision `sklearn.metrics.precision_score`
 * recall `sklearn.metrics.recall_score`
 * roc_auc `sklearn.metrics.roc_auc_score`

---

# Outras métricas II
* Regressão
 * mean absolute error `sklearn.metrics.mean_absolute_error`
 * mean squared error `sklearn.metrics.mean_squared_error`
 * r2 `sklearn.metrics.r2_score`

---

# Outras métricas III

* Clustering	 
 * adjusted rand score `sklearn.metrics.adjusted_rand_score`

---

# Reaproveitando treino

* Ficou bom? Guarde o resultado do treino para uso futuro.

```python
from sklearn.externals import joblib

...
joblib.dump(model, 'my_model.pkl')

...
model = joblib.load('my_model.pkl')

```

---

# Resumo 1: Guerrilha

* Numpy + Sklearn
* Importar dados
* Criar modelos
* Executar algoritmos
* Avaliar resultado
* Joblib save & load

---

# ML & Infra

* Explorações e avaliações (muito)rápidas no ambiente local
* Processamentos pesados na nuvem
 * AWS e outros
 * 0(zero) desperdício de tempo do servidor
* Não saturar processador local
 * Alguns treinos podem levar horas, dias até.

---

# ML na Nuvem - Guerrilha

* Idéia:
 * Depois de criar o porgrama que realiza o treino enviar para nuvem para computação e terminar a instância.
* Resgatar o resultado na máquina local e montar um histórico para gerar um ranking.
* Buscar o máximo de automatização.

---

# Exemplo EC2

* Boto - Criar instância
* Ansible para provisionar
* Paramiko - envio de código
* Python stack (Numpy+Sklearn)

---

# Conectando AWS

```python
def connection(region, key_id, access_key):
    conn = boto.ec2.connect_to_region(region, aws_access_key_id=key_id,
                                              aws_secret_access_key=access_key)
    return conn
```

---

# Rodando instância

```python
def run_instance(conn, key_name, _type, sec_group, instance_id=UBUNTU1404_ID):
    reservation = conn.run_instances(instance_id,
                                     key_name=key_name,
                                     instance_type=_type,
                                     security_groups=sec_group)

    instance = reservation.instances[0]
    while instance.state != u'running':
        sys.stdout.write('.')
        sys.stdout.flush()
        time.sleep(2)
        instance.update()

    return instance
```

---

# Provisionando instância

```python
import ansible.playbook

from ansible import utils
from ansible import callbacks
from ansible import inventory

def run_playbook(playbook_file_name, inventory_file_name):
    stats = callbacks.AggregateStats()
    playbook_cb = callbacks.PlaybookCallbacks(verbose=utils.VERBOSITY)
    runner_cb = callbacks.PlaybookRunnerCallbacks(stats, verbose=utils.VERBOSITY)

    pb = ansible.playbook.PlayBook(playbook_file_name,
        inventory=inventory.Inventory(inventory_file_name),
        callbacks=playbook_cb,
        runner_callbacks=runner_cb,
        stats=stats)

    pb.run()
```

---

# Process context snippet

```python
class process_unit(object):
    def __init__(self, instance_id=None):
        self.conn = connection("us-west-2", "YOUR_KEY", "YOUR_SECRET")
        self.instance_id = instance_id

    def __enter__(self):
        self.running_instance = get_instance(self.conn,
                                             instance_id=self.instance_id)

        self.running_instance = ComputeInstance(self.running_instance)

        gen_inventory_file('dev.hosts', self.running_instance.aws_instance)
        if not self.instance_id:
            run_playbook('aws_setup_python_env.yml', 'dev.hosts')
            _id = self.running_instance.aws_instance.id
            print("Image Id: {}".format(_id))

        return self.running_instance

    def __exit__(self, _type, value, traceback):
        self.running_instance.stop()
```

---

# Da pra fazer tudo em 1 comando?

* Sim, porém:
 * Casos mais complexos pedem mais ferramentas/códigos

---

# Exemplo EC2

```python
from my_ml_cloud import process_unit

with process_unit() as instance:
    instance.send_run('ml.py')
```

* Código completo e instruções em: https://github.com/loogica/in\_the\_cloud

---

# Mais a se pensar

* Onde manter os datasets?
 * Sugestão: de preferência no mesmo serviço de computação para que eles sejam mais rapidamente transferidos
  * S3 ou similares
 * Como agregar dados de diversas fontes?
  * Cada caso pode ter uma particularidade muito específica: analisar.

---

# Mais a se pensar II

* Paralelismo
 * Subir várias máquinas ao mesmo tempo trabalhando em paralelo
* Nenhum resultado deve ser jogado fora
 * Podem ter insights/dicas importantes
  * Montar um ranking
  * HTML + JS + JSON

---

# Considerações Finais

* Não existe bala de prata
 * Python é considerado lento em alguns casos
* Python se integra com facilidade com projetos em C/C++ para machine learning
 * As vezes é bom usar coisas que já são reconhecidamente boas de outras linguagens, como C/C++.

---

# Considerações Finais II

* Estudar matemática mais a fundo pode ser um diferencial.
* Dentro da área ML existem várias outras sub-áreas que usam técnicas diferentes.
 * Tentar aprender muitas de uma vez pode atrasar resultados e confundir.
* Técnicas de visualização/exibição de dados são muito importantes.

---

# Fim

* Perguntas ?
* Obrigado
* felipecruz@loogica.net
* @felipejcruz
* github.com/felipecruz
