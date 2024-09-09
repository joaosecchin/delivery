# {
  "Comment": "Step Function de demonstração, tratamento de um jantar romantico.\nDados iniciais para execução\n{\n\t\"prompt_one\": \"Sugetão de comidas.\",\n\t\"prompt_two\": \"Sugetão de cobebidas.\",\n  \t\"prompt_three\": \"Sugetão de local.\"\n}",
  "StartAt": "Prompt para sugestão de comidas",
  "States": {
    "Prompt para sugestão de comidas": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:sa-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 2000,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "Preciso de sugestões para um jantar a dois, baseado em massas."
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "Next": "Trata o resultado da sugestão de comidas",
      "ResultPath": "$.result_one",
      "ResultSelector": {
        "result_one.$": "$.Body.content[0].text"
      }
    },
    "Trata o resultado da sugestão de comidas": {
      "Type": "Pass",
      "Next": "Prompt para sugestão de bebidas",
      "Parameters": {
        "convo_one.$": "States.Format('{}\n{}', $.prompt_one, $.result_one.result_one)"
      },
      "ResultPath": "$.convo_one"
    },
    "Prompt para sugestão de bebidas": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:sa-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 2000,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "Com base nas massas, me recomende 2 bebidas"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "Next": "Trata o resultado da sugestão de bebidas",
      "ResultSelector": {
        "result_two.$": "$.Body.content[0].text"
      },
      "ResultPath": "$.result_two"
    },
    "Trata o resultado da sugestão de bebidas": {
      "Type": "Pass",
      "Next": "Prompt para sugestão do local",
      "Parameters": {
        "convo_two.$": "States.Format('{}\n{}\n{}', $.convo_one.convo_one, $.prompt_two, $.result_two.result_two)"
      },
      "ResultPath": "$.convo_two"
    },
    "Prompt para sugestão do local": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:sa-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 200,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "Com base nas massas e bebidas, encontre um restaurante onde posso desfrutar das recomendações"
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
