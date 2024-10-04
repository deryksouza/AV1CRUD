Descrição do Desafio
Neste desafio, você irá desenvolver uma API RESTful utilizando Node.js , Express , Sequelize (para a interação com o banco de dados MySQL) e MySQL2 . O objetivo principal é implementar um CRUD (Create, Read, Update, Delete) completo, utilizando comandos SQL nativos através das consultas do Sequelize.

Você irá configurar um banco de dados MySQL , realizar uma conexão com a API e criar rotas para cada operação CRUD.

Objetivo
Crie um backend de aplicativo em Node.js.
Utilização do Express para gerenciar as rotas.
Usar Sequelize para gerenciar o banco de dados MySQL com consultas SQL personalizadas.
Monitore mudanças no código com Nodemon .
Implementar operações CRUD (Create, Read, Update, Delete) para uma tabela do banco de dados.
Ferramentas e Tecnologias Necessárias
Node.js
Expressar
Sequenciar
MySQL2
Nodemônio
Requisitos
Conectar a API ao banco de dados MySQL .
Crie uma tabela chamada userscom os seguintes campos:
id: Chave primária, auto incrementada.
name: Nome do usuário (string).
email: E-mail do usuário (string, único).
age: Idade do usuário (inteiro).
Implemente as seguintes rotas:
POST/users : Criar um novo usuário.
GET/users : Listar todos os usuários.
GET/users/:id : Obtenha um único usuário id.
PUT/users/:id : Atualizar um usuário por id.
DELETE/users/:id : Excluir um usuário por id.
Utilização de comandos SQL nativos para realizar as consultas, em vez dos métodos padrões do Sequelize.
Instruções
1. Configuração do Ambiente
Instale o Node.js e configure seu ambiente de desenvolvimento.

Sem terminal, crie um novo projeto Node.js:

mkdir desafio-crud-nodejs
cd desafio-crud-nodejs
npm init -y
Instale as dependências necessárias:

npm install express sequelize mysql2 nodemon
Configure o script do Nodemon no arquivo package.jsonpara reiniciar o servidor automaticamente ao detectar alterações no código:

"scripts": {
  "start": "nodemon src/app.js"
}
2. Configuração do Banco de Dados MySQL
Crie um banco de dados no MySQL chamado crud_db.

CREATE DATABASE crud_db;
Crie uma tabela usersno banco de dados:

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  age INT NOT NULL
);
3. Configuração do Sequelize
No diretório do projeto, crie uma pasta srce dentro dela crie os seguintes arquivos:
config/database.jspara a configuração do banco de dados.
models/User.jspara o modelo do usuário.
// src/config/database.js
const { Sequelize } = require('sequelize');

const sequelize = new Sequelize('crud_db', 'root', 'sua_senha', {
  host: 'localhost',
  dialect: 'mysql',
});

module.exports = sequelize;
// src/models/User.js
const { DataTypes } = require('sequelize');
const sequelize = require('../config/database');

const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  email: {
    type: DataTypes.STRING,
    unique: true,
    allowNull: false,
  },
  age: {
    type: DataTypes.INTEGER,
    allowNull: false,
  },
}, {
  timestamps: false,
  tableName: 'users'
});

module.exports = User;
4. Implementação das Rotas
Crie o arquivo src/routes/userRoutes.jspara definir as rotas do CRUD:
const express = require('express');
const router = express.Router();
const User = require('../models/User');

// CREATE: Rota para adicionar um novo usuário
router.post('/users', async (req, res) => {
  const { name, email, age } = req.body;
  try {
    const newUser = await User.sequelize.query(
      'INSERT INTO users (name, email, age) VALUES (?, ?, ?)',
      { replacements: [name, email, age], type: User.sequelize.QueryTypes.INSERT }
    );
    res.status(201).json({ message: 'Usuário criado com sucesso!' });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// READ: Rota para listar todos os usuários
router.get('/users', async (req, res) => {
  try {
    const users = await User.sequelize.query(
      'SELECT * FROM users',
      { type: User.sequelize.QueryTypes.SELECT }
    );
    res.json(users);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// READ: Rota para obter um único usuário pelo ID
router.get('/users/:id', async (req, res) => {
  const { id } = req.params;
  try {
    const user = await User.sequelize.query(
      'SELECT * FROM users WHERE id = ?',
      { replacements: [id], type: User.sequelize.QueryTypes.SELECT }
    );
    if (user.length === 0) return res.status(404).json({ error: 'Usuário não encontrado' });
    res.json(user[0]);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// UPDATE: Rota para atualizar um usuário
router.put('/users/:id', async (req, res) => {
  const { id } = req.params;
  const { name, email, age } = req.body;
  try {
    await User.sequelize.query(
      'UPDATE users SET name = ?, email = ?, age = ? WHERE id = ?',
      { replacements: [name, email, age, id], type: User.sequelize.QueryTypes.UPDATE }
    );
    res.json({ message: 'Usuário atualizado com sucesso!' });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// DELETE: Rota para excluir um usuário
router.delete('/users/:id', async (req, res) => {
  const { id } = req.params;
  try {
    await User.sequelize.query(
      'DELETE FROM users WHERE id = ?',
      { replacements: [id], type: User.sequelize.QueryTypes.DELETE }
    );
    res.json({ message: 'Usuário excluído com sucesso!' });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

module.exports = router;
5. Configuração do Servidor
Crie o arquivo src/app.jspara iniciar o servidor:
const express = require('express');
const sequelize = require('./config/database');
const userRoutes = require('./routes/userRoutes');

const app = express();
const port = 3000;

app.use(express.json());
app.use(userRoutes);

// Teste a conexão com o banco de dados
sequelize.authenticate()
  .then(() => console.log('Conectado ao banco de dados!'))
  .catch(err => console.error('Não foi possível conectar ao banco de dados:', err));

app.listen(port, () => {
  console.log(`Servidor rodando na porta ${port}`);
});
6. Executar o Projeto
Execute a aplicação com o comando:
npm start
Teste as rotas utilizando ferramentas como Postman ou Insomnia .
Entregáveis
Código do projeto funcionando conforme o desafio proposto.
O projeto deve ser versionado em um repositório no GitHub .
Dicas
Verifique a documentação do Sequelize para realizar consultas SQL personalizadas.
Use o Postman para testar as rotas.
Mantenha boas práticas de manipulação, como validação de dados e tratamento de erros.
Boa sorte!
