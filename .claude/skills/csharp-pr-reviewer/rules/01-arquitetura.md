# Regras — Arquitetura

- O projeto segue **Clean Architecture** com as camadas: `Domain`, `Application`, `Infrastructure`, `API`.
- Dependências só podem apontar para dentro: `API → Application → Domain`. `Infrastructure` implementa interfaces definidas em `Application` ou `Domain`.
- Nenhuma camada interna (`Domain`, `Application`) pode referenciar `Infrastructure` ou `API`.
- Classes de domínio não devem ter dependências externas (sem injeção de serviços, sem referência a EF, sem HTTP).
- Nunca injetar classe concreta onde deveria ser usada uma interface — isso quebra testabilidade e o contrato de DI.
