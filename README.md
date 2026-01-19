<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PalmCore ERP - RAM Sawit Edition</title>
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

        @media print {
            body * { visibility: hidden; }
            #print-area, #print-area * { visibility: visible; }
            #print-area { position: absolute; left: 0; top: 0; width: 80mm; padding: 5mm; color: black !important; }
            .no-print { display: none !important; }
        }
    </style>
</head>
<body class="text-slate-700">

    <div id="custom-toast">Notifikasi</div>

    <!-- Area Cetak Struk (Tersembunyi di Layar) -->
    <div id="print-area" class="hidden text-black font-mono text-sm">
        <div class="text-center border-b border-dashed pb-2 mb-2">
            <h2 class="font-bold text-lg">RAM SAWIT BERKAH</h2>
            <p>Bukti Timbangan TBS</p>
        </div>
        <div id="print-content"></div>
        <div class="text-center border-t border-dashed mt-4 pt-2">
            <p>Terima Kasih</p>
            <p class="text-[10px]" id="print-timestamp"></p>
        </div>
    </div>

    <!-- Modals -->
    <div id="delete-modal" class="modal-overlay">
        <div class="bg-white p-8 rounded-3xl max-w-sm w-full mx-4 shadow-2xl text-center">
            <div class="text-rose-500 mb-4"><i class="fas fa-trash-alt text-5xl"></i></div>
            <h3 class="text-xl font-black mb-2">Hapus Data?</h3>
            <p class="text-slate-500 text-sm mb-6">Data ini akan dihapus secara permanen.</p>
            <div class="flex gap-3">
                <button onclick="closeDeleteModal()" class="flex-1 py-3 bg-slate-100 font-bold rounded-xl">Batal</button>
                <button id="confirm-delete-btn" class="flex-1 py-3 bg-rose-600 text-white font-bold rounded-xl">Hapus</button>
            </div>
        </div>
    </div>

    <div id="app-body">
        <nav class="fixed left-0 top-0 h-full w-20 lg:w-64 bg-white border-r border-slate-200 z-50 no-print">
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
                        <i class="fas fa-shopping-basket w-5"></i><span class="font-bold hidden lg:block text-sm">Beli TBS</span>
                    </button>
                    <button id="nav-logistik" onclick="navTo('logistik')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-truck w-5"></i><span class="font-bold hidden lg:block text-sm">Logistik</span>
                    </button>
                    <button id="nav-jual" onclick="navTo('jual')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-file-invoice-dollar w-5"></i><span class="font-bold hidden lg:block text-sm">Klaim PKS</span>
                    </button>
                    <button id="nav-biaya" onclick="navTo('biaya')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-wallet w-5"></i><span class="font-bold hidden lg:block text-sm">Biaya</span>
                    </button>
                    <button id="nav-laporan" onclick="navTo('laporan')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-history w-5"></i><span class="font-bold hidden lg:block text-sm">Riwayat</span>
                    </button>
                    <button id="nav-rekap" onclick="navTo('rekap')" class="nav-btn w-full flex items-center gap-4 p-3 rounded-xl text-slate-500 transition-all">
                        <i class="fas fa-calendar-check w-5"></i><span class="font-bold hidden lg:block text-sm">Rekap</span>
                    </button>
                </div>
            </div>
        </nav>

        <main class="ml-20 lg:ml-64 p-8 min-h-screen no-print">
            <!-- DASHBOARD -->
            <div id="page-dashboard" class="page-content">
                <h1 class="text-2xl font-black mb-6">Ringkasan Operasional</h1>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-8">
                    <div class="glass-panel p-6 rounded-3xl shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Stok RAM</p>
                        <h2 id="stok-val" class="text-3xl font-black text-emerald-600">0</h2>
                        <span class="text-xs text-slate-400 font-bold">Kg</span>
                    </div>
                    <div class="glass-panel p-6 rounded-3xl shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">OTW PKS</p>
                        <h2 id="otw-val" class="text-3xl font-black text-blue-600">0</h2>
                        <span class="text-xs text-slate-400 font-bold">Kg</span>
                    </div>
                    <div class="glass-panel p-6 rounded-3xl shadow-sm">
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Total Susut</p>
                        <h2 id="shrink-val" class="text-3xl font-black text-amber-600">0</h2>
                        <span class="text-xs text-slate-400 font-bold">Kg</span>
                    </div>
                    <div class="bg-slate-900 p-6 rounded-3xl shadow-xl text-white">
                        <p class="text-[10px] font-bold text-emerald-400 uppercase tracking-widest">Laba Bersih</p>
                        <h2 id="profit-val" class="text-2xl font-black">Rp 0</h2>
                        <span class="text-[10px] opacity-60">Estimasi Real-time</span>
                    </div>
                </div>
                <div class="glass-panel p-8 rounded-3xl h-[400px]">
                    <canvas id="chartView"></canvas>
                </div>
            </div>

            <!-- PAGE BELI -->
            <div id="page-beli" class="page-content hidden">
                <div class="max-w-5xl mx-auto glass-panel p-10 rounded-[2.5rem] border-t-8 border-emerald-500 shadow-2xl">
                    <div class="flex justify-between items-center mb-8">
                        <div>
                            <h3 class="font-black text-3xl text-emerald-900">Timbang TBS Masuk</h3>
                            <p class="text-slate-400">Brutto - Tarra = Kotor | Kotor - Pot% = Netto</p>
                        </div>
                        <button onclick="setTodayDates()" class="text-emerald-600 font-bold"><i class="fas fa-sync-alt mr-2"></i>Reset Form</button>
                    </div>
                    
                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-12">
                        <div class="space-y-6">
                            <div>
                                <label class="block text-[10px] font-black text-slate-400 uppercase mb-2">Pemasok</label>
                                <input id="b-nama" type="text" placeholder="Nama Petani" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-[10px] font-black text-slate-400 uppercase mb-2">Brutto (Kg)</label>
                                    <input id="b-bruto" type="number" oninput="hitungBeli()" class="w-full p-4 border rounded-2xl font-black text-lg">
                                </div>
                                <div>
                                    <label class="block text-[10px] font-black text-slate-400 uppercase mb-2">Tarra (Kg)</label>
                                    <input id="b-tara" type="number" oninput="hitungBeli()" class="w-full p-4 border rounded-2xl font-black text-lg">
                                </div>
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-[10px] font-black text-rose-500 uppercase mb-2">Potongan (%)</label>
                                    <input id="b-persen" type="number" oninput="hitungBeli()" value="3" class="w-full p-4 bg-rose-50 border-rose-100 rounded-2xl font-black text-rose-700 text-lg">
                                </div>
                                <div>
                                    <label class="block text-[10px] font-black text-emerald-600 uppercase mb-2">Harga (Rp)</label>
                                    <input id="b-harga" type="number" oninput="hitungBeli()" class="w-full p-4 bg-emerald-50 border-emerald-200 rounded-2xl font-black text-emerald-700 text-lg">
                                </div>
                            </div>
                            <input type="date" id="b-date" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                        </div>

                        <div class="bg-emerald-900 rounded-[3rem] p-10 text-white flex flex-col justify-between">
                            <div class="space-y-4">
                                <div class="flex justify-between border-b border-emerald-800 pb-2">
                                    <span class="opacity-60 text-xs">Kotor (B-T)</span>
                                    <span id="res-kotor" class="font-bold">0 Kg</span>
                                </div>
                                <div class="flex justify-between border-b border-emerald-800 pb-2">
                                    <span class="opacity-60 text-xs">Potongan Kg</span>
                                    <span id="res-pot-kg" class="text-rose-400 font-bold">0 Kg</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-emerald-400 font-black">NETTO AKHIR</span>
                                    <span id="res-netto" class="text-3xl font-black text-emerald-400">0 Kg</span>
                                </div>
                            </div>
                            <div class="text-center mt-10">
                                <p class="text-[10px] uppercase opacity-60 mb-1">Total Pembayaran</p>
                                <h2 id="res-total" class="text-5xl font-black mb-8">Rp 0</h2>
                                <button onclick="simpan('BELI')" class="w-full py-5 bg-emerald-500 hover:bg-emerald-400 rounded-2xl font-black shadow-lg">SIMPAN & CETAK</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- ... (Sisa Page Logistik, Jual, Laporan Sama seperti sebelumnya) ... -->
            <!-- Agar ringkas, saya tetap sertakan kodenya di bawah agar fungsionalitas utuh -->
            
            <div id="page-logistik" class="page-content hidden">
                <div class="max-w-4xl mx-auto glass-panel p-8 rounded-[2rem] border-t-8 border-blue-500 shadow-xl">
                    <h3 class="font-black text-2xl text-blue-900 mb-6">Logistik Pengiriman</h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-10">
                        <div class="space-y-4">
                            <input id="l-do" type="text" placeholder="No. DO / Surat Jalan" class="w-full p-4 border rounded-2xl font-bold">
                            <div class="grid grid-cols-2 gap-4">
                                <input id="l-bruto" type="number" oninput="hitungLogistik()" placeholder="Brutto RAM" class="w-full p-4 border rounded-2xl">
                                <input id="l-tara" type="number" oninput="hitungLogistik()" placeholder="Tarra RAM" class="w-full p-4 border rounded-2xl">
                            </div>
                            <input id="l-pks" type="text" placeholder="PKS Tujuan" class="w-full p-4 border rounded-2xl font-bold">
                            <input type="date" id="l-date" class="w-full p-4 border rounded-2xl">
                        </div>
                        <div class="bg-blue-900 rounded-[2.5rem] p-10 text-white flex flex-col justify-center text-center">
                            <h2 id="res-log-netto" class="text-6xl font-black mb-4">0</h2>
                            <p class="text-sm uppercase font-bold text-blue-300">Netto Muat (Kg)</p>
                            <button onclick="simpan('LOGISTIK')" class="w-full py-5 mt-10 bg-blue-500 hover:bg-blue-400 rounded-2xl font-black">CATAT PENGIRIMAN</button>
                        </div>
                    </div>
                </div>
            </div>

            <div id="page-jual" class="page-content hidden">
                 <div class="max-w-4xl mx-auto glass-panel p-8 rounded-[2rem] border-t-8 border-indigo-500 shadow-xl">
                    <div class="flex justify-between mb-6">
                        <h3 class="font-black text-2xl text-indigo-900">Klaim Pabrik (PKS)</h3>
                        <select id="j-do-ref" onchange="autoFillDO()" class="p-2 bg-slate-100 border rounded-lg text-xs font-bold">
                            <option value="">-- Pilih DO Belum Diklaim --</option>
                        </select>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-10">
                        <div class="space-y-4">
                            <div class="grid grid-cols-2 gap-4">
                                <input id="j-bruto" type="number" oninput="hitungJual()" placeholder="Brutto Pabrik" class="w-full p-4 border rounded-2xl">
                                <input id="j-tara" type="number" oninput="hitungJual()" placeholder="Tarra Pabrik" class="w-full p-4 border rounded-2xl">
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <input id="j-persen" type="number" oninput="hitungJual()" placeholder="Sortasi %" class="w-full p-4 border rounded-2xl">
                                <input id="j-harga" type="number" oninput="hitungJual()" placeholder="Harga Jual" class="w-full p-4 border rounded-2xl font-black">
                            </div>
                            <input type="date" id="j-date" class="w-full p-4 border rounded-2xl">
                            <div id="diff-box" class="hidden p-4 bg-amber-50 border-2 border-amber-200 rounded-2xl">
                                <p class="text-[10px] font-black text-amber-600 uppercase">Selisih Timbangan (Susut)</p>
                                <h4 id="diff-kg" class="text-2xl font-black text-amber-900">0 Kg</h4>
                            </div>
                        </div>
                        <div class="bg-indigo-900 rounded-[2.5rem] p-10 text-white text-center">
                            <h2 id="res-jual-total" class="text-5xl font-black mb-4">Rp 0</h2>
                            <p class="opacity-60 text-sm">Netto Bongkar: <span id="res-jual-netto" class="font-black">0</span> Kg</p>
                            <button onclick="simpan('JUAL')" class="w-full py-5 mt-10 bg-indigo-500 rounded-2xl font-black">SIMPAN KLAIM</button>
                        </div>
                    </div>
                </div>
            </div>

            <div id="page-laporan" class="page-content hidden">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-black">Riwayat Transaksi</h2>
                    <button onclick="exportExcel()" class="bg-emerald-600 text-white px-5 py-2 rounded-xl text-sm font-bold">Excel</button>
                </div>
                <div class="glass-panel rounded-3xl overflow-hidden shadow-sm">
                    <table class="w-full text-sm text-left">
                        <thead class="bg-slate-50">
                            <tr>
                                <th class="p-4">Tanggal</th>
                                <th class="p-4">Tipe</th>
                                <th class="p-4">Nama / Ket</th>
                                <th class="p-4 text-right">Netto</th>
                                <th class="p-4 text-right">Susut</th>
                                <th class="p-4 text-right">Total Rp</th>
                                <th class="p-4 text-center">Aksi</th>
                            </tr>
                        </thead>
                        <tbody id="table-log" class="divide-y"></tbody>
                    </table>
                </div>
            </div>

            <div id="page-biaya" class="page-content hidden">
                <div class="max-w-md mx-auto glass-panel p-8 rounded-3xl border-t-8 border-rose-500 shadow-lg">
                    <h3 class="text-xl font-black mb-6 text-center">Pengeluaran Operasional</h3>
                    <div class="space-y-4">
                        <input id="c-ket" type="text" placeholder="Bensin, Gaji, Listrik, dll" class="w-full p-4 border rounded-2xl font-bold">
                        <input id="c-nominal" type="number" placeholder="Nominal Rp" class="w-full p-4 border rounded-2xl font-black text-rose-600">
                        <input type="date" id="c-date" class="w-full p-4 border rounded-2xl">
                        <button onclick="simpan('BIAYA')" class="w-full py-4 bg-rose-600 text-white font-black rounded-2xl">SIMPAN BIAYA</button>
                    </div>
                </div>
            </div>

            <div id="page-rekap" class="page-content hidden">
                 <div class="flex justify-between items-center mb-8">
                    <h2 class="text-2xl font-black">Laporan Bulanan</h2>
                    <select id="rekap-year" onchange="renderRekap()" class="p-2 border rounded-xl font-bold">
                        <option value="2025">2025</option><option value="2026">2026</option>
                    </select>
                </div>
                <div id="rekap-container" class="grid grid-cols-1 gap-6"></div>
            </div>
        </main>
    </div>

    <script>
        let dataStore = JSON.parse(localStorage.getItem('sawit_erp_vfinal')) || [];
        let pendingDeleteIdx = null;
        const months = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"];

        function setTodayDates() {
            const today = new Date().toISOString().split('T')[0];
            ['b-date', 'l-date', 'j-date', 'c-date'].forEach(id => { if(document.getElementById(id)) document.getElementById(id).value = today; });
        }

        function showToast(msg) {
            const t = document.getElementById('custom-toast');
            t.innerText = msg; t.classList.add('show');
            setTimeout(() => t.classList.remove('show'), 3000);
        }

        function navTo(id) {
            document.querySelectorAll('.page-content').forEach(p => p.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
            document.getElementById('page-' + id).classList.remove('hidden');
            document.getElementById('nav-' + id).classList.add('active');
            if(id === 'rekap') renderRekap();
        }

        // --- CALCULATIONS ---
        function hitungBeli() {
            const b = parseFloat(document.getElementById('b-bruto').value) || 0;
            const t = parseFloat(document.getElementById('b-tara').value) || 0;
            const p = parseFloat(document.getElementById('b-persen').value) || 0;
            const h = parseFloat(document.getElementById('b-harga').value) || 0;
            
            const kotor = b - t;
            const potKg = Math.round(kotor * (p/100));
            const netto = kotor - potKg;
            const total = netto * h;

            document.getElementById('res-kotor').innerText = kotor.toLocaleString() + ' Kg';
            document.getElementById('res-pot-kg').innerText = '- ' + potKg.toLocaleString() + ' Kg';
            document.getElementById('res-netto').innerText = netto.toLocaleString() + ' Kg';
            document.getElementById('res-total').innerText = 'Rp ' + total.toLocaleString();
            return { kotor, potKg, netto, total, b, t, p, h };
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
            const kotor = b - t;
            const netto = kotor - Math.round(kotor * (p/100));
            const total = netto * h;
            document.getElementById('res-jual-netto').innerText = netto.toLocaleString();
            document.getElementById('res-jual-total').innerText = 'Rp ' + total.toLocaleString();

            const refId = document.getElementById('j-do-ref').value;
            if(refId) {
                const doData = dataStore.find(x => x.id == refId);
                const diff = doData.netto - kotor;
                document.getElementById('diff-box').classList.remove('hidden');
                document.getElementById('diff-kg').innerText = diff.toLocaleString() + ' Kg';
            }
            return { netto, total, kotor };
        }

        function autoFillDO() {
            const id = document.getElementById('j-do-ref').value;
            if(!id) return;
            hitungJual();
        }

        function cetakStruk(d) {
            const content = `
                <div class="flex justify-between"><span>Nama</span> <span>: ${d.nama}</span></div>
                <div class="flex justify-between"><span>Tanggal</span> <span>: ${new Date(d.ts).toLocaleDateString()}</span></div>
                <div class="border-b border-dashed my-2"></div>
                <div class="flex justify-between"><span>Brutto</span> <span>: ${d.raw.b} Kg</span></div>
                <div class="flex justify-between"><span>Tarra</span> <span>: ${d.raw.t} Kg</span></div>
                <div class="flex justify-between font-bold"><span>Kotor</span> <span>: ${d.raw.kotor} Kg</span></div>
                <div class="flex justify-between text-xs"><span>Potongan (${d.raw.p}%)</span> <span>: -${d.raw.potKg} Kg</span></div>
                <div class="flex justify-between font-bold text-lg mt-2"><span>NETTO</span> <span>: ${d.netto} Kg</span></div>
                <div class="flex justify-between mt-1"><span>Harga</span> <span>: Rp ${d.raw.h}</span></div>
                <div class="flex justify-between font-bold text-lg border-t border-dashed pt-1"><span>TOTAL</span> <span>: Rp ${d.total.toLocaleString()}</span></div>
            `;
            document.getElementById('print-content').innerHTML = content;
            document.getElementById('print-timestamp').innerText = new Date().toLocaleString();
            window.print();
        }

        function simpan(tipe) {
            let p = { tipe, id: Date.now() };
            if(tipe === 'BELI') {
                const r = hitungBeli();
                p.nama = document.getElementById('b-nama').value || 'Umum';
                p.netto = r.netto; p.total = r.total; p.ts = new Date(document.getElementById('b-date').value).getTime();
                p.raw = r; 
                cetakStruk(p); // Langsung panggil cetak
            } else if(tipe === 'LOGISTIK') {
                const r = hitungLogistik();
                p.do = document.getElementById('l-do').value; p.pks = document.getElementById('l-pks').value;
                p.nama = `Kirim: ${p.do}`; p.netto = r.netto; p.total = 0; p.ts = new Date(document.getElementById('l-date').value).getTime();
            } else if(tipe === 'JUAL') {
                const r = hitungJual();
                p.refId = document.getElementById('j-do-ref').value;
                p.nama = `Klaim: DO ${p.refId}`; p.netto = r.netto; p.total = r.total; p.ts = new Date(document.getElementById('j-date').value).getTime();
                if(p.refId) p.susut = dataStore.find(x => x.id == p.refId).netto - r.kotor;
            } else if(tipe === 'BIAYA') {
                p.nama = document.getElementById('c-ket').value; p.total = parseFloat(document.getElementById('c-nominal').value);
                p.netto = 0; p.ts = new Date(document.getElementById('c-date').value).getTime();
            }

            dataStore.unshift(p);
            localStorage.setItem('sawit_erp_vfinal', JSON.stringify(dataStore));
            renderAll();
            updateDOSelect();
            showToast("Transaksi Berhasil!");
            setTodayDates();
        }

        function openDeleteModal(id) { pendingDeleteIdx = id; document.getElementById('delete-modal').style.display = 'flex'; }
        function closeDeleteModal() { document.getElementById('delete-modal').style.display = 'none'; }
        document.getElementById('confirm-delete-btn').onclick = () => {
            dataStore = dataStore.filter(d => d.id !== pendingDeleteIdx);
            localStorage.setItem('sawit_erp_vfinal', JSON.stringify(dataStore));
            renderAll(); updateDOSelect(); closeDeleteModal();
        };

        function updateDOSelect() {
            const sel = document.getElementById('j-do-ref');
            if(!sel) return;
            sel.innerHTML = '<option value="">-- Pilih DO Belum Diklaim --</option>';
            const claimed = dataStore.filter(d => d.tipe === 'JUAL').map(d => d.refId);
            dataStore.filter(d => d.tipe === 'LOGISTIK' && !claimed.includes(d.id.toString())).forEach(d => {
                sel.insertAdjacentHTML('beforeend', `<option value="${d.id}">${d.do} (${d.netto}Kg)</option>`);
            });
        }

        function renderAll() {
            const table = document.getElementById('table-log');
            if(!table) return; table.innerHTML = '';
            let s=0, o=0, ss=0, bRp=0, jRp=0, exp=0;
            const claimed = dataStore.filter(d => d.tipe === 'JUAL').map(d => d.refId);

            dataStore.forEach(d => {
                if(d.tipe === 'BELI') { s += d.netto; bRp += d.total; }
                if(d.tipe === 'LOGISTIK') { s -= d.netto; if(!claimed.includes(d.id.toString())) o += d.netto; }
                if(d.tipe === 'JUAL') { jRp += d.total; if(d.susut) ss += d.susut; }
                if(d.tipe === 'BIAYA') exp += d.total;

                table.insertAdjacentHTML('beforeend', `<tr class="hover:bg-slate-50">
                    <td class="p-4 text-xs font-bold text-slate-400">${new Date(d.ts).toLocaleDateString()}</td>
                    <td class="p-4"><span class="px-2 py-0.5 rounded text-[10px] font-black uppercase ${d.tipe==='BELI'?'bg-emerald-100 text-emerald-700':d.tipe==='JUAL'?'bg-indigo-100 text-indigo-700':'bg-slate-100'}">${d.tipe}</span></td>
                    <td class="p-4 font-bold">${d.nama}</td>
                    <td class="p-4 text-right">${d.netto ? d.netto.toLocaleString() : '-'}</td>
                    <td class="p-4 text-right text-amber-600 font-bold">${d.susut ? d.susut.toLocaleString() : '-'}</td>
                    <td class="p-4 text-right font-black">Rp ${d.total.toLocaleString()}</td>
                    <td class="p-4 text-center"><button onclick="openDeleteModal(${d.id})" class="text-slate-300 hover:text-rose-500"><i class="fas fa-trash"></i></button></td>
                </tr>`);
            });
            document.getElementById('stok-val').innerText = s.toLocaleString();
            document.getElementById('otw-val').innerText = o.toLocaleString();
            document.getElementById('shrink-val').innerText = ss.toLocaleString();
            document.getElementById('profit-val').innerText = 'Rp ' + (jRp - bRp - exp).toLocaleString();
            updateChart();
        }

        let mainChart;
        function updateChart() {
            const ctx = document.getElementById('chartView');
            const dataset = [...Array(7)].map((_, i) => {
                const date = new Date(); date.setDate(date.getDate() - i);
                const ds = date.toISOString().split('T')[0];
                return dataStore.filter(d => d.tipe === 'BELI' && new Date(d.ts).toISOString().split('T')[0] === ds).reduce((a,c)=>a+c.netto, 0);
            }).reverse();
            if(mainChart) mainChart.destroy();
            mainChart = new Chart(ctx, {
                type: 'bar',
                data: { labels: ['H-6','H-5','H-4','H-3','H-2','H-1','Hari Ini'], datasets: [{ label: 'Beli (Kg)', data: dataset, backgroundColor: '#10b981', borderRadius: 8 }] },
                options: { maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
        }

        function renderRekap() {
            const container = document.getElementById('rekap-container');
            const yr = document.getElementById('rekap-year').value;
            container.innerHTML = '';
            for(let m=11; m>=0; m--) {
                const md = dataStore.filter(d => { const dt = new Date(d.ts); return dt.getMonth()===m && dt.getFullYear().toString()===yr; });
                if(!md.length) continue;
                let b=0, j=0, e=0; md.forEach(d => { if(d.tipe==='BELI') b+=d.total; if(d.tipe==='JUAL') j+=d.total; if(d.tipe==='BIAYA') e+=d.total; });
                const p = j - b - e;
                container.insertAdjacentHTML('beforeend', `<div class="glass-panel p-6 rounded-3xl border-l-8 ${p>=0?'border-emerald-500':'border-rose-500'}">
                    <div class="flex justify-between mb-4"><div><h4 class="font-black text-xl">${months[m]} ${yr}</h4></div><div class="text-right"><p class="text-[10px] font-bold opacity-40">PROFIT</p><p class="font-black text-xl ${p>=0?'text-emerald-600':'text-rose-600'}">Rp ${p.toLocaleString()}</p></div></div>
                    <div class="grid grid-cols-3 gap-4 text-xs font-bold uppercase tracking-widest text-slate-400">
                        <div class="bg-slate-50 p-3 rounded-xl">Beli: <span class="text-slate-700 block text-sm">Rp ${b.toLocaleString()}</span></div>
                        <div class="bg-slate-50 p-3 rounded-xl">Jual: <span class="text-slate-700 block text-sm">Rp ${j.toLocaleString()}</span></div>
                        <div class="bg-slate-50 p-3 rounded-xl">Biaya: <span class="text-rose-600 block text-sm">Rp ${e.toLocaleString()}</span></div>
                    </div>
                </div>`);
            }
        }

        window.onload = () => { setTodayDates(); renderAll(); updateDOSelect(); };
    </script>
</body>
</html>
