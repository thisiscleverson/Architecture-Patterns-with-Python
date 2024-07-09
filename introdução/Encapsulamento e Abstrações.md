# Encapsulamento e Abstrações

O **encapsulamento** nos permite usar abstrações para criar códigos mais expressivos e mais testaveis e mais facies de manter. 

É a prática de esconder a implementação interna e expor apenas o necessário através de métodos públicos. Isso melhora a modularidade e facilita a manutenção.

> **NOTE:** A abstração envolve a criação de uma representação simplificada de uma entidade do mundo real, destacando as características mais importantes e ignorando as menos relevantes para o contexto específico. Esse processo resulta em uma classe que define um objeto com suas propriedades e comportamentos essenciais.

**Exemplo sem encapsulamento:**

```python
import requests

params = dict(q='Sausages', format='json')
parsed = requests.get('http://api.duckduckgo.com/',
params=params).json()
results = parsed['RelatedTopics']

for r in results:
	if 'Text' in r:
		print(r['FirstURL'] + ' - ' + r['Text'])
```

Veja que o codigo a cima envia valores codificados em formulário para uma URL para usar uma `API` de mecanismo de busca.

Podemos usar um modulo para encapsular o código:

```python
import duckduckgo
for r in duckduckgo.query('Sausages').results:
	print(r.url + ' - ' + r.text)
```

O encapsulamento e abstração nos ajuda a esconder e proteger a consistência dos nosso dados.