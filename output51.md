---
domain: langfuse.com
path: /docs/scores/model-based-evals/langchain
url: https://langfuse.com/docs/scores/model-based-evals/langchain
---

[Join us in Engineering & DevRel →We're hiring. Join us in Product Eng, Backend Eng, and DevRel →](/careers)

[![Langfuse Logo](https://langfuse.com/langfuse_logo_white.svg)![Langfuse Logo](https://langfuse.com/langfuse_logo.svg)](/)
[DocsDocs](/docs)
[PricingPricing](/pricing)
[ChangelogChangelog](/changelog)
[BlogBlog](/blog)

Demo

[Discord](https://discord.langfuse.com)
[](https://x.com/langfuse)
[GitHub](https://github.com/langfuse/langfuse "GitHub Repository")
[Sign Up](https://cloud.langfuse.com)

*   [Docs](/docs)
    
    Guides
    
    [FAQ](/faq)
    
*   [Overview](/guides)
    
*   Cookbooks
    
    *   [Datasets](/guides/cookbook/datasets)
        
    *   [Evaluation of Rag with Ragas](/guides/cookbook/evaluation_of_rag_with_ragas)
        
    *   [Evaluation with Langchain](/guides/cookbook/evaluation_with_langchain)
        
    *   [Evaluation with Uptrain](/guides/cookbook/evaluation_with_uptrain)
        
    *   [Example Decorator Openai Langchain](/guides/cookbook/example_decorator_openai_langchain)
        
    *   [Example Llm Security Monitoring](/guides/cookbook/example_llm_security_monitoring)
        
    *   [Integration Azure Openai Langchain](/guides/cookbook/integration_azure_openai_langchain)
        
    *   [Integration Haystack](/guides/cookbook/integration_haystack)
        
    *   [Integration Langchain](/guides/cookbook/integration_langchain)
        
    *   [Integration Langserve](/guides/cookbook/integration_langserve)
        
    *   [Integration Litellm Proxy](/guides/cookbook/integration_litellm_proxy)
        
    *   [Integration Llama Index](/guides/cookbook/integration_llama-index)
        
    *   [Integration Llama Index Milvus Lite](/guides/cookbook/integration_llama-index_milvus-lite)
        
    *   [Integration Mirascope](/guides/cookbook/integration_mirascope)
        
    *   [Integration Openai Sdk](/guides/cookbook/integration_openai_sdk)
        
    *   [JS Integration Langchain](/guides/cookbook/js_integration_langchain)
        
    *   [JS Integration Litellm Proxy](/guides/cookbook/js_integration_litellm_proxy)
        
    *   [JS Integration Openai](/guides/cookbook/js_integration_openai)
        
    *   [JS Prompt Management Langchain](/guides/cookbook/js_prompt_management_langchain)
        
    *   [JS Tracing Example Vercel Ai Sdk](/guides/cookbook/js_tracing_example_vercel_ai_sdk)
        
    *   [Langfuse Sdk Performance Test](/guides/cookbook/langfuse_sdk_performance_test)
        
    *   [Observe OpenAI Structured Outputs with Langfuse](/guides/cookbook/integration_openai_structured_output)
        
    *   [Open Source Observability for LangGraph](/guides/cookbook/integration_langgraph)
        
    *   [OSS Observability for Instructor](/guides/cookbook/integration_instructor)
        
    *   [OSS Observability for OpenAI Assistants API](/guides/cookbook/integration_openai_assistants)
        
    *   [Prompt Management Langchain](/guides/cookbook/prompt_management_langchain)
        
    *   [Prompt Management Openai Functions](/guides/cookbook/prompt_management_openai_functions)
        
    *   [Python Decorators](/guides/cookbook/python_decorators)
        
    *   [Python SDK (Low-level)](/guides/cookbook/python_sdk_low_level)
        
    
*   Videos
    
    *   [Haystack Integration](/guides/videos/haystack)
        
    *   [Introducing Datasets v2](/guides/videos/introducing-datasets-v2)
        
    *   [Introducing Langfuse 2.0](/guides/videos/introducing-langfuse-2.0)
        
    *   [Introducing the observe() decorator for Python](/guides/videos/introducing-python-decorator)
        
    *   [LlamaIndex Integration](/guides/videos/llama-index)
        
    *   [LLM Playground](/guides/videos/llm-playground)
        
    *   [Posthog Integration](/guides/videos/posthog-integration)
        
    *   [Webinar: Traceability and Observability in Multi-Step LLM Systems](/guides/videos/webinar-observability-llm-systems)
        
    

Light

On This Page

*   [Setup](#setup)
    
*   [Fetching data](#fetching-data)
    
*   [Set up evaluation functions](#set-up-evaluation-functions)
    
*   [Execute evaluation](#execute-evaluation)
    
*   [See Scores in Langfuse](#see-scores-in-langfuse)
    
*   [Get in touch](#get-in-touch)
    

[Question? Give us feedback → (opens in a new tab)](https://github.com/langfuse/langfuse-docs/issues/new?title=Feedback%20for%20%E2%80%9CRun%20Langchain%20Evaluations%20on%20data%20in%20Langfuse%E2%80%9D&labels=feedback)
[Edit this page on GitHub](https://github.com/langfuse/langfuse-docs/tree/main/pages/guides/cookbook/evaluation_with_langchain.md)
Scroll to top

Guides

Cookbooks

Evaluation with Langchain

This is a Jupyter notebook

[Open on GitHub](https://github.com/langfuse/langfuse-docs/blob/main/cookbook/evaluation_with_langchain.ipynb)
[Run on Google Colab](https://colab.research.google.com/github/langfuse/langfuse-docs/blob/main/cookbook/evaluation_with_langchain.ipynb)

Run Langchain Evaluations on data in Langfuse
=============================================

This cookbook shows how model-based evaluations can be used to automate the evaluation of production completions in Langfuse. This example uses Langchain and is adaptable to other libraries. Which library is the best to use depends heavily on the use case.

This cookbook follows three steps:

1.  Fetch production `generations` stored in Langfuse
2.  Evaluate these `generations` using Langchain
3.  Ingest results back into Langfuse as `scores`

* * *

Not using Langfuse yet? [Get started (opens in a new tab)](https://langfuse.com/docs/get-started)
 by capturing LLM events.

### Setup[](#setup)

First you need to install Langfuse and Langchain via pip and then set the environment variables.

    %pip install langfuse langchain langchain-openai --upgrade

    import os
     
    # get keys for your project from https://cloud.langfuse.com
    os.environ["LANGFUSE_PUBLIC_KEY"] = ""
    os.environ["LANGFUSE_SECRET_KEY"] = ""
     
    # your openai key
    os.environ["OPENAI_API_KEY"] = ""
     
    # Your host, defaults to https://cloud.langfuse.com
    # For US data region, set to "https://us.cloud.langfuse.com"
    # os.environ["LANGFUSE_HOST"] = "http://localhost:3000"

    os.environ['EVAL_MODEL'] = "gpt-3.5-turbo-instruct"
     
    # Langchain Eval types
    EVAL_TYPES={
        "hallucination": True,
        "conciseness": True,
        "relevance": True,
        "coherence": True,
        "harmfulness": True,
        "maliciousness": True,
        "helpfulness": True,
        "controversiality": True,
        "misogyny": True,
        "criminality": True,
        "insensitivity": True
    }

Initialize the Langfuse Python SDK, more information [here (opens in a new tab)](https://langfuse.com/docs/sdk/python#1-installation)
.

    from langfuse import Langfuse
     
    langfuse = Langfuse()
     
    langfuse.auth_check()

### Fetching data[](#fetching-data)

Load all `generations` from Langfuse filtered by `name`, in this case `OpenAI`. Names are used in Langfuse to identify different types of generations within an application. Change it to the name you want to evaluate.

Checkout [docs (opens in a new tab)](https://langfuse.com/docs/sdk/python#generation)
 on how to set the name when ingesting an LLM Generation.

    def fetch_all_pages(name=None, user_id = None, limit=50):
        page = 1
        all_data = []
     
        while True:
            response = langfuse.get_generations(name=name, limit=limit, user_id=user_id, page=page)
            if not response.data:
                break
     
            all_data.extend(response.data)
            page += 1
     
        return all_data

    generations = fetch_all_pages(user_id='user:abc')

### Set up evaluation functions[](#set-up-evaluation-functions)

In this section, we define functions to set up the Langchain eval based on the entries in `EVAL_TYPES`. Hallucinations require their own function. More on the Langchain evals can be found [here (opens in a new tab)](https://python.langchain.com/docs/guides/evaluation/string/criteria_eval_chain)
.

    from langchain.evaluation import load_evaluator
    from langchain_openai import OpenAI
    from langchain.evaluation.criteria import LabeledCriteriaEvalChain
     
    def get_evaluator_for_key(key: str):
      llm = OpenAI(temperature=0, model=os.environ.get('EVAL_MODEL'))
      return load_evaluator("criteria", criteria=key, llm=llm)
     
    def get_hallucination_eval():
      criteria = {
        "hallucination": (
          "Does this submission contain information"
          " not present in the input or reference?"
        ),
      }
      llm = OpenAI(temperature=0, model=os.environ.get('EVAL_MODEL'))
     
      return LabeledCriteriaEvalChain.from_llm(
          llm=llm,
          criteria=criteria,
      )

### Execute evaluation[](#execute-evaluation)

Below, we execute the evaluation for each `Generation` loaded above. Each score is ingested into Langfuse via [`langfuse.score()` (opens in a new tab)](https://langfuse.com/docs/scores)
.

    def execute_eval_and_score():
     
      for generation in generations:
        criteria = [key for key, value in EVAL_TYPES.items() if value and key != "hallucination"]
     
        for criterion in criteria:
          eval_result = get_evaluator_for_key(criterion).evaluate_strings(
              prediction=generation.output,
              input=generation.input,
          )
          print(eval_result)
     
          langfuse.score(name=criterion, trace_id=generation.trace_id, observation_id=generation.id, value=eval_result["score"], comment=eval_result['reasoning'])
     
    execute_eval_and_score()
     

    # hallucination
     
     
    def eval_hallucination():
     
      chain = get_hallucination_eval()
     
      for generation in generations:
        eval_result = chain.evaluate_strings(
          prediction=generation.output,
          input=generation.input,
          reference=generation.input
        )
        print(eval_result)
        if eval_result is not None and eval_result["score"] is not None and eval_result["reasoning"] is not None:
          langfuse.score(name='hallucination', trace_id=generation.trace_id, observation_id=generation.id, value=eval_result["score"], comment=eval_result['reasoning'])
     

    if EVAL_TYPES.get("hallucination") == True:
      eval_hallucination()

    # SDK is async, make sure to await all requests
    langfuse.flush()

### See Scores in Langfuse[](#see-scores-in-langfuse)

In the Langfuse UI, you can filter Traces by `Scores` and look into the details for each. Check out Langfuse Analytics to understand the impact of new prompt versions or application releases on these scores.

![Image of Trace](https://langfuse.com/images/docs/trace-conciseness-score.jpg) _Example trace with conciseness score_

Get in touch[](#get-in-touch)

------------------------------

Looking for a specific way to score your production data in Langfuse? Join the [Discord (opens in a new tab)](https://langfuse.com/discord)
 and discuss your use case!

[Evaluation of Rag with Ragas](/guides/cookbook/evaluation_of_rag_with_ragas "Evaluation of Rag with Ragas")
[Evaluation with Uptrain](/guides/cookbook/evaluation_with_uptrain "Evaluation with Uptrain")

### Was this page useful?

YesCould be better

### Questions? We're here to help

[GitHub Q&AGitHub](/gh-support)
Chat [Email](/cdn-cgi/l/email-protection#a5d6d0d5d5cad7d1e5c9c4cbc2c3d0d6c08bc6cac8)
[Talk to sales](/schedule-demo)

### Subscribe to updates

Get updates

Light

* * *

Platform

*   [LLM Tracing](/docs/tracing)
    
*   [Prompt Management](/docs/prompts/get-started)
    
*   [Evaluation](/docs/scores/overview)
    
*   [Manual Annotation](/docs/scores/annotation)
    
*   [Datasets](/docs/datasets/overview)
    
*   [Metrics](/docs/analytics)
    
*   [Playground](/docs/playground)
    

Integrations

*   [Python SDK](/docs/sdk/python)
    
*   [JS/TS SDK](/docs/sdk/typescript/guide)
    
*   [OpenAI SDK](/docs/integrations/openai/get-started)
    
*   [Langchain](/docs/integrations/langchain/tracing)
    
*   [Llama-Index](/docs/integrations/llama-index/get-started)
    
*   [Litellm](/docs/integrations/litellm)
    
*   [Dify](/docs/integrations/dify)
    
*   [Flowise](/docs/integrations/flowise)
    
*   [Langflow](/docs/integrations/langflow)
    
*   [Vercel AI SDK](/docs/sdk/typescript/example-vercel-ai)
    
*   [Instructor](/docs/integrations/instructor)
    
*   [Mirascope](/docs/integrations/mirascope)
    
*   [API](https://api.reference.langfuse.com/)
    

Resources

*   [Documentation](/docs)
    
*   [Interactive Demo](/demo)
    
*   [Video demo (3 min)](/video)
    
*   [Changelog](/changelog)
    
*   [Roadmap](/docs/roadmap)
    
*   [Pricing](/pricing)
    
*   [Enterprise](/enterprise)
    
*   [Self-hosting](/docs/deployment/self-host)
    
*   [Open Source](/docs/open-source)
    
*   [Why Langfuse?](/why)
    
*   [Status](https://status.langfuse.com)
    

About

*   [Blog](/blog)
    
*   [Careers](/careers)
    3
*   [About us](/about)
    
*   [Support](/support)
    
*   [Schedule Demo](/schedule-demo)
    
*   [OSS Friends](/oss-friends)
    

Legal

*   [Security](/security)
    
*   [Imprint](/imprint)
    
*   [Terms](/terms)
    
*   [Privacy](/privacy)
    

© 2022-2024 Langfuse GmbH / Finto Technologies Inc.