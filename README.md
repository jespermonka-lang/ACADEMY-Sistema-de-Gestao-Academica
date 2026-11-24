# ACADEMY — Sistema de Gestão Acadêmica

Projeto acadêmico em C, MySQL e GTK 3. Versão revisada para demonstração local, não destinada a produção. Mantém o terminal e a interface gráfica de administração.

## Instalação (Ubuntu/Debian)

```sh
sudo apt install build-essential default-libmysqlclient-dev libgtk-3-dev libssl-dev mysql-server pkg-config
sudo mysql < ACADEMY.sql
```

Use um banco novo: o script não apaga nem migra uma instalação existente. Faça backup se já houver um banco ACADEMY. Requer MySQL 8.0.16+.

Crie uma conta local de banco com senha própria (substitua o texto abaixo):

```sql
CREATE USER 'academy_app'@'localhost' IDENTIFIED BY 'SUBSTITUA_POR_SENHA_FORTE';
GRANT SELECT, INSERT, UPDATE, DELETE ON ACADEMY.* TO 'academy_app'@'localhost';
```

Execute no terminal:

```sh
export ACADEMY_DB_USER=academy_app
read -rs -p 'Senha MySQL: ' ACADEMY_DB_PASSWORD; echo
export ACADEMY_DB_PASSWORD
make
make gui
make test
./sistema
# ou, em ambiente gráfico:
./interface_grafic
```

Conta acadêmica de demonstração: `admin@academy.br`, senha `AcademyDemo2025!`. É uma credencial pública de teste, não reutilize em produção. As demais contas de exemplo têm hashes de senhas aleatórias. As duas interfaces são administrativas; não há portal individual para aluno ou professor. A senha digitada no terminal acadêmico fica visível. A senha do banco não está no código. Se a senha antiga era real, altere-a no servidor.

## Alterações

- Consultas de cursos e médias alinhadas ao esquema; erros SQL não anunciam sucesso.
- Entradas do terminal limitadas e verificadas; literais SQL protegidos contra injeção.
- Senhas acadêmicas com PBKDF2-HMAC-SHA256, salt aleatório e 600 mil iterações.
- Login administrativo em ambas as interfaces.
- GTK: callbacks, colunas, formulários de inclusão, confirmação de exclusão e tratamento de erros.
- SQL: correção do trigger de matrícula, validação de papéis e notas, unicidade de notas e limites de turma. Não promete notificação em matrícula rejeitada (a transação seria revertida).
- Arquivamento manual pelo menu do terminal; exemplos SQL separados da instalação.

## Validação e limitações

Compilação do terminal com GCC e MariaDB Connector/C, OpenSSL, `-Wall -Wextra -Werror`: aprovada. Testes locais de hash, senha incorreta, salt e literal SQL: aprovados. Não havia servidor MySQL nem bibliotecas de desenvolvimento GTK 3 neste ambiente; importação SQL, compilação gráfica e testes de integração não foram executados. Antes de considerar a versão estável, execute os passos abaixo em uma máquina com as dependências.

1. Importe o SQL em banco novo e compile ambos os executáveis.
2. Teste login válido e inválido, inclusive usuário sem papel administrativo.
3. Cadastre curso, professor, aluno, turma, matrícula, material, atividade e nota; confira os dados nas duas interfaces.
4. Teste matrícula duplicada, turma cheia, nota fora do limite e nota de aluno de outra turma.
5. Abra e feche as sete telas GTK, teste exclusões e confira as médias por turma.

O histórico é atualizado na inserção de notas conforme o modelo original; alterações/exclusões posteriores podem exigir recálculo. Notificações e prioridade permanecem estruturas do banco, sem fluxo completo na aplicação. Não é uma aplicação multiusuário de produção.
