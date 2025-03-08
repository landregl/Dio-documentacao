


# Sistema Experimental de Cadastro de Clientes para Farmácia

Este é um sistema de cadastro de clientes para uma farmácia, desenvolvido com Python e Flask. O sistema permite cadastrar, visualizar e gerenciar informações dos clientes.

## Funcionalidades

- Cadastro de clientes com validação de dados
- Armazenamento de dados em banco de dados SQLite
- Interface web para cadastro e visualização de clientes
- Mensagens de erro e sucesso para feedback ao usuário
- Estilização básica com CSS

## Estrutura de Pastas

```
farmacia_cadastro/
│
├── templates/
│   ├── base.html
│   ├── index.html
│   └── cadastro.html
│
├── static/
│   └── style.css
│
├── app.py
└── clientes.db
```

## Requisitos

- Python 3.x
- Flask
- Flask-SQLAlchemy

## Instalação

1. **Instale o Python**:
   - Baixe e instale o Python a partir do site oficial.

2. **Crie um ambiente virtual** (opcional, mas recomendado):
   ```bash
   python -m venv venv
   source venv/bin/activate  # No Windows, use `venv\Scripts\activate`
   ```

3. **Instale as dependências**:
   ```bash
   pip install flask flask_sqlalchemy
   ```

4. **Execute o aplicativo**:
   ```bash
   python app.py
   ```

5. **Acesse a aplicação**:
   Abra um navegador web e vá para `http://127.0.0.1:5000/`.

## Estrutura do Código

### `app.py`

```python
from flask import Flask, render_template, request, redirect, url_for, flash
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SECRET_KEY'] = 'sua_chave_secreta'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///clientes.db'
db = SQLAlchemy(app)

class Cliente(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    cpf = db.Column(db.String(11), unique=True, nullable=False)
    nome = db.Column(db.String(100), nullable=False)
    endereco = db.Column(db.String(200), nullable=False)
    cidade = db.Column(db.String(100), nullable=False)
    bairro = db.Column(db.String(100), nullable=False)
    cep = db.Column(db.String(8), nullable=False)
    genero = db.Column(db.String(10), nullable=False)
    data_nascimento = db.Column(db.String(10), nullable=False)
    telefone = db.Column(db.String(15), nullable=False)
    email = db.Column(db.String(100), nullable=False)
    instagram = db.Column(db.String(100))

@app.route('/')
def index():
    clientes = Cliente.query.all()
    return render_template('index.html', clientes=clientes)

@app.route('/cadastro', methods=['GET', 'POST'])
def cadastro():
    if request.method == 'POST':
        cpf = request.form['cpf']
        nome = request.form['nome']
        endereco = request.form['endereco']
        cidade = request.form['cidade']
        bairro = request.form['bairro']
        cep = request.form['cep']
        genero = request.form['genero']
        data_nascimento = request.form['data_nascimento']
        telefone = request.form['telefone']
        email = request.form['email']
        instagram = request.form['instagram']

        if not cpf or not nome ou not endereco ou not cidade ou not bairro ou not cep ou not genero ou not data_nascimento ou not telefone ou not email:
            flash('Por favor, preencha todos os campos obrigatórios!', 'error')
        else:
            novo_cliente = Cliente(cpf=cpf, nome=nome, endereco=endereco, cidade=cidade, bairro=bairro, cep=cep, genero=genero, data_nascimento=data_nascimento, telefone=telefone, email=email, instagram=instagram)
            db.session.add(novo_cliente)
            db.session.commit()
            flash('Cliente cadastrado com sucesso!', 'success')
            return redirect(url_for('index'))
    return render_template('cadastro.html')

if __name__ == '__main__':
    db.create_all()
    app.run(debug=True)
```

### `templates/base.html`

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <div class="container">
        {% with messages = get_flashed_messages(with_categories=true) %}
            {% if messages %}
                {% for category, message in messages %}
                    <div class="flash {{ category }}">{{ message }}</div>
                {% endfor %}
            {% endif %}
        {% endwith %}
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

### `templates/cadastro.html`

```html
{% extends "base.html" %}
{% block title %}Cadastro de Cliente{% endblock %}
{% block content %}
<h1>Cadastro de Cliente</h1>
<form action="/cadastro" method="post">
    <label for="cpf">CPF:</label>
    <input type="text" id="cpf" name="cpf" required><br>

    <label for="nome">Nome:</label>
    <input type="text" id="nome" name="nome" required><br>

    <label for="endereco">Endereço:</label>
    <input type="text" id="endereco" name="endereco" required><br>

    <label for="cidade">Cidade:</label>
    <input type="text" id="cidade" name="cidade" required><br>

    <label for="bairro">Bairro:</label>
    <input type="text" id="bairro" name="bairro" required><br>

    <label for="cep">CEP:</label>
    <input type="text" id="cep" name="cep" required><br>

    <label for="genero">Gênero:</label>
    <input type="text" id="genero" name="genero" required><br>

    <label for="data_nascimento">Data de Nascimento:</label>
    <input type="date" id="data_nascimento" name="data_nascimento" required><br>

    <label for="telefone">Telefone:</label>
    <input type="text" id="telefone" name="telefone" required><br>

    <label for="email">E-mail:</label>
    <input type="email" id="email" name="email" required><br>

    <label for="instagram">Perfil do Instagram:</label>
    <input type="text" id="instagram" name="instagram"><br>

    <button type="submit">Cadastrar</button>
</form>
{% endblock %}
```

### `templates/index.html`

```html
{% extends "base.html" %}
{% block title %}Lista de Clientes{% endblock %}
{% block content %}
<h1>Lista de Clientes</h1>
<a href="/cadastro">Cadastrar Novo Cliente</a>
<ul>
    {% for cliente in clientes %}
        <li>{{ cliente.nome }} - {{ cliente.cpf }}</li>
    {% endfor %}
</ul>
{% endblock %}
```

### `static/style.css`

```css
body {
    font-family: Arial, sans-serif;
}

.container {
    width: 80%;
    margin: 0 auto;
}

.flash {
    padding: 10px;
    margin-bottom: 10px;
}

.flash.success {
    background-color: #d4edda;
    color: #155724;
}

.flash.error {
    background-color: #f8d7da;
    color: #721c24;
}
```

## Dependências

| Dependência       | Versão  | Link Oficial                                      |
|-------------------|---------|---------------------------------------------------|
| Python            | 3.x     | python.org   |
| Flask             | 2.x     | flask.palletsprojects.com |
| Flask-SQLAlchemy  | 2.x     | flask-sqlalchemy.palletsprojects.com |

## Contribuição

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues e pull requests para melhorias e correções.

## Licença

Este projeto está licenciado sob a licença MIT. Veja o arquivo LICENSE para mais detalhes.

## Tecnologia IA utilizada
Microsoft Copilot


