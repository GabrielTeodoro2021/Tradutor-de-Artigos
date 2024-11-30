* Tradutor-de-Artigos *
Código para fazer a tradução de artigos.

Passo a passo:

1) Importando biblioteca
!pip install requests beautifulsoup4 openai langchain-openai

2) 
import requests
from bs4 import BeautifulSoup

def extract_text_from_url(url):
    response = requests.get(url)

  if response.status_code == 200:
      soup = BeautifulSoup(response.text, 'html.parser')
      for script_or_style in soup(['script', 'style']):
        script_or_style.decompose()
        text = soup.get_text(separator= ' ')
        #Limpar texto
        lines = (line.strip() for line in text.splitlines())
        parts = (phrase.strip() for line in lines for phrase in line.split("  "))
        texto_limpo = '\n'.join(chunk for chunk in parts if chunk)
        return texto_limpo
    else:
      print(f"Failed to fetch the URL. Status code:{response.status_code}")
      return None
    
  text = soup.get_text()
  return text

extract_text_from_url('https://dev.to/kenakamu/azure-open-ai-in-vnet-3alo')

3) Configuração do serviço de Tradução de artigos do Azure

from langchain_openai.chat_models.azure import AzureChatOpenAI

client = AzureChatOpenAI(
    azure_endpoint= "SEU ENDPOINT",
    api_key= "SUA CHAVE",
    api_version= "VERSAO DO MODELO",
    deployment_name= "NOME DO MODELO",
    max_retries= 0
)

def translate_article(text, lang):
  messages = [
      ("system" , "Você atua como tradutor de textos"),
      ("user", f"Traduza o {text} para o idioma {lang} e responda em markdown")
  ]

  response = client.invoke(messages)
  print(response.content)
  return response.content

translate_article("Let's see", 'portugues')

4) Inserindo o caminho da URL para verificar se a tradução está sendo feita corretamente.

url = 'https://dev.to/kenakamu/azure-open-ai-in-vnet-3alo'
text = extract_text_from_url(url)
article = translate_article(text, 'pt-br')

print(article)
