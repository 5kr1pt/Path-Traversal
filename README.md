# 🚨 Vulnerabilidade: **Path Traversal**

_A vulnerabilidade de Path Traversal, também conhecida como **Directory Traversal**, permite que um atacante acesse arquivos e diretórios restritos explorando falhas na validação dos caminhos informados pela aplicação._

---

## 📑 Sumário

- [Descrição](#descrição)
- [Impacto](#impacto)
- [Exemplos de Payloads](#exemplos-de-payloads)
- [Vetores de Ataque](#vetores-de-ataque)
- [Medidas de Mitigação](#medidas-de-mitigação)
- [Recomendações e Boas Práticas](#recomendações-e-boas-práticas)
- [Conclusão](#conclusão)
- [Referências](#referências)

---

## 📝 Descrição

A vulnerabilidade de **Path Traversal** ocorre quando a aplicação não trata de forma adequada os dados fornecidos na requisição, permitindo que sequências especiais de caracteres (por exemplo, `../` ou `..\`) sejam utilizadas para navegar na estrutura de diretórios do servidor. Essa falha pode resultar no acesso não autorizado a arquivos sensíveis como configurações, senhas e logs, além de possibilitar a execução de ações maliciosas.

> **Nota:** Esse tipo de vulnerabilidade é comum em sistemas que não impõem uma correta sanitização e validação de entradas fornecidas pelo usuário.

---

## ⚠️ Impacto

Os principais impactos de uma exploração bem-sucedida incluem:

- **Exfiltração de Dados:** Acesso a arquivos confidenciais, como `/etc/passwd`, `/etc/issue`, `/Windows/win.ini` ou outros arquivos de configuração do Windows e Linux.
- **Escalada de Privilégios:** Informações obtidas podem ser usadas para ampliar o acesso e executar tarefas com privilégios mais elevados.
- **Comprometimento Total do Sistema:** Possibilidade de manipulação de arquivos críticos, execução de comandos arbitrários ou mesmo a instalação de backdoors.

---

## 💻 Exemplos de Payloads

Utilize os seguintes payloads para testar a vulnerabilidade em ambientes de desenvolvimento ou durante a realização de testes de penetração:

```plaintext
..\..\..\..\..\..\etc\passwd
..\..\..\..\..\..\etc\issue
..\..\..\..\..\..\boot.ini
..\..\..\..\..\..\windows\system32\drivers\etc\hosts
```
## 🦠 Técnicas de Codificação para Exploração

Em cenários onde se busca explorar vulnerabilidades, é comum o uso de diversas formas de codificação para ofuscar a intenção maliciosa e contornar os filtros de segurança. A seguir, abordamos algumas técnicas relevantes que podem ser utilizadas nesse contexto:

### Codificação Percentual

A codificação percentual converte caracteres especiais em seus equivalentes hexadecimais, possibilitando a representação indireta de símbolos críticos. Por exemplo, caracteres que indicam separadores ou pontos podem ser alterados para suas formas codificadas, confundindo os mecanismos de validação ao interpretar os dados.

`..%c0%af` representa `../`

### Codificação Dupla

Uma abordagem sofisticada envolve a aplicação de codificação percentual duas vezes. Muitas aplicações realizam apenas uma decodificação inicial, o que pode deixar a entrada parcialmente codificada. Com isso, uma sequência de caracteres pode ser submetida a duas passagens de decodificação, retornando ao seu estado original apenas no estágio final, o que permite a injeção de direções de diretórios e outros comandos sem serem detectados durante as verificações iniciais.

`%2e%2e%2f` representa `../`

### Considerações em Diferentes Sistemas Operacionais

A interpretação dos caminhos de diretórios varia conforme o sistema operacional:

- **Sistemas UNIX-like:**  
  O diretório raiz é representado por “/” e os separadores também utilizam essa barra, o que padroniza a forma como os caminhos são tratados.

- **Sistemas Windows:**  
  O ambiente Windows utiliza uma estrutura diferente, onde o diretório raiz é definido por uma letra de unidade seguida de dois pontos e uma barra invertida (por exemplo, `C:\`). Além disso, o Windows geralmente aceita tanto a barra quanto a barra invertida como separadores, proporcionando mais flexibilidade – e, consequentemente, aumentando as nuances na validação.

### Técnicas Complementares

- **Inserção de Caractere Nulo:**  
  Alguns sistemas interpretam um caractere nulo (representado por `%00` na codificação) como o fim de uma string, o que pode ser explorado para truncar a extensão de um arquivo ou bypassar determinadas checagens de entrada.

  `../../../../../../etc/passwd%00`

- **Variações na Representação:**  
  Alterar a forma como os códigos hexadecimais são representados (como a alternância entre letras maiúsculas e minúsculas) pode afetar a decodificação realizada pela aplicação ou pelo servidor, possibilitando a evasão de filtros que não consideram essas diferenças.

---

## 🕵️ Vetores de Ataque

Os principais caminhos explorados pelos atacantes são:

- **Entrada não Sanitizada:**  
  Falha na validação dos dados de entrada que podem conter sequências de fuga, como `../` ou `..\`.
- **Endpoints Vulneráveis:**  
  APIs ou pontos de acesso onde os arquivos são lidos sem restrição.
- **Configurações Inseguras:**  
  Permissões inadequadas nos sistemas, que permitem maior acesso que o necessário.

---

## 🔐 Medidas de Mitigação

Para prevenir e mitigar essa vulnerabilidade, adote as seguintes práticas:

### Validação e Sanitização de Entradas
- Remova ou neutralize sequências que indiquem tentativas de navegação fora do diretório esperado.
- Utilize funções nativas para normalizar e validar caminhos.

### Uso de Listas Brancas (Whitelisting)
- Permita acesso apenas a arquivos ou diretórios previamente autorizados e necessários para a operação da aplicação.

### Controle de Acesso e Permissões
- Garanta que o sistema opere com o princípio de privilégio mínimo, restringindo o acesso a arquivos sensíveis.

### Monitoramento e Logging
- Registre todas as tentativas de acesso suspeitas para análise e resposta rápida a possíveis incidentes.

### Atualizações e Patches
- Mantenha o sistema e as bibliotecas utilizadas sempre atualizados para evitar a exploração de falhas conhecidas.

---

## 💡 Recomendações e Boas Práticas

### Realização de Testes de Segurança
- Conduza testes de penetração regulares e utilize ferramentas automatizadas para detectar vulnerabilidades.

### Capacitação da Equipe
- Treine os desenvolvedores para reconhecer e evitar práticas de codificação inseguras.

### Uso de Frameworks Seguros e "containernização" da aplicação
- Opte por frameworks e bibliotecas que já incorporam mecanismos de proteção contra injeção de diretórios e isole aplicação para evitar escape.

---

## 🏁 Conclusão

A exploração de vulnerabilidades de Path Traversal pode levar ao comprometimento total do sistema, expondo dados sensíveis e facilitando ataques subsequentes. A adoção de práticas robustas de validação de entrada, combinada com uma política de acesso rigorosa, é essencial para mitigar esse risco. Mantenha sua equipe informada e invista em testes regulares de segurança para proteger sua aplicação contra ameaças.

---

## 📚 Referências

- [OWASP - Directory Traversal](https://owasp.org/www-community/attacks/Path_Traversal)
- [NIST - Guidelines on Securing Web Applications](https://www.nist.gov/)
