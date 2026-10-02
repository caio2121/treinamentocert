Você está trabalhando no repositório:

`caio2121/treinamentocert`

Existe dentro do próprio repositório um PDF/mock contendo aproximadamente **730 questões** que precisam ser incorporadas ao simulador existente.

Seu papel é atuar como **agente principal/orquestrador** do trabalho.

## Objetivo

Importar corretamente todas as questões do novo mock para o simulador, preservando:

- enunciados;
- alternativas;
- gabaritos já existentes;
- número/origem de cada questão;
- imagens necessárias;
- tipo da questão;
- explicações posteriormente geradas por subagentes;
- separação lógica entre o banco atual e o novo mock.

O novo conteúdo deverá aparecer como uma **nova esteira/modalidade dentro do site existente**, reutilizando a engine atual do simulador.

Não crie um segundo simulador independente.

---

# REGRA FUNDAMENTAL

**Não comece alterando `index.html`, JavaScript, CSS ou banco de questões.**

Primeiro entenda completamente como o projeto funciona hoje.

Antes de qualquer implementação, faça uma análise técnica do repositório e identifique:

1. arquitetura atual;
2. arquivos responsáveis pelo simulador;
3. onde ficam as questões existentes;
4. estrutura/schema atual das questões;
5. como os gabaritos são armazenados;
6. como explicações são armazenadas;
7. como imagens são tratadas;
8. quais tipos de questão já são suportados;
9. como a engine escolhe e apresenta questões;
10. como funciona a esteira infinita/repetição;
11. como ocorre a pontuação;
12. como questões erradas são revisitadas;
13. como modalidades/categorias existentes são diferenciadas;
14. dependências entre HTML, JS, JSON, assets e demais arquivos;
15. riscos de regressão.

Use o código existente como fonte de verdade.

Não faça alterações arquiteturais desnecessárias.

---

# FASE 1 — AUDITORIA DO SITE ATUAL

Antes de modificar qualquer arquivo, produza um relatório curto contendo:

```text
ARQUITETURA ATUAL

Engine principal:
Banco(s) de questões:
Schema atual:
Fluxo de carregamento:
Tipos de questão suportados:
Tratamento de imagens:
Tratamento de explicações:
Seleção de modalidade:
Persistência/progresso:
Arquivos que provavelmente precisarão ser alterados:
Arquivos que NÃO deveriam precisar ser alterados:
Riscos encontrados:
```

Depois determine a estratégia mínima de integração.

O objetivo é **estender o simulador existente**, e não reescrevê-lo.

---

# FASE 2 — LOCALIZAR E ANALISAR O PDF

Localize no repositório o PDF/mock contendo aproximadamente 730 questões.

Antes de extrair tudo, analise uma amostra suficientemente representativa do documento.

Procure diferentes estruturas de questões, incluindo:

- múltipla escolha com uma resposta;
- múltipla escolha com várias respostas;
- verdadeiro/falso;
- questões com imagens;
- questões discursivas;
- questões de digitação/preenchimento;
- questões com tabelas;
- questões com códigos/comandos;
- questões cujo conteúdo continue em outra página;
- imagens que apareçam antes ou depois do texto;
- gabaritos em página separada;
- numeração irregular;
- questões potencialmente duplicadas.

Não assuma que todas seguem o mesmo layout.

---

# FASE 3 — DEFINIR O SCHEMA DE IMPORTAÇÃO

Antes da extração completa, defina uma representação estruturada capaz de preservar todas as informações relevantes.

Exemplo conceitual:

```json
{
  "id": "...",
  "source": "...",
  "source_question_number": 417,
  "type": "single_choice",
  "question": "...",
  "options": [
    {
      "id": "A",
      "text": "..."
    }
  ],
  "answer": ["A"],
  "images": [],
  "explanation": null,
  "review_required": false,
  "review_reasons": [],
  "metadata": {}
}
```

Esse exemplo não é obrigatório.

Adapte o schema à arquitetura existente sempre que isso for mais apropriado.

O schema precisa suportar, no mínimo:

```text
single_choice
multiple_choice
true_false
text_input / discursive
image_question
```

Um mesmo registro pode combinar características, por exemplo:

```text
type = multiple_choice
images = [...]
```

Não force todas as questões para um único tipo.

---

# FASE 4 — EXTRAIR AS ~730 QUESTÕES

Extraia todas as questões do PDF de forma estruturada.

Para cada questão, preserve obrigatoriamente:

- número original;
- origem/mock;
- texto completo;
- todas as alternativas;
- resposta/gabarito fornecido;
- tipo detectado;
- imagens associadas;
- observações de extração;
- necessidade ou não de revisão manual.

## Não renumerar silenciosamente

Se o documento contiver:

```text
416
417
419
```

não transforme automaticamente em:

```text
416
417
418
```

Registre a numeração original e sinalize a ausência.

---

# GABARITOS

Todas as questões do mock **já possuem gabarito**.

Portanto:

**NÃO use IA para descobrir qual alternativa deveria ser correta.**

O gabarito extraído do documento é a fonte de verdade para a importação.

Os agentes posteriores poderão analisar o conteúdo para produzir uma explicação, mas não deverão substituir silenciosamente o gabarito original.

Se surgir aparente inconsistência entre pergunta e gabarito:

```text
não corrigir automaticamente;
não inventar resposta;
não trocar a alternativa.
```

Marque:

```json
"review_required": true
```

e registre o motivo.

---

# VALIDAÇÃO DOS GABARITOS

Implemente validações automáticas.

Exemplos:

Se:

```text
answer = D
```

precisa existir alternativa `D`.

Se:

```text
answer = A,C
```

a questão deve possuir as alternativas A e C e ser compatível com seleção múltipla.

Detecte também:

- alternativa ausente;
- resposta inexistente;
- opções duplicadas;
- alternativas vazias;
- número de opções inesperado;
- questão sem gabarito;
- gabarito ambíguo;
- múltiplos gabaritos conflitantes;
- texto aparentemente truncado.

Nenhum desses problemas deve ser silenciosamente ignorado.

---

# QUESTÕES DISCURSIVAS / DIGITAÇÃO

Questões discursivas ou de preenchimento devem receber tratamento próprio.

**É proibido convertê-las silenciosamente em múltipla escolha.**

Identifique claramente o tipo.

Exemplo:

```json
{
  "type": "text_input",
  "expected_answer": "..."
}
```

Analise como incorporar esse tipo à engine atual com a menor alteração possível.

O simulador deve ser capaz de:

1. mostrar o enunciado;
2. permitir digitação;
3. comparar ou apresentar a resposta esperada conforme o modelo definido;
4. exibir a explicação;
5. preservar o comportamento geral de progresso da esteira.

Caso respostas discursivas admitam múltiplas variações razoáveis, não implemente uma comparação textual ingênua que penalize automaticamente diferenças irrelevantes sem avaliar o impacto.

---

# QUESTÕES COM IMAGENS

Esse ponto é crítico.

Uma questão com imagem deve preservar a associação:

```text
QUESTÃO 417
        ↓
imagem correta
        ↓
enunciado
        ↓
alternativas
        ↓
gabarito
```

Não basta extrair OCR/texto e descartar o elemento visual.

Extraia a imagem relevante do PDF e salve-a de forma organizada no projeto.

Utilize nomes previsíveis, por exemplo:

```text
assets/questions/<mock>/<numero>/image-01.webp
```

ou outra estrutura coerente com o projeto existente.

Nunca associe uma imagem somente pela proximidade sem validar o contexto.

Analise:

- página;
- posição;
- legenda;
- proximidade do enunciado;
- continuidade entre páginas;
- número da questão.

Se não for possível afirmar com confiança que determinada imagem pertence à questão:

```json
{
  "review_required": true,
  "review_reasons": [
    "image_association_uncertain"
  ]
}
```

Não importe silenciosamente uma questão incompleta.

---

# DUPLICATAS

Faça duas verificações distintas.

## Duplicatas dentro do novo mock

Identifique questões repetidas utilizando:

- número;
- texto normalizado;
- similaridade do enunciado;
- alternativas;
- imagem;
- gabarito.

Não considere apenas igualdade literal.

Classifique possíveis duplicatas como:

```text
exact_duplicate
probable_duplicate
similar_but_distinct
```

## Comparação com o banco atual

Compare também todas as novas questões contra as questões que já existem no simulador.

Não elimine uma questão apenas porque parece semelhante.

Gere relatório indicando:

```text
nova questão
questão existente correspondente
similaridade
motivo
gabaritos
classificação
```

A decisão final precisa ser rastreável.

---

# NÃO MISTURAR OS BANCOS

O banco atual e o novo mock não devem ser misturados inadvertidamente.

Crie uma camada, dataset ou coleção separada para o novo mock.

Exemplo conceitual:

```text
questions/
    current-bank.*
    novo-mock.*
```

ou estrutura equivalente compatível com o projeto.

O usuário deve conseguir selecionar claramente a nova modalidade.

A engine pode unificar internamente o schema, mas a **origem dos dados deve permanecer identificável**.

Cada questão deve preservar algo equivalente a:

```json
{
  "source": "novo-mock"
}
```

---

# NOVA ESTEIRA / MODALIDADE

Adicione o novo mock como uma nova modalidade dentro da interface atual.

O usuário deve conseguir escolher algo equivalente a:

```text
Banco atual
Novo Mock
```

Não é obrigatório utilizar esses nomes. Utilize o padrão visual e textual já existente.

A nova modalidade deve reutilizar:

- engine de apresentação;
- sistema de pontuação;
- navegação;
- controle de erros;
- repetição;
- progresso;
- explicações;
- demais recursos comuns.

Não duplique centenas de linhas da engine apenas para trocar a origem das questões.

Prefira parametrizar o dataset.

---

# ORQUESTRAÇÃO DE SUBAGENTES PARA EXPLICAÇÕES

O PDF contém os gabaritos, porém não contém explicações.

Você, agente principal, deverá atuar como **orquestrador**.

Distribua as questões em lotes para subagentes especializados.

Cada subagente receberá:

```text
número da questão
origem
tipo
enunciado
alternativas
gabarito OFICIAL extraído
imagem, quando existir
```

A tarefa do subagente será:

> Explicar por que o gabarito fornecido está correto.

O subagente **não deve escolher uma nova resposta**.

---

# PROMPT CONCEITUAL DOS SUBAGENTES

Cada subagente deve seguir estas regras:

```text
Você recebeu uma questão cujo gabarito já foi definido pela fonte original.

Sua função NÃO é resolver novamente a questão para substituir o gabarito.

Sua função é produzir uma explicação tecnicamente correta sobre por que o gabarito informado corresponde à resposta esperada.

Quando for uma questão de múltipla escolha:

1. explique a alternativa correta;
2. quando houver segurança técnica, explique brevemente por que cada alternativa incorreta não corresponde ao gabarito;
3. não invente fatos;
4. não force justificativas quando houver evidência de erro no material.

Se identificar aparente inconsistência entre conteúdo e gabarito:

- mantenha o gabarito original;
- marque a questão para revisão;
- explique objetivamente a inconsistência percebida.

Se a pergunta depender de imagem, analise também a imagem fornecida.

Produza explicação didática, objetiva e adequada a um simulador de certificação.
```

---

# PARALELIZAÇÃO

Não envie as ~730 questões a um único agente em um contexto gigantesco.

Distribua em lotes controlados.

Exemplo:

```text
Lote 001: questões 1–20
Lote 002: questões 21–40
...
```

O tamanho real dos lotes deve considerar:

- quantidade de texto;
- quantidade de imagens;
- complexidade;
- limite de contexto;
- capacidade de validação.

Questões com imagens podem exigir lotes menores.

O agente principal deve manter controle sobre:

```text
questão
lote
status
subagente
explicação recebida
validação
review_required
```

---

# NÃO PERDER RASTREABILIDADE

Cada transformação deve ser rastreável:

```text
PDF
 ↓
extração bruta
 ↓
normalização
 ↓
validação
 ↓
explicação
 ↓
dataset final
 ↓
simulador
```

Sempre deve ser possível descobrir de onde veio uma questão.

Considere manter artefatos intermediários fora da aplicação final, como:

```text
import/
  raw/
  normalized/
  reports/
```

caso isso seja adequado ao repositório.

---

# PIPELINE REPRODUZÍVEL

Não faça a conversão das 730 questões exclusivamente através de edição manual.

Crie scripts/ferramentas reproduzíveis quando necessário.

O objetivo é permitir algo equivalente a:

```text
PDF
 → extractor
 → normalized JSON
 → validator
 → duplicate checker
 → explanations
 → final dataset
```

Isso será importante caso seja necessário:

- corrigir a extração;
- trocar o PDF;
- repetir o processo;
- adicionar novos mocks futuramente.

Evite gerar um enorme arquivo manual impossível de auditar.

---

# VALIDAÇÃO QUANTITATIVA

Ao final da extração, apresente métricas.

Exemplo:

```text
Total identificado no PDF:
Total extraído:
Total importável:
Single choice:
Multiple choice:
True/false:
Discursivas/text_input:
Questões com imagem:
Questões sem imagem necessária:
Questões com review_required:
Duplicatas exatas:
Possíveis duplicatas:
Coincidências com banco existente:
Questões sem gabarito:
Gabaritos inválidos:
Explicações geradas:
Explicações pendentes:
```

Os totais precisam fechar.

Por exemplo:

```text
extraídas =
válidas +
review_required +
descartadas justificadamente
```

Não aceite simplesmente "aproximadamente 730" como validação final.

Determine a quantidade efetiva encontrada no documento.

---

# VALIDAÇÃO ESTRUTURAL AUTOMÁTICA

Crie um validador que percorra todo o banco e detecte, no mínimo:

```text
ID duplicado
número/origem ausente
enunciado vazio
tipo inválido
alternativas ausentes quando necessárias
alternativas duplicadas
gabarito ausente
gabarito apontando para alternativa inexistente
imagem referenciada mas inexistente
arquivo de imagem órfão
explicação vazia
schema inválido
origem desconhecida
```

A execução deve retornar erro quando existirem violações bloqueantes.

---

# VALIDAÇÃO DAS EXPLICAÇÕES

Não aceite automaticamente tudo que os subagentes produzirem.

O orquestrador deve verificar:

- explicação pertence à questão correta;
- referência ao gabarito correto;
- nenhuma alternativa foi silenciosamente trocada;
- nenhuma imagem foi perdida;
- não ocorreu mistura entre questões de lotes diferentes;
- conteúdo está no formato esperado.

Questões com inconsistência deverão permanecer sinalizadas para revisão.

---

# TESTES DO SIMULADOR

Depois da integração, teste pelo menos:

### Questão simples

```text
pergunta
alternativas
seleção
correção
explicação
próxima questão
```

### Múltiplas respostas

Confirme que:

```text
A + C
```

não é tratada como:

```text
A OU C
```

quando o gabarito exige ambas.

### Verdadeiro/Falso

Validar apresentação e correção.

### Discursiva

Validar entrada digitada e tratamento da resposta.

### Imagem

Confirmar:

```text
imagem correta
questão correta
responsividade
carregamento
```

### Alternância entre modalidades

Trocar:

```text
banco atual → novo mock → banco atual
```

e verificar que estados e datasets não se misturam indevidamente.

### Repetição de erro

Se a engine atual repete questões erradas após determinado número de questões, confirme que o novo dataset também respeita esse comportamento.

---

# RESPONSIVIDADE

O site precisa continuar funcionando corretamente em:

```text
desktop
tablet
mobile
```

Questões com imagens não podem:

- estourar a largura;
- ficar ilegíveis;
- criar scroll horizontal desnecessário;
- sobrepor alternativas.

Não redesenhe o site sem necessidade.

Preserve o padrão visual atual.

---

# PROTEÇÃO CONTRA REGRESSÃO

Não altere comportamento existente sem necessidade.

Antes e depois das alterações:

1. rode testes existentes;
2. identifique comportamento atual;
3. valide o banco antigo;
4. valide a nova modalidade;
5. compare funcionalidades.

Não "corrija" código existente que não faça parte desta tarefa apenas porque encontrou algo que poderia ser refatorado.

Registre problemas paralelos separadamente.

---

# NÃO FAZER

É proibido:

- começar diretamente editando `index.html`;
- substituir a engine existente;
- criar outro simulador paralelo;
- misturar silenciosamente os dois bancos;
- inventar gabaritos;
- substituir gabaritos usando IA;
- transformar discursivas em múltipla escolha sem justificativa;
- descartar imagens;
- associar imagem a uma questão sem confiança;
- eliminar duplicatas sem registrar;
- alterar questões existentes silenciosamente;
- inserir ~730 questões manualmente sem pipeline reproduzível;
- ignorar erros de extração;
- considerar importação concluída apenas porque o JSON parseia;
- fazer commit/push/deploy sem autorização explícita.

---

# COMMITS / PUSH / DEPLOY

Não faça:

```text
git commit
git push
deploy
```

sem autorização explícita.

Você pode modificar o working tree para realizar o trabalho, mas ao final deverá apresentar claramente:

```text
arquivos criados
arquivos alterados
arquivos removidos
testes executados
resultado dos testes
problemas pendentes
questões em review_required
```

---

# ORDEM OBRIGATÓRIA DE EXECUÇÃO

Siga esta sequência:

```text
1. Entender o repositório atual
2. Identificar a engine e schema existentes
3. Localizar e analisar o PDF
4. Identificar os formatos de questões existentes no PDF
5. Projetar a representação dos novos dados
6. Criar pipeline de extração
7. Extrair as questões
8. Extrair e validar os gabaritos
9. Extrair e associar imagens
10. Classificar os tipos de questões
11. Validar estruturalmente os dados
12. Detectar duplicatas internas
13. Comparar com o banco atual
14. Gerar relatório da importação
15. Orquestrar subagentes para explicações
16. Validar as explicações retornadas
17. Gerar dataset final separado
18. Adaptar minimamente a engine caso necessário
19. Criar a nova modalidade/esteira
20. Testar todos os tipos de questão
21. Rodar validações e testes de regressão
22. Apresentar relatório final
```

Não pule diretamente para as etapas 18 ou 19.

---

# CHECKPOINT ANTES DA INTEGRAÇÃO NO SITE

Depois de concluir extração, normalização e validação, mas **antes de integrar o dataset à interface**, apresente um checkpoint semelhante a:

```text
CHECKPOINT — IMPORTAÇÃO

PDF localizado:
Quantidade real de questões:
Questões extraídas:
Questões válidas:
Questões em revisão:
Questões com imagem:
Discursivas:
Single choice:
Multiple choice:
True/false:
Duplicatas internas:
Correspondências com banco atual:
Erros de gabarito:
Imagens sem associação confiável:
Schema final:
Local do dataset:
```

Somente após essa camada estar consistente avance para a integração.

---

# CRITÉRIO DE CONCLUSÃO

O trabalho somente será considerado concluído quando:

- todas as questões do PDF tiverem sido contabilizadas;
- todas possuírem rastreabilidade;
- gabaritos estiverem preservados;
- alternativas estiverem validadas;
- tipos estiverem corretamente classificados;
- imagens necessárias estiverem associadas;
- questões ambíguas estiverem marcadas para revisão;
- duplicatas estiverem reportadas;
- o banco atual tiver sido comparado;
- explicações tiverem sido geradas ou explicitamente marcadas como pendentes;
- o novo mock estiver separado do banco atual;
- uma nova modalidade utilizar a engine existente;
- testes de regressão tiverem sido executados;
- desktop e mobile tiverem sido verificados;
- nenhum commit/push/deploy tiver ocorrido sem autorização.

---

# RELATÓRIO FINAL

Ao terminar, entregue:

```text
1. Resumo da implementação

2. Arquitetura encontrada

3. Estratégia adotada

4. Estatísticas da extração

5. Estatísticas das explicações

6. Duplicatas encontradas

7. Comparação com o banco existente

8. Questões marcadas para revisão

9. Alterações realizadas no simulador

10. Arquivos criados/alterados/removidos

11. Testes executados e resultados

12. Riscos ou limitações restantes

13. Próximos passos recomendados
```

Inclua também uma tabela/lista específica das questões que ainda exigem revisão humana, informando exatamente o motivo.

O principal objetivo não é apenas colocar 730 perguntas no site.

O objetivo é construir uma **importação confiável, auditável, reproduzível e compatível com a arquitetura existente**, sem comprometer o simulador que já está funcionando.