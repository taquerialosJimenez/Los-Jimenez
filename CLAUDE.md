# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single, self-contained menu for the taquería **Los Jiménez** (San Pedro Garza García, N.L., Mexico), published via GitHub Pages. The entire project is `index.html` — markup, CSS (in a `<style>` block), an inline vanilla-JS `<script>`, and content all in one file. No build step, no framework, no dependencies (Google Fonts via CDN), no tests.

To preview: open `index.html` in a browser. To produce the print artifact, use the browser's Print dialog (the `@media print` block hides all interactive UI and restores the 3-ink, US-Letter design).

## Two modes in one file

The same document serves two purposes; keep both working:
- **Screen (interactive):** category filters, per-item +/− steppers, and a fixed bottom order bar with a live total, an expandable detail panel, and a "send order via WhatsApp" link.
- **Print:** the original 3-ink, single-page letter menu. The `@media print` block hides `.filtros`, `.stepper`, and `.pedido-bar`, forces all sections visible, and drops the cart highlight.

## How the interactivity works (inline `<script>`)

The script is intentionally **data-free** — it does not duplicate the menu. On load it scans every `.item`, reads the display name from `.item-nombre` (the ★ is a CSS `::after`, so it isn't captured; the "Mega" span text is), parses the price from `.item-precio`, injects a stepper, and builds an in-memory `cart` keyed by positional id (`i0`, `i1`, …). Consequences when editing:
- **To add/remove/reprice an item, edit only the HTML row.** The cart, totals, and WhatsApp message follow automatically.
- Filtering keys off `data-cat` on each `.seccion` (`tacos`/`hamburguesas`/`papas`/`bebidas`), matched against the filter buttons' `data-filter`. A new section needs a `data-cat` and a matching `.filtro` button.
- The WhatsApp target is the `WHATSAPP` constant at the top of the script (country-coded, digits only).

## Design constraints (read before editing styles)

The menu is engineered as a **3-ink print job** — this is the core constraint, documented in the comment block near the top of the `<style>`:

- **T1** `#1C1C1C` (black) — body text, footer, rules
- **T2** `#C8181A` (red) — header, accents, "más pedido" (most-ordered) markers
- **T3** — the cream paper itself (`--papel #FAFAF6`), i.e. no ink

Gray (`--gris`) and the faint rules (`--linea`) are **not extra inks** — they are screened/halftoned black. Do not introduce new colors; any "new color" must be expressed as an opacity of T1 or T2, or it breaks the print spec.

The page is sized to **US Letter at 96 dpi: 816 × 1056 px** (`.carta`). Keep content fitting on one page when adding items.

## Content structure

Menu sections (`.seccion`) are numbered `01`–`04`: TACOS, HAMBURGUESAS, PAPAS, BEBIDAS. Each holds a 2-column `.items-grid` of `.item` rows (`.item-nombre` + `.item-precio`).

Conventions when editing items:
- Mark a best-seller by adding the `top` class to the `.item` — this auto-appends a red ★ after the name and turns the price red (via the `.item.top` CSS rules). The `★ Más pedido` legend at the top explains it.
- Prefix a name with `<span class="mega">Mega</span>` for the "Mega" product line (e.g. Mega Gringa).
- Prices are plain text like `$175`.

Content (item names, prices, address, phone, delivery badges) is Spanish and specific to this business; treat copy changes as real business data, not placeholders.

## Taquería - Sistema de Órdenes (Proyecto Personal Esteban)

Negocio de taquería independiente de FTS. Stack: Stripe (pagos online) + Netpay (presencial).

**Flujos de orden:**
- Mesa: QR en mesa → cliente pide y paga desde su cel
- RT (Recoger en Taquería): cliente pide y paga anticipado desde su cel
- Para llevar: igual que RT
- Tablet standalone: taquero toma pedido y cobra él mismo

**Integración Rappi/UberEats:**
- NO scraping, NO API directa (restringida)
- Flujo: empleado recibe pedido → copia texto ticket → pega en app GitHub Pages → Claude API parsea → push a Odoo vía XML-RPC
- Formato input: texto plano copy/paste, no PDF
- Pendiente: parser tickets (necesita ejemplos reales) + UI + Odoo

**Todos los pedidos convergen en Odoo para reportería unificada.**
