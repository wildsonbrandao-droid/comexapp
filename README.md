ComexApp — Plataforma de Comércio Exterior
Aplicação web para apoio a processos de importação e exportação de empresas brasileiras.
Funcionalidades
Cálculo tributário — II, IPI, PIS, COFINS e ICMS encadeados com câmbio Bacen PTAX automático
IPI com dados reais da TIPI — Alíquota de IPI consultada na tabela oficial da Receita Federal (10.521 NCMs, com descrições completas e exceções tarifárias/Ex-tarifário quando existentes). Dados embutidos diretamente no arquivo — funcionam mesmo abrindo `index.html` direto, sem servidor. II, PIS, COFINS e ICMS permanecem estimados (TEC/CONFAZ ainda sem fonte estruturada gratuita)
Busca de NCM por nome coloquial — dicionário de sinônimos conecta termos do dia a dia (ex: "computador", "tênis", "geladeira") à nomenclatura técnica oficial usada na TIPI
Desembaraço aduaneiro — Checklist dinâmico de documentos e simulador de canal de conferência
Incoterms 2020 — Sequência completa de obrigações (importação e exportação) para os 11 termos
Classificação NCM — Busca por descrição com alíquotas TIPI e alertas de licenciamento
Documentação — Gerador de invoice, packing list, certificado de origem, DUIMP (roteiro de Catálogo de Produtos), RE e LI com badges de validação
Custo logístico — AFRMM e Taxa de Utilização do Mercante calculados com exatidão (Lei nº 10.893/2004, alíquotas confirmadas na página vigente da Receita Federal), ICMS de referência sobre frete, e faixas históricas de mercado para frete internacional — sem fingir cotação em tempo real, já que essa informação não tem fonte gratuita estruturada
Financiamento — Simulador de elegibilidade a partir do perfil da empresa (faturamento, CNAE, % afetado por tarifa), avaliando BNDES Brasil Soberano Competitividade, PROEX e Pronampe com critérios públicos reais, além de listar ACC, ACE, FINIMP e NCE — instrumentos de mercado sempre disponíveis via bancos comerciais, independentes de programa emergencial
Inteligência de mercado — Consulta em tempo real à API pública do Comex Stat (MDIC), mostrando os principais países compradores ou fornecedores de um NCM, com valores oficiais em USD FOB. Chamada feita direto do navegador do usuário, com tratamento de erro explícito caso a API externa esteja indisponível
Dashboard — Carteira consolidada calculada 100% a partir das transações cadastradas (sem dados de exemplo): KPIs de importação/exportação, exposição por país, margem por operação (a partir de custo total e preço de venda informados em cada transação) e exposição cambial estimada das operações em aberto, na cotação Bacen PTAX do dia
Assistente IA — Chat especializado em comércio exterior brasileiro (NCM, Incoterms, tributos, documentos, desembaraço, drawback/ex-tarifário), usando a API da Anthropic (Claude Sonnet 4.6). Dentro do Claude.ai funciona automaticamente; fora dele (ex: GitHub Pages), exige configurar um proxy próprio — veja `Guia_Assistente_IA_Producao.md` e `comexapp-ia-proxy-worker.js` neste pacote.
Incentivos fiscais — Simulador de elegibilidade a Drawback (suspensão/isenção) e Ex-Tarifário a partir de critérios públicos (gov.br/siscomex, resoluções Gecex/CAMEX), com aviso sobre a transição regulatória de 2026/2027 ligada à Reforma Tributária (Decreto nº 12.955/2026)
Câmbio automático — USD e EUR via API pública Bacen PTAX, atualização a cada 5 minutos
Rastreio de transações — Linha do tempo por operação na tela inicial
Sistema integrado — Abas por transação; todos os módulos sincronizam ao trocar de transação
Minha empresa — Upload de logo, dados cadastrais aplicados em todos os documentos
Tour guiado — Passo a passo interativo para novos usuários
Tecnologia
HTML5 + CSS3 + JavaScript puro (sem frameworks)
Fontes: IBM Plex Sans + JetBrains Mono (Google Fonts)
Ícones: Tabler Icons
API de câmbio: Bacen PTAX (pública, sem autenticação)
Dados tributários: tabela TIPI completa (10.521 NCMs) embutida diretamente no `index.html` como constante JavaScript — gerada a partir do arquivo oficial TIPI (Decreto nº 11.158/2022 e atos declaratórios posteriores, Receita Federal). Não depende de fetch nem de servidor para funcionar.
Como usar localmente
O app funciona abrindo `index.html` diretamente no navegador — não precisa de servidor:
```bash
git clone https://github.com/SEU_USUARIO/comexapp.git
cd comexapp
open index.html
```
Opcionalmente, o app tenta buscar `data/tipi.json` em segundo plano (sem bloquear nada) para checar se há uma versão mais recente da tabela — isso só funciona quando servido por HTTP, mas não é necessário no dia a dia, já que os dados embutidos cobrem o uso normal.
Para servir por HTTP (e habilitar essa atualização em segundo plano):
```bash
python3 -m http.server 8000
# Acesse http://localhost:8000
```
Ou publique no GitHub Pages (abaixo), que já serve por HTTPS.
Publicar no GitHub Pages
Faça o fork ou clone este repositório
Vá em Settings → Pages
Em Source, selecione Deploy from a branch
Selecione a branch `main` e pasta `/ (root)`
Clique em Save
Aguarde ~1 minuto e acesse: `https://SEU_USUARIO.github.io/comexapp`
Estrutura do projeto
```
comexapp/
├── index.html          # Aplicação principal (single-page)
├── data/
│   └── tipi.json       # Tabela de alíquotas de IPI por NCM (fonte: Receita Federal/TIPI)
├── README.md           # Este arquivo
├── .github/
│   └── workflows/
│       └── pages.yml   # Deploy automático via GitHub Actions
└── .nojekyll            # Desativa Jekyll no GitHub Pages
```
Atualizando a tabela TIPI
A Receita Federal atualiza a TIPI periodicamente (novos Atos Declaratórios Executivos). Para atualizar `data/tipi.json`:
Baixe a versão vigente em `gov.br/receitafederal` → Legislação → TIPI (formato `.xlsx`)
Reprocesse o arquivo com o parser (mapeia colunas NCM/EX/Descrição/Alíquota e gera o JSON de produção)
Substitua `data/tipi.json` e confira a data em `data_processamento` e `ultima_atualizacao`
Aviso legal
Este aplicativo é uma ferramenta de apoio e simulação. A alíquota de IPI é obtida da tabela oficial da Receita Federal (TIPI) quando o NCM informado é localizado — quando não encontrado, ou se o arquivo de dados não carregar, o sistema usa uma estimativa de 15% e sinaliza isso visualmente. As alíquotas de II, PIS, COFINS e ICMS continuam sendo estimativas, pois a Tarifa Externa Comum (TEC) e as tabelas estaduais de ICMS (CONFAZ) não possuem fonte estruturada gratuita disponível no momento. Consulte sempre a legislação vigente e um despachante aduaneiro habilitado antes de qualquer decisão comercial ou fiscal.
---
Desenvolvido com ComexApp · 2026
