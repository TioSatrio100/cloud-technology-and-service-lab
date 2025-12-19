# **ОТЧЕТ ПО ЛАБОРАТОРНОЙ РАБОТЕ 3 - АНАЛИЗ CI/CD: ПЛОХИЕ И ХОРОШИЕ ПРАКТИКИ**

## **Обзор**
Сравнительный анализ реализации CI/CD пайплайнов с использованием плохих и хороших практик. Основано на статье "Как рождаются *Ops'ы" с Habr об эволюции DevOps-практик.

## **ыявленные плохие практики**

### **1. Хардкод учетных данных**
**Проблема:**
```yaml
# ПЛОХО
name: Bad CI Pipeline (Lab 3)

on: [push]

jobs:
  build_test_dev:
    runs-on: ubuntu-latest
    container: node:latest 
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Install and Test
        run: |
          npm install
          npm test
          
  
      - name: Deploy to DEV
        run: |
          echo "run deployment in environmet DEV..."
          # TOKEN write in here
          DEPLOY_TOKEN="DEV_SECRET_TOKEN_12345" # <-- security low
          curl -X POST -H "Authorization: Bearer $DEPLOY_TOKEN" http://dev.example.com/deploy
  build_test_prod:
    runs-on: ubuntu-latest
    container: node:latest 
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Install and Test
        run: |
          npm install # <-- code dupication
          npm test    # <-- code duplication
          
      - name: Notification
        run: echo "Code ready for production"
```
# хорошо
```yaml
# ПЛОХО
name: Good CI Pipeline (Lab 3)

on: [push]

jobs:
  common_build_test:
    runs-on: ubuntu-latest
    container: node:20.10.0-slim 
    outputs:
      status: ${{ steps.test_step.outcome }}
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Install and Test (Reusable)
        id: test_step
        run: |
          npm ci 
          npm test

  
  deploy_dev:
    needs: common_build_test 
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to DEV (Secure)
        env:
          DEV_TOKEN: ${{ secrets.DEV_DEPLOY_TOKEN }} 
        run: |
          echo "run deployment to secure environment.."
          if [[ "${{ needs.common_build_test.outputs.status }}" == "success" ]]; then
            echo "Deployment to http://dev.example.com succes to simulate"
            echo "used token (not allowed to see): $DEV_TOKEN"
            sleep 2 
          else
            echo "Deployment aborted"
            exit 1
          fi
```
# Pезультаты


# Выводы
Основные уроки:

Безопасность должна быть приоритетом - никогда не храните секреты в коде

Автоматизация требует архитектуры - продуманная структура пайплайна упрощает поддержку

Воспроизводимость критически важна - фиксируйте версии всех зависимостей

CI/CD - это не только сборка - правильная организация этапов обеспечивает качество доставки

Реализованные улучшения превращают CI/CD пайплайн из простого скрипта сборки в надежный инструмент DevOps, соответствующий современным стандартам индустрии.