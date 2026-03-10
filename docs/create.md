# postman-newman

Para implementar o **Newman** (CLI do Postman) e automatizar testes de API em **CI/CD pipelines**, você pode seguir os passos abaixo. O processo envolve a instalação do **Newman**, a criação de testes no **Postman**, e a integração com ferramentas de **CI/CD** como **Jenkins**, **GitLab CI**, **Travis CI** ou **GitHub Actions**.

Aqui está um passo a passo detalhado para a implementação:

### **Passo 1: Instalar o Newman**

Antes de usar o Newman, você precisa instalar o Node.js e o Newman.

1. **Instale o Node.js:**
   A primeira coisa que você precisa ter é o **Node.js** instalado no seu sistema, pois o Newman é uma ferramenta baseada em Node.js.
   - Baixe e instale o Node.js a partir de [aqui](https://nodejs.org/).

2. **Instale o Newman:**
   Após a instalação do Node.js, você pode instalar o Newman globalmente no seu sistema usando o npm (Node Package Manager).
   Execute o seguinte comando no terminal:

   ```bash
   npm install -g newman
   ```

   Isso irá instalar o Newman globalmente e torná-lo acessível de qualquer lugar no seu sistema.

### **Passo 2: Exportar Coleção e Ambiente do Postman**

Antes de rodar os testes com o Newman, você precisa ter uma coleção do Postman e, se necessário, um ambiente.

1. **Exportar a Coleção do Postman**:
   - Abra o **Postman**.
   - Selecione a coleção que você deseja automatizar.
   - Clique com o botão direito na coleção e selecione "Exportar".
   - Escolha o formato **Collection v2.1** e salve o arquivo JSON.

2. **Exportar o Ambiente do Postman (se necessário)**:
   Se você estiver usando variáveis de ambiente no Postman, também precisará exportar o ambiente:
   - No Postman, clique na aba **Ambientes**.
   - Selecione o ambiente que você deseja usar.
   - Clique no ícone de **configuração** e depois em "Exportar".
   - Salve o arquivo JSON do ambiente.

Agora você tem a **coleção** e o **ambiente** exportados, que serão usados no Newman para rodar os testes.

### **Passo 3: Rodar o Teste com Newman**

Agora que você tem o **Newman** instalado e os arquivos exportados, você pode executar a coleção de testes diretamente no terminal.

1. **Executar um Teste com o Newman:**

   Se você tiver a coleção exportada como `minha-colecao.postman_collection.json`, o comando básico para rodar o teste é:

   ```bash
   newman run minha-colecao.postman_collection.json
   ```

2. **Rodar com Ambiente (opcional):**

   Se você também exportou um ambiente (por exemplo, `meu-ambiente.postman_environment.json`), você pode especificá-lo da seguinte forma:

   ```bash
   newman run minha-colecao.postman_collection.json -e meu-ambiente.postman_environment.json
   ```

   Isso permitirá que você use as variáveis definidas no ambiente.

3. **Gerar Relatórios (opcional):**

   Você pode gerar relatórios em diversos formatos. Por exemplo, para gerar um relatório em formato **HTML**:

   ```bash
   newman run minha-colecao.postman_collection.json -r html
   ```

   Você pode adicionar outros formatos de relatório, como **JSON** ou **JUnit**, dependendo das suas necessidades.

### **Passo 4: Integração com CI/CD (Jenkins, GitLab CI, Travis CI)**

Agora que o Newman está funcionando corretamente no seu sistema local, você pode integrá-lo ao seu pipeline de **CI/CD** para automação.

#### **Exemplo de integração com Jenkins:**

1. **Instale o Jenkins** se ainda não tiver instalado, e configure um projeto de pipeline.

2. **Adicione o Newman ao Jenkins**:
   No Jenkins, você pode configurar um pipeline usando o **Jenkinsfile**. Este arquivo irá definir as etapas de seu pipeline, incluindo a execução dos testes com o Newman.

   Aqui está um exemplo básico de **Jenkinsfile** para rodar testes com o Newman:

   ```groovy
   pipeline {
       agent any

       stages {
           stage('Instalar Dependências') {
               steps {
                   script {
                       // Instala o Node.js e Newman (caso não tenha instalado no Jenkins)
                       sh 'npm install -g newman'
                   }
               }
           }

           stage('Rodar Testes API') {
               steps {
                   script {
                       // Executa os testes usando o Newman
                       sh 'newman run minha-colecao.postman_collection.json -e meu-ambiente.postman_environment.json -r html'
                   }
               }
           }

           stage('Arquivar Relatórios') {
               steps {
                   // Arquiva os relatórios gerados pelos testes
                   archiveArtifacts artifacts: '**/newman/*.html', allowEmptyArchive: true
               }
           }
       }
   }
   ```

3. **Configuração do Jenkins:**
   - Crie um novo projeto **Pipeline** no Jenkins.
   - No campo "Pipeline Script", cole o código do **Jenkinsfile**.
   - Execute o pipeline e o Jenkins irá instalar o Newman e rodar os testes da coleção do Postman conforme configurado.

#### **Exemplo de integração com GitLab CI:**

No **GitLab CI**, você pode criar um arquivo `.gitlab-ci.yml` para rodar o Newman.

```yaml
stages:
  - test

newman_test:
  image: node:16
  stage: test
  script:
    - npm install -g newman
    - newman run minha-colecao.postman_collection.json -e meu-ambiente.postman_environment.json -r html
  artifacts:
    paths:
      - newman/*.html
```

Isso vai rodar o Newman dentro do Docker com a imagem **node:16**, instalar o Newman e executar os testes de API. Os relatórios gerados serão salvos como artefatos do pipeline.

#### **Exemplo de integração com GitHub Actions:**

1. **Crie um arquivo de workflow** `.github/workflows/test.yml` no seu repositório GitHub.

```yaml
name: API Tests

on:
  push:
    branches:
      - main

jobs:
  newman_tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'

      - name: Install Newman
        run: npm install -g newman

      - name: Run Postman Collection Tests
        run: newman run minha-colecao.postman_collection.json -e meu-ambiente.postman_environment.json -r html

      - name: Upload Reports
        uses: actions/upload-artifact@v2
        with:
          name: newman-reports
          path: newman/*.html
```

2. **Configure a execução do workflow:**
   - Quando houver um **push** para o **branch main**, o GitHub Actions irá rodar os testes com o Newman.

### **Conclusão:**
Integrando o Newman com ferramentas de CI/CD como Jenkins, GitLab CI ou GitHub Actions, você consegue rodar os testes de API automaticamente durante o ciclo de vida do desenvolvimento, garantindo a qualidade contínua das APIs de forma eficiente e sem erros manuais.