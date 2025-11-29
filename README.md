<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Doce Essência RMS - Gestão de Varejo Integrada</title>
    <!-- Carrega o Tailwind CSS para estilização -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fb;
        }
        .card {
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
            transition: all 0.3s ease;
        }
        .nav-active {
            color: #ec4899; /* Pink-500 */
            border-bottom: 3px solid #ec4899;
            font-weight: 700;
        }
        .scrollable-list {
            max-height: 50vh;
            overflow-y: auto;
        }
        .text-income { color: #10b981; } /* Emerald-500 */
        .text-expense { color: #ef4444; } /* Red-500 */
        .bg-alert { background-color: #fef2f2; } /* Red-50 */
    </style>
</head>
<body>
    <div id="app" class="min-h-screen flex flex-col">

        <!-- 1. Tela de Login (Simulada) -->
        <div id="login-view" class="flex flex-col items-center justify-center min-h-screen p-4 bg-indigo-50">
            <div class="w-full max-w-md bg-white p-8 rounded-xl card text-center">
                <h1 class="text-3xl font-extrabold text-indigo-700 mb-6">Doce Essência RMS</h1>
                <p class="text-gray-600 mb-8">Sistema de Gestão de Varejo Integrado</p>
                <div id="login-spinner" class="text-center py-4">
                    <div class="animate-spin inline-block w-8 h-8 border-4 border-indigo-500 border-t-transparent rounded-full"></div>
                    <p class="text-gray-500 mt-2">Autenticando usuário...</p>
                </div>
                <p id="auth-message" class="text-sm text-red-500 mt-4 hidden">Falha na autenticação.</p>
            </div>
        </div>

        <!-- 2. Tela Principal (Após Login) -->
        <div id="main-view" class="hidden flex-grow flex">
            <!-- Barra de Navegação Lateral -->
            <nav class="w-64 bg-white p-6 shadow-xl space-y-4">
                <h2 class="text-2xl font-bold text-pink-600 mb-8">RMS</h2>
                
                <ul id="nav-list" class="space-y-2">
                    <li><button data-view="dashboard" class="nav-btn w-full text-left py-2 px-3 text-gray-600 hover:bg-gray-100 rounded-lg nav-active">📊 Dashboard</button></li>
                    <li><button data-view="inventory" class="nav-btn w-full text-left py-2 px-3 text-gray-600 hover:bg-gray-100 rounded-lg">📦 Estoque & Vendas</button></li>
                    <li><button data-view="finance" class="nav-btn w-full text-left py-2 px-3 text-gray-600 hover:bg-gray-100 rounded-lg">💰 Financeiro</button></li>
                    <li><button data-view="reports" class="nav-btn w-full text-left py-2 px-3 text-gray-600 hover:bg-gray-100 rounded-lg">📈 Relatórios</button></li>
                </ul>
                
                <div class="pt-6 border-t mt-6">
                    <h3 class="text-sm font-semibold text-gray-400 mb-2">Configurações</h3>
                    <ul>
                        <li><button data-view="admin" class="nav-btn w-full text-left py-2 px-3 text-gray-600 hover:bg-gray-100 rounded-lg">⚙️ Admin</button></li>
                    </ul>
                </div>
                <div class="text-xs text-gray-400 mt-4 pt-4 border-t">
                    Usuário ID: <span id="user-id-display">N/A</span>
                </div>
            </nav>

            <!-- Conteúdo Principal -->
            <main class="flex-grow p-8 bg-gray-50 overflow-y-auto">
                
                <!-- Alerta Global de Estoque Baixo -->
                <div id="global-low-stock-alert" class="hidden p-4 mb-6 rounded-lg bg-alert border border-red-300 transition-all duration-300">
                    <p class="font-semibold text-red-700">🚨 ALERTA: Estoque Baixo em <span id="low-stock-count">0</span> produtos.</p>
                    <p class="text-sm text-red-600 mt-1">Verifique a seção Estoque & Vendas para detalhes e reposição.</p>
                </div>

                <!-- 2.1. Dashboard View -->
                <section id="dashboard-view" class="view-content">
                    <h1 class="text-3xl font-bold text-gray-800 mb-6">Dashboard Gerencial</h1>
                    
                    <!-- KPIs Financeiros -->
                    <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
                        <div class="p-5 bg-white rounded-xl card border-l-4 border-pink-500">
                            <p class="text-sm font-medium text-gray-500">Faturamento Bruto (Receita)</p>
                            <p class="text-3xl font-extrabold text-pink-600 mt-1" id="kpi-revenue">R$ 0,00</p>
                        </div>
                        <div class="p-5 bg-white rounded-xl card border-l-4 border-indigo-500">
                            <p class="text-sm font-medium text-gray-500">Lucro Bruto (Vendas)</p>
                            <p class="text-3xl font-extrabold text-indigo-600 mt-1" id="kpi-gross-profit">R$ 0,00</p>
                        </div>
                        <div class="p-5 bg-white rounded-xl card border-l-4 border-red-500">
                            <p class="text-sm font-medium text-gray-500">Despesas Totais</p>
                            <p class="text-3xl font-extrabold text-red-600 mt-1" id="kpi-expenses">R$ 0,00</p>
                        </div>
                        <div class="p-5 bg-white rounded-xl card border-l-4 border-emerald-500">
                            <p class="text-sm font-medium text-gray-500">Lucro Líquido (Net Profit)</p>
                            <p class="text-3xl font-extrabold text-emerald-600 mt-1" id="kpi-net-profit">R$ 0,00</p>
                        </div>
                    </div>

                    <h2 class="text-2xl font-semibold text-gray-800 mb-4 border-b pb-2">Atividades Recentes (Vendas & Gastos)</h2>
                    <div class="bg-white p-6 rounded-xl card">
                        <div class="scrollable-list h-72">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-50 sticky top-0">
                                    <tr>
                                        <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Data</th>
                                        <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Tipo</th>
                                        <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Descrição/Item</th>
                                        <th class="px-4 py-2 text-right text-xs font-medium text-gray-500 uppercase">Valor</th>
                                    </tr>
                                </thead>
                                <tbody id="recent-transactions-body" class="bg-white divide-y divide-gray-200">
                                    <tr id="no-recent-transactions"><td colspan="4" class="text-center py-4 text-gray-500">Nenhuma transação recente.</td></tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <!-- 2.2. Inventory View (Estoque & Vendas) -->
                <section id="inventory-view" class="view-content hidden">
                    <h1 class="text-3xl font-bold text-gray-800 mb-6">Estoque, Vendas e Compras</h1>
                    
                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                        <!-- Formulário de Novo Produto -->
                        <div class="lg:col-span-1 bg-white p-6 rounded-xl card h-fit">
                            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">➕ Novo Produto</h2>
                            <form id="add-product-form" class="space-y-3">
                                <input type="text" id="product-name" placeholder="Nome do Produto" required class="w-full p-2 border rounded-lg">
                                <div class="grid grid-cols-2 gap-3">
                                    <input type="number" id="product-stock" placeholder="Qtd Inicial" value="10" min="0" required class="w-full p-2 border rounded-lg">
                                    <input type="number" id="product-min-stock" placeholder="Estoque Mínimo" value="5" min="1" required class="w-full p-2 border rounded-lg">
                                </div>
                                <div class="grid grid-cols-2 gap-3">
                                    <input type="number" id="product-last-cost" placeholder="Custo Unit. (R$)" step="0.01" min="0" required class="w-full p-2 border rounded-lg">
                                    <input type="number" id="product-price" placeholder="Preço Venda (R$)" step="0.01" min="0" required class="w-full p-2 border rounded-lg">
                                </div>
                                <button type="submit" class="w-full bg-indigo-600 text-white py-2 rounded-lg hover:bg-indigo-700 transition">Adicionar Produto</button>
                                <p id="product-message" class="text-xs text-center pt-1"></p>
                            </form>
                        </div>

                        <!-- Tabela de Estoque -->
                        <div class="lg:col-span-2 bg-white p-6 rounded-xl card">
                            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Inventário e Ações (Clique no Produto)</h2>
                            <div class="scrollable-list">
                                <table class="min-w-full divide-y divide-gray-200">
                                    <thead class="bg-gray-50 sticky top-0">
                                        <tr>
                                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Produto</th>
                                            <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Estoque / Mín</th>
                                            <th class="px-4 py-2 text-right text-xs font-medium text-gray-500 uppercase">Preço Venda</th>
                                            <th class="px-4 py-2 text-right text-xs font-medium text-gray-500 uppercase">CMV Unit.</th>
                                        </tr>
                                    </thead>
                                    <tbody id="inventory-table-body" class="divide-y divide-gray-200">
                                        <!-- Itens do estoque aqui -->
                                    </tbody>
                                </table>
                                <p id="empty-inventory" class="text-center py-6 text-gray-500 hidden">Nenhum produto cadastrado.</p>
                            </div>
                        </div>
                    </div>
                </section>
                
                <!-- 2.3. Finance View (Controle Financeiro) -->
                <section id="finance-view" class="view-content hidden">
                    <h1 class="text-3xl font-bold text-gray-800 mb-6">Controle Financeiro e Despesas</h1>
                    
                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                        
                        <!-- Formulário de Despesas -->
                        <div class="lg:col-span-1 bg-white p-6 rounded-xl card h-fit">
                            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">➖ Registrar Despesa (Gasto)</h2>
                            <form id="add-expense-form" class="space-y-3">
                                <input type="text" id="expense-description" placeholder="Ex: Aluguel da Loja" required class="w-full p-2 border rounded-lg">
                                <input type="number" id="expense-amount" placeholder="Valor (R$)" step="0.01" min="0.01" required class="w-full p-2 border rounded-lg">
                                <select id="expense-category" required class="w-full p-2 border rounded-lg">
                                    <option value="Operacional">Operacional</option>
                                    <option value="Marketing">Marketing</option>
                                    <option value="Salarios">Salários</option>
                                    <option value="Outros">Outros</option>
                                </select>
                                <button type="submit" class="w-full bg-red-600 text-white py-2 rounded-lg hover:bg-red-700 transition">Lançar Despesa</button>
                                <p id="expense-message" class="text-xs text-center pt-1"></p>
                            </form>
                        </div>
                        
                        <!-- Resumo e Histórico Financeiro -->
                        <div class="lg:col-span-2 space-y-6">
                             <div class="grid grid-cols-3 gap-4">
                                <div class="p-5 bg-white rounded-xl card border-l-4 border-pink-500">
                                    <p class="text-sm font-medium text-gray-500">Faturamento Bruto</p>
                                    <p class="text-xl font-extrabold text-pink-600 mt-1" id="finance-revenue">R$ 0,00</p>
                                </div>
                                <div class="p-5 bg-white rounded-xl card border-l-4 border-red-500">
                                    <p class="text-sm font-medium text-gray-500">CMV (Custo da Mercadoria)</p>
                                    <p class="text-xl font-extrabold text-red-600 mt-1" id="finance-cmv">R$ 0,00</p>
                                </div>
                                <div class="p-5 bg-white rounded-xl card border-l-4 border-indigo-500">
                                    <p class="text-sm font-medium text-gray-500">Lucro Bruto (Vendas)</p>
                                    <p class="text-xl font-extrabold text-indigo-600 mt-1" id="finance-gross-profit">R$ 0,00</p>
                                </div>
                            </div>
                            <div class="bg-white p-6 rounded-xl card">
                                <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Histórico de Movimentações Financeiras</h2>
                                <div class="scrollable-list">
                                    <table class="min-w-full divide-y divide-gray-200">
                                        <thead class="bg-gray-50 sticky top-0">
                                            <tr>
                                                <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Data</th>
                                                <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Descrição/Item</th>
                                                <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase">Tipo/Cat.</th>
                                                <th class="px-4 py-2 text-right text-xs font-medium text-gray-500 uppercase">Valor</th>
                                            </tr>
                                        </thead>
                                        <tbody id="finance-history-body" class="divide-y divide-gray-200">
                                            <tr id="no-finance-history"><td colspan="4" class="text-center py-4 text-gray-500">Nenhum registro financeiro.</td></tr>
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                        </div>
                    </div>
                </section>

                <!-- 2.4. Reports View (Relatórios) -->
                <section id="reports-view" class="view-content hidden">
                    <h1 class="text-3xl font-bold text-gray-800 mb-6">Relatórios Gerenciais</h1>
                    
                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                        
                        <!-- Top 5 Produtos Mais Lucrativos -->
                        <div class="bg-white p-6 rounded-xl card">
                            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">🏆 Top 5 Produtos Mais Lucrativos</h2>
                            <ul id="top-profit-list" class="space-y-3">
                                <li class="text-gray-500">Processando dados de vendas...</li>
                            </ul>
                        </div>
                        
                        <!-- Top 5 Despesas por Valor -->
                        <div class="bg-white p-6 rounded-xl card">
                            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">📉 Top 5 Maiores Despesas (Gastos)</h2>
                            <ul id="top-expenses-list" class="space-y-3">
                                <li class="text-gray-500">Processando dados de despesas...</li>
                            </ul>
                        </div>
                        
                        <!-- Margem Bruta Geral -->
                        <div class="bg-white p-6 rounded-xl card lg:col-span-2">
                            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Análise de Margem Bruta Geral</h2>
                            <div class="flex items-center space-x-4">
                                <div class="p-3 bg-pink-100 rounded-full">
                                    <svg class="w-6 h-6 text-pink-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c1.657 0 3 .895 3 2s-1.343 2-3 2-3 .895-3 2 1.343 2 3 2m0-8V6m0 10v2"></path></svg>
                                </div>
                                <div>
                                    <p class="text-sm text-gray-500">Margem Bruta Percentual</p>
                                    <p class="text-3xl font-extrabold text-indigo-700" id="report-gross-margin">0.00%</p>
                                </div>
                            </div>
                            <p class="text-sm text-gray-500 mt-2">Margem Bruta = (Receita - CMV) / Receita. Indica a lucratividade das suas vendas, antes de despesas fixas.</p>
                        </div>
                    </div>
                </section>
                
                <!-- 2.5. Admin View (Painel Administrativo) -->
                <section id="admin-view" class="view-content hidden">
                    <h1 class="text-3xl font-bold text-gray-800 mb-6">Painel Administrativo</h1>
                    
                    <div class="bg-white p-6 rounded-xl card max-w-lg space-y-4">
                        <h2 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Configurações do Sistema</h2>
                        
                        <div>
                            <p class="font-medium text-gray-700">ID Único do Aplicativo:</p>
                            <code class="block bg-gray-100 p-2 rounded text-sm text-gray-600 mt-1">${appId}</code>
                        </div>

                        <div>
                            <p class="font-medium text-gray-700">Função do Usuário Atual:</p>
                            <span class="inline-block bg-indigo-100 text-indigo-800 text-sm font-medium px-3 py-1 rounded-full mt-1">Gerente de Varejo</span>
                            <p class="text-xs text-gray-500 mt-1">Aqui você gerenciaria usuários, permissões e regras de acesso.</p>
                        </div>
                        
                        <button onclick="clearAllDataAndReload()" class="mt-4 bg-red-100 text-red-600 border border-red-300 py-2 px-4 rounded-lg hover:bg-red-200 transition">
                            ⚠️ Limpar Todos os Dados (Apenas Teste)
                        </button>
                    </div>
                </section>
            </main>
        </div>
    </div>

    <!-- Modal de Ação (Venda/Ajuste/Compra) -->
    <div id="action-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 hidden items-center justify-center p-4 z-50">
        <div class="bg-white p-6 rounded-xl w-full max-w-md card">
            <h3 class="text-xl font-bold text-gray-800 mb-4" id="modal-title">Registrar Venda / Ajuste</h3>
            <form id="stock-action-form" class="space-y-4">
                <input type="hidden" id="modal-product-id">
                <input type="hidden" id="modal-product-cost">
                <input type="hidden" id="modal-product-price">
                
                <p class="text-sm text-gray-700">Produto: <span id="modal-product-name" class="font-semibold"></span></p>
                <p class="text-sm text-gray-700">Estoque Atual: <span id="modal-current-stock" class="font-bold"></span></p>

                <div>
                    <label for="action-type" class="block text-sm font-medium text-gray-700">Tipo de Movimento</label>
                    <select id="action-type" required class="mt-1 p-2 w-full border rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                        <option value="sale">Saída (Venda) - Gera Faturamento</option>
                        <option value="purchase">Entrada (Compra) - Atualiza Estoque/Custo</option>
                        <option value="adjustment">Ajuste (Entrada/Saída) - Apenas Estoque</option>
                    </select>
                </div>
                
                <div>
                    <label for="action-quantity" class="block text-sm font-medium text-gray-700">Quantidade</label>
                    <input type="number" id="action-quantity" min="1" value="1" required class="mt-1 p-2 w-full border rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                </div>
                
                <div id="purchase-cost-field" class="hidden">
                    <label for="purchase-total-cost" class="block text-sm font-medium text-gray-700">Custo Total da Compra (R$)</label>
                    <input type="number" id="purchase-total-cost" step="0.01" min="0.01" placeholder="Ex: 500.00" class="mt-1 p-2 w-full border rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                    <p class="text-xs text-gray-500 mt-1">Este valor será usado para calcular o novo Custo Unitário (CMV).</p>
                </div>

                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeModal()" class="px-4 py-2 text-gray-600 bg-gray-200 rounded-lg hover:bg-gray-300 font-semibold">
                        Cancelar
                    </button>
                    <button type="submit" id="modal-submit-btn" class="text-white font-semibold py-2 px-4 rounded-lg shadow-md bg-indigo-600 hover:bg-indigo-700">
                        Confirmar Ação
                    </button>
                </div>
                <p id="modal-message" class="text-sm text-center pt-2"></p>
            </form>
        </div>
    </div>


    <!-- Configuração e Lógica do Firebase/JavaScript -->
    <script type="module">
        // Importações do Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, addDoc, onSnapshot, collection, query, runTransaction, Timestamp, setLogLevel, getDocs, deleteDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Variáveis globais do Canvas (MANDATÓRIO USAR)
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-doce-essencia-app';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Paths e Variáveis de Estado
        let app, db, auth;
        let userId = null;
        const INVENTORY_PATH = `/artifacts/${appId}/public/data/products`;
        const TRANSACTIONS_PATH = `/artifacts/${appId}/public/data/transactions`;
        
        let allTransactions = []; // Cache para cálculos financeiros e relatórios
        let allProducts = []; // Cache para renderização e lookups

        // Configurações Iniciais
        setLogLevel('error'); // Apenas erros, para não poluir o console

        // --- Funções de UI ---

        /**
         * Converte um valor numérico para o formato de moeda Real (R$).
         * @param {number} value
         * @returns {string}
         */
        const formatCurrency = (value) => {
            return new Intl.NumberFormat('pt-BR', {
                style: 'currency',
                currency: 'BRL',
            }).format(value || 0);
        };
        
        /**
         * Converte um valor numérico para o formato de porcentagem.
         * @param {number} value
         * @returns {string}
         */
        const formatPercentage = (value) => {
             // O valor deve ser a razão (ex: 0.25 para 25%)
             return `${(value * 100).toFixed(2)}%`;
        };
        
        /**
         * Converte timestamp do Firestore para string de data/hora.
         * @param {Timestamp} timestamp
         * @returns {string}
         */
        const formatDate = (timestamp) => {
            if (!timestamp || !timestamp.toDate) return 'N/A';
            const date = timestamp.toDate();
            return date.toLocaleDateString('pt-BR', {
                day: '2-digit', month: '2-digit',
                hour: '2-digit', minute: '2-digit'
            });
        };

        /**
         * Altera a view principal.
         * @param {string} targetView ID da view (ex: 'dashboard', 'inventory')
         */
        function switchView(targetView) {
            document.querySelectorAll('.view-content').forEach(view => {
                // CORREÇÃO: Adiciona verificação de nulidade para evitar erro
                if (view) { 
                    view.classList.add('hidden');
                }
            });
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.classList.remove('nav-active');
            });

            // CORREÇÃO: Adiciona verificação de nulidade para evitar erro
            const targetEl = document.getElementById(`${targetView}-view`);
            if (targetEl) {
                targetEl.classList.remove('hidden');
            }
            const navBtnEl = document.querySelector(`.nav-btn[data-view="${targetView}"]`);
            if (navBtnEl) {
                navBtnEl.classList.add('nav-active');
            }
            
            // Ação específica para relatórios (re-processar os dados em cache)
            if (targetView === 'reports') {
                renderReports(allTransactions);
            }
        }

        // Listener dos botões de navegação
        document.querySelectorAll('.nav-btn').forEach(button => {
            button.addEventListener('click', (e) => {
                const targetView = e.target.getAttribute('data-view');
                if (targetView) {
                    switchView(targetView);
                }
            });
        });

        // Funções do Modal
        function openModal(productId, productName, currentPrice, currentCost, currentStock) {
            document.getElementById('modal-product-id').value = productId;
            document.getElementById('modal-product-name').textContent = productName;
            document.getElementById('modal-product-cost').value = currentCost;
            document.getElementById('modal-product-price').value = currentPrice;
            document.getElementById('modal-current-stock').textContent = currentStock;

            document.getElementById('action-type').value = 'sale'; // Padrão é Venda
            document.getElementById('action-quantity').value = 1;
            document.getElementById('modal-message').textContent = '';
            document.getElementById('purchase-cost-field').classList.add('hidden');

            document.getElementById('action-modal').classList.remove('hidden');
            document.getElementById('action-modal').classList.add('flex');
        }

        window.closeModal = () => {
            document.getElementById('action-modal').classList.add('hidden');
            document.getElementById('action-modal').classList.remove('flex');
        };
        
        // Listener para alternar campos no modal de acordo com o tipo de ação
        document.getElementById('action-type').addEventListener('change', (e) => {
            const type = e.target.value;
            const costField = document.getElementById('purchase-cost-field');
            const quantityInput = document.getElementById('action-quantity');
            
            quantityInput.min = (type === 'sale' || type === 'purchase' || type === 'adjustment') ? 1 : 0;

            if (type === 'purchase') {
                costField.classList.remove('hidden');
                document.getElementById('modal-title').textContent = 'Registrar Compra (Entrada)';
            } else {
                costField.classList.add('hidden');
                document.getElementById('modal-title').textContent = (type === 'sale' ? 'Registrar Venda (Saída)' : 'Ajuste de Estoque');
            }
        });


        // --- Funções de Inicialização e Auth ---

        /**
         * Inicializa o Firebase e a Autenticação.
         */
        async function initializeFirebase() {
            try {
                if (Object.keys(firebaseConfig).length === 0) {
                    throw new Error("Configuração do Firebase não encontrada.");
                }

                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);
                
                // CORREÇÃO: Adiciona verificação de nulidade antes de usar classList
                const loginSpinner = document.getElementById('login-spinner');
                if (loginSpinner) {
                    loginSpinner.classList.remove('hidden');
                }

                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        userId = user.uid;
                        document.getElementById('user-id-display').textContent = userId;
                        document.getElementById('login-view').classList.add('hidden');
                        document.getElementById('main-view').classList.remove('hidden');
                        
                        setupInitialData(); // Garante dados iniciais se vazio
                        setupRealtimeListeners(); // Inicia a sincronização de dados
                        switchView('dashboard');

                    } else {
                        try {
                            if (initialAuthToken) {
                                await signInWithCustomToken(auth, initialAuthToken);
                            } else {
                                await signInAnonymously(auth);
                            }
                        } catch (e) {
                            console.error("Erro ao tentar login:", e);
                            if (loginSpinner) loginSpinner.classList.add('hidden');
                            const authMessage = document.getElementById('auth-message');
                            if (authMessage) {
                                authMessage.textContent = 'Falha de autenticação. Tente novamente.';
                                authMessage.classList.remove('hidden');
                            }
                        }
                    }
                });

            } catch (error) {
                console.error("Erro na inicialização do Firebase ou Autenticação:", error);
                const loginSpinner = document.getElementById('login-spinner');
                if (loginSpinner) loginSpinner.classList.add('hidden');
                const authMessage = document.getElementById('auth-message');
                if (authMessage) {
                    authMessage.textContent = 'Erro Crítico: ' + error.message;
                    authMessage.classList.remove('hidden');
                }
            }
        }
        
        const initialProducts = [
            { name: "Perfume Floral (F)", stock: 15, minStock: 5, price: 150.00, lastCost: 80.00 },
            { name: "Colônia Amadeirada (M)", stock: 12, minStock: 5, price: 90.00, lastCost: 45.00 },
            { name: "Base Líquida Matte", stock: 8, minStock: 10, price: 65.00, lastCost: 35.00 },
            { name: "Sérum Facial Vitamina C", stock: 3, minStock: 5, price: 85.00, lastCost: 50.00 },
        ];

        /**
         * Verifica e adiciona produtos iniciais se a coleção de estoque estiver vazia.
         */
        async function setupInitialData() {
            if (!db) return;
            const q = query(collection(db, INVENTORY_PATH));
            try {
                const snapshot = await getDocs(q);
                if (snapshot.empty) {
                    console.log("Coleção de Estoque vazia. Adicionando produtos iniciais...");
                    
                    initialProducts.forEach(async (product) => {
                        await addDoc(collection(db, INVENTORY_PATH), {
                            ...product,
                            createdAt: Timestamp.now(),
                        });
                    });
                    console.log("Produtos iniciais adicionados com sucesso.");
                }
            } catch (e) {
                console.error("Erro ao configurar dados iniciais:", e);
            }
        }
        
        // --- Lógica de CRUD (Estoque) ---

        document.getElementById('add-product-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            if (!db) return;

            const name = document.getElementById('product-name').value;
            const stock = parseInt(document.getElementById('product-stock').value);
            const minStock = parseInt(document.getElementById('product-min-stock').value);
            const price = parseFloat(document.getElementById('product-price').value);
            const lastCost = parseFloat(document.getElementById('product-last-cost').value);

            const messageEl = document.getElementById('product-message');
            messageEl.textContent = 'Salvando...';

            try {
                await addDoc(collection(db, INVENTORY_PATH), {
                    name, stock, minStock, price, lastCost, createdAt: Timestamp.now(),
                });

                messageEl.textContent = 'Produto adicionado com sucesso!';
                messageEl.className = 'text-xs text-center pt-1 text-emerald-600';
                e.target.reset();
            } catch (e) {
                console.error("Erro ao adicionar produto: ", e);
                messageEl.textContent = 'Erro ao salvar: ' + e.message;
                messageEl.className = 'text-xs text-center pt-1 text-red-500';
            }
        });
        
        // --- Lógica de Transação (Venda/Ajuste/Compra) ---

        document.getElementById('stock-action-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            if (!db) return;

            const productId = document.getElementById('modal-product-id').value;
            const actionType = document.getElementById('action-type').value;
            const quantity = parseInt(document.getElementById('action-quantity').value);
            const productName = document.getElementById('modal-product-name').textContent;
            
            const messageEl = document.getElementById('modal-message');
            messageEl.textContent = 'Processando...';
            messageEl.className = 'text-sm text-center pt-2 text-indigo-500';
            
            try {
                await runTransaction(db, async (transaction) => {
                    const productRef = doc(db, INVENTORY_PATH, productId);
                    const productDoc = await transaction.get(productRef);

                    if (!productDoc.exists()) throw new Error("Produto não encontrado.");

                    const data = productDoc.data();
                    let newStock = data.stock;
                    let transactionData = {
                        type: actionType,
                        productId: productId,
                        productName: productName,
                        quantity: quantity,
                        timestamp: Timestamp.now(),
                    };
                    
                    let newCost = data.lastCost; // Custo de estoque atual
                    let newPrice = data.price; // Preço de venda atual

                    if (actionType === 'sale') { // VENDA (Saída de Estoque, Receita Financeira)
                        if (data.stock < quantity) throw new Error(`Estoque insuficiente. Disponível: ${data.stock}`);
                        newStock -= quantity;
                        
                        // Faturamento e CMV
                        transactionData.unitPrice = newPrice;
                        transactionData.unitCost = newCost; // CMV unitário usado
                        transactionData.totalRevenue = quantity * newPrice;
                        transactionData.totalCostOfSale = quantity * newCost; // CMV total
                        transactionData.profit = transactionData.totalRevenue - transactionData.totalCostOfSale;
                        transactionData.category = 'Receita de Venda';

                    } else if (actionType === 'purchase') { // COMPRA (Entrada de Estoque, Despesa Financeira)
                        const totalCost = parseFloat(document.getElementById('purchase-total-cost').value);
                        if (totalCost <= 0) throw new Error("Custo total deve ser maior que zero.");
                        
                        newStock += quantity;
                        newCost = totalCost / quantity; // ATUALIZA CUSTO UNITÁRIO
                        
                        // Lançamento Financeiro como Gasto de Compra
                        transactionData.unitCost = newCost;
                        transactionData.totalCost = totalCost;
                        transactionData.description = 'Compra de Mercadoria (Estoque)';
                        transactionData.category = 'Custo de Estoque'; // Distinto da Despesa Operacional

                    } else if (actionType === 'adjustment') { // AJUSTE DE ESTOQUE (Sem impacto financeiro)
                        newStock += quantity; // Simplesmente ajusta o estoque. Se a quantidade for negativa, é uma saída.
                        transactionData.description = 'Ajuste de Estoque';
                    }
                    
                    // Atualiza o estoque e, se foi compra, o custo unitário (lastCost)
                    transaction.update(productRef, { stock: newStock, lastCost: newCost });
                    
                    // Adiciona a transação (Venda ou Compra)
                    if (actionType === 'sale' || actionType === 'purchase') {
                        await addDoc(collection(db, TRANSACTIONS_PATH), transactionData);
                    }
                });

                messageEl.textContent = `Ação (${actionType}) registrada com sucesso!`;
                messageEl.className = 'text-sm text-center pt-2 text-emerald-600';
                
                setTimeout(closeModal, 1000);

            } catch (e) {
                console.error("Erro na transação de estoque:", e);
                messageEl.textContent = `Erro: ${e.message || e}`;
                messageEl.className = 'text-sm text-center pt-2 text-red-500';
            }
        });
        
        // --- Lógica de Despesas (Financeiro) ---

        document.getElementById('add-expense-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            if (!db) return;

            const description = document.getElementById('expense-description').value;
            const amount = parseFloat(document.getElementById('expense-amount').value);
            const category = document.getElementById('expense-category').value;

            const messageEl = document.getElementById('expense-message');
            messageEl.textContent = 'Lançando despesa...';

            try {
                await addDoc(collection(db, TRANSACTIONS_PATH), {
                    type: 'expense',
                    description,
                    amount: amount, // Montante do gasto (negativo no balanço)
                    category,
                    timestamp: Timestamp.now(),
                });

                messageEl.textContent = 'Despesa lançada com sucesso!';
                messageEl.className = 'text-xs text-center pt-1 text-emerald-600';
                e.target.reset();
            } catch (e) {
                console.error("Erro ao lançar despesa: ", e);
                messageEl.textContent = 'Erro ao salvar despesa: ' + e.message;
                messageEl.className = 'text-xs text-center pt-1 text-red-500';
            }
        });


        // --- Listeners e Renderização em Tempo Real ---

        function setupRealtimeListeners() {
            if (!db) return;
            
            // Listener 1: Estoque
            onSnapshot(collection(db, INVENTORY_PATH), renderInventory, (error) => {
                console.error("Erro ao ouvir estoque:", error);
            });

            // Listener 2: Transações (Vendas, Compras, Despesas)
            onSnapshot(collection(db, TRANSACTIONS_PATH), renderTransactionsAndKpis, (error) => {
                console.error("Erro ao ouvir transações:", error);
            });
        }
        
        function renderInventory(snapshot) {
            const tableBody = document.getElementById('inventory-table-body');
            const emptyInventory = document.getElementById('empty-inventory');
            const alertContainer = document.getElementById('global-low-stock-alert');
            const lowStockCountEl = document.getElementById('low-stock-count');
            
            // Inicializa ou limpa
            if (tableBody) tableBody.innerHTML = '';
            allProducts = [];
            let lowStockCount = 0;

            if (snapshot.empty) {
                if (emptyInventory) emptyInventory.classList.remove('hidden');
                if (alertContainer) alertContainer.classList.add('hidden');
                return;
            } else {
                if (emptyInventory) emptyInventory.classList.add('hidden');
            }

            snapshot.forEach((docSnap) => {
                const data = docSnap.data();
                const productId = docSnap.id;
                allProducts.push({ id: productId, ...data });
                
                // Cálculo de Alerta
                if (data.stock <= data.minStock) lowStockCount++;

                // Renderiza a linha
                const row = tableBody.insertRow();
                row.className = 'hover:bg-indigo-50 transition-colors cursor-pointer';
                row.onclick = () => openModal(productId, data.name, data.price, data.lastCost, data.stock);

                const alertClass = data.stock <= data.minStock ? 'bg-red-50 font-semibold text-red-700 p-1 rounded' : 'text-gray-900';

                row.insertCell().innerHTML = `<p class="px-4 py-2 text-sm font-semibold text-gray-800">${data.name}</p>`;

                row.insertCell().innerHTML = `
                    <p class="px-4 py-2 text-sm">
                        <span class="${alertClass}">${data.stock}</span> / ${data.minStock}
                    </p>
                `;

                row.insertCell().innerHTML = `<p class="px-4 py-2 text-right text-sm text-pink-600 font-bold">${formatCurrency(data.price)}</p>`;
                row.insertCell().innerHTML = `<p class="px-4 py-2 text-right text-sm text-gray-500">${formatCurrency(data.lastCost)}</p>`;
            });
            
            // Atualiza Alerta Global
            if (alertContainer && lowStockCountEl) {
                 if (lowStockCount > 0) {
                    lowStockCountEl.textContent = lowStockCount;
                    alertContainer.classList.remove('hidden');
                } else {
                    alertContainer.classList.add('hidden');
                }
            }
        }

        function renderTransactionsAndKpis(snapshot) {
            allTransactions = [];
            let totalRevenue = 0;
            let totalCogs = 0; // Custo da Mercadoria Vendida (CMV)
            let totalOperatingExpenses = 0; // Despesas Operacionais e Outras
            let totalPurchaseCost = 0; // Custo total de compras (lançamento no caixa)

            snapshot.forEach((docSnap) => {
                const data = docSnap.data();
                allTransactions.push({ id: docSnap.id, ...data });

                if (data.type === 'sale') {
                    totalRevenue += data.totalRevenue;
                    totalCogs += data.totalCostOfSale;
                } else if (data.type === 'expense') {
                    totalOperatingExpenses += data.amount;
                } else if (data.type === 'purchase') {
                    totalPurchaseCost += data.totalCost;
                }
            });
            
            const grossProfit = totalRevenue - totalCogs; // Lucro Bruto = Faturamento - CMV
            const netProfit = grossProfit - totalOperatingExpenses; // Lucro Líquido = Lucro Bruto - Despesas
            
            // Atualiza KPIs do Dashboard
            document.getElementById('kpi-revenue').textContent = formatCurrency(totalRevenue);
            document.getElementById('kpi-gross-profit').textContent = formatCurrency(grossProfit);
            document.getElementById('kpi-expenses').textContent = formatCurrency(totalOperatingExpenses);
            document.getElementById('kpi-net-profit').textContent = formatCurrency(netProfit);

            // Atualiza KPIs do Financeiro
            document.getElementById('finance-revenue').textContent = formatCurrency(totalRevenue);
            document.getElementById('finance-cmv').textContent = formatCurrency(totalCogs);
            document.getElementById('finance-gross-profit').textContent = formatCurrency(grossProfit);

            // Renderiza Histórico Recente (Dashboard) e Histórico Financeiro Detalhado
            renderTransactionHistory(totalPurchaseCost);
        }
        
        // Passamos o totalPurchaseCost para a função para fins de contexto, mas o foco é a renderização
        function renderTransactionHistory(totalPurchaseCost) {
            // Ordena as transações pela data mais recente
            const sortedTransactions = allTransactions.sort((a, b) => b.timestamp.toMillis() - a.timestamp.toMillis());

            // 1. Histórico Recente (Dashboard)
            const recentBody = document.getElementById('recent-transactions-body');
            const noRecentEl = document.getElementById('no-recent-transactions');

            // CORREÇÃO: Verificação de nulidade para evitar erro de classe
            if (recentBody) {
                recentBody.innerHTML = '';
            }
            const recentTransactions = sortedTransactions.slice(0, 8);
            
            if (recentBody) {
                if (recentTransactions.length === 0) {
                    if (noRecentEl) noRecentEl.classList.remove('hidden');
                } else {
                    if (noRecentEl) noRecentEl.classList.add('hidden');
                    
                    recentTransactions.forEach(t => {
                        const row = recentBody.insertRow();
                        let typeText, description, value, valueClass;

                        if (t.type === 'sale') {
                            typeText = 'Venda';
                            description = t.productName;
                            value = t.totalRevenue;
                            valueClass = 'text-income';
                        } else if (t.type === 'purchase') {
                            typeText = 'Compra';
                            description = t.productName;
                            value = -t.totalCost;
                            valueClass = 'text-expense'; // Compra é saída de caixa
                        } else if (t.type === 'expense') {
                            typeText = 'Despesa';
                            description = t.description;
                            value = -t.amount;
                            valueClass = 'text-expense';
                        } else {
                            return;
                        }

                        row.insertCell().textContent = formatDate(t.timestamp);
                        row.insertCell().textContent = typeText;
                        row.insertCell().textContent = description;
                        row.insertCell().innerHTML = `<p class="text-right font-bold ${valueClass}">${formatCurrency(value)}</p>`;
                    });
                }
            }


            // 2. Histórico Financeiro Detalhado (Finance View)
            const financeBody = document.getElementById('finance-history-body');
            const noFinanceEl = document.getElementById('no-finance-history');

            // CORREÇÃO: Verificação de nulidade para evitar erro de classe
            if (financeBody) {
                financeBody.innerHTML = '';
            }
            
            if (financeBody) {
                if (sortedTransactions.length === 0) {
                    if (noFinanceEl) noFinanceEl.classList.remove('hidden');
                } else {
                    if (noFinanceEl) noFinanceEl.classList.add('hidden');

                    sortedTransactions.forEach(t => {
                        // Inclui Vendas, Compras e Despesas
                        if (t.type === 'sale' || t.type === 'expense' || t.type === 'purchase') {
                             const row = financeBody.insertRow();
                             let value, description, category, valueClass;

                            if (t.type === 'sale') {
                                description = `Venda: ${t.productName} (${t.quantity} un.)`;
                                category = 'Receita de Venda';
                                value = t.totalRevenue;
                                valueClass = 'text-income';
                            } else if (t.type === 'expense') {
                                description = t.description;
                                category = t.category;
                                value = -t.amount;
                                valueClass = 'text-expense';
                            } else if (t.type === 'purchase') {
                                description = `Compra de ${t.productName} (${t.quantity} un.)`;
                                category = 'Custo de Estoque';
                                value = -t.totalCost;
                                valueClass = 'text-expense';
                            } else {
                                return;
                            }

                            row.insertCell().textContent = formatDate(t.timestamp);
                            row.insertCell().textContent = description;
                            row.insertCell().textContent = category;
                            row.insertCell().innerHTML = `<p class="text-right font-bold ${valueClass}">${formatCurrency(value)}</p>`;
                        }
                    });
                }
            }
        }
        
        // --- Lógica de Relatórios ---

        function renderReports(transactions) {
            
            const salesTransactions = transactions.filter(t => t.type === 'sale');
            const expenseTransactions = transactions.filter(t => t.type === 'expense');

            // 1. Top 5 Produtos Mais Lucrativos (Profit)
            const profitMap = salesTransactions.reduce((acc, t) => {
                const key = t.productName;
                acc[key] = acc[key] || { name: key, profit: 0, revenue: 0, cogs: 0 };
                acc[key].profit += t.profit;
                acc[key].revenue += t.totalRevenue;
                acc[key].cogs += t.totalCostOfSale;
                return acc;
            }, {});

            const topProfit = Object.values(profitMap)
                .map(item => ({
                    ...item,
                    margin: item.revenue > 0 ? (item.profit / item.revenue) : 0
                }))
                .sort((a, b) => b.profit - a.profit)
                .slice(0, 5);

            const topProfitList = document.getElementById('top-profit-list');
            if (topProfitList) topProfitList.innerHTML = '';
            
            if (topProfit.length === 0) {
                if (topProfitList) topProfitList.innerHTML = '<li class="text-gray-500">Nenhum dado de vendas para relatar lucro.</li>';
            } else {
                topProfit.forEach((item, index) => {
                    if (topProfitList) topProfitList.innerHTML += `
                        <li class="flex justify-between items-center p-3 bg-indigo-50 rounded-lg">
                            <span class="font-bold text-indigo-600">${index + 1}. ${item.name}</span>
                            <span class="text-sm font-semibold text-emerald-600">${formatCurrency(item.profit)} (${formatPercentage(item.margin)})</span>
                        </li>
                    `;
                });
            }

            // 2. Top 5 Maiores Despesas
            const topExpenses = expenseTransactions
                .sort((a, b) => b.amount - a.amount)
                .slice(0, 5);
                
            const topExpensesList = document.getElementById('top-expenses-list');
            if (topExpensesList) topExpensesList.innerHTML = '';

            if (topExpenses.length === 0) {
                if (topExpensesList) topExpensesList.innerHTML = '<li class="text-gray-500">Nenhum dado de despesas para relatar.</li>';
            } else {
                topExpenses.forEach((item, index) => {
                    if (topExpensesList) topExpensesList.innerHTML += `
                        <li class="flex justify-between items-center p-3 bg-red-50 rounded-lg">
                            <span class="font-bold text-red-600">${index + 1}. ${item.description}</span>
                            <span class="text-sm font-semibold">${formatCurrency(item.amount)} (${item.category})</span>
                        </li>
                    `;
                });
            }
            
            // 3. Margem Bruta Geral
            const totalRevenue = salesTransactions.reduce((sum, t) => sum + t.totalRevenue, 0);
            const totalCogs = salesTransactions.reduce((sum, t) => sum + t.totalCostOfSale, 0);
            const totalGrossProfit = totalRevenue - totalCogs;
            
            let grossMargin = 0;
            if (totalRevenue > 0) {
                grossMargin = totalGrossProfit / totalRevenue;
            }

            const reportGrossMarginEl = document.getElementById('report-gross-margin');
            if (reportGrossMarginEl) {
                reportGrossMarginEl.textContent = formatPercentage(grossMargin);
            }
        }
        
        // --- Admin/Debug Functions ---
        
        async function clearAllDataAndReload() {
            if (!confirm("Tem certeza que deseja limpar TODOS os dados do Firestore para este APP? Isso é irreversível!")) return;
            
            try {
                // Deletar Produtos
                const productDocs = await getDocs(collection(db, INVENTORY_PATH));
                const deleteProducts = productDocs.docs.map(d => deleteDoc(doc(db, INVENTORY_PATH, d.id)));
                
                // Deletar Transações
                const transactionDocs = await getDocs(collection(db, TRANSACTIONS_PATH));
                const deleteTransactions = transactionDocs.docs.map(d => deleteDoc(doc(db, TRANSACTIONS_PATH, d.id)));
                
                await Promise.all([...deleteProducts, ...deleteTransactions]);
                
                alert("Todos os dados foram apagados! Recarregando a página.");
                window.location.reload();
            } catch (error) {
                console.error("Erro ao limpar dados:", error);
                alert("Erro ao limpar dados. Verifique o console.");
            }
        }
        window.clearAllDataAndReload = clearAllDataAndReload; // Torna acessível no escopo global
        
        // Inicia a aplicação
        window.onload = initializeFirebase;
    </script>
</body>
</html>
