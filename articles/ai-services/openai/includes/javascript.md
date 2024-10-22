---
title: 'Quickstart: Use Azure OpenAI Service with the JavaScript SDK and the completions API'
titleSuffix: Azure OpenAI
description: Walkthrough on how to get started with Azure OpenAI and make your first completions call with the JavaScript SDK. 
#services: cognitive-services
manager: nitinme
ms.service: azure-ai-openai
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/22/2024
---

[Source code](https://github.com/openai/openai-node) | [Package (npm)](https://www.npmjs.com/package/openai) | [Samples](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/openai/openai/samples)

> [!NOTE]
> This article has been updated to use the [latest OpenAI npm package](https://www.npmjs.com/package/openai) which now fully supports Azure OpenAI. If you are looking for code examples for the legacy Azure OpenAI JavaScript SDK they are currently still [available in this repo](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/openai/openai/samples/v2-beta/javascript).

## Prerequisites

## [**TypeScript**](#tab/typescript)

- An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services?azure-portal=true)
- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- [TypeScript](https://www.typescriptlang.org/download/)
- [Azure CLI](/cli/azure/install-azure-cli) used for passwordless authentication in a local development environment, create the necessary context by signing in with the Azure CLI.
- An Azure OpenAI Service resource with the `gpt-35-turbo-instruct` model deployed. For more information about model deployment, see the [resource deployment guide](../how-to/create-resource.md).

> [!div class="nextstepaction"]
> [I ran into an issue with the prerequisites.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=AOAI&&Product=gpt&Page=quickstart&Section=Prerequisites)

## [**JavaScript**](#tab/javascript)

- An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services?azure-portal=true)
- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- [Azure CLI](/cli/azure/install-azure-cli) used for passwordless authentication in a local development environment, create the necessary context by signing in with the Azure CLI.
- An Azure OpenAI Service resource with the `gpt-35-turbo-instruct` model deployed. For more information about model deployment, see the [resource deployment guide](../how-to/create-resource.md).

> [!div class="nextstepaction"]
> [I ran into an issue with the prerequisites.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=AOAI&&Product=gpt&Page=quickstart&Section=Prerequisites)

---

## Set up

[!INCLUDE [get-key-endpoint](get-key-endpoint.md)]

[!INCLUDE [environment-variables](environment-variables.md)]

In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app, and navigate to it.

## Install the client library

Install the required packages for JavaScript with npm from within the context of your new directory:

```console
npm install openai dotenv @azure/identity
```

Your app's _package.json_ file will be updated with the dependencies.

> [!div class="nextstepaction"]
> [I ran into an issue with the setup.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=AOAI&&Product=gpt&Page=quickstart&Section=Set-up-the-environment)

## Create a sample application

Open a command prompt where you created the new project, and create a new file named Completion.js. Copy the following code into the Completion.js file.

## [**TypeScript (Entra id)**](#tab/typescript-keyless)

```typescript
import { 
  DefaultAzureCredential, 
  getBearerTokenProvider 
} from "@azure/identity";
import { AzureOpenAI } from "openai";
import { type Completion } from "openai/resources/index";

// Load the .env file if it exists
const dotenv = require("dotenv");
dotenv.config();

// You will need to set these environment variables or edit the following values
const endpoint = process.env["AZURE_OPENAI_ENDPOINT"] || "<endpoint>";
const apiKey = process.env["AZURE_OPENAI_API_KEY"] || "<api key>";

// Required Azure OpenAI deployment name and API version
const apiVersion = "2024-08-01-preview";
const deploymentName = "gpt-35-turbo-instruct";

// keyless authentication    
const credential = new DefaultAzureCredential();
const scope = "https://cognitiveservices.azure.com/.default";
const azureADTokenProvider = getBearerTokenProvider(credential, scope);

// Chat prompt and max tokens
const prompt = ["When was Microsoft founded?"];
const maxTokens = 128;

function getClient(): AzureOpenAI {
  return new AzureOpenAI({
    endpoint,
    azureADTokenProvider,
    apiVersion,
    deployment: deploymentName,
  });
}
async function getCompletion(
  client: AzureOpenAI,
  prompt: string[],
  max_tokens: number
): Promise<Completion> {
  return client.completions.create({
    prompt,
    model: "",
    max_tokens,
  });
}
async function printChoices(completion: Completion): Promise<void> {
  for (const choice of completion.choices) {
    console.log(choice.text);
  }
}
export async function main() {
  console.log("== Get completions Sample ==");

  const client = getClient();
  const completion = await getCompletion(client, prompt, maxTokens);
  await printChoices(completion);
}

main().catch((err) => {
  console.error("Error occurred:", err);
});
```

Build the script with the following command:

```cmd
tsc
```

Run the script with the following command:

```cmd
node.exe Completion.js
```

## [**JavaScript (Entra id)**](#tab/javascript-keyless)

```javascript
const { AzureOpenAI } = require("openai");
import { 
  DefaultAzureCredential, 
  getBearerTokenProvider 
} from "@azure/identity";

// Load the .env file if it exists
const dotenv = require("dotenv");
dotenv.config();

// You will need to set these environment variables or edit the following values
const endpoint = process.env["AZURE_OPENAI_ENDPOINT"] || "<endpoint>";
const apiKey = process.env["AZURE_OPENAI_API_KEY"] || "<api key>";
const apiVersion = "2024-04-01-preview";
const deployment = "gpt-35-turbo-instruct"; //The deployment name for your completions API model. The instruct model is the only new model that supports the legacy API.

// keyless authentication    
const credential = new DefaultAzureCredential();
const scope = "https://cognitiveservices.azure.com/.default";
const azureADTokenProvider = getBearerTokenProvider(credential, scope);

const prompt = ["When was Microsoft founded?"];

async function main() {
  console.log("== Get completions Sample ==");

  const client = new AzureOpenAI({ endpoint, azureADTokenProvider, apiVersion, deployment });  

  const result = await client.completions.create({ prompt, model: deployment, max_tokens: 128 });

  for (const choice of result.choices) {
    console.log(choice.text);
  }
}

main().catch((err) => {
  console.error("Error occurred:", err);
});

module.exports = { main };
```

Run the script with the following command:

```cmd
node.exe Completion.js
```

## [**TypeScript (API key)**](#tab/typescript-key)

```typescript
import "dotenv/config";
import { AzureOpenAI } from "openai";
import { type Completion } from "openai/resources/index";

// You will need to set these environment variables or edit the following values
const endpoint = process.env["AZURE_OPENAI_ENDPOINT"] || "<endpoint>";
const apiKey = process.env["AZURE_OPENAI_API_KEY"] || "<api key>";

// Required Azure OpenAI deployment name and API version
const apiVersion = "2024-08-01-preview";
const deploymentName = "gpt-35-turbo-instruct";

// Chat prompt and max tokens
const prompt = ["When was Microsoft founded?"];
const maxTokens = 128;

function getClient(): AzureOpenAI {
  return new AzureOpenAI({
    endpoint,
    apiKey,
    apiVersion,
    deployment: deploymentName,
  });
}
async function getCompletion(
  client: AzureOpenAI,
  prompt: string[],
  max_tokens: number
): Promise<Completion> {
  return client.completions.create({
    prompt,
    model: "",
    max_tokens,
  });
}
async function printChoices(completion: Completion): Promise<void> {
  for (const choice of completion.choices) {
    console.log(choice.text);
  }
}
export async function main() {
  console.log("== Get completions Sample ==");

  const client = getClient();
  const completion = await getCompletion(client, prompt, maxTokens);
  await printChoices(completion);
}

main().catch((err) => {
  console.error("Error occurred:", err);
});
```

Build the script with the following command:

```cmd
tsc
```

Run the script with the following command:

```cmd
node.exe Completion.js
```

## [**JavaScript (API key)**](#tab/javascript-key)

```javascript
const { AzureOpenAI } = require("openai");

// Load the .env file if it exists
const dotenv = require("dotenv");
dotenv.config();

// You will need to set these environment variables or edit the following values
const endpoint = process.env["AZURE_OPENAI_ENDPOINT"] || "<endpoint>";
const apiKey = process.env["AZURE_OPENAI_API_KEY"] || "<api key>";
const apiVersion = "2024-04-01-preview";
const deployment = "gpt-35-turbo-instruct"; //The deployment name for your completions API model. The instruct model is the only new model that supports the legacy API.

const prompt = ["When was Microsoft founded?"];

async function main() {
  console.log("== Get completions Sample ==");

  const client = new AzureOpenAI({ endpoint, apiKey, apiVersion, deployment });  

  const result = await client.completions.create({ prompt, model: deployment, max_tokens: 128 });

  for (const choice of result.choices) {
    console.log(choice.text);
  }
}

main().catch((err) => {
  console.error("Error occurred:", err);
});

module.exports = { main };
```

Run the script with the following command:

```cmd
node.exe Completion.js
```

---

## Output

```output
== Get completions Sample ==

Microsoft was founded on April 4, 1975.
```

## Microsoft Entra ID

> [!IMPORTANT]
> In the previous example we are demonstrating key-based authentication. Once you have tested with key-based authentication successfully, we recommend using the more secure [Microsoft Entra ID](/entra/fundamentals/whatis) for authentication which is demonstrated in the next code sample. Getting started with [Microsoft Entra ID] will require some additional [prerequisites](https://www.npmjs.com/package/@azure/identity).

## [**TypeScript**](#tab/typescript)

```typescript
import {
  DefaultAzureCredential,
  getBearerTokenProvider,
} from "@azure/identity";
import "dotenv/config";
import { AzureOpenAI } from "openai";
import { type Completion } from "openai/resources/index";

// You will need to set these environment variables or edit the following values
const endpoint = process.env["AZURE_OPENAI_ENDPOINT"] || "<endpoint>";

// Required Azure OpenAI deployment name and API version
const apiVersion = "2024-08-01-preview";
const deploymentName = "gpt-35-turbo-instruct";

// Chat prompt and max tokens
const prompt = ["When was Microsoft founded?"];
const maxTokens = 128;

function getClient(): AzureOpenAI {
  const scope = "https://cognitiveservices.azure.com/.default";
  const azureADTokenProvider = getBearerTokenProvider(
    new DefaultAzureCredential(),
    scope
  );
  return new AzureOpenAI({
    endpoint,
    azureADTokenProvider,
    deployment: deploymentName,
    apiVersion,
  });
}
async function getCompletion(
  client: AzureOpenAI,
  prompt: string[],
  max_tokens: number
): Promise<Completion> {
  return client.completions.create({
    prompt,
    model: "",
    max_tokens,
  });
}
async function printChoices(completion: Completion): Promise<void> {
  for (const choice of completion.choices) {
    console.log(choice.text);
  }
}
export async function main() {
  console.log("== Get completions Sample ==");

  const client = getClient();
  const completion = await getCompletion(client, prompt, maxTokens);
  await printChoices(completion);
}

main().catch((err) => {
  console.error("Error occurred:", err);
});

```

Build the script with the following command:

```cmd
tsc
```

Run the script with the following command:

```cmd
node.exe Completion.js
```


## [**JavaScript**](#tab/javascript)

```javascript
const { AzureOpenAI } = require("openai");
const { DefaultAzureCredential, getBearerTokenProvider } = require("@azure/identity");

// Set AZURE_OPENAI_ENDPOINT to the endpoint of your
// OpenAI resource. You can find this in the Azure portal.
// Load the .env file if it exists
require("dotenv/config");

const prompt = ["When was Microsoft founded?"];

async function main() {
  console.log("== Get completions Sample ==");

  const scope = "https://cognitiveservices.azure.com/.default";
  const azureADTokenProvider = getBearerTokenProvider(new DefaultAzureCredential(), scope);
  const deployment = "gpt-35-turbo-instruct";
  const apiVersion = "2024-04-01-preview";
  const client = new AzureOpenAI({ azureADTokenProvider, deployment, apiVersion });
  const result = await client.completions.create({ prompt, model: deployment, max_tokens: 128 });

  for (const choice of result.choices) {
    console.log(choice.text);
  }
}

main().catch((err) => {
  console.error("Error occurred:", err);
});

module.exports = { main };
```

---

> [!NOTE]
> If your receive the error: *Error occurred: OpenAIError: The `apiKey` and `azureADTokenProvider` arguments are mutually exclusive; only one can be passed at a time.* You may need to remove a pre-existing environment variable for the API key from your system. Even though the Microsoft Entra ID code sample is not explicitly referencing the API key environment variable, if one is present on the system executing this sample, this error will still be generated.

> [!div class="nextstepaction"]
> [I ran into an issue when running the code sample.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=AOAI&&Product=gpt&Page=quickstart&Section=Create-application)

## Clean up resources

If you want to clean up and remove an Azure OpenAI resource, you can delete the resource. Before deleting the resource, you must first delete any deployed models.

- [Azure portal](../../multi-service-resource.md?pivots=azportal#clean-up-resources)
- [Azure CLI](../../multi-service-resource.md?pivots=azcli#clean-up-resources)

## Next steps

* [Azure OpenAI Overview](../overview.md)
* For more examples, check out the [Azure OpenAI Samples GitHub repository](https://aka.ms/AOAICodeSamples)
