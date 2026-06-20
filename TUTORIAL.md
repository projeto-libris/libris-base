# Tutorial de instalação do Libris Base

Bem-vindo(a) ao **Libris Base**! Este guia explica como criar o seu próprio projeto da **família de enciclopédias gerais Libris**.

## Requisitos

Antes de começar, você precisará de:

* Uma conta no GitHub;
* Uma conta no Supabase;
* Um serviço de hospedagem (Cloudflare Pages, GitHub Pages, Netlify, Vercel ou outro);
* Conhecimentos básicos de HTML, CSS e JavaScript.

---

# 1. Obtendo o código-fonte

Faça um fork deste repositório ou clone-o diretamente pelo **terminal do Windows**:

```bash
git clone https://github.com/projeto-libris/libris-base.git
cd libris-base
```

Ou baixe o código-fonte em formato `ZIP`.

---

# 2. Criando um projeto no Supabase

1. Acesse o **[Supabase](https://supabase.com)**.

2. Crie uma nova organização.

3. Clique em **New Project**.

4. Escolha:

* Nome do projeto;
* Senha do banco de dados;
* Região do servidor (**recomendado: South America - São Paulo (sa-east-1)**).

Após a criação, abra:

**Project Settings → API**

Copie:

* Project URL;
* anon public key (**pesquisar por Get API keys**).

---

# 3. Configurando as variáveis

Em ``main.js``, substitua:

```
const SUPABASE_URL     = 'SUBSTITUA_AQUI_SEU_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'SUBSTITUA_AQUI_SEU_SUPABASE_ANON_KEY';
```

pelo **Supabase URL** e **Supabase anon key** que o projeto gerou.

---

# 4. Criando as tabelas

Copie o código a seguir.

```
CREATE TABLE public.categories (
  id         SERIAL PRIMARY KEY,
  name       TEXT NOT NULL,
  slug       TEXT NOT NULL UNIQUE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

COMMENT ON TABLE  public.categories        IS 'Categorias das publicações do projeto.';
COMMENT ON COLUMN public.categories.slug   IS 'Identificador único usado na URL (?categoria=slug).';


CREATE TABLE public.posts (
  id           SERIAL PRIMARY KEY,
  title        TEXT        NOT NULL,
  slug         TEXT        NOT NULL UNIQUE,
  content      TEXT        NOT NULL,
  excerpt      TEXT,
  category_id  INTEGER     REFERENCES public.categories(id) ON DELETE SET NULL,
  published_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

COMMENT ON TABLE  public.posts             IS 'Artigos do projeto.';
COMMENT ON COLUMN public.posts.slug        IS 'Identificador único usado na URL (?post=slug).';
COMMENT ON COLUMN public.posts.content     IS 'Texto completo. Parágrafos separados por linha em branco.';
COMMENT ON COLUMN public.posts.excerpt     IS 'Resumo opcional (não obrigatório na versão atual do front-end).';


CREATE OR REPLACE FUNCTION public.set_updated_at()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$;

CREATE TRIGGER posts_set_updated_at
  BEFORE UPDATE ON public.posts
  FOR EACH ROW
  EXECUTE FUNCTION public.set_updated_at();


ALTER TABLE public.categories ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.posts      ENABLE ROW LEVEL SECURITY;

CREATE POLICY "leitura_publica_categories"
  ON public.categories
  FOR SELECT
  USING (true);

CREATE POLICY "leitura_publica_posts"
  ON public.posts
  FOR SELECT
  USING (true);

CREATE POLICY "escrita_autenticada_categories"
  ON public.categories
  FOR ALL
  USING (auth.role() = 'authenticated');

CREATE POLICY "escrita_autenticada_posts"
  ON public.posts
  FOR ALL
  USING (auth.role() = 'authenticated');


INSERT INTO public.categories (name, slug) VALUES
  ('Cultura',   'Cultura'),
  ('Geografia',    'Geografia'),
  ('Tecnologia',  'Tecnologia'),
  ('Fora de Tópico',     'Fora de Tópico');


INSERT INTO public.posts (title, slug, content, category_id, published_at)
VALUES (
  'Primeiro artigo',
  'Primeiro_artigo',
  'Este é o primeiro artigo do seu projeto Libris.

Edite ou apague este registro no Supabase assim que quiser começar com seu próprio conteúdo.

Para criar um novo artigo, vá em Table Editor → posts → Insert row.',
  (SELECT id FROM public.categories WHERE slug = 'pessoal'),
  NOW()
);
```

Você pode adicionar outras tabelas conforme as necessidades do seu Libris.

---

# 5. Configurando o projeto

Edite as informações principais:

* nome do projeto;
* descrição;
* logotipo;
* cores;
* favicon;
* categorias iniciais.

Exemplo:

```txt
Nome: Libris para Educadores
Descrição: Enciclopédia digital livre dedicada à educação.
```

---

# 6. Alterando os metadados

Altere:

* `<title>`;
* `meta description`;
* Open Graph;
* ícones;
* imagens de compartilhamento.

---

# 7. Criando o primeiro artigo

Crie um artigo de teste.

Exemplo:

```txt
Título: Página principal
Slug: Pagina_principal
Conteúdo: Bem-vindo(a) ao seu novo projeto Libris.
```

---

# 8. Publicando

Você pode publicar o projeto em:

* Cloudflare Pages (domínio: **.pages.dev**);
* GitHub Pages (domínio: **.github.io**);
* Netlify (domínio: **.netlify.app**);
* Vercel (domínio: **.vercel.app**);
* servidor próprio.

Após a publicação, verifique:

* carregamento das páginas;
* funcionamento das categorias;
* conexão com o Supabase;
* carregamento de artigos.

---

# 9. Fazendo parte da Família Libris

Os projetos pertencentes à Família de enciclopédias gerais Libris deverão exibir a seguinte declaração (no ``<footer class="card-footer">``, onde se encontra "**© 2026 Projeto Libris. Todos os direitos reservados.**"):

> © 2026 Projeto Libris. Todos os direitos reservados. | Este projeto Libris faz parte da família de enciclopédias gerais Libris.

ou

> © 2026 Projeto Libris. All rights reserved. | This Libris project is part of the Libris family of general encyclopedias.

---

# 10. Próximos passos

Agora você pode:

* criar categorias;
* escrever artigos;
* personalizar o visual;
* convidar colaboradores;
* documentar conhecimentos sobre qualquer tema.

---

## Veja também

* [README.md](./README.md)
* [CONTRIBUTING.md](./CONTRIBUTING.md)
* [Página de documentação](https://libris-base.pages.dev/docs)

---

> ***Libris Base**: Uma base para a criação de enciclopédias digitais independentes.*
