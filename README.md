# etl_python

# ETL de produções científicas usando a API OpenAlex
# Adequado para estudos bibliométricos em Ciência da Informação

import requests
import pandas as pd
from datetime import datetime

# -----------------------------
# CONFIGURAÇÕES
# -----------------------------

URL = "https://api.openalex.org/works"
EMAIL = "rammonzulu@alu.ufc.br"
PER_PAGE = 200  # máximo permitido pelo OpenAlex

PARAMS_BASE = {
    "mailto": EMAIL,
    "per-page": PER_PAGE,
    "filter": 'title_and_abstract.search:"ciência da informação"',
    "sort": "relevance_score:desc"
}

# -----------------------------
# EXTRACT (paginação por page)
# -----------------------------

## Extrai produções científicas da OpenAlex utilizando paginação por página.
def extract_openalex_por_pagina(max_pages=10): 
    todos_resultados = []
    for page in range(1, max_pages + 1):
        params = {
            "mailto": EMAIL,
            "per-page": PER_PAGE,
            "page": page,
            "filter": 'title_and_abstract.search:"ciência da informação"'
        }
        response = requests.get(BASE_URL, params=params)
        response.raise_for_status()
        data = response.json()
        resultados = data.get("results", [])
        if not resultados:
            break
        todos_resultados.extend(resultados)
    return todos_resultados

# -----------------------------
# TRANSFORM
# -----------------------------

    '"""
    Normaliza metadados relevantes para análise bibliométrica.
    """
def transform_openalex(registros):
    linhas = []
    for r in registros:
        linhas.append({
            "id": r.get("id"),
            "titulo": r.get("title"),
            "ano": r.get("publication_year"),
            "data_publicacao": r.get("publication_date"),
            "doi": r.get("doi"),
            "tipo": r.get("type"),
            "periodico": r.get("host_venue", {}).get("display_name"),
            "issn": ", ".join(r.get("host_venue", {}).get("issn", []) or []),
            "autores": "; ".join([
                (a.get("author", {}).get("display_name") or "")
                for a in r.get("authorships", [])
            ]),
            "instituicoes": "; ".join([
                (i.get("display_name") or "")
                for a in r.get("authorships", [])
                for i in a.get("institutions", [])
            ]),
            "citacoes": r.get("cited_by_count"),
            "open_access": r.get("open_access", {}).get("is_oa"),
            "licenca": r.get("open_access", {}).get("license")
        })
    return pd.DataFrame(linhas)

# -----------------------------
# LOAD
# -----------------------------

def load_data(df):
    """
    Salva os dados tratados em CSV.
    """
    timestamp = datetime.now().strftime("%Y%m%d_%H%M")
    df.to_csv(f"openalex_ciencia_da_informacao_{timestamp}.csv",
              index=False,
              encoding="utf-8")

# -----------------------------
# PIPELINE ETL
# -----------------------------

def run_etl():
    dados_brutos = extract_openalex_por_pagina(max_pages=20) # Changed function name to extract_openalex_por_pagina
    df_tratado = transform_openalex(dados_brutos)
    load_data(df_tratado)
    print(f"ETL concluído com {len(df_tratado)} registros.")

if __name__ == "__main__":
    run_etl()
