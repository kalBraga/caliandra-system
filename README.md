# 🌺 Calliandra System — Agro-Resilience System

> **Democratização de dados de sensoriamento remoto da NASA para prevenção de incêndios e mitigação de estresse hídrico no Cerrado.**

![License: MIT](https://img.shields.io/badge/License-MIT-orange.svg)
![React](https://img.shields.io/badge/React-18-blue)
![TypeScript](https://img.shields.io/badge/TypeScript-5-blue)
![Architecture: Offline-First](https://img.shields.io/badge/Architecture-Offline--First-green)
![PWA](https://img.shields.io/badge/PWA-Ready-purple)

---

## 🎯 O Problema
No interior do Centro-Oeste brasileiro (com foco no Estado de Goiás e Bioma Cerrado), pequenos e médios produtores rurais enfrentam janelas climáticas cada vez mais imprevisíveis, secas severas e queimadas sazonais devastadoras.

### Principais Dores Identificadas:
- **Zonas Cegas de Conectividade:** Ausência prolongada de sinal 3G/4G/5G dentro dos talhões e pastagens.
- **Assimetria de Informação:** Dados de satélite (como queimadas e estresse hídrico) são altamente técnicos e inacessíveis para o produtor tradicional.
- **Dispositivos Limitados:** Dificuldade de executar aplicações pesadas em smartphones de entrada ou modelos antigos sem armazenamento em nuvem constante.

---

## 💡 A Solução

O **Calliandra System** é uma aplicação **PWA (Progressive Web App)** construída sob a filosofia **Offline-First** e impulsionada por uma **Arquitetura Orientada a Eventos (EDA)**. 

A plataforma coleta, sanitiza e condensa dados massivos de observação da Terra da **NASA (FIRMS, MODIS e VIIRS)**, permitindo que o produtor rural consulte o nível de risco de incêndios e recomendações de manejo da pastagem diretamente no celular, **mesmo no meio do pasto e sem qualquer sinal de internet**.

### 🌟 Principais Diferenciais:
- **Modo Duplo de UX (Acessibilidade):**
  - **Modo Simples (Produtor):** Cards visuais em linguagem natural, ícones de alto contraste e respostas rápidas ("Risco Baixo", "Risco Crítico").
  - **Modo Avançado (Agrônomo):** Camadas de geoprocessamento em tempo real, suporte à leitura de polígonos KML e métricas técnicas (FRP, NDWI, NDVI).
- **Recálculo Reativo de Risco no Dispositivo:** O aplicativo possui um algoritmo local que degrada e reavalia a criticidade dos focos de calor com base no tempo decorrido desde a última sincronização.

---

## 🏗️ Arquitetura do Sistema

O sistema utiliza uma abordagem em **Monorepo Unificado**, separando claramente os scripts de ingestão de dados da camada de interface reativa local.

## Architecture

```text
+-----------------------------------------------------------------------+
|                            USER INTERFACE                             |
|          (Modo Simples para Produtor | Modo Avançado para Agrônomo)   |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|                         APPLICATION LAYER (PWA)                       |
|           • UI Render: React 18 + Tailwind CSS                        |
|           • Geo Engine: Leaflet.js + Turf.js                          |
|           • Risk Engine: Local Fire Risk & Hydric Stress Calculator   |
+-----------------------------------------------------------------------+
                   /                                   \
     [ 1. Local Read/Write ]                   [ 2. Background Sync ]
                /                                       \
               v                                         v
+-----------------------------+           +-----------------------------+
|    LOCAL PERSISTENCE EDGE   |           |    REMOTE DATA SOURCE       |
|  • Dexie.js (IndexedDB)     |           |  • NASA FIRMS API           |
|  • Persistent Storage API   |           |    (MODIS / VIIRS Satellites)|
|  • Local Events & Cache     |           |  • Sanitized JSON Payload   |
+-----------------------------+           +-----------------------------+

Flow Breakdown
Local Read/Write (Offline Flow):

Toda interação, consulta de talhão ou cálculo de risco de queimada acontece primeiro contra o banco local (IndexedDB via Dexie.js).

O motor de risco local (Risk Engine) utiliza os dados armazenados na memória flash do aparelho para recalcular alertas em tempo real mesmo sem qualquer sinal de celular.

Background Sync (Online Flow):

Quando o smartphone detecta sinal de internet (Wi-Fi na sede da fazenda ou 4G na cidade), o Service Worker baixa os vetores sanitizados da NASA (JSONs pré-processados da região de Goiás).

Os novos pontos térmicos e dados de satélite atualizam a base local sem interromper o uso da interface.
