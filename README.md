<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TI Nova POS 2.0</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary: #4361ee; --bg: #f8faff; --card: #ffffff; --text: #1b254b;
            --secondary: #707eae; --success: #01b574; --danger: #ee5d50;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); color: var(--text); overflow-x: hidden; }

        /* --- Modern Opening Page with Glassmorphism --- */
        #opening-page {
            position: fixed; inset: 0; background: #020617; 
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            z-index: 10000; transition: 1.2s cubic-bezier(0.85, 0, 0.15, 1);
            overflow: hidden;
        }

        /* Animated Background Lights */
        .light-blob {
            position: absolute; width: 300px; height: 300px;
            background: radial-gradient(circle, rgba(67, 97, 238, 0.4) 0%, rgba(67, 97, 238, 0) 70%);
            filter: blur(60px); border-radius: 50%; z-index: 1;
            animation: move 10s infinite alternate;
        }
        .blob-1 { top: -50px; left: -50px; }
        .blob-2 { bottom: -50px; right: -50px; animation-delay: -5s; }

        @keyframes move {
            from { transform: translate(0, 0) scale(1); }
            to { transform: translate(100px, 50px) scale(1.2); }
        }

        /* Glass Card Container */
        .glass-box {
            position: relative; z-index: 2; padding: 40px;
            background: rgba(255, 255, 255, 0.03);
            backdrop-filter: blur(15px); -webkit-backdrop-filter: blur(15px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 40px; text-align: center;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
        }

        .logo-box h1 { 
            font-size: 48px; font-weight: 800; color: white; letter-spacing: -1.5px;
            opacity: 0; transform: translateY(20px);
            animation: fadeInUp 0.8s ease forwards 0.3s;
        }

        .logo-box p { 
            color: #94a3b8; font-weight: 600; letter-spacing: 5px; font-size: 11px; margin-top: 5px;
            opacity: 0; animation: fadeIn 1s ease forwards 1s;
        }

        .btn-start {
            margin-top: 40px; padding: 18px 50px; border-radius: 16px; border: none;
            background: white; color: #020617; font-weight: 800; font-size: 16px;
            cursor: pointer; transition: 0.4s; z-index: 2;
            opacity: 0; transform: scale(0.9);
            animation: popIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards 1.3s;
        }

        .btn-start:hover { transform: scale(1.05); box-shadow: 0 0 20px rgba(255,255,255,0.4); }
        .btn-start:active { transform: scale(0.95); }

        /* Animations */
        @keyframes fadeInUp { to { opacity: 1; transform: translateY(0); } }
        @keyframes fadeIn { to { opacity: 1; } }
        @keyframes popIn { to { opacity: 1; transform: scale(1); } }

        /* --- Main App Layout --- */
        #main-content { display: none; opacity: 0; transition: 1s; padding-bottom: 30px; }
        header { padding: 25px 20px; display: flex; justify-content: space-between; align-items: center; }
        .container { padding: 0 16px; }

        .wallet-card {
            background: linear-gradient(135deg, #1e293b 0%, #4361ee 100%);
            border-radius: 30px; padding: 25px; color: white; margin-bottom: 20px;
        }

        .chart-card { background: var(--card); border-radius: 24px; padding: 16px; margin-bottom: 20px; box-shadow: 0 4px 20px rgba(0,0,0,0.03); }

        .order-card {
            background: var(--card); border-radius: 22px; padding: 18px; margin-bottom: 12px;
            border: 1px solid #f1f4f9; display: flex; flex-direction: column;
        }

        .receipt-ui { text-align: center; padding: 10px 5px; }
        .receipt-header { border-bottom: 1.5px dashed #e2e8f0; padding-bottom: 20px; margin-bottom: 20px; }
        .receipt-row { display: flex; justify-content: space-between; margin-bottom: 12px; font-size: 14px; color: var(--text); }
        .receipt-footer { background: #f8fafc; padding: 15px; border-radius: 15px; margin-top: 20px; font-weight: 700; }

        .fab {
            position: fixed; bottom: 30px; right: 25px; width: 65px; height: 65px;
            background: var(--primary); color: white; border-radius: 22px;
            display: flex; align-items: center; justify-content: center; font-size: 35px;
            box-shadow: 0 10px 25px rgba(67, 97, 238, 0.4); border: none; z-index: 1000;
        }

        .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 2000; display: none; align-items: center; justify-content: center; padding: 20px; }
        .modal-content { background: white; width: 100%; max-width: 400px; border-radius: 30px; padding: 25px; }

        input { width: 100%; padding: 15px; border-radius: 14px; border: 1px solid #edf2f7; background: #f7fafc; margin-bottom: 12px; outline: none; font-weight: 600; }
        .btn-action { background: var(--primary); color: white; width: 100%; padding: 16px; border: none; border-radius: 14px; font-weight: 800; cursor: pointer; }

    </style>
</head>
<body>

    <div id="opening-page">
        <div class="light-blob blob-1"></div>
        <div class="light-blob blob-2"></div>
        
        <div class="glass-box">
            <div class="logo-box">
                <h1>TI Nova</h1>
                <p>POS 2.0 DIGITAL</p>
            </div>
            <button class="btn-start" onclick="startApp()">Get Started</button>
        </div>
    </div>

    <div id="main-content">
        <header>
            <div>
                <p style="font-size: 12px; font-weight: 700; color: var(--secondary);">LATEST UPDATE</p>
                <h2 style="font-weight: 800; color: var(--primary);">Dashboard</h2>
            </div>
            <div id="liveTime" style="font-size: 14px; font-weight: 800;"></div>
        </header>

        <div class="container">
            <div class="wallet-card">
                <p style="font-size: 11px; font-weight: 700; opacity: 0.8; letter-spacing: 1px;">TOTAL REVENUE (BST)</p>
                <h1 id="totalRevenue" style="font-size: 38px; margin: 8px 0 15px;">$0.00</h1>
                <div style="display: flex; gap: 30px;">
                    <div><p style="font-size: 10px; opacity: 0.7;">Total Due</p><b id="totalDue" style="font-size: 18px;">$0.00</b></div>
                    <div><p style="font-size: 10px; opacity: 0.7;">Transactions</p><b id="transCount" style="font-size: 18px;">0</b></div>
                </div>
            </div>

            <div class="chart-card"><canvas id="revenueChart" style="max-height: 140px;"></canvas></div>

            <div style="display: flex; justify-content: space-between; align-items: center; margin: 25px 0 15px;">
                <h3 style="font-weight: 800;">Recent Sales</h3>
                <input type="text" id="searchInput" onkeyup="searchLog()" placeholder="Search..." style="width: 130px; padding: 10px; border-radius: 12px; border: 1px solid #ddd; margin-bottom: 0;">
            </div>

            <div id="orderList"></div>
        </div>
    </div>

    <button class="fab" onclick="toggleModal('entryModal', true)">+</button>

    <div id="entryModal" class="modal" onclick="if(event.target==this) toggleModal('entryModal', false)">
        <div class="modal-content">
            <h3 style="margin-bottom: 20px; font-weight: 800; text-align: center;">New Transaction</h3>
            <input type="text" id="cName" placeholder="Client Name">
            <input type="tel" id="cPhone" placeholder="Phone Number">
            <input type="text" id="cService" placeholder="Service Details">
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                <input type="number" id="cPrice" placeholder="Total">
                <input type="number" id="cPaid" placeholder="Paid">
            </div>
            <button class="btn-action" onclick="addEntry()">Confirm Order</button>
        </div>
    </div>

    <div id="receiptModal" class="modal" onclick="if(event.target==this) toggleModal('receiptModal', false)">
        <div class="modal-content" id="receiptContent"></div>
    </div>

<script>
    let db = JSON.parse(localStorage.getItem('nova_v2_final_bd')) || [];
    let myChart;

    function getBDTime() {
        return new Date().toLocaleTimeString('en-US', { timeZone: 'Asia/Dhaka', hour: '2-digit', minute: '2-digit', hour12: true });
    }
    function getBDDate() {
        return new Date().toLocaleDateString('en-GB', { timeZone: 'Asia/Dhaka', day: '2-digit', month: 'short', year: 'numeric' });
    }

    setInterval(() => { document.getElementById('liveTime').innerText = getBDTime(); }, 1000);

    function startApp() {
        const op = document.getElementById('opening-page');
        const mc = document.getElementById('main-content');
        op.style.transform = 'translateY(-100%)';
        setTimeout(() => { 
            op.style.display = 'none'; 
            mc.style.display = 'block';
            setTimeout(() => mc.style.opacity = '1', 50);
            render();
        }, 1000);
    }

    function toggleModal(id, show) { document.getElementById(id).style.display = show ? 'flex' : 'none'; }

    function addEntry() {
        const name = document.getElementById('cName').value;
        const price = parseFloat(document.getElementById('cPrice').value) || 0;
        const paid = parseFloat(document.getElementById('cPaid').value) || 0;

        if(!name || price <= 0) return alert("Enter details!");

        const order = {
            id: Date.now(), orderNo: 'TN-' + Math.floor(1000 + Math.random() * 9000),
            name, phone: document.getElementById('cPhone').value,
            service: document.getElementById('cService').value || 'Service',
            price, paid, status: 'Pending',
            date: getBDDate(), time: getBDTime()
        };

        db.unshift(order);
        sync();
        toggleModal('entryModal', false);
        ['cName','cPhone','cService','cPrice','cPaid'].forEach(i => document.getElementById(i).value = '');
        showReceipt(order.id);
    }

    function sync() { localStorage.setItem('nova_v2_final_bd', JSON.stringify(db)); render(); }

    function showReceipt(id) {
        const item = db.find(o => o.id === id);
        const due = item.price - item.paid;
        
        document.getElementById('receiptContent').innerHTML = `
            <div class="receipt-ui">
                <div class="receipt-header">
                    <h2 style="color:var(--primary); font-weight:800;">TI NOVA 2.0</h2>
                    <p style="font-size:11px; color:var(--secondary); letter-spacing:2px; margin-top:5px;">DIGITAL RECEIPT</p>
                </div>
                <div class="receipt-row"><span>Receipt ID</span><b>#${item.orderNo}</b></div>
                <div class="receipt-row"><span>Date & Time</span><b>${item.date} | ${item.time}</b></div>
                <div class="receipt-row"><span>Customer</span><b>${item.name}</b></div>
                <div class="receipt-row"><span>Service</span><b>${item.service}</b></div>
                <hr style="border:none; border-top:1.5px dashed #eee; margin:15px 0;">
                <div class="receipt-row" style="font-size:16px;"><span>Total Amount</span><b>$${item.price}</b></div>
                <div class="receipt-row" style="color:var(--success);"><span>Paid Now</span><b>$${item.paid}</b></div>
                <div class="receipt-footer" style="color: ${due > 0 ? 'var(--danger)' : 'var(--success)'}">
                    ${due > 0 ? 'Remaining Due: $' + due : 'PAYMENT SUCCESSFUL'}
                </div>
                <button class="btn-action" style="margin-top:20px; background:#f1f4f9; color:var(--text);" onclick="toggleModal('receiptModal', false)">Close View</button>
            </div>
        `;
        toggleModal('receiptModal', true);
    }

    function render() {
        const list = document.getElementById('orderList');
        list.innerHTML = '';
        let rev = 0, due = 0;

        db.forEach(item => {
            const itemDue = item.price - item.paid;
            rev += item.paid; due += itemDue;
            list.innerHTML += `
                <div class="order-card" onclick="showReceipt(${item.id})">
                    <div style="display:flex; justify-content:space-between; align-items:center;">
                        <span style="font-size:10px; font-weight:800; color:var(--primary);">#${item.orderNo}</span>
                        <span style="font-size:10px; font-weight:800; color:var(--secondary);">${item.date}</span>
                    </div>
                    <b style="font-size:16px; margin:5px 0;">${item.name}</b>
                    <div style="display:flex; justify-content:space-between; align-items:flex-end; margin-top:8px;">
                        <div>
                            <p style="font-size:10px; font-weight:700; color:var(--secondary);">TOTAL BILL</p>
                            <span style="font-weight:800; font-size:18px;">$${item.price}</span>
                        </div>
                        <div>
                            ${itemDue > 0 ? 
                                `<button class="btn-action" style="padding:6px 15px; width:auto; font-size:11px; background:var(--success);" onclick="event.stopPropagation(); clearDue(${item.id})">Pay Due $${itemDue}</button>` : 
                                `<span style="color:var(--success); font-weight:800; font-size:11px;">COMPLETED ✓</span>`
                            }
                        </div>
                    </div>
                </div>`;
        });
        document.getElementById('totalRevenue').innerText = '$' + rev.toLocaleString();
        document.getElementById('totalDue').innerText = '$' + due.toLocaleString();
        document.getElementById('transCount').innerText = db.length;
        updateChart();
    }

    function clearDue(id) {
        const idx = db.findIndex(o => o.id === id);
        db[idx].paid = db[idx].price;
        db[idx].status = 'Completed';
        sync();
    }

    function searchLog() {
        const term = document.getElementById('searchInput').value.toLowerCase();
        document.querySelectorAll('.order-card').forEach(card => {
            card.style.display = card.innerText.toLowerCase().includes(term) ? 'flex' : 'none';
        });
    }

    function updateChart() {
        const ctx = document.getElementById('revenueChart').getContext('2d');
        const labels = [...Array(7)].map((_, i) => {
            const d = new Date(); d.setDate(d.getDate() - i);
            return d.toLocaleDateString('en-GB', { day: '2-digit', month: 'short' });
        }).reverse();
        const data = labels.map(l => db.filter(i => i.date.includes(l)).reduce((s, i) => s + i.paid, 0));
        if(myChart) myChart.destroy();
        myChart = new Chart(ctx, {
            type: 'line',
            data: { labels, datasets: [{ data, borderColor: '#4361ee', tension: 0.4, fill: true, backgroundColor: 'rgba(67, 97, 238, 0.05)', borderWidth: 3, pointRadius: 0 }] },
            options: { plugins: { legend: { display: false } }, scales: { y: { display: false }, x: { grid: { display: false }, ticks: { font: { size: 9, weight: '700' } } } } }
        });
    }
</script>
</body>
</html>
