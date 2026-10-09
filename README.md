<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CODE_MATCH - MVP Avançado TCC</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .font-mono { font-family: 'JetBrains Mono', monospace; }
        .hidden-section { display: none !important; }
        
        /* Garante que o canvas fique no fundo sem atrapalhar os cliques nos botões */
        #tech-bg-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            pointer-events: none;
            opacity: 0.15; /* Sutil para não atrapalhar a leitura do texto */
        }
        
        /* Ajuste de profundidade para os elementos da interface ficarem por cima da animação */
        #toast, #modal-chat, #screen-cadastro, #screen-dashboard {
            position: relative;
            z-index: 1;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col selection:bg-emerald-500 selection:text-slate-950 overflow-x-hidden">

    <canvas id="tech-bg-canvas"></canvas>

    <div id="toast" class="fixed bottom-5 right-5 bg-emerald-500 text-slate-950 font-mono text-sm px-4 py-3 rounded-lg shadow-2xl transition-all duration-300 transform translate-y-20 opacity-0 z-50 flex items-center space-x-2 font-bold">
        <span>✓</span>
        <span id="toast-message">Ação realizada!</span>
    </div>

    <div id="modal-chat" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 flex items-center justify-center hidden-section">
        <div class="bg-slate-900 border border-slate-800 w-full max-w-lg rounded-xl overflow-hidden shadow-2xl flex flex-col h-[500px]">
            <div class="bg-slate-950/40 p-4 border-b border-slate-800 flex justify-between items-center">
                <div>
                    <span id="chat-tag" class="bg-amber-500/10 text-amber-400 text-[10px] font-mono px-2 py-0.5 rounded border border-amber-500/20 uppercase">LÓGICA</span>
                    <h3 id="chat-title" class="font-bold text-slate-200 mt-1">Sala de Mentoria Ativa</h3>
                </div>
                <button onclick="fecharChat()" class="text-slate-400 hover:text-slate-200 font-mono text-xs p-1 cursor-pointer">[ FECHAR ]</button>
            </div>
            <div id="chat-messages" class="flex-1 p-4 overflow-y-auto space-y-3 font-mono text-xs">
                <div class="bg-slate-950/50 p-3 rounded border border-slate-800/50">
                    <span class="text-sky-400 font-bold">[ALUNO]:</span> <span id="chat-desc">Dúvida inicial...</span>
                </div>
            </div>
            <div class="p-4 border-t border-slate-800 bg-slate-950/30 flex space-x-2">
                <input type="text" id="chat-input" onkeypress="if(event.key === 'Enter') enviarMensagem()" class="flex-1 bg-slate-950 border border-slate-800 rounded-lg px-4 py-2 text-slate-200 text-xs font-mono focus:outline-none focus:border-emerald-500" placeholder="Digite uma orientação prática ou código...">
                <button onclick="enviarMensagem()" class="bg-emerald-500 hover:bg-emerald-600 text-slate-950 font-mono font-bold px-4 py-2 rounded-lg text-xs cursor-pointer">ENVIAR</button>
            </div>
            <div class="p-3 bg-slate-950/60 border-t border-slate-800/50 flex justify-between items-center px-4">
                <span class="text-[10px] font-mono text-amber-400">Finalize para validar a sessão</span>
                <button onclick="finalizarMentoria()" class="bg-amber-500 hover:bg-amber-600 text-slate-950 text-xs font-bold font-mono px-3 py-1 rounded cursor-pointer">ENCERRAR_ATENDIMENTO</button>
            </div>
        </div>
    </div>

    <div id="screen-cadastro" class="flex flex-col items-center justify-center min-h-screen px-4 py-8">
        <div class="w-full max-w-md bg-slate-900/90 border border-slate-800 backdrop-blur-md rounded-xl p-8 shadow-2xl">
            <div class="text-center mb-6">
                <span class="font-mono text-emerald-400 text-sm">// ECOSSISTEMA_P2P v4.0</span>
                <h1 class="text-2xl font-bold tracking-tight mt-1">Plataforma CodeMatch</h1>
                <p class="text-slate-400 text-sm mt-2">Triagem algorítmica para redução de evasão escolar.</p>
            </div>

            <div class="space-y-4">
                <div>
                    <label class="block text-xs font-mono uppercase tracking-wider text-slate-400 mb-1.5">Nome Completo</label>
                    <input type="text" id="input-nome" class="w-full bg-slate-950 border border-slate-800 rounded-lg px-4 py-2 text-slate-100 focus:outline-none focus:border-emerald-500 font-mono text-sm" value="Isaías Alves">
                </div>

                <div>
                    <label class="block text-xs font-mono uppercase tracking-wider text-slate-400 mb-1.5">E-mail Estudantil</label>
                    <input type="email" id="input-email" class="w-full bg-slate-950 border border-slate-800 rounded-lg px-4 py-2 text-slate-100 focus:outline-none focus:border-emerald-500 font-mono text-sm" value="isaias.alves@instituto.edu.br">
                </div>

                <div class="border-t border-slate-800 pt-3">
                    <label class="block text-xs font-mono uppercase tracking-wider text-slate-400 mb-2">Qual o seu perfil atual na TI?</label>
                    
                    <div class="space-y-2">
                        <label class="flex items-start p-3 bg-slate-950 border border-slate-800 rounded-lg cursor-pointer hover:border-emerald-500 transition-colors">
                            <input type="radio" name="nivel" value="iniciante" class="mt-1 accent-emerald-500">
                            <div class="ml-3">
                                <span class="block text-sm font-semibold text-slate-200">Sou Iniciante (Preciso de Mentoria)</span>
                                <span class="block text-xs text-slate-400 mt-0.5">Quero postar dúvidas para veteranos me guiarem.</span>
                            </div>
                        </label>

                        <label class="flex items-start p-3 bg-slate-9550 border border-slate-800 rounded-lg cursor-pointer hover:border-emerald-500 transition-colors">
                            <input type="radio" name="nivel" value="experiente" checked class="mt-1 accent-emerald-500">
                            <div class="ml-3">
                                <span class="block text-sm font-semibold text-slate-200">Sou Veterano / Monitor (Quero Ser Mentor)</span>
                                <span class="block text-xs text-slate-400 mt-0.5">Quero acumular XP resolvendo bugs e validar horas.</span>
                            </div>
                        </label>
                    </div>
                </div>

                <button type="button" onclick="avancarParaDashboard()" class="w-full bg-emerald-500 hover:bg-emerald-600 text-slate-950 font-bold py-3 px-4 rounded-lg transition-colors font-mono tracking-wide text-sm mt-4 cursor-pointer block text-center">
                    CONCLUIR_TRIAGEM_E_ENTRAR ->
                </button>
            </div>
        </div>
    </div>

    <div id="screen-dashboard" class="hidden-section min-h-screen flex flex-col">
        <header class="bg-slate-900/80 backdrop-blur-md border-b border-slate-800 px-6 py-4 flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <div class="bg-emerald-500 text-slate-950 font-bold p-2 rounded font-mono text-xs"><></div>
                <span class="font-mono font-bold tracking-tight text-lg">CODE_MATCH</span>
            </div>
            
            <div class="flex items-center space-x-6 bg-slate-950/80 px-4 py-2 rounded-lg border border-slate-800">
                <div class="text-right">
                    <span id="user-role" class="block font-mono text-[10px] uppercase tracking-wider text-emerald-400">MENTOR</span>
                    <span id="user-display-name" class="block text-sm font-semibold text-slate-200">Isaías Alves</span>
                </div>
                <div class="border-l border-slate-800 h-8 pl-4 flex items-center space-x-2">
                    <span class="font-mono text-xs text-slate-400">LVL</span>
                    <span id="user-level" class="font-mono text-lg font-bold text-emerald-400">01</span>
                    <div class="w-20 bg-slate-800 h-2 rounded-full overflow-hidden ml-1">
                        <div id="xp-bar" class="bg-emerald-500 h-full transition-all duration-500" style="width: 20%;"></div>
                    </div>
                    <span id="user-xp-text" class="font-mono text-[10px] text-slate-500 ml-1">20/100 XP</span>
                </div>
            </div>
        </header>

        <div class="flex flex-1">
            <aside class="w-64 bg-slate-900/40 backdrop-blur-md border-r border-slate-800 p-6 flex flex-col justify-between">
                <div class="space-y-2">
                    <span class="block text-[10px] font-mono uppercase tracking-wider text-slate-500 mb-4">Navegação</span>
                    
                    <button onclick="switchTab('mural')" id="btn-tab-mural" class="w-full text-left flex items-center space-x-3 bg-slate-900 border border-slate-800 text-emerald-400 p-3 rounded-lg font-mono text-sm border-l-2 border-emerald-400 font-medium cursor-pointer transition-all">
                        <span>// 01.</span> <span>Mural / Hub Ativo</span>
                    </button>
                    
                    <button onclick="switchTab('conquistas')" id="btn-tab-conquistas" class="w-full text-left flex items-center space-x-3 text-slate-400 hover:text-slate-200 p-3 rounded-lg font-mono text-sm cursor-pointer transition-all">
                        <span>// 02.</span> <span>Badges & Gamificação</span>
                    </button>
                    
                    <button onclick="switchTab('ranking')" id="btn-tab-ranking" class="w-full text-left flex items-center space-x-3 text-slate-400 hover:text-slate-200 p-3 rounded-lg font-mono text-sm cursor-pointer transition-all">
                        <span>// 03.</span> <span>Ranking Institucional</span>
                    </button>
                </div>

                <div class="pt-4 border-t border-slate-800">
                    <button onclick="limparSessao()" class="w-full bg-rose-500/10 border border-rose-500/20 hover:bg-rose-500 hover:text-slate-950 text-rose-400 font-mono text-[10px] py-1.5 rounded transition-all cursor-pointer text-center">
                        RESETAR_SISTEMA (Zerar Cache)
                    </button>
                </div>
            </aside>

            <main class="flex-1 p-8 max-w-4xl flex flex-col h-[calc(100vh-73px)] overflow-y-auto justify-between">
                
                <div>
                    <div class="grid grid-cols-3 gap-4 mb-6">
                        <div class="bg-slate-900/80 backdrop-blur-md border border-slate-800 p-4 rounded-xl">
                            <span class="block text-xs font-mono text-slate-400 uppercase">// Atendimentos</span>
                            <span id="stat-atendimentos" class="block text-2xl font-bold mt-1 text-slate-100">0</span>
                        </div>
                        <div class="bg-slate-900/80 backdrop-blur-md border border-slate-800 p-4 rounded-xl">
                            <span class="block text-xs font-mono text-slate-400 uppercase">// Horas Validadas</span>
                            <span id="stat-horas" class="block text-2xl font-bold mt-1 text-emerald-400">0.0h</span>
                        </div>
                        <div class="bg-slate-900/80 backdrop-blur-md border border-slate-800 p-4 rounded-xl">
                            <span class="block text-xs font-mono text-slate-400 uppercase">// Status Core</span>
                            <span class="block text-2xl font-bold mt-1 text-sky-400 font-mono text-sm uppercase">P2P_CLUSTER_OK</span>
                        </div>
                    </div>

                    <div id="tab-mural" class="tab-content">
                        
                        <div id="area-mentorado-postar" class="bg-slate-900/80 border border-slate-800 rounded-xl p-5 mb-6 hidden-section">
                            <h3 class="font-bold text-sm font-mono text-emerald-400 mb-3">// Abrir Ticket de Suporte (Fila P2P)</h3>
                            <div class="space-y-3">
                                <input type="text" id="post-titulo" class="w-full bg-slate-950 border border-slate-800 rounded-lg px-3 py-2 text-xs font-mono focus:outline-none focus:border-emerald-500" placeholder="Ex: Erro de segmentação ao ler ponteiro">
                                <textarea id="post-desc" rows="2" class="w-full bg-slate-9550 border border-slate-800 rounded-lg px-3 py-2 text-xs font-mono focus:outline-none focus:border-emerald-500" placeholder="Explique sucintamente a trava do seu código..."></textarea>
                                <div class="flex justify-between items-center">
                                    <select id="post-tag" class="bg-slate-950 border border-slate-800 rounded px-2 py-1 text-xs font-mono text-slate-300">
                                        <option value="LOGICA">LOGICA</option>
                                        <option value="WEB">WEB</option>
                                        <option value="SQL">SQL</option>
                                    </select>
                                    <button onclick="criarChamadoPeloMentorado()" class="bg-emerald-500 hover:bg-emerald-600 text-slate-950 font-mono font-bold text-xs px-4 py-2 rounded-lg cursor-pointer">INJETAR_NO_FEED ></button>
                                </div>
                            </div>
                        </div>

                        <div class="mb-4 bg-slate-900/60 border border-slate-800 rounded-lg p-3 flex items-center justify-between">
                            <span class="text-xs font-mono text-slate-400">// Filtragem Algorítmica por Tag:</span>
                            <div class="flex space-x-2">
                                <button onclick="filtrarMural('TODOS')" class="bg-slate-800 text-slate-100 px-2.5 py-1 rounded font-mono text-[11px] font-bold cursor-pointer" id="filter-btn-TODOS">Todos</button>
                                <button onclick="filtrarMural('LOGICA')" class="bg-slate-950 text-slate-400 hover:text-slate-200 px-2.5 py-1 rounded font-mono text-[11px] cursor-pointer" id="filter-btn-LOGICA">Lógica</button>
                                <button onclick="filtrarMural('WEB')" class="bg-slate-950 text-slate-400 hover:text-slate-200 px-2.5 py-1 rounded font-mono text-[11px] cursor-pointer" id="filter-btn-WEB">Web</button>
                                <button onclick="filtrarMural('SQL')" class="bg-slate-950 text-slate-400 hover:text-slate-200 px-2.5 py-1 rounded font-mono text-[11px] cursor-pointer" id="filter-btn-SQL">SQL</button>
                            </div>
                        </div>

                        <div id="mural-cards-container" class="space-y-4">
                            </div>

                        <div id="mural-vazio" class="hidden-section text-center py-12 bg-slate-900/30 border border-dashed border-slate-800 rounded-xl">
                            <span class="font-mono text-slate-500 text-sm">// Fila limpa! Todos os chamados do bloco foram indexados ou resolvidos.</span>
                        </div>
                    </div>

                    <div id="tab-conquistas" class="tab-content hidden-section">
                        <div class="mb-6">
                            <h2 class="text-2xl font-bold tracking-tight">Suas Conquistas Acadêmicas</h2>
                            <p class="text-slate-400 text-sm mt-1">Ganhos em formato de insígnias para validação de atividades complementares.</p>
                        </div>
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div class="bg-slate-900/80 border border-slate-800 rounded-xl p-5 flex items-center space-x-4">
                                <div class="bg-emerald-500/20 text-emerald-400 p-3 rounded-full text-2xl font-bold border border-emerald-500/30">⚡</div>
                                <div>
                                    <h4 class="font-bold text-slate-200 text-sm">Perfil Ativo</h4>
                                    <p class="text-slate-400 text-xs mt-0.5">Fez a triagem inicial com sucesso no sistema.</p>
                                    <span class="inline-block mt-2 font-mono text-[10px] text-emerald-400 bg-emerald-950/50 px-2 py-0.5 rounded border border-emerald-500/20">CONQUISTADO</span>
                                </div>
                            </div>
                            <div id="badge-mentor" class="bg-slate-900/30 border border-slate-950 rounded-xl p-5 flex items-center space-x-4 opacity-50 transition-all duration-300">
                                <div id="badge-icon-mentor" class="bg-slate-800 text-slate-500 p-3 rounded-full text-2xl font-bold border border-slate-700">🎓</div>
                                <div>
                                    <h4 class="font-bold text-slate-200 text-sm">Mentor Certificado</h4>
                                    <p class="text-slate-400 text-xs mt-0.5">Conclua pelo menos 1 atendimento completo via chat P2P.</p>
                                    <span id="badge-status-mentor" class="inline-block mt-2 font-mono text-[10px] text-slate-500 bg-slate-950 px-2 py-0.5 rounded border border-slate-800">BLOQUEADO</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div id="tab-ranking" class="tab-content hidden-section">
                        <div class="mb-6">
                            <h2 class="text-2xl font-bold tracking-tight">Ranking Geral da Unidade</h2>
                            <p class="text-slate-400 text-sm mt-1">Gamificação activa estimulando a colaboração discente contínua.</p>
                        </div>
                        <div class="bg-slate-900/80 border border-slate-800 rounded-xl overflow-hidden shadow-lg font-mono text-sm">
                            <div class="grid grid-cols-12 bg-slate-950/40 p-4 border-b border-slate-800 text-slate-400 font-bold text-xs uppercase tracking-wider">
                                <div class="col-span-2">Posição</div>
                                <div class="col-span-6">Estudante</div>
                                <div class="col-span-2 text-center">Nível</div>
                                <div class="col-span-2 text-right">Total XP</div>
                            </div>
                            <div class="divide-y divide-slate-800/50" id="ranking-list-container">
                                </div>
                        </div>
                    </div>
                </div>

                <div class="mt-8 border border-slate-800 bg-slate-950/90 rounded-lg p-3 font-mono text-[10px] text-slate-400 shadow-inner">
                    <div class="flex justify-between items-center text-slate-500 border-b border-slate-800 pb-1.5 mb-1.5 uppercase tracking-wider font-bold">
                        <span>Console de Auditoria do Sistema (State Monitor)</span>
                        <span class="text-emerald-500 animate-pulse">● ENGINE_READY</span>
                    </div>
                    <div id="system-logs-container" class="space-y-0.5 max-h-[60px] overflow-y-auto">
                        <div>[SYSTEM]: Inicializando estruturas de dados locais...</div>
                    </div>
                </div>

            </main>
        </div>
    </div>

    <script>
        // BANCO DE DADOS MOCK INICIAL
        const defaultCards = [
            { id: 1, titulo: "Dúvida com laço condicional While", desc: "Meu loop está ficando infinito quando digito a opção de sair do menu no terminal. Alguém pode me ajudar a debugar?", tag: "LOGICA", autor: "Aluno_Novo99", xp: 50 },
            { id: 2, titulo: "Como alinhar duas caixas flexbox lado a lado?", desc: "Estou tentando fazer a barra do meu site de roupas mas os itens ficam empilhados verticalmente.", tag: "WEB", autor: "DevIniciante_", xp: 30 },
            { id: 3, titulo: "Múltiplos JOINS travando a query", desc: "Tenho 3 tabelas relacionadas e ao rodar a query o terminal do MySQL trava.", tag: "SQL", autor: "BancoData_00", xp: 40 }
        ];

        let appState = {
            userName: "Isaías Alves",
            role: "MENTOR",
            currentXp: 20,
            level: 1,
            xpToNextLevel: 100,
            resolvedCount: 0,
            simulatedIdCounter: 20,
            cards: [...defaultCards],
            currentFilter: "TODOS"
        };

        // CONTROLE LOCALSTORAGE (PERSISTÊNCIA)
        if(localStorage.getItem('codematch_state')) {
            try {
                appState = JSON.parse(localStorage.getItem('codematch_state'));
                setTimeout(() => {
                    document.getElementById('screen-cadastro').classList.add('hidden-section');
                    document.getElementById('screen-dashboard').classList.remove('hidden-section');
                    inicializarDashboardLayout();
                }, 10);
            } catch(e) { console.error(e); }
        }

        function saveStateToCache() {
            localStorage.setItem('codematch_state', JSON.stringify(appState));
        }

        function logEvent(msg) {
            const container = document.getElementById('system-logs-container');
            const time = new Date().toLocaleTimeString();
            const log = document.createElement('div');
            log.innerHTML = `<span class="text-slate-600">[${time}]</span> <span class="text-amber-400/90">${msg}</span>`;
            container.appendChild(log);
            container.scrollTop = container.scrollHeight;
        }

        function avancarParaDashboard() {
            const nomeDigitado = document.getElementById('input-nome').value.trim();
            const nivelSelecionado = document.querySelector('input[name="nivel"]:checked').value;
            
            if(nomeDigitado) appState.userName = nomeDigitado;
            appState.role = nivelSelecionado === 'iniciante' ? "MENTORADO" : "MENTOR";
            appState.currentXp = nivelSelecionado === 'iniciante' ? 0 : 20;

            logEvent(`Perfil criado: ${appState.userName} registrado como ${appState.role}.`);
            inicializarDashboardLayout();

            document.getElementById('screen-cadastro').classList.add('hidden-section');
            document.getElementById('screen-dashboard').classList.remove('hidden-section');
            showToast("✓ Triagem executada. Estado persistido.");
        }

        function inicializarDashboardLayout() {
            document.getElementById('user-display-name').innerText = appState.userName;
            const roleElement = document.getElementById('user-role');
            roleElement.innerText = appState.role;
            
            if(appState.role === "MENTORADO") {
                roleElement.className = "block font-mono text-[10px] uppercase tracking-wider text-sky-400";
                document.getElementById('area-mentorado-postar').classList.remove('hidden-section');
            } else {
                roleElement.className = "block font-mono text-[10px] uppercase tracking-wider text-emerald-400";
                document.getElementById('area-mentorado-postar').classList.add('hidden-section');
            }

            renderMuralCards();
            atualizarLayoutEstado();
            renderRanking();
        }

        function renderMuralCards() {
            const container = document.getElementById('mural-cards-container');
            container.innerHTML = "";

            const filtered = appState.cards.filter(c => appState.currentFilter === "TODOS" || c.tag === appState.currentFilter);

            if(filtered.length === 0) {
                document.getElementById('mural-vazio').classList.remove('hidden-section');
                return;
            } else {
                document.getElementById('mural-vazio').classList.add('hidden-section');
            }

            filtered.forEach(card => {
                const badgeColor = card.tag === 'LOGICA' ? 'bg-amber-500/10 text-amber-400 border-amber-500/20' : 
                                   card.tag === 'WEB' ? 'bg-purple-500/10 text-purple-400 border-purple-500/20' : 
                                   'bg-blue-500/10 text-blue-400 border-blue-500/20';

                const cardHtml = `
                    <div id="card-${card.id}" class="bg-slate-900/90 border border-slate-800 backdrop-blur-md rounded-xl p-6 hover:border-slate-700 transition-all shadow-md">
                        <div class="flex justify-between items-start mb-3">
                            <div class="flex items-center space-x-2">
                                <span class="text-xs font-mono px-2 py-0.5 rounded border ${badgeColor}">${card.tag}</span>
                            </div>
                            <span class="font-mono text-xs text-amber-400 font-bold">+${card.xp} XP</span>
                        </div>
                        <h3 class="font-semibold text-base text-slate-200">${card.titulo}</h3>
                        <p class="text-slate-400 text-sm mt-1">"${card.desc}"</p>
                        <div class="mt-4 pt-4 border-t border-slate-800/50 flex justify-between items-center text-xs text-slate-500">
                            <span>Postado por: <strong class="text-slate-400 font-mono font-normal">${card.autor}</strong></span>
                            ${appState.role === 'MENTOR' ? `
                                <button onclick="abrirMentoria(${card.id}, '${card.titulo}', '${card.desc}', '${card.tag}', ${card.xp})" class="bg-emerald-500/10 text-emerald-400 font-mono px-3 py-1.5 rounded-md hover:bg-emerald-500 hover:text-slate-950 font-bold transition-all cursor-pointer">
                                    ACEITAR_CHAMADO >
                                </button>
                            ` : `<span class="text-sky-400 font-mono">Fila Global</span>`}
                        </div>
                    </div>
                `;
                container.insertAdjacentHTML('beforeend', cardHtml);
            });
        }

        function filtrarMural(tag) {
            appState.currentFilter = tag;
            document.querySelectorAll('[id^="filter-btn-"]').forEach(btn => {
                btn.className = "bg-slate-950 text-slate-400 hover:text-slate-200 px-2.5 py-1 rounded font-mono text-[11px] cursor-pointer";
            });
            document.getElementById('filter-btn-' + tag).className = "bg-slate-800 text-slate-100 px-2.5 py-1 rounded font-mono text-[11px] font-bold cursor-pointer";
            renderMuralCards();
        }

        function criarChamadoPeloMentorado() {
            const tit = document.getElementById('post-titulo').value.trim();
            const dsc = document.getElementById('post-desc').value.trim();
            const tag = document.getElementById('post-tag').value;

            if(!tit || !dsc) {
                showToast("⚠ Preencha o formulário antes!");
                return;
            }

            appState.simulatedIdCounter++;
            const newCard = {
                id: appState.simulatedIdCounter,
                titulo: tit,
                desc: dsc,
                tag: tag,
                autor: "Você",
                xp: Math.floor(Math.random() * 30) + 30
            };

            appState.cards.unshift(newCard);
            document.getElementById('post-titulo').value = "";
            document.getElementById('post-desc').value = "";
            
            logEvent(`Match Pipeline: Novo chamado mapeado [ID ${newCard.id}]`);
            saveStateToCache();
            renderMuralCards();
            showToast("✓ Injetado na fila de monitoria!");
        }

        function atualizarLayoutEstado() {
            document.getElementById('user-level').innerText = appState.level < 10 ? "0" + appState.level : appState.level;
            let percent = (appState.currentXp / appState.xpToNextLevel) * 100;
            document.getElementById('xp-bar').style.width = (percent > 100 ? 100 : percent) + "%";
            document.getElementById('user-xp-text').innerText = appState.currentXp + "/" + appState.xpToNextLevel + " XP";

            document.getElementById('stat-atendimentos').innerText = appState.resolvedCount;
            document.getElementById('stat-horas').innerText = (appState.resolvedCount * 0.5).toFixed(1) + "h";

            if(appState.resolvedCount >= 1) {
                const badge = document.getElementById('badge-mentor');
                badge.classList.remove('opacity-50', 'bg-slate-900/30');
                badge.classList.add('bg-slate-900', 'border-emerald-500/30');
                document.getElementById('badge-icon-mentor').className = "bg-emerald-500/20 text-emerald-400 p-3 rounded-full text-2xl font-bold border border-emerald-500/30";
                document.getElementById('badge-status-mentor').className = "inline-block mt-2 font-mono text-[10px] text-emerald-400 bg-emerald-950/50 px-2 py-0.5 rounded border border-emerald-500/20";
                document.getElementById('badge-status-mentor').innerText = "CONQUISTADO";
            }
            saveStateToCache();
        }

        function renderRanking() {
            const container = document.getElementById('ranking-list-container');
            let rankingData = [
                { nome: "Guilherme Santos (Veterano)", lvl: 8, xp: 1450, isUser: false },
                { nome: appState.userName + " (Você)", lvl: appState.level, xp: appState.currentXp + (appState.level * 100) - 100, isUser: true }
            ];

            rankingData.sort((a, b) => b.xp - a.xp);
            container.innerHTML = "";
            rankingData.forEach((row, index) => {
                const posStr = index === 0 ? "#1 🥇" : "#2 🥈";
                const bgClass = row.isUser ? "bg-emerald-500/10 border-y border-emerald-500/20" : "bg-amber-500/5";
                const textClass = row.isUser ? "font-bold text-emerald-400" : "font-semibold text-slate-200";

                const html = `
                    <div class="grid grid-cols-12 p-4 items-center ${bgClass}">
                        <div class="col-span-2 font-bold ${row.isUser ? 'text-emerald-400' : 'text-amber-400'}">${posStr}</div>
                        <div class="col-span-6 font-sans ${textClass}">${row.nome}</div>
                        <div class="col-span-2 text-center font-bold ${row.isUser ? 'text-emerald-400' : 'text-amber-400'}">Lvl 0${row.lvl}</div>
                        <div class="col-span-2 text-right font-bold ${row.isUser ? 'text-emerald-400' : 'text-slate-300'}">${row.xp} XP</div>
                    </div>
                `;
                container.insertAdjacentHTML('beforeend', html);
            });
        }

        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden-section'));
            document.getElementById('btn-tab-mural').className = "w-full text-left flex items-center space-x-3 text-slate-400 hover:text-slate-200 p-3 rounded-lg font-mono text-sm cursor-pointer transition-all";
            document.getElementById('btn-tab-conquistas').className = "w-full text-left flex items-center space-x-3 text-slate-400 hover:text-slate-200 p-3 rounded-lg font-mono text-sm cursor-pointer transition-all";
            document.getElementById('btn-tab-ranking').className = "w-full text-left flex items-center space-x-3 text-slate-400 hover:text-slate-200 p-3 rounded-lg font-mono text-sm cursor-pointer transition-all";

            if(tabName === 'mural') {
                document.getElementById('tab-mural').classList.remove('hidden-section');
                document.getElementById('btn-tab-mural').className = "w-full text-left flex items-center space-x-3 bg-slate-900 border border-slate-800 text-emerald-400 p-3 rounded-lg font-mono text-sm border-l-2 border-emerald-400 font-medium cursor-pointer transition-all";
            } else if(tabName === 'conquistas') {
                document.getElementById('tab-conquistas').classList.remove('hidden-section');
                document.getElementById('btn-tab-conquistas').className = "w-full text-left flex items-center space-x-3 bg-slate-900 border border-slate-800 text-emerald-400 p-3 rounded-lg font-mono text-sm border-l-2 border-emerald-400 font-medium cursor-pointer transition-all";
            } else if(tabName === 'ranking') {
                document.getElementById('tab-ranking').classList.remove('hidden-section');
                document.getElementById('btn-tab-ranking').className = "w-full text-left flex items-center space-x-3 bg-slate-900 border border-slate-800 text-emerald-400 p-3 rounded-lg font-mono text-sm border-l-2 border-emerald-400 font-medium cursor-pointer transition-all";
                renderRanking();
            }
        }

        function abrirMentoria(id, titulo, descricao, tag, xp) {
            appState.activeMentoriaId = id;
            appState.activeMentoriaXp = xp;
            document.getElementById('chat-tag').innerText = tag;
            document.getElementById('chat-title').innerText = titulo;
            document.getElementById('chat-desc').innerText = descricao;

            const chatMessages = document.getElementById('chat-messages');
            chatMessages.innerHTML = `
                <div class="bg-slate-950/50 p-3 rounded border border-slate-800/50">
                    <span class="text-sky-400 font-bold">[ALUNO]:</span> <span>${descricao}</span>
                </div>
                <div class="text-center text-[10px] text-slate-500 py-1">// Handshake P2P WebRTC estabelecido.</div>
            `;
            document.getElementById('modal-chat').classList.remove('hidden-section');
        }

        function fecharChat() {
            document.getElementById('modal-chat').classList.add('hidden-section');
        }

        function enviarMensagem() {
            const input = document.getElementById('chat-input');
            const msg = input.value.trim();
            if(!msg) return;

            const chatMessages = document.getElementById('chat-messages');
            const node = document.createElement('div');
            node.className = "bg-emerald-950/30 p-3 rounded border border-emerald-500/20 text-right";
            node.innerHTML = `<span class="text-emerald-400 font-bold">[VOCÊ]:</span> <span>${msg}</span>`;
            chatMessages.appendChild(node);
            input.value = "";
            chatMessages.scrollTop = chatMessages.scrollHeight;

            setTimeout(() => {
                const rep = document.createElement('div');
                rep.className = "bg-slate-950/50 p-3 rounded border border-slate-800/50";
                rep.innerHTML = `<span class="text-sky-400 font-bold">[ALUNO]:</span> <span>Entendi perfeitamente a lógica, mestre! Rodou aqui.</span>`;
                chatMessages.appendChild(rep);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }, 900);
        }

        function finalizarMentoria() {
            const cardId = appState.activeMentoriaId;
            appState.currentXp += appState.activeMentoriaXp;
            appState.resolvedCount += 1;

            document.getElementById('modal-chat').classList.add('hidden-section');
            appState.cards = appState.cards.filter(c => c.id !== cardId);

            let lvlUp = false;
            if(appState.currentXp >= appState.xpToNextLevel) {
                appState.level += 1;
                appState.currentXp -= appState.xpToNextLevel;
                lvlUp = true;
            }

            logEvent(`Atendimento concluído. Transação: +${appState.activeMentoriaXp} XP computados.`);
            renderMuralCards();
            atualizarLayoutEstado();
            renderRanking();
            showToast(lvlUp ? `🔥 LEVEL UP! Chegou ao Nível ${appState.level}!` : `+${appState.activeMentoriaXp} XP creditados!`);
        }

        function limparSessao() {
            localStorage.removeItem('codematch_state');
            window.location.reload();
        }

        function showToast(m) {
            const t = document.getElementById('toast');
            document.getElementById('toast-message').innerText = m;
            t.classList.remove('translate-y-20', 'opacity-0');
            t.classList.add('translate-y-0', 'opacity-100');
            setTimeout(() => {
                t.classList.remove('translate-y-0', 'opacity-100');
                t.classList.add('translate-y-20', 'opacity-0');
            }, 3000);
        }

        // ================= ENGINE DE ANIMAÇÃO DO CANVAS (FONTE BINÁRIA HACKER) =================
        const canvas = document.getElementById('tech-bg-canvas');
        const ctx = canvas.getContext('2d');

        // Redimensiona para cobrir a janela inteira
        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Configuração dos caracteres binários caindo
        const binaryChars = ['0', '1'];
        const fontSize = 14;
        const columns = canvas.width / fontSize;
        const drops = Array(Math.floor(columns)).fill(1);

        function drawBinaryMatrixAnimation() {
            // Cria um rastro sutil limpando a tela aos poucos
            ctx.fillStyle = 'rgba(10, 15, 30, 0.05)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Cor verde-esmeralda tecnológica para as letras
            ctx.fillStyle = '#10b981';
            ctx.font = fontSize + 'px "JetBrains Mono", monospace';

            for (let i = 0; i < drops.length; i++) {
                // Escolhe aleatoriamente entre 0 ou 1
                const text = binaryChars[Math.floor(Math.random() * binaryChars.length)];
                const x = i * fontSize;
                const y = drops[i] * fontSize;

                ctx.fillText(text, x, y);

                // Se o caractere chegou no final da tela, joga de volta pro topo aleatoriamente
                if (y > canvas.height && Math.random() * 100 > 97.5) {
                    drops[i] = 0;
                }
                drops[i]++;
            }
        }
        // Roda a animação a 30 quadros por segundo para ficar fluida e leve
        setInterval(drawBinaryMatrixAnimation, 33);
    </script>
</body>
</html>
