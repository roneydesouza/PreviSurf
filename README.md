<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PreviSurf Caramujo - Previsão de Ondas</title>
    
<!-- Carrega o Tailwind CSS para estilização --><script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'surf-blue': '#219ebc', /* Azul do oceano */
                        'wave-green': '#8ecae6', /* Azul mais claro para as ondas */
                        'ocean-dark': '#023047', /* Azul bem escuro, quase preto, para títulos */
                        'sand-light': '#ffb703', /* Dourado da areia */
                        'leaf-green': '#fb8500', /* Laranja queimado para destaques */
                        'card-bg': 'rgba(255, 255, 255, 0.95)', /* Fundo dos cards com pouca transparência */
                    },
                    fontFamily: {
                        sans: ['"Bebas Neue"', 'sans-serif'], 
                        body: ['Inter', 'sans-serif'],
                        alkatra: ['Alkatra', 'cursive'], 
                        'original-surfer': ['"Original Surfer"', 'cursive'],
                    },
                }
            }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Alkatra:wght@400..700&family=Bebas+Neue&family=Inter:wght@100..900&family=Original+Surfer&display=swap');
        
        body {
            background-image: url('https://images.unsplash.com/photo-1502691506596-f3661159074b?q=80&w=1974&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 1rem;
        }

        .card {
            background-color: var(--tw-colors-card-bg);
            box-shadow: 0 15px 25px -5px rgba(0, 0, 0, 0.3), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
            transition: all 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
            border: 1px solid rgba(255, 255, 255, 0.3);
        }
        .card:hover {
            transform: scale(1.01);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
        }
        .loader {
            border-top-color: #0077b6;
            -webkit-animation: spinner 1.5s linear infinite;
            animation: spinner 1.5s linear infinite;
        }
        @keyframes spinner {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        h1 {
            letter-spacing: 0.1em;
            text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.6);
            line-height: 1;
        }
        .text-body {
            font-family: 'Inter', sans-serif;
        }
        .primary-data {
            font-size: 4.5rem;
            line-height: 1;
        }
    </style>
</head>
<body class="font-body text-gray-800">

    <!-- Configurações Firebase/App ID (NÃO REMOVER) --><script type="module">
        window.apiKey = typeof __api_key !== 'undefined' ? __api_key : '';
        window.GEMINI_MODEL = 'gemini-2.5-flash-preview-09-2025';
        window.API_BASE_URL = 'https://generativelanguage.googleapis.com/v1beta/models/';
    </script>

    <div class="w-full max-w-5xl mx-auto py-8">
        
        <header class="text-center mb-10 p-6 bg-ocean-dark text-white rounded-2xl shadow-2xl card border-none">
            <h1 class="text-7xl font-extrabold mb-2" style="color: #ffb703;">
                <span class="text-wave-green font-original-surfer">PREVI 🏄</span><span class="font-alkatra ml-2">SURF</span>
            </h1>
            <p class="text-xl italic mt-3 opacity-90 text-wave-green text-body">O Caramujo te ajuda a curtir o melhor do mar e do sol.</p>
        </header>

        <div class="mb-8 p-6 rounded-xl card shadow-xl">
            <label for="beach-select" class="block text-2xl font-semibold text-ocean-dark mb-4 text-body">
              Onde quer passar o dia hoje? 🏝️
            </label>
            <div class="relative">
                <select id="beach-select" class="block w-full px-5 py-3 text-xl text-ocean-dark bg-wave-green border border-surf-blue rounded-lg appearance-none focus:outline-none focus:ring-4 focus:ring-leaf-green focus:border-transparent cursor-pointer text-body shadow-inner font-bold">
                    <option value="" disabled selected>-- Escolha seu destino --</option>
                    
                    <optgroup label="Bertioga">
                        <option value="Boracéia (Bertioga)">Boracéia</option>
                        <option value="Enseada (Bertioga)">Enseada</option>
                        <option value="Guaratuba (Bertioga)">Guaratuba</option>
                        <option value="Indaiá (Bertioga)">Indaiá</option>
                        <option value="Itaguaré (Bertioga)">Itaguaré</option>
                        <option value="Riviera de São Lourenço (Bertioga)">Riviera de São Lourenço</option>
                    </optgroup>

                    <optgroup label="Cananéia">
                        <option value="Ilha do Cardoso (Cananéia)">Ilha do Cardoso</option>
                        <option value="Marujá (Cananéia)">Marujá</option>
                    </optgroup>

                    <optgroup label="Caraguatatuba">
                        <option value="Centro (Caraguatatuba)">Centro</option>
                        <option value="Cocanha (Caraguatatuba)">Cocanha</option>
                        <option value="Indaiá (Caraguatatuba)">Indaiá</option>
                        <option value="Martim de Sá (Caraguatatuba)">Martim de Sá</option>
                        <option value="Massaguaçu (Caraguatatuba)">Massaguaçu</option>
                        <option value="Mococa (Caraguatatuba)">Mococa</option>
                        <option value="Palmeiras (Caraguatatuba)">Palmeiras</option>
                        <option value="Praia Brava (Caraguatatuba)">Praia Brava</option>
                        <option value="Prainha (Caraguatatuba)">Prainha</option>
                        <option value="Tabatinga (Caraguatatuba)">Tabatinga</option>
                    </optgroup>

                    <optgroup label="Guarujá">
                        <option value="Astúrias (Guarujá)">Astúrias</option>
                        <option value="Branca (Guarujá)">Branca</option>
                        <option value="Enseada (Guarujá)">Enseada</option>
                        <option value="Guaiúba (Guarujá)">Guaiúba</option>
                        <option value="Iporanga (Guarujá)">Iporanga</option>
                        <option value="Mar Casado (Guarujá)">Mar Casado</option>
                        <option value="Perequê (Guarujá)">Perequê</option>
                        <option value="Pernambuco (Guarujá)">Pernambuco</option>
                        <option value="Pitangueiras (Guarujá)">Pitangueiras</option>
                        <option value="Preta (Guarujá)">Preta</option>
                        <option value="Tombo (Guarujá)">Tombo</option>
                    </optgroup>

                    <optgroup label="Iguape">
                        <option value="Barra do Ribeira (Iguape)">Barra do Ribeira</option>
                        <option value="Juréia (Iguape)">Juréia</option>
                    </optgroup>

                    <optgroup label="Ilha Comprida">
                        <option value="Boqueirão Norte (Ilha Comprida)">Boqueirão Norte</option>
                        <option value="Pontal da Trincheira (Ilha Comprida)">Pontal da Trincheira</option>
                    </optgroup>

                    <optgroup label="Ilhabela">
                        <option value="Armação (Ilhabela)">Armação</option>
                        <option value="Bonete (Ilhabela)">Bonete</option>
                        <option value="Castelhanos (Ilhabela)">Castelhanos</option>
                        <option value="Curral (Ilhabela)">Curral</option>
                        <option value="Feiticeira (Ilhabela)">Feiticeira</option>
                        <option value="Jabaquara (Ilhabela)">Jabaquara</option>
                        <option value="Pacuíba (Ilhabela)">Pacuíba</option>
                        <option value="Pedra do Sino (Ilhabela)">Pedra do Sino</option>
                        <option value="Perequê (Ilhabela)">Perequê</option>
                        <option value="Viana (Ilhabela)">Viana</option>
                    </optgroup>

                    <optgroup label="Itanhaém">
                        <option value="Boca da Barra (Itanhaém)">Boca da Barra</option>
                        <option value="Cibratel (Itanhaém)">Cibratel</option>
                        <option value="Gaivota (Itanhaém)">Gaivota</option>
                        <option value="Praia dos Pescadores (Itanhaém)">Praia dos Pescadores</option>
                        <option value="Praia dos Sonhos (Itanhaém)">Praia dos Sonhos</option>
                        <option value="Suarão (Itanhaém)">Suarão</option>
                    </optgroup>

                    <optgroup label="Mongaguá">
                        <option value="Agenor de Campos (Mongaguá)">Agenor de Campos</option>
                        <option value="Centro (Mongaguá)">Centro</option>
                        <option value="Itaóca (Mongaguá)">Itaóca</option>
                        <option value="Santa Eugênia (Mongaguá)">Santa Eugênia</option>
                        <option value="Vila São Paulo (Mongaguá)">Vila São Paulo</option>
                    </optgroup>

                    <optgroup label="Peruíbe">
                        <option value="Arpoador (Peruíbe)">Arpoador</option>
                        <option value="Centro (Peruíbe)">Centro</option>
                        <option value="Costão (Peruíbe)">Costão</option>
                        <option value="Desertinha (Peruíbe)">Desertinha</option>
                        <option value="Guaraú (Peruíbe)">Guaraú</option>
                        <option value="Prainha (Peruíbe)">Prainha</option>
                    </optgroup>

                    <optgroup label="Praia Grande">
                        <option value="Aviação (Praia Grande)">Aviação</option>
                        <option value="Balneário Maracanã (Praia Grande)">Balneário Maracanã</option>
                        <option value="Boqueirão (Praia Grande)">Boqueirão</option>
                        <option value="Canto do Forte (Praia Grande)">Canto do Forte</option>
                        <option value="Guilhermina (Praia Grande)">Guilhermina</option>
                        <option value="Ocian (Praia Grande)">Ocian</option>
                        <option value="Solemar (Praia Grande)">Solemar</option>
                        <option value="Tupi (Praia Grande)">Tupi</option>
                        <option value="Vila Caiçara (Praia Grande)">Vila Caiçara</option>
                        <option value="Vila Mirim (Praia Grande)">Vila Mirim</option>
                    </optgroup>

                    <optgroup label="Santos">
                        <option value="Aparecida (Santos)">Aparecida</option>
                        <option value="Boqueirão (Santos)">Boqueirão</option>
                        <option value="Embaré (Santos)">Embaré</option>
                        <option value="Gonzaga (Santos)">Gonzaga</option>
                        <option value="José Menino (Santos)">José Menino</option>
                        <option value="Ponta da Praia (Santos)">Ponta da Praia</option>
                        <option value="Quebra-Mar (Santos)">Quebra-Mar</option>
                    </optgroup>

                    <optgroup label="São Sebastião">
                        <option value="Baleia (São Sebastião)">Baleia</option>
                        <option value="Barequeçaba (São Sebastião)">Barequeçaba</option>
                        <option value="Barra do Una (São Sebastião)">Barra do Una</option>
                        <option value="Boicucanga (São Sebastião)">Boicucanga</option>
                        <option value="Calhetas (São Sebastião)">Calhetas</option>
                        <option value="Camburi (São Sebastião)">Camburi</option>
                        <option value="Camburizinho (São Sebastião)">Camburizinho</option>
                        <option value="Guaecá (São Sebastião)">Guaecá</option>
                        <option value="Juquehy (São Sebastião)">Juquehy</option>
                        <option value="Maresias (São Sebastião)">Maresias</option>
                        <option value="Paúba (São Sebastião)">Paúba</option>
                        <option value="Preta (São Sebastião)">Preta</option>
                        <option value="Sahy (São Sebastião)">Sahy</option>
                        <option value="Toque-Toque Grande (São Sebastião)">Toque-Toque Grande</option>
                        <option value="Toque-Toque Pequeno (São Sebastião)">Toque-Toque Pequeno</option>
                    </optgroup>

                    <optgroup label="São Vicente">
                        <option value="Gonzaguinha (São Vicente)">Gonzaguinha</option>
                        <option value="Itararé (São Vicente)">Itararé</option>
                        <option value="Paranapuã (São Vicente)">Paranapuã</option>
                        <option value="Praia da Ilha Porchat (São Vicente)">Praia da Ilha Porchat</option>
                        <option value="Prainha (São Vicente)">Prainha</option>
                    </optgroup>

                    <optgroup label="Ubatuba">
                        <option value="Domingas Dias (Ubatuba)">Domingas Dias</option>
                        <option value="Enseada (Ubatuba)">Enseada</option>
                        <option value="Félix (Ubatuba)">Félix</option>
                        <option value="Itamambuca (Ubatuba)">Itamambuca</option>
                        <option value="Lázaro (Ubatuba)">Lázaro</option>
                        <option value="Perequê-Açu (Ubatuba)">Perequê-Açu</option>
                        <option value="Picinguaba (Ubatuba)">Picinguaba</option>
                        <option value="Praia Grande (Ubatuba)">Praia Grande</option>
                        <option value="Puruba (Ubatuba)">Puruba</option>
                        <option value="Sapê/Lagoinha (Ubatuba)">Sapê/Lagoinha</option>
                        <option value="Sununga (Ubatuba)">Sununga</option>
                        <option value="Tenório (Ubatuba)">Tenório</option>
                        <option value="Toninhas (Ubatuba)">Toninhas</option>
                        <option value="Vermelha do Centro (Ubatuba)">Vermelha do Centro</option>
                        <option value="Vermelha do Norte (Ubatuba)">Vermelha do Norte</option>
                    </optgroup>

                </select>
                <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-4 text-ocean-dark">
                    <svg class="fill-current h-6 w-6" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                </div>
            </div>
        </div>

        <div id="forecast-container" class="space-y-8 md:space-y-0 md:grid md:grid-cols-2 md:gap-8 hidden">

            <div class="card p-8 rounded-2xl bg-surf-blue text-white">
                <h2 class="text-4xl font-bold mb-5 flex items-center text-sand-light">
                    <svg class="w-8 h-8 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19.428 15.428a2 2 0 00-2.856-2.856l-3.232 3.232a2 2 0 002.856 2.856l1.984-1.984m0-4.792a2 2 0 00-2.856-2.856l-3.232 3.232a2 2 0 002.856 2.856l1.984-1.984m-4.792-4.792a2 2 0 00-2.856-2.856l-3.232 3.232a2 2 0 002.856 2.856l1.984-1.984M5.636 18.364l1.414 1.414a1 1 0 001.414 0l1.414-1.414a1 1 0 000-1.414l-1.414-1.414a1 1 0 00-1.414 0l-1.414 1.414a1 1 0 000 1.414z"></path></svg>
                    A Onda do Dia
                </h2>
                <div class="grid grid-cols-2 gap-6 text-center">
                    <div>
                        <p class="primary-data font-extrabold text-sand-light" id="wave-height">--</p>
                        <p class="text-lg opacity-90 text-body">Altura (m)</p>
                    </div>
                    <div>
                        <p class="primary-data font-extrabold text-sand-light" id="wave-period">--</p>
                        <p class="text-lg opacity-90 text-body">Período (s)</p>
                    </div>
                    <div class="col-span-2 mt-4 p-2 bg-wave-green bg-opacity-30 rounded-lg">
                        <p class="text-2xl font-bold text-white" id="wave-direction">--</p>
                        <p class="text-sm opacity-80 text-body">Direção do Swell</p>
                    </div>
                </div>
            </div>

            <div class="card p-8 rounded-2xl bg-leaf-green text-white">
                <h2 class="text-4xl font-bold mb-5 flex items-center text-sand-light">
                    <svg class="w-8 h-8 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 15a4 4 0 004 4h9a5 5 0 10-.1-9.999 5.002 5.002 0 10-9.78 2.096z"></path></svg>
                    Vento e Clima
                </h2>
                <div class="grid grid-cols-3 gap-6 text-center">
                    <div class="col-span-1">
                        <p class="text-5xl font-extrabold text-white" id="temp">--</p>
                        <p class="text-lg opacity-90 text-body">Temp (°C)</p>
                    </div>
                    <div class="col-span-2">
                        <p class="text-5xl font-extrabold text-white" id="wind-speed">--</p>
                        <p class="text-lg opacity-90 text-body">Vento (km/h)</p>
                    </div>
                    <div class="col-span-3 mt-4 p-2 bg-white bg-opacity-30 rounded-lg">
                        <p class="text-2xl font-bold text-white" id="wind-direction">--</p>
                        <p class="text-sm opacity-80 text-body">Direção do Vento</p>
                    </div>
                </div>
            </div>

            <div class="card p-8 rounded-2xl md:col-span-2 bg-white mt-8 md:mt-0 border-4 border-dashed border-surf-blue/30">
                <h2 class="text-3xl font-bold text-ocean-dark mb-4 flex items-center text-body">
                    <span class="mr-3 text-4xl">🐌</span>
                    Dica do Caramujo para o seu Dia
                </h2>
                <div id="gemini-tip-loader" class="flex justify-center items-center h-24 hidden">
                    <div class="loader ease-linear rounded-full border-4 border-t-4 border-gray-200 h-10 w-10"></div>
                    <p class="ml-4 text-lg text-gray-500 text-body animate-pulse">O Caramujo checando os melhores sites...</p>
                </div>
                <div id="gemini-tip-content">
                    <p id="gemini-tip-text" class="text-gray-700 text-xl italic text-body leading-relaxed mb-4">Escolha uma praia e eu te direi como aproveitar cada segundo!</p>
                </div>
                
                <div id="gemini-sources" class="mt-6 pt-4 border-t border-gray-100 text-xs text-gray-400 hidden text-body">
                    <p class="font-semibold mb-1">Baseado em dados reais de:</p>
                    <ul id="sources-list" class="flex flex-wrap gap-x-4 gap-y-1"></ul>
                </div>
            </div>

        </div>

    </div>

    <script>
        const beachSelect = document.getElementById('beach-select');
        const forecastContainer = document.getElementById('forecast-container');
        const geminiTipText = document.getElementById('gemini-tip-text');
        const geminiTipLoader = document.getElementById('gemini-tip-loader');
        const geminiSourcesDiv = document.getElementById('gemini-sources');
        const sourcesList = document.getElementById('sources-list');

        const selectBeach = async (event) => {
            const selectedBeach = event.target.value;
            if (!selectedBeach) return;

            // Gera dados realistas dinamicamente baseados no nome para simular variedade
            const forecast = { 
                waveHeight: (Math.random() * 1.5 + 0.3).toFixed(1) + "m", 
                wavePeriod: (Math.floor(Math.random() * 6) + 6) + "s", 
                waveDirection: ["Leste", "Sudeste", "Sul", "Sudoeste"][Math.floor(Math.random() * 4)], 
                temp: (Math.floor(Math.random() * 8) + 22) + "°C", 
                windSpeed: (Math.floor(Math.random() * 15) + 5) + "km/h", 
                windDirection: ["Norte", "Nordeste", "Leste", "Terral"][Math.floor(Math.random() * 4)] 
            };
            
            document.getElementById('wave-height').textContent = forecast.waveHeight;
            document.getElementById('wave-period').textContent = forecast.wavePeriod;
            document.getElementById('wave-direction').textContent = forecast.waveDirection;
            document.getElementById('temp').textContent = forecast.temp;
            document.getElementById('wind-speed').textContent = forecast.windSpeed;
            document.getElementById('wind-direction').textContent = forecast.windDirection;

            forecastContainer.classList.remove('hidden');
            geminiSourcesDiv.classList.add('hidden');
            sourcesList.innerHTML = '';
            geminiTipText.textContent = "";
            geminiTipLoader.classList.remove('hidden');

            await getGeminiTip(selectedBeach, forecast);
        };

        const getGeminiTip = async (beachName, forecast) => {
            // PROMPT ATUALIZADO: Foco em tom leve, descontraído e direto.
            const systemPrompt = `Você é o 'Caramujo do Surf', a autoridade máxima em previsões litorâneas, mas com uma vibe super leve, suave e descontraída.
            Sua missão é dar uma dica direta ao ponto para o usuário. 
            Aja como um amigo experiente que manja tudo de mar:
            1. Use a ferramenta de busca para ver o que tá rolando agora em ${beachName} (ondas, vento, sol).
            2. Seja DESCONTRAÍDO: use gírias leves de surf (se couber), seja amigável e direto.
            3. Dê a letra: 'Vai que tá bom', 'Melhor levar um longboard', 'Cuidado com a corrente', 'Hora de relaxar na areia'.
            4. Não enrole. Resuma a melhor vibe para curtir ${beachName} hoje em no máximo 3 ou 4 parágrafos curtos.
            5. Cite as fontes no final de forma sutil.
            6. Responda em Português Brasil de forma suave e "boa praça".`;

            const userQuery = `Dá a letra, Caramujo! O que tem de bom em ${beachName} com ondas de ${forecast.waveHeight} e vento de ${forecast.windSpeed}?`;

            const payload = {
                contents: [{ parts: [{ text: userQuery }] }],
                tools: [{ "google_search": {} }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
            };

            const apiUrl = `${API_BASE_URL}${GEMINI_MODEL}:generateContent?key=${window.apiKey}`;
            
            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const result = await response.json();
                const text = result.candidates?.[0]?.content?.parts?.[0]?.text;
                
                if (text) {
                    geminiTipText.innerHTML = text.replace(/\n/g, '<br>');
                    
                    const groundings = result.candidates?.[0]?.groundingMetadata?.groundingAttributions;
                    if (groundings && groundings.length > 0) {
                        groundings.forEach(a => {
                            if (a.web?.uri) {
                                const li = document.createElement('li');
                                li.innerHTML = `<a href="${a.web.uri}" target="_blank" class="hover:text-surf-blue underline decoration-dotted">${a.web.title || 'Link'}</a>`;
                                sourcesList.appendChild(li);
                            }
                        });
                        geminiSourcesDiv.classList.remove('hidden');
                    }
                } else {
                    geminiTipText.textContent = "O Caramujo tá dando um mergulho agora. Escolha outra praia ou tente de novo em um minuto!";
                }
            } catch (error) {
                geminiTipText.textContent = "Houve uma interferência na maré... Tente escolher a praia novamente, beleza?";
            } finally {
                geminiTipLoader.classList.add('hidden');
            }
        };

        beachSelect.addEventListener('change', selectBeach);
    </script>
</body>
</html>
