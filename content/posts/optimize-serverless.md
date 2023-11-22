---
title: "Optimización serverless"
date: 2023-11-21T11:30:03+00:00
tags: ["serverless", "aws", "performance"]
author: "Kevin L"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Optimizing your AWS Lambda function memory settings can improve performance and reduce costs. Use the appropriate amount of memory for your function based on its requirements, and consider using a smaller amount for functions that don't need it. Additionally, use AWS Lambda power tuning to optimize your function's performance, and eliminate unnecessary resources and costs by removing unused functions and optimizing your stack."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
--- 
# Optimización serverless

## Optimizar código

Utilizar empaquetadores como esbuild o webpack, en varias pruebas realizadas se evidencio mejora usando esbuild por tiempos y tamaño de compilación aquí  una referencia. Ambos tienen soporte offline.


* [serverless-esbuild](https://www.npmjs.com/package/serverless-esbuild)

* [serverless-webpack](https://www.serverless.com/plugins/serverless-webpack)

## Uso de Graviton2

### Cargas de trabajo

* Multithreading o realizar muchas operaciones de E/S

* Inferencia de aprendizaje automático basada en la CPUs

* Codificación de vídeo

* Gaming

### Costo
* 20% más económico, incluida la concurrencia aprovisionada

* Apoyado en Compute Savings Plans

### Configuración en Serverless Framework
Se debe agregar el la siguiente linea `architecture: 'arm64'`


```
# serverless.yml
provider:
    architecture: 'arm64'
```

### Uso de AWS Lambda Power Tunning
Medir el tamaño de memoria costo eficiente es una de las practicas de optimización muy fáciles y útiles.

Por defecto usando frameworks como serverless framework o SAM se puede definir de forma granular la cantidad de memoria a cada lambda pero es bueno cuanta memoria y vCPU (Virtual CPUs) brindan mejores tiempos de respuesta y con ello mejor.
Tenemos una lambda que realiza una operación de lectura en una BD y trae 50 registros para la cual se tiene un cuadro comparativo con las diferentes configuraciones de memoria, tiempos de respuesta y el costo.

![1_step](/1_step_power_tuning.png "Step 1")

Un dato muy importante es que la configuración de 128 MB tiene un costo similar a la opción de 1536 MB pero la diferencia en tiempos es significativa 10 a 1.

No siempre mas memoria significan incrementar costos

![2_step](/2_step_power_tuning.png "Step 2")

### Guía de uso
Opte por la opción que implique menor esfuerzo opción #1. Abrir el siguiente [link](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:451282441545:applications~aws-lambda-power-tuning) teniendo iniciada sesión en la cuenta AWS.

En la siguiente plantilla se pueden configurar el rango de memoria RAM permitida para la evaluación

![3_step](/3_step_power_tuning.png "Step 3")
Una vez termine el proceso de creación se debe dar clic en el link powerTuningStateMachine

![4_step](/4_step_power_tuning.png "Step 4")
![5_step](/5_step_power_tuning.png "Step 5")

Se ingresa el json con la información de la lambda a probar


```
{
        "lambdaARN": "your-lambda-function-arn",
        "powerValues": [128, 256, 512, 1024, 2048, 3008],
        "num": 10,
        "payload": "{}",
        "parallelInvocation": true,
        "strategy": "cost"
}
```

Se inicia la ejecución:
![5_step](/5_step_power_tuning.png "Step 5")

![6_step](/6_step_power_tuning.png "Step 6")

Tenemos el siguiente resultado el cual nos brinda información muy útil para realizar ajustes en nuestra lambda [link](https://lambda-power-tuning.show/#gAAAAQACAAQACMAL;AACJQ6uqGUMAAIhBAAA4QVVVPUEAAEBB;ERP6NIWNDDXGP/gzEzwvNBM8rzQesAA1)

![result_power_tuning](/result_power_tuning.png "result_power_tuning")


## Optimizaciones serverless 

Una vez finalizado se debe eliminar el stack para ello nos vamos a CloudFormation > Stacks seleccionar el stack de power Tunings y se lo elimina con eso se borraran todos los recursos creados, para evitar incurrir en gastos adicionales.

## Referencias:

* https://docs.aws.amazon.com/lambda/latest/operatorguide/perf-optimize.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/execution-environments.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/computing-power.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/static-initialization.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/architecture-best-practice.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/profile-functions.html

* https://github.com/alexcasalboni/aws-lambda-power-tuning

 

