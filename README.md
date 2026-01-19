<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TBS PRO - Enterprise Responsive</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        
        :root {
            --primary: #059669;
            --primary-dark: #064e3b;
        }

        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background-color: #f8fafc;
            -webkit-tap-highlight-color: transparent;
        }

        .tab-content {
            display: none;
            animation: fadeIn 0.3s ease-out;
        }

        .tab-content.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .sidebar-link.active {
            background: #10b981;
            color: white;
            box-shadow: 0 4px 12px rgba(16, 185, 129, 0.2);
        }

        #sidebar-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.5);
            z-index: 40;
        }

        @media (max-width: 1024px) {
            #sidebar {
                transform: translateX(-100%);
                transition: transform 0.3s ease-in-out;
            }
            #sidebar.open {
                transform: translateX(0);
            }
            #sidebar-overlay.open {
                display: block;
            }
        }

        .table-container {
            width: 100%;
            overflow-x: auto;
            -webkit-overflow-scrolling: touch;
        }

        .pro-card {
            background: white;
            border-radius: 1rem;
            border: 1px solid #e2e8f0;
            overflow: hidden;
        }

        input, select, textarea {
            font-size: 16px !important;
        }
    </style>
</head>
<body class="text-slate-800">

    <div id="sidebar-overlay" onclick="toggleSidebar()"></div>

    <div class="flex min-h-screen">
        <aside id="sidebar" class="fixed lg:static w-72 h-full bg-slate-900 text-slate-300 flex flex-col z-50">
            <div class="p-6 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 bg-emerald-500 rounded-xl flex items-center justify-center">
                        <i data-lucide="leaf" class="text-white w-6 h-6"></i>
                    </div>
                    <h1 class="text-xl font-bold text-white tracking-tight">TBS PRO</h1>
                </div>
                <button onclick="toggleSidebar()" class="lg:hidden p-2 text-slate-400">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>
            
            <nav class="flex-1 px-4 space-y-1 mt-4 overflow-y-auto">
                <button onclick="showTab('dashboard')" id="btn-dashboard" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="layout-grid" class="w-5 h-5"></i> Dashboard
                </button>
                <button onclick="showTab('pembelian')" id="btn-pembelian" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="shopping-bag" class="w-5 h-5"></i> Pembelian
                </button>
                <button onclick="showTab('pengiriman')" id="btn-pengiriman" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="truck" class="w-5 h-5"></i> Pengiriman
                </button>
                <button onclick="showTab('penjualan')" id="btn-penjualan" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="factory" class="w-5 h-5"></i> Hasil PKS
                </button>
                <button onclick="showTab('lossis')" id="btn-lossis" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="scale" class="w-5 h-5"></i> Edit Stok
                </button>
                <div class="h-[1px] bg-slate-800 my-4 mx-4"></div>
                <button onclick="showTab('keuangan')" id="btn-keuangan" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="landmark" class="w-5 h-5"></i> Kas/Modal
                </button>
                <button onclick="showTab('operasional')" id="btn-operasional" class="sidebar-link w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-white/5 font-semibold text-sm transition-all">
                    <i data-lucide="receipt" class="w-5 h-5"></i> Operasional
                </button>
            </nav>

            <div class="p-6">
                <button onclick="exportToExcel()" class="w-full bg-emerald-600/10 hover:bg-emerald-600/20 text-emerald-400 flex items-center justify-center gap-2 py-3 rounded-xl text-xs font-bold border border-emerald-600/20 transition-all">
                    <i data-lucide="download-cloud" class="w-4 h-4"></i> EXPORT DATA
                </button>
            </div>
        </aside>

        <main class="flex-1 flex flex-col min-w-0">
            <header class="h-16 bg-white border-b flex items-center justify-between px-4 lg:px-8 flex-shrink-0 sticky top-0 z-30">
                <div class="flex items-center gap-4">
                    <button onclick="toggleSidebar()" class="lg:hidden p-2 bg-slate-50 rounded-lg text-slate-600">
                        <i data-lucide="menu" class="w-6 h-6"></i>
                    </button>
                    <div>
                        <h2 id="current-title" class="text-lg font-bold text-slate-900 leading-tight">Dashboard</h2>
                    </div>
                </div>
                <div class="flex items-center gap-3">
                    <div class="hidden sm:block text-right">
                        <p class="text-[10px] font-bold text-emerald-600 uppercase">TBS-PRO-v3</p>
                    </div>
                    <div class="w-8 h-8 bg-slate-100 rounded-full border border-slate-200 flex items-center justify-center font-bold text-xs">AD</div>
                </div>
            </header>

            <div class="p-4 lg:p-8 flex-1 overflow-y-auto">
                
                <section id="content-dashboard" class="tab-content active space-y-6">
                    <div class="grid grid-cols-2 lg:grid-cols-4 gap-3 lg:gap-6">
                        <div class="pro-card p-4 lg:p-6 border-l-4 border-l-emerald-500">
                            <p class="text-[9px] font-bold text-slate-400 uppercase tracking-widest mb-1">Kas Akhir</p>
                            <h3 id="dash-modal" class="text-sm lg:text-xl font-extrabold text-slate-900">Rp 0</h3>
                        </div>
                        <div class="pro-card p-4 lg:p-6 border-l-4 border-l-blue-500">
                            <p class="text-[9px] font-bold text-slate-400 uppercase tracking-widest mb-1">Stok Gudang</p>
                            <h3 id="dash-stok-akhir" class="text-sm lg:text-xl font-extrabold text-slate-900">0 Kg</h3>
                        </div>
                        <div class="pro-card p-4 lg:p-6 border-l-4 border-l-amber-500">
                            <p class="text-[9px] font-bold text-slate-400 uppercase tracking-widest mb-1">Kirim PKS</p>
                            <h3 id="dash-do-total" class="text-sm lg:text-xl font-extrabold text-slate-900">0 Kg</h3>
                        </div>
                        <div class="pro-card p-4 lg:p-6 border-l-4 border-l-indigo-600">
                            <p class="text-[9px] font-bold text-slate-400 uppercase tracking-widest mb-1">Profit</p>
                            <h3 id="dash-profit" class="text-sm lg:text-xl font-extrabold text-emerald-600">Rp 0</h3>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                        <div class="lg:col-span-2 pro-card p-6">
                            <h4 class="font-bold mb-4 text-sm">Neraca Keuangan</h4>
                            <div class="space-y-3">
                                <div class="flex justify-between items-center p-3 bg-slate-50 rounded-lg">
                                    <span class="text-xs text-slate-500">Omzet PKS</span>
                                    <span id="lr-penjualan" class="text-sm font-bold">Rp 0</span>
                                </div>
                                <div class="flex justify-between items-center p-3 bg-slate-50 rounded-lg">
                                    <span class="text-xs text-slate-500">HPP Pembelian</span>
                                    <span id="lr-hpp" class="text-sm font-bold text-red-500">Rp 0</span>
                                </div>
                                <div class="flex justify-between items-center p-3 bg-slate-50 rounded-lg">
                                    <span class="text-xs text-slate-500">Operasional</span>
                                    <span id="lr-ops" class="text-sm font-bold text-orange-500">Rp 0</span>
                                </div>
                                <div class="pt-4 border-t flex justify-between items-center">
                                    <span class="font-bold text-sm">Profit Bersih</span>
                                    <span id="lr-bersih" class="text-xl font-black text-emerald-600">Rp 0</span>
                                </div>
                            </div>
                        </div>
                        <div class="pro-card p-6 bg-slate-900 text-white flex flex-col justify-between">
                            <div>
                                <h4 class="text-emerald-400 font-bold text-[10px] uppercase mb-4 tracking-tighter">Analisa Fisik Barang</h4>
                                <div class="space-y-4">
                                    <div>
                                        <p class="text-[10px] opacity-50 uppercase mb-1 font-bold">Susut Kirim vs PKS</p>
                                        <p id="total-susut" class="text-xl font-bold text-red-400">0 Kg</p>
                                    </div>
                                    <div>
                                        <p class="text-[10px] opacity-50 uppercase mb-1 font-bold">Koreksi Manual Lossis</p>
                                        <p id="total-lossis" class="text-xl font-bold text-blue-400">0 Kg</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="content-pembelian" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-12 gap-6">
                        <div class="xl:col-span-4 pro-card p-6">
                            <h3 class="font-bold mb-4 flex items-center gap-2"><i data-lucide="plus-circle" class="w-4 h-4 text-emerald-500"></i> Form Pembelian</h3>
                            <form onsubmit="handleForm(event, 'pembelian')" class="space-y-4">
                                <div class="grid grid-cols-2 gap-4">
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tanggal</label><input type="date" id="beli-tgl" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" required></div>
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Pemasok</label><input type="text" id="beli-pemasok" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" placeholder="Nama Petani" required></div>
                                </div>
                                <div class="grid grid-cols-3 gap-2">
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Brutto</label><input type="number" id="beli-bruto" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcPurchase()" required></div>
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tara</label><input type="number" id="beli-tara" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcPurchase()" required></div>
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Pot %</label><input type="number" id="beli-pot-pct" step="0.1" value="0" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcPurchase()"></div>
                                </div>
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Harga (Rp/Kg)</label><input type="number" id="beli-harga" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm font-bold" oninput="calcPurchase()" required></div>
                                <div class="p-3 bg-emerald-50 rounded-lg flex justify-between">
                                    <span class="text-[10px] font-bold text-emerald-600 uppercase">Total Bayar</span>
                                    <span id="beli-total-label" class="font-bold text-emerald-700">Rp 0</span>
                                </div>
                                <button type="submit" class="w-full bg-emerald-600 text-white font-bold py-3 rounded-lg text-sm shadow-md">Simpan Data</button>
                            </form>
                        </div>
                        <div class="xl:col-span-8 pro-card">
                            <div class="table-container">
                                <table class="w-full text-xs text-left">
                                    <thead class="bg-slate-50 text-slate-500 uppercase font-bold border-b">
                                        <tr><th class="p-4">Tanggal</th><th class="p-4">Pemasok</th><th class="p-4">Netto</th><th class="p-4">Total</th><th class="p-4 text-center">Aksi</th></tr>
                                    </thead>
                                    <tbody id="list-pembelian" class="divide-y"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="content-pengiriman" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-12 gap-6">
                        <div class="xl:col-span-4 pro-card p-6 h-fit">
                            <h3 class="font-bold mb-4 flex items-center gap-2 text-orange-600"><i data-lucide="truck" class="w-4 h-4"></i> Logistik DO</h3>
                            <form onsubmit="handleForm(event, 'pengiriman')" class="space-y-4">
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tanggal Kirim</label><input type="date" id="kirim-tgl" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" required></div>
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Nama PKS Tujuan</label><input type="text" id="kirim-pks" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" placeholder="PKS XXX" required></div>
                                <div class="grid grid-cols-2 gap-4">
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Brutto</label><input type="number" id="kirim-bruto" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcNetto('kirim')" required></div>
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tara</label><input type="number" id="kirim-tara" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcNetto('kirim')" required></div>
                                </div>
                                <div class="p-3 bg-slate-100 rounded-lg flex justify-between items-center">
                                    <span class="text-[10px] font-bold uppercase text-slate-500">Netto Kirim</span>
                                    <span id="kirim-netto-label" class="font-bold text-orange-600">0 Kg</span>
                                </div>
                                <button type="submit" class="w-full bg-orange-600 text-white font-bold py-3 rounded-lg text-sm shadow-md uppercase">Kirim Barang</button>
                            </form>
                        </div>
                        <div class="xl:col-span-8 pro-card overflow-hidden">
                            <div class="table-container">
                                <table class="w-full text-xs text-left">
                                    <thead class="bg-slate-50 text-slate-500 uppercase font-bold border-b">
                                        <tr><th class="p-4">Tanggal</th><th class="p-4">PKS</th><th class="p-4">Netto</th><th class="p-4">Status</th><th class="p-4 text-right">Aksi</th></tr>
                                    </thead>
                                    <tbody id="list-pengiriman" class="divide-y"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="content-penjualan" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-12 gap-6">
                        <div class="xl:col-span-4 pro-card p-6 h-fit">
                            <h3 class="font-bold mb-4 flex items-center gap-2 text-indigo-600"><i data-lucide="check-circle" class="w-4 h-4"></i> Cairkan DO (PKS)</h3>
                            <form onsubmit="handleForm(event, 'penjualan')" class="space-y-4">
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Pilih DO Kirim</label>
                                    <select id="jual-id-pengiriman" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" onchange="fillJualInfo()" required>
                                        <option value="">-- Pilih DO Aktif --</option>
                                    </select>
                                </div>
                                <div class="grid grid-cols-3 gap-2">
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Brutto</label><input type="number" id="jual-bruto" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcJual()" required></div>
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tara</label><input type="number" id="jual-tara" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcJual()" required></div>
                                    <div><label class="text-[10px] font-bold text-slate-400 uppercase">Pot %</label><input type="number" id="jual-pot-pct" step="0.1" value="0" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" oninput="calcJual()"></div>
                                </div>
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Harga PKS (Rp/Kg)</label><input type="number" id="jual-harga" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm font-bold text-indigo-700" oninput="calcJual()" required></div>
                                <div class="space-y-2">
                                    <div class="flex justify-between text-[10px] px-1 font-bold text-slate-400 uppercase">
                                        <span>Netto Akhir PKS:</span>
                                        <span id="jual-netto-label">0 Kg</span>
                                    </div>
                                    <div class="p-3 bg-indigo-50 rounded-lg flex justify-between items-center">
                                        <span class="text-[10px] font-bold uppercase text-indigo-600">Total Cair</span>
                                        <span id="jual-total-label" class="font-bold text-indigo-700">Rp 0</span>
                                    </div>
                                </div>
                                <button type="submit" class="w-full bg-indigo-600 text-white font-bold py-3 rounded-lg text-sm shadow-md uppercase">Finalisasi Penjualan</button>
                            </form>
                        </div>
                        <div class="xl:col-span-8 pro-card overflow-hidden">
                            <div class="table-container">
                                <table class="w-full text-xs text-left">
                                    <thead class="bg-slate-50 text-slate-500 uppercase font-bold border-b">
                                        <tr><th class="p-4">Tanggal</th><th class="p-4">Netto PKS</th><th class="p-4">Total Cair</th><th class="p-4 text-right">Aksi</th></tr>
                                    </thead>
                                    <tbody id="list-penjualan" class="divide-y"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="content-lossis" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-12 gap-6">
                        <div class="xl:col-span-4 pro-card p-6 h-fit">
                            <h3 class="font-bold mb-4 flex items-center gap-2 text-blue-600"><i data-lucide="scale" class="w-4 h-4"></i> Edit Stok Manual</h3>
                            <form onsubmit="handleForm(event, 'lossis')" class="space-y-4">
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tanggal</label><input type="date" id="lossis-tgl" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" required></div>
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Tipe</label>
                                    <select id="lossis-tipe" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm">
                                        <option value="kurang">Kurangi Stok (Lossis)</option>
                                        <option value="tambah">Tambah Stok (Koreksi)</option>
                                    </select>
                                </div>
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Berat (Kg)</label><input type="number" id="lossis-kg" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm font-bold" required></div>
                                <div><label class="text-[10px] font-bold text-slate-400 uppercase">Keterangan</label><textarea id="lossis-ket" class="w-full bg-slate-50 border p-2.5 rounded-lg text-sm" placeholder="Alasan perubahan stok..."></textarea></div>
                                <button type="submit" class="w-full bg-blue-600 text-white font-bold py-3 rounded-lg text-sm">Update Fisik Stok</button>
                            </form>
                        </div>
                        <div class="xl:col-span-8 pro-card overflow-hidden">
                            <div class="table-container">
                                <table class="w-full text-xs text-left">
                                    <thead class="bg-slate-50 text-slate-500 uppercase font-bold border-b">
                                        <tr><th class="p-4">Tanggal</th><th class="p-4">Tipe</th><th class="p-4">Berat</th><th class="p-4">Alasan</th><th class="p-4 text-right">Aksi</th></tr>
                                    </thead>
                                    <tbody id="list-lossis" class="divide-y"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="content-keuangan" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-6">
                        <div class="pro-card p-6 h-fit"><form onsubmit="handleForm(event, 'modal')" class="space-y-4">
                            <h3 class="font-bold text-sm">Input Kas/Modal</h3>
                            <input type="date" id="modal-tgl" class="w-full border p-2 rounded text-sm">
                            <input type="text" id="modal-ket" class="w-full border p-2 rounded text-sm" placeholder="Keterangan">
                            <input type="number" id="modal-amount" class="w-full border p-2 rounded text-sm" placeholder="Rp Jumlah">
                            <button class="w-full bg-emerald-600 text-white py-2 rounded font-bold text-xs uppercase">Simpan Kas</button>
                        </form></div>
                        <div class="xl:col-span-3 pro-card"><div class="table-container"><table class="w-full text-xs"><tbody id="list-modal" class="divide-y"></tbody></table></div></div>
                    </div>
                </section>

                <section id="content-operasional" class="tab-content">
                    <div class="grid grid-cols-1 xl:grid-cols-4 gap-6">
                        <div class="pro-card p-6 h-fit"><form onsubmit="handleForm(event, 'operasional')" class="space-y-4">
                            <h3 class="font-bold text-sm">Input Biaya Ops</h3>
                            <input type="date" id="ops-tgl" class="w-full border p-2 rounded text-sm">
                            <input type="text" id="ops-ket" class="w-full border p-2 rounded text-sm" placeholder="Bensin/Gaji">
                            <input type="number" id="ops-amount" class="w-full border p-2 rounded text-sm" placeholder="Rp Jumlah">
                            <button class="w-full bg-red-600 text-white py-2 rounded font-bold text-xs uppercase">Simpan Biaya</button>
                        </form></div>
                        <div class="xl:col-span-3 pro-card"><div class="table-container"><table class="w-full text-xs"><tbody id="list-operasional" class="divide-y"></tbody></table></div></div>
                    </div>
                </section>
            </div>
        </main>
    </div>

    <div id="toast" class="fixed bottom-6 left-1/2 -translate-x-1/2 opacity-0 translate-y-4 transition-all duration-300 z-[100] w-[90%] max-w-xs">
        <div class="bg-slate-900 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center justify-center gap-3 text-xs font-bold border border-white/10">
            <i data-lucide="check-circle-2" class="w-4 h-4 text-emerald-400"></i>
            <span id="toast-msg"></span>
        </div>
    </div>

    <script>
        let db = JSON.parse(localStorage.getItem('tbs_pro_multi_device')) || {
            pembelian: [], pengiriman: [], penjualan: [], operasional: [], modal: [], lossis: []
        };

        const titles = {
            dashboard: "Dashboard", pembelian: "Pembelian TBS", pengiriman: "Logistik (DO)",
            penjualan: "Hasil PKS", lossis: "Koreksi Stok", keuangan: "Kas & Modal", operasional: "Biaya Ops"
        };

        window.onload = () => {
            lucide.createIcons();
            initDates();
            updateUI();
            showTab('dashboard');
        };

        function toggleSidebar() {
            const sb = document.getElementById('sidebar');
            const ol = document.getElementById('sidebar-overlay');
            sb.classList.toggle('open');
            ol.classList.toggle('open');
        }

        function initDates() {
            const today = new Date().toISOString().split('T')[0];
            document.querySelectorAll('input[type="date"]').forEach(el => el.value = today);
        }

        function showTab(tab) {
            document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
            document.querySelectorAll('.sidebar-link').forEach(b => b.classList.remove('active'));
            document.getElementById('content-' + tab).classList.add('active');
            document.getElementById('btn-' + tab).classList.add('active');
            document.getElementById('current-title').innerText = titles[tab];
            
            if(tab === 'penjualan') populateDOSelect();
            if(window.innerWidth < 1024 && document.getElementById('sidebar').classList.contains('open')) toggleSidebar();
            updateDashboard();
        }

        function formatRp(num) {
            return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', maximumFractionDigits: 0 }).format(num || 0);
        }

        function notify(msg) {
            const t = document.getElementById('toast');
            document.getElementById('toast-msg').innerText = msg;
            t.classList.replace('opacity-0', 'opacity-100');
            t.classList.replace('translate-y-4', 'translate-y-0');
            setTimeout(() => {
                t.classList.replace('opacity-100', 'opacity-0');
                t.classList.replace('translate-y-0', 'translate-y-4');
            }, 2500);
        }

        function calcPurchase() {
            const bruto = parseFloat(document.getElementById('beli-bruto').value) || 0;
            const tara = parseFloat(document.getElementById('beli-tara').value) || 0;
            const potPct = parseFloat(document.getElementById('beli-pot-pct').value) || 0;
            const harga = parseFloat(document.getElementById('beli-harga').value) || 0;
            const netto1 = bruto - tara;
            const nettoAkhir = netto1 - (netto1 * (potPct/100));
            const total = Math.round(nettoAkhir * harga);
            document.getElementById('beli-total-label').innerText = formatRp(total);
            return { netto: nettoAkhir, total: total };
        }

        function calcNetto(prefix) {
            const bruto = parseFloat(document.getElementById(prefix + '-bruto').value) || 0;
            const tara = parseFloat(document.getElementById(prefix + '-tara').value) || 0;
            const netto = Math.max(0, bruto - tara);
            const label = document.getElementById(prefix + '-netto-label');
            if(label) label.innerText = netto.toLocaleString() + ' Kg';
            return netto;
        }

        function calcJual() {
            const bruto = parseFloat(document.getElementById('jual-bruto').value) || 0;
            const tara = parseFloat(document.getElementById('jual-tara').value) || 0;
            const potPct = parseFloat(document.getElementById('jual-pot-pct').value) || 0;
            const harga = parseFloat(document.getElementById('jual-harga').value) || 0;
            const netto1 = Math.max(0, bruto - tara);
            const nettoAkhir = netto1 - (netto1 * (potPct / 100));
            const total = Math.round(nettoAkhir * harga);
            document.getElementById('jual-netto-label').innerText = nettoAkhir.toLocaleString() + ' Kg';
            document.getElementById('jual-total-label').innerText = formatRp(total);
            return { netto: nettoAkhir, total: total };
        }

        function populateDOSelect() {
            const s = document.getElementById('jual-id-pengiriman');
            const active = db.pengiriman.filter(x => x.status === 'Kirim');
            s.innerHTML = '<option value="">-- Pilih DO Aktif --</option>' + 
                active.map(x => `<option value="${x.id}">${x.pks} (${x.netto}Kg)</option>`).join('');
        }

        function fillJualInfo() {
            const id = document.getElementById('jual-id-pengiriman').value;
            const item = db.pengiriman.find(x => x.id == id);
            if(item) {
                document.getElementById('jual-bruto').value = item.netto;
                document.getElementById('jual-tara').value = 0;
                calcJual();
            }
        }

        function handleForm(e, type) {
            e.preventDefault();
            const data = { id: Date.now() };

            if(type === 'pembelian') {
                const res = calcPurchase();
                Object.assign(data, {
                    tgl: document.getElementById('beli-tgl').value,
                    pemasok: document.getElementById('beli-pemasok').value,
                    netto: res.netto, total: res.total
                });
                db.pembelian.push(data);
            } 
            else if(type === 'lossis') {
                const tipe = document.getElementById('lossis-tipe').value;
                const kg = parseFloat(document.getElementById('lossis-kg').value);
                Object.assign(data, {
                    tgl: document.getElementById('lossis-tgl').value,
                    tipe, kg, netto: tipe === 'kurang' ? -kg : kg,
                    ket: document.getElementById('lossis-ket').value
                });
                db.lossis.push(data);
            }
            else if(type === 'pengiriman') {
                Object.assign(data, {
                    tgl: document.getElementById('kirim-tgl').value,
                    pks: document.getElementById('kirim-pks').value,
                    netto: calcNetto('kirim'), status: 'Kirim'
                });
                db.pengiriman.push(data);
            }
            else if(type === 'penjualan') {
                const idKirim = document.getElementById('jual-id-pengiriman').value;
                const res = calcJual();
                Object.assign(data, {
                    idKirim, tgl: new Date().toISOString().split('T')[0],
                    netto: res.netto, total: res.total
                });
                db.penjualan.push(data);
                const kIdx = db.pengiriman.findIndex(x => x.id == idKirim);
                if(kIdx !== -1) db.pengiriman[kIdx].status = 'Selesai';
            }
            else if(type === 'modal' || type === 'operasional') {
                Object.assign(data, {
                    tgl: document.getElementById(type+'-tgl').value,
                    ket: document.getElementById(type+'-ket').value,
                    amount: parseFloat(document.getElementById(type+'-amount').value)
                });
                db[type].push(data);
            }

            saveData();
            e.target.reset();
            initDates();
            notify("Data Berhasil Disimpan");
        }

        function deleteRecord(type, id) {
            if(!confirm("Hapus data?")) return;
            if(type === 'penjualan') {
                const item = db.penjualan.find(x => x.id == id);
                if(item) {
                    const kIdx = db.pengiriman.findIndex(x => x.id == item.idKirim);
                    if(kIdx !== -1) db.pengiriman[kIdx].status = 'Kirim';
                }
            }
            db[type] = db[type].filter(x => x.id !== id);
            saveData();
        }

        function saveData() {
            localStorage.setItem('tbs_pro_multi_device', JSON.stringify(db));
            updateUI();
        }

        function updateDashboard() {
            const sum = (arr, key) => (arr || []).reduce((a, b) => a + (b[key] || 0), 0);
            const totalBeli = sum(db.pembelian, 'total');
            const totalJual = sum(db.penjualan, 'total');
            const totalOps = sum(db.operasional, 'amount');
            const totalModal = sum(db.modal, 'amount');
            
            const totalKgBeli = sum(db.pembelian, 'netto');
            const totalKgKirim = sum(db.pengiriman, 'netto');
            const totalKgLossis = sum(db.lossis, 'netto');
            const stokFisik = totalKgBeli - totalKgKirim + totalKgLossis;

            let totalSusut = 0;
            db.penjualan.forEach(j => {
                const k = db.pengiriman.find(p => p.id == j.idKirim);
                if(k) totalSusut += (k.netto - j.netto);
            });

            document.getElementById('dash-modal').innerText = formatRp(totalModal - totalBeli + totalJual - totalOps);
            document.getElementById('dash-stok-akhir').innerText = Math.round(stokFisik).toLocaleString() + ' Kg';
            document.getElementById('dash-do-total').innerText = totalKgKirim.toLocaleString() + ' Kg';
            document.getElementById('dash-profit').innerText = formatRp(totalJual - totalBeli - totalOps);

            document.getElementById('lr-penjualan').innerText = formatRp(totalJual);
            document.getElementById('lr-hpp').innerText = formatRp(totalBeli);
            document.getElementById('lr-ops').innerText = formatRp(totalOps);
            document.getElementById('lr-bersih').innerText = formatRp(totalJual - totalBeli - totalOps);
            document.getElementById('total-susut').innerText = Math.round(totalSusut).toLocaleString() + ' Kg';
            document.getElementById('total-lossis').innerText = Math.round(totalKgLossis).toLocaleString() + ' Kg';
        }

        function updateUI() {
            const render = (id, data, tpl) => {
                const el = document.getElementById(id);
                if(el) el.innerHTML = (data || []).slice().reverse().map(x => tpl(x)).join('');
            };

            render('list-pembelian', db.pembelian, x => `
                <tr class="hover:bg-slate-50">
                    <td class="p-4">${x.tgl}</td>
                    <td class="p-4 font-bold">${x.pemasok}</td>
                    <td class="p-4">${Math.round(x.netto).toLocaleString()} Kg</td>
                    <td class="p-4 font-bold text-emerald-600">${formatRp(x.total)}</td>
                    <td class="p-4 text-center"><button onclick="deleteRecord('pembelian', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
                </tr>
            `);

            render('list-lossis', db.lossis, x => `
                <tr>
                    <td class="p-4">${x.tgl}</td>
                    <td class="p-4"><span class="px-2 py-1 rounded text-[9px] font-bold uppercase ${x.tipe==='kurang'?'bg-red-50 text-red-600':'bg-blue-50 text-blue-600'}">${x.tipe}</span></td>
                    <td class="p-4 font-bold">${x.netto.toLocaleString()} Kg</td>
                    <td class="p-4 opacity-50">${x.ket || '-'}</td>
                    <td class="p-4 text-right"><button onclick="deleteRecord('lossis', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
                </tr>
            `);

            render('list-pengiriman', db.pengiriman, x => `
                <tr>
                    <td class="p-4">${x.tgl}</td>
                    <td class="p-4 font-bold">${x.pks}</td>
                    <td class="p-4">${x.netto.toLocaleString()} Kg</td>
                    <td class="p-4"><span class="px-2 py-1 rounded text-[9px] font-bold ${x.status==='Selesai'?'bg-emerald-50 text-emerald-600':'bg-orange-50 text-orange-600'}">${x.status}</span></td>
                    <td class="p-4 text-right"><button onclick="deleteRecord('pengiriman', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
                </tr>
            `);

            render('list-penjualan', db.penjualan, x => `
                <tr>
                    <td class="p-4">${x.tgl}</td>
                    <td class="p-4 font-bold text-indigo-600">${Math.round(x.netto).toLocaleString()} Kg</td>
                    <td class="p-4 font-bold text-emerald-600">${formatRp(x.total)}</td>
                    <td class="p-4 text-right"><button onclick="deleteRecord('penjualan', ${x.id})" class="text-slate-300 hover:text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
                </tr>
            `);

            render('list-modal', db.modal, x => `<tr><td class="p-3">${x.tgl}</td><td class="p-3">${x.ket}</td><td class="p-3 text-right font-bold text-emerald-600">${formatRp(x.amount)}</td><td class="p-3 text-right"><button onclick="deleteRecord('modal', ${x.id})" class="text-slate-200 hover:text-red-500"><i data-lucide="x-circle" class="w-4 h-4"></i></button></td></tr>`);
            render('list-operasional', db.operasional, x => `<tr><td class="p-3">${x.tgl}</td><td class="p-3">${x.ket}</td><td class="p-3 text-right font-bold text-red-600">${formatRp(x.amount)}</td><td class="p-3 text-right"><button onclick="deleteRecord('operasional', ${x.id})" class="text-slate-200 hover:text-red-500"><i data-lucide="x-circle" class="w-4 h-4"></i></button></td></tr>`);

            updateDashboard();
            lucide.createIcons();
        }

        function exportToExcel() {
            // Cek apakah ada data sama sekali
            const hasData = Object.values(db).some(arr => arr.length > 0);
            if (!hasData) {
                notify("Gagal: Belum ada data untuk diekspor!");
                return;
            }

            try {
                const wb = XLSX.utils.book_new();
                let addedAny = false;

                Object.keys(db).forEach(key => {
                    if (db[key] && db[key].length > 0) {
                        const ws = XLSX.utils.json_to_sheet(db[key]);
                        XLSX.utils.book_append_sheet(wb, ws, key.toUpperCase());
                        addedAny = true;
                    }
                });

                if (addedAny) {
                    XLSX.writeFile(wb, `TBS_PRO_Full_Report.xlsx`);
                    notify("Laporan Diunduh!");
                } else {
                    notify("Gagal: Tidak ada sheet yang valid!");
                }
            } catch (err) {
                console.error("Export error:", err);
                notify("Terjadi kesalahan saat mengekspor data.");
            }
        }
    </script>
</body>
</html>
