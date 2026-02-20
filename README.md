# AI Overview Gap Analyzer

Сервис анализирует веб-страницу и показывает, чего не хватает для попадания в Google AI Overview.

## Как использовать

POST https://primary-production-0c2e7.up.railway.app/webhook/analyze

Body:
{"query": "what is kubernetes", "url": "https://kubernetes.io/docs/concepts/overview/"}

## Архитектура

Webhook → валидация → SerpAPI (получение AI Overview) → Jina Reader (извлечение текста страницы) → GPT-4o-mini (извлечение сущностей и фактов) → GPT-4o-mini (gap-анализ и рекомендации) → Redis (сохранение) → ответ.

Пайплайн построен на n8n. SerpAPI получает AI Overview из Google. Jina Reader извлекает чистый текст страницы без парсинга HTML. GPT-4o-mini в два прохода анализирует расхождения и формирует рекомендации. Результаты сохраняются в Redis.

## Ограничения SerpAPI

Бесплатный план даёт 100 запросов в месяц. AI Overview генерируется не для всех запросов — только для информационных. Результаты зависят от региона.

## Пример запроса

curl -X POST https://primary-production-0c2e7.up.railway.app/webhook/analyze \
  -H "Content-Type: application/json" \
  -d '{"query": "what is kubernetes", "url": "https://kubernetes.io/docs/concepts/overview/"}'
```
