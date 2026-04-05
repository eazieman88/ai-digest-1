# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Команды

- `npm run dev` — локальный dev-сервер
- `npm run build` — production-сборка
- `npm run preview` — превью билда
- `npx astro check` — проверка типов и ошибок

## Стек

Astro 6 (static), TypeScript, MDX, Vercel. Node >= 22.12.0.

## Архитектура

Статический блог на Astro с content collections. Контент на русском языке (`lang="ru"`).

### Content model

Посты хранятся в `src/content/blog/YYYY/MM/` — организация по году и месяцу. Glob-паттерн `**/*.{md,mdx}` подхватывает вложенные папки автоматически.

Схема frontmatter определена в `src/content.config.ts` (Zod):
- `title`, `description`, `pubDate` — обязательные
- `type: 'morning' | 'evening' | 'weekly'` — обязательное
- `tags: string[]` — по умолчанию `[]`
- `heroImage`, `source`, `updatedDate` — опциональные

### Slug и маршрутизация

Файловая структура `YYYY/MM/` используется только для организации. В URL попадает только имя файла (без пути папок) — логика обрезки в `src/pages/blog/[...slug].astro`.

URL: `/blog/2026-03-25-evening-claude-opus/` (не `/blog/2026/03/...`).

### Layout

`BlogPost.astro` используется и для постов, и для статичных страниц (about). Props типизированы как `Partial<CollectionEntry<'blog'>['data']>` с обязательными `title`, `description`, `pubDate` — чтобы about мог не передавать `type` и `tags`.

Компонент `Disclaimer.astro` автоматически добавляется в конце каждого поста через layout — не дублировать в Markdown.

### Соглашения по файлам

- Имя файла поста: `YYYY-MM-DD-type-slug.md`
- Изображения: `public/images/YYYY-MM-DD/`, формат WebP
- Теги: рубрики (`stocks`, `futures`, `crypto`, `commodities`, `forex`, `macro`), формат (`weekly`, `morning`, `evening`), аналитика (`atas`, `order-flow`, `macro-us`, `macro-ru`, `macro-eu`)
