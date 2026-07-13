5. Documentar o funcionamento da pipeline neste README

## O que implementar
- [x] Secrets Scanning com **Gitleaks**
- [x] SAST com **Semgrep**
- [x] SCA com **Grype**
- [x] Deploy com **GitHub Pages**

## Como a pipeline funciona

A esteira de DevSecOps automatizada é executada a cada push na branch `main`. Ela garante que nenhuma vulnerabilidade seja implantada em produção através do conceito de **"Break the Build"** (se qualquer gate de segurança falhar, o deploy é cancelado).

Abaixo está o detalhamento de cada step:

1. **📥 Checkout do Código**:
   - **O que faz**: Baixa o código-fonte do repositório para o runner (máquina virtual) do GitHub Actions.
   - **Importância**: Necessário para que todas as ferramentas subsequentes possam ler os arquivos do projeto.

2. **⚙️ Build**:
   - **O que faz**: Faz uma verificação preliminar da integridade e da existência dos arquivos estáticos dentro do diretório `src/`.
   - **Importância**: Garante que a aplicação básica esteja estruturada e funcional antes de iniciar as análises de segurança.

3. **🔑 Secrets Scanning (Gitleaks)**:
   - **O que faz**: Analisa o código em busca de segredos expostos hardcoded (chaves de API, senhas, tokens).
   - **Importância**: Evita o vazamento de credenciais que poderiam comprometer outros sistemas. Corrigimos isso removendo constantes inseguras expostas como `API_KEY` e `DB_PASSWORD`.

4. **🔍 SAST - Semgrep (Static Application Security Testing)**:
   - **O que faz**: Analisa o código-fonte de forma estática procurando por padrões perigosos e falhas lógicas conhecidas (OWASP Top 10).
   - **Importância**: Identifica falhas no próprio código escrito. Corrigimos vulnerabilidades críticas detectadas por ele, como a injeção via `eval()` e vulnerabilidade XSS ao utilizar `innerHTML` sem sanitização, além de vazamento de informações com `err.stack`.

5. **📦 SCA - Grype (Software Composition Analysis)**:
   - **O que faz**: Analisa as dependências instaladas no projeto (no nosso caso, as bibliotecas no `package.json` como Axios, Express e Lodash) contra um banco de vulnerabilidades públicas conhecidas (CVEs).
   - **Importância**: Garante que a aplicação não utilize bibliotecas de terceiros que possuam falhas conhecidas. Corrigimos isso atualizando as dependências para versões seguras (Axios `^1.18.1`, Express `^4.22.2`, Lodash `^4.18.1`).

6. **🚀 Deploy em Produção (GitHub Pages)**:
   - **O que faz**: Configura e publica os arquivos estáticos da pasta `src/` no GitHub Pages.
   - **Importância**: Só é executado se todos os steps anteriores passarem. Garante que apenas código validado e seguro chegue ao usuário final.
