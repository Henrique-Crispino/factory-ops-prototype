# Controle da Fábrica

**Protótipo full-stack no browser** para operação de fábrica de salgados + lojas — estoque por lote, caixa, envio, venda e encomenda de festa.

[![Next.js](https://img.shields.io/badge/Next.js-16-black?logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-4-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

> Repo: `factory-ops-prototype` · **não é CRM**. Protótipo de propósito: validar **regra de negócio** no browser antes de API, auth e estoque centralizado.

---

## Sobre o projeto

Sistema pensado para o dia a dia de uma rede pequena: a **câmara** produz e manda, a **loja** vende com caixa aberto, o **admin** enxerga a rede. Regras explícitas — FIFO que pula vencido, pedido que reserva estoque, festa com sinal separada do balcão.

**Destaques técnicos:**

- App Router (Next.js 16) com UI responsiva (desktop + telefone)
- Persistência **100 % client-side** com Dexie (IndexedDB)
- Domínio rico em TypeScript: caixa, estoque, encomendas, volume da fábrica
- Suite de auditoria automatizada (**243** cenários de fluxo + **71** de UI)

**Posicionamento:** case de portfólio / demo de domínio. Não é SaaS comercial nem ERP em produção.

---

## Funcionalidades

| Módulo | O que faz |
|--------|-----------|
| **Estoque** | Lote, validade, FIFO, inventário com 2ª contagem + testemunha |
| **Fábrica** | Produzir, comprar, enviar, romaneio, cliente levou (volume) |
| **Loja** | Caixa, vender, pedir reposição, receber envio, sobra do dia |
| **Festa** | Encomenda com data, sinal e resto na entrega — fora do `/vender` |
| **Pessoas** | Porta com PIN, ficha única, consumo interno, carimbo de quem operou |
| **Admin** | Dashboard, relatórios CSV/impressão, pacote do dia |

---

## Stack

| Camada | Tecnologia |
|--------|------------|
| Framework | Next.js 16 (App Router) |
| UI | React 19, Tailwind CSS 4, Lucide |
| Linguagem | TypeScript 5 |
| Dados | Dexie 4 + IndexedDB (`gp-salgados`) |
| Gráficos | Recharts 3 |
| Qualidade | ESLint 9, scripts de auditoria (`tsx` + Chrome headless) |

**Arquitetura:** sem API REST nem banco remoto neste protótipo. Cada navegador mantém seu próprio IndexedDB (útil para demo; limitação consciente para produção).

---

## Pré-requisitos

| Requisito | Versão |
|-----------|--------|
| **Node.js** | 20 LTS ou superior |
| **npm** | 10+ (vem com Node) |
| **Navegador** | Chrome ou Edge (recomendado para IndexedDB) |

---

## Como rodar

### 1. Clonar e instalar

```bash
git clone https://github.com/Henrique-Crispino/factory-ops-prototype.git
cd factory-ops-prototype
npm install
```

### 2. Ambiente de desenvolvimento

```bash
npm run dev
```

Abra **http://localhost:3000**. Na primeira visita, o seed de demonstração carrega automaticamente (dados relativos ao dia atual).

### 3. Build de produção (local)

```bash
npm run build
npm run start
```

---

## Scripts disponíveis

| Comando | Descrição |
|---------|-----------|
| `npm run dev` | Servidor de desenvolvimento com hot reload |
| `npm run build` | Gera build de produção |
| `npm run start` | Sobe o build (requer `build` antes) |
| `npm run lint` | ESLint no projeto |
| `npx tsx scripts/audit-flows.mts` | Auditoria automatizada de regras de negócio |
| `node scripts/audit-ui.mjs` | Smoke de UI no Chrome (requer dependências do script) |

---

## Demonstração (credenciais de exemplo)

Na tela inicial (`/`), escolha quem opera e use o PIN **`1234`**.

| Personagem | Papel | Acesso |
|------------|-------|--------|
| **Ana** | Dono | Admin · todos os painéis · sem caixa na ficha |
| **Carlos** | Gerente | Admin · todos os painéis · abre caixa em qualquer loja |
| **Lia** | Operadora de caixa | Loja Centro |
| **Bruno** | Fábrica | Produção, envio, cliente levou |

**Fluxo sugerido para testar:**

1. Entrar como **Lia** → abrir caixa → vender
2. Trocar para **Bruno** (rodapé: *Ir para outro lugar*) → produzir → mandar para loja
3. Voltar à loja → **Receber** → conferir envio
4. Entrar como **Carlos** na admin → ver dashboard e relatórios

Lojas do exemplo: **Loja Centro** e **Loja Jardim**.

---

## Estrutura do projeto

```
├── src/
│   ├── app/                 # Rotas Next.js (loja, fábrica, admin, caixa…)
│   ├── components/          # UI, AppShell, dashboards
│   └── lib/                 # Regra de negócio
│       ├── stock.ts         # Estoque, venda, produção, inventário
│       ├── cash.ts          # Caixa, sangria, fechamento
│       ├── encomendas.ts    # Festa, sinal, entrega
│       ├── requests.ts      # Pedido da loja / poço
│       ├── factory-orders.ts
│       ├── actor.ts         # Quem opera, testemunha
│       └── seed.ts          # Dados de demonstração
└── scripts/
    ├── audit-flows.mts      # 243 testes de fluxo
    └── audit-ui.mjs         # Smoke de UI
```

---

## Testes e qualidade

```bash
npx tsx scripts/audit-flows.mts
node scripts/audit-ui.mjs
```

Resultado esperado: **243/243 PASS** (lib) e **71/71 PASS** (UI no Chrome).

---

## Status do protótipo

- Temporadas 1–11 fechadas no código (caps 01–61)
- Suite de auditoria de fluxos e UI em verde
- Limitação consciente (classe A): PIN em texto, DevTools, multi-dispositivo → escopo de produção

---

## Limitações (protótipo)

Este repositório **valida regra**, não substitui ERP em produção:

- IndexedDB local — dois PCs = dois estoques
- PIN e senhas em texto plano
- Sem NF-e, motoboy real, delivery operacional ou crediário
- “Delivery” na venda é **rótulo** de relatório, não logística

---

## Próximos passos (produção — fora deste repo)

Caminho natural se o protótipo virasse sistema de loja:

1. **API + banco** (ex.: Postgres) — estoque único entre dispositivos
2. **Auth de verdade** — sem PIN em texto / IndexedDB como fonte da verdade
3. **Hospedagem** e backup — fora do Chrome de uma máquina só
4. Escopos à parte (NF-e, logística, etc.) quando a operação pedir

Neste repositório isso **não** está implementado de propósito: o valor do case é o domínio modelado e a suite de auditoria.

---

## Documentação interna

Notas de produto / capítulos de regra ficam **só na máquina de desenvolvimento** (`docs/` no `.gitignore`) — não fazem parte do repositório público.

---

## Licença

[MIT](./LICENSE) — uso livre para estudo, demo e portfólio.

---

<p align="center">
  Protótipo de propósito · Next.js + TypeScript + Dexie · portfólio
</p>
