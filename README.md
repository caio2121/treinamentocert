# Simulador de exame HCIA Storage (Huawei)

Repositório com um **simulador estático de prática** para o exame **Huawei HCIA Storage V5.0**: uma aplicação HTML única que roda no navegador, sem backend.

## O que é este repositório

Este projeto serve para **estudo e autoavaliação** no formato de simulado (questões e conjuntos de prova). O conteúdo é voltado ao domínio de armazenamento Huawei alinhado à certificação HCIA Storage.

## Conteúdo do repositório

| Caminho | Descrição |
|---------|-------------|
| `docs/` | Site estático publicado no GitHub Pages (`docs/index.html` é a página principal). |
| `docs/index.html` | Aplicação completa do simulador (HTML, CSS e JavaScript embutidos). |

## Aviso sobre origem dos dados e do conteúdo

- Parte do material pode ter sido **organizado ou gerado com base em informações de sites públicos** e em **documentação oficial ou pública** disponível na internet.
- Este repositório **não é afiliado à Huawei** nem substitui materiais oficiais, treinamentos credenciados ou o exame real.
- As questões e textos são **apenas para prática**; podem conter imprecisões ou ficar desatualizados em relação ao exame vigente. **Use por sua conta e risco** no contexto de estudo.

## GitHub Pages

Após o primeiro deploy bem-sucedido, o simulador fica disponível na URL do Pages do repositório (ex.: `https://<usuario>.github.io/<repositorio>/`).

O site é servido a partir da pasta **`docs/`** na branch **`main`** (`docs/index.html`).

### Habilitar Pages (uma vez no GitHub)

1. Abra o repositório no GitHub.
2. Vá em **Settings** → **Pages**.
3. Em **Build and deployment** → **Source**, escolha **Deploy from a branch**.
4. Defina **Branch** como `main` e a pasta **`/docs`**, depois **Save**.
5. Aguarde um ou dois minutos e acesse a URL indicada em **Pages**.

### Pré-visualização local

Sirva a raiz do repositório com qualquer servidor estático e abra `docs/index.html`, por exemplo:

```bash
python -m http.server 8765
```

Em seguida acesse `http://127.0.0.1:8765/docs/`.

## Licença e marcas

**Huawei** e nomes de produtos/certificações Huawei são marcas dos respectivos titulares. Consulte os termos de uso da documentação oficial ao reutilizar conteúdo proprietário.
