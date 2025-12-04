# Simulador-IRPF-M-nimo-2025
Simulador IRPF Mínimo 2025
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora IRPF Mínimo 2025 (Lei 15.270)</title>
    
    <!-- Tailwind CSS para Estilo -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Ícones Lucide -->
    <script src="https://unpkg.com/lucide@latest"></script>
    
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .card { transition: all 0.2s; }
        .card:hover { transform: translateY(-2px); box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); }
    </style>
</head>
<body class="text-slate-800">

    <div class="max-w-6xl mx-auto p-4 md:p-8 space-y-6">
        
        <!-- CABEÇALHO -->
        <header class="bg-white p-6 rounded-xl shadow-sm border border-slate-200 flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-indigo-900 flex items-center gap-2">
                    <i data-lucide="calculator" class="w-8 h-8 text-indigo-600"></i>
                    Simulador IRPF Mínimo 2025
                </h1>
                <p class="text-slate-500 mt-1 text-sm">Baseado na Lei 15.270/2025 (Art. 16-A e 16-B)</p>
            </div>
            <div class="flex gap-2">
                <button onclick="app.toggleLogs()" class="px-4 py-2 text-sm font-medium text-slate-600 bg-slate-100 rounded-lg hover:bg-slate-200 transition flex items-center gap-2">
                    <i data-lucide="file-text" class="w-4 h-4"></i>
                    <span id="btnLogText">Ver Logs</span>
                </button>
                <button onclick="window.print()" class="px-4 py-2 text-sm font-medium text-white bg-indigo-600 rounded-lg hover:bg-indigo-700 transition flex items-center gap-2">
                    <i data-lucide="printer" class="w-4 h-4"></i>
                    Imprimir/PDF
                </button>
            </div>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            
            <!-- COLUNA DA ESQUERDA: INPUTS -->
            <div class="lg:col-span-2 space-y-6">
                
                <!-- Lista de Fontes -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                    <div class="p-4 bg-slate-50 border-b border-slate-200 flex justify-between items-center">
                        <h2 class="font-semibold text-slate-700 flex items-center gap-2">
                            <i data-lucide="plus-circle" class="w-5 h-5"></i>
                            Fontes de Renda
                        </h2>
                        <button onclick="app.addIncome()" class="text-indigo-600 hover:text-indigo-800 text-sm font-bold flex items-center gap-1">
                            + Adicionar
                        </button>
                    </div>

                    <div id="incomes-list" class="p-4 space-y-4">
                        <!-- Os itens serão renderizados aqui via JS -->
                    </div>
                    
                    <div id="empty-state" class="hidden text-center py-10 text-slate-400">
                        Nenhuma fonte adicionada. Clique em adicionar.
                    </div>
                </div>

                <!-- Painel de Logs -->
                <div id="logs-panel" class="hidden bg-slate-800 text-slate-200 p-6 rounded-xl font-mono text-xs shadow-inner h-64 overflow-y-auto">
                    <h3 class="text-slate-400 font-bold mb-4 uppercase flex items-center gap-2 sticky top-0 bg-slate-800 pb-2">
                        <i data-lucide="terminal" class="w-4 h-4"></i>
                        Log de Processamento
                    </h3>
                    <ul id="logs-list" class="space-y-2"></ul>
                </div>

            </div>

            <!-- COLUNA DA DIREITA: RESULTADOS -->
            <div class="space-y-6">
                <div class="bg-white p-6 rounded-xl shadow-lg border border-indigo-100 sticky top-6">
                    <h2 class="text-xl font-bold text-slate-800 mb-6 flex items-center gap-2">
                        <i data-lucide="pie-chart" class="w-5 h-5 text-indigo-500"></i>
                        Resultado Consolidado
                    </h2>

                    <div class="space-y-4">
                        <!-- Renda Total -->
                        <div class="flex justify-between items-center pb-2 border-b border-slate-100">
                            <span class="text-sm text-slate-500">Renda Total Declarada</span>
                            <span class="font-mono font-medium" id="res-total-income">R$ 0,00</span>
                        </div>

                        <!-- Base Mínima -->
                        <div class="flex justify-between items-center pb-2 border-b border-slate-100">
                            <span class="text-sm text-slate-500">Base IRPF Mínimo</span>
                            <span class="font-mono font-bold text-slate-700" id="res-base-min">R$ 0,00</span>
                        </div>

                        <!-- Alíquota Efetiva -->
                        <div class="flex justify-between items-center pb-2 border-b border-slate-100">
                            <span class="text-sm text-slate-500">Alíquota Efetiva Calc.</span>
                            <span class="font-mono font-bold px-2 py-0.5 rounded text-sm bg-slate-100 text-slate-500" id="res-rate">0,00%</span>
                        </div>

                        <!-- Imposto Bruto -->
                        <div class="flex justify-between items-center pt-2">
                            <span class="text-sm text-slate-600">Imposto Mínimo Bruto</span>
                            <span class="font-mono text-slate-600" id="res-gross-tax">R$ 0,00</span>
                        </div>

                        <!-- Redutor -->
                        <div id="row-reducer" class="flex justify-between items-center text-green-600 hidden">
                            <span class="text-sm flex items-center gap-1">(-) Redutor (Art. 16-B)</span>
                            <span class="font-mono" id="res-reducer">-R$ 0,00</span>
                        </div>

                        <!-- Resumo Final -->
                        <div class="bg-slate-50 p-3 rounded-lg border border-slate-200 mt-2">
                            <div class="flex justify-between items-center mb-2">
                                <span class="text-xs font-bold text-slate-500 uppercase">Imposto Mínimo Devido</span>
                                <span class="font-mono font-bold text-slate-800" id="res-net-tax">R$ 0,00</span>
                            </div>
                            <div class="flex justify-between items-center text-slate-500">
                                <span class="text-xs">(-) Imposto Já Pago</span>
                                <span class="font-mono text-sm" id="res-paid-tax">-R$ 0,00</span>
                            </div>
                        </div>

                        <!-- Caixa de Destaque -->
                        <div id="box-result" class="mt-6 p-4 rounded-xl text-center border-2 bg-green-50 border-green-100 transition-colors">
                            <span id="label-result" class="block text-xs font-bold uppercase tracking-wider mb-1 text-slate-500">
                                Situação Regular
                            </span>
                            <span id="val-result" class="text-2xl font-black text-green-600">
                                R$ 0,00
                            </span>
                            <span id="msg-result" class="block text-xs text-green-700 mt-2 hidden">
                                Você pagou a mais do que o mínimo.
                            </span>
                        </div>
                    </div>
                </div>
            </div>

        </div>

        <!-- TEMPLATE DO ITEM (HIDDEN) -->
        <template id="income-item-template">
            <div class="card bg-slate-50 p-4 rounded-lg border border-slate-200 relative group mb-4">
                <button class="btn-remove absolute top-2 right-2 text-slate-400 hover:text-red-500 transition p-1" title="Remover">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                </button>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4 pr-6">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 uppercase mb-1">Descrição</label>
                        <input type="text" class="inp-desc w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 outline-none text-sm" placeholder="Ex: Salário, Dividendos...">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 uppercase mb-1">Tipo</label>
                        <select class="inp-type w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 outline-none text-sm bg-white">
                            <option value="salary">Salário / Pró-Labore</option>
                            <option value="dividend">Lucros e Dividendos</option>
                            <option value="capital_gain">Ganho de Capital</option>
                            <option value="savings">Poupança / LCI / LCA (Isento)</option>
                            <option value="other">Outros Rendimentos</option>
                        </select>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 uppercase mb-1">Valor Anual Bruto</label>
                        <input type="number" class="inp-value w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 outline-none text-sm font-mono" value="0">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 uppercase mb-1">IR Já Pago/Retido</label>
                        <input type="number" class="inp-tax w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 outline-none text-sm font-mono" value="0">
                    </div>
                    <div class="flex items-center pt-5">
                        <span class="badge-status text-xs px-2 py-1 rounded-full border bg-green-100 text-green-700 border-green-200">
                            Compõe Base
                        </span>
                    </div>
                </div>

                <!-- Área do Redutor (Dividendos) -->
                <div class="area-reducer hidden mt-4 pt-4 border-t border-slate-200 bg-indigo-50/50 -mx-4 -mb-4 p-4 rounded-b-lg">
                    <div class="flex items-center gap-2 mb-2 text-indigo-800">
                        <i data-lucide="info" class="w-4 h-4"></i>
                        <span class="text-xs font-bold uppercase">Dados para Redutor (Art. 16-B)</span>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs text-indigo-700 mb-1">Carga Efetiva PJ (Decimal)</label>
                            <input type="number" step="0.01" class="inp-corp-rate w-full p-2 border border-indigo-200 rounded text-sm" value="0.34" placeholder="0.34">
                        </div>
                        <div>
                            <label class="block text-xs text-indigo-700 mb-1">Teto Global (Setor)</label>
                            <select class="inp-corp-limit w-full p-2 border border-indigo-200 rounded text-sm bg-white">
                                <option value="0.34">34% (Geral)</option>
                                <option value="0.40">40% (Seguradoras)</option>
                                <option value="0.45">45% (Bancos)</option>
                            </select>
                        </div>
                    </div>
                </div>
            </div>
        </template>

    </div>

    <!-- LÓGICA DA APLICAÇÃO -->
    <script>
        const CONFIG = {
            MIN_THRESHOLD: 600000,
            MAX_THRESHOLD: 1200000,
            MAX_RATE: 0.10
        };

        const INCOME_TYPES = {
            'salary': { include: true, isDividend: false },
            'dividend': { include: true, isDividend: true },
            'capital_gain': { include: true, isDividend: false },
            'savings': { include: false, isDividend: false },
            'other': { include: true, isDividend: false }
        };

        const app = {
            incomes: [],
            
            init() {
                this.addIncome({ type: 'salary', desc: 'Salário Exemplo', val: 300000, tax: 75000 });
                this.render();
                lucide.createIcons();
            },

            addIncome(defaults = null) {
                const id = Date.now();
                this.incomes.push({
                    id,
                    type: defaults?.type || 'dividend',
                    description: defaults?.desc || '',
                    value: defaults?.val || 0,
                    taxPaid: defaults?.tax || 0,
                    corpRate: 0.34,
                    corpLimit: 0.34
                });
                this.render();
            },

            removeIncome(id) {
                this.incomes = this.incomes.filter(i => i.id !== id);
                this.render();
            },

            updateIncome(id, field, value) {
                const idx = this.incomes.findIndex(i => i.id === id);
                if (idx === -1) return;
                
                // Conversão de tipos
                if (field === 'value' || field === 'taxPaid' || field === 'corpRate' || field === 'corpLimit') {
                    value = parseFloat(value) || 0;
                }
                
                this.incomes[idx][field] = value;
                this.calculate(); // Recalcula valores
                this.updateUIValues(); // Atualiza UI de resultados apenas, sem redesenhar lista para não perder foco
            },

            render() {
                const list = document.getElementById('incomes-list');
                const template = document.getElementById('income-item-template');
                
                list.innerHTML = '';
                
                if (this.incomes.length === 0) {
                    document.getElementById('empty-state').classList.remove('hidden');
                } else {
                    document.getElementById('empty-state').classList.add('hidden');
                    
                    this.incomes.forEach(inc => {
                        const clone = template.content.cloneNode(true);
                        const rules = INCOME_TYPES[inc.type];

                        // Set values
                        const elDesc = clone.querySelector('.inp-desc');
                        elDesc.value = inc.description;
                        elDesc.oninput = (e) => this.updateIncome(inc.id, 'description', e.target.value);

                        const elType = clone.querySelector('.inp-type');
                        elType.value = inc.type;
                        elType.onchange = (e) => {
                            this.updateIncome(inc.id, 'type', e.target.value);
                            this.render(); // Re-render needed to show/hide reducer fields
                        };

                        const elVal = clone.querySelector('.inp-value');
                        elVal.value = inc.value;
                        elVal.oninput = (e) => this.updateIncome(inc.id, 'value', e.target.value);

                        const elTax = clone.querySelector('.inp-tax');
                        elTax.value = inc.taxPaid;
                        elTax.oninput = (e) => this.updateIncome(inc.id, 'taxPaid', e.target.value);

                        const btnRemove = clone.querySelector('.btn-remove');
                        btnRemove.onclick = () => this.removeIncome(inc.id);

                        // Badge Status
                        const badge = clone.querySelector('.badge-status');
                        if (rules.include) {
                            badge.className = 'badge-status text-xs px-2 py-1 rounded-full border bg-green-100 text-green-700 border-green-200';
                            badge.textContent = 'Compõe Base Mínima';
                        } else {
                            badge.className = 'badge-status text-xs px-2 py-1 rounded-full border bg-amber-100 text-amber-700 border-amber-200';
                            badge.textContent = 'Não Compõe Base';
                        }

                        // Reducer Area
                        const reducerArea = clone.querySelector('.area-reducer');
                        if (rules.isDividend) {
                            reducerArea.classList.remove('hidden');
                            
                            const elCorpRate = clone.querySelector('.inp-corp-rate');
                            elCorpRate.value = inc.corpRate;
                            elCorpRate.oninput = (e) => this.updateIncome(inc.id, 'corpRate', e.target.value);
                            
                            const elCorpLimit = clone.querySelector('.inp-corp-limit');
                            elCorpLimit.value = inc.corpLimit;
                            elCorpLimit.onchange = (e) => this.updateIncome(inc.id, 'corpLimit', e.target.value);
                        }

                        list.appendChild(clone);
                    });
                }
                
                lucide.createIcons();
                this.calculate();
            },

            calculate() {
                let logs = [];
                let totalIncome = 0;
                let minTaxBase = 0;
                let regularTaxPaid = 0;

                // 1. Base Calc
                this.incomes.forEach(inc => {
                    const rules = INCOME_TYPES[inc.type];
                    totalIncome += inc.value;
                    regularTaxPaid += inc.taxPaid;
                    
                    if (rules.include) {
                        minTaxBase += inc.value;
                        logs.push(`[FONTE] ${inc.description || 'S/ Nome'}: R$ ${inc.value.toLocaleString('pt-BR')} -> Base Mínima.`);
                    } else {
                        logs.push(`[FONTE] ${inc.description || 'S/ Nome'}: R$ ${inc.value.toLocaleString('pt-BR')} -> Isento.`);
                    }
                });

                // 2. Rate Calc
                let minTaxRate = 0;
                if (minTaxBase <= CONFIG.MIN_THRESHOLD) {
                    minTaxRate = 0;
                    logs.push(`Base (${minTaxBase}) <= 600k. Alíquota 0%.`);
                } else if (minTaxBase >= CONFIG.MAX_THRESHOLD) {
                    minTaxRate = CONFIG.MAX_RATE;
                    logs.push(`Base (${minTaxBase}) >= 1.2M. Alíquota 10%.`);
                } else {
                    const excess = minTaxBase - CONFIG.MIN_THRESHOLD;
                    const range = CONFIG.MAX_THRESHOLD - CONFIG.MIN_THRESHOLD;
                    minTaxRate = (excess / range) * CONFIG.MAX_RATE;
                    logs.push(`Transição. Excesso ${excess}. Alíquota ${(minTaxRate*100).toFixed(4)}%.`);
                }

                const grossMinTax = minTaxBase * minTaxRate;

                // 3. Reducer
                let totalReducer = 0;
                this.incomes.forEach(inc => {
                    const rules = INCOME_TYPES[inc.type];
                    if (rules.isDividend && minTaxRate > 0) {
                        const totalLoad = inc.corpRate + minTaxRate;
                        if (totalLoad > inc.corpLimit) {
                            const excessRate = totalLoad - inc.corpLimit;
                            const red = inc.value * excessRate;
                            totalReducer += red;
                            logs.push(`[REDUTOR] ${inc.description}: Carga ${totalLoad.toFixed(4)} > ${inc.corpLimit}. Reduz R$ ${red.toFixed(2)}`);
                        }
                    }
                });

                const minTaxAfterReducer = Math.max(0, grossMinTax - totalReducer);
                const diff = minTaxAfterReducer - regularTaxPaid;
                const topUp = Math.max(0, diff);

                // Update UI Texts
                this.updateText('res-total-income', totalIncome);
                this.updateText('res-base-min', minTaxBase);
                document.getElementById('res-rate').textContent = (minTaxRate * 100).toFixed(2) + '%';
                if(minTaxRate > 0) {
                    document.getElementById('res-rate').className = "font-mono font-bold px-2 py-0.5 rounded text-sm bg-indigo-100 text-indigo-700";
                } else {
                    document.getElementById('res-rate').className = "font-mono font-bold px-2 py-0.5 rounded text-sm bg-slate-100 text-slate-500";
                }

                this.updateText('res-gross-tax', grossMinTax);
                
                const elRed = document.getElementById('row-reducer');
                if (totalReducer > 0) {
                    elRed.classList.remove('hidden');
                    document.getElementById('res-reducer').textContent = '- ' + totalReducer.toLocaleString('pt-BR', {style: 'currency', currency: 'BRL'});
                } else {
                    elRed.classList.add('hidden');
                }

                this.updateText('res-net-tax', minTaxAfterReducer);
                document.getElementById('res-paid-tax').textContent = '- ' + regularTaxPaid.toLocaleString('pt-BR', {style: 'currency', currency: 'BRL'});

                // Final Box
                const box = document.getElementById('box-result');
                const valRes = document.getElementById('val-result');
                const lblRes = document.getElementById('label-result');
                const msgRes = document.getElementById('msg-result');

                if (topUp > 0) {
                    box.className = "mt-6 p-4 rounded-xl text-center border-2 bg-red-50 border-red-100 transition-colors";
                    valRes.className = "text-2xl font-black text-red-600";
                    valRes.textContent = topUp.toLocaleString('pt-BR', {style: 'currency', currency: 'BRL'});
                    lblRes.textContent = "Complemento a Pagar";
                    msgRes.classList.add('hidden');
                } else {
                    box.className = "mt-6 p-4 rounded-xl text-center border-2 bg-green-50 border-green-100 transition-colors";
                    valRes.className = "text-2xl font-black text-green-600";
                    valRes.textContent = "R$ 0,00";
                    lblRes.textContent = "Situação Regular";
                    
                    if (regularTaxPaid > minTaxAfterReducer) {
                        msgRes.classList.remove('hidden');
                        const surplus = regularTaxPaid - minTaxAfterReducer;
                        msgRes.textContent = `Você pagou ${surplus.toLocaleString('pt-BR', {style:'currency', currency:'BRL'})} a mais que o mínimo.`;
                    } else {
                        msgRes.classList.add('hidden');
                    }
                }

                // Logs
                const logList = document.getElementById('logs-list');
                logList.innerHTML = logs.map(l => `<li class="border-l-2 border-slate-600 pl-3 py-1 hover:bg-slate-700/50">${l}</li>`).join('');
            },

            updateUIValues() {
                // Função auxiliar chamada no updateIncome para não perder foco do input
                // Na prática, o calculate já atualiza o DOM dos resultados.
                // Esta função existe para integridade futura se separarmos lógica.
            },

            updateText(id, val) {
                document.getElementById(id).textContent = val.toLocaleString('pt-BR', {style: 'currency', currency: 'BRL'});
            },

            toggleLogs() {
                const p = document.getElementById('logs-panel');
                p.classList.toggle('hidden');
                document.getElementById('btnLogText').textContent = p.classList.contains('hidden') ? 'Ver Logs' : 'Ocultar Logs';
            }
        };

        // Start
        window.onload = () => app.init();

    </script>
</body>
</html>
