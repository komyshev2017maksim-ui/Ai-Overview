# AI Overview Gap Analyzer

## Что делает
Принимает поисковый запрос и URL страницы, получает текст Google AI Overview,
сравнивает его с контентом страницы и возвращает gap-анализ с конкретными
рекомендациями как улучшить страницу для попадания в AI Overview.

## Архитектура пайплайна
Webhook → валидация входных данных → SerpAPI (получение AI Overview) →
проверка наличия AI Overview → Jina Reader (извлечение текста страницы) →
GPT-4o-mini (извлечение сущностей и фактов из AI Overview) →
GPT-4o-mini (gap-анализ и рекомендации) → Redis (сохранение результатов) → ответ.

Пайплайн построен на n8n. Если AI Overview не найден — возвращается корректный
статус без падения. На всех HTTP-нодах настроены retry (3 попытки) и таймауты.

## Способ получения AI Overview
SerpAPI — Google Search API. Ограничения: 100 бесплатных запросов в месяц,
AI Overview генерируется не для всех запросов (только информационные),
результаты зависят от региона и языка запроса.

## Живое API (задеплоено на Railway)

POST https://primary-production-0c2e7.up.railway.app/webhook/analyze
Content-Type: application/json

{"query": "what is kubernetes", "url": "https://kubernetes.io/docs/concepts/overview/"}

## Запуск
Сервис задеплоен на Railway и доступен по ссылке выше.
Для самостоятельного развёртывания: импортируй файл
workflow/AI_Overview_Gap_Analyzer.json в своей n8n,
добавь API ключи SerpAPI и OpenAI, настрой Redis credential, активируй workflow.

## Необходимые API ключи
- SerpAPI: https://serpapi.com
- OpenAI: https://platform.openai.com (gpt-4o-mini)
- Redis: https://redis.io/try-free

## Примеры запусков
См. папку examples/
