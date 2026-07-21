Я бы начал не с кода и не с плагина, а с определения границ Product Toolkit. У тебя уже есть Architecture Toolkit, поэтому главная ошибка была бы сделать второй "комбайн".

Для Product Toolkit я бы построил его как product discovery + product documentation pipeline.

Шаг 1. Определить миссию плагина

Первый документ:

docs/
    product/
        VISION.md

Пример:

# Product Toolkit

## Purpose

Help teams create, maintain and evolve product documentation.

## Main capabilities

- Create PRD for new functionality
- Reverse engineer PRD from existing systems
- Validate requirements
- Maintain product knowledge

То есть сначала фиксируем: зачем существует этот инструмент.

Шаг 2. Определить основные capability

Я бы начал с минимального ядра:

Product Toolkit

├── PRD Management
│
├── Product Discovery
│
├── Requirements Management
│
└── Product Review
Шаг 3. Первый настоящий skill — PRD Generator

Это должен быть фундамент.

Например:

skills/
    prd/
        SKILL.md

Он должен поддерживать два режима:

Mode A — New Feature

Когда функциональность ещё не существует.

Input:

I want to add:
AI architecture reviewer

Output:

docs/features/ai-reviewer/

PRD.md
Mode B — Existing Feature

Когда функциональность уже есть.

Input:

Analyze existing authentication module

Output:

docs/features/authentication/

PRD.md

Но с разделами:

## Current Behavior

## User Value

## Functional Requirements

## Non Functional Requirements

## Unknowns

## Assumptions
Шаг 4. Определить структуру PRD

Я бы не копировал шаблоны из интернета. Для твоей системы нужен PRD, который хорошо передаётся в Architecture Toolkit.

Например:

PRD.md

# Overview

# Problem Statement

# Goals

# Non Goals

# Users

# User Stories

# Functional Requirements

# Non Functional Requirements

# Business Rules

# Data Requirements

# API Requirements

# Security Requirements

# Success Metrics

# Open Questions

Почему так?

Потому что потом Architecture Toolkit сможет читать:

Functional Requirements
        ↓
arc42 Context
        ↓
ADR decisions
Шаг 5. Создать связь с Architecture Toolkit

Не сейчас в коде, а концептуально.

Например:

Product Toolkit

creates:

docs/features/payments/PRD.md


Architecture Toolkit

consumes:

docs/features/payments/PRD.md
Шаг 6. Только потом превращать в плагин

Структура могла бы быть:

product-toolkit/

├── .claude-plugin/
│
├── skills/
│   ├── prd-generator/
│   ├── product-review/
│   └── requirements-analysis/
│
├── agents/
│   └── product-reviewer.md
│
├── templates/
│   └── PRD.md
│
└── README.md
Я бы сделал первый MVP очень маленьким

Версия 0.1:

Product Toolkit v0.1

✅ Create PRD
✅ Reverse engineer PRD
✅ PRD validation
✅ PRD template

Без:

roadmap;
personas;
analytics;
user research;
backlog management.

Это уже можно добавлять позже.

И ещё важный момент: твой Architecture Toolkit уже даёт тебе преимущество. Я бы проектировал Product Toolkit так, чтобы через время они могли работать как цепочка:

Product Toolkit
        |
        | PRD.md
        ↓
Architecture Toolkit
        |
        | arc42 + ADR
        ↓
Implementation

Поэтому первым артефактом я бы сделал не SKILL.md, а сам PRD template для Product Toolkit. Он определит весь будущий дизайн.

Link to the architecture-toolkit plugin - https://github.com/mmizin/architecture-toolkit