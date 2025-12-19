# **ОТЧЕТ ПО ЛАБОРАТОРНОЙ РАБОТЕ 3 - АНАЛИЗ CI/CD: ПЛОХИЕ И ХОРОШИЕ ПРАКТИКИ**

## **📋 Обзор**
Сравнительный анализ реализации CI/CD пайплайнов с использованием плохих и хороших практик. Основано на статье "Как рождаются *Ops'ы" с Habr об эволюции DevOps-практик.

## **🔍 Выявленные плохие практики**

### **1. Хардкод учетных данных**
**❌ Проблема:**
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
          echo "Memulai deployment ke lingkungan DEV..."
          # Credentials/Token langsung ditulis di sini
          DEPLOY_TOKEN="DEV_SECRET_TOKEN_12345" # <-- KEAMANAN RENDAH
          curl -X POST -H "Authorization: Bearer $DEPLOY_TOKEN" http://dev.example.com/deploy
  build_test_prod:
    runs-on: ubuntu-latest
    container: node:latest 
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Install and Test
        run: |
          npm install # <-- Duplikasi kode
          npm test    # <-- Duplikasi kode
          
      - name: Notification
        run: echo "Code siap untuk Production."
```
# хорошо
