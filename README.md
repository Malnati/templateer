<!-- README.md -->
<h1 align="center">Malnati/templateer</h1>

<p align="center">
  <b>Engine simples para renderizar templates em GitHub Actions usando variáveis de ambiente.</b>
</p>

<p align="center">
  <a href="https://github.com/Malnati/templateer/actions/workflows/ci.yml">
    <img alt="CI" src="https://img.shields.io/github/actions/workflow/status/Malnati/templateer/ci.yml?branch=main" />
  </a>
  <a href="https://github.com/Malnati/templateer/releases">
    <img alt="Release" src="https://img.shields.io/github/v/release/Malnati/templateer?sort=semver" />
  </a>
  <a href="https://github.com/Malnati/templateer/blob/main/LICENSE">
    <img alt="License" src="https://img.shields.io/github/license/Malnati/templateer" />
  </a>
  <a href="https://github.com/Malnati/templateer/issues">
    <img alt="Issues" src="https://img.shields.io/github/issues/Malnati/templateer" />
  </a>
</p>

<hr />

<h2>O que é</h2>

<p>
  O <b>Templateer</b> é uma <i>composite action</i> para renderizar arquivos de template usando
  <code>envsubst</code> com base nas variáveis de ambiente disponíveis no step/job do GitHub Actions.
</p>

<ul>
  <li>Recebe <code>template</code> e <code>result</code></li>
  <li>Opcionalmente imprime variáveis exportadas via <code>debug=true</code></li>
  <li>Gera outputs: <code>path</code> e <code>body</code></li>
</ul>

<h2>Como funciona</h2>

<ol>
  <li>Exporta variáveis do ambiente para <code>$GITHUB_ENV</code> (exceto as que começam com <code>GITHUB_</code>)</li>
  <li>Executa <code>envsubst</code> para substituir placeholders (ex.: <code>$VAR</code>) no template</li>
  <li>Publica o caminho do arquivo e o conteúdo renderizado como outputs</li>
</ol>

<h2>Inputs</h2>

<table>
  <thead>
    <tr>
      <th>Input</th>
      <th>Obrigatório</th>
      <th>Padrão</th>
      <th>Descrição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>template</code></td>
      <td>Sim</td>
      <td>-</td>
      <td>Caminho do arquivo de template</td>
    </tr>
    <tr>
      <td><code>result</code></td>
      <td>Sim</td>
      <td>-</td>
      <td>Caminho do arquivo de saída renderizado</td>
    </tr>
    <tr>
      <td><code>debug</code></td>
      <td>Não</td>
      <td><code>false</code></td>
      <td>Se <code>true</code>, imprime as variáveis exportadas no log</td>
    </tr>
  </tbody>
</table>

<h2>Outputs</h2>

<table>
  <thead>
    <tr>
      <th>Output</th>
      <th>Descrição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>path</code></td>
      <td>Caminho do arquivo renderizado</td>
    </tr>
    <tr>
      <td><code>body</code></td>
      <td>Conteúdo renderizado (multiline)</td>
    </tr>
  </tbody>
</table>

<h2>Exemplo de uso</h2>

<h3>1) Template</h3>

<pre><code># .github/templates/report.md
Olá, $NAME.

Arquivo: $FILE_NAME
Repositório: $GITHUB_REPOSITORY
</code></pre>

<h3>2) Workflow</h3>

<pre><code># .github/workflows/example.yml
name: templateer-example

on:
  workflow_dispatch:

jobs:
  render:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Render template
        id: tpl
        uses: Malnati/templateer@v1.0.0
        env:
          NAME: Ricardo
          FILE_NAME: .reports/20251215-0100_hardcode.json
        with:
          template: .github/templates/report.md
          result: /tmp/report.rendered.md

      - name: Mostrar caminho
        shell: bash
        run: echo "${{ steps.tpl.outputs.path }}"

      - name: Mostrar conteúdo
        shell: bash
        run: |
          echo "-----"
          echo "${{ steps.tpl.outputs.body }}"
          echo "-----"
</code></pre>

<h2>Regras do template</h2>

<ul>
  <li>Use placeholders no formato <code>$VAR</code> (padrão do <code>envsubst</code>).</li>
  <li>As variáveis podem vir de <code>env:</code> do step/job, ou de steps anteriores via <code>$GITHUB_ENV</code>.</li>
</ul>

<h2>Limitações</h2>

<ul>
  <li><code>envsubst</code> substitui somente variáveis de ambiente, não executa lógica condicional.</li>
  <li>Se uma variável não existir, o placeholder pode ficar vazio no resultado.</li>
</ul>

<h2>Licença</h2>

<p>
  Consulte o arquivo <a href="https://github.com/Malnati/templateer/blob/main/LICENSE">LICENSE</a>.
</p>
