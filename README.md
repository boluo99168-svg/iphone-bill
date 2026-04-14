<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>最终稳定版 | 账单截图工具 · 无图片Logo · ID强制单行</title>
    <!-- 使用最稳定的 CDN 源，确保 html2canvas 100% 加载成功 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }
        body {
            background: linear-gradient(145deg, #d4d9e2 0%, #bfc6d0 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            padding: 30px 24px;
        }
        .dashboard {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            align-items: flex-start;
            gap: 36px;
            max-width: 1350px;
            width: 100%;
        }
        /* 左侧控制面板 */
        .editor-card {
            width: 280px;
            background: rgba(255, 255, 255, 0.96);
            border-radius: 36px;
            box-shadow: 0 25px 40px -12px rgba(0, 0, 0, 0.3);
            padding: 24px 20px;
            border: 1px solid rgba(255, 255, 255, 0.6);
        }
        .editor-card h2 {
            font-size: 1.6rem;
            font-weight: 700;
            background: linear-gradient(135deg, #1e2b3c, #0f5b7a);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            margin-bottom: 6px;
        }
        .editor-card .desc {
            font-size: 12px;
            color: #4b5c6e;
            margin-bottom: 24px;
            border-left: 3px solid #1677ff;
            padding-left: 10px;
        }
        .input-field {
            margin-bottom: 18px;
        }
        .input-field label {
            display: block;
            font-size: 13px;
            font-weight: 600;
            color: #1e2f3e;
            margin-bottom: 6px;
        }
        .editor-card input {
            width: 100%;
            padding: 12px 14px;
            background: #ffffff;
            border: 1.5px solid #e2edf7;
            border-radius: 24px;
            font-size: 14px;
            outline: none;
            font-family: monospace;
        }
        .editor-card input:focus {
            border-color: #1677ff;
            box-shadow: 0 0 0 3px rgba(22, 119, 255, 0.2);
        }
        .button-group {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 28px;
        }
        .btn {
            border: none;
            padding: 12px;
            border-radius: 40px;
            font-weight: 600;
            font-size: 15px;
            background: #1677ff;
            color: white;
            cursor: pointer;
            transition: 0.2s;
        }
        .btn-outline {
            background: #2c3e50;
        }
        .btn:hover {
            background: #0e5fdf;
            transform: translateY(-2px);
        }

        /* ========= 手机相框 - 纯代码 iPhone 16 风格 ========= */
        .phone-container {
            position: relative;
            width: 400px;
            flex-shrink: 0;
            filter: drop-shadow(0 28px 38px -12px rgba(0, 0, 0, 0.5));
        }
        .iphone-body {
            position: relative;
            width: 100%;
            aspect-ratio: 360 / 780;
            background: #1c1c1e;
            border-radius: 56px;
            box-shadow: 0 30px 40px -15px rgba(0, 0, 0, 0.6), inset 0 0 0 3px rgba(255, 255, 255, 0.08);
            padding: 8px;
            border: 1px solid #3a3a3c;
        }
        /* 动态岛 */
        .iphone-body::before {
            content: "";
            position: absolute;
            top: 16px;
            left: 50%;
            transform: translateX(-50%);
            width: 110px;
            height: 34px;
            background: #111;
            border-radius: 28px;
            z-index: 20;
            box-shadow: inset 0 1px 1px rgba(255,255,255,0.15), 0 2px 4px rgba(0,0,0,0.2);
        }
        /* 物理按键 */
        .iphone-body .action-btn { position: absolute; left: -3px; top: 60px; width: 4px; height: 28px; background: #2a2a2c; border-radius: 4px 0 0 4px; }
        .iphone-body .vol-up { position: absolute; left: -3px; top: 100px; width: 4px; height: 32px; background: #2a2a2c; border-radius: 4px 0 0 4px; }
        .iphone-body .vol-down { position: absolute; left: -3px; top: 142px; width: 4px; height: 32px; background: #2a2a2c; border-radius: 4px 0 0 4px; }
        .iphone-body .power { position: absolute; right: -3px; top: 120px; width: 4px; height: 48px; background: #2a2a2c; border-radius: 0 4px 4px 0; }

        .phone-screen {
            background: #f8fafc;
            border-radius: 50px;
            width: 100%;
            height: 100%;
            overflow: hidden;
            box-shadow: inset 0 0 0 1px rgba(0, 0, 0, 0.08);
        }
        .screen-inner {
            background: #ffffff;
            min-height: 100%;
            padding-bottom: 20px;
        }

        /* 状态栏 */
        .status-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 20px 6px 20px;
            font-size: 15px;
            font-weight: 590;
            background: #ffffff;
            font-family: monospace;
        }
        .status-icons { display: flex; align-items: center; gap: 6px; }
        .signal { display: flex; align-items: flex-end; gap: 2px; }
        .signal span { width: 3.5px; background: #1c1c1e; display: block; border-radius: 1px; }
        .signal span:nth-child(1) { height: 4px; }
        .signal span:nth-child(2) { height: 7px; }
        .signal span:nth-child(3) { height: 10px; }
        .signal span:nth-child(4) { height: 12px; }

        /* 纯色电池 - 红绿切换逻辑 */
        .apple-battery { display: flex; align-items: center; margin-left: 4px; }
        .battery-shell { width: 24px; height: 12px; border: 1.2px solid #1c1c1e; border-radius: 4px; position: relative; }
        .battery-fill { height: 100%; background: #2e7d32; border-radius: 2px; width: 80%; transition: 0.1s; }
        .battery-shell::after { content: ''; position: absolute; right: -3px; top: 3px; width: 2px; height: 5px; background: #1c1c1e; border-radius: 1px; }
        .battery-low .battery-shell { border-color: #e34d4d; }
        .battery-low .battery-fill { background: #e34d4d; }
        .battery-low .battery-shell::after { background: #e34d4d; }

        .safari-url {
            background: #eef2f8;
            margin: 8px 16px;
            border-radius: 16px;
            padding: 8px 14px;
            font-size: 13px;
            text-align: center;
            color: #1f2e3a;
        }

        /* 账单卡片 */
        .bill-card {
            background: white;
            margin: 12px 16px 24px 16px;
            border-radius: 28px;
            box-shadow: 0 8px 24px rgba(0, 0, 0, 0.05);
            padding: 20px 18px;
            border: 1px solid #f0f2f6;
        }
        /* ✅ 稳定方案：纯文字 Logo，无任何图片依赖，永不出错 */
        .logo-text {
            font-size: 20px;
            font-weight: 800;
            color: #0f5b7a;
            letter-spacing: 1px;
            margin-bottom: 18px;
            font-family: monospace;
            background: linear-gradient(135deg, #1e2b3c, #0f5b7a);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            display: inline-block;
        }

        .title-right {
            text-align: right;
            font-weight: 800;
            font-size: 16px;
            color: #0b2b3b;
            margin: 10px 0 18px 0;
        }
        .subheader-right {
            text-align: right;
            font-weight: 700;
            font-size: 14px;
            margin: 14px 0 12px 0;
            color: #1f2a44;
        }
        .info-row {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            margin-bottom: 16px;
            font-size: 13px;
            flex-wrap: nowrap;
        }
        .label-txt {
            font-weight: 600;
            color: #5b6e8c;
            flex-shrink: 0;
        }
        .value-txt {
            font-weight: 500;
            color: #11181c;
            text-align: right;
            word-break: keep-all;
            white-space: nowrap;
            overflow-x: visible;
        }
        /* ID 单行强制 - 绝无跳行 */
        .id-single-line {
            white-space: nowrap;
            display: inline-block;
            font-family: 'SF Mono', monospace;
            font-size: 10px;
            letter-spacing: 0.2px;
            max-width: 100%;
            overflow: visible;
        }
        .id-wrapper {
            display: inline-block;
            max-width: 65%;
            text-align: right;
            white-space: nowrap;
            overflow: visible;
        }
        .divider {
            height: 1px;
            background: #eef2f8;
            margin: 18px 0;
        }
        .footnote {
            font-size: 9px;
            text-align: center;
            color: #94a3b8;
            margin-top: 16px;
        }
        /* 彻底隐藏所有滚动条 */
        ::-webkit-scrollbar {
            display: none;
        }
        body, .phone-screen, .iphone-body, .phone-container {
            scrollbar-width: none;
            -ms-overflow-style: none;
        }
    </style>
</head>
<body>
<div class="dashboard">
    <div class="editor-card">
        <h2>📱 账单工坊</h2>
        <div class="desc">纯文字Logo · ID强制单行 · 红绿电池</div>
        <div class="input-field">
            <label>💰 Valor (R$)</label>
            <input type="text" id="amountInput" placeholder="例如: 62,00" value="62,00">
        </div>
        <div class="input-field">
            <label>⏱️ Tempo de transaccao</label>
            <input type="text" id="timeInput" placeholder="dd/mm/aaaa HH:MM" value="13/04/2026 01:34">
        </div>
        <div class="input-field">
            <label>🆔 ID da transacção (自定义)</label>
            <input type="text" id="idInput" placeholder="交易ID" value="E508719212026041304341DIQRSOLGTM">
        </div>
        <div class="input-field">
            <label>👤 Destino (Nome)</label>
            <input type="text" id="destinoInput" placeholder="Nome completo" value="Jose Rodrigues Meneses">
        </div>
        <div class="input-field">
            <label>📱 状态栏时间</label>
            <input type="text" id="statusTimeInput" placeholder="如 01:34" value="01:34">
        </div>
        <div class="input-field">
            <label>🔋 电量 (%) ≤20红色 / >20绿色</label>
            <input type="number" id="batteryInput" min="0" max="100" value="87">
        </div>
        <div class="button-group">
            <button class="btn" id="updateBtn">✨ 刷新账单</button>
            <button class="btn btn-outline" id="screenshotBtn">📸 下载完美截图</button>
        </div>
        <div class="desc" style="margin-top: 18px; font-size: 10px;">✔ 纯文字Logo（永不失效）<br>✔ ID强制一行不跳行 | 电量红绿切换</div>
    </div>

    <!-- 手机截图核心区 -->
    <div class="phone-container" id="captureArea">
        <div class="iphone-body">
            <div class="action-btn"></div>
            <div class="vol-up"></div>
            <div class="vol-down"></div>
            <div class="power"></div>
            <div class="phone-screen">
                <div class="screen-inner">
                    <div class="status-bar">
                        <span class="time-text" id="phoneTime">01:34</span>
                        <div class="status-icons">
                            <div class="signal"><span></span><span></span><span></span><span></span></div>
                            <span style="font-size:12px;">5G</span>
                            <div class="apple-battery" id="appleBatteryWidget">
                                <div class="battery-wrapper">
                                    <div class="battery-shell">
                                        <div class="battery-fill" id="batteryLevelFill" style="width:87%"></div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="safari-url">uspay.univepay.com 🔒 PIX</div>
                    <div class="bill-card">
                        <!-- 纯文字 Logo，永不出错 -->
                        <div class="logo-text">UNIVEPAY</div>
                        <div class="title-right">comprovante de transacção</div>
                        <div class="info-row">
                            <span class="label-txt">Valor</span>
                            <span class="value-txt" id="amountDisplay">R$ 62,00</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">Tempo de transaccao</span>
                            <span class="value-txt" id="timeDisplay">13/04/2026 01:34</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">ID da transacção</span>
                            <div class="id-wrapper">
                                <span class="value-txt id-single-line" id="idDisplay">E508719212026041304341DIQRSOLGTM</span>
                            </div>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">Tipo de transferência</span>
                            <span class="value-txt">PIX</span>
                        </div>
                        <div class="divider"></div>
                        <div class="subheader-right">Informações do depositante</div>
                        <div class="info-row">
                            <span class="label-txt">Nome</span>
                            <span class="value-txt">Univebet Gaming Ltda</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">CNPJ</span>
                            <span class="value-txt">64663535000125</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">Banco/Instituição</span>
                            <span class="value-txt">50871921</span>
                        </div>
                        <div class="divider"></div>
                        <div class="subheader-right">Informações do destino</div>
                        <div class="info-row">
                            <span class="label-txt">Nome</span>
                            <span class="value-txt" id="destinoDisplay">Jose Rodrigues Meneses</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">CPF/CNPJ</span>
                            <span class="value-txt">17649476812</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">AGÊNCIA</span>
                            <span class="value-txt">10573521</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">ISPB Nome:</span>
                            <span class="value-txt">MERCADO PAGO IP LTDA.</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">Chave</span>
                            <span class="value-txt">+5511981760984</span>
                        </div>
                        <div class="info-row">
                            <span class="label-txt">Tipo de Conta</span>
                            <span class="value-txt">PHONE</span>
                        </div>
                        <div class="divider"></div>
                        <div class="footnote">Transação verificada · PIX instantâneo</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    // DOM 元素绑定
    const amountInput = document.getElementById('amountInput');
    const timeInput = document.getElementById('timeInput');
    const idInput = document.getElementById('idInput');
    const destinoInput = document.getElementById('destinoInput');
    const statusTimeInput = document.getElementById('statusTimeInput');
    const batteryInput = document.getElementById('batteryInput');
    const updateBtn = document.getElementById('updateBtn');
    const screenshotBtn = document.getElementById('screenshotBtn');

    const amountDisplay = document.getElementById('amountDisplay');
    const timeDisplay = document.getElementById('timeDisplay');
    const idDisplay = document.getElementById('idDisplay');
    const destinoDisplay = document.getElementById('destinoDisplay');
    const phoneTimeSpan = document.getElementById('phoneTime');
    const batteryFillElement = document.getElementById('batteryLevelFill');
    const batteryWidget = document.getElementById('appleBatteryWidget');

    function formatCurrencyBRL(value) {
        let raw = value.trim();
        if (raw === "") return "R$ 0,00";
        let numeric = raw.replace(/\./g, '').replace(',', '.').replace(/[^\d.-]/g, '');
        let number = parseFloat(numeric);
        if (isNaN(number)) number = 0;
        let fixed = number.toFixed(2).replace('.', ',');
        let parts = fixed.split(',');
        let integerPart = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ".");
        return `R$ ${integerPart},${parts[1]}`;
    }

    function updateBatteryUI(percent) {
        let p = Math.min(100, Math.max(0, percent));
        batteryFillElement.style.width = p + "%";
        if (p <= 20) {
            batteryFillElement.style.background = "#e34d4d";
            batteryWidget.classList.add('battery-low');
        } else {
            batteryFillElement.style.background = "#2e7d32";
            batteryWidget.classList.remove('battery-low');
        }
    }

    function refreshAllData() {
        let amountRaw = amountInput.value;
        if (amountRaw === "") amountRaw = "0,00";
        amountDisplay.innerText = formatCurrencyBRL(amountRaw);
        
        let timeVal = timeInput.value.trim();
        if (timeVal === "") timeVal = "13/04/2026 01:34";
        timeDisplay.innerText = timeVal;
        
        let idVal = idInput.value.trim();
        if (idVal === "") idVal = "E508719212026041304341DIQRSOLGTM";
        idVal = idVal.replace(/[\n\r\t]/g, '');
        idDisplay.innerText = idVal;
        if (idInput.value !== idVal) idInput.value = idVal;
        
        let destVal = destinoInput.value.trim();
        if (destVal === "") destVal = "Jose Rodrigues Meneses";
        destinoDisplay.innerText = destVal;
        
        let phoneTimeVal = statusTimeInput.value.trim();
        if (phoneTimeVal === "") phoneTimeVal = "01:34";
        phoneTimeSpan.innerText = phoneTimeVal;
        
        let batteryVal = parseInt(batteryInput.value);
        if (isNaN(batteryVal)) batteryVal = 87;
        batteryVal = Math.min(100, Math.max(0, batteryVal));
        updateBatteryUI(batteryVal);
    }

    async function captureScreenshot() {
        const target = document.getElementById('captureArea');
        if (!target) return;
        const originalText = screenshotBtn.innerText;
        screenshotBtn.innerText = '📸 生成截图中...';
        screenshotBtn.disabled = true;
        try {
            refreshAllData();
            await new Promise(r => setTimeout(r, 80));
            const canvas = await html2canvas(target, {
                scale: 2.8,
                backgroundColor: null,
                useCORS: true,
                logging: false,
                allowTaint: false,
                onclone: (clonedDoc) => {
                    const clonedBatteryWidget = clonedDoc.querySelector('#appleBatteryWidget');
                    if (clonedBatteryWidget && batteryWidget.classList.contains('battery-low')) {
                        clonedBatteryWidget.classList.add('battery-low');
                    }
                    const clonedFill = clonedDoc.querySelector('#batteryLevelFill');
                    if (clonedFill) {
                        clonedFill.style.width = batteryFillElement.style.width;
                        clonedFill.style.background = batteryFillElement.style.background;
                    }
                }
            });
            const link = document.createElement('a');
            link.download = 'stable_bill_screenshot.png';
            link.href = canvas.toDataURL('image/png');
            link.click();
        } catch (err) {
            console.error('截图失败:', err);
            alert('截图生成失败，请刷新页面重试');
        } finally {
            screenshotBtn.innerText = originalText;
            screenshotBtn.disabled = false;
        }
    }
    
    updateBtn.addEventListener('click', refreshAllData);
    screenshotBtn.addEventListener('click', captureScreenshot);
    
    const allInputs = [amountInput, timeInput, idInput, destinoInput, statusTimeInput, batteryInput];
    allInputs.forEach(inp => {
        inp.addEventListener('input', refreshAllData);
        inp.addEventListener('change', refreshAllData);
    });
    
    refreshAllData();
    
    idInput.addEventListener('input', function() {
        let raw = this.value;
        if (raw.includes('\n') || raw.includes('\r') || raw.includes('\t')) {
            this.value = raw.replace(/[\n\r\t]/g, '');
            refreshAllData();
        }
    });
</script>
</body>
</html>
