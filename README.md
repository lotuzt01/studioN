<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PalmCore ERP - Full Reporting</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f8fafc; }
        .glass-panel { background: white; border: 1px solid #e2e8f0; }
        .nav-btn.active { background: #059669 !important; color: white !important; box-shadow: 0 10px 15px -3px rgba(5, 150, 105, 0.2); }
        
        .modal-overlay {
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.5);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 100000;
        }

        #custom-toast {
            position: fixed;
            bottom: 2rem;
            left: 50%;
            transform: translateX(-50%) translateY(100px);
            background: #1e293b;
            color: white;
            padding: 1rem 2rem;
            border-radius: 1rem;
            font-weight: bold;
            transition: transform 0.3s ease;
            z-index: 999999;
        }
        #custom-toast.show { transform: translateX(-50%) translateY(0); }
    </style>
</head>
<body class="text-slate-700">

    <div id="custom-toast">Pesan Notifikasi</div>

    <div id="delete-modal" class="modal-overlay">
        <div class="bg-white p-8 rounded-3xl max-w-sm w-full mx-4 shadow-2xl text-center">
            <div class="text-rose-500 mb-4"><i class="fas fa-exclamation-triangle text-5xl"></i></div>
            <h3 class="text-xl font-black mb-2">Hapus Transaksi?</h3>
            <p class="text-slate-500 text-sm mb-6">Data ini akan dihapus permanen.</p>
            <div class="flex gap-3">
                <button onclick="closeDeleteModal()" class="flex-1 py-3 bg-slate-100 font-bold rounded-xl">Batal</button>
                <button id="confirm-delete-btn" class="flex-1 py-3 bg-rose-600 text-white font-bold rounded-xl">Hapus</button>
            </div>
        </div>
    </div>

    <div id="app-body">
        <nav class="fixed left-0 top-0 h-full w-20 lg:w-64 bg-white border-r border-slate-200 z-50">
            <div class="p-6 flex flex-col h-full">
                <div class="flex items-center gap-3 mb-10">
                    <div class="bg-emerald-600 p-2 rounded-xl text-white"><i class="fas fa-leaf text-xl"></i></div>
                    <span class="font-bold text-xl hidden lg:block text-emerald-900">PalmCore<span class="text-emerald-500">ERP</span></span>
                </div>
                
                <div class="space-y-2 flex-1">
                    <button id="nav-dashboard" onclick="navTo('dashboard')" class="nav-btn active w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-chart-pie w-5"></i><span class="font-bold hidden lg:block text-sm">Dashboard</span>
                    </button>
                    <button id="nav-beli" onclick="navTo('beli')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-balance-scale w-5"></i><span class="font-bold hidden lg:block text-sm">Beli TBS</span>
                    </button>
                    <button id="nav-logistik" onclick="navTo('logistik')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-truck-loading w-5"></i><span class="font-bold hidden lg:block text-sm">Kirim DO</span>
                    </button>
                    <button id="nav-jual" onclick="navTo('jual')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-file-invoice-dollar w-5"></i><span class="font-bold hidden lg:block text-sm">Klaim PKS</span>
                    </button>
                    <button id="nav-biaya" onclick="navTo('biaya')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-money-bill-wave w-5"></i><span class="font-bold hidden lg:block text-sm">Biaya</span>
                    </button>
                    <button id="nav-laporan" onclick="navTo('laporan')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-list-alt w-5"></i><span class="font-bold hidden lg:block text-sm">Riwayat</span>
                    </button>
                    <button id="nav-rekap" onclick="navTo('rekap')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-file-contract w-5"></i><span class="font-bold hidden lg:block text-sm">Rekap Bulanan</span>
                    </button>
                </div>
            </div>
        </nav>

        <main class="ml-20 lg:ml-64 p-8 min-h-screen">
            <!-- Dashboard -->
            <div id="page-dashboard" class="page-content">
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4 mb-8">
                    <div class="glass-panel p-5 rounded-3xl border-b-4 border-emerald-500 shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Stok RAM</p>
                        <h2 id="stok-val" class="text-2xl font-black text-slate-800">0</h2>
                        <span class="text-[10px] text-slate-400">Kg</span>
                    </div>
                    <div class="glass-panel p-5 rounded-3xl border-b-4 border-blue-500 shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Perjalanan (OTW)</p>
                        <h2 id="otw-val" class="text-2xl font-black text-blue-600">0</h2>
                        <span class="text-[10px] text-slate-400">Kg</span>
                    </div>
                    <div class="glass-panel p-5 rounded-3xl border-b-4 border-amber-500 shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Total Susut</p>
                        <h2 id="shrink-val" class="text-2xl font-black text-amber-600">0 Kg</h2>
                        <span id="shrink-kg-val" class="text-[10px] text-slate-400">Seluruh Periode</span>
                    </div>
                    <div class="glass-panel p-5 rounded-3xl border-b-4 border-rose-500 shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Total Biaya</p>
                        <h2 id="biaya-val" class="text-xl font-black text-rose-600">Rp 0</h2>
                    </div>
                    <div class="bg-slate-900 p-5 rounded-3xl text-white shadow-xl">
                        <p class="text-[10px] font-bold text-emerald-400 uppercase tracking-widest">Est. Profit</p>
                        <h2 id="profit-val" class="text-xl font-black text-emerald-400">Rp 0</h2>
                    </div>
                </div>
                <div class="glass-panel p-8 rounded-3xl h-[400px] shadow-sm">
                    <h3 class="font-bold text-slate-500 mb-4 text-xs uppercase tracking-widest">Volume Pembelian (Per Hari)</h3>
                    <canvas id="chartView"></canvas>
                </div>
            </div>

            <!-- Page Beli -->
            <div id="page-beli" class="page-content hidden">
                <div class="max-w-4xl mx-auto glass-panel p-8 rounded-[2rem] border-t-8 border-emerald-500 shadow-xl">
                    <h3 class="font-black text-2xl text-emerald-900 mb-6">Input TBS Masuk (RAM)</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-10">
                        <div class="space-y-5">
                            <input id="b-nama" type="text" placeholder="Nama Pemasok" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                            <div class="grid grid-cols-2 gap-4">
                                <input id="b-bruto" type="number" oninput="hitungBeli()" placeholder="Brutto" class="w-full p-4 bg-slate-50 border rounded-2xl">
                                <input id="b-tara" type="number" oninput="hitungBeli()" placeholder="Potongan Tarra" class="w-full p-4 bg-slate-50 border rounded-2xl">
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <div class="relative">
                                    <span class="absolute left-4 top-1 text-[9px] font-bold text-rose-400 uppercase">Pot %</span>
                                    <input id="b-persen" type="number" oninput="hitungBeli()" value="3" class="w-full pt-6 pb-2 px-4 bg-rose-50 border-2 border-rose-100 rounded-2xl font-bold text-rose-700">
                                </div>
                                <div class="relative">
                                    <span class="absolute left-4 top-1 text-[9px] font-bold text-emerald-400 uppercase">Harga</span>
                                    <input id="b-harga" type="number" oninput="hitungBeli()" placeholder="Rp" class="w-full pt-6 pb-2 px-4 bg-emerald-50 border-2 border-emerald-200 rounded-2xl font-bold text-emerald-700">
                                </div>
                            </div>
                            <input type="date" id="b-date" class="w-full p-4 bg-slate-50 border rounded-2xl text-sm">
                        </div>
                        <div class="bg-emerald-900 rounded-[2.5rem] p-10 text-white flex flex-col justify-center text-center">
                            <h2 id="res-total" class="text-5xl font-black mb-4">Rp 0</h2>
                            <p class="text-sm opacity-80 uppercase tracking-tighter">Netto Berat: <span id="res-netto" class="font-black">0</span> Kg</p>
                            <button onclick="simpan('BELI')" class="w-full py-5 mt-10 bg-emerald-500 hover:bg-emerald-400 rounded-2xl font-black shadow-lg transition-all">SIMPAN TRANSAKSI</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Page Logistik -->
            <div id="page-logistik" class="page-content hidden">
                <div class="max-w-4xl mx-auto glass-panel p-8 rounded-[2rem] border-t-8 border-blue-500 shadow-xl">
                    <h3 class="font-black text-2xl text-blue-900 mb-6">Logistik: Kirim TBS</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-10">
                        <div class="space-y-5">
                            <input id="l-do" type="text" placeholder="Nomor DO / Surat Jalan" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                            <div class="grid grid-cols-2 gap-4">
                                <input id="l-supir" type="text" placeholder="Supir" class="w-full p-4 bg-slate-50 border rounded-2xl">
                                <input id="l-plat" type="text" placeholder="No. Polisi" class="w-full p-4 bg-slate-50 border rounded-2xl">
                            </div>
                            <input id="l-pks" type="text" placeholder="PKS Tujuan" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                            <div class="grid grid-cols-2 gap-4">
                                <input id="l-bruto" type="number" oninput="hitungLogistik()" placeholder="Brutto RAM" class="w-full p-4 bg-slate-50 border rounded-2xl">
                                <input id="l-tara" type="number" oninput="hitungLogistik()" placeholder="Tarra RAM" class="w-full p-4 bg-slate-50 border rounded-2xl">
                            </div>
                            <input type="date" id="l-date" class="w-full p-4 bg-slate-50 border rounded-2xl text-sm">
                        </div>
                        <div class="bg-blue-900 rounded-[2.5rem] p-10 text-white flex flex-col justify-center text-center">
                            <h2 id="res-log-netto" class="text-6xl font-black mb-4">0</h2>
                            <p class="text-sm uppercase font-bold tracking-widest text-blue-300">Kilogram (Berangkat)</p>
                            <button onclick="simpan('LOGISTIK')" class="w-full py-5 mt-10 bg-blue-500 hover:bg-blue-400 rounded-2xl font-black shadow-lg transition-all">CATAT PENGIRIMAN</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Page Jual -->
            <div id="page-jual" class="page-content hidden">
                <div class="max-w-4xl mx-auto glass-panel p-8 rounded-[2rem] border-t-8 border-indigo-500 shadow-xl">
                    <div class="flex justify-between items-start mb-6">
                        <h3 class="font-black text-2xl text-indigo-900">Input Klaim Hasil PKS</h3>
                        <select id="j-do-ref" onchange="autoFillDO()" class="p-2 bg-slate-100 border rounded-lg text-sm font-bold">
                            <option value="">-- Hubungkan DO --</option>
                        </select>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-10">
                        <div class="space-y-4">
                            <div class="grid grid-cols-2 gap-4">
                                <input id="j-pabrik" type="text" placeholder="Nama PKS" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                                <input id="j-do-num" type="text" placeholder="No. DO" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <input id="j-bruto" type="number" oninput="hitungJual()" placeholder="Brutto Pabrik" class="w-full p-4 bg-slate-50 border rounded-2xl">
                                <input id="j-tara" type="number" oninput="hitungJual()" placeholder="Tarra Pabrik" class="w-full p-4 bg-slate-50 border rounded-2xl">
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <input id="j-persen" type="number" oninput="hitungJual()" placeholder="Sortasi %" class="w-full p-4 bg-indigo-50 border rounded-2xl">
                                <input id="j-harga" type="number" oninput="hitungJual()" placeholder="Harga Jual" class="w-full p-4 bg-indigo-50 border rounded-2xl">
                            </div>
                            <input type="date" id="j-date" class="w-full p-4 bg-slate-50 border rounded-2xl text-sm">
                            <div id="diff-box" class="hidden p-4 bg-amber-50 border border-amber-200 rounded-2xl">
                                <div class="flex justify-between font-bold text-amber-700 text-xs mb-1">
                                    <span>SELISIH MUATAN</span>
                                    <span id="diff-percent">0%</span>
                                </div>
                                <div class="text-2xl font-black text-amber-900" id="diff-kg">0 Kg</div>
                            </div>
                        </div>
                        <div class="bg-indigo-900 rounded-[2.5rem] p-10 text-white flex flex-col justify-center text-center">
                            <h2 id="res-jual-total" class="text-5xl font-black mb-4">Rp 0</h2>
                            <p class="text-sm opacity-80 uppercase">Netto PKS: <span id="res-jual-netto" class="font-black">0</span> Kg</p>
                            <button onclick="simpan('JUAL')" class="w-full py-5 mt-10 bg-indigo-500 hover:bg-indigo-400 rounded-2xl font-black shadow-lg transition-all">SIMPAN PENJUALAN</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Page Laporan Riwayat -->
            <div id="page-laporan" class="page-content hidden">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-black">Riwayat Transaksi</h2>
                    <button onclick="exportExcel()" class="bg-emerald-600 text-white px-5 py-2 rounded-xl text-sm font-bold">Export Excel</button>
                </div>
                <div class="glass-panel rounded-3xl overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full text-sm text-left">
                            <thead class="bg-slate-50 border-b">
                                <tr>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400">Tanggal</th>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400">Tipe</th>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400">Keterangan</th>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400 text-right">Netto (Kg)</th>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400 text-right">Susut (Kg)</th>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400 text-right">Nilai Rp</th>
                                    <th class="p-4 uppercase text-[10px] font-bold text-slate-400 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="table-log" class="divide-y divide-slate-100"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- Page Rekap Bulanan -->
            <div id="page-rekap" class="page-content hidden">
                <div class="flex justify-between items-center mb-8">
                    <div>
                        <h2 class="text-2xl font-black text-slate-800">Rekapitulasi Laporan Bulanan</h2>
                        <p class="text-slate-400 text-sm">Rangkuman kinerja pembelian, penjualan, dan operasional.</p>
                    </div>
                    <div class="flex gap-2">
                        <select id="rekap-year" onchange="renderRekap()" class="p-3 bg-white border rounded-xl font-bold">
                            <option value="2025">2025</option>
                            <option value="2026">2026</option>
                        </select>
                    </div>
                </div>

                <div class="grid grid-cols-1 gap-6" id="rekap-container">
                    <!-- Cards per Month will be rendered here -->
                </div>
            </div>

            <!-- Page Biaya -->
            <div id="page-biaya" class="page-content hidden">
                <div class="max-w-xl mx-auto glass-panel p-8 rounded-[2rem] border-t-8 border-rose-500 shadow-xl">
                    <h3 class="font-black text-2xl mb-6 text-rose-900 text-center">Catat Biaya Operasional</h3>
                    <div class="space-y-5">
                        <input id="c-ket" type="text" placeholder="Keterangan (Gaji/Bensin/Sewa)" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                        <input id="c-nominal" type="number" placeholder="Nominal Rp" class="w-full p-4 bg-rose-50 border-2 border-rose-100 rounded-2xl font-bold text-rose-700">
                        <input type="date" id="c-date" class="w-full p-4 bg-slate-50 border rounded-2xl text-sm">
                        <button onclick="simpan('BIAYA')" class="w-full py-4 bg-rose-600 hover:bg-rose-700 text-white font-black rounded-2xl shadow-lg transition-all">SIMPAN BIAYA</button>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <script>
        // Global State
        let dataStore = JSON.parse(localStorage.getItem('sawit_erp_data_v2')) || [];
        let pendingDeleteIdx = null;

        // Configuration
        const months = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"];

        // Init Helpers
        function setTodayDates() {
            const today = new Date().toISOString().split('T')[0];
            ['b-date', 'l-date', 'j-date', 'c-date'].forEach(id => {
                document.getElementById(id).value = today;
            });
        }

        function saveData() {
            localStorage.setItem('sawit_erp_data_v2', JSON.stringify(dataStore));
            renderAll();
            updateDOSelect();
            if(!document.getElementById('page-rekap').classList.contains('hidden')) renderRekap();
        }

        function navTo(id) {
            document.querySelectorAll('.page-content').forEach(p => p.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
            document.getElementById('page-' + id).classList.remove('hidden');
            document.getElementById('nav-' + id).classList.add('active');
            if(id === 'rekap') renderRekap();
        }

        function showToast(msg) {
            const toast = document.getElementById('custom-toast');
            toast.innerText = msg;
            toast.classList.add('show');
            setTimeout(() => toast.classList.remove('show'), 3000);
        }

        // --- CALCULATIONS ---
        function hitungBeli() {
            const b = parseFloat(document.getElementById('b-bruto').value) || 0;
            const t = parseFloat(document.getElementById('b-tara').value) || 0;
            const p = parseFloat(document.getElementById('b-persen').value) || 0;
            const h = parseFloat(document.getElementById('b-harga').value) || 0;
            const netto = (b - t) - Math.round((b - t) * (p / 100));
            const total = netto * h;
            document.getElementById('res-netto').innerText = netto.toLocaleString();
            document.getElementById('res-total').innerText = 'Rp ' + total.toLocaleString();
            return { netto, total };
        }

        function hitungLogistik() {
            const b = parseFloat(document.getElementById('l-bruto').value) || 0;
            const t = parseFloat(document.getElementById('l-tara').value) || 0;
            const netto = b - t;
            document.getElementById('res-log-netto').innerText = netto.toLocaleString();
            return { netto };
        }

        function hitungJual() {
            const b = parseFloat(document.getElementById('j-bruto').value) || 0;
            const t = parseFloat(document.getElementById('j-tara').value) || 0;
            const p = parseFloat(document.getElementById('j-persen').value) || 0;
            const h = parseFloat(document.getElementById('j-harga').value) || 0;
            const netto = (b - t) - Math.round((b - t) * (p / 100));
            const total = netto * h;
            document.getElementById('res-jual-netto').innerText = netto.toLocaleString();
            document.getElementById('res-jual-total').innerText = 'Rp ' + total.toLocaleString();

            const doRefId = document.getElementById('j-do-ref').value;
            if(doRefId) {
                const doData = dataStore.find(d => d.id == doRefId);
                const pksNettoBruto = (b - t);
                const diff = doData.netto - pksNettoBruto;
                const diffPerc = ((diff / doData.netto) * 100).toFixed(2);
                document.getElementById('diff-box').classList.remove('hidden');
                document.getElementById('diff-kg').innerText = diff.toLocaleString() + ' Kg';
                document.getElementById('diff-percent').innerText = diffPerc + '%';
            } else {
                document.getElementById('diff-box').classList.add('hidden');
            }
            return { netto, total };
        }

        function autoFillDO() {
            const id = document.getElementById('j-do-ref').value;
            if(!id) return;
            const d = dataStore.find(x => x.id == id);
            document.getElementById('j-pabrik').value = d.pks;
            document.getElementById('j-do-num').value = d.do;
            hitungJual();
        }

        function updateDOSelect() {
            const sel = document.getElementById('j-do-ref');
            sel.innerHTML = '<option value="">-- Hubungkan DO --</option>';
            const claimedIds = dataStore.filter(d => d.tipe === 'JUAL' && d.refId).map(d => d.refId);
            dataStore.filter(d => d.tipe === 'LOGISTIK' && !claimedIds.includes(d.id.toString())).forEach(d => {
                sel.insertAdjacentHTML('beforeend', `<option value="${d.id}">${d.do} (${d.netto}Kg)</option>`);
            });
        }

        // --- CRUD ---
        function simpan(tipe) {
            let p = { tipe, id: Date.now() };
            
            if(tipe === 'BELI') {
                const r = hitungBeli();
                p.nama = document.getElementById('b-nama').value || 'Pemasok Umum';
                p.netto = r.netto; p.total = r.total; p.ts = new Date(document.getElementById('b-date').value).getTime();
            } else if(tipe === 'LOGISTIK') {
                const r = hitungLogistik();
                p.do = document.getElementById('l-do').value;
                p.pks = document.getElementById('l-pks').value;
                p.nama = `Kirim: ${p.do}`;
                p.netto = r.netto; p.total = 0; p.ts = new Date(document.getElementById('l-date').value).getTime();
            } else if(tipe === 'JUAL') {
                const r = hitungJual();
                p.refId = document.getElementById('j-do-ref').value;
                p.do = document.getElementById('j-do-num').value;
                p.nama = `Klaim: ${p.do}`;
                p.netto = r.netto; p.total = r.total; p.ts = new Date(document.getElementById('j-date').value).getTime();
                if(p.refId) {
                    const doData = dataStore.find(d => d.id == p.refId);
                    const pksMurni = (parseFloat(document.getElementById('j-bruto').value) || 0) - (parseFloat(document.getElementById('j-tara').value) || 0);
                    p.susut = doData.netto - pksMurni;
                }
            } else {
                p.nama = document.getElementById('c-ket').value || 'Biaya';
                p.total = parseFloat(document.getElementById('c-nominal').value) || 0;
                p.netto = 0; p.ts = new Date(document.getElementById('c-date').value).getTime();
            }

            dataStore.unshift(p);
            saveData();
            showToast("Transaksi Berhasil Dicatat");
            setTodayDates();
        }

        function openDeleteModal(id) {
            pendingDeleteIdx = id;
            document.getElementById('delete-modal').style.display = 'flex';
        }

        function closeDeleteModal() {
            document.getElementById('delete-modal').style.display = 'none';
        }

        document.getElementById('confirm-delete-btn').onclick = () => {
            dataStore = dataStore.filter(d => d.id !== pendingDeleteIdx);
            saveData();
            closeDeleteModal();
            showToast("Data dihapus");
        };

        // --- RENDERING ---
        function renderAll() {
            const table = document.getElementById('table-log');
            table.innerHTML = '';
            
            let stok=0, otw=0, exp=0, beliRp=0, jualRp=0, susut=0;
            const claimed = dataStore.filter(d => d.tipe === 'JUAL').map(d => d.refId);

            dataStore.forEach(d => {
                const dateStr = new Date(d.ts).toLocaleDateString('id-ID');
                const rowClass = d.tipe === 'BELI' ? 'text-emerald-600' : (d.tipe === 'JUAL' ? 'text-indigo-600' : '');

                if(d.tipe === 'BELI') { stok += d.netto; beliRp += d.total; }
                if(d.tipe === 'LOGISTIK') { stok -= d.netto; if(!claimed.includes(d.id.toString())) otw += d.netto; }
                if(d.tipe === 'JUAL') { jualRp += d.total; if(d.susut) susut += d.susut; }
                if(d.tipe === 'BIAYA') { exp += d.total; }

                table.insertAdjacentHTML('beforeend', `<tr class="border-b hover:bg-slate-50">
                    <td class="p-4 text-[10px] font-bold text-slate-400">${dateStr}</td>
                    <td class="p-4"><span class="px-2 py-0.5 rounded-[4px] text-[9px] font-black uppercase ${d.tipe==='BELI'?'bg-emerald-100 text-emerald-700':d.tipe==='JUAL'?'bg-indigo-100 text-indigo-700':'bg-slate-100'}">${d.tipe}</span></td>
                    <td class="p-4 font-bold">${d.nama}</td>
                    <td class="p-4 text-right font-medium">${d.netto ? d.netto.toLocaleString() : '-'}</td>
                    <td class="p-4 text-right text-amber-600 font-bold">${d.susut ? d.susut.toLocaleString() : '-'}</td>
                    <td class="p-4 text-right font-black ${rowClass}">Rp ${d.total.toLocaleString()}</td>
                    <td class="p-4 text-center"><button onclick="openDeleteModal(${d.id})" class="text-slate-300 hover:text-rose-500"><i class="fas fa-trash-alt"></i></button></td>
                </tr>`);
            });

            document.getElementById('stok-val').innerText = stok.toLocaleString();
            document.getElementById('otw-val').innerText = otw.toLocaleString();
            document.getElementById('biaya-val').innerText = 'Rp ' + exp.toLocaleString();
            document.getElementById('shrink-val').innerText = susut.toLocaleString() + ' Kg';
            document.getElementById('profit-val').innerText = 'Rp ' + (jualRp - beliRp - exp).toLocaleString();

            updateChart();
        }

        function renderRekap() {
            const container = document.getElementById('rekap-container');
            const targetYear = document.getElementById('rekap-year').value;
            container.innerHTML = '';

            // Group by Month
            for (let m = 11; m >= 0; m--) {
                const monthData = dataStore.filter(d => {
                    const dt = new Date(d.ts);
                    return dt.getMonth() === m && dt.getFullYear().toString() === targetYear;
                });

                if (monthData.length === 0) continue;

                let mBeli = 0, mJual = 0, mBiaya = 0, mNettoBeli = 0, mNettoJual = 0, mSusut = 0;
                monthData.forEach(d => {
                    if(d.tipe === 'BELI') { mBeli += d.total; mNettoBeli += d.netto; }
                    if(d.tipe === 'JUAL') { mJual += d.total; mNettoJual += d.netto; if(d.susut) mSusut += d.susut; }
                    if(d.tipe === 'BIAYA') { mBiaya += d.total; }
                });

                const profit = mJual - mBeli - mBiaya;

                container.insertAdjacentHTML('beforeend', `
                    <div class="glass-panel p-6 rounded-[2rem] shadow-sm border-l-8 ${profit >= 0 ? 'border-emerald-500' : 'border-rose-500'}">
                        <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 gap-4">
                            <div>
                                <h3 class="text-xl font-black text-slate-800">${months[m]} ${targetYear}</h3>
                                <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Ringkasan Performa Bulanan</p>
                            </div>
                            <div class="text-right">
                                <span class="text-[10px] font-bold text-slate-400 block uppercase">Laba Bersih</span>
                                <span class="text-2xl font-black ${profit >= 0 ? 'text-emerald-600' : 'text-rose-600'}">Rp ${profit.toLocaleString()}</span>
                            </div>
                        </div>
                        <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                            <div class="bg-slate-50 p-4 rounded-2xl">
                                <p class="text-[9px] font-bold text-slate-400 uppercase">Total Beli</p>
                                <p class="font-bold">Rp ${mBeli.toLocaleString()}</p>
                                <p class="text-[10px] text-slate-500">${mNettoBeli.toLocaleString()} Kg</p>
                            </div>
                            <div class="bg-slate-50 p-4 rounded-2xl">
                                <p class="text-[9px] font-bold text-slate-400 uppercase">Total Jual</p>
                                <p class="font-bold">Rp ${mJual.toLocaleString()}</p>
                                <p class="text-[10px] text-slate-500">${mNettoJual.toLocaleString()} Kg</p>
                            </div>
                            <div class="bg-slate-50 p-4 rounded-2xl">
                                <p class="text-[9px] font-bold text-slate-400 uppercase">Operasional</p>
                                <p class="font-bold text-rose-600">Rp ${mBiaya.toLocaleString()}</p>
                            </div>
                            <div class="bg-amber-50 p-4 rounded-2xl border border-amber-100">
                                <p class="text-[9px] font-bold text-amber-600 uppercase">Total Susut</p>
                                <p class="font-bold text-amber-700">${mSusut.toLocaleString()} Kg</p>
                                <p class="text-[10px] text-amber-500">Logistik PKS</p>
                            </div>
                        </div>
                    </div>
                `);
            }
            
            if (container.innerHTML === '') {
                container.innerHTML = '<div class="text-center py-20 text-slate-400 font-bold">Tidak ada data untuk tahun ini.</div>';
            }
        }

        let mainChart;
        function updateChart() {
            const ctx = document.getElementById('chartView').getContext('2d');
            const last7Days = [...Array(7)].map((_, i) => {
                const d = new Date();
                d.setDate(d.getDate() - i);
                return d.toISOString().split('T')[0];
            }).reverse();

            const dataset = last7Days.map(date => {
                return dataStore
                    .filter(d => d.tipe === 'BELI' && new Date(d.ts).toISOString().split('T')[0] === date)
                    .reduce((acc, curr) => acc + curr.netto, 0);
            });

            if(mainChart) mainChart.destroy();
            mainChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: last7Days.map(d => d.split('-').reverse().slice(0,2).join('/')),
                    datasets: [{ label: 'Tonase (Kg)', data: dataset, backgroundColor: '#10b981', borderRadius: 10 }]
                },
                options: { maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
        }

        function exportExcel() {
            const ws = XLSX.utils.json_to_sheet(dataStore.map(d => ({
                Tanggal: new Date(d.ts).toLocaleDateString(),
                Tipe: d.tipe,
                Nama: d.nama,
                Netto: d.netto,
                Susut: d.susut || 0,
                Total: d.total
            })));
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Laporan");
            XLSX.writeFile(wb, "PalmCore_ERP_FullReport.xlsx");
        }

        window.onload = () => {
            setTodayDates();
            renderAll();
            updateDOSelect();
        };
    </script>
</body>
</html>
