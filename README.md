# 🚛 Gerador de Rotograma 

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

> Criei esse projeto para resolver um B.O diário na operação logística: a galera perdia muito tempo digitando as mesmas rotas e paradas na mão para gerar o documento de viagem.
>
> A ideia foi montar uma SPA (Single Page Application) bem leve, que rodasse 100% no navegador (offline-first) e que fosse aprendendo o histórico de digitação para preencher tudo sozinho nas próximas vezes.

---

## ⚙️ Como a engrenagem funciona (Vanilla JS "Na Unha")

Fiz o projeto inteiro em HTML, CSS e Vanilla JS, sem framework. O motivo? Precisava rodar liso em qualquer PC da operação sem depender de internet, servidor ou processo de build.

Algumas soluções que implementei:

* 🔌 **O problema do Estado vs DOM:** Quem mexe com Vanilla sabe a dor de cabeça que é manter o estado sincronizado com a tela. Eu tava tendo uns bugs (principalmente no Autocomplete) onde os dados sumiam. A sacada foi criar uma função `Form.sync()`. Agora, milissegundos antes de gerar o PDF ou manipular qualquer nó no DOM (adicionar/remover linhas), o código varre os inputs da tela e força a atualização do array de estado. O DOM virou a única fonte da verdade.

* 🔎 **Busca Fuzzy Customizada:** O pessoal digita nome de posto de todo jeito. Então criei um motor de busca na mão que desconsidera erro de digitação. Ele não busca só a string exata, o algoritmo dá um score de proximidade e ainda usa um sistema de pesos (`freqMap`) pra jogar os motoristas e postos mais acessados pro topo da lista. Meti um debounce no input pra não engasgar a thread principal enquanto a usuário digita.

* ♻️ **Banco Local e "Lixeiro" Automático:** Tô usando o `localStorage` como banco JSON. Mas pra evitar lixo (como duplicidades por causa de espaço invisível no final da palavra), criei um método `DB.migrate()` que dispara no `window.onload`. Ele roda um regex nas chaves, limpa tudo e unifica os cadastros de forma silenciosa.

* 📄 **Desenhando PDF na unha:** Uso o `jsPDF`, mas não é um simples print da tela. O JS pega o objeto final (`collectAll`) e desenha os blocos e textos usando coordenadas exatas no canvas. Imagens (como logo e mapas) já são convertidas em Base64 no upload pra não dar pau na hora de imprimir.

---

## 🚀 Plano pra V2: Levando pra Web

Hoje a ferramenta funciona local, mas o banco fica preso na máquina da pessoa. O próximo passo da arquitetura é transformar isso num Web App de verdade, com os dados centralizados na nuvem.

Como pode acontecer esta migração:

* 🗄️ **Back-end (API REST):** Vou subir uma API (pensando em ir de Node.js + Express ou Python). A ideia é tirar a lógica pesada do front e deixar o back-end cuidando de receber os payloads e fazer as queries.
* 💾 **Banco de Dados de Verdade:** Sair do localStorage e plugar num PostgreSQL ou MySQL. Quero normalizar os dados, criando as tabelas estruturadas (ex: Tabela de Motoristas relacionando 1:1 ou 1:N com Placas, tabelas de Postos, etc.).
* 💻 **Front-end:** Refatorar a camada de dados pra começar a bater nos endpoints da nova API usando Fetch ou Axios. Talvez role uma migração pra React pra facilitar a componentização.

---

## 🧪 Automação E2E (Meu lab de QA)

![Cypress](https://img.shields.io/badge/Cypress-17202C?style=for-the-badge&logo=cypress&logoColor=white)

Como estou no processo de transição da minha carreira para QA / Automação, esse projeto virou meu laboratório perfeito. Mexer com manipulação de DOM bruto costuma dar margem pra regressão, então vou plugar o Cypress no repositório.

Os próximos specs que vou escrever vão cobrir:

- [ ] Simular a digitação e os atalhos de teclado (setas/enter) no dropdown da busca Fuzzy pra garantir que não rola re-render infinito (um bug que já apanhei pra corrigir).
- [ ] Criar objetos simulados chamando o jsPDF pra validar se o payload sendo enviado pra impressão para verificar se tem a estrutura de dados certinha.
- [ ] Adicionar e apagar linhas dinâmicas de entregas/paradas e validar se os values dos outros inputs continuam intactos.