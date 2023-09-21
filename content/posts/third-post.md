---
title: "Сайт портфолио"
date: 2023-09-19T18:01:39+03:00
slug: "portfolio-site"
categories: ["web", "frontend"]
description: Статический сайт и тема для портфолио. Создан при помощи Hugo.
---

## Общая информация

- Статический сайт который предназначен для вывода информации о проетах в виде отельных статей;
- сайт создан на основе фреймворка [Hugo](https://gohugo.io/);
- была создана собсвенная тема для Hugo;
- была использована css библиотека [Tachyons](https://tachyons.io/).

## Реализация

```html
    {{ define "main" }}<main class="pa4">
    <p class="lh-copy tc"> {{ .Param "description" }} </p>
    {{ range where .Site.RegularPages "Type" "posts" }}<div class="bt b--black-10 pa3 bw1">
        <a class="f3 fw6 hover-light-blue link blue" href="{{ .Permalink }}"> {{ .Title }} </a>
        <p> Добавлено: {{ .Params.date.Format "02.01.2006" }} </p>
        <p class="lh-copy"> {{ .Params.description }} </p>
        {{ with .Params.categories }}<ul class="list pl0 cf w-100">
            {{ range . }}<li class="hover-light-silver mw4 fl ma1 w-100 w-20-ns h2 ba br3 grow black-60">
                 <a class="f5 link dib black-80 w-100 h-100 tc pt2">{{ . }}</a>
             </li>{{ end }}
         </ul>{{ end }}
      </div>{{ end }}
    </main>{{ end }}
```

#### Фрагмент html разметки вывода списка статей.

### Полный исходный код сайта можно посмотреть [здесь](https://github.com/Mofrog).