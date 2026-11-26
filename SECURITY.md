# Segurança da Academia IA

Revisão do código-fonte: 26/11/2026.

## Arquitetura observada

A Academia IA é atualmente um site estático servido pelo Netlify. Não há aplicação própria de servidor, contas de usuário, login, painel administrativo, API própria, banco de dados, upload de arquivos ou integração com Firebase/Supabase. O progresso das aulas fica no `localStorage` do navegador e não é um registro oficial nem sincronizado. O formulário de contato é processado pelo Netlify Forms.

## Checklist de segurança (21 pontos)

| # | Controle | Situação nesta arquitetura |
|---|---|---|
| 1 | Chaves de API protegidas | Não há chamadas de API de IA no navegador nem chaves próprias. Se uma integração for criada, o segredo deve ficar no ambiente de servidor, nunca no HTML/JavaScript público. |
| 2 | Arquivo .env não exposto | `.env`, arquivos de chave e diretório de segredos foram adicionados ao `.gitignore`. O ignore não remove arquivos que já tenham sido versionados. |
| 3 | Senhas fora do código | Não há autenticação própria nem senha de usuário codificada no site atual. |
| 4 | Login verdadeiro | Não aplicável: o site não cria contas nem oferece área privada. Não foi adicionado um login falso. |
| 5 | Permissões no servidor | Não existem dados privados ou operações autenticadas na aplicação. Netlify Forms recebe o formulário de contato. Se houver área privada no futuro, a autorização deverá ser verificada no servidor. |
| 6 | Não confiar em IDs da tela | IDs de cursos e aulas apenas selecionam conteúdo público; não concedem acesso a dados privados. |
| 7 | Cada usuário vê somente os próprios dados | Não há perfis nem dados de usuário em nuvem. O progresso é local ao navegador e pode ser alterado pelo próprio visitante. |
| 8 | Banco de dados protegido | Não há banco de dados conectado. |
| 9 | Firebase, Supabase e Storage | Não estão integrados; não foram adicionados sem necessidade funcional. |
| 10 | Administração protegida | Não existe painel administrativo público ou privado no site. |
| 11 | Debug desligado | A revisão do HTML atual não encontrou instruções `debugger` nem chamadas `console.*`. |
| 12 | Erros sem detalhes internos | Não há API própria que exponha stack traces. Respostas de arquivos estáticos e formulários são tratadas pela plataforma Netlify. |
| 13 | Validação no servidor | O formulário usa validação HTML e a proteção reCAPTCHA fornecida pelo Netlify, cuja resposta é validada nos servidores do Netlify. Não há lógica de negócio própria no servidor. |
| 14 | Entrada do usuário tratada | O nome do certificado é escapado antes de ser inserido no HTML; o progresso local é filtrado para aceitar somente chaves conhecidas com valor `true`. Os campos de contato têm tipo, obrigatoriedade e limites de tamanho. |
| 15 | Upload protegido | Não há funcionalidade de upload. |
| 16 | Injeção SQL | Não há SQL nem banco de dados na aplicação atual. |
| 17 | Tentativas e spam | O formulário tem filtro antispam do Netlify, campo honeypot e reCAPTCHA 2. Não há endpoint próprio com limite de requisições por IP/usuário; essa proteção seria necessária se surgisse uma API. |
| 18 | Git sem senhas vazadas | `.gitignore` protege novos arquivos locais de segredo. A busca do código atual não encontrou padrões comuns de chaves conhecidas. Os dois arquivos ZIP de versões antigas no repositório não foram inspecionados como arquivos binários nesta revisão; devem ser verificados antes de serem mantidos publicamente. |
| 19 | Headers e CORS | Foram adicionados cabeçalhos HTTP de segurança e uma CSP compatível com o JavaScript inline atual e com o reCAPTCHA. Não foi liberado CORS amplo; como não há API pública própria, não há motivo para `Access-Control-Allow-Origin: *`. |
| 20 | Teste como visitante externo | A revisão cobre o código e a configuração do repositório. O teste final dos cabeçalhos e do formulário no site publicado depende de um deploy concluído. |
| 21 | Auditoria com IA | Foi feita revisão estática assistida por IA. Isto não substitui pentest independente, análise de histórico completo do Git ou auditoria certificada. |

## Regras para funcionalidades futuras

- **Login/contas:** usar um provedor de autenticação real e validar permissões no servidor em cada operação.
- **APIs:** guardar credenciais em variáveis de ambiente secretas do Netlify, com escopo mínimo e acesso apenas por Functions/servidor.
- **Banco:** aplicar políticas de acesso por linha (RLS), consultas parametrizadas, menor privilégio e backups.
- **Uploads:** validar tamanho, tipo real do arquivo, extensão, nome, autorização e armazenamento; nunca confiar apenas no nome ou MIME enviado pelo navegador.
- **Rate limit:** implementar limites por IP/conta e proteção contra abuso no endpoint de servidor.
- **CORS:** liberar somente origens específicas quando uma API própria realmente precisar ser chamada de outro domínio.

Este documento descreve os controles presentes no código revisado, não uma garantia de segurança absoluta.
