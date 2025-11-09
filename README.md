<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Portal de Dashboards Power BI</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&amp;display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <style>
        :root {
            --primary-color: #3b82f6; /* Azul vibrante */
            --secondary-color: #2563eb; /* Azul mais escuro */
            --background-light: #f8f9fa; /* Fundo muito claro */
            --background-dark: #e9ecef; /* Fundo um pouco mais escuro */
            --text-dark: #212529; /* Texto escuro */
            --text-muted: #6c757d; /* Texto cinza */
            --border-color: #dee2e6; /* Cor da borda */
            --card-bg: #ffffff; /* Fundo dos cartões */
            --shadow-light: rgba(0, 0, 0, 0.08);
            --shadow-medium: rgba(0, 0, 0, 0.15);
        }

        body {
            font-family: 'Inter', sans-serif;
            margin: 0;
            padding: 0;
            background-color: var(--background-light);
            color: var(--text-dark);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow-x: hidden; /* Evita scroll horizontal */
        }

        .container {
            background-color: var(--card-bg);
            border-radius: 12px;
            box-shadow: 0 4px 20px var(--shadow-light);
            width: 100%;
            max-width: 1200px;
            margin: 20px;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            min-height: 80vh;
        }

        /* --- Login Screen --- */
        #login-screen {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 40px;
            text-align: center;
            height: 100%;
            flex-grow: 1;
        }

        #login-screen h1 {
            color: var(--primary-color);
            margin-bottom: 20px;
            font-size: 2.5em;
        }

        #login-screen p {
            color: var(--text-muted);
            margin-bottom: 30px;
            font-size: 1.1em;
        }

        .login-form {
            display: flex;
            flex-direction: column;
            gap: 15px;
            width: 100%;
            max-width: 350px;
        }

        .login-form input[type="password"] {
            padding: 12px 15px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            font-size: 1em;
            outline: none;
            transition: border-color 0.3s ease;
        }

        .login-form input[type="password"]:focus {
            border-color: var(--primary-color);
        }

        .login-form button {
            background-color: var(--primary-color);
            color: white;
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            font-size: 1.1em;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
        }

        .login-form button:hover {
            background-color: var(--secondary-color);
            transform: translateY(-2px);
        }

        .login-form .error-message {
            color: var(--danger); /* Definir --danger no root se não estiver */
            font-size: 0.9em;
            margin-top: 5px;
        }

        /* --- Dashboard Layout --- */
        #dashboard-content {
            display: none; /* Escondido por padrão, mostrado após login */
            flex-direction: column;
            flex-grow: 1;
        }

        .tab-nav {
            display: flex;
            background-color: var(--background-dark);
            border-bottom: 1px solid var(--border-color);
            flex-wrap: wrap; /* Permite que as abas quebrem a linha em telas pequenas */
        }

        .tab-button {
            padding: 15px 25px;
            border: none;
            background-color: transparent;
            color: var(--text-muted);
            font-size: 1em;
            font-weight: 600;
            cursor: pointer;
            transition: color 0.3s ease, background-color 0.3s ease;
            border-bottom: 3px solid transparent;
            flex-grow: 1; /* Faz os botões ocuparem o espaço disponível */
            text-align: center;
        }

        .tab-button:hover {
            color: var(--primary-color);
            background-color: var(--background-light);
        }

        .tab-button.active {
            color: var(--primary-color);
            border-bottom-color: var(--primary-color);
            background-color: var(--card-bg);
        }

        .tab-content {
            flex-grow: 1;
            padding: 20px;
            display: none; /* Conteúdo da aba escondido por padrão */
            background-color: var(--card-bg);
            position: relative; /* Para o iframe */
        }

        .tab-content.active {
            display: block;
        }

        .powerbi-iframe {
            width: 100%;
            height: 100%;
            min-height: 600px; /* Altura mínima para o dashboard */
            border: none;
            border-radius: 8px;
            box-shadow: 0 2px 10px var(--shadow-light);
        }

        /* --- Responsividade --- */
        @media (max-width: 768px) {
            .container {
                margin: 10px;
                min-height: 95vh;
            }
            #login-screen h1 {
                font-size: 2em;
            }
            #login-screen p {
                font-size: 0.9em;
            }
            .login-form {
                max-width: 90%;
            }
            .tab-button {
                padding: 12px 15px;
                font-size: 0.9em;
            }
            .tab-content {
                padding: 15px;
            }
            .powerbi-iframe {
                min-height: 400px; /* Ajuste para telas menores */
            }
        }

        @media (max-width: 480px) {
            .tab-nav {
                flex-direction: column; /* Abas empilhadas em telas muito pequenas */
            }
            .tab-button {
                flex-grow: unset; /* Desativa o flex-grow para empilhar */
                width: 100%;
                border-bottom: 1px solid var(--border-color); /* Borda entre abas empilhadas */
            }
            .tab-button.active {
                border-bottom: 3px solid var(--primary-color); /* Mantém a borda ativa */
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Tela de Login -->
        <div id="login-screen">
            <h1>Bem-vindo ao Portal de Dashboards</h1>
            <p>Por favor, insira a senha para acessar os relatórios.</p>
            <form class="login-form" id="login-form">
                <input type="password" id="password-input" placeholder="Digite a senha" required>
                <button type="submit">Acessar Dashboards</button>
                <div class="error-message" id="login-error"></div>
            </form>
        </div>

        <!-- Conteúdo dos Dashboards (escondido por padrão) -->
        <div id="dashboard-content">
            <div class="tab-nav">
                <button class="tab-button active" data-tab="tab1">Visão Geral de Vendas</button>
                <button class="tab-button" data-tab="tab2">Detalhes de Vendas</button>
                <button class="tab-button" data-tab="tab3">Performance de Vendas</button>
            </div>

            <div id="tab1" class="tab-content active">
                <iframe class="powerbi-iframe" src="https://app.powerbi.com/view?r=eyJrIjoiMTBjZTE1ZGYtYmU4Ny00OGVkLTkzMDQtM2UzNjhkMTczZmUxIiwidCI6Ijc0MmYyNGViLWYyOWMtNDQwMS04NGU1LTYxN2Q3MzQ0YjViNyJ9" allowFullScreen="true"></iframe>
            </div>
            <div id="tab2" class="tab-content">
                <iframe class="powerbi-iframe" src="https://app.powerbi.com/view?r=eyJrIjoiMjBhZDdjMDctYzBmOS00YjMyLThiZDYtNDczN2FkZjhjYzA3IiwidCI6Ijc0MmYyNGViLWYyOWMtNDQwMS04NGU1LTYxN2Q3MzQ0YjViNyJ9" allowFullScreen="true"></iframe>
            </div>
            <div id="tab3" class="tab-content">
                <iframe class="powerbi-iframe" src="https://app.powerbi.com/view?r=eyJrIjoiMWE3MzQ4YjEtMTNiOS00MDY0LTgxNzYtMjIyYjRmNGI4M2ZiIiwidCI6Ijc0MmYyNGViLWYyOWMtNDQwMS04NGU1LTYxN2Q3MzQ0YjViNyJ9" allowFullScreen="true"></iframe>
            </div>
        </div>
    </div>

    <script>
        const CORRECT_PASSWORD = "12345"; // A senha para acesso

        document.addEventListener('DOMContentLoaded', () => {
            const loginScreen = document.getElementById('login-screen');
            const dashboardContent = document.getElementById('dashboard-content');
            const loginForm = document.getElementById('login-form');
            const passwordInput = document.getElementById('password-input');
            const loginError = document.getElementById('login-error');
            const tabButtons = document.querySelectorAll('.tab-button');
            const tabContents = document.querySelectorAll('.tab-content');

            // --- Lógica de Login ---
            loginForm.addEventListener('submit', (e) => {
                e.preventDefault();
                if (passwordInput.value === CORRECT_PASSWORD) {
                    loginScreen.style.display = 'none';
                    dashboardContent.style.display = 'flex'; // Mostra o conteúdo do dashboard
                    loginError.textContent = ''; // Limpa qualquer mensagem de erro
                    activateTab('tab1'); // Ativa a primeira aba por padrão após o login
                } else {
                    loginError.textContent = 'Senha incorreta. Tente novamente.';
                    passwordInput.value = ''; // Limpa o campo da senha
                }
            });

            // --- Lógica de Navegação por Abas ---
            tabButtons.forEach(button => {
                button.addEventListener('click', () => {
                    const targetTab = button.dataset.tab;

                    // Desativa todos os botões e conteúdos
                    tabButtons.forEach(btn => btn.classList.remove('active'));
                    tabContents.forEach(content => content.classList.remove('active'));

                    // Ativa o botão e o conteúdo da aba clicada
                    button.classList.add('active');
                    document.getElementById(targetTab).classList.add('active');
                });
            });

            // Função para ativar uma aba específica (usada após o login)
            function activateTab(tabId) {
                // Encontra o botão correspondente e simula um clique
                const buttonToActivate = document.querySelector(`.tab-button[data-tab="${tabId}"]`);
                if (buttonToActivate) {
                    buttonToActivate.click();
                }
            }
        });
    </script>
</body>
</html>
