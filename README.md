# Desafio-DIO-Assistente-delivery-Step-Function-Bedrock-AWS
Desafio do Felipão-DIO - Criando um Assistente de Delivery com AWS Step Functions e Bedrock

Criando o desafio do Felipão da DIO, aplicando o conteudo ensinado no bootcamp da Nexa - Engenharia de Prompts na AWS com Claude
aplicando AWS Step Functions e Bedrock na prática, com a criação de engenharia de propmt para um assistente de Delivery, utilizando aorendizado como Workflow Studio & ASL.


# A prática

Infelizmente não foi possivel conseguir as permissões necessária no Amazon Bedrock para executar completamente o exercicio, então finalizei usando o ChatGPT para simular o códico e o resultado final, baseado no código criado no Haiku Cloud da Bedrock.

# Resultado Simulado

Primeiro Prompt:

- Pergunta: "Estou programando um jantar romântico, nesse jantar irei pedir macarrão, me dê uma lista de 3 itens que combinam em uma experiência gastronômica."

- Resposta simulada: Para complementar o macarrão em um jantar romântico, você poderia considerar:
  
  1.Pão de alho: levemente torrado, para acompanhar como entrada.
  
  2.Queijo parmesão ralado fresco: para polvilhar no prato e realçar o sabor.
  
  3.Salada Caprese: uma combinação leve de tomate, manjericão e mussarela de búfala.


Segundo Prompt:

- Pergunta: "Liste 2 bebidas que acompanhem um jantar romântico."

- Resposta simulada: Para bebidas, duas boas opções seriam:
  
    1.Vinho tinto suave: combina bem com pratos de massa e cria uma atmosfera romântica.
  
    2.Espumante rosé: leve, refrescante e ideal para brindar momentos especiais.


Terceiro Prompt:

- Pergunta: "Liste um lugar perfeito para um jantar romântico em Paris."

- Resposta simulada: Um dos lugares mais icônicos seria:
  
  1.Le Jules Verne: restaurante elegante localizado na Torre Eiffel, com uma vista espetacular da cidade e um menu requintado ideal para um jantar a dois.


Essas respostas são simuladas e baseadas em sugestões clássicas para jantares românticos. Para obter os resultados específicos, será preciso executar o fluxo no seu ambiente AWS.

## Código JSON extraido do AWS Step Functios - Bedrock

{
  "Comment": "An example of using Bedrock to chain prompts and their responses together.",
  "StartAt": "Invoke model with first prompt",
  "States": {
    "Invoke model with first prompt": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 2000,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "Estou programando um jantar romantico, nesse jantar irei pedir macarrão, me de uma lista de 3 itens que combinam em uma experiencia gastronomica"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "Next": "Add first result to conversation history",
      "ResultPath": "$.result_one",
      "ResultSelector": {
        "result_one.$": "$.Body.content[0].text"
      }
    },
    "Add first result to conversation history": {
      "Type": "Pass",
      "Next": "Invoke model with second prompt",
      "Parameters": {
        "convo_one.$": "States.Format('{}\n{}', $.prompt_one, $.result_one.result_one)"
      },
      "ResultPath": "$.convo_one"
    },
    "Invoke model with second prompt": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 2000,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "Liste 2 bebidas que acompanhe um jantar romantico"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "Next": "Add second result to conversation history",
      "ResultSelector": {
        "result_two.$": "$.Body.content[0].text"
      },
      "ResultPath": "$.result_two"
    },
    "Add second result to conversation history": {
      "Type": "Pass",
      "Next": "Invoke model with third prompt",
      "Parameters": {
        "convo_two.$": "States.Format('{}\n{}\n{}', $.convo_one.convo_one, $.prompt_two, $.result_two.result_two)"
      },
      "ResultPath": "$.convo_two"
    },
    "Invoke model with third prompt": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 2000,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "Liste um lugar perfeito para um jantar romantico em Paris"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "End": true,
      "ResultSelector": {
        "result_three.$": "$.Body.content[0].text"
      }
    }
  }
}
