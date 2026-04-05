# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Команды

- `npm run dev` — dev-сервер на localhost:4321
- `npm run build` — production-сборка в `/dist/`
- `npm run preview` — превью билда
- `npx astro check` — проверка типов и ошибок

## Стек

Astro 6 (static), TypeScript, MDX, Sharp, Vercel. Node >= 22.12.0.

Интеграции: `@astrojs/mdx`, `@astrojs/sitemap`, `@astrojs/rss`.

## Архитектура

Статический блог на Astro с content collections. Контент и интерфейс на русском (`lang="ru"`).

Проект курса Claude Code Basics — от шаблона до автоматического пайплайна публикации.

### Content model

Посты в `src/content/blog/YYYY/MM/`. Glob-паттерн `**/*.{md,mdx}` подхватывает вложенные папки.

Схема frontmatter в `src/content.config.ts` (Zod):
- **Обязательные:** `title`, `description`, `pubDate`, `type: 'morning' | 'evening' | 'weekly'`
- **Опциональные:** `tags: string[]` (default `[]`), `heroImage`, `source` (URL первоисточника), `updatedDate`

### Slug и маршрутизация

Папки `YYYY/MM/` — только для организации. В URL попадает только имя файла: `/blog/2026-03-25-evening-claude-opus/` (не `/blog/2026/03/...`).

Логика обрезки пути: `src/pages/blog/[...slug].astro` — `post.id.split('/').pop()`.

**Важно:** RSS-фид (`src/pages/rss.xml.js`) использует `post.id` напрямую (включая путь папок) — это расхождение с маршрутизацией блога.

### Layout

`BlogPost.astro` — единый layout для постов и статичных страниц (about). Props: `Partial<CollectionEntry<'blog'>['data']>` с обязательными `title`, `description`, `pubDate`. Это позволяет about не передавать `type` и `tags`.

`Disclaimer.astro` автоматически добавляется в конце каждого поста через layout — не дублировать в Markdown.

### Соглашения по файлам

- Имя файла поста: `YYYY-MM-DD-type-slug.md` (пример: `2026-03-25-evening-claude-opus.md`)
- Изображения: `public/images/YYYY-MM-DD/`, формат WebP
- Hero image: рекомендуемый размер 1020×510px

### Теги

Рубрики: `stocks`, `futures`, `crypto`, `commodities`, `forex`, `macro`
Формат: `weekly`, `morning`, `evening`
Аналитика: `atas`, `order-flow`, `macro-us`, `macro-ru`, `macro-eu`

### Ключевые файлы

| Файл | Назначение |
|------|------------|
| `src/consts.ts` | `SITE_TITLE`, `SITE_DESCRIPTION` — используются в BaseHead, Header, RSS |
| `src/content.config.ts` | Zod-схема контента, loader для blog-коллекции |
| `src/styles/global.css` | Дизайн-система: accent `#2337ff`, шрифт Atkinson, контент 720px, breakpoint 720px |
| `src/components/BaseHead.astro` | Meta/OG/Twitter, шрифты, RSS-ссылка, fallback-изображение `placeholder-cover.jpg` |
| `astro.config.mjs` | Site URL (сейчас `https://example.com` — заглушка), интеграции MDX + Sitemap |
