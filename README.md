# ETL de Produções Científicas – OpenAlex

Este projeto implementa um pipeline ETL (Extract, Transform, Load) em Python
para a coleta e organização de produções científicas da área de
**Ciência da Informação**, utilizando a API da plataforma OpenAlex.

O ETL é adequado para estudos bibliométricos e cientométricos.

---

## 🎯 Objetivo

Extrair e estruturar metadados de publicações científicas que contenham a
expressão **“ciência da informação”** nos campos de título e resumo,
possibilitando análises sobre autoria, periódicos, instituições,
impacto científico e acesso aberto.

---

## 🌐 Fonte de dados

- Plataforma: OpenAlex  
- Recurso: Works  
- Acesso: API REST pública  
- URL base: https://api.openalex.org/works  

---

## 🔍 Estratégia de busca

```text
title_and_abstract.search:"ciência da informação"
