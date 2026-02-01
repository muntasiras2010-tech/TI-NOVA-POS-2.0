<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TI Nova POS 2.0 | Trexivo</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #4361ee;
            --glass: rgba(255, 255, 255, 0.7);
            --glass-border: rgba(255, 255, 255, 0.3);
            --card-grad: linear-gradient(135deg, #1e293b 0%, #4361ee 100%);
            --text-main: #1b254b;
            --success: #01b574;
            --danger: #ee5d50;
            --warning: #ffb547;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; }
        
        body { background: #f4f7fe; color: var(--text-main); min-height: 100vh; overflow-x: hidden; }

        /* --- Intro Page Styling --- */
        #intro-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: var(--card-grad); display: flex; flex-direction: column;
            align-items: center; justify-content: center; z-index: 9999;
            transition: transform 0.8s cubic-bezier(0.77, 0, 0.175, 1);
            color: white; text-align: center; padding: 20px;
        }

        .intro-logo {
            font-size: 42px; font-weight: 800; letter-spacing: -2px;
            animation: fadeInDown 1s ease; margin-bottom: 10px;
        }

        .intro-sub {
            font-size: 14px; font-weight: 600; opacity: 0.8; letter-spacing: 3px;
            text-transform: uppercase; animation: fadeInUp 1.2s ease; margin-bottom: 40px;
        }

        .btn-start {
            padding: 18px 45px; border-radius: 50px; border: none;
            background: white; color: var(--primary); font-weight: 800;
            font-size: 16px; cursor: pointer; box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            transition: 0.3s; animation: pulse 2s infinite;
        }

        .btn-start:hover { transform: scale(1.05); background: #f0f3ff; }

        /* --- Main Content (Hidden Initially) --- */
        #main-app { padding: 15px; display: none; flex-direction: column; opacity: 0; transition: 1s; }

        /* --- Animations --- */
        @keyframes fadeInDown { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes fadeInUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(255,255,255,0.4); } 70% { box-shadow: 0 0 0 20px rgba(255,255,255,0); } 100% { box-shadow: 0 0 0 0 rgba(255,255,255,0); } }

        /* --- POS Styling (Same as Before) --- */
        .profit-card { background: var(--card-grad); border-radius: 28px; padding: 25px; color: white; margin-bottom: 30px; aspect-ratio: 1.8 / 1; display: flex; flex-direction: column; justify-content: space-between; box-shadow: 0px 20px 40px rgba(30, 41, 59, 0.25); }
        .card-brand { font-size: 20px; font-weight: 800; letter-spacing: 2px; }
        .card-chip { width: 48px; height: 38px; background: linear-gradient(135deg, #ffd700 0%, #b8860b 100%); border-radius: 8px; }
        .glass-panel { background: var(--glass); backdrop-filter: blur(15px); border: 1px solid var(--glass-border); border-radius: 30px; padding: 25px; margin-bottom: 25px; }
        input { width: 100%; padding: 14px; border-radius: 15px; border: 1px solid #e0e5f2; background: rgba(255,255,255,0.5); font-weight: 600; outline: none; margin-bottom: 15px; }
        .order-row { background: white; border-radius: 20px; padding: 18px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; border: 1px solid #f1f4f9; }
        .status-badge { padding: 6px 10px; border-radius: 8px; font-size: 10px; font-weight: 800; border: none; cursor: pointer; }
        .status-Pending { background: #fff8eb; color: var(--warning); }
        .status-Completed { background: #e6fff5; color: var(--success); }
        footer { margin-top: auto; padding: 25px 0 10px; text-align: center; font-size: 10px; font-weight: 800; color: #a3aed0; letter-spacing: 2px; }
        .btn-main { width: 100%; padding: 16px; border-radius: 18px; border: none; background: var(--card-grad); color: white; font-weight: 800; cursor: pointer; }
        #invoiceModal { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.6); z-index: 2000; align-items: center; justify-content: center; padding: 20px; }
        .invoice-box { background: white; width: 100%; max-width: 380px; border-radius: 30px; padding: 40px 30px; position: relative; }
        .invoice-box::before { content: ""; position: absolute; top:0; left:0; width:100%; height:8px; background: var(--card-grad); border-radius: 30px 30px 0 0; }
        .invoice-row { display: flex; justify-content: space-between; margin-bottom: 12px; font-size: 14px; }
    </style>
</head>
<body>

    <div id="intro-screen">
        <div class="intro-logo">TI NOVA POS 2.0</div>
        <div class="intro-sub">Powered by TREXIVO IT</div>
        <button class="btn-start" onclick="startApp()">GET STARTED</button>
    </div>

    <div id="main-app">
        <h1 style="font-weight: 800; letter-spacing: -1px; color: var(--primary); margin-bottom: 20px;">TI NOVA POS <span style="font-weight: 400; color: #a3aed0;">2.0</span></h1>

        <div class="profit-card">
            <div style="display: flex; justify-content: space-between; align-items: flex-start;">
                <div class="card-brand">TI NOVA</div>
                <div class="card-chip"></div>
            </div>
            <div>
                <p style="font-size: 10px; text-transform: uppercase; letter-spacing: 1.5px; opacity: 0.7; font-weight: 700;">Net Profit Balance</p>
                <h2 id="totalRevenue">$0.00</h2>
            </div>
            <div style="display: flex; justify-content: space-between; align-items: flex-end;">
                <div>
                    <p style="font-size: 10px; text-transform: uppercase; letter-spacing: 1.5px; opacity: 0.7; font-weight: 700;">Outstanding Due</p>
                    <p id="totalDue" style="font-weight: 800; font-size: 18px; color: #ff8fa3;">$0.00</p>
                </div>
                <div style="text-align: right;">
                    <p style="font-size: 10px; text-transform: uppercase; letter-spacing: 1.5px; opacity: 0.7; font-weight: 700;">Premium Admin</p>
                    <p style="font-size: 11px; font-weight: 700; color: rgba(255,255,255,0.6);">SINCE 2026</p>
                </div>
            </div>
        </div>

        <div class="glass-panel">
            <label style="font-size: 11px; font-weight: 800; color: #707eae; margin-left: 5px;">CLIENT NAME</label>
            <input type="text" id="cName">
            <label style="font-size: 11px; font-weight: 800; color: #707eae; margin-left: 5px;">PHONE NUMBER</label>
            <input type="text" id="cPhone">
            <label style="font-size: 11px; font-weight: 800; color: #707eae; margin-left: 5px;">SERVICE</label>
            <input type="text" id="cService">
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                <div><label style="font-size: 11px; font-weight: 800; color: #707eae;">TOTAL BILL</label><input type="number" id="cPrice"></div>
                <div><label style="font-size: 11px; font-weight: 800; color: #707eae;">PAID NOW</label><input type="number" id="cPaid"></div>
            </div>
            <button class="btn-main" onclick="addEntry()">CONFIRM TRANSACTION</button>
        </div>

        <h3 style="margin: 0 0 15px 10px; font-weight: 800;">Recent Sales Log</h3>
        <div id="orderList"></div>

        <footer>POS MADE BY TREXIVO IT</footer>
    </div>

    <div id="invoiceModal">
        <div class="invoice-box">
            <div style="text-align: center; margin-bottom: 25px;">
                <h2 style="color: var(--primary);">TI NOVA POS</h2>
                <p style="font-size: 11px; color: #a3aed0; font-weight: 700;">TRANSACTION RECEIPT</p>
            </div>
            <div id="invoiceData"></div>
            <button class="btn-main" onclick="document.getElementById('invoiceModal').style.display='none'">CLOSE</button>
        </div>
    </div>

<script>
    let db = JSON.parse(localStorage.getItem('nova_final_v6')) || [];

    function startApp() {
        const intro = document.getElementById('intro-screen');
        const main = document.getElementById('main-app');
        
        intro.style.transform = 'translateY(-100%)';
        main.style.display = 'flex';
        setTimeout(() => { main.style.opacity = '1'; }, 100);
    }

    function addEntry() {
        const name = document.getElementById('cName').value;
        const phone = document.getElementById('cPhone').value;
        const service = document.getElementById('cService').value;
        const price = parseFloat(document.getElementById('cPrice').value) || 0;
        const paid = parseFloat(document.getElementById('cPaid').value) || 0;

        if(!name || price <= 0) return alert("Fill Name and Total Price!");

        const now = new Date();
        const order = {
            id: Date.now(),
            orderId: 'TN-' + Math.floor(1000 + Math.random() * 9000),
            name, phone, service, price, paid, 
            status: 'Pending',
            date: now.toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
            time: now.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit' }),
            fullDate: now.toLocaleString()
        };

        db.unshift(order);
        sync();
        ['cName','cPhone','cService','cPrice','cPaid'].forEach(id => document.getElementById(id).value = '');
    }

    function deleteOrder(id) {
        if(confirm("Delete this record?")) {
            db = db.filter(o => o.id !== id);
            sync();
        }
    }

    function clearDue(id) {
        const idx = db.findIndex(o => o.id === id);
        db[idx].paid = db[idx].price;
        sync();
    }

    function toggleStatus(id) {
        const idx = db.findIndex(o => o.id === id);
        db[idx].status = db[idx].status === 'Pending' ? 'Completed' : 'Pending';
        sync();
    }

    function viewInvoice(id) {
        const item = db.find(o => o.id === id);
        document.getElementById('invoiceData').innerHTML = `
            <div class="invoice-row"><span>Order ID</span><b>#${item.orderId}</b></div>
            <div class="invoice-row"><span>Customer</span><b>${item.name}</b></div>
            <div class="invoice-row"><span>Status</span><b>${item.status}</b></div>
            <div class="invoice-row" style="margin-top:10px; border-top:1px dashed #eee; padding-top:10px;"><span>Total Bill</span><b>$${item.price}</b></div>
            <div class="invoice-row" style="color:var(--success)"><span>Total Paid</span><b>$${item.paid}</b></div>
            <div class="invoice-row" style="color:var(--danger)"><span>Balance Due</span><b>$${item.price - item.paid}</b></div>
        `;
        document.getElementById('invoiceModal').style.display = 'flex';
    }

    function sync() {
        localStorage.setItem('nova_final_v6', JSON.stringify(db));
        render();
    }

    function render() {
        const list = document.getElementById('orderList');
        list.innerHTML = '';
        let rev = 0, due = 0;

        db.forEach(item => {
            const itemDue = item.price - item.paid;
            rev += item.paid;
            due += itemDue;

            list.innerHTML += `
                <div class="order-row">
                    <div class="order-meta">
                        <span class="tag">#${item.orderId}</span>
                        <b style="font-size:14px; display:block;">${item.name}</b>
                        <div style="font-size:10px; color:var(--primary); font-weight:700;">🕒 ${item.date} • ${item.time}</div>
                    </div>
                    <div style="display:flex; align-items:center; gap:8px;">
                        <div style="text-align:right;">
                            <div style="font-weight:800; font-size:13px;">$${item.price}</div>
                            ${itemDue > 0 ? `<button style="background:var(--success); color:white; border:none; padding:4px 8px; border-radius:6px; font-size:9px; font-weight:800; cursor:pointer;" onclick="clearDue(${item.id})">Pay $${itemDue}</button>` : `<span style="color:var(--success); font-size:10px; font-weight:800;">PAID ✓</span>`}
                        </div>
                        <button class="status-badge status-${item.status}" onclick="toggleStatus(${item.id})">${item.status}</button>
                        <button style="border:none; background:none; font-size:20px; cursor:pointer;" onclick="viewInvoice(${item.id})">📄</button>
                        <button style="border:none; background:#fff1f1; color:var(--danger); padding:8px; border-radius:10px; cursor:pointer;" onclick="deleteOrder(${item.id})">🗑️</button>
                    </div>
                </div>
            `;
        });

        document.getElementById('totalRevenue').innerText = '$' + rev.toLocaleString();
        document.getElementById('totalDue').innerText = '$' + due.toLocaleString();
    }

    window.onload = render;
</script>
</body>
</html>
