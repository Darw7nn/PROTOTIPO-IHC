# 🍔 Projeto Waiter — O "Trivago" do Delivery
**Link do Protótipo:** [https://munch-compare-quest.lovable.app/](https://munch-compare-quest.lovable.app/)

## 📝 Visão Geral
O **Waiter** é uma plataforma inovadora de comparação de preços para o mercado de delivery. O objetivo central é economizar tempo e dinheiro do usuário, agregando em uma única interface os preços de pratos, bebidas e taxas de entrega das principais plataformas do mercado, como **iFood, Uber Eats, 99Food e Keeta**.

---

## 🚀 Status do Projeto: Protótipo Funcional (Fase IA)
Atualmente, o projeto encontra-se na fase de **MVP (Produto Mínimo Viável)**. É importante destacar que:

* **Desenvolvimento com IA:** A interface e a lógica inicial foram construídas utilizando ferramentas de Inteligência Artificial de última geração (como Lovable.dev e modelos de LLM).
* **Foco na Experiência (UX):** Nesta etapa, o objetivo é validar a navegação, o layout profissional e a facilidade de comparação.
* **Dados Simulados (Mock Data):** Os preços e produtos exibidos no momento servem para demonstrar a funcionalidade. Os ajustes para dados reais e integrações técnicas serão realizados nas próximas etapas.

---

## 🛠️ Funcionalidades Implementadas

### 1. Comparação de Preços em Tempo Real (Interface)
O sistema exibe cards de produtos que mostram simultaneamente:
* Preço do item em cada plataforma.
* Taxa de entrega estimada.
* Tempo de espera para cada opção.

### 2. Filtros e Categorias
Navegação intuitiva por categorias populares:
* Hambúrgueres, Pizzas, Comida Japonesa.
* **Bebidas Alcoólicas:** Com sistema de verificação de idade (**Age Gate**) integrado para conformidade legal.

### 3. Otimização para Tráfego Pago
O site foi estruturado com foco em conversão:
* Design limpo e focado na barra de busca.
* Páginas leves para garantir carregamento rápido em anúncios de redes sociais.
* Estrutura de SEO amigável para motores de busca.

---

## 📋 Próximas Etapas e Ajustes Finais

Para a evolução do protótipo para um produto de mercado, os próximos passos incluem:

1.  **Integração de APIs:** Conectar o backend a serviços de dados para puxar preços reais e dinâmicos.
2.  **Geolocalização:** Personalizar os resultados com base no endereço de entrega do usuário.
3.  **Sistema de Cupons:** Adicionar um verificador de cupons ativos para cada plataforma, garantindo o "menor preço absoluto".
4.  **Expansão de Categorias:** Incluir mercados e farmácias para ampliar o ecossistema de comparação.

---

> **Nota:** Este projeto demonstra como o uso estratégico de IAs pode acelerar o ciclo de inovação, permitindo sair da ideia para um protótipo navegável em tempo recorde.
## 🛠️ Código Fonte do Protótipo (Index.tsx)

```tsx
import { useEffect, useMemo, useRef, useState } from "react";
import { SiteHeader } from "@/components/SiteHeader";
import { SiteFooter } from "@/components/SiteFooter";
import { SearchHero } from "@/components/SearchHero";
import { CategoryFilter } from "@/components/CategoryFilter";
import { ProductCard } from "@/components/ProductCard";
import { AgeGate } from "@/components/AgeGate";
import { Category, CATEGORIES, PRODUCTS } from "@/data/products";
import { SlidersHorizontal } from "lucide-react";

const ALCOHOLIC_CATEGORIES: Category[] = ["Cervejas", "Vinhos/Destilados"];

const Index = () => {
  const [query, setQuery] = useState("");
  const [category, setCategory] = useState<Category | "Todos">("Todos");
  const [forceGate, setForceGate] = useState(false);
  const [ageConfirmed, setAgeConfirmed] = useState(
    () => typeof window !== "undefined" && localStorage.getItem("waiter:age-confirmed") === "yes"
  );

  useEffect(() => {
    document.title = "Waiter — Compare preços de delivery em segundos";
  }, []);

  const handleCategory = (c: Category | "Todos") => {
    if (ALCOHOLIC_CATEGORIES.includes(c as Category) && !ageConfirmed) {
      pendingApply.current = (ok: boolean) => ok && setCategory(c);
      setForceGate(true);
      return;
    }
    setCategory(c);
  };

  const pendingApply = useRef<null | ((ok: boolean) => void)>(null);

  const filtered = useMemo(() => {
    return PRODUCTS.filter((p) => {
      if (category !== "Todos" && p.category !== category) return false;
      if (query.trim()) {
        const q = query.toLowerCase();
        return p.name.toLowerCase().includes(q) || p.restaurant.toLowerCase().includes(q);
      }
      return true;
    });
  }, [query, category]);

  return (
    <div className="min-h-screen bg-background">
      <AgeGate
        forceOpen={forceGate}
        onResolved={(ok) => {
          setForceGate(false);
          setAgeConfirmed(ok);
          pendingApply.current?.(ok);
          pendingApply.current = null;
        }}
      />
      <SiteHeader />
      <main>
        <SearchHero query={query} onQuery={setQuery} />
        <section id="comparar" className="container mx-auto -mt-12 px-4 md:-mt-16">
          <div className="mb-8 rounded-3xl bg-card p-4 shadow-card md:p-6">
            <CategoryFilter selected={category} onSelect={handleCategory} />
          </div>
          <div className="grid gap-6">
            {filtered.map((p) => <ProductCard key={p.id} product={p} />)}
          </div>
        </section>
      </main>
      <SiteFooter />
    </div>
  );
};

export default Index;
