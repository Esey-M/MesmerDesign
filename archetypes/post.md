---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
# 150–160 char meta description. Lead with the answer/benefit + keyword.
description: ""
date: {{ .Date }}
lastmod: {{ .Date }}
draft: true
author: "The MesmerDesign Team"

# Cross-cutting taxonomy facets → auto-generated landing pages + related posts.
# Use existing terms where possible (see data/products.yaml for vocabulary).
themes: []          # faith | funny | mental-health | personalized | business | pet
recipients: []      # him | her | mom | teacher | friend | coworker | dog-lover | cat-lover | kids
occasions: []       # birthday | christmas | anniversary | mothers-day | teacher-appreciation | corporate
product_types: []   # mug | shirt | sweatshirt | poster | canvas | bottle | digital
keywords: []        # extra search phrases this post targets

# Products to feature (ids from data/products.yaml). Also powers ItemList schema.
featured_products: []

# Optional FAQ block → rendered by {{</* faq */>}} + emits FAQPage schema.
# faq:
#   - q: "Question phrased the way people search?"
#     a: "Concise, quotable answer."
---

{{</* answer */>}}
**One-paragraph, directly-quotable answer goes here.** Lead with the single best
recommendation so search snippets and AI engines can lift it verbatim.
{{</* /answer */>}}

## Top picks at a glance

{{</* product-grid ids="" */>}}

## Intro

Write 2–3 human, helpful paragraphs. Speak to the reader's situation.

## The picks

{{</* product id="" /*/>}}

## Frequently asked questions

{{</* faq */>}}
